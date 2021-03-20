---
title: Tanúsítvány-létrehozás monitorozása és kezelése
description: Olyan forgatókönyvek, amelyek számos lehetőséget mutatnak a tanúsítvány-létrehozási folyamat létrehozásához, figyeléséhez és a Key Vaultsal való interakcióhoz.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 02e13ce81ed2f11c0bb69015a4864c4a1ad55593
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "81430967"
---
# <a name="monitor-and-manage-certificate-creation"></a>Tanúsítvány-létrehozás monitorozása és kezelése
A következőkre vonatkozik: Azure

A cikkben ismertetett forgatókönyvek/műveletek a következők:

- Egy támogatott kiállítóval rendelkező KV-tanúsítvány igénylése
- Kérelem függőben lévő kérelmének állapota "Inprogress"
- A kérelem függőben lévő kérésének állapota "Complete"
- Függőben lévő kérelem beolvasása – függőben lévő kérelem állapota "megszakítva" vagy "sikertelen"
- Függőben lévő kérelem beolvasása – függőben lévő kérelem állapota "törölve" vagy "átírt"
- Létrehozás (vagy importálás), ha a függőben lévő kérelem létezik – az állapot "folyamatban"
- Egyesítés, ha a függőben lévő kérelem létrehozása kiállítóval történik (például DigiCert)
- Lemondás kérése, ha a függőben lévő kérelem állapota "Inprogress"
- Függőben lévő kérelem objektumának törlése
- KV-tanúsítvány manuális létrehozása
- Egyesítés egy függőben lévő kérelem létrehozásakor – manuális tanúsítvány létrehozása

## <a name="request-a-kv-certificate-with-a-supported-issuer"></a>Egy támogatott kiállítóval rendelkező KV-tanúsítvány igénylése 

|Metódus|Kérés URI-ja|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

A következő példákban egy "mydigicert" nevű objektumra van szükség ahhoz, hogy a Key vaultban már elérhető legyen a DigiCert. A tanúsítvány kiállítója Azure Key Vault (KV) CertificateIssuer erőforrásként jelölt entitás. A rendszer a KV-tanúsítvány forrására vonatkozó információk megadására szolgál. kiállító neve, szolgáltatója, hitelesítő adatai és egyéb rendszergazdai részletek.

### <a name="request"></a>Kérés

```json
{
  "policy": {
    "x509_props": {
      "subject": "CN=MyCertSubject1"
    },
    "issuer": {
      "name": "mydigicert",
      "cty": "OV-SSL",
    }
  }
}
```

### <a name="response"></a>Reagálás

```
StatusCode: 202, ReasonPhrase: 'Accepted'
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "mydigicert"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "InProgress",
  "status_details": "Pending certificate created. Certificate request is in progress. This may take some time based on the issuer provider. Please check again later",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}

```

## <a name="get-pending-request---request-status-is-inprogress"></a>Kérelem függőben lévő kérelmének állapota "Inprogress"

|Metódus|Kérés URI-ja|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Kérés
GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OR

GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

> [!NOTE]
> Ha *request_id* van megadva a lekérdezésben, úgy viselkedik, mint egy szűrő. Ha a lekérdezésben és a függőben lévő objektumban lévő *request_id* eltérnek, a rendszer a 404-es HTTP-állapotkódot adja vissza.

### <a name="response"></a>Reagálás

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "inProgress",
  "status_details": "…",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}

```

## <a name="get-pending-request---request-status-is-complete"></a>A kérelem függőben lévő kérésének állapota "Complete"

### <a name="request"></a>Kérés

|Metódus|Kérés URI-ja|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OR

GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Reagálás

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "completed",
  "request_id": "a76827a18b63421c917da80f28e9913d",
  "target": “https://mykeyvault.vault.azure.net/certificates/mycert1?api-version={api-version}"
}

```

## <a name="get-pending-request---pending-request-status-is-canceled-or-failed"></a>Függőben lévő kérelem beolvasása – függőben lévő kérelem állapota "megszakítva" vagy "sikertelen"

### <a name="request"></a>Kérés

|Metódus|Kérés URI-ja|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OR

GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Reagálás

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "failed",
  "status_details": "",
  "request_id": "a76827a18b63421c917da80f28e9913d",
  "error": {
    "code": "<errorcode>",
    "message": "<message>"
  }
}

```

> [!NOTE]
> A *errorcode* értéke lehet "tanúsítvány kiállítói hiba" vagy "elutasított kérelem" a kiállító vagy a felhasználói hiba alapján.

## <a name="get-pending-request---pending-request-status-is-deleted-or-overwritten"></a>Függőben lévő kérelem beolvasása – függőben lévő kérelem állapota "törölve" vagy "átírt"
Egy függő objektumot törölheti vagy felülírhat egy létrehozási/importálási művelet, ha az állapota nem "Inprogress".

|Metódus|Kérés URI-ja|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Kérés
GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OR

GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Reagálás

```
StatusCode: 404, ReasonPhrase: 'Not Found'
{
  "error": {
    "code": "PendingCertificateNotFound",
    "message": "…"
  }
}

```

## <a name="create-or-import-when-pending-request-exists---status-is-inprogress"></a>Létrehozás (vagy importálás), ha a függőben lévő kérelem létezik – az állapot "folyamatban"
Egy függőben lévő objektum négy lehetséges állapottal rendelkezik; "befejezetlen", "megszakított", "sikertelen" vagy "befejezett".

Ha egy függőben lévő kérelem állapota "Inprogress", a létrehozás (és az importálás) művelet sikertelen lesz, és a http-állapotkód 409 (ütközés).

Ütközés javítása:

- Ha a tanúsítvány manuálisan lett létrehozva, a függő objektumon egyesítve vagy törléssel elvégezheti a KV-es tanúsítványt.

- Ha a tanúsítvány egy kiállítóval jön létre, megvárhatja, amíg a tanúsítvány be nem fejeződik, a művelet meghiúsul vagy meg lett szakítva. Másik lehetőségként törölheti is a függőben lévő objektumot.

> [!NOTE]
> A függőben lévő objektumok törlésével előfordulhat, hogy nem szakítja meg a x509-tanúsítványkérelmet a szolgáltatónál.

|Metódus|Kérés URI-ja|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

### <a name="request"></a>Kérés

```json
{
  "policy": {
    "x509_props": {
      "subject": "CN=MyCertSubject1"
    },
    "issuer": {
      "name": "mydigicert"
    }
  }
}
```

### <a name="response"></a>Reagálás

```
StatusCode: 409, ReasonPhrase: 'Conflict'
{
  "error": {
    "code": "Forbidden",
    "message": "A new key vault certificate can not be created or imported while a pending key vault certificate's status is inProgress."
  }
}

```

## <a name="merge-when-pending-request-is-created-with-an-issuer"></a>Egyesítés, ha a függőben lévő kérelem egy kiállítóval jön létre
Az egyesítés nem engedélyezett, ha a függőben lévő objektum egy kiállítóval jön létre, de engedélyezett, ha az állapota "Inprogress". 

Ha a x509-tanúsítvány létrehozására irányuló kérelem valamilyen okból meghiúsul vagy leáll, és ha sávon kívüli x509-tanúsítványt kérhet le, a rendszer egyesítő műveletet hajthat végre a KV-os tanúsítvány végrehajtásához.

|Metódus|Kérés URI-ja|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|

### <a name="request"></a>Kérés

```json
{
  "x5c": [ "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8=" ]
}

```

### <a name="response"></a>Reagálás

```json
StatusCode: 403, ReasonPhrase: 'Forbidden'
{
  "error": {
    "code": "Forbidden",
    "message": "Merge is forbidden on pending object created with issuer : <issuer-name> while it is in progess."
  }
}

```

## <a name="request-a-cancellation-while-the-pending-request-status-is-inprogress"></a>Lemondás kérése, ha a függőben lévő kérelem állapota "Inprogress"
Csak törlésre lehet szükség. Egy kérelem vagy nem törölhető. Ha egy kérelem nem "folyamatban", a rendszer 400 (hibás kérelem) http-állapotot ad vissza.

|Metódus|Kérés URI-ja|
|------------|-----------------|
|JAVÍTÁS|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Kérés
JAVÍTÁS `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OR

JAVÍTÁS `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

```json
{
  "cancellation_requested": true
}

```

### <a name="response"></a>Reagálás

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": true,
  "status": "inProgress",
  "status_details": "…",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}
```

## <a name="delete-a-pending-request-object"></a>Függőben lévő kérelem objektumának törlése

> [!NOTE]
> A függőben lévő objektum törlése lehet, hogy nem szakítja meg a x509-tanúsítványkérelmet a szolgáltatóval.

|Metódus|Kérés URI-ja|
|------------|-----------------|
|DELETE|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Kérés
TÖRLÉSE `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OR

TÖRLÉSE `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Reagálás

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "inProgress",
  "request_id": "a76827a18b63421c917da80f28e9913d",
}
```

## <a name="create-a-kv-certificate-manually"></a>KV-tanúsítvány manuális létrehozása
Létrehozhat egy tetszőleges HITELESÍTÉSSZOLGÁLTATÓval kiadott tanúsítványt egy manuális létrehozási folyamat segítségével. Állítsa a kiállító nevét "Unknown" értékre, vagy ne adja meg a kiállító mezőt.

|Metódus|Kérés URI-ja|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

### <a name="request"></a>Kérés

```json
{
  "policy": {
    "x509_props": {
      "subject": "CN=MyCertSubject1"
    },
    "issuer": {
      "name": "Unknown"
    }
  }
}

```

### <a name="response"></a>Reagálás

```
StatusCode: 202, ReasonPhrase: 'Accepted'
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "Unknown"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "status": "inProgress",
  "status_details": "Pending certificate created. Please Perform Merge to complete the request.",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}

```

## <a name="merge-when-a-pending-request-is-created---manual-certificate-creation"></a>Egyesítés egy függőben lévő kérelem létrehozásakor – manuális tanúsítvány létrehozása

|Metódus|Kérés URI-ja|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|

### <a name="request"></a>Kérés

```json
{
  "x5c": [ "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8=" ]
}

```

|Elem neve|Kötelező|Típus|Verzió|Leírás|
|------------------|--------------|----------|-------------|-----------------|
|x5c|Yes|array|\<introducing version>|X509-tanúsítványlánc alap 64 sztring tömbként.|

### <a name="response"></a>Reagálás

```
StatusCode: 201, ReasonPhrase: 'Created'
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1?api-version={api-version}"
{
    "id": "https mykeyvault.vault.azure.net/certificates/mycert1/f366e1a9dd774288ad84a45a5f620352",
    "kid": "https:// mykeyvault.vault.azure.net/keys/mycert1/f366e1a9dd774288ad84a45a5f620352",
    "sid": " mykeyvault.vault.azure.net/secrets/mycert1/f366e1a9dd774288ad84a45a5f620352",
    "cer": "……de34534……",
    "x5t": "n14q2wbvyXr71Pcb58NivuiwJKk",
    "attributes": {
        "enabled": true,
        "exp": 1530394215,
        "nbf": 1435699215,
        "created": 1435699919,
        "updated": 1435699919
    },
    "pending": {
        "id": "https:// mykeyvault.vault.azure.net/certificates/mycert1/pending"
    },
    "policy": {
        "id": "https:// mykeyvault.vault.azure.net/certificates/mycert1/policy",
        "key_props": {
            "exportable": false,
            "kty": "RSA",
            "key_size": 2048,
            "reuse_key": false
        },
        "secret_props": {
            "contentType": "application/x-pkcs12"
        },
        "x509_props": {
            "subject": "CN=Mycert1",
            "ekus": ["1.3.6.1.5.5.7.3.1", "1.3.6.1.5.5.7.3.2"],
            "validity_months": 12
        },
        "lifetime_actions": [{
            "trigger": {
                "lifetime_percentage": 80
            },
            "action": {
                "action_type": "EmailContacts"
            }
        }],
        "issuer": {
            "name": "Unknown"
        },
        "attributes": {
            "enabled": true,
            "created": 1435699811,
            "updated": 1435699811
        }
    }
}

```

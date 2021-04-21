---
title: Tanúsítvány-létrehozás monitorozása és kezelése
description: Forgatókönyvek, amelyek számos lehetőséget bemutatnak a tanúsítvány-létrehozási folyamat létrehozásához, figyeléséhez és Key Vault.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 8e4acb5195497dd31f466829b1cde301ba9696b3
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751093"
---
# <a name="monitor-and-manage-certificate-creation"></a>Tanúsítvány-létrehozás monitorozása és kezelése
A következőkre vonatkozik: Azure

A cikkben ismertetett forgatókönyvek/műveletek a következőek:

- KV-tanúsítvány igénylése támogatott kiállítóval
- Függőben lévő kérés lekérése – a kérés állapota "inProgress"
- Függőben lévő kérelem lekérése – a kérelem állapota "complete" (kész)
- Függőben lévő kérelem lekérése – a függőben lévő kérés állapota "megszakítva" vagy "sikertelen"
- Függőben lévő kérelem lekérése – a függőben lévő kérés állapota "törölve" vagy "felülírva"
- Létrehozás (vagy importálás) függőben lévő kérelem esetén – az állapot "inProgress"
- Egyesítés függőben lévő kérelem létrehozásakor egy kiállítóval (például DigiCert)
- Megszakítás kérése, amíg a függőben lévő kérés állapota "InProgress" (Folyamatban)
- Függőben lévő kérelemobjektum törlése
- KV-tanúsítvány manuális létrehozása
- Egyesítés függőben lévő kérelem létrehozásakor – manuális tanúsítvány-létrehozás

## <a name="request-a-kv-certificate-with-a-supported-issuer"></a>KV-tanúsítvány igénylése támogatott kiállítóval 

|Metódus|Kérés URI-ja|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

Az alábbi példákhoz szükséges, hogy a "mydigicert" nevű objektum már elérhető legyen a kulcstartóban, és a kiállító szolgáltatója DigiCert legyen. A tanúsítványkiállító egy, a Azure Key Vault (KV) által CertificateIssuer erőforrásként képviselt entitás. A KV-tanúsítvány forrásának információit adja meg; a kiállító neve, a szolgáltató, a hitelesítő adatok és egyéb felügyeleti adatok.

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

## <a name="get-pending-request---request-status-is-inprogress"></a>Függőben lévő kérés lekérése – a kérés állapota "inProgress"

|Metódus|Kérés URI-ja|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Kérés
Kap `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OR

Kap `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

> [!NOTE]
> Ha *request_id* van megadva a lekérdezésben, az szűrőként működik. Ha *request_id* lekérdezésben és a függőben lévő objektumban lévő adatok eltérnek, a 404-es HTTP-állapotkódot ad vissza.

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

## <a name="get-pending-request---request-status-is-complete"></a>Függőben lévő kérelem lekérése – a kérelem állapota "complete" (kész)

### <a name="request"></a>Kérés

|Metódus|Kérés URI-ja|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

Kap `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OR

Kap `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

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

## <a name="get-pending-request---pending-request-status-is-canceled-or-failed"></a>Függőben lévő kérelem lekérése – a függőben lévő kérés állapota "megszakítva" vagy "sikertelen"

### <a name="request"></a>Kérés

|Metódus|Kérés URI-ja|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

Kap `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OR

Kap `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

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
> A hibakód *értéke* lehet "Tanúsítványkiállítói hiba" vagy "Kérelem elutasítva" a kiállító vagy a felhasználói hiba alapján.

## <a name="get-pending-request---pending-request-status-is-deleted-or-overwritten"></a>Függőben lévő kérelem lekérése – a függőben lévő kérelem állapota "törölve" vagy "felülírva"
A függőben lévő objektumok törölhetők vagy felülírhatók egy létrehozási/importálási művelettel, ha az állapota nem "inProgress".

|Metódus|Kérés URI-ja|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Kérés
Kap `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OR

Kap `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

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

## <a name="create-or-import-when-pending-request-exists---status-is-inprogress"></a>Létrehozás (vagy Importálás) függőben lévő kérelem esetén – az állapot "inProgress"
A függőben lévő objektumok négy lehetséges állammal rendelkezik: "inprogress", "canceled", "failed" vagy "completed".

Ha egy függőben lévő kérelem állapota "inprogress", a létrehozási (és importálási) műveletek 409-es HTTP-állapotkóddal (ütközéssel) meghiúsulnak.

Ütközés kijavítása:

- Ha a tanúsítványt manuálisan hozta létre, akkor a kv tanúsítványt egyesítés vagy törlés alkalmazással is befejezheti a függőben lévő objektumon.

- Ha a tanúsítványt kiállítóval együtt hozták létre, megvárhatja, amíg a tanúsítvány befejeződik, meghibásodik vagy visszavonják. Másik lehetőségként törölheti a függőben lévő objektumot.

> [!NOTE]
> A függőben lévő objektumok törlése megszakíthatja az x509-tanúsítványkérelmet a szolgáltatónál.

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

## <a name="merge-when-pending-request-is-created-with-an-issuer"></a>Egyesítés függőben lévő kérelem kiállítóval való létrehozásakor
Az egyesítés nem engedélyezett, ha függőben lévő objektum jön létre egy kiállítóval, de az "inProgress" állapot engedélyezett. 

Ha az x509-tanúsítvány létrehozására vonatkozó kérelem valamilyen okból meghiúsul vagy megszakad, és egy x509-tanúsítvány sávon nem sávon lévő módszerrel kérhető le, egyesítési művelettel befejezhető a KV-tanúsítvány.

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

## <a name="request-a-cancellation-while-the-pending-request-status-is-inprogress"></a>Megszakítás kérése, amíg a függőben lévő kérés állapota "InProgress" (Folyamatban)
Lemondást csak kérhet. Előfordulhat, hogy egy kérést visszavonnak, vagy nem. Ha a kérés nem "bejövő forgalom", a rendszer 400 -as (Hibás kérés) HTTP-állapotot ad vissza.

|Metódus|Kérés URI-ja|
|------------|-----------------|
|Javítás|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Kérés
Javítás `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OR

Javítás `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

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

## <a name="delete-a-pending-request-object"></a>Függőben lévő kérelemobjektum törlése

> [!NOTE]
> A függőben lévő objektum törlése megszakíthatja az x509-tanúsítványkérelmet a szolgáltatónál.

|Metódus|Kérés URI-ja|
|------------|-----------------|
|DELETE|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Kérés
Töröl `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OR

Töröl `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

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
Létrehozhat egy ön által választott hitelesítésszolgáltató által kiállított tanúsítványt egy manuális létrehozási folyamattal. Állítsa a kiállító nevét "Ismeretlen" névre, vagy ne adja meg a kiállító mezőt.

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

## <a name="merge-when-a-pending-request-is-created---manual-certificate-creation"></a>Egyesítés függőben lévő kérelem létrehozásakor – manuális tanúsítvány-létrehozás

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
|x5c|Yes|array|\<introducing version>|X509-tanúsítványlánc base 64 sztringtömbként.|

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

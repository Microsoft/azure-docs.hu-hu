---
title: Virtualizálás-alapú biztonság (VBS) protokoll az Azure-igazoláshoz
description: VBS-igazolási protokoll
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 38012c5b4bb9338c1200d9583256193ee8402c98
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99507879"
---
# <a name="trusted-platform-module-tpm-and-virtualization-based-securityvbs-enclave-attestation-protocol"></a>Platformmegbízhatósági modul (TPM) és virtualizálás-alapú biztonság (VBS) enklávé igazolási protokoll 

Microsoft Azure igazolást, amely erős biztonsági garanciát biztosít a megbízhatósági lánc ellenőrzéséhez, a rendszer egy megbízhatósági kapcsolatot (TPM) tart fenn a hypervisor és a Secure kernel elindításához. Ahhoz, hogy ez az Azure-tanúsítvány elérhető legyen, a biztonságos enklávéban való megbízhatósági kapcsolat létrehozása előtt igazolnia kell a gép rendszerindítási állapotát. Az operációs rendszert, a Hypervisort és a Secure kernel bináris fájljait a megfelelő hivatalos Microsoft-hatóságoknak kell aláírnia, és azokat biztonságos módon kell konfigurálni. Ha kötött megbízhatósági kapcsolat áll fenn a platformmegbízhatósági modul (TPM) és a hypervisor állapota között, megbízhatónak tartjuk a mért rendszerindítási naplóban megadott virtualizációs biztonsági (VBS) enklávé IDKs, ezzel ellenőrizheti, hogy egy kulcspár létrejött-e az enklávé és egy olyan igazolási jelentés alapján, amely az adott kulcs megbízhatóságát köti össze, és más jogcímeket, például a biztonsági szintet és a rendszerindítási igazolási tulajdonságokat is tartalmaz. 

A VBS enklávék TPM-t igényelnek a biztonsági alaprendszer érvényesítéséhez szükséges mérték biztosításához. A VBS enklávékat a TPM-végpont igazolja a protokollban található kérelem-objektum mellett. 

## <a name="protocol-messages"></a>Protokoll üzenetei

### <a name="init-message"></a>Init-üzenet

#### <a name="direction"></a>Irány

Ügyfél – > Azure-igazolás

#### <a name="payload"></a>Adattartalom

```
{ 
  "type": "aikcert" 
} 
```

"type" (ASCII karakterlánc): a kért igazolás típusát jelöli. Jelenleg csak a "aikcert" támogatott.

### <a name="challenge-message"></a>Kérdéses üzenet

#### <a name="direction"></a>Irány

Azure-igazolás – > ügyfél

#### <a name="payload"></a>Adattartalom

```
{ 
  "challenge": "<BASE64URL(CHALLENGE)>", 
  "service_context": "<BASE64URL(SERVICECONTEXT)>" 
} 
```

**Challenge** (BASE64URL (oktett)): a szolgáltatás által kiállított véletlenszerű érték.

**service_context** (BASE64URL (oktettek)): a szolgáltatás által létrehozott átlátszatlan környezet.


### <a name="request-message"></a>Kérelem üzenete

#### <a name="direction"></a>Irány

Ügyfél – > Azure-igazolás 

#### <a name="payload"></a>Adattartalom

```
{
  "request": "<JWS>"
}
```

**kérelem** (JWS): a kérelem egy JSON web Signature (JWS) struktúrából áll. A JWS által védett fejléc és JWS adattartalom alább látható. Ahogy a JWS struktúrában, a végső érték a következőkből áll:

BASE64URL (UTF8 (JWS Protected header)) | | '.' ||

BASE64URL (JWS hasznos adat) | | '.' ||

BASE64URL (JWS-aláírás)

##### <a name="jws-protected-header"></a>JWS által védett fejléc

```
{
  "alg": "PS256",
  "typ": "attReq"
  // no "kid" parameter as the key specified by attest_key MUST sign this JWS to prove possession.
}
```

##### <a name="jws-payload"></a>JWS hasznos adat

A JWS-tartalom alapszintű vagy VBS típusú lehet. Az alapszintű érték akkor használatos, ha a tanúsítvány igazolása nem tartalmaz VBS-adatokat. 

Csak TPM minta: 

``` 
{ 
  "att_type": "basic", 
  "att_data": { 
    "rp_id": "<URL>", 
    "rp_data": "<BASE64URL(RPCUSTOMDATA)>", 
    "challenge": "<BASE64URL(CHALLENGE)>", 

    "tpm_att_data": { 
      "srtm_boot_log": "<BASE64URL(SRTMBOOTLOG)>", 
      "srtm_resume_log": "<BASE64URL(SRTMRESUMELOG)>", 
      "drtm_boot_log": "<BASE64URL(DRTMBOOTLOG)>", 
      "drtm_resume_log": "<BASE64URL(DRTMRESUMELOG)>", 
      "aik_cert": "<BASE64URL(AIKCERTIFICATE)>", 

      // aik_pub is represented as a JSON Web Key (JWK) object (RFC 7517). 

      "aik_pub": { 
        "kty": "RSA", 
        "n": "<Base64urlUInt(MODULUS)>", 
        "e": "<Base64urlUInt(EXPONENT)>" 
      }, 
      "current_claim": "<BASE64URL(CURRENTCLAIM)>", 
      "boot_claim": "<BASE64URL(BOOTCLAIM)>" 
    }, 

    // attest_key is represented as a JSON Web Key (JWK) object (RFC 7517). 

    "attest_key": { 
      "kty": "RSA", 
      "n": "<Base64urlUInt(MODULUS)>", 
      "e": "<Base64urlUInt(EXPONENT)>" 
    }, 
    "custom_claims": [ 
      { 
        "name": "<name>", 
        "value": "<value>", 
        "value_type": "<value_type>" 
      }, 
      { 
        "name": "<name>", 
        "value": "<value>", 
        "value_type": "<value_type>" 
      } 
    ], 
    "service_context": "<BASE64URL(SERVICECONTEXT)>" 
  } 
} 
```

TPM + VBS enklávé minta: 

``` 
{ 
  "att_type": "vbs", 
  "att_data": { 
    "report_signed": { 
      "rp_id": "<URL>", 
      "rp_data": "<BASE64URL(RPCUSTOMDATA)>", 
      "challenge": "<BASE64URL(CHALLENGE)>", 
      "tpm_att_data": { 
        "srtm_boot_log": "<BASE64URL(SRTMBOOTLOG)>", 
        "srtm_resume_log": "<BASE64URL(SRTMRESUMELOG)>", 
        "drtm_boot_log": "<BASE64URL(DRTMBOOTLOG)>", 
        "drtm_resume_log": "<BASE64URL(DRTMRESUMELOG)>", 
        "aik_cert": "<BASE64URL(AIKCERTIFICATE)>", 

        // aik_pub is represented as a JSON Web Key (JWK) object (RFC 7517). 

        "aik_pub": { 
          "kty": "RSA", 
          "n": "<Base64urlUInt(MODULUS)>", 
          "e": "<Base64urlUInt(EXPONENT)>" 
        }, 
        "current_claim": "<BASE64URL(CURRENTCLAIM)>", 
        "boot_claim": "<BASE64URL(BOOTCLAIM)>" 
      }, 

      // attest_key is represented as a JSON Web Key (JWK) object (RFC 7517). 

      "attest_key": { 
        "kty": "RSA", 
        "n": "<Base64urlUInt(MODULUS)>", 
        "e": "<Base64urlUInt(EXPONENT)>" 
      }, 
      "custom_claims": [ 
        { 
          "name": "<name>", 
          "value": "<value>", 
          "value_type": "<value_type>" 
        }, 
        { 
          "name": "<name>", 
          "value": "<value>", 
          "value_type": "<value_type>" 
        } 
      ], 
      "service_context": "<BASE64URL(SERVICECONTEXT)>" 
    }, 
    "vsm_report": "<BASE64URL(REPORT)>" 
  } 
} 
``` 

**rp_id** (StringOrURI): függő entitás azonosítója. A szolgáltatás használja a számítógép-azonosító jogcím kiszámításához

**rp_data** (BASE64URL (oktettek)): a függő entitás által átadott átlátszatlan adatvesztés. Ezt a függő entitás általában a jelentés frissességének biztosítására használja.

**Challenge** (BASE64URL (oktett)): a szolgáltatás által kiállított véletlenszerű érték

**tpm_att_data**: TPM-hez kapcsolódó igazolási adat

- **srtm_boot_log (BASE64URL (oktettek))**: az SRTM-rendszerindítási naplók lekérése a (z) függvény Tbsi_Get_TCG_Log_Ex log type = TBS_TCGLOG_SRTM_BOOT

- **srtm_resume_log (BASE64URL (oktettek))**: a SRTM folytatja a napló lekérését a (z) function Tbsi_Get_TCG_Log_Ex és a log type = TBS_TCGLOG_SRTM_RESUME használatával.

- **drtm_boot_log (BASE64URL (oktettek))**: az drtm-rendszerindítási naplók lekérése a (z) függvény Tbsi_Get_TCG_Log_Ex log type = TBS_TCGLOG_DRTM_BOOT

- **drtm_resume_log (BASE64URL (oktettek))**: a drtm folytatja a napló lekérését a (z) function Tbsi_Get_TCG_Log_Ex és a log type = TBS_TCGLOG_DRTM_RESUME használatával.

- **aik_cert (BASE64URL (oktettek))**: az AIK X. 509 tanúsítványa, amelyet a (z) tulajdonsággal rendelkező Function NCryptGetProperty adott vissza. NCRYPT_CERTIFICATE_PROPERTY

- **aik_pub**: az AIK nyilvános része JSON webkulcs (JWK) objektumként van megjelölve (RFC 7517)

- **current_claim (BASE64URL (oktettek))**: az aktuális PCR-állapothoz tartozó igazolási jogcím, amelyet a dwClaimType = NCRYPT_CLAIM_PLATFORM és a (z) NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK paraméterrel rendelkező NCryptCreateClaim függvény ad vissza, amely tartalmazza az összes PCR. A szolgáltatás által eljuttatott kihívást is fel kell használni a jogcím kiszámításához.

- **boot_claim (BASE64URL (bájt))**: az igazolási jogcímet, amelyet a rendszer a (z) dwClaimType = NCRYPT_CLAIM_PLATFORM és NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK paraméterrel rendelkező NCryptCreateClaim függvény által visszaadott rendszerindítási jogcímen visszaadott, az összes PCR

**vsm_report**   (BASE64URL (OKTETTek)): a EnclaveGetAttestationReport függvény által visszaadott VBS enklávé igazolási jelentés. A EnclaveData paraméternek a report_signed értékének SHA-512 kivonatának kell lennie (beleértve a nyitó és a záró kapcsos zárójeleket is). A kivonatoló függvény bemenete UTF8 (report_signed)

**attest_key**: az enklávé kulcsának nyilvános része JSON webkulcs (JWK) objektumként van megjelölve (RFC 7517)

**custom_claims**: a szolgáltatásnak eljuttatott egyéni enklávé-jogcímek tömbje, amelyet a szabályzat kiértékel. A jogcím

- **név (karakterlánc)**: a jogcím neve. Ezt a nevet a rendszer hozzáfűzi az igazolási szolgáltatás által meghatározott URL-címhez (az ütközések elkerülése érdekében), az összefűzött karakterlánc pedig a szabályzatban használható jogcím típusa lesz.

- **érték (karakterlánc)**: a jogcím értéke

- **value_type (karakterlánc)**: a jogcím értékének adattípusa

**service_context** (BASE64URL (oktettek)): a szolgáltatás által létrehozott átlátszatlan környezet.

### <a name="report-message"></a>Jelentés üzenete

#### <a name="direction"></a>Irány

Azure-igazolás – > ügyfél

#### <a name="payload"></a>Adattartalom

```
{
  "report": "<JWT>"
}
```

**jelentés** (JWT): az igazolási jelentés JSON web token (JWT) formátumban (RFC 7519).

## <a name="next-steps"></a>Következő lépések

- [Azure-igazolási munkafolyamat](workflow.md)

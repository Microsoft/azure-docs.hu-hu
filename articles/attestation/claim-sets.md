---
title: Az Azure igazolási jogcímek készletei
description: Az Azure-igazolás igénylési készlete.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 23bcfcb92a7fa642e111a67bf92c1306a606bb2a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704803"
---
# <a name="claim-sets"></a>Jogcímkészletek

A Microsoft Azure igazolást használó enklávék igazolása során létrehozott jogcímek az alábbi kategóriákba oszthatók:

- **Bejövő jogcímek**: Microsoft Azure igazolások által az igazolási bizonyítékok elemzése után generált jogcímek, valamint a szabályzatok szerzői által használható engedélyezési szabályok definiálása egyéni házirendben

- **Kimenő jogcímek**: az Azure-igazolás által generált jogcímek és az igazolási jogkivonatban az összes jogcímet tartalmazza

- **Property jogcímek**: az Azure-igazolás által kimenetként létrehozott jogcímek. Tartalmazza az igazolási jogkivonat tulajdonságait jelképező jogcímeket, például a jelentés kódolását, a jelentés érvényességi időtartamát stb.

### <a name="common-incoming-claims-across-all-attestation-types"></a>Gyakori bejövő jogcímek az összes igazolási típus között

Az alábbi jogcímeket az Azure igazolása hozza létre, és a szabályzatok szerzője az összes igazolási típushoz egyéni szabályzatban határozhat meg engedélyezési szabályokat.

- **x-MS-ver**: JWT séma verziója (várhatóan "1,0")
- **x-MS-igazolás-típus**: az igazolás típusát jelképező karakterlánc-érték 
- **x-MS-Policy-hash**: az Azure igazolási értékelési szabályzatának kivonata BASE64URL-ként kiszámítva (sha256 (UTF8 (BASE64URL (Policy Text))))
- **x-MS-Policy-aláíró**: JSON-objektum "jwk" taggal, amely a szabályzat aláírására használt ügyfelet jelöli, amikor az ügyfél feltölt egy aláírt szabályzatot

Az alábbi jogcímek elavultnak tekintendők, de teljes mértékben támogatottak. A nem elavult jogcím-nevek használata javasolt.

Elavult jogcím | Ajánlott jogcím 
--- | --- 
ver | x-MS-ver
póló | x-MS-igazolás-típus
Maa – policyHash | x-MS-Policy-hash
policy_hash | x-MS-Policy-hash
policy_signer | x-MS-Policy-aláíró

### <a name="common-outgoing-claims-across-all-attestation-types"></a>Általános kimenő jogcímek az összes igazolási típus között

Az alábbi jogcímek a szolgáltatás által az összes igazolási típushoz tartozó igazolási jogkivonat részét képezik.

Forrás: az [IETF JWT](https://tools.ietf.org/html/rfc7519) által meghatározottak szerint

- **"kezdeményezés" (JWT ID) jogcím**
- **"ISS" (kiállítói) jogcím**
- **"IAT" (kiadott) jogcím**
- **"exp" (lejárati idő) jogcím**
- **"NBF" (nem korábban) jogcím**

Forrás: [IETF](https://tools.ietf.org/html/draft-ietf-rats-eat-03#page-9) által meghatározott

- **"Alkalmi jogcím" (alkalom)**

Az alábbi jogcímeket a rendszer alapértelmezés szerint az igazolási jogkivonatban tartalmazza a bejövő jogcímek alapján:

- **x-MS-ver**: JWT séma verziója (várhatóan "1,0")
- **x-MS-igazolás-típus**: az igazolás típusát jelképező karakterlánc-érték 
- **x-MS-Policy-hash**: karakterlánc-érték, amely tartalmazza a BASE64URL által kiszámított sha256-kivonatot (sha256 (UTF8 (BASE64URL)))
- **x-MS-Policy-aláíró**: egy JWK tartalmaz, amely tartalmazza a nyilvános kulcsot, vagy az aláírt szabályzat fejlécében lévő tanúsítványlánc. x-MS-Policy-aláíró csak akkor lesz hozzáadva, ha a házirend aláírása megtörténik

## <a name="claims-specific-to-sgx-enclaves"></a>A SGX ENKLÁVÉHOZ enklávés-re vonatkozó jogcímek

### <a name="incoming-claims-specific-to-sgx-attestation"></a>SGX ENKLÁVÉHOZ-igazolásra vonatkozó bejövő jogcímek

Az alábbi jogcímeket az Azure igazolása hozza létre, és a szabályzatok szerzői a SGX ENKLÁVÉHOZ-igazoláshoz egyéni szabályzatban határozzák meg az engedélyezési szabályokat.

- **x-MS-SGX enklávéhoz-** a-hibakereső: logikai érték, amely azt jelzi, hogy az enklávéban engedélyezve van-e a hibakeresés
- **x-MS-SGX enklávéhoz-Product-ID**
- **x-MS-SGX enklávéhoz-mrsigner**: az idézőjel "mrsigner" mezőjének hexadecimális kódolású értéke
- **x-MS-SGX enklávéhoz-mrenclave**: az idézőjel "mrenclave" mezőjének hexadecimális kódolású értéke
- **x-MS-SGX enklávéhoz-svn**: az idézőjelbe kódolt biztonsági verziószám 

### <a name="outgoing-claims-specific-to-sgx-attestation"></a>A SGX ENKLÁVÉHOZ-igazolásra vonatkozó kimenő jogcímek

Az alábbi jogcímeket a szolgáltatás az igazolási jogkivonatban hozza létre és tartalmazza a SGX ENKLÁVÉHOZ-igazoláshoz.

- **x-MS-SGX enklávéhoz-** a-hibakereső: logikai érték, amely azt jelzi, hogy az enklávéban engedélyezve van-e a hibakeresés
- **x-MS-SGX enklávéhoz-Product-ID**
- **x-MS-SGX enklávéhoz-mrsigner**: az idézőjel "mrsigner" mezőjének hexadecimális kódolású értéke
- **x-MS-SGX enklávéhoz-mrenclave**: az idézőjel "mrenclave" mezőjének hexadecimális kódolású értéke
- **x-MS-SGX enklávéhoz-svn**: az idézőjelbe kódolt biztonsági verziószám 
- **x-MS-SGX enklávéhoz-EHD**: az enklávéban tárolt adat a BASE64URL-ként formázott (enklávéban tárolt adat)
- **x-MS-SGX enklávéhoz-biztosíték**: az igazolás végrehajtásához használt biztosítékot leíró JSON-objektum. Az x-MS-SGX enklávéhoz-biztosíték jogcím értéke beágyazott JSON-objektum a következő kulcs/érték párokkal:
    - **qeidcertshash**: a qe-identitás kiállító sha256 értéke
    - **qeidcrlhash**: a qe-identitás kiállítói tanúsítvány-visszavonási listájának sha256 értéke
    - **qeidhash**: a qe sha256 értéke
    - **quotehash**: a kiértékelt idézet sha256 értéke
    - **tcbinfocertshash**: a TCB információ kiállító tanúsítványának sha256 értéke
    - **tcbinfocrlhash**: az sha256 a TCB-adatok kiállító tanúsítványok CRL-listájának értéke
    - **tcbinfohash**: az igazolás végrehajtásához használt biztosítékot leíró JSON-objektum

Az alábbi jogcímek elavultnak tekintendők, de teljes mértékben támogatottak, és a jövőben is szerepelni fognak. A nem elavult jogcím-nevek használata javasolt.

Elavult jogcím | Ajánlott jogcím
--- | --- 
$is – hibakereső | x-MS-SGX enklávéhoz-a-hibakereső
$sgx – mrsigner | x-MS-SGX enklávéhoz-mrsigner
$sgx – mrenclave | x-MS-SGX enklávéhoz-mrenclave
$product azonosítója | x-MS-SGX enklávéhoz-Product-ID
$svn | x-MS-SGX enklávéhoz-svn
$tee | x-MS-igazolás-típus
Maa – EHD | x-MS-SGX enklávéhoz-EHD
AAS – EHD | x-MS-SGX enklávéhoz-EHD
Maa – attestationcollateral | x-MS-SGX enklávéhoz-biztosíték

## <a name="claims-specific-to-trusted-platform-module-tpm-vbs-attestation"></a>Platformmegbízhatósági modul (TPM)/VBS igazolásra vonatkozó jogcímek

### <a name="incoming-claims-for-tpm-attestation"></a>TPM-igazolás bejövő jogcímei

A TPM-igazoláshoz az Azure-igazolás által kiállított jogcímek. A jogcímek rendelkezésre állása az igazoláshoz megadott bizonyítékoktól függ.

- **aikValidated**: logikai érték, amely információt tartalmaz, ha az igazolási azonosító kulcs (AIK) tanúsítványa ellenőrizve lett vagy nem
- **aikPubHash**: a Base64-t tartalmazó karakterlánc (sha256 (AIK public key in der Format))
- **tpmVersion**: Integer érték, amely a PLATFORMMEGBÍZHATÓSÁGI modul (TPM) főverzióját tartalmazza
- **secureBootEnabled**: logikai érték, amely azt jelzi, hogy engedélyezve van-e a biztonságos rendszerindítás
- **iommuEnabled**: logikai érték, amely azt jelzi, hogy engedélyezve van-e a bemeneti kimeneti memória kezelési egysége (Iommu)
- **bootDebuggingDisabled**: logikai érték, amely azt jelzi, hogy le van-e tiltva a rendszerindítási hibakeresés
- **notSafeMode**: logikai érték, amely azt jelzi, hogy a Windows nem biztonságos módban fut-e
- **notWinPE**: logikai érték, amely azt jelzi, hogy a Windows nem a WinPE módban fut-e
- **vbsEnabled**: logikai érték, amely azt jelzi, hogy a vbs engedélyezve van-e
- **vbsReportPresent**: logikai érték, amely azt jelzi, hogy elérhető-e a vbs enklávé-jelentés

### <a name="incoming-claims-for-vbs-attestation"></a>A VBS-igazolás bejövő jogcímei

Az Azure-igazolás által a VBS igazoláshoz kiadott jogcímek a TPM-igazoláshoz elérhető jogcímek mellett is szerepelnek. A jogcímek rendelkezésre állása az igazoláshoz megadott bizonyítékoktól függ.

- **enclaveAuthorId**: az enklávé szerző azonosítójának Base64Url kódolt értékét tartalmazó karakterlánc-érték – az enklávé elsődleges moduljának szerzői azonosítója
- **enclaveImageId**: az enklávé rendszerkép-azonosítójának Base64Url kódolt értékét tartalmazó karakterlánc-érték – az enklávé elsődleges moduljának képazonosítója
- **enclaveOwnerId**: az enklávé tulajdonosi azonosítójának Base64Url kódolt értékét tartalmazó karakterlánc-érték – az enklávé tulajdonosának azonosítója
- **enclaveFamilyId**: az ENKLÁVÉ család azonosítójának Base64Url kódolt értékét tartalmazó karakterlánc-érték. Az enklávé elsődleges moduljának családjának azonosítója
- **enclaveSvn**: Integer érték, amely az enklávé elsődleges moduljának biztonsági verziószámát tartalmazza
- **enclavePlatformSvn**: egész szám, amely tartalmazza az enklávét működtető platform biztonsági verziószámát
- **enclaveFlags**: a enclaveFlags jogcím egy egész érték, amely az enklávé futtatókörnyezeti szabályzatát leíró jelzőket tartalmaz.

### <a name="outgoing-claims-specific-to-tpm-and-vbs-attestation"></a>TPM-és VBS-tanúsítványra vonatkozó kimenő jogcímek

- **cnf (megerősítés)**: a "cnf" jogcím azonosítja a rendelkezésére álló kulcs azonosítására szolgáló kulcsot. A megerősítő jogcím a 7800-as számú RFC-ben meghatározva, az igazolt enklávé kulcsának nyilvános része, amely JSON-webkulcs (JWK) objektumként szerepel (RFC 7517)
- **rp_data (függő entitások)**: a kérelemben megadott, függő entitások által használt, a kérésben szereplő, a függő entitás által a jelentés frissességének garantálása céljából felhasználva. rp_data csak akkor lesz hozzáadva, ha van rp_data

### <a name="property-claims"></a>Tulajdonság jogcímei

- **report_validity_in_minutes**: egy egész számból álló jogcím, amely azt jelzi, hogy mennyi ideig érvényes a jogkivonat.
  - **Alapértelmezett érték (Time)**: egy nap percben.
  - **Maximális érték (idő)**: egy év percben.
- **omit_x5c**: logikai jogcím, amely azt jelzi, hogy az Azure-igazolásnak ki kell-e hagyni a szolgáltatás hitelességének igazolásához használt tanúsítványt. Ha az értéke igaz, a rendszer hozzáadja a x5t az igazolási jogkivonathoz. Ha hamis (alapértelmezett), a rendszer hozzáadja a x5c az igazolási jogkivonathoz.

## <a name="next-steps"></a>Következő lépések
- [Igazolási szabályzat létrehozása és aláírása](author-sign-policy.md)
- [Az Azure-igazolás beállítása a PowerShell használatával](quickstart-powershell.md)

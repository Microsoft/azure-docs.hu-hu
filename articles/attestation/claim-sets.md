---
title: Az Azure igazolási jogcímek készletei
description: Az Azure-igazolás igénylési készlete.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 0d6d5a08ea85ebb666acc0336f1e1d7ec5e097da
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044668"
---
# <a name="claim-sets"></a>Jogcímkészletek

A Microsoft Azure igazolást használó enklávék igazolása során létrehozott jogcímek az alábbi kategóriákba oszthatók:

- **Bejövő jogcímek**: Microsoft Azure igazolások által az igazolási bizonyítékok elemzése után generált jogcímek, valamint a szabályzatok szerzői által használható engedélyezési szabályok definiálása egyéni házirendben

- **Kimenő jogcímek**: az Azure-igazolás által létrehozott és az igazolási jogkivonatban szereplő jogcímek

- **Property jogcímek**: az Azure-igazolás által kimenetként létrehozott jogcímek. Tartalmazza az igazolási jogkivonat tulajdonságait jelképező jogcímeket, például a jelentés kódolását, a jelentés érvényességi időtartamát stb.

## <a name="incoming-claims"></a>Bejövő jogcímek 

### <a name="sgx-attestation"></a>SGX ENKLÁVÉHOZ igazolása

A házirend-szerzők által a SGX ENKLÁVÉHOZ-igazolási szabályzatban meghatározott engedélyezési szabályok meghatározására szolgáló jogcímek:

- **x-MS-SGX enklávéhoz-** a-hibakereső: logikai érték, amely azt jelzi, hogy az enklávéban engedélyezve van-e a hibakeresés
- **x-MS-SGX enklávéhoz-Product-ID**: a SGX enklávéhoz enklávé termék-azonosító értéke 
- **x-MS-SGX enklávéhoz-mrsigner**: az idézőjel "mrsigner" mezőjének hexadecimális kódolású értéke
- **x-MS-SGX enklávéhoz-mrenclave**: az idézőjel "mrenclave" mezőjének hexadecimális kódolású értéke
- **x-MS-SGX enklávéhoz-svn**: az idézőjelbe kódolt biztonsági verziószám 

Az alábbi jogcímek elavultnak tekintendők, de teljes mértékben támogatottak, és a jövőben is szerepelni fognak. A nem elavult jogcím-nevek használata javasolt.

Elavult jogcím | Ajánlott jogcím
--- | --- 
$is – hibakereső | x-MS-SGX enklávéhoz-a-hibakereső
$product azonosítója | x-MS-SGX enklávéhoz-Product-ID
$sgx – mrsigner | x-MS-SGX enklávéhoz-mrsigner
$sgx – mrenclave | x-MS-SGX enklávéhoz-mrenclave
$svn | x-MS-SGX enklávéhoz-svn

### <a name="tpm-attestation"></a>TPM-igazolás

A házirend-szerzők által az engedélyezési szabályok definiálásához használt jogcímek a TPM-igazolási szabályzatban:

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

### <a name="vbs-attestation"></a>VBS-igazolás

A TPM-igazolási házirend jogcímein kívül a jogcímek az alábbi jogcímeket is használhatják az engedélyezési szabályok megadásához egy VBS igazolási szabályzatban.

- **enclaveAuthorId**: az enklávé szerző azonosítójának Base64Url kódolt értékét tartalmazó karakterlánc-érték – az enklávé elsődleges moduljának szerzői azonosítója
- **enclaveImageId**: az enklávé rendszerkép-azonosítójának Base64Url kódolt értékét tartalmazó karakterlánc-érték – az enklávé elsődleges moduljának képazonosítója
- **enclaveOwnerId**: az enklávé tulajdonosi azonosítójának Base64Url kódolt értékét tartalmazó karakterlánc-érték – az enklávé tulajdonosának azonosítója
- **enclaveFamilyId**: az ENKLÁVÉ család azonosítójának Base64Url kódolt értékét tartalmazó karakterlánc-érték. Az enklávé elsődleges moduljának családjának azonosítója
- **enclaveSvn**: Integer érték, amely az enklávé elsődleges moduljának biztonsági verziószámát tartalmazza
- **enclavePlatformSvn**: egész szám, amely tartalmazza az enklávét működtető platform biztonsági verziószámát
- **enclaveFlags**: a enclaveFlags jogcím egy egész érték, amely az enklávé futtatókörnyezeti szabályzatát leíró jelzőket tartalmaz.

## <a name="outgoing-claims"></a>Kimenő jogcímek 

### <a name="common-for-all-attestation-types"></a>Az összes igazolási típushoz közös

Az Azure igazolása az alábbi jogcímeket tartalmazza az igazolási jogkivonatban az összes igazolási típushoz. 

- **x-MS-ver**: JWT séma verziója (várhatóan "1,0")
- **x-MS-igazolás-típus**: az igazolás típusát jelképező karakterlánc-érték 
- **x-MS-Policy-hash**: az Azure igazolási értékelési szabályzatának kivonata BASE64URL-ként kiszámítva (sha256 (UTF8 (BASE64URL (Policy Text))))
- **x-MS-Policy-aláíró**: JSON-objektum egy "jwk" taggal, amely az ügyfél által a szabályzat aláírásához használt kulcsot jelképezi. Ez akkor alkalmazható, ha az ügyfél egy aláírt szabályzatot tölt fel

Az alábbi jogcím-nevek használhatók az [IETF JWT-specifikációból](https://tools.ietf.org/html/rfc7519)

- **"kezdeményezés" (JWT ID) jogcím** – egyedi azonosító a JWT
- **"ISS" (kiállítói) jogcím** – a JWT kiállító rendszerbiztonsági tag 
- **"IAT" (kiállított) jogcím** – az az idő, amikor a JWT kiadták 
- **"exp" (lejárati idő) jogcím** – lejárati idő, amely után a JWT nem fogadható el feldolgozásra
- **"NBF" (nem korábban) jogcím** – nem szükséges idő előtt, hogy a JWT nem fogadható el a feldolgozáshoz. 

Az alábbi jogcím-nevek az [IETF Eat draft-specifikációban](https://tools.ietf.org/html/draft-ietf-rats-eat-03#page-9) használatosak

- **"Egyszeres jogcím" (alkalom)** – egy ügyfél által megadott, nem átalakított közvetlen másolat 

Az alábbi jogcímek elavultnak tekintendők, de teljes mértékben támogatottak, és a jövőben is szerepelni fognak. A nem elavult jogcím-nevek használata javasolt.

Elavult jogcím | Ajánlott jogcím
--- | --- 
ver | x-MS-ver
póló | x-MS-igazolás-típus
policy_hash | x-MS-Policy-hash
Maa – policyHash | x-MS-Policy-hash
policy_signer  | x-MS-Policy-aláíró

### <a name="sgx-attestation"></a>SGX ENKLÁVÉHOZ igazolása 

Az alábbi jogcímeket a szolgáltatás az igazolási jogkivonatban hozza létre és tartalmazza a SGX ENKLÁVÉHOZ-igazoláshoz.

- **x-MS-SGX enklávéhoz-** a-hibakereső: logikai érték, amely azt jelzi, hogy az enklávéban engedélyezve van-e a hibakeresés
- **x-MS-SGX enklávéhoz-Product-ID**: a SGX enklávéhoz enklávé termék-azonosító értéke 
- **x-MS-SGX enklávéhoz-mrsigner**: az idézőjel "mrsigner" mezőjének hexadecimális kódolású értéke
- **x-MS-SGX enklávéhoz-mrenclave**: az idézőjel "mrenclave" mezőjének hexadecimális kódolású értéke
- **x-MS-SGX enklávéhoz-svn**: az idézőjelbe kódolt biztonsági verziószám 
- **x-MS-SGX enklávéhoz-EHD**: az enklávéban tárolt adat a BASE64URL-ként formázott (enklávéban tárolt adat)
- **x-MS-SGX enklávéhoz-biztosíték**: az igazolás végrehajtásához használt biztosítékot leíró JSON-objektum. Az x-MS-SGX enklávéhoz-biztosíték jogcím értéke beágyazott JSON-objektum a következő kulcs/érték párokkal:
    - **qeidcertshash**: az sha256 (qe) identitás kiállító tanúsítványának értéke
    - **qeidcrlhash**: a qe-identitás kiállítói tanúsítvány-visszavonási listájának sha256 értéke
    - **qeidhash**: a qe sha256 értéke
    - **quotehash**: a kiértékelt idézet sha256 értéke
    - **tcbinfocertshash**: a TCB információ kiállító tanúsítványának sha256 értéke
    - **tcbinfocrlhash**: az sha256 a TCB-adatok kiállító tanúsítványok CRL-listájának értéke
    - **tcbinfohash**: a TCB információs biztosíték sha256 értéke

Az alábbi jogcímek elavultnak tekintendők, de teljes mértékben támogatottak, és a jövőben is szerepelni fognak. A nem elavult jogcím-nevek használata javasolt.

Elavult jogcím | Ajánlott jogcím
--- | --- 
$is – hibakereső | x-MS-SGX enklávéhoz-a-hibakereső
$product azonosítója | x-MS-SGX enklávéhoz-Product-ID
$sgx – mrsigner | x-MS-SGX enklávéhoz-mrsigner
$sgx – mrenclave | x-MS-SGX enklávéhoz-mrenclave
$svn | x-MS-SGX enklávéhoz-svn
$maa – EHD | x-MS-SGX enklávéhoz-EHD
$aas – EHD | x-MS-SGX enklávéhoz-EHD
$maa – attestationcollateral | x-MS-SGX enklávéhoz-biztosíték

### <a name="tpm-and-vbs-attestation"></a>TPM-és VBS-igazolás

- **cnf (megerősítés)**: a "cnf" jogcím azonosítja a rendelkezésére álló kulcs azonosítására szolgáló kulcsot. A megerősítő jogcím a 7800-as számú RFC-ben meghatározva, az igazolt enklávé kulcsának nyilvános része, amely JSON-webkulcs (JWK) objektumként szerepel (RFC 7517)
- **rp_data (függő entitások)**: a kérelemben megadott, függő entitások által használt, a kérésben szereplő, a függő entitás által a jelentés frissességének garantálása céljából felhasználva. rp_data csak akkor lesz hozzáadva, ha van rp_data

## <a name="property-claims"></a>Tulajdonság jogcímei

### <a name="tpm-and-vbs-attestation"></a>TPM-és VBS-igazolás

- **report_validity_in_minutes**: egy egész számból álló jogcím, amely azt jelzi, hogy mennyi ideig érvényes a jogkivonat.
  - **Alapértelmezett érték (Time)**: egy nap percben.
  - **Maximális érték (idő)**: egy év percben.
- **omit_x5c**: logikai jogcím, amely azt jelzi, hogy az Azure-igazolásnak ki kell-e hagyni a szolgáltatás hitelességének igazolásához használt tanúsítványt. Ha az értéke igaz, a rendszer hozzáadja a x5t az igazolási jogkivonathoz. Ha hamis (alapértelmezett), a rendszer hozzáadja a x5c az igazolási jogkivonathoz.

## <a name="next-steps"></a>Következő lépések
- [Igazolási szabályzat létrehozása és aláírása](author-sign-policy.md)
- [Az Azure-igazolás beállítása a PowerShell használatával](quickstart-powershell.md)

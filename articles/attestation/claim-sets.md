---
title: Azure Attestation jogcímkészletek
description: A jogcímkészletek Azure Attestation.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: e82e9fc93bf8c816fcbfd5869156745dea630313
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517555"
---
# <a name="claim-sets"></a>Jogcímkészletek

Az enklávak igazolásának folyamata során létrehozott jogcímek Microsoft Azure az alábbi kategóriákra oszthatók:

- **Bejövő jogcímek:** Az igazolási igazolás Microsoft Azure által létrehozott jogcímek, és a házirend szerzői az engedélyezési szabályok egyéni házirendben való meghatározásához használhatók

- **Kimenő jogcímek:** Az Azure Attestation által létrehozott és az igazolási jogkivonatban található jogcímek

- **Tulajdonság jogcímek:** A következő által kimenetként létrehozott Azure Attestation. Ez tartalmazza az igazolási jogkivonat tulajdonságait képviselő összes jogcímet, például a jelentés kódolását, a jelentés érvényességi időtartamát stb.

## <a name="incoming-claims"></a>Bejövő jogcímek 

### <a name="sgx-attestation"></a>SGX-igazolás

A házirend-szerzők által az SGX-igazolási házirendek engedélyezési szabályainak meghatározásához használt jogcímek:

- **x-ms-sgx-is-debuggable:** Logikai érték, amely azt jelzi, hogy az enklávé-hibakeresés engedélyezve van-e.
  
  Az SGX-enklávek betölthetőek úgy, hogy a hibakeresés le van tiltva vagy engedélyezve van. Ha a jelző true (igaz) értékre van állítva az enklávéban, lehetővé teszi az enklávékód hibakeresési funkcióit. Ez magában foglalja az enklávé memóriája elérésének képességét. Ezért javasoljuk, hogy a jelzőt csak fejlesztési célokra állítsa true (igaz) értékre. Ha éles környezetben engedélyezi, az SGX biztonsági garanciák nem maradnak meg.
  
  Azure Attestation az igazolási szabályzat segítségével ellenőrizhetik, hogy az SGX-enklávéban le van-e tiltva a hibakeresés. A szabályzatszabály hozzáadása után az igazolás sikertelen lesz, ha egy rosszindulatú felhasználó bekapcsolja a hibakeresés támogatását, hogy hozzáférjen az enklávé tartalmához.

- **x-ms-sgx-product-id:** Egész szám, amely az SGX-enklávé termékazonosítóját jelzi.

  Az enklávé szerzője minden enklávéhoz termékazonosítót rendel. A termékazonosító lehetővé teszi, hogy az enklávé szerzője szegmentálja az ugyanazokkal az MRSIGNER-ekkel aláírt enklávékat. Ha érvényesítési szabályt ad hozzá az igazolási szabályzathoz, az ügyfelek ellenőrizhetik, hogy a kívánt enklávokat használják-e. Az igazolás sikertelen lesz, ha az enklávé termékazonosítója nem egyezik meg az enklávé szerzője által közzétett értékkel.

- **x-ms-sgx-mrsigner:** Egy sztringérték, amely az SGX enklávé szerzőjének azonosítására alkalmas.

  Az MRSIGNER az enklávé szerzőjének nyilvános kulcsának kivonata, amely az enklávé bináris fájl aláírására használatos. Az MRSIGNER igazolási szabályzaton keresztüli ellenőrzéssel az ügyfelek ellenőrizhetik, hogy a megbízható bináris fájlok egy enklávéban futnak-e. Ha a szabályzat jogcíme nem egyezik meg az enklávé szerzőjének MRSIGNER-jéhez, az azt jelenti, hogy az enklávé bináris fájlját nem megbízható forrás írta alá, és az igazolás sikertelen lesz.
  
  Ha egy enklávé szerzője biztonsági okokból inkább az MRSIGNER-t szeretné váltogatni, Azure Attestation szabályzatot frissíteni kell, hogy támogassa az új és régi MRSIGNER-értékeket a bináris fájlok frissítése előtt. Ellenkező esetben az engedélyezési ellenőrzések sikertelenek lesznek, ami igazolási hibákat eredményez.
  
  Az igazolási szabályzatot az alábbi formátumban kell frissíteni. 
 
  #### <a name="before-key-rotation"></a>Kulcsrotáció előtt
 
   ```
    version= 1.0;
    authorizationrules 
    {
    [ type=="x-ms-sgx-is-debuggable", value==false]&&
    [ type=="x-ms-sgx-mrsigner", value=="mrsigner1"] => permit(); 
    };
  ```

   #### <a name="during-key-rotation"></a>Kulcsrotáció során

    ```
      version= 1.0;
      authorizationrules 
      {
      [ type=="x-ms-sgx-is-debuggable", value==false]&&
      [ type=="x-ms-sgx-mrsigner", value=="mrsigner1"] => permit(); 
      [ type=="x-ms-sgx-is-debuggable", value==false ]&& 
      [ type=="x-ms-sgx-mrsigner", value=="mrsigner2"] => permit(); 
      };
    ```

   #### <a name="after-key-rotation"></a>Kulcsrotáció után

    ```
      version= 1.0;
      authorizationrules 
      { 
      [ type=="x-ms-sgx-is-debuggable", value==false]&& 
      [ type=="x-ms-sgx-mrsigner", value=="mrsigner2"] => permit(); 
      };
    ```

- **x-ms-sgx-mrenclave:** Egy sztringérték, amely azonosítja az enklávémemóba betöltött kódot és adatokat. 

  Az MRENCLAVE az enklávémérések egyike, amely az enklávé bináris fájlok ellenőrzéséhez használható. Ez az enklávéban futó kód kivonata. A mérés az enklávé bináris kód minden változásával változik. Az MRENCLAVE igazolási szabályzaton keresztüli ellenőrzéssel az ügyfelek ellenőrizhetik, hogy a kívánt bináris fájlok egy enklávéban futnak-e. Mivel azonban az MRENCLAVE várhatóan gyakran változik a meglévő kód triviális módosításakor, javasoljuk, hogy ellenőrizze az enklávé bináris fájlokat az MRSIGNER-érvényesítés használatával egy igazolási szabályzatban.

- **x-ms-sgx-svn:** Egész szám, amely az SGX-enklávé biztonsági verziószámát jelzi

  Az enklávé szerzője biztonsági verziószámot (SVN) rendel hozzá az SGX-enklávé minden verziójához. Ha biztonsági problémát fedeznek fel az enklávé kódban, az enklávé szerzője növeli az SVN értékét a biztonsági rések kijavítás után. A nem biztonságos enklávékóddal való interakció megakadályozása érdekében az ügyfelek érvényesítési szabályt adhatnak hozzá az igazolási szabályzathoz. Ha az enklávékód SVN-je nem egyezik meg az enklávé szerzője által javasolt verzióval, az igazolás sikertelen lesz.

Az alábbi jogcímek elavultnak minősülnek, de teljes mértékben támogatottak, és a jövőben is szerepelni fognak benne. Javasoljuk, hogy használja a nem elavult jogcímneveket.

Elavult jogcím | Ajánlott jogcím
--- | --- 
$is hibakeresésre használható | x-ms-sgx-is-debuggable
$product-id | x-ms-sgx-product-id
$sgx-mrsigner | x-ms-sgx-mrsigner
$sgx-mrenclave | x-ms-sgx-mrenclave
$svn | x-ms-sgx-svn

### <a name="tpm-attestation"></a>TPM-igazolás

A házirend-szerzők által a TPM-igazolási házirendek engedélyezési szabályainak meghatározásához használt jogcímek:

- **aikValidated:** Olyan logikai érték, amely információt tartalmaz, ha az igazolási identitáskulcs (AIK) tanúsítványa érvényesítve lett-e vagy sem
- **aikPubHash:** A base64(SHA256(AIK nyilvános kulcs DER formátumban) sztring
- **tpmVersion:** Egész szám, amely a platformmegbízhatósági modul (TPM) főverziót tartalmazza
- **secureBootEnabled:** Logikai érték, amely azt jelzi, hogy engedélyezve van-e a biztonságos rendszerindítás
- **iommuEnabled:** Logikai érték, amely azt jelzi, hogy engedélyezve van-e az Input-output memóriakezelési egység (Iommu).
- **bootDebuggingDisabled:** Logikai érték, amely azt jelzi, hogy a rendszerindítási hibakeresés le van-e tiltva
- **notSafeMode:** Logikai érték, amely azt jelzi, hogy a Windows nem csökkentett módban fut-e
- **notWinPE:** Logikai érték, amely azt jelzi, hogy a Windows nem WinPE módban fut-e
- **vbsEnabled:** Logikai érték, amely azt jelzi, hogy a VBS engedélyezve van-e
- **vbsReportPresent:** Logikai érték, amely azt jelzi, hogy elérhető-e a VBS enklávéjelentés

### <a name="vbs-attestation"></a>VBS-igazolás

A TPM-igazolási házirend jogcímei mellett az alábbi jogcímeket a házirend szerzői a VBS-igazolási házirendek engedélyezési szabályainak meghatározására is használhatjak.

- **enclaveAuthorId:** Az enklávé szerzőazonosítójának Base64Url kódolású értékét tartalmazó sztringérték – Az enklávé elsődleges moduljának szerzőazonosítója
- **enclaveImageId:** Az enklávékép azonosítójának Base64Url kódolású értékét tartalmazó sztringérték – Az enklávé elsődleges moduljának képazonosítója
- **enclaveOwnerId:** Az enklávétulajdonos azonosítójának Base64Url kódolású értékét tartalmazó sztringérték – Az enklávé tulajdonosának azonosítója
- **enclaveFamilyId:** Az enklávécsalád azonosítójának Base64Url kódolású értékét tartalmazó sztringérték. Az enklávé elsődleges moduljának családazonosítója
- **enclaveSvn:** Egész szám, amely az enklávé elsődleges modulja biztonsági verziószámát tartalmazza
- **enclavePlatformSvn:** Egész szám, amely az enklávét tároló platform biztonsági verziószámát tartalmazza
- **enclaveFlags:** Az enclaveFlags jogcím egy egész szám érték, amely az enklávé futásidejű szabályzatát leíró jelzőket tartalmazza

## <a name="outgoing-claims"></a>Kimenő jogcímek 

### <a name="common-for-all-attestation-types"></a>Minden igazolási típus esetében közös

Azure Attestation az alábbi jogcímeket tartalmazza az igazolási jogkivonatban minden igazolási típushoz. 

- **x-ms-ver:** JWT sémaverzió (várhatóan "1.0")
- **x-ms-attestation-type:** Az igazolás típusát képviselő sztringérték 
- **x-ms-policy-hash:** AZURE ATTESTATION BASE64URL(SHA256(UTF8(BASE64URL(UTF8(policy text)) számítási kiértékelési szabályzat kivonata)
- **x-ms-policy-signer: JSON-objektum** egy "jwk" taggal, amely azt a kulcsot képviseli, amely az ügyfél által a szabályzat aláírásához használt. Ez akkor érvényes, ha az ügyfél aláírt szabályzatot tölt fel

Az alábbi jogcímneveket az [IETF JWT specifikációja használja](https://tools.ietf.org/html/rfc7519)

- **"jti" (JWT-azonosító) jogcím** – A JWT egyedi azonosítója
- **"iss" (Kiállító) jogcím** – A JWT-t kiállító rendszerbiztonsági tag 
- **"iat" (Kibocsátott időpont) jogcím** – A JWT kibocsátásának ideje a következő időpontban: 
- **"exp" (Lejárati idő) jogcím** – Lejárati idő, amely után a JWT nem fogadható el feldolgozásra
- **"nbf" (Nem előtte) jogcím** – Nem olyan időpont előtt, amely előtt a JWT nem fogadható el feldolgozásra 

Az alábbi jogcímneveket az [IETF EGYED-vázlat specifikációja használja](https://tools.ietf.org/html/draft-ietf-rats-eat-03#page-9)

- **"Nonce claim" (nonce) –** Egy ügyfél által megadott választható nonce érték nem lefordított közvetlen másolata 

Az alábbi jogcímek elavultnak minősülnek, de teljes mértékben támogatottak, és a jövőben is szerepelni fognak benne. Javasoljuk, hogy használja a nem elavult jogcímneveket.

Elavult jogcím | Ajánlott jogcím
--- | --- 
Ver | x-ms-ver
Póló | x-ms-attestation-type
policy_hash | x-ms-policy-hash
maa-policyHash | x-ms-policy-hash
policy_signer  | x-ms-policy-signer

### <a name="sgx-attestation"></a>SGX-igazolás 

Az alábbi jogcímeket az SGX-igazolás szolgáltatása állítja elő és tartalmazza az igazolási jogkivonatban.

- **x-ms-sgx-is-debuggable:** Logikai érték, amely azt jelzi, hogy az enklávéban engedélyezve van-e a hibakeresés
- **x-ms-sgx-product-id:** az SGX enklávé termékazonosítója 
- **x-ms-sgx-mrsigner:** az idézőjel "mrsigner" mezőjének hexikus kódolású értéke
- **x-ms-sgx-mrenclave:** az idézőjel "mrenclave" mezőjének hexikus kódolású értéke
- **x-ms-sgx-svn:** az idézőjelben kódolt biztonsági verziószám 
- **x-ms-sgx-ehd:** enklávéban található adatok BASE64URL(enklávéban lévő adatok) formátumban
- **x-ms-sgx-egyentetve: JSON-objektum,** amely leírja az igazolás végrehajtásához használt megfelelőt. Az x-ms-sgx-egyedek jogcímének értéke egy beágyazott JSON-objektum, amely a következő kulcs/érték párokat tartalmazza:
    - **qeidcertshash:** A tanúsítványokat kiállító enklávé (QE) azonosító SHA256 értéke
    - **qeidcrlhash:** A tanúsítványokat kiállító QE-identitás SHA256-értéke CRL-lista
    - **qeidhash:** A QE-identitás sha256 értéke
    - **quotehash:** a kiértékelt ajánlat SHA256 értéke
    - **tcbinfocertshash:** A TCB Info kiállító tanúsítványának SHA256 értéke
    - **tcbinfocrlhash:** A TCB Info kiállító tanúsítvány CRL-listájának SHA256 értéke
    - **tcbinfohash:** A TCB-információ kieső SHA256-értéke

Az alábbi jogcímek elavultnak minősülnek, de teljes mértékben támogatottak, és a jövőben is szerepelni fognak benne. Javasoljuk, hogy használja a nem elavult jogcímneveket.

Elavult jogcím | Ajánlott jogcím
--- | --- 
$is hibakeresésre használható | x-ms-sgx-is-debuggable
$product-id | x-ms-sgx-product-id
$sgx-mrsigner | x-ms-sgx-mrsigner
$sgx-mrenclave | x-ms-sgx-mrenclave
$svn | x-ms-sgx-svn
$maa-ehd | x-ms-sgx-ehd
$aas-ehd | x-ms-sgx-ehd
$maa-attestationcollateral | x-ms-sgx-egyedek

### <a name="tpm-and-vbs-attestation"></a>TPM- és VBS-igazolás

- **cnf (Megerősítés):** A "cnf" jogcím a tulajdonlás igazolási kulcsának azonosítására használható. Megerősítő jogcím az RFC 7800 szabványban meghatározottak szerint, amely az igazolt enklávékulcs nyilvános részét tartalmazza, amelyet egy JSON-webkulcs (JWK) objektumként (RFC 7517) képviselt.
- **rp_data (függő adatokat)**: Függőben található adatok , ha van ilyen, a kérelemben megadottak szerint, és a függő függő fél a jelentés frissességét garantálja. rp_data csak akkor lesz hozzáadva, ha van rp_data

## <a name="property-claims"></a>Tulajdonság jogcímek

### <a name="tpm-and-vbs-attestation"></a>TPM- és VBS-igazolás

- **report_validity_in_minutes:** Egész szám jogcím, amely azt írja le, hogy mennyi ideig érvényes a jogkivonat.
  - **Alapértelmezett érték(idő):** Egy nap percben.
  - **Maximális érték (idő)**: Egy év percben.
- **omit_x5c:** Logikai jogcím, amely azt jelzi, Azure Attestation kell-e kihagyni a szolgáltatás hitelességének igazolására használt tanúsítványt. Ha igaz, az x5t hozzá lesz adva az igazolási jogkivonathoz. Ha a false (alapértelmezett) érték van megadva, a rendszer hozzáadja az x5c-t az igazolási jogkivonathoz.

## <a name="next-steps"></a>Következő lépések
- [Igazolási szabályzat írása és aláírása](author-sign-policy.md)
- [Az alkalmazás Azure Attestation PowerShell használatával](quickstart-powershell.md)

---
title: Azure IoT Hub Device Provisioning Service – TPM-igazolás
description: Ez a cikk a TPM-igazolási folyamatnak a IoT Device kiépítési szolgáltatás (DPS) használatával történő fogalmi áttekintését tartalmazza.
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 12860629d78391ed271306daba29a51aeb326c1d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90531593"
---
# <a name="tpm-attestation"></a>TPM-igazolás

A IoT Hub Device Provisioning Service egy olyan IoT Hub segítő szolgáltatás, amellyel a nulla érintéses eszköz kiépíthető a megadott IoT hubhoz. A Device kiépítési szolgáltatással több millió eszköz biztonságos módon is kiépíthető.

Ez a cikk platformmegbízhatósági modul (TPM) használatakor ismerteti az identitás igazolási folyamatát. A TPM a hardveres biztonsági modul (HSM) típusa. Ez a cikk feltételezi, hogy diszkrét, belső vezérlőprogramot vagy integrált TPM-t használ. A szoftveresen emulált TPM a prototípus-készítéshez és teszteléshez alkalmasak, de nem biztosítják ugyanazt a biztonsági szintet, mint a diszkrét, a belső vezérlőprogram vagy az integrált TPM. A szoftveres TPM nem ajánlott éles környezetben használni. A TPM típusaival kapcsolatos további információkért tekintse [meg a TPM rövid bemutatása](https://trustedcomputinggroup.org/wp-content/uploads/TPM-2.0-A-Brief-Introduction.pdf)című témakört.

Ez a cikk csak azokra az eszközökre vonatkozik, amelyek a TPM 2,0-es verziójú HMAC és azok hátirati kulcsaival rendelkeznek. A hitelesítéshez X. 509 tanúsítványokat használó eszközök esetében nem. A TPM a Trusted Computing Group iparági szintű, ISO szabványa, és a TPM a [teljes tpm 2,0 specifikációban](https://trustedcomputinggroup.org/tpm-library-specification/) , illetve az [ISO/IEC 11889 specifikációban](https://www.iso.org/standard/66510.html)is olvasható. A cikk azt is feltételezi, hogy ismeri a nyilvános és a titkos kulcs párokat, valamint azt, hogyan használják őket a titkosításhoz.

Az eszköz kiépítési szolgáltatásához tartozó SDK-k az ebben a cikkben ismertetett mindent kezelik. Ha az SDK-kat használja az eszközökön, nincs szükség további további megvalósításra. Ebből a cikkből megtudhatja, hogyan tekintheti meg a TPM biztonsági chipet, amikor az eszköz kiépíti a biztonságot, és hogy miért biztonságos.

## <a name="overview"></a>Áttekintés

A TPM használja a megbízhatósági kapcsolat biztonságos gyökerének nevezett hátirat kulcs (EK) használatát. Az EK egyedi a TPM-ben, és a módosítása lényegében megváltoztatja az eszközt egy újat.

Van egy másik típusú kulcs is, amelyet a TPM (a Storage root kulcsának) nevezünk. A rendszer a TPM tulajdonosa által a TPM tulajdonosának megtartása után generálhatja a rendszerbiztonsági frissítést. A TPM tulajdonjogának átvétele a TPM-specifikus módszer, amely azt jelzi, hogy a "valaki jelszót állít be a HSM-ben". Ha a TPM-eszközt új tulajdonosnak értékesíti, az új felhasználói tulajdonba veheti a TPM-t, hogy létrehozzon egy újat. Az új bejelentési művelet biztosítja, hogy az előző tulajdonos nem használhatja a TPM-t. Mivel a kiállítók egyediek a TPM tulajdonosa számára, a rendszer felhasználhatja az adatzárolást a TPM modulba az adott tulajdonos számára. A revocability egy Sandboxot biztosít a tulajdonos számára, hogy tárolja a kulcsokat, és hozzáférést biztosít a hozzáféréshez, ha az eszközt vagy a TPM-t eladják. Ez olyan, mint egy új házba való átállás: a tulajdonjog megváltoztatásával megváltoztatja az ajtók zárolásait, és megsemmisíti az előző tulajdonosok által hagyott összes bútort, de a ház (EK) címe nem módosítható.

Ha egy eszköz be van állítva, és használatra kész állapotban van, akkor a rendszer mind az EK-t, mind a következőt fogja használni.

![TPM tulajdonjogának átvétele](./media/concepts-tpm-attestation/tpm-ownership.png)

Egy Megjegyzés a TPM tulajdonjogának átvételéhez: a TPM tulajdonjogának átvétele számos dologtól függ, többek között a TPM gyártójától, a használt TPM-eszközök készlettől és az eszköz operációs rendszertől. A tulajdonba vételhez kövesse a rendszerrel kapcsolatos utasításokat.

A Device kiépítési szolgáltatás az EK (EK_pub) nyilvános részét használja az eszközök azonosításához és regisztrálásához. Az eszköz gyártója elolvashatja a EK_pub a gyártás vagy a végső tesztelés során, és feltöltheti a EK_pubt a kiépítési szolgáltatásba, hogy az eszköz felismerhető legyen a létesítéshez való csatlakozáskor. Az eszköz kiépítési szolgáltatása nem vizsgálja meg a beolvasást vagy a tulajdonost, így az "Törlés" a TPM törli a vásárlói adatmennyiséget, de az EK-t (és az egyéb szállítói adatmennyiséget) megőrzi, és az eszköz kiépítési szolgáltatása továbbra is felismeri az eszközt a létesítéshez való csatlakozáskor.

## <a name="detailed-attestation-process"></a>Részletes igazolási folyamat

Ha egy TPM-eszköz először csatlakozik a Device kiépítési szolgáltatáshoz, a szolgáltatás először a beléptetési listán tárolt EK_pub ellenőrzi a megadott EK_pub. Ha a EK_pubs nem egyeznek, az eszköz nem jogosult a létesítésre. Ha a EK_pubs megegyeznek, a szolgáltatásnak szüksége van arra, hogy az eszköz egy egyszeres kihívással igazolja az EK privát részének tulajdonjogát, amely az identitás bizonyítására szolgáló biztonságos kihívás. Az eszköz-kiépítési szolgáltatás létrehoz egy egyszeres, majd titkosítja azt a kiindulási ponttal, majd a EK_pub, amelyek közül mindkettőt az eszköz a kezdeti regisztrációs hívás során biztosít. A TPM mindig az EK biztonságos magánhálózati részét tartja. Ez megakadályozza a hamisítást, és biztosítja, hogy az SAS-tokenek biztonságosan legyenek kiépítve a jogosult eszközökhöz.

Lássuk részletesen az igazolási folyamatot.

### <a name="device-requests-an-iot-hub-assignment"></a>Az eszköz IoT Hub-hozzárendelést kér

Először az eszköz csatlakozik a Device kiépítési szolgáltatáshoz, és kéri a létesítést. Ennek során az eszköz megadja a szolgáltatást a regisztrációs azonosítójával, egy azonosító hatókörrel, valamint a TPM EK_pub és SRK_pubával. A szolgáltatás visszaküldi a titkosított időegységet az eszközre, és arra kéri az eszközt, hogy fejtse vissza az egyszer használatos azonosítót, és használja az SAS-token aláírására a kapcsolat újbóli csatlakoztatásához és a létesítés befejezéséhez

![Eszközre vonatkozó kérelmek kiépítés](./media/concepts-tpm-attestation/step-one-request-provisioning.png)

### <a name="nonce-challenge"></a>Egyalkalomos kihívás

Az eszköz elveszi az Egypéldányos használatát, és az EK és a kihelyezés privát részeit használja az alkalomnak a TPM-ben való visszafejtéséhez. az Egypéldányos titkosítási sorrendje megbízhatónak minősíti a megbízhatóságot az EK-ból, amely nem módosítható a kiállítók között, ami megváltoztathatja, ha egy új tulajdonos a TPM tulajdonjogát veszi át.

![Az alkalom visszafejtése](./media/concepts-tpm-attestation/step-two-nonce.png)

### <a name="validate-the-nonce-and-receive-credentials"></a>Az alkalmi és a fogadási hitelesítő adatok ellenőrzése

Az eszköz ezután aláírhat egy SAS-jogkivonatot a visszafejtett alkalom használatával, és visszaállíthatja a kapcsolatot az eszköz kiépítési szolgáltatásával az aláírt SAS-token használatával. Az egyszeres kihívás befejezése után a szolgáltatás lehetővé teszi, hogy az eszköz kiépíthető legyen.

![Az eszköz újra létrehozza a kapcsolatot az eszköz kiépítési szolgáltatásával az EK tulajdonjogának ellenőrzéséhez](./media/concepts-tpm-attestation/step-three-validation.png)

## <a name="next-steps"></a>Következő lépések

Az eszköz most csatlakozik IoT Hubhoz, és biztonságban van, hogy az eszközök kulcsai biztonságosan legyenek tárolva. Most, hogy tudja, hogy az eszköz kiépítési szolgáltatása hogyan biztonságos módon ellenőrzi az eszköz azonosítóját a TPM használatával, tekintse meg a következő cikkeket további információért:

* [Ismerje meg a kiépítés fogalmait](about-iot-dps.md#provisioning-process)
* Az [automatikus kiépítés használatának első lépései](./quick-setup-auto-provision.md) az SDK-k használatával a folyamat kezeléséhez.

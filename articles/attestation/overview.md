---
title: Az Azure igazolásának áttekintése
description: Az Microsoft Azure igazolásának áttekintése, amely a megbízható végrehajtási környezetek (pólók) igazolására szolgáló megoldás
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: references_regions
ms.openlocfilehash: 85585b771d9c0ed7c6fcdba9cfef7b589a987c8c
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99429262"
---
# <a name="microsoft-azure-attestation"></a>Microsoft Azure Attestation 

Az Microsoft Azure igazolás egy egységes megoldás, amely távolról ellenőrzi a platform megbízhatóságát és a rajta futó bináris fájlok integritását. A szolgáltatás támogatja a platformmegbízhatósági modulok (TPM-EK) által támogatott platformok igazolását, és lehetővé teszi a megbízható végrehajtási környezetek (pólók) állapotának igazolását, mint például az [Intel® Software Guard Extensions](https://www.intel.com/content/www/us/en/architecture-and-technology/software-guard-extensions.html) (SGX enklávéhoz) enklávék és a [virtualizálás-alapú biztonsági](/windows-hardware/design/device-experiences/oem-vbs) (vbs) enklávék. 

Az igazolás egy folyamat, amely azt mutatja be, hogy a szoftver bináris fájljai megfelelően lettek-e létrehozva egy megbízható platformon. A távoli függő entitások így biztosak lehetnek abban, hogy csak az ilyen jellegű szoftverek megbízható hardveren futnak. Az Azure-igazolás egy egységesített ügyfél-szolgáltatás és keretrendszer az igazoláshoz.

Az Azure-igazolás lehetővé teszi az olyan élvonalbeli biztonsági paradigmák használatát, mint az [Azure bizalmas számítástechnika](../confidential-computing/overview.md) és az intelligens peremhálózat védelme. Ügyfeleinknek lehetősége van arra, hogy egymástól függetlenül ellenőrizzék a gép helyét, a virtuális gép (VM) helyzetét a gépen, valamint azt a környezetet, amelyben az enklávék futnak a virtuális gépen. Az Azure igazolása ezeket és számos további ügyfél-kérelmet is felruház.

Az Azure igazolása befogadja a számítási entitásokból származó bizonyítékokat, bekapcsolja őket egy készletbe, érvényesíti azokat a konfigurálható házirendekkel, és kriptográfiai igazolásokat állít elő a jogcímbarát alkalmazásokhoz (például a függő entitásokhoz és a naplózási hatóságokhoz).

## <a name="use-cases"></a>Használati esetek

Az Azure igazolása átfogó igazolási szolgáltatásokat nyújt több környezethez és megkülönböztető használati esetekhez.

### <a name="sgx-attestation"></a>SGX ENKLÁVÉHOZ igazolása

A SGX ENKLÁVÉHOZ olyan hardveres elkülönítésre utal, amely bizonyos Intel CPU-modellekben támogatott. A SGX ENKLÁVÉHOZ lehetővé teszi, hogy a kód a SGX ENKLÁVÉHOZ enklávés néven ismert, megtisztított rekeszekben fusson. A hozzáférés-és a memória-engedélyeket a hardver kezeli, így biztosítva a megfelelő elkülönítést biztosító minimális támadási felületet.

Az ügyfélalkalmazások úgy is kiállíthatók, hogy kihasználják a SGX ENKLÁVÉHOZ enklávék előnyeit azáltal, hogy a biztonság szempontjából kényes feladatokat delegálnak az adott enklávékban. Az ilyen alkalmazások ezt követően az Azure-igazolás használatával rutinszerűen hozhatnak létre megbízhatóságot az enklávéban, és képesek hozzáférni a bizalmas adatokhoz.

### <a name="open-enclave"></a>Nyitott enklávé
Az [Open enklávé](https://openenclave.io/sdk/) (OE) olyan kódtárak gyűjteménye, amelyek egyetlen egységes enclaving absztrakciót hoznak létre a fejlesztők számára a Tee-alapú alkalmazások létrehozásához. Univerzális biztonságos alkalmazás-modellt kínál, amely a platform sajátosságait is lekicsinyíti. A Microsoft fontos lépésként tekinti át a demokratizálása-alapú enklávé-technológiákat, például a SGX ENKLÁVÉHOZ, és növeli az Azure-ban való felvételét.

Az OE az enklávé-tanúsítványok ellenőrzésére vonatkozó konkrét követelményeket Szabványosít. Ez megfelel az OE-nek, amely az Azure-igazolások kiválóan illeszkedő tanúsítvány-felhasználója.

### <a name="tpm-attestation"></a>TPM-igazolás 

A platformmegbízhatósági modul (TPM) alapú igazolás kritikus fontosságú a platformok állapotának igazolásához. A TPM a megbízhatóság gyökerének és a biztonsági munkafolyamatnak a mérések (bizonyíték) titkosítási érvényességének biztosítására szolgál. A TPM-sel rendelkező eszközök igazolják az igazolást, hogy igazolják, hogy a rendszerindítási integritás nem sérül, és nem sérült meg a szolgáltatás állapotának észlelése a rendszerindításkor. 

Az ügyfélalkalmazások úgy is kiállíthatók, hogy kihasználják a TPM-igazolás előnyeit azáltal, hogy a biztonsági szempontból kényes feladatokat csak akkor végzik el, ha a platformot a biztonságos állapotba helyezték. Az ilyen alkalmazások ezt követően az Azure-igazolás használatával rutinszerűen hozhatnak létre megbízhatóságot a platformon, és képesek hozzáférni a bizalmas adatokhoz.

## <a name="azure-attestation-can-run-in-a-tee"></a>Az Azure-igazolás egy PÓLÓban is futtatható

Az Azure-igazolás kritikus fontosságú a bizalmas számítástechnikai forgatókönyvek esetében, mivel az a következő műveleteket végzi el:

- Ellenőrzi, hogy érvényes-e az enklávé bizonyítéka.
- Kiértékeli az enklávét a felhasználó által meghatározott házirend alapján.
- Kezeli és tárolja a bérlőre vonatkozó házirendeket.
- Létrehoz és aláír egy jogkivonatot, amelyet a függő entitások a enklávéval való interakcióra használnak.

Az Azure-igazolás két különböző típusú környezetben futtatható:
- Az Azure-igazolás egy SGX ENKLÁVÉHOZ-kompatibilis PÓLÓn fut.
- Az Azure-igazolás nem a PÓLÓn fut.

Az Azure igazolási ügyfelei kifejezték azt a követelményt, hogy a Microsoft működésbe kerüljön a megbízható számítástechnikai bázis (TCB) alapján. Ezzel megakadályozhatja, hogy a Microsoft entitások, például a virtuálisgép-rendszergazdák, a gazda-rendszergazdák és a Microsoft-fejlesztők módosíthassák az igazolási kérelmeket, a szabályzatokat és az Azure-tanúsítványok kiállított jogkivonatait. Az Azure-igazolás a PÓLÓban is futtatható, ahol az Azure-igazolások, például az árajánlatok érvényesítése, a jogkivonat-létrehozás és a jogkivonat-aláírás funkcióit egy SGX ENKLÁVÉHOZ enklávéba helyezi át a rendszer.

## <a name="why-use-azure-attestation"></a>Miért érdemes az Azure-igazolást használni?

Az Azure-igazolás a legjobb választás a pólók igazolására, mivel az a következő előnyöket kínálja: 

- Egységes keretrendszer több környezet (például TPM, SGX ENKLÁVÉHOZ enklávés és VBS enklávé) igazolásához 
- Több-bérlős szolgáltatás, amely lehetővé teszi az egyéni igazolási szolgáltatók és házirendek konfigurálását a jogkivonat-generálás korlátozására
- Olyan regionális közös szolgáltatókat kínál, amelyek a felhasználóktól való konfiguráció nélkül is tanúsítanak
- A SGX ENKLÁVÉHOZ enklávéban való használat közben védi az adatvédelmet
- Kiválóan elérhető szolgáltatás 

## <a name="business-continuity-and-disaster-recovery-bcdr-support"></a>Üzletmenet-folytonosság és vész-helyreállítási (BCDR) támogatás

Az [üzletmenet folytonossága és](../best-practices-availability-paired-regions.md) a vész-helyreállítási szolgáltatás (BCDR) lehetővé teszi az Azure-igazolást, hogy enyhítse a jelentős rendelkezésre állási problémákkal vagy egy adott régióban észlelt katasztrófákkal kapcsolatos szolgáltatások megszakadását.

A két régióban üzembe helyezett fürtök normál körülmények között egymástól függetlenül működnek. Az egyik régió hibája vagy kimaradása esetén a következő történik:

- Az Azure igazolási BCDR zökkenőmentes feladatátvételt tesz lehetővé, amelyben az ügyfeleknek nem kell további lépéseket tenniük a helyreállításhoz
- A régióhoz tartozó [Azure Traffic Manager](../traffic-manager/index.yml) felismeri az állapot-mintavételt, és átváltja a végpontot a párosított régióra.
- A meglévő kapcsolatok nem fognak működni, és belső kiszolgálóhiba-vagy időtúllépési problémákat fognak kapni
- A vezérlési sík összes művelete le lesz tiltva. Az ügyfelek nem tudnak tanúsítvány-előállítók létrehozására és a szabályzatok frissítésére az elsődleges régióban
- Az összes adatsík művelet, beleértve a tanúsító hívásokat, továbbra is az elsődleges régióban fog működni

## <a name="next-steps"></a>Következő lépések
- Ismerje meg az [Azure igazolásának alapfogalmait](basic-concepts.md)
- [Igazolási szabályzat létrehozása és aláírása](author-sign-policy.md)
- [Az Azure-igazolás beállítása a PowerShell használatával](quickstart-powershell.md)

---
title: Azure VMware-megoldás CloudSimple – privát felhők
description: Ismerje meg a CloudSimple kapcsolatos fogalmakat és előnyöket, beleértve a teljes VMware működés folytonosságát, a meglévő eszközökkel való kompatibilitást, a képességeket és a folyamatokat.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e2096e8c0ebfb233c7449510bf0bc8e80b56231e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88140717"
---
# <a name="cloudsimple-private-cloud-overview"></a>CloudSimple – a privát felhő áttekintése

A CloudSimple percek alatt átalakítja és kiterjeszti a VMware-alapú számítási feladatokat a nyilvános felhőkbe. A CloudSimple szolgáltatás használatával natív módon telepítheti a VMware-et az Azure operációs rendszer nélküli számítógépek infrastruktúráján. Az üzembe helyezés az Azure-beli helyszíneken működik, és teljes mértékben integrálódik az Azure-felhő többi részébe.

A CloudSimple-megoldás teljes körű VMware működési folytonosságot biztosít. Ez a megoldás a következő nyilvános Felhőbeli előnyöket nyújtja:

* Rugalmasság
* Innováció
* Hatékonyság

A CloudSimple a Felhőbeli fogyasztási modellek előnyeit kihasználva csökkenti a teljes tulajdonlási költségeket. Igény szerinti üzembe helyezést, utólagos elszámolást és kapacitás-optimalizálást is kínál.

A CloudSimple teljes mértékben kompatibilis az alábbiakkal:

* Meglévő eszközök
* Szakértelem
* Folyamatok

Ez a kompatibilitási funkció lehetővé teszi, hogy a csapatok az Azure-felhőben kezeljék a munkaterheléseket, anélkül, hogy megzavarják az ilyen típusú szabályzatokat:

* Network (Hálózat)
* Biztonság  
* Adatvédelem  
* Naplózás

A CloudSimple kezeli az infrastruktúrát és az összes szükséges hálózatkezelési és felügyeleti szolgáltatást. A CloudSimple szolgáltatás lehetővé teszi, hogy csapata a következőkre koncentráljon:

* Üzleti érték
* Alkalmazások üzembe helyezése
* Az üzletmenet folytonossága
* Támogatás
* Szabályzatbetartatás

## <a name="private-cloud-environment-overview"></a>A saját felhőalapú környezet áttekintése

A privát felhő egy elszigetelt VMware-verem, amely a következőket támogatja:

* ESXi-gazdagépek
* vCenter
* vSAN
* NSX

A privát felhők kezelése a CloudSimple-portálon keresztül történik. Saját vCenter-kiszolgálóval rendelkeznek a saját felügyeleti tartományában.

A verem a következőn fut:

* Dedikált csomópontok
* Elszigetelt operációs rendszer nélküli hardveres csomópontok

A felhasználók a natív VMware-eszközökön keresztül használják a veremet, beleértve a következőket:

* vCenter
* NSX-kezelő

A dedikált csomópontok üzembe helyezése az Azure-beli helyszíneken végezhető el. Ezt követően kezelheti őket az Azure-ban és a CloudSimple-ban is. A privát felhő egy vagy több vSphere-fürtből áll, és minden fürt 3 – 16 csomópontot tartalmaz.

Létrehozhat egy privát felhőt a megvásárolt, utólagos elszámolású csomópontok vagy fenntartott, dedikált csomópontok használatával.

A privát felhő a következő kapcsolatok használatával csatlakoztatható a helyszíni környezethez és az Azure-hálózathoz:

* Biztonságossá tétel
* Privát VPN
* Azure ExpressRoute

A privát felhőalapú környezet az egyes meghibásodási pontok eltávolítására szolgál:

* Az ESXi-fürtök vSphere magas rendelkezésre állással vannak konfigurálva, és a rugalmasság érdekében a méretük legalább egy tartalék csomópontot tartalmaz.
* a vSAN redundáns elsődleges tárterületet biztosít. a vSan legalább három csomópontot igényel az egyetlen hiba elleni védelem biztosításához. A vSAN konfigurálásával nagyobb rugalmasságot biztosíthat a nagyobb fürtök számára.
* A vCenter, a PSC és a NSX Manager virtuális gépeket RAID-10 tárolási házirenddel is konfigurálhatja a tárolási hibák elleni védelem érdekében. a vSphere HA védelmet biztosít a csomópont-és hálózati hibák ellen.

## <a name="scenarios-for-deploying-a-private-cloud"></a>A privát felhő üzembe helyezésének forgatókönyvei

Íme néhány példa a saját Felhőbeli üzembe helyezés használati eseteire.

### <a name="data-center-retirement-or-migration"></a>Adatközpont-kivonulás vagy áttelepítés

* Ha eléri a meglévő adatközpont korlátait, vagy frissíti a hardvert, további kapacitást érhet el.
* Adja hozzá a szükséges kapacitást a felhőben, és távolítsa el a hardveres frissítések kezelésének fejfájását.
* Csökkentse a felhőbe való Migrálás kockázatát és költségeit az időigényes átalakítások vagy az újraarchitektúrák összehasonlításával.
* A felhőbe történő Migrálás felgyorsításához használja a jól ismert VMware-eszközöket és-képességeket. A felhőben az Azure-szolgáltatások segítségével modernizálhatja alkalmazásait a tempóban.

### <a name="expand-on-demand"></a>Kibontás igény szerint

* Bontsa ki a felhőt a nem várt igények kielégítéséhez, például az új fejlesztési környezetekhez vagy a szezonális kapacitások kitöréséhez.
* Igény szerint új kapacitást hozhat létre, és megtarthatja, amíg szüksége van rá.
* Csökkentse a kezdeti befektetéseket, gyorsítsa fel a kiépítés sebességét, és csökkentse a bonyolultságot ugyanazzal az architektúrával és házirenddel mind a helyszínen, mind a felhőben.

### <a name="disaster-recovery-and-virtual-desktops-in-the-azure-cloud"></a>Vész-helyreállítási és virtuális asztalok az Azure-felhőben

* Távoli hozzáférés létrehozása az Azure-felhőben tárolt adatelemekhez, alkalmazásokhoz és asztali gépekhez. A nagy sávszélességű kapcsolatokkal gyorsan tölthet le/tölthet le adatokat az incidensekről. Az alacsony késleltetésű hálózatok gyors válaszidőt biztosítanak a felhasználók számára egy asztali alkalmazástól.

* A CloudSimple-portál és a jól ismert VMware-eszközök használatával replikálhatja az összes szabályzatot és hálózatkezelést a felhőben. A replikáció csökkenti a DR-és VDI-implementációk létrehozásának és kezelésének erőfeszítéseit és kockázatait.

### <a name="high-performance-applications-and-databases"></a>Nagy teljesítményű alkalmazások és adatbázisok

* Futtassa a legigényesebb számítási feladatokat a CloudSimple által biztosított hiperkonvergens architektúrával.
* Az Oracle, a Microsoft SQL Server, a köztes rendszerek és a nagy teljesítményű, nem SQL-adatbázisok futtatása.
* A felhőt saját adatközpontként, nagy sebességű, 25 GB/s-os hálózati kapcsolattal tapasztalhatja. A nagy sebességű kapcsolatok lehetővé teszik, hogy olyan hibrid alkalmazásokat futtasson, amelyek a helyszíni, a VMware-en és az Azure-beli privát számítási feladatokon alapulnak, és nem veszélyeztethetik a teljesítményt.

### <a name="true-hybrid"></a>Valódi hibrid

* A DevOps egyesítheti a VMware-és az Azure-szolgáltatások között.
* Optimalizálja a VMware-felügyeletet az Azure-szolgáltatásokhoz és-megoldásokhoz, amelyek az összes számítási feladatra alkalmazhatók.
* A nyilvános felhőalapú szolgáltatásokat anélkül érheti el, hogy kibővíti az adatközpontot, vagy újratervezi az alkalmazásokat.
* Az Azure-beli VMware-alkalmazások identitásának, hozzáférés-vezérlési házirendjeinek, naplózásának és figyelésének központosítása.

## <a name="limits"></a>Korlátok

A következő táblázat felsorolja a privát felhő erőforrásaihoz tartozó csomópontok korlátozásait.

| Erőforrás | Korlát |
|----------|-------|
| A privát felhő létrehozásához szükséges csomópontok minimális száma | 3 |
| Fürtben lévő csomópontok maximális száma egy privát felhőben | 16 |
| A privát felhőben lévő csomópontok maximális száma | 64 |
| Csomópontok minimális száma egy új fürtön | 3 |

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan [hozhat létre saját felhőt](create-private-cloud.md)
* Ismerje meg, hogyan [konfigurálhatja a saját felhőalapú környezetét](quickstart-create-private-cloud.md)

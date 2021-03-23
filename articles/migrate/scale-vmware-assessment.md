---
title: Nagy számú kiszolgáló kiértékelése VMware-környezetben az Azure-ba való Migrálás Azure Migrate
description: Ismerteti, hogyan lehet kiértékelni a VMware-környezetben található nagy számú kiszolgálót az Azure-ba való áttelepítéshez az Azure Migrate szolgáltatás használatával.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 10b8aaeaa25e49140dbf6f31c064c7f823d23e31
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778253"
---
# <a name="assess-large-numbers-of-servers-in-vmware-environment-for-migration-to-azure"></a>Nagy számú kiszolgáló kiértékelése VMware-környezetben az Azure-ba való Migrálás céljából


Ez a cikk azt ismerteti, hogyan értékelhető ki a VMware-környezetben a helyszíni kiszolgálók nagy száma (1000-35000) az Azure-ba való áttelepítéshez a Azure Migrate felderítési és értékelési eszköz használatával.

[Azure Migrate](migrate-services-overview.md) olyan eszközöket biztosít, amelyek segítségével az alkalmazások, az infrastruktúra és a munkaterhelések felderíthető, mérhetők és áttelepíthetők a Microsoft Azure. A hub Azure Migrate eszközöket és külső gyártótól származó független szoftvergyártó (ISV) ajánlatokat tartalmaz. 

Ebben a cikkben az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Tervezze meg az értékelést a skálán.
> * Azure-engedélyek konfigurálása és a VMware előkészítése az értékeléshez.
> * Hozzon létre egy Azure Migrate projektet, és hozzon létre egy értékelést.
> * Tekintse át az értékelést az áttelepítés megtervezése során.


> [!NOTE]
> Ha szeretné kipróbálni egy próba-koncepciót egy pár kiszolgáló értékelésére a méretezés előtt, kövesse az [oktatóanyag-sorozatot](./tutorial-discover-vmware.md)

## <a name="plan-for-assessment"></a>Az értékelés megtervezése

A VMware-környezetekben a nagy számú kiszolgáló értékelésének tervezésekor néhány dolgot érdemes meggondolni:

- **Tervezze meg Azure Migrate projekteket**: Ismerje meg, hogyan helyezhet üzembe Azure Migrate-projekteket. Ha például az adatközpontok különböző földrajzi területeken vannak, vagy ha a felderítést, az értékelést vagy az áttelepítéssel kapcsolatos metaadatokat más földrajzi helyeken kell tárolnia, akkor lehet, hogy több projektre van szüksége. 
- **Berendezések tervezése**: Azure Migrate a kiszolgálók folyamatos felderítéséhez helyszíni Azure Migrate berendezést használ, amely VMWare virtuális gépként van üzembe helyezve. A készülék figyeli a környezet változásait, például kiszolgálók, lemezek vagy hálózati adapterek hozzáadását. Emellett metaadatokat és teljesítményadatokat is küld az Azure-nak. Meg kell állapítani, hogy hány készüléket kell telepítenie.
- A **felderítési fiókok megtervezése**: az Azure Migrate készülék olyan fiókot használ, amely vCenter Server hozzáféréssel rendelkezik a kiszolgálók értékeléséhez és áttelepítéséhez. Ha több mint 10 000-kiszolgálót derít fel, több fiókot kell beállítania, mivel az szükséges, hogy a projektek két készülékéről felderített kiszolgálók között ne legyen átfedés. 

> [!NOTE]
> Ha több berendezést állít be, ügyeljen arra, hogy ne legyen átfedés a megadott vCenter-fiókok kiszolgálói között. Az ilyen átfedés mellett zajló felderítés nem támogatott forgatókönyv. Ha egy kiszolgálót egynél több berendezés észlel, akkor a rendszer duplikálja a felderítésben és a problémákban, miközben a kiszolgáló áttelepítésének Azure Portal segítségével engedélyezi a replikációt a kiszolgálón.

## <a name="planning-limits"></a>Tervezési korlátok
 
Használja az ebben a táblázatban foglalt korlátokat a tervezéshez.

**Tervezés** | **Korlátok**
--- | --- 
**Azure Migrate projektek** | Egy projektben akár 35 000-kiszolgálót is megvizsgálhat.
**Azure Migrate-berendezés** | Egy készülék legfeljebb 10 000 kiszolgálót tud felderíteni egy vCenter Server.<br/> Egy készülék csak egyetlen vCenter Server tud csatlakozni.<br/> Egy berendezés csak egyetlen Azure Migrate projekthez társítható.<br/>  Tetszőleges számú készülék társítható egyetlen Azure Migrate-projekthez is. <br/><br/> 
**Csoport** | Egyetlen csoportban akár 35 000-kiszolgálót is hozzáadhat.
**Azure Migrate Értékelés** | Egyetlen értékeléssel akár 35 000-kiszolgálót is megvizsgálhat.

Ezekkel a korlátozásokkal kapcsolatban néhány példa az üzemelő példányokra:


**vCenter-kiszolgáló** | **Kiszolgálók a kiszolgálón** | **Ajánlás** | **Művelet**
---|---|---|---
Eggyel | < 10 000 | Egy Azure Migrate projekt.<br/> Egy készülék.<br/> Egy vCenter-fiók a felderítéshez. | Állítsa be a készüléket, és kapcsolódjon vCenter Server fiókkal.
Eggyel | > 10 000 | Egy Azure Migrate projekt.<br/> Több készülék.<br/> Több vCenter-fiók. | Állítsa be a készüléket minden 10 000-kiszolgálóhoz.<br/><br/> Állítsa be a vCenter-fiókokat, és ossza fel a leltárt, hogy korlátozza a fiókok hozzáférését a 10 000-nál kevesebb kiszolgálóra.<br/> Csatlakoztasson minden készüléket a vCenter-kiszolgálóhoz egy fiókkal.<br/> Elemezheti a függőségeket a különböző készülékekkel felderített kiszolgálók között. <br/> <br/> Győződjön meg arról, hogy a vCenter-fiókokban nincsenek átfedésben a kiszolgálók között. Az ilyen átfedés mellett zajló felderítés nem támogatott forgatókönyv. Ha egy kiszolgálót egynél több berendezés észlel, akkor a rendszer duplikálja a felderítésben és a problémákban, miközben a kiszolgáló áttelepítésének Azure Portal segítségével engedélyezi a replikációt a kiszolgálón.
Többszörös | < 10 000 |  Egy Azure Migrate projekt.<br/> Több készülék.<br/> Egy vCenter-fiók a felderítéshez. | Állítsa be a készülékeket, és kapcsolódjon a vCenter Serverhoz egy fiókkal.<br/> Elemezheti a függőségeket a különböző készülékekkel felderített kiszolgálók között.
Többszörös | > 10 000 | Egy Azure Migrate projekt.<br/> Több készülék.<br/> Több vCenter-fiók. | Ha vCenter Server felderítési < 10 000-kiszolgálókat, állítson be egy berendezést az egyes vCenter Serverokhoz.<br/><br/> Ha vCenter Server felderítési > 10 000-kiszolgálókat, állítson be egy berendezést minden 10 000-kiszolgálóhoz.<br/> Állítsa be a vCenter-fiókokat, és ossza fel a leltárt, hogy korlátozza a fiókok hozzáférését a 10 000-nál kevesebb kiszolgálóra.<br/> Csatlakoztasson minden készüléket a vCenter-kiszolgálóhoz egy fiókkal.<br/> Elemezheti a függőségeket a különböző készülékekkel felderített kiszolgálók között. <br/><br/> Győződjön meg arról, hogy a vCenter-fiókokban nincsenek átfedésben a kiszolgálók között. Az ilyen átfedés mellett zajló felderítés nem támogatott forgatókönyv. Ha egy kiszolgálót egynél több berendezés észlel, akkor a rendszer duplikálja a felderítésben és a problémákban, miközben a kiszolgáló áttelepítésének Azure Portal segítségével engedélyezi a replikációt a kiszolgálón.



## <a name="plan-discovery-in-a-multi-tenant-environment"></a>A felderítés tervezése több-bérlős környezetben

Ha több-bérlős környezetet szeretne tervezni, a felderítést a vCenter Serverra szűkítheti.

- A berendezés felderítési hatókörét beállíthatja vCenter Server adatközpontokra, fürtökre vagy a fürtök, a gazdagépek vagy az egyes kiszolgálók mappájára, illetve az egyes kiszolgálókra.
- Ha a környezet a bérlők között van megosztva, és az egyes bérlőket külön szeretné felderíteni, akkor a hatókörhöz való hozzáférést a berendezés által a felderítéshez használt vCenter-fiókhoz is elérheti. 
    - Előfordulhat, hogy a virtuális gépek mappáinak hatókörét szeretné használni, ha a bérlők megosztják a gazdagépeket. Azure Migrate nem tudja felderíteni a kiszolgálókat, ha a vCenter-fiókhoz hozzáférés van megadva a vCenter VM-mappa szintjén. Ha a virtuális gépek mappái alapján szeretné felderíteni a felderítést, ezt úgy teheti meg, hogy a vCenter-fiókhoz csak olvasási hozzáférés van hozzárendelve a kiszolgáló szintjén. [További információ](set-discovery-scope.md).

## <a name="prepare-for-assessment"></a>Felkészülés az értékelésre

Az Azure és a VMware előkészítése a felderítési és értékelési eszközre:

1. Ellenőrizze [a VMware támogatási követelményeit és korlátozásait](migrate-support-matrix-vmware.md).
2. Az Azure-fiók engedélyeinek beállítása a Azure Migrate való interakcióhoz.
3. A VMware előkészítése az értékeléshez.

A beállítások konfigurálásához kövesse az [oktatóanyag](./tutorial-discover-vmware.md) utasításait.


## <a name="create-a-project"></a>Projekt létrehozása

A tervezési követelményekkel összhangban tegye a következőket:

1. Hozzon létre egy Azure Migrate projekteket.
2. Adja hozzá a Azure Migrate felderítési és értékelési eszközt a projektekhez.

[További információ](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>Értékelés létrehozása és áttekintése

1. A VMware-környezetben található kiszolgálók értékelésének létrehozása.
1. Tekintse át az áttelepítési tervezés előkészítésének értékelését.


A beállítások konfigurálásához kövesse az [oktatóanyag](./tutorial-assess-vmware-azure-vm.md) utasításait.
    

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben:
 
> [!div class="checklist"] 
> * A VMware-környezetben található kiszolgálók Azure Migrate értékelésének tervezett méretezése
> * Az Azure és a VMware előkészítése az értékeléshez
> * Létrehozott egy Azure Migrate projektet és futtatott értékeléseket
> * Az áttelepítésre való felkészülés során felülvizsgálták az értékeléseket.

Most [megtudhatja, hogyan](concepts-assessment-calculation.md) számítja ki az értékeléseket, és hogyan [módosíthatja az értékeléseket](how-to-modify-assessment.md).
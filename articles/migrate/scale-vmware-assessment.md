---
title: Nagy számú VMware virtuális gép értékelése az Azure-ba való Migrálás Azure Migrate
description: Ismerteti, hogyan lehet kiértékelni az Azure-ba való áttelepítéshez szükséges nagy számú VMware virtuális gépet a Azure Migrate szolgáltatás használatával.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 206df399736dbd3b5d1d52531a249bbd37646514
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96753671"
---
# <a name="assess-large-numbers-of-vmware-vms-for-migration-to-azure"></a>Nagy számú VMware virtuális gép értékelése az Azure-ba való áttelepítéshez


Ez a cikk bemutatja, hogyan értékelheti a helyszíni VMware virtuális gépek nagy számait (1000-35000) az Azure-ba való áttelepítéshez az Azure Migrate Server Assessment Tool használatával.

[Azure Migrate](migrate-services-overview.md) olyan eszközöket biztosít, amelyek segítségével az alkalmazások, az infrastruktúra és a munkaterhelések felderíthető, mérhetők és áttelepíthetők a Microsoft Azure. A hub Azure Migrate eszközöket és külső gyártótól származó független szoftvergyártó (ISV) ajánlatokat tartalmaz. 

Ebben a cikkben az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Tervezze meg az értékelést a skálán.
> * Azure-engedélyek konfigurálása és a VMware előkészítése az értékeléshez.
> * Hozzon létre egy Azure Migrate projektet, és hozzon létre egy értékelést.
> * Tekintse át az értékelést az áttelepítés megtervezése során.


> [!NOTE]
> Ha szeretné kipróbálni egy próba-koncepciót néhány virtuális gép értékeléséhez, mielőtt a méretezést kivizsgáljuk, kövesse az [oktatóanyag-sorozatot](./tutorial-discover-vmware.md)

## <a name="plan-for-assessment"></a>Az értékelés megtervezése

A nagyszámú VMware virtuális gép értékelésének tervezésekor néhány dolgot érdemes meggondolni:

- **Tervezze meg Azure Migrate projekteket**: Ismerje meg, hogyan helyezhet üzembe Azure Migrate-projekteket. Ha például az adatközpontok különböző földrajzi területeken vannak, vagy ha a felderítést, az értékelést vagy az áttelepítéssel kapcsolatos metaadatokat más földrajzi helyeken kell tárolnia, akkor lehet, hogy több projektre van szüksége. 
- **Berendezések megtervezése**: Azure Migrate a virtuális gépek folyamatos felderítéséhez helyszíni Azure Migrate berendezést használ, amely VMWare virtuális gépként van üzembe helyezve. A készülék figyeli a környezet változásait, például virtuális gépek, lemezek vagy hálózati adapterek hozzáadását. Emellett metaadatokat és teljesítményadatokat is küld az Azure-nak. Meg kell állapítani, hogy hány készüléket kell telepítenie.
- A **felderítési fiókok megtervezése**: az Azure Migrate készülék olyan fiókot használ, amely hozzáféréssel rendelkezik vCenter Serverhoz, hogy felderítse a virtuális gépeket az értékeléshez és az áttelepítéshez Ha 10 000-nál több virtuális gépet derít fel, több fiókot kell beállítania, mivel az szükséges, hogy a projektek két készülékéről felderített virtuális gépek között ne legyen átfedés. 

> [!NOTE]
> Ha több berendezést állít be, ügyeljen arra, hogy a virtuális gépek ne legyenek átfedésben a megadott vCenter-fiókok között. Az ilyen átfedés mellett zajló felderítés nem támogatott forgatókönyv. Ha egynél több berendezés észleli a virtuális gépet, az a felderítésben és a problémák során duplikálja a virtuális gép replikálását a kiszolgáló áttelepítésének Azure Portal használatával.

## <a name="planning-limits"></a>Tervezési korlátok
 
Használja az ebben a táblázatban foglalt korlátokat a tervezéshez.

**Tervezés** | **Korlátok**
--- | --- 
**Azure Migrate projektek** | Egy projektben akár 35 000 virtuális gépet is megvizsgálhat.
**Azure Migrate-berendezés** | Egy készülék legfeljebb 10 000 virtuális gépet képes felderíteni egy vCenter Server.<br/> Egy készülék csak egyetlen vCenter Server tud csatlakozni.<br/> Egy berendezés csak egyetlen Azure Migrate projekthez társítható.<br/>  Tetszőleges számú készülék társítható egyetlen Azure Migrate-projekthez is. <br/><br/> 
**Csoport** | Egyetlen csoportban legfeljebb 35 000 virtuális gépet adhat hozzá.
**Azure Migrate Értékelés** | Egyetlen értékeléssel akár 35 000 virtuális gépet is megvizsgálhat.

Ezekkel a korlátozásokkal kapcsolatban néhány példa az üzemelő példányokra:


**vCenter-kiszolgáló** | **Kiszolgálókon futó virtuális gépek** | **Ajánlás** | **Művelet**
---|---|---|---
Eggyel | < 10 000 | Egy Azure Migrate projekt.<br/> Egy készülék.<br/> Egy vCenter-fiók a felderítéshez. | Állítsa be a készüléket, és kapcsolódjon vCenter Server fiókkal.
Eggyel | > 10 000 | Egy Azure Migrate projekt.<br/> Több készülék.<br/> Több vCenter-fiók. | Állítsa be a készüléket minden 10 000 virtuális gépre.<br/><br/> Állítsa be a vCenter-fiókokat, és ossza fel a leltárt, hogy korlátozza a fiókok hozzáférését a 10 000-nél kevesebb virtuális gépre.<br/> Csatlakoztasson minden készüléket a vCenter-kiszolgálóhoz egy fiókkal.<br/> Elemezheti a függőségeket a különböző készülékekkel felderített gépek között. <br/> <br/> Győződjön meg arról, hogy a virtuális gépek között nincs átfedés a megadott vCenter-fiókok között. Az ilyen átfedés mellett zajló felderítés nem támogatott forgatókönyv. Ha egy virtuális gépet több berendezés is felderít, akkor a rendszer duplikálja a felderítésben és a problémákban, miközben engedélyezi a virtuális gép replikálását a kiszolgáló áttelepítésének Azure Portal használatával.
Többszörös | < 10 000 |  Egy Azure Migrate projekt.<br/> Több készülék.<br/> Egy vCenter-fiók a felderítéshez. | Állítsa be a készülékeket, és kapcsolódjon a vCenter Serverhoz egy fiókkal.<br/> Elemezheti a függőségeket a különböző készülékekkel felderített gépek között.
Többszörös | > 10 000 | Egy Azure Migrate projekt.<br/> Több készülék.<br/> Több vCenter-fiók. | Ha vCenter Server felderítést < 10 000 virtuális gépeket, állítson be egy berendezést az egyes vCenter Serverokhoz.<br/><br/> Ha vCenter Server felderítést > 10 000 virtuális gépeket, állítson be egy berendezést minden 10 000 virtuális gép számára.<br/> Állítsa be a vCenter-fiókokat, és ossza fel a leltárt, hogy korlátozza a fiókok hozzáférését a 10 000-nél kevesebb virtuális gépre.<br/> Csatlakoztasson minden készüléket a vCenter-kiszolgálóhoz egy fiókkal.<br/> Elemezheti a függőségeket a különböző készülékekkel felderített gépek között. <br/><br/> Győződjön meg arról, hogy a virtuális gépek között nincs átfedés a megadott vCenter-fiókok között. Az ilyen átfedés mellett zajló felderítés nem támogatott forgatókönyv. Ha egy virtuális gépet több berendezés is felderít, akkor a rendszer duplikálja a felderítésben és a problémákban, miközben engedélyezi a virtuális gép replikálását a kiszolgáló áttelepítésének Azure Portal használatával.



## <a name="plan-discovery-in-a-multi-tenant-environment"></a>A felderítés tervezése több-bérlős környezetben

Ha több-bérlős környezetet szeretne tervezni, a felderítést a vCenter Serverra szűkítheti.

- A berendezés felderítési hatókörét beállíthatja egy vCenter Server adatközpontra, fürtökre vagy a fürtök, a gazdagépek vagy az egyes virtuális gépek mappájára vagy mappára.
- Ha a környezet a bérlők között van megosztva, és az egyes bérlőket külön szeretné felderíteni, akkor a hatókörhöz való hozzáférést a berendezés által a felderítéshez használt vCenter-fiókhoz is elérheti. 
    - Előfordulhat, hogy a virtuális gépek mappáinak hatókörét szeretné használni, ha a bérlők megosztják a gazdagépeket. Azure Migrate nem tudja felderíteni a virtuális gépeket, ha a vCenter-fiókhoz hozzáférés van megadva a vCenter VM-mappa szintjén. Ha a virtuális gép mappáiban szeretné kiterjeszteni a felderítést, a vCenter-fióknak csak olvasási hozzáférése van hozzárendelve a virtuális gép szintjén. [További információ](set-discovery-scope.md).

## <a name="prepare-for-assessment"></a>Felkészülés az értékelésre

Az Azure és a VMware előkészítése a kiszolgálók értékeléséhez. 

1. Ellenőrizze [a VMware támogatási követelményeit és korlátozásait](migrate-support-matrix-vmware.md).
2. Az Azure-fiók engedélyeinek beállítása a Azure Migrate való interakcióhoz.
3. A VMware előkészítése az értékeléshez.

A beállítások konfigurálásához kövesse az [oktatóanyag](./tutorial-discover-vmware.md) utasításait.


## <a name="create-a-project"></a>Projekt létrehozása

A tervezési követelményekkel összhangban tegye a következőket:

1. Hozzon létre egy Azure Migrate projekteket.
2. Adja hozzá a Azure Migrate Server Assessment eszközt a projektekhez.

[További információ](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>Értékelés létrehozása és áttekintése

1. Hozzon létre értékeléseket a VMware virtuális gépekhez.
1. Tekintse át az áttelepítési tervezés előkészítésének értékelését.


A beállítások konfigurálásához kövesse az [oktatóanyag](./tutorial-assess-vmware-azure-vm.md) utasításait.
    

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben:
 
> [!div class="checklist"] 
> * A VMware virtuális gépek Azure Migrate értékelésének tervezett méretezése
> * Az Azure és a VMware előkészítése az értékeléshez
> * Létrehozott egy Azure Migrate projektet és futtatott értékeléseket
> * Az áttelepítésre való felkészülés során felülvizsgálták az értékeléseket.

Most [megtudhatja, hogyan](concepts-assessment-calculation.md) számítja ki az értékeléseket, és hogyan [módosíthatja az értékeléseket](how-to-modify-assessment.md).
---
title: Nagy számú Hyper-V virtuális gép értékelése az Azure-ba való Migrálás Azure Migrate használatával | Microsoft Docs
description: Ismerteti, hogyan lehet kiértékelni az Azure-ba való áttelepítéshez szükséges nagy számú Hyper-V virtuális gépet az Azure Migrate szolgáltatással.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/10/2019
ms.openlocfilehash: 92c275ee3f8e00e71b80e448c9adb94f0b6d21dc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96753722"
---
# <a name="assess-large-numbers-of-hyper-v-vms-for-migration-to-azure"></a>Nagy számú Hyper-V virtuális gép értékelése az Azure-ba való áttelepítéshez

Ez a cikk azt ismerteti, hogyan értékelhető ki nagy számú helyszíni Hyper-V virtuális gép az Azure-ba való áttelepítéshez az Azure Migrate Server Assessment Tool használatával.

[Azure Migrate](migrate-services-overview.md) olyan eszközöket biztosít, amelyek segítségével az alkalmazások, az infrastruktúra és a munkaterhelések felderíthető, mérhetők és áttelepíthetők a Microsoft Azure. A hub Azure Migrate eszközöket és külső gyártótól származó független szoftvergyártó (ISV) ajánlatokat tartalmaz. 


Ebben a cikkben az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Tervezze meg az értékelést a skálán.
> * Konfigurálja az Azure-engedélyeket, és készítse elő a Hyper-V-t az értékeléshez.
> * Hozzon létre egy Azure Migrate projektet, és hozzon létre egy értékelést.
> * Tekintse át az értékelést az áttelepítés megtervezése során.


> [!NOTE]
> Ha szeretné kipróbálni egy próba-koncepciót néhány virtuális gép értékeléséhez, mielőtt a méretezést kivizsgáljuk, kövesse az [oktatóanyag-sorozatot](./tutorial-discover-hyper-v.md)

## <a name="plan-for-assessment"></a>Az értékelés megtervezése

A nagy számú Hyper-V virtuális gép értékelésének tervezésekor néhány dolgot érdemes meggondolni:

- **Tervezze meg Azure Migrate projekteket**: Ismerje meg, hogyan helyezhet üzembe Azure Migrate-projekteket. Ha például az adatközpontok különböző földrajzi területeken vannak, vagy ha a felderítést, az értékelést vagy az áttelepítéssel kapcsolatos metaadatokat más földrajzi helyeken kell tárolnia, akkor lehet, hogy több projektre van szüksége.
- **Berendezések megtervezése**: a Azure Migrate Hyper-V virtuális gépként üzembe helyezett helyszíni Azure Migrate-berendezést használ a virtuális gépek folyamatos felderítéséhez az értékeléshez és az áttelepítéshez. A készülék figyeli a környezet változásait, például virtuális gépek, lemezek vagy hálózati adapterek hozzáadását. Emellett metaadatokat és teljesítményadatokat is küld az Azure-nak. Meg kell állapítani, hogy hány berendezést kell üzembe helyezni.


## <a name="planning-limits"></a>Tervezési korlátok
 
Használja az ebben a táblázatban foglalt korlátokat a tervezéshez.

**Tervezés** | **Korlátok**
--- | --- 
**Azure Migrate projektek** | Egy projektben akár 35 000 virtuális gépet is megvizsgálhat.
**Azure Migrate-berendezés** | Egy készülék legfeljebb 5000 virtuális gépet képes felderíteni.<br/> Egy készülék legfeljebb 300 Hyper-V gazdagéphez tud csatlakozni.<br/> Egy berendezés csak egyetlen Azure Migrate projekthez társítható.<br/> Tetszőleges számú készülék társítható egyetlen Azure Migrate-projekthez is. <br/><br/> 
**Csoport** | Egyetlen csoportban legfeljebb 35 000 virtuális gépet adhat hozzá.
**Azure Migrate Értékelés** | Egyetlen értékeléssel akár 35 000 virtuális gépet is megvizsgálhat.



## <a name="other-planning-considerations"></a>Egyéb tervezési szempontok

- A felderítés a készülékből való indításához ki kell választania az egyes Hyper-V-gazdagépeket. 
- Ha több-bérlős környezetet futtat, a jelenleg nem tud csak egy adott bérlőhöz tartozó virtuális gépeket felderíteni. 

## <a name="prepare-for-assessment"></a>Felkészülés az értékelésre

Az Azure és a Hyper-V előkészítése a kiszolgálók értékeléséhez. 

1. Ellenőrizze [a Hyper-V támogatási követelményeit és korlátozásait](migrate-support-matrix-hyper-v.md).
2. Az Azure-fiók engedélyeinek beállítása a Azure Migrate való interakcióhoz
3. Hyper-V-gazdagépek és virtuális gépek előkészítése

A beállítások konfigurálásához kövesse az [oktatóanyag](./tutorial-discover-hyper-v.md) utasításait.

## <a name="create-a-project"></a>Projekt létrehozása

A tervezési követelményekkel összhangban tegye a következőket:

1. Hozzon létre egy Azure Migrate projekteket.
2. Adja hozzá a Azure Migrate Server Assessment eszközt a projektekhez.

[További információ](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>Értékelés létrehozása és áttekintése

1. Hozza létre a Hyper-V virtuális gépek értékeléseit.
1. Tekintse át az áttelepítési tervezés előkészítésének értékelését.

[További](tutorial-assess-hyper-v.md) információ az értékelések létrehozásáról és áttekintéséről.
    

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben:
 
> [!div class="checklist"] 
> * A Hyper-V virtuális gépek Azure Migrate értékelésének tervezett méretezése
> * Az Azure és a Hyper-V előkészítése az értékeléshez
> * Létrehozott egy Azure Migrate projektet és futtatott értékeléseket
> * Az áttelepítésre való felkészülés során felülvizsgálták az értékeléseket.

Most [megtudhatja, hogyan](concepts-assessment-calculation.md) számítja ki az értékeléseket, és hogyan [módosíthatja az értékeléseket](how-to-modify-assessment.md)
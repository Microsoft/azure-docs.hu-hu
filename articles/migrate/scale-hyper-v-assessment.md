---
title: Nagy számú kiszolgáló értékelése a Hyper-V környezetben az Azure-ba való Migrálás Azure Migrate használatával | Microsoft Docs
description: Ismerteti, hogyan lehet kiértékelni a Hyper-V környezetben található nagy számú kiszolgálót az Azure-ba való áttelepítéshez az Azure Migrate szolgáltatás használatával.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/10/2019
ms.openlocfilehash: 495e1bf415146471fcccad34e2879398e12e1769
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780293"
---
# <a name="assess-large-numbers-of-servers-in-hyper-v-environment-for-migration-to-azure"></a>Nagy számú kiszolgáló értékelése a Hyper-V környezetben az Azure-ba való Migrálás céljából

Ez a cikk azt ismerteti, hogyan értékelhető ki a Hyper-V környezetben található nagy számú helyszíni kiszolgáló az Azure-ba való Migrálás során a Azure Migrate felderítési és értékelési eszköz használatával.

[Azure Migrate](migrate-services-overview.md) olyan eszközöket biztosít, amelyek segítségével az alkalmazások, az infrastruktúra és a munkaterhelések felderíthető, mérhetők és áttelepíthetők a Microsoft Azure. A hub Azure Migrate eszközöket és külső gyártótól származó független szoftvergyártó (ISV) ajánlatokat tartalmaz. 


Ebben a cikkben az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Tervezze meg az értékelést a skálán.
> * Konfigurálja az Azure-engedélyeket, és készítse elő a Hyper-V-t az értékeléshez.
> * Hozzon létre egy Azure Migrate projektet, és hozzon létre egy értékelést.
> * Tekintse át az értékelést az áttelepítés megtervezése során.


> [!NOTE]
> Ha szeretné kipróbálni egy próba-koncepciót egy pár kiszolgáló értékelésére a méretezés előtt, kövesse az [oktatóanyag-sorozatot](./tutorial-discover-hyper-v.md)

## <a name="plan-for-assessment"></a>Az értékelés megtervezése

A Hyper-V környezetben a nagy számú kiszolgáló értékelésének tervezésekor néhány dolgot érdemes meggondolni:

- **Tervezze meg Azure Migrate projekteket**: Ismerje meg, hogyan helyezhet üzembe Azure Migrate-projekteket. Ha például az adatközpontok különböző földrajzi területeken vannak, vagy ha a felderítést, az értékelést vagy az áttelepítéssel kapcsolatos metaadatokat más földrajzi helyeken kell tárolnia, akkor lehet, hogy több projektre van szüksége.
- **Berendezések tervezése**: Azure Migrate a helyszíni Azure Migrate berendezést Hyper-V virtuális gépként telepíti, hogy folyamatosan felderítse a kiszolgálók értékelését és áttelepítését. A készülék figyeli a környezet változásait, például kiszolgálók, lemezek vagy hálózati adapterek hozzáadását. Emellett metaadatokat és teljesítményadatokat is küld az Azure-nak. Meg kell állapítani, hogy hány berendezést kell üzembe helyezni.


## <a name="planning-limits"></a>Tervezési korlátok
 
Használja az ebben a táblázatban foglalt korlátokat a tervezéshez.

**Tervezés** | **Korlátok**
--- | --- 
**Azure Migrate projektek** | Egy projektben akár 35 000-kiszolgálót is megvizsgálhat.
**Azure Migrate-berendezés** | Egy készülék legfeljebb 5000 kiszolgálót képes felderíteni.<br/> Egy készülék legfeljebb 300 Hyper-V gazdagéphez tud csatlakozni.<br/> Egy berendezés csak egyetlen Azure Migrate projekthez társítható.<br/> Tetszőleges számú készülék társítható egyetlen Azure Migrate-projekthez is. <br/><br/> 
**Csoport** | Egyetlen csoportban akár 35 000-kiszolgálót is hozzáadhat.
**Azure Migrate Értékelés** | Egyetlen értékeléssel akár 35 000-kiszolgálót is megvizsgálhat.



## <a name="other-planning-considerations"></a>Egyéb tervezési szempontok

- A felderítés a készülékből való indításához ki kell választania az egyes Hyper-V-gazdagépeket. 
- Ha több-bérlős környezetet futtat, a jelenleg csak egy adott bérlőhöz tartozó kiszolgálók felderítése nem lehetséges. 

## <a name="prepare-for-assessment"></a>Felkészülés az értékelésre

Az Azure és a Hyper-V előkészítése a felderítési és értékelési eszközhöz: 

1. Ellenőrizze [a Hyper-V támogatási követelményeit és korlátozásait](migrate-support-matrix-hyper-v.md).
2. Az Azure-fiók engedélyeinek beállítása a Azure Migrate való interakcióhoz
3. Hyper-V-gazdagépek és-kiszolgálók előkészítése

A beállítások konfigurálásához kövesse az [oktatóanyag](./tutorial-discover-hyper-v.md) utasításait.

## <a name="create-a-project"></a>Projekt létrehozása

A tervezési követelményekkel összhangban tegye a következőket:

1. Hozzon létre egy Azure Migrate projekteket.
2. Adja hozzá a Azure Migrate felderítési és értékelési eszközt a projektekhez.

[További információ](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>Értékelés létrehozása és áttekintése

1. A Hyper-V környezetben található kiszolgálók értékelésének létrehozása.
1. Tekintse át az áttelepítési tervezés előkészítésének értékelését.

[További](tutorial-assess-hyper-v.md) információ az értékelések létrehozásáról és áttekintéséről.
    

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben:
 
> [!div class="checklist"] 
> * A Hyper-V környezetben található kiszolgálók Azure Migrate értékelésének tervezett méretezése
> * Az Azure és a Hyper-V előkészítése az értékeléshez
> * Létrehozott egy Azure Migrate projektet és futtatott értékeléseket
> * Az áttelepítésre való felkészülés során felülvizsgálták az értékeléseket.

Most [megtudhatja, hogyan](concepts-assessment-calculation.md) számítja ki az értékeléseket, és hogyan [módosíthatja az értékeléseket](how-to-modify-assessment.md)
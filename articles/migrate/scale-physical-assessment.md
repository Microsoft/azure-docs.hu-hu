---
title: Nagy számú fizikai kiszolgáló értékelése az Azure-ba való Migrálás Azure Migrate használatával | Microsoft Docs
description: Ismerteti, hogyan lehet kiértékelni az Azure-ba való áttelepítéshez szükséges nagy számú fizikai kiszolgálót a Azure Migrate szolgáltatás használatával.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 01/19/2020
ms.author: hamusa
ms.openlocfilehash: b84a7b88f700e2ba8e9fbe29099dfcbce8dbf3b7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85081069"
---
# <a name="assess-large-numbers-of-physical-servers-for-migration-to-azure"></a>Nagy számú fizikai kiszolgáló értékelése az Azure-ba való áttelepítéshez

Ez a cikk azt ismerteti, hogyan értékelhető ki nagy számú helyszíni fizikai kiszolgáló az Azure-ba való áttelepítéshez az Azure Migrate Server Assessment Tool használatával.

[Azure Migrate](migrate-services-overview.md) olyan eszközöket biztosít, amelyek segítségével az alkalmazások, az infrastruktúra és a munkaterhelések felderíthető, mérhetők és áttelepíthetők a Microsoft Azure. A hub Azure Migrate eszközöket és külső gyártótól származó független szoftvergyártó (ISV) ajánlatokat tartalmaz. 


Ebben a cikkben az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Tervezze meg az értékelést a skálán.
> * Konfigurálja az Azure-engedélyeket, és készítse elő a fizikai kiszolgálókat az értékeléshez.
> * Hozzon létre egy Azure Migrate projektet, és hozzon létre egy értékelést.
> * Tekintse át az értékelést az áttelepítés megtervezése során.


> [!NOTE]
> Ha szeretné kipróbálni egy próba-koncepciót, hogy néhány kiszolgálót mérjen fel a méretezés előtt, kövesse az [oktatóanyag-sorozatot](tutorial-prepare-physical.md).

## <a name="plan-for-assessment"></a>Az értékelés megtervezése

Ha nagy számú fizikai kiszolgáló értékelését tervezi, néhány dolgot érdemes meggondolni:

- **Tervezze meg Azure Migrate projekteket**: Ismerje meg, hogyan helyezhet üzembe Azure Migrate-projekteket. Ha például az adatközpontok különböző földrajzi területeken vannak, vagy ha a felderítést, az értékelést vagy az áttelepítéssel kapcsolatos metaadatokat más földrajzi helyeken kell tárolnia, akkor lehet, hogy több projektre van szüksége.
- **Berendezések tervezése**: Azure Migrate egy Windows rendszerű gépen üzembe helyezett helyszíni Azure Migrate berendezést használ, hogy folyamatosan felderítse a kiszolgálók értékelését és áttelepítését. A készülék figyeli a környezet változásait, például virtuális gépek, lemezek vagy hálózati adapterek hozzáadását. Emellett metaadatokat és teljesítményadatokat is küld az Azure-nak. Meg kell állapítani, hogy hány berendezést kell üzembe helyezni.


## <a name="planning-limits"></a>Tervezési korlátok
 
Használja az ebben a táblázatban foglalt korlátokat a tervezéshez.

**Tervezés** | **Korlátok**
--- | --- 
**Azure Migrate projektek** | Egy projektben akár 35 000-kiszolgálót is megvizsgálhat.
**Azure Migrate-berendezés** | Egy készülék legfeljebb 1000 kiszolgálót képes felderíteni.<br/> Egy berendezés csak egyetlen Azure Migrate projekthez társítható.<br/> Tetszőleges számú készülék társítható egyetlen Azure Migrate-projekthez is. <br/><br/> 
**Csoport** | Egyetlen csoportban akár 35 000-kiszolgálót is hozzáadhat.
**Azure Migrate Értékelés** | Egyetlen értékeléssel akár 35 000-kiszolgálót is megvizsgálhat.


## <a name="other-planning-considerations"></a>Egyéb tervezési szempontok

- A felderítés a készülékből való indításához ki kell választania az egyes fizikai kiszolgálókat. 

## <a name="prepare-for-assessment"></a>Felkészülés az értékelésre

Készítse elő az Azure-t és a fizikai kiszolgálókat a kiszolgálók értékeléséhez. 

1. Ellenőrizze [a fizikai kiszolgáló támogatási követelményeit és korlátozásait](migrate-support-matrix-physical.md).
2. Az Azure-fiók engedélyeinek beállítása a Azure Migrate való interakcióhoz.
3. Készítse elő a fizikai kiszolgálókat.

A beállítások konfigurálásához kövesse az [oktatóanyag](tutorial-prepare-physical.md) utasításait.

## <a name="create-a-project"></a>Projekt létrehozása

A tervezési követelményekkel összhangban tegye a következőket:

1. Azure Migrate-projekt létrehozása.
2. Adja hozzá a Azure Migrate Server Assessment eszközt a projektekhez.

[További információ](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Értékelés létrehozása és áttekintése

1. A fizikai kiszolgálók értékelésének létrehozása.
1. Tekintse át az áttelepítési tervezés előkészítésének értékelését.

[További](tutorial-assess-physical.md) információ az értékelések létrehozásáról és áttekintéséről.
    

## <a name="next-steps"></a>További lépések

Ebben a cikkben:
 
> [!div class="checklist"] 
> * A fizikai kiszolgálók Azure Migrate értékelésének tervezett méretezése.
> * Előkészített Azure-és fizikai kiszolgálók az értékeléshez.
> * Létrehozott egy Azure Migrate projektet, és futtatta az értékeléseket.
> * Az áttelepítésre való felkészülés során felülvizsgálták az értékeléseket.

Most [megtudhatja, hogyan](concepts-assessment-calculation.md) számítja ki az értékeléseket, és hogyan [módosíthatja az értékeléseket](how-to-modify-assessment.md).

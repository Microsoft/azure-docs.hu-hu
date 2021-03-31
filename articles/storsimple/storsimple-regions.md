---
title: StorSimple régió elérhetősége
description: Ismerteti azokat az Azure-régiókat, amelyekben elérhetők a különböző StorSimple-eszközök.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/16/2017
ms.author: alkohli
ms.openlocfilehash: 5efb87ad674474b2e21b86b5cdc48785753a51dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90564151"
---
# <a name="available-regions-for-your-storsimple"></a>A StorSimple elérhető régiók

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Áttekintés

Az Azure-adatközpontok világszerte több földrajzi régión működnek, hogy megfeleljenek az ügyfelek által támasztott teljesítményre, követelményekre és preferenciára vonatkozó igényeknek. Az Azure földrajz a világ egy meghatározott területe, amely legalább egy Azure-régiót tartalmaz. Az Azure-régió egy földrajzon belüli terület, amely egy vagy több adatközpontot tartalmaz.

Az Azure-régió kiválasztása nagyon fontos, és a választott régiót olyan tényezők befolyásolják, mint például az adattárolás és a szuverenitás, a szolgáltatás rendelkezésre állása, a teljesítmény, a költséghatékonyság és a redundancia. A régió kiválasztásával kapcsolatos további információkért keresse fel az [Azure-régiót?](https://azure.microsoft.com/overview/datacenters/how-to-choose/)

A StorSimple-megoldás esetében a régió választását kifejezetten a következő tényezők határozzák meg:

- Azok a régiók, amelyeken a StorSimple Eszközkezelő szolgáltatás elérhető.
- Azok az országok/régiók, ahol a StorSimple fizikai, Felhőbeli vagy virtuális eszköze elérhető.
- Az optimális teljesítmény érdekében az a régió, ahol a StorSimple tárolására szolgáló Storage-fiókok találhatók.

Ez az oktatóanyag a StorSimple Eszközkezelő szolgáltatás, a helyszíni fizikai és a felhőalapú eszközök régiójának rendelkezésre állását ismerteti. A cikkben szereplő információk a StorSimple 8000 és a 1200 Series eszközökre vonatkoznak.

## <a name="region-availability-for-storsimple-device-manager-service"></a>Régió rendelkezésre állása StorSimple Eszközkezelő szolgáltatáshoz

A StorSimple Eszközkezelő szolgáltatás jelenleg 12 nyilvános régióban és 2 Azure Government régióban támogatott.

A StorSimple Eszközkezelő szolgáltatás első létrehozásakor meg kell adnia egy régiót vagy helyet. Általánosságban elmondható, hogy a földrajzi régióhoz legközelebb eső hely van kiválasztva, ahol az eszköz telepítve van. Az eszköz és a szolgáltatás azonban különböző helyszíneken is üzembe helyezhető.

Itt találja azokat a régiókat, ahol a StorSimple Eszközkezelő szolgáltatás elérhető az Azure nyilvános felhőben, és üzembe helyezhető.

![Képernyőfelvétel: a StorSimple Eszközkezelő a hely listáról kiemelve.](./media/storsimple-region/storsimple-device-manager-service-regions.png)

Azure Government felhő esetén a StorSimple Eszközkezelő szolgáltatás US Gov Iowa és US Gov Virginia adatközpontokban érhető el.

## <a name="region-availability-for-data-stored-in-storsimple"></a>Régió rendelkezésre állása a StorSimple tárolt adattároláshoz

A StorSimple adatai fizikailag tárolódnak az Azure Storage-fiókokban, és ezek a fiókok az összes Azure-régióban elérhetők. Azure Storage-fiók létrehozásakor a rendszer kiválasztja a Storage-fiók elsődleges helyét, és meghatározza azt a régiót, ahol az adat található.

Amikor először hoz létre egy StorSimple Eszközkezelő szolgáltatást, és társít hozzá egy Storage-fiókot, a StorSimple Eszközkezelő szolgáltatás és az Azure Storage két különálló helyen lehet. Ilyen esetben a StorSimple-eszközkezelő fiókot és az Azure-tárfiókot külön kell létrehoznia.

Általánosságban a Storage-fiókhoz válassza ki a legközelebbi régiót a szolgáltatáshoz. Előfordulhat azonban, hogy a legközelebbi Microsoft Azure régió valójában nem a legalacsonyabb késéssel rendelkező régió. Ez a késés, amely a hálózati szolgáltatás teljesítményét és így a megoldás teljesítményét diktálja. Tehát ha egy másik régióban kíván Storage-fiókot választani, fontos tudni, hogy mi a késés a szolgáltatás és a Storage-fiókhoz társított régió között.

Ha StorSimple Cloud Appliance használ, javasoljuk, hogy a szolgáltatás és a társított Storage-fiók ugyanabban a régióban legyen. Egy másik régióban lévő Storage-fiókok gyenge teljesítményt eredményezhetnek.

## <a name="availability-of-storsimple-device"></a>A StorSimple-eszköz rendelkezésre állása

A modelltől függően a StorSimple-eszközök különböző földrajzi egységekben vagy országokban/régiókban is elérhetők.

### <a name="storsimple-physical-device-models-81008600"></a>StorSimple fizikai eszköz (8100/8600-es modellek)

Ha StorSimple 8100 vagy 8600 fizikai eszközt használ, az eszköz a következő országokban/régiókban érhető el.

| #  | Ország/régió        | #  | Ország/régió     | #  | Ország/régió      | #  | Ország/régió             |
|----|-----------------------|----|--------------------|----|---------------------|----|----------------------------|
| 1  | Ausztrália             | 16 | Hongkong (KKT)      | 31 | Új-Zéland         | 46 | Dél-afrikai Köztársaság               |
| 2  | Ausztria               | 17 | Magyarország            | 32 | Nigéria             | 47 | Dél-Korea                |
| 3  | Bahrein               | 18 | Izland            | 33 | Norvégia              | 48 | Spanyolország                      |
| 4  | Belgium               | 19 | India              | 34 | Peru                | 49 | Srí Lanka                  |
| 5  | Brazília                | 20 | Indonézia          | 35 | Fülöp-szigetek         | 50 | Svédország                     |
| 6  | Kanada                | 21 | Írország            | 36 | Lengyelország              | 51 | Svájc                |
| 7  | Chile                 | 22 | Izrael             | 37 | Portugália            | 52 | Tajvan                     |
| 8  | Kolumbia              | 23 | Olaszország              | 38 | Puerto Rico         | 53 | Thaiföld                   |
| 9  | Cseh Köztársaság        | 24 | Japán              | 39 | Katar               | 54 | Törökország                     |
| 10 | Dánia               | 25 | Kenya              | 40 | Románia             | 55 | Ukrajna                    |
| 11 | Egyiptom                 | 26 | Kuvait             | 41 | Oroszország              | 56 | Egyesült Arab Emírségek       |
| 12 | Finnország               | 27 | Makaó (KKT)          | 42 | Szaúd-Arábia        | 57 | Egyesült Királyság             |
| 13 | Franciaország                | 28 | Malajzia           | 43 | Szingapúr           | 58 | Egyesült Államok              |
| 14 | Németország               | 29 | Mexikó             | 44 | Szlovákia            | 59 | Vietnam                    |
| 15 | Görögország                | 30 | Hollandia        | 45 | Szlovénia            | 60 | Horvátország                    |

Ez a lista több ország/régió hozzáadásával változik. A földrajzi területek legnaprakészebb listáját a [termék használati feltételeinek](https://www.microsoft.com/en-us/licensing/product-licensing/products)a tárolási tömb feltételei függelékében olvashatja.

A Microsoft elvégezheti a fizikai hardverek szállítását, és hardveres pótalkatrészeket állíthat be a StorSimple az előző listán szereplő földrajzi helyekre.

> [!IMPORTANT]
> Ne helyezzen StorSimple fizikai eszközt olyan régióba, ahol a StorSimple nem támogatott. A Microsoft nem tud semmilyen olyan pótalkatrészt szállítani az országba/régiókba, ahol a StorSimple nem támogatott.

### <a name="storsimple-cloud-appliance-models-80108020"></a>StorSimple Cloud Appliance (modellek 8010/8020)

Ha StorSimple Cloud Appliance 8010 vagy 8020-es verzióval rendelkezik, az eszköz minden olyan régióban támogatott és elérhető, ahol a mögöttes virtuális gép támogatott. A 8010 az összes Azure-régióban támogatott _Standard_A3_ virtuális gépet használ.

A 8020 Premium Storage-t és _Standard_DS3_ virtuális gépet használ felhőalapú berendezés létrehozásához. Az 8020-es verzió az Azure _Standard_DS3_ -beli virtuális gépeket támogató Azure-régiókban támogatott Premium Storage. [Ebben a listában](https://azure.microsoft.com/regions/services/) ellenőrizheti, hogy a **Virtuális gépek &gt; DS-sorozat** és a **Storage &gt; Disk Storage** termékek egyaránt elérhetők-e az adott régióban.

### <a name="storsimple-virtual-array-model-1200"></a>StorSimple virtuális tömb (1200-es modell)

Ha a StorSimple egy 1200 sorozatú virtuális tömböt használ, akkor a virtuális lemez lemezképe az összes Azure-régióban támogatott.

## <a name="next-steps"></a>Következő lépések

* További információ a [különböző StorSimple-modellek díjszabásáról](https://azure.microsoft.com/pricing/calculator/#storsimple2).
* További információ [a StorSimple Storage-fiók kezeléséről](storsimple-8000-manage-storage-accounts.md).
* További információ arról, hogyan [használható a StorSimple Eszközkezelő szolgáltatás a StorSimple-eszköz felügyeletéhez](storsimple-8000-manager-service-administration.md).

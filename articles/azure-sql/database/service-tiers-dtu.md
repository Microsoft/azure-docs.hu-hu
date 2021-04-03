---
title: Szolgáltatási szintek – DTU-alapú vásárlási modell
description: Ismerje meg a DTU-alapú vásárlási modellben a számítási és a tárolási méretek biztosításához Azure SQL Database szolgáltatási rétegeit.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: references_regions
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.date: 10/15/2020
ms.reviewer: ''
ms.openlocfilehash: 19178359d1eeb935499a01828f7c53b123e17571
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793177"
---
# <a name="service-tiers-in-the-dtu-based-purchase-model"></a>A DTU-alapú vásárlási modell szolgáltatásszintjei
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A DTU-alapú vásárlási modellben a szolgáltatási szintek különböző számítási méretekből állnak, amelyek rögzített mennyiségű foglalt tárterülettel, rögzített megőrzési időtartammal rendelkeznek a biztonsági mentésekhez és a rögzített árakhoz képest. A DTU-alapú vásárlási modell minden szolgáltatási szintje rugalmasságot biztosít a számítási méretek minimális [állásidővel](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/)való módosításához. van azonban egy olyan időszak, amelyben a kapcsolat megszakadt az adatbázisba rövid idő alatt, ami az újrapróbálkozási logika használatával enyhíthető. Az önálló adatbázisok és a rugalmas készletek számlázása óránként, a szolgáltatási réteg és a számítási méret alapján történik.

> [!IMPORTANT]
> Az [Azure SQL felügyelt példánya](../managed-instance/sql-managed-instance-paas-overview.md) nem támogatja a DTU-alapú vásárlási modellt. 


> [!NOTE]
> További információ a virtuális mag-alapú szolgáltatási rétegekről: [virtuális mag-alapú szolgáltatási szintek](service-tiers-vcore.md). További információ a DTU-alapú szolgáltatási szintek és a virtuális mag-alapú szolgáltatási szintek megkülönböztetéséről: [modellek beszerzése](purchasing-models.md).

## <a name="compare-the-dtu-based-service-tiers"></a>A DTU-alapú szolgáltatási Rétegek összehasonlítása

A szolgáltatási szint kiválasztása elsősorban az üzletmenet folytonossága, a tárterület és a teljesítménnyel kapcsolatos követelményektől függ.

||Alapszintű|Standard|Prémium|
| :-- | --: |--:| --:|
|**Cél munkaterhelés**|Fejlesztés és gyártás|Fejlesztés és gyártás|Fejlesztés és gyártás|
|**Rendelkezésre állási SLA**|99.99%|99.99%|99.99%|
|**Biztonsági másolatok maximális megőrzése**|7 nap|35 nap|35 nap|
|**CPU**|Alacsony|Alacsony, közepes és magas|Közepes, magas|
|**IOPS (hozzávetőleges)**\* |1-4 IOPS/DTU| 1-4 IOPS/DTU | 25 IOPS/DTU|
|**IO-késés (hozzávetőleges)**|5 MS (olvasás), 10 MS (írás)|5 MS (olvasás), 10 MS (írás)|2 MS (olvasás/írás)|
|**Oszlopcentrikus indexelése** |N/A|S3 és újabb verziók|Támogatott|
|**Memóriában tárolt OLTP**|N.A.|N.A.|Támogatott|

\* Az adatfájlok minden olvasási és írási IOPS, beleértve a háttér i/o-t (ellenőrzőpont és lusta író)

> [!IMPORTANT]
> Az alapszintű, a S0, az S1 és az S2 szolgáltatási célkitűzések kevesebb mint egy virtuális mag (CPU) biztosítanak.  A CPU-igényes számítási feladatokhoz az S3 vagy újabb szolgáltatási cél javasolt. 
>
> Az alapszintű, a S0 és az S1 szolgáltatási célkitűzésekben az adatbázisfájlok tárolása az Azure standard Storage-ban történik, amely merevlemez-(HDD-) alapú adathordozót használ. Ezek a szolgáltatási célkitűzések a legjobb fejlesztési, tesztelési és egyéb ritkán használt számítási feladatokhoz ideálisak, amelyek kevésbé érzékenyek a teljesítmény változékonyságára.
>

> [!TIP]
> Ha meg szeretné tekinteni egy adatbázis vagy rugalmas készlet tényleges [erőforrás-irányítási](resource-limits-logical-server.md#resource-governance) korlátait, kérdezze le a [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) nézetet.

> [!NOTE]
> Az alapszintű szolgáltatási szinten Azure SQL Database ingyenes adatbázist szerezhet be az Azure-ban az ingyenes Azure-fiókkal együtt. További információkért lásd: [felügyelt felhőalapú adatbázis létrehozása az ingyenes Azure-fiókkal](https://azure.microsoft.com/free/services/sql-database/).

## <a name="single-database-dtu-and-storage-limits"></a>Önálló adatbázis-DTU és tárterület-korlátok

A számítási méretek az önálló adatbázisok és a rugalmas adatbázis-tranzakciós egységek (Edtu-EK) adatbázis-tranzakciós egységei (DTU) alapján vannak kifejezve. A DTU és Edtu kapcsolatos további információkért lásd: [DTU-alapú vásárlási modell](purchasing-models.md#dtu-based-purchasing-model).

||Alapszintű|Standard|Prémium|
| :-- | --: | --: | --: |
| **Maximális tárterület** | 2 GB | 1 TB | 4 TB  |
| **Maximális DTU** | 5 | 3000 | 4000 |

> [!IMPORTANT]
> Bizonyos körülmények között előfordulhat, hogy az adatbázist fel kell zsugorodnia a fel nem használt területek visszaigényléséhez. További információ: [a tárterület kezelése Azure SQL Databaseban](file-space-manage.md).

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>Rugalmas készlet eDTU, tárolás és készletezett adatbázisok korlátai

|| **Basic** | **Standard** | **Prémium** |
| :-- | --: | --: | --: |
| **Tárterület maximális mérete adatbázison**  | 2 GB | 1 TB | 1 TB |
| **Tárterület maximális mérete készlet szerint** | 156 GB | 4 TB | 4 TB |
| **Edtu maximális száma** | 5 | 3000 | 4000 |
| **Edtu maximális száma** | 1600 | 3000 | 4000 |
| **Adatbázisok maximális száma készlet szerint** | 500  | 500 | 100 |

> [!IMPORTANT]
> A prémium szinten több mint 1 TB tárterület jelenleg minden régióban elérhető, kivéve a következőket: Kelet-Kína, Észak-Kína, Közép-Németország és Németország északkeleti régiója. Ezekben a régiókban a prémium szintű Storage Max 1 TB-ra van korlátozva.  További információ: [P11-P15 current korlátozások](single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  
> [!IMPORTANT]
> Bizonyos körülmények között előfordulhat, hogy az adatbázist fel kell zsugorodnia a fel nem használt területek visszaigényléséhez. További információ: [a tárterület kezelése Azure SQL Databaseban](file-space-manage.md).

## <a name="dtu-benchmark"></a>DTU-teljesítményteszt

Az egyes DTU-mértékekhez társított fizikai jellemzők (CPU, memória, IO) egy olyan teljesítményteszt használatával vannak kalibrálva, amely szimulálja a valós adatbázis-számítási feladatokat.

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Teljesítményteszt eredményeinek összekorrelációja a valós adatbázis-teljesítményhez

Fontos tisztában lenni azzal, hogy az összes teljesítményteszt csak reprezentatív és csak tájékoztató jellegű. A teljesítményteszt-alkalmazás által elért tranzakciók aránya nem ugyanaz, mint más alkalmazásokkal. A teljesítményteszt egy különböző tranzakciótípusok gyűjteményét tartalmazza, amely egy, a táblákat és az adattípusokat tartalmazó sémán fut. Míg a teljesítményteszt ugyanazokat az alapszintű műveleteket alkalmazza, amelyek az összes OLTP-számítási feladatnál közösek, nem az adatbázis vagy alkalmazás adott osztályát jelöli. A viszonyítási alap célja, hogy ésszerű útmutatást nyújtson egy olyan adatbázis relatív teljesítményéhez, amely várhatóan a számítási méretek közötti felfelé vagy lefelé történő skálázás esetén várható. A valóságban az adatbázisok különböző méretűek és összetettebbek, a számítási feladatok különböző kombinációit tapasztalják, és különböző módokon reagálnak. Például egy IO-igényes alkalmazás hamarabb elérheti az IO-küszöbértékeket, vagy a CPU-igényes alkalmazások hamarabb megkaphatják a CPU-korlátokat. Nincs garancia arra, hogy az adott adatbázis a terhelés növelésével megegyező módon fog méretezni.

Az alábbiakban részletesebben ismertetjük a teljesítménytesztet és annak módszerét.

### <a name="benchmark-summary"></a>Teljesítményteszt összefoglalása

A teljesítményteszt a leggyakrabban az online tranzakció-feldolgozási (OLTP) számítási feladatokban leggyakrabban előforduló alapszintű adatbázis-műveletek együttes teljesítményét méri. Bár a teljesítménytesztet a felhőalapú számítástechnika szem előtt tartásával tervezték, az adatbázis-sémát, az adatpopulációt és a tranzakciókat úgy alakítottuk ki, hogy széles körben reprezentatívak legyenek a OLTP-munkaterhelésekben leggyakrabban használt alapelemek esetében.

### <a name="schema"></a>Séma

A séma úgy lett kialakítva, hogy elegendő változatosságot és összetettséget nyújtson a műveletek széles körének támogatásához. A teljesítményteszt hat táblázatból álló adatbázison fut. A táblázatok három kategóriába sorolhatók: rögzített méretű, méretezés és növekvő. Két rögzített méretű tábla létezik; három skálázási táblázat; és egy növekvő tábla. A rögzített méretű táblák állandó számú sorral rendelkeznek. A skálázási táblázatok olyan számos fajta, amely az adatbázis teljesítményével arányos, de a teljesítményteszt során nem változik. A növekvő tábla a kezdeti betöltéshez hasonló méretezési táblázattal rendelkezik, de ezt követően a rendszer a teljesítményteszt futtatásának menetét is megváltoztatja a sorok beillesztése és törlése során.

A séma többféle adattípust tartalmaz, beleértve az egész számot, a számot, a karaktert és a dátumot és az időt. A séma elsődleges és másodlagos kulcsokat is tartalmaz, de nem rendelkezik idegen kulcsokkal – vagyis nincsenek a táblák közötti hivatkozási integritási korlátozások.

Az adatgeneráló program létrehozza a kezdeti adatbázishoz tartozó adatértékeket. Az egész szám és a numerikus adat különböző stratégiákkal jön létre. Bizonyos esetekben az értékek eloszlása véletlenszerűen történik egy tartományon belül. Más esetekben az értékek egy halmaza véletlenszerűen permuted, így biztosítható, hogy egy adott eloszlás megmaradjon. A szöveges mezők a szavak súlyozott listájából jönnek létre, amelyek reális megjelenési adatok előállítására szolgálnak.

Az adatbázis méretezési tényezőn alapul. A méretezési tényező (SF-ként rövidítve) meghatározza a skálázási és a növekvő táblákat. A következő szakaszban leírtak szerint a felhasználók és a tempó, az adatbázis mérete, a felhasználók száma és a maximális teljesítmény az összes méretezés arányában.

### <a name="transactions"></a>Tranzakciók

A munkaterhelés kilenc tranzakciós típusból áll, ahogy az alábbi táblázatban is látható. Minden tranzakció úgy van kialakítva, hogy kiemelje a rendszerjellemzők egy adott készletét az adatbázismotor és a rendszer hardverén, nagy kontraszttal a többi tranzakciótól. Ezzel a megközelítéssel könnyebben mérhetővé válik a különböző összetevők hatása a teljes teljesítményre. Például a "READ Heavy" tranzakció jelentős számú olvasási műveletet eredményez a lemezről.

| Transaction Type (Tranzakció típusa) | Leírás |
| --- | --- |
| A Lite olvasása |Válassza memóriában tárolt; csak olvasható |
| Adathordozó olvasása |Válassza többnyire a memóriában; csak olvasható |
| Jelentős olvasási |Válassza többnyire nincs a memóriában; csak olvasható |
| A Lite frissítése |FRISSÍTÉSE memóriában tárolt; írás és olvasás |
| Nagy frissítés |FRISSÍTÉSE többnyire nincs a memóriában; írás és olvasás |
| Lite beszúrása |INSERT memóriában tárolt; írás és olvasás |
| Nehéz Beszúrás |INSERT többnyire nincs a memóriában; írás és olvasás |
| Törlés |TÖRLÉSE memóriában tárolt és nem memóriában tárolt értékek írás és olvasás |
| Nagy CPU |Válassza memóriában tárolt; viszonylag nagy CPU-terhelés; csak olvasható |

### <a name="workload-mix"></a>Munkaterhelés-kombináció

A tranzakciók véletlenszerűen vannak kiválasztva egy súlyozott eloszlásból a következő összesített kombinációval. A teljes kombináció körülbelül 2:1 olvasási/írási aránnyal rendelkezik.

| Transaction Type (Tranzakció típusa) | Vegyes |
| --- | --- |
| A Lite olvasása |35 |
| Adathordozó olvasása |20 |
| Jelentős olvasási |5 |
| A Lite frissítése |20 |
| Nagy frissítés |3 |
| Lite beszúrása |3 |
| Nehéz Beszúrás |2 |
| Törlés |2 |
| Nagy CPU |10 |

### <a name="users-and-pacing"></a>Felhasználók és a tempó

A teljesítményteszt számítási feladatait egy olyan eszköz vezérli, amely több kapcsolaton keresztül küld tranzakciókat a több egyidejű felhasználó viselkedésének szimulálása érdekében. Bár az összes kapcsolat és tranzakció a gép által generált, az egyszerűség kedvéért ezeket a kapcsolatokat a "felhasználók" kifejezéssel tekintjük át. Bár minden felhasználó az összes többi felhasználótól függetlenül működik, az összes felhasználó ugyanazon ciklusban hajtja végre az alábbi lépéseket:

1. Hozzon létre egy adatbázis-kapcsolatot.
2. Ismételje meg a jelet a kilépésig:
   - Válassza ki a tranzakciót véletlenszerűen (súlyozott eloszlásból).
   - Hajtsa végre a kiválasztott tranzakciót, és mérje fel a válaszidőt.
   - Várjon egy tempót.
3. Az adatbázis-kapcsolatok lezárása.
4. Kilépési.

Az ingerlési késleltetés (a 2c. lépésben) véletlenszerűen van kiválasztva, de egy átlagos 1,0 másodperces eloszlással. Így minden felhasználó átlagosan legfeljebb egy tranzakciót tud előállítani másodpercenként.

### <a name="scaling-rules"></a>Skálázási szabályok

A felhasználók számát az adatbázis mérete határozza meg (a skála faktoros egységekben). Minden öt méretezési faktoros egységhez egyetlen felhasználó van. A késés miatt az egyik felhasználó másodpercenként legfeljebb egy tranzakciót tud előállítani.

Például az 500-es (SF = 500) adatbázis skálázási tényezője 100 felhasználó lesz, és a maximálisan engedélyezett 100 TPS. Magasabb TPS arány növeléséhez több felhasználónak és egy nagyobb adatbázisnak kell lennie.

### <a name="measurement-duration"></a>Mérési időtartam

Egy érvényes teljesítményteszt-futtatáshoz legalább egy óra egyenletes állapotú mérési időtartamra van szükség.

### <a name="metrics"></a>Mérőszámok

A teljesítményteszt fő mérőszámai az átviteli sebesség és a válaszidő.

- Az átviteli sebesség a teljesítményteszt alapvető teljesítményének mértéke. Az átviteli sebesség az egyes egységenkénti tranzakciókban, az összes tranzakciótípus számlálásával van jelezve.
- A válaszidő a teljesítmény kiszámíthatóságának mértéke. A válaszidő-korlátozás a szolgáltatási osztálytól eltérő, a magasabb szintű szolgáltatási osztályokkal pedig szigorúbb válaszidő szükséges, ahogy az alább látható.

| Szolgáltatás osztálya | Átviteli sebesség mértéke | Válaszadási idő követelménye |
| --- | --- | --- |
| Prémium |Tranzakció/másodperc |95. percentilis 0,5 másodpercnél |
| Standard |Percenkénti tranzakciók |90 százalék 1,0 másodpercnél |
| Alapszintű |Tranzakció/óra |80th percentilis 2,0 másodpercnél |

## <a name="next-steps"></a>Következő lépések

- Az önálló adatbázisok esetében elérhető számítási méretekről és a tárolási méretekről az önálló [adatbázisok SQL Database DTU-alapú erőforrás-korlátozásai](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes)című témakörben olvashat bővebben.
- Az adott számítási méretek és a rugalmas készletekhez rendelkezésre álló tárolási méretek részleteiért lásd: [SQL Database DTU-alapú erőforrás-korlátok](resource-limits-dtu-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
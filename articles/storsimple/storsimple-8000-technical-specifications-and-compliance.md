---
title: StorSimple műszaki specifikációk | Microsoft Docs
description: Ismerteti a technikai specifikációkat és a szabályozási szabványoknak a StorSimple hardveres összetevőire vonatkozó megfelelőségi információkat.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: 061194422a8c1bc449dbef0c4f04bb8e1db10dea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "68965288"
---
# <a name="technical-specifications-and-compliance-for-the-storsimple-device"></a>A StorSimple-eszköz műszaki specifikációi és megfelelőségi adatai

## <a name="overview"></a>Áttekintés

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

A Microsoft Azure StorSimple eszköz hardveres összetevői megfelelnek a jelen cikkben ismertetett technikai előírásoknak és előírásoknak. A műszaki specifikációk az energiagazdálkodási és hűtési modulokat (PCMs), a lemezmeghajtókat, a tárolókapacitást és a bekerítéseket ismertetik. A megfelelőségi információk olyan dolgokra terjednek ki, mint a nemzetközi szabványok, a biztonság és a kibocsátás, valamint a kábelezés.

## <a name="power-and-cooling-module-specifications"></a>A Power és a hűtés modul specifikációi

A StorSimple-eszköz két 100-240 V Dual ventilátorral, az SBB-kompatibilis energiagazdálkodási modulokkal (PCMs) rendelkezik. Ez redundáns energiaellátási konfigurációt biztosít. Ha egy PCM meghibásodása meghiúsul, az eszköz továbbra is a szokásos módon működik a másik PCM-ben, amíg meg nem történik a hibás modul cseréje.

A EBOD-kamra 580 W PCM-et használ, és az elsődleges kamra 764 W PCM-t használ. A következő táblázatok felsorolják a PCMs kapcsolatos műszaki specifikációkat.

| Specifikáció | 580 W PCM (EBOD) | 764 W PCM (elsődleges) |
| --- | --- | --- |
| Maximális kimeneti teljesítmény |580 W |764 |
| Gyakoriság |50/60 Hz |50/60 Hz |
| Feszültség-tartomány kiválasztása |Automatikus hatókör: 90 – 264 V AC, 47/63 Hz |Automatikus hatókör: 90-264 V AC, 47/63 Hz |
| Maximális inrush-áramerősség |20 A |20 A |
| Teljesítménytényező javítása |>95%-os névleges bemeneti feszültség |>95%-os névleges bemeneti feszültség |
| Harmonikus |Megfelel a EN61000-3-2 |Megfelel a EN61000-3-2 |
| Kimenet |5V-os készenléti feszültség \@ 2,0 A |5V-os készenléti feszültség \@ 2,7 A |
| + 5V \@ 42 A |+ 5V \@ 40 A | |
| + 12V \@ 38 A |+ 12V \@ 38 A | |
| Gyors csatlakoztatás |Igen |Yes |
| Kapcsolók és LED-EK |AC be-és kikapcsoló kapcsoló és négy állapotjelző LED |AC be-és kikapcsoló kapcsoló és hat állapotjelző LED |
| Ház hűtése |Axiális hűtési ventilátorok változó ventilátoros fordulatszám-vezérléssel |Axiális hűtési ventilátorok változó ventilátoros fordulatszám-vezérléssel |

## <a name="power-consumption-statistics"></a>Energiafogyasztási statisztika

A következő táblázat felsorolja a tipikus energiafogyasztási adatokat (a tényleges értékek a közzétetttől függően változhatnak) a különböző StorSimple-eszközökhöz.

| Feltételek | 240 V AC | 240 V AC | 240 V AC | 110 V AC | 110 V AC | 110 V AC |
| --- | --- | --- | --- | --- | --- | --- |
|  A ventilátorok lassúak, a meghajtók tétlenek |1,45 A |0,31 kW |1057,76 BTU/HR |3,19 A |0,34 kW |1160,13 BTU/HR |
|  A ventilátorok lassúak, a meghajtókhoz való hozzáférés |1,54 A |0,33 kW |1126,01 BTU/HR |3,27 A |0,36 kW |1228,37 BTU/HR |
|  A ventilátorok gyorsak, a meghajtók tétlenek, két tápegység |2,14 A |0,49 kW |1671,95 BTU/HR |4,99 A |0,54 kW |1842,56 BTU/HR |
|  A ventilátorok gyorsak, a meghajtók tétlenek, az egyik PSU egy tétlen |2,05 A |0,48 kW |1637,83 BTU/HR |4,58 A |0,50 kW |1706,07 BTU/HR |
|  Gyorsan elérhető ventilátorok |2,26 A |0,51 kW |1740,19 BTU/HR |4,95 A |0,54 kW |1842,56 BTU/HR |
|  A ventilátorok gyorsak, a hozzájuk tartozó meghajtók egy tétlen |2,14 A |0,49 kW |1671,95 BTU/HR |4,81 A |0,53 kW |1808,44 BTU/HR |

## <a name="disk-drive-specifications"></a>Lemezmeghajtó-specifikációk

A StorSimple-eszköz legfeljebb 12 3,5 hüvelykes, lemezhez csatlakoztatott SCSI-(SAS-) lemezmeghajtót támogat. A tényleges meghajtók a termék konfigurációjától függően szilárdtest-meghajtók (SSD-k) vagy merevlemez-meghajtók (HDD-k) együttesek lehetnek. A 12 lemezmeghajtó-tárolóhelyek a ház előtt 3 – 4 konfigurációban találhatók. A EBOD ház további tárhelyet tesz lehetővé egy másik 12 lemezmeghajtó számára. Ezek mindig HDD-k.

## <a name="storage-specifications"></a>Tárolási specifikációk

A StorSimple-eszközök a 8100-es és a 8600-os merevlemez-meghajtók és SSD-meghajtók kombinációját is felhasználhatják. Az 8100-es és a 8600-as teljes felhasználható kapacitás körülbelül 15 TB és 38 TB. Az alábbi táblázat az SSD-, a HDD-és a felhő-kapacitás részleteit ismerteti a StorSimple-megoldási kapacitás kontextusában.

| Eszköz modellje/kapacitása | 8100 | 8600 |
| --- | --- | --- |
| Merevlemez-meghajtók (HDD-k) száma |8 |19 |
| SSD-meghajtók száma |4 |5 |
| Egyetlen HDD-kapacitás |4 TB |4 TB |
| Egyetlen SSD-kapacitás |400 GB |800 GB |
| Tartalék kapacitás |4 TB |4 TB |
| Használható HDD-kapacitás |14 TB |36 TB |
| Használható SSD-kapacitás |800 GB |2 TB |
| Teljes felhasználható kapacitás * |~ 15 TB |~ 38 TB |
| A megoldás maximális kapacitása (beleértve a felhőt is) |200 TB |500 TB |

<sup>* </sup>- *A teljes felhasználható kapacitás magában foglalja az adatok, a metaadatok és a pufferek számára rendelkezésre álló kapacitást. Az 8100-es eszközön legfeljebb 8,5 TB méretű, helyileg rögzített köteteket hozhat létre, a nagyobb 8600-eszközön pedig akár 22,5 TB-ot is kiépítve. További információért lépjen a [StorSimple helyileg rögzített kötetek](storsimple-8000-local-volume-faq.md)elemre.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>A bekerítés méretei és súlyozási jellemzői

A következő táblázatok a méretek és a súlyozás különböző területekre vonatkozó specifikációit sorolja fel.

### <a name="enclosure-dimensions"></a>Bekerítés méretei

A következő táblázat a ház dimenzióit mutatja milliméterben és hüvelykben.

| Ház | Milliméter | Hüvelyk |
| --- | --- | --- |
| Magasság |87,9 |3,46 |
| Szélesség a csatlakoztatási karimával |483 |19,02 |
| A bekerítés törzsének szélessége |443 |17,44 |
| Az első csatlakoztatási karimás mélysége a bekerítés törzséhez |577 |22,72 |
| Az operatív panel mélysége a bekerítés legtávolabbi részének |630,5 |24,82 |
| A beépítési karimák legtávolabbi részének mélysége |603 |23,74 |

### <a name="enclosure-weight"></a>Ház súlya

A konfigurációtól függően egy teljesen betöltött elsődleges ház 21 – 33 kg-ra is felhasználható, és két személyt igényel a kezeléséhez.

| Ház | Tömeg |
| --- | --- |
| Maximális súlyozás (a konfigurációtól függ) |30 kg – 33 kg |
| Üres (nincsenek ellátott meghajtók) |21 – 23 kg |

## <a name="enclosure-environment-specifications"></a>Bekerítési környezet specifikációi

Ez a szakasz a bekerítési környezettel kapcsolatos specifikációkat sorolja fel. Ebben a kategóriában a hőmérsékletet, a páratartalomot, a magasságot, a sokkot, a vibrációt, a tájolást, a biztonságot és az elektromágneses kompatibilitást (EMC) tartalmazza.

### <a name="temperature-and-humidity"></a>Hőmérséklet és páratartalom

| Ház | Környezeti hőmérséklet tartománya | Környezeti relatív páratartalom | Maximális nedves izzó |
| --- | --- | --- | --- |
| Működik |5 °C – 35 °C (41 °F-95 °F) |20%-80% nem kondenzációs – |28 °C (82 °F) |
| Nem működő |-40 °C-70 °C (40 °F – 158 °F) |5%-100% nem kondenzációs |29 °C (84 °F) |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Légáram, magasság, sokk, vibráció, orientáció, biztonság és EMC

| Ház | Üzemeltetési specifikációk |
| --- | --- |
| Légáramlás |A rendszer légáram elölről hátrafelé. A rendszert alacsony terhelésű, hátsó kimeneti telepítéssel kell működtetni. Az Állványos ajtók és akadályok által létrehozott ellennyomás nem lépheti túl az 5 Pascalt (0,5 mm-es vízmérőműszer). |
| Magasság, működés |-30 méter – 3045 méter (– 100 láb – 10 000 méter), az 7000 méternél nagyobb teljesítményű, de 5 °C-os működési hőmérséklettel. |
| Magasság, nem működő |-305 méter – 12 192 méter (-1 000 láb – 40 000 méter) |
| Sokk, működési |5G 10 MS 1/2 szinusz |
| Sokk, nem működő |30g 10 MS 1/2 sine |
| Vibráció, működési |0.21 g RMS 5-500 Hz véletlenszerű |
| Vibráció, nem működő |1,04 g RMS 2-200 Hz véletlenszerű |
| Vibráció, áthelyezés |3G 2-200 Hz szinusz |
| Tájolás és csatlakoztatás |19 "rack csatlakoztatása (2 EIA egység) |
| Rack Rails |A minimális 700 mm (31,50 hüvelyk) mélységű állványok méretének megfelelő, IEC 297-kompatibilis |
| Biztonság és jóváhagyások |CE és UL EN 61000-3, IEC 61000-3, UL 61000-3 |
| EMC |EN55022 (CISPR-A), FCC A |

## <a name="international-standards-compliance"></a>Nemzetközi szabványok megfelelősége

A Microsoft Azure StorSimple-eszköz megfelel a következő nemzetközi szabványoknak:  

* CE-EN 60950-1
* CB-jelentés az IEC 60950-1
* UL és Kul – UL 60950 – 1

## <a name="safety-compliance"></a>Biztonsági megfelelőség

A Microsoft Azure StorSimple eszköz megfelel a következő biztonsági minősítéseknek:

* Rendszer-terméktípus jóváhagyása: UL, KUL, CE
* Biztonsági megfelelőség: UL 60950, IEC 60950, EN 60950

## <a name="emc-compliance"></a>EMC-megfelelőség

A Microsoft Azure StorSimple eszköz megfelel a következő EMC-minősítéseknek.

### <a name="emissions"></a>Kibocsátás

Az eszköz EMC-kompatibilis a végrehajtott és a kisugárzott kibocsátási szintekhez.

* A kibocsátási határértékek elvégzése: a CFR 47 része a 15B osztály a CISPR osztály EN55022 osztálya
* Kisugárzott kibocsátási határértékek: CFR 47 rész 15B. osztály – a CISPR osztály EN55022 osztálya

### <a name="harmonics-and-flicker"></a>Harmonikus és vibrálás

Az eszköz megfelel a EN61000-3-2/3.

### <a name="immunity-limit-levels"></a>Mentelmi korlátok szintjei

Az eszköz megfelel a EN55024.

## <a name="ac-power-cord-compliance"></a>HÁLÓZATI tápkábel megfelelősége

A dugónak és a teljes tápkábel-szerelvénynek meg kell felelnie azon országnak/régiónak megfelelő szabványoknak, amelyben az eszköz használatban van, és az adott országban vagy régióban elfogadható biztonsági jóváhagyásokkal kell rendelkezniük. Az alábbi táblázatok felsorolják az USA és Európa szabványait.

### <a name="ac-power-cords---usa-must-be-nrtl-listed"></a>AC-tápegységek – USA (NRTL kell szerepelniük)

| Összetevő | Specifikáció |
| --- | --- |
| Kábel típusa |SV vagy SVT, 18 AWG minimum, 3 vezető, 2,0 méteres maximális hossz |
| Beépülő modul |NÉMA 5 – 15P, megalapozó típusú melléklet, 120 V, 10 A; vagy IEC 320 C14, 250 V, 10 A |
| Szoftvercsatorna |IEC 320 C-13, 250 V, 10 A |

### <a name="ac-power-cords---europe"></a>AC-tápkábelek – Európa

| Összetevő | Specifikáció |
| --- | --- |
| Kábel típusa |Harmonizált, H05-VVF-3G 1.0 |
| Szoftvercsatorna |IEC 320 C-13, 250 V, 10 A |

## <a name="supported-network-cables"></a>Támogatott hálózati kábelek

A 10 GbE hálózati adapter, a 2. és a 3. adatkapcsolat esetében tekintse meg a [támogatott hálózati kábelek és modulok listáját](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).

## <a name="next-steps"></a>Következő lépések

Most már készen áll egy StorSimple-eszköz üzembe helyezésére az adatközpontban. További információ: a [helyszíni eszköz üzembe helyezése](storsimple-8000-deployment-walkthrough-u2.md).


---
title: Microsoft Azure FXT Edge Filer-specifikációk | Microsoft Docs
description: Ismerje meg az Microsoft Azure FXT Edge Filer-hardver fizikai és környezeti specifikációit.
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: b1a172adb54593421ef56e09c131e0ddcaee9f6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92215824"
---
# <a name="azure-fxt-edge-filer-specifications"></a>Az Azure FXT Edge Filer-specifikációi

Ez a cikk az Azure FXT Edge Filer-hardveres csomópontjaival kapcsolatos hardver-specifikációkat ismerteti. A gyakorlatban három vagy több csomópont van konfigurálva a fürtözött gyorsítótárazási rendszer biztosításához.

## <a name="hardware-specifications"></a>Hardverspecifikációk

| Összetevő | FXT 6600 | FXT 6400 |
|----------|-----------|-----------|
| Processzormagok |  16 | 16 |
| DRAM  | 1536 GB | 768 GB |
| Hálózati portok | 6 x 25/10 GB + 2 x 1 GB | 6 x 25/10 GB + 2 x 1 GB |
| NVMe SSD-kapacitás | 25,6 TB | 12,8 TB |

## <a name="drive-specifications"></a>A meghajtó specifikációi

A rendszer 10 meghajtó-öblöt tartalmaz, amelyek elölről elérhetők. Az egyes feltöltött meghajtók a kapacitással kapcsolatos információkkal jobbra vannak címkézve.

A meghajtón lévő számok a meghajtók közötti területre vannak kinyomtatva. Az Azure FXT Edge Filer-ben a 0. meghajtó a bal felső meghajtó, az 1. meghajtó pedig közvetlenül alatta van.

![a FXT váz egyik merevlemez-öblében található, a meghajtók számát és a kapacitás feliratait ábrázoló fénykép](media/fxt-drives-photo.png)

| Meghajtók száma    |  Használat   |  Specifikációk |
|------------------|--------|-----------------|
| 0, 1             | Operációs rendszer     | 480 GB SATA SSD |
| 2, 3, 4, 5, 6, 7, 8, 9 | Adatok   | FXT 6600:3,2 TB NVMe SSD <br> FXT 6400:1,6 TB NVMe SSD |

## <a name="dimensions-and-weight"></a>Méretek és súlyozás

Az Azure FXT Edge Filer úgy lett kialakítva, hogy a standard 19 "berendezés-rackbe illeszkedjen, és egy rack-egység magas (1U).

<!-- 10x2.5 inches version -->

| Filer-méretek            | Érték                    |
|-----------------------------|--------------------------|
| Magasság                      | 42,8 mm (1,68 hüvelyk)    |
| Szélesség (beleértve a rack fület is) | 482,0 mm (18,97 hüvelyk)  |
| Szélesség – fő ház      | 434,0 mm (17,08 hüvelyk) |
| A fő ház hátoldalának mélysége – a rack fülei                   | 733,82 mm (29,61 hüvelyk) |
| Mélység – a rack fülei a legtávolabbi hátsó kiemelkedésig                 | 772,67 mm (30,42 hüvelyk) |
| Mélység – a fülek a legtávolabbi kezdeti kiemelkedésig, keret nélkül | 22,0 mm (0,87 hüvelyk)  |
| Mélység – a fülek legtávolabbi kiemelkedése az elõlaptal    | 35,84 mm (1,41 hüvelyk) |

| Tömeg | Érték |
|-----------------|----------------------|
| Csomópont súlya (csomagolás nélkül, tartozékok nélkül) | 40 lbs (18,1 kg) |
| Nettó súly (csomagolás nélkül, beleértve a tartozékokat is) | 51 lbs (23,1 kg)|
| Bruttó súly (szállítottként, beleértve az összes csomagolást) |  64 lbs (29,0 kg) |

### <a name="shipping-dimensions"></a>Szállítási méretek

| Csomag dimenzió | Milliméter | Hüvelyk |
|-------------------|-------------|--------|
| Magasság            | 311,2       | 12,25 " |
| Szélesség             | 642,8       | 25,31 " |
| Hossz            | 1 051,1     | 41,38 " |

## <a name="power-and-thermal-specifications"></a>Energiaellátási és termikus specifikációk

Ez a szakasz az Azure FXT Edge Filer számára biztosít teljesítmény-és mérési értékeket.

### <a name="nameplate-ratings"></a>Névtábla minősítése

| FXT 6000 sorozatú modellekhez tartozó névtábla-minősítések |
|----------------|
| 100 – 240V ~    |
| 10A-5A (X2)  |
| 50/60Hz         |

<!-- matches the Dell regulatory label exactly -->

### <a name="power-and-thermal-measurements"></a>Teljesítmény-és termikus mérések

Az Azure FXT Edge Filer-csomópontjai változó sebességű ventilátorokat használnak, így a teljesítmény a hőmérséklettől és a terheléstől függ. A ventilátorok maximális sebességét a nagy terhelésű és emelt szintű környezeti hőmérséklet bizonyos kombinációi érhetik el.

Ezek a diagramok energiafogyasztást és termikus kimeneti méréseket biztosítanak a gyakran használt feszültség-gyakorisági kombinációk esetében.

| FXT 6600 bekapcsolás szobahőmérsékleten <br />(22 ° C, 71,6 ° F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz |
|---------|---|---|---|---|---|
| Feszültség (V) | 100 | 120 | 208 | 230 | 240 |
| Gyakoriság (Hz) | 60 | 60 | 60 | 50 | 50 |
| Aktuális (A) | 5,02 | 4,16 |2,40 | 2,20 | 2,16 |
| Látszólagos teljesítmény (VA) | 502 | 499 | 499 | 506 | 518|
| Teljesítménytényező | 0,99 | 0,99 |0,98 | 0,98 | 0,98 |
| Valós teljesítmény (W) | 497 |494 | 489 | 496 | 508 |
| Termikus elszóródás (BTU/HR) |1696 | 1686 | 1669 | 1692 | 1733 |

| FXT 6600 teljesítmény a ventilátorok maximális sebességével | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz |
|---------|---|---|---|---|---|
| Feszültség (V) | 100 |120 | 208 | 230 | 240|
| Gyakoriság (Hz) | 60 | 60 | 60 | 50 | 50 |
| Aktuális (A) | 5,98 | 5,01 | 2,81 | 2.55 | 2,48 |
| Látszólagos teljesítmény (VA) | 598 | 601 | 584 | 587 | 595 |
| Teljesítménytényező | 0,99 | 0,99 | 0,98 | 0,98 | 0,98 |
| Valós teljesítmény (W) | 592 | 595 | 573 | 575 | 583 |
| Termikus elszóródás (BTU/HR) | 2020 |2031 | 1954 | 1961 | 1990 |

| FXT 6400 bekapcsolás szobahőmérsékleten <br />(22 ° C, 71,6 ° F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz |
|---------|---|---|---|---|---|
| Feszültség (V) | 100 | 120 | 208 | 230 | 240 |
| Gyakoriság (Hz) |60 | 60 | 60 | 50 | 50 |
| Aktuális (A) | 4.63 | 3,86 | 2.24 | 2,04 | 1,94 |
| Látszólagos teljesítmény (VA) | 463 | 463 | 466 | 469 | 466 |
| Teljesítménytényező | 0,99 | 0,99 | 0,98 | 0,98 | 0,98 |
| Valós teljesítmény (W) | 458 | 459 | 457 | 460 | 456 |
| Termikus elszóródás (BTU/HR) | 1564 | 1565 | 1558 | 1569 | 1557 |

| FXT 6400 teljesítmény a ventilátorok maximális sebességével | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz |
|---------|---|---|---|---|---|
| Feszültség (V) | 100 | 120 | 208 | 230 | 240 |
| Gyakoriság (Hz) | 60 | 60 | 60 | 50 | 50 |
| Aktuális (A) | 5,15 | 4,28 | 2,48 | 2,28 | 2,13 |
| Látszólagos teljesítmény (VA) | 515 | 514 | 516 | 524 | 511 |
| Teljesítménytényező | 0,99 | 0,99 | 0,98 | 0,98 | 0,98 |
| Valós teljesítmény (W) | 510 | 508 | 506 | 514 | 501 |
| Termikus elszóródás (BTU/HR) | 1740 | 1735 | 1725 | 1753 | 1709 |

## <a name="environmental-requirements"></a>Környezeti követelmények

Ez a szakasz a hardver környezeti környezetének specifikációit ismerteti.

### <a name="temperature-and-humidity"></a>Hőmérséklet és páratartalom

| Környezeti attribútum   | Működési tartomány                   | Nem működési tartomány         |
|---------------------------|-----------------------------------|-----------------------------|
| Környezeti hőmérséklet tartománya | 10 °C – 35 °C (50 – 86 °F)          | -40 °C-tól 65 °C-ig (-40-149 °F) |
| Környezeti relatív páratartalom | 10%-80% nem kondenzációs          | 5%-95% nem kondenzációs     |
| Maximális harmatpont         | 29 °C (84 °F)                       | 33 °C (91 °F)                 |
| Magasság                  | akár 3048 méter (10 000 láb), az alábbi hőmérséklet-minősítéstől függően | akár 12 000 méter (39 370 méter) |

> [!NOTE]
> **Magassági hőmérséklet de-Rating:** A maximális hőmérsékletet 1 °C/300 m (1 °F/547 Ft) csökkenti 950 m felett (3 117 Ft).

### <a name="airflow-shock-and-vibration"></a>Légáram, sokk és vibráció

| Attribútum         | Specifikáció |
|-------------------|---------------|
| Légáramlás                    | A rendszer légáram elölről hátrafelé. A rendszernek alacsony nyomású hátsó kipufogógáz-telepítéssel kell működnie. |
| Sokk, működési         | 6 G 11 ezredmásodpercig (6 tájolással tesztelt) |
| Sokk, nem működő     | 71 G 2 ezredmásodpercnél (tesztelt 6 tájolással) |
| Vibráció, működési     | 0,26 G<sub>RMS</sub> 5 hz – 350 Hz véletlenszerű         |
| Vibráció, nem működő | 1,88 G<sub>RMS</sub> 10 hz – 500 Hz 15 percre (mind a hat oldal tesztelt)  |

## <a name="safety-regulation-compliance"></a>Biztonsági szabályozás megfelelősége

Az Azure FXT Edge Filer megfelel a felsorolt előírásoknak.

| Kategória       | Szabályozási specifikáció |
|----------------|--------------------------|
| Általános biztonság | EN 60950-1:2006 + a1:2010 + a2:2013 + A11:2009 + A12:2011/IEC 60950-1:2005 ED2 + a1:2009 + a2:2013 <br>EN 62311:2008 |
| EMC            | FCC A, ICES-003  <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 + A1:2015/CISPR 24:2010 + A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (D osztály)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013 |
| Energia         | A bizottsági rendelet (EU) nem. 617/2013  |
| RoHS           |    EN 50581:2012   |

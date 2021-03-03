---
title: SKU-SAP HANA az Azure-ban (nagyméretű példányok) | Microsoft Docs
description: SKU-SAP HANA az Azure-ban (nagyméretű példányok).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
keywords: HLI, HANA, SKU, S896, S224, S448, S672, Optane, SAP
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 25a11bf96ba680608e5bb22835becf80fadee4f3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101668932"
---
# <a name="available-skus-for-hana-large-instances"></a>Rendelkezésre álló SKU-a HANA nagyméretű példányaihoz

Az Azure-beli (nagyméretű példányok) szolgáltatáson csak a 3 bélyegzőn alapuló SAP HANA a következő Azure-régiók számos konfigurációjában érhető el:

- Kelet-Ausztrália
- Délkelet-Ausztrália
- Kelet-Japán
- Nyugat-Japán

Az Azure (nagyméretű példányok) szolgáltatás 4. változatán alapuló SAP HANA a következő Azure-régiók számos konfigurációjában érhető el:

- USA 2. nyugati régiója
- USA keleti régiója

BareMetal-infrastruktúra (SAP HANA számítási feladatokhoz) szolgáltatás, a 4,2-es kiadási bélyegek alapján. Több konfigurációban is elérhető az Azure-régiókban:
- Nyugat-Európa
- Észak-Európa
- USA 2. keleti régiója
- USA déli középső régiója




Az elérhető Azure-beli nagyméretű példányok listája, például a következőhöz hasonló listák.

> [!IMPORTANT]
> Ügyeljen arra, hogy az első oszlop tartalmazza a HANA-tanúsítványok állapotát a listában szereplő nagyméretű példányok esetében. Az oszlopnak a (z) betűvel kezdődő Azure SKU- **k** [SAP HANA hardveres könyvtárával](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) kell korrelálnia



| SAP HANA tanúsított | Modellezés | Teljes memória | Memória DRAM | Memória Optane | Tárolás | Rendelkezésre állás |
| --- | --- | --- | --- | --- | --- | --- |
| IGEN <br />[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2185), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2265) | SAP HANA az Azure S96<br /> – 2 x Intel® Xeon® processzor E7 – 8890 v4 <br /> 48 CPU-magok és 96 CPU-szálak |  768 GB | 768 GB | --- | 3,0 TB | Elérhető |
| IGEN <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2186), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2269) | SAP HANA az Azure S224<br /> – 4 x Intel® Xeon® Platinum 8276 processzor <br /> 112 CPU-magok és 224 CPU-szálak |  3,0 TB | 3,0 TB | --- | 6,3 TB | Elérhető |
| IGEN <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2297) | SAP HANA az Azure S224m<br /> – 4 x Intel® Xeon® Platinum 8276 processzor <br /> 112 CPU-magok és 224 CPU-szálak |  6,0 TB | 6,0 TB | --- | 10,5 TB | Elérhető |
| IGEN <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2381) | SAP HANA az Azure S224om<br /> – 4 x Intel® Xeon® Platinum 8276 processzor <br /> 112 CPU-magok és 224 CPU-szálak | 6,0 TB |  3,0 TB |  3,0 TB | 10,5 TB | Elérhető |
| NO | SAP HANA az Azure S224oo<br /> – 4 x Intel® Xeon® Platinum 8276 processzor <br /> 112 CPU-magok és 224 CPU-szálak | 4,5 TB |  1,5 TB |  3,0 TB | 8,4 TB | Elérhető |
| NO | SAP HANA az Azure S224ooo<br /> – 4 x Intel® Xeon® Platinum 8276 processzor <br /> 112 CPU-magok és 224 CPU-szálak | 7,5 TB |  1,5 TB |  6,0 TB | 12,7 TB | Elérhető |
| NO | SAP HANA az Azure S224oom<br /> – 4 x Intel® Xeon® Platinum 8276 processzor <br /> 112 CPU-magok és 224 CPU-szálak | 9,0 TB |  3,0 TB |  6,0 TB | 14,8 TB | Elérhető |
| IGEN <br />[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1983), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2268) | SAP HANA az Azure S384<br /> – 8 x Intel® Xeon® processzor E7 – 8890 v4<br /> 192 CPU-magok és 384 CPU-szálak |  4,0 TB | 4,0 TB | --- | 16 TB | Elérhető |
| IGEN <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2080) | SAP HANA az Azure S384m<br /> – 8 x Intel® Xeon® processzor E7 – 8890 v4<br /> 192 CPU-magok és 384 CPU-szálak |  6,0 TB | 6,0 TB | --- | 18 TB |  Elérhető  |
| IGEN <br />[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1984), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2267) | SAP HANA az Azure S384xm<br /> – 8 x Intel® Xeon® processzor E7 – 8890 v4<br /> 192 CPU-magok és 384 CPU-szálak |  8,0 TB | 8,0 TB | --- | 22 TB | Elérhető |
| IGEN <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2411), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2378) | SAP HANA az Azure S448<br /> – 8 x Intel® Xeon® Platinum 8276 processzor <br /> 224 CPU-magok és 448 CPU-szálak | 6,0 TB |  6,0 TB |  --- | 10,5 TB | Elérhető (csak Rev 4 esetén) |
| IGEN <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2410), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2377) | SAP HANA az Azure S448m<br /> – 8 x Intel® Xeon® Platinum 8276 processzor <br /> 224 CPU-magok és 448 CPU-szálak | 12,0 TB |  12,0 TB |  --- | 18,9 TB | Elérhető (csak Rev 4 esetén) |
| NO | SAP HANA az Azure S448oo<br /> – 8 x Intel® Xeon® Platinum 8276 processzor <br /> 224 CPU-magok és 448 CPU-szálak | 9,0 TB |  3,0 TB |  6,0 TB | 14,8 TB  | Elérhető (csak Rev 4 esetén) |
| NO | SAP HANA az Azure S448om<br /> – 8 x Intel® Xeon® Platinum 8276 processzor <br /> 224 CPU-magok és 448 CPU-szálak | 12,0 TB |  6,0 TB |  6,0 TB | 18,9 TB  | Elérhető (csak Rev 4 esetén) |
| NO | SAP HANA az Azure S448ooo<br /> – 8 x Intel® Xeon® Platinum 8276 processzor <br /> 224 CPU-magok és 448 CPU-szálak | 15,0 TB |  3,0 TB |  12,0 TB | 23,2 TB  | Elérhető (csak Rev 4 esetén) |
| NO | SAP HANA az Azure S448oom<br /> – 8 x Intel® Xeon® Platinum 8276 processzor <br /> 224 CPU-magok és 448 CPU-szálak | 18,0 TB |  6,0 TB |  12,0 TB | 27,4 TB  | Elérhető (csak Rev 4 esetén) |
| IGEN <br /> [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2049) | SAP HANA az Azure S576m<br /> – 12 x Intel® Xeon® processzor E7 – 8890 v4<br /> 288 CPU-magok és 576 CPU-szálak |  12,0 TB | 12,0 TB | --- | 28 TB | Elérhető (csak Rev 4 esetén) |
| NO | SAP HANA az Azure S576xm<br /> – 12 x Intel® Xeon® processzor E7 – 8890 v4<br /> 288 CPU-magok és 576 CPU-szálak |  18,0 TB | 18.0 | --- |  41 TB | Elérhető |
| IGEN <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2409), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2380) | SAP HANA az Azure S672<br /> – 12 x Intel® Xeon® Platinum 8276 processzor <br /> 336 CPU-magok és 672 CPU-szálak | 9,0 TB |  9,0 TB |  --- | 14,7 TB | Elérhető (csak Rev 4 esetén) |
| IGEN <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2408), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2379) | SAP HANA az Azure S672m<br /> – 12 x Intel® Xeon® Platinum 8276 processzor <br /> 336 CPU-magok és 672 CPU-szálak | 18,0 TB |  18,0 TB |  --- | 27,4 TB | Elérhető (csak Rev 4 esetén) |
| NO | SAP HANA az Azure S672oo<br /> – 12 x Intel® Xeon® Platinum 8276 processzor <br /> 336 CPU-magok és 672 CPU-szálak | 13,5 TB |  4,5 TB |  9,0 TB | 21,1 TB  | Elérhető (csak Rev 4 esetén) |
| NO | SAP HANA az Azure S672om<br /> – 12 x Intel® Xeon® Platinum 8276 processzor <br /> 336 CPU-magok és 672 CPU-szálak | 18,0 TB |  9,0 TB |  9,0 TB | 27,4 TB  | Elérhető (csak Rev 4 esetén) |
| NO | SAP HANA az Azure S672ooo<br /> – 12 x Intel® Xeon® Platinum 8276 processzor <br /> 336 CPU-magok és 672 CPU-szálak | 22,5 TB |  4,5 TB |  18,0 TB | 33,7 TB  | Elérhető (csak Rev 4 esetén) |
| NO | SAP HANA az Azure S672oom<br /> – 12 x Intel® Xeon® Platinum 8276 processzor <br /> 336 CPU-magok és 672 CPU-szálak | 27,0 TB |  9,0 TB |  18,0 TB | 40,0 TB  | Elérhető (csak Rev 4 esetén) |
| IGEN <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1985) | SAP HANA az Azure S768m<br /> – 16 x Intel® Xeon® processzor E7 – 8890 v4<br /> 384 CPU-magok és 768 CPU-szálak |  16,0 TB | 16,0 TB | -- | 36 TB | Elérhető |
| NO | SAP HANA az Azure S768xm<br /> – 16 x Intel® Xeon® processzor E7 – 8890 v4<br /> 384 CPU-magok és 768 CPU-szálak |  24,0 TB | 24,0 TB | --- | 56 TB | Elérhető |
|  IGEN <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2407), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2376)  | SAP HANA az Azure S896<br /> – 16 x Intel® Xeon® Platinum 8276 processzor <br /> 448 CPU-magok és 896 CPU-szálak | 12,0 TB |  12,0 TB |  --- | 18,9 TB | Elérhető (csak Rev 4 esetén) |
| IGEN <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2406), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2328) | SAP HANA az Azure S896m<br /> – 16 x Intel® Xeon® Platinum 8276 processzor <br /> 448 CPU-magok és 896 CPU-szálak | 24,0 TB | 24,0 TB | -- | 35,8 TB | Elérhető |
| NO | SAP HANA az Azure S896oo<br /> – 16 x Intel® Xeon® Platinum 8276 processzor <br /> 448 CPU-magok és 896 CPU-szálak | 18,0 TB |  6,0 TB |  12,0 TB | 27,4 TB  | Elérhető (csak Rev 4 esetén) |
| NO | SAP HANA az Azure S896om<br /> – 16 x Intel® Xeon® Platinum 8276 processzor <br /> 448 CPU-magok és 896 CPU-szálak | 24,0 TB |  12,0 TB |  12,0 TB | 35,8 TB  | Elérhető (csak Rev 4 esetén) |
| NO | SAP HANA az Azure S896ooo<br /> – 16 x Intel® Xeon® Platinum 8276 processzor <br /> 448 CPU-magok és 896 CPU-szálak | 30,0 TB |  6,0 TB |  24,0 TB | 44,3 TB  | Elérhető (csak Rev 4 esetén) |
| NO | SAP HANA az Azure S896oom<br /> – 16 x Intel® Xeon® Platinum 8276 processzor <br /> 448 CPU-magok és 896 CPU-szálak | 36,0 TB |  12,0 TB |  24,0 TB | 52,7 TB  | Elérhető (csak Rev 4 esetén) |
| IGEN <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1986) | SAP HANA az Azure S960m<br /> – 20 x Intel® Xeon® processzor E7 – 8890 v4<br /> 480 CPU-magok és 960 CPU-szálak |  20,0 TB | 20,0 TB | -- | 46 TB | Elérhető (csak Rev 4 esetén) |


- CPU-magok = a kiszolgálói egység processzorai összegének nem Hyper-threaded CPU-magok összessége.
- CPU-szálak = a kiszolgálói egység processzorai összegének a Hyper-threaded CPU-magok által megadott számítási szálak összessége. A legtöbb egység alapértelmezés szerint a Hyper-Threading technológia használatára van konfigurálva.
- A szállítói javaslatok alapján a S768m, a S768xm és a S960m nem úgy vannak konfigurálva, hogy a SAP HANA futtatásához Hyper-Threading használják.


> [!IMPORTANT]
> A következő SKU-ket, bár még mindig támogatottak, nem vásárolhatók meg többé: S72, S72m, S144, S144m, S192 és S192m 

A kiválasztott konfigurációk a munkaterhelés, a CPU-erőforrások és a kívánt memória függenek. A OLTP számítási feladatnak az OLAP számítási feladathoz optimalizált SKU-k használatára van lehetősége. 

A hardver két különböző osztálya osztható fel a SKU-ba:

- A S72, a S72m, a S96, a S144, a S144m, a S192, a S192m, a S192xm, a S224 és a S224m, a S224oo, a S224om, a S224ooo, a S224oom az SKU típusú "I osztály".
- Az összes többi SKU-t a SKU "Type II osztályának" nevezzük.
- Ha olyan SKU-t szeretne kapni, amely még nem szerepel az SAP Hardware Directory címtárban, további információért forduljon a Microsoft-fiók csapatához. 


Egy teljes HANA nagyméretű példány bélyegzője nem kizárólagos módon van lefoglalva egyetlen ügyfél&#39;s használatára. Ez a tény az Azure-ban üzembe helyezett hálózati hálón keresztül csatlakoztatott számítási és tárolási erőforrások állványára vonatkozik. A HANA nagyméretű példány-infrastruktúrája, például az Azure, &quot; a következő három szinten helyez üzembe különböző, egymástól elkülönített bérlőket &quot; :

- **Hálózat**: a HANA nagyméretű példány bélyegzőn belüli virtuális hálózatok elkülönítése.
- **Tárterület**: elkülönítés a tároló virtuális gépei között, amelyekhez tárolási kötetek vannak rendelve, és elkülönítik a tárolási köteteket a bérlők között.
- **Számítás**: kiszolgálói egységek dedikált hozzárendelése egyetlen bérlőhöz. A kiszolgálói egységek nem rendelkeznek kemény vagy lágy particionálással. Egyetlen kiszolgáló vagy gazda egység megosztása a bérlők között. 

A HANA nagyméretű példány-egységek különböző bérlők közötti központi telepítései nem láthatók egymás számára. A különböző bérlők által üzembe helyezett HANA nagyméretű példányok nem tudnak közvetlenül kommunikálni egymással a HANA nagyméretű példány-bélyegző szintjén. Az egyik bérlőn belüli HANA Large instances egységek csak a HANA nagyméretű példány-bélyegző szintjén tudnak kommunikálni egymással.

A nagyméretű példányok bélyegzőn üzembe helyezett bérlők egy Azure-előfizetéshez vannak rendelve számlázási célokra. A hálózatok esetében az adott Azure-regisztráción belül más Azure-előfizetések virtuális hálózatáról is elérhető. Ha ugyanabban az Azure-régióban egy másik Azure-előfizetéssel telepíti a üzembe helyezést, dönthet úgy is, hogy egy különálló HANA nagyméretű példány bérlőt kér.

Jelentős különbségek vannak a HANA nagyméretű példányon futó SAP HANA és az Azure-ban üzembe helyezett virtuális gépeken futó SAP HANA között:

- Az Azure-ban nincs SAP HANA virtualizációs réteg (nagyméretű példányok). Az alapul szolgáló operációs rendszer nélküli hardverek teljesítményét kapja meg.
- Az Azure-tól eltérően az Azure-beli (nagyméretű példányok) kiszolgáló SAP HANA egy adott ügyfélhez van hozzárendelve. Nincs lehetőség arra, hogy a kiszolgáló egysége vagy gazdagépe kemény vagy puha particionálású legyen. Ennek eredményeképpen a HANA nagyméretű példányok egysége a bérlőhöz és az Önhöz hozzárendelt teljes egészében van kiosztva. A kiszolgáló újraindítása vagy leállítása nem vezet automatikusan az operációs rendszerhez, és SAP HANA egy másik kiszolgálóra. (Az I osztályba tartozó SKU típus esetében az egyetlen kivétel az, ha egy kiszolgáló problémákba ütközik, és egy másik kiszolgálón kell végrehajtani az újratelepítést.)
- Az Azure-tól eltérően, ahol a gazdagép processzor-típusai a legjobb ár/teljesítmény arányhoz vannak kiválasztva, az Azure-ban (nagyméretű példányok) SAP HANA számára kiválasztott processzor-típusok az Intel E7v3 és a E7v4 processzor legmagasabb teljesítménye.

**Következő lépések**
- [HLI méretezése](hana-sizing.md)

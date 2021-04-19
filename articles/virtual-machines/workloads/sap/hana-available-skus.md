---
title: Azure-beli SAP HANA (nagyméretű példányok) szolgáltatáshoz | Microsoft Docs
description: Azure-beli SAP HANA (nagy méretű példányok) szolgáltatáshoz.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
keywords: HLI, HANA, SKUs, S896, S224, S448, S672, Optane, SAP
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 4/16/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017, references_regions
ms.openlocfilehash: 3ecbbe4d477f3e6c3c6606528c51b934b6cf534a
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718736"
---
# <a name="available-skus-for-hana-large-instances"></a>Nagy méretű HANA-példányokhoz elérhető SKUS-k

A 4.2-es vagy SAP HANA-alapú BareMetal Infrastructure szolgáltatás a következő régiókban érhető el:
- Nyugat-Európa
- Észak-Európa
- Nyugat-Németország – Közép-Németország zónatámogatással
- USA keleti régiója zónák támogatásával
- USA 2. keleti régiója
- USA déli középső régiója
- Az USA 2. nyugati régiója zónák támogatásával

A 3. kiadáson* alapuló BareMetal Infrastructure (tanúsítvánnyal rendelkezik SAP HANA számítási feladatokhoz) szolgáltatás korlátozottan elérhető a következő régiókban:
- USA nyugati régiója
- USA keleti régiója 
- Kelet-Ausztrália 
- Délkelet-Ausztrália
- Kelet-Japán


Az alábbi lista az elérhető nagyméretű Azure-példányokat tartalmazza.

> [!IMPORTANT]
> Vegye figyelembe az első oszlopot, amely a listában lévő összes nagyméretű példánytípus HANA-minősítésének állapotát jelöli. Az oszlopnak az S [betűvel SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Azure-SKUs hardverkönyvtárával kell korrelálnia. 



| SAP HANA minősítés | Modellezés | Teljes memória | Memória-DRAM | Memória- és memória-beállítások | Tárolás | Rendelkezésre állás |
| --- | --- | --- | --- | --- | --- | --- |
| IGEN <br />[OLAP,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2185) [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2265) | SAP HANA Azure S96-on<br /> – 2 db Intel® Xeon® E7-8890 v4 processzor <br /> 48 processzormag és 96 PROCESSZORszál |  768 GB | 768 GB | --- | 3,0 TB | Elérhető |
| IGEN <br /> [OLAP,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2186) [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2269) | SAP HANA Azure S224-en<br /> – 4 db Intel® Xeon® Fog 8276 processzor <br /> 112 processzormag és 224 processzorszál |  3,0 TB | 3,0 TB | --- | 6,3 TB | Elérhető |
| IGEN <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2297) | SAP HANA Azure S224m-en<br /> – 4 db Intel® Xeon® Fog 8276 processzor <br /> 112 processzormag és 224 processzorszál |  6,0 TB | 6,0 TB | --- | 10,5 TB | Elérhető |
| IGEN <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2381) | SAP HANA Azure S224om-on<br /> – 4 db Intel® Xeon® Fog 8276 processzor <br /> 112 processzormag és 224 processzorszál | 6,0 TB |  3,0 TB |  3,0 TB | 10,5 TB | Elérhető |
| NO | SAP HANA Azure S224oo-on<br /> – 4 x Intel® Xeon® Fog 8276 processzor <br /> 112 processzormag és 224 processzorszál | 4,5 TB |  1,5 TB |  3,0 TB | 8,4 TB | Elérhető |
| NO | SAP HANA Azure S224ooo-on<br /> – 4 db Intel® Xeon® Fog 8276 processzor <br /> 112 processzormag és 224 processzorszál | 7,5 TB |  1,5 TB |  6,0 TB | 12,7 TB | Elérhető |
| NO | SAP HANA Azure S224oom-on<br /> – 4 db Intel® Xeon® Fog 8276 processzor <br /> 112 processzormag és 224 processzorszál | 9,0 TB |  3,0 TB |  6,0 TB | 14,8 TB | Elérhető |
| IGEN <br />[OLAP,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1983) [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2268) | SAP HANA Azure S384-en<br /> – 8 x Intel® Xeon® E7-8890 v4 processzor<br /> 192 processzormag és 384 processzorszál |  4,0 TB | 4,0 TB | --- | 16 TB | Elérhető |
| IGEN <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2080) | SAP HANA Azure S384m-en<br /> – 8 x Intel® Xeon® E7-8890 v4 processzor<br /> 192 processzormag és 384 processzorszál |  6,0 TB | 6,0 TB | --- | 18 TB |  Elérhető  |
| IGEN <br />[OLAP,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1984) [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2267) | SAP HANA Azure S384xm-en<br /> – 8 x Intel® Xeon® E7-8890 v4 processzor<br /> 192 processzormag és 384 processzorszál |  8,0 TB | 8,0 TB | --- | 22 TB | Elérhető |
| IGEN <br /> [OLAP,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2411) [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2378) | SAP HANA Azure S448-on<br /> – 8 x Intel® Xeon® Fog 8276 processzor <br /> 224 processzormag és 448 processzorszál | 6,0 TB |  6,0 TB |  --- | 10,5 TB | Elérhető (csak a Rev 4.2 esetén) |
| IGEN <br /> [OLAP,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2410) [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2377) | SAP HANA Azure S448m-en<br /> – 8 x Intel® Xeon® Fog 8276 processzor <br /> 224 processzormag és 448 processzorszál | 12,0 TB |  12,0 TB |  --- | 18,9 TB | Elérhető (csak a 4.2-es rev. |
| NO | SAP HANA Azure S448oo-on<br /> – 8 x Intel® Xeon® Fog 8276 processzor <br /> 224 processzormag és 448 processzorszál | 9,0 TB |  3,0 TB |  6,0 TB | 14,8 TB  | Elérhető (csak a Rev 4.2 esetén) |
| NO | SAP HANA Azure S448om-on<br /> – 8 db Intel® Xeon® Fog 8276 processzor <br /> 224 processzormag és 448 processzorszál | 12,0 TB |  6,0 TB |  6,0 TB | 18,9 TB  | Elérhető (csak a Rev 4.2 esetén) |
| NO | SAP HANA Azure S448ooo-on<br /> – 8 db Intel® Xeon® Fog 8276 processzor <br /> 224 processzormag és 448 processzorszál | 15,0 TB |  3,0 TB |  12,0 TB | 23,2 TB  | Elérhető (csak a 4.2-es rev. |
| NO | SAP HANA Azure S448oom-on<br /> – 8 x Intel® Xeon® Fog 8276 processzor <br /> 224 processzormag és 448 processzorszál | 18,0 TB |  6,0 TB |  12,0 TB | 27,4 TB  | Elérhető (csak a 4.2-es rev. |
| IGEN <br /> [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2049) | SAP HANA Azure S576m-on<br /> – 12 x Intel® Xeon® E7-8890 v4 processzor<br /> 288 processzormag és 576 PROCESSZORszál |  12,0 TB | 12,0 TB | --- | 28 TB | Elérhető (csak a Rev 4.2 esetén) |
| NO | SAP HANA Azure S576xm-ben<br /> – 12 x Intel® Xeon® E7-8890 v4 processzor<br /> 288 processzormag és 576 PROCESSZORszál |  18,0 TB | 18.0 | --- |  41 TB | Elérhető |
| IGEN <br /> [OLAP,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2409) [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2380) | SAP HANA Azure S672-ben<br /> – 12 db Intel® Xeon® Fog 8276 processzor <br /> 336 processzormag és 672 processzorszál | 9,0 TB |  9,0 TB |  --- | 14,7 TB | Elérhető (csak a Rev 4.2 esetén) |
| IGEN <br /> [OLAP,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2408) [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2379) | SAP HANA Azure S672m-ben<br /> – 12 db Intel® Xeon® Fog 8276 processzor <br /> 336 processzormag és 672 processzorszál | 18,0 TB |  18,0 TB |  --- | 27,4 TB | Elérhető (csak a Rev 4.2 esetén) |
| NO | SAP HANA Azure S672oo-on<br /> – 12 db Intel® Xeon® Fog 8276 processzor <br /> 336 processzormag és 672 PROCESSZORszál | 13,5 TB |  4,5 TB |  9,0 TB | 21,1 TB  | Elérhető (csak a 4.2-es rev. |
| NO | SAP HANA Azure S672om-on<br /> – 12 x Intel® Xeon® Fog 8276 processzor <br /> 336 processzormag és 672 processzorszál | 18,0 TB |  9,0 TB |  9,0 TB | 27,4 TB  | Elérhető (csak a 4.2-es rev. |
| NO | SAP HANA Azure S672ooo-on<br /> – 12 x Intel® Xeon® Fog 8276 processzor <br /> 336 processzormag és 672 PROCESSZORszál | 22,5 TB |  4,5 TB |  18,0 TB | 33,7 TB  | Elérhető (csak a Rev 4.2 esetén) |
| NO | SAP HANA Azure S672oom-on<br /> – 12 db Intel® Xeon® Fog 8276 processzor <br /> 336 processzormag és 672 processzorszál | 27,0 TB |  9,0 TB |  18,0 TB | 40,0 TB  | Elérhető (csak a Rev 4.2 esetén) |
| IGEN <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1985) | SAP HANA Azure S768m-ben<br /> – 16 x Intel® Xeon® E7-8890 v4 processzor<br /> 384 processzormag és 768 processzorszál |  16,0 TB | 16,0 TB | -- | 36 TB | Elérhető |
| NO | SAP HANA Azure S768xm-ben<br /> – 16 x Intel® Xeon® E7-8890 v4 processzor<br /> 384 processzormag és 768 processzorszál |  24,0 TB | 24,0 TB | --- | 56 TB | Elérhető |
|  IGEN <br /> [OLAP,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2407) [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2376)  | SAP HANA Azure S896-on<br /> – 16 x Intel® Xeon® Fog 8276 processzor <br /> 448 processzormag és 896 processzorszál | 12,0 TB |  12,0 TB |  --- | 18,9 TB | Elérhető (csak a 4.2-es rev. |
| IGEN <br /> [OLAP,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2406) [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2328) | SAP HANA on Azure S896m<br /> – 16 x Intel® Xeon® Fog 8276 processzor <br /> 448 processzormag és 896 processzorszál | 24,0 TB | 24,0 TB | -- | 35,8 TB | Elérhető |
| NO | SAP HANA on Azure S896oo<br /> – 16 x Intel® Xeon® Fog 8276 processzor <br /> 448 processzormag és 896 PROCESSZORszál | 18,0 TB |  6,0 TB |  12,0 TB | 27,4 TB  | Elérhető (csak a Rev 4.2 esetén) |
| NO | SAP HANA on Azure S896om<br /> – 16 x Intel® Xeon® Fog 8276 processzor <br /> 448 processzormag és 896 processzorszál | 24,0 TB |  12,0 TB |  12,0 TB | 35,8 TB  | Elérhető (csak a Rev 4.2 esetén) |
| NO | SAP HANA Azure S896ooo-on<br /> – 16 x Intel® Xeon® Fog 8276 processzor <br /> 448 processzormag és 896 processzorszál | 30,0 TB |  6,0 TB |  24,0 TB | 44,3 TB  | Elérhető (csak a Rev 4.2 esetén) |
| NO | SAP HANA on Azure S896oom<br /> – 16 x Intel® Xeon® Fog 8276 processzor <br /> 448 processzormag és 896 processzorszál | 36,0 TB |  12,0 TB |  24,0 TB | 52,7 TB  | Elérhető (csak a Rev 4.2 esetén) |
| IGEN <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1986) | SAP HANA Azure S960m-ben<br /> – 20 x Intel® Xeon® E7-8890 v4 processzor<br /> 480 processzormag és 960 processzorszál |  20,0 TB | 20,0 TB | -- | 46 TB | Elérhető (csak a Rev 4.2 esetén) |


- CPU-magok = a kiszolgálóegység processzorai összegének nem többszálú processzormagok összege.
- CPU-szálak = a többszálú processzormagok által biztosított számítási szálak összege a kiszolgálóegység processzorai összegének. A legtöbb egység alapértelmezés szerint úgy van konfigurálva, hogy az Hyper-Threading technológiát használja.
- Az S768m, az S768xm és az S960m szállítói javaslatok alapján nincs konfigurálva a Hyper-Threading használatára a SAP HANA.


> [!IMPORTANT]
> A következő termékkódok , bár továbbra sem támogatottak, már nem vásárolhatók meg: S72, S72m, S144, S144m, S192 és S192m 

A kiválasztott konfigurációk a számítási feladattól, a CPU-erőforrásoktól és a kívánt memóriától függenek. Az OLTP számítási feladat használhatja az OLAP számítási feladathoz optimalizált SKUs-t. 

A hardver két különböző osztálya osztja fel a SKU-kat a következőre:

- Az S72, S72m, S96, S144, S144m, S192m, S192m, S192xm, S224 és S224m, S224oo, S224om, S224ooo, S224oom S224oom SKUs "Type I class" (Típus I osztálya) néven hivatkozunk.
- Az összes többi SKU-t a SKU-k "II. típusú osztályának" nevezzük.
- Ha érdeklik az SAP-hardverkönyvtárban még nem szereplő SKUS-k, Microsoft-fiók további információért forduljon az Microsoft-fiók csapatához. 


A teljes HANA nagyméretű példányok bélyege nem kizárólag egyetlen ügyfél számára van lefoglalva&#39;használatra. Ez a tény az Azure-ban üzembe helyezett hálózati hálón keresztül csatlakoztatott számítási és tárolási erőforrások állványaira is vonatkozik. A HANA nagy méretű példány infrastruktúrája, például az Azure különböző ügyfélbérlőket helyez üzembe, amelyek a következő három szinten vannak &quot; &quot; elkülönítve egymástól:

- **Hálózat:** Elkülönítés virtuális hálózatokon keresztül a HANA nagyméretű példánybélyegén belül.
- **Tárolás:** Elkülönítés olyan tároló virtuális gépeken keresztül, amelyekhez tárolókötet van hozzárendelve, és elkülöníti a tárolóköteteket a bérlők között.
- **Számítás:** Kiszolgálóegységek dedikált hozzárendelése egyetlen bérlőhöz. A kiszolgálóegységek nem particionálhatóak vagy nem particionálhatóak. Egyetlen kiszolgáló vagy gazdagépegység bérlők közötti megosztása nélkül. 

A hana nagyméretű példányegységei különböző bérlők közötti üzembe helyezései nem láthatók egymás számára. A különböző bérlőkben üzembe helyezett HANA nagyméretű példányegységek nem tudnak közvetlenül kommunikálni egymással a HANA nagyméretű példányok bélyegszintén. Csak az egyik bérlőn belüli HANA nagyméretű példányegységei kommunikálhatnak egymással a HANA nagyméretű példányok bélyegszintén.

A nagyméretű példány bélyegében üzembe helyezett bérlő egy Azure-előfizetéshez van rendelve számlázási célból. Hálózat esetén elérhető más Azure-előfizetések virtuális hálózatairól ugyanazon az Azure-regisztráción belül. Ha egy másik Azure-előfizetéssel helyez üzembe ugyanabban az Azure-régióban, választhatja azt is, hogy külön HANA nagyméretű példány bérlőt kér.

A nagy méretű HANA-SAP HANA és az Azure-ban üzembe SAP HANA virtuális gépeken való futtatás között jelentős különbségek vannak:

- Az Azure-ban (nagyméretű példányok SAP HANA nincs virtualizálási réteg. A mögöttes operációs rendszer nélküli hardver teljesítményét is kihozja.
- Az Azure-ral SAP HANA Azure-beli (nagy méretű példányok) kiszolgálón található szolgáltatások egy adott ügyfél számára vannak kisziva. Nem valószínű, hogy egy kiszolgálóegység vagy gazdagép kemény vagy soft partícióval van particionálva. Ennek eredményeképpen a HANA nagyméretű példányok egysége lesz a bérlőhöz rendelve, és ezzel együtt az Ön számára. A kiszolgáló újraindítása vagy leállítása nem vezet automatikusan az operációs rendszerhez, és SAP HANA másik kiszolgálóra való központi telepítéséhez. (Az I. osztályú SKU-k esetén az egyetlen kivétel az, ha egy kiszolgáló problémákba ütközik, és egy másik kiszolgálón kell újratelepítést végezni.)
- Az Azure-ral ellentétben, ahol a legjobb ár/teljesítmény arányú gazdaprocesszorok vannak kiválasztva, az Azure SAP HANA-hoz (nagy méretű példányokhoz) kiválasztott processzortípusok az Intel E7v3 és E7v4 processzorvonal legnagyobb teljesítménye.

## <a name="next-steps"></a>Következő lépések
- Lásd: [HLI Sizing](hana-sizing.md).

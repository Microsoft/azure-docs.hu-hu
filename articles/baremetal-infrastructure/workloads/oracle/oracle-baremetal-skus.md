---
title: BareMetal SKUs Oracle számítási feladatokhoz
description: Ismerje meg az Oracle BareMetal-infrastruktúra számítási feladataihoz szükséges SKUS-okat.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: 42ff26b9ea9bcc022f1ddbf3dddcb041b2cea3a2
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588853"
---
# <a name="baremetal-skus-for-oracle-workloads"></a>BareMetal SKUs Oracle számítási feladatokhoz

Ebben a cikkben az Oracle számítási feladatokhoz készült speciális BareMetal infrastruktúra-SKUS-okat fogjuk átfedni.

Az Oracle termékkódú BareMetal-infrastruktúra két szoftvercsatornától négy szoftvercsatornán át esik át. Különböző processzormagok és memóriaméretek közül is választhat, hogy megfeleljen a számítási feladatok követelményeinek. Az alábbi táblázat összefoglalja az elérhető SKUs-okat.
 
| **Oracle-tanúsítvánnyal rendelkező**  **hardver** | **Modellezés** | **Teljes memória** | **Storage** | **Rendelkezésre állás** |
| --- | --- | --- | --- | --- |
| IGEN | SAP HANA Azure S32m– 2 x Intel® Xeon® I6234 processzormag 16 processzormag és 32 processzorszál | 1,5 TB | --- | Elérhető |
| IGEN | SAP HANA Azure S64m– 4 x Intel® Xeon® I6234 Processzor 32 processzormag és 64 processzorszál | 3,0 TB | --- | Elérhető |
| IGEN | SAP HANA Azure S96-on – 2 db Intel® Xeon® E7-8890 v4 processzor 48 processzormag és 96 processzorszál | 768 GB | 3,0 TB | Elérhető |
| IGEN | SAP HANA Azure S224–4 x Intel® Xeon® Fog 8276 processzor, 112 processzormag és 224 processzorszál | 3,0 TB | 6,3 TB | Elérhető |
| IGEN | SAP HANA Azure S224m– 4 x Intel® Xeon® Fog 8276 processzor, 112 processzormag és 224 processzorszál | 6,0 TB | 10,5 TB | Elérhető |

- CPU-magok = a kiszolgálóegység nem többszálú processzormagjának (a fizikai processzorok teljes száma) összege. 
- CPU-szálak = a kiszolgálóegység többszálú processzormagja (a logikai processzorok teljes száma) által biztosított számítási szálak összege. A legtöbb egység alapértelmezés szerint úgy van konfigurálva, hogy az Hyper-Threading technológiát használja.
- A kiszolgálókat az ügyfelek számára árolják.
- Az ügyfél gyökér szintű hozzáféréssel rendelkezik (nincs hipervizor).
- A kiszolgálók nem közvetlenül az Azure-beli virtuális gépeken vannak.

## <a name="next-steps"></a>Következő lépések

Ismerje meg az BareMetal Infrastructure for Oracle által kínált tárterületet.

> [!div class="nextstepaction"]
> [Tárolás BareMetalon Oracle számítási feladatokhoz](oracle-baremetal-storage.md)

---
title: BareMetal SKUs Oracle számítási feladatokhoz
description: Ismerje meg az Oracle BareMetal-infrastruktúra számítási feladataihoz szükséges SKUS-okat.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 2482f8ed682a982ee3c8c4907e21b4e6c6ebbb4f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559093"
---
# <a name="baremetal-skus-for-oracle-workloads"></a>BareMetal SKUs Oracle számítási feladatokhoz

Ebben a cikkben az Oracle számítási feladatok speciális BareMetal Infrastructure SKUs-ját fogjuk átfedni.

Az Oracle termékkódok BareMetal-infrastruktúrája két szoftvercsatornától négy szoftvercsatornán át esik át. Különböző processzormagok és memóriaméretek közül is választhat, hogy megfeleljen a számítási feladatok követelményeinek. Az alábbi táblázat az elérhető SKUS-okat foglalja össze.
 
| **Oracle Certified**  **hardver** | **Modellezés** | **Teljes memória** | **Storage** | **Rendelkezésre állás** |
| --- | --- | --- | --- | --- |
| IGEN | SAP HANA Azure S32m– 2 x Intel® Xeon® processzor I623416 processzormag és 32 processzorszál | 1,5 TB | --- | Elérhető |
| IGEN | SAP HANA Azure S64m– 4 x Intel® Xeon® processzor I623432 processzormagok és 64 processzorszál | 3,0 TB | --- | Elérhető |
| IGEN | SAP HANA Azure S96-on – 2 x Intel® Xeon® E7-8890 v448 processzormag és 96 processzorszál | 768 GB | 3,0 TB | Elérhető |
| IGEN | SAP HANA Azure S224 – 4 x Intel® Xeon® Valamint 8276 processzormag, 112 processzormag és 224 processzorszál | 3,0 TB | 6,3 TB | Elérhető |
| IGEN | SAP HANA Azure S224m– 4 x Intel® Xeon® Valamint 8276 processzormag, 112 processzormag és 224 processzorszál | 6,0 TB | 10,5 TB | Elérhető |

- CPU-magok = a kiszolgálóegység nem többszálú processzormagok (a fizikai processzorok teljes száma) összege. 
- CPU-szálak = a kiszolgálóegység többszálú processzormagja (a logikai processzorok teljes száma) által biztosított számítási szálak összege. A legtöbb egység alapértelmezés szerint a Hyper-Threading használatára van konfigurálva.
- A kiszolgálókat az ügyfelek számára árolják.
- Az ügyfél gyökér hozzáféréssel rendelkezik (nincs hipervizor).
- A kiszolgálók nem közvetlenül az Azure-beli virtuális gépeken vannak.

## <a name="next-steps"></a>Következő lépések

Ismerje meg az BareMetal Infrastructure for Oracle által kínált tárterületet.

> [!div class="nextstepaction"]
> [Tárolás BareMetalon Oracle számítási feladatokhoz](oracle-baremetal-storage.md)

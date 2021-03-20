---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: ef18feb10dabc6a77e6512c6a32ad44b32c6e832
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "80334787"
---
**Prémium nem felügyelt virtuálisgép-lemezek: felhasználónkénti korlátok**

| Erőforrás | Korlát |
| --- | --- |
| Fiókonkénti teljes lemezkapacitás |35 TB |
| Fiókonkénti teljes pillanatkép-kapacitás |10 TB |
| Maximális sávszélesség/fiók (bejövő és kimenő forgalom)<sup>1</sup> |<=50 Gbps |

<sup>1</sup>a *bejövő* forgalom a Storage-fiókba küldött kérelmekből származó összes értékre vonatkozik. A *kimenő* forgalom a Storage-fiókból kapott válaszok összes adatértékére vonatkozik.

**Prémium nem felügyelt virtuálisgép-lemezek: lemezterület-korlátok**

| Prémium szintű tárolólemezek típusai | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| Lemezméret |128 GiB |512 GiB |1 024 GiB (1 TB) |2 048 GiB (2 TB)|4 095 GiB (4 TB)|
| IOPS maximális száma lemezenként |500 |2300 |5000 |7500 |7500 |
| Maximális átviteli sebesség (lemez) |100 MB/s | 150 MB/s |200 MB/s |250 MB/s |250 MB/s |
| Lemezek maximális száma Storage-fiókban |280 |70 |35 | 17 | 8 |

**Prémium nem felügyelt virtuálisgép-lemezek: virtuális gépekre vonatkozó korlátok**

| Erőforrás | Korlát |
| --- | --- |
| IOPS maximális száma virtuális gépenként |80 000 IOPS GS5 virtuális géppel |
| Maximális átviteli sebesség virtuális gépenként |2 000 MB/mp GS5 virtuális géppel |


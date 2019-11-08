---
title: fájl belefoglalása
description: fájl belefoglalása
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: 380a0a76de763df5f2cc6ff702750b36d233728d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818355"
---
Egy előfizetésen belül több szolgáltatást is létrehozhat. Mindegyiket egy adott rétegben lehet kiépíteni. Csak az egyes szintek által engedélyezett szolgáltatások száma korlátozza. Létrehozhat például akár 12 szolgáltatást az alapszintű szinten, és egy másik 12 szolgáltatást az S1 szinten ugyanazon előfizetés keretében. További információ a rétegek használatáról: [SKU vagy szintű Azure Cognitive Search kiválasztása](../articles/search/search-sku-tier.md).

Kérés esetén a szolgáltatási korlátok maximális száma is megoldható. Ha ugyanahhoz az előfizetéshez több szolgáltatásra van szüksége, forduljon az Azure ügyfélszolgálatához.

| Erőforrás            | Ingyenes<sup>1</sup> | Basic | S1  | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| Szolgáltatások maximális száma    |1     | 16    | 16  | 8  | 6  | 6     | 6  | 6  |
| Maximális méretezés a keresési egységekben (SU)<sup>2</sup> |N/A |3 SU |36 SU |36 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> az ingyenes szolgáltatás megosztott, nem dedikált erőforrásokon alapul. A vertikális felskálázás nem támogatott a megosztott erőforrásokon.

<sup>2</sup> a keresési egységek olyan számlázási egységek, amelyek *replikaként* vagy *partícióként*vannak kiosztva. A tárolási, indexelési és lekérdezési műveletekhez mindkét erőforrásra szüksége van. A SU-számításokkal kapcsolatos további információkért lásd: [erőforrások szintjének skálázása lekérdezési és indexelési](../articles/search/search-capacity-planning.md)feladatokhoz. 
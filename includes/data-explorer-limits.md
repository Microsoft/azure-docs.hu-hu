---
author: ilayrn
ms.service: data-explorer
ms.topic: include
ms.date: 01/20/2020
ms.author: ilayr
ms.openlocfilehash: d9e23e74cd980ecf44cd04ad2b6f89dc6a4cf868
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "76548207"
---
Az alábbi táblázat az Azure Adatkezelő-fürtök maximális korlátait mutatja be.

| Erőforrás | Korlát |
| --- | --- |
| Fürtök régiónként/előfizetés | 20 |
| Példányok száma fürt szerint | 1000 | 
| A fürtben található adatbázisok száma | 10,000 |
| A fürtben található csatolt adatbázis-konfigurációk száma | 70 |

Az alábbi táblázat az Azure Adatkezelő-fürtökön végrehajtott felügyeleti műveletek korlátait ismerteti.

| Hatókör | Művelet | Korlát |
| --- | --- | --- |
| Fürt | olvasás (például egy fürt beszerzése) | 500/5 perc |
| Fürt | írás (például adatbázis létrehozása) | 1000/óra |


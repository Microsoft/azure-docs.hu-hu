---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/20/2020
ms.author: glenga
ms.openlocfilehash: 7d1bf8dd2d1c8feab8b051a8edad7d5e570ee11b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96025736"
---
A CRON-kifejezésekkel használt alapértelmezett időzóna az egyezményes világidő (UTC) szerint van megadva. Ha egy másik időzóna alapján szeretné megkeresni a CRON-kifejezést, hozzon létre egy alkalmazást az nevű Function-alkalmazáshoz `WEBSITE_TIME_ZONE` . 

A beállítás értéke attól függ, hogy az operációs rendszer és az alkalmazás melyik csomagja fut.

|Operációs rendszer |Felkészülés |Érték |
|-|-|-|
| **Windows** |Mind | Állítsa az értéket a kívánt időzóna nevére, ahogy azt a Windows-parancs által megadott párok második sora adja. `tzutil.exe /L` |
| **Linux** |Prémium<br/>Dedikált |Állítsa az értéket a kívánt időzóna nevére a [TZ-adatbázisban](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)látható módon. |

> [!NOTE]
> `WEBSITE_TIME_ZONE` jelenleg nem támogatott a Linux-fogyasztási tervben.

Például a keleti idő az Egyesült Államokban (amelyet `Eastern Standard Time` (Windows) vagy `America/New_York` (Linux)) jelenleg utc-05:00-et használ a téli idő és az utc-04:00 során a nyári idő alatt. Ahhoz, hogy az időzítő elindítsa a tüzet a 10:00 Kelet-napi időszakban, hozzon létre egy alkalmazás-beállítást a nevű Function alkalmazáshoz `WEBSITE_TIME_ZONE` , állítsa az értéket `Eastern Standard Time` (Windows) vagy `America/New_York` (Linux), majd használja a következő NCRONTAB kifejezést: 

```
"0 0 10 * * *"
``` 

Ha az `WEBSITE_TIME_ZONE` időpontot használja az időzóna időváltozásainak megfelelően, beleértve a nyári időmegtakarítást és a normál időben végzett módosításokat is.

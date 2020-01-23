---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 08/09/2019
ms.author: jroth
ms.openlocfilehash: 17d6947efcbaf8a01aeca70bf7a88dcc7b65cd0a
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548210"
---
| Erőforrás | Ingyenes | Közös | Basic | Standard | Prémium (v2) | Izolált </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Web-, mobil-vagy API-alkalmazások](https://azure.microsoft.com/services/app-service/) [Azure app Service](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> . csomag |10 |100 |Korlátlan<sup>2</sup> |Korlátlan<sup>2</sup> |Korlátlan<sup>2</sup> |Korlátlan<sup>2</sup>|
| [App Service-csomag](../articles/app-service/overview-hosting-plans.md) |10 régiónként |10/erőforráscsoport |100/erőforráscsoport |100/erőforráscsoport |100/erőforráscsoport |100/erőforráscsoport|
| Számítási példány típusa |Közös |Közös |<sup>3</sup> . dedikált |<sup>3</sup> . dedikált |<sup>3</sup> . dedikált</p> |<sup>3</sup> . dedikált|
| Vertikális [felskálázás](../articles/app-service/manage-scale-up.md) (példányok maximális száma) |1 megosztott |1 megosztott |3 dedikált<sup>3</sup> |10 dedikált<sup>3</sup> |30 dedikált<sup>3</sup>|100 dedikált<sup>4</sup>|
| <sup>5</sup> . tárterület |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup></p> |1 TB<sup>5</sup>|
| CPU-idő (5 perc)<sup>6</sup> |3 perc |3 perc |Korlátlan, standard [díjszabású](https://azure.microsoft.com/pricing/details/app-service/) díj</a> |Korlátlan, standard [díjszabású](https://azure.microsoft.com/pricing/details/app-service/) díj</a> |Korlátlan, standard [díjszabású](https://azure.microsoft.com/pricing/details/app-service/) díj</a> |Korlátlan, standard [díjszabású](https://azure.microsoft.com/pricing/details/app-service/) díj</a>|
| CPU-idő (nap)<sup>6</sup> |60 perc |240 perc |Korlátlan, standard [díjszabású](https://azure.microsoft.com/pricing/details/app-service/) díj</a> |Korlátlan, standard [díjszabású](https://azure.microsoft.com/pricing/details/app-service/) díj</a> |Korlátlan, standard [díjszabású](https://azure.microsoft.com/pricing/details/app-service/) díj</a> |Korlátlan, standard [díjszabású](https://azure.microsoft.com/pricing/details/app-service/) díj</a> |
| Memória (1 óra) |1 024 MB/App Service csomag |1 024 MB/alkalmazás |– |– |– |– |
| Bandwidth |165 MB |Korlátlan [adatátviteli díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/) érvényes |Korlátlan [adatátviteli díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/) érvényes |Korlátlan [adatátviteli díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/) érvényes |Korlátlan [adatátviteli díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/) érvényes |Korlátlan [adatátviteli díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/) érvényes |
| Alkalmazásarchitektúra |32 bites |32 bites |32 bites/64 bites |32 bites/64 bites |32 bites/64 bites |32 bites/64 bites |
| Web Sockets/instance<sup>7</sup> |5 |35 |350 |Korlátlan |Korlátlan |Korlátlan |
| IP-kapcsolatok | 600 | 600 | A példány<sup>8</sup> . mérettől függ | A példány<sup>8</sup> . mérettől függ | A példány<sup>8</sup> . mérettől függ | 16000 |
| Egyidejű [hibakereső kapcsolatok](../articles/app-service/troubleshoot-dotnet-visual-studio.md) alkalmazása |1 |1 |1 |5 |5 |5 |
| Tanúsítványok App Service<sup>9</sup> /előfizetés| Nem támogatott | Nem támogatott |10 |10 |10 |10 |
| Egyéni tartományok/alkalmazás</a> |0 (csak azurewebsites.net altartomány)|500 |500 |500 |500 |500 |
| Egyéni tartomány [SSL-támogatása](../articles/app-service/configure-ssl-certificate.md) |Nem támogatott, a (*. azurewebsites.net) helyettesítő tanúsítványa alapértelmezés szerint elérhető|Nem támogatott, a (*. azurewebsites.net) helyettesítő tanúsítványa alapértelmezés szerint elérhető|Korlátlan SNI SSL kapcsolatok |Korlátlan számú SNI SSL és 1 IP SSL kapcsolat található |Korlátlan számú SNI SSL és 1 IP SSL kapcsolat található | Korlátlan számú SNI SSL és 1 IP SSL kapcsolat található|
| Hibrid kapcsolatok/csomag | | | 5 | 25 | 200 | 200 |
| Integrált Load Balancer | |X |X |X |X |X<sup>10</sup> |
| [Always on](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Ütemezett biztonsági mentések](../articles/app-service/manage-backup.md) | | | | Ütemezett biztonsági mentések 2 óránként, legfeljebb 12 biztonsági mentés naponta (manuális + ütemezett) | Ütemezett biztonsági mentések óránként, legfeljebb 50 biztonsági mentéssel (manuális + ütemezett) | Ütemezett biztonsági mentések óránként, legfeljebb 50 biztonsági mentéssel (manuális + ütemezett) |
| [Automatikus méretezés](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [Webjobs](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| [Azure Scheduler](https://azure.microsoft.com/services/scheduler/) -támogatás | |X |X |X |X |X |
| [Végpontmonitoring](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Átmeneti tárolóhelyek](../articles/app-service/deploy-staging-slots.md) | | | |5 |20 |20 |
| Szolgáltatási szerződés | |  |99,95%|99,95%|99,95%|99,95%|  

<sup>1</sup> Az alkalmazások és a tárolási kvóták App Service csomaggal rendelkeznek, hacsak másként nincs jelezve.  
<sup>2</sup> Az ezeken a gépeken üzemeltethető alkalmazások tényleges száma az alkalmazások tevékenységéből, a gépi példányok méretétől és a megfelelő erőforrás-felhasználástól függ.  
<sup>3</sup> A dedikált példányok különböző méretűek lehetnek. További információ: [app Service díjszabása](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup> További kérések esetén engedélyezett.  
<sup>5</sup> A tárolási korlát a teljes tartalom mérete az adott app Service-csomagban lévő összes alkalmazásban. Az egyetlen erőforráscsoport és régió összes app Service-csomagjában lévő alkalmazások teljes tartalmának mérete nem haladhatja meg az 500 GB-ot.  
<sup>6</sup> Ezeket az erőforrásokat a dedikált példányokon található fizikai erőforrások korlátozzák (a példány mérete és a példányok száma).  
<sup>7</sup> Ha az alkalmazást az alapszintű csomagból két példányra méretezi, akkor a két példány esetében 350 egyidejű kapcsolat. A standard szinthez és a fentiekhez nem tartoznak elméleti korlátok a webes szoftvercsatornák számára, más tényezők pedig korlátozhatják a webes szoftvercsatornák számát. Például a maximálisan megengedett kérelmek (a `maxConcurrentRequestsPerCpu`által meghatározottak szerint) a következők: 7 500/kisméretű virtuális gép, 15 000/közepes virtuális gép (7 500 x 2 mag) és 75 000/nagyméretű virtuális gép (18 750 x 4 mag).  
<sup>8</sup> A maximális IP-kapcsolatok a példányok száma, és a példány méretétől függ: 1 920/B1/S1/P1V2 példány, 3 968/B2/S2/P2V2-példány, 8 064/B3/S3/P3V2 példány.  
<sup>9</sup> Az előfizetések App Service-tanúsítvány kvóta-korlátja egy támogatási kérelem alapján növelhető, amely legfeljebb 200 lehet.  
<sup>10</sup> A izolált App Service SKU-k belsőleg terheléselosztást (ILB) is használhatnak a Azure Load Balancer, így nincs nyilvános kapcsolat az internetről. Ezért a belső terheléselosztású izolált App Service egyes funkcióit csak olyan gépekről lehet elérni, amelyek közvetlen hozzáféréssel rendelkeznek az ILB-hálózat végpontjához.  
<sup>11</sup> Az egyéni végrehajtható fájlokat és/vagy parancsfájlokat igény szerint, ütemezés szerint, vagy a App Service-példányon belüli háttérbeli feladatként futtathatja. A folyamatos WebJobs-végrehajtáshoz folyamatos üzem szükséges. Az ütemezett WebJobs-végrehajtás csak az Azure Scheduler Ingyenes vagy Standard rétegével használható. Nincs előre meghatározott korlát a App Service-példányban futtatható webjobs-feladatok számánál. Az alkalmazás kódjának működéséhez szükséges gyakorlati korlátozások is megváltoznak.  

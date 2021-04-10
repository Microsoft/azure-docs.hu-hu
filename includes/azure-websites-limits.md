---
author: rothja
ms.service: app-service
ms.topic: include
ms.date: 03/17/2020
ms.author: msangapu
ms.openlocfilehash: 80d295d017b11d86df7a3fe4c14afc7a5665cd96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104612986"
---
| Erőforrás | Ingyenes | Megosztott | Alapszintű | Standard | Prémium (v1-v3) | Izolált </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Web-, mobil-vagy API-alkalmazások](https://azure.microsoft.com/services/app-service/) [Azure app Service](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> . csomag |10 |100 |Korlátlan<sup>2</sup> |Korlátlan<sup>2</sup> |Korlátlan<sup>2</sup> |Korlátlan<sup>2</sup>|
| [App Service-csomag](../articles/app-service/overview-hosting-plans.md) |10 régiónként |10/erőforráscsoport |100/erőforráscsoport |100/erőforráscsoport |100/erőforráscsoport |100/erőforráscsoport|
| Számítási példány típusa |Megosztott |Megosztott |<sup>3</sup> . dedikált |<sup>3</sup> . dedikált |<sup>3</sup> . dedikált</p> |<sup>3</sup> . dedikált|
| Vertikális [felskálázás](../articles/app-service/manage-scale-up.md) (példányok maximális száma) |1 megosztott |1 megosztott |3 dedikált<sup>3</sup> |10 dedikált<sup>3</sup> | 20 dedikált v1 és v2; 30 dedikált v3. <sup>3</sup>|100 dedikált<sup>4</sup>|
| <sup>5</sup> . tárterület |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup> <br/><br/> Ha több mint 250 GB-nyi, küldjön egy támogatási kérést. |1 TB<sup>5</sup> <br/><br/> A rendelkezésre álló tárolási kvóta 999 GB. |
| CPU-idő (5 perc)<sup>6</sup> |3 perc |3 perc |Korlátlan, standard [díjszabás](https://azure.microsoft.com/pricing/details/app-service/) szerint fizetve</a> |Korlátlan, standard [díjszabás](https://azure.microsoft.com/pricing/details/app-service/) szerint fizetve</a> |Korlátlan, standard [díjszabás](https://azure.microsoft.com/pricing/details/app-service/) szerint fizetve</a> |Korlátlan, standard [díjszabás](https://azure.microsoft.com/pricing/details/app-service/) szerint fizetve</a>|
| CPU-idő (nap)<sup>6</sup> |60 perc |240 perc |Korlátlan, standard [díjszabás](https://azure.microsoft.com/pricing/details/app-service/) szerint fizetve</a> |Korlátlan, standard [díjszabás](https://azure.microsoft.com/pricing/details/app-service/) szerint fizetve</a> |Korlátlan, standard [díjszabás](https://azure.microsoft.com/pricing/details/app-service/) szerint fizetve</a> |Korlátlan, standard [díjszabás](https://azure.microsoft.com/pricing/details/app-service/) szerint fizetve</a> |
| Memória (1 óra) |1 024 MB/App Service csomag |1 024 MB/alkalmazás |N.A. |N.A. |N.A. |N.A. |
| Sávszélesség |165 MB |Korlátlan [adatátviteli díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/) érvényes |Korlátlan [adatátviteli díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/) érvényes |Korlátlan [adatátviteli díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/) érvényes |Korlátlan [adatátviteli díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/) érvényes |Korlátlan [adatátviteli díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/) érvényes |
| Alkalmazásarchitektúra |32 bites |32 bites |32 bites/64 bites |32 bites/64 bites |32 bites/64 bites |32 bites/64 bites |
| Web Sockets/instance<sup>7</sup> |5 |35 |350 |Korlátlan |Korlátlan |Korlátlan |
| Kimenő IP-kapcsolatok száma példányban | 600 | 600 | A példány<sup>8</sup> . mérettől függ | A példány<sup>8</sup> . mérettől függ | A példány<sup>8</sup> . mérettől függ | 16000 |
| Egyidejű [hibakereső kapcsolatok](../articles/app-service/troubleshoot-dotnet-visual-studio.md) alkalmazása |1 |1 |1 |5 |5 |5 |
| Tanúsítványok App Service<sup>9</sup> /előfizetés| Nem támogatott | Nem támogatott |10 |10 |10 |10 |
| Egyéni tartományok/alkalmazás</a> |0 (csak azurewebsites.net altartomány)|500 |500 |500 |500 |500 |
| Egyéni tartomány [SSL-támogatása](../articles/app-service/configure-ssl-certificate.md) |Nem támogatott, a. azurewebsites.net helyettesítő tanúsítványa \* alapértelmezés szerint elérhető.|Nem támogatott, a. azurewebsites.net helyettesítő tanúsítványa \* alapértelmezés szerint elérhető.|Korlátlan SNI SSL kapcsolatok |Korlátlan számú SNI SSL és 1 IP SSL kapcsolat található |Korlátlan számú SNI SSL és 1 IP SSL kapcsolat található | Korlátlan számú SNI SSL és 1 IP SSL kapcsolat található|
| Hibrid kapcsolatok | | | 5/csomag | 25/csomag | 200/alkalmazás | 200/alkalmazás |
| [Virtuális hálózat integrációja](../articles/app-service/web-sites-integrate-with-vnet.md) | | |   |  X |  X  |  X  |
| [Privát végpontok](../articles/app-service/networking/private-endpoint.md) | | |   |   |  100/alkalmazás  |    |
| Integrált Load Balancer | |X |X |X |X |X<sup>10</sup> |
| [Hozzáférési korlátozások](../articles/app-service/networking-features.md#access-restrictions) | 512 szabályok/alkalmazás | 512 szabályok/alkalmazás | 512 szabályok/alkalmazás | 512 szabályok/alkalmazás | 512 szabályok/alkalmazás | 512 szabályok/alkalmazás |
| [Always on](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Ütemezett biztonsági mentések](../articles/app-service/manage-backup.md) | | | | Ütemezett biztonsági mentések 2 óránként, legfeljebb 12 biztonsági mentés naponta (manuális + ütemezett) | Ütemezett biztonsági mentések óránként, legfeljebb 50 biztonsági mentéssel (manuális + ütemezett) | Ütemezett biztonsági mentések óránként, legfeljebb 50 biztonsági mentéssel (manuális + ütemezett) |
| [Automatikus méretezés](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [Webjobs](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| [Végpont-monitorozás](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Átmeneti tárolóhelyek](../articles/app-service/deploy-staging-slots.md) /alkalmazás| | | |5 |20 |20 |
| [Tesztelés éles környezetben](../articles/app-service/deploy-staging-slots.md#route-traffic)| | | |X |X |X |
| [Diagnosztikai naplók](../articles/app-service/troubleshoot-diagnostic-logs.md) | X | X | X | X | X | X |
| Kudu | X | X | X | X | X | X |
| [Hitelesítés és engedélyezés](../articles/app-service/overview-authentication-authorization.md) | X | X | X | X | X | X |
| [App Service felügyelt tanúsítványok (nyilvános előzetes verzió)](https://azure.microsoft.com/updates/secure-your-custom-domains-at-no-cost-with-app-service-managed-certificates-preview/)<sup>12</sup> | |  | X | X | X | X |
| SLA | |  |99,95%|99,95%|99,95%|99,95%|

<sup>1</sup> az alkalmazások és a tárolási kvóták app Service csomagra vonatkoznak, hacsak másként nincs jelezve.

<sup>2</sup> az ezeken a gépeken üzemeltethető alkalmazások tényleges száma az alkalmazások tevékenységéből, a gépi példányok méretétől és a megfelelő erőforrás-felhasználástól függ.

<sup>3</sup> a dedikált példányok különböző méretűek lehetnek. További információ: [app Service díjszabása](https://azure.microsoft.com/pricing/details/app-service/).

<sup>4</sup> további kérelem esetén engedélyezett.

<sup>5</sup> a tárolási korlát a teljes tartalom mérete az adott app Service-csomagban lévő összes alkalmazásban. Az egyetlen erőforráscsoport és régió összes app Service-csomagjában lévő alkalmazások teljes tartalmának mérete nem haladhatja meg a 500 GB-ot. App Service üzemeltetett alkalmazások fájlrendszer-kvótáját az adott régióban és erőforráscsoporthoz létrehozott App Service csomagok összesítése határozza meg.

<sup>6</sup> ezeket az erőforrásokat a dedikált példányokon található fizikai erőforrások korlátozzák (a példány mérete és a példányok száma).

<sup>7</sup> ha az alkalmazást az alapszintű csomagból két példányra méretezi, akkor a két példány esetében 350 egyidejű kapcsolat. A standard szinthez és a fentiekhez nem tartoznak elméleti korlátok a webes szoftvercsatornák számára, más tényezők pedig korlátozhatják a webes szoftvercsatornák számát. Például a maximálisan megengedett kérelmek (a által meghatározottak szerint `maxConcurrentRequestsPerCpu` ) a következők: 7 500/kisméretű virtuális gép, 15 000/közepes méretű virtuális gép (7 500 x 2 mag) és 75 000/nagyméretű virtuális gép (18 750 x 4 mag).

<sup>8</sup> a maximális IP-kapcsolat a példányok száma, és a példány méretétől függ: 1 920/B1/S1/P1V3 példány, 3 968/B2/S2/P2V3-példány, 8 064/B3/S3/P3V3 példány.

<sup>9</sup> az előfizetések app Service-tanúsítvány kvóta-korlátja egy támogatási kérésen keresztül növelhető, legfeljebb 200.

a <sup>10</sup> izolált app Service SKU-k belsőleg terheléselosztást (ILB) is használhatnak a Azure Load Balancer, így nincs nyilvános kapcsolat az internetről. Ennek eredményeképpen a ILB elkülönített App Service egyes funkcióit olyan gépekről kell használni, amelyek közvetlen hozzáféréssel rendelkeznek a ILB hálózati végponthoz.

<sup>11</sup> egyéni végrehajtható fájl és/vagy parancsfájl igény szerint, ütemezés szerint, vagy a app Service-példányon belüli háttérbeli feladatként való folyamatos futtatása. A folyamatos webjobs-végrehajtáshoz mindig be kell jelentkezni. Nincs előre meghatározott korlát a App Service-példányban futtatható webjobs-feladatok számánál. Az alkalmazás kódjának működéséhez szükséges gyakorlati korlátozások is megváltoznak.

<sup>12</sup> meztelen tartomány nem támogatott. Csak a szabványos tanúsítványok kiállítása (a helyettesítő tanúsítványok nem érhetők el). Csak egy ingyenes tanúsítványra korlátozódik egyéni tartományon.

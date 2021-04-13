---
author: rothja
ms.service: app-service
ms.topic: include
ms.date: 03/17/2020
ms.author: msangapu
ms.openlocfilehash: dad7799cb5a7579b28847e3968b6b38f1f98298a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107327021"
---
| Erőforrás | Ingyenes | Megosztott | Alapszintű | Standard | Prémium (v1-v3) | Izolált </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Web-, mobil- vagy API-alkalmazások Azure App Service](https://azure.microsoft.com/services/app-service/) [](../articles/app-service/overview-hosting-plans.md)<sup>1. csomagonként</sup> |10 |100 |Korlátlan<sup>2</sup> |Korlátlan<sup>2</sup> |Korlátlan<sup>2</sup> |Korlátlan<sup>2</sup>|
| [App Service-csomag](../articles/app-service/overview-hosting-plans.md) |Régiónként 10 |Erőforráscsoportonként 10 |Erőforráscsoportonként 100 |Erőforráscsoportonként 100 |Erőforráscsoportonként 100 |Erőforráscsoportonként 100|
| Számítási példány típusa |Megosztott |Megosztott |Dedikált<sup>3</sup> |Dedikált<sup>3</sup> |Dedikált<sup>3</sup></p> |Dedikált<sup>3</sup>|
| [Horizontális felskálás](../articles/app-service/manage-scale-up.md) (maximális példányok) |1 megosztva |1 megosztva |3 dedikált<sup>3</sup> |10 dedikált<sup>3</sup> | 20 dedikált v1 és v2; 30 dedikált a v3-ashoz. <sup>3</sup>|100 dedikált<sup>4</sup>|
| <sup>5. tároló</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup> |1 TB<sup>5</sup> <br/><br/> A rendelkezésre álló tárolási kvóta 999 GB. |
| CPU-idő (5 perc)<sup>6</sup> |3 perc |3 perc |Korlátlan, standard díjszabás mellett [fizet](https://azure.microsoft.com/pricing/details/app-service/)</a> |Korlátlan, normál díjszabás mellett [fizet](https://azure.microsoft.com/pricing/details/app-service/)</a> |Korlátlan, normál díjszabás mellett [fizet](https://azure.microsoft.com/pricing/details/app-service/)</a> |Korlátlan, standard díjszabás mellett [fizet](https://azure.microsoft.com/pricing/details/app-service/)</a>|
| CPU-idő (nap)<sup>6</sup> |60 perc |240 perc |Korlátlan, normál díjszabás mellett [fizet](https://azure.microsoft.com/pricing/details/app-service/)</a> |Korlátlan, normál díjszabás mellett [fizet](https://azure.microsoft.com/pricing/details/app-service/)</a> |Korlátlan, normál díjszabás mellett [fizet](https://azure.microsoft.com/pricing/details/app-service/)</a> |Korlátlan, normál díjszabás mellett [fizet](https://azure.microsoft.com/pricing/details/app-service/)</a> |
| Memória (1 óra) |1024 MB App Service csomagonként |1024 MB alkalmazásonként |N.A. |N.A. |N.A. |N.A. |
| Sávszélesség |165 MB |Korlátlan, [adatátviteli díjszabás érvényes](https://azure.microsoft.com/pricing/details/data-transfers/) |Korlátlan, [adatátviteli díjszabás érvényes](https://azure.microsoft.com/pricing/details/data-transfers/) |Korlátlan, [adatátviteli díjszabás érvényes](https://azure.microsoft.com/pricing/details/data-transfers/) |Korlátlan, [adatátviteli díjszabás érvényes](https://azure.microsoft.com/pricing/details/data-transfers/) |Korlátlan, [adatátviteli díjszabás érvényes](https://azure.microsoft.com/pricing/details/data-transfers/) |
| Alkalmazásarchitektúra |32 bites |32 bites |32 bites/64 bites |32 bites/64 bites |32 bites/64 bites |32 bites/64 bites |
| Webes szoftvercsatornák példányonként<sup>7</sup> |5 |35 |350 |Korlátlan |Korlátlan |Korlátlan |
| Kimenő IP-kapcsolatok példányonként | 600 | 600 | A<sup>8-as</sup> példánymérettől függ | A<sup>8-as példánymérettől függ</sup> | A<sup>8-as példánymérettől függ</sup> | 16000 |
| Egyidejű [hibakereső-kapcsolatok](../articles/app-service/troubleshoot-dotnet-visual-studio.md) alkalmazásonként |1 |1 |1 |5 |5 |5 |
| App Service tanúsítványok előfizetésenként<sup>9</sup>| Nem támogatott | Nem támogatott |10 |10 |10 |10 |
| Egyéni tartományok alkalmazásonként</a> |0 (csak azurewebsites.net altartomány)|500 |500 |500 |500 |500 |
| Egyéni tartomány [SSL-támogatása](../articles/app-service/configure-ssl-certificate.md) |Nem támogatott, a \* .azurewebsites.net helyettesítő tanúsítvány alapértelmezés szerint elérhető|Nem támogatott, a \* .azurewebsites.net helyettesítő tanúsítvány alapértelmezés szerint elérhető|Korlátlan SNI SSL kapcsolatok |Korlátlan SNI SSL és 1 IP SSL tartalmaz |Korlátlan SNI SSL és 1 IP SSL kapcsolat | Korlátlan SNI SSL és 1 IP SSL tartalmaz|
| Hibrid kapcsolatok | | | Csomagonként 5 | Csomagonként 25 | Alkalmazásonként 200 | 200 alkalmazásonként |
| [Virtuális hálózat integrációja](../articles/app-service/web-sites-integrate-with-vnet.md) | | |   |  X |  X  |  X  |
| [Privát végpontok](../articles/app-service/networking/private-endpoint.md) | | |   |   |  100 alkalmazásonként  |    |
| Integrált terheléselosztás | |X |X |X |X |X<sup>10</sup> |
| [Hozzáférési korlátozások](../articles/app-service/networking-features.md#access-restrictions) | 512 szabály alkalmazásonként | 512 szabály alkalmazásonként | 512 szabály alkalmazásonként | 512 szabály alkalmazásonként | 512 szabály alkalmazásonként | 512 szabály alkalmazásonként |
| [Always On](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Ütemezett biztonsági mentések](../articles/app-service/manage-backup.md) | | | | Ütemezett biztonsági mentések 2 óránként, naponta legfeljebb 12 biztonsági mentés (manuális + ütemezett) | Ütemezett biztonsági mentések óránként, naponta legfeljebb 50 biztonsági mentéssel (manuális + ütemezett) | Ütemezett biztonsági mentések óránként, naponta legfeljebb 50 biztonsági mentéssel (manuális + ütemezett) |
| [Automatikus méretezés](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| [Végpontfigyelés](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Előkészítési pont alkalmazásonként](../articles/app-service/deploy-staging-slots.md)| | | |5 |20 |20 |
| [Tesztelés éles környezetben](../articles/app-service/deploy-staging-slots.md#route-traffic)| | | |X |X |X |
| [Diagnosztikai naplók](../articles/app-service/troubleshoot-diagnostic-logs.md) | X | X | X | X | X | X |
| Kudu | X | X | X | X | X | X |
| [Hitelesítés és engedélyezés](../articles/app-service/overview-authentication-authorization.md) | X | X | X | X | X | X |
| [App Service tanúsítványok (nyilvános előzetes verzió)](https://azure.microsoft.com/updates/secure-your-custom-domains-at-no-cost-with-app-service-managed-certificates-preview/)<sup>12</sup> | |  | X | X | X | X |
| SLA | |  |99,95%|99,95%|99,95%|99,95%|

<sup>1 Az</sup> alkalmazás- és tárolási kvóták tervenként App Service kivéve, ha másként jelezték.

<sup>2</sup> Az ezeken a gépeken gazdaalkalmazások tényleges száma az alkalmazások tevékenységétől, a géppéldányok méretétől és a megfelelő erőforrás-kihasználtságtól függ.

<sup>3 A</sup> dedikált példányok különböző méretűek lehetnek. További információkért lásd: [App Service díjszabása.](https://azure.microsoft.com/pricing/details/app-service/)

<sup>4</sup> Kérés esetén több is engedélyezett.

<sup>5</sup> A tárhelykorlát az összes alkalmazás teljes tartalommérete ugyanabban az App Service-csomagban. Az összes App Service-csomag összes alkalmazásának teljes tartalommérete egyetlen erőforráscsoportban és régióban nem haladhatja meg az 500 GB-ot. Az üzemeltetett alkalmazások App Service kvótáját az egy régióban és erőforráscsoportban App Service csomagok összesítése határozza meg.

<sup>6</sup> Ezeket az erőforrásokat a dedikált példányok fizikai erőforrásai (a példány mérete és a példányok száma) korlátozza.

<sup>7</sup> Ha az Alapszintű szinten egy alkalmazást két példányra skáláz, mindkét példányhoz 350 egyidejű kapcsolattal rendelkezik. A Standard szint és a magasabb szintek esetében a webes szoftvercsatornákra nem vonatkoznak elméleti korlátok, más tényezők azonban korlátozhatják a webes szoftvercsatornák számát. Az egyidejű kérések maximálisan engedélyezett száma (a által definiált) például `maxConcurrentRequestsPerCpu` a következő: 7500 kis virtuális gépenként, 15 000 közepes virtuális gépenként (7500 x 2 mag) és 75 000 nagy virtuális gépenként (18 750 x 4 mag).

<sup>8</sup> A maximális IP-kapcsolatok példányonként vannak, és a példány méretétől függnek: 1 920 B1/S1/P1V3 példányonként, 3968 B2/S2/P2V3 példányonként, 8064 B3/S3/P3V3 példányonként.

<sup>9</sup> Az App Service-tanúsítvány kvótakorlátja egy támogatási kéréssel növelhető legfeljebb 200-ra.

<sup>10</sup> izolált App Service SKUs belsőleg elosztott terhelésű (ILB) Azure Load Balancer, így az internetről nincs nyilvános kapcsolat. Ennek eredményeképpen az ILB elkülönített App Service egyes funkcióit olyan gépekről kell használni, amelyek közvetlen hozzáféréssel rendelkezik az ILB hálózati végponthoz.

<sup>11</sup> Egyéni végrehajtható fájlok és/vagy szkriptek futtatása igény szerint, ütemezés szerint vagy folyamatosan háttérfeladatként a App Service példányon. Folyamatos WebJobs-végrehajtáshoz Always On szükséges. A webpéldányban futtatható WebJobs-App Service nincs előre meghatározott korlátja. Gyakorlati korlátok attól függnek, hogy az alkalmazás kódja mit próbál tenni.

<sup>12 A</sup> tartományok nem támogatottak. Csak szabványos tanúsítványok kiállítása (helyettesítő tanúsítványok nem érhetők el). Egyéni tartományonként csak egy ingyenes tanúsítványra van korlátozva.

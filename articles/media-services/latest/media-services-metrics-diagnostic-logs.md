---
title: Metrikák és diagnosztikai naplók Azure Monitor
description: Megtudhatja, hogyan figyelheti Azure Media Services metrikáit és a diagnosztikai naplókat Azure Monitor használatával.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/02/2020
ms.author: inhenkel
ms.openlocfilehash: cd8c6ca67a1e475279cba8ccc3f4cb8cc7412d66
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590780"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs-with-azure-monitor"></a>Media Services metrikák és diagnosztikai naplók figyelése a Azure Monitor

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[Azure monitor](../../azure-monitor/overview.md) segítségével figyelheti a mérőszámokat és a diagnosztikai naplókat, amelyek segítenek megérteni az alkalmazások teljesítményét. Az Azure Monitor által összegyűjtött adatok két alapvető típusba sorolhatók: metrikák és naplók. Nyomon követheti Media Services diagnosztikai naplóit, és riasztásokat és értesítéseket hozhat létre az összegyűjtött metrikák és naplók számára. A metrikák adatait a [metrikák Explorerrel](../../azure-monitor/essentials/metrics-getting-started.md)jelenítheti meg és elemezheti. Naplókat küldhet az [Azure Storage](https://azure.microsoft.com/services/storage/)-ba, továbbíthatja őket az [Azure Event Hubsba](https://azure.microsoft.com/services/event-hubs/), exportálhatja őket [log Analyticsba](https://azure.microsoft.com/services/log-analytics/), vagy külső szolgáltatásokat is használhat.

Részletes Áttekintés: [Azure monitor metrikák](../../azure-monitor/data-platform.md) és [Azure monitor diagnosztikai naplók](../../azure-monitor/essentials/platform-logs-overview.md).

Ez a témakör a támogatott [Media Services metrikákat](#media-services-metrics) és [Media Services diagnosztikai naplókat](#media-services-diagnostic-logs)ismerteti.

## <a name="media-services-metrics"></a>Media Services metrikák

A metrikák gyűjtése rendszeres időközönként történik, függetlenül attól, hogy az érték megváltozik-e. Ezek a riasztások azért hasznosak, mert gyakran mintavételezésre is képesek, és a riasztások gyorsan és viszonylag egyszerű logikával is elindíthatók. A metrikai riasztások létrehozásával kapcsolatos információkért lásd: [metrikai riasztások létrehozása, megtekintése és kezelése Azure monitor használatával](../../azure-monitor/alerts/alerts-metric.md).

Media Services támogatja a következő erőforrások figyelési metrikáit:

* Fiók
* Streamvégpont

### <a name="account"></a>Fiók

A következő fiók metrikáit figyelheti.

|Metrika neve|Megjelenített név|Description|
|---|---|---|
|AssetCount|Eszközök száma|A fiókban lévő eszközök.|
|AssetQuota|Eszköz kvótája|A fiókban lévő eszköz kvótája.|
|AssetQuotaUsedPercentage|Eszköz kvótájának kihasználtsága (%)|Az eszköz kvótájának százalékos aránya már használatban van.|
|ContentKeyPolicyCount|Tartalmi kulcsokra vonatkozó szabályzatok száma|A fiókban található tartalmi kulcsokra vonatkozó szabályzatok.|
|ContentKeyPolicyQuota|Tartalmi kulcs házirend-kvótája|A fiókban található tartalmi kulcs házirendjeinek kvótája.|
|ContentKeyPolicyQuotaUsedPercentage|Tartalom kulcsára vonatkozó házirend kvótájának százalékos aránya|A tartalmi kulcs házirend-kvótájának százalékos aránya már használatban van.|
|StreamingPolicyCount|Folyamatos átviteli szabályzatok száma|Streaming-szabályzatok a fiókban.|
|StreamingPolicyQuota|Streaming Policy-kvóta|Folyamatos átviteli szabályzatok kvótája a fiókban.|
|StreamingPolicyQuotaUsedPercentage|Adatfolyam-házirend kvótájának kihasználtsága (%)|Az adatfolyam-házirend kvótájának százalékos aránya már használatban van.|

Tekintse át [a fiók kvótáit és korlátozásait](limits-quotas-constraints.md)is.

### <a name="streaming-endpoint"></a>Streamvégpont

A következő Media Services [folyamatos átviteli végpontok](/rest/api/media/streamingendpoints) metrikáit támogatja:

|Metrika neve|Megjelenített név|Description|
|---|---|---|
|Kérelmek|Kérelmek|A folyamatos átviteli végpont által kiszolgált HTTP-kérelmek teljes számát adja meg.|
|Kimenő forgalom|Kimenő forgalom|Kimenő átviteli végpontok percenkénti bájtjainak száma.|
|SuccessE2ELatency|A Befejezés végének késése|Az az időtartam, amikor a folyamatos átviteli végpont a válasz utolsó bájtjának küldésére vonatkozó kérést kapott.|
|Processzorhasználat| | A prémium szintű streaming-végpontok CPU-használata. Ezek az adatátviteli végpontok nem érhetők el. |
|Kimenő sávszélesség | | Kimenő forgalom (bit/mp).|

### <a name="metrics-are-useful"></a>A metrikák hasznosak

Íme néhány példa arra, hogy a monitorozási Media Services mérőszámai hogyan segíthetnek az alkalmazások teljesítményének megismerésében. Media Services metrikákkal foglalkozó kérdések a következők:

* Hogyan a standard folyamatos átviteli végpontját, hogy megtudjam, mikor lépték túl a korlátot?
* Hogyan tudni, hogy van-e elegendő prémium szintű streaming Endpoint skálázási egység?
* Hogyan állíthatok be egy riasztást a folyamatos átviteli végpontok vertikális felskálázásához?
* Hogyan beállítani a riasztást, hogy a fiókon beállított maximális kimenő forgalom mikor lett elérve?
* Hogyan láthatom a sikertelen kérelmek részletezését, és mi okozza a hibát?
* Honnan tudhatom meg, hogy hány HLS-vagy DASH-kérelem van lehúzva a csomagolóból?
* A Hogyan riasztást állít be, hogy a rendszer mikor találta meg a sikertelen kérelmek száma értékének küszöbértékét?

A párhuzamosság az egyetlen fiókban használt adatfolyam-végpontok számának időbeli alakulására vonatkozik. Figyelembe kell vennie az egyidejű streamek és az összetett közzétételi paraméterek (például a dinamikus csomagolás több protokoll, több DRM-titkosítás stb.) közötti kapcsolatát. Minden további közzétett élő adatfolyam hozzáadja a PROCESSZORt és a kimeneti sávszélességet a streaming végponton. Ennek szem előtt tartásával érdemes Azure Monitor a folyamatos átviteli végpont kihasználtságának (CPU és kimenő kapacitás) alapos figyelésére, hogy a megfelelő méretezéssel (vagy több folyamatos átviteli végpont közötti adatforgalom felosztásával) legyen.

### <a name="example"></a>Példa

Lásd: [Media Services-metrikák figyelése](media-services-metrics-howto.md).

## <a name="media-services-diagnostic-logs"></a>Diagnosztikai naplók Media Services

A diagnosztikai naplók részletes és gyakori információkat biztosítanak az Azure-erőforrások működéséről. További információkért lásd: az [Azure-erőforrások naplózási adatainak gyűjtése és felhasználása](../../azure-monitor/essentials/platform-logs-overview.md).

A Media Services a következő diagnosztikai naplókat támogatja:

* Kulcs kézbesítése

### <a name="key-delivery"></a>Kulcs kézbesítése

|Név|Leírás|
|---|---|
|Kulcs kézbesítési szolgáltatásának kérése|A kulcs kézbesítési szolgáltatására vonatkozó adatokat megjelenítő naplók. További információ: [sémák](media-services-diagnostic-logs-schema.md).|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>Miért érdemes diagnosztikai naplókat használni?

Néhány dolog, amit megvizsgálhat a kulcsfontosságú kézbesítési diagnosztikai naplók használatával:

* Tekintse meg a DRM-típus által szállított licencek számát.
* Tekintse meg a szabályzat által szállított licencek számát.
* A hibák a DRM vagy a házirend típusa szerint jelennek meg.
* Tekintse meg az ügyfelektől érkező jogosulatlan licencek számát.

### <a name="example"></a>Példa

Lásd: [a Media Service diagnosztikai naplóinak figyelése](media-services-diagnostic-logs-howto.md).

## <a name="next-steps"></a>Következő lépések

* [Az Azure-erőforrások naplózási adatainak gyűjtése és felhasználása](../../azure-monitor/essentials/platform-logs-overview.md)
* [Metrikariasztások létrehozása, megtekintése és kezelése az Azure Monitorral](../../azure-monitor/alerts/alerts-metric.md)
* [Media Services mérőszámok figyelése](media-services-metrics-howto.md)
* [A Media Service diagnosztikai naplóinak figyelése](media-services-diagnostic-logs-howto.md)

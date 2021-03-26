---
title: Figyelés Media Services
description: Itt megtudhatja, hogyan figyelheti Media Services
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femilia
ms.topic: how-to
ms.service: media-services
ms.custom: subject-monitoring
ms.date: 03/17/2021
ms.openlocfilehash: 90ca92dc19c588d0b19adf009301cf844e0cdbde
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609045"
---
# <a name="monitor-media-services"></a>Figyelő Media Services

Ha kritikus fontosságú alkalmazásokat és üzleti folyamatokat kíván használni az Azure-erőforrásokon, figyelnie kell ezeket az erőforrásokat a rendelkezésre állással, a teljesítménnyel és a művelettel kapcsolatban. Ez a cikk ismerteti a Media Services által generált figyelési információkat, valamint azt, hogy miként használhatók a Azure Monitor funkciói az adatelemzéshez és a riasztáshoz.

## <a name="metrics-are-useful"></a>A metrikák hasznosak

Íme néhány példa arra, hogy a monitorozási Media Services mérőszámai hogyan segíthetnek az alkalmazások teljesítményének megismerésében. Media Services metrikákkal foglalkozó kérdések a következők:

- Hogyan a standard folyamatos átviteli végpontját, hogy megtudjam, mikor lépték túl a korlátot?
- Hogyan tudni, hogy van-e elegendő prémium szintű streaming Endpoint skálázási egység?
- Hogyan állíthatok be egy riasztást a folyamatos átviteli végpontok vertikális felskálázásához?
- Hogyan beállítani a riasztást, hogy a fiókon beállított maximális kimenő forgalom mikor lett elérve?
- Hogyan láthatom a sikertelen kérelmek részletezését, és mi okozza a hibát?
- Honnan tudhatom meg, hogy hány HLS-vagy DASH-kérelem van lehúzva a csomagolóból?
- Hogyan beállítani a riasztást, hogy mikor van a sikertelen kérelmek küszöbértéke?

<!--THIS DOESN'T BELONG HERE Concurrency becomes a concern for the number of Streaming Endpoints used in a single account over time. You need to keep in mind the relationship between the number of concurrent streams with complex publishing parameters like dynamic packaging to multiple protocols, multiple DRM encryptions etc. Each additional published live stream adds to the CPU and output bandwidth on the Streaming Endpoint. With that in mind, you should use Azure Monitor to closely watch the Streaming Endpoint's utilization (CPU and Egress capacity) to make certain that you are scaling it appropriately (or split traffic out between multiple Streaming Endpoints if you are getting into very high concurrency).-->

<!-- Optional diagram showing monitoring for your service. If you need help creating one, contact 
robb@microsoft.com -->

## <a name="what-is-azure-monitor"></a>Mi az Azure Monitor?

A Media Services a [Azure monitor](../../../azure-monitor/overview.md)használatával hoz létre figyelési adatokkal, amely az Azure teljes verem-figyelési szolgáltatása, amely teljes körű funkciókat biztosít az Azure-erőforrások figyeléséhez más Felhőbeli és helyszíni erőforrásokon kívül.

Először olvassa el az [Azure-erőforrások monitorozása Azure monitorokkal](../../../azure-monitor/essentials/monitor-azure-resource.md)című cikket, amely a következő fogalmakat ismerteti:

- Mi az Azure Monitor?
- A figyeléshez kapcsolódó költségek
- Az Azure-ban összegyűjtött adatok figyelése
- Adatgyűjtés konfigurálása
- Szabványos eszközök az Azure-ban a figyelési adatok elemzéséhez és riasztásához

## <a name="monitoring-data"></a>Adatok monitorozása

Media Services ugyanolyan típusú figyelési adatokat gyűjt, mint az [Azure-erőforrások monitorozásával](../../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)kapcsolatos további Azure-erőforrások.

Az Azure Monitor által összegyűjtött adatok két alapvető típusba sorolhatók: metrikák és naplók. A következő két típussal rendelkezhet:

- A metrikák adatait a metrikák Explorerrel ábrázolhatja és elemezheti.
- Figyelje Media Services diagnosztikai naplókat, és hozzon létre riasztásokat és értesítéseket.
- A naplók segítségével a következőket teheti:
  - Küldés az Azure Storage-ba
  - Streamek továbbítása az Azure Event Hubsba
  - Exportálás a Log Analyticsba
  - Harmadik féltől származó szolgáltatások használata

A Media Services által létrehozott mérőszámokra és naplókra vonatkozó részletes információkért tekintse meg a [Media Services adatok figyelése](monitor-media-services-data-reference.md) című cikket.

## <a name="collection-and-routing"></a>Gyűjtés és Útválasztás

A *platform metrikáit* és a *tevékenység naplóját* a rendszer automatikusan összegyűjti és tárolja, de a diagnosztikai beállítások segítségével más helyekre is átirányíthatja őket.  

Az *erőforrás-naplók* Gyűjtése és tárolása addig nem történik meg, amíg létre **nem** hozza a diagnosztikai beállításokat, és egy vagy több helyre irányítja őket.

A diagnosztikai beállítások létrehozásához a Azure Portal, a CLI vagy a PowerShell használatával olvassa el a [diagnosztikai beállítás létrehozása a platform-naplók és-mérőszámok létrehozásához az Azure-ban](../../../azure-monitor/essentials/diagnostic-settings.md) című cikket.

Diagnosztikai beállítás létrehozásakor meg kell adnia, hogy a rendszer milyen típusú naplókat gyűjtsön. A Media Services kategóriái [Media Services figyelési adatreferenciában](monitor-media-services-data-reference.md)vannak felsorolva.

## <a name="analyzing-metrics"></a>Mérőszámok elemzése

A metrikákat a **Azure monitor** menüből **megnyitva a metrikák** segítségével elemezheti az Media Services metrikáit más Azure-szolgáltatásoktól származó metrikákkal. Az eszköz használatával kapcsolatos részletekért lásd: az [Azure Metrikaböngésző használatának első lépései](../../../azure-monitor/essentials/metrics-getting-started.md) .

A Media Services gyűjtött metrikák listáját lásd: [Media Services adathivatkozás figyelése](monitor-media-services-data-reference.md).

## <a name="analyzing-logs"></a>Naplók elemzése

Azure Monitor naplókban lévő, az egyes táblákban található, egyedi tulajdonságokkal rendelkező táblázatokban tárolt adathalmazok.  

Azure Monitor összes erőforrás-naplója ugyanazokkal a mezőkkel rendelkezik, amelyeket a szolgáltatás-specifikus mezők követnek. Az általános séma [Azure monitor erőforrás-naplózási sémában](../../../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)szerepel.

A Media Services erőforrás-naplók sémája a [figyelés Media Services Adathivatkozásban](monitor-media-services-data-reference.md)található.

A [műveletnapló](../../../azure-monitor/essentials/activity-log.md) egy Azure-beli platform-napló, amely betekintést nyújt az előfizetési szintű eseményekre. Megtekintheti egymástól függetlenül, vagy átirányíthatja Azure Monitor naplókba, ahol a Log Analytics használatával jóval összetettebb lekérdezéseket végezhet.

A Media Serviceshoz gyűjtött erőforrás-naplók típusai listáját az [adatMedia Services monitorozása](monitor-media-services-data-reference.md)című témakörben találhatja meg.

### <a name="why-would-i-want-to-use-diagnostic-logs"></a>Miért érdemes diagnosztikai naplókat használni?

Néhány dolog, amit megvizsgálhat a diagnosztikai naplók használatával:

- A DRM-típus által szállított licencek száma.
- A szabályzat által leszállított licencek száma.
- Hibák DRM-vagy házirend-típus szerint.
- Az ügyfelektől érkező jogosulatlan licencelési kérelmek száma.

## <a name="alerts"></a>Riasztások

Azure Monitor riasztások proaktívan értesítik Önt, ha fontos feltételek találhatók a megfigyelési adataiban. Lehetővé teszik a rendszeren észlelt problémák azonosítását és megoldását, mielőtt az ügyfelek bejelentkeznek. Riasztásokat állíthat be a [metrikák](../../../azure-monitor/alerts/alerts-metric-overview.md), [naplók](../../../azure-monitor/alerts/alerts-unified-log.md)és a [tevékenység naplójában](../../../azure-monitor/alerts/activity-log-alerts.md).

Media Services a metrikákat rendszeres időközönként gyűjti, függetlenül attól, hogy az érték módosul-e. Ezek a riasztások akkor hasznosak, ha gyakran mintákat vesznek fel. A riasztások gyorsan és viszonylag egyszerű logikával is elindíthatók.

<!--
The following table lists common and recommended alert rules for Media Services.

<!-- Fill in the table with metric and log alerts that would be valuable for your service. Change the format as necessary to make it more readable
**PLACEHOLDER** table

| Alert type | Condition | Description  |
|:---|:---|:---|
| | | |
| | | |
-->

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [monitoring-next-steps](../includes/monitoring-next-steps.md)]

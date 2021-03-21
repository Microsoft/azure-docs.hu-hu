---
title: Azure Relay metrikák a Azure Monitorban | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használható a Azure Monitor az Azure Relay állapotának figyelésére.
services: service-bus-relay
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 7f91ac0aec46d0fc24901585156dceabb18640c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100586834"
---
# <a name="azure-relay-metrics-in-azure-monitor"></a>Azure Monitor Azure Relay metrikák 
Azure Relay mérőszámok az Azure-előfizetésében lévő erőforrások állapotát adják meg. A metrikai adatok gazdag készletével felmérhető a továbbítási erőforrások általános állapota, nem csak a névtér szintjén, hanem az entitás szintjén is. Ezek a statisztikák fontosak lehetnek, mivel segítenek a Azure Relay állapotának figyelésében. A metrikák az Azure-támogatáshoz való kapcsolódás nélkül is segíthetnek a hibák elhárításában.

A Azure Monitor egységes felhasználói felületet biztosít a különböző Azure-szolgáltatások figyelésére. További információkért lásd: [Microsoft Azure figyelése](../azure-monitor/overview.md) és a [Azure monitor metrikáinak beolvasása](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) a githubon .net-minta használatával.

> [!IMPORTANT]
> Ez a cikk csak a Azure Relay Hibrid kapcsolatok szolgáltatására vonatkozik, nem a WCF Relay. 

## <a name="access-metrics"></a>Hozzáférési metrikák

Azure Monitor több módszert biztosít a metrikák eléréséhez. A mérőszámokat a [Azure Portalon](https://portal.azure.com)keresztül érheti el, vagy használhatja a Azure monitor API-kat (REST és .net) és az elemzési megoldásokat, például az Operation Management Suite-t és a Event Hubst. További információ: [Azure monitor által összegyűjtött adatok figyelése](../azure-monitor/data-platform.md).

A metrikák alapértelmezés szerint engedélyezve vannak, és a legutóbbi 30 nap adatait is elérheti. Ha hosszabb ideig kell megőriznie az adatokat, archiválhatja a metrikák adatait egy Azure Storage-fiókba. Ez a Azure Monitor [diagnosztikai beállításaiban](../azure-monitor/essentials/diagnostic-settings.md) van konfigurálva.

## <a name="access-metrics-in-the-portal"></a>Hozzáférési metrikák a portálon

A metrikák a [Azure Portalban](https://portal.azure.com)is megfigyelhetők. Az alábbi példa bemutatja, hogyan tekintheti meg a sikeres kérelmeket és a bejövő kérelmeket a fiók szintjén:

![A "figyelő-metrikák (előzetes verzió)" címet viselő oldal megjeleníti az elmúlt 30 napban használt memóriahasználat grafikonját.][1]

A metrikákat közvetlenül a névtér használatával is elérheti. Ehhez válassza ki a névteret, majd kattintson a **metrikák** elemre. 

A dimenziókat támogató metrikák esetében a kívánt dimenzió értékkel kell szűrnie.

## <a name="billing"></a>Számlázás

A Azure Monitor metrikáinak használata jelenleg előzetes verzióban ingyenes. Ha azonban olyan további megoldásokat használ, amelyek metrikai adatokat töltenek fel, akkor ezeket a megoldásokat számlázhatja. Ha például az Azure Storage-ba számít, ha egy Azure Storage-fiókba archiválja a metrikákat. A Azure Monitor naplók számlázása akkor is történik, ha a metrikák adatait a speciális elemzéshez Azure Monitor naplókba.

A következő mérőszámok áttekintést nyújtanak a szolgáltatás állapotáról. 

> [!NOTE]
> Számos mérőszámot elavultunk, mert más néven vannak áthelyezve. Előfordulhat, hogy frissítenie kell a hivatkozásokat. Az "elavult" kulcsszóval jelölt mérőszámok nem lesznek támogatottak.

Minden metrikai érték Azure Monitor percenként lesz elküldve. Az idő részletessége határozza meg azt az időintervallumot, ameddig a metrikák értékei bemutatva lesznek. Az összes Azure Relay-metrika támogatott időintervalluma 1 perc.

## <a name="connection-metrics"></a>Kapcsolatok metrikái

| Metrika neve | Description |
| ------------------- | ----------------- |
| ListenerConnections-Success  | Egy adott időszakban Azure Relay sikeres figyelő-kapcsolatok száma. <br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|
|ListenerConnections-ClientError |A figyelő kapcsolatainak száma a megadott időszakban.<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|
|ListenerConnections-ServerError |A figyelő kapcsolatainak száma a megadott időszakban.<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|
|SenderConnections-Success |A megadott időszakban sikeres küldő kapcsolatok száma.<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|
|SenderConnections-ClientError |Az ügyfél hibáinak száma a küldő kapcsolatain a megadott időszakban.<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|
|SenderConnections-ServerError |A megadott időszakon belül a küldő kapcsolaton futó kiszolgálói hibák száma.<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|
|ListenerConnections-TotalRequests |A figyelő kapcsolatainak teljes száma egy adott időszakban.<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|
|SenderConnections-TotalRequests |A küldők által megadott időszakon keresztül kezdeményezett kapcsolati kérelmek.<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|
|Aktív kapcsolatok |Az aktív kapcsolatok száma. Ez az érték egy időponthoz tartozó érték.<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|
|ActiveListeners |Az aktív figyelők száma. Ez az érték egy időponthoz tartozó érték.<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|
|Bontásai |A leválasztott figyelők száma egy adott időszakban.<br/><br/> Egység: bájtok <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|
|SenderDisconnects |A leválasztott küldők száma egy adott időszakban.<br/><br/> Egység: bájtok <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|

## <a name="memory-usage-metrics"></a>Memóriahasználat mérőszámai

| Metrika neve | Description |
| ------------------- | ----------------- |
|BytesTransferred |A megadott időszakon át továbbított bájtok száma.<br/><br/> Egység: bájtok <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|

## <a name="metrics-dimensions"></a>Metrikák méretei

A Azure Relay a Azure Monitor metrikáinak következő dimenzióit támogatja. Nem kötelező dimenziókat hozzáadni a metrikához. Ha nem ad hozzá dimenziókat, a metrikák a névtér szintjén vannak megadva. 

|Dimenzió neve|Description|
| ------------------- | ----------------- |
|EntityName| Azure Relay támogatja az üzenetküldési entitásokat a névtérben.|

## <a name="next-steps"></a>Következő lépések

Lásd az [Azure monitoring áttekintése](../azure-monitor/overview.md)című témakört.

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png

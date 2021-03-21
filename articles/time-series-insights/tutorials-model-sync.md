---
title: Modell-szinkronizálás az Azure Digital Twins és a Time Series Insights között | Microsoft Docs
description: Ajánlott eljárások és eszközök az ADT az Azure-beli erőforrás-modellben való fordításához
ms.service: time-series-insights
services: time-series-insights
author: msrsrinivasan
ms.author: radhsrin
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/19/2021
ms.custom: dpalled
ms.openlocfilehash: 5e8e5367daa532fa0cc36a18cd28b382e7cd8f78
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98682902"
---
# <a name="model-synchronization-between-azure-digital-twins-and-time-series-insights-gen2"></a>Modellszinkronizálás az Azure Digital Twins és a Time Series Insights Gen2 között

Ez a cikk azokat az ajánlott eljárásokat és eszközöket ismerteti, amelyekkel az eszköz modelljét az Azure Digital Twins (ADT) modellben fordíthatja át Azure Time Series Insights (ÁME).  Ez a cikk egy kétrészes oktatóanyag-sorozat második része, amely az Azure Digital Twins és a Azure Time Series Insights integrációját ismerteti. Az Azure Digital Twins és a Time Series Insights integrációja lehetővé teszi az archiválást és a digitális ikrek telemetriáiról és számított tulajdonságainak nyomon követését. Ez az oktatóanyag-Sorozat olyan fejlesztők számára készült, akik a Time Series Insights Azure digitális Twins-nal való integrálására törekednek. Az 1. rész ismerteti  [az adatfolyamatok létrehozását, amely az Azure Digital Twins-ból származó tényleges idősorozat-adatokat hozza Time Series Insightsba](../digital-twins/how-to-integrate-time-series-insights.md) , és ez az oktatóanyag-sorozat második része az Azure Digital Twins és a Time Series Insights közötti adatmodell-szinkronizálást ismerteti. Ez az oktatóanyag ismerteti az idősorozat-azonosító (TS ID) elnevezési konvenció kiválasztásának és létrehozásának ajánlott eljárásait, valamint a hierarchiák manuális létrehozását a Time Series-modellben (TSM).

## <a name="choosing-a-time-series-id"></a>Idősorozat-azonosító kiválasztása

Az idősorozat-azonosító egy egyedi azonosító, amely a Time Series Insightsban lévő eszközök azonosítására szolgál. Az idősorozat-adatok (az időérték párokat tartalmazó mezőből származó telemetriáiról) a TSID szakaszban felsorolt változók alapján jelennek meg. Az Azure Digital Twins-ben a Twin tulajdonságok és a telemetriáiról a Twin és a Twin által előállított mérések állapotának ábrázolására szolgálnak. A TSM aktuális kialakításának megfelelően a TSIDs egyedinek kell lennie. Az ikrek az Azure Digital Twins-ben vagy a tulajdonság-vagy telemetria-névvel kombinálva a Twin-azonosítók használatával mindig egyedi TS-azonosítót fog használni a TSM-ben. Egy tipikus esetben a a **_`<Twin ID>`_** TSID lesz, a tulajdonság-és telemetria pedig a TSM változói lesznek. Vannak azonban olyan használati esetek, amelyekben előnyben részesítettük, hogy az adategység-hierarchiák a Time Series Insights összetett kulcsok formátumával legyenek lelapulva, például: **_`<Twin ID>+ <Delimiter of Choice> + <Name of the Property or Telemetry>`_** . Vegyük például a későbbi esetet. Vegye fontolóra a Twin-ként és a Twin ID-Room22 rendelkező épületben található szobát. A hőmérséklet-beállítási tulajdonságot **_TSID-Room22_TempSetting_** és hőmérséklet-mérésként kell lefordítani a TSM-ben **_Room22_TempMeare_** .

[![Idősorozat-azonosító kiválasztása](media/tutorials-model-sync/choosing-tsid.png)](media/tutorials-model-sync/choosing-tsid.png#lightbox)

## <a name="contextualizing-time-series"></a>Contextualizing idő sorozata

A Time Series Insightsben lévő Contextualization (főként a természet térbeli jellege) az adategység-hierarchiák révén valósul meg, és a Time Series Insights Explorer fanézetében az adatnavigációhoz is ugyanezt az értéket használja. Az idősorozat-típusok és a hierarchiák definiálása a Time Series Model (TSM) használatával történik Time Series Insightsban. A TSM súgójában a változók definiálása, míg a hierarchia szintjei és a példány mezői a fanézet létrehozásához használatosak a Time Series Insights Explorerben. A TSM kapcsolatos további információkért tekintse meg az [online Time Series Insights dokumentációját](./concepts-model-overview.md).

Az Azure Digital Twins szolgáltatásban az eszközök közötti kapcsolat kettős kapcsolatokkal van kifejezve. A kettős kapcsolatok egyszerűen a csatlakoztatott eszközök gráfja. Az idősorozatok betekintése azonban az adategységek közötti kapcsolatok hierarchikus jellegűek. Ez azt jelzi, hogy az eszközök osztoznak egy szülő-gyermek típusú od-kapcsolaton, és fastruktúrát használnak. Az Azure Digital Twins kapcsolati információinak Time Series Insights hierarchiába való lefordításához ki kell választania az Azure Digital Twins megfelelő hierarchikus kapcsolatait. Az Azure Digital Twins egy nyílt szabványú, Digital Twin Definition Language (DTDL) nevű modellezési nyelvet használ. A DTDL-modellekben JSON-LD néven a JSON egyik változatát használjuk. A specifikációval kapcsolatos részletes információkért tekintse meg a [DTDL dokumentációját](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) .

[![Eszközök közötti kapcsolat](media/tutorials-model-sync/asset-connection.png)](media/tutorials-model-sync/asset-connection.png#lightbox)

## <a name="translating-graph-representation-in-azure-digital-twins-to-tree-structure-in-time-series-insights"></a>Diagram ábrázolásának lefordítása az Azure Digital Ikrekben a fastruktúrában Time Series Insights

Az oktatóanyag következő fejezetei néhány alapvető forgatókönyvet mutatnak be, amelyekkel manuálisan lefordíthatja a Graph-struktúrát az Azure Digital Twins-ben a Time Series Insights fastruktúrájában.

Mintarendszer: ez az oktatóanyag a következő példa használatával ismerteti az alább tárgyalt fogalmakat. Ez egy egyszerű, kitalált épületfelügyeleti rendszer egy emeleten és két szobával. Három termosztáttal rendelkezik, egyet az egyes szobákban, és egy másikat a padlóhoz. Emellett egy vízforgalmi mérő is van, amely a Room21 és a Room22 közötti vizet méri. Az ikrek közötti térbeli kapcsolattal kapcsolatban mindkét típusú kapcsolat létezik.

1. Leggyakoribb, hierarchikus kapcsolat. Például: Building40-> Floor01-> FloorTS *-> hőmérséklet
1. Nem annyira gyakori, körkörös kapcsolat. Például a Building40-től kezdődően a FlowMtr két különböző útvonalon lehet nyomon követni.

   1. Building40-> Floor01-> Room21-> FlowMtr *-> folyamat
   1. Building40-> Floor01-> Room22-> FlowMtr *-> folyamat  
      Ahol a "flow" az a tényleges telemetria, amely a Room21 és a Room22 közötti víz áramlását méri

> [!Note]
>
> `*` A FloorTS a FloorThermoStat és a FlowMtr áll, és általában a TSID-ként van kiválasztva. A hőmérséklet és a folyamat a Time Series Insights változóként említett nyers telemetria.

A Time Series Insights jelenlegi korlátozását tekintve, hogy az egyik eszköz nem szerepelhet több ág esetében, az alábbi szakasz ismerteti a hierarchikus és körkörös kapcsolatok modellezését Time Series Insightsokban.

## <a name="case-1-hierarchical-parent-child-relationship"></a>1. eset: hierarchikus (szülő-gyermek) kapcsolat

Ez a leggyakoribb kapcsolattípus az ikrek között. a tiszta szülő-gyermek kapcsolat modellezését az alábbi ábra ismerteti. A példány mezői és a TSID a lent látható Twin-azonosítóból származnak. Míg a példányok mezői manuálisan frissíthetők Time Series Insights Explorer használatával, az alábbi szakasz "az API-k használata az API-kkal" című szakaszban a modell változásainak az Azure Digital Twins-ban való megtervezése és az Azure functions Time Series Insights-beli példány mezőinek frissítése című részben olvashat.

[![Dupla azonosítók leképezése](media/tutorials-model-sync/mapping-twin-ids.png)](media/tutorials-model-sync/mapping-twin-ids.png#lightbox)

[![Dupla azonosítók leképezése 2](media/tutorials-model-sync/mapping-twin-ids2.png)](media/tutorials-model-sync/mapping-twin-ids2.png#lightbox)

## <a name="case-2-circular-relationship"></a>2. eset: körkörös kapcsolat

### <a name="circular-relationship-in-azure-digital-twins-to-single-hierarchy-relationship-in-time-series-insights"></a>Körkörös kapcsolat az Azure Digital Twins-ben az önálló hierarchia kapcsolata Time Series Insights

Mivel a TSID-nek egyedinek kell lennie, és csak egy hierarchiában szerepelhet, ebben az esetben a " _FlowMtr"_ tulajdonságot a " _flow"_ nevű telemetria jelöli, közvetlenül a Twin _"Room21"_ alatt. A jövőben, amikor a Time Series Insights képes támogatni az idősorozatok többszörös megjelenítését a TSM-ben, a " _flow"_ telemetria a _"Room 21"_ és a _"Room 22"_ szövegben jelennek meg.

Az alábbi képernyőképen az Azure Digital Twins-beli Twin-azonosítók manuális leképezése a TSM-ben és az eredményül kapott hierarchia Time Series Insightsban című ábrán látható.

[![Twin-azonosítók leképezése az Azure Digital Ikrekben](media/tutorials-model-sync/mapping-twin-ids-adt.png)](media/tutorials-model-sync/mapping-twin-ids-adt.png#lightbox)

### <a name="circular-relationship-in-azure-digital-twins-to-multiple-hierarchies-in-time-series-insights-using-duplicates"></a>Körkörös kapcsolat az Azure Digital Twins-ben több hierarchiában Time Series Insights, duplikált elemek használatával

Az oktatóanyag 1. része azt mutatja be, hogy egy ügyfélalkalmazás (Azure-függvény) hogyan segíti a telemetria-adatok átvitelét IoT Hub vagy más esemény-forrásokból az Azure digitális Twins-ba. Ez a megközelítés azt javasolja, hogy ugyanazt az ügyfélprogramot használja a szülő ikrek releváns tulajdonságainak frissítéséhez. Az adott példában a FlowMtr telemetria olvasása a IoT Hubról és a "flow" tulajdonság frissítése a FlowMtr Twin-ben a program a forrás összes lehetséges szülő-ikrekben is frissítheti a megfelelő tulajdonságokat. A példánkban az "outflowmea" (a "kiáramlás" kapcsolat használatával azonosítható) a Room22 "inflowmea" tulajdonságának "Room21" tulajdonsága.  Az alábbi képernyőfelvételen a Time Series Insights Explorer végső felhasználói felülete látható. Meg kell jegyezni, hogy ezt a módszert követve az adatismétlődések is megtalálhatók.

[![Time Series Insights Explorer](media/tutorials-model-sync/tsi-explorer.png)](media/tutorials-model-sync/tsi-explorer.png#lightbox)

Az alábbi kódrészlet azt mutatja be, hogy az ügyfélalkalmazás hogyan tudott az Azure Digital Twins API-k használatával navigálni a kettős kapcsolaton.

> [!Note]
>
> A kódrészlet példája feltételezi, hogy az olvasók ismerik az oktatóanyag [1. részét](../digital-twins/tutorial-end-to-end.md#set-up-the-sample-function-app) , és ez a kód változása a "ProcessHubToDTEvents" függvényen belül történt.

```csharp
if (propertyPath.Equals("/Flow"))
{
//Update the flow value property of the flow meter
await AdtUtilities.UpdateTwinProperty(client, twinId, "replace",
propertyPath, "double", propertyValue, log);

//also update the sending end flow
string parentIdOutflow = await AdtUtilities.FindParent(client, twinId,
"outflow", log);
if (parentIdOutflow != null)
await AdtUtilities.UpdateTwinProperty(client, parentIdOutflow, "replace", "outflow", "double", propertyValue, log);
else
log.LogInformation("Unable to find Parent with outflow
relationship for " + twinId );
//and receiving end flow value
string parentIdinflow = await AdtUtilities.FindParent(client, twinId,
"inflow", log);
if (parentIdinflow != null)

await AdtUtilities.UpdateTwinProperty(client, parentIdinflow,
"replace", "inflow", "double", propertyValue, log);
else
log.LogInformation("Unable to find Parent with inflow
relationship for " + twinId);
}
```

## <a name="updating-instance-fields-using-apis"></a>A példány mezőinek frissítése API-kkal

Az oktatóanyag ezen szakasza azt mutatja be, hogyan lehet megfigyelni a modell változásait az Azure-beli digitális Ikrekben, például az ikrek létrehozását, törlését, illetve az ikrek közötti kapcsolatok változását, valamint a példányok és a hierarchiák a Time Series Insights Model API-k használatával Az Time Series Insights modell frissítésének ezt a módszerét általában az Azure functions szolgáltatáson keresztül érheti el. Az Azure digitális Ikrekben az olyan eseményekről szóló értesítések, mint például a Twin-Hozzáadás vagy a törlések átirányíthatók az alárendelt szolgáltatások, például a Event Hubs, amelyek az Azure functions szolgáltatásba is bekapcsolhatók. Az események útválasztásával és szűrésével kapcsolatos további részleteket [itt talál](../digital-twins/how-to-manage-routes-portal.md).  A szakasz hátralévő részében a Time Series Insights Model API-k használata az Azure functions szolgáltatásban című cikkből megtudhatja, hogyan frissítheti Time Series Insights modellt a Twin (modell módosítása) válaszként az Azure digitális Twins szolgáltatásban.

### <a name="receiving-and-identifying-twin-addition-event-notification"></a>A Twin kiegészítő esemény értesítésének fogadása és azonosítása

```csharp
[FunctionName("RouteEventsToTsi")]
public async Task Run([EventGridTrigger]EventGridEvent eventGridEvent)
{
    try
    {
        if (eventGridEvent != null && eventGridEvent.Data != null)
        {
            logger.LogInformation($"EventType: {eventGridEvent.EventType}");
            logger.LogInformation($"EventGridEvent: {JsonConvert.SerializeObject(eventGridEvent)}");

            //Shape event and Send event data to event hub and tsi
            await SendEventToEventHubAsync(eventGridEvent).ConfigureAwait(false);

            //If a new twin was created, update the newly created instance in TSI with info retrieved from ADT
            if (eventGridEvent.EventType == Constants.TwinCreateEventType)
            {
                //retrieve building, floor and room of value twin
                var twinInfo = await RetrieveTwinInfoAsync(eventGridEvent).ConfigureAwait(false);
                //Update Tsi instance with type(sensor type), hierarchy(space hierarchy) and instance fields(twin info retrieved above)
                var instance = await CreateInstanceToSendAsync(twinInfo).ConfigureAwait(false);
                var instanceToUpdate = new List<TimeSeriesInstance>() { instance };
                var response = await tsiClient.TimeSeriesInstances.ExecuteBatchAsync(new InstancesBatchRequest(update: instanceToUpdate)).ConfigureAwait(false);
            }
        }
    }
    catch (Exception ex)
    {
        logger.LogError($"Exception: {ex.Message}");
    }
}
```

### <a name="creating-time-series-insights-client-and-adding-instance-details"></a>Time Series Insights-ügyfél létrehozása és a példány adatainak hozzáadása

```csharp
private async Task<TimeSeriesInstance> CreateInstanceToSendAsync(Dictionary<string, string> twinInfo)
{
    try
    {
        tsiClient = await GetTSIClientAsync().ConfigureAwait(false);

        var timeSeriesId = new object[] { twinInfo[Constants.DtId] };
        var instances = await tsiClient.TimeSeriesInstances.ExecuteBatchAsync(
            new InstancesBatchRequest(
                get: new InstancesRequestBatchGetOrDelete(
                    new IList<object>[] { timeSeriesId }))).ConfigureAwait(false);
        var instance = instances.Get.First().Instance;

        if (instance != null)
        {
            instance = await AddHierarchyToInstanceAsync(instance).ConfigureAwait(false);
            instance = await AddTypeToInstanceAsync(instance, twinInfo[Constants.TwinType]).ConfigureAwait(false);

            instance.InstanceFields = new Dictionary<string, object>
                            {
                                { "Building", twinInfo[Constants.BuildingName] },
                                { "Floor", twinInfo[Constants.FloorName] },
                                { "Room", twinInfo[Constants.ParentName] }
                            };

            //If value twin is a sensor value twin, add sensor type instance field to diff from spatial value twin
            if (twinInfo[Constants.ParentType] == Constants.Sensor)
            {
                instance.InstanceFields.Add(Constants.SensorType, twinInfo[Constants.TwinType]);
            }
            return instance;
        }
        else
        {
            logger.LogError($"instance with id {twinInfo[Constants.DtId]} not found");
            return new TimeSeriesInstance();
        }
    }
    catch (Exception e)
    {
        logger.LogError(e.Message);
        throw;
    }
}
```

### <a name="applying-hierarchy-info-to-instance"></a>Hierarchia adatainak alkalmazása példányra

```csharp
private async Task<TimeSeriesInstance> AddHierarchyToInstanceAsync(TimeSeriesInstance instance)
{
    if (instance.HierarchyIds == null)
    {
        TimeSeriesHierarchy spacesHierarchy = null;
        try
        {
            var hierarchy = await RunGetHierarchiesAsync(Constants.SpaceHierarchy).ConfigureAwait(false);
            spacesHierarchy = hierarchy.First(h => h.Name.Equals(Constants.SpaceHierarchy));
            instance.HierarchyIds = new List<Guid?>();
            instance.HierarchyIds.Add(spacesHierarchy.Id);
        }
        catch (Exception ex)
        {
            logger.LogWarning($"Hierarchy 'space hierarchy' not found, {ex}");
            throw;
        }
    }
    return instance;
}
```

## <a name="next-steps"></a>Következő lépések

A harmadik az oktatóanyagok sorozatában azt mutatja be, hogyan lehet lekérdezni az Azure digitális Twins korábbi adatait Time Series Insights API-k használatával. Ez a folyamat folyamatban van, és készen áll a szakasz frissítésére. Addig is javasoljuk, hogy olvassa el az olvasókat a [Time Series Insights Adatlekérdezési API dokumentációjában](./concepts-query-overview.md).

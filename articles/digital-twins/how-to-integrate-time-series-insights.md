---
title: Integrálás az Azure Time Series Insights szolgáltatással
titleSuffix: Azure Digital Twins
description: Ismerje meg, hogyan állíthatja be az esemény-útvonalakat az Azure Digital Ikrekből a Azure Time Series Insightsba.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 1/19/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 951c52cdba191aa291061259e1c15b9190513770
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99092720"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>Az Azure Digital Twins integrálása Azure Time Series Insights

Ebből a cikkből megtudhatja, hogyan integrálhatja az Azure Digital Twins-t [Azure Time Series Insights (ÁME)](../time-series-insights/overview-what-is-tsi.md)használatával.

A cikkben ismertetett megoldás lehetővé teszi a IoT-megoldással kapcsolatos korábbi adatok összegyűjtését és elemzését. Az Azure Digital Twins remek megoldás a Time Series Insightsba való adatbetöltéshez, mivel segítségével több adatfolyamot is összefűzhet, és szabványosíthatja az adatokat a Time Series Insightsnak való küldés előtt. 

## <a name="prerequisites"></a>Előfeltételek

Mielőtt kapcsolatot hozna létre Time Series Insightsval, rendelkeznie kell egy **Azure digitális Twins-példánnyal**. Ezt a példányt úgy kell beállítani, hogy az adatok alapján frissítse a digitális Twin-adatokat, mivel a két adatot frissíteni kell, hogy az adatok nyomon kövessék a Time Series Insights. 

Ha még nem rendelkezik ezzel a beállítással, létrehozhatja azt az Azure Digital Twins [*oktatóanyagának használatával: Kapcsolódás végpontok közötti megoldáshoz*](./tutorial-end-to-end.md). Az oktatóanyag végigvezeti egy olyan Azure Digital Twins-példány beállításán, amely egy virtuális IoT-eszközzel működik a digitális dupla frissítések elindításához.

## <a name="solution-architecture"></a>Megoldásarchitektúra

Az alábbi elérési úton az Azure digitális Twins-hoz Time Series Insights fog kapcsolódni.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Az Azure-szolgáltatások egy végpontok közötti forgatókönyvben, kiemelve Time Series Insights" lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Útvonal létrehozása és szűrés az ikerpéldány frissítési értesítéseire

Az Azure Digital Twins-példányok [dupla frissítési eseményeket](how-to-interpret-event-data.md) bocsátanak ki, amikor a Twin állapot frissül. Ebben a szakaszban egy Azure digitális Twins- [**esemény útvonalát**](concepts-route-events.md) fogja létrehozni, amely további feldolgozás céljából irányítja ezeket a frissítési eseményeket az Azure [Event Hubsba](../event-hubs/event-hubs-about.md) .

Az Azure Digital Twins [*oktatóanyaga: egy végpontok közötti megoldás összekapcsolása*](./tutorial-end-to-end.md) egy olyan forgatókönyv segítségével, amelyben egy hőmérőt használ egy hőmérséklet-attribútum frissítésére egy olyan digitális ikeren, amely egy helyet jelöl. Ez a minta a Twin-frissítésekre támaszkodik, és nem továbbítja a telemetria egy IoT-eszközről, így rugalmasan módosíthatja az alapul szolgáló adatforrást anélkül, hogy frissítenie kellene a Time Series Insights logikát.

1. Először hozzon létre egy Event hub-névteret, amely az Azure Digital Twins-példányról származó eseményeket fogja kapni. Használhatja az alábbi Azure CLI-utasításokat, vagy használhatja a Azure Portal: rövid útmutató [*: Event hub létrehozása Azure Portal használatával*](../event-hubs/event-hubs-create.md). Ha szeretné megtekinteni, hogy mely régiók támogatják a Event Hubs, látogasson el az [*Azure-termékek területére*](https://azure.microsoft.com/global-infrastructure/services/?products=event-hubs).

    ```azurecli-interactive
    az eventhubs namespace create --name <name for your Event Hubs namespace> --resource-group <resource group name> -l <region>
    ```

2. Hozzon létre egy Event hub-t a névtéren belül a kettős módosítási események fogadásához. Adja meg az Event hub nevét.

    ```azurecli-interactive
    az eventhubs eventhub create --name <name for your Twins event hub> --resource-group <resource group name> --namespace-name <Event Hubs namespace from above>
    ```

3. Hozzon létre egy [engedélyezési szabályt](/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest&preserve-view=true#az-eventhubs-eventhub-authorization-rule-create) a küldési és fogadási engedélyekkel. Adja meg a szabály nevét.

    ```azurecli-interactive
        az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from above> --eventhub-name <Twins event hub name from above> --name <name for your Twins auth rule>
    ```

4. Hozzon létre egy Azure digitális Twins- [végpontot](concepts-route-events.md#create-an-endpoint) , amely az Event hub-t az Azure Digital Twins-példánnyal csatolja.

    ```azurecli-interactive
    az dt endpoint create eventhub -n <your Azure Digital Twins instance name> --endpoint-name <name for your Event Hubs endpoint> --eventhub-resource-group <resource group name> --eventhub-namespace <Event Hubs namespace from above> --eventhub <Twins event hub name from above> --eventhub-policy <Twins auth rule from above>
    ```

5. Hozzon létre egy [útvonalat](concepts-route-events.md#create-an-event-route) az Azure Digital Twinsben, amelyen elküldheti az ikerpéldányok frissítési eseményeit a végpontnak. Az ebben az útvonalban lévő szűrő csak a kettős frissítési üzeneteket továbbítja a végpontnak.

    >[!NOTE]
    >A Cloud Shellben jelenleg egy **ismert probléma** akadályozza az alábbi parancscsoportokat: `az dt route`, `az dt model`, `az dt twin`.
    >
    >Ennek feloldásához futtassa az `az login` parancsot a parancs előtt a Cloud Shellben, vagy használja a [helyi CLI-t](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) a Cloud Shell helyett. Erről további részleteket a [*Hibaelhárítás: az Azure digitális Twins ismert problémái*](troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell)című témakörben talál.

    ```azurecli-interactive
    az dt route create -n <your Azure Digital Twins instance name> --endpoint-name <Event Hub endpoint from above> --route-name <name for your route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

A továbblépés előtt jegyezze fel a *Event Hubs névteret* és az *erőforráscsoportot*, ahogy azt újra használni fogja a cikk későbbi részében újabb Event hub létrehozásához.

## <a name="create-a-function-in-azure"></a>Függvény létrehozása az Azure-ban

Ezután a Azure Functions használatával hozzon létre egy **Event Hubs által aktivált függvényt** egy Function alkalmazásban. Használhatja a teljes körű oktatóanyagban létrehozott Function alkalmazást ([*oktatóanyag: végpontok közötti megoldás összekapcsolását*](./tutorial-end-to-end.md)) vagy a sajátját. 

Ez a függvény átalakítja ezeket a kettős frissítési eseményeket az eredeti űrlapról JSON-javításként szolgáló dokumentumként a JSON-objektumokba, amelyek csak a frissített és hozzáadott értékeket tartalmazzák az ikrektől.

A Event Hubs és a Azure Functions használatával kapcsolatos további információkért lásd: [*Azure Event Hubs trigger Azure Functionshoz*](../azure-functions/functions-bindings-event-hubs-trigger.md).

A közzétett Function alkalmazásban vegyen fel egy **ProcessDTUpdatetoTSI** nevű új függvényt a következő kóddal.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateTSI.cs":::

>[!NOTE]
>Előfordulhat, hogy hozzá kell adnia a csomagokat a projekthez a `dotnet add package` parancs vagy a Visual Studio NuGet csomagkezelő használatával.

Ezután **tegye közzé** az új Azure-függvényt. Ennek módjával kapcsolatos útmutatásért tekintse meg az [*Azure-függvény beállítása az adatfeldolgozáshoz*](how-to-create-azure-function.md#publish-the-function-app-to-azure)című témakört.

Előretekintve a függvény elküldi az általa létrehozott JSON-objektumokat egy második esemény-hubhoz, amelyet Time Series Insightshoz fog csatlakozni. Az Event hub-t a következő szakaszban fogja létrehozni.

Később olyan környezeti változók is megadhatók, amelyeket ez a függvény a saját Event hubokhoz való kapcsolódáshoz használ majd.

## <a name="send-telemetry-to-an-event-hub"></a>Telemetria küldése egy eseményközpontnak

Most hozzon létre egy második Event hub-t, és konfigurálja a függvényt, hogy a kimenetét továbbítsa az Event hub-nak. Az eseményközpontot összekapcsolja a Time Series Insightsszal.

### <a name="create-an-event-hub"></a>Eseményközpont létrehozása

A második Event hub létrehozásához használhatja az alábbi Azure CLI-utasításokat, vagy használhatja a Azure Portal: rövid útmutató [*: Event hub létrehozása Azure Portal használatával*](../event-hubs/event-hubs-create.md).

1. Készítse elő a *Event Hubs névteret* és az *erőforráscsoport* nevét a jelen cikk korábbi részében

2. Hozzon létre egy új Event hub-t. Adja meg az Event hub nevét.

    ```azurecli-interactive
    az eventhubs eventhub create --name <name for your TSI event hub> --resource-group <resource group name from earlier> --namespace-name <Event Hubs namespace from earlier>
    ```
3. Hozzon létre egy [engedélyezési szabályt](/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest&preserve-view=true#az-eventhubs-eventhub-authorization-rule-create) a küldési és fogadási engedélyekkel. Adja meg a szabály nevét.

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from earlier> --eventhub-name <TSI event hub name from above> --name <name for your TSI auth rule>
    ```

## <a name="configure-your-function"></a>A függvény konfigurálása

A következő lépésben környezeti változókat kell beállítania a Function alkalmazásban, amely tartalmazza a létrehozott Event hubok kapcsolati karakterláncait.

### <a name="set-the-twins-event-hub-connection-string"></a>A Twins-eseményközpont kapcsolati sztringjének beállítása

1. Szerezze be az ikrek [Event hub kapcsolati karakterláncát](../event-hubs/event-hubs-get-connection-string.md)a fent létrehozott engedélyezési szabályok alapján az ikrek hubhoz.

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <Twins event hub name from earlier> --name <Twins auth rule from earlier>
    ```

2. Az eredményből származó *primaryConnectionString* érték használatával hozzon létre egy alkalmazás-beállítást a Function alkalmazásban, amely tartalmazza a kapcsolódási karakterláncot:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<Twins event hub connection string>" -g <resource group> -n <your App Service (function app) name>
    ```

### <a name="set-the-time-series-insights-event-hub-connection-string"></a>A Time Series Insights-eseményközpont kapcsolati sztringjének beállítása

1. Szerezze be az ÁME [Event hub kapcsolati karakterláncát](../event-hubs/event-hubs-get-connection-string.md)az Time Series Insights hub felett létrehozott engedélyezési szabályok használatával:

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <TSI event hub name> --name <TSI auth rule>
    ```

2. Az eredményből származó *primaryConnectionString* érték használatával hozzon létre egy alkalmazás-beállítást a Function alkalmazásban, amely tartalmazza a kapcsolódási karakterláncot:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<TSI event hub connection string>" -g <resource group> -n <your App Service (function app) name>
    ```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Time Series Insights-példány létrehozása és összekapcsolása

Ezután állítson be egy Time Series Insights-példányt, amely a második (ÁME) Event hub adatait fogja fogadni. Kövesse az alábbi lépéseket, és a folyamattal kapcsolatos további információkért tekintse [*meg az oktatóanyag: Azure Time Series Insights GEN2 TB-környezet beállítása*](../time-series-insights/tutorials-set-up-tsi-environment.md)című témakört.

1. A Azure Portal a Time Series Insights-környezet létrehozásának megkezdéséhez. 
    1. Válassza ki a **Gen2 (L1)** árképzési szintet.
    2. Ehhez a környezethez ki kell választania egy **idősorozat-azonosítót** . Az idősorozat-azonosító legfeljebb három olyan érték lehet, amelyet a Time Series Insightsban lévő adatok kereséséhez fog használni. Ebben az oktatóanyagban használhatja a **$dtId**. További információ az azonosító érték kiválasztásáról az [*ajánlott eljárásokban az idősorozat-azonosító kiválasztásához*](../time-series-insights/how-to-select-tsid.md).
    
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png" alt-text="A létrehozási portál UX Time Series Insights-környezethez. Válassza ki az előfizetését, az erőforráscsoportot és a helyet a megfelelő legördülő listából, és válassza ki a környezet nevét." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png":::
        
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png" alt-text="A létrehozási portál UX Time Series Insights-környezethez. A Gen2 (L1) árképzési szintje van kiválasztva, az idősorozat-azonosító tulajdonság neve pedig $dtId" lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png":::

2. Válassza a Next (tovább) gombot **: eseményforrás** , és válassza ki az ÁME Event hub-információkat a korábbi verziók közül. Emellett új Event Hubs fogyasztói csoportot is létre kell hoznia.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/event-source-twins.png" alt-text="A létrehozási portál UX Time Series Insights környezeti esemény forrásához. Az Event hub információi alapján hozza létre az eseményforrás adatait. Új fogyasztói csoportot is létrehoz." lightbox="media/how-to-integrate-time-series-insights/event-source-twins.png":::

## <a name="begin-sending-iot-data-to-azure-digital-twins"></a>IoT-adatok küldésének megkezdése az Azure Digital Twinsba

Az adatok Time Series Insightsba való küldésének megkezdéséhez meg kell kezdenie a digitális Twin-tulajdonságok frissítését az Azure Digital Twins-ban az adatértékek módosításával. Használja az az [DT Twin Update](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext-azure-iot-az-dt-twin-update) parancsot.

Ha teljes körű oktatóanyagot használ ([*oktatóanyag: végpontok közötti megoldás összekapcsolásával*](tutorial-end-to-end.md)) a környezet beállításának elősegítése érdekében, megkezdheti a szimulált IoT-adatok küldését a minta *DeviceSimulator* -projekt futtatásával. Az útmutató az oktatóanyag [*konfigurálása és futtatása a szimuláció*](tutorial-end-to-end.md#configure-and-run-the-simulation) szakaszban található.

## <a name="visualize-your-data-in-time-series-insights"></a>Az adatok vizualizálása a Time Series Insightsban

Az adatforgalom az Time Series Insights-példányba kerül, és készen áll az elemzésre. Kövesse az alábbi lépéseket a beérkező adatforgalom megismeréséhez.

1. Nyissa meg Time Series Insights-környezetét a [Azure Portalban](https://portal.azure.com) (a portálon keresse meg a környezet nevét). Látogasson el a példány áttekintésében látható *Time Series Insights Explorer URL-címére* .
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="A Time Series Insights-környezet áttekintés lapján válassza ki a Time Series Insights Explorer URL-címét":::

2. Az Explorerben megjelenik a három ikrek a bal oldalon látható Azure digitális Ikrekből. Válassza a _**thermostat67**_ lehetőséget, válassza a **hőmérséklet** lehetőséget, és kattintson a **Hozzáadás gombra**.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Válassza a * * thermostat67 * * elemet, válassza a * * hőmérséklet * * elemet, és a * * Hozzáadás * *":::

3. Ekkor látnia kell a termosztátból a kezdeti hőmérséklet-leolvasásokat az alább látható módon. Ugyanazt a hőmérséklet-olvasási értéket frissíti a rendszer a *room21* és a *floor1*, és párhuzamosan jelenítheti meg az adatfolyamokat.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="A kezdeti hőmérsékleti adatai az ÁME Explorerben vannak ábrázolva. 68 és 85 közötti véletlenszerű értékek sora":::

4. Ha lehetővé teszi, hogy a szimuláció jóval tovább fusson, a vizualizáció a következőképpen fog kinézni:
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="Az egyes Twin-sorok hőmérsékleti értékeit három, különböző színű párhuzamos vonal ábrázolja.":::

## <a name="next-steps"></a>Következő lépések

A digitális ikreket alapértelmezés szerint a rendszer a Time Series Insightsban lévő, lapos hierarchiában tárolja, de a modell adataival és a szervezet többszintű hierarchiájának használatával gazdagíthatja őket. A folyamattal kapcsolatos további információkért olvassa el a következőt: 

* [*Oktatóanyag: modell meghatározása és alkalmazása*](../time-series-insights/tutorials-set-up-tsi-environment.md#define-and-apply-a-model) 

Egyéni logikát írhat arra, hogy automatikusan megadja ezeket az adatokat az Azure digitális Twins-ban már tárolt modell-és gráf-adatok használatával. Az alábbi hivatkozásokat követve további információkat olvashat az ikrek gráf információinak kezelésével, frissítésével és lekérésével kapcsolatban:

* [*Útmutató: digitális kettős kezelés*](./how-to-manage-twin.md)
* [*Útmutató: a Twin gráf lekérdezése*](./how-to-query-graph.md)
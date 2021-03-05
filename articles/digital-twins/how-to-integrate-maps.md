---
title: Integrálás az Azure Mapsszel
titleSuffix: Azure Digital Twins
description: Tekintse meg, hogyan használható a Azure Functions egy olyan függvény létrehozásához, amely a Twin Graph és az Azure digitális Twins értesítéseivel frissítheti Azure Maps beltéri térképeit.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 1/19/2021
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: 990a0ee73bd91ccb748c948b5fcf0e6124d84a03
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201430"
---
# <a name="use-azure-digital-twins-to-update-an-azure-maps-indoor-map"></a>Azure Maps beltéri Térkép frissítése az Azure digitális Twins használatával

Ez a cikk végigvezeti az Azure Digital Twins-adatok használatához szükséges lépéseken, hogy a [Azure Maps](../azure-maps/about-azure-maps.md)használatával egy *fedett térképen* megjelenített információkat frissítsen. Az Azure Digital Twins egy gráfot tárol a IoT, és a különböző végpontokra irányítja a telemetria, így tökéletes szolgáltatásként frissítheti a térképeken található információs átfedéseket.

Ez a útmutató a következő módon fog kiterjedni:

1. Az Azure Digital Twins-példány konfigurálása két frissítési esemény küldésére a [Azure functions](../azure-functions/functions-overview.md)függvényében.
2. Függvény létrehozása Azure Maps beltéri térképek szolgáltatás stateset frissítéséhez.
3. A Maps ID és a Feature stateset AZONOSÍTÓjának tárolása az Azure Digital Twins gráfban.

### <a name="prerequisites"></a>Előfeltételek

* Kövesse az Azure digitális Twins [*oktatóanyagát: teljes körű megoldás összekapcsolását*](./tutorial-end-to-end.md).
    * Ezt a Twin-t egy további végponttal és útvonallal kell kiterjeszteni. Az oktatóanyagból egy másik függvényt is hozzáadhat a Function alkalmazáshoz. 
* Kövesse a Azure Maps [*oktatóanyagot: a Azure Maps Creator használatával beltéri térképeket hozhat*](../azure-maps/tutorial-creator-indoor-maps.md) létre, amelyekkel Azure Maps fedett térképet hozhat létre a *szolgáltatás stateset*.
    * A [statesets](../azure-maps/creator-indoor-maps.md#feature-statesets) olyan dinamikus tulajdonságok (állapotok) gyűjteményei, amelyek adatkészlet-funkciókhoz, például helyiségekhez vagy berendezésekhez vannak rendelve. A fenti Azure Maps oktatóanyagban a funkció stateset a térképen megjelenített szoba állapotát tárolja.
    * Szüksége lesz a szolgáltatás *stateset-azonosítójára* és Azure Maps *előfizetési kulcsára*.

### <a name="topology"></a>Topológia

Az alábbi képen látható, hogy az oktatóanyag Indoor Maps integrációs elemei hogyan illeszkednek egy nagyobb, végpontok közötti Azure digitális Twins-forgatókönyvhöz.

:::image type="content" source="media/how-to-integrate-maps/maps-integration-topology.png" alt-text="Az Azure-szolgáltatások egy teljes körű forgatókönyvből álló nézete, amely kiemeli a beltéri térképek integrációs részletét" lightbox="media/how-to-integrate-maps/maps-integration-topology.png":::

## <a name="create-a-function-to-update-a-map-when-twins-update"></a>Függvény létrehozása Térkép frissítéséhez az ikrek frissítésekor

Először létre kell hoznia egy útvonalat az Azure Digital Ikrekben, hogy az összes, az Event Grid-témakörbe tartozó eseményt továbbítsa. Ezután egy függvény használatával olvassa be ezeket a frissítési üzeneteket, és frissítse a szolgáltatások stateset a Azure Mapsban. 

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Útvonal létrehozása és szűrés az ikerpéldány frissítési értesítéseire

Az Azure Digital Twins-példányok dupla frissítési eseményeket bocsátanak ki, amikor a Twin állapot frissül. Az Azure Digital Twins [*oktatóanyaga: a fenti lépésekhez kapcsolódó végpontok közötti megoldás összekapcsolása*](./tutorial-end-to-end.md) egy olyan forgatókönyvvel, amelyben egy hőmérőt használ egy szoba Twin-hez csatolt hőmérséklet-attribútum frissítéséhez. Ezt a megoldást kiterjesztheti az ikrek frissítési értesítéseire való feliratkozással, és ezekkel az információkkal frissítheti a térképeket.

Ez a minta közvetlenül a IoT-eszköz helyett a Twin szobaból olvassa be, amely rugalmasságot biztosít a mögöttes adatforrások hőmérsékletének módosításához anélkül, hogy frissítenie kellene a leképezési logikát. Például több hőmérőt is hozzáadhat, vagy beállíthatja, hogy egy hőmérőt egy másik helyiséggel osszanak meg, anélkül, hogy frissítenie kellene a Térkép logikáját.

1. Hozzon létre egy Event Grid-témakört, amely az Azure digitális Twins-példányának eseményeit fogja kapni.
    ```azurecli-interactive
    az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
    ```

2. Hozzon létre egy végpontot, amely összekapcsolja az Event Grid-témakört az Azure digitális Twins szolgáltatással.
    ```azurecli-interactive
    az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
    ```

3. Hozzon létre egy útvonalat az Azure Digital Twinsben, amelyen elküldheti az ikerpéldányok frissítési eseményeit a végpontnak.

    >[!NOTE]
    >A Cloud Shellben jelenleg egy **ismert probléma** akadályozza az alábbi parancscsoportokat: `az dt route`, `az dt model`, `az dt twin`.
    >
    >Ennek feloldásához futtassa az `az login` parancsot a parancs előtt a Cloud Shellben, vagy használja a [helyi CLI-t](/cli/azure/install-azure-cli) a Cloud Shell helyett. Erről további részleteket a [*Hibaelhárítás: az Azure digitális Twins ismert problémái*](troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell)című témakörben talál.

    ```azurecli-interactive
    az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <Event-Grid-endpoint-name> --route-name <my_route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

## <a name="create-a-function-to-update-maps"></a>Leképezést frissítő függvény létrehozása

Létre fog hozni egy **Event Grid által aktivált függvényt** a Function alkalmazásban a teljes körű oktatóanyagban ([*oktatóanyag: végpontok közötti megoldás összekapcsolása*](./tutorial-end-to-end.md)). Ez a függvény kicsomagolja ezeket az értesítéseket, és frissítéseket küld egy Azure Maps szolgáltatás stateset egy szoba hőmérsékletének frissítéséhez.

Tekintse meg a következő dokumentumot a hivatkozási információkhoz: [*Azure Event Grid trigger Azure functions*](../azure-functions/functions-bindings-event-grid-trigger.md).

Cserélje le a függvény kódját a következő kódra. Csak a Space ikrek frissítéseit szűri, a frissített hőmérsékletet olvassa be, majd elküldi ezeket az adatokat a Azure Mapsnak.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateMaps.cs":::

A Function alkalmazásban két környezeti változót kell beállítania. Az egyik a [Azure Maps elsődleges előfizetési kulcs](../azure-maps/quick-demo-map-app.md#get-the-primary-key-for-your-account), és az egyik a [Azure Maps stateset azonosítója](../azure-maps/tutorial-creator-indoor-maps.md#create-a-feature-stateset).

```azurecli-interactive
az functionapp config appsettings set --name <your-App-Service-(function-app)-name> --resource-group <your-resource-group> --settings "subscription-key=<your-Azure-Maps-primary-subscription-key>"
az functionapp config appsettings set --name <your-App-Service-(function-app)-name>  --resource-group <your-resource-group> --settings "statesetID=<your-Azure-Maps-stateset-ID>"
```

### <a name="view-live-updates-on-your-map"></a>Élő frissítések megtekintése a térképen

Az élő frissítés hőmérsékletének megtekintéséhez kövesse az alábbi lépéseket:

1. Szimulált IoT-adatok küldésének megkezdése a **DeviceSimulator** -projekt futtatásával az Azure Digital Twins [*oktatóanyagból: Kapcsolódás végpontok közötti megoldáshoz*](tutorial-end-to-end.md). Az ehhez tartozó útmutatást a a [*configure és a Run The szimuláció*](././tutorial-end-to-end.md#configure-and-run-the-simulation) szakaszban találja.
2. [A **Azure Maps beltéri** modul](../azure-maps/how-to-use-indoor-module.md) használatával jelenítheti meg a Azure Maps creatorben létrehozott beltéri térképeket.
    1. Másolja a HTML-t a [*példából: használja*](../azure-maps/how-to-use-indoor-module.md#example-use-the-indoor-maps-module) a beltéri térképek című oktatóanyag beltéri térképek modulját [*: használja a Azure Maps beltéri térképek modult*](../azure-maps/how-to-use-indoor-module.md) egy helyi fájlba.
    1. Cserélje le az *előfizetési kulcsot*, a *TilesetId* és a *statesetID*  a helyi HTML-fájlba az értékekkel.
    1. Nyissa meg a fájlt a böngészőben.

Mindkét minta egy kompatibilis tartományba küldi a hőmérsékletet, ezért 30 másodpercenként a térképen a 121-es frissítés színét kell látnia.

:::image type="content" source="media/how-to-integrate-maps/maps-temperature-update.png" alt-text="Egy Office-Térkép, amely színes narancssárga termet 121":::

## <a name="store-your-maps-information-in-azure-digital-twins"></a>A Maps-információk tárolása az Azure Digital Twinsban

Most, hogy már rendelkezik hardcoded-megoldással a Maps-információk frissítéséhez, az Azure digitális Twins gráf használatával tárolhatja a belső Térkép frissítéséhez szükséges összes információt. Ez magában foglalja a stateset AZONOSÍTÓját, a Maps előfizetés AZONOSÍTÓját és az egyes térképekhez és helyekhez tartozó szolgáltatás AZONOSÍTÓját. 

Az adott példához tartozó megoldás a legfelső szintű helyek frissítését is magában foglalja a stateset-azonosító és a Maps előfizetés-azonosító attribútum használatával, valamint az egyes helyiségek frissítését, hogy azok rendelkezzenek a szolgáltatás azonosítójával. Ezeket az értékeket egyszer kell megadnia a Twin gráf inicializálásakor, majd le kell kérdezni ezeket az értékeket az egyes dupla frissítési eseményekhez.

A topológia konfigurációjától függően a három attribútumot a Térkép részletességéhez képest különböző szinteken fogja tudni tárolni.

## <a name="next-steps"></a>Következő lépések

Az alábbi hivatkozásokat követve további információkat olvashat az ikrek gráf információinak kezelésével, frissítésével és lekérésével kapcsolatban:

* [*Útmutató: digitális ikrek kezelése*](./how-to-manage-twin.md)
* [*Útmutató: a Twin gráf lekérdezése*](./how-to-query-graph.md)

---
title: Integrálás az Azure Time Series Insights szolgáltatással
titleSuffix: Azure Digital Twins
description: Ismerje meg, hogyan állíthatja be az esemény-útvonalakat az Azure Digital Ikrekből a Azure Time Series Insightsba.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 4/7/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 256bb80687ffedba9718303710335cce1a582c1d
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304005"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>Az Azure Digital Twins integrálása Azure Time Series Insights

Ebből a cikkből megtudhatja, hogyan integrálhatja az Azure Digital Twins-t [Azure Time Series Insights (ÁME)](../time-series-insights/overview-what-is-tsi.md)használatával.

A cikkben ismertetett megoldás lehetővé teszi a IoT-megoldással kapcsolatos korábbi adatok összegyűjtését és elemzését. Az Azure Digital Twins remek megoldás a Time Series Insightsba való adatbetöltéshez, mivel segítségével több adatfolyamot is összefűzhet, és szabványosíthatja az adatokat a Time Series Insightsnak való küldés előtt.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt kapcsolatot hozna létre Time Series Insightsval, be kell állítania a következő erőforrásokat:
* Egy **IoT hub**. Útmutatásért tekintse *meg a IoT hub küldési telemetria* [*IoT hub létrehozása*](../iot-hub/quickstart-send-telemetry-cli.md#create-an-iot-hub) című szakaszát.
* Egy **Azure-beli digitális Twins-példány**.
Útmutatásért lásd [*: útmutató: Azure digitális Twins-példány és-hitelesítés beállítása*](./how-to-set-up-instance-portal.md).
* Egy **modell és egy Twin az Azure Digital Twins-példányban**.
A Twin adatokat néhányszor frissítenie kell, hogy megtekintse az adatokat a Time Series Insightsban. Útmutatásért tekintse meg a *How to: betöltés IoT hub* című cikket a [*modell hozzáadása és az iker*](how-to-ingest-iot-hub-data.md#add-a-model-and-twin) .

> [!TIP]
> Ebben a cikkben a Time Series Insights megtekintett digitális kettős értékek módosításait az egyszerűség kedvéért manuálisan frissítjük. Ha azonban ezt a cikket élő szimulált adatokkal szeretné befejezni, beállíthat egy olyan Azure-függvényt, amely egy szimulált eszközről származó IoT telemetria események alapján frissíti a digitális ikreket. Útmutatásért kövesse az [*útmutató: IoT hub-információ*](how-to-ingest-iot-hub-data.md)betöltését, beleértve az eszköz-szimulátor futtatásának végső lépéseit, és ellenőrizze, hogy az adatfolyam működik-e.
>
> Később keresse meg azt a TIPPet, amelyből megtudhatja, hová szeretné elindítani az eszközt, és az Azure functions automatikusan frissíti az ikreket, ahelyett, hogy manuális digitális kettős frissítési parancsokat küldene.


## <a name="solution-architecture"></a>Megoldásarchitektúra

Az alábbi elérési úton az Azure digitális Twins-hoz Time Series Insights fog kapcsolódni.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Az Azure-szolgáltatások diagramja egy végpontok közötti forgatókönyvben, Time Series Insights kiemelése." lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-event-hub-namespace"></a>Eseményközpont-névtér létrehozása

Az Event hub létrehozása előtt először létre kell hoznia egy Event hub-névteret, amely az Azure Digital Twins-példányból kap eseményeket. Használhatja az alábbi Azure CLI-utasításokat, vagy használhatja a Azure Portal: rövid útmutató [*: Event hub létrehozása Azure Portal használatával*](../event-hubs/event-hubs-create.md). Ha szeretné megtekinteni, hogy mely régiók támogatják az Event hubokat, látogasson el [*Az Azure-termékek területére*](https://azure.microsoft.com/global-infrastructure/services/?products=event-hubs).

```azurecli-interactive
az eventhubs namespace create --name <name-for-your-event-hubs-namespace> --resource-group <your-resource-group> -l <region>
```

> [!TIP]
> Ha hibaüzenet jelenik meg, győződjön meg `BadRequest: The specified service namespace is invalid.` arról, hogy a névtérhez választott név megfelel a jelen dokumentumban ismertetett elnevezési követelményeknek: [névtér létrehozása](/rest/api/servicebus/create-namespace).

Ezt az Event hubok-névteret fogja használni a cikkhez szükséges két esemény hubok tárolására:

  1. **Twins hub** – Event hub a kettős változási események fogadásához
  2. **Time Series hub** – Event hub – események továbbítása Time Series Insights

A következő szakasz végigvezeti ezen hubok létrehozásán és konfigurálásán az Event hub-névtéren belül.

## <a name="create-twins-hub"></a>Twins-központ létrehozása

Ebben a cikkben az **ikrek hub**-t fogja létrehozni. Ez az Event hub kettős változási eseményt fog kapni az Azure digitális Twins-ból.
Az ikrek-központ beállításához hajtsa végre a következő lépéseket a szakaszban:

1. Az ikrek hub létrehozása
2. Engedélyezési szabály létrehozása a hub engedélyeinek vezérléséhez
3. Hozzon létre egy végpontot az Azure Digital Ikrekben, amely az engedélyezési szabályt használja a hub eléréséhez
4. Hozzon létre egy útvonalat az Azure Digital Ikrekben, amelyek Twin Updates eseményt küldenek a végpontnak és a csatlakoztatott Twins-hubhoz
5. Az ikrek hub-beli kapcsolatok karakterláncának beolvasása

Hozza létre az **ikrek hubot** a következő CLI-paranccsal. Adja meg az ikrek központja nevét.

```azurecli-interactive
az eventhubs eventhub create --name <name-for-your-twins-hub> --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-above>
```

### <a name="create-twins-hub-authorization-rule"></a>Twins hub-engedélyezési szabály létrehozása

Hozzon létre egy [engedélyezési szabályt](/cli/azure/eventhubs/eventhub/authorization-rule#az-eventhubs-eventhub-authorization-rule-create) a küldési és fogadási engedélyekkel. Adja meg a szabály nevét.

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-twins-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above>
```

### <a name="create-twins-hub-endpoint"></a>Twins hub-végpont létrehozása

Hozzon létre egy Azure digitális Twins- [végpontot](concepts-route-events.md#create-an-endpoint) , amely az Event hub-t az Azure Digital Twins-példánnyal csatolja. Adja meg az Twins hub-végpont nevét.

```azurecli-interactive
az dt endpoint create eventhub -n <your-Azure-Digital-Twins-instance-name> --eventhub-resource-group <your-resource-group> --eventhub-namespace <your-event-hubs-namespace-from-earlier> --eventhub <your-twins-hub-name-from-above> --eventhub-policy <your-twins-hub-auth-rule-from-earlier> --endpoint-name <name-for-your-twins-hub-endpoint>
```

### <a name="create-twins-hub-event-route"></a>Twins hub-esemény útvonalának létrehozása

Az Azure Digital Twins-példányok [dupla frissítési eseményeket](how-to-interpret-event-data.md) bocsátanak ki, amikor a Twin állapot frissül. Ebben a szakaszban egy Azure digitális Twins- **esemény útvonalát** fogja létrehozni, amely további feldolgozás céljából irányítja ezeket a frissítési eseményeket az ikrek-hubhoz.

Hozzon létre egy [útvonalat](concepts-route-events.md#create-an-event-route) az Azure Digital ikrekben, hogy a fenti két frissítési eseményt küldje el a végpontnak. Az ebben az útvonalban lévő szűrő csak a kettős frissítési üzeneteket továbbítja a végpontnak. Adja meg az ikrek hub esemény útvonalának nevét.

```azurecli-interactive
az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <your-twins-hub-endpoint-from-above> --route-name <name-for-your-twins-hub-event-route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
```

### <a name="get-twins-hub-connection-string"></a>Ikrek hub-kapcsolatok karakterláncának beolvasása

Szerezze be az [ikrek Event hub kapcsolati karakterláncát](../event-hubs/event-hubs-get-connection-string.md)a fent létrehozott engedélyezési szabályok alapján az ikrek hubhoz.

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above> --name <your-twins-hub-auth-rule-from-earlier>
```
Jegyezze fel az **primaryConnectionString** értéket az eredményből az ikrek hub-alkalmazás beállításainak a jelen cikk későbbi részében történő konfigurálásához.

## <a name="create-time-series-hub"></a>Time Series hub létrehozása

A második Event hub ebben a cikkben a **Time Series hub**-t fogja létrehozni. Ez egy olyan Event hub, amely az Azure digitális Twins-eseményeket Time Series Insightsba továbbítja.
A Time Series hub beállításához hajtsa végre a következő lépéseket:

1. A Time Series hub létrehozása
2. Engedélyezési szabály létrehozása a hub engedélyeinek vezérléséhez
3. A Time Series hub-beli kapcsolatok karakterláncának beolvasása

Később, amikor létrehozza a Time Series Insights példányt, a Time Series Insights példányhoz tartozó eseményforrásként csatlakoztatja ezt az idősorozat-központot.

Hozza létre az **idősorozat hubot** a következő paranccsal. Adja meg a Time Series hub nevét.

```azurecli-interactive
 az eventhubs eventhub create --name <name-for-your-time-series-hub> --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier>
```

### <a name="create-time-series-hub-authorization-rule"></a>Time Series hub engedélyezési szabály létrehozása

Hozzon létre egy [engedélyezési szabályt](/cli/azure/eventhubs/eventhub/authorization-rule#az-eventhubs-eventhub-authorization-rule-create) a küldési és fogadási engedélyekkel. Adja meg a Time Series hub hitelesítési szabályának nevét.

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-time-series-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-above>
```

### <a name="get-time-series-hub-connection-string"></a>Time Series hub-beli kapcsolatok karakterláncának beolvasása

Szerezze be a [Time Series hub-beli kapcsolatok karakterláncát](../event-hubs/event-hubs-get-connection-string.md)a fent létrehozott engedélyezési szabályok használatával az idősorozat-hubhoz:

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-earlier> --name <your-time-series-hub-auth-rule-from-earlier>
```
Jegyezze fel a **primaryConnectionString** értékét az eredményből, hogy a cikk későbbi részében konfigurálja a Time Series hub-alkalmazás beállításait.

Jegyezze fel a következő értékeket is, amelyek segítségével később Time Series Insights példányt hozhat létre.
* Eseményközpont-névtér
* Time Series hub neve
* Time Series hub hitelesítési szabálya

## <a name="create-a-function"></a>Függvény létrehozása

Ebben a szakaszban egy olyan Azure-függvényt hoz létre, amely az eredeti űrlapon található Twin Update-eseményeket JSON-javítási dokumentumként átalakítja a JSON-objektumokba, amelyek csak a frissített és hozzáadott értékeket tartalmazzák az ikrektől.

### <a name="step-1-create-function-app"></a>1. lépés: Function-alkalmazás létrehozása

Először hozzon létre egy új Function app-projektet a Visual Studióban. Ennek módjával kapcsolatos útmutatásért tekintse meg a [**Function alkalmazás létrehozása a Visual Studióban**](how-to-create-azure-function.md#create-a-function-app-in-visual-studio) című szakaszt az *útmutató: függvény beállítása az adat feldolgozásához* című cikkben.

### <a name="step-2-add-a-new-function"></a>2. lépés: új függvény hozzáadása

Hozzon létre egy új, *ProcessDTUpdatetoTSI. cs* nevű Azure-függvényt az eszköz telemetria-eseményeinek a Time Series Insights való frissítéséhez. A függvény típusa az **Event hub eseményindítója** lesz.

:::image type="content" source="media/how-to-integrate-time-series-insights/create-event-hub-trigger-function.png" alt-text="A Visual Studio képernyőképe egy új Azure-függvény létrehozásához az Event hub trigger típussal.":::

### <a name="step-3-fill-in-function-code"></a>3. lépés: a függvény kódjának kitöltése

Adja hozzá a következő csomagokat a projekthez:
* [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
* [Microsoft. Azure. webjobs. Extensions. EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
* [Microsoft. NET. SDK. functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/)

Cserélje le a *ProcessDTUpdatetoTSI. cs* fájl kódját a következő kódra:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateTSI.cs":::

Mentse a függvény kódját.

### <a name="step-4-publish-the-function-app-to-azure"></a>4. lépés: a Function alkalmazás közzététele az Azure-ban

Tegye közzé a projektet a *ProcessDTUpdatetoTSI. cs* függvénnyel az Azure-beli Function alkalmazásban.

Ennek módjával kapcsolatos útmutatásért tekintse meg a [**Function alkalmazás közzététele az Azure**](how-to-create-azure-function.md#publish-the-function-app-to-azure) -ban című szakaszt az *útmutató: funkció beállítása az adat feldolgozásához* című cikkben.

Mentse a Function app nevet később, hogy az alkalmazás beállításait a két esemény hubok számára konfigurálja.

### <a name="step-5-security-access-for-the-function-app"></a>5. lépés: a Function app biztonsági hozzáférése

Ezután **rendeljen hozzá egy hozzáférési szerepkört** a függvényhez, és **konfigurálja az alkalmazás beállításait** , hogy hozzáférhessen az Azure Digital Twins-példányhoz. Ennek módjáról a következő témakörben talál útmutatást: a [**biztonsági hozzáférés beállítása a Function alkalmazáshoz**](how-to-create-azure-function.md#set-up-security-access-for-the-function-app) az *adatfeldolgozáshoz* című cikkben.

### <a name="step-6-configure-app-settings-for-the-two-event-hubs"></a>6. lépés: az alkalmazás beállításainak konfigurálása a két esemény hubokhoz

Ezután adja hozzá a környezeti változókat a Function alkalmazás beállításaiban, amelyek lehetővé teszik az IT számára az ikrek hub és a Time Series hub elérését.

A korábban mentett Twins hub **primaryConnectionString** -érték használatával hozzon létre egy alkalmazás-beállítást a Function alkalmazásban, amely tartalmazza az ikrek hub kapcsolódási karakterláncát:

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<your-twins-hub-primaryConnectionString>" -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

A korábban mentett Time Series hub **primaryConnectionString** -érték használatával hozzon létre egy alkalmazás-beállítást a Function alkalmazásban, amely tartalmazza a Time Series hub kapcsolódási karakterláncát:

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<your-time-series-hub-primaryConnectionString>" -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Time Series Insights-példány létrehozása és összekapcsolása

Ebben a szakaszban Time Series Insights példányt fog beállítani az idősorozat-hubhoz érkező adatok fogadásához. További információ erről a folyamatról [*: oktatóanyag: Azure Time Series Insights GEN2 TB-környezet beállítása*](../time-series-insights/tutorial-set-up-environment.md). Az alábbi lépéseket követve hozzon létre egy Time Series-beli adatáttekintési környezetet.

1. A [Azure Portal](https://portal.azure.com)keresse meg *Time Series Insights környezetek* kifejezést, majd kattintson a **Hozzáadás** gombra. Az idősorozat-környezet létrehozásához válassza a következő beállításokat.

    * **Előfizetés** – válassza ki az előfizetését.
        - **Erőforráscsoport** – válassza ki az erőforráscsoportot.
    * **Környezet neve** – adja meg a Time Series-környezet nevét.
    * **Hely** – válasszon egy helyet.
    * **Réteg** – válassza ki a **Gen2 (L1)** árképzési szintet.
    * **Tulajdonságnév** – írja be a **$dtId** (További információ az [*idősorozat-azonosító kiválasztására vonatkozó ajánlott eljárások*](../time-series-insights/how-to-select-tsid.md)azonosítójának kiválasztásáról).
    * **Storage-fiók neve** – adja meg a Storage-fiók nevét.
    * **Meleg tároló engedélyezése** – hagyja ezt a mezőt *Igen* értékre állítani.

    Ezen a lapon más tulajdonságok alapértelmezett értékeit is meghagyhatja. Válassza ki a **következőt: eseményforrás >** gomb.

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png" alt-text="Képernyőkép a Azure Portalról Time Series Insights környezet létrehozásához. Válassza ki az előfizetését, az erőforráscsoportot és a helyet a megfelelő legördülő listából, és válassza ki a környezet nevét." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png":::
        
    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png" alt-text="Képernyőkép a Azure Portalról Time Series Insights környezet létrehozásához. A Gen2 (L1) árképzési szintje van kiválasztva, az idősorozat-azonosító tulajdonság neve pedig $dtId." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png":::

2. Az *eseményforrás* lapon válassza a következő mezőket:

   * **Létrehoz egy eseményforrás?** – Válassza az *Igen* lehetőséget.
   * **Forrás típusa** – válassza az *Event hub* elemet.
   * **Név** – adja meg az eseményforrás nevét.
   * **Előfizetés** – válassza ki az Azure-előfizetését.
   * **Event hub-névtér** – válassza ki azt a névteret, amelyet a cikkben korábban hozott létre.
   * **Event hub neve** – válassza ki a cikkben korábban létrehozott **Time Series hub** -nevet.
   * **Event hub hozzáférési szabályzatának neve** – válassza ki a cikkben korábban létrehozott *Time Series hub Auth szabályt* .
   * **Event hub fogyasztói csoport** – válassza az *új* lehetőséget, és adja meg az Event hub fogyasztói csoport nevét. Ezután válassza a *Hozzáadás* lehetőséget.
   * **Tulajdonság neve** – hagyja üresen ezt a mezőt.
    
    A részletek áttekintéséhez kattintson a **felülvizsgálat + létrehozás** gombra. Ezután válassza újra a **felülvizsgálat + létrehozás** gombot az idősorozat-környezet létrehozásához.

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png" alt-text="Képernyőkép a Azure Portalról Time Series Insights környezet létrehozásához. Az Event hub információi alapján hozza létre az eseményforrás adatait. Új fogyasztói csoportot is létrehoz." lightbox="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png":::

## <a name="send-iot-data-to-azure-digital-twins"></a>IoT-adatküldés az Azure Digital Twinsba

Az adatok Time Series Insightsba való küldésének megkezdéséhez meg kell kezdenie a digitális Twin-tulajdonságok frissítését az Azure Digital Twins-ban az adatértékek módosításával.

Az alábbi CLI-paranccsal frissítheti a példányhoz az [Előfeltételek](#prerequisites) szakaszban megadott *thermostat67* Twin *hőmérséklet* tulajdonságát.

```azurecli-interactive
az dt twin update -n <your-azure-digital-twins-instance-name> --twin-id thermostat67 --json-patch '{"op":"replace", "path":"/Temperature", "value": 20.5}'
```

**Ismételje meg a parancsot legalább 4 alkalommal a különböző hőmérsékleti értékekkel**, hogy több adatpontot hozzon létre, amelyek később megfigyelhetők a Time Series Insights környezetben.

> [!TIP]
> Ha ezt a cikket élő szimulált adatokkal szeretné befejezni a digitális dupla értékek manuális frissítése helyett, először győződjön meg arról, hogy végrehajtotta a TIPPet az [*Előfeltételek*](#prerequisites) szakaszból egy olyan Azure-függvény beállításához, amely egy szimulált eszközről frissíti az ikreket.
Ezután futtathatja az eszközt a szimulált adatok küldésének megkezdéséhez és a digitális Twin-adatok frissítéséhez az adott adatfolyamon keresztül.

## <a name="visualize-your-data-in-time-series-insights"></a>Az adatok vizualizálása a Time Series Insightsban

Az adatforgalom az Time Series Insights-példányba kerül, és készen áll az elemzésre. Kövesse az alábbi lépéseket a beérkező adatforgalom megismeréséhez.

1. A [Azure Portal](https://portal.azure.com)keresse meg a korábban létrehozott idősorozat-környezet nevét. A bal oldali menüben válassza az *Áttekintés* lehetőséget az *Time Series Insights Explorer URL-címének* megtekintéséhez. Válassza ki az URL-címet a Time Series Insights környezetben megjelenített hőmérséklet-változások megtekintéséhez.

    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Képernyőkép a Azure Portalról az Time Series Insights-környezet áttekintés lapján a Time Series Insights Explorer URL-címének kiválasztásához." lightbox="media/how-to-integrate-time-series-insights/view-environment.png":::

2. Az Intézőben megjelenik az ikrek a bal oldalon látható Azure Digital Twins-példányban. Válassza ki a *thermostat67* Twin, válassza ki a tulajdonság *hőmérsékletét*, és kattintson a **Hozzáadás gombra**.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Képernyőfelvétel a Time Series Insights Explorerről a thermostat67 kiválasztásához válassza ki a tulajdonság hőmérsékletét, és kattintson a Hozzáadás gombra." lightbox="media/how-to-integrate-time-series-insights/add-data.png":::

3. Ekkor látnia kell a kezdeti hőmérséklet-leolvasásokat a termosztátból az alább látható módon. 

    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="Képernyőkép az ÁME-tallózóról a kezdeti hőmérsékleti érték megtekintéséhez. 68 és 85 közötti véletlenszerű értékek sora" lightbox="media/how-to-integrate-time-series-insights/initial-data.png":::

Ha engedélyezi, hogy a szimuláció jóval tovább fusson, a vizualizáció a következőképpen fog kinézni:

:::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="Képernyőfelvétel: az ÁME Explorer, ahol az egyes Twin-sorok hőmérsékleti értéke három, különböző színű párhuzamos vonalban van ábrázolva." lightbox="media/how-to-integrate-time-series-insights/day-data.png":::

## <a name="next-steps"></a>Következő lépések

A digitális ikreket alapértelmezés szerint a rendszer a Time Series Insightsban lévő, lapos hierarchiában tárolja, de a modell adataival és a szervezet többszintű hierarchiájának használatával gazdagíthatja őket. A folyamattal kapcsolatos további információkért olvassa el a következőt: 

* [*Oktatóanyag: modell meghatározása és alkalmazása*](../time-series-insights/tutorial-set-up-environment.md#define-and-apply-a-model) 

Egyéni logikát írhat arra, hogy automatikusan megadja ezeket az adatokat az Azure digitális Twins-ban már tárolt modell-és gráf-adatok használatával. Az alábbi hivatkozásokat követve további információkat olvashat az ikrek gráf információinak kezelésével, frissítésével és lekérésével kapcsolatban:

* [*Útmutató: digitális kettős kezelés*](./how-to-manage-twin.md)
* [*Útmutató: a Twin gráf lekérdezése*](./how-to-query-graph.md)
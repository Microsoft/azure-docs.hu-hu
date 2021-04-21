---
title: Integrálás az Azure Time Series Insights szolgáltatással
titleSuffix: Azure Digital Twins
description: Tekintse meg, hogyan állíthat be eseményútvonalat a Azure Digital Twins és Azure Time Series Insights.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 4/7/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: d89ee4c8e66ba4dda004fbd27e15b96ab13c642b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783772"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>Integráció Azure Digital Twins és Azure Time Series Insights

Ebből a cikkből megtudhatja, hogyan integrálhatja a Azure Digital Twins a [Azure Time Series Insights (TSI) megoldással.](../time-series-insights/overview-what-is-tsi.md)

A cikkben ismertetett megoldással összegyűjtheti és elemezheti az IoT-megoldás előzményadatokat. Az Azure Digital Twins remek megoldás a Time Series Insightsba való adatbetöltéshez, mivel segítségével több adatfolyamot is összefűzhet, és szabványosíthatja az adatokat a Time Series Insightsnak való küldés előtt.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt kapcsolatot hoz létre a Time Series Insights, a következő erőforrásokat kell beállítania:
* Egy **IoT Hub.** Útmutatásért tekintse meg a Telemetria [*küldése IoT Hub*](../iot-hub/quickstart-send-telemetry-cli.md#create-an-iot-hub) című IoT Hub *szakaszának* Új alkalmazás létrehozása című szakaszát.
* Egy **Azure Digital Twins példány.**
Útmutatásért [*lásd: Útmutató: Azure Digital Twins példány és hitelesítés beállítása.*](./how-to-set-up-instance-portal.md)
* Egy **modell és egy iker a Azure Digital Twins példányban.**
Néhányszor frissítenie kell az ikerinformációkat, hogy lássa az adatok nyomon követését a Time Series Insights. Útmutatásért tekintse meg [*a*](how-to-ingest-iot-hub-data.md#add-a-model-and-twin) How *to: Ingest IoT hub (Útmutató: IoT Hubba* való beúszás) című cikk Modell és iker hozzáadása című szakaszát.

> [!TIP]
> Ebben a cikkben az egyszerűség kedvéért manuálisan frissítjük a Time Series Insights által megtekintett digitális ikerértékeket. Ha azonban élő szimulált adatokkal szeretné befejezni ezt a cikket, beállíthat egy Azure-függvényt, amely egy szimulált eszköz IoT-telemetriaeseményei alapján frissíti a digitális ikereket. Útmutatásért kövesse a [*How to: Ingest IoT Hub data (Útmutató:*](how-to-ingest-iot-hub-data.md)adatok IoT Hub) útmutatást, beleértve az eszközszimulátor futtatásának és az adatfolyam-ellenőrzés utolsó lépéseit.
>
> Később egy újabb TIPP segítségével megmutatjuk, hol kezdheti el az eszközszimulátor futtatását, és hogy az Azure Functions automatikusan frissítse az ikereket ahelyett, hogy manuális digitális ikerfrissítési parancsokat küldenének.


## <a name="solution-architecture"></a>Megoldásarchitektúra

Az alábbi útvonalon Time Series Insights csatolja Azure Digital Twins-hez.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Az Azure-szolgáltatások diagramja egy végpontok között forgatókönyvben, kiemelt Time Series Insights." lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-event-hub-namespace"></a>Eseményközpont-névtér létrehozása

Az eseményközpontok létrehozása előtt létre kell hoznia egy eseményközpont-névteret, amely eseményeket fogad a Azure Digital Twins példánytól. Használhatja az alábbi Azure CLI-utasításokat, vagy használhatja a Azure Portal: Rövid útmutató: Eseményközpont létrehozása a [*Azure Portal.*](../event-hubs/event-hubs-create.md) Az eseményközpontokat támogató régiókért látogasson el a [*régiónként elérhető Azure-termékekre.*](https://azure.microsoft.com/global-infrastructure/services/?products=event-hubs)

```azurecli-interactive
az eventhubs namespace create --name <name-for-your-event-hubs-namespace> --resource-group <your-resource-group> -l <region>
```

> [!TIP]
> Ha a következő hibaüzenet jelenik meg: , győződjön meg arról, hogy a névtérhez kiválasztott név megfelel a következő referenciadokumentumban ismertetett elnevezési `BadRequest: The specified service namespace is invalid.` követelményeknek: [Névtér létrehozása.](/rest/api/servicebus/create-namespace)

Ezt az Event Hubs-névteret fogja használni a cikkhez szükséges két eseményközponthoz:

  1. **Ikerközpont –** Eseményközpont az iker módosítási események fogadásához
  2. **Time Series Hub** – Eseményközpont az események streamelése Time Series Insights

A következő szakaszok végigkesik ezeknek a központoknak az eseményközpont-névtérben való létrehozásán és konfigurálásán.

## <a name="create-twins-hub"></a>Ikerközpont létrehozása

A cikkben elsőként az ikerközpontot fogja **létrehozni.** Ez az eseményközpont ikereszköz-módosítási eseményeket fogad a Azure Digital Twins.
Az ikerközpont beállításához a következő lépéseket kell végrehajtania ebben a szakaszban:

1. Az ikerközpont létrehozása
2. Engedélyezési szabály létrehozása a központ engedélyeinek vezérlésére
3. Hozzon létre egy végpontot a Azure Digital Twins, amely az engedélyezési szabály használatával fér hozzá a központhoz
4. Hozzon létre egy útvonalat a Azure Digital Twins, amely ikerfrissítési eseményt küld a végpontnak és a csatlakoztatott ikerközpontnak
5. Az ikerközpont kapcsolati sztringének lekérte

Hozza létre **az ikerközpontot** a következő CLI-paranccsal. Adja meg az ikerközpont nevét.

```azurecli-interactive
az eventhubs eventhub create --name <name-for-your-twins-hub> --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-above>
```

### <a name="create-twins-hub-authorization-rule"></a>Ikerközpont engedélyezési szabályának létrehozása

Hozzon létre [egy engedélyezési szabályt](/cli/azure/eventhubs/eventhub/authorization-rule#az_eventhubs_eventhub_authorization_rule_create) küldési és fogadási engedélyekkel. Adja meg a szabály nevét.

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-twins-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above>
```

### <a name="create-twins-hub-endpoint"></a>Ikereszköz hubvégpontjának létrehozása

Hozzon létre egy Azure Digital Twins [végpontot,](concepts-route-events.md#create-an-endpoint) amely az eseményközpontot a Azure Digital Twins példányhoz. Adja meg az ikerközpont végpontjának nevét.

```azurecli-interactive
az dt endpoint create eventhub -n <your-Azure-Digital-Twins-instance-name> --eventhub-resource-group <your-resource-group> --eventhub-namespace <your-event-hubs-namespace-from-earlier> --eventhub <your-twins-hub-name-from-above> --eventhub-policy <your-twins-hub-auth-rule-from-earlier> --endpoint-name <name-for-your-twins-hub-endpoint>
```

### <a name="create-twins-hub-event-route"></a>Ikerközpont eseményútvonalának létrehozása

Azure Digital Twins ikerpéldányok ikerfrissítési [eseményeket bocsátanak](how-to-interpret-event-data.md) ki az ikerállapot frissítésekor. Ebben a szakaszban egy új eseményútvonalat  Azure Digital Twins, amely ezeket a frissítési eseményeket az ikerközpontba irányítja további feldolgozás céljából.

Hozzon [létre egy útvonalat](concepts-route-events.md#create-an-event-route) a Azure Digital Twins, hogy a fenti ikerfrissítési eseményeket a végpontra küldje. Az útvonal szűrője csak az ikerfrissítési üzenetek végpontnak való küldését engedélyezi. Adja meg az ikerközpont eseményútvonalának nevét.

```azurecli-interactive
az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <your-twins-hub-endpoint-from-above> --route-name <name-for-your-twins-hub-event-route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
```

### <a name="get-twins-hub-connection-string"></a>Az ikerközpont kapcsolati sztringének lekérte

Szerezze be az [twins eseményközpont kapcsolati sztringet](../event-hubs/event-hubs-get-connection-string.md)a fenti engedélyezési szabályok használatával az ikerközponthoz.

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above> --name <your-twins-hub-auth-rule-from-earlier>
```
Jegyezze fel az **eredmény primaryConnectionString** értékét, hogy a cikk későbbi része konfigurálja az ikerközpont alkalmazásbeállítását.

## <a name="create-time-series-hub"></a>Idősorozat-központ létrehozása

A második eseményközpont, amely ebben a cikkben lesz létrehozva, az **idősorozat-központ.** Ez egy eseményközpont, amely streameli a Azure Digital Twins eseményeket a Time Series Insights.
Az idősorozat-központ beállításához a következő lépéseket kell végrehajtania:

1. Az idősorozat-központ létrehozása
2. Engedélyezési szabály létrehozása a központ engedélyeinek vezérlésére
3. Az idősorközpont kapcsolati sztringje

Később, amikor létrehozza a Time Series Insights-példányt, ezt az idősor-központot fogja csatlakoztatni a Time Series Insights eseményforrásaként.

Hozza létre **az idősorozat-központot** a következő paranccsal. Adja meg az idősor-központ nevét.

```azurecli-interactive
 az eventhubs eventhub create --name <name-for-your-time-series-hub> --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier>
```

### <a name="create-time-series-hub-authorization-rule"></a>Idősorközpont engedélyezési szabályának létrehozása

Hozzon létre [egy engedélyezési szabályt](/cli/azure/eventhubs/eventhub/authorization-rule#az_eventhubs_eventhub_authorization_rule_create) küldési és fogadási engedélyekkel. Adja meg az idősor-központ hitelesítési szabályának nevét.

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-time-series-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-above>
```

### <a name="get-time-series-hub-connection-string"></a>Time Series Hub kapcsolati sztring lekért része

Szerezze be a [time series hub kapcsolati sztringet](../event-hubs/event-hubs-get-connection-string.md)a time series hubhoz létrehozott fenti engedélyezési szabályokkal:

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-earlier> --name <your-time-series-hub-auth-rule-from-earlier>
```
Jegyezze fel az **eredmény primaryConnectionString** értékét a cikk későbbi, Time Series Hub-alkalmazásbeállításának konfiguráláshoz.

Jegyezze fel az alábbi értékeket is, hogy később használjuk őket egy Time Series Insights létrehozásához.
* Eseményközpont-névtér
* Idősorközpont neve
* Idősorozat-központ hitelesítési szabálya

## <a name="create-a-function"></a>Függvény létrehozása

Ebben a szakaszban egy Azure-függvényt fog létrehozni, amely JSON Patch-dokumentumokká alakítja az ikerfrissítési eseményeket az eredeti formjukból JSON-objektumokká, és csak az ikerdokumentumok frissített és hozzáadott értékeit tartalmazza.

### <a name="step-1-create-function-app"></a>1. lépés: Függvényalkalmazás létrehozása

Először hozzon létre egy új függvényalkalmazás-projektet a Visual Studio. Ennek létrehozására vonatkozó utasításokat az [](how-to-create-azure-function.md#create-a-function-app-in-visual-studio) *Útmutató:* Függvény beállítása adatfeldolgozáshoz című cikk Függvényalkalmazás létrehozása Visual Studio szakaszában talál.

### <a name="step-2-add-a-new-function"></a>2. lépés: Új függvény hozzáadása

Hozzon létre egy új Azure-függvényt *ProcessDTUpdatetoTSI.cs* néven az eszköz telemetriai eseményeinek frissítéséhez a Time Series Insights. A függvény típusa **Eseményközpont-eseményindító** lesz.

:::image type="content" source="media/how-to-integrate-time-series-insights/create-event-hub-trigger-function.png" alt-text="Képernyőkép egy Visual Studio eseményközpont-eseményindító típusú új Azure-függvény létrehozásáról.":::

### <a name="step-3-fill-in-function-code"></a>3. lépés: A függvénykód kitöltése

Adja hozzá a következő csomagokat a projekthez:
* [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
* [Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
* [Microsoft .NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/)

Cserélje le a *ProcessDTUpdatetoTSI.cs* fájlban található kódot a következőre:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateTSI.cs":::

Mentse a függvénykódot.

### <a name="step-4-publish-the-function-app-to-azure"></a>4. lépés: A függvényalkalmazás közzététele az Azure-ban

Tegye közzé a projektet a *ProcessDTUpdatetoTSI.cs* függvény használatával egy Azure-beli függvényalkalmazásban.

Ennek mikéntjéhez lásd a Függvényalkalmazás közzététele az [**Azure-ban**](how-to-create-azure-function.md#publish-the-function-app-to-azure) című szakaszt az Útmutató: Függvény beállítása az adatok feldolgozásához *című cikkben.*

Mentse a függvényalkalmazás nevét, hogy később konfigurálja a két eseményközpont alkalmazásbeállítását.

### <a name="step-5-security-access-for-the-function-app"></a>5. lépés: Biztonsági hozzáférés a függvényalkalmazáshoz

Ezután **rendeljen hozzá egy** hozzáférési  szerepkört a függvényhez, és konfigurálja az alkalmazásbeállításokat, hogy az hozzáfér Azure Digital Twins példányhoz. Ennek mikéntjéhez útmutatást talál az [](how-to-create-azure-function.md#set-up-security-access-for-the-function-app) Útmutató: Függvény beállítása az adatok feldolgozásához című cikk Biztonsági hozzáférés beállítása a függvényalkalmazáshoz *című szakaszában.*

### <a name="step-6-configure-app-settings-for-the-two-event-hubs"></a>6. lépés: A két eseményközpont alkalmazásbeállításának konfigurálása

Ezután környezeti változókat fog hozzáadni a függvényalkalmazás beállításaihoz, amelyek lehetővé teszik az ikereszköz és a Time Series Hub hozzáférését.

A korábban mentett **primaryConnectionString érték** használatával hozzon létre egy alkalmazásbeállítást a függvényalkalmazásban, amely tartalmazza az twins hub kapcsolati sztringet:

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<your-twins-hub-primaryConnectionString>" -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

Használja a korábban mentett time series hub **primaryConnectionString** értéket egy olyan alkalmazásbeállítás létrehozásához a függvényalkalmazásban, amely tartalmazza az idősorozat-központ kapcsolati sztringet:

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<your-time-series-hub-primaryConnectionString>" -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Time Series Insights-példány létrehozása és összekapcsolása

Ebben a szakaszban egy új példányt fog beállítani, Time Series Insights adatokat fogad az idősorozat-központból. A folyamattal kapcsolatos további részletekért [*lásd: Oktatóanyag: Azure Time Series Insights Gen2 PAYG-környezet beállítása.*](../time-series-insights/tutorial-set-up-environment.md) Time Series Insights-környezet létrehozásához kövesse az alábbi lépéseket.

1. A [Azure Portal](https://portal.azure.com)keresse meg a *Time Series Insights környezeteket,* és válassza a **Hozzáadás** gombot. Az idősorozat-környezet létrehozásához válassza az alábbi lehetőségeket.

    * **Előfizetés –** Válassza ki az előfizetését.
        - **Erőforráscsoport** – Válassza ki az erőforráscsoportot.
    * **Környezet neve** – Adja meg az idősorozat-környezet nevét.
    * **Hely** – Válasszon egy helyet.
    * **Szint** – Válassza a **Gen2(L1)** tarifacsomagot.
    * **Tulajdonság neve** – Adja **$dtId** (További információ az azonosítóérték kiválasztásáról az Ajánlott eljárások a Time Series ID [*kiválasztásához) alatt.*](../time-series-insights/how-to-select-tsid.md)
    * **Tárfiók neve** – Adja meg a tárfiók nevét.
    * **Meleg tároló engedélyezése** – Hagyja a mezőben a Yes *(Igen) beállítását.*

    Ezen az oldalon meghagyhatja az egyéb tulajdonságok alapértelmezett értékeit. Válassza **a Tovább: Eseményforrás >** gombot.

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png" alt-text="Képernyőkép a Azure Portal létrehozásához szükséges Time Series Insights létrehozásáról. Válassza ki előfizetését, erőforráscsoportját és helyét a megfelelő legördülő menüből, és válasszon egy nevet a környezetnek." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png":::
        
    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png" alt-text="Képernyőkép a Azure Portal létrehozásához szükséges Time Series Insights létrehozásáról. A Gen2(L1) tarifacsomag van kiválasztva, és az idősor-azonosító tulajdonságneve $dtId." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png":::

2. Az *Eseményforrás lapon* válassza ki a következő mezőket:

   * **Létrehoz egy eseményforrást?** – Válassza az *Igen lehetőséget.*
   * **Forrás típusa –** Válassza az *Eseményközpont lehetőséget.*
   * **Név** – Adja meg az eseményforrás nevét.
   * **Előfizetés –** Válassza ki az Azure-előfizetését.
   * **Eseményközpont-névtér** – Válassza ki a cikkben korábban létrehozott névteret.
   * **Eseményközpont neve** – Válassza ki a **cikkben** korábban létrehozott idősorozat-központ nevét.
   * **Eseményközpont hozzáférési szabályzatának neve** – Válassza ki a cikkben korábban létrehozott *Time Series Hub* hitelesítési szabályt.
   * **Eseményközpont fogyasztói csoportja** – Válassza az *Új lehetőséget,* és adja meg az eseményközpont fogyasztói csoportjának nevét. Ezután válassza a *Hozzáadás lehetőséget.*
   * **Tulajdonság neve** – Ezt a mezőt hagyja üresen.
    
    Válassza az **Áttekintés + létrehozás gombot** az összes részlet áttekintéshez. Ezután válassza ismét az **Áttekintés + létrehozás** gombot az idősorozat-környezet létrehozásához.

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png" alt-text="Képernyőkép a Azure Portal létrehozásához szükséges Time Series Insights létrehozásáról. A fentiekből származó eseményközpont-információkkal hoz létre egy eseményforrást. Új fogyasztói csoportot is létrehoz." lightbox="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png":::

## <a name="send-iot-data-to-azure-digital-twins"></a>IoT-adatok küldése Azure Digital Twins

Ahhoz, hogy adatokat küld Time Series Insights számára, el kell kezdenie frissíteni a digitális ikertulajdonságokat a Azure Digital Twins adatértékek módosításával.

A következő CLI-paranccsal frissítheti a *Példányhoz* az Előfeltételek szakaszban hozzáadott *termosztát67 ikereszköz* [Temperature tulajdonságát.](#prerequisites)

```azurecli-interactive
az dt twin update -n <your-azure-digital-twins-instance-name> --twin-id thermostat67 --json-patch '{"op":"replace", "path":"/Temperature", "value": 20.5}'
```

Ismételje meg a parancsot legalább **4** alkalommal különböző hőmérsékletértékekkel, hogy több adatpontot hozzon létre, amelyek később megfigyelhetők a Time Series Insights környezetben.

> [!TIP]
> Ha a digitális ikerértékek manuális frissítése helyett élő szimulált adatokkal szeretné elvégezni ezt a cikket, [](#prerequisites) először győződjön meg arról, hogy befejezte az Előfeltételek szakaszban található TIPPet, és beállít egy Azure-függvényt, amely egy szimulált eszközről frissíti az ikereket.
Ezután futtathatja az eszközt, hogy elindítsa a szimulált adatok küldését és a digitális ikereszköz frissítését az adatfolyamon keresztül.

## <a name="visualize-your-data-in-time-series-insights"></a>Az adatok vizualizálása a Time Series Insightsban

Az adatoknak most a saját Time Series Insights kell áramlni, és készen állnak az elemzésre. Kövesse az alábbi lépéseket a bekért adatok feltárására.

1. A [Azure Portal](https://portal.azure.com)keresse meg a korábban létrehozott idősorozat-környezet nevét. A bal oldali menüben válassza az *Áttekintés lehetőséget* az URL-cím *Time Series Insights-kezelő meg.* Válassza ki az URL-címet a hőmérséklet változásainak megtekintéséhez a Time Series Insights környezetben.

    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="A Azure Portal áttekintési lapján Time Series Insights explorer URL-címének kiválasztására Time Series Insights képernyőkép." lightbox="media/how-to-integrate-time-series-insights/view-environment.png":::

2. Az Explorerben az ikereket a bal oldalon Azure Digital Twins példányban láthatja. Válassza ki *a termosztát67* ikereszközt, válassza ki a *Temperature tulajdonságot,* majd kattintson a **Hozzáadás gombra.**

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Képernyőkép a Time Series Insights kiválasztásához, válassza ki a termosztát67-et, válassza ki a hőmérséklet tulajdonságot, majd nyomja le a Hozzáadás gombot." lightbox="media/how-to-integrate-time-series-insights/add-data.png":::

3. Most már látnia kell a termosztát kezdeti hőmérsékleti értékeket az alább látható módon. 

    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="Képernyőkép a TSI Explorerről a kezdeti hőmérsékleti adatok megtekintéséhez. Ez egy 68 és 85 közötti véletlenszerű értékekből áll" lightbox="media/how-to-integrate-time-series-insights/initial-data.png":::

Ha egy szimuláció sokkal hosszabb ideig futhat, a vizualizáció a következő képi megjelenítéshez hasonló lesz:

:::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="Képernyőkép a TSI Explorerről, ahol az egyes ikerpárok hőmérsékleti adatai három különböző színű párhuzamos vonalon vannak ábrázolva." lightbox="media/how-to-integrate-time-series-insights/day-data.png":::

## <a name="next-steps"></a>Következő lépések

A digitális ikereket a rendszer alapértelmezés szerint egyszintű hierarchiaként tárolja a Time Series Insights-ban, de bővítheti őket modellinformációk és a szervezet többszintű hierarchiája. A folyamattal kapcsolatos további információkért olvassa el a következőt: 

* [*Oktatóanyag: Modell meghatározása és alkalmazása*](../time-series-insights/tutorial-set-up-environment.md#define-and-apply-a-model) 

Írhat egyéni logikát, amely automatikusan biztosítja ezt az információt a modell és a gráfadatok használatával, amelyek már Azure Digital Twins. Az ikergráfban található információk kezelésével, frissítésével és lekért értékével kapcsolatos további információkért tekintse meg a következő hivatkozásokat:

* [*How-to: Manage a digital twin (How-to: Digitális iker kezelése)*](./how-to-manage-twin.md)
* [*Hogyan lehet lekérdezni az ikergráfot?*](./how-to-query-graph.md)
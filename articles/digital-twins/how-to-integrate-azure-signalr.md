---
title: Integrálás az Azure SignalR szolgáltatással
titleSuffix: Azure Digital Twins
description: Ismerje meg, hogyan stream Azure Digital Twins telemetria az ügyfeleknek az Azure Signaler használatával
author: dejimarquis
ms.author: aymarqui
ms.date: 09/02/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 86d0c75d8b4c7c331e3e7ad90271e3fb42ff1964
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2021
ms.locfileid: "99980728"
---
# <a name="integrate-azure-digital-twins-with-azure-signalr-service"></a>Az Azure Digital Twins integrálása az Azure Signaler szolgáltatással

Ebből a cikkből megtudhatja, hogyan integrálhatja az Azure Digital Twins-t az [Azure signaler szolgáltatással](../azure-signalr/signalr-overview.md).

Az ebben a cikkben ismertetett megoldás lehetővé teszi a digitális kettős telemetria-információk leküldését a csatlakoztatott ügyfelekre, például egyetlen weboldalra vagy egy mobil alkalmazásra. Ennek eredményeképpen az ügyfelek valós idejű metrikákkal és állapottal frissülnek a IoT-eszközökről, anélkül, hogy le kellene kérdezni a kiszolgálót, vagy új HTTP-kéréseket kell elküldeniük a frissítésekhez.

## <a name="prerequisites"></a>Előfeltételek

A továbblépés előtt végezze el a következő előfeltételeket:

* A megoldás az Azure Signaler szolgáltatással való integrálása előtt ebben a cikkben az Azure Digital Twins oktatóanyagát kell elvégeznie, amely egy teljes körű [_**megoldás csatlakoztatására**_](tutorial-end-to-end.md)szolgál, mivel ez az útmutató ennek megfelelően épül fel. Az oktatóanyag végigvezeti egy olyan Azure Digital Twins-példány beállításán, amely egy virtuális IoT-eszközzel működik együtt a digitális kettős frissítések elindításához. Ez a útmutató ezeket a frissítéseket egy minta-webalkalmazáshoz fogja kapcsolni az Azure Signaler szolgáltatás használatával.
    - Szüksége lesz az oktatóanyagban létrehozott **Event Grid-témakör** nevére.
* [**Node.js**](https://nodejs.org/) telepítve van a gépen.

Emellett az Azure-fiókjával is bejelentkezhet a [Azure Portalba](https://portal.azure.com/) .

## <a name="solution-architecture"></a>Megoldásarchitektúra

Az Azure Signaler szolgáltatást az alábbi elérési úton csatlakoztathatja az Azure Digital Twins szolgáltatáshoz. A diagram "A", "B" és "C" részeit a rendszer a [végpontok közötti oktatóanyag előfeltételének](tutorial-end-to-end.md)architektúra-diagramján mutatja be. Ebben az útmutatóban ezt a D. szakasz hozzáadásával fogja felépíteni.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-integration-topology.png" alt-text="Az Azure-szolgáltatások egy végpontok közötti forgatókönyvben való megtekintése. Az eszközről a IoT Hubba, egy Azure-függvénnyel (B. nyíl) egy Azure Digital Twins-példányra (A szakasz), Event Grid majd egy másik Azure-függvényre (A C. nyílra) átáramló adatok ábrázolása. A D. szakasz a &quot;Broadcast&quot; címkével ellátott Azure-függvényhez tartozó, azonos Event Gridból áramló adatok megjelenítését jeleníti meg. a &quot;Broadcast&quot; az &quot;egyeztetés&quot; címkével ellátott másik Azure-függvénysel kommunikál, és a &quot;szórás&quot; és az &quot;egyeztetés&quot; is számítógépes eszközökkel kommunikál." lightbox="media/how-to-integrate-azure-signalr/signalr-integration-topology.png":::

## <a name="download-the-sample-applications"></a>A minta alkalmazások letöltése

Először töltse le a szükséges minta alkalmazásokat. A következőkre lesz szüksége:
* [**Azure digitális Twins végpontok közötti minták**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/): Ez a minta egy *AdtSampleApp* tartalmaz, amely két Azure-függvényt tárol az adatok Azure digitális Twins-példány körüli áthelyezéséhez (ez a forgatókönyv részletesebben is elérhető az [*oktatóanyagban: végpontok közötti megoldás összekötése*](tutorial-end-to-end.md)). Emellett egy *DeviceSimulator* -minta alkalmazást is tartalmaz, amely egy IoT-eszközt szimulál, és másodpercenként új hőmérséklet-értéket generál. 
    - Ha még nem töltötte le a mintát az oktatóanyag [*előfeltételeinek*](#prerequisites)részeként, keresse meg a minta hivatkozást, és válassza a cím alatt található *tallózási kód* gombot. Ekkor megnyílik a minták GitHub-tárháza, amelyet a-ként tölthet le *. ZIP* -t a *Code (kód* ) gomb kiválasztásával és a *zip letöltésével*.

    :::image type="content" source="media/includes/download-repo-zip.png" alt-text="A Digital-Twins-Samples tárház áttekintése a GitHubon. A kód gomb be van jelölve, és létrehoz egy kis párbeszédpanelt, ahol ki van emelve a ZIP-Letöltés gomb." lightbox="media/includes/download-repo-zip.png":::

    Ezzel letölti a minta-tárház egy példányát a gépre, **digital-twins-samples-master.zip**. Csomagolja ki a mappát.
* A [**signaler Integration Web App minta**](/samples/azure-samples/digitaltwins-signalr-webapp-sample/digital-twins-samples/): ez egy példa a webalkalmazásra, amely az Azure-beli digitális Twins telemetria adatait az Azure signaler szolgáltatásból fogja felhasználni.
    -  Navigáljon a minta hivatkozásra, és a *zip letöltése* gombra kattintva töltse le a minta egy példányát a gépre, _**Azure_Digital_Twins_SignalR_integration_web_app_sample.zip**_. Csomagolja ki a mappát.

[!INCLUDE [Create instance](../azure-signalr/includes/signalr-quickstart-create-instance.md)]

Hagyja nyitva a böngészőablakot a Azure Portal, ahogy azt a következő szakaszban újra használni fogja.

## <a name="configure-and-run-the-azure-functions-app"></a>A Azure Functions alkalmazás konfigurálása és futtatása

Ebben a szakaszban két Azure-függvényt fog beállítani:
* **egyeztetés** – http-trigger függvény. A *SignalRConnectionInfo* bemeneti kötését használja az érvényes kapcsolati adatok létrehozásához és visszaadásához.
* **Broadcast** – egy [Event Grid](../event-grid/overview.md) trigger függvény. Az Azure digitális Twins telemetria adatokat fogad az Event griden keresztül, és az előző lépésben létrehozott *Signal* -példány kimeneti kötését használja az üzenetnek az összes csatlakoztatott ügyfélalkalmazás számára történő szórásához.

Először nyissa meg a böngészőt, ahol a Azure Portal meg van nyitva, és végezze el a következő lépéseket a beállított Signaler-példányhoz tartozó **kapcsolódási karakterlánc** beszerzéséhez. Szüksége lesz rá a függvények konfigurálásához.
1. Győződjön meg arról, hogy a korábban telepített Signaler Service-példány sikeresen létrejött. Ezt úgy teheti meg, hogy megkeresi a nevét a portál felső részén található keresőmezőbe. A megnyitáshoz válassza ki a példányt.

1. Válassza a példány menü **kulcsok** elemét, hogy megtekintse a signaler szolgáltatás példányához tartozó kapcsolódási karakterláncokat.

1. Válassza ki az ikont az elsődleges kapcsolódási karakterlánc másolásához.

    :::image type="content" source="media/how-to-integrate-azure-signalr/signalr-keys.png" alt-text="Képernyőkép a Azure Portalről, amely a Signaler-példány kulcsok lapját jeleníti meg. Az elsődleges KAPCSOLÓDÁSi karakterlánc melletti &quot;másolás a vágólapra&quot; ikon ki van emelve." lightbox="media/how-to-integrate-azure-signalr/signalr-keys.png":::

Ezután indítsa el a Visual studiót (vagy egy másik szerkesztőprogramot), és nyissa meg a kód megoldást a *Digital-Twins-Samples-master > ADTSampleApp* mappában. Ezután végezze el a következő lépéseket a függvények létrehozásához:

1. A *SampleFunctionsApp* projektben hozzon létre egy új, **SignalRFunctions.cs** nevű C#-osztályt.

1. Cserélje le a Class (osztály) fájl tartalmát a következő kódra:
    
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/signalRFunction.cs":::

1. A Visual Studio *csomagkezelő konzoljának* ablakában vagy a *Digital-Twins-Samples-master\AdtSampleApp\SampleFunctionsApp* mappában található bármelyik parancssorablakban futtassa a következő parancsot a `SignalRService` NuGet-csomag projektbe történő telepítéséhez:
    ```cmd
    dotnet add package Microsoft.Azure.WebJobs.Extensions.SignalRService --version 1.2.0
    ```

    Ennek során fel kell oldania a függőségi problémákat az osztályban.

Ezután tegye közzé a függvényt az Azure-ban, az [ *alkalmazás közzététele* című szakaszban](tutorial-end-to-end.md#publish-the-app) ismertetett lépéseket követve a *Kapcsolódás végpontok közötti megoldáshoz* című oktatóanyagot. Közzéteheti ugyanazt az App Service/Function alkalmazásban, amelyet a végpontok közötti oktatóanyag [előfeltételében](#prerequisites)használt, vagy létrehozhat egy újat – de érdemes lehet ugyanezt is használni a Duplikálás minimalizálásához. 

Ezután fejezze be az alkalmazás közzétételét a következő lépésekkel:
1. Gyűjtse össze az *egyeztetési* függvény **http-végpontjának URL-címét**. Ehhez nyissa meg a Azure Portal [Function apps](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) lapot, és válassza ki a függvény alkalmazást a listából. Az alkalmazás menüben válassza a *függvények* lehetőséget, majd válassza az *egyeztetés* funkciót.

    :::image type="content" source="media/how-to-integrate-azure-signalr/functions-negotiate.png" alt-text="Azure Portal a Function alkalmazás nézetét, és a &quot;functions&quot; elem ki van emelve a menüben. A függvények listája megjelenik az oldalon, és az &quot;egyeztetés&quot; függvény is ki van emelve.":::

    A *függvény URL-címének lekérése* és az érték másolása a **_/API_ használatával (ne szerepeljen az utolsó _/Negotiate?_)**. Ezt később fogja használni.

    :::image type="content" source="media/how-to-integrate-azure-signalr/get-function-url.png" alt-text="Az &quot;egyeztetés&quot; függvény Azure Portal nézete. A &quot;függvény URL-címének beolvasása&quot; gomb ki van emelve, az URL-cím része pedig az elejétől a &quot;/API&quot;":::

1. Végezetül az alábbi Azure CLI-paranccsal adja hozzá az Azure Signaler- **kapcsolódási karakterláncot** a korábbi verzióról a függvény alkalmazás beállításaihoz. A parancs [Azure Cloud Shell](https://shell.azure.com)vagy helyileg is futtatható, ha telepítve van az Azure CLI a [gépen](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true):
 
    ```azurecli-interactive
    az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "AzureSignalRConnectionString=<your-Azure-SignalR-ConnectionString>"
    ```

    A parancs kimenete kinyomtatja az Azure-függvényhez beállított összes alkalmazást. A `AzureSignalRConnectionString` lista alján keresse meg a hozzáadásának ellenőrzését.

    :::image type="content" source="media/how-to-integrate-azure-signalr/output-app-setting.png" alt-text="A parancs kimenetének kivonata, amely egy &quot;AzureSignalRConnectionString&quot; nevű listaelemet mutat be":::

#### <a name="connect-the-function-to-event-grid"></a>A függvény összekötése Event Grid

Ezután fizessen elő a *Broadcast* Azure-függvényt az oktatóanyag során létrehozott **Event Grid-témakörre** [*: csatlakoztasson egy végpontok*](tutorial-end-to-end.md) közötti prereq. Ez lehetővé teszi, hogy a telemetria-adatok a *thermostat67* a Twin-en keresztül az Event Grid-témakörben, amely az összes ügyfél számára továbbítható.

Ehhez létre kell hoznia egy Event Grid- **előfizetést** az Event Grid-témakörből a *szórásos* Azure-függvényhez végpontként.

A [Azure Portalban](https://portal.azure.com/)keresse meg az Event Grid-témakört úgy, hogy a felső keresési sávban keresi a nevét. Válassza a *+ Esemény-előfizetés* lehetőséget.

:::image type="content" source="media/how-to-integrate-azure-signalr/event-subscription-1b.png" alt-text="Azure Portal: Event Grid esemény-előfizetés":::

Az *esemény-előfizetés létrehozása* lapon töltse ki a mezőket a következő módon (alapértelmezés szerint kitöltött mezők nincsenek megemlítve):
* *esemény-előfizetés részletei*  >  **Név**: adjon nevet az esemény-előfizetésnek.
* *VÉGPONT részletei*  >  **Végpont típusa**: válassza az *Azure Function* lehetőséget a menüpontok közül.
* *VÉGPONT részletei*  >  **Végpont**: nyomja meg a *végpont kiválasztása* hivatkozást. Ekkor megnyílik az *Azure-függvény kiválasztása* ablak:
    - Töltse ki az **előfizetést**, az **erőforráscsoportot**, a **Function app** és a Function (*szórás*) **függvényt** . Ezek némelyike az előfizetés kiválasztása után automatikusan feltölthető.
    - Nyomja **meg a megerősítés jóváhagyása elemet**.

:::image type="content" source="media/how-to-integrate-azure-signalr/create-event-subscription.png" alt-text="Azure Portal esemény-előfizetés létrehozásának nézete. A fenti mezők ki vannak töltve, a &quot;kijelölés megerősítése&quot; és a &quot;létrehozás&quot; gomb ki van emelve.":::

Az esemény- *előfizetés létrehozása* lapon kattintson a **create (létrehozás**) elemre.

## <a name="configure-and-run-the-web-app"></a>A webalkalmazás konfigurálása és futtatása

Ebben a szakaszban a művelet eredményét fogja látni. Először konfigurálja a **minta ügyfél-webalkalmazást** úgy, hogy az Ön által beállított Azure-szignáló folyamathoz kapcsolódjon. Ezután elindítja a **szimulált eszköz minta alkalmazást** , amely telemetria adatokat küld az Azure Digital Twins-példányon keresztül. Ezt követően megtekintheti a mintául szolgáló webalkalmazást, amely valós időben frissíti a mintául szolgáló webalkalmazást.

### <a name="configure-the-sample-client-web-app"></a>A minta-ügyfél webalkalmazásának konfigurálása

A **signaler Integration Web App-minta** beállítása a következő lépésekkel:
1. A Visual Studióval vagy bármely más szövegszerkesztővel nyissa meg a [*minta alkalmazások letöltése*](#download-the-sample-applications) szakaszban letöltött kibontott _**Azure_Digital_Twins_SignalR_integration_web_app_sample**_ mappát.

1. Nyissa meg a *src/App.js* fájlt, és cserélje le az URL-címet a `HubConnectionBuilder` korábban mentett **egyeztetési** függvény http-végpont URL-címére:

    ```javascript
        const hubConnection = new HubConnectionBuilder()
            .withUrl('<URL>')
            .build();
    ```
1. A Visual Studio *fejlesztői parancssorában* vagy bármelyik parancssori ablakban navigáljon a *Azure_Digital_Twins_SignalR_integration_web_app_sample \src* mappára. Futtassa a következő parancsot a függő csomópont-csomagok telepítéséhez:

    ```cmd
    npm install
    ```

Ezután állítsa be az engedélyeket a Function alkalmazásban a Azure Portalban:
1. A Azure Portal [Function apps](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) lapon válassza ki a Function app-példányt.
1. Görgessen le a példány menüben, és válassza a *CORS* lehetőséget. A CORS lapon adja hozzá a következőt `http://localhost:3000` engedélyezett forrásként az üres mezőbe való beírásával. Jelölje be a *hozzáférés-vezérlés – engedélyezés – hitelesítő adatok engedélyezése* jelölőnégyzetet, és kattintson a *Mentés gombra*.

    :::image type="content" source="media/how-to-integrate-azure-signalr/cors-setting-azure-function.png" alt-text="CORS-beállítás az Azure-függvényben":::

### <a name="run-the-device-simulator"></a>Az eszköz-szimulátor futtatása

A végpontok közötti oktatóanyag előfeltétele, hogy az eszközt egy IoT Hub és az Azure Digital Twins-példányon keresztül küldje el [az eszközön](tutorial-end-to-end.md#configure-and-run-the-simulation) .

Most mindössze annyit kell tennie, hogy elindítja a szimulátor projektet, amely a *Digital-Twins-Samples-master > DeviceSimulator > DeviceSimulator. SLN* címen található. Ha a Visual studiót használja, nyissa meg a projektet, majd futtassa ezt a gombot az eszköztáron:

:::image type="content" source="media/how-to-integrate-azure-signalr/start-button-simulator.png" alt-text="A Visual Studio Start gombja (DeviceSimulator projekt)":::

Ekkor megnyílik egy konzolablak szimulált hőmérsékleti telemetria üzenetek. Ezeket az Azure Digital Twins-példányon keresztül küldi el a rendszer, ahol ezeket az Azure functions és a Signaler is felvette.

Ebben a konzolban nem kell mást tennie, de a következő lépés végrehajtása közben ne futtassa.

### <a name="see-the-results"></a>Az eredmények megtekintése

Az eredmények működés közbeni megjelenítéséhez indítsa el a **signaler Integration Web App mintát**. Ezt a következő parancs futtatásával bármelyik konzolról elvégezheti *Azure_Digital_Twins_SignalR_integration_web_app_sample \src* helyen:

```cmd
npm start
```

Ekkor megnyílik a minta alkalmazást futtató böngészőablak, amely egy vizuális hőmérsékleti mérőműszert jelenít meg. Az alkalmazás futása után érdemes megtekinteni az telemetria az Azure Digital Twins-n keresztül propagált hőmérséklet-értékeit, amelyeket a webalkalmazás valós időben tükröz.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-webapp-output.png" alt-text="A mintául szolgáló ügyfél-webalkalmazás kivonata, amely egy vizuális hőmérsékleti mérőműszert mutat. A mért hőmérséklet 67,52":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az ebben a cikkben létrehozott erőforrásokra, a következő lépésekkel törölheti őket. 

Az Azure Cloud Shell vagy a helyi Azure CLI használatával törölheti az erőforráscsoport összes Azure-erőforrását az az [Group delete](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete) paranccsal. Az erőforráscsoport eltávolítása is el lesz távolítva...
* Az Azure Digital Twins-példány (a végpontok közötti oktatóanyagból)
* az IoT hub és a hub-eszköz regisztrálása (a végpontok közötti oktatóanyagból)
* az Event Grid-témakör és a hozzá tartozó előfizetések
* a Azure Functions alkalmazás, beleértve mindhárom funkciót és a hozzájuk kapcsolódó erőforrásokat, például a Storage-t
* Az Azure Signaler-példány

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Végezetül törölje a helyi gépre letöltött Project Sample-mappákat (*digital-twins-samples-master.zip* és *Azure_Digital_Twins_SignalR_integration_web_app_sample.zip*).

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben a Signaler Azure functions szolgáltatással állíthatja be az Azure Digital Twins telemetria-eseményeit egy minta ügyfélalkalmazás számára.

Következő lépésként Ismerkedjen meg az Azure Signaler szolgáltatással:
* [*Mi az az Azure SignalR Service?*](../azure-signalr/signalr-overview.md)

Vagy további információ az Azure Signaler szolgáltatás hitelesítéséről Azure Functions:
* [*Azure Signaler szolgáltatás hitelesítése*](../azure-signalr/signalr-tutorial-authenticate-azure-functions.md)
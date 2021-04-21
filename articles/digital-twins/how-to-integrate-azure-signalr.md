---
title: Integrálás az Azure SignalR szolgáltatással
titleSuffix: Azure Digital Twins
description: Tekintse meg, hogyan streamelhet Azure Digital Twins telemetriai adatokat az ügyfelek számára az Azure SignalR használatával
author: dejimarquis
ms.author: aymarqui
ms.date: 02/12/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: e8bdb843ab6304f2f38228f37d8709e4084ee52e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775330"
---
# <a name="integrate-azure-digital-twins-with-azure-signalr-service"></a>Integráció Azure Digital Twins Azure SignalR Service

Ebből a cikkből megtudhatja, hogyan integrálhatja a Azure Digital Twins a [Azure SignalR Service.](../azure-signalr/signalr-overview.md)

Az ebben a cikkben ismertetett megoldás lehetővé teszi a digitális iker telemetriaadatok leküldését a csatlakoztatott ügyfelekre, például egy weblapra vagy egy mobilalkalmazásba. Ennek eredményeképpen az ügyfelek valós idejű metrikákkal és állapottal frissülnek az IoT-eszközökről anélkül, hogy le kellene kérniük a kiszolgálót, vagy új HTTP-kéréseket kellene beküldenünk frissítésekért.

## <a name="prerequisites"></a>Előfeltételek

A folytatás előtt a következő előfeltételeket kell végrehajtania:

* Mielőtt integrálja a megoldást a Azure SignalR Service-val ebben a cikkben, el kell látnia az Azure Digital Twins [_**Oktatóanyag:**_](tutorial-end-to-end.md)Végpontok között megoldás csatlakoztatása oktatóanyagot, mert ez az útmutató cikk erre épül. Az oktatóanyag végigvezeti egy virtuális IoT Azure Digital Twins példány beállításán, amely egy virtuális IoT-eszközzel együtt aktiválja a digitális ikerfrissítéseket. A cikk ezeket a frissítéseket egy minta-webalkalmazáshoz csatlakoztatja a Azure SignalR Service.

* Az oktatóanyag következő értékeire lesz szüksége:
  - Event Grid-témakör
  - Erőforráscsoport
  - App Service/függvényalkalmazás neve
    
* A számítógépen [**Node.js**](https://nodejs.org/) kell telepítenie.

Emellett jelentkezzen be a fiókkal [](https://portal.azure.com/) Azure Portal Azure-fiókjával.

## <a name="solution-architecture"></a>Megoldásarchitektúra

Az alábbi útvonalon Azure SignalR Service Azure Digital Twins kell csatolnia. A diagram A, B és C szakasza a végpontok között oktatóanyag előfeltételének [architektúradiagramja.](tutorial-end-to-end.md) Ebben a cikkben a D szakaszt fogja összeépíteni a meglévő architektúrával.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-integration-topology.png" alt-text="Az Azure-szolgáltatások teljes forgatókönyvben való megtekintése. Az eszközről az IoT Hub-be áramló adatokat ábrázolja egy Azure-függvényen (B nyíl) keresztül egy Azure Digital Twins-példányba (A szakasz), majd az Event Grid-on keresztül egy másik Azure-függvénybe feldolgozás céljából (c nyíl). A D szakasz a C nyílból a &quot;broadcast&quot; (szórás) címkével Event Grid azure-függvényre mutató adatokat mutatja. A broadcast egy másik, &quot;negotiate&quot; címkével jelölt Azure-függvénysel kommunikál, és a &quot;broadcast&quot; és a &quot;negotiate&quot; is kommunikál a számítógép-eszközökkel." lightbox="media/how-to-integrate-azure-signalr/signalr-integration-topology.png":::

## <a name="download-the-sample-applications"></a>A mintaalkalmazások letöltése

Először töltse le a szükséges mintaalkalmazásokat. Az alábbiak közül mindkettőre szüksége lesz:
* [**Azure Digital Twins**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)teljes minták: Ez a minta egy *AdtSampleApp* alkalmazást tartalmaz, amely két [*Azure-függvényt*](tutorial-end-to-end.md)tartalmaz az adatok egy Azure Digital Twins-példányon való áthelyezéséhez (erről a forgatókönyvről részletesebben az Oktatóanyag: Teljes megoldás csatlakoztatása) című cikkből olvashat. Emellett tartalmaz egy *DeviceSimulator* mintaalkalmazást is, amely szimulál egy IoT-eszközt, és másodpercenként új hőmérsékletértéket hoz létre.
    - Ha még nem letöltötte a mintát az Előfeltételek című oktatóanyag [](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) [*részeként,*](#prerequisites)lépjen a mintahivatkozásra, és válassza a cím alatti *Kód* tallózása gombot. Ezzel a GitHub-adattárba fogja vinni a mintákat, amelyeket letölthet a következőként: *. A* ZIP-fájlhoz válassza *a Kód gombot,* majd *a ZIP letöltése gombot.*

        :::image type="content" source="media/includes/download-repo-zip.png" alt-text="A digital-twins-samples adattár megtekintése a GitHubon. A Kód gomb van kiválasztva, amely egy kis párbeszédpanelt hoz, ahol a ZIP letöltése gomb ki van emelve." lightbox="media/includes/download-repo-zip.png":::

    Ezzel letölti a mintaadattára egy példányát a gépére, **digital-twins-samples-master.zip.** Csomagolja ki a mappát.
* [**SignalR-integrációs webalkalmazás-minta:**](/samples/azure-samples/digitaltwins-signalr-webapp-sample/digital-twins-samples/)Ez egy minta React-webalkalmazás, amely Azure Digital Twins telemetriai adatokat fog Azure SignalR Service.
    -  Nyissa meg a mintahivatkozást, és ugyanezen letöltési folyamattal töltse le a minta másolatát a gépére, ahogy _**digitaltwins-signalr-webapp-sample-main.zip.**_ Csomagolja ki a mappát.

[!INCLUDE [Create instance](../azure-signalr/includes/signalr-quickstart-create-instance.md)]

Hagyja megnyitva a böngészőablakot a Azure Portal, mivel a következő szakaszban ismét használni fogja.

## <a name="publish-and-configure-the-azure-functions-app"></a>A Azure Functions közzététele és konfigurálása

Ebben a szakaszban két Azure-függvényt fog beállítani:
* **negotiate** – EGY HTTP-eseményindító függvény. A *SignalRConnectionInfo bemeneti kötéssel* hoz létre és ad vissza érvényes kapcsolati adatokat.
* **broadcast** – Egy [Event Grid](../event-grid/overview.md) trigger függvény. Az eseményrácson keresztül fogadja Azure Digital Twins telemetriai adatokat, és az előző lépésben létrehozott *SignalR-példány* kimeneti kötését használja az üzenet elküldését az összes csatlakoztatott ügyfélalkalmazásnak.

Indítsa Visual Studio alkalmazást (vagy egy másik választott kódszerkesztőt), és nyissa meg a kódmegoldást a *digital-twins-samples-master > ADTSampleApp mappában.* Ezután a következő lépésekkel hozza létre a függvényeket:

1. A *SampleFunctionsApp projektben* hozzon létre egy új C#-osztályt **SignalRFunctions.cs néven.**

1. Cserélje le a osztályfájl tartalmát a következő kódra:
    
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/signalRFunction.cs":::

1. Az Visual Studio *Csomagkezelő-konzolablakában* vagy a gép bármely parancsablakában keresse meg a *digital-twins-samples-master\AdtSampleApp\SampleFunctionsApp* mappát, és futtassa a következő parancsot a NuGet-csomag projekthez való `SignalRService` telepítéséhez:
    ```cmd
    dotnet add package Microsoft.Azure.WebJobs.Extensions.SignalRService --version 1.2.0
    ```

    Ezzel meg kell oldania a osztály függőségi problémáit.

1. Tegye közzé a függvényt az Azure-ban a Végpontok között megoldás csatlakoztatása oktatóanyag Alkalmazás közzététele *szakaszának lépéseit* követve. [](tutorial-end-to-end.md#publish-the-app) Közzéteheti ugyanazt az App Service-t/függvényalkalmazást, mint amit a végpontok között oktatóanyag előfeltételei között [használt,](#prerequisites)vagy létrehozhat egy újat– de a duplikálás minimalizálása érdekében ugyanezt a függvényt is használhatja. 

Ezután konfigurálja a függvényeket, hogy kommunikáljanak az Azure SignalR-példánysal. Először begyűjti a SignalR-példány kapcsolati sztringét, majd hozzáadja a függvényalkalmazás beállításaihoz.

1. A portál [Azure Portal](https://portal.azure.com/) keresse meg a SignalR-példány nevét a portál tetején található keresősávban. A megnyitáshoz válassza ki a példányt.
1. A **SignalR** szolgáltatáspéldány kapcsolati sztringek megtekintéséhez válassza a Példány menü Kulcsok parancsát.
1. Az elsődleges *kapcsolati* sztring másoláshoz válassza a Másolás ikont.

    :::image type="content" source="media/how-to-integrate-azure-signalr/signalr-keys.png" alt-text="Képernyőkép a Azure Portal SignalR-példány Kulcsok oldalának képernyőképe. Az Elsődleges KAPCSOLATI SZTRING melletti Másolás a vágólapra ikon ki van emelve." lightbox="media/how-to-integrate-azure-signalr/signalr-keys.png":::

1. Végül adja hozzá az Azure SignalR kapcsolati **sztringet** a függvény alkalmazásbeállításihoz az alábbi Azure CLI-paranccsal. Emellett cserélje le a helyőrzőket az erőforráscsoportra és az app service/function alkalmazás nevére az [oktatóanyag előfeltételeből.](how-to-integrate-azure-signalr.md#prerequisites) A parancs futtatható a következő [Azure Cloud Shell,](https://shell.azure.com)vagy helyileg, ha az Azure CLI telepítve [van a gépen:](/cli/azure/install-azure-cli)
 
    ```azurecli-interactive
    az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "AzureSignalRConnectionString=<your-Azure-SignalR-ConnectionString>"
    ```

    A parancs kimenete kinyomtatja az Azure-függvényhez beállított összes alkalmazásbeállítást. Keresse meg `AzureSignalRConnectionString` a lista alján, és ellenőrizze, hogy hozzá lett-e adva.

    :::image type="content" source="media/how-to-integrate-azure-signalr/output-app-setting.png" alt-text="A parancsablak kimenetének kivonata, amely egy &quot;AzureSignalRConnectionString&quot; nevű listaelemet mutat be":::

#### <a name="connect-the-function-to-event-grid"></a>A függvény csatlakoztatása Event Grid

Ezután iratkozzon fel *a szórásos* Azure-függvényre az oktatóanyag előfeltétele során létrehozott Event [Grid-témakörre.](how-to-integrate-azure-signalr.md#prerequisites)  Ez lehetővé teszi a telemetriai adatok áramlását a termosztát67 ikereszközről az event grid témakörben és a függvényben. Innen a függvény az összes ügyfélnek közvetítheti az adatokat.

Ehhez létre kell hoznia egy esemény-előfizetést **az** Event Grid-témakörből a szórásos  Azure-függvénybe végpontként.

A [Azure Portal](https://portal.azure.com/)keresse meg az Event Grid-témakört úgy, hogy rákeres a nevére a felső keresősávban. Válassza a *+ Esemény-előfizetés* lehetőséget.

:::image type="content" source="media/how-to-integrate-azure-signalr/event-subscription-1b.png" alt-text="Azure Portal: Event Grid előfizetése":::

Az *Esemény-előfizetés létrehozása lapon* töltse ki a mezőket a következőképpen (az alapértelmezés szerint kitöltött mezőket a rendszer nem említi):
* *ESEMÉNY-ELŐFIZETÉS RÉSZLETEI*  >  **Név:** Adjon nevet az esemény-előfizetésnek.
* *VÉGPONT RÉSZLETEI*  >  **Végpont típusa:** Válassza az *Azure-függvény lehetőséget* a menüpontok közül.
* *VÉGPONT RÉSZLETEI*  >  **Végpont:** Kattintson a *Végpont kiválasztása hivatkozásra.* Ez megnyit egy *Select Azure Function (Azure-függvény kiválasztása)* ablakot:
    - Töltse ki az **előfizetést,** az **erőforráscsoportot,** **a függvényalkalmazást** és a **függvényt** *(szórás).* Ezek némelyike automatikusan kitöltődik az előfizetés kiválasztása után.
    - Nyomja le **a Confirm Selection (Kijelölés megerősítése) jelölőt.**

:::image type="content" source="media/how-to-integrate-azure-signalr/create-event-subscription.png" alt-text="Azure Portal esemény-előfizetés létrehozásának nézete. A fenti mezők ki vannak töltve, és ki vannak emelve a &quot;Kijelölés megerősítése&quot; és a &quot;Létrehozás&quot; gombok.":::

Az Esemény-előfizetés *létrehozása oldalon* kattintson a **Létrehozás gombra.**

Ezen a ponton két esemény-előfizetést kell látnia a *Event Grid lapján.*

:::image type="content" source="media/how-to-integrate-azure-signalr/view-event-subscriptions.png" alt-text="Azure Portal event subscriptions (Eseményrács-témakör) lapon megtekintheti a két esemény-előfizetést." lightbox="media/how-to-integrate-azure-signalr/view-event-subscriptions.png":::

## <a name="configure-and-run-the-web-app"></a>A webalkalmazás konfigurálása és futtatása

Ebben a szakaszban az eredmény látható műveletben. Először konfigurálja a minta **ügyfél-webalkalmazást,** hogy csatlakozzon a beállított Azure SignalR-folyamathoz. Ezután elindítja a szimulált eszköz mintaalkalmazást, amely telemetriai adatokat küld a Azure Digital Twins példányon keresztül.  Ezt követően megtekintheti a minta-webalkalmazást, hogy valós időben megtekintsen egy szimulált eszközadatokat, amelyek frissítik a minta-webalkalmazást.

### <a name="configure-the-sample-client-web-app"></a>A minta ügyfél-webalkalmazás konfigurálása

A következő lépés a minta ügyfél-webalkalmazás konfigurálása. Először gyűjti össze a *negotiate* függvény **HTTP-végponti** URL-címét, majd ezzel konfigurálja az alkalmazáskódot a gépen.

1. A függvényalkalmazás Azure Portal [oldalára,](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) és válassza ki a függvényalkalmazást a listából. Az alkalmazás menüjében válassza a *Függvények lehetőséget,* majd válassza ki a *negotiate* függvényt.

    :::image type="content" source="media/how-to-integrate-azure-signalr/functions-negotiate.png" alt-text="Azure Portal a függvényalkalmazás nézetét, a menüben a &quot;Függvények&quot; elemet kiemelve. A függvények listája megjelenik az oldalon, és a &quot;negotiate&quot; függvény is ki van emelve.":::

1. Nyomja *le a Get function URL (Függvény URL-címének* le szolgáltatása) gombra, és másolja felfelé az értéket az /api használatával (ne tartalmazza az utolsó **/negotiate? ) _adatokat._** Ezt a következő lépésben fogja használni.

    :::image type="content" source="media/how-to-integrate-azure-signalr/get-function-url.png" alt-text="Azure Portal &quot;negotiate&quot; függvény nézetét. A &quot;Függvény URL-címének le szolgáltatása&quot; gomb ki van emelve, és az URL-cím része az elejétől az /api-n keresztül":::

1. A Visual Studio vagy bármely választott kódszerkesztővel nyissa meg a mintaalkalmazások letöltése szakaszban letöltött _**kicsomagolt digitaltwins-signalr-webapp-sample-main**_ [*mappát.*](#download-the-sample-applications)

1. Nyissa meg *az src/App.js* fájlt, és cserélje le a függvény URL-címét az előző lépésben mentett negotiate függvény `HubConnectionBuilder` HTTP-végponti URL-címére: 

    ```javascript
        const hubConnection = new HubConnectionBuilder()
            .withUrl('<Function URL>')
            .build();
    ```
1. Az Visual Studio *fejlesztői* parancssorában vagy a gép bármely parancsablakában keresse meg a *digitaltwins-signalr-webapp-sample-main\src* mappát. Futtassa a következő parancsot a függő csomópontcsomagok telepítéséhez:

    ```cmd
    npm install
    ```

Ezután állítsa be az engedélyeket a függvényalkalmazásban a Azure Portal:
1. A Azure Portal alkalmazás [lapján](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) válassza ki a függvényalkalmazás példányát.

1. Görgessen le a példány menüjében, és válassza a *CORS lehetőséget.* A CORS lapon adja hozzá az et engedélyezett forrásként az `http://localhost:3000` üres mezőben. Jelölje be az *Enable Access-Control-Allow-Credentials (Hozzáférés-vezérlés engedélyezése– Hitelesítő adatok engedélyezése) jelölőnégyzetet,* majd kattintson a Save (Mentés) *gombra.*

    :::image type="content" source="media/how-to-integrate-azure-signalr/cors-setting-azure-function.png" alt-text="CORS-beállítás az Azure-függvényben":::

### <a name="run-the-device-simulator"></a>Az eszközszimulátor futtatása

A végpontok között oktatóanyag előfeltétele [](tutorial-end-to-end.md#configure-and-run-the-simulation) során úgy konfigurálta az eszközszimulátort, hogy adatokat küldjön egy IoT Hub a saját Azure Digital Twins számára.

Most már csak el kell kezdenie a szimulátorprojektet, amely a *DeviceSimulator.sln digital-twins-samples-master > a DeviceSimulator.sln*> található. Ha a böngészőt Visual Studio, nyissa meg a projektet, majd futtassa az eszköztáron ezzel a gombbal:

:::image type="content" source="media/how-to-integrate-azure-signalr/start-button-simulator.png" alt-text="A Visual Studio gomb (DeviceSimulator projekt)":::

Megnyílik egy konzolablak, és megjeleníti a szimulált hőmérséklet telemetriai üzeneteket. Ezeket a virtuális Azure Digital Twins küldi el, ahol az Azure Functions és a SignalR is átveszi őket.

Ebben a konzolban semmi mást nem kell végrehajtania, de hagyja futni a következő lépés befejezése közben.

### <a name="see-the-results"></a>Az eredmények megtekintése

Az eredmények művelet közbeni eléréséhez indítsa el a **SignalR-integrációs webalkalmazás-mintát.** Ezt a *digitaltwins-signalr-webapp-sample-main\src* helyen található bármelyik konzolablakból meg lehet tenni a következő paranccsal:

```cmd
npm start
```

Ez megnyit egy böngészőablakot, amely a mintaalkalmazást futtatja, és megjeleníti a vizualizáció hőmérséklet-mérőműszerét. Az alkalmazás futása után meg kell kezdenie látni az eszközszimulátor hőmérsékleti telemetriai értékeit, amelyek a Azure Digital Twins keresztül propagálnak, és a webalkalmazás valós időben tükrözi őket.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-webapp-output.png" alt-text="A minta ügyfél-webalkalmazás kivonata, amely egy vizualizáció hőmérséklet-mérőműszerét mutatja be. A hőmérséklet 67,52":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a cikkben létrehozott erőforrásokra, az alábbi lépésekkel törölheti őket. 

A Azure Cloud Shell vagy helyi Azure CLI használatával egy erőforráscsoport összes Azure-erőforrását törölheti [az az group delete paranccsal.](/cli/azure/group#az_group_delete) Az erőforráscsoport eltávolítása a...
* Azure Digital Twins példány (a végpontok között oktatóanyagból)
* az IoT Hub és a hub eszközregisztrációja (a végpontok között oktatóanyagból)
* az Event Grid-témakör és a társított előfizetések
* az Azure Functions alkalmazást, beleértve mindhárom funkciót és a társított erőforrásokat, például a tárolást
* az Azure SignalR-példány

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Végül törölje a helyi gépre *letöltött* projektmintamappákat (digital-twins-samples-master.zip, *digitaltwins-signalr-webapp-sample-main.zip*, és azok kicsomagolt megfelelői).

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben beállít egy Azure Functions-függvényt a SignalR-sel, hogy Azure Digital Twins telemetriai eseményeket közvetítsen egy minta ügyfélalkalmazásnak.

A következő lépés az Azure SignalR Service:
* [*Mi az az Azure SignalR Service?*](../azure-signalr/signalr-overview.md)

Vagy további információ a Azure SignalR Service hitelesítésről Azure Functions:
* [*Azure SignalR Service hitelesítés*](../azure-signalr/signalr-tutorial-authenticate-azure-functions.md)
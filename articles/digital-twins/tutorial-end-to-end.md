---
title: Végpontok közötti megoldás összekötése
titleSuffix: Azure Digital Twins
description: Oktatóanyag egy teljes körű Azure digitális Twins-megoldás kiépítéséhez, amelyet az eszközön tárolt adatszolgáltatások vezéreltek.
author: baanders
ms.author: baanders
ms.date: 4/15/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: e824641f261fc920dd234ba8d4787fbd8ddffe48
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84613385"
---
# <a name="build-out-an-end-to-end-solution"></a>Hozzon létre egy végpontok közötti megoldást

A környezetből származó élő adatok teljes körű megoldásának beállításához az Azure Digital Twins-példányát más Azure-szolgáltatásokhoz is összeállíthatja az eszközök és adatok kezeléséhez.

Ebben az oktatóanyagban a következő lesz:...
* Azure digitális Twins-példány beállítása
* Ismerje meg a minta-építési forgatókönyvet, és hozza létre az előre megírt összetevőket
* Szimulált telemetria átirányítása egy [Azure functions](../azure-functions/functions-overview.md) alkalmazással egy [IoT hub](../iot-hub/about-iot-hub.md) eszközről a digitális dupla tulajdonságokba
* Változások propagálása a **Twin gráfon**keresztül, digitális kettős értesítések feldolgozásával Azure functions, végpontokkal és útvonalakkal

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

## <a name="get-started-with-the-building-scenario"></a>Ismerkedés az építési forgatókönyvvel

Az oktatóanyagban használt minta projekt egy, a padlót, egy termet és egy termosztátot tartalmazó valós **építési forgatókönyvet**képvisel. Ezek az összetevők digitálisan képviseltetik magukat egy Azure Digital Twins-példányban, amely ezután csatlakozik [IoT hubhoz](../iot-hub/about-iot-hub.md), [Event Gridhoz](../event-grid/overview.md)és két [Azure-függvényhez](../azure-functions/functions-overview.md) az adatok mozgatásának elősegítése érdekében.

Az alábbi ábra a teljes forgatókönyvet jelképezi. 

Először létre kell hoznia az Azure Digital Twins-példányt (a diagram a.**szakasza** ), majd be kell állítania a telemetria adatfolyamot a digitális ikrekbe (**B nyíl**), majd be kell állítania az adatpropagálást a Twin gráfon keresztül (**C nyíl**).

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="A teljes építési forgatókönyv ábrája. Az eszközről a IoT Hubba, egy Azure-függvénnyel (B. nyíl) egy Azure Digital Twins-példányra (A szakasz), Event Grid majd egy másik Azure-függvényre (A C. nyílra) átáramló adatok ábrázolása.":::

A forgatókönyvben való működéshez a korábban letöltött, előre megírt minta-alkalmazás összetevőit fogja használni.

Az alábbi, az építési forgatókönyv *AdtSampleApp* minta alkalmazás által megvalósított összetevők:
* Eszköz hitelesítése 
* A [.net (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core) használati példái (a *CommandLoop.cs*-ben találhatók)
* Konzol kezelőfelülete az Azure Digital Twins API meghívásához
* *SampleClientApp* – egy minta Azure digitális Twins-megoldás
* *SampleFunctionsApp* – egy Azure functions alkalmazás, amely a IoT hub és az Azure digitális Twins eseményeiből származó telemetria eredményeképpen frissíti az Azure digitális Twins-diagramot

A minta projekt egy interaktív engedélyezési összetevőt is tartalmaz. A projekt minden indításakor megnyílik egy böngészőablak, amely arra kéri, hogy jelentkezzen be az Azure-fiókjával.

### <a name="instantiate-the-pre-created-twin-graph"></a>Az előre létrehozott Twin gráf példányának létrehozása

Először a *AdtSampleApp* megoldást fogja használni a mintául szolgáló projektből a végpontok közötti forgatókönyv (**a szakasz**) Azure digitális Twins-darabjának létrehozásához:

:::image type="content" source="media/tutorial-end-to-end/building-scenario-a.png" alt-text="Egy részlet a teljes építési forgatókönyv grafikus kiemelési szakasza, az Azure Digital Twins-példány":::

A _**AdtE2ESample**_ -projektet megnyitó Visual Studio-ablakban futtassa a projektet az eszköztáron ezzel a gombbal:

:::image type="content" source="media/tutorial-end-to-end/start-button-sample.png" alt-text="A Visual Studio Start gombja (SampleClientApp projekt)":::

Megnyílik a konzol ablaka, majd hajtsa végre a hitelesítést, és várjon egy parancsot. Ebben a konzolban futtassa a következő parancsot a minta Azure digitális Twins-megoldás létrehozásához.

> [!IMPORTANT]
> Ha már rendelkezik digitális ikrekkel és kapcsolatokkal az Azure digitális Twins-példányában, a parancs futtatása törli őket, és lecseréli azokat az ikrekre és a minta kapcsolataira.

```cmd/sh
SetupBuildingScenario
```

A parancs kimenete egy megerősítő üzenet, amely három [**digitális ikreket**](concepts-twins-graph.md) hoz létre és csatlakozik az Azure Digital Twins-példányban: egy *floor1*nevű emeleten, egy *room21*nevű szobát és egy *thermostat67*nevű hőmérséklet-érzékelőt. Ezek a digitális ikrek a valós környezetekben létező entitásokat jelölik.

Ezek kapcsolaton keresztül kapcsolódnak a következő [**Twin gráfhoz**](concepts-twins-graph.md). A Twin gráf a környezet egészét jelöli, beleértve azt is, hogy az entitások hogyan hatnak egymással, és hogyan kapcsolódnak egymáshoz.

:::image type="content" source="media/tutorial-end-to-end/building-scenario-graph.png" alt-text="Egy gráf, amely azt mutatja, hogy a floor1 tartalmaz room21, és a room21 tartalmaz thermostat67" border="false":::

A következő parancs futtatásával ellenőrizheti a létrehozott ikreket, amely lekérdezi a csatlakoztatott Azure Digital Twins-példányt az összes olyan digitális ikrek esetében, amely tartalmazza a következőket:

```cmd/sh
Query
```

Ezután leállíthatja a projekt futtatását. A megoldást megnyithatja a Visual Studióban, de az oktatóanyag során továbbra is használhatja azt.

## <a name="set-up-the-sample-function-app"></a>A minta funkció alkalmazás beállítása

A következő lépés egy [Azure functions alkalmazás](../azure-functions/functions-overview.md) beállítása, amely az oktatóanyag során az adatfeldolgozáshoz lesz használva. A Function alkalmazás, a *SampleFunctionsApp*, két függvényt tartalmaz:
* *ProcessHubToDTEvents*: a bejövő IoT hubi adatfeldolgozás feldolgozása és az Azure digitális Twins frissítése ennek megfelelően
* *ProcessDTRoutedData*: a digitális ikrekből származó adatok feldolgozása, és ennek megfelelően frissíti a szülő ikreket az Azure digitális ikrekben

Ebben a szakaszban közzé fogja tenni az előre megírt Function alkalmazást, és gondoskodjon arról, hogy a Function alkalmazás hozzáférhessen az Azure digitális Twins-hoz, Azure Active Directory (HRE) identitás hozzárendelésével. Ezeknek a lépéseknek a végrehajtása lehetővé teszi, hogy az oktatóanyag további részében a functions alkalmazásban lévő függvények is használhatók legyenek. 

### <a name="publish-the-app"></a>Az alkalmazás közzététele

Vissza a Visual Studio-ablakba, ahol a _**AdtE2ESample**_ -projekt meg van nyitva, a *megoldáskezelő* ablaktáblán kattintson a jobb gombbal a _**SampleFunctionsApp**_ -projektfájl elemre, majd kattintson a **Közzététel**elemre.

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="Visual Studio: projekt közzététele":::

Az alábbi *közzétételi* oldalon hagyja meg az **Azure** alapértelmezett céljának kijelölését, és kattintson a *Tovább gombra*. 

Egy adott célpont esetében válassza az **Azure függvényalkalmazás (Windows)** lehetőséget, és kattintson a *Tovább gombra*.

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="Azure-függvény közzététele a Visual Studióban: konkrét cél":::

A *functions példány* lapon válassza ki az előfizetését. A mezőnek fel kell töltenie egy mezőt az előfizetésében található *erőforráscsoportok* alapján.

Válassza ki a példánya erőforráscsoportot, és nyomja meg az *+ új Azure-függvény létrehozása...* lehetőséget.

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="Azure-függvény közzététele a Visual Studióban: functions-példány (a Function app előtt)":::

A *függvényalkalmazás (Windows) – új ablak létrehozása* ablakban töltse ki a mezőket a következő módon:
* A **Name (név** ) annak a használati tervnek a neve, amelyet az Azure a Azure functions alkalmazás üzemeltetéséhez fog használni. Ez lesz a függvény alkalmazásának neve is, amely a tényleges függvényt tárolja. Megadhatja a saját egyedi értékét, vagy meghagyhatja az alapértelmezett javaslatot.
* Győződjön meg arról, hogy az **előfizetés** megfelel a használni kívánt előfizetésnek 
* Győződjön meg arról, hogy az **erőforráscsoport** a használni kívánt erőforráscsoporthoz van
* A **csomag típusának** elhagyása *felhasználásként*
* Válassza ki azt a **helyet** , amely megfelel az erőforráscsoport helyének.
* Hozzon létre egy új **Azure Storage** -erőforrást az *új.* .. hivatkozás használatával. Állítsa be úgy a helyet, hogy egyezzen az erőforráscsoporthoz, használja a többi alapértelmezett értéket, és nyomja meg az "OK" gombot.

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="Azure-függvény közzététele a Visual Studióban: függvényalkalmazás (Windows) – új létrehozása":::

Ezután válassza a **Létrehozás**lehetőséget.

Ekkor vissza kell térnie a *functions-példány* lapra, ahol az új Function alkalmazás már látható az erőforráscsoport alatt. Nyomja meg a *Finish (Befejezés*).

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="Azure-függvény közzététele a Visual Studióban: functions instance (a Function app után)":::

A Visual Studio fő ablakában megnyíló *Közzététel* ablaktáblán győződjön meg arról, hogy az összes információ helyesnek tűnik, majd válassza a **Közzététel**lehetőséget.

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="Azure-függvény közzététele a Visual Studióban: közzététel":::

> [!NOTE]
> A következőhöz hasonló előugró ablak jelenhet meg: :::image type="content" source="media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="Az Azure-függvény közzététele a Visual Studióban: hitelesítő adatok közzététele" border="false":::
> Ha igen, válassza **a kísérlet a hitelesítő adatok lekérése az Azure-ból és a** **Mentés**lehetőséget.

### <a name="assign-permissions-to-the-function-app"></a>Engedélyek kiosztása a Function alkalmazáshoz

Ha engedélyezni szeretné a Function app számára az Azure Digital Twins elérését, a következő lépés az alkalmazás beállításainak konfigurálása, a rendszer által felügyelt HRE-identitás kiosztása, valamint az identitás *tulajdonosi* engedélyeinek megadása az Azure Digital Twins-példányban.

Azure Cloud Shell a következő parancs használatával állítson be egy olyan alkalmazás-beállítást, amelyet a Function alkalmazás a digitális Twins-példányra való hivatkozáshoz fog használni.

```azurecli-interactive
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=<your-digital-twin-instance-URL>"
```

A rendszerfelügyelt identitás létrehozásához használja a következő parancsot. Jegyezze fel a kimenet *principalId* mezőjét.

```azurecli-interactive
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

A következő parancs *principalId* értékének használatával rendelje hozzá a Function alkalmazás identitását az Azure Digital Twins-példány *tulajdonosi* szerepköréhez:

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Owner (Preview)"
```

Ennek a parancsnak az eredménye a létrehozott szerepkör-hozzárendeléssel kapcsolatos információ. A Function app mostantól rendelkezik az Azure Digital Twins-példány eléréséhez szükséges engedélyekkel.

## <a name="process-simulated-telemetry-from-an-iot-hub-device"></a>Szimulált telemetria feldolgozása IoT Hub eszközről

Az Azure digitális Twins-gráfok célja, hogy a valódi eszközökről származó telemetria vezérelje. 

Ebben a lépésben egy szimulált termosztátos eszközt fog összekötni [IoT hub](../iot-hub/about-iot-hub.md) a digitális Twin-ben, amely az Azure digitális ikrekben van. Ahogy a szimulált eszköz telemetria bocsát ki, az adat a *ProcessHubToDTEvents* Azure-függvényen keresztül lesz átirányítva, amely egy megfelelő frissítést indít el a digitális Twin-ben. Így a digitális dupla naprakész állapotban marad a valós eszköz adatokkal. Az Azure Digital Twins-ben az események egyik helyről a másikra történő irányításának folyamatát [**útválasztási eseményeknek**](concepts-route-events.md)nevezzük.

Ez a végpontok közötti forgatókönyv (**B nyíl**) ezen részén történik:

:::image type="content" source="media/tutorial-end-to-end/building-scenario-b.png" alt-text="Egy részlet a teljes építési forgatókönyvből, a "B" nyilat kiemelve, az Azure Digital Twins előtti elemek: az eszköz, a IoT Hub és az első Azure-függvény":::

Az eszköz kapcsolódásának beállításához a következő műveleteket kell végrehajtania:
1. Hozzon létre egy IoT hub-t, amely a szimulált eszközt felügyeli
2. Az IoT hub és a megfelelő Azure-függvény összekötése egy esemény-előfizetés beállításával
3. A szimulált eszköz regisztrálása az IoT hub-ban
4. A szimulált eszköz futtatása és a telemetria előállítása
5. Az Azure digitális Twins lekérdezése az élő eredmények megtekintéséhez

### <a name="create-an-iot-hub-instance"></a>IoT Hub példány létrehozása

Az Azure Digital Twins úgy lett kialakítva, hogy [IoT hub](../iot-hub/about-iot-hub.md), egy Azure-szolgáltatást, amely az eszközök és az adatkezelési szolgáltatások mellett működik. Ebben a lépésben egy IoT hubot fog beállítani, amely az oktatóanyagban felügyeli a minta eszközt.

Azure Cloud Shell a paranccsal hozzon létre egy új IoT hubot:

```azurecli-interactive
az iot hub create --name <name-for-your-IoT-hub> -g <your-resource-group> --sku S1
```

A parancs kimenete a létrehozott IoT hub adatait ismerteti.

Mentse az IoT hub-ba kapott nevet. Erre később még szüksége lesz.

### <a name="connect-the-iot-hub-to-the-azure-function"></a>Az IoT hub és az Azure-függvény összekötése

Ezután az IoT hub csatlakoztatása a *ProcessHubToDTEvents* Azure-függvényhez a korábban közzétett Function alkalmazásban, hogy az adatok az eszközről IoT hub a függvény használatával, amely frissíti az Azure Digital Twins-t.

Ehhez létre kell hoznia egy esemény- **előfizetést** a IoT hub, és az Azure-függvényt végpontként kell létrehoznia. Ez a függvény a IoT Hubban megjelenő eseményekre vonatkozik.

A [Azure Portal](https://portal.azure.com/)navigáljon az újonnan létrehozott IoT hubhoz a felső keresési sávban megkeresve a nevét. Válassza az *események* lehetőséget a központi menüben, majd válassza az *+ esemény-előfizetés*lehetőséget.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1.png" alt-text="Azure Portal: IoT Hub esemény-előfizetés":::

Ekkor megjelenik az esemény- *előfizetés létrehozása* lap.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-2.png" alt-text="Azure Portal: esemény-előfizetés létrehozása":::

Töltse ki a mezőket a következő módon:
* *esemény-előfizetés részletei*  >  **Név**: adjon nevet az esemény-előfizetésnek.
* *események típusai*  >  **Szűrés az események típusai**között: válassza az *eszközök telemetria* lehetőséget a menüpontok közül.
* *VÉGPONT részletei*  >  **Végpont típusa**: válassza az *Azure Function* lehetőséget a menüpontok közül.
* *VÉGPONT részletei*  >  **Végpont**: nyomja meg a *végpont kiválasztása* hivatkozást. Ekkor megnyílik az *Azure-függvény kiválasztása* ablak: :::image type="content" source="media/tutorial-end-to-end/event-subscription-3.png" alt-text="Azure Portal esemény-előfizetés: válassza az Azure-függvény lehetőséget" border="false"::: .
    - Töltse ki az **előfizetést**, az **erőforráscsoportot**, a **Function app** és a Function (*ProcessHubToDTEvents*) **függvényt** . Ezek némelyike az előfizetés kiválasztása után automatikusan feltölthető.
    - Nyomja **meg a megerősítés jóváhagyása elemet**.

Az esemény- *előfizetés létrehozása* lapon kattintson a **create (létrehozás**) elemre.

### <a name="register-the-simulated-device-with-iot-hub"></a>A szimulált eszköz regisztrálása IoT Hub 

Ez a szakasz létrehoz egy eszközt a IoT Hub a *THERMOSTAT67*azonosítóval. A szimulált eszköz ehhez csatlakozik, és így az telemetria-események az eszközről a IoT Hubba kerülnek, ahol az előző lépésből származó előfizetett Azure-függvény figyel, készen áll az események felvételére és a feldolgozás folytatására.

A Azure Cloud Shellban hozzon létre egy eszközt a IoT Hubban a következő paranccsal:

```azurecli-interactive
az iot hub device-identity create --device-id thermostat67 --hub-name <your-IoT-hub-name> -g <your-resource-group>
```

A kimenet a létrehozott eszközzel kapcsolatos információ.

### <a name="configure-and-run-the-simulation"></a>A szimuláció konfigurálása és futtatása

Ezután konfigurálja az eszköz-szimulátort, hogy az adatküldés a IoT Hub-példányba.

Először az *IoT hub-kapcsolatok karakterláncának* beszerzése ezzel a paranccsal:

```azurecli
az iot hub show-connection-string -n <your-IoT-hub-name>
```

Ezután szerezze be az *eszköz-kapcsolatok karakterláncát* a következő paranccsal:

```azurecli
az iot hub device-identity show-connection-string --device-id thermostat67 --hub-name <your-IoT-hub-name>
```

Ezeket az értékeket a helyi projekt eszköz-szimulátor kódjába kell csatlakoztatnia a szimulátornak a IoT hub és az IoT hub-eszközhöz való csatlakoztatásához.

Az új Visual Studio-ablakban nyissa meg a (a letöltött megoldás mappából) _> **DeviceSimulator. SLN**nevű eszközt_.

>[!NOTE]
> Ekkor két Visual Studio-Windowsnak kell lennie, egyet a _**DeviceSimulator. SLN**_ és egy korábbi verziójával a _**AdtE2ESample. SLN**_.

Az új Visual Studio ablak *megoldáskezelő* paneljén válassza ki a _DeviceSimulator/**AzureIoTHub.cs** _ elemet a szerkesztési ablakban való megnyitásához. Módosítsa a következő kapcsolódási karakterlánc-értékeket a fent összegyűjtött értékekre:

```csharp
connectionString = <Iot-hub-connection-string>
deviceConnectionString = <device-connection-string>
```

Mentse a fájlt.

Most, hogy megtekintse a beállított adatszimulálás eredményét, futtassa a **DeviceSimulator** projektet ezzel a gombbal az eszköztáron:

:::image type="content" source="media/tutorial-end-to-end/start-button-simulator.png" alt-text="A Visual Studio Start gombja (DeviceSimulator projekt)":::

Ekkor megnyílik egy konzolablak szimulált hőmérsékleti telemetria üzenetek. Ezeket a rendszer elküldje IoT Hubba, ahol az Azure-függvény felveszi és feldolgozza azokat.

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="Az eszköz-szimulátor konzoljának kimenete, amely az elküldött hőmérsékleti telemetria jeleníti meg":::

Ebben a konzolban nem kell mást tennie, de a következő lépések elvégzése után ne futtassa.

### <a name="see-the-results-in-azure-digital-twins"></a>Az eredmények megtekintése az Azure Digital Twinsban

A korábban közzétett *ProcessHubToDTEvents* függvény figyeli a IoT hub az adatát, és meghívja az Azure Digital Twins API-t, hogy frissítse a *hőmérséklet* tulajdonságot a *thermostat67* Twin-ben.

Ha szeretné megtekinteni az Azure Digital ikrek oldalának adatait, nyissa meg a Visual Studio ablakát, ahol a _**AdtE2ESample**_ projekt meg van nyitva, és futtassa a projektet.

A megnyíló Project Console ablakban futtassa a következő parancsot a digitális kettős *thermostat67*által jelentett hőmérsékletek lekéréséhez:

```cmd
ObserveProperties thermostat67 Temperature
```

Az *Azure Digital Twins-példány* élő frissített hőmérsékletét 10 másodpercenként kell megtekinteni a konzolon.

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry.png" alt-text="Konzol kimenete, amely a digitális Twin thermostat67 származó hőmérsékleti üzenetek naplóját jeleníti meg":::

Miután ellenőrizte, hogy ez sikeresen működik, leállíthatja mindkét projekt futtatását. Tartsa nyitva a Visual Studio ablakait, ahogy az oktatóanyag további részében is használni fogja őket.

## <a name="propagate-azure-digital-twins-events-through-the-graph"></a>Azure digitális Twins-események propagálása a gráfon keresztül

Ebből az oktatóanyagból megtudhatta, hogyan frissíthetik az Azure digitális Twins a külső eszközökről származó adatokból. Következő lépésként láthatja, hogy az egyik digitális iker módosítása hogyan propagálható az Azure Digital Twins gráfon – más szóval, hogyan frissítheti az ikreket a szolgáltatás belső adatainak használatával.

Ehhez használja a *ProcessDTRoutedData* Azure-függvényt, hogy frissítsen egy különálló *szobát* , amikor a csatlakoztatott *termosztátot* tartalmazó Twin frissítés frissül. Ez a végpontok közötti forgatókönyv (**C nyíl**) ezen részében fordul elő:

:::image type="content" source="media/tutorial-end-to-end/building-scenario-c.png" alt-text="Egy részlet a teljes építési forgatókönyvből, grafikus kiemeléssel C, az Azure Digital Twins utáni elemek: a Event Grid és a második Azure-függvény":::

Az alábbi műveleteket hajthatja végre az adatfolyamatok beállításához:
1. Hozzon létre egy Azure digitális Twins-végpontot, amely összekapcsolja a példányt Event Grid
2. Hozzon létre egy útvonalat az Azure Digital Ikreken belül, hogy a Twin Property változási eseményeit elküldi a végpontnak.
3. Helyezzen üzembe egy Azure Functions alkalmazást, amely a végponton figyeli ( [Event Grid](../event-grid/overview.md)), és ennek megfelelően frissíti a többi ikreket
4. Futtassa a szimulált eszközt, és az Azure digitális Twins lekérdezésével tekintse meg az élő eredményeket

### <a name="set-up-endpoint"></a>Végpont beállítása

A [Event Grid](../event-grid/overview.md) egy Azure-szolgáltatás, amely segít az Azure-szolgáltatásokból érkező események átirányításában és továbbításában az Azure-on belüli más helyekre. Létrehozhat egy [Event Grid-témakört](../event-grid/concepts.md) bizonyos események forrásból való összegyűjtéséhez, majd az előfizetők megtekinthetik a témakört, hogy megkapják az áthaladó eseményeket.

Ebben a szakaszban létrehoz egy Event Grid-témakört, majd létrehoz egy végpontot az Azure Digital Twins-n belül, amely az adott témakörre mutat (eseményeket küld). 

Azure Cloud Shell a következő parancs futtatásával hozzon létre egy Event Grid-témakört:

```azurecli-interactive
az eventgrid topic create -g <your-resource-group> --name <name-for-your-event-grid-topic> -l <region>
```

> [!TIP]
> Futtassa a következő parancsot az Azure-régiók azon neveinek listájához, amelyek átadhatók az Azure CLI parancsaihoz:
> ```azurecli
> az account list-locations -o table
> ```

A parancs kimenete az Ön által létrehozott Event Grid-témakörre vonatkozó információkat tartalmazza.

Következő lépésként hozzon létre egy Azure digitális Twins-végpontot, amely az Event Grid-témakörre mutat. Használja az alábbi parancsot, és szükség szerint töltse ki a helyőrző mezőket:

```azurecli
az dt endpoint create eventgrid --dt-name <your-Azure-Digital-Twins-instance> --eventgrid-resource-group <your-resource-group> --eventgrid-topic <your-event-grid-topic> --endpoint-name <name-for-your-Azure-Digital-Twins-endpoint>
```

A parancs kimenete a létrehozott végpontra vonatkozó információkat jeleníti meg.

Azt is ellenőrizheti, hogy a végpont létrehozása sikeres volt-e a következő parancs futtatásával, hogy lekérdezze az Azure Digital Twins-példányt ehhez a végponthoz:

```azurecli
az dt endpoint show --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> 
```

Keresse meg a `provisioningState` kimenetben a mezőt, és ellenőrizze, hogy az érték "sikeres".

:::image type="content" source="media/tutorial-end-to-end/output-endpoints.png" alt-text="A végpont-lekérdezés eredménye, amely a sikeres provisioningState rendelkező végpontot mutatja":::

Mentse az Event Grid-témakörbe és az Azure Digital Twins-végpontba kapott neveket. Ezeket később fogja használni.

### <a name="set-up-route"></a>Útvonal beállítása

Következő lépésként hozzon létre egy Azure digitális Twins-útvonalat, amely az imént létrehozott Azure digitális Twins-végpontnak küld eseményeket.

```azurecli
az dt route create --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> --route-name <name-for-your-Azure-Digital-Twins-route>
```

A parancs kimenete néhány információ a létrehozott útvonalról.

#### <a name="connect-the-function-to-event-grid"></a>A függvény összekötése Event Grid

Ezután fizessen elő a *ProcessDTRoutedData* Azure-függvényt a korábban létrehozott Event Grid-témakörre, hogy a telemetria-adatok az Event Grid-témakörben a függvénybe *thermostat67* , amely visszakerül az Azure digitális Twins-be, és ennek megfelelően frissíti a *room21* .

Ehhez létre kell hoznia egy Event Grid- **előfizetést** az Event Grid-témakörből a *ProcessDTRoutedData* Azure-függvényhez végpontként.

A [Azure Portalban](https://portal.azure.com/)keresse meg az Event Grid-témakört úgy, hogy a felső keresési sávban keresi a nevét. Válassza a *+ Esemény-előfizetés* lehetőséget.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1b.png" alt-text="Azure Portal: Event Grid esemény-előfizetés":::

Az esemény-előfizetés létrehozásának lépései hasonlóak ahhoz, amikor előfizetett az első Azure-függvényre, hogy IoT Hub az oktatóanyag korábbi részében. A különbség az, hogy ezúttal nem kell megadnia az *eszköz telemetria* a figyelni kívánt esemény típusaként, és egy másik Azure-függvényhez fog csatlakozni.

Az előfizetés lépései újra:

Az *esemény-előfizetés létrehozása* lapon töltse ki a mezőket a következő módon:
* *esemény-előfizetés részletei*  >  **Név**: adjon nevet az esemény-előfizetésnek.
* *VÉGPONT részletei*  >  **Végpont típusa**: válassza az *Azure Function* lehetőséget a menüpontok közül.
* *VÉGPONT részletei*  >  **Végpont**: nyomja meg a *végpont kiválasztása* hivatkozást. Ekkor megnyílik az *Azure-függvény kiválasztása* ablak:
    - Töltse ki az **előfizetést**, az **erőforráscsoportot**, a **Function app** és a Function (*ProcessDTRoutedData*) **függvényt** . Ezek némelyike az előfizetés kiválasztása után automatikusan feltölthető.
    - Nyomja **meg a megerősítés jóváhagyása elemet**.

Az esemény- *előfizetés létrehozása* lapon kattintson a **create (létrehozás**) elemre.

### <a name="run-the-simulation-and-see-the-results"></a>Futtassa a szimulációt, és tekintse meg az eredményeket.

Most már futtathatja az Eszközkezelőt, hogy elindítsa a beállított új esemény folyamatát. Nyissa meg a Visual Studio ablakát, ahol a _**DeviceSimulator**_ projekt meg van nyitva, és futtassa a projektet.

Ahogy korábban futtatta az eszköz-szimulátort, megnyílik egy konzolablak, amely szimulált hőmérsékletű telemetria-üzeneteket jelenít meg. Ezek az események a korábban beállított folyamaton keresztül frissítik a *thermostat67* , majd a közelmúltban beállított folyamaton keresztül frissítik a *room21* Twin-et, hogy azok megfeleljenek.

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="Az eszköz-szimulátor konzoljának kimenete, amely az elküldött hőmérsékleti telemetria jeleníti meg":::

Ebben a konzolban nem kell mást tennie, de a következő lépések elvégzése után ne futtassa.

Ha szeretné megtekinteni az Azure Digital ikrek oldalának adatait, lépjen a Visual Studio ablakába, ahol a _**AdtE2ESample**_ -projekt meg van nyitva, és futtassa a projektet.

A megnyíló Project Console ablakban futtassa a következő **parancsot a digitális** kettős *thermostat67* és a digitális kettős *room21*által jelentett hőmérsékletek lekéréséhez.

```cmd
ObserveProperties thermostat67 Temperature room21 Temperature
```

Az *Azure Digital Twins-példány* élő frissített hőmérsékletét 10 másodpercenként kell megtekinteni a konzolon. Figyelje meg, hogy a *room21* hőmérséklete frissül, hogy megfeleljen a *thermostat67*frissítéseinek.

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry-b.png" alt-text="Konzol kimenete, amely a hőmérsékleti üzenetek naplóját mutatja egy termosztátból és egy helyiségből":::

Miután ellenőrizte, hogy ez sikeresen működik, leállíthatja mindkét projekt futtatását. Bezárhatja a Visual Studio Windowst is, mivel az oktatóanyag már elkészült.

## <a name="review"></a>Áttekintés

Itt látható az oktatóanyagban kiépített forgatókönyv áttekintése.

1. Az Azure digitális Twins-példányok digitális a padlót, a termet és a termosztátot jelöli (amelyet az alábbi ábra a **szakasza tartalmaz** )
2. A szimulált eszköz telemetria a rendszer elküldi IoT Hubba, ahol a *ProcessHubToDTEvents* Azure-függvény figyeli a telemetria eseményeket. Az *ProcessHubToDTEvents* Azure-függvény az ezekben az eseményekben található információk alapján állítja be a *hőmérséklet* tulajdonságot a *thermostat67* (a diagramban a**B nyíl** ).
3. Az Azure Digital Twins-beli tulajdonság-változási események egy Event Grid-témakörbe vannak irányítva, ahol a *ProcessDTRoutedData* Azure-függvény figyeli az eseményeket. Az *ProcessDTRoutedData* Azure-függvény az ezekben az eseményekben található információk használatával állítja be a *hőmérséklet* tulajdonságot a *room21* (a diagramon a**C. nyíl** ).

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="A teljes építési forgatókönyv ábrája. Az eszközről a IoT Hubba, egy Azure-függvénnyel (B. nyíl) egy Azure Digital Twins-példányra (A szakasz), Event Grid majd egy másik Azure-függvényre (A C. nyílra) átáramló adatok ábrázolása.":::

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha már nincs szüksége az oktatóanyagban létrehozott erőforrásokra, a következő lépésekkel törölheti őket. 

A Azure Cloud Shell használatával törölheti az erőforráscsoport összes Azure-erőforrását az az [Group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) paranccsal. Ezzel eltávolítja az erőforráscsoportot; Az Azure Digital Twins-példány; az IoT hub és a hub-eszköz regisztrációja; az Event Grid-témakör és a hozzá tartozó előfizetések; és mindkét Azure Functions alkalmazás, beleértve a kapcsolódó erőforrásokat, például a tárolást.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Ezután törölje az ügyfélalkalmazás számára a következő paranccsal létrehozott HRE-regisztrációs alkalmazást:

```azurecli
az ad app delete --id <your-application-ID>
```

Végezetül törölje a helyi gépről letöltött Project Sample mappát.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy teljes körű forgatókönyvet, amely bemutatja, hogy az Azure digitális ikrek az élő eszközön tárolt adatmennyiségen alapulnak.

Ezután tekintse meg a koncepció dokumentációját, ahol további információt talál az oktatóanyagban használt elemekről:
* [Fogalmak: egyéni modellek](concepts-models.md)

Vagy részletesebben is megtudhatja az oktatóanyag folyamatait az útmutató cikkeinek elindításával:
* [Útmutató: az Azure digitális Twins parancssori felületének használata](how-to-use-cli.md)

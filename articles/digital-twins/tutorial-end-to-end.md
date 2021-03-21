---
title: 'Oktatóanyag: végpontok közötti megoldás összekötése'
titleSuffix: Azure Digital Twins
description: Oktatóanyag egy teljes körű Azure digitális Twins-megoldás kiépítéséhez, amelyet az eszközön tárolt adatszolgáltatások vezéreltek.
author: baanders
ms.author: baanders
ms.date: 4/15/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: b7883d6c541558e26793f94e37014a20b14d761e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104577254"
---
# <a name="tutorial-build-out-an-end-to-end-solution"></a>Oktatóanyag: végpontok közötti megoldás kiépítése

A környezetből származó élő adatok teljes körű megoldásának beállításához az Azure Digital Twins-példányát más Azure-szolgáltatásokhoz is összeállíthatja az eszközök és adatok kezeléséhez.

Ebben az oktatóanyagban a következő lesz:...
> [!div class="checklist"]
> * Azure digitális Twins-példány beállítása
> * Ismerje meg a minta-építési forgatókönyvet, és hozza létre az előre megírt összetevőket
> * Szimulált telemetria átirányítása egy [Azure functions](../azure-functions/functions-overview.md) alkalmazással egy [IoT hub](../iot-hub/about-iot-hub.md) eszközről a digitális dupla tulajdonságokba
> * Változások propagálása a **Twin gráfon** keresztül, digitális kettős értesítések feldolgozásával Azure functions, végpontokkal és útvonalakkal

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="set-up-cloud-shell-session"></a>Cloud Shell munkamenet beállítása
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

## <a name="get-started-with-the-building-scenario"></a>Ismerkedés az építési forgatókönyvvel

Az oktatóanyagban használt minta projekt egy, a padlót, egy termet és egy termosztátot tartalmazó valós **építési forgatókönyvet** képvisel. Ezek az összetevők digitálisan képviseltetik magukat egy Azure Digital Twins-példányban, amely ezután csatlakozik [IoT hubhoz](../iot-hub/about-iot-hub.md), [Event Gridhoz](../event-grid/overview.md)és két [Azure-függvényhez](../azure-functions/functions-overview.md) az adatok mozgatásának elősegítése érdekében.

Az alábbi ábra a teljes forgatókönyvet jelképezi. 

Először létre kell hoznia az Azure Digital Twins-példányt (a diagram a.**szakasza** ), majd be kell állítania a telemetria adatfolyamot a digitális ikrekbe (**B nyíl**), majd be kell állítania az adatpropagálást a Twin gráfon keresztül (**C nyíl**).

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="A teljes építési forgatókönyv ábrája. Az eszközről a IoT Hubba, egy Azure-függvénnyel (B. nyíl) egy Azure Digital Twins-példányra (A szakasz), Event Grid majd egy másik Azure-függvényre (A C. nyílra) átáramló adatok ábrázolása.":::

A forgatókönyvben való működéshez a korábban letöltött, előre megírt minta-alkalmazás összetevőit fogja használni.

Az alábbi, az építési forgatókönyv *AdtSampleApp* minta alkalmazás által megvalósított összetevők:
* Eszköz hitelesítése 
* [.Net (C#) SDK-](/dotnet/api/overview/azure/digitaltwins/client) használati példák (a *CommandLoop. cs* csomagban található)
* Konzol kezelőfelülete az Azure Digital Twins API meghívásához
* *SampleClientApp* – egy minta Azure digitális Twins-megoldás
* *SampleFunctionsApp* – egy Azure functions alkalmazás, amely a IoT hub és az Azure digitális Twins eseményeiből származó telemetria eredményeképpen frissíti az Azure digitális Twins-diagramot

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

A parancs kimenete egy megerősítő üzenet, amely három [**digitális ikreket**](concepts-twins-graph.md) hoz létre és csatlakozik az Azure Digital Twins-példányban: egy *floor1* nevű emeleten, egy *room21* nevű szobát és egy *thermostat67* nevű hőmérséklet-érzékelőt. Ezek a digitális ikrek a valós környezetekben létező entitásokat jelölik.

Ezek kapcsolaton keresztül kapcsolódnak a következő [**Twin gráfhoz**](concepts-twins-graph.md). A Twin gráf a környezet egészét jelöli, beleértve azt is, hogy az entitások hogyan hatnak egymással, és hogyan kapcsolódnak egymáshoz.

:::image type="content" source="media/tutorial-end-to-end/building-scenario-graph.png" alt-text="Egy gráf, amely azt mutatja, hogy a floor1 tartalmaz room21, és a room21 tartalmaz thermostat67" border="false":::

A következő parancs futtatásával ellenőrizheti a létrehozott ikreket, amely lekérdezi a csatlakoztatott Azure Digital Twins-példányt az összes olyan digitális ikrek esetében, amely tartalmazza a következőket:

```cmd/sh
Query
```

>[!TIP]
> Ez az egyszerűsített módszer a _**AdtE2ESample**_ projekt részeként van megadva. A mintakód kontextusán kívül bármikor, a [lekérdezési API](/rest/api/digital-twins/dataplane/query) -k vagy a [CLI-parancsok](how-to-use-cli.md)használatával lekérdezheti a példányban lévő összes ikreket.
>
> Itt látható a teljes lekérdezési törzs az összes digitális ikrek számára a példányban:
> 
> :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="GetAllTwins":::

Ezután leállíthatja a projekt futtatását. A megoldást megnyithatja a Visual Studióban, de az oktatóanyag során továbbra is használhatja azt.

## <a name="set-up-the-sample-function-app"></a>A minta funkció alkalmazás beállítása

A következő lépés egy [Azure functions alkalmazás](../azure-functions/functions-overview.md) beállítása, amely az oktatóanyag során az adatfeldolgozáshoz lesz használva. A Function alkalmazás, a *SampleFunctionsApp*, két függvényt tartalmaz:
* *ProcessHubToDTEvents*: a bejövő IoT hubi adatfeldolgozás feldolgozása és az Azure digitális Twins frissítése ennek megfelelően
* *ProcessDTRoutedData*: a digitális ikrekből származó adatok feldolgozása, és ennek megfelelően frissíti a szülő ikreket az Azure digitális ikrekben

Ebben a szakaszban közzé fogja tenni az előre megírt Function alkalmazást, és gondoskodjon arról, hogy a Function alkalmazás hozzáférhessen az Azure digitális Twins-hoz, Azure Active Directory (Azure AD) identitás hozzárendelésével. Ezeknek a lépéseknek a végrehajtása lehetővé teszi, hogy az oktatóanyag további részében a functions alkalmazásban lévő függvények is használhatók legyenek. 

Vissza a Visual Studio-ablakba, ahol a _**AdtE2ESample**_ -projekt meg van nyitva, a Function alkalmazás a _**SampleFunctionsApp**_ projektfájl alatt található. A *megoldáskezelő* ablaktáblán tekinthető meg.

### <a name="update-dependencies"></a>Frissítési függőségek

Az alkalmazás közzététele előtt érdemes meggyőződni arról, hogy a függőségek naprakészek, így biztos lehet benne, hogy rendelkezik az összes mellékelt csomag legújabb verziójával.

A *megoldáskezelő* ablaktáblán bontsa ki a _**SampleFunctionsApp** > függőségek_ elemet. Kattintson a jobb gombbal a *csomagok* elemre, és válassza a *NuGet-csomagok kezelése...* lehetőséget.

:::image type="content" source="media/tutorial-end-to-end/update-dependencies-1.png" alt-text="Visual Studio: NuGet-csomagok kezelése a SampleFunctionsApp projekthez" border="false":::

Ekkor megnyílik a NuGet Package Manager. Válassza a *frissítések* fület, és ha vannak olyan csomagok, amelyeket frissíteni szeretne, jelölje be a jelölőnégyzetet az *összes csomag kiválasztásához*. Ezután nyomja meg a *frissítés*.

:::image type="content" source="media/tutorial-end-to-end/update-dependencies-2.png" alt-text="Visual Studio: a NuGet csomagkezelő összes csomagjának frissítésének kiválasztása":::

### <a name="publish-the-app"></a>Az alkalmazás közzététele

Vissza a Visual Studio-ablakba, ahol a _**AdtE2ESample**_ -projekt meg van nyitva, keresse meg a _**SampleFunctionsApp**_ -projektet a *megoldáskezelő* ablaktáblán.

[!INCLUDE [digital-twins-publish-azure-function.md](../../includes/digital-twins-publish-azure-function.md)]

Ahhoz, hogy a Function alkalmazás hozzáférhessen az Azure Digital Twins-hoz, rendelkeznie kell az Azure Digital Twins-példány és a példány állomásneve eléréséhez szükséges engedélyekkel. Ezeket a következőt kell konfigurálnia.

### <a name="configure-permissions-for-the-function-app"></a>A Function alkalmazás engedélyeinek konfigurálása

Két beállítást kell beállítani a Function alkalmazás számára az Azure Digital Twins-példányhoz való hozzáféréshez. Ezeket a [Azure Cloud Shell](https://shell.azure.com)parancsain keresztül is elvégezheti. 

#### <a name="assign-access-role"></a>Hozzáférési szerepkör kiosztása

Az első beállítás azt adja meg, hogy az Azure Digital Twins-beli **adattulajdonosi** szerepkör az Azure Digital Twins-példányban hogyan használható. Ez a szerepkör minden olyan felhasználóhoz vagy függvényhez szükséges, amely sok adatsík-tevékenységet szeretne végrehajtani a példányon. A biztonsággal és a szerepkör-hozzárendelésekkel kapcsolatos további információkért tekintse meg a [*következő fogalmakat: az Azure Digital Twins-megoldások biztonsága*](concepts-security.md). 

1. A következő parancs használatával megtekintheti a függvény rendszer által felügyelt identitásának részleteit. Jegyezze fel a kimenet **principalId** mezőjét.

    ```azurecli-interactive 
    az functionapp identity show -g <your-resource-group> -n <your-App-Service-(function-app)-name> 
    ```

    >[!NOTE]
    > Ha az eredmény üres az identitás részleteinek megjelenítése helyett, hozzon létre egy új, rendszer által felügyelt identitást a függvényhez a következő parancs használatával:
    > 
    >```azurecli-interactive    
    >az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>  
    >```
    >
    > A kimenet ezután megjeleníti az identitás részleteit, beleértve a következő lépéshez szükséges **principalId** értéket. 

1. Az alábbi parancsban a **principalId** érték használatával rendelje hozzá a függvényalkalmazás identitását az Azure Digital Twins-példány **Azure Digital Twins-adattulajdonosi** szerepköréhez.

    ```azurecli-interactive 
    az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
    ```

Ennek a parancsnak az eredménye a létrehozott szerepkör-hozzárendeléssel kapcsolatos információ. A Function app mostantól rendelkezik az Azure Digital Twins-példányban tárolt adathozzáféréshez szükséges engedélyekkel.

#### <a name="configure-application-settings"></a>Alkalmazásbeállítások konfigurálása

A második beállítás egy **környezeti változót** hoz létre a függvényhez az Azure Digital Twins-példány URL-címével. A függvény kódja ezt fogja használni a példányra való hivatkozáshoz. A környezeti változókról további információt a [*Function app kezelése*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal)című témakörben talál. 

Futtassa az alábbi parancsot, és töltse ki a helyőrzőket az erőforrások részleteivel.

```azurecli-interactive
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-hostname>"
```

A kimenet az Azure-függvény beállításainak listája, amely most már tartalmaz egy **ADT_SERVICE_URL** nevű bejegyzést.


## <a name="process-simulated-telemetry-from-an-iot-hub-device"></a>Szimulált telemetria feldolgozása IoT Hub eszközről

Az Azure digitális Twins-gráfok célja, hogy a valódi eszközökről származó telemetria vezérelje. 

Ebben a lépésben egy szimulált termosztátos eszközt fog összekötni [IoT hub](../iot-hub/about-iot-hub.md) a digitális Twin-ben, amely az Azure digitális ikrekben van. Ahogy a szimulált eszköz telemetria bocsát ki, az adat a *ProcessHubToDTEvents* Azure-függvényen keresztül lesz átirányítva, amely egy megfelelő frissítést indít el a digitális Twin-ben. Így a digitális dupla naprakész állapotban marad a valós eszköz adatokkal. Az Azure Digital Twins-ben az események egyik helyről a másikra történő irányításának folyamatát [**útválasztási eseményeknek**](concepts-route-events.md)nevezzük.

Ez a végpontok közötti forgatókönyv (**B nyíl**) ezen részén történik:

:::image type="content" source="media/tutorial-end-to-end/building-scenario-b.png" alt-text="Egy részlet a teljes építési forgatókönyvből, a &quot;B&quot; nyilat kiemelve, az Azure Digital Twins előtti elemek: az eszköz, a IoT Hub és az első Azure-függvény":::

Az eszköz kapcsolódásának beállításához a következő műveleteket kell végrehajtania:
1. Hozzon létre egy IoT hub-t, amely a szimulált eszközt felügyeli
2. Az IoT hub és a megfelelő Azure-függvény összekötése egy esemény-előfizetés beállításával
3. A szimulált eszköz regisztrálása az IoT hub-ban
4. A szimulált eszköz futtatása és a telemetria előállítása
5. Az Azure digitális Twins lekérdezése az élő eredmények megtekintéséhez

### <a name="create-an-iot-hub-instance"></a>IoT Hub-példány létrehozása

Az Azure Digital Twins úgy lett kialakítva, hogy [IoT hub](../iot-hub/about-iot-hub.md), egy Azure-szolgáltatást, amely az eszközök és az adatkezelési szolgáltatások mellett működik. Ebben a lépésben egy IoT hubot fog beállítani, amely az oktatóanyagban felügyeli a minta eszközt.

Azure Cloud Shell a paranccsal hozzon létre egy új IoT hubot:

```azurecli-interactive
az iot hub create --name <name-for-your-IoT-hub> -g <your-resource-group> --sku S1
```

A parancs kimenete a létrehozott IoT hub adatait ismerteti.

Mentse az IoT hub-ba kapott **nevet** . Erre később még szüksége lesz.

### <a name="connect-the-iot-hub-to-the-azure-function"></a>Az IoT hub és az Azure-függvény összekötése

Ezután az IoT hub csatlakoztatása a *ProcessHubToDTEvents* Azure-függvényhez a korábban közzétett Function alkalmazásban, hogy az adatok az eszközről IoT hub a függvény használatával, amely frissíti az Azure Digital Twins-t.

Ehhez létre kell hoznia egy esemény- **előfizetést** a IoT hub, és az Azure-függvényt végpontként kell létrehoznia. Ez a függvény a IoT Hubban megjelenő eseményekre vonatkozik.

A [Azure Portal](https://portal.azure.com/)navigáljon az újonnan létrehozott IoT hubhoz a felső keresési sávban megkeresve a nevét. Válassza az *események* lehetőséget a központi menüben, majd válassza az *+ esemény-előfizetés* lehetőséget.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1.png" alt-text="Azure Portal: IoT Hub esemény-előfizetés":::

Ekkor megjelenik az esemény- *előfizetés létrehozása* lap.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-2.png" alt-text="Azure Portal: esemény-előfizetés létrehozása":::

Töltse ki a mezőket a következőképpen (az alapértelmezés szerint kitöltött mezők nincsenek megemlítve):
* *esemény-előfizetés részletei*  >  **Név**: adjon nevet az esemény-előfizetésnek.
* *témakör részletei*  >  **Rendszertéma neve**: adjon meg egy nevet a rendszer témakörhöz. 
* *események típusai*  >  **Szűrés az események típusai** között: válassza az *eszközök telemetria* lehetőséget a menüpontok közül.
* *VÉGPONT részletei*  >  **Végpont típusa**: válassza az *Azure Function* lehetőséget a menüpontok közül.
* *VÉGPONT részletei*  >  **Végpont**: nyomja meg a *végpont kiválasztása* hivatkozást. Ekkor megnyílik az *Azure-függvény kiválasztása* ablak: :::image type="content" source="media/tutorial-end-to-end/event-subscription-3.png" alt-text="Azure Portal esemény-előfizetés: válassza az Azure-függvény lehetőséget" border="false"::: .
    - Töltse ki az **előfizetést**, az **erőforráscsoportot**, a **Function app** és a Function (*ProcessHubToDTEvents*) **függvényt** . Ezek némelyike az előfizetés kiválasztása után automatikusan feltölthető.
    - Nyomja **meg a megerősítés jóváhagyása elemet**.

Az esemény- *előfizetés létrehozása* lapon kattintson a **create (létrehozás**) elemre.

### <a name="register-the-simulated-device-with-iot-hub"></a>A szimulált eszköz regisztrálása IoT Hub 

Ez a szakasz létrehoz egy eszközt a IoT Hub a *THERMOSTAT67* azonosítóval. A szimulált eszköz ehhez csatlakozik, és így az telemetria-események az eszközről a IoT Hubba kerülnek, ahol az előző lépésből származó előfizetett Azure-függvény figyel, készen áll az események felvételére és a feldolgozás folytatására.

A Azure Cloud Shellban hozzon létre egy eszközt a IoT Hubban a következő paranccsal:

```azurecli-interactive
az iot hub device-identity create --device-id thermostat67 --hub-name <your-IoT-hub-name> -g <your-resource-group>
```

A kimenet a létrehozott eszközzel kapcsolatos információ.

### <a name="configure-and-run-the-simulation"></a>A szimuláció konfigurálása és futtatása

Ezután konfigurálja az eszközszimulátort az IoT Hub-példányba való adatküldéshez.

Először az *IoT hub-kapcsolatok karakterláncának* beszerzése ezzel a paranccsal:

```azurecli-interactive
az iot hub connection-string show -n <your-IoT-hub-name>
```

Az alábbi paranccsal szerezze be az *eszközkapcsolati sztringet*:

```azurecli-interactive
az iot hub device-identity connection-string show --device-id thermostat67 --hub-name <your-IoT-hub-name>
```

Ezeket az értékeket a helyi projekt eszköz-szimulátor kódjába kell csatlakoztatnia a szimulátornak a IoT hub és az IoT hub-eszközhöz való csatlakoztatásához.

Az új Visual Studio-ablakban nyissa meg a (a letöltött megoldás mappából) _> **DeviceSimulator. SLN** nevű eszközt_.

>[!NOTE]
> Ekkor két Visual Studio-Windowsnak kell lennie, egyet a _**DeviceSimulator. SLN**_ és egy korábbi verziójával a _**AdtE2ESample. SLN**_.

Az új Visual Studio ablak *megoldáskezelő* paneljén válassza a _DeviceSimulator/**AzureIoTHub. cs**_ elemet a szerkesztési ablakban való megnyitásához. Módosítsa a következő kapcsolódási karakterlánc-értékeket a fent összegyűjtött értékekre:

```csharp
iotHubConnectionString = <your-hub-connection-string>
deviceConnectionString = <your-device-connection-string>
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

A megnyíló Project Console ablakban futtassa a következő parancsot a digitális kettős *thermostat67* által jelentett hőmérsékletek lekéréséhez:

```cmd
ObserveProperties thermostat67 Temperature
```

Az *Azure Digital Twins-példány* élő frissített hőmérsékletét két másodpercenként naplózza a konzolon.

>[!NOTE]
> Eltarthat néhány másodpercig, amíg az eszközről az adatok továbbítva vannak a Twin-re. Az első néhány hőmérséklet-olvasási érték 0 lehet, mielőtt megkezdődik az adat érkezése.

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry.png" alt-text="Konzol kimenete, amely a digitális Twin thermostat67 származó hőmérsékleti üzenetek naplóját jeleníti meg":::

Miután ellenőrizte, hogy ez sikeresen működik, leállíthatja mindkét projekt futtatását. Tartsa nyitva a Visual Studio ablakait, ahogy az oktatóanyag további részében is használni fogja őket.

## <a name="propagate-azure-digital-twins-events-through-the-graph"></a>Azure digitális Twins-események propagálása a gráfon keresztül

Ebből az oktatóanyagból megtudhatta, hogyan frissíthetik az Azure digitális Twins a külső eszközökről származó adatokból. Következő lépésként láthatja, hogy az egyik digitális iker módosítása hogyan propagálható az Azure Digital Twins gráfon – más szóval, hogyan frissítheti az ikreket a szolgáltatás belső adatainak használatával.

Ehhez használja a *ProcessDTRoutedData* Azure-függvényt, hogy frissítsen egy különálló *szobát* , amikor a csatlakoztatott *termosztátot* tartalmazó Twin frissítés frissül. Ez a végpontok közötti forgatókönyv (**C nyíl**) ezen részében fordul elő:

:::image type="content" source="media/tutorial-end-to-end/building-scenario-c.png" alt-text="Egy részlet a teljes építési forgatókönyvből, grafikus kiemeléssel C, az Azure Digital Twins utáni elemek: a Event Grid és a második Azure-függvény":::

Az alábbi műveleteket hajthatja végre az adatfolyamatok beállításához:
1. Hozzon létre egy Event Grid végpontot az Azure Digital Ikrekben, amely összekapcsolja a példányt a Event Grid
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
> ```azurecli-interactive
> az account list-locations -o table
> ```

A parancs kimenete az Ön által létrehozott Event Grid-témakörre vonatkozó információkat tartalmazza.

Következő lépésként hozzon létre egy Event Grid-végpontot az Azure digitális Twins-ben, amely a példányt az Event Grid-témakörhöz fogja kapcsolni. Használja az alábbi parancsot, és szükség szerint töltse ki a helyőrző mezőket:

```azurecli-interactive
az dt endpoint create eventgrid --dt-name <your-Azure-Digital-Twins-instance> --eventgrid-resource-group <your-resource-group> --eventgrid-topic <your-event-grid-topic> --endpoint-name <name-for-your-Azure-Digital-Twins-endpoint>
```

A parancs kimenete a létrehozott végpontra vonatkozó információkat jeleníti meg.

Azt is ellenőrizheti, hogy a végpont létrehozása sikeres volt-e a következő parancs futtatásával, hogy lekérdezze az Azure Digital Twins-példányt ehhez a végponthoz:

```azurecli-interactive
az dt endpoint show --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> 
```

Keresse meg a `provisioningState` kimenetben a mezőt, és ellenőrizze, hogy az érték "sikeres". Azt is megteheti, hogy "kiépítés", ami azt jelenti, hogy a végpont létrehozása még folyamatban van. Ebben az esetben várjon néhány másodpercet, és futtassa újra a parancsot, és győződjön meg arról, hogy a művelet sikeresen befejeződött.

:::image type="content" source="media/tutorial-end-to-end/output-endpoints.png" alt-text="A végpont-lekérdezés eredménye, amely a sikeres provisioningState rendelkező végpontot mutatja":::

Mentse az **Event Grid-témakörben** és a Event Grid- **végpontban** megadott neveket az Azure digitális Twins-ban. Ezeket később fogja használni.

### <a name="set-up-route"></a>Útvonal beállítása

Következő lépésként hozzon létre egy Azure Digital Twins-útvonalat, amely az imént létrehozott Event Grid-végpontnak küld eseményeket.

```azurecli-interactive
az dt route create --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> --route-name <name-for-your-Azure-Digital-Twins-route>
```

A parancs kimenete néhány információ a létrehozott útvonalról.

>[!NOTE]
>A végpontokat (az előző lépésből) be kell fejezni a kiépítés előtt, hogy be lehessen állítani az azokat használó esemény-útvonalat. Ha az útvonal létrehozása sikertelen, mert a végpontok nem állnak készen, várjon néhány percet, majd próbálkozzon újra.

#### <a name="connect-the-function-to-event-grid"></a>A függvény összekötése Event Grid

Ezután fizessen elő a *ProcessDTRoutedData* Azure-függvényt a korábban létrehozott Event Grid-témakörre, hogy a telemetria-adatok az Event Grid-témakörben a függvénybe *thermostat67* , amely visszakerül az Azure digitális Twins-be, és ennek megfelelően frissíti a *room21* .

Ehhez létre kell hoznia egy Event Grid- **előfizetést** , amely adatokat küld az *ProcessDTRoutedData* Azure-függvénynek korábban létrehozott **Event Grid-témakörből** .

A [Azure Portalban](https://portal.azure.com/)keresse meg az Event Grid-témakört úgy, hogy a felső keresési sávban keresi a nevét. Válassza a *+ Esemény-előfizetés* lehetőséget.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1b.png" alt-text="Azure Portal: Event Grid esemény-előfizetés":::

Az esemény-előfizetés létrehozásának lépései hasonlóak ahhoz, amikor előfizetett az első Azure-függvényre, hogy IoT Hub az oktatóanyag korábbi részében. Ezúttal nem kell megadnia az *eszköz telemetria* a figyelni kívánt esemény típusaként, és egy másik Azure-függvényhez fog csatlakozni.

Az *esemény-előfizetés létrehozása* lapon töltse ki a mezőket a következő módon (alapértelmezés szerint kitöltött mezők nincsenek megemlítve):
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

A megnyíló Project Console ablakban futtassa a következő **parancsot a digitális** kettős *thermostat67* és a digitális kettős *room21* által jelentett hőmérsékletek lekéréséhez.

```cmd
ObserveProperties thermostat67 Temperature room21 Temperature
```

Az *Azure Digital Twins-példány* élő frissített hőmérsékletét két másodpercenként naplózza a konzolon. Figyelje meg, hogy a *room21* hőmérséklete frissül, hogy megfeleljen a *thermostat67* frissítéseinek.

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry-b.png" alt-text="Konzol kimenete, amely a hőmérsékleti üzenetek naplóját mutatja egy termosztátból és egy helyiségből":::

Miután ellenőrizte, hogy ez sikeresen működik, leállíthatja mindkét projekt futtatását. Bezárhatja a Visual Studio Windowst is, mivel az oktatóanyag már elkészült.

## <a name="review"></a>Áttekintés

Itt látható az oktatóanyagban kiépített forgatókönyv áttekintése.

1. Az Azure digitális Twins-példányok digitális a padlót, a termet és a termosztátot jelöli (amelyet az alábbi ábra a **szakasza tartalmaz** )
2. A szimulált eszköz telemetria a rendszer elküldi IoT Hubba, ahol a *ProcessHubToDTEvents* Azure-függvény figyeli a telemetria eseményeket. Az *ProcessHubToDTEvents* Azure-függvény az ezekben az eseményekben található információk alapján állítja be a *hőmérséklet* tulajdonságot a *thermostat67* (a diagramban a **B nyíl** ).
3. Az Azure Digital Twins-beli tulajdonság-változási események egy Event Grid-témakörbe vannak irányítva, ahol a *ProcessDTRoutedData* Azure-függvény figyeli az eseményeket. Az *ProcessDTRoutedData* Azure-függvény az ezekben az eseményekben található információk használatával állítja be a *hőmérséklet* tulajdonságot a *room21* (a diagramon a **C. nyíl** ).

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="A teljes építési forgatókönyv ábrája. Az eszközről a IoT Hubba, egy Azure-függvénnyel (B. nyíl) egy Azure Digital Twins-példányra (A szakasz), Event Grid majd egy másik Azure-függvényre (A C. nyílra) átáramló adatok ábrázolása.":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyag elvégzése után kiválaszthatja, hogy mely erőforrásokat szeretné eltávolítani, attól függően, hogy mit szeretne tenni.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

* **Ha továbbra is az ebben a cikkben beállított Azure Digital Twins-példányt szeretné használni, de a modell, az ikrek és a kapcsolatok némelyikének vagy mindegyikének törlésével** törölheti az eltávolítani kívánt elemeket az [Azure Cloud Shell](https://shell.azure.com) ablakban található az [DT](/cli/azure/ext/azure-iot/dt) CLI-parancsokkal.

    Ez a beállítás nem távolítja el az oktatóanyagban létrehozott többi Azure-erőforrást (IoT Hub, Azure Functions alkalmazást stb.). Ezeket egyenként törölheti az egyes erőforrástípusok számára megfelelő [DT-parancsok](/cli/azure/reference-index) használatával.

Érdemes törölni a Project mappát is a helyi gépről.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy teljes körű forgatókönyvet, amely bemutatja, hogy az Azure digitális ikrek az élő eszközön tárolt adatmennyiségen alapulnak.

Ezután tekintse meg a koncepció dokumentációját, ahol további információt talál az oktatóanyagban használt elemekről:

> [!div class="nextstepaction"]
> [*Fogalmak: egyéni modellek*](concepts-models.md)
---
title: Eszközök automatikus felügyelete az eszköz kiépítési szolgáltatásával
titleSuffix: Azure Digital Twins
description: A Device kiépítési szolgáltatás (DPS) használatával megtudhatja, hogyan állíthat be egy automatizált folyamatot a IoT-eszközök kiépítéséhez és kivonásához az Azure Digital Twins-ban.
author: baanders
ms.author: baanders
ms.date: 3/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: a571d92dd9663c7d2d0a576b59e5cd2b3352cb76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104951014"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>Eszközök automatikus felügyelete az Azure Digital Twins-ben a Device kiépítési szolgáltatás (DPS) használatával

Ebből a cikkből megtudhatja, hogyan integrálhatja az Azure Digital ikreket a [Device kiépítési szolgáltatással (DPS)](../iot-dps/about-iot-dps.md).

A cikkben ismertetett megoldás lehetővé teszi, hogy automatizálja IoT Hub eszközök Azure digitális Twins **_-ban_** való kiépítésének **_és_** kivonásának folyamatát az eszköz kiépítési szolgáltatásának használatával. 

A _kiépítési_ és kivonási szakaszokkal kapcsolatos további információkért _, valamint az_ összes vállalati IoT-projekthez közös általános eszközkezelés szakaszának jobb megismeréséhez tekintse meg a IoT hub eszközkezelés dokumentációjának [ *eszközök életciklusa* című szakaszát](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) .

## <a name="prerequisites"></a>Előfeltételek

A kiépítés előtt be kell állítania a következőket:
* egy **Azure-beli digitális Twins-példány**. Kövesse az útmutató [*: példány és hitelesítés beállítása*](how-to-set-up-instance-portal.md) Azure digitális Twins-példány létrehozásához című témakör utasításait. Gyűjtse össze a példány **_állomásneve_** a Azure Portalban ([utasítások](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).
* egy **IoT hub**. Útmutatásért tekintse meg a [IoT hub](../iot-hub/quickstart-send-telemetry-cli.md)rövid útmutató *IoT hub létrehozása* című szakaszát.
* egy [**Azure-függvény**](../azure-functions/functions-overview.md) , amely IoT hub adatok alapján frissíti a digitális Twin-információkat. Az Azure-függvény létrehozásához kövesse az útmutató [*: IoT hub-adat*](how-to-ingest-iot-hub-data.md) betöltése című témakör útmutatását. Gyűjtse össze a függvény **_nevét_** , hogy használhassa azt ebben a cikkben.

Ez a minta egy **eszköz-szimulátort** is használ, amely az eszközök kiépítési szolgáltatásával történő telepítést is magában foglalja. Az eszköz-szimulátor itt található: [Azure digitális Twins és IoT hub integrációs minta](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Szerezze be a minta projektet a gépen a minta hivatkozásra kattintva, majd a cím alatt található *zip letöltése* gombra kattintva. Bontsa ki a letöltött mappát.

Telepítenie kell [**Node.js**](https://nodejs.org/download) a gépre. Az eszköz-szimulátor **Node.js** 10.0. x vagy újabb verzión alapul.

## <a name="solution-architecture"></a>Megoldásarchitektúra

Az alábbi ábra a megoldás architektúráját mutatja be az Azure Digital Twins és a Device kiépítési szolgáltatás használatával. Az eszköz kiépítése és a kivonási folyamat is megjelenik.

:::image type="content" source="media/how-to-provision-using-dps/flows.png" alt-text="Az eszköz és számos Azure-szolgáltatás diagramja egy végpontok közötti forgatókönyvben. Az adatforgalom a termosztátos eszköz és a DPS közötti kapcsolaton keresztül zajlik. Az adatok a DPS-ből a IoT Hubba és az Azure digitális Twins-ba is áramlanak a &quot;kiosztás&quot; címkével ellátott Azure-függvény használatával. A manuális &quot;eszköz törlése&quot; műveletből származó adatok IoT Hub > Event Hubs > Azure Functions > Azure digitális Ikreken keresztül áramlanak." lightbox="media/how-to-provision-using-dps/flows.png":::

Ez a cikk két szakaszra oszlik:
* [*Eszköz automatikus kiépítése az eszköz kiépítési szolgáltatásával*](#auto-provision-device-using-device-provisioning-service)
* [*Eszköz automatikus kivonása IoT Hub életciklus-események használatával*](#auto-retire-device-using-iot-hub-lifecycle-events)

Az architektúra egyes lépéseinek mélyebb magyarázatához tekintse meg az egyes szakaszokat a cikk későbbi részében.

## <a name="auto-provision-device-using-device-provisioning-service"></a>Eszköz automatikus kiépítése az eszköz kiépítési szolgáltatásával

Ebben a szakaszban az eszköz kiépítési szolgáltatását az Azure digitális Twins-hoz csatlakoztatja, hogy automatikusan kiépítse az eszközöket az alábbi elérési úton. Ez a [korábban](#solution-architecture)bemutatott teljes architektúra kivonata.

:::image type="content" source="media/how-to-provision-using-dps/provision.png" alt-text="A flow kiépítésének diagramja – a megoldás architektúra diagramjának kivonata, amely a folyamat egyes részeit címkézi. Az adatforgalom egy termosztátos eszköz és a DPS (1 for Device > DPS és 5 a DPS > eszköz esetében) között zajlik. Az adatok a DPS-ből a IoT Hub (4) és az Azure digitális Twins (3) rendszerbe is áramlanak a &quot;kiosztás&quot; (2) címkével ellátott Azure-függvény használatával." lightbox="media/how-to-provision-using-dps/provision.png":::

Itt látható a folyamat folyamatábrája:
1. Az eszköz kapcsolatba lép a DPS-végponttal, és azonosítja az azonosító adatokat, hogy igazolja az identitását.
2. A DPS érvényesíti az eszköz identitását a regisztrációs azonosító és a kulcs érvényesítésével a beléptetési listán, és egy [Azure-függvényt](../azure-functions/functions-overview.md) hív meg a foglalás elvégzéséhez.
3. Az Azure-függvény létrehoz egy új [Twin](concepts-twins-graph.md) -et az Azure digitális Twins szolgáltatásban az eszközhöz. A Twin neve megegyezik az eszköz **regisztrációs azonosítójával**.
4. A DPS regisztrálja az eszközt egy IoT hub-ban, és feltölti az eszközt a kívánt Twin állapotba.
5. Az IoT hub az eszköz AZONOSÍTÓjának adatait és az IoT hub kapcsolódási adatait adja vissza az eszközhöz. Az eszköz mostantól képes csatlakozni az IoT hubhoz.

Az alábbi szakaszokban ismertetjük az automatikus kiépítésű eszköz folyamatának lépéseit.

### <a name="create-a-device-provisioning-service"></a>Eszköz-kiépítési szolgáltatás létrehozása

Ha egy új eszközt kiépítenek az eszköz kiépítési szolgáltatásával, az adott eszközhöz egy új iker is létrehozható az Azure digitális Twins-ban ugyanazzal a névvel, mint a regisztrációs AZONOSÍTÓval.

Hozzon létre egy eszköz-kiépítési szolgáltatási példányt, amely a IoT-eszközök kiépítésére szolgál majd. Használhatja az alábbi Azure CLI-utasításokat, vagy használhatja a Azure Portal: gyors útmutató [*: a IoT hub Device Provisioning Service beállítása a Azure Portal*](../iot-dps/quick-setup-auto-provision.md).

A következő Azure CLI-parancs létrehoz egy eszköz-kiépítési szolgáltatást. Meg kell adnia egy eszköz kiépítési szolgáltatásának nevét, az erőforráscsoportot és a régiót. Ha szeretné megtekinteni, hogy mely régiók támogatják az eszközök kiépítési szolgáltatását, látogasson el [*Az Azure-termékek területére*](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub).
A parancs [Cloud Shell](https://shell.azure.com)vagy helyileg is futtatható, ha telepítve van az Azure CLI a [gépen](/cli/azure/install-azure-cli).

```azurecli-interactive
az iot dps create --name <Device Provisioning Service name> --resource-group <resource group name> --location <region>
```

### <a name="add-a-function-to-use-with-device-provisioning-service"></a>A Device kiépítési szolgáltatással használandó függvény hozzáadása

Az [Előfeltételek](#prerequisites) szakaszban létrehozott Function app-projekten belül létrehoz egy új függvényt, amelyet az eszköz kiépítési szolgáltatásához fog használni. Ezt a függvényt az eszköz kiépítési szolgáltatása fogja használni egy [Egyéni kiosztási szabályzatban](../iot-dps/how-to-use-custom-allocation-policies.md) egy új eszköz kiépítéséhez.

Először nyissa meg a Function app projektet a Visual Studióban a gépen, és kövesse az alábbi lépéseket.

#### <a name="step-1-add-a-new-function"></a>1. lépés: új függvény hozzáadása 

Adjon hozzá egy *http-trigger* típusú új függvényt a Function app projekthez a Visual Studióban.

:::image type="content" source="media/how-to-provision-using-dps/add-http-trigger-function-visual-studio.png" alt-text="Képernyőfelvétel a Visual Studio nézetről a Function app-projektben http-trigger típusú Azure-függvény hozzáadásához." lightbox="media/how-to-provision-using-dps/add-http-trigger-function-visual-studio.png":::

#### <a name="step-2-fill-in-function-code"></a>2. lépés: töltse ki a függvény kódját

Új NuGet-csomag hozzáadása a projekthez: [Microsoft. Azure. Devices. kiépítés. szolgáltatás](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/). Előfordulhat, hogy további csomagokat is hozzá kell adnia a projekthez, ha a kódban használt csomagok már nem részei a projektnek.

Az újonnan létrehozott függvény kódjában illessze be a következő kódot, nevezze át a függvényt a *DpsAdtAllocationFunc. cs* fájlba, és mentse a fájlt.

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DpsAdtAllocationFunc.cs":::

#### <a name="step-3-publish-the-function-app-to-azure"></a>3. lépés: a Function alkalmazás közzététele az Azure-ban

Tegye közzé a projektet a *DpsAdtAllocationFunc. cs* függvénnyel az Azure-beli Function alkalmazásban.

[!INCLUDE [digital-twins-publish-and-configure-function-app.md](../../includes/digital-twins-publish-and-configure-function-app.md)]

### <a name="create-device-provisioning-enrollment"></a>Eszköz kiépítési regisztrációjának létrehozása

Ezután létre kell hoznia egy regisztrációt az eszköz kiépítési szolgáltatásában egy **Egyéni foglalási függvénnyel**. Az eszköz üzembe helyezési szolgáltatásának dokumentációjában kövesse az egyéni foglalási szabályzatok című cikk [*regisztráció létrehozása*](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment) című szakaszának utasításait.

A folyamat átadása közben ügyeljen arra, hogy a következő beállításokkal kapcsolja össze a beléptetést az imént létrehozott függvénnyel.

* **Válassza ki, hogyan szeretné hozzárendelni az eszközöket az hubokhoz**: Custom (Azure-függvény használata).
* **Válassza ki azokat a IoT hubokat, amelyekre ez a csoport hozzá lehet rendelni:** Válassza ki az IoT hub nevét, vagy válassza az *új IoT hub csatolása* gombot, és válassza ki a IoT hubot a legördülő listából.

Ezután kattintson az *új függvény választása* gombra a Function alkalmazás a beléptetési csoporthoz való kapcsolásához. Ezután adja meg a következő értékeket:

* **Előfizetés**: az Azure-előfizetés automatikusan ki van töltve. Győződjön meg arról, hogy a megfelelő előfizetés.
* **Függvényalkalmazás**: válassza ki a Function alkalmazás nevét.
* **Függvény**: válassza a DpsAdtAllocationFunc lehetőséget.

Mentse a részleteket.                  

:::image type="content" source="media/how-to-provision-using-dps/link-enrollment-group-to-iot-hub-and-function-app.png" alt-text="Képernyőfelvétel a beléptetési csoport részletei ablakról az egyéni (az Azure-függvény használata) és az IoT hub nevének kiválasztásával válassza ki, hogyan szeretné hozzárendelni az eszközöket a központokhoz, és válassza ki azokat a IoT hubokat, amelyekre ez a csoport hozzá lehet rendelni. Válassza ki az előfizetését, a függvény alkalmazást a legördülő listából, és ügyeljen rá, hogy DpsAdtAllocationFunc válassza ki." lightbox="media/how-to-provision-using-dps/link-enrollment-group-to-iot-hub-and-function-app.png":::

A regisztráció létrehozása után a rendszer később a regisztrálás **elsődleges kulcsát** fogja használni a cikkhez tartozó eszköz-szimulátor konfigurálásához.

### <a name="set-up-the-device-simulator"></a>Az eszköz-szimulátor beállítása

Ez a minta egy eszköz-szimulátort használ, amely az eszközök kiépítési szolgáltatásával történő üzembe helyezést tartalmaz. Az eszköz-szimulátor itt található: [Azure digitális Twins és IoT hub integrációs minta](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Ha még nem töltötte le a mintát, most lépjen a minta hivatkozásra, és válassza a cím alatt a *zip letöltése* gombot. Bontsa ki a letöltött mappát.

#### <a name="upload-the-model"></a>A modell feltöltése

Az eszköz-szimulátor egy olyan termosztát típusú eszköz, amely a modellt használja a következő AZONOSÍTÓval: `dtmi:contosocom:DigitalTwins:Thermostat;1` . Ezt a modellt fel kell töltenie az Azure Digital Twins-be, mielőtt az eszközhöz létre lehetne hozni ezt a típust.

[!INCLUDE [digital-twins-thermostat-model-upload.md](../../includes/digital-twins-thermostat-model-upload.md)]

A modellekkel kapcsolatos további információkért tekintse meg az [*útmutató: modellek kezelése*](how-to-manage-model.md#upload-models)című témakört.

#### <a name="configure-and-run-the-simulator"></a>A szimulátor konfigurálása és futtatása

A parancsablakban navigáljon a letöltött minta *Azure digitális Twins és IoT hub integrációhoz* , amelyet korábban kibontott, majd az *eszköz-szimulátor* könyvtárba. Ezután telepítse a projekt függőségeit a következő parancs használatával:

```cmd
npm install
```

Ezután a Device Simulator könyvtárban másolja a. env. template fájlt egy új,. env nevű fájlba, és a következő értékeket kell összegyűjtenie a beállítások kitöltéséhez:

* PROVISIONING_IDSCOPE: az érték beszerzéséhez navigáljon az eszköz kiépítési szolgáltatásához a [Azure Portalban](https://portal.azure.com/), majd válassza az *Áttekintés* lehetőséget a menüpontokban, és keresse meg a mező- *azonosító hatókörét*.

    :::image type="content" source="media/how-to-provision-using-dps/id-scope.png" alt-text="Képernyőkép az eszköz kiépítési áttekintése oldal Azure Portal nézetéről az azonosító hatókör értékének másolásához." lightbox="media/how-to-provision-using-dps/id-scope.png":::

* PROVISIONING_REGISTRATION_ID: az eszköz regisztrációs AZONOSÍTÓját is kiválaszthatja.
* ADT_MODEL_ID: `dtmi:contosocom:DigitalTwins:Thermostat;1`
* PROVISIONING_SYMMETRIC_KEY: Ez a korábban beállított regisztráció elsődleges kulcsa. Az érték újbóli lekéréséhez navigáljon az eszköz kiépítési szolgáltatásához a Azure Portalban, válassza a *regisztrációk kezelése* lehetőséget, majd válassza ki a korábban létrehozott beléptetési csoportot, és másolja az *elsődleges kulcsot*.

    :::image type="content" source="media/how-to-provision-using-dps/sas-primary-key.png" alt-text="Képernyőfelvétel: az eszköz kiépítési szolgáltatásának Azure Portal nézete a regisztrációk kezelése lapon másolja az SAS elsődleges kulcsának értékét." lightbox="media/how-to-provision-using-dps/sas-primary-key.png":::

Most a fenti értékek használatával frissítse a. env fájl beállításait.

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device Provisioning Service Scope ID>"
PROVISIONING_REGISTRATION_ID = "<Device Registration ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device Provisioning Service enrollment primary SAS key>"
```

Mentse és zárja be a fájlt.

### <a name="start-running-the-device-simulator"></a>Az eszköz-szimulátor futtatásának megkezdése

A parancssorablakban még mindig a *Device-Simulator* könyvtárban indítsa el az eszközt a következő parancs használatával:

```cmd
node .\adt_custom_register.js
```

Látnia kell az eszközt, amely regisztrálva van, és csatlakoztatva van IoT Hubhoz, majd megkezdi az üzenetek küldését.
:::image type="content" source="media/how-to-provision-using-dps/output.png" alt-text="Képernyőfelvétel az eszközök regisztrálását és az üzenetek küldését bemutató Parancsablak" lightbox="media/how-to-provision-using-dps/output.png":::

### <a name="validate"></a>Érvényesítés

A cikkben beállított folyamat eredményeképpen az eszköz automatikusan regisztrálva lesz az Azure digitális Twins-ban. A következő [Azure digitális Twins CLI](how-to-use-cli.md) -paranccsal megkeresheti az eszköz két részét a létrehozott Azure digitális Twins-példányban.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id "<Device Registration ID>"
```

Meg kell jelennie az Azure Digital Twins-példányban található eszköz két részének.
:::image type="content" source="media/how-to-provision-using-dps/show-provisioned-twin.png" alt-text="Képernyőkép az újonnan létrehozott Twin-t bemutató Parancsablakról." lightbox="media/how-to-provision-using-dps/show-provisioned-twin.png":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>Eszköz automatikus kivonása IoT Hub életciklus-események használatával

Ebben a szakaszban IoT Hub életciklus-eseményeket csatlakoztat az Azure digitális Twins-hoz, hogy az alábbi elérési úton automatikusan kivonja az eszközöket. Ez a [korábban](#solution-architecture)bemutatott teljes architektúra kivonata.

:::image type="content" source="media/how-to-provision-using-dps/retire.png" alt-text="A kivonási eszköz folyamatának ábrája – a megoldás architektúra diagramjának kivonata, amely a folyamat egyes részeit címkézi. A termosztátos eszköz a diagramon az Azure-szolgáltatásokhoz való csatlakozás nélkül jelenik meg. A manuális &quot;eszköz törlése&quot; műveletből származó adatok IoT Hub (1) > Event Hubs (2) > Azure Functions > Azure digitális Twins (3) használatával áramlanak." lightbox="media/how-to-provision-using-dps/retire.png":::

Itt látható a folyamat folyamatábrája:
1. Egy külső vagy manuális folyamat elindít egy eszköz törlését IoT Hubban.
2. IoT Hub törli az eszközt, és létrehoz egy [eszköz életciklusa](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) eseményt, amely az [Event hub](../event-hubs/event-hubs-about.md)-ba lesz irányítva.
3. Egy Azure-függvény törli az eszköz két részét az Azure digitális Twins-ban.

Az alábbi szakasz végigvezeti az automatikus kivonási folyamat beállításának lépésein.

### <a name="create-an-event-hub"></a>Eseményközpont létrehozása

Ezután létre fog hozni egy Azure [Event hub](../event-hubs/event-hubs-about.md) -t, amely IoT hub életciklus-események fogadására. 

Kövesse az [*Event hub létrehozása*](../event-hubs/event-hubs-create.md) rövid útmutatójában ismertetett lépéseket. Nevezze el az Event hub- *lifecycleevents*. Ezt az Event hub-nevet fogja használni, amikor beállítja IoT Hub útvonalat és egy Azure-függvényt a következő szakaszokban.

Az alábbi képernyőkép az Event hub létrehozását mutatja be.
:::image type="content" source="media/how-to-provision-using-dps/create-event-hub-lifecycle-events.png" alt-text="Képernyőfelvétel a Azure Portal ablakról egy lifecycleevents nevű Event hub létrehozásához." lightbox="media/how-to-provision-using-dps/create-event-hub-lifecycle-events.png":::

#### <a name="create-sas-policy-for-your-event-hub"></a>SAS-szabályzat létrehozása az Event hub-hoz

Ezután létre kell hoznia egy [közös hozzáférési aláírási (SAS-) szabályzatot](../event-hubs/authorize-access-shared-access-signature.md) az Event hub és a Function alkalmazás konfigurálásához.
Ehhez
1. Navigáljon az imént létrehozott Event hub-Azure Portal, majd a bal oldali menüben válassza a **megosztott hozzáférési szabályzatok** lehetőséget.
2. Válassza a **Hozzáadás** lehetőséget. A megnyíló *sas-házirend hozzáadása* ablakban adja meg a kívánt szabályzat nevét, és jelölje be a *figyelés* jelölőnégyzetet.
3. Válassza a **Létrehozás** lehetőséget.
    
:::image type="content" source="media/how-to-provision-using-dps/add-event-hub-sas-policy.png" alt-text="Képernyőkép a Azure Portalról az Event hub SAS-házirendjének hozzáadásához." lightbox="media/how-to-provision-using-dps/add-event-hub-sas-policy.png":::

#### <a name="configure-event-hub-with-function-app"></a>Az Event hub konfigurálása a Function alkalmazással

Ezután konfigurálja az Azure Function alkalmazást, amelyet az [Előfeltételek](#prerequisites) szakaszban beállított, hogy működjön az új Event hub-vel. Ezt úgy teheti meg, hogy egy környezeti változót állít be a Function alkalmazásban az Event hub kapcsolati karakterláncával.

1. Nyissa meg az imént létrehozott szabályzatot, és másolja a **kapcsolatok karakterláncának elsődleges kulcs** értékét.

    :::image type="content" source="media/how-to-provision-using-dps/event-hub-sas-policy-connection-string.png" alt-text="Képernyőfelvétel a Azure Portalról a kapcsolódási karakterlánc – elsődleges kulcs másolásához." lightbox="media/how-to-provision-using-dps/event-hub-sas-policy-connection-string.png":::

2. Adja hozzá a kapcsolatok karakterláncát változóként a Function app-beállításokban az alábbi Azure CLI-paranccsal. A parancs [Cloud Shell](https://shell.azure.com)vagy helyileg is futtatható, ha telepítve van az Azure CLI a [gépen](/cli/azure/install-azure-cli).

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event Hubs SAS connection string Listen>" -g <resource group> -n <your App Service (function app) name>
    ```

### <a name="add-a-function-to-retire-with-iot-hub-lifecycle-events"></a>IoT Hub életciklus-eseményekkel való kivonáshoz használható függvény hozzáadása

Az [Előfeltételek](#prerequisites) szakaszban létrehozott Function app-projektben új függvényt fog létrehozni egy meglévő eszköz kivonásához IoT hub életciklus-események használatával.

Az életciklus eseményeivel kapcsolatos további információkért lásd: [*IoT hub nem telemetria események*](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events). További információ a Event Hubs Azure functions használatával történő használatáról: [*azure Event Hubs trigger Azure Functionshoz*](../azure-functions/functions-bindings-event-hubs-trigger.md).

Először nyissa meg a Function app projektet a Visual Studióban a gépen, és kövesse az alábbi lépéseket.

#### <a name="step-1-add-a-new-function"></a>1. lépés: új függvény hozzáadása
     
Adjon hozzá egy *Event hub trigger* típusú új függvényt a Function app projekthez a Visual Studióban.

:::image type="content" source="media/how-to-provision-using-dps/create-event-hub-trigger-function.png" alt-text="Képernyőfelvétel a Visual Studio ablakáról az Event hub-eseményindító típusú Azure-függvény hozzáadásához a Function app projektben." lightbox="media/how-to-provision-using-dps/create-event-hub-trigger-function.png":::

#### <a name="step-2-fill-in-function-code"></a>2. lépés: töltse ki a függvény kódját

Az újonnan létrehozott függvény kódjában illessze be a következő kódot, nevezze át a függvényt `DeleteDeviceInTwinFunc.cs` , és mentse a fájlt.

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DeleteDeviceInTwinFunc.cs":::

#### <a name="step-3-publish-the-function-app-to-azure"></a>3. lépés: a Function alkalmazás közzététele az Azure-ban

Tegye közzé a projektet a *DeleteDeviceInTwinFunc. cs* függvénnyel az Azure-beli Function alkalmazásban.

[!INCLUDE [digital-twins-publish-and-configure-function-app.md](../../includes/digital-twins-publish-and-configure-function-app.md)]

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>IoT Hub útvonal létrehozása életciklus-események esetén

Most be kell állítania egy IoT Hub útvonalat, hogy átirányítsa az eszköz életciklusának eseményeit. Ebben az esetben kifejezetten a által azonosított eszköz-törlési eseményeket fogja figyelni `if (opType == "deleteDeviceIdentity")` . Ez elindítja a digitális kettős elem törlését, az eszköz kivonásának véglegesítését és a digitális ikerét.

Először létre kell hoznia egy Event hub-végpontot az IoT hub-ban. Ezután hozzáadhat egy útvonalat az IoT hub-ban, hogy életciklus-eseményeket küldjön az Event hub-végpontnak.
Az alábbi lépéseket követve hozzon létre egy Event hub-végpontot:

1. A [Azure Portalban](https://portal.azure.com/)navigáljon az [Előfeltételek](#prerequisites) szakaszban létrehozott IoT hubhoz, és válassza a bal oldali menüpontban az **üzenet-útválasztás** lehetőséget.
2. Válassza az **Egyéni végpontok** fület.
3. Válassza a **+ Hozzáadás** lehetőséget, majd válassza az **Event hubok** lehetőséget az Event hub típusú végpont hozzáadásához.

    :::image type="content" source="media/how-to-provision-using-dps/event-hub-custom-endpoint.png" alt-text="Képernyőfelvétel a Visual Studio ablakról az Event hub egyéni végpontjának hozzáadásához." lightbox="media/how-to-provision-using-dps/event-hub-custom-endpoint.png":::

4. A megnyíló *Event hub-végpont hozzáadása* ablakban válassza ki a következő értékeket:
    * **Végpont neve**: válasszon egy végpont nevét.
    * **Event hub-névtér**: válassza ki az Event hub-névteret a legördülő listából.
    * **Event hub-példány**: válassza ki az előző lépésben létrehozott Event hub-nevet.
5. Válassza a **Létrehozás** lehetőséget. Tartsa nyitva ezt az ablakot, és adjon hozzá egy útvonalat a következő lépéshez.

    :::image type="content" source="media/how-to-provision-using-dps/add-event-hub-endpoint.png" alt-text="Képernyőfelvétel a Visual Studio ablakról az Event hub-végpont hozzáadásához." lightbox="media/how-to-provision-using-dps/add-event-hub-endpoint.png":::

Ezután adjon hozzá egy útvonalat, amely a fenti lépésben létrehozott végponthoz csatlakozik, egy útválasztási lekérdezéssel, amely elküldi a törlési eseményeket. Útvonal létrehozásához kövesse az alábbi lépéseket:

1. Navigáljon az *útvonalak* lapra, és válassza a **Hozzáadás** lehetőséget az útvonal hozzáadásához.

    :::image type="content" source="media/how-to-provision-using-dps/add-message-route.png" alt-text="Képernyőfelvétel a Visual Studio ablakáról az események küldéséhez szükséges útvonal hozzáadásához." lightbox="media/how-to-provision-using-dps/add-message-route.png":::

2. A megnyíló *útvonal hozzáadása* oldalon válassza ki a következő értékeket:

   * **Név**: válassza ki az útvonal nevét. 
   * **Endpoint (végpont**): válassza ki a legördülő listából korábban létrehozott Event hubok-végpontot.
   * **Adatforrás**: válassza az *eszközök életciklusának eseményei* elemet.
   * **Útválasztási lekérdezés**: írja be a értéket `opType='deleteDeviceIdentity'` . Ez a lekérdezés korlátozza az eszköz életciklusának eseményeit, hogy csak a DELETE eseményeket küldje el.

3. Kattintson a **Mentés** gombra.

    :::image type="content" source="media/how-to-provision-using-dps/lifecycle-route.png" alt-text="Képernyőkép a Azure Portal ablakról az életciklus-események küldéséhez szükséges útvonal hozzáadásához." lightbox="media/how-to-provision-using-dps/lifecycle-route.png":::

Miután elvégezte a folyamatot, minden úgy van beállítva, hogy teljes körűen kivonja az eszközöket.

### <a name="validate"></a>Érvényesítés

A kivonási folyamat elindításához kézzel kell törölnie az eszközt IoT Hubról.

Ezt megteheti egy [Azure CLI-paranccsal](/cli/azure/ext/azure-iot/iot/hub/module-identity#ext_azure_iot_az_iot_hub_module_identity_delete) vagy a Azure Portal. Az alábbi lépések végrehajtásával törölheti az eszközt a Azure Portalban:

1. Navigáljon az IoT hubhoz, és válassza a bal oldali menüpontban a **IoT-eszközök** lehetőséget. 
2. Ekkor megjelenik egy eszköz, amelyen a [cikk első felében](#auto-provision-device-using-device-provisioning-service)kiválasztott eszköz regisztrációs azonosító található. Azt is megteheti, hogy kiválaszthatja a törölni kívánt bármely más eszközt, ha az egy Twin az Azure digitális Twins szolgáltatásban, így ellenőrizheti, hogy a Twin automatikusan törlődik-e az eszköz törlése után.
3. Válassza ki az eszközt, és válassza a **Törlés** lehetőséget.

:::image type="content" source="media/how-to-provision-using-dps/delete-device-twin.png" alt-text="Képernyőfelvétel a Azure Portalról a IoT-eszközökről a Twin eszköz törléséhez." lightbox="media/how-to-provision-using-dps/delete-device-twin.png":::

Néhány percet is igénybe vehet, hogy megjelenjenek a változások az Azure digitális Twins-ban.

A következő [Azure digitális Twins CLI](how-to-use-cli.md) -paranccsal ellenőrizheti, hogy az eszköz két példánya az Azure Digital Twins-példánnyal lett-e törölve.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id "<Device Registration ID>"
```

Látnia kell, hogy az eszköz két része nem található az Azure Digital Twins-példányban.

:::image type="content" source="media/how-to-provision-using-dps/show-retired-twin.png" alt-text="A Twin nem található Parancsablak képernyőképe." lightbox="media/how-to-provision-using-dps/show-retired-twin.png":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az ebben a cikkben létrehozott erőforrásokra, a következő lépésekkel törölheti őket.

Az Azure Cloud Shell vagy a helyi Azure CLI használatával törölheti az erőforráscsoport összes Azure-erőforrását az az [Group delete](/cli/azure/group#az-group-delete) paranccsal. Ezzel eltávolítja az erőforráscsoportot; Az Azure Digital Twins-példány; az IoT hub és a hub-eszköz regisztrációja; az Event Grid-témakör és a hozzá tartozó előfizetések; az Event hub-névtér és mindkét Azure Functions alkalmazás, beleértve a kapcsolódó erőforrásokat, például a tárolást.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Ezután törölje a helyi gépről letöltött Project Sample mappát.

## <a name="next-steps"></a>Következő lépések

Az eszközökhöz létrehozott digitális Twins-ket az Azure Digital Twins szolgáltatásban tárolja a rendszer, de a modell adataival és a szervezet többszintű hierarchiájának használatával gazdagíthatja őket. Ha többet szeretne megtudni erről a fogalomról, olvassa el a következőt:

* [*Fogalmak: digitális ikrek és a Twin gráf*](concepts-twins-graph.md)

A HTTP-kérelmek Azure functions használatával történő használatáról további információt a következő témakörben talál:

* [*Azure HTTP-kérelem trigger Azure Functions*](../azure-functions/functions-bindings-http-webhook-trigger.md)

Egyéni logikát írhat arra, hogy automatikusan megadja ezeket az adatokat az Azure digitális Twins-ban már tárolt modell-és gráf-adatok használatával. Az alábbi témakörben olvashat bővebben arról, hogyan kezelheti, frissítheti és beolvashatja az adatokat az Twins-gráfból:

* [*Útmutató: digitális kettős kezelés*](how-to-manage-twin.md)
* [*Útmutató: a Twin gráf lekérdezése*](how-to-query-graph.md)
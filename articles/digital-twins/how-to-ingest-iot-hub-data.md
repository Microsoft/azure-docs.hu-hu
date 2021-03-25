---
title: Telemetria betöltése az IoT Hubról
titleSuffix: Azure Digital Twins
description: 'Lásd: az eszköz telemetria üzeneteinek betöltése IoT Hubról.'
author: alexkarcher-msft
ms.author: alkarche
ms.date: 9/15/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: de16932f1f77e569302b222fe2948de3046fabd6
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950593"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>IoT Hub telemetria betöltése az Azure digitális Twinsba

Az Azure digitális Twins a IoT-eszközökről és más forrásokból származó adatokon alapul. Az Azure Digital Twins szolgáltatásban használható eszköz-adatforrások közös forrása [IoT hub](../iot-hub/about-iot-hub.md).

Az adatok Azure digitális Ikrekbe való betöltésének folyamata egy külső számítási erőforrás, például egy [Azure functions](../azure-functions/functions-overview.md)használatával végzett függvény beállítása. A függvény fogadja az összes adatát, és a [DigitalTwins API](/rest/api/digital-twins/dataplane/twins) -kkal állítja be a tulajdonságokat vagy a telemetria eseményeket a [digitális ikreknek](concepts-twins-graph.md) megfelelően. 

Ez a dokumentum végigvezeti a telemetria IoT Hub-ből betölthető függvény írásához használt folyamaton.

## <a name="prerequisites"></a>Előfeltételek

A példának való továbblépés előtt a következő erőforrásokat kell beállítania előfeltételekként:
* **Egy IoT hub**. Útmutatásért tekintse meg a [IoT hub](../iot-hub/quickstart-send-telemetry-cli.md)rövid útmutató *IoT hub létrehozása* című szakaszát.
* **Egy Azure digitális Twins-példány** , amely az eszköz telemetria fogja fogadni. Útmutatásért lásd [*: útmutató: Azure digitális Twins-példány és-hitelesítés beállítása*](./how-to-set-up-instance-portal.md).

### <a name="example-telemetry-scenario"></a>Példa telemetria forgatókönyvre

Ez a útmutató ismerteti, hogyan küldhet üzeneteket IoT Hubról Azure digitális Twins-ra az Azure-függvény használatával. Számos lehetséges konfiguráció és megfelelő stratégia használható az üzenetek küldéséhez, de a cikk példája a következő részeket tartalmazza:
* Egy IoT Hub termosztáttal rendelkező eszköz, egy ismert eszköz azonosítójával
* Egy digitális Twin, amely az eszközt a megfelelő AZONOSÍTÓval jelöli

> [!NOTE]
> Ez a példa egy egyszerű azonosító egyezést használ az eszköz azonosítója és a hozzá tartozó digitális Twin azonosító között, de az eszközről a Twin (például egy leképezési táblával) kifinomultabb leképezések is megadhatók.

Ha a termosztátos eszköz egy hőmérséklet-telemetria eseményt küld, a függvény feldolgozza a telemetria és a digitális Twin *hőmérséklet* tulajdonságát. Ezt a forgatókönyvet az alábbi ábrán ismertetjük:

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Ábrája IoT Hub eszköz küldési hőmérsékleti IoT Hub telemetria az Azure-ban egy függvényre, amely frissíti az Azure-beli digitális Twins-n belül egy adott hőmérséklet-tulajdonságot." border="false":::

## <a name="add-a-model-and-twin"></a>Modell és ikerpéldány felvétele

Ebben a szakaszban egy [Digital Twin](concepts-twins-graph.md) -et állít be az Azure Digital ikrek szolgáltatásban, amely a termosztát-eszközt jelképezi, és a IoT hubról származó információkkal fog frissülni.

A következő típusú termosztát létrehozásához először fel kell töltenie a termosztát [modelljét](concepts-models.md) a példányba, amely leírja a termosztát tulajdonságait, és később a Twin létrehozásához lesz használva. 

[!INCLUDE [digital-twins-thermostat-model-upload.md](../../includes/digital-twins-thermostat-model-upload.md)]

Ezután **létre kell hoznia egy IKeret a modell használatával**. A következő parancs használatával hozzon létre egy **thermostat67** nevű termosztátot, és állítsa be a 0,0 értéket kezdeti hőmérsékletként.

```azurecli-interactive
az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}' --dt-name {digital_twins_instance_name}
```

> [!Note]
> Ha Cloud Shellt használ a PowerShell-környezetben, akkor előfordulhat, hogy el kell kerülnie az idézőjelek karaktereit a beágyazott JSON-mezőkben az értékek megfelelő elemzéséhez. Az alábbi paranccsal hozhatja létre a Twin-et ezzel a módosítással:
>
> Dupla létrehozás:
> ```azurecli-interactive
> az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{\"Temperature\": 0.0,}' --dt-name {digital_twins_instance_name}
> ```

Ha a Twin létrehozása sikeresen megtörtént, a parancssori felület kimenetének a következőhöz hasonlóan kell kinéznie:
```json
{
  "$dtId": "thermostat67",
  "$etag": "W/\"0000000-9735-4f41-98d5-90d68e673e15\"",
  "$metadata": {
    "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
    "Temperature": {
      "ackCode": 200,
      "ackDescription": "Auto-Sync",
      "ackVersion": 1,
      "desiredValue": 0.0,
      "desiredVersion": 1
    }
  },
  "Temperature": 0.0
}
```

## <a name="create-a-function"></a>Függvény létrehozása

Ebben a szakaszban egy Azure-függvényt hoz létre az Azure Digital Twins eléréséhez és az ikrek frissítéséhez a kapott IoT telemetria-események alapján. A függvény létrehozásához és közzétételéhez kövesse az alábbi lépéseket.

#### <a name="step-1-create-a-function-app-project"></a>1. lépés: a Function app-projekt létrehozása

Először hozzon létre egy új Function app-projektet a Visual Studióban. Ennek módjával kapcsolatos útmutatásért tekintse meg a [**Function alkalmazás létrehozása a Visual Studióban**](how-to-create-azure-function.md#create-a-function-app-in-visual-studio) című szakaszt az *útmutató: függvény beállítása az adat feldolgozásához* című cikkben.

#### <a name="step-2-fill-in-function-code"></a>2. lépés: töltse ki a függvény kódját

Adja hozzá a következő csomagokat a projekthez:
* [Azure. DigitalTwins. Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure. Identity](https://www.nuget.org/packages/Azure.Identity/)
* [Microsoft. Azure. webjobs. Extensions. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid/)

Nevezze át a *Function1. cs* minta függvényt, amelyet a Visual Studio generált az új projekttel a *IoTHubtoTwins. cs* használatával. Cserélje le a fájlban található kódot a következő kódra:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs":::

Mentse a függvény kódját.

#### <a name="step-3-publish-the-function-app-to-azure"></a>3. lépés: a Function alkalmazás közzététele az Azure-ban

Tegye közzé a projektet a *IoTHubtoTwins. cs* függvénnyel egy Azure-beli Function alkalmazásban.

Ennek módjával kapcsolatos útmutatásért tekintse meg a [**Function alkalmazás közzététele az Azure**](how-to-create-azure-function.md#publish-the-function-app-to-azure) -ban című szakaszt az *útmutató: funkció beállítása az adat feldolgozásához* című cikkben.

#### <a name="step-4-configure-the-function-app"></a>4. lépés: a Function alkalmazás konfigurálása

Ezután **rendeljen hozzá egy hozzáférési szerepkört** a függvényhez, és **konfigurálja az alkalmazás beállításait** , hogy hozzáférhessen az Azure Digital Twins-példányhoz. Ennek módjáról a következő témakörben talál útmutatást: a [**biztonsági hozzáférés beállítása a Function alkalmazáshoz**](how-to-create-azure-function.md#set-up-security-access-for-the-function-app) az *adatfeldolgozáshoz* című cikkben.

## <a name="connect-your-function-to-iot-hub"></a>A függvény összekapcsolásával IoT Hub

Ebben a szakaszban a függvényt a IoT hub-eszköz adataihoz tartozó eseményként fogja beállítani. Ezzel biztosíthatja, hogy a IoT Hub termosztát eszközének adatait a rendszer a feldolgozásra az Azure-függvénynek küldje el.

A [Azure Portal](https://portal.azure.com/)navigáljon az [*előfeltételek*](#prerequisites) szakaszban létrehozott IoT hub-példányhoz. Az **események** területen hozzon létre egy előfizetést a függvényhez.

:::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Képernyőkép az esemény-előfizetés hozzáadását bemutató Azure Portalról.":::

Az **esemény-előfizetés létrehozása** lapon töltse ki a mezőket a következőképpen:
  1. A **név** mezőben adja meg az esemény-előfizetés kívánt nevét.
  2. Az **Event Schema** esetében válassza _Event Grid sémát_.
  3. A **Rendszertéma neve** mezőben adja meg a kívánt nevet.
  1. Az **eseménytípus szűréséhez** válassza az _eszköz telemetria_ jelölőnégyzetet, és törölje a többi eseménytípus jelölését.
  1. A **végpont típusa** beállításnál válassza az _Azure-függvény_ lehetőséget.
  1. A **végpont** esetében a _válasszon egy végpontot_ hivatkozásra kattintva válassza ki, hogy melyik Azure-függvényt szeretné használni a végponthoz.
    
:::image type="content" source="media/how-to-ingest-iot-hub-data/create-event-subscription.png" alt-text="Képernyőkép a Azure Portalról az esemény-előfizetés részleteinek létrehozásához":::

Az alábbi részleteket az _Azure-függvény kiválasztása_ oldalon nyithatja meg, ellenőrizheti vagy kitöltheti.
 1. **Előfizetés**: az Azure-előfizetése.
 2. **Erőforráscsoport**: az erőforráscsoport.
 3. **Function alkalmazás**: a Function alkalmazás neve.
 4. **Tárolóhely**: _éles üzem_.
 5. **Függvény**: válassza ki a függvényt a korábbi, *IoTHubtoTwins*, a legördülő listából.

Mentse a részleteket a _kijelölés megerősítése_ gombbal.            
      
:::image type="content" source="media/how-to-ingest-iot-hub-data/select-azure-function.png" alt-text="Képernyőkép a Azure Portalről a függvény kiválasztásához.":::

Az esemény-előfizetés létrehozásához kattintson a _Létrehozás_ gombra.

## <a name="send-simulated-iot-data"></a>Szimulált IoT-adatgyűjtés küldése

Az új beáramlási funkció teszteléséhez használja az eszköz-szimulátort az [*oktatóanyag: végpontok közötti megoldás csatlakoztatása*](./tutorial-end-to-end.md). Ezt az oktatóanyagot C# nyelven írt minta projekt vezérli. A mintakód itt található: [Azure digitális Twins végpontok közötti minták](/samples/azure-samples/digital-twins-samples/digital-twins-samples). A **DeviceSimulator** projektet a tárházban fogja használni.

A végpontok közötti oktatóanyagban hajtsa végre a következő lépéseket:
1. [*A szimulált eszköz regisztrálása IoT Hub*](./tutorial-end-to-end.md#register-the-simulated-device-with-iot-hub)
2. [*A szimuláció konfigurálása és futtatása*](./tutorial-end-to-end.md#configure-and-run-the-simulation)

## <a name="validate-your-results"></a>Az eredmények ellenőrzése

A fenti eszköz-szimulátor futtatásakor a rendszer megváltoztatja a digitális iker hőmérséklet-értékét. Az Azure CLI-ben futtassa a következő parancsot a hőmérséklet értékének megtekintéséhez.

```azurecli-interactive
az dt twin query -q "select * from digitaltwins" -n {digital_twins_instance_name}
```

A kimenetnek az alábbihoz hasonló hőmérsékleti értéket kell tartalmaznia:

```json
{
  "result": [
    {
      "$dtId": "thermostat67",
      "$etag": "W/\"0000000-1e83-4f7f-b448-524371f64691\"",
      "$metadata": {
        "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
        "Temperature": {
          "ackCode": 200,
          "ackDescription": "Auto-Sync",
          "ackVersion": 1,
          "desiredValue": 69.75806974934324,
          "desiredVersion": 1
        }
      },
      "Temperature": 69.75806974934324
    }
  ]
}
```

Az érték változásának megtekintéséhez futtassa többször a fenti lekérdezési parancsot.

## <a name="next-steps"></a>Következő lépések

További információ az Azure Digital Twins szolgáltatással való bejövő és kimenő adatforgalomról:
* [*Fogalmak: integráció más szolgáltatásokkal*](concepts-integration.md)

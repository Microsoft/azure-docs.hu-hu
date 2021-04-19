---
title: A hálózati Azure IoT Central-híd | Microsoft Docs
description: Üzembe helyezheti IoT Central eszközhidat, hogy más IoT-felhőket is csatlakoztathat IoT Central alkalmazáshoz. Egyéb IoT-felhők például a Sigfox, a Particle Device Cloud és a The Things Network.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 04/19/2021
ms.topic: how-to
ms.openlocfilehash: 6b535ecb80fae9f55eb6ab11751c26e0c6d0e9e5
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713723"
---
# <a name="use-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>A IoT Central eszközhíd használatával csatlakoztathat más IoT-felhőket a IoT Central

*Ez a cikk a rendszergazdákra vonatkozik.*

## <a name="azure-iot-central-device-bridge"></a>Azure IoT Central eszközhíd

A IoT Central eszközhíd egy nyílt forráskódú megoldás, amely más IoT-felhőket csatlakoztat a IoT Central alkalmazáshoz. Egyéb IoT-felhők például a [Sigfox,](https://www.sigfox.com/) [a Particle Device Cloud](https://www.particle.io/)és a The Things [Network.](https://www.thethingsnetwork.org/) Az eszközhíd úgy működik, hogy továbbítja az adatokat a más IoT-felhőkhöz csatlakoztatott eszközökről a IoT Central alkalmazásba. Az eszközhíd csak a IoT Central továbbítja az adatokat, nem küld parancsokat vagy tulajdonságfrissítéseket a IoT Central az eszközökre.

Az eszközhíd lehetővé teszi, hogy az IoT Central-t olyan eszközökkel kombinálja, mint a Sigfox alacsony áramellátású nagy áramot tartalmazó hálózatához csatlakoztatott eszközkövető eszközök, a particle device Cloud levegőminőség-figyelő eszközei, vagy a The Things Network talajnedvesség-figyelő eszközei. Használhatja az IoT Central funkcióit, például az adatokra vonatkozó szabályokat és elemzéseket, munkafolyamatokat hozhat létre Power Automate Azure Logic-alkalmazásokban, vagy exportálhatja az adatokat.

Az eszközhíd-megoldás több Azure-erőforrást épít ki az Azure-előfizetésbe, amelyek együtt átalakítják és továbbítják az eszközüzeneteket a IoT Central.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató lépéseit egy aktív Azure-előfizetéssel kell végrehajtania.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

A Create [an Azure IoT Central application (Alkalmazás](./quick-deploy-iot-central.md) Azure IoT Central létrehozása) rövid útmutató segítségével hozzon létre egy IoT Central-alkalmazást az Egyéni alkalmazás > **alkalmazássablonnal.**

## <a name="overview"></a>Áttekintés

A IoT Central eszközhíd egy nyílt forráskódú megoldás a GitHubon. Egyéni függvénysablont használ Azure Resource Manager több erőforrást helyez üzembe az Azure-előfizetésben, beleértve egy Azure-függvényalkalmazást is.

A függvényalkalmazás az eszközhidak alapvető része. Http POST-kéréseket fogad más IoT-platformokról egy egyszerű webhookon keresztül. A [Azure IoT Central Device Bridge](https://github.com/Azure/iotc-device-bridge) adattár olyan példákat tartalmaz, amelyek bemutatják a Sigfox, a Particle és a The Things Network felhők csatlakoztatását. Ezt a megoldást kiterjesztheti az egyéni IoT-felhőhöz való csatlakozáshoz, ha a platform http POST-kéréseket tud küldeni a függvényalkalmazásnak.

A függvényalkalmazás átalakítja az adatokat egy, a IoT Central által elfogadott formátumba, és továbbítja őket az eszközátépítési szolgáltatás és az eszköz ügyféloldali API-i segítségével:

:::image type="content" source="media/howto-build-iotc-device-bridge/azure-function.png" alt-text="Képernyőkép az Azure Functions képernyőképről.":::

Ha a IoT Central felismeri a továbbított üzenetben található eszközazonosítót, az eszközről származó telemetria megjelenik a IoT Central. Ha a IoT Central-alkalmazás nem ismeri fel az eszközazonosítót, a függvényalkalmazás megpróbál regisztrálni egy új eszközt az eszközazonosítóval. Az új eszköz társítás  nélküli eszközként jelenik  meg az alkalmazás Eszközök IoT Central lapján. Az Eszközök **lapon** társíthatja az új eszközt egy eszközsablonnal, majd megtekintheti a telemetriát.

## <a name="deploy-the-device-bridge"></a>Az eszközhíd üzembe helyezése

Az eszközhíd üzembe helyezése az előfizetésben:

1. A saját IoT Central nyissa meg az Adminisztráció és **> lapon.**

    1. Jegyezze fel az azonosító **hatókörét.** Ezt az értéket az eszközhíd üzembe helyezésekor használhatja.

    1. Ugyanezen az oldalon nyissa meg a **SAS-IoT-Devices** regisztrációs csoportot. A **SAS-IoT-Devices csoportlapon** másolja ki az **Elsődleges kulcsot.** Ezt az értéket az eszközhíd üzembe helyezésekor használhatja.

1. Az alábbi **Üzembe helyezés az Azure-ban** gombbal megnyithatja az egyéni Resource Manager sablont, amely üzembe helyezi a függvényalkalmazást az előfizetésében. Használja az előző  **lépés azonosítóhatókörét** és elsődleges kulcsát:

    [![Üzembe helyezés az Azure-ban](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fiotc-device-bridge%2Fmaster%2Fazuredeploy.json)

Az üzembe helyezés befejezése után telepítenie kell a függvény által megkövetelt NPM-csomagokat:

1. A Azure Portal nyissa meg az előfizetésben üzembe helyezett függvényalkalmazást. Ezután lépjen a **Fejlesztői eszközök > konzolra.** A -konzolon futtassa a következő parancsokat a csomagok telepítéséhez:

    ```bash
    cd IoTCIntegration
    npm install
    ```

    Ezek a parancsok több percig is eltarthatnak. A figyelmeztető üzeneteket nyugodtan figyelmen kívül hagyhatja.

1. A csomag telepítésének befejezése után válassza az **Újraindítás** lehetőséget **a** függvényalkalmazás Áttekintés lapján:

    :::image type="content" source="media/howto-build-iotc-device-bridge/restart.png" alt-text="Az Újraindítás képernyőképe.":::

1. A függvény most már használatra kész. A külső rendszerek HTTP POST-kérésekkel küldhetnek eszközadatokat az eszközhidon keresztül a IoT Central alkalmazásba. A függvény URL-címének le > **az IoTCIntegration**> Code + Test > Get function URL (Függvény URL-címének le > le):

    :::image type="content" source="media/howto-build-iotc-device-bridge/get-function-url.png" alt-text="Képernyőkép a Függvény URL-címének lekért címről.":::

Az eszközhidra küldött üzenettárak formátumának a következőnek kell lennie:

```json
"device": {
  "deviceId": "my-cloud-device"
},
"measurements": {
  "temp": 20.31,
  "pressure": 50,
  "humidity": 8.5,
  "ledColor": "blue"
}
```

Az objektum minden kulcsának meg kell egyeznie a telemetriatípus nevével az eszközsablonban a `measurements` IoT Central alkalmazásban. Ez a megoldás nem támogatja az interfészazonosító megadását az üzenet törzsében. Ha tehát két különböző felületnek ugyanaz a neve, a mérés mindkét telemetriastreamben megjelenik a IoT Central alkalmazásban.

A szöveg törzsébe be is foglalhat egy mezőt az üzenet UTC szerinti dátumának és `timestamp` időpontjanak megadásához. Ennek a mezőnek ISO 8601 formátumúnak kell lennie. Például: `2020-06-08T20:16:54.602Z`. Ha nem ad meg időbélyeget, a rendszer az aktuális dátumot és időt használja.

A törzsbe `modelId` egy mezőt is be lehet foglalni. Ezzel a mezővel társítja az eszközt egy eszközsablonhoz a kiépítés során. Ezt a funkciót csak a [3-as verziós alkalmazások támogatják.](howto-get-app-info.md)

A `deviceId` értéknek alfanumerikusnak és kisbetűsnek kell lennie, és kötőjelet tartalmazhat.

Ha nem foglalja bele a mezőt, vagy ha IoT Central nem ismeri fel a modellazonosítót, akkor egy ismeretlen üzenet létrehoz egy új, nem társított eszközt `modelId` `deviceId` a IoT Central.  Az operátorok manuálisan is átemelheti az eszközt a megfelelő eszközsablonba. További információ: Eszközök kezelése a Azure IoT Central [alkalmazásban > Eszközök áttelepítése sablonba.](howto-manage-devices.md)

A [V2 alkalmazásokban](howto-get-app-info.md)az új eszköz megjelenik a Device Explorer > **eszközök** lapon. Válassza **a Társítás lehetőséget,** és válasszon egy eszközsablont, hogy elkezdődjön a bejövő telemetria fogadása az eszközről.

> [!NOTE]
> Amíg az eszköz nincs sablonhoz társítva, a függvénynek minden HTTP-hívása 403-as hibaállapotot ad vissza.

Ha bekapcsolja a naplózást a függvényalkalmazáshoz  a Application Insights, lépjen a > naplók figyelése a függvényalkalmazásban a Azure Portal. Válassza **a Bekapcsolás Application Insights** lehetőséget.

## <a name="provisioned-resources"></a>Kiépített erőforrások

A Resource Manager sablon a következő erőforrásokat tartalmazza az Azure-előfizetésben:

* Függvényalkalmazás
* App Service-csomag
* Tárfiók
* Key Vault

A kulcstartó tárolja az sas-csoportkulcsot a IoT Central számára.

A függvényalkalmazás használaton belüli [csomagon fut.](https://azure.microsoft.com/pricing/details/functions/) Bár ez a lehetőség nem kínál dedikált számítási erőforrásokat, lehetővé teszi az eszközhíd számára, hogy percenként több száz eszközüzenetet kezeljen, ami kisebb méretű eszközök vagy eszközök kisebb, ritkábban üzeneteket küldünk. Ha az alkalmazás nagy mennyiségű eszközüzenet streamelésétől függ, cserélje le a használatban való használatot egy dedikált [App Service-csomagra.](https://azure.microsoft.com/pricing/details/app-service/windows/) Ez a csomag dedikált számítási erőforrásokat kínál, amelyek gyorsabb válaszidőt adnak a kiszolgálónak. Standard App Service Plan használatával az Azure-függvény maximális megfigyelt teljesítménye ebben az adattárban percenként körülbelül 1500 eszközüzenet volt. További információ: [Az Azure-függvények üzemeltetési lehetőségei.](../../azure-functions/functions-scale.md)

Ha használathasználati csomag helyett dedikált App Service, az üzembe helyezés előtt szerkessze az egyéni sablont. Válassza **a Sablon szerkesztése lehetőséget.**

:::image type="content" source="media/howto-build-iotc-device-bridge/edit-template.png" alt-text="Képernyőkép a Sablon szerkesztése elemről.":::

Cserélje le a következő szegmenst:

```json
{
  "type": "Microsoft.Web/serverfarms",
  "apiVersion": "2015-04-01",
  "name": "[variables('planName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "name": "[variables('planName')]",
    "computeMode": "Dynamic",
    "sku": "Dynamic"
  }
},
```

nevű és

```json
{
  "type": "Microsoft.Web/serverfarms",
  "sku": {
      "name": "S1",
      "tier": "Standard",
      "size": "S1",
      "family": "S",
      "capacity": 1
  },
  "kind": "app",
  "name": "[variables('planName')]",
  "apiVersion": "2016-09-01",
  "location": "[resourceGroup().location]",
  "tags": {
      "iotCentral": "device-bridge",
      "iotCentralDeviceBridge": "app-service-plan"
  },
  "properties": {
      "name": "[variables('planName')]"
  }
},
```

Ezután szerkessze a sablont úgy, hogy szerepeljen az erőforrás konfigurációjában az AlwaysOn konfiguráció alatt, hogy a függvényalkalmazás `"alwaysOn": true` `functionapp` mindig `"properties": {"SiteConfig": {...}}` futjon. [](https://github.com/Azure/Azure-Functions/wiki/Enable-Always-On-when-running-on-dedicated-App-Service-Plan)

## <a name="examples"></a>Példák

Az alábbi példák bemutatják, hogyan konfigurálhatja az eszközhidat különböző IoT-felhőkhöz:

### <a name="example-1-connecting-particle-devices-through-the-device-bridge"></a>1. példa: Részecskék eszközeinek csatlakoztatása az eszközhidon keresztül

Ha egy részecskéket tartalmazó eszközt az eszközhidon keresztül szeretne csatlakoztatni a IoT Central-hez, lépjen a Particle konzolra, és hozzon létre egy új webhook-integrációt. A **Kérelemformátum beállításnál adja meg a** **JSON formátumot.**  A **Speciális beállítások alatt** használja az alábbi egyéni törzsformátumot:

```json
{
  "device": {
    "deviceId": "{{{PARTICLE_DEVICE_ID}}}"
  },
  "measurements": {
    "{{{PARTICLE_EVENT_NAME}}}": {{{PARTICLE_EVENT_VALUE}}}
  }
}
```

Illessze be **a függvény URL-címét** az Azure-függvényalkalmazásból, és láthatja, hogy a részecskék eszközei nem társított eszközként jelennek meg a IoT Central. További tudnivalókért tekintse [](https://blog.particle.io/2019/09/26/integrate-particle-with-azure-iot-central/) meg a Következő blogbejegyzést, amelyből megtudhatja, hogyan integrálhatja a Azure IoT Central-projekteket.

### <a name="example-2-connecting-sigfox-devices-through-the-device-bridge"></a>2. példa: A Sigfox-eszközök csatlakoztatása az eszközhidon keresztül

Egyes platformokon nem lehet megadni a webhookon keresztül küldött eszközüzenetek formátumát. Ilyen rendszerek esetében az üzenet hasznos adatát a várt törzsformátumra kell konvertálni, mielőtt az eszközhíd feldolgozza azt. Az átalakítást az eszközhidat futtató Azure-függvényben is el lehet használhatja.

Ez a szakasz bemutatja, hogyan konvertálhatja egy Sigfox webhook integrációjának hasznos adatát az eszközhíd által várt törzsformátumra. A Sigfox felhő hexadecimális sztring formátumban továbbítja az eszközadatokat. Az egyszerűség kedvéért az eszközhíd tartalmaz egy konverziós függvényt ehhez a formátumhoz, amely fogadja a Sigfox eszköz hasznos adatában a lehetséges mezőtípusok egy részét: és `int` `uint` 8, 16, 32 vagy 64 bites; 32 bites vagy `float` 64 bites; kis endian és big endian. A Sigfox webhook-integrációból származó üzenetek feldolgozásához a következő módosításokat kell eszközlni az _IoTCIntegration/index.js_ fájlban a függvényalkalmazásban.

Az üzenet hasznos adatokat úgy konvertálhatja át, hogy hozzáadja a következő kódot a 21. sorban a hívása előtt, és cserélje le a kódot a `handleMessage` `payloadDefinition` Sigfox hasznos adatokat definiáló definícióra:

```javascript
const payloadDefinition = 'gforce::uint:8 lat::uint:8 lon::uint:16'; // Replace this with your payload definition

req.body = {
    device: {
        deviceId: req.body.device
    },
    measurements: require('./converters/sigfox')(payloadDefinition, req.body.data)
};
```

A Sigfox-eszközök válaszkódot `204` várnak. Adja hozzá a következő kódot a `handleMessage` 21. sorban a hívása után:

```javascript
context.res = {
    status: 204
};
```

### <a name="example-3-connecting-devices-from-the-things-network-through-the-device-bridge"></a>3. példa: Eszközök csatlakoztatása az eszközhálózatról az eszközhidon keresztül

Az eszközök hálózati eszközeinek csatlakoztatása IoT Central:

* Adjon hozzá egy új HTTP-integrációt az alkalmazáshoz a The Things Network: Application > Integrations > add integration > HTTP Integration (Az >: Alkalmazás-integrációk) > **HTTP-integrációval.**
* Győződjön meg arról, hogy az alkalmazás tartalmaz egy dekóder függvényt, amely automatikusan konvertálja az eszközüzenetek hasznos adatokat JSON formátumba, mielőtt az azure-függvénybe kerül: **Application > Payload Functions > dekóder**.

Az alábbi minta egy JavaScript-dekóder függvényt mutat be, amely a bináris adatok gyakori numerikus típusainak dekódolása érdekében használható:

```javascript
function Decoder(bytes, port) {
  function bytesToFloat(bytes, decimalPlaces) {
    var bits = (bytes[3] << 24) | (bytes[2] << 16) | (bytes[1] << 8) | bytes[0];
    var sign = (bits >>> 31 === 0) ? 1.0 : -1.0;
    var e = bits >>> 23 & 0xff;
    var m = (e === 0) ? (bits & 0x7fffff) << 1 : (bits & 0x7fffff) | 0x800000;
    var f = Math.round((sign * m * Math.pow(2, e - 150)) * Math.pow(10, decimalPlaces)) / Math.pow(10, decimalPlaces);
    return f;
  }

  function bytesToInt32(bytes, signed) {
    var bits = bytes[0] | (bytes[1] << 8) | (bytes[2] << 16) | (bytes[3] << 24);
    var sign = 1;

    if (signed && bits >>> 31 === 1) {
      sign = -1;
      bits = bits & 0x7FFFFFFF;
    }

    return bits * sign;
  }

  function bytesToShort(bytes, signed) {
    var bits = bytes[0] | (bytes[1] << 8);
    var sign = 1;

    if (signed && bits >>> 15 === 1) {
      sign = -1;
      bits = bits & 0x7FFF;
    }

    return bits * sign;
  }

  return {
    temperature: bytesToFloat(bytes.slice(0, 4), 2),
    presscounter: bytesToInt32(bytes.slice(4, 8), true),
    blueLux: bytesToShort(bytes.slice(8, 10), false)
  };
}
```

Az integráció definiálása után adja hozzá a következő kódot az Azure-függvényalkalmazás `handleMessage` *IoTCIntegration/index.js* 21. sorában található hívása előtt. Ez a kód lefordítja a HTTP-integráció törzsét a várt formátumra.

```javascript
device: {
    deviceId: req.body.hardware_serial.toLowerCase()
},
measurements: req.body.payload_fields
};
```

## <a name="limitations"></a>Korlátozások

Az eszközhíd csak a IoT Central továbbítja az üzeneteket, és nem küld vissza üzeneteket az eszközökre. Ezért a tulajdonságok és parancsok nem működnek az ezen az eszközhídon keresztül IoT Central eszközökhöz. Mivel az ikereszköz-műveletek nem támogatottak, nem lehet frissíteni az eszköztulajdonságokat az eszközhidon keresztül. Ezeknek a funkcióknak a használata esetén az eszköznek közvetlenül kell csatlakoznia a IoT Central az [Azure IoT eszköz-ADATTK-k egyikével.](../../iot-hub/iot-hub-devguide-sdks.md)

## <a name="next-steps"></a>Következő lépések
Most, hogy megtanulta, hogyan helyezheti üzembe IoT Central eszközhidat, a következő javasolt lépés:

> [!div class="nextstepaction"]
> [Eszközök kezelése](howto-manage-devices.md)

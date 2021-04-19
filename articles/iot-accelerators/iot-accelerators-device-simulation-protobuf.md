---
title: Protokollpufferek használata eszközszimulációval – Azure| Microsoft Docs
description: Ebből az útmutatóból megtudhatja, hogyan használhatók a protokollpufferek az eszközszimulációs megoldásgyorsítóból küldött telemetria szerializálására.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc, amqp, devx-track-csharp
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: a94f3cc6b2387857d19c7b98fa0be64dd6646ec9
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713857"
---
# <a name="serialize-telemetry-using-protocol-buffers"></a>Telemetria szerializálása protokollpufferek használatával

A protokollpufferek (Protobuf) a strukturált adatok bináris szerializálási formátuma. A Protobuf célja az egyszerűség és a teljesítmény hangsúlyozása azzal a céllal, hogy kisebb és gyorsabb legyen, mint az XML.

Az Eszközszimuláció a **protokollpufferek nyelvének proto3** verzióját támogatja.

Mivel a Protobuf lefordított kódot igényel az adatok szerializálásához, az Eszközszimuláció egy egyéni verzióját kell létrehoznia.

Az útmutató lépései a következőket mutatják be:

1. Fejlesztési környezet előkészítése
1. A megadása a Protobuf formátummal egy eszközmodellben
1. A Protobuf formátum meghatározása
1. Protobuf-osztályok létrehozása
1. Helyi tesztelés

## <a name="prerequisites"></a>Előfeltételek

Az útmutató lépéseit a következőkre lesz szüksége:

* Visual Studio Code. A Code [Visual Studio Mac, Linux és Windows rendszerekre](https://code.visualstudio.com/download)is letölthető.
* .NET Core. A [.NET Core Mac, Linux és Windows rendszerekre is letölthető.](https://www.microsoft.com/net/download)
* Postás. A [Postman Macre, Windowsra vagy Linuxra is letölthető.](https://www.getpostman.com/apps)
* Az [Azure-előfizetésben üzembe helyezett IoT Hub.](../iot-hub/iot-hub-create-through-portal.md) Az útmutató lépéseit az IoT Hub kapcsolati sztringje segítségével tudja végrehajtani. A kapcsolati sztringet a következőből Azure Portal.
* Egy [Cosmos DB, amely az SQL](../cosmos-db/create-sql-api-dotnet.md#create-account) API-t használja, és erős konzisztenciára van konfigurálva az Azure-előfizetésében. [](../cosmos-db/how-to-manage-database-account.md) Az útmutató Cosmos DB az adatbázis kapcsolati sztringje szükséges. A kapcsolati sztringet a következő Azure Portal.
* Az [Azure-előfizetésben üzembe helyezett Azure Storage-fiók.](../storage/common/storage-account-create.md) Az útmutató lépéseit a tárfiók kapcsolati sztringje segítségével tudja végrehajtani. A kapcsolati sztringet a következő Azure Portal.

## <a name="prepare-your-development-environment"></a>A fejlesztőkörnyezet előkészítése

A fejlesztési környezet előkészítéséhez a következő feladatokat kell elvégeznie:

* Töltse le az eszközszimulációs mikroszolgáltatás forrását.
* Töltse le a tárolóadapter mikroszolgáltatásának forrását.
* Futtassa helyileg a tárolóadapter mikroszolgáltatását.

A cikkben található utasítások feltételezik, hogy Windowst használ. Ha másik operációs rendszert használ, előfordulhat, hogy módosítania kell néhány fájl elérési útját és parancsát a környezetének megfelelően.

### <a name="download-the-microservices"></a>A mikroszolgáltatások letöltése

Töltse le és csomagolja ki a Távoli monitorozási [mikroszolgáltatások](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) adatokat a GitHubról egy megfelelő helyre a helyi gépen. Ez az adattár tartalmazza az ehhez a megoldáshoz szükséges tárolóadapter-mikroszolgáltatást.

Töltse le és csomagolja ki az [eszközszimulációs mikroszolgáltatást](https://github.com/Azure/azure-iot-pcs-device-simulation/archive/master.zip) a GitHubról egy megfelelő helyre a helyi gépen.

### <a name="run-the-storage-adapter-microservice"></a>A tárolóadapter mikroszolgáltatásának futtatása

A Visual Studio Code-ban nyissa meg **a remote-monitoring-services-dotnet-master\storage-adapter mappát.** A **feloldatlan** függőségek kijavítása a Visszaállítás gombokra kattintva oldható meg.

Nyissa meg a **.vscode/launch.js** fájlt, és rendelje hozzá Cosmos DB kapcsolati sztringet a **PCS \_ STORAGEADAPTER \_ DOCUMENTDB \_ CONNSTRING környezeti** változóhoz.

> [!NOTE]
> Amikor helyileg futtatja a mikroszolgáltatást a gépen, a megfelelő Cosmos DB azure-beli virtuálisgép-példányra van szükség.

A tárolóadapter mikroszolgáltatásának helyi futtatásához kattintson **a Hibakeresés \> indítási hibakeresése elemre.**

Az **Visual Studio** Code Terminál ablakában megjelenik a futó mikroszolgáltatás kimenete, beleértve a webszolgáltatás állapot-ellenőrzéséhez tartozó URL-címet: <http://127.0.0.1:9022/v1/status> . Ha erre a címre navigál, az állapotnak "OK: Életben és jól" kell lennie.

Hagyja futni a tárolóadapter mikroszolgáltatását az Visual Studio Code ezen példányában, amíg a következő lépéseket végre nem követi.

## <a name="define-your-device-model"></a>Az eszközmodell meghatározása

Nyissa meg a **GitHubról letöltött device-simulation-dotnet-master** mappát a Visual Studio Code-ban. Kattintson bármelyik **Visszaállítás gombra** a feloldatlan függőségek kijavításaként.

Ebben az útmutatóban egy új eszközmodellt hoz létre az eszközkövetőhöz:

1. Hozzon létre egy új,assettracker-01.js **nevű** eszközmodell-fájlt a **Services\data\devicemodels mappában.**

1. Határozza meg az eszközmodell eszközfunkcióját, **assettracker-01.jsfájlban.** A Protobuf eszközmodell telemetriai szakaszának a következőnek kell lennie:

   * Foglalja bele az eszközhöz generált Protobuf osztály nevét. A következő szakasz bemutatja, hogyan hozhatja létre ezt az osztályt.
   * Adja meg a Protobuf üzenetformátumot.

     ```json
     {
     "SchemaVersion": "1.0.0",
     "Id": "assettracker-01",
     "Version": "0.0.1",
     "Name": "Asset Tracker",
     "Description": "An asset tracker with location, temperature, and humidity",
     "Protocol": "AMQP",
     "Simulation": {
       "InitialState": {
         "online": true,
         "latitude": 47.445301,
         "longitude": -122.296307,
         "temperature": 38.0,
         "humidity": 62.0
       },
       "Interval": "00:01:00",
       "Scripts": [
         {
           "Type": "javascript",
           "Path": "assettracker-01-state.js"
         }
       ]
     },
     "Properties": {
       "Type": "AssetTracker",
       "Location": "Field",
       "Latitude": 47.445301,
       "Longitude": -122.296307
     },
     "Telemetry": [
       {
         "Interval": "00:00:10",
         "MessageTemplate": "{\"latitude\":${latitude},\"longitude\":${longitude},\"temperature\":${temperature},\"humidity\":${humidity}}",
         "MessageSchema": {
           "Name": "assettracker-sensors;v1",
           "ClassName": "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf.AssetTracker",
           "Format": "Protobuf",
           "Fields": {
             "latitude": "double",
             "longitude": "double",
             "temperature": "double",
             "humidity": "double"
           }
         }
       }
     ]
     }
     ```

### <a name="create-device-behaviors-script"></a>Eszközviselkedési szkript létrehozása

Írja meg az eszköz viselkedését meghatározó viselkedési szkriptet. További információ: [Speciális szimulált eszköz létrehozása.](iot-accelerators-device-simulation-advanced-device.md)

## <a name="define-your-protobuf-format"></a>A Protobuf formátum meghatározása

Ha már van eszközmodellje, és meghatározta az üzenetformátumot, létrehozhat egy **prototípus-fájlt.** A **proto fájlban** a következőt kell hozzáadnia:

* Egy, amely megegyezik az eszközmodell `csharp_namespace` **ClassName** tulajdonságával.
* Az egyes adatstruktúrák szerializálására vonatkozó üzenet.
* Az üzenet egyes mezőinek neve és típusa.

1. Hozzon létre egy új fájlt **assettracker.proto** néven a **Services\Models\Protobuf\proto mappában.**

1. Definiálja a szintaxist, a névteret és az üzenetsémát a **prototípus-fájlban** az alábbiak szerint:

    ```proto
    syntax = "proto3";

    option csharp_namespace = "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf";

    message AssetTracker {
      double latitude=1;
      double longitude=2;
      double temperature=5;
      double humidity=6;
    }
    ```

Az egyes elemekhez tartozó , jelölők a mező által a bináris kódolásban használt `=1` `=2` egyedi címkét határozzák meg. Az 1-15-ös számok kódolásához 1-15 bájtnál kevesebb bájt szükséges.

## <a name="generate-the-protobuf-class"></a>A Protobuf osztály létrehozása

ha van **egy prototípus-fájlja,** a következő lépés az üzenetek olvasására és írására szükséges osztályok létrehozása. A lépés befejezéséhez szüksége lesz a **Protoc Protobuf** fordítóra.

1. [A Protobuf-fordító letöltése a GitHubról](https://github.com/protocolbuffers/protobuf/releases/download/v3.4.0/protoc-3.4.0-win32.zip)

1. Futtassa a fordítót, és adja meg a forráskönyvtárat, a célkönyvtárat és a **prototípus-fájl** nevét. Például:

    ```cmd
    protoc -I c:\temp\device-simulation-dotnet-master\Services\Models\Protobuf\proto --csharp_out=C:\temp\device-simulation-dotnet-master\Services\Models\Protobuf assettracker.proto
    ```

    Ez a parancs létrehoz egy **Assettracker.cs** fájlt a **Services\Models\Protobuf mappában.**

## <a name="test-protobuf-locally"></a>A Protobuf helyi tesztelése

Ebben a szakaszban az előző szakaszokban létrehozott eszközkövető eszközt teszteli helyileg.

### <a name="run-the-device-simulation-microservice"></a>Az eszközszimulációs mikroszolgáltatás futtatása

Nyissa meg **a .vscode/launch.jsfájlt,** és rendelje hozzá a következőt:

* IoT Hub a **PCS \_ IOTHUB \_ CONNSTRING környezeti** változóhoz.
* Tárfiók kapcsolati sztring a **PCS \_ AZURE STORAGE \_ ACCOUNT \_ környezeti** változóhoz.
* Cosmos DB a **PCS \_ STORAGEADAPTER \_ DOCUMENTDB \_ CONNSTRING környezeti** változóhoz.

Nyissa meg **a WebService/Properties/launchSettings.jsfájlt,** és rendelje hozzá a következőt:

* IoT Hub a **PCS \_ IOTHUB \_ CONNSTRING környezeti** változóhoz.
* Tárfiók kapcsolati sztring a **PCS \_ AZURE STORAGE \_ ACCOUNT \_ környezeti** változóhoz.
* Cosmos DB a **PCS \_ STORAGEADAPTER \_ DOCUMENTDB \_ CONNSTRING környezeti** változóhoz.

Nyissa meg **WebService\appsettings.ini** fájlt, és módosítsa a beállításokat a következőképpen:

#### <a name="configure-the-solution-to-include-your-new-device-model-files"></a>A megoldás konfigurálása úgy, hogy tartalmazza az új eszközmodell fájljait

Alapértelmezés szerint az új eszközmodell JSON- és JS-fájljai nem lesznek átmásolva az beépített megoldásba. Ezeket explicit módon kell szerepeltetni.

Minden egyes kívánt fájlhoz adjon hozzá egy bejegyzést a **services\services.csproj** fájlhoz. Például:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

A mikroszolgáltatás helyi futtatásához kattintson **a Hibakeresés \> indítási hibakeresése elemre.**

A **Kód Visual Studio** terminálablakában megjelenik a futó mikroszolgáltatás kimenete.

Hagyja futni az eszközszimulációs mikroszolgáltatást az Visual Studio Code ezen példányában a következő lépések során.

### <a name="set-up-a-monitor-for-device-events"></a>Figyelő beállítása az eszközesemények számára

Ebben a szakaszban az Azure CLI használatával fog beállítani egy eseményfigyelőt az IoT Hubhoz csatlakoztatott eszközökről küldött telemetria megtekintéséhez.

A következő szkript feltételezi, hogy az IoT Hub neve **device-simulation-test.**

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

A szimulált eszközök tesztelése közben hagyja futni az eseményfigyelőt.

### <a name="create-a-simulation-with-the-asset-tracker-device-type"></a>Szimuláció létrehozása az eszközkövető eszköztípussal

Ebben a szakaszban a Postman eszközzel kérheti le az eszközszimulációs mikroszolgáltatást egy szimuláció futtatására az eszközkövető eszköztípussal. A Postman egy olyan eszköz, amellyel REST-kéréseket küldhet egy webszolgáltatásnak.

A Postman beállítása:

1. Nyissa meg a Postmant a helyi gépen.

1. Kattintson a **Fájl \> importálása elemre.** Ezután kattintson a **Choose Files (Fájlok kiválasztása) elemre.**

1. Válassza **ki az Azure IoT Eszközszimulációs megoldásgyorsító.postman \_ gyűjteményt** és az Azure IoT Eszközszimulációs megoldásgyorsító.postman **\_ környezetet,** majd kattintson a **Megnyitás gombra.**

1. Bontsa ki az **Azure IoT Eszközszimulációs** megoldásgyorsítót az elküldhető kérések megtekintéséhez.

1. Kattintson **a Nincs környezet elemre,** és válassza az Azure **IoT Eszközszimulációs megoldásgyorsító lehetőséget.**

Most már be van töltve egy gyűjtemény és környezet a Postman-munkaterületre, amely az eszközszimulációs mikroszolgáltatással való interakcióra használható.

A szimuláció konfigurálása és futtatása:

1. A Postman-gyűjteményben válassza az **Eszközkövető szimuláció** létrehozása lehetőséget, majd kattintson a **Küldés gombra.** Ez a kérés a szimulált eszközkövető eszköztípus négy példányát hozza létre.

1. Az Azure CLI ablakában az eseményfigyelő kimenete megjeleníti a szimulált eszközök telemetriát.

A szimuláció leállításhoz válassza a **Szimuláció** leállítása kérelmet a Postmanben, majd kattintson a Send (Küldés) **gombra.**

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Leállíthatja a két helyileg futó mikroszolgáltatást az Visual Studio Code-példányban (**\> Hibakeresési hibakeresés – Hibakeresés**– hibakeresés ).

Ha már nincs szüksége a IoT Hub és Cosmos DB, törölje őket az Azure-előfizetéséből a szükségtelen költségek elkerülése érdekében.

## <a name="iot-hub-support"></a>IoT Hub támogatás

Számos IoT Hub funkció nem támogatja natív módon a Protobuf vagy más bináris formátumokat. Nem irányíthat például az üzenetek hasznos adatok alapján, mert IoT Hub nem tudja feldolgozni az üzenet hasznos adatokat. Az útvonal azonban az üzenetfejlécek alapján is lehet.

## <a name="next-steps"></a>Következő lépések

Most, hogy megtanulta, hogyan szabhatja testre az eszközszimulációt, hogy a Protobuf használatával [](https://github.com/Azure/azure-iot-pcs-device-simulation)küldjön telemetriai adatokat, a következő lépés a GitHub-adattár, ahol további információt olvashat az eszközszimulációról.

---
title: Protokoll-pufferek használata a Device szimulációs szolgáltatással – Azure | Microsoft Docs
description: Ebben a útmutatóban megtudhatja, hogyan használhatja a protokollok puffereit az eszköz-szimulációs megoldás-gyorssegédből eljuttatott telemetria szerializálásához.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc, amqp, devx-track-csharp
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: 77a795b8f704084b612a2dcbd364b8d5be0a3333
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96852150"
---
# <a name="serialize-telemetry-using-protocol-buffers"></a>Telemetria szerializálása protokoll-pufferek használatával

A protokoll-pufferek (Protopuf) a strukturált adathalmazok bináris szerializálási formátuma. A protopuf úgy lett kialakítva, hogy az egyszerűség és a teljesítmény kihangsúlyozása érdekében az XML-nél kisebb és gyorsabb legyen a célja.

Az eszköz szimulációja támogatja a **proto3** nyelvének verzióját.

Mivel a Protopuf lefordított kódot igényel az adatszerializáláshoz, az eszköz-szimuláció egyéni verzióját kell létrehoznia.

A útmutató lépései a következőket mutatják be:

1. Fejlesztési környezet előkészítése
1. A Protopuf formátum használata az eszköz modelljében
1. A Protopuf formátumának meghatározása
1. Protopuf osztályok előállítása
1. Helyi tesztelés

## <a name="prerequisites"></a>Előfeltételek

A jelen útmutató lépéseinek követéséhez a következőkre lesz szüksége:

* Visual Studio Code. [A Visual Studio Code letölthető Mac, Linux és Windows rendszerekhez](https://code.visualstudio.com/download).
* .NET Core. [A .net Core for Mac, Linux és Windows rendszerhez](https://www.microsoft.com/net/download)is letölthető.
* Postman. [A Poster letölthető Mac, Windows vagy Linux rendszerű számítógépekre](https://www.getpostman.com/apps).
* Az [Azure-előfizetéshez üzembe helyezett IoT hub](../iot-hub/iot-hub-create-through-portal.md). Az útmutató lépéseinek elvégzéséhez szüksége lesz az IoT hub kapcsolódási karakterláncára. A Azure Portal a kapcsolatok sztringjét is beszerezheti.
* Az [Azure-előfizetéshez üzembe helyezett Cosmos db-adatbázis](../cosmos-db/create-sql-api-dotnet.md#create-account) , amely az SQL API-t használja, és amely [erős konzisztencia](../cosmos-db/how-to-manage-database-account.md)használatára van konfigurálva. A jelen útmutató lépéseinek elvégzéséhez szüksége lesz a Cosmos DB adatbázisához tartozó kapcsolódási karakterláncra. A Azure Portal a kapcsolatok sztringjét is beszerezheti.
* Az [Azure-előfizetéshez üzembe helyezett Azure Storage-fiók](../storage/common/storage-account-create.md). Az útmutató lépéseinek elvégzéséhez szüksége lesz a Storage-fiók kapcsolódási karakterláncára. A Azure Portal a kapcsolatok sztringjét is beszerezheti.

## <a name="prepare-your-development-environment"></a>A fejlesztőkörnyezet előkészítése

A fejlesztési környezet előkészítéséhez végezze el a következő feladatokat:

* Töltse le az eszköz szimulációs szolgáltatásának forrását.
* Töltse le a Storage adapter-szolgáltatás forrását.
* Futtassa helyileg a Storage-adapter-szolgáltatást.

A cikkben szereplő utasítások feltételezik, hogy a Windowst használja. Ha másik operációs rendszert használ, előfordulhat, hogy módosítania kell néhány fájl elérési útját és parancsát a környezetének megfelelően.

### <a name="download-the-microservices"></a>A szervizcsomagok letöltése

Töltse le és csomagolja ki a [távoli monitorozási szolgáltatásokat](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) a githubról a helyi gépen található megfelelő helyre. Ez a tárház tartalmazza az ehhez az útmutatóhoz szükséges Storage-adapter-szolgáltatást.

Töltse le és csomagolja ki az [eszköz szimulációs szolgáltatását](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) a githubról egy megfelelő helyre a helyi gépen.

### <a name="run-the-storage-adapter-microservice"></a>A Storage-adapter szolgáltatásának futtatása

A Visual Studio Code-ban nyissa meg a **Remote-Monitoring-Services-DotNet-master\storage-adapter** mappát. A feloldatlan függőségek kijavításához kattintson bármelyik **visszaállítási** gombra.

Nyissa meg a **. vscode/launch.js** fájlt, és rendelje hozzá a Cosmos db-kapcsolódási karakterláncot a **számítógépek \_ STORAGEADAPTER \_ DOCUMENTDB \_ CONNSTRING** környezeti változóhoz.

> [!NOTE]
> Ha helyileg futtatja a szolgáltatást a gépen, akkor továbbra is szükség van egy Cosmos DB példányra az Azure-ban, hogy megfelelően működjön.

Ha helyileg szeretné futtatni a Storage-adaptert, kattintson a hibakeresés **\> megkezdése** elemre.

A Visual Studio Code-ban található **Terminálablak** a futó szolgáltatás kimenetét jeleníti meg, beleértve a webszolgáltatás állapot-ellenőrzési URL-címét: <http://127.0.0.1:9022/v1/status> . Amikor navigál erre a címre, az állapotnak "OK: Alive and Well" értéknek kell lennie.

A következő lépések elvégzése után a Visual Studio Code ezen példányán ne futtassa a Storage-adapter-szolgáltatást.

## <a name="define-your-device-model"></a>Az eszköz modelljének meghatározása

Nyissa meg a GitHubról letöltött **Device-szimulációs-DotNet-Master** mappát a Visual Studio Code új példányán. A feloldatlan függőségek kijavításához kattintson bármelyik **visszaállítási** gombra.

Ebben a útmutatóban egy új eszközt hoz létre egy eszköz követéséhez:

1. Hozzon létre egy új, **assettracker-01.js** nevű eszköz-modellt a **Services\data\devicemodels** mappában.

1. Adja meg az eszköz funkcióit az eszköz modellje **assettracker-01.js** fájlban. A Protopuf-modell telemetria szakaszának a következőket kell tennie:

   * Adja meg az eszközhöz generált Protopuf osztály nevét. A következő szakasz bemutatja, hogyan hozhatja elő ezt az osztályt.
   * A Protopuf megadásához írja be az üzenet formátumát.

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

### <a name="create-device-behaviors-script"></a>Eszköz-viselkedési parancsfájl létrehozása

Írja be a viselkedési parancsfájlt, amely meghatározza, hogy az eszköz hogyan viselkedik. További információ: [Advanced szimulált eszköz létrehozása](iot-accelerators-device-simulation-advanced-device.md).

## <a name="define-your-protobuf-format"></a>A Protopuf formátumának meghatározása

Ha rendelkezik egy eszköz modellel, és meghatározta az üzenet formátumát, létrehozhat egy **proto** -fájlt. A **proto** -fájlban adja hozzá a következőket:

* A `csharp_namespace` , amely megfelel a **Osztálynév** tulajdonságnak az eszköz modelljében.
* A szerializálni kívánt adatszerkezetek üzenete.
* Az üzenet egyes mezőinek neve és típusa.

1. Hozzon létre egy **assettracker. proto** nevű új fájlt a **Services\Models\Protobuf\proto** mappában.

1. Adja meg a szintaxist, a névteret és az üzenet-sémát a **proto** -fájlban a következő módon:

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

A `=1` , az `=2` egyes elemek jelölői egy egyedi címkét határoznak meg, amelyet a mező a bináris kódolásban használ. A 1-15 számnál kevesebb bájtra van szükség a nagyobb számú kódoláshoz.

## <a name="generate-the-protobuf-class"></a>A Protopuf osztály előállítása

Ha egy **proto** -fájllal rendelkezik, a következő lépés az üzenetek olvasásához és írásához szükséges osztályok előállítása. A lépés elvégzéséhez szüksége lesz a **Protoc** protopuf-fordítóra.

1. [A Protopuf Compiler letöltése a GitHubról](https://github.com/protocolbuffers/protobuf/releases/download/v3.4.0/protoc-3.4.0-win32.zip)

1. Futtassa a fordítót, adja meg a forrás könyvtárat, a célhely könyvtárat és a **proto** -fájl nevét. Például:

    ```cmd
    protoc -I c:\temp\device-simulation-dotnet-master\Services\Models\Protobuf\proto --csharp_out=C:\temp\device-simulation-dotnet-master\Services\Models\Protobuf assettracker.proto
    ```

    Ez a parancs létrehoz egy **Assettracker. cs** fájlt a **Services\Models\Protobuf** mappában.

## <a name="test-protobuf-locally"></a>Protopuf helyi tesztelése

Ebben a szakaszban a korábban az előző szakaszokban létrehozott Asset Tracker-eszközt teszteli.

### <a name="run-the-device-simulation-microservice"></a>Az eszköz szimulációs szolgáltatásának futtatása

Nyissa meg a **. vscode/launch.js** fájlt, és rendelje hozzá a következőt:

* IoT Hub a kapcsolódási karakterláncot a **számítógépek \_ IOTHUB \_ CONNSTRING** környezeti változóhoz.
* A Storage-fiók kapcsolódási karakterlánca a **számítógépek \_ Azure \_ Storage- \_ fiók** környezeti változóhoz.
* Cosmos DB a kapcsolódási karakterláncot a **számítógépek \_ STORAGEADAPTER \_ DOCUMENTDB \_ CONNSTRING** környezeti változóhoz.

Nyissa meg a **webszolgáltatást/tulajdonságokat/launchSettings.jsa** fájlon, és rendelje hozzá a következőt:

* IoT Hub a kapcsolódási karakterláncot a **számítógépek \_ IOTHUB \_ CONNSTRING** környezeti változóhoz.
* A Storage-fiók kapcsolódási karakterlánca a **számítógépek \_ Azure \_ Storage- \_ fiók** környezeti változóhoz.
* Cosmos DB a kapcsolódási karakterláncot a **számítógépek \_ STORAGEADAPTER \_ DOCUMENTDB \_ CONNSTRING** környezeti változóhoz.

Nyissa meg a **WebService\appsettings.ini** fájlt, és módosítsa a beállításokat a következőképpen:

#### <a name="configure-the-solution-to-include-your-new-device-model-files"></a>A megoldás konfigurálása az új eszköz-modell fájljainak belefoglalásához

Alapértelmezés szerint az új eszköz-modell JSON-és JS-fájljai nem másolódnak át az épített megoldásba. Explicit módon fel kell vennie őket.

Adjon hozzá egy bejegyzést a **services\services.csproj** -fájlhoz minden olyan fájlhoz, amelyet fel szeretne venni. Például:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

Ha helyileg szeretné futtatni a szolgáltatást, kattintson a hibakeresés **\> megkezdése** elemre.

A Visual Studio Code-ban található **Terminálablak** a futó szolgáltatás kimenetét jeleníti meg.

A következő lépések elvégzése után hagyja a Visual Studio Code ezen példányán futó Device szimulációs szolgáltatást.

### <a name="set-up-a-monitor-for-device-events"></a>Figyelő beállítása az eszköz eseményeihez

Ebben a szakaszban az Azure CLI-vel állít be egy eseményt figyelőt az IoT hub-hoz csatlakoztatott eszközökről érkező telemetria megtekintéséhez.

A következő parancsfájl feltételezi, hogy az IoT hub neve **Device-szimulációs-test**.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

A szimulált eszközök tesztelése közben hagyja futni az eseménynaplót.

### <a name="create-a-simulation-with-the-asset-tracker-device-type"></a>Szimuláció létrehozása az Asset Tracker-eszköz típusával

Ebben a szakaszban a Poster eszköz használatával kérheti le az eszköz szimulációs szolgáltatását, hogy futtasson egy szimulációt az eszközoldali eszköz típusának használatával. A Poster egy olyan eszköz, amely lehetővé teszi a REST-kérések küldését egy webszolgáltatásnak.

Poster beállítása:

1. Nyissa meg a Poster szolgáltatást a helyi gépen.

1. Kattintson a **fájl \> importálása** elemre. Ezután kattintson a **fájlok kiválasztása** elemre.

1. Válassza az **Azure IoT-eszköz szimulációs megoldás gyorsító. Poster \_ Collection** és az **Azure IoT Device szimulációs megoldás gyorsító. Poster- \_ környezet** lehetőséget, és kattintson a **Megnyitás** gombra.

1. Bontsa ki az **Azure IoT-eszköz szimulációs megoldásának Gyorssegédjét** a küldendő kérések megtekintéséhez.

1. Kattintson a **nincs környezet** elemre, és válassza az **Azure IoT-eszköz szimulációs megoldásának gyorsítása** elemet.

Most már betöltött egy gyűjteményt és egy környezetet a Poster-munkaterületen, amely az eszköz-szimulációs szolgáltatással való kommunikációhoz használható.

A szimuláció konfigurálása és futtatása:

1. A Poster gyűjteményben válassza az **Asset Tracker-szimuláció létrehozása** lehetőséget, majd kattintson a **Küldés** gombra. Ez a kérelem a szimulált eszköz típusának négy példányát hozza létre.

1. Az Eseménynapló kimenete az Azure CLI-ablakban a szimulált eszközök telemetria jeleníti meg.

A szimuláció leállításához jelölje ki a **Szimuláció leállítására** irányuló kérelmet a Poster alkalmazásban, és kattintson a **Küldés** gombra.

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A két helyileg futó Service-t a Visual Studio Code-példányaiban állíthatja le (**hibakeresési \> Leállítás hibakeresése**).

Ha már nincs szüksége a IoT Hubra és Cosmos DB példányokra, törölje azokat az Azure-előfizetésből a szükségtelen költségek elkerülése érdekében.

## <a name="iot-hub-support"></a>IoT Hub támogatás

Számos IoT Hub funkció nem támogatja natív módon a Protopuf vagy más bináris formátumot. Például nem lehet átirányítani az üzenet adattartalma alapján, mert IoT Hub nem fogja tudni feldolgozni az üzenet tartalmát. Az üzeneteket azonban az üzenetek fejlécei alapján is elvégezheti.

## <a name="next-steps"></a>Következő lépések

Most, hogy megtanulta, hogyan szabhatja testre az Protopuf a telemetria küldéséhez, a következő lépés a GitHub-tárházba kerül, ahol további [eszközök](https://github.com/Azure/device-simulation-dotnet)is elérhetők.

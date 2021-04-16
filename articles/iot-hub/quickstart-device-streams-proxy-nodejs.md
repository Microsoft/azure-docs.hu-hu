---
title: Rövid útmutató – Azure IoT Hub SSH és RDP Node.js gyorsútmutató
description: Ebben a rövid útmutatóban egy minta Node.js-alkalmazást fog futtatni, amely proxyként működik az SSH- és RDP-forgatókönyvek engedélyezéséhez IoT Hub eszközstreamek esetében.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: references_regions, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 16daee2855b8e493521c1b40468e7068dac9e759
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477936"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-nodejs-proxy-application-preview"></a>Rövid útmutató: SSH és RDP engedélyezése egy IoT Hub-eszközstreamen egy Node.js proxyalkalmazással (előzetes verzió)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Ebben a rövid útmutatóban engedélyezi a Secure Shell- (SSH-) és RDP protokoll-forgalmat (RDP) az eszközre egy eszközstreamen keresztül. Azure IoT Hub az eszközstreamek lehetővé teszik, hogy a szolgáltatás- és eszközalkalmazások biztonságos és tűzfalbarát módon kommunikáljanak. Ez a rövid útmutató a szolgáltatásoldalon Node.js futó proxyalkalmazás futtatását ismerteti. A nyilvános előzetes verzióban a Node.js SDK csak a szolgáltatásoldalon támogatja az eszközstreameket. Ennek eredményeképpen ez a rövid útmutató csak a szolgáltatás helyi proxyalkalmazásának futtatására vonatkozó utasításokat tartalmazza.

## <a name="prerequisites"></a>Előfeltételek

* Az [SSH és](./quickstart-device-streams-proxy-c.md) az RDP engedélyezése IoT Hub-eszközstreameken C proxyalkalmazással vagy SSH és RDP engedélyezése IoT Hub-eszközstreameken [C#-proxyalkalmazás használatával.](./quickstart-device-streams-proxy-csharp.md)

* Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egyet ingyenesen.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Node.js 10+.](https://nodejs.org)

    A következő paranccsal ellenőrizheti a Node.js aktuális verzióját a fejlesztői gépen:

    ```cmd/sh
    node --version
    ```

* [Mintaprojekt Node.js.](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Microsoft Azure IoT Hub jelenleg előzetes verziójú funkcióként támogatja az [eszközstreameket.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

> [!IMPORTANT]
> Az eszközstreamek előzetes verziója jelenleg csak a következő régiókban létrehozott IoT Hubok esetében támogatott:
>
> * Az USA középső régiója
> * USA középső régiója – EUAP
> * Észak-Európa
> * Délkelet-Ázsia

### <a name="add-azure-iot-extension"></a>Azure IoT-bővítmény hozzáadása

Adja hozzá az Azure CLI-hez való Azure IoT-bővítményt Cloud Shell a következő parancs futtatásával. Az IoT-bővítmény IoT Hub, IoT Edge és IoT Device Provisioning Service-specifikus parancsokat ad hozzá az Azure CLI-hez.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Ha már elvégezte a [Rövid útmutató: Telemetria küldése egy eszközről IoT Hubra](quickstart-send-telemetry-node.md) című előző útmutatót, kihagyhatja ezt a lépést.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Ha befejezte a Rövid útmutató: Telemetria küldése egy eszközről [egy IoT Hubra](quickstart-send-telemetry-node.md)lépést, kihagyhatja ezt a lépést.

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a szakaszban a Azure Cloud Shell egy szimulált eszköz regisztrálását.

1. Az eszközidentitás létrehozásához futtassa a következő parancsot a Cloud Shell:

   > [!NOTE]
   > * Cserélje le *a YourIoTHubName* helyőrzőt az IoT Hubhoz választott névre.
   > * A regisztráló eszköz neveként javasoljuk, hogy használja a *MyDevice* eszközt az itt látható módon. Ha másik nevet választ az eszköz számára, használja ezt a nevet a cikkben, és a futtatás előtt frissítse az eszköz nevét a mintaalkalmazásban.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Ahhoz, hogy a háttéralkalmazás csatlakozzon az IoT Hubhoz és lekérni az üzeneteket, szüksége lesz egy szolgáltatáskapcsolati *sztringre is.* A következő parancs lekéri az IoT Hub sztringet:

   > [!NOTE]
   > Cserélje le *a YourIoTHubName* helyőrzőt az IoT Hubhoz választott névre.

    ```azurecli-interactive
    az iot hub connection-string show --policy-name service --hub-name {YourIoTHubName} --output table
    ```

   Jegyezze fel a visszaadott szolgáltatáskapcsolati sztringet a rövid útmutató későbbi használatra. Az alábbi példához hasonlóan néz ki:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH-kapcsolat egy eszközhöz eszközstreamek segítségével

Ebben a szakaszban egy végpontok között található streamet hoz létre az SSH-forgalom bújtatása érdekében.

### <a name="run-the-device-local-proxy-application"></a>Az eszköz helyi proxyalkalmazásának futtatása

Ahogy korábban említettük, a IoT Hub Node.js SDK csak a szolgáltatási oldalon támogatja az eszközstreameket. Az eszköz helyi alkalmazásához használjon olyan eszközproxy-alkalmazást, amely az alábbi rövid útmutatók egyikében érhető el:

   * [SSH és RDP engedélyezése IoT Hub keresztül egy C proxyalkalmazással](./quickstart-device-streams-proxy-c.md)
   * [SSH és RDP engedélyezése IoT Hub eszközstreamek használatával egy C#-proxyalkalmazással](./quickstart-device-streams-proxy-csharp.md) 

Mielőtt továbblép a következő lépésre, ellenőrizze, hogy fut-e az eszköz helyi proxyalkalmazása. A beállítás áttekintését lásd: [Helyi proxyminta.](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp)

### <a name="run-the-service-local-proxy-application"></a>A szolgáltatás helyi proxyalkalmazásának futtatása

Ez a cikk az SSH beállítását ismerteti (a 22-es porton keresztül), majd az RDP beállításának módosítását (amely a 3389-es portot használja). Mivel az eszközstreamek alkalmazás- és protokolltól függetlenek, módosíthatja ugyanazt a mintát, hogy más típusú ügyfél-kiszolgáló alkalmazás forgalmát is el tudja fogadni, általában a kommunikációs port módosításával.

Ha az eszköz helyi proxyalkalmazása fut, futtassa az Node.js-ban írt helyi proxyalkalmazást a következővel egy helyi terminálablakban:

1. Környezeti változók esetén adja meg a szolgáltatás hitelesítő adatait, a céleszköz azonosítóját, ahol az SSH-démon fut, valamint az eszközön futó proxy portszámát.

   ```
   # In Linux
   export IOTHUB_CONNECTION_STRING="{ServiceConnectionString}"
   export STREAMING_TARGET_DEVICE="MyDevice"
   export PROXY_PORT=2222

   # In Windows
   SET IOTHUB_CONNECTION_STRING={ServiceConnectionString}
   SET STREAMING_TARGET_DEVICE=MyDevice
   SET PROXY_PORT=2222
   ```

   Módosítsa a ServiceConnectionString helyőrzőt a szolgáltatás kapcsolati sztringjére, a **MyDevice-et** pedig az eszközazonosítóra, ha más nevet adott a sajátjának.

1. Lépjen a `Quickstarts/device-streams-service` kicsomagolt projektmappában található könyvtárba. A szolgáltatás helyi proxyalkalmazásának futtatásához használja a következő kódot:

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   # Run the service-local proxy application
   node proxy.js
   ```

### <a name="ssh-to-your-device-via-device-streams"></a>SSH-kapcsolat az eszközzel eszközstreamek segítségével

Linux rendszeren futtassa az SSH-t `ssh $USER@localhost -p 2222` a terminálon a használatával. Windows rendszeren használja kedvenc SSH-ügyfelét (például PuTTY).

Konzolkimenet a szolgáltatás helyi hálózatán az SSH-munkamenet létrejötte után (a szolgáltatás helyi proxyalkalmazása a 2222-es porton figyel):

![SSH terminálkimenet](./media/quickstart-device-streams-proxy-nodejs/service-console-output.png)

Az SSH-ügyfélalkalmazás konzolkimenete (az SSH-ügyfél a 22-es porthoz csatlakozva kommunikál az SSH-démonnal, ahol a szolgáltatás helyi proxyalkalmazása figyel):

![SSH-ügyfél kimenete](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.png)

### <a name="rdp-to-your-device-via-device-streams"></a>RDP-kapcsolat az eszközzel eszközstreamek segítségével

Most használja az RDP-ügyfélalkalmazást, és csatlakozzon a szolgáltatásproxyhoz a 2222-es porton, egy tetszőleges, korábban kiválasztott porton.

> [!NOTE]
> Győződjön meg arról, hogy az eszközproxy megfelelően van konfigurálva az RDP-hez, és a 3389-es RDP-porton van konfigurálva.

![Az RDP-ügyfél a szolgáltatás helyi proxyalkalmazásához csatlakozik](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban beállított egy IoT Hubot, regisztrált egy eszközt, és üzembe helyezett egy szolgáltatásproxy-alkalmazást az RDP és az SSH engedélyezéséhez egy IoT-eszközön. Az RDP- és az SSH-forgalom egy eszközstreamen keresztül lesz bújtatva az IoT Hubon keresztül. Ezzel a folyamattal nincs szükség közvetlen eszközkapcsolatra.

További információ az eszközstreamekkel kapcsolatban:

> [!div class="nextstepaction"]
> [Eszközstreamek áttekintése](./iot-hub-device-streams-overview.md)

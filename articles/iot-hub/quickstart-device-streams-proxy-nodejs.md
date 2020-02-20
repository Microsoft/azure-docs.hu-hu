---
title: Azure IoT Hub Device Streams Node. js gyors útmutató SSH-hoz és RDP-hez
description: Ebben a rövid útmutatóban egy minta Node. js-alkalmazást futtat, amely proxyként szolgál az SSH-és RDP-forgatókönyvek engedélyezéséhez IoT Hub eszköz streamen.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: f7dfa1bf391e4affba52fc40a8c22ea9b5f4b4df
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77470683"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-nodejs-proxy-application-preview"></a>Gyors útmutató: az SSH és az RDP engedélyezése IoT Hub eszköz streamen egy Node. js-alkalmazásproxy használatával (előzetes verzió)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Ebben a rövid útmutatóban engedélyezi, hogy a Secure Shell (SSH) és a RDP protokoll (RDP) forgalmat az eszközre egy eszközön keresztül továbbítsa. Az Azure IoT Hub-eszközök lehetővé teszik a szolgáltatás-és eszköz-alkalmazások számára a biztonságos és tűzfalon keresztüli kommunikációt. Ez a rövid útmutató egy, a szolgáltatási oldalon futó Node. js-proxy alkalmazás végrehajtását ismerteti. A nyilvános előzetes verzióban a Node. js SDK csak a szolgáltatási oldalon található eszköz-adatfolyamokat támogatja. Ennek eredményeképpen ez a rövid útmutató a csak a szolgáltatás helyi proxybeállításait futtató alkalmazások futtatására vonatkozó utasításokat tartalmazza.

## <a name="prerequisites"></a>Előfeltételek

* Az [SSH és az RDP használatának engedélyezése IoT hub eszköz streameken egy C proxy alkalmazás használatával](./quickstart-device-streams-proxy-c.md) , illetve az [SSH és az RDP engedélyezése IoT hub eszközön lévő C# adatfolyamok számára egy proxy alkalmazás használatával](./quickstart-device-streams-proxy-csharp.md).

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Node. js 10 +](https://nodejs.org).

* [Egy példa Node. js-projekt](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip).

A következő parancs használatával ellenőrizheti a Node. js aktuális verzióját a fejlesztői gépen:

```cmd/sh
node --version
```

A Microsoft Azure IoT Hub jelenleg [előzetes verziójú szolgáltatásként](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)támogatja az eszközök adatfolyamait.

> [!IMPORTANT]
> Az adatfolyamok előnézete jelenleg csak a következő régiókban létrehozott IoT hubok esetében támogatott:
>
> * USA középső régiója
> * USA középső – EUAP
> * Észak-Európa
> * Délkelet-Ázsia
  
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Azure IoT-bővítmény hozzáadása

A következő parancs futtatásával adja hozzá az Azure CLI-hez készült Azure IoT-bővítményt a Cloud Shell-példányához. Az IoT bővítmény hozzáadja az Azure CLI-hez IoT Hub, IoT Edge és IoT eszközök kiépítési szolgáltatásának (DPS) specifikus parancsait.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Ha már elvégezte a [Rövid útmutató: Telemetria küldése egy eszközről IoT Hubra](quickstart-send-telemetry-node.md) című előző útmutatót, kihagyhatja ezt a lépést.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Ha elvégezte a gyors üzembe helyezést [: küldjön telemetria egy eszközről egy IoT hubhoz](quickstart-send-telemetry-node.md), ezt a lépést kihagyhatja.

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a szakaszban a Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Az eszköz identitásának létrehozásához futtassa a következő parancsot Cloud Shellban:

   > [!NOTE]
   > * Cserélje le a *YourIoTHubName* helyőrzőt az IoT hub számára kiválasztott névre.
   > * Annak az eszköznek a nevére, amelyet regisztrál, javasolt a *MyDevice* használata az ábrán látható módon. Ha más nevet választ az eszköznek, ezt a nevet használja ebben a cikkben, és a futtatásuk előtt frissítse az eszköz nevét a minta alkalmazásokban.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Ahhoz, hogy a háttérbeli alkalmazás csatlakozhasson az IoT hubhoz, és lekérje az üzeneteket, szüksége lesz egy *szolgáltatás kapcsolati karakterláncára*is. A következő parancs lekérdezi az IoT hub karakterláncát:

   > [!NOTE]
   > Cserélje le a *YourIoTHubName* helyőrzőt az IoT hub számára kiválasztott névre.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

   Jegyezze fel a visszaadott szolgáltatás-kapcsolatok karakterláncát a rövid útmutató későbbi verzióihoz. Az alábbi példához hasonlóan néz ki:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH-eszköz az eszközön keresztül streamek használatával

Ebben a szakaszban egy végpontok közötti streamet hoz létre az SSH-forgalom bújtatásához.

### <a name="run-the-device-local-proxy-application"></a>Az eszköz helyi proxy alkalmazásának futtatása

Ahogy korábban említettük, a IoT Hub Node. js SDK csak az eszközön lévő adatfolyamokat támogatja a szolgáltatás oldalán. Az eszköz helyi alkalmazásához használja az alábbi rövid útmutatók egyikében elérhető alkalmazásproxy-alkalmazást:

   * [Az SSH és az RDP engedélyezése IoT Hub eszköz streamen C proxy alkalmazás használatával](./quickstart-device-streams-proxy-c.md)
   * [SSH és RDP engedélyezése IoT Hub eszközön lévő streamek számára C# egy proxy alkalmazás használatával](./quickstart-device-streams-proxy-csharp.md) 

Mielőtt továbblép a következő lépésre, győződjön meg arról, hogy az eszközön helyi alkalmazásproxy fut. A telepítés áttekintését lásd: [helyi proxy minta](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

### <a name="run-the-service-local-proxy-application"></a>A szolgáltatás helyi proxy alkalmazásának futtatása

Ez a cikk az SSH beállítását ismerteti (a 22-es port használatával), majd leírja, hogyan lehet módosítani az RDP-t (amely a 3389-es portot használja). Mivel az eszköz-adatfolyamok alkalmazás-és protokoll-függetlenek, ugyanezt a mintát úgy módosíthatja, hogy az ügyfél-kiszolgáló alkalmazások más típusú forgalmát is kielégítse, általában a kommunikációs port módosításával.

A-t futtató helyi alkalmazásproxy-alkalmazás futtatásával futtassa a Node. js-ben írt szolgáltatás-helyi proxykiszolgálót a következő helyi terminál-ablakban történő végrehajtásával:

1. Környezeti változók esetén adja meg a szolgáltatás hitelesítő adatait, a megcélzott eszköz AZONOSÍTÓját, ahol az SSH démon fut, az eszközön futó proxy portszáma pedig.

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

   Módosítsa a ServiceConnectionString helyőrzőjét úgy, hogy az megfeleljen a szolgáltatás kapcsolódási karakterláncának, és **MyDevice** , ha más nevet adott a felhasználónak.

1. A kibontott projekt mappájában navigáljon a `Quickstarts/device-streams-service` könyvtárba. A következő kód használatával futtassa a szolgáltatás – helyi alkalmazásproxy alkalmazást:

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   # Run the service-local proxy application
   node proxy.js
   ```

### <a name="ssh-to-your-device-via-device-streams"></a>SSH-n keresztül az eszközön

Linux rendszerben futtassa az SSH-t `ssh $USER@localhost -p 2222` használatával egy terminálon. A Windowsban használja kedvenc SSH-ügyfelét (például a PuTTY-t).

Konzol kimenete a szolgáltatásban – helyi SSH-munkamenet létrehozása után (a szolgáltatás helyi proxy alkalmazás figyeli a 2222-es portot):

![SSH-terminál kimenete](./media/quickstart-device-streams-proxy-nodejs/service-console-output.png)

Az SSH-ügyfélalkalmazás konzoljának kimenete (az SSH-ügyfél a 22-es porthoz való csatlakozással kommunikál az SSH démonsal):

![SSH-ügyfél kimenete](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.png)

### <a name="rdp-to-your-device-via-device-streams"></a>RDP az eszközön az eszköz streamen keresztül

Most használja az RDP-ügyfélalkalmazás használatát, és kapcsolódjon a Service proxyhoz a 2222-as porton, egy tetszőleges portot, amelyet korábban választott.

> [!NOTE]
> Győződjön meg arról, hogy az eszköz proxyja megfelelően van konfigurálva az RDP-hez, és konfigurálja a 3389-es RDP-portot.

![Az RDP-ügyfél a szolgáltatás helyi alkalmazásproxy-alkalmazásához csatlakozik](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy IoT hubot állít be, regisztrált egy eszközt, és üzembe helyezett egy Service proxy-alkalmazást az RDP és az SSH IoT-eszközön való engedélyezéséhez. Az RDP-és SSH-forgalom az IoT hub-on keresztül egy eszköz streamen keresztül lesz átirányítva. Ez a folyamat nem teszi szükségessé az eszközhöz való közvetlen csatlakozást.

További információ az eszközök streamekről:

> [!div class="nextstepaction"]
> [Az eszközök adatfolyamának áttekintése](./iot-hub-device-streams-overview.md)

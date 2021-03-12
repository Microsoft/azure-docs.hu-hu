---
title: Gyors útmutató Azure IoT Edge eszköz létrehozásához Windows rendszeren | Microsoft Docs
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre IoT Edge eszközt, majd távolról is üzembe helyezheti az előre elkészített kódot a Azure Portal.
author: rsameser
manager: kgremban
ms.author: riameser
ms.date: 01/20/2021
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-azurecli
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 6170f91d11b47a43e15488bcbb0e91ff3f7c906e
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102616094"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-windows-device-preview"></a>Rövid útmutató: az első IoT Edge modul üzembe helyezése Windows-eszközön (előzetes verzió)

Próbálja ki Azure IoT Edge ebben a rövid útmutatóban, ha a Windows IoT Edge eszközön egy Linux rendszerbe helyez üzembe egy tároló kódot. IoT Edge lehetővé teszi a kódok távoli kezelését az eszközökön, így több számítási feladatot is elküldhet a peremhálózat számára. Ebben a rövid útmutatóban azt javasoljuk, hogy használja a saját eszközét, hogy megtekintse, milyen egyszerűen használható Azure IoT Edge Linux rendszerhez Windows rendszeren.

Ennek a rövid útmutatónak a segítségével megtanulhatja a következőket:

* Hozzon létre egy IoT hubot.
* IoT Edge-eszköz regisztrálása az IoT Hubon
* Telepítse és indítsa el a Linux rendszerhez készült IoT Edge a Windows Runtime eszközön.
* Modul távoli üzembe helyezése egy IoT Edge eszközön és telemetria küldése.

![Az eszközhöz és a felhőhöz tartozó rövid útmutató architektúráját bemutató ábra.](./media/quickstart/install-edge-full.png)

Ez a rövid útmutató bemutatja, hogyan állíthatja be a Linux rendszerhez készült Azure IoT Edge a Windows-eszközökön. Ezután telepítsen egy modult a Azure Portal az eszközre. A használni kívánt modul egy szimulált érzékelő, amely hőmérséklet-, páratartalom-és adatterhelési adattípust generál. Más Azure IoT Edge oktatóanyagok az üzleti elemzések szimulált adatait elemző modulok üzembe helyezésével foglalkoznak.

Ha nem rendelkezik aktív Azure-előfizetéssel, kezdetnek hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free).

>[!NOTE]
>A Windows rendszerhez készült Linux IoT Edge [nyilvános előzetes](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verzióban érhető el.

## <a name="prerequisites"></a>Előfeltételek

Készítse elő a környezetet az Azure CLI-hez.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Hozzon létre egy felhőalapú erőforráscsoportot az ebben a rövid útmutatóban használt összes erőforrás kezeléséhez.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

Győződjön meg arról, hogy a IoT Edge eszköz megfelel a következő követelményeknek:

* Kiadások
  * Windows 10 1809-es vagy újabb verzió; Build 17763 vagy újabb
    * Professional, Enterprise, IoT Enterprise
  * Windows Server 2019 Build 17763 vagy újabb

  
* Hardverkövetelmények
  * Minimális szabad memória: 2 GB
  * Minimális szabad lemezterület: 10 GB


>[!NOTE]
>Ez a rövid útmutató a Windows felügyeleti központot használja a Linux rendszerhez készült IoT Edge központi telepítésének létrehozásához Windows rendszeren. Használhatja a PowerShellt is. Ha a PowerShell használatával szeretné létrehozni a központi telepítést, kövesse a útmutató a Windows rendszerű [eszközök Linux rendszerhez való telepítéséhez Azure IoT Edge és](how-to-install-iot-edge-on-windows.md)üzembe helyezéséhez című témakör lépéseit.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Először hozzon létre egy IoT hubot az Azure CLI-vel.

![Az I o T hub létrehozásának lépéseit bemutató diagram.](./media/quickstart/create-iot-hub.png)

Ebben a rövid útmutatóban az Azure IoT Hub ingyenes szintje működik. Ha korábban már használta a IoT Hubt, és már rendelkezik egy hubhoz, akkor használhatja ezt az IoT hubot.

A következő kód egy ingyenes **F1** -hubot hoz létre az erőforráscsoporthoz `IoTEdgeResources` . Cserélje le `{hub_name}` az nevet az IoT hub egyedi nevére. Az IoT hub létrehozása eltarthat néhány percig.

```azurecli-interactive
az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
```

Ha hibaüzenet jelenik meg, mert már rendelkezik egy ingyenes hubhoz az előfizetésében, módosítsa az SKU-t `S1` . Ha hibaüzenet jelenik meg, hogy az IoT hub neve nem érhető el, valaki másnak már van ilyen nevű központja. Próbálkozzon új névvel.

## <a name="register-an-iot-edge-device"></a>IoT Edge-eszköz regisztrálása

Regisztráljon egy IoT Edge-eszközt az újonnan létrehozott IoT Hubon.

![Az eszköz IoT hub-identitással való regisztrálásának lépéseit bemutató diagram.](./media/quickstart/register-device.png)

Hozzon létre egy eszközidentitást a szimulált eszközhöz, hogy az kommunikálhasson az IoT Hubbal. Az eszközidentitás a felhőben található, és egy egyedi eszközkapcsolati sztringgel társíthat fizikai eszközt az eszközidentitáshoz.

IoT Edge-eszközök viselkedése és kezelése eltérően történik, mint a tipikus IoT-eszközök. A `--edge-enabled` jelzővel állapítsa meg, hogy ez az identitás egy IoT Edge eszközre vonatkozik-e.

1. A Azure Cloud Shellban adja meg a következő parancsot egy **myEdgeDevice** nevű eszköz létrehozásához a központban.

     ```azurecli-interactive
     az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
     ```

     Ha hibaüzenetet kap a szabályzat kulcsaival kapcsolatban, győződjön meg arról, `iothubowner` hogy Cloud shell az Azure IoT bővítmény legújabb verzióját futtatja.

1. Megtekintheti az eszköz kapcsolati karakterláncát, amely a fizikai eszközt a IoT Hub identitásával társítja. Tartalmazza az IoT hub nevét, az eszköz nevét, valamint egy megosztott kulcsot, amely a kettő közötti kapcsolatokat hitelesíti.

     ```azurecli-interactive
     az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
     ```

1. Másolja le a `connectionString` kulcs értékét a JSON-kimenetből, és mentse. Ez az érték az eszköz kapcsolati sztringje. A következő szakaszban fogja használni a IoT Edge futtatókörnyezet konfigurálásához.

     ![A Cloud Shellban található connectionString kimenetet bemutató képernyőkép.](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>Az IoT Edge-futtatókörnyezet telepítése és elindítása

Telepítse a Linux rendszerhez készült IoT Edget az eszközön, és konfigurálja az eszköz csatlakoztatási karakterláncával.

![A IoT Edge futtatókörnyezet elindításának lépéseit bemutató diagram.](./media/quickstart/start-runtime.png)

1. [Töltse le a Windows felügyeleti központot](https://aka.ms/wacdownload).

1. Kövesse a telepítővarázsló utasításait a Windows felügyeleti központ beállításához az eszközön.

1. Nyissa meg a Windows felügyeleti központot.

1. Válassza a **Beállítások fogaskerék** ikont a jobb felső sarokban, majd válassza a **bővítmények** lehetőséget.

1. A **hírcsatornák** lapon válassza a **Hozzáadás** lehetőséget.

1. Adja meg `https://aka.ms/wac-insiders-feed` a szövegmezőt, majd kattintson a **Hozzáadás** gombra.

1. A hírcsatorna hozzáadása után nyissa meg a **rendelkezésre álló bővítmények** lapot, és várjon, amíg a bővítmények listája frissül.

1. Az **elérhető bővítmények** listájából válassza a **Azure IoT Edge** lehetőséget.

1. Telepítse a bővítményt.

1. Ha a bővítmény telepítve van, kattintson a **Windows felügyeleti központ** elemre a bal felső sarokban, hogy a fő irányítópult lapra ugorjon.

     A **localhost** -kapcsolatok azt a számítógépet képviselik, amelyen a Windows felügyeleti központot futtatja.

     :::image type="content" source="media/quickstart/windows-admin-center-start-page.png" alt-text="Képernyőkép a Windows admin kezdőlapról.":::

1. Válassza a **Hozzáadás** lehetőséget.

     :::image type="content" source="media/quickstart/windows-admin-center-start-page-add.png" alt-text="Képernyőfelvétel: a Hozzáadás gombra kattintva megjelenik a Windows felügyeleti központban.":::

1. A Azure IoT Edge csempén válassza a **Létrehozás új** elemet a telepítővarázsló elindításához.

     :::image type="content" source="media/quickstart/select-tile-screen.png" alt-text="A Azure IoT Edge til új központi telepítésének létrehozását bemutató képernyőkép.":::

1. Folytassa a telepítővarázsló lépéseit a Microsoft szoftverlicenc-szerződés elfogadásához, majd válassza a **tovább** lehetőséget.

     :::image type="content" source="media/quickstart/wizard-welcome-screen.png" alt-text="A telepítés varázsló folytatásához kattintson a Tovább gombra.":::

1. Válassza a **választható diagnosztikai adatértékek** lehetőséget, majd válassza a **Tovább: telepítés** lehetőséget. Ez a beállítás kiterjesztett diagnosztikai adatait nyújtja, amelyek segítségével a Microsoft figyeli és karbantarthatja a szolgáltatás minőségét.

     :::image type="content" source="media/quickstart/diagnostic-data-screen.png" alt-text="A diagnosztikai adatbeállításokat bemutató képernyőkép.":::

1. A **cél eszköz kiválasztása** képernyőn válassza ki a kívánt céleszköz annak ellenőrzéséhez, hogy az megfelel-e a minimális követelményeknek. Ebben a rövid útmutatóban IoT Edget telepítünk a helyi eszközön, ezért válassza a **localhost** -kapcsolatokat. Ha a céleszköz megfelel a követelményeknek **, kattintson a Tovább gombra** a folytatáshoz.

     :::image type="content" source="media/quickstart/wizard-select-target-device-screen.png" alt-text="A cél eszköz listáját megjelenítő képernyőkép.":::

1. Az alapértelmezett beállítások elfogadásához kattintson a **tovább** gombra. A telepítési képernyő a csomag letöltésének, a csomag telepítésének, a gazdagép konfigurálásának és a Linux virtuális gép (VM) végleges beállításának folyamatát mutatja be. A sikeres üzembe helyezés így néz ki:

     :::image type="content" source="media/quickstart/wizard-deploy-success-screen.png" alt-text="Képernyőkép a sikeres üzembe helyezésről.":::

1. Válassza a Next (tovább) lehetőséget **: kapcsolódjon** a folytatáshoz, és folytassa az utolsó lépéssel, hogy kiépítse Azure IoT Edge eszközét az IoT hub-példány eszköz-azonosítójával.

1. Illessze be az ebben a rövid útmutatóban [korábban](#register-an-iot-edge-device) átmásolt, az **eszközhöz tartozó kapcsolatok karakterláncát** tartalmazó karakterláncot. Ezután válassza ki **a kiépítés lehetőséget a kiválasztott módszerrel**.

     :::image type="content" source="media/quickstart/wizard-provision.png" alt-text="Képernyőkép, amely megjeleníti a kapcsolatok karakterláncát az eszköz kapcsolatainak karakterlánca mezőben.":::

1. A kiépítés befejezése után válassza a **Befejezés** lehetőséget, és térjen vissza a Windows felügyeleti központ kezdőképernyőn. Az eszköz IoT Edge eszközként jelenik meg.

     :::image type="content" source="media/quickstart/windows-admin-center-device-screen.png" alt-text="Képernyőkép, amely az összes kapcsolatot megjeleníti a Windows felügyeleti központban.":::

1. Válassza ki Azure IoT Edge eszközét az irányítópult megtekintéséhez. Látnia kell, hogy az eszközön lévő munkaterhelések üzembe helyezése az Azure IoT Hub. A **IoT Edge modulnak** meg kell jelennie egy **edgeAgent**-t futtató modulnak, és az **IoT Edge állapotnak** aktívnak kell lennie **(fut)**.

Ezzel konfigurálta az IoT Edge-eszközt. Az eszköz készen áll a felhőben üzembe helyezett modulok futtatására.

## <a name="deploy-a-module"></a>Modul üzembe helyezése

Felügyelheti Azure IoT Edge eszközét a felhőből egy olyan modul üzembe helyezéséhez, amely telemetria adatokat küld IoT Hub.

![A modul üzembe helyezésének lépéseit bemutató diagram.](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-the-generated-data"></a>A létrejött adatok megtekintése

Ebben a rövid útmutatóban létrehozott egy új IoT Edge-eszközt, és telepítette rajta az IoT Edge-futtatókörnyezetet. Ezután a Azure Portal használatával telepített egy IoT Edge modult az eszközön való futtatásra anélkül, hogy magát az eszközt kellene módosítania.

A lekért modul olyan minta környezeti adatfeldolgozást hoz létre, amelyet később teszteléshez használhat. A szimulált érzékelő a gépet és a környezetet is figyeli a gép körül. Ez az érzékelő lehet például egy kiszolgálóteremben, egy üzemben vagy akár szélturbinán. Az általa küldött üzenetek közé tartozik a környezeti hőmérséklet és a páratartalom, a gépi hőmérséklet és a nyomás, valamint egy időbélyeg. IoT Edge oktatóanyagok a modul által létrehozott adatelemzési adatként használják.

Ellenőrizze, hogy a felhőből telepített modul fut-e a IoT Edge eszközön a Windows felügyeleti központban.

1. Kapcsolódjon az újonnan létrehozott IoT Edge eszközhöz.

     :::image type="content" source="media/quickstart/connect-edge-screen.png" alt-text="Képernyőkép, amely a Windows felügyeleti központban a kapcsolat kiválasztása lehetőséget mutatja.":::

     Az **Áttekintés** lapon megjelenik a **IoT Edge modulok listája** és **IoT Edge állapota**. Láthatja a telepített modulokat és az eszköz állapotát.  

1. Az **eszközök** területen válassza a **parancs-rendszerhéj** elemet. A parancshéj egy PowerShell-terminál, amely a Secure Shell (SSH) használatával csatlakozik a Azure IoT Edge-eszköz Linux rendszerű virtuális géphez a Windows-SZÁMÍTÓGÉPén.

     :::image type="content" source="media/quickstart/command-shell-screen.png" alt-text="A parancs-rendszerhéj megnyitását bemutató képernyőkép.":::

1. Az eszközön található három modul ellenőrzéséhez futtassa a következő bash-parancsot:

     ```bash
     sudo iotedge list
     ```

    :::image type="content" source="media/quickstart/iotedge-list-screen.png" alt-text="Képernyőkép, amely a parancs-rendszerhéj I o T Edge-listájának kimenetét jeleníti meg.":::

1. Megtekintheti a hőmérséklet-érzékelő modulból a felhőbe küldött üzeneteket.

     ```bash
     iotedge logs SimulatedTemperatureSensor -f
     ```

    >[!Important]
    >A IoT Edge parancsok megkülönböztetik a kis-és nagybetűket, ha a modulok neveire hivatkoznak.

    :::image type="content" source="media/quickstart/temperature-sensor-screen.png" alt-text="Képernyőkép, amely a modulból a felhőbe küldött üzenetek listáját jeleníti meg.":::

A [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) -hoz készült Azure IoT hub-bővítmény használatával megtekintheti az IoT hub-ra érkező üzeneteket.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha folytatni szeretné a IoT Edge oktatóanyagokat, ugorja át ezt a lépést. Használhatja az ebben a rövid útmutatóban regisztrált és beállított eszközt. Ellenkező esetben törölheti a létrehozott Azure-erőforrásokat a díjak elkerülése érdekében.

Ha a virtuális gépet és az IoT Hubot egy új erőforráscsoportban hozta létre, törölheti azt a csoportot és az összes társított erőforrást. Ha nem szeretné törölni a teljes csoportot, az egyes erőforrásokat is törölheti.

> [!IMPORTANT]
> Ellenőrizze az erőforráscsoport tartalmát, és győződjön meg róla, hogy nem kívánja megtartani a kívánt lépést. Az erőforráscsoport törlése nem vonható vissza.

A következő parancs használatával távolítsa el a **IoTEdgeResources** csoportot. A törlés néhány percet is igénybe vehet.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

Ezzel a paranccsal ellenőrizheti, hogy az erőforráscsoport el lett-e távolítva az erőforráscsoportok listájának megtekintéséhez.

```azurecli-interactive
az group list
```

### <a name="remove-azure-iot-edge-for-linux-on-windows"></a>Linux-Azure IoT Edge eltávolítása Windows rendszeren

A Windows felügyeleti központban található irányítópult-bővítmény használatával távolítsa el Azure IoT Edge Linux rendszerre Windows rendszeren.

1. Kapcsolódjon a IoT Edge eszközhöz a Windows felügyeleti központban. Az Azure Dashboard Tool bővítmény betöltődik.

1. Válassza az **Eltávolítás** lehetőséget. Azure IoT Edge eltávolítása után a Windows felügyeleti központ eltávolítja a Azure IoT Edge eszköz-csatlakoztatási bejegyzést a **kezdőlapról** .

>[!Note]
>A Azure IoT Edge a Windows rendszerből való eltávolításának egy másik módja , ha a  >    >    >    >   IoT Edge eszközön kiválasztja a Start Settings apps Azure IoT Edge az Eltávolítás lehetőséget. Ez a módszer eltávolítja a Azure IoT Edge a IoT Edge eszközről, de a Windows felügyeleti központban marad. Az eltávolítás befejezéséhez távolítsa el a Windows felügyeleti központot a **Beállítások** menüből is.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy IoT Edge eszközt, és a Azure IoT Edge Cloud Interface használatával helyezi üzembe a kódot az eszközön. Most már rendelkezik egy tesztelési eszközzel, amely nyers információkat generál a környezetéről.

Ezután állítsa be a helyi fejlesztési környezetet, hogy el tudja kezdeni az üzleti logikát futtató IoT Edge-modulok létrehozását.

> [!div class="nextstepaction"]
> [IoT Edge-modulok fejlesztésének megkezdése](tutorial-develop-for-linux.md)

---
title: 'Rövid útmutató: Új Azure IoT Edge létrehozása Windows rendszerű | Microsoft Docs'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre IoT Edge eszközt, majd hogyan helyezhet üzembe előre összeállított kódot távolról a Azure Portal.
author: rsameser
manager: kgremban
ms.author: riameser
ms.date: 01/20/2021
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-azurecli
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 9f0562d4471ac1129bf9bc7ecfee058cddac7c61
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533143"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-windows-device-preview"></a>Rövid útmutató: Az első IoT Edge üzembe helyezése Windows-eszközön (előzetes verzió)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Próbálja ki Azure IoT Edge rövid útmutatóban található megoldásokat, ha tárolóba helyezett kódot helyez üzembe Egy Linux rendszerű windowsos IoT Edge eszközön. IoT Edge lehetővé teszi, hogy távolról kezelje az eszközökön található kódot, így több számítási feladatot küldhet a peremhálózatra. Ebben a rövid útmutatóban azt javasoljuk, hogy használja a saját eszközét, hogy lássa, milyen egyszerűen használható Azure IoT Edge Linux rendszeren.

Ennek a rövid útmutatónak a segítségével megtanulhatja a következőket:

* IoT Hub létrehozása.
* IoT Edge-eszköz regisztrálása az IoT Hubon
* Telepítse és indítsa el IoT Edge linuxos windowsos futásidejű környezetben az eszközön.
* Modul távoli üzembe helyezése egy IoT Edge és telemetria küldése.

![Az eszközre és a felhőre vonatkozó rövid útmutató architektúráját bemutató ábra.](./media/quickstart/install-edge-full.png)

Ez a rövid útmutató bemutatja, hogyan állíthatja be a linuxos Azure IoT Edge Windows-eszközön. Ezután üzembe fog helyezni egy modult a Azure Portal eszközén. A használt modul egy szimulált érzékelő, amely hőmérséklet-, páratartalom- és nyomásadatokat hoz létre. Más Azure IoT Edge oktatóanyagok az itt található munkára épülnek, és olyan modulokat helyeznek üzembe, amelyek a szimulált adatokat elemzik az üzleti elemzésekhez.

Ha nem rendelkezik aktív Azure-előfizetéssel, kezdetnek hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free).

>[!NOTE]
>IoT Edge linuxos windowsos verzió nyilvános [előzetes verzióban érhető el.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="prerequisites"></a>Előfeltételek

A környezet előkészítése az Azure CLI-hez.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Hozzon létre egy felhőbeli erőforráscsoportot az ebben a rövid útmutatóban használt összes erőforrás kezeléséhez.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

Győződjön meg arról, IoT Edge eszköz megfelel a következő követelményeknek:

* Kiadások
  * Windows 10 1809-es vagy újabb verzió; 17763-as vagy újabb build
    * Professional, Enterprise, IoT Enterprise
  * Windows Server 2019 17763-as vagy újabb build

* Hardverkövetelmények
  * Minimális szabad memória: 1 GB
  * Minimális szabad lemezterület: 10 GB

>[!NOTE]
>Ez a rövid útmutató Windows Admin Center segítségével hozza létre a linuxos IoT Edge üzemelő példányát Windows rendszeren. A PowerShellt is használhatja. Ha a PowerShellt szeretné használni az üzemelő példány létrehozásához, kövesse az útmutató lépéseit a linuxos Azure IoT Edge linuxos eszközökre történő telepítéséhez és üzembe [helyezéséhez.](how-to-install-iot-edge-on-windows.md)

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Első lehetőségként hozzon létre egy IoT Hubot az Azure CLI használatával.

![Az I O T hubok létrehozási lépését bemutató ábra.](./media/quickstart/create-iot-hub.png)

Ebben a rövid útmutatóban Azure IoT Hub ingyenes szint működik. Ha korábban már használt IoT Hub, és már létrehozott egy központot, használhatja ezt az IoT Hubot.

Az alábbi kód egy ingyenes **F1 központot** hoz létre a erőforráscsoportban. `IoTEdgeResources` Cserélje `{hub_name}` le a helyére az IoT Hub egyedi nevét. Az IoT Hub létrehozása eltarthat néhány percig.

```azurecli-interactive
az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
```

Ha hibaüzenetet kap, mert az előfizetésében már van egy ingyenes központ, módosítsa a termékváltozatot a `S1` következőre: . Ha hibaüzenetet kap arról, hogy az IoT Hub neve nem érhető el, valaki más már rendelkezik ilyen nevű központkal. Próbálkozzon egy új névvel.

## <a name="register-an-iot-edge-device"></a>IoT Edge-eszköz regisztrálása

Regisztráljon egy IoT Edge-eszközt az újonnan létrehozott IoT Hubon.

![Az eszköz IoT Hub-identitással való regisztrálásának lépését bemutató ábra.](./media/quickstart/register-device.png)

Hozzon létre egy eszközidentitást a szimulált eszközhöz, hogy az kommunikálhasson az IoT Hubbal. Az eszközidentitás a felhőben található, és egy egyedi eszközkapcsolati sztringgel társíthat fizikai eszközt az eszközidentitáshoz.

IoT Edge eszközök másképp viselkednek és kezelhetők, mint a tipikus IoT-eszközök. A jelzővel deklaráljuk, hogy ez az identitás egy IoT Edge `--edge-enabled` eszközhöz.

1. A Azure Cloud Shell írja be a következő parancsot egy **myEdgeDevice** nevű eszköz létrehozásához a központban.

     ```azurecli-interactive
     az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
     ```

     Ha hibaüzenetet kap a szabályzatkulcsokkal kapcsolatban, győződjön meg arról, Cloud Shell az Azure IoT-bővítmény legújabb verzióját `iothubowner` futtatja.

1. Tekintse meg az eszköz kapcsolati sztringet, amely összeköti a fizikai eszközt az eszköz identitásával a IoT Hub. Tartalmazza az IoT Hub nevét, az eszköz nevét, valamint egy megosztott kulcsot, amely hitelesíti a kettő közötti kapcsolatokat.

     ```azurecli-interactive
     az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
     ```

1. Másolja le a `connectionString` kulcs értékét a JSON-kimenetből, és mentse. Ez az érték az eszköz kapcsolati sztringje. Ezzel fogja konfigurálni a IoT Edge a következő szakaszban.

     ![Képernyőkép a connectionString kimenetről a Cloud Shell.](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>Az IoT Edge-futtatókörnyezet telepítése és elindítása

Telepítse IoT Edge linuxos windowsos alkalmazást az eszközén, és konfigurálja az eszköz kapcsolati sztringjéhez.

![A IoT Edge indítási lépését bemutató ábra.](./media/quickstart/start-runtime.png)

1. [Töltse le Windows Admin Center.](https://aka.ms/wacdownload)

1. Kövesse a telepítővarázslóban megjelenő utasításokat az Windows Admin Center beállításhoz.

1. Nyissa meg Windows Admin Center.

1. Válassza a **beállítások fogaskerék** ikonját a jobb felső sarokban, majd válassza a **Bővítmények lehetőséget.**

1. A **Hírcsatornák lapon** válassza a Hozzáadás **lehetőséget.**

1. Írja `https://aka.ms/wac-insiders-feed` be a szöveget a szövegmezőbe, majd válassza a Hozzáadás **lehetőséget.**

1. A hírcsatorna hozzáadása után válassza az Elérhető **bővítmények** lapot, és várja meg, amíg a bővítmények listája frissül.

1. Az Elérhető bővítmények **listájából válassza a** **Azure IoT Edge.**

1. Telepítse a bővítményt.

1. Ha telepítette a  bővítményt, Windows Admin Center a bal felső sarokban található Képernyők lehetőséget a fő irányítópult oldalára való ugráshoz.

     A **localhost** kapcsolat azt a számítógépet jelöli, ahol a Windows Admin Center.

     :::image type="content" source="media/quickstart/windows-admin-center-start-page.png" alt-text="A Windows rendszergazdai kezdőlap képernyőképe.":::

1. Válassza a **Hozzáadás** lehetőséget.

     :::image type="content" source="media/quickstart/windows-admin-center-start-page-add.png" alt-text="Képernyőkép a Hozzáadás gomb kiválasztásáról a Windows Admin Center.":::

1. A Azure IoT Edge a telepítővarázsló létrehozásához válassza az **Új** létrehozása lehetőséget.

     :::image type="content" source="media/quickstart/select-tile-screen.png" alt-text="Képernyőkép egy új üzembe helyezés létrehozásáról a Azure IoT Edge til.":::

1. Folytassa a telepítővarázslóval, és fogadja el a Microsoft szoftverlicenc-szerződés, majd válassza a **Tovább lehetőséget.**

     :::image type="content" source="media/quickstart/wizard-welcome-screen.png" alt-text="Képernyőkép a Tovább lehetőség kiválasztásáról a telepítővarázslóval való folytatáshoz.":::

1. Válassza **a Választható diagnosztikai adatok,** majd a **Tovább: Üzembe helyezés lehetőséget.** Ez a választás kiterjesztett diagnosztikai adatokat biztosít, amelyek segítenek a Microsoftnak a szolgáltatásminőség figyelése és fenntartása érdekében.

     :::image type="content" source="media/quickstart/diagnostic-data-screen.png" alt-text="A Diagnosztikai adatok beállításait bemutató képernyőkép.":::

1. A **Céleszköz kiválasztása képernyőn** válassza ki a kívánt céleszközt annak ellenőrzéséhez, hogy az megfelel-e a minimális követelményeknek. Ebben a rövid útmutatóban a helyi eszközre IoT Edge, ezért válassza ki a **localhost** kapcsolatot. Ha a céleszköz megfelel a követelményeknek, kattintson **a Tovább gombra** a folytatáshoz.

     :::image type="content" source="media/quickstart/wizard-select-target-device-screen.png" alt-text="A Céleszköz listát bemutató képernyőkép.":::

1. Az **alapértelmezett beállítások** elfogadásához válassza a Tovább lehetőséget. Az üzembe helyezési képernyőn látható a csomag letöltése, a csomag telepítése, a gazdagép konfigurálása és a Linux rendszerű virtuális gép (VM) végső beállítása. A sikeres üzembe helyezés a következő:

     :::image type="content" source="media/quickstart/wizard-deploy-success-screen.png" alt-text="Sikeres üzembe helyezés képernyőképe.":::

1. Válassza **a Tovább: Csatlakozás** lehetőséget, hogy folytassa az utolsó lépéssel, és Azure IoT Edge az IoT Hub-példány eszközazonosítójával.

1. Illessze be az [](#register-an-iot-edge-device) ebben a rövid útmutatóban korábban kimáselt kapcsolati sztringet az **Eszközkapcsolati sztring mezőbe.** Ezután válassza **a Kiépítés a kiválasztott módszerrel lehetőséget.**

     :::image type="content" source="media/quickstart/wizard-provision.png" alt-text="Képernyőkép a kapcsolati sztringről az Eszközkapcsolati sztring mezőben.":::

1. A kiépítés befejezése után válassza a **Befejezés** lehetőséget a befejezéshez, és térjen vissza Windows Admin Center kezdőképernyőjére. Az eszköznek megjelenik egy IoT Edge eszközként.

     :::image type="content" source="media/quickstart/windows-admin-center-device-screen.png" alt-text="Képernyőkép az összes kapcsolatról a Windows Admin Center.":::

1. Az irányítópult Azure IoT Edge válassza ki a saját eszközét. Látnia kell, hogy az ikereszköz számítási feladatai már üzembe Azure IoT Hub ikereszközön. A **IoT Edge modullistának** egy **edgeAgentet** futtató modult kell mutatnia, és a **IoT Edge állapotnak** **aktívnak (futónak) kell lennie.**

Ezzel konfigurálta az IoT Edge-eszközt. Az eszköz készen áll a felhőben üzembe helyezett modulok futtatására.

## <a name="deploy-a-module"></a>Modul üzembe helyezése

Felügyelheti Azure IoT Edge eszközét a felhőből, és üzembe helyezhet egy modult, amely telemetriai adatokat küld a IoT Hub.

![A modul üzembe helyezésének lépését bemutató ábra.](./media/quickstart/deploy-module.png)

<!--
[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

Include content included below to support versioned steps in Linux quickstart. Can update include file once Windows quickstart supports v1.2
-->

Az alkalmazás egyik legfontosabb Azure IoT Edge kód üzembe helyezése a IoT Edge a felhőből. *IoT Edge modulok tárolóként* megvalósított végrehajtható csomagok. Ebben a szakaszban egy előre felépített modult fog üzembe helyezni a modul IoT Edge [Modulok szakaszában, Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) közvetlenül a Azure IoT Hub.

Az ebben a szakaszban üzembe helyezett modul egy érzékelőt szimulál, és elküldi a létrehozott adatokat. Ez a modul hasznos kód, amikor elkezdi a IoT Edge mert a szimulált adatokat fejlesztési és tesztelési célokra is használhatja. Ha szeretné pontosan megtekinteni a modult, megtekintheti a szimulált hőmérséklet-érzékelő [forráskódját.](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs)

Kövesse az alábbi lépéseket az első modul üzembe helyezéséhez Azure Marketplace.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) az IoT Hubra.

1. A bal oldali menüBen, az **Automatikus** Eszközkezelés alatt válassza **a** IoT Edge lehetőséget.

1. Válassza ki a céleszköz eszközazonosítóját az eszközök listájából.

1. A felső sávon válassza a **Modulok beállítása lehetőséget.**

   ![A Modulok beállítása lehetőség kiválasztását bemutató képernyőkép.](./media/quickstart/select-set-modules.png)

1. A **IoT Edge alatt** nyissa meg a Hozzáadás **legördülő** menüt, majd válassza a **Marketplace-modul lehetőséget.**

   ![A Hozzáadás legördülő menüt bemutató képernyőkép.](./media/quickstart/add-marketplace-module.png)

1. A **IoT Edge Piactéren** keresse meg és válassza ki a `Simulated Temperature Sensor` modult.

   A modult a rendszer hozzáadja IoT Edge modulok szakaszhoz a kívánt **futtatás állapottal.**

1. Válassza **a Tovább: Útvonalak lehetőséget** a varázsló következő lépésének folytatásához.

   ![Képernyőkép a modul hozzáadása utáni következő lépésre való továbblépésről.](./media/quickstart/view-temperature-sensor-next-routes.png)

1. Az Útvonalak **lapon** távolítsa el az alapértelmezett útvonalat, útvonalként a **útvonalat,** majd válassza a **Tovább:** Áttekintés + létrehozás lehetőséget a varázsló következő lépésének folytatásához.

   >[!Note]
   >Az útvonalak név-érték párok használatával vannak összeépítve. Ezen az oldalon két útvonalnak kell lennie. A útvonal alapértelmezett **útvonala** minden üzenetet IoT Hub (amelynek a neve `$upstream` ). A második **útvonal, a SimulatedTemperatureSensorToIoTHub** automatikusan létrejött, amikor hozzáadta a modult a Azure Marketplace. Ez az útvonal a szimulált hőmérséklet modul összes üzenetét elküldi a IoT Hub. Az alapértelmezett útvonalat törölheti, mert ebben az esetben redundáns.

   ![Képernyőkép az alapértelmezett útvonal eltávolításáról, majd a következő lépésre való áthelyezésről.](./media/quickstart/delete-route-next-review-create.png)

1. Tekintse át a JSON-fájlt, majd válassza a **Létrehozás lehetőséget.** A JSON-fájl határozza meg az összes olyan modult, amely az IoT Edge üzembe. Látni fogja a **SimulatedTemperatureSensor** modult és a két futásidejű modult, az **edgeAgent és** az **edgeHub modult.**

   >[!Note]
   >Amikor egy új üzemelő példányt küld egy IoT Edge-eszközre, semmi sem kerül az eszközre. Ehelyett az eszköz rendszeresen lekérdezi az IoT Hubról az új utasításokat. Ha az eszköz egy frissítettüzembe helyezési jegyzéket talál, felhasználja az új üzemelő példányra vonatkozó információt a modul lemezképeinek felhőből történő adatkiszolgálásához, majd elindítja a modulok helyi futtatását. Ez a folyamat eltarthat néhány percig.

1. A modul üzembe helyezési részleteinek létrehozása után a varázsló visszatér az eszköz részleteit tartalmazó oldalra. Tekintse meg az üzembe helyezés állapotát a **Modulok** lapon.

   Három modulnak kell **$edgeAgent:** **$edgeHub**, és **SimulatedTemperatureSensor.** Ha egy vagy több  modul IGEN értékkel rendelkezik a **MEGADOTT** AZ ÜZEMBE HELYEZÉS ALATT, de az ESZKÖZ ÁLTAL JELENTETT alatt **nem,** a IoT Edge az eszköz továbbra is elkezdi azokat. Várjon néhány percet, majd frissítse az oldalt.

   ![Képernyőkép a Simulated Temperature Sensorról az üzembe helyezett modulok listájában.](./media/quickstart/view-deployed-modules.png)

## <a name="view-the-generated-data"></a>A létrejött adatok megtekintése

Ebben a rövid útmutatóban létrehozott egy új IoT Edge-eszközt, és telepítette rajta az IoT Edge-futtatókörnyezetet. Ezután a Azure Portal üzembe helyezett egy IoT Edge-modult, hogy anélkül fusson az eszközön, hogy magát az eszközt kellett volna módosítanunk.

A lekért modul mintakörnyezeti adatokat hoz létre, amelyek később tesztelésre használhatók. A szimulált érzékelő a gépet és a gépet körülvesző környezetet is figyeli. Ez az érzékelő lehet például egy kiszolgálóteremben, egy üzemben vagy akár szélturbinán. Az elküldött üzenetek közé tartozik a környezeti hőmérséklet és a páratartalom, a géphőmérséklet és a nyomás, valamint egy időbélyeg. IoT Edge oktatóanyagok a modul által létrehozott adatokat használják elemzési tesztadatokként.

A parancshéjban ellenőrizze, Windows Admin Center, hogy a felhőből üzembe helyezett modul fut-e a IoT Edge eszközön.

1. Csatlakozzon az újonnan létrehozott IoT Edge eszközhöz.

     :::image type="content" source="media/quickstart/connect-edge-screen.png" alt-text="Képernyőkép a Csatlakozás lehetőség kiválasztásáról a Windows Admin Center.":::

     Az Áttekintés **lapon** megjelenik a modullista IoT Edge **és** a **IoT Edge állapota.** Láthatja az üzembe helyezett modulokat és az eszköz állapotát.  

1. A **Tools (Eszközök) alatt** válassza a Command Shell **(Parancshéj) lehetőséget.** A parancshéj egy PowerShell-terminál, amely automatikusan a Secure Shell (SSH) használatával csatlakozik a Azure IoT Edge linuxos virtuális gépéhez a Windows rendszerű számítógépen.

     :::image type="content" source="media/quickstart/command-shell-screen.png" alt-text="A parancshéj megnyitását bemutató képernyőkép.":::

1. Az eszközön található három modul ellenőrzéséhez futtassa a következő Bash-parancsot:

     ```bash
     sudo iotedge list
     ```

    :::image type="content" source="media/quickstart/iotedge-list-screen.png" alt-text="Képernyőkép a parancs rendszerhéj I o T edge list kimenetéről.":::

1. Tekintse meg a hőmérséklet-érzékelő modulból a felhőbe küldött üzeneteket.

     ```bash
     iotedge logs SimulatedTemperatureSensor -f
     ```

    >[!Important]
    >IoT Edge a parancsok megkülönböztetik a kis- és nagybetűket, amikor modulnevekre hivatkoznak.

    :::image type="content" source="media/quickstart/temperature-sensor-screen.png" alt-text="A modulból a felhőbe küldött üzenetek listáját bemutató képernyőkép.":::

A kódhoz használható [Azure IoT Hub is Visual Studio,](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) hogy figyelje az IoT Hubra érkező üzeneteket.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha tovább szeretne lépni a IoT Edge oktatóanyagokra, hagyja ki ezt a lépést. Használhatja az ebben a rövid útmutatóban regisztrált és beállított eszközt. Ellenkező esetben a díjak elkerülése érdekében törölheti a létrehozott Azure-erőforrásokat.

Ha a virtuális gépet és az IoT Hubot egy új erőforráscsoportban hozta létre, törölheti azt a csoportot és az összes társított erőforrást. Ha nem szeretné törölni a teljes csoportot, az egyes erőforrásokat is törölheti.

> [!IMPORTANT]
> Ellenőrizze az erőforráscsoport tartalmát, és győződjön meg arról, hogy nincs semmi, amit meg szeretne tartani. Az erőforráscsoport törlése nem vonható vissza.

A következő paranccsal távolítsa el az **IoTEdgeResources** csoportot. A törlés eltarthat néhány percig.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

Ezzel a paranccsal az erőforráscsoportok listájának megtekintésével meggyőződhet arról, hogy az erőforráscsoport el lett távolítva.

```azurecli-interactive
az group list
```

### <a name="remove-azure-iot-edge-for-linux-on-windows"></a>Linux Azure IoT Edge windowsos gépek eltávolítása

A windowsos Linux Windows Admin Center irányítópult-Azure IoT Edge távolítsa el.

1. Csatlakozzon a IoT Edge eszközhöz a Windows Admin Center. Betöltődik az Azure-irányítópult eszközbővítménye.

1. Válassza az **Eltávolítás** lehetőséget. A Azure IoT Edge eltávolítása után a Windows Admin Center eltávolítja Azure IoT Edge eszközkapcsolat bejegyzését a **Kezdőlapról.**

>[!Note]
>A windowsos rendszerből Azure IoT Edge beállítások eltávolításának másik módja, ha a Start Settings Apps Azure IoT Edge Uninstall (Eltávolítás) lehetőséget választja a  >    >    >    >   IoT Edge eszközön. Ez a metódus eltávolítja Azure IoT Edge a IoT Edge, de a kapcsolatot a Windows Admin Center. Az eltávolítás befejezéséhez távolítsa el a Windows Admin Center a **Beállítások menüből** is.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy IoT Edge eszközt, és a Azure IoT Edge használatával kódot helyezett üzembe az eszközön. Most már van egy teszteszköze, amely nyers adatokat generál a környezetéről.

Ezután állítsa be a helyi fejlesztési környezetet, hogy IoT Edge az üzleti logikát futtató modulokat.

> [!div class="nextstepaction"]
> [A IoT Edge fejlesztése](tutorial-develop-for-linux.md)

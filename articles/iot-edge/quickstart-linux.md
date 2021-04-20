---
title: 'Rövid útmutató: Virtuális Azure IoT Edge létrehozása Linux-| Microsoft Docs'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre IoT Edge-eszközt Linux rendszeren, majd hogyan helyezhet üzembe előre összeállított kódot távolról a Azure Portal.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/07/2021
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: cdc5dd2df0dc6ac682d37aea3328545fcb7e5ad2
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728607"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-linux-device"></a>Rövid útmutató: Az első IoT Edge üzembe helyezése virtuális Linux-eszközön

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Ebben a rövid Azure IoT Edge tesztelje a teszteket, ha tárolóba helyezett kódot helyez üzembe egy virtuális Linux rendszerű IoT Edge eszközön. IoT Edge lehetővé teszi, hogy távolról kezelje az eszközökön található kódot, így több számítási feladatot küldhet a peremhálózatra. Ebben a rövid útmutatóban egy Azure-beli virtuális gép használatát javasoljuk IoT Edge-eszközhöz, amellyel gyorsan létrehozhat egy tesztgépet, majd törölheti, amikor végzett.

Ennek a rövid útmutatónak a segítségével megtanulhatja az alábbiakat:

* IoT Hub létrehozása
* IoT Edge-eszköz regisztrálása az IoT Hubon
* Telepítse és indítsa IoT Edge futtatás virtuális eszközön.
* Modul távoli üzembe helyezése IoT Edge-eszközön.

![Ábra – Gyors üzembe helyezési architektúra az eszközhöz és a felhőhöz](./media/quickstart-linux/install-edge-full.png)

Ez a rövid útmutató végigvezeti egy olyan Linux rendszerű virtuális gép létrehozásának folyamatán, amely IoT Edge eszköz. Ezután üzembe helyez egy modult a Azure Portal eszközén. Az ebben a rövid útmutatóban használt modul egy szimulált érzékelő, amely hőmérséklet-, páratartalom- és nyomásadatokat hoz létre. A Azure IoT Edge oktatóanyagok további modulokat helyeznek üzembe, amelyek elemzik a szimulált adatokat az üzleti elemzésekhez.

Ha nem rendelkezik aktív Azure-előfizetéssel, kezdetnek hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Előfeltételek

A környezet előkészítése az Azure CLI-hez.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Felhőerőforrások:

* Egy erőforráscsoport a rövid útmutató során létrehozott összes erőforrás kezelésére. Ebben a rövid útmutatóban és az alábbi oktatóanyagokban az **IoTEdgeResources** erőforráscsoport-nevet használjuk.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

A rövid útmutató első létrehozására hozzon létre egy IoT Hubot az Azure CLI használatával.

![Ábra – IoT Hub létrehozása a felhőben](./media/quickstart-linux/create-iot-hub.png)

Ehhez a rövid útmutatóhoz az IoT Hub ingyenes csomagja is elegendő. Ha korábban már használt IoT Hub, és már létrehozott egy központot, használhatja ezt az IoT Hubot.

Az alábbi kód egy ingyenes **F1 központot** hoz létre az **IoTEdgeResources erőforráscsoportban.** Cserélje `{hub_name}` le a helyére az IoT Hub egyedi nevét. A fiók létrehozása eltarthat néhány IoT Hub.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   Ha hibaüzenetet kap, mert az előfizetése már tartalmaz egy ingyenes központot, akkor módosítsa az SKU-t **S1**-re. Mindegyik előfizetés csak egy ingyenes IoT-központtal rendelkezhet. Ha hibaüzenetet kap, IoT Hub a név nem érhető el, az azt jelenti, hogy valaki más már rendelkezik ezzel a névvel. Próbálkozzon egy új névvel.

## <a name="register-an-iot-edge-device"></a>IoT Edge-eszköz regisztrálása

Regisztráljon egy IoT Edge-eszközt az újonnan létrehozott IoT Hubon.

![Ábra – Eszköz regisztrálása IoT Hub identitással](./media/quickstart-linux/register-device.png)

Hozzon létre egy eszközidentitást a IoT Edge eszközhöz, hogy kommunikáljon az IoT Hubbal. Az eszközidentitás a felhőben található, és egy egyedi eszközkapcsolati sztringgel társíthat fizikai eszközt az eszközidentitáshoz.

Mivel IoT Edge eszközök másképp viselkednek és kezelhetők, mint a tipikus IoT-eszközök, deklarálhatja, hogy ez az identitás egy IoT Edge-eszközhöz legyen a `--edge-enabled` jelzővel.

1. A Azure Cloud Shell írja be a következő parancsot egy **myEdgeDevice** nevű eszköz létrehozásához a központban.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
   ```

   Ha hibaüzenetet kap az iothubowner szabályzatkulcsokkal kapcsolatban, ellenőrizze, hogy a Cloud Shell az azure-iot bővítmény legújabb verzióját futtatja-e.

2. Tekintse meg az eszköz kapcsolati sztringet, amely összeköti a fizikai eszközt az eszköz identitásával a IoT Hub. Tartalmazza az IoT Hub nevét, az eszköz nevét, majd egy megosztott kulcsot, amely hitelesíti a kettő közötti kapcsolatokat. A kapcsolati sztringre a következő szakaszban, az eszköz beállításakor IoT Edge hivatkozni.

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
   ```

   ![Kapcsolati sztring megtekintése a CLI-kimenetből](./media/quickstart/retrieve-connection-string.png)

## <a name="configure-your-iot-edge-device"></a>A IoT Edge konfigurálása

Hozzon létre egy virtuális gépet a Azure IoT Edge futtatáskor.

![Ábra – A futásidő elindítani az eszközön](./media/quickstart-linux/start-runtime.png)

Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön üzembe van helyezve. Három összetevőből áll. A IoT Edge biztonsági *démon* minden alkalommal elindul, amikor egy IoT Edge elindul, és az IoT Edge elindítja az eszközt. A *IoT Edge ügynök* elősegíti a modulok üzembe helyezését és monitorozását a IoT Edge eszközön, beleértve a IoT Edge központot is. A *IoT Edge központ* kezeli a modulok közötti kommunikációt a IoT Edge eszközön, valamint az eszköz és a IoT Hub.

A futtatókörnyezet konfigurálása során meg kell adnia egy eszközkapcsolati sztringet. Ez az Azure CLI-ről lekért sztring. Ez a sztring társítja a fizikai eszközt az IoT Edge-eszköz identitásához az Azure-ban.

### <a name="deploy-the-iot-edge-device"></a>A IoT Edge üzembe helyezése

Ez a szakasz egy Azure Resource Manager sablont használ egy új virtuális gép létrehozásához és a IoT Edge telepítéséhez. Ha inkább a saját Linux-eszközét szeretné használni, kövesse az [Install the Azure IoT Edge runtime](how-to-install-iot-edge.md)(A Azure IoT Edge-futásidő telepítése) telepítési lépéseit, majd térjen vissza ehhez a rövid útmutatóhoz.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

A következő CLI-paranccsal hozza létre a IoT Edge az előre összeállított [iotedge-vm-deploy sablon](https://github.com/Azure/iotedge-vm-deploy) alapján.

* Bash vagy Cloud Shell esetén másolja a következő parancsot egy szövegszerkesztőbe, cserélje le a helyőrző szövegét a saját adataira, majd másolja a bash vagy a Cloud Shell ablakába:

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' \
   --parameters adminUsername='azureUser' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```

* PowerShell-felhasználók esetén másolja a következő parancsot a PowerShell-ablakba, majd cserélje le a helyőrző szövegét a saját adataira:

   ```azurecli
   az deployment group create `
   --resource-group IoTEdgeResources `
   --template-uri "https://aka.ms/iotedge-vm-deploy" `
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' `
   --parameters adminUsername='azureUser' `
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) `
   --parameters authenticationType='password' `
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

A következő CLI-paranccsal hozza létre a IoT Edge az előre összeállított [iotedge-vm-deploy sablon](https://github.com/Azure/iotedge-vm-deploy/tree/1.2.0) alapján.

* Bash vagy Cloud Shell esetén másolja a következő parancsot egy szövegszerkesztőbe, cserélje le a helyőrző szövegét a saját adataira, majd másolja a bash vagy a Cloud Shell ablakába:

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json" \
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' \
   --parameters adminUsername='azureUser' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```

* PowerShell-felhasználók esetén másolja a következő parancsot a PowerShell-ablakba, majd cserélje le a helyőrző szövegét a saját adataira:

   ```azurecli
   az deployment group create `
   --resource-group IoTEdgeResources `
   --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json" `
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' `
   --parameters adminUsername='azureUser' `
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) `
   --parameters authenticationType='password' `
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```
:::moniker-end
<!-- end 1.2 -->

Ez a sablon a következő paramétereket veszi fel:

| Paraméter | Leírás |
| --------- | ----------- |
| **erőforráscsoport** | Az erőforráscsoport, amelyben az erőforrások létrejönnek. Használja a cikk során használt alapértelmezett **IoTEdgeResources** erőforrást, vagy adja meg az előfizetésben meglévő erőforráscsoport nevét. |
| **template-uri** | A használt Resource Manager mutató. |
| **dnsLabelPrefix** | A virtuális gép gazdagépnevének létrehozásához használt sztring. Cserélje le a helyőrző szöveget a virtuális gép nevére. |
| **adminUsername** | A virtuális gép rendszergazdai fiókjának felhasználóneve. Használja az **azureUser példát, vagy** adjon meg egy új felhasználónevet. |
| **deviceConnectionString** | Az IoT Hub eszközidentitásának kapcsolati sztringjére, IoT Edge konfigurálni kell a virtuális gépen. A parancssori felület ezen paraméteren belüli parancsa megragadja a kapcsolati sztringet. Cserélje le a helyőrző szövegét az IoT Hub nevére. |
| **authenticationType** | A rendszergazdai fiók hitelesítési módszere. Ez a rövid útmutató **jelszó-hitelesítést** használ, de ezt a paramétert **sshPublicKey értékre is beállíthatja.** |
| **adminPasswordOrKey** | A rendszergazdai fiók SSH-kulcsának jelszava vagy értéke. Cserélje le a helyőrző szövegét egy biztonságos jelszóra. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, és a következő négyből háromnak kell lennie: kisbetűs karakterek, nagybetűk, számjegyek és speciális karakterek. |

Az üzembe helyezés befejezése után JSON-formátumú kimenetet kell kapnia a cli-vel, amely tartalmazza a virtuális géphez való csatlakozáshoz szükséges SSH-információkat. Másolja ki a **kimenetek szakasz nyilvános SSH-bejegyzésének** értékét: 

   ![Nyilvános ssh-érték lekérése a kimenetből](./media/quickstart-linux/outputs-public-ssh.png)

### <a name="view-the-iot-edge-runtime-status"></a>Az IoT Edge-futtatókörnyezet állapotának megtekintése

A rövid útmutatóban található többi parancs magára a IoT Edge eszközre fog történni, hogy lássa, mi történik az eszközön. Ha virtuális gépet használ, most csatlakozzon a géphez a beállított rendszergazdai felhasználónév és az üzembe helyezési parancs által kimenetként megadott DNS-név használatával. A DNS-nevet a virtuális gép áttekintő oldalán is megtalálja a Azure Portal. Használja a következő parancsot a virtuális géphez való csatlakozásra. Cserélje le `{admin username}` a és `{DNS name}` a értékeket a saját értékeire.

   ```console
   ssh {admin username}@{DNS name}
   ```

Miután csatlakozott a virtuális géphez, ellenőrizze, hogy a futásidő sikeresen telepítve és konfigurálva lett-e a IoT Edge eszközön.

<!--1.1 -->
:::moniker range="iotedge-2018-06"

1. Ellenőrizze, hogy a IoT Edge biztonsági démon rendszerszolgáltatásként fut-e.

   ```bash
   sudo systemctl status iotedge
   ```

   ![Lásd a IoT Edge szolgáltatásként futó démont](./media/quickstart-linux/iotedged-running.png)

   >[!TIP]
   >Az `iotedge` parancsok futtatásához megemelt jogosultsági szint szükséges. Amikor az IoT Edge-futtatókörnyezet telepítése után kijelentkezik, majd először újra bejelentkezik a gépre, az engedélyei automatikusan frissülnek. Addig használja a `sudo` parancsot a parancsok előtt.

2. Ha hibaelhárításra van szükség, kérje le a szolgáltatás naplóit.

   ```bash
   journalctl -u iotedge
   ```

3. Tekintse meg az IoT Edge-eszközön futó összes modult. Mivel első alkalommal indította el ezt a szolgáltatást, csak az **edgeAgent** modulnak szabad futnia. Az edgeAgent modul alapértelmezés szerint fut, és segít telepíteni és elindítani az eszközön üzembe helyezett további modulokat.

   ```bash
   sudo iotedge list
   ```

   ![Egy modul megtekintése az eszközön](./media/quickstart-linux/iotedge-list-1.png)
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Ellenőrizze, hogy IoT Edge fut-e. A következő parancsnak **OK** állapotot kell visszaadni, ha IoT Edge fut, vagy szolgáltatási hibákat ad vissza.

   ```bash
   sudo iotedge system status
   ```

   >[!TIP]
   >Az `iotedge` parancsok futtatásához megemelt jogosultsági szint szükséges. Amikor az IoT Edge-futtatókörnyezet telepítése után kijelentkezik, majd először újra bejelentkezik a gépre, az engedélyei automatikusan frissülnek. Addig használja a `sudo` parancsot a parancsok előtt.

2. Ha hibaelhárításra van szükség, kérje le a szolgáltatás naplóit.

   ```bash
   sudo iotedge system logs
   ```

3. Tekintse meg az IoT Edge-eszközön futó összes modult. Mivel első alkalommal indította el ezt a szolgáltatást, csak az **edgeAgent** modulnak szabad futnia. Az edgeAgent modul alapértelmezés szerint fut, és segít telepíteni és elindítani az eszközön üzembe helyezett további modulokat.

   ```bash
   sudo iotedge list
   ```

:::moniker-end
<!-- end 1.2 -->

Ezzel konfigurálta az IoT Edge-eszközt. Az eszköz készen áll a felhőben üzembe helyezett modulok futtatására.

## <a name="deploy-a-module"></a>Modul üzembe helyezése

Azure IoT Edge-eszközeit kezelheti a felhőből, és üzembe helyezhet egy olyan modult, amely telemetriaadatokat küld az IoT Hubra.

![Ábra – Modul üzembe helyezése a felhőből az eszközre](./media/quickstart-linux/deploy-module.png)

<!-- [!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

Include content included below to support versioned steps in Linux quickstart. Can update include file once Windows quickstart supports v1.2 -->

Az alkalmazás egyik legfontosabb Azure IoT Edge kód üzembe helyezése a IoT Edge a felhőből. *IoT Edge modulok tárolóként* megvalósított végrehajtható csomagok. Ebben a szakaszban egy előre felépített modult fog üzembe helyezni a IoT Edge Modulok szakaszában, [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) közvetlenül a Azure IoT Hub.

Az ebben a szakaszban üzembe helyezett modul egy érzékelőt szimulál, és elküldi a létrehozott adatokat. Ez a modul hasznos kód, amikor elkezdi a IoT Edge mert a szimulált adatokat fejlesztési és tesztelési célokra is használhatja. Ha szeretné pontosan megtekinteni a modult, megtekintheti a szimulált hőmérséklet-érzékelő [forráskódját.](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs)

Az alábbi lépéseket követve indítsa el a **Modulok** beállítása varázslót az első modul üzembe helyezéséhez Azure Marketplace.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) az IoT Hubra.

1. A bal oldali menüBen, az **Automatikus Eszközkezelés** alatt válassza **a** IoT Edge lehetőséget.

1. Válassza ki a céleszköz eszközazonosítóját az eszközök listájából.

1. A felső sávon válassza a **Modulok beállítása lehetőséget.**

   ![A Modulok beállítása lehetőség kiválasztását bemutató képernyőkép.](./media/quickstart/select-set-modules.png)

### <a name="modules"></a>Modulok

A varázsló első lépéseként ki kell választania, hogy mely modulokat szeretné futtatni az eszközön.

A **IoT Edge alatt** nyissa meg a Hozzáadás **legördülő** menüt, majd válassza a **Marketplace-modul lehetőséget.**

   ![A Hozzáadás legördülő menüt bemutató képernyőkép.](./media/quickstart/add-marketplace-module.png)

A **IoT Edge Piactéren** keresse meg és válassza ki a `Simulated Temperature Sensor` modult. A modult a rendszer hozzáadja IoT Edge modulok szakaszhoz a kívánt **futtatás állapotával.**

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Válassza **a Futásidejű beállítások lehetőséget** az edgeHub és az edgeAgent modulok beállításainak megnyitásához. Ebben a beállítási szakaszban környezeti változók hozzáadásával vagy a létrehozási beállítások módosításával kezelheti a futásidejű modulokat.

Frissítse **az EdgeHub** és az edgeAgent modul Image (Rendszerkép) mezőjét úgy, hogy az 1.2-es verziócímkét használják. Például:

* `mcr.microsoft.com/azureiotedge-hub:1.2`
* `mcr.microsoft.com/azureiotedge-agent:1.2`

A **módosítások a** futásidejű modulokra való alkalmazáshoz válassza a Mentés lehetőséget.

:::moniker-end
<!--end 1.2-->

Válassza **a Tovább: Útvonalak lehetőséget** a varázsló következő lépésének folytatásához.

   ![Képernyőkép a modul hozzáadása utáni következő lépésre való továbblépésről.](./media/quickstart/view-temperature-sensor-next-routes.png)

### <a name="routes"></a>Útvonalak

Az Útvonalak **lapon** távolítsa el az alapértelmezett útvonalat, útvonalként a útvonalat, majd válassza a **Tovább:** Áttekintés + létrehozás lehetőséget a varázsló következő lépésének folytatásához.

   >[!Note]
   >Az útvonalak név-érték párok használatával vannak összeépítve. Ezen az oldalon két útvonalnak kell lennie. A útvonal alapértelmezett **útvonala** minden üzenetet IoT Hub (amelynek a neve `$upstream` ). A második **útvonal, a SimulatedTemperatureSensorToIoTHub** automatikusan létrejött, amikor hozzáadta a modult a Azure Marketplace. Ez az útvonal minden üzenetet küld a szimulált hőmérséklet modulból a IoT Hub. Az alapértelmezett útvonalat törölheti, mert ebben az esetben redundáns.

   ![Képernyőkép az alapértelmezett útvonal eltávolításáról, majd a következő lépésre való áthelyezésről.](./media/quickstart/delete-route-next-review-create.png)

### <a name="review-and-create"></a>Áttekintés és létrehozás

Tekintse át a JSON-fájlt, majd válassza a **Létrehozás lehetőséget.** A JSON-fájl határozza meg az összes olyan modult, amely az IoT Edge üzembe. Látni fogja a **SimulatedTemperatureSensor** modult és a két futásidejű modult, az **edgeAgent és** az **edgeHub modult.**

   >[!Note]
   >Amikor egy új üzemelő példányt küld egy IoT Edge-eszközre, semmi sem kerül az eszközre. Ehelyett az eszköz rendszeresen lekérdezi az IoT Hubról az új utasításokat. Ha az eszköz egy frissítettüzembe helyezési jegyzéket talál, felhasználja az új üzemelő példányra vonatkozó információt a modul lemezképeinek felhőből történő adatkiszolgálásához, majd elindítja a modulok helyi futtatását. Ez a folyamat eltarthat néhány percig.

A modul üzembe helyezési részleteinek létrehozása után a varázsló visszatér az eszköz részleteit tartalmazó oldalra. Tekintse meg az üzembe helyezés állapotát a **Modulok** lapon.

Három modulnak kell **$edgeAgent:** **$edgeHub**, és **SimulatedTemperatureSensor**. Ha egy vagy több  modul ESETÉBEN a **MEGADOTT** AZ ÜZEMBE HELYEZÉS ALATT IGEN érték található, de az ESZKÖZ JELENTÉSE alatt **nem,** a IoT Edge eszköz továbbra is elkezdi azokat. Várjon néhány percet, majd frissítse az oldalt.

   ![Képernyőkép a Simulated Temperature Sensorról az üzembe helyezett modulok listájában.](./media/quickstart/view-deployed-modules.png)

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Ebben a rövid útmutatóban létrehozott egy új IoT Edge-eszközt, és telepítette rajta az IoT Edge-futtatókörnyezetet. Ezután a Azure Portal üzembe helyezett egy IoT Edge-modult, hogy anélkül fusson az eszközön, hogy magát az eszközt módosítanának.

Ebben az esetben a lekért modul mintakörnyezeti adatokat hoz létre, amelyek később tesztelésre használhatók. A szimulált érzékelő a gépet és a gépet körülvesző környezetet is figyeli. Ez az érzékelő lehet például egy kiszolgálóteremben, egy üzemben vagy akár szélturbinán. Az üzenet tartalmazza a környezeti hőmérsékletet, a páratartalmat, a gép hőmérsékletét, a nyomást és egy timestampet. Az IoT Edge oktatóanyagok a modul által létrehozott adatokat használják elemzési tesztadatokként.

Nyissa meg ismét a parancssort a IoT Edge-eszközön, vagy használja az SSH-kapcsolatokat az Azure CLI-ről. Győződjön meg arról, hogy a felhőből üzembe helyezett modul fut az IoT Edge-eszközön:

   ```bash
   sudo iotedge list
   ```

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
   ![Három modul megtekintése az eszközön](./media/quickstart-linux/iotedge-list-2-version-201806.png)
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
   ![Három modul megtekintése az eszközön](./media/quickstart-linux/iotedge-list-2-version-202011.png)
:::moniker-end

Tekintse meg a hőmérséklet-érzékelő modulból küldött üzeneteket:

   ```bash
   sudo iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!TIP]
   >IoT Edge a parancsok megkülönböztetik a kis- és nagybetűket a modulnevekre való hivatkozáskor.

   ![A modulból származó adatok megtekintése](./media/quickstart-linux/iotedge-logs.png)

Az IoT Hubra érkező üzeneteket a kódhoz Azure IoT Hub [is Visual Studio figyelheti.](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha tovább szeretne dolgozni az IoT Edge-oktatóanyagokkal, használhatja az ebben a rövid útmutatóban regisztrált és létrehozott eszközt. Ellenkező esetben a díjak elkerülése érdekében törölheti a létrehozott Azure-erőforrásokat.

Ha a virtuális gépet és az IoT Hubot egy új erőforráscsoportban hozta létre, törölheti azt a csoportot és az összes társított erőforrást. Ellenőrizze újra az erőforráscsoport tartalmát, és győződjön meg arról, hogy nincs semmi, amit meg szeretne tartani. Ha nem szeretné a teljes csoportot törölni, akkor egyéni erőforrásokat is törölhet.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza.

Távolítsa el az **IoTEdgeResources** csoportot. Az erőforráscsoport törlése eltarthat néhány percig.

```azurecli-interactive
az group delete --name IoTEdgeResources --yes
```

Az erőforráscsoportok listájának megtekintésével meggyőződhet arról, hogy az erőforráscsoport el lett távolítva.

```azurecli-interactive
az group list
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy IoT Edge eszközt, és a Azure IoT Edge használatával kódot helyezett üzembe az eszközön. Most már van egy teszteszköze, amely nyers adatokat állít elő a környezetéről.

A következő lépés a helyi fejlesztési környezet beállítása, hogy IoT Edge az üzleti logikát futtató modulokat.

> [!div class="nextstepaction"]
> [Új modulok IoT Edge Linux-eszközökhöz](tutorial-develop-for-linux.md)

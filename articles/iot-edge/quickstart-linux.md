---
title: Gyors útmutató Azure IoT Edge-eszköz létrehozásához Linux rendszeren | Microsoft Docs
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre IoT Edge-eszközt Linux rendszeren, majd a Azure Portalból távolról is üzembe helyezhet egy előre elkészített kódot.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/07/2021
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 10a073914a79d29ae4b1c1d90ae5be624e7d7673
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303883"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-linux-device"></a>Rövid útmutató: az első IoT Edge modul üzembe helyezése egy virtuális Linux-eszközön

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Kipróbálhatja Azure IoT Edge ebben a rövid útmutatóban, ha egy virtuális Linux IoT Edge eszközre helyez üzembe tároló kódot. IoT Edge lehetővé teszi a kódok távoli kezelését az eszközökön, így több számítási feladatot is elküldhet a peremhálózat számára. Ebben a rövid útmutatóban azt javasoljuk, hogy használjon egy Azure-beli virtuális gépet a IoT Edge eszközhöz, amely lehetővé teszi, hogy gyorsan hozzon létre egy tesztelési gépet, majd törölje azt, ha elkészült.

Ennek a rövid útmutatónak a segítségével megtanulhatja az alábbiakat:

* IoT Hub létrehozása
* IoT Edge-eszköz regisztrálása az IoT Hubon
* Telepítse és indítsa el a IoT Edge futtatókörnyezetet egy virtuális eszközön.
* Modul távoli üzembe helyezése IoT Edge-eszközön.

![Diagram – az eszköz és a felhő gyors üzembe helyezési architektúrája](./media/quickstart-linux/install-edge-full.png)

Ez a rövid útmutató végigvezeti a IoT Edge eszközként konfigurált Linux rendszerű virtuális gépek létrehozásának lépésein. Ezután telepítsen egy modult a Azure Portal az eszközre. Az ebben a rövid útmutatóban használt modul egy szimulált érzékelő, amely hőmérséklet-, páratartalom-és adatterhelési adattípust generál. A többi Azure IoT Edge oktatóanyag az itt végzett munkára építve olyan további modulok üzembe helyezésével végezhető el, amelyek elemzik az üzleti információk szimulált adatait.

Ha nem rendelkezik aktív Azure-előfizetéssel, kezdetnek hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Előfeltételek

Készítse elő a környezetet az Azure CLI-hez.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Felhőerőforrások:

* Egy erőforráscsoport a rövid útmutató során létrehozott összes erőforrás kezelésére. Ebben a rövid útmutatóban a példa nevű erőforráscsoport-nevet használjuk, és a következő oktatóanyagokat **IoTEdgeResources** .

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

A rövid útmutató elindításához hozzon létre egy IoT hubot az Azure CLI-vel.

![Diagram – IoT hub létrehozása a felhőben](./media/quickstart-linux/create-iot-hub.png)

Ehhez a rövid útmutatóhoz az IoT Hub ingyenes csomagja is elegendő. Ha korábban már használta a IoT Hubt, és már rendelkezik egy hubhoz, akkor használhatja ezt az IoT hubot.

A következő kód egy ingyenes **F1** -hubot hoz létre az erőforráscsoport **IoTEdgeResources**. Cserélje le `{hub_name}` az nevet az IoT hub egyedi nevére. A IoT Hub létrehozása néhány percet is igénybe vehet.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   Ha hibaüzenetet kap, mert az előfizetése már tartalmaz egy ingyenes központot, akkor módosítsa az SKU-t **S1**-re. Mindegyik előfizetés csak egy ingyenes IoT-központtal rendelkezhet. Ha hibaüzenet jelenik meg, hogy a IoT Hub neve nem érhető el, az azt jelenti, hogy valaki más már rendelkezik az adott névvel rendelkező hubhoz. Próbálkozzon új névvel.

## <a name="register-an-iot-edge-device"></a>IoT Edge-eszköz regisztrálása

Regisztráljon egy IoT Edge-eszközt az újonnan létrehozott IoT Hubon.

![Diagram – eszköz regisztrálása IoT Hub identitással](./media/quickstart-linux/register-device.png)

Hozzon létre egy eszköz-identitást a IoT Edge eszközön, hogy az képes legyen kommunikálni az IoT hubhoz. Az eszközidentitás a felhőben található, és egy egyedi eszközkapcsolati sztringgel társíthat fizikai eszközt az eszközidentitáshoz.

Mivel IoT Edge-eszközök viselkedése és kezelése eltérően történik a szokásos IoT-eszközöknél, deklarálja ezt az identitást egy IoT Edge eszközhöz a `--edge-enabled` jelzővel.

1. A Azure Cloud Shell írja be a következő parancsot egy **myEdgeDevice** nevű eszköz létrehozásához a központban.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
   ```

   Ha hibaüzenetet kap a iothubowner, győződjön meg arról, hogy a Cloud Shell az Azure-IOT bővítmény legújabb verzióját futtatja.

2. Megtekintheti az eszköz kapcsolati karakterláncát, amely a fizikai eszközt a IoT Hub identitásával társítja. Tartalmazza az IoT hub nevét, az eszköz nevét, majd egy megosztott kulcsot, amely a kettő közötti kapcsolatokat hitelesíti. Ezt a kapcsolódási karakterláncot ismét a következő szakaszban fogjuk megtekinteni a IoT Edge eszköz beállításakor.

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
   ```

   ![A CLI-kimenetből származó kapcsolatok karakterláncának megtekintése](./media/quickstart/retrieve-connection-string.png)

## <a name="configure-your-iot-edge-device"></a>A IoT Edge eszköz konfigurálása

Hozzon létre egy virtuális gépet a Azure IoT Edge futtatókörnyezettel.

![Diagram – a futtatókörnyezet elindítása az eszközön](./media/quickstart-linux/start-runtime.png)

Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön üzembe van helyezve. Három összetevőből áll. A *IoT Edge biztonsági démon* minden alkalommal elindul, amikor egy IoT Edge-eszköz elindul, és elindítja az eszközt a IoT Edge ügynök elindításával. A *IoT Edge ügynök* megkönnyíti a modulok üzembe helyezését és figyelését a IoT Edge eszközön, beleértve az IoT Edge hubot is. Az *IoT Edge hub* kezeli a IoT Edge eszközön található modulok közötti kommunikációt, valamint az eszköz és a IoT hub között.

A futtatókörnyezet konfigurálása során meg kell adnia egy eszközkapcsolati sztringet. Ez az Azure CLI-ből beolvasott karakterlánc. Ez a sztring társítja a fizikai eszközt az IoT Edge-eszköz identitásához az Azure-ban.

### <a name="deploy-the-iot-edge-device"></a>A IoT Edge eszköz üzembe helyezése

Ez a szakasz egy Azure Resource Manager sablont használ egy új virtuális gép létrehozásához és a IoT Edge futtatókörnyezet telepítéséhez. Ha ehelyett a saját linuxos eszközét szeretné használni, kövesse a telepítési lépéseket a [Azure IoT Edge futtatókörnyezet telepítése](how-to-install-iot-edge.md)című témakörben, majd térjen vissza ehhez a rövid útmutatóhoz.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

A következő CLI-paranccsal hozhatja létre IoT Edge eszközét az előre elkészített [iotedge-VM-Deploy](https://github.com/Azure/iotedge-vm-deploy) sablon alapján.

* Bash-vagy Cloud Shell-felhasználók esetén másolja a következő parancsot egy szövegszerkesztőbe, cserélje le a helyőrző szövegét az adataira, majd másolja be a bash vagy Cloud Shell ablakába:

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

* PowerShell-felhasználók esetén másolja a következő parancsot a PowerShell ablakába, majd cserélje le a helyőrző szöveget a saját adataira:

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

A következő CLI-paranccsal hozhatja létre IoT Edge eszközét az előre elkészített [iotedge-VM-Deploy](https://github.com/Azure/iotedge-vm-deploy/tree/1.2.0-rc4) sablon alapján.

* Bash-vagy Cloud Shell-felhasználók esetén másolja a következő parancsot egy szövegszerkesztőbe, cserélje le a helyőrző szövegét az adataira, majd másolja be a bash vagy Cloud Shell ablakába:

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0-rc4/edgeDeploy.json" \
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' \
   --parameters adminUsername='azureUser' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```

* PowerShell-felhasználók esetén másolja a következő parancsot a PowerShell ablakába, majd cserélje le a helyőrző szöveget a saját adataira:

   ```azurecli
   az deployment group create `
   --resource-group IoTEdgeResources `
   --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0-rc4/edgeDeploy.json" `
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' `
   --parameters adminUsername='azureUser' `
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) `
   --parameters authenticationType='password' `
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```
:::moniker-end
<!-- end 1.2 -->

A sablon a következő paramétereket veszi figyelembe:

| Paraméter | Leírás |
| --------- | ----------- |
| **erőforrás-csoport** | Az az erőforráscsoport, amelyben az erőforrások létre lesznek hozva. Használja az ebben a cikkben használt alapértelmezett **IoTEdgeResources** , vagy adja meg egy meglévő erőforráscsoport nevét az előfizetésében. |
| **sablon – URI** | A használt Resource Manager-sablonra mutató hivatkozás. |
| **dnsLabelPrefix** | Egy karakterlánc, amely a virtuális gép állomásneve létrehozásához lesz felhasználva. Cserélje le a helyőrző szöveget a virtuális gép nevére. |
| **adminUsername** | A virtuális gép rendszergazdai fiókjához tartozó Felhasználónév. Használja a példa **azureuser** -t, vagy adjon meg egy új felhasználónevet. |
| **deviceConnectionString** | A IoT Hub eszköz identitásának kapcsolódási karakterlánca, amely a IoT Edge futtatókörnyezetnek a virtuális gépen való konfigurálására szolgál. A paraméteren belüli CLI-parancs megkeresi a kapcsolatok karakterláncát. Cserélje le a helyőrző szövegét az IoT hub nevére. |
| **authenticationType** | A rendszergazdai fiók hitelesítési módszere. Ez a rövid útmutató **jelszavas** hitelesítést használ, de ezt a paramétert **sshPublicKey** is beállíthatja. |
| **adminPasswordOrKey** | A rendszergazdai fiók SSH-kulcsának jelszava vagy értéke. Cserélje le a helyőrző szövegét biztonságos jelszóra. A jelszónak legalább 12 karakterből kell állnia, és a következők közül hármat tartalmaznia kell: kisbetűk, nagybetűk, számjegyek és speciális karakterek. |

Az üzembe helyezés befejezése után a virtuális géphez való kapcsolódáshoz használt SSH-adatokat tartalmazó CLI-ből kell fogadnia a JSON-formátumú kimenetet. Másolja a **kimenetek** szakasz **nyilvános SSH** -bejegyzésének értékét:

   ![Nyilvános SSH-érték beolvasása a kimenetből](./media/quickstart-linux/outputs-public-ssh.png)

### <a name="view-the-iot-edge-runtime-status"></a>Az IoT Edge-futtatókörnyezet állapotának megtekintése

Az ebben a rövid útmutatóban szereplő további parancsok a IoT Edge eszközön történnek, így megtekintheti, hogy mi történik az eszközön. Ha virtuális gépet használ, az Ön által beállított rendszergazdai felhasználónévvel és az üzembe helyezési parancs által kimeneti DNS-névvel csatlakozhat a géphez. A DNS-nevet a virtuális gép áttekintés lapján is megtalálhatja a Azure Portalban. Használja a következő parancsot a virtuális géphez való csatlakozásra. Cserélje `{admin username}` le `{DNS name}` a és a értéket a saját értékeire.

   ```console
   ssh {admin username}@{DNS name}
   ```

A virtuális géphez való csatlakozás után ellenőrizze, hogy a futtatókörnyezet telepítése és konfigurálása sikeres volt-e a IoT Edge eszközön.

<!--1.1 -->
:::moniker range="iotedge-2018-06"

1. Ellenőrizze, hogy a IoT Edge biztonsági démon rendszerszolgáltatásként fut-e.

   ```bash
   sudo systemctl status iotedge
   ```

   ![Tekintse meg a rendszerszolgáltatásként futó IoT Edge démont](./media/quickstart-linux/iotedged-running.png)

   >[!TIP]
   >Az `iotedge` parancsok futtatásához megemelt jogosultsági szint szükséges. Amikor az IoT Edge-futtatókörnyezet telepítése után kijelentkezik, majd először újra bejelentkezik a gépre, az engedélyei automatikusan frissülnek. Addig használja `sudo` a parancsot a parancsok előtt.

2. Ha hibaelhárításra van szükség, kérje le a szolgáltatás naplóit.

   ```bash
   journalctl -u iotedge
   ```

3. Tekintse meg az IoT Edge-eszközön futó összes modult. Mivel első alkalommal indította el ezt a szolgáltatást, csak az **edgeAgent** modulnak szabad futnia. A edgeAgent modul alapértelmezés szerint fut, és az eszközre telepített további modulok telepítését és elindítását is lehetővé teszi.

   ```bash
   sudo iotedge list
   ```

   ![Egy modul megtekintése az eszközön](./media/quickstart-linux/iotedge-list-1.png)
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Ellenőrizze, hogy a IoT Edge fut-e. A következő parancsnak az **OK** állapotot kell visszaadnia, ha IoT Edge fut, vagy szolgáltatási hibát ad meg.

   ```bash
   sudo iotedge system status
   ```

   >[!TIP]
   >Az `iotedge` parancsok futtatásához megemelt jogosultsági szint szükséges. Amikor az IoT Edge-futtatókörnyezet telepítése után kijelentkezik, majd először újra bejelentkezik a gépre, az engedélyei automatikusan frissülnek. Addig használja `sudo` a parancsot a parancsok előtt.

2. Ha hibaelhárításra van szükség, kérje le a szolgáltatás naplóit.

   ```bash
   sudo iotedge system logs
   ```

3. Tekintse meg az IoT Edge-eszközön futó összes modult. Mivel első alkalommal indította el ezt a szolgáltatást, csak az **edgeAgent** modulnak szabad futnia. A edgeAgent modul alapértelmezés szerint fut, és az eszközre telepített további modulok telepítését és elindítását is lehetővé teszi.

   ```bash
   sudo iotedge list
   ```

:::moniker-end
<!-- end 1.2 -->

Ezzel konfigurálta az IoT Edge-eszközt. Az eszköz készen áll a felhőben üzembe helyezett modulok futtatására.

## <a name="deploy-a-module"></a>Modul üzembe helyezése

Azure IoT Edge-eszközeit kezelheti a felhőből, és üzembe helyezhet egy olyan modult, amely telemetriaadatokat küld az IoT Hubra.

![Diagram – modul üzembe helyezése a felhőből az eszközre](./media/quickstart-linux/deploy-module.png)

<!-- [!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

Include content included below to support versioned steps in Linux quickstart. Can update include file once Windows quickstart supports v1.2 -->

Azure IoT Edge egyik fő funkciója, hogy a felhőből üzembe helyezi a IoT Edge-eszközökön a kódot. *IoT Edge modulok* tárolóként megvalósított végrehajtható csomagok. Ebben a szakaszban egy előre összeállított modult helyezünk üzembe az [Azure Marketplace IoT Edge-modulok szakaszával](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) közvetlenül az Azure IoT hub-ból.

Az ebben a szakaszban üzembe helyezett modul szimulál egy érzékelőt, és a generált adatokat küldi el. Ez a modul hasznos kódrészlet, ha első lépések a IoT Edgehoz, mivel a szimulált adat a fejlesztéshez és a teszteléshez használható. Ha pontosan látni szeretné a modult, akkor megtekintheti a [szimulált hőmérséklet-érzékelő forráskódját](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Kövesse az alábbi lépéseket a **modulok beállítása** varázsló elindításához az első modul üzembe helyezéséhez az Azure Marketplace-en.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és nyissa meg az IoT hubot.

1. A bal oldali menüben az **automatikus eszközkezelés** területen válassza a **IoT Edge** lehetőséget.

1. Válassza ki a céleszköz eszközének AZONOSÍTÓját az eszközök listájából.

1. A felső sávon válassza a **modulok beállítása** lehetőséget.

   ![Képernyőkép, amely a set modulok kiválasztása lehetőséget mutatja.](./media/quickstart/select-set-modules.png)

### <a name="modules"></a>Modulok

A varázsló első lépéseként ki kell választania, hogy mely modulokat szeretné futtatni az eszközön.

A **IoT Edge modulok** területen nyissa meg a **Hozzáadás** legördülő menüt, majd válassza a **piactér modul** elemet.

   ![A Hozzáadás legördülő menüt megjelenítő képernyőkép.](./media/quickstart/add-marketplace-module.png)

A **IoT Edge modul piactéren** keresse meg és válassza ki a `Simulated Temperature Sensor` modult. A modul hozzá lesz adva a IoT Edge modulok szakaszhoz a kívánt **futtatási** állapottal.

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Válassza ki a **futtatókörnyezet beállításait** a edgeHub és a edgeAgent modulok beállításainak megnyitásához. Ez a beállítások szakasz a futásidejű modulok kezelését környezeti változók hozzáadásával vagy a létrehozási beállítások módosításával végezheti el.

Frissítse a **képmezőt mind a edgeHub** , mind a edgeAgent-modulhoz a 1,2-es verzió-címke használatára. Például:

* `mcr.microsoft.com/azureiotedge-hub:1.2`
* `mcr.microsoft.com/azureiotedge-agent:1.2`

Válassza a **Save (Mentés** ) lehetőséget a módosítások alkalmazásához a futásidejű modulokban.

:::moniker-end
<!--end 1.2-->

Válassza a **tovább lehetőséget: útvonalakat** a varázsló következő lépéséhez.

   ![Képernyőkép, amely a modul hozzáadása után a következő lépés folytatását mutatja.](./media/quickstart/view-temperature-sensor-next-routes.png)

### <a name="routes"></a>Útvonalak

Az **útvonalak** lapon távolítsa el az alapértelmezett útvonalat, az **útvonalat**, majd válassza a **Tovább: felülvizsgálat + létrehozás** lehetőséget a varázsló következő lépésének folytatásához.

   >[!Note]
   >Az útvonalak név és érték párok használatával jönnek létre. Ezen a lapon két útvonalnak kell megjelennie. Az alapértelmezett útvonal, az **útvonal**, az összes üzenet küldése a IoT hubnak (amely neve `$upstream` ). A második útvonal, a **SimulatedTemperatureSensorToIoTHub**, automatikusan jött létre, amikor hozzáadta a modult az Azure Marketplace-ről. Ez az útvonal a szimulált hőmérséklet modul összes üzenetét IoT Hubba küldi. Az alapértelmezett útvonalat törölheti, mert ebben az esetben redundáns lehet.

   ![Képernyőfelvétel: az alapértelmezett útvonal eltávolítása, majd a következő lépésre való áttérés.](./media/quickstart/delete-route-next-review-create.png)

### <a name="review-and-create"></a>Áttekintés és létrehozás

Tekintse át a JSON-fájlt, majd kattintson a **Létrehozás** gombra. A JSON-fájl a IoT Edge eszközre telepített összes modult definiálja. Ekkor megjelenik a **SimulatedTemperatureSensor** modul és a két futásidejű modul, a **EdgeAgent** és a **edgeHub**.

   >[!Note]
   >Amikor egy új üzemelő példányt küld egy IoT Edge-eszközre, semmi sem kerül az eszközre. Ehelyett az eszköz rendszeresen lekérdezi az IoT Hubról az új utasításokat. Ha az eszköz egy frissítettüzembe helyezési jegyzéket talál, felhasználja az új üzemelő példányra vonatkozó információt a modul lemezképeinek felhőből történő adatkiszolgálásához, majd elindítja a modulok helyi futtatását. Ez a folyamat eltarthat néhány percig.

Miután létrehozta a modul központi telepítésének részleteit, a varázsló visszaadja az eszköz adatai lapot. Tekintse meg a telepítés állapotát a **modulok** lapon.

Három modulnak kell megjelennie: **$edgeAgent**, **$edgeHub** és **SimulatedTemperatureSensor**. Ha egy vagy több modul **Igen** értékkel rendelkezik **az üzembe helyezés során** , de nem az **eszköz által jelentett**, akkor a IoT Edge eszköz továbbra is elindul. Várjon néhány percet, majd frissítse az oldalt.

   ![A telepített modulok listájában a szimulált hőmérséklet-érzékelőt bemutató képernyőkép.](./media/quickstart/view-deployed-modules.png)

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Ebben a rövid útmutatóban létrehozott egy új IoT Edge-eszközt, és telepítette rajta az IoT Edge-futtatókörnyezetet. Ezt követően a Azure Portal használatával telepítheti az eszközön a IoT Edge modult anélkül, hogy magát az eszközt kellene módosítania.

Ebben az esetben a lekért modul olyan minta környezeti adatfeldolgozást hoz létre, amelyet később tesztelésre használhat. A szimulált érzékelő a gépet és a környezetet is figyeli a gép körül. Ez az érzékelő lehet például egy kiszolgálóteremben, egy üzemben vagy akár szélturbinán. Az üzenet tartalmazza a környezeti hőmérsékletet, a páratartalmat, a gép hőmérsékletét, a nyomást és egy timestampet. A IoT Edge oktatóanyagok a modul által létrehozott adatelemzési adatként használják.

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
   >IoT Edge parancsok a kis-és nagybetűk megkülönböztetésére szolgálnak a modulok neveire való hivatkozáskor.

   ![A modulból származó adatok megtekintése](./media/quickstart-linux/iotedge-logs.png)

A [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)-hoz készült Azure IoT hub-bővítmény használatával megtekintheti az IoT hub-ra érkező üzeneteket is.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha tovább szeretne dolgozni az IoT Edge-oktatóanyagokkal, használhatja az ebben a rövid útmutatóban regisztrált és létrehozott eszközt. Ellenkező esetben törölheti a létrehozott Azure-erőforrásokat a díjak elkerülése érdekében.

Ha a virtuális gépet és az IoT Hubot egy új erőforráscsoportban hozta létre, törölheti azt a csoportot és az összes társított erőforrást. Ellenőrizze az erőforráscsoport tartalmát, és győződjön meg róla, hogy nem kívánja megőrizni a lépést. Ha nem szeretné törölni a teljes csoportot, az egyes erőforrásokat is törölheti.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza.

Távolítsa el az **IoTEdgeResources** csoportot. Egy erőforráscsoport törlése eltarthat néhány percig.

```azurecli-interactive
az group delete --name IoTEdgeResources --yes
```

Az erőforráscsoport törlését az erőforráscsoportok listájának megtekintésével ellenőrizheti.

```azurecli-interactive
az group list
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy IoT Edge eszközt, és a Azure IoT Edge Cloud Interface használatával helyezi üzembe a kódot az eszközön. Most már van egy teszteszköze, amely nyers adatokat állít elő a környezetéről.

A következő lépés a helyi fejlesztési környezet beállítása, amelynek segítségével megkezdheti az üzleti logikát futtató IoT Edge-modulok létrehozását.

> [!div class="nextstepaction"]
> [A Linux-eszközökhöz készült IoT Edge modulok fejlesztésének megkezdése](tutorial-develop-for-linux.md)

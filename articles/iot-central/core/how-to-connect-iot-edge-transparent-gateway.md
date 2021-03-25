---
title: IoT Edge transzparens átjáró összekötése egy Azure IoT Central-alkalmazással
description: Eszközök csatlakoztatása IoT Edge transzparens átjárón keresztül egy IoT Central alkalmazáshoz
author: dominicbetts
ms.author: dobett
ms.date: 03/08/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 4e88ad58c7baba1c66c30df3f4effdbf11371c18
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045331"
---
# <a name="how-to-connect-devices-through-an-iot-edge-transparent-gateway"></a>Eszközök csatlakoztatása IoT Edge transzparens átjárón keresztül

Egy IoT Edge eszköz olyan átjáróként működhet, amely kapcsolatot biztosít a helyi hálózaton lévő más eszközök és a IoT Central alkalmazás között. Az átjárót akkor használja, ha az eszköz nem fér hozzá közvetlenül a IoT Central alkalmazáshoz.

A IoT Edge támogatja az [ *átlátható* és a *fordítási* átjárók mintáit](../../iot-edge/iot-edge-as-gateway.md). Ez a cikk az átlátszó átjáró mintázatának megvalósítását összegzi. Ebben a mintában az átjáró továbbítja az üzeneteket az alsóbb rétegbeli eszközről a IoT Central alkalmazás IoT Hub végpontján.

Ez a cikk a virtuális gépeket használja az alsóbb rétegbeli eszköz és az átjáró üzemeltetéséhez. Valós esetben az alsóbb rétegbeli eszköz és az átjáró a helyi hálózaton található fizikai eszközökön fut.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag lépéseinek elvégzéséhez aktív Azure-előfizetésre van szükség.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Fejezze be az [Azure IoT Central-alkalmazás létrehozása](./quick-deploy-iot-central.md) című rövid útmutatót egy IoT Central-alkalmazás létrehozásához az **egyéni alkalmazás > egyéni alkalmazásspecifikus** sablon használatával.

A cikkben ismertetett lépéseket követve töltse le a következő fájlokat a számítógépre:

- [Termosztátos eszköz modellje](https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main/dtmi/com/example/thermostat-1.json)
- [Transzparens átjáró jegyzékfájlja](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/EdgeTransparentGatewayManifest.json)

## <a name="add-device-templates"></a>Eszköz sablonok hozzáadása

Az alsóbb rétegbeli eszközök és az átjáró eszköz számára a IoT Central eszköz sablonjai szükségesek. IoT Central lehetővé teszi az alsóbb rétegbeli eszközök és az átjáró közötti kapcsolat modellezését, így a csatlakozás után megtekintheti és kezelheti őket.

Egy alsóbb rétegbeli eszközhöz tartozó sablon létrehozásához hozzon létre egy szabványos eszközt, amely modellezi az eszköz képességeit. A cikkben bemutatott példa a termosztát eszköz modelljét használja.

Eszköz sablonjának létrehozása alsóbb rétegbeli eszközhöz:

1. Hozzon létre egy sablont, és válassza a **IoT eszközt** a sablon típusaként.

1. A varázsló **Testreszabás** lapján adjon meg egy nevet, például *termosztátot* az eszköz sablonja számára.

1. Az eszköz sablonjának létrehozása után válassza **a modell importálása** lehetőséget. Válasszon ki egy modellt, például a korábban letöltött fájl *thermostat-1.js* .

1. A termosztát alapértelmezett nézeteinek létrehozásához válassza a nézetek lehetőséget, majd válassza az **alapértelmezett nézetek előállítása** lehetőséget.

1. Tegye közzé az eszköz sablonját.

Eszköz sablonjának létrehozása átlátszó IoT Edge átjáróhoz:

1. Hozzon létre egy sablont, és válassza a **Azure IoT Edge** lehetőséget a sablon típusaként.

1. A varázsló **Testreszabás** lapján adjon meg egy nevet, például a *peremhálózati átjárót* az eszköz sablonja számára.

1. A varázsló **Testreszabás** lapján keresse meg az **átjáró eszközt az alsóbb rétegbeli eszközökkel**.

1. A varázsló **Testreszabás** lapján válassza a **Tallózás** lehetőséget. Töltse fel a korábban letöltött fájl *EdgeTransparentGatewayManifest.jsét* .

1. Vegyen fel egy bejegyzést a **kapcsolatokban** az alsóbb rétegbeli eszköz sablonnal.

Az alábbi képernyőfelvételen egy olyan IoT Edge Gateway-eszköz **kapcsolatok** lapja látható, amely a **termosztát** -eszköz sablonját használó alsóbb rétegbeli eszközökkel rendelkezik:

:::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/device-template-relationship.png" alt-text="Képernyőfelvétel: IoT Edge Gateway-eszköz sablonjának kapcsolata egy termosztátos alsóbb rétegbeli eszköz sablonnal.":::

Az előző képernyőképen egy olyan IoT Edge átjáró-eszköz sablon látható, amelyen nincs definiálva modul. Az átlátszó átjárók nem igényelnek modulokat, mert az IoT Edge futtatókörnyezet továbbítja az üzeneteket az alsóbb rétegbeli eszközökről IoT Central. Ha az átjárónak telemetria kell küldenie, szinkronizálnia vagy kezelnie kell a parancsokat, megadhatja ezeket a képességeket az alapértelmezett összetevőben vagy egy modulban.

Az átjáró és az alsóbb rétegbeli eszközök sablonjainak közzététele előtt adja hozzá a szükséges Felhőbeli tulajdonságokat és nézeteket.

## <a name="add-the-devices"></a>Az eszközök hozzáadása

Amikor hozzáadja az eszközöket a IoT Central alkalmazáshoz, megadhatja az alárendelt eszközök és az átlátszó átjáró közötti kapcsolatot.

Az eszközök hozzáadása:

1. Navigáljon az eszközök lapra a IoT Central alkalmazásban.

1. Adja hozzá az átlátszó átjáró IoT Edge eszköz egy példányát. Ebben a cikkben az átjáró-eszköz azonosítója a következő: `edgegateway` .

1. Adja hozzá az alsóbb rétegbeli eszköz egy vagy több példányát. Ebben a cikkben az alárendelt eszközök az azonosítókkal és a-val rendelkező termosztátok `thermostat1` `thermostat2` .

1. Az eszközök listájában válassza ki az összes alsóbb rétegbeli eszközt, és válassza a **csatolás az átjáróhoz** lehetőséget.

Az alábbi képernyőfelvételen megtekintheti az átjáróhoz csatolt eszközök listáját az **alárendelt eszközök** oldalon:

:::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/downstream-devices.png" alt-text="Képernyőkép, amely egy transzparens átjáróhoz csatlakoztatott alsóbb rétegbeli eszközök listáját jeleníti meg.":::

Transzparens átjáró esetén az alsóbb rétegbeli eszközök magához az átjáróhoz csatlakoznak, nem pedig az átjáró által üzemeltetett egyéni modulhoz.

Az eszközök üzembe helyezése előtt a következőkre lesz szüksége:

- A IoT Central alkalmazás **azonosító hatóköre** .
- Az átjáró és az alsóbb rétegbeli eszközök **eszköz-azonosító** értékei.
- Az átjáró és az alsóbb rétegbeli eszközök **elsődleges kulcsának** értékei.

Ezeknek az értékeknek a megkereséséhez navigáljon az eszközök listájában található minden eszközre, és válassza a **Kapcsolódás** lehetőséget. A folytatás előtt jegyezze fel ezeket az értékeket.

## <a name="deploy-the-gateway-and-devices"></a>Az átjáró és az eszközök üzembe helyezése

A forgatókönyv kipróbálásához a következő lépések bemutatják, hogyan helyezheti üzembe az átjárót és az alárendelt eszközöket az Azure Virtual Machines szolgáltatásban. Valós esetben az alsóbb rétegbeli eszköz és az átjáró a helyi hálózaton található fizikai eszközökön fut.

Az átlátszó átjáró kipróbálható forgatókönyvének kiválasztásához válassza a következő gombot két linuxos virtuális gép üzembe helyezéséhez. Az egyik virtuális gép egy transzparens IoT Edge átjáró, a másik egy olyan alsóbb rétegbeli eszköz, amely egy termosztátot szimulál:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fiot-central-docs-samples%2Fmaster%2Ftransparent-gateway%2FDeployGatewayVMs.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png" alt="Deploy to Azure button" />
</a>

A két virtuális gép üzembe helyezése és futtatása után ellenőrizze, hogy fut-e a IoT Edge Gateway-eszköz a `edgegateway` virtuális gépen:

1. Nyissa meg az **eszközök** lapot a IoT Central alkalmazásban. Ha a IoT Edge átjáró-eszköz csatlakozik a IoT Centralhoz, annak állapota **kiépítve**.

1. Nyissa meg az IoT Edge átjáró eszközt, és ellenőrizze a modulok állapotát a **modulok** lapon. Ha az IoT Edge futtatókörnyezet sikeresen elindult, a **$edgeAgent** és **$edgeHub** modulok állapota a következőket **futtatja**:

    :::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/iot-edge-runtime.png" alt-text="Képernyőfelvétel: az IoT Edge-átjárón futó $edgeAgent és $edgeHub modulok.":::

> [!TIP]
> Előfordulhat, hogy néhány percet várnia kell, amíg a virtuális gép elindul, és az eszköz a IoT Central alkalmazásban van kiépítve.

## <a name="configure-the-gateway"></a>Az átjáró konfigurálása

Ahhoz, hogy a IoT Edge eszköz transzparens átjáróként működjön, bizonyos tanúsítványokra van szüksége, hogy igazolja az összes alsóbb rétegbeli eszköz személyazonosságát. Ez a cikk bemutató tanúsítványokat használ. Éles környezetben használjon tanúsítványokat a hitelesítésszolgáltatótól.

A bemutató tanúsítványainak előállítása és telepítése az átjáró-eszközön:

1. Az SSH használatával csatlakozzon az átjáró-eszköz virtuális gépén, és jelentkezzen be.

1. Futtassa az alábbi parancsokat a IoT Edge adattár klónozásához és a bemutató tanúsítványok létrehozásához:

    ```bash
    # Clone the repo
    cd ~
    git clone https://github.com/Azure/iotedge.git

    # Generate the demo certificates
    mkdir certs
    cd certs
    cp ~/iotedge/tools/CACertificates/*.cnf .
    cp ~/iotedge/tools/CACertificates/certGen.sh .
    ./certGen.sh create_root_and_intermediate
    ./certGen.sh create_edge_device_ca_certificate "mycacert"
    ```

    Az előző parancsok futtatása után az alábbi fájlok készen állnak a használatra a következő lépésekben:

    - *~/certs/certs/Azure-IOT-test-only.root.ca.CERT.PEM* – az összes többi bemutató tanúsítványának tesztelésére használt legfelső szintű hitelesítésszolgáltatói tanúsítvány IoT Edge-forgatókönyv vizsgálatához.
    - *~/certs/certs/IOT-Edge-Device-mycacert-Full-Chain.CERT.PEM* – a *config. YAML* fájlból hivatkozott eszköz hitelesítésszolgáltatói tanúsítvány. Átjáró-forgatókönyv esetén ez a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány azt ismerteti, hogy a IoT Edge eszköz hogyan ellenőrzi az identitását az alárendelt eszközökre.
    - *~/certs/Private/IOT-Edge-Device-mycacert.key.PEM* – az eszköz hitelesítésszolgáltatói tanúsítványához társított titkos kulcs.

    Ha többet szeretne megtudni ezekről a bemutató-tanúsítványokról, tekintse meg [a bemutató tanúsítványok létrehozása IoT Edge eszköz funkcióinak teszteléséhez](../../iot-edge/how-to-create-test-certificates.md)című témakört.

1. Nyissa meg a *config. YAML* fájlt egy szövegszerkesztőben. Például:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Keresse meg a `Certificate settings` beállításokat. A következő lépésekkel tegye közzé megjegyzéseit, és módosítsa a tanúsítvány beállításait:

    ```text
    certificates:
      device_ca_cert: "file:///home/AzureUser/certs/certs/iot-edge-device-ca-mycacert-full-chain.cert.pem"
      device_ca_pk: "file:///home/AzureUser/certs/private/iot-edge-device-ca-mycacert.key.pem"
      trusted_ca_certs: "file:///home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem"
    ```

    A fenti példa azt feltételezi, hogy Ön bejelentkezett az **azureuser** néven, és létrehozott egy "mycacert" nevű hitelesítésszolgáltatói tanúsítványt.

1. Mentse a módosításokat, és indítsa újra a IoT Edge futtatókörnyezetet:

    ```bash
    sudo systemctl restart iotedge
    ```

Ha az IoT Edge futtatókörnyezet sikeresen elindul a módosítások után, a **$edgeAgent** és a **$edgeHub** modulok állapota a IoT Centralban az átjáróhoz tartozó **modulok** lapon **futó** eszközökre változik.

Ha a futtatókörnyezet nem indul el, ellenőrizze a *config. YAML fájlban* végrehajtott módosításokat, és tekintse [meg a IoT Edge eszköz hibáinak hibakeresését](../../iot-edge/troubleshoot.md)ismertető témakört.

Az transzparens átjáró konfigurálva van, és készen áll arra, hogy elindítsa a telemetria továbbítását az alárendelt eszközökről.

## <a name="provision-a-downstream-device"></a>Alsóbb rétegbeli eszköz kiépítése

A IoT Edge jelenleg nem tud automatikusan kiépíteni egy alsóbb rétegbeli eszközt a IoT Central alkalmazásba. Az alábbi lépések bemutatják, hogyan építheti ki az `thermostat1` eszközt. A lépések elvégzéséhez szükség van egy, a Python 3,6 (vagy újabb) rendszerű környezetre, amely az internetkapcsolatot is telepíti. A [Azure Cloud Shell](https://shell.azure.com/) a Python 3,7 előre telepítve van:

1. Futtassa a következő parancsot a modul telepítéséhez `azure.iot.device` :

    ```bash
    pip install azure.iot.device
    ```

1. Futtassa a következő parancsot a kiépítés alatt álló Python-szkript letöltéséhez:

    ```bash
    wget https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/provision_device.py
    ```

1. Ha az `thermostat1` alsóbb rétegbeli eszközt a IoT Central alkalmazásban szeretné kiépíteni, futtassa a következő parancsokat, és cserélje le a parancsot `{your application id scope}` `{your device primary key}`  :

    ```bash
    export IOTHUB_DEVICE_DPS_DEVICE_ID=thermostat1
    export IOTHUB_DEVICE_DPS_ID_SCOPE={your application id scope}
    export IOTHUB_DEVICE_DPS_DEVICE_KEY={your device primary key}
    python provision_device.py
    ```

A IoT Central alkalmazásban ellenőrizze, hogy a thermostat1 eszköz **állapotának** **kiépítve** van-e. 

## <a name="configure-a-downstream-device"></a>Alsóbb rétegbeli eszköz konfigurálása

Az előző szakaszban a `edgegateway` virtuális gépet a bemutató tanúsítványokkal konfigurálta, hogy az átjáróként fusson. A `leafdevice` virtuális gép készen áll arra, hogy olyan termosztát-szimulátort telepítsen, amely az átjáró használatával csatlakozik IoT Centralhoz.

A `leafdevice` virtuális gépnek szüksége van a virtuális gépen létrehozott legfelső szintű hitelesítésszolgáltatói tanúsítvány másolatára `edgegateway` . Másolja a */Home/AzureUser/certs/certs/Azure-IOT-test-only.root.ca.CERT.PEM* -fájlt a `edgegateway` virtuális gépről a `leafdevice` virtuális gép saját könyvtárába. A **szolgáltatáskapcsolódási** pont parancs használatával fájlokat másolhat egy linuxos virtuális gépre, illetve onnan is.

A következő témakörből megtudhatja, hogyan ellenőrizheti a kapcsolódást az alsóbb rétegbeli eszközről az átjáróra: [az átjáró-kapcsolatok tesztelése](../../iot-edge/how-to-connect-downstream-device.md#test-the-gateway-connection).

A termosztát szimulátor futtatása a `leafdevice` virtuális gépen:

1. Töltse le a Python-mintát a saját könyvtárába:

    ```bash
    cd ~
    wget https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/simple_thermostat.py
    ```

1. Telepítse az Azure IoT-eszköz Python-modulját:

    ```bash
    sudo apt update
    sudo apt install python3-pip
    pip3 install azure.iot.device
    ```

1. Állítsa be a környezeti változókat a minta konfigurálásához. A helyére írja `{your device shared key}` `thermostat1` be a korábban létrehozott jegyzet elsődleges kulcsát. Ezek a változók feltételezik az átjáró virtuális gép nevét `edgegateway` és a termosztát eszköz azonosítóját `thermostat1` :

    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    export IOTHUB_DEVICE_CONNECTION_STRING="HostName=edgegateway;DeviceId=thermostat1;SharedAccessKey={your device shared key}"
    export IOTEDGE_ROOT_CA_CERT_PATH=~/azure-iot-test-only.root.ca.cert.pem
    ```

    Figyelje meg, hogy a kapcsolatok karakterlánca az átjáró-eszköz nevét használja, nem az IoT hub nevét.

1. A kód futtatásához használja a következő parancsot:

    ```bash
    python3 simple_thermostat.py
    ```

    A parancs kimenete a következőképpen néz ki:

    ```output
    Connecting using Connection String HostName=edgegateway;DeviceId=thermostat1;SharedAccessKey={your device shared key}
    Listening for command requests and property updates
    Press Q to quit
    Sending telemetry for temperature
    Sent message
    Sent message
    Sent message
    ...
    ```

1. A IoT Central telemetria megtekintéséhez navigáljon a **thermostat1** eszköz **Áttekintés** lapjára:

    :::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/downstream-device-telemetry.png" alt-text="Képernyőkép, amely az alsóbb rétegbeli eszköz telemetria mutatja.":::

    A **Névjegy** lapon megtekintheti az alsóbb rétegbeli eszközről eljuttatott tulajdonságértékeket, a **parancs** lapon pedig parancsokat hívhat meg az alsóbb rétegbeli eszközön.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan konfigurálhat egy transzparens átjárót IoT Centralval, a javasolt következő lépés a [IoT Edge](../../iot-edge/about-iot-edge.md)megismerése.

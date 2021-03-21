---
title: Oktatóanyag – video Analytics IoT Edge-példány létrehozása az Azure IoT Central (Intel NUC)
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy video Analytics IoT Edge-példányt a video Analytics-Object és a Motion Detection alkalmazás sablonnal.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/27/2020
ms.openlocfilehash: 64cdb41540d9750be8664dc60c2b6ceda6c324ca
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99831926"
---
# <a name="tutorial-create-an-iot-edge-instance-for-video-analytics-intel-nuc"></a>Oktatóanyag: IoT Edge-példány létrehozása a video analyticshez (Intel NUC)

A Azure IoT Edge egy teljes körűen felügyelt szolgáltatás, amely helyileg biztosítja a felhőalapú intelligenciát az üzembe helyezés és a Futtatás során:

* Egyéni logika
* Azure-szolgáltatások
* Mesterséges intelligencia

IoT Edge ezek a szolgáltatások közvetlenül futnak a platformfüggetlen IoT-eszközökön, így a IoT-megoldás biztonságosan és méretezhetően futtatható a felhőben vagy offline.

Ebből az oktatóanyagból megtudhatja, hogyan telepítheti és konfigurálhatja a IoT Edge futtatókörnyezetet egy Intel NUC-eszközön.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * IoT Edge frissítése és konfigurálása
> * Az IoT Edge-átjáró beállítása
> * Helyi ONVIF-kompatibilis kamera csatlakoztatása Intel NUC-eszközhöz

## <a name="prerequisites"></a>Előfeltételek

* Mielőtt elkezdené, végezze el az előző [Live Video Analytics-alkalmazás létrehozása az azure IoT Centralban (YOLO v3)](./tutorial-video-analytics-create-app-yolo-v3.md) , vagy [hozzon létre egy videó-elemzést az Azure IoT Central (OpenVINO &trade; )](tutorial-video-analytics-create-app-openvino.md)oktatóanyagban.
* Egy olyan eszköz, például egy Linux rendszerű Intel-NUC, amely Docker-tárolókat képes futtatni, és elegendő feldolgozási teljesítménnyel rendelkezik a videó-elemzések futtatásához.
* Az eszközön telepített és futó [IoT Edge futtatókörnyezet](../../iot-edge/how-to-install-iot-edge.md) .
* Képesnek kell lennie csatlakozni a IoT Edge eszközre a Windows rendszerű gépről, szüksége lesz a [PUTTY SSH-ügyfélre](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) vagy egy ezzel egyenértékű eszközre.
* Szüksége lesz egy Azure-előfizetésre is. Ha nem rendelkezik Azure-előfizetéssel, az [Azure regisztrációs oldalán](https://aka.ms/createazuresubscription)ingyenesen létrehozhat egyet.

## <a name="configure-the-iot-edge-device"></a>Az IoT Edge-eszköz konfigurálása

Ha nem rendelkezik az Intel NUC-gépen telepített IoT Edge futtatókörnyezettel, olvassa el [a Azure IoT Edge futtatókörnyezet telepítése Debian-alapú Linux rendszerekre](../../iot-edge/how-to-install-iot-edge.md) vonatkozó utasításokat.

A IoT Edge futtatókörnyezet frissítése:

1. A PuTTY segédprogram használatával csatlakozhat a IoT Edge eszközhöz.

1. Futtassa az alábbi parancsokat a IoT Edge futtatókörnyezet verziójának frissítéséhez és a verziószámának vizsgálatához. Az írás időpontjában a verzió 1.0.9:

    ```bash
    sudo apt-get update
    sudo apt-get install libiothsm iotedge
    sudo iotedge --version
    ```

1. A következő parancsokkal hozza létre azokat a mappákat, amelyeket a központi telepítés a szükséges engedélyekkel használ:

    ```bash
    sudo mkdir -p /data/storage
    sudo mkdir -p /data/media
    sudo chmod -R 777 /data
    ```

Adja hozzá a *state.jsa* konfigurációs fájlhoz a IoT Edge eszköz */Data/Storage* mappájába:

1. Egy szövegszerkesztővel nyissa meg a *LVA-Configuration* mappában található *state.js* fájlt a helyi gépen.

1. Frissítse a `system` és a `iotCentral > properties` helyőrzőket az átjáró eszközét leíró karakterlánc-értékekkel. Ezeket az értékeket később is megtekintheti a IoT Central alkalmazás irányítópultján.

1. Frissítse a `iotCentral > appKeys` helyőrzőket azokra az értékekre, amelyeket az előző oktatóanyag *scratchpad.txt* fájljában jegyzett készített. Mentse a módosításokat.

1. A PuTTY `scp` segédprogramot a parancssorban használva másolja át a *state.jst* az imént a IoT Edge eszközön */Data/Storage* mappába szerkesztett fájlba. Ez a példa `192.168.0.144` példaként használt IP-címet használ, és lecseréli a IoT Edge eszközének IP-címére:

    ```cmd
    scp state.json YourUserName@192.168.0.144:/data/storage/state.json`
    ```

Konfigurálja IoT Edge a IoT Central alkalmazás regisztrálásához és a hozzá való kapcsolódáshoz:

1. A PuTTY segédprogram használatával csatlakozhat a IoT Edge eszközhöz.

1. A `nano` IoT Edge config. YAML fájl megnyitásához használjon szövegszerkesztőt, például:.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

    > [!WARNING]
    > A YAML-fájlok nem használhatják a tabulátorokat a behúzáshoz, hanem két szóközt is használhatnak. A legfelső szintű elemek nem rendelkezhetnek kezdő szóközökkel.

1. Görgessen lefelé, amíg meg nem jelenik `# Manual provisioning configuration` . Tegye megjegyzésbe a következő három sort az alábbi kódrészletben látható módon:

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "temp"
    ```

1. Görgessen lefelé, amíg meg nem jelenik `# DPS symmetric key provisioning configuration` . A következő nyolc sor megjegyzésének visszalépése az alábbi kódrészletben látható módon:

    ```yaml
    # DPS symmetric key provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "{scope_id}"
      attestation:
        method: "symmetric_key"
        registration_id: "{registration_id}"
        symmetric_key: "{symmetric_key}"
    ```

1. A helyére írja be az `{scope_id}` **azonosító hatókört** , amelyet az előző oktatóanyag *scratchpad.txt* fájljában jegyzett készített.

1. Cserélje le az `{registration_id}` *átjáró-001*, az előző oktatóanyagban létrehozott eszközt.

1. A helyére írja `{symmetric_key}` be az **átjáró-001** eszköz **elsődleges kulcsát** , és jegyezze fel az előző oktatóanyag *scratchpad.txt* fájljában.

1. Futtassa a következő parancsot a IoT Edge démon újraindításához:

    ```bash
    sudo systemctl restart iotedge
    ```

1. A IoT Edge modulok állapotának megtekintéséhez futtassa a következő parancsot:

    ```bash
    iotedge list
    ```

    A áteresztő parancs kimenete öt futó modult mutat be. A futó modulok állapotát a IoT Central alkalmazásban is megtekintheti.

    > [!TIP]
    > A parancs futtatásával megtekintheti az állapotot. Előfordulhat, hogy várnia kell az összes modul futtatásának megkezdéséhez.

Ha a IoT Edge modulok nem indulnak el megfelelően, tekintse [meg a IoT Edge-eszköz hibáinak megoldása](../../iot-edge/troubleshoot.md)című témakört.

## <a name="collect-the-rtsp-stream-from-your-camera"></a>Az RTSP stream begyűjtése a kamerából

Azonosítsa a IoT Edge eszközhöz csatlakoztatott fényképezőgépek RTSP stream URL-címeit, például:

`rtsp://192.168.1.64:554/Streaming/Channels/101/`

> [!TIP]
> Próbálja meg megtekinteni a kamera streamjét a IoT Edge számítógépen egy médialejátszó, például a VLC használatával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett az alkalmazással, a következőképpen távolíthatja el az összes létrehozott erőforrást:

1. A IoT Central alkalmazásban navigáljon az **alkalmazás** lapjára az **Adminisztráció** szakaszban. Ezután válassza a **Törlés** elemet.
1. A Azure Portal törölje a **LVA-RG** erőforráscsoportot.
1. A helyi gépen állítsa le az **amp-Viewer** Docker-tárolót.

## <a name="next-steps"></a>Következő lépések

Most telepítette az IoT Edge futtatókörnyezetet és a LVA modulokat az Intel NUC Gateway-eszközre.

A kamerák kezeléséhez kövesse a következő oktatóanyagot:

> [!div class="nextstepaction"]
> [Video Analytics-objektum-és mozgásészlelési alkalmazás monitorozása és kezelése](./tutorial-video-analytics-manage.md)
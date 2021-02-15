---
title: Oktatóanyag – IoT Edge-eszközök hierarchiájának létrehozása – Azure IoT Edge
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre IoT Edge-eszközök hierarchikus struktúráját átjárók használatával.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 11/10/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: a7f82ec5a4ef918b1bc7ab0fd6813199c0a1d772
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100366392"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices-preview"></a>Oktatóanyag: IoT Edge-eszközök hierarchiájának létrehozása (előzetes verzió)

Azure IoT Edge csomópontok központi telepítése hierarchikus rétegekben rendezett hálózatok között. A hierarchia minden rétege egy átjáró-eszköz, amely az alatta lévő rétegbeli eszközök üzeneteit és kéréseit kezeli.

>[!NOTE]
>Ehhez a szolgáltatáshoz a IoT Edge 1,2-es verziója szükséges, amely nyilvános előzetes verzióban, Linux-tárolókat futtat.

Az eszközök hierarchiáját strukturálhatja úgy, hogy csak a legfelső réteg létesítsen kapcsolatot a felhővel, és az alsó réteg csak a szomszédos Észak-és Dél-rétegekkel tud kommunikálni. Ez a hálózati réteg a legtöbb ipari hálózat alapja, amely az [ISA-95 szabványt](https://en.wikipedia.org/wiki/ANSI/ISA-95)követi.

Ennek az oktatóanyagnak a célja, hogy IoT Edge-eszközök hierarchiáját hozza létre, amely egy éles környezetet szimulál. A rendszer a [szimulált hőmérséklet-érzékelő modult](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.simulated-temperature-sensor) az Internet-hozzáférés nélküli alsóbb rétegbeli eszközre helyezi át a tároló lemezképének a hierarchián keresztüli letöltésével.

A cél megvalósítása érdekében ez az oktatóanyag végigvezeti IoT Edge eszközök hierarchiájának létrehozásán, IoT Edge futtatókörnyezeti tárolók üzembe helyezésén az eszközökön, valamint az eszközök helyi konfigurálásán. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * IoT Edge-eszközök hierarchiájában hozza létre és határozza meg a kapcsolatokat.
> * Konfigurálja a IoT Edge futtatókörnyezetet a hierarchiában lévő eszközökön.
> * Konzisztens tanúsítványokat telepíthet az eszköz-hierarchiába.
> * Munkaterhelések hozzáadása a hierarchiában lévő eszközökhöz.
> * Egy API-proxy modul használatával biztonságosan irányíthatók át a HTTP-forgalom az alsóbb rétegbeli eszközökről származó egyetlen porton keresztül.

Ebben az oktatóanyagban a következő hálózati rétegek vannak definiálva:

* **Legfelső réteg**: IoT Edge a rétegben lévő eszközök közvetlenül kapcsolódhatnak a felhőhöz.

* **Alsó réteg**: IoT Edge eszközök ebben a rétegben nem csatlakoztathatók közvetlenül a felhőhöz. Egy vagy több közvetítő IoT Edge eszközön kell átesniük az adatküldésre és fogadásra.

Ez az oktatóanyag a két eszköz hierarchiáját használja az egyszerűség kedvéért. Egy eszköz, a **topLayerDevice**, a hierarchia legfelső szintjén található eszközt jelöli, amely közvetlenül kapcsolódhat a felhőhöz. Ez az eszköz **szülő eszközként** is hivatkozni fog. A másik eszköz, a **lowerLayerDevice**, a hierarchia alsó rétegében lévő eszközt jelöli, amely nem tud közvetlenül kapcsolódni a felhőhöz. Ezt az eszközt a rendszer **alárendelt eszközként** is említi. Az éles környezetnek megfelelő további alsóbb rétegbeli eszközöket adhat hozzá. A további alsóbb rétegbeli eszközök konfigurációja a **lowerLayerDevice** konfigurációját fogja követni.

## <a name="prerequisites"></a>Előfeltételek

IoT Edge eszközök hierarchiájának létrehozásához a következőkre lesz szüksége:

* Internetkapcsolattal rendelkező számítógép (Windows vagy Linux).
* Egy érvényes előfizetéssel rendelkező Azure-fiók. Ha nem rendelkezik Azure- [előfizetéssel](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .
* Ingyenes vagy standard szintű [IoT hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* Azure CLI v 2.3.1 Az Azure IoT Extension v 0.10.6 vagy újabb verziójával. Ez az oktatóanyag a [Azure Cloud Shell](../cloud-shell/overview.md)használja. Ha nem ismeri a Azure Cloud Shell, a [részletekért tekintse meg a](./quickstart-linux.md#prerequisites)rövid útmutatót.
* Két linuxos eszköz IoT Edge eszközként való konfiguráláshoz. Ha nem érhető el eszközök, két Azure-beli virtuális gépet hozhat létre a helyőrző szövegének a következő parancsban való lecserélésével, és kétszer futtathatja:

   ```azurecli-interactive
   az vm create \
    --resource-group <REPLACE_WITH_RESOURCE_GROUP> \
    --name <REPLACE_WITH_UNIQUE_NAMES_FOR_EACH_VM> \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password <REPLACE_WITH_PASSWORD>
   ```

* Győződjön meg arról, hogy a következő portok nyitva vannak: 8000, 443, 5671, 8883:
  * 8000: a Docker-tárolók rendszerképeinek az API-proxyn keresztüli lekérésére használatos.
  * 443: a szülő és a gyermek peremhálózati hubok között használatos REST API hívásokhoz.
  * 5671, 8883: AMQP és MQTT használatos.

  További információért olvassa el [a portok a virtuális gépek számára az Azure Portallal való megnyitását](../virtual-machines/windows/nsg-quickstart-portal.md) ismertető cikket.

Ezt a forgatókönyvet úgy is kipróbálhatja, hogy a megírt [Azure IoT Edge az ipari IoT minta](https://aka.ms/iotedge-nested-sample)alapján, amely az Azure-beli virtuális gépeket előre konfigurált eszközökként helyezi üzembe a gyári környezet szimulálása érdekében.

## <a name="configure-your-iot-edge-device-hierarchy"></a>Az IoT Edge-eszköz hierarchiájának konfigurálása

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>IoT Edge-eszközök hierarchiájának létrehozása

Az IoT Edge-eszközök létrehozásának első lépése a Azure Portal vagy az Azure CLI használatával végezhető el. Ez az oktatóanyag két IoT Edge-eszköz hierarchiáját hozza létre: a **topLayerDevice** -t és annak gyermek- **lowerLayerDevice**.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A [Azure Portal](https://ms.portal.azure.com/)navigáljon a IoT hub.

1. A bal oldali ablaktábla menüjének **automatikus eszközkezelés** területén válassza a **IoT Edge** lehetőséget.

1. Válassza **a + IoT Edge eszköz hozzáadása** elemet. Ez az eszköz lesz a legfelső rétegbeli eszköz, ezért adjon meg egy megfelelő egyedi azonosítót. Kattintson a **Mentés** gombra.

1. Válassza **az + IoT Edge eszköz hozzáadása** elemet. Ez az eszköz lesz a peremhálózati alsóbb rétegbeli eszköz, ezért adjon meg egy megfelelő egyedi azonosítót.

1. Válassza a **fölérendelt eszköz beállítása** lehetőséget, válassza ki a legfelső rétegbeli eszközt az eszközök listájából, majd kattintson **az OK gombra**. Kattintson a **Mentés** gombra.

   ![A szülő beállítása az alsó rétegbeli eszközhöz](./media/tutorial-nested-iot-edge/set-parent-device.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. A [Azure Cloud Shell](https://shell.azure.com/)írja be a következő parancsot egy IoT Edge-eszköz létrehozásához a központban. Ez az eszköz lesz a legfelső rétegbeli eszköz, ezért adjon meg egy megfelelő egyedi eszközt:

   ```azurecli-interactive
   az iot hub device-identity create --device-id {top_layer_device_id} --edge-enabled --hub-name {hub_name}
   ```

1. Adja meg a következő parancsot a gyermek IoT Edge eszköz létrehozásához és az eszközök közötti szülő-gyermek kapcsolat létrehozásához:

    ```azurecli-interactive
    az iot hub device-identity create --device-id {lower_layer_device_id} --edge-enabled --pd {top_layer_device_id} --hub-name {iothub_name}
    ```

---

Jegyezze fel az egyes IoT Edge eszközökhöz tartozó kapcsolatok sztringjét. Később lesznek használatban.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A [Azure Portal](https://ms.portal.azure.com/)navigáljon a IoT hub **IoT Edge** szakaszához.

1. Kattintson az eszközök listájában található egyik eszköz AZONOSÍTÓJÁRA.

1. Válassza a **Másolás** lehetőséget az **elsődleges kapcsolatok karakterlánca** mezőben, és jegyezze fel a kívánt helyre.

1. Ismételje meg az összes többi eszközt.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. A [Azure Cloud Shell](https://shell.azure.com/)minden eszköznél adja meg a következő parancsot az eszközhöz tartozó kapcsolódási karakterlánc lekéréséhez, és jegyezze fel a kívánt helyre:

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id {device_id} --hub-name {hub_name}
   ```

---

### <a name="create-certificates"></a>Tanúsítványok létrehozása

Az [átjáró-forgatókönyvben](iot-edge-as-gateway.md) szereplő összes eszköznek megosztott tanúsítványra van szüksége a közöttük lévő biztonságos kapcsolatok beállításához. A következő lépésekkel hozhat létre bemutató tanúsítványokat mindkét eszközhöz ebben a forgatókönyvben.

Ha Linux rendszerű eszközön szeretne bemutató-tanúsítványokat létrehozni, a létrehozási parancsfájlokat meg kell adni, és azokat a bash-ben helyileg kell futtatni.

1. A IoT Edge git-tárház klónozása, amely parancsfájlokat tartalmaz a bemutató tanúsítványok létrehozásához.

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

1. Navigáljon ahhoz a címtárhoz, amelyben dolgozni szeretne. Az összes tanúsítvány-és kulcsfájl ebben a könyvtárban lesz létrehozva.

1. Másolja a config és a script fájlokat a klónozott IoT Edge-tárházból a munkakönyvtárba.

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

1. Hozza létre a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt és egy köztes tanúsítványt.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Ez a parancsfájl több tanúsítványt és kulcsot hoz létre, de a következő fájlt használja az átjáró-hierarchia **legfelső szintű hitelesítésszolgáltatói tanúsítványának** :

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

1. Hozzon létre két IoT Edge-eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványát és titkos kulcsát a következő paranccsal: egy készletet a felső rétegbeli eszközhöz, és egy készletet az alsó rétegbeli eszközhöz. Adjon meg egy emlékezetes nevet a HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok számára, hogy megkülönböztesse őket egymástól.

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "top-layer-device"
   ./certGen.sh create_edge_device_ca_certificate "lower-layer-device"
   ```

   Ez a parancsfájl több tanúsítványt és kulcsot hoz létre, de a következő tanúsítványt és kulcspárt használjuk minden IoT Edge eszközön, és a config. YAML fájlban hivatkozunk rá:

   * `<WRKDIR>/certs/iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<CA cert name>.key.pem`

Minden eszköznek szüksége van a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány másolatára és a saját eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványának és titkos kulcsának egy másolatára. A tanúsítványok minden eszközre való áthelyezéséhez USB-meghajtó vagy [biztonságos fájlmásolás](https://www.ssh.com/ssh/scp/) is használható.

1. A tanúsítványok átvitele után telepítse a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓT minden eszközhöz.

   ```bash
   sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```

   A parancs kimenete azt eredményezi, hogy egy tanúsítvány hozzá lett adva a/etc/SSL/certs.-ben

### <a name="install-iot-edge-on-the-devices"></a>IoT Edge telepítése az eszközökön

A IoT Edge telepítéséhez mindkét eszközön végezze el a következő lépéseket.

1. Telepítse az eszköz operációs rendszerének megfelelő adattár-konfigurációt.

   * **Ubuntu Server 18,04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Málna PI operációs rendszer stretch**:

     ```bash
     curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
     ```

1. Másolja a generált listát a sources. list. d könyvtárba.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

1. Telepítse a Microsoft GPG nyilvános kulcsot.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```
   
1. Frissítési csomagok listája az eszközön.

   ```bash
   sudo apt-get update
   ```

1. Telepítse a Moby Engine-t.

   ```bash
   sudo apt-get install moby-engine
   ```

1. Telepítse a hsmlib és a IoT Edge démont. A más Linux-disztribúciók eszközeinek megtekintéséhez [látogasson el a GitHub-kiadásra](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1). <!-- Update with proper image links on release -->

   ```bash
   curl -L https://github.com/Azure/azure-iotedge/releases/download/1.2.0-rc1/libiothsm-std_1.2.0_rc1-1-1_debian9_amd64.deb -o libiothsm-std.deb
   curl -L https://github.com/Azure/azure-iotedge/releases/download/1.2.0-rc1/iotedge_1.2.0_rc1-1_debian9_amd64.deb -o iotedge.deb
   sudo dpkg -i ./libiothsm-std.deb
   sudo dpkg -i ./iotedge.deb
   ```

### <a name="configure-the-iot-edge-runtime"></a>Az IoT Edge-futtatókörnyezet konfigurálása

Konfigurálja a IoT Edge futtatókörnyezetet a következő lépésekkel mindkét eszközön. Az eszközök IoT Edge futtatókörnyezetének konfigurálása négy lépésből áll, amelyek mindegyike a IoT Edge konfigurációs fájl szerkesztésével valósítható meg:

1. Hozzon létre manuálisan minden eszközt úgy, hogy hozzáadja az eszközhöz tartozó kapcsolódási karakterláncot a konfigurációs fájlhoz.

1. Fejezze be az eszköz tanúsítványait úgy, hogy a konfigurációs fájlt az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa, az eszköz HITELESÍTÉSSZOLGÁLTATÓI titkos kulcsa és a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány alapján állítja be.

1. Rendszerindító a IoT Edge ügynök használatával.

1. Frissítse az **állomásnév** paramétert a **felső rétegbeli** eszközhöz, és frissítse a **hostname** paramétert és a **parent_hostname** paramétert az **alsóbb rétegbeli** eszközökhöz.

Hajtsa végre ezeket a lépéseket, majd indítsa újra a IoT Edge szolgáltatást az eszközök konfigurálásához.

1. Minden eszközön nyissa meg a IoT Edge konfigurációs fájlt.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Keresse meg a fájl kiépítési konfigurációit, és a **manuális üzembe helyezési konfigurációt a kapcsolatok karakterlánc szakasz használatával** írja vissza.

1. **Device_connection_string** értékének frissítése a IoT Edge eszközhöz tartozó kapcsolatok karakterláncával. Ügyeljen rá, hogy a többi kiépítési szakaszt is jegyezze fel. Győződjön meg arról, hogy a **kiépítés:** sor nem tartalmaz korábbi szóközt, és a beágyazott elemek két szóközzel vannak behúzva.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
     dynamic_reprovisioning: false
   ```

   >[!TIP]
   >A vágólap tartalmának a Nanoba vagy a sajtóba való beillesztéséhez `Shift+Right Click` `Shift+Insert` .

1. Keresse meg a **tanúsítványok** szakaszt. Az előző szakaszban létrehozott és a IoT Edge eszközre áthelyezett tanúsítványokra való Rámutatás után a három tanúsítvány mezőjét írja le és frissítse. Adja meg a fájl URI elérési útját, amely a formátumot használja `file:///<path>/<filename>` .

   ```yml
   certificates:
     device_ca_cert: "<File URI path to the device CA certificate unique to this device.>"
     device_ca_pk: "<File URI path to the device CA private key unique to this device.>"
     trusted_ca_certs: "<File URI path to the root CA certificate shared by all devices in the gateway hierarchy.>"
   ```

1. A **felső rétegbeli** eszköznél keresse meg a **hostname** paramétert. Frissítse az értéket a IoT Edge eszköz teljes tartománynevére (FQDN) vagy IP-címére. Tetszőleges értéket használhat a hierarchiában lévő eszközök között.

   ```yml
   hostname: <device fqdn or IP>
   ```

1. Az **alacsonyabb rétegekben** lévő IoT Edge-eszközök esetében frissítse a konfigurációs fájlt úgy, hogy az a szülő eszköz teljes tartománynevére vagy IP-címére mutasson, amely a szülő eszköz **állomásnév** mezőjében található. A **felső rétegben** lévő IoT Edge eszközöknél hagyja ki ezt a paramétert.

   ```yml
   parent_hostname: <parent device fqdn or IP>
   ```

   > [!NOTE]
   > Az egynél több alsóbb réteggel rendelkező hierarchiák esetében frissítse a *parent_hostname* mezőt az eszköz teljes tartománynevével, közvetlenül a fenti rétegben.

1. A **felső rétegbeli** eszköznél keresse meg az **ügynök** YAML szakaszt, és frissítse a rendszerkép értékét a IoT Edge ügynök megfelelő verziójára. Ebben az esetben a legfelső réteg IoT Edge ügynökét fogjuk a Azure Container Registry a IoT Edge Agent rendszerkép nyilvános előzetes verziójával.

   ```yml
   agent:
     name: "edgeAgent"
     type: "docker"
     env: {}
     config:
       image: "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2"
       auth: {}
   ```

1. Az **alacsonyabb rétegekben** lévő IoT Edge eszközök esetében frissítse a rendszerkép értékének tartománynevét úgy, hogy az a szülő eszköz teljes tartománynevére vagy IP-címére mutasson, majd a 8000-es API-proxy portját. A következő szakaszban hozzá kell adnia az API-proxy modult.

   ```yml
   agent:
     name: "edgeAgent"
     type: "docker"
     env: {}
     config:
       image: "<parent_device_fqdn_or_ip>:8000/azureiotedge-agent:1.2.0-rc2"
       auth: {}
   ```

1. Mentse és zárja be a fájlt.

   `CTRL + X`, `Y`, `Enter`

1. A konfigurációs fájlban szereplő kiépítési információk megadása után indítsa újra a démont:

   ```bash
   sudo systemctl restart iotedge
   ```

## <a name="deploy-modules-to-the-top-layer-device"></a>Modulok üzembe helyezése a legfelső rétegbeli eszközön

A IoT Edge futtatókörnyezet konfigurációjának befejezéséhez és a munkaterhelések üzembe helyezéséhez szükséges lépések a felhőből a Azure Portal vagy az Azure CLI használatával hajthatók végre.

# <a name="portal"></a>[Portál](#tab/azure-portal)

A [Azure Portalban](https://ms.portal.azure.com/):

1. Navigáljon a IoT Hub.

1. A bal oldali ablaktábla menüjének **automatikus eszközkezelés** területén válassza a **IoT Edge** lehetőséget.

1. Kattintson a **felső réteg** peremhálózati eszközének eszköz-azonosítójára az eszközök listájában.

1. A felső sávon válassza a **modulok beállítása** lehetőséget.

1. Válassza a **Futtatási beállítások** lehetőséget a fogaskerék ikon mellett.

1. Az **Edge hub** terület rendszerkép mezőjébe írja be a értéket `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc2` .

   ![Az Edge-központ rendszerképének szerkesztése](./media/tutorial-nested-iot-edge/edge-hub-image.png)

1. Adja hozzá az alábbi környezeti változókat az Edge hub-modulhoz:

    | Name | Érték |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

   ![Az Edge hub környezeti változóinak szerkesztése](./media/tutorial-nested-iot-edge/edge-hub-environment-variables.png)

1. Az **Edge-ügynök** területének rendszerkép mezőjébe írja be a értéket `mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2` . Kattintson a **Mentés** gombra.

1. Adja hozzá a Docker beállításjegyzék-modulját a felső rétegbeli eszközhöz. Válassza a **+ Hozzáadás** lehetőséget, majd válassza ki **IoT Edge modult** a legördülő menüből. Adja meg a `registry` Docker beállításjegyzék-moduljának nevét, és adja meg a `registry:latest` rendszerkép URI azonosítóját. Ezután adja hozzá a környezeti változókat, és hozzon létre beállításokat a helyi beállításjegyzék-modul a Microsoft Container registryben való letöltéséhez, hogy letöltse a lemezképeket a alkalmazásból, és a következő beállításjegyzékben szolgálja ki a képeket: 5000

1. A környezeti változók lapon adja meg a következő környezeti változó név-érték párokat:

    | Name | Érték |
    | - | - |
    | `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

1. A tároló létrehozása beállítások lapon adja meg a következő JSON-t:

   ```json
   {
    "HostConfig": {
        "PortBindings": {
            "5000/tcp": [
                {
                    "HostPort": "5000"
                }
            ]
         }
      }
   }
   ```

1. Ezután adja hozzá az API-proxy modult a felső rétegbeli eszközhöz. Válassza a **+ Hozzáadás** lehetőséget, majd válassza ki a **piactér modult** a legördülő menüből. Keresse meg `IoT Edge API Proxy` és válassza ki a modult. A IoT Edge API proxy a 8000-es portot használja, és úgy van konfigurálva, hogy az 5000-es porton található beállításjegyzék-modult használja `registry` alapértelmezettként.

1. Válassza a **felülvizsgálat + létrehozás**, majd a **Létrehozás** lehetőséget a telepítés befejezéséhez. A legfelső rétegbeli eszköz IoT Edge futtatókörnyezete, amely hozzáfér az internethez, lekéri és futtatja az IoT Edge hub és a IoT Edge ügynök **nyilvános előzetes** konfigurációit.

   ![Az Edge hub, Edge Agent, beállításjegyzék-modul és API proxy modult tartalmazó üzembe helyezés befejezése](./media/tutorial-nested-iot-edge/complete-top-layer-deployment.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. A [Azure Cloud Shell](https://shell.azure.com/)írja be a következő parancsot egy deployment.jslétrehozásához a fájlon:

   ```azurecli-interactive
   code deploymentTopLayer.json
   ```

1. Másolja az alábbi JSON tartalmát a deployment.jsba, és mentse, majd zárjuk be.

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "dockerContainerRegistry": {
                           "settings": {
                               "image": "registry:latest",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5000/tcp\":[{\"HostPort\":\"5000\"}]}}}"
                           },
                           "type": "docker",
                           "version": "1.0",
                           "env": {
                               "REGISTRY_PROXY_REMOTEURL": {
                                   "value": "https://mcr.microsoft.com"
                               } 
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       },
                       "IoTEdgeAPIProxy": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-api-proxy",
                               "createOptions": "{\"HostConfig\": {\"PortBindings\": {\"8000/tcp\": [{\"HostPort\":\"8000\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "NGINX_DEFAULT_PORT": {
                                   "value": "8000"
                               },
                               "DOCKER_REQUEST_ROUTE_ADDRESS": {
                                   "value": "registry:5000"
                               },
                               "BLOB_UPLOAD_ROUTE_ADDRESS": {
                                   "value": "AzureBlobStorageonIoTEdge:11002"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25"
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-hub:1.2.0-rc2",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "experimentalFeatures__enabled": {
                                   "value": "true"
                               },
                               "experimentalFeatures__nestedEdgeEnabled": {
                                   "value": "true"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

1. A következő parancs megadásával hozzon létre egy központi telepítést a legfelső rétegbeli peremhálózati eszközön:

   ```azurecli-interactive
   az iot edge set-modules --device-id <top_layer_device_id> --hub-name <iot_hub_name> --content ./deploymentTopLayer.json
   ```

---

## <a name="deploy-modules-to-the-lower-layer-device"></a>Modulok üzembe helyezése az alsó rétegbeli eszközön

Az Azure Portal és az Azure CLI használatával is üzembe helyezheti a számítási feladatokat a felhőből az **alsóbb rétegbeli** eszközökre.

# <a name="portal"></a>[Portál](#tab/azure-portal)

A [Azure Portalban](https://ms.portal.azure.com/):

1. Navigáljon a IoT Hub.

1. A bal oldali ablaktábla menüjének **automatikus eszközkezelés** területén válassza a **IoT Edge** lehetőséget.

1. Kattintson az alsó réteg eszközének AZONOSÍTÓJÁRA a IoT Edge eszközök listájában.

1. A felső sávon válassza a **modulok beállítása** lehetőséget.

1. Válassza a **Futtatási beállítások** lehetőséget a fogaskerék ikon mellett.

1. Az **Edge hub** terület rendszerkép mezőjébe írja be a értéket `$upstream:8000/azureiotedge-hub:1.2.0-rc2` .

1. Adja hozzá az alábbi környezeti változókat az Edge hub-modulhoz:

    | Name | Érték |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

1. Az **Edge-ügynök** területének rendszerkép mezőjébe írja be a értéket `$upstream:8000/azureiotedge-agent:1.2.0-rc2` . Kattintson a **Mentés** gombra.

1. Adja hozzá a hőmérséklet-érzékelő modult. Válassza a **+ Hozzáadás** lehetőséget, majd válassza ki a **piactér modult** a legördülő menüből. Keresse meg `Simulated Temperature Sensor` és válassza ki a modult.

1. A **IoT Edge modulok** területen válassza ki az `Simulated Temperature Sensor` imént hozzáadott modult, és frissítse a rendszerképének URI-ját, hogy erre mutasson `$upstream:8000/azureiotedge-simulated-temperature-sensor:1.0` .

1. A telepítés befejezéséhez válassza a **Mentés**, a **+ Létrehozás** és a **Létrehozás** lehetőséget.

   ![Az Edge hub, Edge Agent és szimulált hőmérséklet-érzékelőt tartalmazó üzembe helyezés befejezése](./media/tutorial-nested-iot-edge/complete-lower-layer-deployment.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. A [Azure Cloud Shell](https://shell.azure.com/)írja be a következő parancsot egy deployment.jslétrehozásához a fájlon:

   ```azurecli-interactive
   code deploymentLowerLayer.json
   ```

1. Másolja az alábbi JSON tartalmát a deployment.jsba, és mentse, majd zárjuk be.

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "simulatedTemperatureSensor": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-simulated-temperature-sensor:1.0",
                               "createOptions": ""
                           },
                           "type": "docker",
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25"
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-agent:1.2.0-rc2",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-hub:1.2.0-rc2",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "experimentalFeatures__enabled": {
                                   "value": "true"
                               },
                               "experimentalFeatures__nestedEdgeEnabled": {
                                   "value": "true"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

1. A következő parancs megadásával hozzon létre egy set modulok üzembe helyezését az alsó rétegbeli peremhálózati eszközön:

   ```azurecli-interactive
   az iot edge set-modules --device-id <lower_layer_device_id> --hub-name <iot_hub_name> --content ./deploymentLowerLayer.json

---

Notice that the image URI that we used for the simulated temperature sensor module pointed to `$upstream:8000` instead of to a container registry. We configured this device to not have direct connections to the cloud, because it's in a lower layer. To pull container images, this device requests the image from its parent device instead. At the top layer, the API proxy module routes this container request to the registry module, which handles the image pull.

On the device details page for your lower layer IoT Edge device, you should now see the temperature sensor module listed along the system modules as **Specified in deployment**. It may take a few minutes for the device to receive its new deployment, request the container image, and start the module. Refresh the page until you see the temperature sensor module listed as **Reported by device**.

## IotEdge check

Run the `iotedge check` command to verify the configuration and to troubleshoot errors.

You can run `iotedge check` in a nested hierarchy, even if the child machines don't have direct internet access.

When you run `iotedge check` from the lower layer, the program tries to pull the image from the parent through port 443.

In this tutorial, we use port 8000, so we need to specify it:

```bash
sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:8000/azureiotedge-diagnostics:1.2.0-rc2
```
   
Az `azureiotedge-diagnostics` értéket a rendszer a beállításjegyzék-modullal összekapcsolt tároló beállításjegyzékből kéri le. Ez az oktatóanyag alapértelmezés szerint a következőre van beállítva: https://mcr.microsoft.com:

| Name | Érték |
| - | - |
| `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

Ha privát tároló-beállításjegyzéket használ, győződjön meg arról, hogy az összes rendszerkép (például a IoTEdgeAPIProxy, a edgeAgent, a edgeHub és a Diagnostics) megtalálható a tároló beállításjegyzékében.    
    
## <a name="view-generated-data"></a>A létrejött adatok megtekintése

A **szimulált hőmérséklet-érzékelő** modul a minta környezeti adattípust hozza létre. A környezeti hőmérsékletet, a páratartalomot, a gépi hőmérsékletet és a nyomást, valamint egy időbélyeget tartalmazó üzeneteket küld.

A [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)-hoz készült Azure IoT hub-bővítmény használatával megtekintheti az IoT hub-ra érkező üzeneteket.

Ezeket az üzeneteket a [Azure Cloud Shell](https://shell.azure.com/)is megtekintheti:

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a cikkben létrehozott helyi konfigurációkat és az Azure-erőforrásokat törölheti a díjak elkerülése érdekében.

Az erőforrások törlése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és válassza az **Erőforráscsoportok** elemet.

2. Válassza ki az IoT Edge teszterőforrásokat tartalmazó erőforráscsoport nevét. 

3. Ellenőrizze az erőforráscsoportban található erőforrások listáját. Ha mindet törölni szeretné, válassza az **Erőforráscsoport törlése** lehetőséget. Ha csak bizonyos elemeket szeretne törölni, az egyes erőforrásokra kattintva külön törölheti őket. 

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban két IoT Edge eszközt konfigurált átjáróként, és az egyiket a másik szülő eszközként állította be. Ezután megmutatta, hogyan húz egy tároló lemezképet a gyermek eszközre egy átjárón keresztül.

A többi oktatóanyagra tovább lépve megtudhatja, hogyan lehet az Azure IoT Edge használatával egyéb üzleti megoldásokat létrehozni.

> [!div class="nextstepaction"]
> [Azure Machine Learning-modell üzembe helyezése modulként](tutorial-deploy-machine-learning.md)

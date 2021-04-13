---
title: Oktatóanyag – IoT Edge-eszközök hierarchiájának létrehozása – Azure IoT Edge
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre IoT Edge-eszközök hierarchikus struktúráját átjárók használatával.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 2/26/2021
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: bfecc88dc0c504cee615f1a3d35f9208aeb724f8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309191"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices"></a>Oktatóanyag: IoT Edge-eszközök hierarchiájának létrehozása

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Azure IoT Edge csomópontok központi telepítése hierarchikus rétegekben rendezett hálózatok között. A hierarchia minden rétege egy átjáró-eszköz, amely az alatta lévő rétegbeli eszközök üzeneteit és kéréseit kezeli.

Az eszközök hierarchiáját strukturálhatja úgy, hogy csak a legfelső réteg létesítsen kapcsolatot a felhővel, és az alsó réteg csak a szomszédos Észak-és Dél-rétegekkel tud kommunikálni. Ez a hálózati réteg a legtöbb ipari hálózat alapja, amely az [ISA-95 szabványt](https://en.wikipedia.org/wiki/ANSI/ISA-95)követi.

Ennek az oktatóanyagnak a célja, hogy olyan IoT Edge-eszközök hierarchiáját hozza létre, amelyek egyszerűsített üzemi környezetet szimulálnak. A rendszer a [szimulált hőmérséklet-érzékelő modult](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.simulated-temperature-sensor) az Internet-hozzáférés nélküli alsóbb rétegbeli eszközre helyezi át a tároló lemezképének a hierarchián keresztüli letöltésével.

A cél megvalósítása érdekében ez az oktatóanyag végigvezeti IoT Edge eszközök hierarchiájának létrehozásán, IoT Edge futtatókörnyezeti tárolók üzembe helyezésén az eszközökön, valamint az eszközök helyi konfigurálásán. Ebben az oktatóanyagban egy automatizált konfigurációs eszközt használ a következőhöz:

> [!div class="checklist"]
>
> * IoT Edge-eszközök hierarchiájában hozza létre és határozza meg a kapcsolatokat.
> * Konfigurálja a IoT Edge futtatókörnyezetet a hierarchiában lévő eszközökön.
> * Konzisztens tanúsítványokat telepíthet az eszköz-hierarchiába.
> * Munkaterhelések hozzáadása a hierarchiában lévő eszközökhöz.
> * A [IOT Edge API-proxy modul](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview) használatával biztonságosan irányíthatók át a http-forgalom az alsóbb rétegbeli eszközökről származó egyetlen porton keresztül.

>[!TIP]
>Ez az oktatóanyag olyan manuális és automatizált lépések keverékét tartalmazza, amelyekkel megadható a beágyazott IoT Edge funkciók bemutatója.
>
>Ha szeretné, hogy a IoT Edge-eszközök hierarchiájának beállítása teljesen automatizált legyen, kövesse az [ipari IoT-minta megírt Azure IoT Edge](https://aka.ms/iotedge-nested-sample). Ez a parancsfájlos forgatókönyv az Azure-beli virtuális gépeket előre konfigurált eszközökként helyezi üzembe a gyári környezet szimulálása érdekében.
>
>Ha szeretné részletesen megtekinteni a IoT Edge eszközök hierarchiájának létrehozásához és kezeléséhez szükséges manuális lépéseket, tekintse meg [a útmutató az IoT Edge Device Gateway-hierarchiák](how-to-connect-downstream-iot-edge-device.md)című témakört.

Ebben az oktatóanyagban a következő hálózati rétegek vannak definiálva:

* **Legfelső réteg**: IoT Edge a rétegben lévő eszközök közvetlenül kapcsolódhatnak a felhőhöz.

* **Alsó rétegek**: IoT Edge eszközök a legfelső réteg alatti rétegekben nem csatlakoztathatók közvetlenül a felhőhöz. Egy vagy több közvetítő IoT Edge eszközön kell átesniük az adatküldésre és fogadásra.

Ez az oktatóanyag az egyszerűség kedvéért két eszköz-hierarchiát használ, az alábbi ábrán látható. Egy eszköz, a **legfelső rétegbeli eszköz**, amely a hierarchia legfelső szintjén található eszközt jelöli, amely közvetlenül a felhőhöz tud csatlakozni. Ez az eszköz **szülő eszközként** is hivatkozni fog. A másik eszköz, az **alsó rétegbeli** eszköz a hierarchia alsó rétegében lévő eszközt jelöli, amely nem tud közvetlenül kapcsolódni a felhőhöz. Szükség szerint az éles környezetnek megfelelően több alsóbb rétegbeli eszközt is hozzáadhat. Az alacsonyabb rétegekben lévő eszközök is **alárendelt eszközökként** is hivatkoznak.

![Az oktatóanyag-hierarchia szerkezete, amely két eszközt tartalmaz: a felső rétegbeli eszközt és az alsó rétegbeli eszközt](./media/tutorial-nested-iot-edge/tutorial-hierarchy-diagram.png)

## <a name="prerequisites"></a>Előfeltételek

IoT Edge eszközök hierarchiájának létrehozásához a következőkre lesz szüksége:

* Internetkapcsolattal rendelkező számítógép (Windows vagy Linux).
* Egy érvényes előfizetéssel rendelkező Azure-fiók. Ha nem rendelkezik Azure- [előfizetéssel](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .
* Ingyenes vagy standard szintű [IoT hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* Azure Cloud Shell bash-rendszerhéj az Azure CLI v 2.3.1 használatával, az Azure IoT Extension v 0.10.6 vagy újabb verziójával. Ez az oktatóanyag a [Azure Cloud Shell](../cloud-shell/overview.md)használja. Ha nem ismeri a Azure Cloud Shell, a [részletekért tekintse meg a](./quickstart-linux.md#prerequisites)rövid útmutatót.
  * Az Azure CLI-modulok és-bővítmények aktuális verziójának megtekintéséhez futtassa az [az Version](/cli/azure/reference-index?#az_version)parancsot.
* Egy Linux-eszköz, amelyet IoT Edge eszközként konfigurálhat a hierarchiában lévő összes eszközhöz. Ez az oktatóanyag két eszközt használ. Ha nincs elérhető eszköz, az alábbi parancs használatával hozhat létre Azure-beli virtuális gépeket a hierarchiában lévő összes eszközhöz.

   Cserélje le a helyőrző szövegét a következő parancsra, és futtassa kétszer, egyszer az egyes virtuális gépeknél. Minden virtuális gépnek egyedi DNS-előtagra van szüksége, amely neveként is szolgál. A DNS-előtagnak meg kell felelnie a következő reguláris kifejezésnek: `[a-z][a-z0-9-]{1,61}[a-z0-9]` .

   ```bash
   az deployment group create \
    --resource-group <REPLACE_WITH_YOUR_RESOURCE_GROUP> \
    --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json" \
    --parameters dnsLabelPrefix='<REPLACE_WITH_UNIQUE_DNS_FOR_VIRTUAL_MACHINE>' \
    --parameters adminUsername='azureuser' \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/id_rsa.pub)" \
    --query "properties.outputs.[publicFQDN.value, publicSSH.value]" -o tsv
   ```

   A virtuális gép SSH-kulcsokat használ a felhasználók hitelesítéséhez. Ha nem ismeri az SSH-kulcsok létrehozását és használatát, [Az Azure-beli Linux rendszerű virtuális gépekhez tartozó nyilvános és titkos SSH-kulcspár utasításait](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)követheti.

   IoT Edge az 1,2-es verzió előre telepítve van ezzel az ARM-sablonnal, így a virtuális gépeken manuálisan kell telepíteni az eszközöket. Ha a IoT Edget a saját eszközeire telepíti, tekintse meg a [Azure IoT Edge telepítése Linux rendszerre (1,2-es verzió)](how-to-install-iot-edge.md) vagy a [1,2-es verzióra történő frissítési IoT Edge](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12).

   Egy virtuális gép sikeres létrehozása ezzel az ARM-sablonnal a virtuális gép `SSH` leíróját és teljes tartománynevét () fogja kiadni `FQDN` . A későbbi lépésekben az SSH-leírót és az egyes virtuális gépek teljes tartománynevét vagy IP-címét fogja használni, ezért nyomon követheti ezeket az adatokat. A minta kimenete alább látható.

   >[!TIP]
   >A Azure Portal az IP-címet és a teljes tartománynevet is megtalálhatja. Az IP-cím mezőben navigáljon a virtuális gépek listájához, és jegyezze fel a **nyilvános IP-cím mezőt**. A teljes tartománynév esetében lépjen az egyes virtuális gépek áttekintő oldalára, és keresse meg a **DNS-név** mezőt.

   ![A virtuális gép létrehoz egy JSON-t a létrehozás után, amely az SSH-leíróját tartalmazza](./media/tutorial-nested-iot-edge/virtual-machine-outputs.png)

* Győződjön meg arról, hogy a következő portok nyitva vannak az összes eszközön, kivéve a legkisebb rétegű eszközt: 8000, 443, 5671, 8883:
  * 8000: a Docker-tárolók rendszerképeinek az API-proxyn keresztüli lekérésére használatos.
  * 443: a szülő és a gyermek peremhálózati hubok között használatos REST API hívásokhoz.
  * 5671, 8883: AMQP és MQTT használatos.

  További információkért lásd: [portok megnyitása virtuális géphez a Azure Portal használatával](../virtual-machines/windows/nsg-quickstart-portal.md).

## <a name="configure-your-iot-edge-device-hierarchy"></a>Az IoT Edge-eszköz hierarchiájának konfigurálása

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>IoT Edge-eszközök hierarchiájának létrehozása

IoT Edge eszközök alkotják a hierarchia rétegeit. Ez az oktatóanyag két IoT Edge-eszköz hierarchiáját hozza létre: a **felső rétegbeli eszközt** és annak gyermekét, az **alsó rétegbeli eszközt**. Szükség szerint további alárendelt eszközöket is létrehozhat.

IoT Edge-eszközök hierarchiájának létrehozásához és konfigurálásához használja az `iotedge-config` eszközt. Ez az eszköz leegyszerűsíti a hierarchia konfigurációját azáltal, hogy több lépést automatizál és kondenzációs:

1. A felhő konfigurációjának beállítása és az egyes eszközök konfigurációjának előkészítése, beleértve a következőket:

   * Eszközök létrehozása a IoT Hub
   * A szülő-gyermek kapcsolatok beállítása az eszközök közötti kommunikáció engedélyezéséhez
   * Tanúsítványok láncának létrehozása minden eszközhöz a közöttük való biztonságos kommunikáció érdekében
   * Konfigurációs fájlok létrehozása minden eszközhöz

1. Az egyes eszközök konfigurációjának telepítése, beleértve a következőket:

   * Tanúsítványok telepítése minden eszközön
   * Az egyes eszközök konfigurációs fájljainak alkalmazása

Az `iotedge-config` eszköz automatikusan kiépíti a modul központi telepítését a IoT Edge eszközre.

Ha az `iotedge-config` eszközt a hierarchia létrehozására és konfigurálására szeretné használni, kövesse az alábbi lépéseket az Azure CLI-ben:

1. A [Azure Cloud Shell](https://shell.azure.com/)készítse el az oktatóanyag erőforrásainak könyvtárát:

   ```bash
   mkdir nestedIotEdgeTutorial
   ```

1. Töltse le a konfigurációs eszköz és a konfigurációs sablonok kiadását:

   ```bash
   cd ~/nestedIotEdgeTutorial
   wget -O iotedge_config.tar "https://github.com/Azure-Samples/iotedge_config_cli/releases/download/latest/iotedge_config_cli.tar.gz"
   tar -xvf iotedge_config.tar
   ```

   Ezzel létrehozza a `iotedge_config_cli_release` mappát az oktatóanyag-címtárban.

   Az eszköz-hierarchia létrehozásához használt sablonfájl a `iotedge_config.yaml` fájlban található `~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial` . Ugyanebben a könyvtárban `deploymentLowerLayer.json` egy JSON-telepítési fájl, amely utasításokat tartalmaz az **alsóbb rétegbeli eszközre** telepítendő modulok számára. A `deploymentTopLayer.json` fájl ugyanaz, de a **felső rétegbeli eszköz** esetében, mivel az egyes eszközökön üzembe helyezett modulok nem egyeznek. A `device_config.toml` fájl IoT Edge eszköz konfigurációjának sablonja, és a rendszer a hierarchiában lévő eszközökhöz tartozó konfigurációs csomagok automatikus előállítására szolgál.

   Ha szeretné megtekinteni az eszköz forráskódját és parancsfájlait `iotedge-config` , tekintse meg [a Azure-Samples adattárat a githubon](https://github.com/Azure-Samples/iotedge_config_cli).

1. Nyissa meg az oktatóanyag konfigurációs sablonját, és szerkessze az adatait:

   ```bash
   code ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml
   ```

   A **iothub** szakaszban töltse fel a és a `iothub_hostname` mezőket az `iothub_name` adataival. Ezek az információk a Azure Portal IoT Hub áttekintés lapján találhatók.

   A nem kötelező **tanúsítványok** szakaszban feltöltheti a mezőket a tanúsítvány és a kulcs abszolút elérési útjaival. Ha üresen hagyja ezeket a mezőket, a parancsfájl automatikusan önaláírt tesztelési tanúsítványokat fog előállítani a használathoz. Ha nem ismeri, hogyan használják a tanúsítványokat egy átjáró-forgatókönyvben, tekintse [meg a útmutató tanúsítványának szakaszát](how-to-connect-downstream-iot-edge-device.md#prepare-certificates).

   A **konfigurációs** szakaszban a az `template_config_path` `device_config.toml` eszköz konfigurációjának létrehozásához használt sablon elérési útja. A `default_edge_agent` mező határozza meg, hogy az Edge Agent-rendszerkép mely alsóbb rétegbeli eszközöket fogja lekérni, és honnan.

   Éles környezetben a **edgedevices** szakaszban szerkesztheti a hierarchia fáját, hogy az tükrözze a kívánt struktúrát. Az oktatóanyag esetében fogadja el az alapértelmezett fát. Minden eszközhöz van egy `device_id` mező, ahol megadhatja az eszközök nevét. Ott van a `deployment` mező is, amely az eszköz központi telepítési JSON elérési útját határozza meg.

   Manuálisan is regisztrálhatja IoT Edge eszközeit a IoT Hub a Azure Portal vagy a Azure Cloud Shell használatával. További információt [a IoT Edge-eszközök regisztrálásával kapcsolatos útmutatóban](how-to-register-device.md)talál.

   A szülő-gyermek kapcsolatokat manuálisan is meghatározhatja. További információkért tekintse meg az [átjáró-hierarchia létrehozása](how-to-connect-downstream-iot-edge-device.md#create-a-gateway-hierarchy) című szakaszt.

   ![A konfigurációs fájl edgedevices szakasza lehetővé teszi a hierarchia definiálását](./media/tutorial-nested-iot-edge/hierarchy-config-sample.png)

1. Mentse és zárjuk be a fájlt:

   `CTRL + S`, `CTRL + Q`

1. Hozzon létre egy kimeneti könyvtárat a konfigurációs kötegekhez az oktatóanyag erőforrásainak könyvtárában:

   ```bash
   mkdir ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs
   ```

1. Navigáljon a `iotedge_config_cli_release` címtárhoz, és futtassa az eszközt a IoT Edge-eszközök hierarchiájának létrehozásához:

   ```bash
   cd ~/nestedIotEdgeTutorial/iotedge_config_cli_release
   ./iotedge_config --config ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml --output ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs -f
   ```

   A `--output` jelzővel az eszköz létrehozza az eszköz tanúsítványait, a tanúsítvány-csomagokat és egy naplófájlt az Ön által választott könyvtárban. Ha a `-f` jelző be van állítva, az eszköz automatikusan megkeresi a meglévő IoT Edge eszközöket a IoT Hubban, és eltávolítja őket, hogy elkerülje a hibákat, és megtisztítsa a hubot.

   A konfigurációs eszköz létrehozza a IoT Edge eszközöket, és beállítja a közöttük lévő szülő-gyermek kapcsolatokat. Igény szerint tanúsítványokat hoz létre az eszközök számára. Ha a rendszer az üzembe helyezési útvonalakat is megadja, az eszköz automatikusan létrehozza ezeket az üzemelő példányokat az eszközökön, de ez nem kötelező. Végül az eszköz létrehozza az eszközökhöz tartozó konfigurációs csomagokat, és elhelyezi őket a kimeneti könyvtárban. A konfigurációs eszköz lépéseinek alapos megtekintéséhez tekintse meg a naplófájlt a kimeneti könyvtárban.

   ![A parancsfájl végrehajtás után megjeleníti a hierarchia topológiáját.](./media/tutorial-nested-iot-edge/successful-setup-tool-run.png)

Ellenőrizze, hogy a parancsfájl topológiájának kimenete megfelelő-e. Ha meggyőződött arról, hogy a hierarchia megfelelően van strukturálva, készen áll a folytatásra.

### <a name="configure-the-iot-edge-runtime"></a>Az IoT Edge-futtatókörnyezet konfigurálása

Az eszközök kiépítésén kívül a konfigurációs lépések megbízható kommunikációt hoznak létre a hierarchiában lévő eszközök között a korábban létrehozott tanúsítványok használatával. A lépések azt is megkezdik, hogy létrehozza a hierarchia hálózati szerkezetét. A felső rétegbeli eszköz fenntartja az internetkapcsolatot, így lehetővé teszi a lemezképek lekérését a felhőből, míg az alsó rétegbeli eszközök a legfelső rétegbeli eszközön keresztül érik el ezeket a lemezképeket.

A IoT Edge futtatókörnyezet konfigurálásához alkalmaznia kell a telepítési parancsfájl által létrehozott konfigurációs csomagokat az eszközeire. A konfigurációk némileg eltérnek a **legfelső rétegbeli eszköz** és az **alsó rétegbeli eszköz** között, ezért szem előtt tartva, hogy az eszköz melyik konfigurációs fájlját alkalmazza.

1. Minden eszköznek szüksége van a hozzá tartozó konfigurációs csomagra. USB-meghajtó vagy [biztonságos fájlmásolás](https://www.ssh.com/ssh/scp/) használatával helyezheti át a konfigurációs csomagokat az egyes eszközökre.

   Ügyeljen arra, hogy minden eszköz számára a megfelelő konfigurációs csomagot küldje el.

   ```bash
   scp <PATH_TO_CONFIGURATION_BUNDLE> <USER>@<VM_IP_OR_FQDN>:~
   ```

   Az `:~` azt jelenti, hogy a konfigurációs mappát a rendszer a virtuális gép kezdőkönyvtárának mappájába helyezi.

1. Jelentkezzen be a virtuális gépre, és alkalmazza a konfigurációs csomagot az eszközre:

   ```bash
   ssh <USER>@<VM_IP_OR_FQDN>
   ```

1. Minden eszközön bontsa ki a konfigurációs csomagot. Először telepítenie kell a zip-t:

   ```bash
   sudo apt install zip
   unzip ~/<PATH_TO_CONFIGURATION_BUNDLE>/<CONFIGURATION_BUNDLE>.zip
   ```

1. Minden eszközön alkalmazza a konfigurációs csomagot az eszközre:

   ```bash
   sudo ./install.sh
   ```

   A **felső rétegbeli eszközön** megjelenik egy üzenet, amely megadja az állomásnév beírását. Az **alsó rétegbeli eszközön** az állomásnév és a szülő állomásneve fog megjelenni. Adja meg a megfelelő IP-címet vagy teljes tartománynevet az egyes kérésekhez. Használhatja bármelyiket, de konzisztens lehet az eszközök között. Az alábbi ábrán látható a telepítési parancsfájl kimenete.

   Ha azt szeretné, hogy az eszköz konfigurációs fájljában milyen módosítások történnek, tekintse meg a útmutató [az eszközök IoT Edge konfigurálása című szakaszát](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices).

  ![A konfigurációs csomagok telepítése frissíti a config. toml fájlokat az eszközön, és újraindítja az összes IoT Edge szolgáltatást automatikusan](./media/tutorial-nested-iot-edge/configuration-install-output.png)

Ha helyesen végrehajtotta a fenti lépéseket, ellenőrizheti, hogy az eszközök megfelelően vannak-e konfigurálva.

Futtassa a konfigurációt és a kapcsolati ellenőrzéseket az eszközökön. A **felső rétegbeli eszközhöz**:

   ```bash
   sudo iotedge check
   ```

Az **alsó rétegbeli eszköz** esetében a diagnosztikai rendszerképet manuálisan át kell adni a következő parancsban:

   ```bash
   sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:8000/azureiotedge-diagnostics:1.2
   ```

A **felső rétegbeli eszközön** várhatóan több elküldés értékeléssel rendelkező kimenet jelenik meg. Előfordulhat, hogy bizonyos figyelmeztetések jelennek meg a naplók házirendjeivel kapcsolatban, és a hálózattól függően a DNS-házirendek.

<!-- Add pic after GA -->
<!-- KEEP! A sample output of the `iotedge check` is shown below: -->

<!-- KEEP! ![Sample configuration and connectivity results](./media/tutorial-nested-iot-edge/configuration-and-connectivity-check-results.png) -->

Ha meggyőződött arról, hogy a konfigurációk helyesek az egyes eszközökön, készen áll a folytatásra.

## <a name="deploy-modules-to-your-devices"></a>Modulok üzembe helyezése az eszközökön

Az eszközök üzembe helyezését a rendszer automatikusan generálta az eszközök létrehozásakor. Az `iotedge-config-cli` eszközön a létrehozásuk után a **felső és az alsó rétegbeli eszközökhöz** tartozó üzembe helyezési JSON-ket. A modul üzembe helyezése a IoT Edge futtatókörnyezet minden eszközön való konfigurálása közben függőben volt. A futtatókörnyezet konfigurálása után megkezdődött a **legfelső rétegbeli eszközre** történő központi telepítés. A központi telepítések befejezése után az **alsó rétegbeli eszköz** a **IoT Edge API-proxy** modul használatával lekéri a szükséges képeket.

A [Azure Cloud Shellban](https://shell.azure.com/)megtekintheti a **felső rétegbeli eszköz** üzembe helyezésének JSON-fájlját, hogy megtudja, milyen modulok lettek telepítve az eszközre:

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentTopLayer.json
   ```

Emellett a futásidejű modulok **IoT Edge ügynök** és **IoT Edge hub** esetében a **felső rétegbeli eszköz** megkapja a **docker beállításjegyzék** -modulját és **IoT Edge API-proxy** modulját.

A **Docker beállításjegyzék** -modulja egy meglévő Azure Container Registryra mutat. Ebben az esetben `REGISTRY_PROXY_REMOTEURL` a Microsoft Container Registryra mutat. A-ben láthatja `createOptions` , hogy kommunikál a 5000-es porton.

A **IOT Edge API-proxy** modul http-kéréseket irányít más modulokba, így az alsó rétegbeli eszközök lehívhatják a tároló lemezképeit vagy leküldéses blobokat a tárolóba. Ebben az oktatóanyagban a 8000-es porton keresztül kommunikál, és úgy van konfigurálva, hogy a Docker-tároló rendszerképét lekéréses kérelmeket továbbítsa az 5000-es porton lévő **Docker beállításjegyzék** -modul Emellett a blob Storage-feltöltési kérések a 11002-es port AzureBlobStorageonIoTEdge is átirányítják a modult. További információ a **IOT Edge API-proxy** modulról és annak konfigurálásáról: a [modul útmutatója.](how-to-configure-api-proxy-module.md)

Ha szeretné megtekinteni, hogyan hozhat létre egy központi telepítést a Azure Portal vagy a Azure Cloud Shell használatával, tekintse meg [a útmutató első réteg eszköz szakaszát](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-top-layer-devices).

A [Azure Cloud Shellban](https://shell.azure.com/)megtekintheti az **alsó rétegbeli eszköz** üzembe helyezésének JSON-fájlját, hogy megtudja, milyen modulok lettek telepítve az eszközre:

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentLowerLayer.json
   ```

Azt is láthatja, `systemModules` hogy az **alsó rétegbeli eszköz** futtatókörnyezeti moduljai úgy vannak beállítva, hogy a `$upstream:8000` helyett a `mcr.microsoft.com` **felső rétegbeli eszközként** legyenek lekérésre. Az **alsó rétegbeli eszköz** a Docker-rendszerképet küldi a **IoT Edge API-proxy** modulnak a 8000-es porton, mivel nem tudja közvetlenül lekérni a rendszerképeket a felhőből. Az **alsó rétegbeli eszközre** telepített másik modul, a **szimulált hőmérséklet-érzékelő** modul is elvégzi a képkérését `$upstream:8000` .

Ha szeretné megtekinteni, hogyan hozhat létre egy központi telepítést a Azure Portal vagy Azure Cloud Shell használatával, tekintse meg [a útmutató alsó rétegű eszköz című szakaszát](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-lower-layer-devices).

A modulok állapotát a parancs használatával tekintheti meg:

   ```bash
   az iot hub module-twin show --device-id <edge_device_id> --module-id '$edgeAgent' --hub-name <iot_hub_name> --query "properties.reported.[systemModules, modules]"
   ```

   Ezzel a paranccsal a rendszer az összes jelentett edgeAgent-tulajdonságot megjeleníti. Íme néhány hasznos információ az eszköz állapotának figyeléséhez: *futtatókörnyezet állapota*, *futásidejű indítási idő*, *futtatókörnyezet utolsó kilépésének időpontja*, *futásidejű újraindítások száma*.

A modulok állapotát a [Azure Portal](https://ms.portal.azure.com/)is megtekintheti. Az eszközök és modulok megtekintéséhez keresse meg a IoT Hub **IoT Edge** szakaszát.

Ha elégedett a modul üzembe helyezésével, készen áll a folytatásra.

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

A **szimulált hőmérséklet-érzékelő** modul a minta környezeti adattípust hozza létre. A környezeti hőmérsékletet, a páratartalomot, a gépi hőmérsékletet és a nyomást, valamint egy időbélyeget tartalmazó üzeneteket küld.

Ezeket az üzeneteket a [Azure Cloud Shell](https://shell.azure.com/)is megtekintheti:

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="troubleshooting"></a>Hibaelhárítás

Futtassa a `iotedge check` parancsot a konfiguráció ellenőrzéséhez és a hibák elhárításához.

`iotedge check`Egy beágyazott hierarchiában is futtatható, még akkor is, ha a gyermek gépek nem rendelkeznek közvetlen internet-hozzáféréssel.

Az `iotedge check` alsó rétegből való futtatáskor a program megpróbálja lekérni a rendszerképet a szülőtől az 443-es porton keresztül.

Ebben az oktatóanyagban az 8000-es portot használjuk, ezért meg kell adnia a következőket:

```bash
sudo iotedge check --diagnostics-image-name $upstream:8000/azureiotedge-diagnostics:1.2.0-rc4
```

Az `azureiotedge-diagnostics` értéket a rendszer a beállításjegyzék-modullal összekapcsolt tároló beállításjegyzékből kéri le. Ez az oktatóanyag alapértelmezés szerint a következőre van beállítva: https://mcr.microsoft.com:

| Name | Érték |
| - | - |
| `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

Ha privát tároló-beállításjegyzéket használ, győződjön meg arról, hogy az összes rendszerkép (IoTEdgeAPIProxy, edgeAgent, edgeHub, szimulált hőmérséklet-érzékelő és diagnosztika) megtalálható a tároló beállításjegyzékében.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a cikkben létrehozott helyi konfigurációkat és az Azure-erőforrásokat törölheti a díjak elkerülése érdekében.

Az erőforrások törlése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és válassza az **Erőforráscsoportok** elemet.

2. Válassza ki az IoT Edge teszterőforrásokat tartalmazó erőforráscsoport nevét. 

3. Ellenőrizze az erőforráscsoportban található erőforrások listáját. Ha mindet törölni szeretné, válassza az **Erőforráscsoport törlése** lehetőséget. Ha csak bizonyos elemeket szeretne törölni, az egyes erőforrásokra kattintva külön törölheti őket. 

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban két IoT Edge eszközt konfigurált átjáróként, és az egyiket a másik szülő eszközként állította be. Ezután bemutatta, hogy a tároló lemezképét a IoT Edge API-proxy modul használatával egy átjárón keresztül a gyermek eszközre húzza. Ha további információra van szüksége, tekintse meg a [proxy modul használatának útmutatóját](how-to-configure-api-proxy-module.md) .

Ha többet szeretne megtudni az átjárók IoT Edge eszközök hierarchikus rétegeinek létrehozásáról, tekintse meg a következő témakört: útmutató az alsóbb rétegbeli [IoT Edge eszközök csatlakoztatásához](how-to-connect-downstream-iot-edge-device.md).

A többi oktatóanyagra tovább lépve megtudhatja, hogyan lehet az Azure IoT Edge használatával egyéb üzleti megoldásokat létrehozni.

> [!div class="nextstepaction"]
> [Azure Machine Learning-modell üzembe helyezése modulként](tutorial-deploy-machine-learning.md)

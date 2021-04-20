---
title: Oktatóanyag – Új eszközök hierarchiájának IoT Edge létrehozása – Azure IoT Edge
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre hierarchikus struktúrát a IoT Edge átjárók használatával.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 2/26/2021
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 44fe6bb3787e1fe0df7ccf83200497b46c473568
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728499"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices"></a>Oktatóanyag: Új eszközök hierarchiájának IoT Edge létrehozása

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Üzembe Azure IoT Edge csomópontokat hierarchikus rétegekbe rendezett hálózatok között. A hierarchia minden rétege egy átjáróeszköz, amely az alatta lévő réteg eszközeitől származó üzeneteket és kéréseket kezeli.

Az eszközök hierarchiáját úgy is fel lehet struktúratálni, hogy csak a felső rétegnek van kapcsolata a felhővel, az alsó rétegek pedig csak a szomszédos északi és déli rétegekkel kommunikálhatnak. Ez a hálózati réteg a legtöbb ipari hálózat alapja, amely az [ISA-95 szabványt követi.](https://en.wikipedia.org/wiki/ANSI/ISA-95)

Ennek az oktatóanyagnak az a célja, hogy egy egyszerűsített éles környezetet szimuláló IoT Edge hierarchiát hozzon létre. A végén üzembe fogja helyezni a [Simulated Temperature Sensor](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.simulated-temperature-sensor) modult egy internet-hozzáférés nélküli alsóbb rétegben lévő eszközön úgy, hogy a hierarchián keresztül letölti a tároló rendszerképeket.

Ennek a célnak az eléréséhez ez az oktatóanyag végigvezeti a IoT Edge-eszközök hierarchiájának létrehozásán, IoT Edge futásidejű tárolók az eszközökön való üzembe helyezésén és az eszközök helyi konfigurálásán. Ebben az oktatóanyagban egy automatizált konfigurációs eszközt fog használni a következő műveletekhez:

> [!div class="checklist"]
>
> * Hozza létre és határozza meg a kapcsolatokat a IoT Edge hierarchiában.
> * Konfigurálja IoT Edge-kiszolgálót a hierarchiában lévő eszközökön.
> * Telepítsen konzisztens tanúsítványokat az eszközhierarchiában.
> * Számítási feladatok hozzáadása a hierarchiában lévő eszközökhöz.
> * A [IoT Edge API-proxy modullal](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview) biztonságosan irányíthatja a HTTP-forgalmat egyetlen porton keresztül az alsó réteg eszközeiről.

>[!TIP]
>Ez az oktatóanyag manuális és automatizált lépések egyveületét tartalmazza a beágyazott és IoT Edge bemutatásához.
>
>Ha teljesen automatizáltan szeretné megszabadni a IoT Edge-eszközök hierarchiájának beállítását, kövesse az ipari [IoT-eszközökhöz Azure IoT Edge](https://aka.ms/iotedge-nested-sample)szkriptekkel Azure IoT Edge mintát. Ez a szkriptekkel futtatott forgatókönyv előre konfigurált eszközként helyez üzembe Azure-beli virtuális gépeket a gyári környezet szimulálása érdekében.
>
>Ha szeretné részletesen áttekinteni az IoT Edge-eszközök hierarchiájának manuális létrehozási és kezelési lépéseit, tekintse meg az IoT Edge-átjáró-hierarchiákra IoT Edge [útmutatót.](how-to-connect-downstream-iot-edge-device.md)

Ebben az oktatóanyagban a következő hálózati rétegeket definiáljuk:

* **Felső réteg:** IoT Edge rétegben lévő eszközök közvetlenül csatlakozhatnak a felhőhöz.

* **Alacsonyabb rétegek:** IoT Edge réteg alatti rétegekben található eszközök nem csatlakozhatnak közvetlenül a felhőhöz. Az adatok küldését és fogadását egy vagy több IoT Edge kell végigmenni.

Ez az oktatóanyag az egyszerűség kedvéért két eszközhierarchiát használ, az alábbi képen látható módon. Az egyik eszköz, a **legfelső rétegbeli** eszköz a hierarchia felső rétegében található eszközt jelöli, amely közvetlenül tud csatlakozni a felhőhöz. Ezt az eszközt szülőeszköznek is **nevezzük.** A másik eszköz, az alsó rétegbeli eszköz a hierarchia alsó rétegében lévő eszközt jelöli, amely nem tud közvetlenül kapcsolódni a felhőhöz.  Szükség szerint további alsóbb rétegű eszközöket is hozzáadhat, amelyek az éles környezetet képviselik. Az alsóbb rétegben az eszközöket gyermekeszközöknek **is nevezzük.**

![Az oktatóanyag-hierarchia szerkezete, amely két eszközt tartalmaz: a felső rétegben lévő eszközt és az alsó rétegben lévő eszközt](./media/tutorial-nested-iot-edge/tutorial-hierarchy-diagram.png)

## <a name="prerequisites"></a>Előfeltételek

A IoT Edge hierarchiájának létrehozásához a következőre lesz szüksége:

* Internetkapcsolattal kapcsolatos számítógép (Windows vagy Linux).
* Azure-fiók érvényes előfizetéssel. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) a kezdés előtt. [](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)
* Ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* Egy Bash-Azure Cloud Shell az Azure CLI 2.3.1-es vagy újabb, telepített Azure IoT-bővítmény 0.10.6-os vagy újabb verzióval való használatával. Ez az oktatóanyag a következő [Azure Cloud Shell:](../cloud-shell/overview.md). Ha még nem ismeri a Azure Cloud Shell, tekintse meg [a részleteket tartalmazó rövid útmutatót.](./quickstart-linux.md#prerequisites)
  * Az Azure CLI-modulok és -bővítmények aktuális verzióinak a futtatásához futtassa [az az version (az az verzió) futtatását.](/cli/azure/reference-index?#az_version)
* Egy Linux-eszköz, amely IoT Edge eszközként konfigurálható a hierarchiában lévő összes eszközhöz. Ez az oktatóanyag két eszközt használ. Ha nem állnak rendelkezésre eszközök, az alábbi paranccsal hozhat létre Azure-beli virtuális gépeket a hierarchiában lévő összes eszközhöz.

   Cserélje le a helyőrző szövegét a következő parancsban, és futtassa kétszer, minden virtuális gépen egyszer. Minden virtuális gépnek egyedi DNS-előtagra van szüksége, amely egyben a neve is. A DNS-előtagnak meg kell felelnie a következő reguláris kifejezésnek: `[a-z][a-z0-9-]{1,61}[a-z0-9]` .

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

   A virtuális gép SSH-kulcsokat használ a felhasználók hitelesítésére. Ha nem ismeri az SSH-kulcsok létrehozását és használatát, kövesse az Azure-beli Linux rendszerű virtuális gépek [nyilvános-titkos SSH-kulcspárjára vonatkozó utasításokat.](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)

   IoT Edge 1.2-es verziója már telepítve van ezzel az ARM-sablonnal, így nincs szükség az eszközök manuális telepítésére a virtuális gépeken. Ha saját eszközeire IoT Edge telepít, lásd: [Install Azure IoT Edge for Linux (version 1.2) (1.2-es verzió)](how-to-install-iot-edge.md) vagy Update IoT Edge to version [1.2 (1.2-es](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12)verzióra való frissítés).

   Ha sikeresen létrehozott egy virtuális gépet ezzel az ARM-sablonnal, a rendszer kiadja a virtuális gép leíróját és teljes `SSH` tartománynevét ( `FQDN` ). A későbbi lépésekben az SSH-leírót és az egyes virtuális gépek teljes tartománykészletét vagy IP-címét fogja használni a konfiguráláshoz, ezért kövesse nyomon ezeket az információkat. Az alábbi képen egy mintakimenet látható.

   >[!TIP]
   >Az IP-címet és az FQDN-t a Azure Portal. Az IP-cím mezőben keresse meg a virtuális gépek listáját, és jegyezze fel a **Nyilvános IP-cím mezőt.** A teljes tartománynévhez keresse meg az egyes virtuális gépek áttekintő lapját, és keresse meg a **DNS-név mezőt.**

   ![A virtuális gép létrehozáskor egy JSON-kimenetet fog kiíratni, amely tartalmazza az SSH-leíróját](./media/tutorial-nested-iot-edge/virtual-machine-outputs.png)

* Győződjön meg arról, hogy a legalsó rétegben található eszközök kivételével minden eszköz számára nyitva vannak a következő portok: 8000, 443, 5671, 8883:
  * 8000: A Docker-tároló rendszerképének API-proxyn keresztüli lekért használata.
  * 443: A szülő- és gyermek-peremhálózatok között használatos a REST API hívásokhoz.
  * 5671, 8883: AmQP-hez és MQTT-hez használatos.

  További információ: [Portok megnyitása virtuális géphez a Azure Portal.](../virtual-machines/windows/nsg-quickstart-portal.md)

## <a name="configure-your-iot-edge-device-hierarchy"></a>A IoT Edge-eszközhierarchia konfigurálása

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>Új eszközök hierarchiájának IoT Edge létrehozása

IoT Edge eszközök a hierarchia rétegeit teszik ki. Ez az oktatóanyag két különböző IoT Edge hoz létre: a felső rétegben lévő eszközt és annak gyermekét, az alsó  **rétegben lévő eszközt.** Szükség szerint további gyermekeszközöket is létrehozhat.

Az eszköz használatával létrehozhatja és konfigurálhatja IoT Edge-eszközök `iotedge-config` hierarchiáját. Ez az eszköz leegyszerűsíti a hierarchia konfigurálási lépéseit azáltal, hogy több lépést automatizál két lépésre:

1. A felhőkonfiguráció beállítása és az egyes eszközkonfigurációk előkészítése, beleértve a következőket:

   * Eszközök létrehozása a IoT Hub
   * A szülő-gyermek kapcsolatok beállítása az eszközök közötti kommunikációhoz
   * Tanúsítványlánc létrehozása minden eszközhöz a közöttük biztonságos kommunikáció létrehozásához
   * Konfigurációs fájlok létrehozása minden eszközhöz

1. Az egyes eszközkonfigurációk telepítése, beleértve a következőket:

   * Tanúsítványok telepítése az egyes eszközökre
   * Konfigurációs fájlok alkalmazása az egyes eszközökre

Az `iotedge-config` eszköz automatikusan üzembe is fogja IoT Edge modulokat a saját eszközén.

Ha az eszközzel létre és konfigurálja a hierarchiát, kövesse az Alábbi lépéseket az `iotedge-config` Azure CLI-ban:

1. A [Azure Cloud Shell](https://shell.azure.com/)könyvtárat az oktatóanyag erőforrásaihoz:

   ```bash
   mkdir nestedIotEdgeTutorial
   ```

1. Töltse le a konfigurációs eszköz és a konfigurációs sablonok kiadását:

   ```bash
   cd ~/nestedIotEdgeTutorial
   wget -O iotedge_config.tar "https://github.com/Azure-Samples/iotedge_config_cli/releases/download/latest/iotedge_config_cli.tar.gz"
   tar -xvf iotedge_config.tar
   ```

   Ezzel létrehozza a `iotedge_config_cli_release` mappát az oktatóanyag könyvtárában.

   Az eszközhierarchia létrehozásához használt sablonfájl a `iotedge_config.yaml` fájlban `~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial` található. Ugyanabban a könyvtárban található egy JSON üzembe helyezési fájl, amely utasításokat tartalmaz arra, hogy mely modulokat kell üzembe helyezni `deploymentLowerLayer.json` az **alsó rétegben található eszközön.** A fájl ugyanaz, de a felső rétegben lévő eszköz esetében, mivel az egyes eszközökre üzembe helyezett modulok `deploymentTopLayer.json` nem azonosak.  A fájl az eszközkonfigurációk IoT Edge sablonja, és a rendszer automatikusan létrehozza a konfigurációs csomagokat a `device_config.toml` hierarchiában lévő eszközökhöz.

   Ha meg szeretné nézni az eszköz forráskódját és szkripteket, tekintse meg a GitHub Azure-Samples `iotedge-config` tárházát. [](https://github.com/Azure-Samples/iotedge_config_cli)

1. Nyissa meg az oktatóanyag konfigurációs sablonját, és szerkessze az adataival:

   ```bash
   code ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml
   ```

   Az **iothub szakaszban** töltse ki a és a `iothub_hostname` mezőt az `iothub_name` adataival. Ezek az információk a webhely áttekintési lapján IoT Hub találhatók Azure Portal.

   A választható **tanúsítványok szakaszban** feltöltheti a mezőket a tanúsítvány és a kulcs abszolút elérési útjukkal. Ha üresen hagyja ezeket a mezőket, a szkript automatikusan elő fog hozni önaírt teszttanúsítványokat. Ha nem ismeri a tanúsítványok átjáró-forgatókönyvekben való alkalmazását, tekintse meg az útmutató [tanúsítványokkal kapcsolatos szakaszát.](how-to-connect-downstream-iot-edge-device.md#prepare-certificates)

   A konfigurációs **szakaszban** a az eszközkonfigurációk létrehozásához használt `template_config_path` sablon elérési `device_config.toml` útja. A mező határozza meg, hogy az Edge Agent melyik rendszerképének alsó rétegbeli eszközeit kell le- `default_edge_agent` és honnan lekérte.

   Az **edgedevices** (peremhálózatok) szakaszban éles környezetben szerkesztheti a hierarchiafát, hogy az tükrözze a kívánt struktúrát. Ebben az oktatóanyagban fogadja el az alapértelmezett fát. Minden eszközhöz van egy mező, ahol `device_id` az eszközök elnevezése is elérhető. A mezőben található az eszköz üzembe helyezési JSON-fájlja elérési `deployment` útja is.

   Manuálisan is regisztrálhat IoT Edge-eszközöket a IoT Hub a Azure Portal vagy Azure Cloud Shell. További információért tekintse meg az útmutatót, amely bemutatja, [hogyan regisztrálhat IoT Edge eszközt.](how-to-register-device.md)

   A szülő-gyermek kapcsolatokat manuálisan is meghatározhatja. További [információért](how-to-connect-downstream-iot-edge-device.md#create-a-gateway-hierarchy) tekintse meg az útmutató Átjáróhierarchia létrehozása című szakaszát.

   ![A konfigurációs fájl edgedevices szakaszában meghatározhatja a hierarchiát](./media/tutorial-nested-iot-edge/hierarchy-config-sample.png)

1. Mentse és zárja be a fájlt:

   `CTRL + S`, `CTRL + Q`

1. Hozzon létre egy outputs könyvtárat a konfigurációs csomagokhoz az oktatóanyag erőforrásainak könyvtárában:

   ```bash
   mkdir ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs
   ```

1. Lépjen a könyvtárba, és futtassa az eszközt a virtuális eszközök `iotedge_config_cli_release` hierarchiájának IoT Edge létrehozásához:

   ```bash
   cd ~/nestedIotEdgeTutorial/iotedge_config_cli_release
   ./iotedge_config --config ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml --output ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs -f
   ```

   A jelzővel az eszköz létrehozza az eszköztanúsítványokat, a tanúsítványcsomagokat és egy naplófájlt `--output` egy Ön által választott könyvtárban. Ha a jelző be van állítva, az eszköz automatikusan keresni fogja a meglévő IoT Edge eszközöket a IoT Hub és eltávolítja őket a hibák elkerülése és a hub `-f` tisztántartása érdekében.

   A konfigurációs eszköz létrehozza a IoT Edge eszközöket, és beállítja közöttük a szülő-gyermek kapcsolatokat. Ha szükséges, tanúsítványokat hoz létre az eszközök számára. Ha meg vannak biztosítanak elérési utak az üzembe helyezési JSON-hez, az eszköz automatikusan létrehozza ezeket az üzembe helyezéseket az eszközökön, de ez nem szükséges. Végül az eszköz létrehozza az eszközökhöz szükséges konfigurációs csomagokat, és a kimeneti könyvtárba fogja őket helyezze. A konfigurációs eszköz által végzett lépések alapos vizsgálatához tekintse meg a naplófájlt a kimeneti könyvtárban.

   ![A szkript megjeleníti a hierarchia topológiáját a végrehajtáskor](./media/tutorial-nested-iot-edge/successful-setup-tool-run.png)

Ellenőrizze, hogy a szkript topológiakimenete helyes-e. Ha meggyőződött arról, hogy a hierarchia megfelelően strukturálva van, készen áll a folytatásra.

### <a name="configure-the-iot-edge-runtime"></a>Az IoT Edge-futtatókörnyezet konfigurálása

Az eszközök kiépítése mellett a konfigurációs lépések megbízható kommunikációt létesítnek a hierarchiában lévő eszközök között a korábban létrehozott tanúsítványokkal. A lépések megkezdik a hierarchia hálózati struktúrájának megállapítását is. A felső rétegbeli eszköz fenntartja az internetkapcsolatot, így leküldi a rendszerképeket a felhőből a futásidejű rendszerképe számára, míg az alsó rétegbeli eszközök a felső rétegbeli eszközön keresztül fognak hozzáférni ezekhez a képekhez.

A IoT Edge konfigurálásához alkalmaznia kell a telepítési szkript által létrehozott konfigurációs csomagokat az eszközökre. A konfigurációk kismértékben eltérnek **a** legfelső rétegben lévő és az alsó rétegben lévő eszköz között, ezért ne feledje, hogy melyik eszköz konfigurációs fájlját alkalmazza az egyes eszközökre. 

1. Minden eszköznek szüksége van a megfelelő konfigurációs csomagra. A konfigurációs csomagokat [](https://www.ssh.com/ssh/scp/) USB-meghajtóval vagy biztonságos fájlmásolóval is áthelyezheti az egyes eszközökre.

   Mindenképpen a megfelelő konfigurációs csomagot küldje el mindegyik eszközre.

   ```bash
   scp <PATH_TO_CONFIGURATION_BUNDLE> <USER>@<VM_IP_OR_FQDN>:~
   ```

   Ez azt jelenti, hogy a konfigurációs mappa a virtuális gép `:~` kezdőkönyvtárába kerül.

1. Jelentkezzen be a virtuális gépre a konfigurációs csomag eszközre való alkalmazáshoz:

   ```bash
   ssh <USER>@<VM_IP_OR_FQDN>
   ```

1. Csomagolja ki a konfigurációs csomagot minden eszközön. Először telepítenie kell a zip-t:

   ```bash
   sudo apt install zip
   unzip ~/<PATH_TO_CONFIGURATION_BUNDLE>/<CONFIGURATION_BUNDLE>.zip
   ```

1. Minden eszközön alkalmazza a konfigurációs csomagot az eszközre:

   ```bash
   sudo ./install.sh
   ```

   A felső **rétegben lévő eszközön** a gazdanév beíratására vonatkozó kérés fog kapni. Az alsó **rétegben lévő eszközön** a rendszer az állomásnevet és a szülő gazdagépnevét kéri. Minden kérdéshez meg kell adnia a megfelelő IP-címet vagy teljes tartományt. Bármelyiket használhatja, de legyen konzisztens a különböző eszközökön. A telepítési szkript kimenete az alábbi képen látható.

   Ha szeretné közelebbről megnézni, hogy milyen módosításokat végzett az eszköz konfigurációs fájljában, tekintse meg az útmutató IoT Edge konfigurálása az eszközökön című [szakaszát.](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices)

  ![A konfigurációs csomagok telepítése frissíti a config.toml fájlokat az eszközön, és automatikusan újraindítja IoT Edge összes konfigurációs szolgáltatást](./media/tutorial-nested-iot-edge/configuration-install-output.png)

Ha a fenti lépéseket megfelelően befejezte, ellenőrizheti, hogy az eszközök megfelelően vannak-e konfigurálva.

Futtassa a konfiguráció és a kapcsolat ellenőrzését az eszközökön. A felső **rétegben lévő eszközhöz:**

   ```bash
   sudo iotedge check
   ```

Az alsó **rétegben található eszközhöz** a diagnosztikai rendszerképet manuálisan kell megadni a parancsban:

   ```bash
   sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:8000/azureiotedge-diagnostics:1.2
   ```

A felső **réteg eszközén** a kimenet számos átadási kiértékelést fog látni. Előfordulhat, hogy figyelmeztetés jelenik meg a naplók házirendjére és a hálózatától függően a DNS-szabályzatra.

<!-- Add pic after GA -->
<!-- KEEP! A sample output of the `iotedge check` is shown below: -->

<!-- KEEP! ![Sample configuration and connectivity results](./media/tutorial-nested-iot-edge/configuration-and-connectivity-check-results.png) -->

Ha minden eszközön megfelelőek a konfigurációk, készen áll a folytatásra.

## <a name="deploy-modules-to-your-devices"></a>Modulok üzembe helyezése az eszközökön

Az eszközökre való modultelepítések automatikusan létrejöttek az eszközök létrehozásakor. Az `iotedge-config-cli` eszköz létrehozta az üzembe  helyezési JSON-eket a felső és alsó réteg eszközein. A modul üzembe helyezése függőben volt, amíg az IoT Edge konfigurálta az egyes eszközökre. A futtatás konfigurálása után megkezdődött az üzembe helyezés a legfelső rétegben **lévő eszközön.** Az üzembe helyezés befejezése  után az alsó rétegben található eszköz a IoT Edge **API-proxy modullal** le tudja majd tudni lekért szükséges rendszerképeket.

A [Azure Cloud Shell](https://shell.azure.com/)a felső rétegben található eszköz  üzembe helyezési JSON-ját, hogy megértse, milyen modulokat helyezett üzembe az eszközön:

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentTopLayer.json
   ```

Emellett a modulokat **IoT Edge Agent** és IoT Edge **Hub** modullal, a felső rétegbeli eszköz megkapja a **Docker** beállításjegyzék-modult és IoT Edge **API-proxy modult.** 

A **Docker beállításjegyzék-modul** egy meglévő adatbázisra Azure Container Registry. Ebben az esetben `REGISTRY_PROXY_REMOTEURL` a a következőre Microsoft Container Registry. A `createOptions` -ben látható, hogy az 5000-es porton kommunikál.

A **IoT Edge API-proxy** modul más modulokhoz is átkérte a HTTP-kéréseket, így az alsó réteg eszközei lehívják a tároló rendszerképeit, vagy blobokat küldő blobokat a tárolóba. Ebben az oktatóanyagban a 8000-es porton kommunikál, és úgy van konfigurálva, hogy Docker-tároló rendszerképének lekéréses kérelmek útvonalát az 5000-es porton küldje el a **Docker** beállításjegyzék-moduljának. Emellett minden Blob Storage-feltöltési kérelem az AzureBlobStorageonIoTEdge modulhoz irányít az 11002-es porton. A IoT Edge **API-proxy** modulról és annak konfigurálásról további információt a modul [útmutatója tartalmaz.](how-to-configure-api-proxy-module.md)

Ha szeretné megnézni, hogyan hozhat létre egy ehhez hasonló üzembe helyezést a Azure Portal vagy Azure Cloud Shell segítségével, tekintse meg az útmutató felső rétegben található eszközre [című szakaszát.](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-top-layer-devices)

A [Azure Cloud Shell](https://shell.azure.com/)az alsó rétegben található eszköz  üzembe helyezési JSON-ját, hogy megértse, milyen modulokat helyezett üzembe az eszközön:

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentLowerLayer.json
   ```

A alatt látható, hogy az alsó rétegben az eszköz futásidejű moduljai úgy vannak beállítva, hogy a helyett a rendszerből indulnak ki, ahogyan a `systemModules`  `$upstream:8000` felső `mcr.microsoft.com` **rétegben lévő eszköz** tette. Az **alsó rétegbeli eszköz** a 8000-es porton küldi a **Docker IoT Edge API-proxy** modult, mivel nem tudja közvetlenül le kérni a rendszerképeket a felhőből. Az alsó rétegben található eszközön üzembe helyezett másik modul **,** a **Simulated Temperature Sensor** modul szintén a következőnek teszi a képkérést: `$upstream:8000` .

Ha szeretné megnézni, hogyan hozhat létre egy ehhez hasonló üzembe helyezést a Azure Portal vagy Azure Cloud Shell segítségével, tekintse meg az útmutató alsó rétegben található eszközről [való szakaszát.](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-lower-layer-devices)

A modulok állapotát az paranccsal tudja megtekinteni:

   ```bash
   az iot hub module-twin show --device-id <edge_device_id> --module-id '$edgeAgent' --hub-name <iot_hub_name> --query "properties.reported.[systemModules, modules]"
   ```

   Ez a parancs az összes edgeAgent jelentett tulajdonságot ki fogja kimenetként kihozni. Íme néhány hasznos eszközállapot figyelése: futásidő *állapota,* futásidő kezdési *ideje,* futásidő utolsó kilépési ideje, *futásidő újraindításának száma.*

A modulok állapotát a következő oldalon is [Azure Portal.](https://ms.portal.azure.com/) Az eszközök IoT Edge modulok **IoT Hub** a saját eszközeit és moduljait.

Ha elégedett a modul üzemelő példányával, készen áll a folytatásra.

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

A **lekért Simulated Temperature Sensor** modul minta környezeti adatokat hoz létre. Olyan üzeneteket küld, amelyek tartalmazzák a környezeti hőmérsékletet és a páratartalmat, a gép hőmérsékletét és nyomását, valamint egy időbélyeget.

Ezeket az üzeneteket a következő [Azure Cloud Shell:](https://shell.azure.com/)

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="troubleshooting"></a>Hibaelhárítás

Futtassa `iotedge check` az parancsot a konfiguráció ellenőrzéséhez és a hibák elhárításához.

Akkor is futtathat beágyazott hierarchiában, ha a gyermekgépek nem férnek hozzá `iotedge check` közvetlen internet-hozzáféréssel.

Az alsó rétegből való futtatáskor a program a 443-as porton keresztül próbálja meg lekért rendszerképet a `iotedge check` szülőtől.

Ebben az oktatóanyagban a 8000-es portot használjuk, ezért meg kell adnunk:

```bash
sudo iotedge check --diagnostics-image-name $upstream:8000/azureiotedge-diagnostics:1.2
```

Az érték a tároló-beállításjegyzékből lesz lekért `azureiotedge-diagnostics` érték, amely a beállításjegyzék-modullal van összekapcsolva. Ez az oktatóanyag alapértelmezés szerint a következőre van beállítva: https://mcr.microsoft.com:

| Name | Érték |
| - | - |
| `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

Ha privát tárolójegyzéket használ, győződjön meg arról, hogy az összes rendszerkép (IoTEdgeAPIProxy, edgeAgent, edgeHub, Simulated Temperature Sensor és diagnostics) jelen van a tárolójegyzékben.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A díjak elkerülése érdekében törölheti a cikkben létrehozott helyi konfigurációkat és Azure-erőforrásokat.

Az erőforrások törlése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és válassza az **Erőforráscsoportok** elemet.

2. Válassza ki az IoT Edge teszterőforrásokat tartalmazó erőforráscsoport nevét. 

3. Ellenőrizze az erőforráscsoportban található erőforrások listáját. Ha mindet törölni szeretné, válassza az **Erőforráscsoport törlése** lehetőséget. Ha csak bizonyos elemeket szeretne törölni, az egyes erőforrásokra kattintva külön törölheti őket. 

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban két IoT Edge konfigurált átjáróként, és az egyiket a másik szülőeszközeként állította be. Ezután bemutatta, hogyan lehet tárolóképet lekért a gyermekeszközre egy átjárón keresztül a IoT Edge API-proxy modullal. További információért tekintse meg a [proxymodul](how-to-configure-api-proxy-module.md) használatával kapcsolatban a útmutatót.

Ha többet szeretne megtudni az átjárókról a IoT Edge-eszközök hierarchikus rétegeinek létrehozásához, tekintse meg az lefelé irányuló és IoT Edge összekötő [útmutatót.](how-to-connect-downstream-iot-edge-device.md)

A többi oktatóanyagra tovább lépve megtudhatja, hogyan lehet az Azure IoT Edge használatával egyéb üzleti megoldásokat létrehozni.

> [!div class="nextstepaction"]
> [Azure Machine Learning-modell üzembe helyezése modulként](tutorial-deploy-machine-learning.md)

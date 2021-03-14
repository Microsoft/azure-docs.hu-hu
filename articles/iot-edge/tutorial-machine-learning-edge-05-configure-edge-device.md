---
title: 'Oktatóanyag: Azure IoT Edge eszköz – gépi tanulás konfigurálása IoT Edge'
description: Ebben az oktatóanyagban egy Linux rendszerű Azure-beli virtuális gépet konfigurál egy transzparens átjáróként működő Azure IoT Edge eszközként.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/5/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: amqp, devx-track-azurecli
ms.openlocfilehash: b59f8343c9dff07a32accd471f70ddf9f5309b8d
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463085"
---
# <a name="tutorial-configure-an-azure-iot-edge-device"></a>Oktatóanyag: Azure IoT Edge-eszköz konfigurálása

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Ebben a cikkben egy Linux rendszerű Azure-beli virtuális gépet konfigurálunk olyan Azure IoT Edge eszközként, amely transzparens átjáróként működik. Egy transzparens átjáró konfigurációja lehetővé teszi az eszközök számára, hogy az átjárón keresztül csatlakozzanak az Azure IoT Hubhoz, és nem tudta, hogy az átjáró létezik. Ugyanakkor a IoT Hub eszközzel kommunikáló felhasználók nem ismerik a köztes átjáró eszközét. Végső soron az Edge Analytics szolgáltatást hozzáadjuk a rendszerhez, ha IoT Edge modulokat adunk hozzá az átlátszó átjáróhoz.

>[!NOTE]
>Az oktatóanyagban szereplő fogalmak a IoT Edge összes verziójára érvényesek, de a forgatókönyv kipróbálásához létrehozott minta-eszköz IoT Edge 1,1-es verzióban fut.

A cikkben ismertetett lépéseket általában egy felhőalapú fejlesztő hajtja végre.

Az oktatóanyag ezen szakaszában a következőket sajátíthatja el:

> [!div class="checklist"]
>
> * Hozzon létre tanúsítványokat, amelyek lehetővé teszik, hogy az átjáró eszköz biztonságosan kapcsolódjon az alsóbb rétegbeli eszközökhöz.
> * Hozzon létre egy IoT Edge eszközt.
> * Hozzon létre egy Azure-beli virtuális gépet, hogy szimulálja IoT Edge eszközét.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk egy sorozat részét képezi a Azure Machine Learning IoT Edge-on való használatáról szóló oktatóanyaghoz. A sorozat minden cikke az előző cikkben található munkára épül. Ha ezt a cikket közvetlenül megérkezett, tekintse meg az [első cikket](tutorial-machine-learning-edge-01-intro.md) a sorozatban.

## <a name="create-certificates"></a>Tanúsítványok létrehozása

Ahhoz, hogy egy eszköz átjáróként működjön, biztonságosan kell kapcsolódnia az alsóbb rétegbeli eszközökhöz. A IoT Edge a nyilvános kulcsokra épülő infrastruktúrát (PKI) is használhatja az eszközök közötti biztonságos kapcsolatok beállításához. Ebben az esetben lehetővé tesszük, hogy egy alsóbb rétegbeli IoT-eszköz egy transzparens átjáróként működő IoT Edge-eszközhöz kapcsolódjon. Az ésszerű biztonság fenntartása érdekében az alsóbb rétegbeli eszköznek meg kell erősítenie a IoT Edge eszköz identitását. További információ arról, hogy IoT Edge eszközök hogyan használják a tanúsítványokat: [Azure IoT Edge tanúsítvány-használati adatok](iot-edge-certs.md).

Ebben a szakaszban létrehozjuk az önaláírt tanúsítványokat egy olyan Docker-rendszerkép használatával, amelyet aztán kiépítünk és futtatunk. Úgy döntöttünk, hogy egy Docker-rendszerképet használunk ennek a lépésnek a végrehajtásához, mert csökkenti a Windows fejlesztői gépen lévő tanúsítványok létrehozásához szükséges lépések számát. Annak megismeréséhez, hogy mi automatizálható a Docker-lemezképpel, tekintse meg a [bemutató tanúsítványok létrehozása az IoT Edge eszköz funkcióinak teszteléséhez](how-to-create-test-certificates.md)című témakört.

1. Jelentkezzen be a fejlesztői virtuális gépre.
1. Hozzon létre egy új mappát a Path és a name **c:\edgeCertificates**.

1. Ha még nem fut, a Windows Start menüjéből indítsa el a **Docker for Windows** programot.

1. Nyissa meg a Visual Studio Code-ot.

1. Válassza ki a **fájl**  >  **megnyitása mappát**, majd válassza a **C: \\ Source \\ IoTEdgeAndMlSample \\ CreateCertificates** elemet.

1. Az **Explorer** ablaktáblán kattintson a jobb gombbal a **Docker** elemre, és válassza a **rendszerkép létrehozása** lehetőséget.

1. A párbeszédpanelen fogadja el az alapértelmezett értéket a rendszerkép neve és a címke: **createcertificates: Latest**.

    ![A tanúsítványok Visual Studio Code-ban való létrehozását bemutató képernyőkép.](media/tutorial-machine-learning-edge-05-configure-edge-device/create-certificates.png)

1. Várjon, amíg a Build befejeződik.

    > [!NOTE]
    > Előfordulhat, hogy megjelenik egy figyelmeztetés a hiányzó nyilvános kulcsról. Ezt a figyelmeztetést nyugodtan figyelmen kívül hagyhatja. Hasonlóképpen egy biztonsági figyelmeztetés is megjelenik, amely azt javasolja, hogy ellenőrizze vagy állítsa vissza a rendszerkép engedélyeit, ami nyugodtan figyelmen kívül hagyhatja ezt a rendszerképet.

1. A Visual Studio Code Terminal ablakban futtassa a createcertificates tárolót.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

1. A Docker kérni fogja a **c: \\** meghajtó elérését. Válassza a **megosztás** lehetőséget.

1. Ha a rendszer kéri, adja meg a hitelesítő adatait.

1. A tároló futásának befejeződése után keresse meg a következő fájlokat a **c: \\ edgeCertificates**:

    * c: \\ edgeCertificates \\ tanúsítványok \\ Azure-IOT-test-only. root. ca. CERT. PEM
    * c: \\ edgeCertificates \\ tanúsítványok \\ New-Edge-Device-Full-Chain. CERT. PEM
    * c: \\ edgeCertificates \\ tanúsítványok \\ New-Edge-Device. CERT. PEM
    * c: \\ edgeCertificates \\ tanúsítványok \\ New-Edge-Device. CERT. pfx
    * c: \\ edgeCertificates \\ Private \\ New-Edge-Device. key. PEM

## <a name="upload-certificates-to-azure-key-vault"></a>Tanúsítványok feltöltése a Azure Key Vaultba

A tanúsítványok biztonságos tárolásához és a több eszközről elérhetővé tételéhez feltöltjük a tanúsítványokat a Azure Key Vaultba. Ahogy az előző listából is látható, két típusú tanúsítványfájl létezik: PFX és PEM. A PFX-fájlt Key Vault, Key Vaultre feltöltött tanúsítványként kezeljük. A PEM-fájlok egyszerű szövegként szolgálnak, és Key Vault titokként kezeljük őket. A [Jupyter-jegyzetfüzetek](tutorial-machine-learning-edge-04-train-model.md#run-the-jupyter-notebooks)futtatásával létrehozott Azure Machine learning munkaterülethez társított Key Vault-példányt fogjuk használni.

1. A [Azure Portal](https://portal.azure.com)lépjen a Azure Machine learning munkaterületre.

1. A Machine Learning munkaterület áttekintés lapján keresse meg **Key Vault** nevét.

    ![A Key Vault nevének másolását bemutató képernyőkép.](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

1. A fejlesztői gépen töltse fel a tanúsítványokat Key Vaultba. Cserélje le az **\<subscriptionId\>** és **\<keyvaultname\>** az erőforrás adatait.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

1. Ha a rendszer kéri, jelentkezzen be az Azure-ba.

1. A szkript néhány percig fut, és az új Key Vault bejegyzéseket listázza.

    ![Key Vault szkript kimenetét bemutató képernyőkép.](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-an-iot-edge-device"></a>IoT Edge-eszköz létrehozása

Ha Azure IoT Edge eszközt szeretne csatlakoztatni egy IoT hubhoz, először létre kell hoznia egy identitást az eszközhöz a központban. A kapcsolódási karakterláncot a felhőben található eszköz-identitásból fogjuk használni, hogy a futtatókörnyezetet a IoT Edge eszközön konfigurálja. Ha egy konfigurált eszköz csatlakozik a központhoz, üzembe helyezhetünk modulokat és üzeneteket küldhetünk. A fizikai IoT Edge eszköz konfigurációját úgy is megváltoztathatja, hogy módosítja a megfelelő eszköz identitását IoT Hubban.

Ebben az oktatóanyagban létrehozjuk az új eszköz identitását a Visual Studio Code használatával. Ezeket a lépéseket a Azure Portal vagy az Azure CLI használatával is elvégezheti.

1. A fejlesztői gépen nyissa meg a Visual Studio Code-ot.

1. Bontsa ki az **Azure IoT hub** keretet a Visual Studio Code **Explorer** nézetből.

1. Kattintson a három pontra, majd válassza a **IoT Edge eszköz létrehozása** lehetőséget.

1. Adja meg az eszköz nevét. A kényelem érdekében a **aaTurbofanEdgeDevice** nevet használjuk, hogy az a felsorolt eszközök tetejére legyen rendezve.

1. Az új eszköz megjelenik az eszközök listájában.

    ![Képernyőkép, amely az eszköz nézetét jeleníti meg a Visual Studio Code Explorerben.](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-an-azure-virtual-machine"></a>Azure-beli virtuális gép üzembe helyezése

Az Azure Marketplace-en az Ubuntu-rendszerkép [Azure IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) az oktatóanyaghoz IoT Edge eszköz létrehozásához használjuk. Az Ubuntu-lemezkép Azure IoT Edge telepíti a legújabb IoT Edge futtatókörnyezetet és annak függőségeit az indításkor. A virtuális gépet a alábbiak használatával telepítjük:

- Egy PowerShell-parancsfájl `Create-EdgeVM.ps1` .
- Egy Azure Resource Manager sablon `IoTEdgeVMTemplate.json` .
- Egy rendszerhéj-parancsfájl `install packages.sh` .

### <a name="enable-programmatic-deployment"></a>Programozott telepítés engedélyezése

Ha az Azure Marketplace-en szeretné használni a rendszerképet egy parancsfájlból álló központi telepítésben, engedélyeznie kell a programozott üzembe helyezést a rendszerképhez.

1. Jelentkezzen be az Azure Portalra.

1. Válassza az **Összes szolgáltatás** elemet.

1. A keresősáv mezőben adja meg és válassza ki a **piactér** lehetőséget.

1. A piactéren keresse meg és válassza ki az **Ubuntu-Azure IoT Edge**.

1. A programozott módon történő üzembe helyezéshez válassza az **első lépések** hivatkozást.

1. Kattintson az **Engedélyezés** gombra, majd válassza a **Mentés** lehetőséget.

    ![A virtuális gépek programozott telepítésének engedélyezését bemutató képernyőkép.](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. Ekkor megjelenik egy sikeres értesítés.

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Ezután futtassa a parancsfájlt a IoT Edge eszközhöz tartozó virtuális gép létrehozásához.

1. Nyisson meg egy PowerShell-ablakot, és nyissa meg a **EdgeVM** könyvtárat.

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

1. Futtassa a szkriptet a virtuális gép létrehozásához.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

1. Ha a rendszer kéri, adja meg az egyes paraméterek értékét. Az előfizetés, az erőforráscsoport és a hely esetében javasoljuk, hogy ugyanazokat az értékeket használja, mint az oktatóanyag összes erőforrása esetében.

    * **Azure-előfizetés azonosítója**: a Azure Portal található.
    * **Erőforráscsoport neve**: emlékezetes név az oktatóanyag erőforrásainak csoportosításához.
    * **Hely**: az az Azure-hely, ahol a virtuális gép létre lesz hozva. Például: westus2 vagy northeurope. További információkért tekintse meg az összes [Azure-helyet](https://azure.microsoft.com/global-infrastructure/locations/).
    * **AdminUsername**: a virtuális gépre való bejelentkezéshez használt rendszergazdai fiók neve.
    * **AdminPassword**: a virtuális gép rendszergazdai felhasználónevéhez beállított jelszó.

1. Ahhoz, hogy a parancsfájl beállítsa a virtuális gépet, jelentkezzen be az Azure-ba az Ön által használt Azure-előfizetéshez társított hitelesítő adatokkal.

1. A parancsfájl megerősíti a virtuális gép létrehozásához szükséges információkat. Válassza az **y** vagy az **ENTER billentyűt** a folytatáshoz.

1. A szkript több percig fut, ahogy a következő lépéseket hajtja végre:

    * Létrehozza az erőforráscsoportot, ha még nem létezik
    * Létrehozza a virtuális gépet.
    * NSG-kivételeket biztosít a virtuális géphez a 22-es (SSH), a 5671 (AMQP), a 5672 (AMPQ) és a 443 (TLS) portokhoz.
    * Az [Azure CLI](/cli/azure/install-azure-cli-apt) telepítése

1. A parancsfájl az SSH kapcsolati karakterláncot adja meg a virtuális géphez való csatlakozáshoz. Másolja a következő lépéshez tartozó kapcsolatok karakterláncát.

    ![Képernyőkép, amely a virtuális gép SSH-kapcsolatok karakterláncának másolását mutatja be.](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Kapcsolódás a IoT Edge eszközhöz

A következő néhány szakaszban az általunk létrehozott Azure-beli virtuális gépet konfiguráljuk. Az első lépés a virtuális géphez való kapcsolódás.

1. Nyisson meg egy parancssort, és illessze be a szkript kimenetéről másolt SSH-kapcsolatok karakterláncát. Adja meg saját adatait a Felhasználónév, az utótag és a régió számára az előző szakaszban a PowerShell-szkriptnek megadott értékek alapján.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

1. Ha a rendszer felszólítja a gazdagép hitelességének ellenőrzésére, írja be az **Igen** értéket, és válassza az **ENTER billentyűt**.

1. Ha a rendszer kéri, adja meg a jelszavát.

1. Az Ubuntu egy üdvözlő üzenetet jelenít meg, és a következőhöz hasonló üzenetnek kell megjelennie: `<username>@<machinename>:~$` .

## <a name="download-key-vault-certificates"></a>Key Vault tanúsítványok letöltése

A cikk korábbi szakaszaiban a tanúsítványokat a Key Vault, hogy elérhetővé tegyék azokat a IoT Edge eszközön és a levélben lévő eszközön. A levél eszköz olyan alsóbb rétegbeli eszköz, amely az IoT Edge eszközt használja átjáróként a IoT Hub való kommunikációhoz.

Az oktatóanyagban később fogunk foglalkozni a levél eszközzel. Ebben a szakaszban a tanúsítványokat a IoT Edge eszközre kell letölteni.

1. Jelentkezzen be az Azure-ba az Azure CLI-vel a Linux rendszerű virtuális gépen futó SSH-munkamenetből.

    ```azurecli
    az login
    ```

1. A rendszer megkéri, hogy nyisson meg egy böngészőt egy [Microsoft-eszköz bejelentkezési](https://microsoft.com/devicelogin) oldalán, és adjon meg egy egyedi kódot. Ezeket a lépéseket a helyi gépen hajthatja végre. A hitelesítés befejezése után zárd be a böngészőablakot.

1. Sikeres hitelesítés esetén a Linux rendszerű virtuális gép bejelentkezik, és kilistázza az Azure-előfizetéseit.

1. Állítsa be az Azure CLI-parancsokhoz használni kívánt Azure-előfizetést.

    ```azurecli
    az account set --subscription <subscriptionId>
    ```

1. Hozzon létre egy könyvtárat a virtuális gépen a tanúsítványok számára.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Töltse le a Key vaultban tárolt tanúsítványokat: New-Edge-Device-Full-Chain. CERT. PEM, New-Edge-Device. key. PEM, és Azure-IOT-test-only. root. ca. CERT. PEM.

    ```azurecli
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>A IoT Edge eszköz konfigurációjának frissítése

Az IoT Edge Runtime a fájl/etc/iotedge/config.YAML használja a konfiguráció megőrzéséhez. A fájlban háromféle információt kell frissíteni:

* **Eszköz-összekapcsolási karakterlánc**: az eszköz identitásának IoT hub
* **Tanúsítványok**: az alsóbb rétegbeli eszközökkel létesített kapcsolatokhoz használandó tanúsítványok
* **Állomásnév**: a virtuális gép teljes TARTOMÁNYNEVE (FQDN) IoT Edge eszközön

A IoT Edge virtuális gép létrehozásához használt Ubuntu-rendszerkép Azure IoT Edge egy olyan rendszerhéj-parancsfájlt tartalmaz, amely frissíti a config. YAML fájlt a kapcsolódási karakterlánccal.

1. A Visual Studio Code-ban kattintson a jobb gombbal a IoT Edge eszközre, majd válassza az **eszköz csatlakoztatási karakterláncának másolása** lehetőséget.

    ![Képernyőkép, amely megjeleníti a kapcsolatok karakterláncának a Visual Studio Code-ból való másolását.](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

1. Az SSH-munkamenetben futtassa a parancsot, hogy frissítse a config. YAML fájlt az eszköz kapcsolati karakterláncával.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

A következő lépésben a tanúsítványokat és az állomásnevet a config. YAML fájl közvetlen szerkesztésével frissítjük.

1. Nyissa meg a config. YAML fájlt.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. A config. YAML fájl tanúsítványok szakaszának frissítésével távolítsa el a sortávolságot **#** , és állítsa be az elérési utat úgy, hogy a fájl a következő példához hasonlóan néz ki:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Győződjön meg arról, hogy a (z) **:** sor nem rendelkezik az előző üres hellyel, és hogy a beágyazott tanúsítványok mindegyike két szóközzel van behúzva.

    A nano-on a jobb gombbal kattintva beillesztheti a vágólap tartalmát az aktuális kurzor pozícióba. A karakterlánc cseréjéhez a nyílbillentyűk használatával lépjen a lecserélni kívánt karakterláncra, törölje a karakterláncot, majd kattintson a jobb gombbal a pufferből való beillesztéshez.

1. A Azure Portal nyissa meg a virtuális gépet. Másolja a DNS-nevet (a gép teljes tartománynevét) az **Áttekintés** szakaszból.

1. Illessze be a teljes tartománynevet a config. YML fájl állomásnév szakaszába. Győződjön meg arról, hogy a név csak kisbetűs.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

1. Mentse és zárda be a fájlt a **CTRL + X**, az **Y** billentyű kiválasztásával, majd **írja be** a parancsot.

1. Indítsa újra a IoT Edge démont.

    ```bash
    sudo systemctl restart iotedge
    ```

1. A IoT Edge démon állapotának bejelölése. A parancs után a kilépéshez írja be a következőt **: q** .

    ```bash
    systemctl status iotedge
    ```

1. Ha az állapotban hibaüzenetek jelennek meg (a "hiba" előtaggal ellátott színes szöveg), tekintse meg a hibákkal kapcsolatos \[ \] részletes információkat a démon naplóiban.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez az oktatóanyag egy olyan készlet része, amelyben az egyes cikkek az előzőekben végzett munkára épülnek. Várjon, amíg az összes erőforrást törölni szeretné, amíg el nem végzi a végleges oktatóanyagot.

## <a name="next-steps"></a>Következő lépések

Épp most fejezte be az Azure-beli virtuális gépek IoT Edge transzparens átjáróként való konfigurálását. Elkezdtük a Key Vaultba feltöltött tesztelési tanúsítványok generálását. Ezután egy parancsfájl-és Resource Manager-sablont használtunk a virtuális gép üzembe helyezéséhez az Ubuntu Server 16,04 LTS + Azure IoT Edge Runtime rendszerképpel az Azure Marketplace-ről. A virtuális géppel az SSH-n keresztül kapcsolódunk. Ezután bejelentkezett az Azure-ba, és letöltöttük a tanúsítványokat a Key Vaultból. A config. YAML fájl frissítésével számos frissítést készítettünk a IoT Edge futtatókörnyezet konfigurációjában.

IoT Edge modulok létrehozásához folytassa a következő cikkel.

> [!div class="nextstepaction"]
> [Egyéni IoT Edge-modulok létrehozása és üzembe helyezése](tutorial-machine-learning-edge-06-custom-modules.md)

---
title: 'Oktatóanyag: Azure IoT Edge konfigurálása – Gépi tanulás a IoT Edge'
description: Ebben az oktatóanyagban egy Linuxot futtató Azure-beli virtuális gépet fog konfigurálni transzparens átjáróként Azure IoT Edge eszközként.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/5/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: amqp
ms.openlocfilehash: 65fd6e5b4d494f8e8486d72079b9fa97a175894b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376885"
---
# <a name="tutorial-configure-an-azure-iot-edge-device"></a>Oktatóanyag: Azure IoT Edge konfigurálása

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Ebben a cikkben egy Linuxot futtató Azure-beli virtuális gépet konfigurálunk transzparens átjáróként Azure IoT Edge eszközként. A transzparens átjárókonfigurációval az eszközök anélkül Azure IoT Hub, hogy tudnák, hogy az átjáró létezik. Ugyanakkor a felhőben található eszközökkel kommunikáló IoT Hub nem tud a köztes átjáróeszközről. Végső soron peremhálózati elemzéseket fogunk hozzáadni a rendszerhez úgy, hogy IoT Edge modulokat a transzparens átjáróhoz.

>[!NOTE]
>Az oktatóanyagban található fogalmak a IoT Edge összes verziójára vonatkoznak, de a forgatókönyv kipróbálához létrehozott mintaeszköz az 1.1-es IoT Edge fut.

A cikkben található lépéseket általában egy felhőalapú fejlesztő végzi el.

Az oktatóanyag ezen szakaszában a következővel ismerkedik meg:

> [!div class="checklist"]
>
> * Hozzon létre tanúsítványokat, hogy az átjáróeszköz biztonságosan kapcsolódjon az lefelé irányuló eszközökhöz.
> * Hozzon létre IoT Edge eszközt.
> * Hozzon létre egy Azure-beli virtuális gépet a IoT Edge szimulálására.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk egy oktatóanyag-sorozat része, amely a Azure Machine Learning a IoT Edge. A sorozat minden cikke az előző cikkben lekért munkára épül. Ha közvetlenül ezzel a cikkel érkezett, tekintse meg [a sorozat](tutorial-machine-learning-edge-01-intro.md) első cikkét.

## <a name="create-certificates"></a>Tanúsítványok létrehozása

Ahhoz, hogy egy eszköz átjáróként működn, biztonságosan kell csatlakoznia az lefelé irányuló eszközökhöz. A IoT Edge nyilvános kulcsú infrastruktúrával (PKI) állíthat be biztonságos kapcsolatokat az eszközök között. Ebben az esetben engedélyezünk egy lefelé irányuló IoT-eszközt egy transzparens átjáróként IoT Edge eszközhöz való csatlakozáshoz. Az ésszerű biztonság fenntartása érdekében az lefelé irányuló eszköznek meg kell erősítenie a IoT Edge identitását. További információ arról, hogy az IoT Edge hogyan használják a tanúsítványokat: Azure IoT Edge [tanúsítványhasználat részletei.](iot-edge-certs.md)

Ebben a szakaszban az önaírt tanúsítványokat egy Docker-rendszerkép használatával létrehozására és futtatására használjuk. Úgy döntöttünk, hogy egy Docker-rendszerképet használunk a lépés létrehozására, mivel ez csökkenti a tanúsítványok windowsos fejlesztői gépen való létrehozásához szükséges lépések számát. A Docker-rendszerkép automatizálásával kapcsolatos további adatokat lásd: Bemutató tanúsítványok létrehozása az [IoT Edge teszteléséhez.](how-to-create-test-certificates.md)

1. Jelentkezzen be a fejlesztői virtuális gépre.
1. Hozzon létre egy új mappát a **c:\edgeCertificates elérési út és névvel.**

1. Ha még nem fut, indítsa el a **Docker for Windowst a** Windows Start menü.

1. Nyissa meg a Visual Studio Code-ot.

1. Válassza **a Fájl** megnyitása  >  **mappát,** majd a **C: forrás \\ \\ IoTEdgeAndMlSample \\ CreateCertificates lehetőséget.**

1. Az **Explorer panelen** kattintson a jobb gombbal a **dockerfile elemre,** és válassza a **Rendszerkép összeállítása lehetőséget.**

1. A párbeszédpanelen fogadja el a kép neve és címkéje alapértelmezett értékét: **createcertificates: latest**.

    ![Képernyőkép a tanúsítványok létrehozásáról a Visual Studio kódban.](media/tutorial-machine-learning-edge-05-configure-edge-device/create-certificates.png)

1. Várjon, amíg a build befejeződik.

    > [!NOTE]
    > Előfordulhat, hogy egy figyelmeztetés jelenik meg egy hiányzó nyilvános kulcsról. Ezt a figyelmeztetést biztonságosan figyelmen kívül hagyhatja. Ehhez hasonlóan egy biztonsági figyelmeztetés jelenik meg, amely azt javasolja, hogy ellenőrizze vagy állítsa alaphelyzetbe a lemezkép engedélyét, amelyet a rendszerképhez biztonságosan figyelmen kívül hagyhat.

1. A Visual Studio Code terminálablakában futtassa a createcertificates tárolót.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

1. A Docker kérni fogja a **c: meghajtóhoz \\ való hozzáférést.** Válassza **a Megosztás lehetőséget.**

1. Amikor a rendszer kéri, adja meg a hitelesítő adatait.

1. Miután a tároló futtatása befejeződik, ellenőrizze a következő fájlokat a **c-ben: \\ edgeCertificates:**

    * c: \\ edgeCertificates \\ certs \\ azure-iot-test-only.root.ca.cert.pem
    * c: \\ edgeCertificates \\ certs \\ new-edge-device-full-chain.cert.pem
    * c: \\ edgeCertificates \\ certs \\ new-edge-device.cert.pem
    * c: \\ edgeCertificates \\ certs \\ new-edge-device.cert.pfx
    * c: \\ edgeCertificates \\ private \\ new-edge-device.key.pem

## <a name="upload-certificates-to-azure-key-vault"></a>Tanúsítványok feltöltése Azure Key Vault

A tanúsítványok biztonságos tárolása és több eszközről való elérhetővé tétele érdekében feltöltjük a tanúsítványokat a Azure Key Vault. Ahogy az előző listában láthatja, két tanúsítványfájltípusunk van: PFX és PEM. A PFX-fájlt a Key Vault feltöltni kívánt tanúsítványokként fogjuk Key Vault. A PEM-fájlok egyszerű szövegek, és titkos kulcsokként Key Vault őket. A Jupyter notebookok Azure Machine Learning munkaterülethez társított [Key Vault-példányt fogjuk használni.](tutorial-machine-learning-edge-04-train-model.md#run-the-jupyter-notebooks)

1. A [Azure Portal](https://portal.azure.com)a munkaterületre Azure Machine Learning.

1. A munkaterület áttekintési Machine Learning keresse meg a **Key Vault.**

    ![A kulcstartó nevének másolását bemutató képernyőkép.](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

1. A fejlesztői gépen töltse fel a tanúsítványokat a Key Vault. Cserélje le **\<subscriptionId\>** a és **\<keyvaultname\>** a helyére az erőforrás adatait.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

1. Ha a rendszer kéri, jelentkezzen be az Azure-ba.

1. A szkript néhány percig futni fog, és a kimenet felsorolja az új Key Vault bejegyzéseket.

    ![Képernyőkép a Key Vault kimenetről.](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-an-iot-edge-device"></a>IoT Edge-eszköz létrehozása

A Azure IoT Edge IoT Hubhoz való csatlakoztatásához először létre kell hozni egy identitást az eszközhöz a központban. A felhőben található eszközidentitás kapcsolati sztringjéről a használatával konfiguráljuk a IoT Edge eszközén. Miután egy konfigurált eszköz csatlakozott a központhoz, modulokat helyezhetünk üzembe és üzeneteket küldhetünk. A fizikai eszköz konfigurációját úgy is módosíthatjuk IoT Edge hogy módosítjuk a megfelelő eszközidentitást a IoT Hub.

Ebben az oktatóanyagban az új eszközidentitást az Visual Studio használjuk. Ezeket a lépéseket az Azure CLI vagy a Azure Portal is végre tudja végrehajtani.

1. A fejlesztői gépen nyissa meg a Visual Studio Code-et.

1. Bontsa **Azure IoT Hub** a Visual Studio Code **Explorer nézetből.**

1. Kattintson a három pontra, majd a **Create IoT Edge Device (Eszköz létrehozása) gombra.**

1. Nevezze el az eszközt. Az egyszerűség kedvéért az **aaTurbofanEdgeDevice** nevet használjuk, hogy az a felsorolt eszközök tetejére legyen rendezve.

1. Az új eszköz megjelenik az eszközök listájában.

    ![Képernyőkép az eszköz a Code Explorerben Visual Studio nézetéről.](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-an-azure-virtual-machine"></a>Azure-beli virtuális gép üzembe helyezése

Ebben az oktatóanyagban Azure IoT Edge a Azure Marketplace [ubuntus](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) rendszerképet használjuk IoT Edge eszköz létrehozásához. Az Azure IoT Edge Ubuntu-rendszerképen a legújabb IoT Edge a runtime és annak függőségei az indításkor. A virtuális gépet a következővel helyezheti üzembe:

- Egy PowerShell-szkript( `Create-EdgeVM.ps1` ).
- Egy Azure Resource Manager sablon, `IoTEdgeVMTemplate.json` .
- Egy héjszk `install packages.sh` szkript.

### <a name="enable-programmatic-deployment"></a>Programozott központi telepítés engedélyezése

A parancsprogrammal Azure Marketplace központi telepítésben használt rendszerképet programozott központi telepítéssel kell engedélyezni.

1. Jelentkezzen be az Azure Portalra.

1. Válassza az **Összes szolgáltatás** elemet.

1. A keresősávba írja be a következőt: , majd válassza a **Marketplace lehetőséget.**

1. A Marketplace keresősávjában írja be a következőt: , majd válassza Azure IoT Edge **Ubuntun.**

1. A **programozott üzembe helyezéshez** válassza az Első lépések hivatkozást.

1. Válassza az **Engedélyezés gombot,** majd a Mentés **lehetőséget.**

    ![Képernyőkép egy virtuális gép programozott központi telepítésének engedélyezéséről.](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. Megjelenik egy sikerességről szóló értesítés.

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Ezután futtassa a szkriptet a virtuális gép létrehozásához a IoT Edge eszközhöz.

1. Nyisson meg egy PowerShell-ablakot, és nyissa meg az **EdgeVM könyvtárat.**

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

1. Futtassa a szkriptet a virtuális gép létrehozásához.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

1. Amikor a rendszer kéri, adja meg az egyes paraméterek értékeit. Előfizetés, erőforráscsoport és hely esetén javasoljuk, hogy az oktatóanyag során minden erőforráshoz ugyanazt az értéket használja, mint ön.

    * **Azure-előfizetés azonosítója:** A következő Azure Portal.
    * **Erőforráscsoport neve:** Könnyen megjegyezhető név az oktatóanyag erőforrásainak csoportosításhoz.
    * **Hely:** Az Azure-hely, ahol a virtuális gép létre lesz hozva. Például westus2 vagy northeurope. További információkért tekintse meg az [összes Azure-helyet.](https://azure.microsoft.com/global-infrastructure/locations/)
    * **AdminUsername**: A virtuális gépre való bejelentkezéshez használt rendszergazdai fiók neve.
    * **AdminPassword:** A virtuális gépen a rendszergazdai felhasználónévhez beállított jelszó.

1. Ahhoz, hogy a szkript be tudja állítani a virtuális gépet, jelentkezzen be az Azure-ba a használt Azure-előfizetéshez társított hitelesítő adatokkal.

1. A szkript megerősíti a virtuális gép létrehozásához szükséges információkat. A **folytatáshoz válassza az y** vagy **az Enter** lehetőséget.

1. A szkript néhány percig fut a következő lépések végrehajtásakor:

    * Létrehozza az erőforráscsoportot, ha még nem létezik
    * Létrehozza a virtuális gépet
    * NSG-kivételek hozzáadása a virtuális géphez a 22-es (SSH-), az 5671-es (AMQP), az 5672-es (AMPQ) és a 443-as (TLS) portokhoz
    * Az [Azure CLI telepítése](/cli/azure/install-azure-cli-apt)

1. A szkript kiíratja az SSH kapcsolati sztringet a virtuális géphez való csatlakozáshoz. Másolja ki a kapcsolati sztringet a következő lépéshez.

    ![A virtuális gép SSH-kapcsolati sztring másolását bemutató képernyőkép.](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Csatlakozás a IoT Edge eszközhöz

A következő néhány szakasz a létrehozott Azure-beli virtuális gépet konfigurálja. Az első lépés a virtuális géphez való csatlakozás.

1. Nyisson meg egy parancssort, és illessze be a szkript kimenetében kimásolt SSH kapcsolati sztringet. Adja meg saját felhasználónevét, utótagját és régióját az előző szakaszban a PowerShell-szkripthez megadott értékek alapján.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

1. Amikor a rendszer kéri, hogy ellenőrizze a gazdagép hitelességét, írja be az **igen,** majd az **Enter billentyűt.**

1. Amikor a rendszer kéri, adja meg a jelszavát.

1. Az Ubuntu üdvözlőüzenetet jelenít meg, és ekkor egy hasonló üzenet jelenik `<username>@<machinename>:~$` meg.

## <a name="download-key-vault-certificates"></a>Az Key Vault letöltése

A cikk korábbi verzióiban tanúsítványokat töltöttünk fel a Key Vault, hogy elérhetővé tehetők IoT Edge eszközünk és levéleszközünk számára. A levéleszköz egy lefelé irányuló eszköz, amely a IoT Edge eszközt átjáróként használja a IoT Hub.

A levéleszközről az oktatóanyag későbbi része lesz szó. Ebben a szakaszban töltse le a tanúsítványokat a IoT Edge eszközre.

1. Jelentkezzen be az Azure-ba a Linux rendszerű virtuális gép SSH-munkamenetében az Azure CLI használatával.

    ```azurecli
    az login
    ```

1. A rendszer arra kéri, hogy nyisson meg egy böngészőt egy [Microsoft-eszköz](https://microsoft.com/devicelogin) bejelentkezési oldalán, és adjon meg egy egyedi kódot. Ezeket a lépéseket a helyi gépen is végrehajthatja. Ha végzett a hitelesítéssel, zárja be a böngészőablakot.

1. A sikeres hitelesítés után a Linux rendszerű virtuális gép bejelentkezik, és listába sorolja az Azure-előfizetéseket.

1. Állítsa be az Azure CLI-parancsokhoz használni kívánt Azure-előfizetést.

    ```azurecli
    az account set --subscription <subscriptionId>
    ```

1. Hozzon létre egy könyvtárat a virtuális gépen a tanúsítványok számára.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Töltse le a kulcstartóban tárolt tanúsítványokat: new-edge-device-full-chain.cert.pem, new-edge-device.key.pem és azure-iot-test-only.root.ca.cert.pem.

    ```azurecli
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>Az IoT Edge eszköz konfigurációjának frissítése

A IoT Edge az /etc/iotedge/config.yaml fájlt használja a konfiguráció megőrzéséhez. Három információt kell frissítenünk ebben a fájlban:

* **Eszközkapcsolati sztring:** Az eszköz identitásának kapcsolati sztringe a IoT Hub
* **Tanúsítványok:** Az lefelé irányuló eszközökkel való kapcsolatokhoz használt tanúsítványok
* **Állomásnév:** A virtuális gép teljes tartományneve (FQDN) IoT Edge eszközön

A IoT Edge virtuális gép létrehozásához használt Azure IoT Edge-lemezképhez egy rendszerhéjszkent is található, amely frissíti a config.yaml fájlt a kapcsolati sztring használatával.

1. A Visual Studio code (Kód) menüben kattintson a jobb gombbal az IoT Edge eszközre, majd válassza az Copy Device Connection String (Eszközkapcsolati sztring **másolása) lehetőséget.**

    ![A kapcsolati sztring kódból való másolását Visual Studio bemutató képernyőkép.](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

1. Az SSH-munkamenetben az paranccsal frissítse a config.yaml fájlt az eszköz kapcsolati sztringjére.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

A következő lépés a tanúsítványok és az állomásnév frissítése a config.yaml fájl közvetlen szerkesztésével.

1. Nyissa meg a config.yaml fájlt.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Frissítse a config.yaml fájl tanúsítványok szakaszát a kezdő és az elérési út eltávolításával, hogy a fájl az **#** alábbi példához hasonlítsa:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Győződjön meg arról, hogy a **tanúsítványok sorában** nincs üres terület, és hogy a beágyazott tanúsítványok mindegyikét két szóköz behúzása tartalmazza.

    Ha a jobb gombbal a nano elemre kattint, a vágólap tartalmát beilleszti az aktuális kurzor helyére. A sztring cseréjéhez használja a billentyűzet nyilat a lecserélni kívánt sztringre való ugráshoz, törölje a sztringet, majd kattintson a jobb gombbal a pufferből való beillesztéshez.

1. A Azure Portal a virtuális géphez. Másolja ki a DNS-nevet (a gép teljes tartománynevét) az **Áttekintés szakaszból.**

1. Illessze be az FQDN-t a config.yml fájl állomásnév szakaszába. Győződjön meg arról, hogy a név csak kisbetűkből áll.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

1. Mentse és zárja be a fájlt a **Ctrl+X,** **az Y** és az **Enter billentyűkombinációval.**

1. Indítsa újra IoT Edge démont.

    ```bash
    sudo systemctl restart iotedge
    ```

1. Ellenőrizze a IoT Edge állapotát. A parancs után írja be a **:q parancsot a** kilépéshez.

    ```bash
    systemctl status iotedge
    ```

1. Ha hibákat lát (az "ERROR" előtaggal előtagú színes szöveg) az állapotban, vizsgálja meg a démonnaplókat a hiba részletes \[ \] információiért.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez az oktatóanyag annak a halmaznak a része, amelyben minden cikk az előzőkben végzett munkára épül. Várjon, amíg az utolsó oktatóanyag be nem fejeződik, és minden erőforrást megtisztít.

## <a name="next-steps"></a>Következő lépések

Az előbb fejeztünk be egy Azure-beli virtuális gép konfigurálását IoT Edge transzparens átjáróként. Elsőként a teszttanúsítványokat hoztunk Key Vault. Ezután egy szkriptet és egy Resource Manager-sablonnal üzembe helyeztünk egy virtuális gépet az Ubuntu Server 16.04 LTS + Azure IoT Edge-rendszerképpel a Azure Marketplace. Most, hogy a virtuális gép működik, SSH-n keresztül csatlakoztunk. Ezután bejelentkeztünk az Azure-ba, és letöltöttük a tanúsítványokat a Key Vault. Több frissítést is frissítettünk a IoT Edge konfigurációjában a config.yaml fájl frissítésével.

Folytassa a következő cikkel, és készítsen IoT Edge modulokat.

> [!div class="nextstepaction"]
> [Egyéni modul létrehozása és IoT Edge üzembe](tutorial-machine-learning-edge-06-custom-modules.md)

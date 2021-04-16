---
title: Eszköz kiépítése virtuális TPM-hez Linux rendszerű virtuális gépen – Azure IoT Edge
description: Szimulált TPM használata Linux rendszerű virtuális gépen az Azure Device Provisioning Service teszteléséhez Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/09/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 79fe8acd06084c58b0cf9b47bf93e933c648510c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481990"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-tpm-on-linux"></a>TPM-IoT Edge létrehozása és kiépítése Linux rendszeren

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Ez a cikk bemutatja, hogyan tesztelje az automatikus kiépítést linuxos IoT Edge egy virtuális platformmegbízhatósági modul (TPM) használatával. A Device Provisioning Service Azure IoT Edge automatikusan [kiépítheti](../iot-dps/index.yml)a következő eszközöket: . Ha nem ismeri az automatikus kiépítés folyamatát, a folytatás előtt tekintse át a [kiépítés](../iot-dps/about-iot-dps.md#provisioning-process) áttekintését.

A feladatok a következők:

1. Hozzon létre egy Linux rendszerű virtuális gépet (VM) a Hyper-V-ban szimulált platformmegbízhatósági modul (TPM) a hardverbiztonság érdekében.
1. Hozza létre a IoT Hub Device Provisioning Service (DPS) egy példányát.
1. Egyéni regisztráció létrehozása az eszközhöz.
1. Telepítse a IoT Edge, és csatlakoztassa az eszközt a IoT Hub.

> [!TIP]
> Ez a cikk azt ismerteti, hogyan tesztelheti a DPS-kiépítést egy TPM-szimulátorral, de a legtöbb olyan fizikai TPM-hardverre vonatkozik, mint az [Infineon OPTIGA &trade; TPM,](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board)amely egy Azure Certified for IoT-eszköz.
>
> Ha fizikai eszközt használ, ugorjon [a](#retrieve-provisioning-information-from-a-physical-device) jelen cikk Kiépítési adatok lekérése fizikai eszközről című szakasza elé.

## <a name="prerequisites"></a>Előfeltételek

* Windows rendszerű fejlesztői gép, engedélyezett [Hyper-V-vel.](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) Ez a cikk ubuntu Windows 10 virtuális gép futtatását használja.
* Aktív IoT Hub.

> [!NOTE]
> A TPM 2.0-ra akkor van szükség, ha TPM-igazolást használ a DPS-sel, és csak egyéni, nem csoportos regisztrációk létrehozására használható.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Linux rendszerű virtuális gép létrehozása virtuális TPM-et használva

Ebben a szakaszban egy új Linux rendszerű virtuális gépet hoz létre a Hyper-V-vel. Ezt a virtuális gépet egy szimulált TPM-modul segítségével konfigurálhatja annak tesztelésére, hogyan működik az automatikus kiépítés a IoT Edge.

### <a name="create-a-virtual-switch"></a>Virtuális kapcsoló létrehozása

A virtuális kapcsolók lehetővé teszik, hogy a virtuális gép csatlakozzon egy fizikai hálózathoz.

1. Nyissa meg a Hyper-V kezelőt a Windows rendszerű gépen.

2. A Műveletek **menüben** válassza a **Virtuáliskapcsoló-kezelő lehetőséget.**

3. Válasszon egy **külső virtuális** kapcsolót, majd válassza a Virtuális kapcsoló **létrehozása lehetőséget.**

4. Nevezze el az új virtuális kapcsolót, **például: EdgeSwitch.** Győződjön meg arról, hogy a kapcsolat típusa Külső **hálózat,** majd kattintson az **OK gombra.**

5. Egy előugró ablak figyelmezteti, hogy megszakadhat a hálózati kapcsolat. Válassza az **Igen** lehetőséget a folytatáshoz.

Ha hibákat lát az új virtuális kapcsoló létrehozásakor, győződjön meg arról, hogy más kapcsolók nem használják az Ethernet-adaptert, és hogy más kapcsolók ne használják ugyanazt a nevet.

### <a name="create-virtual-machine"></a>Virtuális gép létrehozása

1. Töltsön le egy lemezképfájlt a virtuális géphez való használathoz, és mentse helyileg. Például: [Ubuntu Server 18.04](http://releases.ubuntu.com/18.04/). További információ a támogatott operációs rendszerekről IoT Edge támogatott [Azure IoT Edge.](support.md)

2. A Hyper-V kezelőben válassza ismét **a Műveletek menü Új** virtuális gép  >    >   **művelete** parancsát.

3. Az Új **virtuális gép varázslóban töltse** ki a következő konfigurációkat:

   1. **Generáció megadása:** Válassza a **2. generáció lehetőséget.** A 2. generációs virtuális gépeken engedélyezve van a beágyazott virtualizálás, amely a virtuális IoT Edge futtatásához szükséges.
   2. **Hálózat konfigurálása:** Állítsa be a **Kapcsolat** értékét az előző szakaszban létrehozott virtuális kapcsolóhoz.
   3. **Telepítési beállítások:** Válassza az Operációs rendszer telepítése rendszerindító lemezképfájlból lehetőséget, és keresse meg **a** helyileg mentett lemezképfájlt.

4. A **virtuális gép** létrehozásához kattintson a varázsló Befejezés gombra.

Az új virtuális gép létrehozása eltarthat néhány percig.

### <a name="enable-virtual-tpm"></a>Virtuális TPM engedélyezése

A virtuális gép létrehozása után nyissa meg a beállításait, hogy engedélyezze a virtuális platform platformmodulját (TPM), amellyel automatikusan kiépítheti az eszközt.

1. A Hyper-V kezelőben kattintson a jobb gombbal a virtuális gépre, és válassza a **Beállítások lehetőséget.**

2. Lépjen a **Biztonság lapra.**

3. Törölje a **jelölést a Biztonságos rendszerindítás engedélyezése jelölőnégyzetből.**

4. Jelölje be **a Platformmegbízhatósági modul** jelölőnégyzetet.

5. Kattintson az **OK** gombra.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Indítsa el a virtuális gépet, és gyűjtse be a TPM-adatokat

A virtuális gépen készítsen egy eszközt, amely lekéri  az eszköz regisztrációs azonosítóját és **ellenőrzőkulcsát.**

1. A Hyper-V kezelőben indítsa el a virtuális gépet, és csatlakozzon hozzá.

1. Kövesse a virtuális gépen megjelenő utasításokat a telepítési folyamat befejezéséhez és a gép újraindításához.

1. Jelentkezzen be a virtuális gépre, majd kövesse [a Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) fejlesztési környezet beállítása a C-hez készült Azure IoT eszközoldali SDK telepítéséhez és felépítéséhez szükséges lépéseket.

   >[!TIP]
   >A cikk során átmásolja és beilleszti a virtuális gépet, ami a Hyper-V kezelője kapcsolati alkalmazáson keresztül nem egyszerű. Előfordulhat, hogy az IP-címének lekérése után egyszer a Hyper-V kezelőn keresztül szeretne csatlakozni a virtuális géphez. Először futtassa `sudo apt install net-tools` a, majd a et. `hostname -I` Ezután az IP-címmel csatlakozhat SSH-kapcsolaton keresztül: `ssh <username>@<ipaddress>` .

1. Futtassa a következő parancsokat az SDK-eszköz felépítéséhez, amely lekéri az eszköz kiépítési adatait a TPM-ről.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. A kimeneti ablakban megjelenik az eszköz regisztrációs **azonosítója** és **ellenőrzőkulcsa.** Másolja ki ezeket az értékeket későbbi használatra, amikor egyéni regisztrációt hoz létre az eszközhöz.

Ha már megvan a regisztrációs azonosító és az ellenőrzőkulcs, folytassa a Következő szakasz [IoT Hub Device Provisioning Service](#set-up-the-iot-hub-device-provisioning-service)

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Kiépítési információk lekérése fizikai eszközről

Ha virtuális gép helyett fizikai IoT Edge használ, készítsen egy eszközt, amely lekéri az eszköz kiépítési adatait.

1. Kövesse a [Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) fejlesztési környezet beállítása a C-hez készült Azure IoT eszközoldali SDK telepítéséhez és felépítéséhez való lépéseit.

1. Futtassa a következő parancsokat az SDK eszköz felépítéséhez, amely lekéri az eszköz kiépítési adatait a TPM-eszközről.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Másolja ki a Regisztrációs **azonosító és** az **Ellenőrzőkulcs értékeit.** Ezekkel az értékekkel egyéni regisztrációt hozhat létre az eszközéhez a DPS-ben.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>A IoT Hub Device Provisioning Service

Hozzon létre egy új azure IoT Hub Device Provisioning Service példányt, és csatolja az IoT Hubhoz. Kövesse a Következő útmutató [utasításait: Set up the IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).

Miután futtatta a Device Provisioning Service-t, másolja ki az Azonosító hatóköre **értéket** az áttekintő lapról. Ezt az értéket akkor használja, amikor a IoT Edge konfigurálja.

## <a name="create-a-dps-enrollment"></a>DPS-regisztráció létrehozása

Lekéri a kiépítési adatokat a virtuális gépről, és a használatával egyéni regisztrációt hozhat létre a Device Provisioning Service-ban.

Amikor létrehoz egy regisztrációt a DPS-ben, lehetősége van kezdeti ikerállapot **deklarálására.** Az ikereszközben beállíthatja, hogy a címkék a megoldásban szükséges metrikák (például régió, környezet, hely vagy eszköztípus) alapján csoportosítják az eszközöket. Ezek a címkék az automatikus [központi telepítések létrehozására használhatók.](how-to-deploy-at-scale.md)

> [!TIP]
> Az Azure CLI-ben létrehozhat [](/cli/azure/iot/dps/enrollment) egy regisztrációt, és az **edge-enabled** jelzővel megadhatja, hogy az eszköz egy IoT Edge eszköz.

1. A [Azure Portal](https://portal.azure.com)nyissa meg a IoT Hub Device Provisioning Service.

2. A **Beállítások alatt** válassza a **Regisztrációk kezelése lehetőséget.**

3. Válassza **az Egyéni regisztráció hozzáadása lehetőséget,** majd a regisztráció konfiguráláshoz kövesse az alábbi lépéseket:  

   1. A **Mechanizmus mezőben** válassza a **TPM lehetőséget.**

   2. Adja meg a  **virtuális gépről** kimáselt ellenőrzőkulcsot és regisztrációs azonosítót.

      > [!TIP]
      > Ha fizikai TPM-eszközt használ, meg kell határoznia az ellenőrzőkulcsot, amely minden TPM-lapka egyedi, és amelyet a hozzá társított TPM-lapkagyártótól szerez be. A TPM-eszköz egyedi regisztrációs azonosítóját származtathatja például az ellenőrzőkulcs SHA-256 kivonatának létrehozásával. 

   3. Ha szeretné, adja meg az eszköz azonosítóját. Ha nem ad meg eszközazonosítót, a rendszer a regisztrációs azonosítót használja.

   4. Az **Igaz lehetőség** kiválasztásával deklaráljuk, hogy ez a virtuális gép IoT Edge eszköz.

   5. Válassza ki azt a IoT Hub, amelyekhez csatlakoztatni szeretné az eszközt, vagy válassza a **Csatolás új** IoT Hub. Több központot is kiválaszthat, és az eszköz a kiválasztott hozzárendelési szabályzatnak megfelelően hozzá lesz rendelve ezek egyikéhez.

   6. Ha szeretné, adjon hozzá egy címkeértéket az **Ikereszköz** kezdeti állapotához. Címkék használatával eszközcsoportokat célozhat meg a modulok üzembe helyezéséhez. További információ: [IoT Edge üzembe helyezése nagy méretekben.](how-to-deploy-at-scale.md)

   7. Kattintson a **Mentés** gombra.

Most, hogy már létezik regisztráció ehhez az eszközhöz, a IoT Edge automatikusan kiépítheti az eszközt a telepítés során.

## <a name="install-the-iot-edge-runtime"></a>Az IoT Edge-futtatókörnyezet telepítése

Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön üzembe van helyezve. Az összetevői tárolókban futnak, és lehetővé teszik további tárolók üzembe helyezését az eszközön, hogy kódokat futtathat a peremhálózaton. Telepítse a IoT Edge a virtuális gépre.

Kövesse az [Install the Azure IoT Edge runtime](how-to-install-iot-edge.md)(Az eszköz telepítése) Azure IoT Edge, majd térjen vissza ehhez a cikkhez az eszköz üzembe lépéséhez.

## <a name="configure-the-device-with-provisioning-information"></a>Az eszköz konfigurálása kiépítési információkkal

Miután telepítette a futásidőt az eszközön, konfigurálja az eszközt a Device Provisioning Service-hez való csatlakozáshoz használt információkkal, és IoT Hub.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Az előző szakaszokban összegyűjtött  **DPS-azonosító hatókörének** és eszközregisztrációs azonosítójának személyazonossága.

1. Nyissa meg a konfigurációs fájlt a IoT Edge eszközön.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Keresse meg a fájl kiépítési konfigurációk szakaszát. Tegye megjegyzésbe a TPM-kiépítés sorait, és mindenképpen tegye megjegyzésbe a többi kiépítési sort.

   A sornak nem szabad megelőző szóközt tartalmazni, a beágyazott elemeket pedig két `provisioning:` szóközt kell behúzottként behúzottként.

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "tpm"
       registration_id: "<REGISTRATION_ID>"
   # always_reprovision_on_startup: true
   # dynamic_reprovisioning: false
   ```

1. Frissítse a és a értékét a DPS és az `scope_id` eszköz `registration_id` adataival.

1. Ha szükséges, a vagy a sorokkal konfigurálhatja az eszköz `always_reprovision_on_startup` `dynamic_reprovisioning` újrabontási viselkedését. Ha egy indításkor az eszköz újraépítésre van beállítva, akkor először mindig a DPS-sel próbál meg kiépítést, majd ha ez sikertelen, vissza fog állítani a kiépítési biztonsági mentésre. Ha egy eszköz úgy van beállítva, hogy dinamikusan újraépíti magát, a IoT Edge újraindul, majd újból kiépíti, ha a rendszer újra kiépíti az eseményt. További információkért [lásd: IoT Hub eszköz-újraosztás fogalmai.](../iot-dps/concepts-device-reprovision.md)

1. Mentse és zárja be a fájlt.

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Ismeri a **DPS-azonosító hatókörét** és az eszközregisztrációs **azonosítót,** amelyek az előző szakaszokban voltak összegyűjtve.

1. Nyissa meg a konfigurációs fájlt a IoT Edge eszközön.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Keresse meg a fájl kiépítési konfigurációk szakaszát. Tegye megjegyzésbe a TPM-kiépítés sorait, és ügyeljen arra, hogy a többi kiépítési sor megjegyzésként legyen ékesedve.

   ```toml
   # DPS provisioning with TPM
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "tpm"
   registration_id = "<REGISTRATION_ID>"
   ```

1. Frissítse a és a értékét a DPS és az `id_scope` eszköz `registration_id` adataival.

1. Ha szükséges, keresse meg a fájl automatikus újrabontási mód szakaszát. A paraméterrel konfigurálhatja az eszköz újra kiépítő `auto_reprovisioning_mode` viselkedését `Dynamic` , vagy `AlwaysOnStartup` `OnErrorOnly` értékkel. További információkért [lásd: IoT Hub eszköz-újraosztás fogalmai.](../iot-dps/concepts-device-reprovision.md)

1. Mentse és zárja be a fájlt.
:::moniker-end
<!-- end 1.2 -->

## <a name="give-iot-edge-access-to-the-tpm"></a>Hozzáférés IoT Edge TPM-hez

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

A IoT Edge-modulnak hozzá kell férni a TPM-hez az eszköz automatikus kiépítése érdekében.

A TPM-nek hozzáférést adhat a IoT Edge futtatáshoz a rendszerbeállítások felülbírálása által, hogy a szolgáltatás rendszergazdai jogosultságokkal `iotedge` rendelkezik. Ha nem szeretné megemelni a szolgáltatás jogosultságszintét, a következő lépésekkel manuálisan is biztosíthatja a TPM-hozzáférést.

1. Keresse meg a TPM hardvermodul elérési útját az eszközön, és mentse helyi változóként.

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Hozzon létre egy új szabályt, amely hozzáférést biztosít a IoT Edge számára a tpm0-hez.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Nyissa meg a szabályfájlt.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Másolja az alábbi hozzáférési adatokat a szabályfájlba.

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="iotedge", MODE="0600"
   ```

5. Mentse a fájlt, majd lépjen ki a fájlból.

6. Aktiválja a udev rendszert az új szabály kiértékeléséhez.

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Ellenőrizze, hogy a szabály alkalmazása sikeres volt-e.

   ```bash
   ls -l /dev/tpm0
   ```

   A sikeres kimenet a következőképpen jelenik meg:

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Ha nem látja, hogy a megfelelő engedélyek vannak alkalmazva, próbálja meg újraindítani a gépet a udev frissítéshez.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
A IoT Edge egy TPM-szolgáltatásra támaszkodik, amely a közvetítőnek az eszköz TPM-éhez való hozzáférését használja. A szolgáltatásnak hozzá kell férni a TPM-hez az eszköz automatikus kiépítése érdekében.

A TPM-hez úgy adhat hozzáférést, hogy felülbírálja a rendszerbeállításokat, hogy a szolgáltatás `aziottpm` rendszergazdai jogosultságokkal rendelkezik. Ha nem szeretné megemelni a szolgáltatás jogosultságszintét, a következő lépésekkel manuálisan is biztosíthatja a TPM-hozzáférést.

1. Keresse meg a TPM hardvermodul elérési útját az eszközön, és mentse helyi változóként.

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Hozzon létre egy új szabályt, amely hozzáférést biztosít IoT Edge számára a tpm0-hez.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Nyissa meg a szabályfájlt.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Másolja az alábbi hozzáférési adatokat a szabályfájlba.

   ```input
   # allow aziottpm access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="aziottpm", MODE="0600"
   ```

5. Mentse a fájlt, majd lépjen ki a fájlból.

6. Aktiválja a udev rendszert az új szabály kiértékeléséhez.

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Ellenőrizze, hogy a szabály alkalmazása sikeres volt-e.

   ```bash
   ls -l /dev/tpm0
   ```

   A sikeres kimenet a következőképpen jelenik meg:

   ```output
   crw-rw---- 1 root aziottpm 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Ha nem látja, hogy a megfelelő engedélyek vannak alkalmazva, próbálja meg újraindítani a gépet a udev frissítéshez.
:::moniker-end
<!-- end 1.2 -->

## <a name="restart-iot-edge-and-verify-successful-installation"></a>A IoT Edge újraindítása és a sikeres telepítés ellenőrzése

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Indítsa újra IoT Edge futásidejű rendszert, hogy az az eszközön végrehajtott összes konfigurációs módosítást átvegye.

   ```bash
   sudo systemctl restart iotedge
   ```

Ellenőrizze, hogy fut-e IoT Edge futtatókörnyezet.

   ```bash
   sudo systemctl status iotedge
   ```

Vizsgálja meg a démonnaplókat.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Ha kiépítési hibákat lát, előfordulhat, hogy a konfigurációs módosítások még nem lépnek életbe. Próbálja meg újraindítani a IoT Edge démont.

   ```bash
   sudo systemctl daemon-reload
   ```

Vagy indítsa újra a virtuális gépet, és nézze meg, hogy a módosítások életbe lépnek-e az újrakezdéskor.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Alkalmazza az eszközön végrehajtott konfigurációs módosításokat.

   ```bash
   sudo iotedge config apply
   ```

Ellenőrizze, hogy fut-e IoT Edge futtatókörnyezet.

   ```bash
   sudo iotedge system status
   ```

Vizsgálja meg a démonnaplókat.

   ```cmd/sh
   sudo iotedge system logs
   ```

Ha kiépítési hibákat lát, előfordulhat, hogy a konfigurációs módosítások még nem lépnek életbe. Próbálja meg újraindítani a IoT Edge démont.

   ```bash
   sudo systemctl daemon-reload
   ```

Vagy indítsa újra a virtuális gépet, és nézze meg, hogy a módosítások életbe lépnek-e az újrakezdéskor.
:::moniker-end
<!-- end 1.2 -->

Ha a futásidő sikeresen elindult, a saját IoT Hub láthatja, hogy az új eszköz automatikusan ki lett építve. Az eszköz most már készen áll a IoT Edge futtatására.

Listából a futó modulokat.

```cmd/sh
iotedge list
```

Ellenőrizheti, hogy a Device Provisioning Service-ban létrehozott egyéni regisztrációt használták-e. Lépjen a Device Provisioning Service-példányra a Azure Portal. Nyissa meg a létrehozott egyéni regisztráció regisztrációs adatait. Figyelje meg, hogy a regisztráció állapota hozzá van **rendelve,** és az eszközazonosító megjelenik a listában.

## <a name="next-steps"></a>Következő lépések

A DPS regisztrációs folyamat lehetővé teszi az eszközazonosító és az ikereszköz-címkék beállítását az új eszköz kiépítésekor. Ezeket az értékeket használhatja az egyes eszközök vagy eszközcsoportok automatikus eszközkezelés használatával való megcélzott kezelésére. Ismerje meg, hogyan helyezhet üzembe és monitor IoT Edge [modulokat](how-to-deploy-at-scale.md) nagy méretekben a Azure Portal vagy az [Azure CLI használatával.](how-to-deploy-cli-at-scale.md)

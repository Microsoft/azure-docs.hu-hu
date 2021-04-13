---
title: Eszköz kiépítése virtuális TPM-sel Linux rendszerű virtuális GÉPEN – Azure IoT Edge
description: Szimulált TPM használata Linux rendszerű virtuális gépen az Azure-eszközök kiépítési szolgáltatásának teszteléséhez Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/09/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ca16099cffc22a19c2ee35b00ae6f1bcbe2977a7
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312399"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-tpm-on-linux"></a>IoT Edge-eszköz létrehozása és kiépítése Linux rendszerű TPM-sel

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Ez a cikk bemutatja, hogyan tesztelheti az automatikus kiépítés egy Linux IoT Edge-eszközön platformmegbízhatósági modul (TPM) használatával. Az eszközök [kiépítési szolgáltatásával](../iot-dps/index.yml)automatikusan kiépítheti Azure IoT Edge eszközöket. Ha nem ismeri az automatikus kiépítés folyamatát, a folytatás előtt tekintse át a [kiépítés](../iot-dps/about-iot-dps.md#provisioning-process) áttekintését.

A feladatok a következők:

1. Hozzon létre egy Linux rendszerű virtuális gépet (VM) a Hyper-V-ben egy szimulált platformmegbízhatósági modul (TPM) a hardveres biztonság érdekében.
1. Hozzon létre egy IoT Hub Device Provisioning Service (DPS) egy példányát.
1. Hozzon létre egy egyéni regisztrációt az eszközhöz.
1. Telepítse a IoT Edge futtatókörnyezetet, és kapcsolja az eszközt a IoT Hubhoz.

> [!TIP]
> Ez a cikk bemutatja, hogyan tesztelheti a DPS-kiépítési modult a TPM-szimulátor használatával, de a fizikai TPM-hardverek, például az [Infineon OPTIGA &trade; TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board), egy Azure Certified for IoT-eszköz esetében.
>
> Ha fizikai eszközt használ, ugorjon a jelen cikk [fizikai eszközről származó kiépítési információinak lekérése](#retrieve-provisioning-information-from-a-physical-device) szakaszára.

## <a name="prerequisites"></a>Előfeltételek

* [Hyper-V-t használó](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)Windows-fejlesztői gép. Ez a cikk az Ubuntu Server rendszerű virtuális gépet futtató Windows 10-es verzióját használja.
* Aktív IoT Hub.

> [!NOTE]
> A TPM 2,0-es kiadására akkor van szükség, ha a TPM-igazolást a DPS használatával használja, és csak egyéni, nem csoportos, regisztrációkat hozhat létre.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Linuxos virtuális gép létrehozása virtuális TPM-sel

Ebben a szakaszban egy új linuxos virtuális gépet hoz létre a Hyper-V-n. Ezt a virtuális gépet szimulált TPM-sel konfigurálja annak teszteléséhez, hogy az automatikus kiépítés hogyan működik a IoT Edgeokkal.

### <a name="create-a-virtual-switch"></a>Virtuális kapcsoló létrehozása

A virtuális kapcsolók lehetővé teszik a virtuális gép számára a fizikai hálózathoz való kapcsolódást.

1. Nyissa meg a Hyper-V kezelőjét a Windows rendszerű gépen.

2. A **műveletek** menüben válassza a **Virtual switch Manager** lehetőséget.

3. Válasszon ki egy **külső** virtuális kapcsolót, majd kattintson a **virtuális kapcsoló létrehozása** elemre.

4. Adja meg az új virtuális kapcsoló nevét, például **EdgeSwitch**. Győződjön meg arról, hogy a kapcsolattípus **külső hálózatra** van beállítva, majd válassza **az OK gombot**.

5. Egy előugró ablak figyelmezteti, hogy a hálózati kapcsolat megszakadhat. Válassza az **Igen** lehetőséget a folytatáshoz.

Ha az új virtuális kapcsoló létrehozásakor hibák jelennek meg, győződjön meg arról, hogy egyetlen másik kapcsoló sem használja az Ethernet-adaptert, és hogy más kapcsolók nem használják ugyanazt a nevet.

### <a name="create-virtual-machine"></a>Virtuális gép létrehozása

1. Töltse le a virtuális géphez használni kívánt lemezképfájlt, és mentse helyileg. Például: [ubuntu server 18,04](http://releases.ubuntu.com/18.04/). További információ a IoT Edge eszközök támogatott operációs rendszereiről: [Azure IoT Edge támogatott rendszerek](support.md).

2. A Hyper-V kezelőjében   >    >  a **műveletek** menüben válassza az új **virtuális gép** művelet lehetőséget.

3. Fejezze be az **új virtuális gép varázslót** a következő beállításokkal:

   1. **Generáció megadása**: válassza a **2. generáció** lehetőséget. A 2. generációs virtuális gépeken engedélyezve van a beágyazott virtualizálás, amely a IoT Edge virtuális gépen való futtatásához szükséges.
   2. **Hálózatkezelés beállítása**: állítsa be az előző szakaszban létrehozott virtuális kapcsolóhoz való **Kapcsolódás** értékét.
   3. **Telepítési beállítások**: válassza az **operációs rendszer telepítése rendszerindító lemezképfájlból** lehetőséget, és keresse meg a helyileg mentett lemezképfájlt.

4. A virtuális gép létrehozásához válassza a **Befejezés** lehetőséget a varázslóban.

Az új virtuális gép létrehozása több percet is igénybe vehet.

### <a name="enable-virtual-tpm"></a>Virtuális TPM engedélyezése

A virtuális gép létrehozása után nyissa meg a beállításait a Virtual platformmegbízhatósági modul (TPM) engedélyezéséhez, amely lehetővé teszi az eszköz automatikus kiépítését.

1. A Hyper-V kezelőjében kattintson a jobb gombbal a virtuális gépre, és válassza a **Beállítások** lehetőséget.

2. Navigáljon a **Biztonság** elemre.

3. Törölje a **biztonságos rendszerindítás engedélyezése** jelölőnégyzet jelölését.

4. Jelölje be az **engedélyezés platformmegbízhatósági modul**.

5. Kattintson az **OK** gombra.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>A virtuális gép elindítása és a TPM-adatok összegyűjtése

A virtuális gépen hozzon létre egy eszközt, amely segítségével lekérheti az eszköz **regisztrációs azonosítóját** és a **hátirat kulcsát**.

1. A Hyper-V kezelőjében indítsa el a virtuális gépet, és kapcsolódjon hozzá.

1. Kövesse a virtuális gépen megjelenő utasításokat a telepítési folyamat befejezéséhez és a számítógép újraindításához.

1. Jelentkezzen be a virtuális gépre, majd kövesse a [Linux fejlesztési környezet beállítása](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) az Azure IOT Device SDK for C telepítéséhez és felépítéséhez című témakör lépéseit.

   >[!TIP]
   >Ebben a cikkben a virtuális gépre másolhatja és beillesztheti a virtuális gépet, amely nem könnyű a Hyper-V Manager-összekötő alkalmazáson keresztül. Előfordulhat, hogy a Hyper-V kezelőjén keresztül szeretne csatlakozni a virtuális géphez az IP-címének lekéréséhez. Először futtassa `sudo apt install net-tools` , majd `hostname -I` . Ezután az IP-címet használhatja az SSH-n keresztül történő kapcsolódáshoz: `ssh <username>@<ipaddress>` .

1. Futtassa az alábbi parancsokat az eszköz kiépítési információinak a TPM-ből való lekéréséhez szükséges SDK-eszköz létrehozásához.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. A kimeneti ablakban az eszköz **regisztrációs azonosítója** és a **jóváhagyó kulcs** látható. Másolja ezeket az értékeket később, amikor egyéni regisztrációt hoz létre az eszközhöz.

Ha megkapta a regisztrációs azonosítót és a jóváhagyó kulcsot, folytassa a [IoT hub Device Provisioning Service beállítása](#set-up-the-iot-hub-device-provisioning-service) című szakasztal.

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Kiépítési információk beolvasása fizikai eszközről

Ha virtuális gép helyett fizikai IoT Edge eszközt használ, hozzon létre egy eszközt, amely segítségével lekérheti az eszköz kiépítési adatait.

1. Kövesse a [Linux fejlesztési környezet beállítása](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) az Azure IOT Device SDK for C telepítéséhez és felépítéséhez című témakör lépéseit.

1. A következő parancsok futtatásával hozza létre azt az SDK-eszközt, amely lekéri az eszköz kiépítési információit a TPM-eszközről.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Másolja a **regisztrációs azonosító** és a **jóváhagyó kulcs** értékeit. Ezeknek az értékeknek a használatával egyéni regisztrációt hozhat létre az eszközhöz a DPS-ben.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>A IoT Hub Device Provisioning Service beállítása

Hozzon létre egy új példányt a IoT Hub Device Provisioning Service az Azure-ban, és kapcsolja össze az IoT hubhoz. A [IOT hub DPS beállítása](../iot-dps/quick-setup-auto-provision.md)című témakör útmutatását követve végezheti el.

Miután futtatta az eszköz kiépítési szolgáltatását, másolja az **azonosító hatókör** értékét az Áttekintés lapról. Ezt az értéket használja a IoT Edge futtatókörnyezet konfigurálásakor.

## <a name="create-a-dps-enrollment"></a>DPS-regisztráció létrehozása

Kérje le a kiépítési információkat a virtuális gépről, és ezt használva hozzon létre egy egyéni regisztrációt a Device kiépítési szolgáltatásban.

Amikor létrehoz egy regisztrációt a DPS-ben, lehetősége van bejelenteni a **kezdeti eszköz kettős állapotát**. Az eszköz Twin-ben címkéket állíthat be az eszközök csoportosításához a megoldásban szükséges mérőszámok, például a régió, a környezet, a hely vagy az eszköz típusa alapján. Ezek a címkék [automatikus központi telepítések](how-to-deploy-at-scale.md)létrehozásához használhatók.

> [!TIP]
> Az Azure CLI-ben létrehozhat egy [beléptetést](/cli/azure/ext/azure-iot/iot/dps/enrollment) , és az **Edge-kompatibilis** jelző használatával meghatározhatja, hogy az eszköz IoT Edge eszköz-e.

1. A [Azure Portal](https://portal.azure.com)navigáljon a IoT hub Device Provisioning Service-példányához.

2. A **Beállítások** területen válassza a **regisztrációk kezelése** lehetőséget.

3. Válassza az **Egyéni regisztráció hozzáadása** lehetőséget, majd végezze el a következő lépéseket a beléptetés konfigurálásához:  

   1. A **mechanizmus** beállításnál válassza a **TPM** lehetőséget.

   2. Adja meg a virtuális gépről másolt **hátirat-kulcsot** és **regisztrációs azonosítót** .

      > [!TIP]
      > Ha fizikai TPM-eszközt használ, meg kell határoznia a **jóváhagyás kulcsát**, amely minden TPM-lapka esetében egyedi, és a hozzá társított TPM-lapka gyártójától származik. Létrehozhat egy egyedi **regisztrációs azonosítót** a TPM-eszközhöz, például a HÁTIRAT kulcs SHA-256 kivonatának létrehozásával.

   3. Ha szeretné, adja meg az eszköz AZONOSÍTÓját. Ha nem adja meg az eszköz AZONOSÍTÓját, a rendszer a regisztrációs azonosítót használja.

   4. A **true (igaz** ) érték kiválasztásával deklarálhatja, hogy a virtuális gép IoT Edge eszköz.

   5. Válassza ki azt a csatolt IoT Hub, amelyhez csatlakoztatni kívánja az eszközt, vagy válassza az **új IoT hub csatolása** lehetőséget. Több hub is kiválasztható, és az eszköz a kiválasztott hozzárendelési házirendnek megfelelően lesz hozzárendelve az egyikhez.

   6. Ha szeretné, adjon hozzá egy címke értéket a **kezdeti eszköz iker állapotához** . A címkék használatával megcélozhatja az eszközök csoportjait a modulok üzembe helyezéséhez. További információ: IoT Edge- [modulok üzembe helyezése nagy léptékben](how-to-deploy-at-scale.md).

   7. Kattintson a **Mentés** gombra.

Most, hogy az eszközhöz regisztrálva van, a IoT Edge futtatókörnyezet automatikusan kiépítheti az eszközt a telepítés során.

## <a name="install-the-iot-edge-runtime"></a>Az IoT Edge-futtatókörnyezet telepítése

Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön üzembe van helyezve. Az összetevői tárolókban futnak, és lehetővé teszik további tárolók üzembe helyezését az eszközön, így a kód a peremhálózat szélén is futtatható. Telepítse a IoT Edge futtatókörnyezetet a virtuális gépre.

Az eszköz kiépítéséhez kövesse az [Azure IoT Edge futtatókörnyezet telepítése](how-to-install-iot-edge.md)című témakör lépéseit, majd térjen vissza ehhez a cikkhez.

## <a name="configure-the-device-with-provisioning-information"></a>Az eszköz konfigurálása az üzembe helyezési adatokkal

Miután telepítette a futtatókörnyezetet az eszközre, konfigurálja az eszközt az általa használt információval az eszköz kiépítési szolgáltatásához való kapcsolódáshoz és a IoT Hubához.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Ismerje meg a DPS- **azonosító hatókörét** és az eszköz **regisztrációs azonosítóját** , amely az előző fejezetekben lett összegyűjtve.

1. Nyissa meg a konfigurációs fájlt a IoT Edge eszközön.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. A fájl kiépítési konfigurációk szakaszának megkeresése. Jegyezze fel a TPM-kiépítés sorait, és győződjön meg arról, hogy az egyéb kiépítési sorok megjegyzése megtörténik.

   A `provisioning:` sornak nem lehetnek korábbi szóközök, és a beágyazott elemeket két szóközzel kell behúzni.

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

1. Frissítse a és a értékét a `scope_id` `registration_id` DPS és az eszköz adataival.

1. Igény szerint a vagy a `always_reprovision_on_startup` `dynamic_reprovisioning` vonalak használatával konfigurálhatja az eszköz újraépítésének viselkedését. Ha egy eszköz úgy van beállítva, hogy a rendszer újraépítse az indítást, a rendszer mindig először a DPS-t próbálja kiépíteni, majd visszatér a kiépítési biztonsági mentéshez, ha az nem sikerül. Ha egy eszköz úgy van beállítva, hogy dinamikusan újra kiépítse magát, IoT Edge újraindítja és újraépíti, ha a rendszer újraépítési eseményt észlel. További információ: [IoT hub eszköz újraépítési fogalmai](../iot-dps/concepts-device-reprovision.md).

1. Mentse és zárja be a fájlt.

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Ismerje meg a DPS- **azonosító hatókörét** és az eszköz **regisztrációs azonosítóját** , amely az előző fejezetekben lett összegyűjtve.

1. Nyissa meg a konfigurációs fájlt a IoT Edge eszközön.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. A fájl kiépítési konfigurációk szakaszának megkeresése. Jegyezze fel a TPM-kiépítés sorait, és győződjön meg arról, hogy az egyéb kiépítési sorok megjegyzése megtörténik.

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

1. Frissítse a és a értékét a `id_scope` `registration_id` DPS és az eszköz adataival.

1. Megkeresheti a fájl automatikus újraépítésének mód szakaszát is. A `auto_reprovisioning_mode` paraméter használatával konfigurálhatja az eszköz újralétesítési viselkedését a, vagy rendszerre `Dynamic` `AlwaysOnStartup` `OnErrorOnly` . További információ: [IoT hub eszköz újraépítési fogalmai](../iot-dps/concepts-device-reprovision.md).

1. Mentse és zárja be a fájlt.
:::moniker-end
<!-- end 1.2 -->

## <a name="give-iot-edge-access-to-the-tpm"></a>IoT Edge hozzáférés biztosítása a TPM-hez

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

A IoT Edge futtatókörnyezetnek hozzá kell férnie a TPM-hez, hogy automatikusan kiépítse az eszközt.

A rendszerszintű beállítások felülbírálása érdekében engedélyezheti a TPM hozzáférését a IoT Edge futtatókörnyezethez `iotedge` . Ha nem szeretné megemelni a szolgáltatási jogosultságokat, az alábbi lépéseket követve manuálisan is megadhatja a TPM-hozzáférést.

1. Keresse meg az eszközön a TPM hardveres modul elérési útját, és mentse helyi változóként.

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Hozzon létre egy új szabályt, amely megadja a IoT Edge Runtime hozzáférést a tpm0.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Nyissa meg a szabályok fájlt.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Másolja a következő hozzáférési adatokat a szabályok fájlba.

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="iotedge", MODE="0600"
   ```

5. Mentse és zárja be a fájlt.

6. Aktiválja a udev rendszerét az új szabály kiértékeléséhez.

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Ellenőrizze, hogy a szabály alkalmazása sikeresen megtörtént-e.

   ```bash
   ls -l /dev/tpm0
   ```

   A sikeres kimenet a következőképpen jelenik meg:

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Ha nem látja, hogy a megfelelő engedélyek lettek alkalmazva, próbálja meg újraindítani a gépet a udev frissítéséhez.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
A IoT Edge futtatókörnyezet olyan TPM-szolgáltatásra támaszkodik, amely a közvetítő eszköz TPM-hez való hozzáférését használja. A szolgáltatásnak hozzá kell férnie a TPM-hez, hogy automatikusan kiépítse az eszközt.

Hozzáférést biztosíthat a TPM-hez, ha felülbírálja a rendszerszintű beállításokat, így a `aziottpm` szolgáltatásnak rendszergazdai jogosultságokkal kell rendelkeznie. Ha nem szeretné megemelni a szolgáltatási jogosultságokat, az alábbi lépéseket követve manuálisan is megadhatja a TPM-hozzáférést.

1. Keresse meg az eszközön a TPM hardveres modul elérési útját, és mentse helyi változóként.

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Hozzon létre egy új szabályt, amely megadja a IoT Edge Runtime hozzáférést a tpm0.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Nyissa meg a szabályok fájlt.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Másolja a következő hozzáférési adatokat a szabályok fájlba.

   ```input
   # allow aziottpm access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="aziottpm", MODE="0600"
   ```

5. Mentse és zárja be a fájlt.

6. Aktiválja a udev rendszerét az új szabály kiértékeléséhez.

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Ellenőrizze, hogy a szabály alkalmazása sikeresen megtörtént-e.

   ```bash
   ls -l /dev/tpm0
   ```

   A sikeres kimenet a következőképpen jelenik meg:

   ```output
   crw-rw---- 1 root aziottpm 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Ha nem látja, hogy a megfelelő engedélyek lettek alkalmazva, próbálja meg újraindítani a gépet a udev frissítéséhez.
:::moniker-end
<!-- end 1.2 -->

## <a name="restart-iot-edge-and-verify-successful-installation"></a>IoT Edge újraindítása és sikeres telepítés ellenőrzése

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Indítsa újra a IoT Edge futtatókörnyezetet, hogy az az eszközön végrehajtott összes konfigurációs módosítást felveszi.

   ```bash
   sudo systemctl restart iotedge
   ```

Ellenőrizze, hogy fut-e a IoT Edge futtatókörnyezet.

   ```bash
   sudo systemctl status iotedge
   ```

Daemon-naplók vizsgálata.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Ha a kiépítési hibák jelennek meg, előfordulhat, hogy a konfiguráció módosítása még nem lépett érvénybe. Próbálkozzon újra a IoT Edge démon újraindításával.

   ```bash
   sudo systemctl daemon-reload
   ```

Vagy próbálja meg újraindítani a virtuális gépet, és ellenőrizze, hogy a módosítások érvénybe lépnek-e egy új indításkor.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Alkalmazza az eszközön végrehajtott konfigurációs módosításokat.

   ```bash
   sudo iotedge config apply
   ```

Ellenőrizze, hogy fut-e a IoT Edge futtatókörnyezet.

   ```bash
   sudo iotedge system status
   ```

Daemon-naplók vizsgálata.

   ```cmd/sh
   sudo iotedge system logs
   ```

Ha a kiépítési hibák jelennek meg, előfordulhat, hogy a konfiguráció módosítása még nem lépett érvénybe. Próbálja meg újraindítani a IoT Edge démont.

   ```bash
   sudo systemctl daemon-reload
   ```

Vagy próbálja meg újraindítani a virtuális gépet, és ellenőrizze, hogy a módosítások érvénybe lépnek-e egy új indításkor.
:::moniker-end
<!-- end 1.2 -->

Ha a futtatókörnyezet sikeresen elindult, beléphet a IoT Hubba, és láthatja, hogy az új eszköz automatikusan lett kiépítve. Az eszköz most már készen áll IoT Edge modulok futtatására.

Futó modulok listázása.

```cmd/sh
iotedge list
```

Ellenőrizheti, hogy a rendszer használta-e a Device kiépítési szolgáltatásban létrehozott egyéni regisztrációt. Navigáljon az eszköz kiépítési szolgáltatási példányához a Azure Portal. Nyissa meg a regisztráció részleteit a létrehozott egyéni regisztrációhoz. Figyelje meg, hogy a regisztráció állapota **hozzá van rendelve** , és az eszköz azonosítója megjelenik.

## <a name="next-steps"></a>Következő lépések

A DPS-beléptetési folyamat lehetővé teszi az eszköz AZONOSÍTÓjának és az eszköz Twin-címkék egyidejű beállítását az új eszköz kiépítésekor. Ezeket az értékeket használhatja az egyes eszközök vagy eszközök automatikus eszközkezelés használatával történő megcélzásához. Megtudhatja, hogyan [helyezheti üzembe és figyelheti IoT Edge-modulok méretezését a Azure Portal vagy az](how-to-deploy-at-scale.md) [Azure CLI használatával](how-to-deploy-cli-at-scale.md).

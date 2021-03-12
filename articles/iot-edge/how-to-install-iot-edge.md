---
title: A Azure IoT Edge telepítése | Microsoft Docs
description: Azure IoT Edge telepítési utasítások Windows vagy Linux rendszerű eszközökön
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: kgremban
ms.openlocfilehash: 6a64bb2801830440dc49e72786c9c00a6e4796b3
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201620"
---
# <a name="install-or-uninstall-azure-iot-edge-for-linux"></a>Linuxos Azure IoT Edge telepítése vagy eltávolítása

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Az Azure IoT Edge futtatókörnyezet az eszköz IoT Edge eszközre való bekapcsolása. A futtatókörnyezet az eszközökön kisméretű, málna PI-ként vagy ipari kiszolgálóként is telepíthető. Miután konfigurált egy eszközt az IoT Edge-futtatókörnyezettel, üzembe helyezhet rajta üzleti logikát a felhőből. További információ: [a Azure IoT Edge futtatókörnyezet és az architektúrájának megismerése](iot-edge-runtime.md).

Ez a cikk a Azure IoT Edge futtatókörnyezet Linux rendszerű eszközökön való telepítésének lépéseit sorolja fel.

## <a name="prerequisites"></a>Előfeltételek

* [Regisztrált eszköz azonosítója](how-to-register-device.md)

  Ha szimmetrikus kulcsos hitelesítéssel regisztrálta az eszközt, az eszköz csatlakoztatási karakterlánca készen áll.

  Ha X. 509 önaláírt tanúsítvány-hitelesítéssel regisztrálta az eszközt, legalább egy olyan identitás-tanúsítvánnyal rendelkezik, amelyet az eszköz regisztrálásához és a hozzá tartozó titkos kulcshoz használt.

* Linux-eszköz

  X64-, ARM32-vagy ARM64-alapú Linux-eszközre van telepítve. A Microsoft az Ubuntu Server 18,04 és a málna PI OS stretch operációs rendszerek telepítési csomagjait biztosítja.

  Az éles környezetekben jelenleg támogatott operációs rendszerekkel kapcsolatos legfrissebb információk: [Azure IoT Edge támogatott rendszerek](support.md#operating-systems)

  >[!NOTE]
  >A ARM64-eszközök támogatása [nyilvános előzetes](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verzióban érhető el.

* Készítse elő az eszközt a Microsoft telepítési csomagjainak eléréséhez.

  Telepítse az eszköz operációs rendszerének megfelelő adattár-konfigurációt.

  * **Ubuntu Server 18,04**:

    ```bash
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
    ```

  * **Málna PI operációs rendszer stretch**:

    ```bash
    curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
    ```

  Másolja a generált listát a sources. list. d könyvtárba.

  ```bash
  sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
  ```

  Telepítse a Microsoft GPG nyilvános kulcsot.

  ```bash
  curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
  sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
  ```

Azure IoT Edge szoftvercsomagok az egyes csomagokban ( `usr/share/doc/{package-name}` vagy a címtárban) található licencfeltételek hatálya alá esnek `LICENSE` . A licencfeltételek elolvasása a csomag használata előtt. A csomagok telepítése és használata a jelen feltételek elfogadását jelenti. Ha nem fogadja el a licencfeltételeket, ne használja ezt a csomagot.

## <a name="install-a-container-engine"></a>Tároló-motor telepítése

Azure IoT Edge egy OCI-kompatibilis tároló-futtatókörnyezetre támaszkodik. Éles környezetekben javasolt a Moby Engine használata. A Moby motor az egyetlen tároló motor, amelyet hivatalosan támogat a Azure IoT Edge. A Docker CE/EE tároló lemezképei kompatibilisek a Moby Runtime szolgáltatással.

Frissítési csomagok listája az eszközön.

   ```bash
   sudo apt-get update
   ```

Telepítse a Moby Engine-t.

   ```bash
   sudo apt-get install moby-engine
   ```

Ha a Moby Container Engine telepítésekor hibákat kap, ellenőrizze a Linux-kernelt a Moby Compatibility szolgáltatáshoz. Néhány beágyazott eszköz gyártója olyan eszközöket szállít, amelyek egyéni Linux-kerneleket tartalmaznak a tároló motorjának kompatibilitásához szükséges funkciók nélkül. Futtassa a következő parancsot, amely a Moby által biztosított [ellenőrzés-konfiguráció parancsfájlt](https://github.com/moby/moby/blob/master/contrib/check-config.sh) használja a rendszermag konfigurációjának ellenőrzéséhez:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

A parancsfájl kimenetében győződjön meg arról, hogy a és a összes eleme `Generally Necessary` `Network Drivers` engedélyezve van. Ha hiányoznak a funkciók, engedélyezze őket a kernel a forrásból való újraépítésével, majd a megfelelő kernel. config fájlban való felvételhez kapcsolódó modulok kiválasztásával. Hasonlóképpen, ha olyan kernel-konfigurációs generátort használ `defconfig` , mint a vagy a `menuconfig` , megkeresheti és engedélyezheti a megfelelő szolgáltatásokat, és ennek megfelelően újjáépítheti a kernelt. Miután telepítette az újonnan módosított kernelt, futtassa újra a bejelentkezést ellenőrző parancsfájlt annak ellenőrzéséhez, hogy az összes szükséges funkció engedélyezve lett-e.

## <a name="install-iot-edge"></a>IoT Edge telepítése

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

A IoT Edge biztonsági démon a IoT Edge eszközön biztosít és tart fenn biztonsági szabványokat. A démon minden rendszerindításkor elindul, és elindítja az eszközt a IoT Edge futtatókörnyezet további részének elindításával.

Az ebben a szakaszban szereplő lépések a legújabb verziónak az internetkapcsolattal rendelkező eszközre történő telepítésének tipikus folyamatát szemléltetik. Ha telepítenie kell egy adott verziót, például egy előzetes verziót, vagy offline üzemmódban kell telepítenie, kövesse a jelen cikk későbbi, [Offline vagy adott verziójának telepítési](#offline-or-specific-version-installation-optional) lépéseit.

Frissítési csomagok listája az eszközön.

   ```bash
   sudo apt-get update
   ```

Ellenőrizze, hogy a IoT Edge mely verziói érhetők el.

   ```bash
   apt list -a iotedge
   ```

Ha a biztonsági démon legújabb verzióját szeretné telepíteni, használja a következő parancsot, amely az **libiothsm-STD** csomag legfrissebb verzióját is telepíti:

   ```bash
   sudo apt-get install iotedge
   ```

Ha a biztonsági démon egy adott verzióját szeretné telepíteni, adja meg a verziót az apt List kimenetből. Ugyanezt a verziót is meg kell adni a **libiothsm-STD** csomaghoz, amely egyébként a legújabb verzióját telepíti. A következő parancs például a 1.0.10 kiadás legújabb verzióját telepíti:

   ```bash
   sudo apt-get install iotedge=1.0.10* libiothsm-std=1.0.10*
   ```

Ha a telepíteni kívánt verzió nem szerepel a listáján, kövesse a cikk későbbi, [Offline vagy adott verziójának telepítési](#offline-or-specific-version-installation-optional) lépéseit. Ez a szakasz bemutatja, hogyan célozhatja meg a IoT Edge biztonsági démon vagy a Release Candidate verziójának korábbi verzióját.

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

A IoT Edge szolgáltatás biztonsági szabványokat biztosít és tart fenn a IoT Edge eszközön. A szolgáltatás minden rendszerindításkor elindul, és elindítja az eszközt a IoT Edge futtatókörnyezet további részének elindításával.

A IoT Identity Service a IoT Edge 1,2-es verziójával együtt lett bevezetve. Ez a szolgáltatás kezeli az identitások kiépítés és felügyeletét IoT Edge és más, a IoT Hubkel kommunikáló eszköz-összetevőkhöz.

Az ebben a szakaszban szereplő lépések a legújabb verziónak az internetkapcsolattal rendelkező eszközre történő telepítésének tipikus folyamatát szemléltetik. Ha telepítenie kell egy adott verziót, például egy előzetes verziót, vagy offline üzemmódban kell telepítenie, kövesse a jelen cikk későbbi, [Offline vagy adott verziójának telepítési](#offline-or-specific-version-installation-optional) lépéseit.

>[!NOTE]
>A jelen szakaszban ismertetett lépések bemutatják, hogyan telepítheti az IoT Edge 1,2-es verzióját, amely jelenleg nyilvános előzetes verzióban érhető el. Ha a IoT Edge legújabb általánosan elérhető verziójának telepítésére vonatkozó lépéseket keresi, tekintse meg a cikk [1,1 (LTS)](?view=iotedge-2018-06&preserve-view=true) verzióját.
>
>Ha már rendelkezik egy régebbi verziót futtató IoT Edge eszközzel, és a 1,2-re szeretné frissíteni, használja a [IoT Edge biztonsági démon és futtatókörnyezet frissítése](how-to-update-iot-edge.md)című témakör lépéseit. Az 1,2-es verzió megfelelően különbözik a korábbi IoT Edge verzióitól, hogy a frissítéshez adott lépések szükségesek.

Frissítési csomagok listája az eszközön.

   ```bash
   sudo apt-get update
   ```

Ellenőrizze, hogy a IoT Edge mely verziói érhetők el.

   ```bash
   apt list -a aziot-edge
   ```

Ha a IoT Edge legújabb verzióját szeretné telepíteni, használja a következő parancsot, amely az Identity Service-csomag legújabb verzióját is telepíti:

   ```bash
   sudo apt-get install aziot-edge
   ```

<!-- commenting out for public preview. reintroduce at GA

Or, if you want to install a specific version of IoT Edge and the identity service, specify the versions from the apt list output. Specify the same versions for both services.. For example, the following command installs the most recent version of the 1.2 release:

   ```bash
   sudo apt-get install aziot-edge=1.2* aziot-identity-service=1.2*
   ```

-->

<!-- end 1.2 -->
::: moniker-end

## <a name="provision-the-device-with-its-cloud-identity"></a>Az eszköz kiépítése a Felhőbeli identitással

Most, hogy telepítette a tároló motort és a IoT Edge futtatókörnyezetet az eszközre, készen áll a következő lépésre, amely az eszköz Felhőbeli identitás-és hitelesítési adataival való beállítására szolgál.

Válassza ki a következő szakaszt a használni kívánt hitelesítési típus alapján:

* [1. lehetőség: hitelesítés szimmetrikus kulcsokkal](#option-1-authenticate-with-symmetric-keys)
* [2. lehetőség: hitelesítés X. 509 tanúsítvánnyal](#option-2-authenticate-with-x509-certificates)

### <a name="option-1-authenticate-with-symmetric-keys"></a>1. lehetőség: hitelesítés szimmetrikus kulcsokkal

Ezen a ponton a IoT Edge Runtime telepítve van a Linux-eszközön, és az eszközt a Felhőbeli identitás-és hitelesítési adataival kell kiépíteni.

Ez a szakasz végigvezeti az eszköz szimmetrikus kulcsos hitelesítéssel történő kiépítésének lépésein. Regisztrálnia kell az eszközét IoT Hubban, és le kell kérnie a kapcsolatok karakterláncát az eszköz adataiból. Ha nem, kövesse az [IoT Edge eszköz regisztrálása IoT Hubban](how-to-register-device.md)című témakör lépéseit.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

A IoT Edge eszközön nyissa meg a konfigurációs fájlt.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Keresse meg a fájl kiépítési konfigurációit, és a **manuális üzembe helyezési konfigurációt a kapcsolatok karakterlánca szakasz használatával** írja le, ha még nem tette meg a megjegyzéseit.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
   ```

**Device_connection_string** értékének frissítése a IoT Edge eszközhöz tartozó kapcsolatok karakterláncával. Győződjön meg arról, hogy a többi kiépítési szakaszt feljegyezték. Győződjön meg arról, hogy a **kiépítés:** sor nem tartalmaz korábbi szóközt, és a beágyazott elemek két szóközzel vannak behúzva.

A vágólap tartalmának a Nanoba vagy a sajtóba való beillesztéséhez `Shift+Right Click` `Shift+Insert` .

Mentse és zárja be a fájlt.

   `CTRL + X`, `Y`, `Enter`

A konfigurációs fájlban szereplő kiépítési információk megadása után indítsa újra a démont:

   ```bash
   sudo systemctl restart iotedge
   ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

Hozza létre az eszköz konfigurációs fájlját a IoT Edge telepítésének részeként megadott sablonfájl alapján.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

A IoT Edge eszközön nyissa meg a konfigurációs fájlt.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

Keresse meg a fájl **kiépítési** szakaszát, és a manuális kiépítés a kapcsolatok karakterlánc-soraival című szakaszt.

   ```toml
   # Manual provisioning with connection string
   [provisioning]
   source = "manual"
   connection_string = "<ADD DEVICE CONNECTION STRING HERE>"
   ```

**Connection_string** értékének frissítése a IoT Edge eszközhöz tartozó kapcsolatok karakterláncával.

A vágólap tartalmának a Nanoba vagy a sajtóba való beillesztéséhez `Shift+Right Click` `Shift+Insert` .

Mentse és zárja be a fájlt.

   `CTRL + X`, `Y`, `Enter`

Miután beírta a kiépítési információkat a konfigurációs fájlban, alkalmazza a módosításokat:

   ```bash
   sudo iotedge config apply
   ```

<!-- end 1.2 -->
::: moniker-end

### <a name="option-2-authenticate-with-x509-certificates"></a>2. lehetőség: hitelesítés X. 509 tanúsítvánnyal

Ezen a ponton a IoT Edge Runtime telepítve van a Linux-eszközön, és az eszközt a Felhőbeli identitás-és hitelesítési adataival kell kiépíteni.

Ez a szakasz végigvezeti az eszköz X. 509 tanúsítvánnyal történő hitelesítésének lépésein. Regisztrálnia kell az eszközét IoT Hubban, és meg kell adnia a IoT Edge eszközön található tanúsítvány-és titkos kulccsal egyező ujjlenyomatai megfelelnek. Ha nem, kövesse az [IoT Edge eszköz regisztrálása IoT Hubban](how-to-register-device.md)című témakör lépéseit.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

A IoT Edge eszközön nyissa meg a konfigurációs fájlt.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Keresse meg a fájl létesítési konfigurációk szakaszát, és írja be a **manuális kiépítési konfigurációt az X. 509 azonosító tanúsítványa szakasz használatával** . Győződjön meg arról, hogy a többi kiépítési szakaszt feljegyezték. Győződjön meg arról, hogy a **kiépítés:** sor nem tartalmaz korábbi szóközt, és a beágyazott elemek két szóközzel vannak behúzva.

   ```yml
   # Manual provisioning configuration using an x.509 identity certificate
   provisioning:
     source: "manual"
     authentication:
       method: "x509"
       iothub_hostname: "<REQUIRED IOTHUB HOSTNAME>"
       device_id: "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

Frissítse a következő mezőket:

* **iothub_hostname**: a IoT hub állomásneve, amelyhez az eszköz csatlakozni fog. Például: `{IoT hub name}.azure-devices.net`.
* **device_id**: az eszköz regisztrálása során megadott azonosító.
* **identity_cert**: URI az eszközön lévő identitási tanúsítványhoz. Például: `file:///path/identity_certificate.pem`.
* **identity_pk**: a megadott identitás tanúsítványához tartozó titkos kulcs fájljának URI-ja. Például: `file:///path/identity_key.pem`.

Mentse és zárja be a fájlt.

   `CTRL + X`, `Y`, `Enter`

A konfigurációs fájlban szereplő kiépítési információk megadása után indítsa újra a démont:

   ```bash
   sudo systemctl restart iotedge
   ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

Hozza létre az eszköz konfigurációs fájlját a IoT Edge telepítésének részeként megadott sablonfájl alapján.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

A IoT Edge eszközön nyissa meg a konfigurációs fájlt.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

Keresse meg a fájl **kiépítési** szakaszát, és tegye meg a manuális kiépítés sorát az X. 509 azonosító tanúsítványával. Győződjön meg arról, hogy a többi kiépítési szakaszt feljegyezték.

   ```toml
   # Manual provisioning with x.509 certificates
   [provisioning]
   source = "manual"
   iothub_hostname = "<REQUIRED IOTHUB HOSTNAME>"
   device_id = "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"

   [provisioning.authentication]
   method = "x509"

   identity_cert = "<REQUIRED URI OR POINTER TO DEVICE IDENTITY CERTIFICATE>"

   identity_pk = "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

Frissítse a következő mezőket:

* **iothub_hostname**: a IoT hub állomásneve, amelyhez az eszköz csatlakozni fog. Például: `{IoT hub name}.azure-devices.net`.
* **device_id**: az eszköz regisztrálása során megadott azonosító.
* **identity_cert**: URI azonosító tanúsítvány az eszközön, például: `file:///path/identity_certificate.pem` . Vagy dinamikusan kiállíthatja a tanúsítványt az EST vagy egy helyi hitelesítésszolgáltató használatával.
* **identity_pk**: a megadott identitási tanúsítványhoz tartozó titkos kulcs fájljának URI azonosítója, például: `file:///path/identity_key.pem` . Vagy adjon meg egy PKCS # 11 URI-t, majd adja meg a konfigurációs adatokat a konfigurációs fájl későbbi, **PKCS # 11** szakaszában.

Mentse és zárja be a fájlt.

   `CTRL + X`, `Y`, `Enter`

Miután beírta a kiépítési információkat a konfigurációs fájlban, alkalmazza a módosításokat:

   ```bash
   sudo iotedge config apply
   ```

<!-- end 1.2 -->
::: moniker-end

## <a name="verify-successful-configuration"></a>Sikeres konfiguráció ellenőrzése

Ellenőrizze, hogy a futtatókörnyezet sikeresen telepítve és konfigurálva van-e a IoT Edge eszközön.

>[!TIP]
>Az `iotedge` parancsok futtatásához megemelt jogosultsági szint szükséges. Amikor az IoT Edge-futtatókörnyezet telepítése után kijelentkezik, majd először újra bejelentkezik a gépre, az engedélyei automatikusan frissülnek. Addig használja `sudo` a parancsot a parancsok előtt.

Ellenőrizze, hogy fut-e a IoT Edge rendszerszolgáltatás.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   sudo systemctl status iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system status
   ```

::: moniker-end

Ha hibaelhárításra van szükség, kérje le a szolgáltatás naplóit.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   journalctl -u iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system logs
   ```

::: moniker-end

Az eszköz használatával `check` ellenőrizze az eszköz konfigurációját és kapcsolódási állapotát.

   ```bash
   sudo iotedge check
   ```

>[!TIP]
>Mindig használja `sudo` az ellenőrzési eszköz futtatására, még az engedélyek frissítése után is. Az eszköznek emelt szintű jogosultsággal kell rendelkeznie a konfigurációs fájl eléréséhez a konfiguráció állapotának ellenőrzéséhez.

Tekintse meg az IoT Edge-eszközön futó összes modult. A szolgáltatás első indításakor csak a **edgeAgent** modult kell futtatnia. A edgeAgent modul alapértelmezés szerint fut, és az eszközre telepített további modulok telepítését és elindítását is lehetővé teszi.

   ```bash
   sudo iotedge list
   ```

## <a name="offline-or-specific-version-installation-optional"></a>Offline vagy adott verzió telepítése (nem kötelező)

Az ebben a szakaszban ismertetett lépések a normál telepítési lépések által nem tárgyalt forgatókönyvekre vonatkoznak. Ilyen lehet például a következők:

* IoT Edge telepítése kapcsolat nélküli üzemmódban
* Release Candidate verziójának telepítése

Az ebben a szakaszban ismertetett lépéseket követve telepítheti a Azure IoT Edge futtatókörnyezet adott verzióját, amely nem érhető el a rendszeren `apt-get install` . A Microsoft-csomagok listája csak korlátozott számú közelmúltbeli verziót és azok alverzióit tartalmazza, ezért ezek a lépések mindazok számára szükségesek, akik egy régebbi verziót vagy egy kiadásra jelölt verziót kívánnak telepíteni.

A curl-parancsok használatával közvetlenül a IoT Edge GitHub-tárházból is megcélozhatja az összetevők fájljait.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

1. Navigáljon a [Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)kiadásokhoz, és keresse meg a célként használni kívánt kiadási verziót.

2. Bontsa ki az **eszközök** szakaszt az adott verzióhoz.

3. Minden kiadásnak új fájlokkal kell rendelkeznie a IoT Edge biztonsági démonhoz és a hsmlib. Ha offline eszközre kívánja telepíteni a IoT Edget, töltse le előre ezeket a fájlokat. Ellenkező esetben az alábbi parancsokkal frissítheti ezeket az összetevőket.

   1. Keresse meg a IoT Edge eszköz architektúrájának megfelelő **libiothsm-STD-** fájlt. Kattintson a jobb gombbal a fájl hivatkozásra, és másolja a hivatkozás címe.

   2. A következő parancsban található másolt hivatkozásra kattintva telepítheti a hsmlib adott verzióját:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```

   3. A IoT Edge eszköz architektúrájának megfelelő **iotedge** -fájl megkeresése. Kattintson a jobb gombbal a fájl hivatkozásra, és másolja a hivatkozás címe.

   4. A következő parancsban található másolt hivatkozásra kattintva telepítse a IoT Edge biztonsági démon adott verzióját.

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

>[!NOTE]
>Ha az eszköz jelenleg a 1,1-es vagy régebbi verziójú IoT Edge fut, távolítsa el a **iotedge** és a **libiothsm-STD** csomagokat, mielőtt ezt a szakaszt végrehajtja. További információ: [Update from 1,0 vagy 1,1 to 1,2](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12).

1. Navigáljon a [Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)kiadásokhoz, és keresse meg a célként használni kívánt kiadási verziót.

2. Bontsa ki az **eszközök** szakaszt az adott verzióhoz.

3. Minden kiadásnak új fájlokkal kell rendelkeznie IoT Edge és az Identity szolgáltatáshoz. Ha offline eszközre kívánja telepíteni a IoT Edget, töltse le előre ezeket a fájlokat. Ellenkező esetben az alábbi parancsokkal frissítheti ezeket az összetevőket.

   1. Keresse meg a IoT Edge eszköz architektúrájának megfelelő, **aziot-Identity-Service** fájlt. Kattintson a jobb gombbal a fájl hivatkozásra, és másolja a hivatkozás címe.

   2. A következő parancsban található másolt hivatkozás használatával telepítheti az Identity Service adott verzióját:

      ```bash
      curl -L <identity service link> -o aziot-identity-service.deb && sudo dpkg -i ./aziot-identity-service.deb
      ```

   3. A IoT Edge eszköz architektúrájának megfelelő **aziot-** fájl megkeresése. Kattintson a jobb gombbal a fájl hivatkozásra, és másolja a hivatkozás címe.

   4. A következő parancsban található másolt hivatkozásra kattintva telepítheti a IoT Edge ezen verzióját.

      ```bash
      curl -L <iotedge link> -o aziot-edge.deb && sudo dpkg -i ./aziot-edge.deb
      ```

<!-- end 1.2 -->
::: moniker-end

Most, hogy a Container Engine és a IoT Edge Runtime telepítve van az eszközre, készen áll a következő lépésre, amely [az eszköz Felhőbeli identitással](#provision-the-device-with-its-cloud-identity)való kiépítésére szolgál.

## <a name="uninstall-iot-edge"></a>IoT Edge eltávolítása

Ha el szeretné távolítani a IoT Edge telepítését az eszközről, használja a következő parancsokat.

Távolítsa el az IoT Edge-futtatókörnyezetet.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

```bash
sudo apt-get remove iotedge
```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

```bash
sudo apt-get remove aziot-edge
```

::: moniker-end

`--purge`Ha törölni szeretné a IoT Edgehoz társított összes fájlt, beleértve a konfigurációs fájlokat is, használja a jelzőt. Hagyja ki ezt a jelzőt, ha újra szeretné telepíteni IoT Edge, és a későbbiekben ugyanazt a konfigurációs információt használja.

A IoT Edge futtatókörnyezet eltávolításakor a létrehozott tárolók leállnak, de továbbra is léteznek az eszközön. Megtekintheti az összes tárolót, hogy azok megmaradjanak-e.

```bash
sudo docker ps -a
```

Törölje a tárolókat az eszközről, beleértve a két futtató tárolót is.

```bash
sudo docker rm -f <container name>
```

Végül távolítsa el a Container Runtime eszközt az eszközről.

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>Következő lépések

Folytassa a [IoT Edge-modulok üzembe](how-to-deploy-modules-portal.md) helyezésével, hogy megtudja, hogyan helyezhet üzembe modulokat az eszközön.

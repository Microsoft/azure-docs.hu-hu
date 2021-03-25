---
title: Eszközök tanúsítványainak kezelése – Azure IoT Edge | Microsoft Docs
description: Hozzon létre tesztelési tanúsítványokat, telepítsen és kezelje őket egy Azure IoT Edge eszközön az éles üzembe helyezés előkészítéséhez.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f3b6bd19d47658e5ad079f0b731cbafc866bb333
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045773"
---
# <a name="manage-certificates-on-an-iot-edge-device"></a>Tanúsítványok kezelése egy IoT Edge eszközön

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Minden IoT Edge-eszköz tanúsítványokat használ a futtatókörnyezet és az eszközön futó modulok közötti biztonságos kapcsolatok létrehozásához. IoT Edge az átjáróként működő eszközök ugyanezeket a tanúsítványokat használják az alsóbb rétegbeli eszközökhöz való kapcsolódáshoz.

## <a name="install-production-certificates"></a>Éles tanúsítványok telepítése

Amikor először telepíti IoT Edge és kiépíti az eszközt, az eszköz ideiglenes tanúsítvánnyal lesz beállítva, hogy tesztelni tudja a szolgáltatást.
Ezek az ideiglenes tanúsítványok 90 nap múlva lejárnak, vagy visszaállíthatók a gép újraindításával.
Ha éles környezetbe helyezi az eszközt, vagy egy átjárót szeretne létrehozni, meg kell adnia a saját tanúsítványait.
Ez a cikk bemutatja a tanúsítványok IoT Edge-eszközökön való telepítésének lépéseit.

Ha többet szeretne megtudni a tanúsítványok különböző típusairól és szerepköreiről, tekintse meg a [Azure IoT Edge tanúsítványok használatának](iot-edge-certs.md)ismertetése című témakört.

>[!NOTE]
>Az ebben a cikkben használt "legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ" kifejezés a IoT-megoldáshoz tartozó tanúsítványlánc legfelső szintű nyilvános tanúsítványára vonatkozik. Nem kell használnia egy konzorciális hitelesítésszolgáltató tanúsítványának gyökerét vagy a szervezet hitelesítésszolgáltatójának gyökerét. Sok esetben valójában egy közbenső HITELESÍTÉSSZOLGÁLTATÓ nyilvános tanúsítványa.

### <a name="prerequisites"></a>Előfeltételek

* Egy IoT Edge eszköz.

  Ha nincs beállítva IoT Edge eszköz, létrehozhat egyet egy Azure-beli virtuális gépen. A [virtuális Linux-eszköz létrehozásához](quickstart-linux.md) vagy [virtuális Windows-eszköz létrehozásához](quickstart.md)kövesse az egyik rövid útmutató lépéseit.

* Rendelkeznie kell egy legfelső szintű hitelesítésszolgáltató (CA) tanúsítvánnyal, amely önaláírt vagy megbízható kereskedelmi hitelesítésszolgáltatótól vásárolt, például Baltimore, VeriSign, DigiCert vagy GlobalSign.

  Ha még nem rendelkezik legfelső szintű hitelesítésszolgáltatóval, de szeretné kipróbálni IoT Edge olyan szolgáltatásokat, amelyek éles tanúsítványokat igényelnek (például átjárói forgatókönyvek), létrehozhat [bemutató-tanúsítványokat a IoT Edge eszköz funkcióinak teszteléséhez](how-to-create-test-certificates.md).

### <a name="create-production-certificates"></a>Éles tanúsítványok létrehozása

A következő fájlok létrehozásához használja a saját hitelesítésszolgáltatóját:

* Legfelső szintű hitelesítésszolgáltató
* Eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa
* Eszköz HITELESÍTÉSSZOLGÁLTATÓI titkos kulcsa

Ebben a cikkben a *legfelső szintű hitelesítésszolgáltatót* nevezzük, nem pedig a szervezet legfelső szintű hitelesítésszolgáltatója. Ez a legfelső szintű hitelesítésszolgáltató a IoT Edge-forgatókönyvhöz, amelyet az IoT Edge hub modul, a felhasználói modulok és az alsóbb rétegbeli eszközök az egymás közötti megbízhatósági kapcsolat létrehozására használnak.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

> [!NOTE]
> Jelenleg a libiothsm korlátozásai meggátolják a 2038 január 1-jén vagy azt követően lejáró tanúsítványok használatát.

:::moniker-end

Ha szeretné megtekinteni a tanúsítványok példáját, tekintse át a bemutató tanúsítványokat létrehozó parancsfájlokat a [teszt hitelesítésszolgáltatói tanúsítványok kezelése a mintákhoz és az oktatóanyagokhoz](https://github.com/Azure/iotedge/tree/master/tools/CACertificates)című témakörben.

### <a name="install-certificates-on-the-device"></a>Tanúsítványok telepítése az eszközön

Telepítse a tanúsítványláncot a IoT Edge eszközre, és konfigurálja a IoT Edge futtatókörnyezetet az új tanúsítványokra való hivatkozáshoz.

Másolja a három tanúsítvány-és kulcsfájl-fájlt a IoT Edge eszközre. Használhat olyan szolgáltatásokat, mint például a [Azure Key Vault](../key-vault/index.yml) vagy a [biztonságos másolási protokollt](https://www.ssh.com/ssh/scp/) használó függvények a tanúsítványfájl áthelyezéséhez. Ha saját maga hozta létre a tanúsítványokat a IoT Edge eszközön, kihagyhatja ezt a lépést, és használhatja a munkakönyvtár elérési útját.

Ha Windows rendszeren használja a Linux rendszerhez készült IoT Edge, akkor az Azure IoT Edge fájlban található SSH-kulcsot kell használnia a `id_rsa` gazda operációs rendszer és a Linux virtuális gép közötti fájlátvitel hitelesítéséhez. A következő parancs használatával elvégezheti a hitelesített SCP használatát:

   ```powershell-interactive
   C:\WINDOWS\System32\OpenSSH\scp.exe -i 'C:\Program Files\Azure IoT Edge\id_rsa' <PATH_TO_SOURCE_FILE> iotedge-user@<VM_IP>:<PATH_TO_FILE_DESTINATION>
   ```

   >[!NOTE]
   >A linuxos virtuális gép IP-címe a parancs használatával kérdezhető le `Get-EflowVmAddr` .

Ha a minta parancsfájlokat használta a [bemutató tanúsítványok létrehozásához](how-to-create-test-certificates.md), másolja a következő fájlokat a IoT-Edge eszközre:

* Eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa: `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* Eszköz HITELESÍTÉSSZOLGÁLTATÓjának titkos kulcsa: `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* Legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ: `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Nyissa meg a IoT Edge biztonsági démon konfigurációs fájlját.

   * Linux-és Linux-IoT Edge Windows rendszeren: `/etc/iotedge/config.yaml`

   * Windows-tárolók használata: `C:\ProgramData\iotedge\config.yaml`

1. Állítsa be a **tanúsítvány** tulajdonságait a config. YAML értékre a IoT Edge eszköz tanúsítvány-és kulcsfájl-FÁJLjának URI-elérési útjára. Távolítsa el a `#` karaktert, mielőtt a tanúsítvány tulajdonságai megszüntessék a négy sort. Győződjön meg arról, hogy a (z) **:** sor nem rendelkezik korábbi szóközökkel, és hogy a beágyazott elemek két szóközzel vannak behúzva. Például:

   * Linux-és Linux-IoT Edge Windows rendszeren:

      ```yaml
      certificates:
        device_ca_cert: "file:///<path>/<device CA cert>"
        device_ca_pk: "file:///<path>/<device CA key>"
        trusted_ca_certs: "file:///<path>/<root CA cert>"
      ```

   * Windows-tárolók használata:

      ```yaml
      certificates:
        device_ca_cert: "file:///C:/<path>/<device CA cert>"
        device_ca_pk: "file:///C:/<path>/<device CA key>"
        trusted_ca_certs: "file:///C:/<path>/<root CA cert>"
      ```

1. Linux-eszközökön ellenőrizze, hogy a felhasználó **iotedge** rendelkezik-e olvasási engedéllyel a tanúsítványokat tároló címtárhoz.

1. Ha korábban más tanúsítványokat használt IoT Edgehoz az eszközön, a IoT Edge elindítása vagy újraindítása előtt törölje a fájlokat a következő két könyvtárban:

   * Linux-és Linux-IoT Edge Windows rendszeren: `/var/lib/iotedge/hsm/certs` és `/var/lib/iotedge/hsm/cert_keys`

   * Windows-tárolók használatával: `C:\ProgramData\iotedge\hsm\certs` és `C:\ProgramData\iotedge\hsm\cert_keys`

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Nyissa meg a IoT Edge biztonsági démon konfigurációs fájlját: `/etc/aziot/config.toml`

1. Keresse meg a `trust_bundle_cert` paramétert a fájl elején. A sor megjegyzésének visszaadása, és a fájl URI-ja megadása a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány számára az eszközön.

   ```toml
   trust_bundle_cert = "file:///<path>/<root CA cert>"
   ```

1. Keresse meg a `[edge_ca]` config. toml fájlban található szakaszt. Az ebben a szakaszban szereplő sorok megjegyzésének visszaadása és a IoT Edge eszközön található tanúsítványhoz és kulcshoz tartozó fájlok URI-elérési útjának megadása.

   ```toml
   [edge_ca]
   cert = "file:///<path>/<device CA cert>"
   pk = "file:///<path>/<device CA key>"
   ```

1. Győződjön meg arról, hogy a felhasználó **iotedge** rendelkezik olvasási engedéllyel a tanúsítványokat tároló címtárhoz.

1. Ha korábban más tanúsítványokat használt IoT Edgehoz az eszközön, a IoT Edge elindítása vagy újraindítása előtt törölje a fájlokat a következő két könyvtárban:

   * `/var/lib/aziot/certd/certs`
   * `/var/lib/aziot/keyd/keys`

:::moniker-end
<!-- end 1.2 -->

<!-- 1.1. -->
<!-- Temporarily, customizable certificate lifetime not available in 1.2. Update before GA. -->
:::moniker range="iotedge-2018-06"

## <a name="customize-certificate-lifetime"></a>A tanúsítvány élettartamának testreszabása

IoT Edge több esetben automatikusan hoz létre tanúsítványokat az eszközön, beleértve a következőket:

* Ha nem ad meg saját éles tanúsítványokat a IoT Edge telepítésekor és kiépítésekor, akkor a IoT Edge Security Manager automatikusan létrehoz egy **eszköz hitelesítésszolgáltatói tanúsítványát**. Ez az önaláírt tanúsítvány kizárólag fejlesztési és tesztelési célokra szolgál, nem éles környezetben. Ez a tanúsítvány 90 nap után lejár.
* A IoT Edge Security Manager az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa által aláírt **MUNKATERHELÉS hitelesítésszolgáltatói tanúsítványt** is létrehoz.

További információ a IoT Edge eszköz különböző tanúsítványainak funkciójával kapcsolatban: a [Azure IoT Edge tanúsítványok használatának ismertetése](iot-edge-certs.md).

A két automatikusan generált tanúsítvány esetében beállíthatja, hogy a konfigurációs fájlban a **auto_generated_ca_lifetime_days** jelzőt állítsa be a tanúsítványok élettartamára vonatkozó napok számának beállításához.

>[!NOTE]
>Létezik egy harmadik automatikusan generált tanúsítvány, amelyet a IoT Edge Security Manager hoz létre, a **IoT Edge hub-kiszolgáló tanúsítványát**. A tanúsítványnak mindig 90 napos élettartama van, de a lejárata előtt automatikusan megújul. A **auto_generated_ca_lifetime_days** érték nem befolyásolja ezt a tanúsítványt.

A megadott számú nap lejárata után IoT Edget újra kell indítani az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványának újbóli létrehozásához. Az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa nem újítható meg automatikusan.

1. Ha a tanúsítvány lejáratát az alapértelmezett 90 napnál nem korábbi értékre szeretné beállítani, adja hozzá az értéket napokban a konfigurációs fájl **tanúsítványok** szakaszához.

   ```yaml
   certificates:
     device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
     device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
     trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
     auto_generated_ca_lifetime_days: <value>
   ```

   > [!NOTE]
   > Jelenleg a libiothsm korlátozásai meggátolják a 2038 január 1-jén vagy azt követően lejáró tanúsítványok használatát.

1. Törölje a mappa tartalmát a `hsm` korábban létrehozott tanúsítványok eltávolításához.

   * Linux-és Linux-IoT Edge Windows rendszeren: `/var/lib/iotedge/hsm/certs` és `/var/lib/iotedge/hsm/cert_keys`

   * Windows-tárolók használatával: `C:\ProgramData\iotedge\hsm\certs` és `C:\ProgramData\iotedge\hsm\cert_keys`

1. Indítsa újra a IoT Edge szolgáltatást.

   * Linux-és Linux-IoT Edge Windows rendszeren:

   ```bash
   sudo systemctl restart iotedge
   ```

   * Windows-tárolók használata:

   ```powershell
   Restart-Service iotedge
   ```

1. Erősítse meg az élettartam beállítást.

   * Linux-és Linux-IoT Edge Windows rendszeren:

   ```bash
   sudo iotedge check --verbose
   ```

   * Windows-tárolók használata:

   ```powershell
   iotedge check --verbose
   ```

   Tekintse meg az **éles készültségi** egység kimenetét: tanúsítványok ellenőrzését, amely felsorolja, hogy hány nap elteltével járjon le az automatikusan létrehozott hitelesítésszolgáltatói tanúsítványok érvényessége.

:::moniker-end
<!-- end 1.1 -->

<!-- 
<!-- 1.2 --
:::moniker range=">=iotedge-2020-11"

1. To configure the certificate expiration to something other than the default 90 days, add the value in days to the **certificates** section of the config file.

   ```toml
   [certificates]
   device_ca_cert = "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
   device_ca_pk = "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
   trusted_ca_certs = "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
   auto_generated_ca_lifetime_days = <value>
   ```

1. Delete the contents of the `certd` and `keyd` folders to remove any previously generated certificates: `/var/lib/aziot/certd/certs` `/var/lib/aziot/keyd/keys`

1. Restart IoT Edge.

   ```bash
   sudo iotedge system restart
   ```

1. Confirm the new lifetime setting.

   ```bash
   sudo iotedge check --verbose
   ```

   Check the output of the **production readiness: certificates** check, which lists the number of days until the automatically generated device CA certificates expire.
:::moniker-end
<!-- end 1.2 --
-->

## <a name="next-steps"></a>Következő lépések

A tanúsítványok IoT Edge eszközön történő telepítése szükséges lépés a megoldás éles környezetben történő üzembe helyezése előtt. További információ a [IoT Edge-megoldás éles környezetben való üzembe helyezésének előkészítéséről](production-checklist.md).

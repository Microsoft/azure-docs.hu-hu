---
title: Eszközök automatikus kiépítése a DPS használatával X. 509 tanúsítványokkal – Azure IoT Edge | Microsoft Docs
description: Az X. 509 tanúsítványokkal tesztelheti a Azure IoT Edge automatikus eszköz-üzembe helyezését az eszközök kiépítési szolgáltatásával
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: kevindaw
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: contperf-fy21q2
ms.openlocfilehash: 44ea6546eb2099165071fd493ec8f890820c0688
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199830"
---
# <a name="create-and-provision-an-iot-edge-device-using-x509-certificates"></a>IoT Edge-eszköz létrehozása és kiépítése X. 509 tanúsítványok használatával

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Az [Azure IoT hub Device Provisioning Service (DPS)](../iot-dps/index.yml)segítségével az X. 509 tanúsítvánnyal automatikusan kiépítheti IoT Edge eszközeit. Ha nem ismeri az automatikus kiépítés folyamatát, a folytatás előtt tekintse át a [kiépítés](../iot-dps/about-iot-dps.md#provisioning-process) áttekintését.

Ebből a cikkből megtudhatja, hogyan hozhat létre egy eszköz-kiépítési szolgáltatást az X. 509 tanúsítványokkal egy IoT Edge eszközön a következő lépésekkel:

* Tanúsítványok és kulcsok generálása.
* Hozzon létre egy eszköz egyéni regisztrációját, vagy egy csoportos regisztrációt az eszközök készletén.
* Telepítse a IoT Edge futtatókörnyezetet, és regisztrálja az eszközt a IoT Hub.

Az X. 509 tanúsítványok az igazolási mechanizmusként való használata kiváló módszer a termelés méretezésére és az eszközök üzembe helyezésének egyszerűsítésére. Az X. 509 tanúsítványok általában megbízhatósági tanúsítványlánc szerint vannak rendezve. Egy önaláírt vagy megbízható főtanúsítványtól kezdve a lánc minden tanúsítványa aláírja a következő alacsonyabb tanúsítványt. Ez a minta létrehoz egy delegált megbízhatósági láncot a főtanúsítványból az egyes közbenső tanúsítványokon keresztül az eszközre telepített utolsó "levél" tanúsítványon.

## <a name="prerequisites"></a>Előfeltételek

* Aktív IoT Hub.
* A IoT Edge eszköz fizikai vagy virtuális eszköze.
* A [git](https://git-scm.com/download/) legújabb verziója telepítve van.
* Az Azure-beli IoT Hub Device Provisioning Service egy példánya, amely az IoT hubhoz van csatolva.
  * Ha nincs eszköz kiépítési szolgáltatási példánya, kövesse az [IOT hub DPS beállítása](../iot-dps/quick-setup-auto-provision.md)című témakör utasításait.
  * Miután futtatta az eszköz kiépítési szolgáltatását, másolja az **azonosító hatókör** értékét az Áttekintés lapról. Ezt az értéket használja a IoT Edge futtatókörnyezet konfigurálásakor.

## <a name="generate-device-identity-certificates"></a>Eszköz-azonosító tanúsítványok előállítása

Az eszköz-identitás tanúsítványa egy levélbeli tanúsítvány, amely egy megbízható tanúsítványlánc használatával csatlakozik az első X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítványhoz. Az eszköz identitási tanúsítványának köznapi névvel (CN) kell rendelkeznie ahhoz az eszköz-AZONOSÍTÓhoz, amelyhez az eszköznek az IoT hub-ban szüksége van.

Az eszköz-azonosító tanúsítványokat csak az IoT Edge eszköz kiépítési és az Azure-IoT Hub hitelesítő eszköz használatával lehet használni. A tanúsítványokat nem aláírják, ellentétben a IoT Edge eszköz által a moduloknak vagy a Leaf eszközöknek az ellenőrzés céljából megjelenített HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokkal. További információ: [Azure IoT Edge tanúsítvány-használati adatok](iot-edge-certs.md).

Az eszköz azonosító tanúsítványának létrehozása után két fájlt kell tartalmaznia: egy. cer vagy. PEM fájlt, amely tartalmazza a tanúsítvány nyilvános részét, valamint egy. cer vagy. PEM fájlt a tanúsítvány titkos kulcsával. Ha azt tervezi, hogy csoportos regisztrációt használ a DPS-ben, akkor a közbenső vagy legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány nyilvános részét is meg kell adni ugyanabban a tanúsítványlánc-megbízhatósági láncban.

A következő fájlokra van szükség az automatikus kiépítés beállításához az X. 509 használatával:

* Az eszköz identitásának tanúsítványa és a titkos kulcs tanúsítványa. Ha egyéni regisztrációt hoz létre, a rendszer feltölti az eszköz Identity tanúsítványát a DPS-be. A titkos kulcsot a IoT Edge futtatókörnyezet adja át.
* Egy teljes láncú tanúsítvány, amelynek tartalmaznia kell legalább az eszköz identitását és a köztes tanúsítványokat. A rendszer átadja a teljes lánc tanúsítványát a IoT Edge futtatókörnyezetnek.
* Egy köztes vagy legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány a megbízhatósági tanúsítványlánc alapján. A tanúsítványt a rendszer feltölti a DPS-be, ha létrehoz egy csoportos regisztrációt.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
> [!NOTE]
> Jelenleg a libiothsm korlátozásai meggátolják a 2038 január 1-jén vagy azt követően lejáró tanúsítványok használatát.

:::moniker-end

### <a name="use-test-certificates-optional"></a>Tesztelési tanúsítványok használata (nem kötelező)

Ha nincs elérhető hitelesítésszolgáltató az új identitási tanúsítványok létrehozásához, és szeretné kipróbálni ezt a forgatókönyvet, a Azure IoT Edge git-tárház olyan parancsfájlokat tartalmaz, amelyek segítségével tesztelési tanúsítványokat hozhat létre. Ezek a tanúsítványok csak fejlesztési tesztelésre vannak kialakítva, és nem használhatók éles környezetben.

A tesztelési tanúsítványok létrehozásához kövesse a [bemutató tanúsítványok létrehozása a IoT Edge eszköz funkcióinak teszteléséhez](how-to-create-test-certificates.md)című témakör lépéseit. A tanúsítvány-létrehozási parancsfájlok beállításához és a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány létrehozásához hajtsa végre a két szükséges szakaszt. Ezután kövesse az eszköz-identitás tanúsítványának létrehozásához szükséges lépéseket. Ha elkészült, a következő tanúsítványláncot és kulcspárt kell használnia:

Linux:

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

Windows:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

Mindkét tanúsítványra szükség van a IoT Edge eszközön. Ha egyéni regisztrációt fog használni a DPS-ben, akkor fel kell töltenie a. CERT. PEM fájlt. Ha a DPS szolgáltatásban csoportos regisztrációt fog használni, akkor a feltöltéshez ugyanahhoz a tanúsítványlánchez is szüksége lesz egy közbenső vagy legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványra. Ha bemutató tanúsítványokat használ, használja a tanúsítványt a `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` csoportos regisztrációhoz.

## <a name="create-a-dps-individual-enrollment"></a>Egyéni DPS-regisztráció létrehozása

A generált tanúsítványok és kulcsok használatával egyéni regisztrációt hozhat létre a DPS-ben egyetlen IoT Edge eszközhöz. Az egyéni regisztrációk az eszköz személyazonossági tanúsítványának nyilvános részét képezik, és megfelelnek az eszköz tanúsítványának.

Ha több IoT Edge eszközt szeretne kiépíteni, kövesse a következő szakaszban leírt lépéseket, és [hozzon létre egy DPS csoportos regisztrációt](#create-a-dps-group-enrollment).

Amikor létrehoz egy regisztrációt a DPS-ben, lehetősége van bejelenteni a **kezdeti eszköz kettős állapotát**. Az eszköz Twin-ben címkéket állíthat be az eszközök csoportosításához a megoldásban szükséges mérőszámok, például a régió, a környezet, a hely vagy az eszköz típusa alapján. Ezek a címkék [automatikus központi telepítések](how-to-deploy-at-scale.md)létrehozásához használhatók.

További információ az eszközök kiépítési szolgáltatásának regisztrálásáról: az [eszközök regisztrálásának kezelése](../iot-dps/how-to-manage-enrollments.md).

   > [!TIP]
   > Az Azure CLI-ben létrehozhat egy [beléptetést](/cli/azure/ext/azure-iot/iot/dps/enrollment) vagy egy [beléptetési csoportot](/cli/azure/ext/azure-iot/iot/dps/enrollment-group) , és az **Edge-kompatibilis** jelző használatával meghatározhatja, hogy egy eszköz vagy egy csoport IoT Edge eszköz-e.

1. A [Azure Portal](https://portal.azure.com)navigáljon a IoT hub Device Provisioning Service-példányához.

1. A **Beállítások** területen válassza a **regisztrációk kezelése** lehetőséget.

1. Válassza az **Egyéni regisztráció hozzáadása** lehetőséget, majd végezze el a következő lépéseket a beléptetés konfigurálásához:  

   * **Mechanizmus**: válassza az **X. 509** elemet.

   * **Elsődleges tanúsítvány. PEM vagy. cer fájl**: töltse fel a nyilvános fájlt az eszköz identitása tanúsítványból. Ha a parancsfájlokat a tesztelési tanúsítvány létrehozásához használta, válassza a következő fájlt:

      `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`

   * **IoT hub eszköz azonosítója**: adja meg az eszköz azonosítóját, ha szeretné. Az eszközök azonosítói segítségével megcélozhat egy különálló eszközt a modulok telepítéséhez. Ha nem adja meg az eszköz AZONOSÍTÓját, a rendszer az X. 509 tanúsítvány köznapi nevét (CN) használja.

   * **IoT Edge eszköz**: a **true (igaz** ) érték kiválasztásával deklarálhatja, hogy a beléptetés egy IoT Edge eszközre vonatkozik.

   * **Válassza ki az eszközhöz hozzárendelni kívánt IoT hubokat**: válassza ki azt a társított IoT hubot, amelyhez csatlakoztatni szeretné az eszközt. Több hub is kiválasztható, és az eszköz a kiválasztott foglalási szabályzatnak megfelelően lesz hozzárendelve az egyikhez.

   * **Kezdeti eszköz kettős állapota**: adjon hozzá egy címke értéket, amelyet hozzá szeretne adni az eszközhöz, ha szeretné. Az automatikus központi telepítéshez címkéket használhat az eszközök célcsoportjának célzásához. Például:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. Kattintson a **Mentés** gombra.

Most, hogy az eszközhöz regisztrálva van, a IoT Edge futtatókörnyezet automatikusan kiépítheti az eszközt a telepítés során. A IoT Edge eszköz beállításához folytassa a [IoT Edge futtatókörnyezet telepítése](#install-the-iot-edge-runtime) című szakaszát.

## <a name="create-a-dps-group-enrollment"></a>DPS csoportos regisztráció létrehozása

A generált tanúsítványok és kulcsok használatával csoportos regisztrációt hozhat létre a DPS-ben több IoT Edge eszközhöz. A csoportos regisztrációk egy köztes vagy legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt használnak az egyes eszköz-azonosító tanúsítványok létrehozásához használt megbízhatósági tanúsítványlánc alapján.

Ha ehelyett egyetlen IoT Edge eszközt szeretne kiépíteni, kövesse az előző szakaszban ismertetett lépéseket, [hozzon létre egy DPS egyéni regisztrációt](#create-a-dps-individual-enrollment).

Amikor létrehoz egy regisztrációt a DPS-ben, lehetősége van bejelenteni a **kezdeti eszköz kettős állapotát**. Az eszköz Twin-ben címkéket állíthat be az eszközök csoportosításához a megoldásban szükséges mérőszámok, például a régió, a környezet, a hely vagy az eszköz típusa alapján. Ezek a címkék [automatikus központi telepítések](how-to-deploy-at-scale.md)létrehozásához használhatók.

### <a name="verify-your-root-certificate"></a>A főtanúsítvány ellenőrzése

Beléptetési csoport létrehozásakor lehetősége van ellenőrzött tanúsítvány használatára. A DPS-tanúsítvánnyal ellenőrizheti, hogy rendelkezik-e a főtanúsítvány tulajdonjogával. További információ: [How to do proof of-of-of-of-the-of-for X. 509 hitelesítésszolgáltatói tanúsítványok](../iot-dps/how-to-verify-certificates.md).

1. A [Azure Portal](https://portal.azure.com)navigáljon a IoT hub Device Provisioning Service-példányához.

1. Válassza ki a **tanúsítványok** elemet a bal oldali menüben.

1. Új tanúsítvány hozzáadásához válassza a **Hozzáadás** lehetőséget.

1. Adjon egy rövid nevet a tanúsítványnak, majd keresse meg az X. 509 tanúsítvány nyilvános részét jelképező. cer vagy. PEM fájlt.

   Ha a bemutató tanúsítványait használja, töltse fel a `<wrkdir>/certs/azure-iot-test-only.root.ca.cert.pem` tanúsítványt.

1. Kattintson a **Mentés** gombra.

1. A tanúsítványnak mostantól szerepelnie kell a **tanúsítványok** lapon. Válassza ki a tanúsítvány adatainak megnyitásához.

1. Válassza az **ellenőrző kód előállítása** lehetőséget, majd másolja a generált kódot.

1. Akár a saját HITELESÍTÉSSZOLGÁLTATÓI tanúsítványát, akár a bemutató tanúsítványait használja, a IoT Edge adattárában található ellenőrzési eszközzel ellenőrizheti a birtoklás igazolását. Az ellenőrző eszköz a HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt használja egy olyan új tanúsítvány aláírására, amely a megadott ellenőrző kóddal rendelkezik tulajdonos neveként.

   * Windows:

     ```powershell
     New-CACertsVerificationCert "<verification code>"
     ```

   * Linux:

     ```bash
     ./certGen.sh create_verification_certificate <verification code>
     ```

1. A Azure Portal azonos tanúsítvány részletei lapján töltse fel az újonnan létrehozott ellenőrző tanúsítványt.

1. Válassza az **Ellenőrzés** lehetőséget.

### <a name="create-enrollment-group"></a>Beléptetési csoport létrehozása

További információ az eszközök kiépítési szolgáltatásának regisztrálásáról: az [eszközök regisztrálásának kezelése](../iot-dps/how-to-manage-enrollments.md).

1. A [Azure Portal](https://portal.azure.com)navigáljon a IoT hub Device Provisioning Service-példányához.

1. A **Beállítások** területen válassza a **regisztrációk kezelése** lehetőséget.

1. Válassza a **regisztrációs csoport hozzáadása** lehetőséget, majd végezze el a következő lépéseket a beléptetés konfigurálásához:

   * **Csoport neve**: adjon meg egy emlékezetes nevet a csoport beléptetéséhez.

   * **Igazolás típusa**: válassza a **tanúsítvány** elemet.

   * **IoT Edge eszköz**: válassza az **igaz** lehetőséget. Csoportos regisztráció esetén minden eszköznek IoT Edge eszköznek kell lennie, vagy egyikük sem lehet.

   * **Tanúsítvány típusa**: válassza ki a **hitelesítésszolgáltatói tanúsítvány** elemet, ha olyan hitelesített hitelesítésszolgáltatói tanúsítvánnyal rendelkezik, amely DPS vagy **köztes tanúsítvánnyal** rendelkezik, ha új fájlt szeretne feltölteni ehhez a regisztrációhoz.

   * **Elsődleges tanúsítvány**: Ha az utolsó szakaszban a hitelesítésszolgáltatói tanúsítvány lehetőséget választotta, válassza ki a tanúsítványt a legördülő listából. Ha a köztes tanúsítványt választotta, töltse fel a nyilvános fájlt egy HITELESÍTÉSSZOLGÁLTATÓI tanúsítványból az eszköz identitási tanúsítványainak létrehozásához használt megbízhatósági láncban.

   * **Válassza ki az eszközhöz hozzárendelni kívánt IoT hubokat**: válassza ki azt a társított IoT hubot, amelyhez csatlakoztatni szeretné az eszközt. Több hub is kiválasztható, és az eszköz a kiválasztott foglalási szabályzatnak megfelelően lesz hozzárendelve az egyikhez.

   * **Kezdeti eszköz kettős állapota**: adjon hozzá egy címke értéket, amelyet hozzá szeretne adni az eszközhöz, ha szeretné. Az automatikus központi telepítéshez címkéket használhat az eszközök célcsoportjának célzásához. Például:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. Kattintson a **Mentés** gombra.

Most, hogy az eszközhöz regisztrálva van, a IoT Edge futtatókörnyezet automatikusan kiépítheti az eszközt a telepítés során. Folytassa a következő szakasszal a IoT Edge eszköz beállításához.

## <a name="install-the-iot-edge-runtime"></a>Az IoT Edge-futtatókörnyezet telepítése

Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön üzembe van helyezve. Az összetevői tárolókban futnak, és lehetővé teszik további tárolók üzembe helyezését az eszközön, így a kód a peremhálózat szélén is futtatható.

Az eszköz kiépítéséhez kövesse az [Azure IoT Edge futtatókörnyezet telepítése](how-to-install-iot-edge.md)című témakör lépéseit, majd térjen vissza ehhez a cikkhez.

Az X. 509 kiépítés a DPS-vel csak IoT Edge 1.0.9 vagy újabb verzióban támogatott.

## <a name="configure-the-device-with-provisioning-information"></a>Az eszköz konfigurálása az üzembe helyezési adatokkal

Miután telepítette a futtatókörnyezetet az eszközre, konfigurálja az eszközt az általa használt információval az eszköz kiépítési szolgáltatásához való kapcsolódáshoz és a IoT Hubához.

A következő információk állnak készen:

* A DPS- **azonosító hatókörének** értéke. Ezt az értéket az Azure Portal DPS-példányának áttekintés lapjáról kérheti le.
* Az eszköz Identity Certificate Chain fájlja az eszközön.
* Az eszköz Identity Key fájlja az eszközön.
* Egy opcionális regisztrációs azonosító. Ha nincs megadva, az azonosító a köznapi név alapján van leképezve az eszköz identitásának tanúsítványában.

### <a name="linux-device"></a>Linuxos eszköz

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Nyissa meg a konfigurációs fájlt a IoT Edge eszközön.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. A fájl kiépítési konfigurációk szakaszának megkeresése. Jegyezze fel a DPS X. 509 tanúsítvány kiépítés sorait, és győződjön meg arról, hogy az egyéb kiépítési sorok megjegyzése megtörténik.

   A `provisioning:` sornak nem lehetnek korábbi szóközök, és a beágyazott elemeket két szóközzel kell behúzni.

   ```yml
   # DPS X.509 provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "x509"
   #   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. A, a és a értékének frissítése a `scope_id` `identity_cert` `identity_pk` DPS és az eszköz adataival.

   Ha hozzáadja az X. 509 tanúsítványt és a legfontosabb adatokat a config. YAML fájlhoz, az elérési utakat fájl URI-ként kell megadni. Például:

   `file:///<path>/identity_certificate_chain.pem`
   `file:///<path>/identity_key.pem`

1. Szükség esetén adjon meg egy `registration_id` eszközt az eszközhöz. Ellenkező esetben hagyja meg ezt a sort, hogy regisztrálja az eszközt az azonosító tanúsítvány CN-nevével.

1. Igény szerint a vagy a `always_reprovision_on_startup` `dynamic_reprovisioning` vonalak használatával konfigurálhatja az eszköz újraépítésének viselkedését. Ha egy eszköz úgy van beállítva, hogy a rendszer újraépítse az indítást, a rendszer mindig először a DPS-t próbálja kiépíteni, majd visszatér a kiépítési biztonsági mentéshez, ha az nem sikerül. Ha egy eszköz úgy van beállítva, hogy dinamikusan újra kiépítse magát, IoT Edge újraindítja és újraépíti, ha a rendszer újraépítési eseményt észlel. További információ: [IoT hub eszköz újraépítési fogalmai](../iot-dps/concepts-device-reprovision.md).

1. Mentse és zárjuk be a config. YAML fájlt.

1. Indítsa újra a IoT Edge futtatókörnyezetet, hogy az az eszközön végrehajtott összes konfigurációs módosítást felveszi.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1. -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Hozzon létre egy konfigurációs fájlt az eszközhöz a IoT Edge telepítésének részeként megadott sablonfájl alapján.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. Nyissa meg a konfigurációs fájlt a IoT Edge eszközön.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. A fájl **kiépítési** szakaszának megkeresése. Jegyezze fel az X. 509 tanúsítvánnyal rendelkező DPS-kiépítés sorait, és győződjön meg arról, hogy minden más kiépítési sor megjegyzésbe kerül.

   ```toml
   # DPS provisioning with X.509 certificate
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "x509"
   # registration_id = "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"

   identity_cert = "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"

   identity_pk = "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

1. A, a és a értékének frissítése a `id_scope` `identity_cert` `identity_pk` DPS és az eszköz adataival.

   Az azonosító tanúsítvány értéke fájl URI-ként is megadható, vagy az EST vagy egy helyi hitelesítésszolgáltató használatával dinamikusan kiállítható. A használandó formátum alapján csak egy sort adjon meg megjegyzésként.

   Az identitás titkos kulcsának értéke fájl URI-ként vagy PKCS # 11 URI-ként is megadható. A használandó formátum alapján csak egy sort adjon meg megjegyzésként.

   Ha PKCS # 11 URI-t használ, keresse meg a **PKCS # 11** szakaszt a konfigurációs fájlban, és adja meg a PKCS # 11 konfigurációjának adatait.

1. Szükség esetén adjon meg egy `registration_id` eszközt az eszközhöz. Ellenkező esetben hagyja meg a sort, hogy regisztrálja az eszközt az azonosító tanúsítvány köznapi nevével.

1. Mentse és zárja be a fájlt.

1. Alkalmazza a IoT Edge végrehajtott konfigurációs módosításokat.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

### <a name="windows-device"></a>Windows-eszköz

1. Nyisson meg egy PowerShell-ablakot rendszergazdai módban. Ügyeljen arra, hogy az IoT Edge telepítésekor a PowerShell AMD64-munkamenetét használja, nem a PowerShell (x86) rendszerre.

1. Az **inicializálás-IoTEdge** parancs konfigurálja a IoT Edge futtatókörnyezetet a gépen. A parancs alapértelmezett értéke a Windows-tárolók manuális kiépítés, ezért a `-DpsX509` jelzővel az X. 509 Tanúsítványos hitelesítéssel használhatja az automatikus kiépítés használatát.

   Cserélje le a, a és a érték helyőrzőjét a `{scope_id}` `{identity cert chain path}` DPS- `{identity key path}` példány megfelelő értékeire, valamint az eszköz fájlelérési útjaira.

   Adja hozzá a (z) lehetőséget, `-RegistrationId {registration_id}` Ha az eszköz azonosítóját nem az Identity tanúsítvány CN-neveként szeretné beállítani.

   `-ContainerOs Linux`Ha Linux-tárolókat használ Windows rendszeren, adja hozzá a paramétert.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsX509 -ScopeId {scope ID} -X509IdentityCertificate {identity cert chain path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >A konfigurációs fájl a tanúsítvány és a kulcs adatait fájl URI-ként tárolja. Azonban a Initialize-IoTEdge parancs kezeli ezt a formázási lépést, így megadhatja a tanúsítvány és a kulcs fájljainak abszolút elérési útját az eszközön.

## <a name="verify-successful-installation"></a>Sikeres telepítés ellenőrzése

Ha a futtatókörnyezet sikeresen elindult, beléphet a IoT Hubba, és megkezdheti a IoT Edge modulok üzembe helyezését az eszközön.

Ellenőrizheti, hogy a rendszer használta-e a Device kiépítési szolgáltatásban létrehozott egyéni regisztrációt. Navigáljon az eszköz kiépítési szolgáltatási példányához a Azure Portal. Nyissa meg a regisztráció részleteit a létrehozott egyéni regisztrációhoz. Figyelje meg, hogy a regisztráció állapota **hozzá van rendelve** , és az eszköz azonosítója megjelenik.

Az eszközön az alábbi parancsokkal ellenőrizheti, hogy a futtatókörnyezet telepítése és elindítása sikeresen megtörtént-e.

### <a name="linux-device"></a>Linuxos eszköz

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Ellenőrizze az IoT Edge-szolgáltatás állapotát.

```cmd/sh
systemctl status iotedge
```

A szolgáltatási naplók vizsgálata.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Futó modulok listázása.

```cmd/sh
iotedge list
```
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Ellenőrizze az IoT Edge-szolgáltatás állapotát.

```cmd/sh
sudo iotedge system status
```

A szolgáltatási naplók vizsgálata.

```cmd/sh
sudo iotedge system logs
```

Futó modulok listázása.

```cmd/sh
sudo iotedge list
```
:::moniker-end

### <a name="windows-device"></a>Windows-eszköz

Ellenőrizze az IoT Edge-szolgáltatás állapotát.

```powershell
Get-Service iotedge
```

A szolgáltatási naplók vizsgálata.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Futó modulok listázása.

```powershell
iotedge list
```

## <a name="next-steps"></a>Következő lépések

Az eszköz kiépítési szolgáltatásának beléptetési folyamata lehetővé teszi, hogy az eszköz AZONOSÍTÓját és az eszköz Twin címkéit az új eszköz kiépítésekor egy időben állítsa be. Ezeket az értékeket használhatja az egyes eszközök vagy eszközök automatikus eszközkezelés használatával történő megcélzásához. Megtudhatja, hogyan [helyezheti üzembe és figyelheti IoT Edge-modulok méretezését a Azure Portal vagy az](how-to-deploy-at-scale.md) [Azure CLI használatával](how-to-deploy-cli-at-scale.md).

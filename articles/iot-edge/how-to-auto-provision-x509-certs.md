---
title: Eszközök automatikus kiépítése DPS-sel X.509-tanúsítványokkal – Azure IoT Edge | Microsoft Docs
description: X.509-tanúsítványok használata az automatikus eszközátépítés teszteléséhez Azure IoT Edge Device Provisioning Service-szel
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: kevindaw
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: contperf-fy21q2
ms.openlocfilehash: f3c783c57b49b45943882703aec6d735d12bf830
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481956"
---
# <a name="create-and-provision-an-iot-edge-device-using-x509-certificates"></a>Eszköz létrehozása és kiépítése IoT Edge X.509-tanúsítványokkal

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

A [Azure IoT Hub Device Provisioning Service (DPS)](../iot-dps/index.yml)segítségével automatikusan kiépíthet IoT Edge X.509-tanúsítványokkal. Ha nem ismeri az automatikus kiépítés folyamatát, a folytatás előtt tekintse át a [kiépítés](../iot-dps/about-iot-dps.md#provisioning-process) áttekintését.

Ez a cikk bemutatja, hogyan hozhat létre device Provisioning Service-regisztrációt X.509-tanúsítványokkal egy IoT Edge eszközön a következő lépésekkel:

* Tanúsítványok és kulcsok létrehozása.
* Egyéni regisztrációt hozhat létre egy eszközhöz, vagy csoportos regisztrációt egy eszközkészlethez.
* Telepítse a IoT Edge, és regisztrálja az eszközt a IoT Hub.

Az X.509-tanúsítványok igazolási mechanizmusként való használata kiváló módszer az éles környezet méretezésére és az eszközátépítés egyszerűsítésére. Az X.509-tanúsítványok általában egy megbízhatósági láncba vannak rendezve. Egy önaírt vagy megbízható főtanúsítványtól kezdve a láncban minden tanúsítvány aláírja a következő alacsonyabb szintű tanúsítványt. Ez a minta egy delegált megbízhatósági láncot hoz létre a főtanúsítványtól az egyes köztes tanúsítványokon keresztül az eszközön telepített végső levéltanúsítványig.

## <a name="prerequisites"></a>Előfeltételek

* Aktív IoT Hub.
* Fizikai vagy virtuális eszköz, amely IoT Edge eszköz.
* A Git legújabb [telepített](https://git-scm.com/download/) verziója.
* Az Azure-ban IoT Hub Device Provisioning Service példánya, amely az IoT Hubhoz kapcsolódik.
  * Ha nem rendelkezik Device Provisioning Service-példánysal, kövesse a Következő útmutató [utasításait: Set up the IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).
  * Miután futtatta a Device Provisioning Service-t, másolja az Azonosító hatóköre **értéket** az áttekintő lapról. Ezt az értéket akkor használja, amikor a IoT Edge konfigurálja.

## <a name="generate-device-identity-certificates"></a>Eszközidentitás-tanúsítványok létrehozása

Az eszközidentitás-tanúsítvány egy levél-tanúsítvány, amely egy tanúsítványláncon keresztül kapcsolódik az első X.509 hitelesítésszolgáltatói (CA) tanúsítványhoz. Az eszközidentitási tanúsítványban a köznévnek (CN) arra az eszközazonosítóra kell beállítania, hogy az eszköz az IoT Hubon legyen.

Az eszközidentitási tanúsítványok csak az eszköz IoT Edge és az eszköz hitelesítéséhez használhatók Azure IoT Hub. Ezek nem aláíró tanúsítványok, ellentétben azokkal a hitelesítésszolgáltatói tanúsítványokkal, IoT Edge az eszköz modulokat vagy levéleszközöket biztosít az ellenőrzéshez. További információkért lásd a [Azure IoT Edge részleteit.](iot-edge-certs.md)

Az eszközidentitás-tanúsítvány létrehozása után két fájlnak kell lennie: egy .cer vagy .pem fájllal, amely a tanúsítvány nyilvános részét tartalmazza, valamint egy .cer vagy .pem fájllal, amely tartalmazza a tanúsítvány titkos kulcsát. Ha csoportos regisztrációt tervez használni a DPS-ben, szüksége lesz egy köztes vagy fő hitelesítésszolgáltatói tanúsítvány nyilvános részére is ugyanabban a megbízhatósági láncban.

Az automatikus kiépítés X.509-ben való beállításhoz a következő fájlokra lesz szüksége:

* Az eszközidentitás-tanúsítvány és annak titkoskulcs-tanúsítványa. Ha egyéni regisztrációt hoz létre, a rendszer feltölti az eszközidentitási tanúsítványt a DPS-be. A titkos kulcsot a IoT Edge számára.
* Teljes láncú tanúsítvány, amelynek legalább az eszközidentitást és a köztes tanúsítványokat kell benne lennie. A teljes láncú tanúsítványt a IoT Edge futtatja.
* Egy köztes vagy fő hitelesítésszolgáltatói tanúsítvány a megbízhatósági láncból. Ez a tanúsítvány akkor lesz feltöltve a DPS-be, ha csoportos regisztrációt hoz létre.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
> [!NOTE]
> A libiisesm korlátozása jelenleg megakadályozza a tanúsítványok használatát, amelyek 2038. január 1-jén vagy azt követően lejárnak.

:::moniker-end

### <a name="use-test-certificates-optional"></a>Teszttanúsítványok használata (nem kötelező)

Ha nem rendelkezik hitelesítésszolgáltatóval új identitástanúsítványok létrehozásához, és ki szeretné próbálni ezt a forgatókönyvet, a Azure IoT Edge git-adattár olyan szkripteket tartalmaz, amelyek használatával teszttanúsítványokat hozhat létre. Ezek a tanúsítványok csak fejlesztési teszteléshez vannak kialakítva, és nem használhatók éles környezetben.

A teszttanúsítványok létrehozásához kövesse a Bemutató tanúsítványok létrehozása [az eszköz funkcióinak teszteléséhez IoT Edge lépéseit.](how-to-create-test-certificates.md) A tanúsítvány-létrehozási parancsfájlok beállításához és a legfelső szintű hitelesítésszolgáltatói tanúsítvány létrehozásához töltse ki a két szükséges szakaszt. Ezután kövesse az eszközidentitás-tanúsítvány létrehozásához szükséges lépéseket. Ha elkészült, a következő tanúsítványlánc- és kulcspárnak kell lennie:

Linux:

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

Windows:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

Mindkét tanúsítványra szüksége lesz a IoT Edge eszközön. Ha egyéni regisztrációt használ a DPS-ben, akkor feltölti a .cert.pem fájlt. Ha csoportos regisztrációt fog használni a DPS-ben, akkor a feltöltéshez egy köztes vagy fő hitelesítésszolgáltatói tanúsítványra is szüksége lesz ugyanabban a megbízhatósági láncban. Ha bemutató tanúsítványokat használ, használja a tanúsítványt `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` a csoportregisztráláshoz.

## <a name="create-a-dps-individual-enrollment"></a>DPS egyéni regisztráció létrehozása

A létrehozott tanúsítványokkal és kulcsokkal egyéni regisztrációt hozhat létre a DPS-ben egyetlen IoT Edge eszközhöz. Az egyéni regisztrációk az eszköz identitási tanúsítványának nyilvános részét az eszközön található tanúsítvánnyal egyeznek meg.

Ha több regisztrációs eszközt is ki IoT Edge, kövesse [a KÖVETKEZŐ, DPS-csoportos](#create-a-dps-group-enrollment)regisztráció létrehozása című szakaszban található lépéseket.

Amikor DPS-regisztrációt hoz létre, lehetősége van kezdeti ikerállapot **deklarálni a kezdeti ikereszköz-állapotot.** Az ikereszközön címkéket állíthat be az eszközök csoportosításához a megoldásban szükséges metrika (például régió, környezet, hely vagy eszköztípus) alapján. Ezek a címkék automatikus központi [telepítések létrehozására használhatók.](how-to-deploy-at-scale.md)

A Device Provisioning Service-regisztrációval kapcsolatos további információkért lásd: [Az eszközregisztrációs szolgáltatás kezelése.](../iot-dps/how-to-manage-enrollments.md)

   > [!TIP]
   > Az Azure CLI-ben létrehozhat [](/cli/azure/iot/dps/enrollment) egy regisztrációt vagy egy  regisztrációs csoportot, és a peremhálózati jelölővel megadhatja, hogy egy eszköz vagy eszközcsoport egy IoT Edge eszköz. [](/cli/azure/iot/dps/enrollment-group)

1. A [Azure Portal](https://portal.azure.com)keresse meg a IoT Hub Device Provisioning Service.

1. A **Beállítások alatt** válassza a **Regisztrációk kezelése lehetőséget.**

1. Válassza **az Egyéni regisztráció hozzáadása lehetőséget,** majd a regisztráció konfiguráláshoz kövesse az alábbi lépéseket:  

   * **Mechanizmus:** Válassza **az X.509 lehetőséget.**

   * **Elsődleges tanúsítvány .pem- vagy .cer-fájl:** Töltse fel a nyilvános fájlt az eszközidentitás-tanúsítványból. Ha a szkriptek használatával generált teszt tanúsítványt, válassza a következő fájlt:

      `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`

   * **IoT Hub Eszközazonosító:** Ha szeretné, adja meg az eszköz azonosítóját. Az eszköz-adatokat arra használhatja, hogy egy adott eszközt célozza meg a modulok üzembe helyezéséhez. Ha nem ad meg eszközazonosítót, a rendszer az X.509-tanúsítvány köznevét (CN) használja.

   * **IoT Edge eszköz:** Válassza az **Igaz** lehetőséget annak deklarálhoz, hogy a regisztráció egy IoT Edge eszközhöz.

   * Válassza ki azokat az **IoT Hubokat,** amelyekhez az eszköz hozzárendelhető: Válassza ki azt a társított IoT Hubot, amelyekhez csatlakoztatni szeretné az eszközt. Több központot is kiválaszthat, és az eszköz a kiválasztott kiosztási szabályzatnak megfelelően hozzá lesz rendelve ezek egyikéhez.

   * **Kezdeti ikereszköz-állapot:** Ha szeretné, adjon hozzá egy címkeértéket az ikereszközhöz. Címkék használatával megcélzott eszközcsoportokat az automatikus üzembe helyezéshez. Például:

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

Most, hogy létezik regisztráció ehhez az eszközhöz, a IoT Edge a telepítés során automatikusan kiépítheti az eszközt. Folytassa az [Install the IoT Edge runtime (A IoT Edge-futtatás](#install-the-iot-edge-runtime) telepítése) című szakaszra a IoT Edge beállítását.

## <a name="create-a-dps-group-enrollment"></a>DPS-csoportregisztrálás létrehozása

A létrehozott tanúsítványokkal és kulcsokkal csoportos regisztrációt hozhat létre a DPS-ben több IoT Edge eszköz számára. A csoportos regisztrációk egy köztes vagy fő hitelesítésszolgáltatói tanúsítványt használnak az egyéni eszközidentitás-tanúsítványok létrehozásához használt megbízhatósági láncból.

Ha ehelyett egyetlen önálló regisztrációs eszközt IoT Edge, kövesse az előző, DPS egyéni regisztráció létrehozása [című szakaszban található lépéseket.](#create-a-dps-individual-enrollment)

Amikor létrehoz egy regisztrációt a DPS-ben, lehetősége van kezdeti ikerállapot **deklarálására.** Az ikereszközön címkéket állíthat be az eszközök csoportosításához a megoldásban szükséges metrika (például régió, környezet, hely vagy eszköztípus) alapján. Ezek a címkék automatikus központi [telepítések létrehozására használhatók.](how-to-deploy-at-scale.md)

### <a name="verify-your-root-certificate"></a>A főtanúsítvány ellenőrzése

Regisztrációs csoport létrehozásakor lehetősége van ellenőrzött tanúsítványt használni. A DPS-sel úgy ellenőrizheti a tanúsítványt, hogy igazolja, hogy Ön a főtanúsítvány tulajdonosa. További információ: [Tulajdonlástanúsítványok használata X.509 hitelesítésszolgáltatói tanúsítványokhoz.](../iot-dps/how-to-verify-certificates.md)

1. A [Azure Portal](https://portal.azure.com)keresse meg a IoT Hub Device Provisioning Service.

1. A **bal oldali** menüben válassza a Tanúsítványok lehetőséget.

1. Új **tanúsítvány hozzáadásához** válassza a Hozzáadás lehetőséget.

1. Adjon egy rövid nevet a tanúsítványnak, majd keresse meg az X.509-tanúsítvány nyilvános részét képviselő .cer vagy .pem fájlt.

   Ha bemutatótanúsítványokat használ, töltse fel a `<wrkdir>/certs/azure-iot-test-only.root.ca.cert.pem` tanúsítványt.

1. Kattintson a **Mentés** gombra.

1. A tanúsítványnak most már fel kell sorolva lennie a Tanúsítványok **lapon.** Válassza ki a tanúsítvány részleteinek megnyitásához.

1. Válassza **az Ellenőrző kód létrehozása lehetőséget,** majd másolja a létrehozott kódot.

1. Akár saját hitelesítésszolgáltatói tanúsítványt hozott, akár bemutatótanúsítványokat használ, az IoT Edge tárházban biztosított ellenőrző eszközzel ellenőrizheti a birtoklási igazolást. Az ellenőrző eszköz a hitelesítésszolgáltatói tanúsítvány használatával ír alá egy új tanúsítványt, amely tulajdonosnévként a megadott ellenőrző kódot tartalmazza.

   * Windows:

     ```powershell
     New-CACertsVerificationCert "<verification code>"
     ```

   * Linux:

     ```bash
     ./certGen.sh create_verification_certificate <verification code>
     ```

1. A tanúsítványadatok ugyanazon lapján töltse fel Azure Portal újonnan létrehozott ellenőrző tanúsítványt.

1. Válassza az **Ellenőrzés** lehetőséget.

### <a name="create-enrollment-group"></a>Regisztrációs csoport létrehozása

A Device Provisioning Service-regisztrációval kapcsolatos további információkért lásd: [Az eszközregisztrációs szolgáltatás kezelése.](../iot-dps/how-to-manage-enrollments.md)

1. A [Azure Portal](https://portal.azure.com)keresse meg a IoT Hub Device Provisioning Service.

1. A **Beállítások alatt** válassza a **Regisztrációk kezelése lehetőséget.**

1. Válassza **a Regisztrációs csoport hozzáadása lehetőséget,** majd a regisztráció konfiguráláshoz kövesse az alábbi lépéseket:

   * **Csoport neve:** Adjon meg egy könnyen megjegyezhető nevet a csoportregisztráláshoz.

   * **Igazolás típusa:** Válassza a Tanúsítvány **lehetőséget.**

   * **IoT Edge:** Válassza az Igaz **lehetőséget.** Csoportregisztrálás esetén minden eszközt külön IoT Edge eszközöknek kell lennie, vagy egyik sem lehet.

   * **Tanúsítvány típusa:** Válassza a **hitelesítésszolgáltatói** tanúsítvány lehetőséget, ha rendelkezik  DPS-sel tárolt ellenőrzött hitelesítésszolgáltatói tanúsítvánnyal, vagy köztes tanúsítvánnyal, ha csak ehhez a regisztrációhoz szeretne feltölteni egy új fájlt.

   * **Elsődleges tanúsítvány:** Ha az előző szakaszban a hitelesítésszolgáltatói tanúsítványt választotta, válassza ki a tanúsítványt a legördülő listából. Ha köztes tanúsítványt választ, töltse fel a nyilvános fájlt egy hitelesítésszolgáltatói tanúsítványból az eszközidentitás-tanúsítványok létrehozásához használt tanúsítványláncba.

   * Válassza ki azokat az **IoT Hubokat,** amelyekhez az eszköz hozzárendelhető: Válassza ki azt a társított IoT Hubot, amelyekhez csatlakoztatni szeretné az eszközt. Több központot is kiválaszthat, és az eszköz a kiválasztott kiosztási szabályzatnak megfelelően hozzá lesz rendelve ezek egyikéhez.

   * **Kezdeti ikereszköz-állapot:** Ha szeretné, adjon hozzá egy címkeértéket az ikereszközhöz. Címkék használatával megcélzott eszközcsoportokat az automatikus üzembe helyezéshez. Például:

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

Most, hogy létezik regisztráció ehhez az eszközhöz, a IoT Edge a telepítés során automatikusan kiépítheti az eszközt. Folytassa a következő szakasz lépésével, és állítsa be IoT Edge eszközét.

## <a name="install-the-iot-edge-runtime"></a>Az IoT Edge-futtatókörnyezet telepítése

Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön üzembe van helyezve. Az összetevői tárolókban futnak, és lehetővé teszik további tárolók üzembe helyezését az eszközön, hogy kódot futtathat a peremhálózaton.

Kövesse az [Install the Azure IoT Edge runtime](how-to-install-iot-edge.md)(Az eszköz telepítése) Azure IoT Edge, majd térjen vissza ehhez a cikkhez az eszköz üzembe lépéséhez.

Az X.509 DPS-sel való kiépítése csak az 1.0.9-es vagy újabb IoT Edge támogatott.

## <a name="configure-the-device-with-provisioning-information"></a>Az eszköz konfigurálása kiépítési információkkal

Miután telepítette a futásidőt az eszközön, konfigurálja az eszközt a Device Provisioning Service-hez való csatlakozáshoz használt információkkal, és IoT Hub.

Készítse elő a következő információkat:

* A **DPS-azonosító hatóköre** érték. Ezt az értéket a DPS-példány áttekintő oldalának a Azure Portal.
* Az eszközidentitás tanúsítványláncfájlja az eszközön.
* Az eszköz identitáskulcsfájlja az eszközön.
* Nem kötelező regisztrációs azonosító. Ha nincs megadva, a rendszer az eszközidentitás-tanúsítványban található köznévből lekéri az azonosítót.

### <a name="linux-device"></a>Linux-eszköz

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Nyissa meg a konfigurációs fájlt a IoT Edge eszközön.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Keresse meg a fájl kiépítési konfigurációk szakaszát. Tegye megjegyzésbe a DPS X.509-tanúsítvány létesítés sorait, és ügyeljen arra, hogy a többi kiépítési sor megjegyzésbe legyen véve.

   A sornak nem szabad megelőző szóközt tartalmazni, a beágyazott elemeket pedig két `provisioning:` szóközt kell behúzásra behúzásra.

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

1. Frissítse a `scope_id` , és értékét a DPS és az `identity_cert` eszköz `identity_pk` adataival.

   Amikor hozzáadja az X.509-tanúsítványt és a kulcsadatokat a config.yaml fájlhoz, az elérési utakat fájl URI-ként kell biztosítani. Például:

   `file:///<path>/identity_certificate_chain.pem`
   `file:///<path>/identity_key.pem`

1. Ha szükséges, adjon meg egy `registration_id` et az eszközhöz. Ellenkező esetben hagyja megjegyzésben ezt a sort, hogy az eszközt az identitás tanúsítványának CN-nevével regisztrálja.

1. Ha szükséges, a vagy a sorokkal konfigurálhatja az eszköz újra `always_reprovision_on_startup` `dynamic_reprovisioning` kiépítő viselkedését. Ha egy indításkor az eszköz újra kiépítésre van beállítva, először mindig megpróbálja kiépítni a DPS-t, majd ha ez sikertelen, akkor vissza fog állítani a kiépítési biztonsági mentésre. Ha egy eszköz úgy van beállítva, hogy dinamikusan újra kiépíti magát, a rendszer újraindítja IoT Edge és újból kiépíti, ha a rendszer újra kiépíti az eseményt. További információkért [lásd: IoT Hub eszköz-újraosztás fogalmai.](../iot-dps/concepts-device-reprovision.md)

1. Mentse és zárja be a config.yaml fájlt.

1. Indítsa újra IoT Edge futásidejű rendszert, hogy az az eszközön végrehajtott összes konfigurációs módosítást átvegye.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1. -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Hozzon létre egy konfigurációs fájlt az eszközhöz egy sablonfájl alapján, amely a IoT Edge része.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. Nyissa meg a konfigurációs fájlt a IoT Edge eszközön.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Keresse meg **a fájl Kiépítés** szakaszát. Az X.509-tanúsítvánnyal való DPS-kiépítés sorait tegye megjegyzésbe, és ügyeljen arra, hogy a többi kiépítési sor megjegyzésként legyen véve.

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

1. Frissítse a `id_scope` , `identity_cert` és `identity_pk` értékeit a DPS és az eszköz adataival.

   Az identitás tanúsítványának értéke meg lehet adni fájl URI-ként, vagy dinamikusan kibocsátható az EST vagy egy helyi hitelesítésszolgáltató használatával. A használni kívánt formátumtól függően csak egy sort használjon.

   Az identitás titkos kulcsának értéke fájl URI-ként vagy PKCS#11 URI-ként is meg lehet adni. A használni kívánt formátumtól függően csak egy sort használjon.

   Ha PKCS#11 URI-t használ, keresse meg a konfigurációs fájl **PKCS#11** szakaszát, és adja meg a PKCS#11 konfigurációval kapcsolatos információkat.

1. Ha szükséges, adjon meg egy `registration_id` et az eszköz számára. Ellenkező esetben ezt a sort megjegyzésként hagyja megjegyzésként, hogy az eszközt az identitás tanúsítványának közkeletű nevével regisztrálja.

1. Mentse és zárja be a fájlt.

1. Alkalmazza a konfigurációs módosításokat, amelyek a IoT Edge.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

### <a name="windows-device"></a>Windows-eszköz

1. Nyisson meg egy PowerShell-ablakot rendszergazdai módban. A PowerShell telepítésekor mindenképpen AMD64-munkamenetet használjon a PowerShell IoT Edge (x86) telepítésekor.

1. Az **Initialize-IoTEdge** parancs konfigurálja a IoT Edge futtatáskor a gépen. A parancs alapértelmezett beállítása a Windows-tárolók manuális kiépítése, ezért használja a jelzőt az automatikus kiépítés `-DpsX509` X.509-tanúsítványhitelesítéssel való használatára.

   Cserélje le a , és helyőrzőértékeket a DPS-példány megfelelő értékeire és az eszközön található `{scope_id}` `{identity cert chain path}` `{identity key path}` fájlútvonalakra.

   Adja hozzá a et, ha az eszközazonosítót nem az identitás `-RegistrationId {registration_id}` tanúsítványának CN-neveként szeretné beállítani.

   Ha `-ContainerOs Linux` Windows rendszeren használ Linux-tárolókat, adja hozzá a paramétert.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsX509 -ScopeId {scope ID} -X509IdentityCertificate {identity cert chain path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >A konfigurációs fájl fájl URI-ként tárolja a tanúsítványt és a kulcsadatokat. Azonban a Initialize-IoTEdge parancs kezeli ezt a formázási lépést, így meg lehet adni az eszközön található tanúsítvány és kulcsfájlok abszolút elérési útját.

## <a name="verify-successful-installation"></a>A sikeres telepítés ellenőrzése

Ha a futásidejű modul sikeresen elindult, a saját IoT Hub üzembe helyezheti IoT Edge modulokat az eszközén.

Ellenőrizheti, hogy a Device Provisioning Service-ban létrehozott egyéni regisztrációt használták-e. Lépjen a Device Provisioning Service-példányra a Azure Portal. Nyissa meg a létrehozott egyéni regisztráció regisztrációs adatait. Figyelje meg, hogy a regisztráció állapota hozzá van **rendelve,** és az eszközazonosító megjelenik a listában.

Az alábbi parancsokkal ellenőrizze az eszközön, hogy a futásidő sikeresen telepítve és elindítva lett-e.

### <a name="linux-device"></a>Linux-eszköz

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Ellenőrizze az IoT Edge-szolgáltatás állapotát.

```cmd/sh
systemctl status iotedge
```

Vizsgálja meg a szolgáltatásnaplókat.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

List running modules.

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

Vizsgálja meg a szolgáltatásnaplókat.

```cmd/sh
sudo iotedge system logs
```

List running modules.

```cmd/sh
sudo iotedge list
```
:::moniker-end

### <a name="windows-device"></a>Windows-eszköz

Ellenőrizze az IoT Edge-szolgáltatás állapotát.

```powershell
Get-Service iotedge
```

Vizsgálja meg a szolgáltatásnaplókat.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

List running modules.

```powershell
iotedge list
```

## <a name="next-steps"></a>Következő lépések

A Device Provisioning Service regisztrációs folyamata lehetővé teszi az eszközazonosító és az ikereszköz-címkék beállítását az új eszköz kiépítésekor. Ezeket az értékeket használhatja az egyes eszközök vagy eszközcsoportok automatikus eszközkezelés használatával való megcélzott kezelésére. Ismerje meg, hogyan [helyezhet üzembe és IoT Edge modulokat](how-to-deploy-at-scale.md) nagy méretekben a Azure Portal vagy az Azure CLI [használatával.](how-to-deploy-cli-at-scale.md)

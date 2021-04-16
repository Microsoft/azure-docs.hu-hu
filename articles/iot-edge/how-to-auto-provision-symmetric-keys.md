---
title: Eszköz kiépítése szimmetrikus kulcsú igazolással – Azure IoT Edge
description: Szimmetrikus kulcsú igazolás használata a Device Provisioning Service-Azure IoT Edge eszköz automatikus eszközátépítésének teszteléséhez
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 66e1e561c14b169d41028e151ac054888830b881
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481973"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>Eszköz létrehozása és IoT Edge szimmetrikus kulcsú igazolással

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Azure IoT Edge a [Device Provisioning](../iot-dps/index.yml) Service-sel automatikusan kiépítheti az eszközöket, csakúgy, mint a peremhálózati eszközök esetében. Ha nem ismeri az automatikus kiépítés folyamatát, a folytatás előtt tekintse át a [kiépítés](../iot-dps/about-iot-dps.md#provisioning-process) áttekintését.

Ez a cikk bemutatja, hogyan hozhat létre a Device Provisioning Service egyéni vagy csoportos regisztrációját szimmetrikus kulcsú igazolással egy IoT Edge eszközön a következő lépésekkel:

* Hozza létre a IoT Hub Device Provisioning Service (DPS) példányát.
* Hozzon létre egy regisztrációt az eszközhöz.
* Telepítse a IoT Edge, és csatlakozzon a IoT Hub.

A szimmetrikus kulcsú igazolás egy egyszerű módszer az eszközök Device Provisioning Service-példányokkal való hitelesítésére. Ez az igazolási módszer "Hello world" élményt jelent az olyan fejlesztők számára, akik még nem rendelkeznek szigorú biztonsági követelményekkel, vagy nem rendelkeznek szigorú biztonsági követelményekkel. A [TPM-](../iot-dps/concepts-tpm-attestation.md) vagy [X.509-tanúsítványokkal](../iot-dps/concepts-x509-attestation.md) használt eszköztanúsítvány biztonságosabb, és szigorúbb biztonsági követelményekhez kell használni.

## <a name="prerequisites"></a>Előfeltételek

* Aktív IoT Hub
* Fizikai vagy virtuális eszköz

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>A IoT Hub Device Provisioning Service

Hozzon létre egy új azure IoT Hub Device Provisioning Service példányt, és csatolja az IoT Hubhoz. Kövesse a Következő útmutató [utasításait: Set up the IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).

Miután futtatta a Device Provisioning Service-t, másolja az Azonosító hatóköre **értéket** az áttekintő lapról. Ezt az értéket akkor használja, amikor a IoT Edge konfigurálja.

## <a name="choose-a-unique-registration-id-for-the-device"></a>Egyedi regisztrációs azonosító kiválasztása az eszközhöz

Az egyes eszközök azonosításához egyedi regisztrációs azonosítót kell meghatározni. Használhatja a MAC-címet, a sorozatszámot vagy az eszközről származó egyedi adatokat.

Ebben a példában egy MAC-cím és egy sorozatszám kombinációját használjuk, amely a következő sztringet használja a regisztrációs azonosítóhoz: `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6` .

Hozzon létre egy egyedi regisztrációs azonosítót az eszközhöz. Az érvényes karakterek a kisbetűs alfanumerikus karakterek és a kötőjel ('-').

## <a name="create-a-dps-enrollment"></a>DPS-regisztráció létrehozása

Az eszköz regisztrációs azonosítójával egyéni regisztrációt hozhat létre a DPS-ben.

Amikor létrehoz egy regisztrációt a DPS-ben, lehetősége van kezdeti ikerállapot **deklarálására.** Az ikereszközben beállíthatja, hogy a címkék a megoldásban szükséges metrikák (például régió, környezet, hely vagy eszköztípus) alapján csoportosítják az eszközöket. Ezek a címkék az automatikus [központi telepítések létrehozására használhatók.](how-to-deploy-at-scale.md)

> [!TIP]
> A csoportos regisztrációk szimmetrikus kulcsú igazolás használata esetén is lehetségesek, és az egyes regisztrációkhoz hasonló döntéseket kell meghozniuk.

1. A [Azure Portal](https://portal.azure.com)nyissa meg a IoT Hub Device Provisioning Service.

1. A **Beállítások alatt** válassza a **Regisztrációk kezelése lehetőséget.**

1. Válassza **az Egyéni regisztráció hozzáadása lehetőséget,** majd a regisztráció konfiguráláshoz kövesse az alábbi lépéseket:  

   1. A **Mechanizmus mezőben** válassza a **Szimmetrikus kulcs lehetőséget.**

   1. Jelölje be **a Kulcsok automatikus létrehozása jelölőnégyzetet.**

   1. Adja meg **az eszközhöz** létrehozott regisztrációs azonosítót.

   1. Ha **szeretné, IoT Hub** eszköz eszközazonosítóját. Az eszköz-adatokat arra használhatja, hogy egy adott eszközt célozza meg a modulok üzembe helyezéséhez. Ha nem ad meg eszközazonosítót, a rendszer a regisztrációs azonosítót használja.

   1. Válassza **az Igaz** lehetőséget annak deklarálhoz, hogy a regisztráció egy IoT Edge eszközre. Csoportregisztrálás esetén minden eszközt külön IoT Edge kell lennie, vagy egyik sem lehet.

      > [!TIP]
      > Az Azure CLI-ben létrehozhat [](/cli/azure/iot/dps/enrollment) egy regisztrációt vagy egy  regisztrációs csoportot, és a peremhálózati jelölővel megadhatja, hogy egy eszköz vagy eszközcsoport egy IoT Edge eszköz. [](/cli/azure/iot/dps/enrollment-group)

   1. Fogadja el a Device Provisioning Service kiosztási szabályzatának alapértelmezett értékét arra az értékre, hogy hogyan szeretné hozzárendelni az eszközöket a **központokhoz,** vagy válasszon egy másik értéket, amely erre a regisztrációra vonatkozik.

   1. Válassza ki **IoT Hub** egymáshoz csatolt adatokat, amelyekhez csatlakoztatni szeretné az eszközt. Több központot is kiválaszthat, és az eszköz a kiválasztott kiosztási szabályzatnak megfelelően hozzá lesz rendelve ezek egyikéhez.

   1. Válassza **ki, hogyan** szeretné kezelni az eszközadatokat az újraépítéskor, amikor az eszközök először kérik a kiépítést.

   1. Ha szeretné, adjon hozzá egy címkeértéket az **Ikereszköz** kezdeti állapotához. Címkék használatával eszközcsoportokat célozhat meg a modulok üzembe helyezéséhez. Például:

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

   1. Győződjön **meg arról, hogy a** Bejegyzés engedélyezése beállítás az Enable **(Engedélyezés) beállításra van beállítva.**

   1. Kattintson a **Mentés** gombra.

Most, hogy már létezik regisztráció ehhez az eszközhöz, a IoT Edge automatikusan kiépítheti az eszközt a telepítés során. Másolja ki a regisztráció elsődleges  kulcsának az IoT Edge-futtatás telepítésekor használt értékét, vagy ha csoportos regisztrációhoz szükséges eszközkulcsokat fog létrehozni.

## <a name="derive-a-device-key"></a>Eszközkulcs származtatása

> [!NOTE]
> Erre a szakaszra csak csoportos regisztráció használata esetén van szükség.

Minden eszköz a saját egyedi regisztrációs azonosítójával használja a származtatott eszközkulcsot, hogy szimmetrikus kulcsigazolást végezzen a regisztrációval a kiépítés során. Az eszközkulcs létrehozásához használja a DPS-regisztrációból másolt kulcsot az eszköz egyedi regisztrációs [azonosítójának HMAC-SHA256](https://wikipedia.org/wiki/HMAC) kiszámításához, és alakítsa át az eredményt Base64 formátumba.

Ne foglalja bele a regisztráció elsődleges vagy másodlagos kulcsát az eszközkódba.

### <a name="linux-workstations"></a>Linux-munkaállomások

Linux-munkaállomás használata esetén az openssl használatával létrehozhatja a származtatott eszközkulcsot az alábbi példában látható módon.

Cserélje le a **KEY** értékét a korábban **feljegyzett** elsődleges kulcsra.

Cserélje le a **REG_ID** értékét az eszköz regisztrációs azonosítójára.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

### <a name="windows-based-workstations"></a>Windows-alapú munkaállomások

Ha Windows-alapú munkaállomást használ, a PowerShell használatával létrehozhatja a származtatott eszközkulcsot az alábbi példában látható módon.

Cserélje le a **KEY** értékét a korábban **feljegyzett** elsődleges kulcsra.

Cserélje le a **REG_ID** értékét az eszköz regisztrációs azonosítójára.

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

## <a name="install-the-iot-edge-runtime"></a>Az IoT Edge-futtatókörnyezet telepítése

Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön üzembe van helyezve. Az összetevői tárolókban futnak, és lehetővé teszik további tárolók üzembe helyezését az eszközön, hogy kódot futtathat a peremhálózaton.

Kövesse az [Install the Azure IoT Edge runtime](how-to-install-iot-edge.md)(Az eszköz telepítése) Azure IoT Edge, majd térjen vissza ehhez a cikkhez az eszköz üzembe lépéséhez.

## <a name="configure-the-device-with-provisioning-information"></a>Az eszköz konfigurálása kiépítési információkkal

Miután telepítette a futásidőt az eszközön, konfigurálja az eszközt a Device Provisioning Service-hez való csatlakozáshoz használt információkkal, és IoT Hub.

Készítse elő a következő információkat:

* A **DPS-azonosító hatóköre** érték
* A létrehozott **eszközregisztrációs** azonosító
* A  DPS-regisztrációból másolt elsődleges kulcs

> [!TIP]
> Csoportos regisztrációkhoz az egyes eszközök [](#derive-a-device-key) származtatott kulcsára van szükség a DPS regisztrációs elsődleges kulcsa helyett.

### <a name="linux-device"></a>Linux-eszköz

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
1. Nyissa meg a konfigurációs fájlt a IoT Edge eszközön.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Keresse meg a fájl kiépítési konfigurációk szakaszát. Tegye megjegyzésbe a DPS szimmetrikus kulcsos kiépítés sorait, és ügyeljen arra, hogy a többi kiépítési sor megjegyzésként legyen kivédve.

   A sornak nem szabad megelőző szóközt tartalmazni, a beágyazott elemeket pedig két `provisioning:` szóközt kell behúzottként behúzottként.

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "symmetric_key"
       registration_id: "<REGISTRATION_ID>"
       symmetric_key: "<SYMMETRIC_KEY>"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. Frissítse a `scope_id` , `registration_id` és `symmetric_key` értékeit a DPS és az eszköz adataival.

1. Ha szükséges, a vagy a sorokkal konfigurálhatja az eszköz `always_reprovision_on_startup` `dynamic_reprovisioning` újrabontási viselkedését. Ha egy indításkor az eszköz újraépítésre van beállítva, akkor először mindig a DPS-sel próbál meg kiépítést, majd ha ez sikertelen, vissza fog állítani a kiépítési biztonsági mentésre. Ha egy eszköz úgy van beállítva, hogy dinamikusan újraépíti magát, a IoT Edge újraindul, majd újból kiépíti, ha a rendszer újra kiépíti az eseményt. További információkért [lásd: IoT Hub eszköz-újraosztás fogalmai.](../iot-dps/concepts-device-reprovision.md)

1. Indítsa újra IoT Edge futásidejű rendszert, hogy az az eszközön végrehajtott összes konfigurációs módosítást átvegye.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1 -->

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

1. Keresse meg **a fájl Kiépítés** szakaszát. A DPS-kiépítés sorait tegye megjegyzésbe szimmetrikus kulccsal, és ügyeljen arra, hogy a többi kiépítési sor megjegyzésbe legyen véve.

   ```toml
   # DPS provisioning with symmetric key
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "symmetric_key"
   registration_id = "<REGISTRATION_ID>"

   symmetric_key = "<PRIMARY_KEY OR DERIVED_KEY>"
   ```

1. Frissítse a `id_scope` , `registration_id` és `symmetric_key` értékeit a DPS és az eszköz adataival.

   A szimmetrikus kulcs paramétere elfogadhatja egy beágyazott kulcs, egy fájl URI vagy egy PKCS#11 URI értékét. A használt formátumtól függően csak egy szimmetrikus kulcssort ne.

   Ha PKCS#11 URI-t használ, keresse meg a konfigurációs fájl **PKCS#11** szakaszát, és adja meg a PKCS#11 konfigurációval kapcsolatos információkat.

1. Mentse és zárja be a config.toml fájlt.

1. Alkalmazza a konfigurációs módosításokat, amelyek a IoT Edge.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

### <a name="windows-device"></a>Windows-eszköz

1. Nyisson meg egy PowerShell-ablakot rendszergazdai módban. A PowerShell telepítésekor mindenképpen AMD64-munkamenetet használjon a PowerShell IoT Edge (x86) telepítésekor.

1. Az **Initialize-IoTEdge** parancs konfigurálja a IoT Edge futtatáskor a gépen. A parancs alapértelmezés szerint manuális kiépítést használ Windows-tárolókkal, ezért használja a jelzőt az automatikus kiépítés szimmetrikus kulcsos `-DpsSymmetricKey` hitelesítéssel való használatára.

   Cserélje le a , és helyőrzőértékeket a korábban összegyűjtött `{scope_id}` `{registration_id}` `{symmetric_key}` adatokra.

   Ha `-ContainerOs Linux` Windows rendszeren használ Linux-tárolókat, adja hozzá a paramétert.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsSymmetricKey -ScopeId {scope ID} -RegistrationId {registration ID} -SymmetricKey {symmetric key}
   ```

## <a name="verify-successful-installation"></a>A sikeres telepítés ellenőrzése

Ha a futásidejű modul sikeresen elindult, a saját IoT Hub üzembe helyezheti IoT Edge modulokat az eszközön. Az alábbi parancsokkal ellenőrizze az eszközön, hogy a futásidő telepítése és sikeresen elindult-e.

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

Ellenőrizheti, hogy a Device Provisioning Service-ban létrehozott egyéni regisztrációt használták-e. Lépjen a Device Provisioning Service-példányra a Azure Portal. Nyissa meg a létrehozott egyéni regisztráció regisztrációs adatait. Figyelje meg, hogy a regisztráció állapota hozzá van **rendelve,** és az eszközazonosító megjelenik a listában.

## <a name="next-steps"></a>Következő lépések

A Device Provisioning Service regisztrációs folyamata lehetővé teszi az eszközazonosító és az ikereszköz-címkék beállítását az új eszköz kiépítésekor. Ezeket az értékeket használhatja egyéni eszközök vagy eszközcsoportok automatikus eszközkezelés használatával való megcélzott kezelésére. Ismerje meg, hogyan helyezhet üzembe és monitor IoT Edge [modulokat](how-to-deploy-at-scale.md) nagy méretekben a Azure Portal vagy az [Azure CLI használatával.](how-to-deploy-cli-at-scale.md)

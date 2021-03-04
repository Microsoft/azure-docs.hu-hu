---
title: Eszköz kiépítése szimmetrikus kulcsú tanúsítvány használatával – Azure IoT Edge
description: A szimmetrikus kulcsos tanúsítványok használata a Azure IoT Edge automatikus eszköz-kiépítés teszteléséhez az eszköz kiépítési szolgáltatásával
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 73d1d873df58c672e9db6b9e4e17ed58e1a6397e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046193"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>IoT Edge-eszköz létrehozása és kiépítése a szimmetrikus kulcs igazolásával

Azure IoT Edge eszközök automatikusan kiállíthatók az [eszköz kiépítési szolgáltatásával](../iot-dps/index.yml) , ugyanúgy, mint az Edge-t nem használó eszközök. Ha nem ismeri az automatikus kiépítés folyamatát, a folytatás előtt tekintse át a [kiépítés](../iot-dps/about-iot-dps.md#provisioning-process) áttekintését.

Ebből a cikkből megtudhatja, hogyan hozhat létre egyéni vagy csoportos tanúsítványigénylési szolgáltatást egy IoT Edge eszközön a szimmetrikus kulcs igazolásával, a következő lépésekkel:

* Hozzon létre egy IoT Hub Device Provisioning Service (DPS) egy példányát.
* Hozzon létre egy regisztrációt az eszközhöz.
* Telepítse a IoT Edge futtatókörnyezetet, és kapcsolódjon a IoT Hubhoz.

A szimmetrikus kulcs igazolása egyszerű módszer egy eszköz kiépítési szolgáltatási példánnyal való hitelesítésére. Ez az igazolási módszer a "Hello World" felhasználói élményt jelöli olyan fejlesztők számára, akik még nem ismerik az eszközök üzembe helyezését, vagy nincsenek szigorú biztonsági követelmények. A [TPM](../iot-dps/concepts-tpm-attestation.md) vagy [X. 509 tanúsítványokat](../iot-dps/concepts-x509-attestation.md) használó eszközök igazolása biztonságosabb, és szigorúbb biztonsági követelményekhez kell használni őket.

## <a name="prerequisites"></a>Előfeltételek

* Aktív IoT Hub
* Fizikai vagy virtuális eszköz

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>A IoT Hub Device Provisioning Service beállítása

Hozzon létre egy új példányt a IoT Hub Device Provisioning Service az Azure-ban, és kapcsolja össze az IoT hubhoz. A [IOT hub DPS beállítása](../iot-dps/quick-setup-auto-provision.md)című témakör útmutatását követve végezheti el.

Miután futtatta az eszköz kiépítési szolgáltatását, másolja az **azonosító hatókör** értékét az Áttekintés lapról. Ezt az értéket használja a IoT Edge futtatókörnyezet konfigurálásakor.

## <a name="choose-a-unique-registration-id-for-the-device"></a>Válasszon egyedi regisztrációs azonosítót az eszközhöz

Az egyes eszközök azonosításához egyedi regisztrációs azonosítót kell megadni. Az eszköz MAC-címe, sorozatszáma vagy bármely egyedi adata használható.

Ebben a példában egy MAC-címe és sorozatszáma kombinációját használjuk, amely a következő karakterláncot képezi a regisztrációs AZONOSÍTÓhoz: `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6` .

Hozzon létre egy egyedi regisztrációs azonosítót az eszközhöz. Az érvényes karakterek a kisbetűs alfanumerikus és kötőjel ("-").

## <a name="create-a-dps-enrollment"></a>DPS-regisztráció létrehozása

Az eszköz regisztrációs AZONOSÍTÓjának használatával egyéni regisztrációt hozhat létre a DPS-ben.

Amikor létrehoz egy regisztrációt a DPS-ben, lehetősége van bejelenteni a **kezdeti eszköz kettős állapotát**. Az eszköz Twin-ben címkéket állíthat be az eszközök csoportosításához a megoldásban szükséges mérőszámok, például a régió, a környezet, a hely vagy az eszköz típusa alapján. Ezek a címkék [automatikus központi telepítések](how-to-deploy-at-scale.md)létrehozásához használhatók.

> [!TIP]
> A csoportos regisztrációk akkor is lehetségesek, ha szimmetrikus kulcsú igazolást használ, és ugyanazokat a döntéseket vonja be, mint az egyéni regisztrációk.

1. A [Azure Portal](https://portal.azure.com)navigáljon a IoT hub Device Provisioning Service-példányához.

1. A **Beállítások** területen válassza a **regisztrációk kezelése** lehetőséget.

1. Válassza az **Egyéni regisztráció hozzáadása** lehetőséget, majd végezze el a következő lépéseket a beléptetés konfigurálásához:  

   1. A **mechanizmus** mezőben válassza a **szimmetrikus kulcs** lehetőséget.

   1. Jelölje be a **kulcsok automatikus generálása** jelölőnégyzetet.

   1. Adja meg az eszközhöz létrehozott **regisztrációs azonosítót** .

   1. Ha szeretné, adja meg **IoT hub eszköz azonosítóját** az eszközhöz. Az eszközök azonosítói segítségével megcélozhat egy különálló eszközt a modulok telepítéséhez. Ha nem adja meg az eszköz AZONOSÍTÓját, a rendszer a regisztrációs azonosítót használja.

   1. A **true (igaz** ) érték kiválasztásával deklarálhatja, hogy a beléptetés egy IoT Edge eszközre vonatkozik. Csoportos regisztráció esetén minden eszköznek IoT Edge eszköznek kell lennie, vagy egyikük sem lehet.

      > [!TIP]
      > Az Azure CLI-ben létrehozhat egy [beléptetést](/cli/azure/ext/azure-iot/iot/dps/enrollment) vagy egy [beléptetési csoportot](/cli/azure/ext/azure-iot/iot/dps/enrollment-group) , és az **Edge-kompatibilis** jelző használatával meghatározhatja, hogy egy eszköz vagy egy csoport IoT Edge eszköz-e.

   1. Fogadja el az alapértelmezett értéket az eszköz kiépítési szolgáltatásának kiosztási házirendjében, hogy **hogyan kívánja hozzárendelni az eszközöket az elosztóhoz** , vagy válasszon egy másik, a regisztrációra vonatkozó értéket.

   1. Válassza ki azt a csatolt **IoT hub** , amelyhez csatlakoztatni szeretné az eszközt. Több hub is kiválasztható, és az eszköz a kiválasztott foglalási szabályzatnak megfelelően lesz hozzárendelve az egyikhez.

   1. Válassza ki **, hogyan történjen az eszközbeállítások kezelése** az eszközök első kiépítés utáni ismételt kiépítés esetén.

   1. Ha szeretné, adjon hozzá egy címke értéket a **kezdeti eszköz iker állapotához** . A címkék használatával megcélozhatja az eszközök csoportjait a modulok üzembe helyezéséhez. Például:

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

   1. Győződjön meg arról, hogy a **bejegyzés** engedélyezése beállítás **engedélyezve** van.

   1. Kattintson a **Mentés** gombra.

Most, hogy az eszközhöz regisztrálva van, a IoT Edge futtatókörnyezet automatikusan kiépítheti az eszközt a telepítés során. Ügyeljen arra, hogy a beléptetés **elsődleges kulcsának** értékét a IoT Edge futtatókörnyezet telepítésekor használja, vagy ha az eszköz kulcsait a csoportos regisztrációval való használatra kívánja létrehozni.

## <a name="derive-a-device-key"></a>Eszköz kulcsának származtatása

> [!NOTE]
> Ez a szakasz csak akkor szükséges, ha csoportos regisztrációt használ.

Minden eszköz a származtatott eszköz kulcsát használja az egyedi regisztrációs AZONOSÍTÓval a szimmetrikus kulcs igazolásának végrehajtásához a regisztráció során. Az eszköz kulcsának létrehozásához használja a DPS-regisztrációból másolt kulcsot, hogy kiszámítsa az eszköz egyedi regisztrációs AZONOSÍTÓjának [HMAC-sha256](https://wikipedia.org/wiki/HMAC) , és az eredményt Base64 formátumra konvertálja.

Ne foglalja bele a regisztráció elsődleges vagy másodlagos kulcsát az eszköz kódjába.

### <a name="linux-workstations"></a>Linux-munkaállomások

Ha Linux-munkaállomást használ, az OpenSSL használatával hozhatja elő a származtatott eszköz kulcsát az alábbi példában látható módon.

Cserélje le a **kulcs** értékét a korábban feljegyzett **elsődleges kulcsra** .

Cserélje le **REG_ID** értékét az eszköz regisztrációs azonosítójával.

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

Ha Windows-alapú munkaállomást használ, a PowerShell használatával hozhatja elő a származtatott eszköz kulcsát az alábbi példában látható módon.

Cserélje le a **kulcs** értékét a korábban feljegyzett **elsődleges kulcsra** .

Cserélje le **REG_ID** értékét az eszköz regisztrációs azonosítójával.

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

Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön üzembe van helyezve. Az összetevői tárolókban futnak, és lehetővé teszik további tárolók üzembe helyezését az eszközön, így a kód a peremhálózat szélén is futtatható.

Az eszköz kiépítéséhez kövesse az [Azure IoT Edge futtatókörnyezet telepítése](how-to-install-iot-edge.md)című témakör lépéseit, majd térjen vissza ehhez a cikkhez.

## <a name="configure-the-device-with-provisioning-information"></a>Az eszköz konfigurálása az üzembe helyezési adatokkal

Miután telepítette a futtatókörnyezetet az eszközre, konfigurálja az eszközt az általa használt információval az eszköz kiépítési szolgáltatásához való kapcsolódáshoz és a IoT Hubához.

A következő információk állnak készen:

* A DPS- **azonosító hatókörének** értéke
* A létrehozott eszköz **regisztrációs azonosítója**
* A DPS-regisztrációból másolt **elsődleges kulcs**

> [!TIP]
> Csoportos regisztráció esetén a DPS-regisztráció elsődleges kulcsa helyett az egyes eszközök [származtatott kulcsára](#derive-a-device-key) van szükség.

### <a name="linux-device"></a>Linuxos eszköz

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
1. Nyissa meg a konfigurációs fájlt a IoT Edge eszközön.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. A fájl kiépítési konfigurációk szakaszának megkeresése. Jegyezze fel a DPS szimmetrikus kulcsú kiépítés sorait, és győződjön meg arról, hogy az egyéb kiépítési sorok megjegyzése megtörténik.

   A `provisioning:` sornak nem lehetnek korábbi szóközök, és a beágyazott elemeket két szóközzel kell behúzni.

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

1. A, a és a értékének frissítése a `scope_id` `registration_id` `symmetric_key` DPS és az eszköz adataival.

1. Igény szerint a vagy a `always_reprovision_on_startup` `dynamic_reprovisioning` vonalak használatával konfigurálhatja az eszköz újraépítésének viselkedését. Ha egy eszköz úgy van beállítva, hogy a rendszer újraépítse az indítást, a rendszer mindig először a DPS-t próbálja kiépíteni, majd visszatér a kiépítési biztonsági mentéshez, ha az nem sikerül. Ha egy eszköz úgy van beállítva, hogy dinamikusan újra kiépítse magát, IoT Edge újraindítja és újraépíti, ha a rendszer újraépítési eseményt észlel. További információ: [IoT hub eszköz újraépítési fogalmai](../iot-dps/concepts-device-reprovision.md).

1. Indítsa újra a IoT Edge futtatókörnyezetet, hogy az az eszközön végrehajtott összes konfigurációs módosítást felveszi.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1 -->

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

1. A fájl **kiépítési** szakaszának megkeresése. A rendszer visszafejti a DPS-kiépítés sorait a szimmetrikus kulccsal, és gondoskodjon arról, hogy minden más kiépítési sor megjegyzésbe kerüljön.

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

1. A, a és a értékének frissítése a `id_scope` `registration_id` `symmetric_key` DPS és az eszköz adataival.

   A szimmetrikus kulcs paraméter a beágyazott kulcs, a fájl URI-ja vagy a PKCS # 11 URI értékét is elfogadhatja. A használt formátum alapján csak egy szimmetrikus kulcspárt kell visszaírnia.

   Ha PKCS # 11 URI-t használ, keresse meg a **PKCS # 11** szakaszt a konfigurációs fájlban, és adja meg a PKCS # 11 konfigurációjának adatait.

1. Mentse és zárjuk be a config. toml fájlt.

1. Alkalmazza a IoT Edge végrehajtott konfigurációs módosításokat.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

### <a name="windows-device"></a>Windows-eszköz

1. Nyisson meg egy PowerShell-ablakot rendszergazdai módban. Ügyeljen arra, hogy az IoT Edge telepítésekor a PowerShell AMD64-munkamenetét használja, nem a PowerShell (x86) rendszerre.

1. Az **inicializálás-IoTEdge** parancs konfigurálja a IoT Edge futtatókörnyezetet a gépen. A parancs alapértelmezett értéke a Windows-tárolók manuális kiépítés, ezért a `-DpsSymmetricKey` jelzővel használhatja a szimmetrikus kulcsos hitelesítéssel történő automatikus kiépítés használatát.

   Cserélje le a, és a helyőrző értékét `{scope_id}` `{registration_id}` `{symmetric_key}` a korábban összegyűjtött adatokra.

   `-ContainerOs Linux`Ha Linux-tárolókat használ Windows rendszeren, adja hozzá a paramétert.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsSymmetricKey -ScopeId {scope ID} -RegistrationId {registration ID} -SymmetricKey {symmetric key}
   ```

## <a name="verify-successful-installation"></a>Sikeres telepítés ellenőrzése

Ha a futtatókörnyezet sikeresen elindult, beléphet a IoT Hubba, és megkezdheti a IoT Edge modulok üzembe helyezését az eszközön. Az eszközön az alábbi parancsokkal ellenőrizheti, hogy a futtatókörnyezet telepítése és elindítása sikeresen megtörtént-e.

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

Ellenőrizheti, hogy a rendszer használta-e a Device kiépítési szolgáltatásban létrehozott egyéni regisztrációt. Navigáljon az eszköz kiépítési szolgáltatási példányához a Azure Portal. Nyissa meg a regisztráció részleteit a létrehozott egyéni regisztrációhoz. Figyelje meg, hogy a regisztráció állapota **hozzá van rendelve** , és az eszköz azonosítója megjelenik.

## <a name="next-steps"></a>Következő lépések

Az eszköz kiépítési szolgáltatásának beléptetési folyamata lehetővé teszi, hogy az eszköz AZONOSÍTÓját és az eszköz Twin címkéit az új eszköz kiépítésekor egy időben állítsa be. Ezeket az értékeket használhatja az egyes eszközök vagy eszközök automatikus eszközkezelés használatával történő megcélzásához. Megtudhatja, hogyan [helyezheti üzembe és figyelheti IoT Edge-modulok méretezését a Azure Portal vagy az](how-to-deploy-at-scale.md) [Azure CLI használatával](how-to-deploy-cli-at-scale.md).

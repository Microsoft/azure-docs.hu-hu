---
title: Új eszköz regisztrálása – Azure IoT Edge | Microsoft Docs
description: Egyetlen IoT Edge eszköz regisztrálása IoT Hub a szimmetrikus kulcsokkal vagy X. 509 tanúsítványokkal történő manuális kiépítés esetén
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: d75f184a324a9d418b0af2e3cf5790205af0fa42
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200721"
---
# <a name="register-an-iot-edge-device-in-iot-hub"></a>IoT Edge eszköz regisztrálása IoT Hub

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Ez a cikk az új IoT Edge eszköz IoT Hub-ben való regisztrálásának lépéseit ismerteti.

Minden IoT hub-hoz csatlakozó eszközön van egy eszköz azonosítója, amely a felhőből az eszközre vagy az eszközről a felhőbe irányuló kommunikáció nyomon követésére szolgál. Konfigurálnia kell egy eszközt a kapcsolati adataival, beleértve az IoT hub állomásnevét, az eszköz AZONOSÍTÓját és az eszköz által a IoT Hub való hitelesítéshez használt információkat.

A cikkben ismertetett lépések végigvezetik a manuális kiépítés folyamatán, ahol egyetlen eszközt csatlakoztathat az IoT hubhoz. A manuális kiépítés esetén két lehetősége van IoT Edge eszközök hitelesítésére:

* **Szimmetrikus kulcs**: amikor új eszköz-identitást hoz létre IoT Hubban, a szolgáltatás két kulcsot hoz létre. Elhelyezi a kulcsok egyikét az eszközön, és megjeleníti a hitelesítő IoT Hub kulcsát.

  Ez a hitelesítési módszer gyorsabb a kezdéshez, de nem biztonságos.

* **X. 509 önaláírt**: két x. 509 identitás-tanúsítványt hoz létre, és elhelyezi őket az eszközön. Amikor új eszköz-identitást hoz létre IoT Hubban, mindkét tanúsítványból ujjlenyomatai megfelelnek biztosít. Amikor az eszköz hitelesíti IoT Hub, egy tanúsítványt mutat be, és IoT Hub ellenőrzi, hogy a tanúsítvány megegyezik-e az ujjlenyomatával.

  Ez a hitelesítési módszer biztonságosabb, és éles környezetekben ajánlott.

Ez a cikk mindkét hitelesítési módszert magában foglalja.

Ha sok eszközt kell beállítania, és nem kívánja manuálisan kiépíteni, az alábbi cikkek egyikével megismerheti, hogyan működik a IoT Edge a IoT Hub Device Provisioning Service:

* [IoT Edge eszközök létrehozása és kiépítése X. 509 tanúsítványok használatával](how-to-auto-provision-x509-certs.md)
* [IoT Edge eszközök létrehozása és kiépítése TPM-sel](how-to-auto-provision-simulated-device-linux.md)
* [IoT Edge eszközök létrehozása és kiépítése szimmetrikus kulcsokkal](how-to-auto-provision-symmetric-keys.md)

## <a name="prerequisites"></a>Előfeltételek

# <a name="portal"></a>[Portál](#tab/azure-portal)

Az Azure-előfizetésében ingyenes vagy standard szintű [IoT hub](../iot-hub/iot-hub-create-through-portal.md) .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Az Azure-előfizetéshez tartozó ingyenes vagy standard szintű [IoT hub](../iot-hub/iot-hub-create-through-portal.md)
* [Visual Studio Code](https://code.visualstudio.com/)
* A Visual Studio Code-hoz készült [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* Az Azure-előfizetésében ingyenes vagy standard szintű [IoT hub](../iot-hub/iot-hub-create-using-cli.md) .
* [Azure CLI](/cli/azure/install-azure-cli) a környezetben. Legalább az Azure CLI-verziójának 2.0.70 vagy újabbnak kell lennie. A verziószámot az `az --version` paranccsal ellenőrizheti. Ez a verzió támogatja az „az” bővítményparancsokat, és ebben a verzióban került bevezetésre a Knack parancskeretrendszer.

---

## <a name="option-1-register-with-symmetric-keys"></a>1. lehetőség: regisztrálás szimmetrikus kulcsokkal

Számos eszköz használatával regisztrálhat egy új IoT Edge eszközt a IoT Hubban, és lekérheti a kapcsolati karakterláncot a beállítástól függően.

# <a name="portal"></a>[Portál](#tab/azure-portal)

A Azure Portal IoT hub-ban IoT Edge eszközöket a rendszer külön hozza létre és felügyeli az olyan IoT-eszközökről, amelyek nem engedélyezettek az Edge-ben.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon az IoT hubhoz.

1. A bal oldali ablaktáblán válassza a menü **IoT Edge** elemét, majd válassza a **IoT Edge eszköz hozzáadása** lehetőséget.

   ![IoT Edge-eszköz hozzáadása a Azure Portal](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. Az **eszköz létrehozása** lapon adja meg a következő információkat:

   * Hozzon létre egy leíró eszköz AZONOSÍTÓját.
   * A hitelesítési típusként válassza a **szimmetrikus kulcs** lehetőséget.
   * Használja az alapértelmezett beállításokat a hitelesítési kulcsok automatikus létrehozásához és az új eszköz csatlakoztatásához a központhoz.

1. Kattintson a **Mentés** gombra.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="sign-in-to-access-your-iot-hub"></a>Jelentkezzen be az IoT hub eléréséhez

A Visual Studio Code-hoz készült Azure IoT Extensions használatával műveleteket hajthat végre az IoT hub segítségével. Ezeknek a műveleteknek a működéséhez be kell jelentkeznie az Azure-fiókjába, és ki kell választania a hubot.

1. A Visual Studio Code-ban nyissa meg a **Explorer** nézetet.
1. Az Explorer alján bontsa ki az **Azure IoT hub** szakaszt.

   ![Az Azure IoT Hub-eszközök szakasz kibontása](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Kattintson a **...** elemre az **Azure IoT hub** szakasz fejlécében. Ha nem látja a három pontot, kattintson vagy vigye a kurzort a fejléc fölé.
1. Válassza a **IoT hub kiválasztása** lehetőséget.
1. Ha nem jelentkezett be az Azure-fiókjába, kövesse az utasításokat.
1. Válassza ki Azure-előfizetését.
1. Válassza ki az IoT hubot.

### <a name="register-a-new-device-with-visual-studio-code"></a>Új eszköz regisztrálása a Visual Studio Code-ban

1. A Visual Studio Code Explorerben bontsa ki az **Azure IoT hub** szakaszt.
1. Kattintson a **...** elemre az **Azure IoT hub** szakasz fejlécében. Ha nem látja a három pontot, kattintson vagy vigye a kurzort a fejléc fölé.
1. Válassza a **létrehozás IoT Edge eszköz** lehetőséget.
1. A megnyíló szövegmezőben adja meg az eszköz AZONOSÍTÓját.

A kimenet képernyőn láthatja a parancs eredményét. A rendszer kinyomtatja az eszköz adatait, beleértve a megadott **deviceId** -t, valamint azt a **ConnectionString** -t, amellyel a fizikai eszközt csatlakoztathatja az IoT hubhoz.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az az [IOT hub Device-Identity Create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) paranccsal hozzon létre egy új eszköz-identitást az IOT hub-ban. Például:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Ez a parancs három paramétert tartalmaz:

* `--device-id` vagy `-d` : adjon meg egy leíró nevet, amely egyedi az IoT hub-n belül.
* `--hub-name` vagy `-n` : adja meg az IoT hub nevét.
* `--edge-enabled` vagy `--ee` : állapítsa meg, hogy az eszköz egy IoT Edge eszköz.

   ![az IOT hub Device-Identity Create output](./media/how-to-register-device/create-edge-device-cli.png)

---

Most, hogy van egy eszköz regisztrálva IoT Hubban, kérje le a kapcsolódási karakterláncot, amelyet a IoT Edge futtatókörnyezet telepítésének és üzembe helyezésének befejezéséhez használ. A cikk későbbi részében ismertetett lépéseket követve [megtekintheti a regisztrált eszközöket, és lekérheti a kapcsolatok karakterláncait](#view-registered-devices-and-retrieve-connection-strings).

## <a name="option-2-register-with-x509-certificates"></a>2. lehetőség: regisztrálás X. 509 tanúsítvánnyal

Az X. 509 tanúsítványokkal történő manuális üzembe helyezéshez IoT Edge 1.0.10 vagy újabb verzió szükséges.

X. 509 tanúsítványalapú hitelesítés esetén az eszközök hitelesítési adatai az *ujjlenyomatai megfelelnek* származó adatok formájában vannak megadva. Ezek a ujjlenyomatai megfelelnek az eszköz regisztrációjának időpontjában IoT Hub kapnak, hogy a szolgáltatás felismerje az eszközt a csatlakozáskor.

### <a name="create-certificates-and-thumbprints"></a>Tanúsítványok és ujjlenyomatai megfelelnek létrehozása

Ha X. 509 tanúsítvánnyal rendelkező IoT Edge eszközt helyez üzembe, az *eszköz identitásának nevezett tanúsítványát* kell használnia. Ez a tanúsítvány csak IoT Edge-eszköz kiépítési és az Azure-IoT Hub az eszköz hitelesítésére használatos. Ez egy levélbeli tanúsítvány, amely nem ír alá más tanúsítványokat. Az eszköz-identitás tanúsítványa elkülöníti a hitelesítésszolgáltató (CA) tanúsítványait, amelyeket az IoT Edge eszköz a modulok vagy az alsóbb rétegbeli eszközök számára biztosít ellenőrzés céljából. További információ a HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok IoT Edge eszközökön való használatáról: a [Azure IoT Edge tanúsítványok használatának ismertetése](iot-edge-certs.md).

Az X. 509 manuális kiépítés esetén a következő fájlokat kell megadnia:

* Két, a. cer vagy a. PEM formátumú hitelesítő titkos kulccsal rendelkező tanúsítvány.

  A IoT Edge futtatókörnyezet egy tanúsítvány-vagy kulcsfájl-készletet biztosít. Ha eszköz-identitási tanúsítványokat hoz létre, állítsa be a tanúsítvány köznapi nevét (CN) annak az eszköz-AZONOSÍTÓnak a nevével, amelyhez az eszköznek az IoT hub-ban szüksége van.

* Ujjlenyomatai megfelelnek mindkét eszköz identitás-tanúsítványa alapján.

  Az ujjlenyomatok értéke 40 – hexadecimális karakter az SHA-1 kivonatokhoz, vagy 64 – hexadecimális karakter az SHA-256 kivonatokhoz. Mindkét ujjlenyomatai megfelelnek az eszköz regisztrálásakor IoT Hub biztosítanak.

Ha nem érhető el tanúsítvány, [létrehozhat bemutató tanúsítványokat IoT Edge eszköz funkcióinak teszteléséhez](how-to-create-test-certificates.md). A tanúsítvány-létrehozási parancsfájlok beállításához kövesse az ebben a cikkben található utasításokat, hozzon létre egy legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt, majd hozzon létre két IoT Edge eszköz identitás-tanúsítványát.

A tanúsítvány ujjlenyomatának lekérésének egyik módja a következő OpenSSL-parancs:

```cmd
openssl x509 -in <certificate filename>.pem -text -fingerprint
```

### <a name="register-a-new-device"></a>Új eszköz regisztrálása

Számos eszköz használatával regisztrálhat egy új IoT Edge eszközt a IoT Hub, és feltöltheti a tanúsítvány-ujjlenyomatai megfelelnek.

# <a name="portal"></a>[Portál](#tab/azure-portal)

A Azure Portal IoT hub-ban IoT Edge eszközöket a rendszer külön hozza létre és felügyeli az olyan IoT-eszközökről, amelyek nem engedélyezettek az Edge-ben.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon az IoT hubhoz.

1. A bal oldali ablaktáblán válassza a menü **IoT Edge** elemét, majd válassza a **IoT Edge eszköz hozzáadása** lehetőséget.

   ![IoT Edge-eszköz hozzáadása a Azure Portal](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. Az **eszköz létrehozása** lapon adja meg a következő információkat:

   * Hozzon létre egy leíró eszköz AZONOSÍTÓját. Jegyezze fel az eszköz AZONOSÍTÓját, ahogy azt a következő szakaszban fogja használni.
   * Válassza az **X. 509 önaláírt** hitelesítési típusként lehetőséget.
   * Adja meg az elsődleges és a másodlagos identitás tanúsítványának ujjlenyomatai megfelelnek. Az ujjlenyomatok értéke 40 – hexadecimális karakter az SHA-1 kivonatokhoz, vagy 64 – hexadecimális karakter az SHA-256 kivonatokhoz.

1. Kattintson a **Mentés** gombra.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

A Visual Studio Code-hoz készült Azure IoT-bővítmény jelenleg nem támogatja az X. 509 tanúsítvánnyal rendelkező eszközök regisztrálását.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az az [IOT hub Device-Identity Create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) paranccsal hozzon létre egy új eszköz-identitást az IOT hub-ban. Például:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA thumbprint] --secondary-thumbprint [SHA thumbprint]
   ```

Ez a parancs több paramétert is tartalmaz:

* `--device-id` vagy `-d` : adjon meg egy leíró nevet, amely egyedi az IoT hub számára. Jegyezze fel az eszköz AZONOSÍTÓját, ahogy azt a következő szakaszban fogja használni.
* `hub-name` vagy `-n` : adja meg az IoT hub nevét.
* `--edge-enabled` vagy `--ee` : állapítsa meg, hogy az eszköz egy IoT Edge eszköz.
* `--auth-method` vagy `--am` : deklarálja azt az engedélyezési típust, amelyet az eszköz használni fog. Ebben az esetben a ujjlenyomatai megfelelnek X. 509 tanúsítványt használunk.
* `--primary-thumbprint` vagy `--ptp` : adjon meg egy X. 509 tanúsítvány ujjlenyomatát, amelyet elsődleges kulcsként kíván használni.
* `--secondary-thumbprint` vagy `--stp` : adjon meg egy X. 509 tanúsítvány-ujjlenyomatot, amelyet másodlagos kulcsként kíván használni.

---

Most, hogy regisztrált egy eszközt a IoT Hubban, készen áll a IoT Edge futtatókörnyezet telepítésére és üzembe helyezésére az eszközön. Az X. 509 tanúsítvánnyal hitelesítő eszközökön nem használhatók a kapcsolódási karakterláncok, így továbbra is folytathatja a következő lépést: IoT Edge

* [Linuxos Azure IoT Edge telepítése vagy eltávolítása](how-to-install-iot-edge.md)
* [Windows Azure IoT Edge telepítése vagy eltávolítása](how-to-install-iot-edge-windows-on-windows.md)

## <a name="view-registered-devices-and-retrieve-connection-strings"></a>Regisztrált eszközök megtekintése és a kapcsolatok karakterláncának beolvasása

A szimmetrikus kulcsos hitelesítést használó eszközökhöz szükség van a kapcsolódási karakterláncokra az IoT Edge futtatókörnyezet telepítésének és üzembe helyezésének befejezéséhez.

Az X. 509 tanúsítványalapú hitelesítést használó eszközökhöz nem szükségesek a kapcsolatok karakterláncai. Ehelyett ezeknek az eszközöknek szükségük van az IoT hub nevére, az eszköz nevére és a tanúsítvány fájljaira az IoT Edge futtatókörnyezet telepítésének és kiépítésének befejezéséhez.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Az IoT hub-hoz csatlakozó összes Edge-kompatibilis eszköz megjelenik a **IoT Edge** oldalon.

![Az IoT hub összes IoT Edge eszközének megtekintése a Azure Portal használatával](./media/how-to-register-device/portal-view-devices.png)

Ha készen áll az eszköz beállítására, szüksége lesz arra a kapcsolati karakterláncra, amely a fizikai eszközt az IoT hub identitásával társítja.

A szimmetrikus kulcsokkal hitelesítő eszközökön a kapcsolódási sztringek elérhetők a portálon való másoláshoz.

1. A portál **IoT Edge** lapján kattintson az eszköz azonosítójára a IoT Edge eszközök listájáról.
2. Másolja az **elsődleges** vagy **másodlagos kapcsolatok karakterláncának** értékét.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>A Visual Studio Code-ban IoT Edge eszközök megtekintése

Az IoT hub-hoz csatlakozó összes eszköz megjelenik a Visual Studio Code Explorer **Azure IoT hub** szakaszában. IoT Edge eszközök a nem peremhálózati eszközöktől eltérő ikonnal vannak elkülönítve, és az a tény, hogy az **$edgeAgent** és **$edgeHub** modulokat minden IoT Edge eszközön telepítik.

![A VS Code használatával megtekintheti az IoT hub összes IoT Edge eszközét](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>A Visual Studio Code-beli kapcsolatok karakterláncának beolvasása

Ha készen áll az eszköz beállítására, szüksége lesz arra a kapcsolati karakterláncra, amely a fizikai eszközt az IoT hub identitásával társítja.

1. Kattintson a jobb gombbal az eszköz AZONOSÍTÓJÁRA az **Azure IoT hub** szakaszban.
1. Válassza az **eszköz csatlakoztatási karakterláncának másolása** lehetőséget.

   A rendszer átmásolja a kapcsolódási karakterláncot a vágólapra.

Azt is megteheti, hogy az eszköz **információinak beolvasása** lehetőségre kattint a jobb gombbal, és megjeleníti a kimeneti ablakban található összes eszközt, beleértve a kapcsolódási karakterláncot is.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>IoT Edge eszközök megtekintése az Azure CLI-vel

Az az [IOT hub Device-Identity List](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) paranccsal megtekintheti az IOT hub összes eszközét. Például:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

A IoT Edge eszközként regisztrált eszközökön a tulajdonság képességei lesznek. a **iotEdge** értéke **true (igaz**).

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>A kapcsolatok karakterláncának beolvasása az Azure CLI-vel

Ha készen áll az eszköz beállítására, szüksége lesz arra a kapcsolati karakterláncra, amely a fizikai eszközt az IoT hub identitásával társítja. Az az [IOT hub Device-Identity kapcsolat-string show](/cli/azure/ext/azure-iot/iot/hub/device-identity/connection-string#ext_azure_iot_az_iot_hub_device_identity_connection_string_show) paranccsal visszaállíthatja egy adott eszköz kapcsolati karakterláncát:

   ```azurecli
   az iot hub device-identity connection-string show --device-id [device id] --hub-name [hub name]
   ```

>[!TIP]
>A `connection-string show` parancs az Azure IoT bővítmény 0.9.8 verziójában lett bevezetve, az elavult `show-connection-string` parancs helyett. Ha a parancs futtatásakor hibaüzenet jelenik meg, győződjön meg arról, hogy a bővítmény verziója 0.9.8 vagy újabb verzióra frissült. További információ és a legújabb frissítések: [Microsoft Azure IoT-bővítmény az Azure CLI-hez](https://github.com/Azure/azure-iot-cli-extension).

A `device-id` paraméter értéke megkülönbözteti a kis-és nagybetűket.

A kapcsolódási karakterlánc eszközön való másolásakor ne adja meg az idézőjeleket a kapcsolódási karakterlánc körül.

---

## <a name="next-steps"></a>Következő lépések

Most, hogy regisztrált egy eszközt a IoT Hubban, készen áll a IoT Edge futtatókörnyezet telepítésére és üzembe helyezésére az eszközön.

* [Linuxos Azure IoT Edge telepítése vagy eltávolítása](how-to-install-iot-edge.md)
* [Windows Azure IoT Edge telepítése vagy eltávolítása](how-to-install-iot-edge-windows-on-windows.md)
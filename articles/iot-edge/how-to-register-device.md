---
title: Új eszköz regisztrálása – Azure IoT Edge | Microsoft Docs
description: Egyetlen IoT Edge eszköz regisztrálása IoT Hub szimmetrikus kulcsokkal vagy X.509-tanúsítványokkal való manuális üzembe építéshez
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: b5d761cfa947b3fd4e5f718e603219c650e8dd72
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481871"
---
# <a name="register-an-iot-edge-device-in-iot-hub"></a>Egy IoT Edge regisztrálása a IoT Hub

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Ez a cikk az új IoT Edge regisztrálásának lépéseit IoT Hub.

Az IoT Hubhoz csatlakozó összes eszköz rendelkezik egy eszközazonosítóval, amely a felhőből az eszközre vagy az eszközről a felhőbe való kommunikáció nyomon követésére használható. Konfigurálhatja az eszközt a kapcsolati adataival, beleértve az IoT Hub állomásnevét, az eszköz azonosítóját és az eszköz által a hitelesítéshez használt IoT Hub.

A cikkben található lépések végigmennek a manuális kiépítés folyamatán, amelyben egyetlen eszközt csatlakoztathat az IoT Hubhoz. A manuális kiépítéshez két lehetőség áll rendelkezésre a különböző eszközök IoT Edge hitelesítésére:

* **Szimmetrikus kulcs:** Amikor új eszközidentitást hoz létre a IoT Hub, a szolgáltatás két kulcsot hoz létre. Helyezze el az eszköz egyik kulcsát, amely a hitelesítéskor IoT Hub kulcsot.

  Ez a hitelesítési módszer gyorsabb az első lépésekhez, de nem annyira biztonságos.

* **X.509** önaírt: Két X.509-identitástanúsítványt hoz létre, és ezeket az eszközön helyezze el. Amikor új eszközidentitást hoz létre a IoT Hub, mindkét tanúsítvány ujjlenyomatait meg kell adnia. Amikor az eszköz hitelesíti magát a IoT Hub, bemutat egy tanúsítványt, IoT Hub ellenőrzi, hogy a tanúsítvány megegyezik-e az ujjlenyomatával.

  Ez a hitelesítési módszer biztonságosabb, és éles környezetben is ajánlott.

Ez a cikk mindkét hitelesítési módszert lefedi.

Ha sok eszközt kell beállítania, és nem szeretné manuálisan üzembe állítani őket, a következő cikkek egyikével megtudhatja, hogyan működik együtt a IoT Edge a IoT Hub Device Provisioning Service:

* [Eszközök létrehozása és IoT Edge X.509-tanúsítványokkal](how-to-auto-provision-x509-certs.md)
* [Eszközök létrehozása és IoT Edge TPM segítségével](how-to-auto-provision-simulated-device-linux.md)
* [Eszközök létrehozása és IoT Edge kiépítése szimmetrikus kulcsokkal](how-to-auto-provision-symmetric-keys.md)

## <a name="prerequisites"></a>Előfeltételek

# <a name="portal"></a>[Portál](#tab/azure-portal)

Ingyenes vagy standard [IoT-központ az](../iot-hub/iot-hub-create-through-portal.md) Azure-előfizetésében.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Ingyenes vagy standard [IoT Hub az](../iot-hub/iot-hub-create-through-portal.md) Azure-előfizetésében
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) a Visual Studio Code-hez

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* Ingyenes vagy standard [IoT-központ az](../iot-hub/iot-hub-create-using-cli.md) Azure-előfizetésében.
* [Azure CLI](/cli/azure/install-azure-cli) a környezetben. Az Azure CLI legalább 2.0.70-es vagy újabb verziójának kell lennie. A verziószámot az `az --version` paranccsal ellenőrizheti. Ez a verzió támogatja az „az” bővítményparancsokat, és ebben a verzióban került bevezetésre a Knack parancskeretrendszer.

---

## <a name="option-1-register-with-symmetric-keys"></a>1. lehetőség: Regisztráció szimmetrikus kulcsokkal

Számos eszközzel regisztrálhat egy új IoT Edge a IoT Hub és lekérheti a kapcsolati sztringet az Igényeinek megfelelően.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Az IoT Hubon a Azure Portal a IoT Edge az edge-kompatibilis IoT-eszközöktől elkülönítve jön létre és kezelhető.

1. Jelentkezzen be a [Azure Portal,](https://portal.azure.com) és lépjen az IoT Hubra.

1. A bal oldali panelen válassza **a** IoT Edge, majd az Add an IoT Edge device (Új eszköz **IoT Edge lehetőséget.**

   ![Új IoT Edge hozzáadása a Azure Portal](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. Az Eszköz **létrehozása lapon** adja meg a következő információkat:

   * Leíró eszközazonosító létrehozása.
   * Hitelesítési **típusként válassza** a Szimmetrikus kulcs lehetőséget.
   * Az alapértelmezett beállításokkal automatikusan létrehozhat hitelesítési kulcsokat, és csatlakoztathatja az új eszközt a hubhoz.

1. Kattintson a **Mentés** gombra.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="sign-in-to-access-your-iot-hub"></a>Az IoT Hub eléréséhez jelentkezzen be

Az Azure IoT Code-hoz Visual Studio Azure IoT-bővítményekkel műveleteket hajthat végre az IoT Hubbal. Ahhoz, hogy ezek a műveletek működjön, be kell jelentkeznie az Azure-fiókjába, és ki kell választania a központot.

1. Az Visual Studio Code-ban nyissa meg az **Explorer nézetet.**
1. Az Explorer alján bontsa ki a **Azure IoT Hub** szakaszt.

   ![Bontsa Azure IoT Hub Eszközök szakaszt](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Kattintson a **... gombra** **Azure IoT Hub** szakasz fejlécében. Ha nem látja a három pontokat, kattintson a fejlécre, vagy vigye fölé az egérmutatót.
1. Válassza **a Select IoT Hub** lehetőséget.
1. Ha nincs bejelentkezve az Azure-fiókjába, kövesse az utasításokat.
1. Válassza ki Azure-előfizetését.
1. Válassza ki az IoT Hubot.

### <a name="register-a-new-device-with-visual-studio-code"></a>Új eszköz regisztrálása a Visual Studio Code-hoz

1. A Visual Studio Code Explorerben bontsa ki a **Azure IoT Hub** szakaszt.
1. Kattintson a **... gombra** **Azure IoT Hub** szakasz fejlécében. Ha nem látja a három pontokat, kattintson a fejlécre, vagy vigye fölé az egérmutatót.
1. Válassza **a Create IoT Edge Device (Eszköz létrehozása) lehetőséget.**
1. A megnyíló szövegmezőben adjon meg egy azonosítót az eszköznek.

A kimeneti képernyőn megjelenik a parancs eredménye. A rendszer kinyomtatja az eszközadatokat, beleértve a megadott **eszközazonosítót** és a **connectionString** karakterláncot, amellyel csatlakoztathatja a fizikai eszközt az IoT Hubhoz.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az [az iot hub device-identity create paranccsal](/cli/azure/iot/hub/device-identity) hozzon létre egy új eszközidentitást az IoT Hubban. Például:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Ez a parancs három paramétert tartalmaz:

* `--device-id` vagy : Adjon meg egy leíró nevet, amely `-d` egyedi az IoT Hubon belül.
* `--hub-name` vagy `-n` : Adja meg az IoT Hub nevét.
* `--edge-enabled` vagy `--ee` : Deklarálja, hogy az eszköz IoT Edge eszköz.

   ![az iot hub device-identity create output](./media/how-to-register-device/create-edge-device-cli.png)

---

Most, hogy regisztrált egy eszközt a IoT Hub, lekéri a kapcsolati sztringet, amely a IoT Edge telepítéséhez és üzembe IoT Edge való telepítéséhez. Kövesse a cikk későbbi lépéseit a Regisztrált eszközök megtekintése és a kapcsolati sztringek [lekérése érdekében.](#view-registered-devices-and-retrieve-connection-strings)

## <a name="option-2-register-with-x509-certificates"></a>2. lehetőség: Regisztráció X.509-tanúsítványokkal

Az X.509-tanúsítványokkal való manuális üzembe IoT Edge 1.0.10-es vagy újabb verzió szükséges.

Az X.509-tanúsítványhitelesítéshez minden eszköz hitelesítési információit  ujjlenyomatok formájában biztosítjuk az eszközidentitás-tanúsítványokból. Ezeket az ujjlenyomatokat IoT Hub az eszközregisztrációkor, hogy a szolgáltatás felismerje az eszközt a csatlakozáskor.

### <a name="create-certificates-and-thumbprints"></a>Tanúsítványok és ujjlenyomatok létrehozása

Amikor X.509 IoT Edge tanúsítványokkal létesít egy eszközeszközt, az úgynevezett eszközidentitás-tanúsítványt *használja.* Ez a tanúsítvány csak az eszköz üzembe IoT Edge és az eszköz hitelesítésére szolgál a Azure IoT Hub. Ez egy levéltanúsítvány, amely nem ír alá más tanúsítványokat. Az eszközidentitási tanúsítvány elkülönül a hitelesítésszolgáltatói (CA)-tanúsítványoktól, IoT Edge az eszköz modulokat vagy lefelé irányuló eszközöket hoz létre az ellenőrzéshez. A hitelesítésszolgáltatói tanúsítványoknak a hitelesítésszolgáltatói eszközökben való IoT Edge lásd: Az Azure IoT Edge [használata.](iot-edge-certs.md)

Az X.509-es manuális üzembe építéshez a következő fájlokra lesz szüksége:

* Két eszközidentitási tanúsítvány a megfelelő titkoskulcs-tanúsítványokkal .cer vagy .pem formátumban.

  A rendszer egy tanúsítvány-/kulcsfájlkészletet biztosít a IoT Edge számára. Eszközidentitási tanúsítványok létrehozásakor állítsa be a tanúsítvány köznevét (CN) azzal az eszközazonosítóval, amelyről az eszközt el szeretné látni az IoT Hubban.

* Mindkét eszközidentitási tanúsítványból származó ujjlenyomatok.

  Az ujjlenyomatértékek az SHA-1-hashek 40 hexiális karakterei, az SHA-256-os hashek 64 hexánként megadott karakterei. Mindkét ujjlenyomat IoT Hub az eszközregisztráció során.

Ha nem rendelkezik elérhető tanúsítványokkal, létrehozhat bemutató tanúsítványokat az [IoT Edge teszteléséhez.](how-to-create-test-certificates.md) Kövesse a cikkben található utasításokat a tanúsítványlétrehozási parancsfájlok beállításához, egy legfelső szintű hitelesítésszolgáltatói tanúsítvány létrehozásához, majd két IoT Edge eszközidentitási tanúsítvány létrehozásához.

Az ujjlenyomat tanúsítványból való lekérésének egyik módja a következő openssl parancs használata:

```cmd
openssl x509 -in <certificate filename>.pem -text -fingerprint
```

### <a name="register-a-new-device"></a>Új eszköz regisztrálása

Számos eszközzel regisztrálhat egy új IoT Edge a IoT Hub és feltöltheti a tanúsítvány-ujjlenyomatait.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Az IoT Hubon a Azure Portal a IoT Edge az edge-kompatibilis IoT-eszközöktől elkülönítve vannak létrehozva és kezelhetők.

1. Jelentkezzen be a [Azure Portal,](https://portal.azure.com) és lépjen az IoT Hubra.

1. A bal oldali panelen válassza **a** IoT Edge a menüből, majd az Add an IoT Edge device (Új IoT Edge **lehetőséget.**

   ![Új IoT Edge hozzáadása a Azure Portal](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. Az Eszköz **létrehozása lapon** adja meg a következő információkat:

   * Leíró eszközazonosító létrehozása. Jegyezze fel ezt az eszközazonosítót, mert a következő szakaszban használni fogja.
   * Válassza **az X.509 Önaírt** hitelesítési típust.
   * Adja meg az elsődleges és a másodlagos identitás tanúsítványának ujjlenyomatát. Az ujjlenyomatértékek az SHA-1-hashek 40 hexánként, az SHA-256-hashek pedig 64 hexánként megadott karakterek.

1. Kattintson a **Mentés** gombra.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

A Visual Studio Code-hoz elérhető Azure IoT-bővítmény jelenleg nem támogatja az X.509-tanúsítványokkal való eszközregisztrációt.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az [az iot hub device-identity create paranccsal](/cli/azure/iot/hub/device-identity) hozzon létre egy új eszközidentitást az IoT Hubban. Például:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA thumbprint] --secondary-thumbprint [SHA thumbprint]
   ```

Ez a parancs több paramétert is tartalmaz:

* `--device-id` vagy : Adjon meg egy leíró nevet, amely `-d` az IoT Hub egyedi. Jegyezze fel ezt az eszközazonosítót, mert a következő szakaszban használni fogja.
* `hub-name` vagy `-n` : Adja meg az IoT Hub nevét.
* `--edge-enabled` vagy `--ee` : Deklarálja, hogy az eszköz IoT Edge eszköz.
* `--auth-method` vagy `--am` : Deklarálhatja az eszköz által használni fog engedélyezési típust. Ebben az esetben X.509-tanúsítvány-ujjlenyomatokat használunk.
* `--primary-thumbprint` vagy `--ptp` : Adja meg az X.509-tanúsítvány ujjlenyomatát elsődleges kulcsként való használatra.
* `--secondary-thumbprint` vagy `--stp` : Adja meg az X.509-tanúsítvány ujjlenyomatát másodlagos kulcsként való használatra.

---

Most, hogy regisztrált egy eszközt a IoT Hub, készen áll a IoT Edge telepítésére és építésére az eszközön. IoT Edge X.509-tanúsítványokkal hitelesített eszközök nem használják a kapcsolati sztringeket, így folytathatja a következő lépéssel:

* [Alkalmazások telepítése vagy Azure IoT Edge Linux rendszeren](how-to-install-iot-edge.md)
* [Windows rendszerű Azure IoT Edge telepítése vagy eltávolítása](how-to-install-iot-edge-windows-on-windows.md)

## <a name="view-registered-devices-and-retrieve-connection-strings"></a>Regisztrált eszközök megtekintése és kapcsolati sztringek lekérése

A szimmetrikus kulcsos hitelesítést használó eszközöknek kapcsolati sztringre van szükségük a hálózati IoT Edge telepítéséhez és üzembe IoT Edge eszközökhöz.

Az X.509 tanúsítványhitelesítést felhasználó eszközöknek nincs szükségük kapcsolati sztringre. Ehelyett ezeknek az eszközöknek szükségük van az IoT Hub nevére, az eszköz nevére és a tanúsítványfájljaikra a IoT Edge telepítéséhez és üzembe IoT Edge számára.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Az IoT Hubhoz csatlakozni képes összes peremhálózati eszköz megjelenik a IoT Edge **lapon.**

![A Azure Portal az IoT Hubban IoT Edge összes eszköz megtekintéséhez](./media/how-to-register-device/portal-view-devices.png)

Amikor készen áll az eszköz beállítására, szüksége lesz a kapcsolati sztringre, amely összeköti a fizikai eszközt az IoT Hubban található identitásával.

A szimmetrikus kulcsokkal hitelesített eszközök kapcsolati sztringjeik a portálon másolhatóak.

1. A **IoT Edge** lapján kattintson az eszközazonosítóra a IoT Edge listájában.
2. Másolja ki az Elsődleges kapcsolati sztring **vagy** a **Másodlagos kapcsolati sztring értékét.**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>A IoT Edge Code-Visual Studio megtekintése

Az IoT Hubhoz csatlakozó összes eszköz  megjelenik a Azure IoT Hub Code Explorer Visual Studio szakaszában. IoT Edge eszközök megkülönböztethetők a különböző ikonnal $edgeAgent nem peremeszközöktől,  és hogy a $edgeAgent- és **$edgeHub-modulok** minden IoT Edge telepítve vannak.

![A VS Code használata az IoT-IoT Edge összes eszközének megtekintéséhez](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>A kapcsolati sztring lekérése Visual Studio kóddal

Amikor készen áll az eszköz beállítására, szüksége lesz a kapcsolati sztringre, amely összeköti a fizikai eszközt az IoT Hubban található identitásával.

1. Kattintson a jobb gombbal az eszköz azonosítójára a **Azure IoT Hub** szakaszban.
1. Válassza az **Eszközkapcsolati sztring másolása lehetőséget.**

   A rendszer a vágólapra másolja a kapcsolati sztringet.

A jobb gombbal kattintva **a get Device Info** (Eszközadatok lekérte) elemre kattintva a kimeneti ablakban az összes eszközinformációt, köztük a kapcsolati sztringet is láthatja.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Az IoT Edge megtekintése az Azure CLI használatával

Az [az iot hub device-identity list paranccsal](/cli/azure/iot/hub/device-identity) megtekintheti az IoT Hubban található összes eszközt. Például:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Az eszközként regisztrált IoT Edge a **capabilities.iotEdge** tulajdonság true **(igaz)** lesz.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>A kapcsolati sztring lekérése az Azure CLI használatával

Amikor készen áll az eszköz beállítására, szüksége lesz a kapcsolati sztringre, amely összeköti a fizikai eszközt az IoT Hubban található identitásával. Az [az iot hub device-identity connection-string show paranccsal](/cli/azure/iot/hub/device-identity/connection-string) adja vissza egyetlen eszköz kapcsolati sztringét:

   ```azurecli
   az iot hub device-identity connection-string show --device-id [device id] --hub-name [hub name]
   ```

>[!TIP]
>A `connection-string show` parancs az Azure IoT-bővítmény 0.9.8-as verziójában lett bevezetve, lecserélve az elavult `show-connection-string` parancsot. Ha hibaüzenetet kap a parancs futtatásakor, győződjön meg arról, hogy a bővítmény verziója 0.9.8-as vagy újabb verzióra frissült. További információkért és a legújabb frissítésekért lásd: Microsoft Azure [IoT-bővítmény az Azure CLI-hez.](https://github.com/Azure/azure-iot-cli-extension)

A paraméter értéke `device-id` megkülönbözteti a kis- és nagybetűket.

A kapcsolati sztring eszközön való másolásakor ne használjon idézőjeleket a kapcsolati sztring körül.

---

## <a name="next-steps"></a>Következő lépések

Most, hogy regisztrált egy eszközt a IoT Hub, készen áll a IoT Edge telepítésére és építésére az eszközön.

* [Alkalmazások telepítése vagy Azure IoT Edge Linux rendszeren](how-to-install-iot-edge.md)
* [Windows rendszerű Azure IoT Edge telepítése vagy eltávolítása](how-to-install-iot-edge-windows-on-windows.md)
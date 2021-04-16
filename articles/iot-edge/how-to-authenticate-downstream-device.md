---
title: Lefelé irányuló eszközök hitelesítése – Azure IoT Edge | Microsoft Docs
description: Az lefelé irányuló eszközök vagy levéleszközök hitelesítése a IoT Hub és a kapcsolatuk útválasztása Azure IoT Edge átjáróeszközökön keresztül.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/15/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4702682dcd6af68242fd5a34d1fb2e0a9273da36
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482024"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Lefelé irányuló eszköz hitelesítése az Azure IoT Hubon

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Transzparens átjáró esetén az lefelé irányuló eszközöknek (más néven levéleszközöknek vagy gyermekeszközöknek) identitásra van szükségük a IoT Hub mint bármely más eszköz esetében. Ez a cikk végigvezeti a lefelé irányuló eszköz hitelesítésének IoT Hub lehetőségein, majd bemutatja, hogyan deklarálható az átjárókapcsolat.

A sikeres transzparens átjárókapcsolat beállításának három általános lépése van. Ez a cikk a második lépést foglalja magában:

1. Konfigurálja az átjáróeszközt kiszolgálóként, hogy az lefelé irányuló eszközök biztonságosan csatlakoz tudjanak hozzá. Állítsa be az átjárót, hogy üzeneteket fogadjon az lefelé irányuló eszközökről, és a megfelelő célhelyre irányítsa őket. A lépésekért [lásd: Configure an IoT Edge device to](how-to-create-transparent-gateway.md)act as a transparent gateway (Átjáró konfigurálása transzparens átjáróként) .
2. **Hozzon létre egy eszközidentitást az lefelé irányuló eszközhöz, hogy az hitelesíthető IoT Hub. Konfigurálja az lefelé irányuló eszközt úgy, hogy üzeneteket küldjön az átjáróeszközön keresztül.**
3. Csatlakoztassa az lefelé irányuló eszközt az átjáróeszközhöz, és kezdje el küldeni az üzeneteket. A lépésekért lásd: Lefelé irányuló eszköz csatlakoztatása egy Azure IoT Edge [átjáróhoz.](how-to-connect-downstream-device.md)

Az lefelé irányuló eszközök a következő három módszer egyikével hitelesíthetők az IoT Hub-val: szimmetrikus kulcsok (más néven megosztott hozzáférési kulcsok), X.509 önaírt tanúsítványok vagy X.509 hitelesítésszolgáltatói (CA) aláírt tanúsítványok. A hitelesítési lépések hasonlóak a nem IoT Edge-eszközök IoT Hub-val való beállításának lépéseihez, és kis eltérésekkel deklarálják az átjárókapcsolatot.

Az lefelé irányuló eszközök automatikus kiépítése a Azure IoT Hub Device Provisioning Service (DPS) szolgáltatással nem támogatott.

## <a name="prerequisites"></a>Előfeltételek

Kövesse a Configure an IoT Edge device to act as a transparent gateway (Transzparens átjáróként való használatra IoT Edge eszköz [konfigurálása) lépéseit.](how-to-create-transparent-gateway.md)

Ha X.509-hitelesítést használ, tanúsítványokat fog létrehozni az lefelé irányuló eszközhöz. Használja ismét a transzparens átjáróhoz használt főtanúsítványt és a tanúsítványgeneráló parancsfájlt.

Ez a cikk több ponton *is hivatkozik az* átjáró állomásnevére. Az átjáró gazdaneve a konfigurációs fájl **gazdanév** paraméterében van deklarálva a IoT Edge átjáróeszközön. Az lefelé irányuló eszköz kapcsolati sztringben hivatkozik rá. Az átjáró állomásnevének feloldhatónak kell lennie egy IP-címre, akár DNS-sel, akár az alhálózati eszközön található gazdagépfájl-bejegyzéssel.

## <a name="register-device-with-iot-hub"></a>Eszköz regisztrálása IoT Hub

Válassza ki, hogyan szeretné hitelesíteni az lefelé irányuló eszközt a IoT Hub:

* [Szimmetrikus kulcsos hitelesítés:](#symmetric-key-authentication)IoT Hub létrehoz egy kulcsot, amely a lefelé irányuló eszközre kerül. Az eszköz hitelesítése után a IoT Hub ellenőrzi, hogy a két kulcs egyezik-e. Szimmetrikus kulcsos hitelesítéshez nem kell további tanúsítványokat létrehoznia.

  Ez a módszer gyorsabb az első lépésekben, ha fejlesztési vagy tesztelési forgatókönyvben tesztel átjárókat.

* X.509 önaírt hitelesítés: Más néven ujjlenyomat-hitelesítés, mert az eszköz [X.509-tanúsítványának](#x509-self-signed-authentication)ujjlenyomatát a IoT Hub.

  Éles környezetben ajánlott a tanúsítványhitelesítés az eszközökhöz.

* [X.509 hitelesítésszolgáltató által](#x509-ca-signed-authentication)aláírt hitelesítés: Töltse fel a legfelső szintű hitelesítésszolgáltatói tanúsítványt a IoT Hub. Amikor az eszközök bemutatják az X.509-tanúsítványukat hitelesítésre, a IoT Hub ellenőrzi, hogy az ugyanannak a legfelső szintű hitelesítésszolgáltatói tanúsítványnak aláírt megbízhatósági lánchoz tartozik-e.

  Éles környezetben ajánlott a tanúsítványhitelesítés az eszközökhöz.

### <a name="symmetric-key-authentication"></a>Tartalomkulcs-hitelesítés

A szimmetrikus kulcsos hitelesítés vagy a megosztott hozzáférési kulcsos hitelesítés a legegyszerűbb módszer a IoT Hub. Szimmetrikus kulcsos hitelesítés esetén a rendszer egy base64-kulcsot társít az IoT-eszközazonosítóhoz a IoT Hub. Ezt a kulcsot fel kell foglalnia az IoT-alkalmazásokba, hogy az eszköz bemutathatja azt, amikor csatlakozik IoT Hub.

Adjon hozzá egy új IoT-eszközt az IoT-központban a Azure Portal, az Azure CLI vagy az Visual Studio Code IoT Visual Studio bővítményével. Ne feledje, hogy a lefelé irányuló eszközöket normál IoT IoT Hub eszközökként kell azonosítani, nem IoT Edge eszközökként.

Az új eszközidentitás létrehozásakor adja meg a következő információkat:

* Hozzon létre egy azonosítót az eszközhöz.

* Hitelesítési **típusként válassza** a Szimmetrikus kulcs lehetőséget.

* Válassza **a Szülőeszköz beállítása** lehetőséget, és IoT Edge az átjáróeszközt, amelyről az lefelé irányuló eszköz csatlakozni fog. A szülőt később is módosíthatja.

   ![Eszközazonosító létrehozása szimmetrikus kulcsos hitelesítéssel a portálon](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

   >[!NOTE]
   >A korábban választható lépésként használt szülőeszköz beállítása a szimmetrikus kulcsos hitelesítést felhasználó lefelé irányuló eszközökhöz. Az 1.1.0 IoT Edge kezdve azonban minden lefelé irányuló eszközt hozzá kell rendelni egy szülőeszközhöz.
   >
   >Az **authenticationMode** környezeti változó CloudAndScope értékre való beállításával konfigurálhatja a IoT Edge hubot, hogy visszatér az előző **viselkedéshez.**

Ugyanezen művelet végrehajtásához használhatja az [Azure CLI IoT-bővítményét](https://github.com/Azure/azure-iot-cli-extension) is. Az alábbi példa az [az iot hub device-identity](/cli/azure/iot/hub/device-identity) parancsot használja egy szimmetrikus kulcsos hitelesítést használó új IoT-eszköz létrehozásához és egy szülőeszköz hozzárendeléséhez:

```azurecli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

Ezután olvassa [be és módosítsa a kapcsolati sztringet,](#retrieve-and-modify-connection-string) hogy az eszköz tudja, hogyan csatlakozhat az átjárón keresztül.

### <a name="x509-self-signed-authentication"></a>X.509 önaírt hitelesítés

Az X.509 önaírt hitelesítés (más néven ujjlenyomat-hitelesítés) esetén tanúsítványokat kell létrehoznia az lefelé irányuló eszközön. Ezek a tanúsítványok egy ujjlenyomattal vannak számukra, amely a hitelesítéshez IoT Hub meg.

1. A hitelesítésszolgáltatói tanúsítvány használatával hozzon létre két eszköztanúsítványt (elsődleges és másodlagos) az lefelé irányuló eszközhöz.

   Ha nem rendelkezik hitelesítésszolgáltatóval X.509-tanúsítványok létrehozásához, az IoT Edge bemutatótanúsítvány-parancsfájlokkal létrehozhat lefelé irányuló eszköztanúsítványokat. [](how-to-create-test-certificates.md#create-downstream-device-certificates) Kövesse az önaírt tanúsítványok létrehozásának lépéseit. Használja ugyanazt a legfelső szintű hitelesítésszolgáltatói tanúsítványt, amely az átjáróeszköz tanúsítványait generálta.

   Ha saját tanúsítványokat hoz létre, győződjön meg arról, hogy az eszköztanúsítvány tulajdonosneve az IoT-eszköz regisztrálásakor használt eszközazonosítóra van Azure IoT Hub. Ez a beállítás szükséges a hitelesítéshez.

2. Az SHA1 ujjlenyomat (az IoT Hub-felületen ujjlenyomat) lekérése minden tanúsítványból, amely egy 40 hexadecimális karakterből áll. A következő openssl paranccsal tekintse meg a tanúsítványt, és keresse meg az ujjlenyomatot:

   * Windows:

     ```PowerShell
     openssl x509 -in <path to primary device certificate>.cert.pem -text -fingerprint
     ```

   * Linux:

     ```Bash
     openssl x509 -in <path to primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
     ```

   Futtassa ezt a parancsot kétszer, egyszer az elsődleges tanúsítványhoz és egyszer a másodlagos tanúsítványhoz. Mindkét tanúsítványhoz ujjlenyomatot kell adnia, amikor önaírt X.509-tanúsítványokkal regisztrál egy új IoT-eszközt.

3. Lépjen az IoT Hubra a Azure Portal, és hozzon létre egy új IoT-eszközidentitást a következő értékekkel:

   * Adja meg **az eszköztanúsítványok** tulajdonosnevének megfelelő eszközazonosítót.
   * Válassza **az X.509 önaírt hitelesítési** típust.
   * Illessze be az eszköz elsődleges és másodlagos tanúsítványaiból másolt hexadecimális sztringeket.
   * Válassza **a Szülőeszköz beállítása** lehetőséget, és IoT Edge az átjáróeszközt, amelyről a lefelé irányuló eszköz csatlakozni fog. A szülőt később is módosíthatja.

   ![Eszközazonosító létrehozása önaírt X.509-hitelesítéssel a portálon](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. Másolja az elsődleges és másodlagos eszköztanúsítványokat és azok kulcsait az lefelé irányuló eszköz bármely helyére. Helyezze át annak a megosztott legfelső szintű hitelesítésszolgáltatói tanúsítványnak a másolatát is, amely az átjáróeszköz tanúsítványát és az lefelé irányuló eszköztanúsítványokat is létrehozott.

   Ezekre a tanúsítványfájlokra a lefelé irányuló eszköz minden olyan alkalmazásában hivatkozni fog, amely a IoT Hub. A tanúsítványfájlok áthelyezéséhez használhat olyan szolgáltatást Azure Key Vault [vagy](../key-vault/index.yml) egy olyan függvényt, mint a [Biztonságos](https://www.ssh.com/ssh/scp/) másolási protokoll.

5. A választott nyelvtől függően tekintse át az X.509-tanúsítványok IoT-alkalmazásokban való hivatkozásának mintáit:

   * C#: [Az X.509-biztonság beállítása az Azure IoT Hubban](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

Ugyanezen eszköz-létrehozási művelet végrehajtásához használhatja az [Azure CLI IoT-bővítményét](https://github.com/Azure/azure-iot-cli-extension) is. Az alábbi példa az [az iot hub device-identity](/cli/azure/iot/hub/device-identity) paranccsal létrehoz egy új IoT-eszközt X.509 önaírt hitelesítéssel, és hozzárendel egy szülőeszközt:

```azurecli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Ezután olvassa [be és módosítsa a kapcsolati sztringet,](#retrieve-and-modify-connection-string) hogy az eszköz tudja, hogyan csatlakozhat az átjárón keresztül.

### <a name="x509-ca-signed-authentication"></a>X.509 hitelesítésszolgáltató által aláírt hitelesítés

Az X.509 hitelesítésszolgáltató (CA) aláírt hitelesítéséhez szüksége van egy, a IoT Hub-ban regisztrált legfelső szintű hitelesítésszolgáltatói tanúsítványra, amely az lefelé irányuló eszköz tanúsítványának aláírására használható. A hitelesítéshez minden olyan eszköz jogosult lesz hitelesítésre, amely a legfelső szintű hitelesítésszolgáltató tanúsítványa vagy bármely köztes tanúsítványa által kiadott tanúsítványt használ.

Ez a szakasz az [X.509-biztonság](../iot-hub/iot-hub-security-x509-get-started.md)beállítása az Azure IoT Hubban című IoT Hub cikkben található utasításokon alapul.

1. A hitelesítésszolgáltatói tanúsítvány használatával hozzon létre két eszköztanúsítványt (elsődleges és másodlagos) az lefelé irányuló eszközhöz.

   Ha nem rendelkezik hitelesítésszolgáltatóval X.509-tanúsítványok létrehozásához, az IoT Edge bemutatótanúsítvány-parancsfájlokkal létrehozhat lefelé irányuló eszköztanúsítványokat. [](how-to-create-test-certificates.md#create-downstream-device-certificates) Kövesse a hitelesítésszolgáltató által aláírt tanúsítványok létrehozásának lépéseit. Használja ugyanazt a legfelső szintű hitelesítésszolgáltatói tanúsítványt, amely az átjáróeszköz tanúsítványait generálta.

2. Kövesse az [X.509](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) hitelesítésszolgáltatói tanúsítványok regisztrálása az IoT Hubon szakasz *X.509-biztonság beállítása az Azure IoT Hubban című szakaszának utasításait.* Ebben a szakaszban a következő lépéseket kell végrehajtania:

   1. Töltsön fel egy legfelső szintű hitelesítésszolgáltatói tanúsítványt. Ha a bemutató tanúsítványokat használja, a legfelső szintű hitelesítésszolgáltató **\<path> a /certs/azure-iot-test-only.root.ca.cert.pem.**

   2. Ellenőrizze, hogy rendelkezik-e ezzel a legfelső szintű hitelesítésszolgáltatói tanúsítvánnyal.

3. Kövesse az [X.509-biztonság](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) beállítása az Azure IoT Hubban című cikk *X.509-eszköz* létrehozása az IoT Hubhoz szakaszának utasításait. Ebben a szakaszban a következő lépéseket kell végrehajtania:

   1. Adjon hozzá egy új eszközt. Adjon kisbetűs nevet az **eszközazonosítónak,** és válassza ki az **X.509 hitelesítésszolgáltató által aláírt hitelesítési típust.**

   2. Állítson be egy szülőeszközt. Válassza **a Szülőeszköz beállítása lehetőséget,** és IoT Edge átjáróeszközt, amely biztosítja a kapcsolatot a IoT Hub.

4. Hozzon létre egy tanúsítványláncot az lefelé irányuló eszközhöz. Használja ugyanazt a legfelső szintű hitelesítésszolgáltatói tanúsítványt, IoT Hub a láncot. Használja ugyanazt a kisbetűs eszközazonosítót, mint amit a portálon adott meg az eszközidentitásnak.

5. Másolja az eszköz tanúsítványát és kulcsait a lefelé irányuló eszköz bármely helyére. Helyezze át annak a megosztott legfelső szintű hitelesítésszolgáltatói tanúsítványnak a másolatát is, amely az átjáróeszköz tanúsítványát és az lefelé irányuló eszköztanúsítványokat is létrehozott.

   Ezekre a fájlokra a lefelé irányuló eszköz minden olyan alkalmazásában hivatkozni fog, amely a IoT Hub. A tanúsítványfájlok áthelyezéséhez használhat olyan szolgáltatást Azure Key Vault [vagy](../key-vault/index.yml) egy olyan függvényt, mint a [Biztonságos](https://www.ssh.com/ssh/scp/) másolási protokoll.

6. Az Ön által választott nyelvtől függően tekintse át az X.509-tanúsítványok IoT-alkalmazásokban való hivatkozásának mintáit:

   * C#: [Az X.509-biztonság beállítása az Azure IoT Hubban](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

Ugyanezen eszköz-létrehozási művelet végrehajtásához használhatja az [Azure CLI IoT-bővítményét](https://github.com/Azure/azure-iot-cli-extension) is. Az alábbi példa az [az iot hub device-identity](/cli/azure/iot/hub/device-identity) parancsot használja egy új IoT-eszköz létrehozásához X.509 hitelesítésszolgáltató által aláírt hitelesítéssel, és hozzárendel egy szülőeszközt:

```azurecli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Ezután olvassa [be és módosítsa a kapcsolati sztringet,](#retrieve-and-modify-connection-string) hogy az eszköz tudja, hogyan csatlakozhat az átjárón keresztül.

## <a name="retrieve-and-modify-connection-string"></a>Kapcsolati sztring lekérése és módosítása

Miután létrehozta az IoT-eszközidentitást a portálon, lekérheti annak elsődleges vagy másodlagos kulcsait. Ezen kulcsok egyikének szerepelnie kell a kapcsolati sztringben, amit az alkalmazások a kapcsolati IoT Hub. A szimmetrikus kulcsos hitelesítéshez a IoT Hub biztosítja a teljes mértékben formázott kapcsolati sztringet az eszköz adatai között. A kapcsolati sztringhez további információkat kell hozzáadnia az átjáróeszközről.

Az lefelé irányuló eszközök kapcsolati sztringinek a következő összetevőkre van szükségük:

* Az IoT Hub, amelyhez az eszköz csatlakozik: `Hostname={iothub name}.azure-devices.net`
* A központban regisztrált eszközazonosító: `DeviceID={device ID}`
* A hitelesítési módszer, legyen az szimmetrikus kulcs vagy X.509-tanúsítvány
  * Szimmetrikus kulcsos hitelesítés használata esetén adja meg az elsődleges vagy a másodlagos kulcsot: `SharedAccessKey={key}`
  * X.509-tanúsítványhitelesítés használata esetén adjon meg egy jelzőt: `x509=true`
* Az átjáróeszköz, amelyről az eszköz csatlakozik. Adja meg **a gazdagépnév** értékét IoT Edge átjáróeszköz konfigurációs fájljában: `GatewayHostName={gateway hostname}`

A teljes kapcsolati sztring együttesen így néz ki:

```console
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Vagy:

```console
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;x509=true;GatewayHostName=myGatewayDevice
```

A szülő/gyermek kapcsolatnak köszönhetően leegyszerűsítheti a kapcsolati sztringet, ha közvetlenül a kapcsolatgazdaként hívja meg az átjárót. Például:

```console
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

Ezt a módosított kapcsolati sztringet a transzparens átjárósorozat következő cikkében fogja használni.

## <a name="next-steps"></a>Következő lépések

Ezen a ponton már van egy IoT Edge, amely regisztrálva van az IoT Hubon, és transzparens átjáróként van konfigurálva. Emellett rendelkezik egy, az IoT Hubbal regisztrált, az átjáróeszközére mutató lefelé irányuló eszközzel is.

Ezután úgy kell konfigurálnia az lefelé irányuló eszközt, hogy megbízjon az átjáróeszközben, és biztonságosan csatlakozzon hozzá. Folytassa a transzparens átjárók sorozatának következő cikkével, amely egy lefelé irányuló eszköz csatlakoztatása egy Azure IoT Edge [átjáróhoz.](how-to-connect-downstream-device.md)
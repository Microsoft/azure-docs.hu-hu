---
title: Alsóbb rétegbeli eszközök hitelesítése – Azure IoT Edge | Microsoft Docs
description: Hogyan hitelesítheti az alárendelt eszközöket vagy a levélben lévő eszközöket IoT Hub, és hogyan irányíthatja át a kapcsolódást Azure IoT Edge átjáró-eszközökön.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/15/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c799e38092c5983b4ad0e3daea6aae99934c7302
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200884"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Lefelé irányuló eszköz hitelesítése az Azure IoT Hubon

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Transzparens átjáró esetén az alsóbb rétegbeli eszközöknek (más néven levél-vagy gyermek-eszközöknek) identitásokra van szükségük IoT Hub mint bármely más eszköz. Ez a cikk végigvezeti az alsóbb rétegbeli eszközök IoT Hubra való hitelesítésének lehetőségeit, majd bemutatja, hogyan deklarálhatja az átjáró-kapcsolatokat.

A sikeres transzparens átjáró-kapcsolatok létrehozásához három általános lépés szükséges. Ez a cikk a második lépést ismerteti:

1. Konfigurálja az átjáró-eszközt kiszolgálóként, hogy az alsóbb rétegbeli eszközök biztonságosan kapcsolódjanak hozzá. Állítsa be az átjárót, hogy üzeneteket kapjon az alsóbb rétegbeli eszközökről, és irányítsa azokat a megfelelő helyre. Ezekhez a lépésekhez lásd: [IoT Edge eszköz konfigurálása transzparens átjáróként való](how-to-create-transparent-gateway.md)használatra.
2. **Hozzon létre egy eszköz-identitást az alsóbb rétegbeli eszköz számára, hogy a hitelesítés a IoT Hub használatával történjen. Az alsóbb rétegbeli eszköz konfigurálásával üzeneteket küldhet az átjáró eszközön keresztül.**
3. Az alárendelt eszköz csatlakoztatása az átjáró eszközhöz és az üzenetek küldésének megkezdése. Ezeket a lépéseket lásd: [alsóbb rétegbeli eszköz csatlakoztatása Azure IoT Edge átjáróhoz](how-to-connect-downstream-device.md).

Az alsóbb rétegbeli eszközök a következő három módszer egyikével hitelesíthetők a IoT Hub használatával: szimmetrikus kulcsok (más néven közös hozzáférési kulcsok), X. 509 önaláírt tanúsítvány vagy X. 509 hitelesítésszolgáltató (CA) által aláírt tanúsítvány. A hitelesítési lépések hasonlók a nem IoT-eszközök IoT Hub-vel való beállításához használt lépésekhez, és kisebb eltéréseket jelentenek az átjáró kapcsolatának bejelentéséhez.

Az alsóbb rétegbeli eszközök automatikus kiépítés az Azure IoT Hub Device Provisioning Service (DPS) nem támogatott.

## <a name="prerequisites"></a>Előfeltételek

Végezze el az [IoT Edge-eszköz konfigurálásának lépéseit transzparens átjáróként való](how-to-create-transparent-gateway.md)művelethez.

Ha X. 509 hitelesítést használ, az alsóbb rétegbeli eszközhöz tanúsítványokat fog készíteni. Ugyanazzal a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvánnyal rendelkezik, és a tanúsítvány generálására szolgáló parancsfájl, amelyet az átlátszó átjáró számára a rendelkezésre álló cikkhez használ.

Ez a cikk az *átjáró állomásneve* több ponton is hivatkozik. Az átjáró állomásneve deklarálva van a konfigurációs fájl **hostname** paraméterében a IoT Edge átjáró eszközön. Az alárendelt eszköz kapcsolódási sztringje hivatkozik rá. Az átjáró állomásneve feloldhatónak kell lennie egy IP-címhez, vagy a DNS-t vagy egy Host file bejegyzést kell használnia az alsóbb rétegbeli eszközön.

## <a name="register-device-with-iot-hub"></a>Eszköz regisztrálása a IoT Hub

Válassza ki, hogyan szeretné hitelesíteni az alsóbb rétegbeli eszközt a IoT Hub:

* [Szimmetrikus kulcsos hitelesítés](#symmetric-key-authentication): IoT hub létrehoz egy kulcsot, amelyet az alsóbb rétegbeli eszközön helyez el. Az eszköz hitelesítése után IoT Hub ellenőrzi, hogy a két kulcs egyezik-e. A szimmetrikus kulcsos hitelesítés használatához nem kell további tanúsítványokat létrehoznia.

  Ez a módszer gyorsabb a kezdéshez, ha fejlesztési vagy tesztelési forgatókönyvben végzi az átjárók tesztelését.

* [X. 509 önaláírt hitelesítés](#x509-self-signed-authentication): néha ujjlenyomat-hitelesítésnek is nevezik, mivel az eszköz X. 509 tanúsítványának ujjlenyomatát IoT hub.

  Az éles környezetben lévő eszközökhöz ajánlott a tanúsítvány hitelesítése.

* [X. 509 hitelesítésszolgáltató által aláírt hitelesítés](#x509-ca-signed-authentication): töltse fel a legfelső szintű hitelesítésszolgáltatói tanúsítványt IoT Hubre. Ha az eszközök a hitelesítéshez X. 509 tanúsítvánnyal rendelkeznek, IoT Hub ellenőrzi, hogy az ugyanahhoz a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvánnyal aláírt megbízhatósági lánchoz tartozik-e.

  Az éles környezetben lévő eszközökhöz ajánlott a tanúsítvány hitelesítése.

### <a name="symmetric-key-authentication"></a>Tartalomkulcs-hitelesítés

A szimmetrikus kulcsos hitelesítés vagy a közös hozzáférésű kulcsos hitelesítés a legegyszerűbb módszer a IoT Hub való hitelesítéshez. A szimmetrikus kulcsos hitelesítéssel Base64-kulcsot társítunk a IoT-eszköz AZONOSÍTÓJÁHOZ IoT Hub. Ezt a kulcsot a IoT-alkalmazásokban is felveszi, hogy az eszköz bemutassa a IoT Hubhoz való csatlakozáskor.

Vegyen fel egy új IoT-eszközt az IoT hub-ban a Visual Studio Code-hoz készült Azure Portal, az Azure CLI vagy a IoT-bővítmény használatával. Ne feledje, hogy az alsóbb rétegbeli eszközöket IoT Hub kell azonosítani a normál IoT-eszközként, nem IoT Edge eszközöket.

Az új eszköz identitásának létrehozásakor adja meg a következő információkat:

* Hozzon létre egy azonosítót az eszközhöz.

* A hitelesítési típusként válassza a **szimmetrikus kulcs** lehetőséget.

* Válassza a **szülő eszköz beállítása** elemet, és válassza ki azt a IoT Edge átjáró-eszközt, amelyre ez az alárendelt eszköz fog csatlakoztatva. Később bármikor módosíthatja a szülőt.

   ![Eszköz AZONOSÍTÓjának létrehozása szimmetrikus kulcsú hitelesítéssel a portálon](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

   >[!NOTE]
   >Ha a fölérendelt eszközt a szimmetrikus kulcsos hitelesítést használó alsóbb rétegbeli eszközökhöz nem kötelező lépésként szeretné beállítani. A IoT Edge Version 1.1.0-től kezdődően azonban minden alsóbb rétegbeli eszköznek hozzá kell rendelnie egy fölérendelt eszközhöz.
   >
   >Az IoT Edge hub úgy is beállítható, hogy visszalépjen az előző viselkedésre úgy, hogy a környezeti változót a **authenticationmode tulajdonsághoz** értékre állítja a **CloudAndScope**.

Ugyanezen művelet végrehajtásához használhatja az [Azure CLI-hez készült IoT-bővítményt](https://github.com/Azure/azure-iot-cli-extension) is. Az alábbi példa az az [IOT hub Device-Identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) paranccsal hoz létre egy új IOT-eszközt szimmetrikus kulcsos hitelesítéssel, és hozzárendel egy fölérendelt eszközt:

```azurecli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

Ezután [kérje le és módosítsa a kapcsolati karakterláncot](#retrieve-and-modify-connection-string) , hogy az eszköz képes legyen csatlakozni az átjárón keresztül.

### <a name="x509-self-signed-authentication"></a>X. 509 önaláírt hitelesítés

Az X. 509 önaláírt hitelesítéshez (más néven ujjlenyomatos hitelesítéshez) létre kell hoznia az alárendelt eszközön elhelyezni kívánt tanúsítványokat. Ezek a tanúsítványok olyan ujjlenyomattal rendelkeznek, amelyet IoT Hub a hitelesítéshez.

1. A HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány használatával hozzon létre két (elsődleges és másodlagos) tanúsítványokat az alsóbb rétegbeli eszközhöz.

   Ha nem rendelkezik hitelesítésszolgáltatóval X. 509 tanúsítványok létrehozásához, akkor a IoT Edge demó tanúsítvány parancsfájljaival [hozhat létre alsóbb rétegbeli tanúsítványokat](how-to-create-test-certificates.md#create-downstream-device-certificates). Kövesse az önaláírt tanúsítványok létrehozásához szükséges lépéseket. Ugyanazt a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt használja, amely az átjáró-eszköz tanúsítványait generálta.

   Ha saját tanúsítványokat hoz létre, győződjön meg arról, hogy az eszköz tanúsítványának tulajdonos neve a IoT-eszköz Azure-IoT Hub való regisztrálása során használt eszköz-AZONOSÍTÓra van beállítva. Ez a beállítás a hitelesítéshez szükséges.

2. Kérje le az SHA1 ujjlenyomatot (a IoT Hub felületen található ujjlenyomatot) minden tanúsítványból, amely egy 40 hexadecimális karakterből álló karakterlánc. A következő OpenSSL-paranccsal tekintheti meg a tanúsítványt, és keresse meg az ujjlenyomatot:

   * Windows:

     ```PowerShell
     openssl x509 -in <path to primary device certificate>.cert.pem -text -fingerprint
     ```

   * Linux:

     ```Bash
     openssl x509 -in <path to primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
     ```

   Futtassa kétszer ezt a parancsot az elsődleges tanúsítványhoz és egyszer a másodlagos tanúsítványhoz. Mindkét tanúsítvány ujjlenyomatát adja meg, ha új IoT-eszközt regisztrál önaláírt X. 509 tanúsítvánnyal.

3. Navigáljon az IoT hubhoz a Azure Portalon, és hozzon létre egy új IoT-eszköz identitását a következő értékekkel:

   * Adja meg az eszköz tanúsítványának tulajdonos nevével megegyező **azonosítóját** .
   * Válassza az **X. 509 önaláírt** hitelesítési típusként lehetőséget.
   * Illessze be azokat a hexadecimális karakterláncokat, amelyeket az eszköz elsődleges és másodlagos tanúsítványainakből másolt.
   * Válassza a **szülő eszköz beállítása** elemet, és válassza ki azt a IoT Edge átjáró-eszközt, amelyre ez az alárendelt eszköz fog csatlakoztatva. Később bármikor módosíthatja a szülőt.

   ![Eszköz AZONOSÍTÓjának létrehozása X. 509 önaláírt hitelesítéssel a portálon](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. Másolja mind az elsődleges, mind a másodlagos eszköz tanúsítványait, valamint azok kulcsait az alsóbb rétegbeli eszköz bármely helyére. Helyezze át a megosztott legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány másolatát is, amely az átjáró-eszköz tanúsítványát és az alsóbb rétegbeli eszköz tanúsítványait is létrehozta.

   Ezekre a tanúsítványokra az alsóbb rétegbeli eszközön lévő, IoT Hubhoz csatlakozó alkalmazásokban hivatkozhat. Használhat olyan szolgáltatásokat, mint például a [Azure Key Vault](../key-vault/index.yml) vagy a [biztonságos másolási protokollt](https://www.ssh.com/ssh/scp/) használó függvények a tanúsítványfájl áthelyezéséhez.

5. Az előnyben részesített nyelvtől függően tekintse át az X. 509 tanúsítványok IoT-alkalmazásokban való hivatkozásának mintáit:

   * C#: [az X. 509 Biztonság beállítása az Azure IoT hub-ban](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample. c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509. Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

Az [Azure CLI-hez készült IoT-bővítményt](https://github.com/Azure/azure-iot-cli-extension) is használhatja ugyanazon eszköz-létrehozási művelet végrehajtásához. Az alábbi példa az az [IOT hub Device-Identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) paranccsal hoz létre egy új IOT-eszközt X. 509 önaláírt hitelesítéssel, és hozzárendel egy fölérendelt eszközt:

```azurecli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Ezután [kérje le és módosítsa a kapcsolati karakterláncot](#retrieve-and-modify-connection-string) , hogy az eszköz képes legyen csatlakozni az átjárón keresztül.

### <a name="x509-ca-signed-authentication"></a>X. 509 HITELESÍTÉSSZOLGÁLTATÓ által aláírt hitelesítés

Az X. 509 hitelesítésszolgáltató (CA) által aláírt hitelesítéshez szükség van egy IoT Hubban regisztrált legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványra, amelyet az alsóbb rétegbeli eszköz tanúsítványának aláírásához használ. Minden olyan eszköz, amely a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány vagy a köztes tanúsítványok valamelyikével kapcsolatos hibát okozó tanúsítványt használ, a hitelesítés engedélyezve lesz.

Ez a szakasz az [X. 509 biztonság Azure IoT hub-ban való beállításának](../iot-hub/iot-hub-security-x509-get-started.md)IoT hub cikkében részletesen ismertetett utasításokon alapul.

1. A HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány használatával hozzon létre két (elsődleges és másodlagos) tanúsítványokat az alsóbb rétegbeli eszközhöz.

   Ha nem rendelkezik hitelesítésszolgáltatóval X. 509 tanúsítványok létrehozásához, akkor a IoT Edge demó tanúsítvány parancsfájljaival [hozhat létre alsóbb rétegbeli tanúsítványokat](how-to-create-test-certificates.md#create-downstream-device-certificates). Kövesse a HITELESÍTÉSSZOLGÁLTATÓ által aláírt tanúsítványok létrehozásához szükséges lépéseket. Ugyanazt a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt használja, amely az átjáró-eszköz tanúsítványait generálta.

2. Kövesse az x [. 509 hitelesítésszolgáltatói tanúsítványok regisztrálása az IoT hub](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) -ban című szakasz utasításait az *x. 509 szintű biztonság beállítása az Azure IoT hub-ban*. Ebben a szakaszban a következő lépéseket hajtja végre:

   1. Töltse fel a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt. Ha a bemutató tanúsítványait használja, a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ a **\<path> /certs/Azure-IOT-test-only.root.ca.CERT.PEM**.

   2. Győződjön meg arról, hogy a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány tulajdonosa.

3. Az *x. 509 Biztonság beállítása az Azure IoT hub-ban* című rész útmutatását követve [hozzon létre egy x. 509-eszközt az IoT hub létrehozásához](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) . Ebben a szakaszban a következő lépéseket hajtja végre:

   1. Adjon hozzá egy új eszközt. Adja meg az **eszköz azonosítójának** kisbetűs nevét, és válassza ki az **X. 509 hitelesítésszolgáltató által aláírt** hitelesítési típust.

   2. Hozzon létre egy fölérendelt eszközt. Válassza a **szülő eszköz beállítása** elemet, és válassza ki azt a IoT Edge átjáró eszközt, amely a IoT hubhoz való kapcsolódást biztosítja.

4. Hozzon létre egy tanúsítványláncot az alsóbb rétegbeli eszközhöz. Használja ugyanazt a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt, amelyet a IoT Hub feltöltött a lánc elvégzéséhez. Használja ugyanazt a kisbetűs eszköz-azonosítót, amelyet az eszköz identitásához adott a portálon.

5. Másolja az eszköz tanúsítványát és kulcsait az alsóbb rétegbeli eszköz bármely helyére. Helyezze át a megosztott legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány másolatát is, amely az átjáró-eszköz tanúsítványát és az alsóbb rétegbeli eszköz tanúsítványait is létrehozta.

   Ezeket a fájlokat az alsóbb rétegbeli eszközön lévő, IoT Hubhoz csatlakozó alkalmazásokban fogja hivatkozni. Használhat olyan szolgáltatásokat, mint például a [Azure Key Vault](../key-vault/index.yml) vagy a [biztonságos másolási protokollt](https://www.ssh.com/ssh/scp/) használó függvények a tanúsítványfájl áthelyezéséhez.

6. Az előnyben részesített nyelvtől függően tekintse át az X. 509 tanúsítványok IoT-alkalmazásokban való hivatkozásának mintáit:

   * C#: [az X. 509 Biztonság beállítása az Azure IoT hub-ban](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample. c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509. Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

Az [Azure CLI-hez készült IoT-bővítményt](https://github.com/Azure/azure-iot-cli-extension) is használhatja ugyanazon eszköz-létrehozási művelet végrehajtásához. Az alábbi példa az az [IOT hub Device-Identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) paranccsal hoz létre egy új IOT-eszközt X. 509 hitelesítésszolgáltató által aláírt hitelesítéssel, és hozzárendel egy fölérendelt eszközt:

```azurecli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Ezután [kérje le és módosítsa a kapcsolati karakterláncot](#retrieve-and-modify-connection-string) , hogy az eszköz képes legyen csatlakozni az átjárón keresztül.

## <a name="retrieve-and-modify-connection-string"></a>A kapcsolatok karakterláncának lekérése és módosítása

Miután létrehozta a IoT-eszköz identitását a portálon, lekérheti az elsődleges vagy másodlagos kulcsait. Az egyik kulcsnak szerepelnie kell a kapcsolódási karakterláncban, amelyet az alkalmazások a IoT Hubsal való kommunikációra használnak. A szimmetrikus kulcsos hitelesítéshez IoT Hub biztosítja a teljes mértékben formázott kapcsolati karakterláncot az eszköz részleteiben az Ön kényelme érdekében. További információkat kell felvennie az átjáró-eszközről a kapcsolódási karakterláncra.

Az alsóbb rétegbeli eszközökhöz tartozó kapcsolatok karakterláncának a következő összetevőket kell megadnia:

* Az a IoT hub, amelyhez az eszköz csatlakozik: `Hostname={iothub name}.azure-devices.net`
* A központban regisztrált eszköz azonosítója: `DeviceID={device ID}`
* A hitelesítési módszer, hogy a szimmetrikus kulcs vagy az X. 509 tanúsítvány
  * Ha szimmetrikus kulcsos hitelesítést használ, adja meg az elsődleges vagy a másodlagos kulcsot: `SharedAccessKey={key}`
  * Ha X. 509 tanúsítványalapú hitelesítést használ, adjon meg egy jelzőt: `x509=true`
* Az az átjáró-eszköz, amelyhez az eszköz csatlakozik. Adja meg a **hostname** értéket az IoT Edge Gateway-eszköz konfigurációs fájljából: `GatewayHostName={gateway hostname}`

Az összes együtt egy teljes körű kapcsolatok karakterlánca a következőképpen néz ki:

```console
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Vagy

```console
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;x509=true;GatewayHostName=myGatewayDevice
```

A szülő-gyermek kapcsolatnak köszönhetően leegyszerűsítheti a kapcsolati karakterláncot úgy, hogy az átjárót közvetlenül a kapcsolati gazdagépként hívja meg. Például:

```console
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

Ezt a módosított összekapcsolási karakterláncot az átlátszó átjáró sorozat következő cikkében fogja használni.

## <a name="next-steps"></a>Következő lépések

Ezen a ponton van egy IoT Edge eszköz regisztrálva az IoT hub-ban, és transzparens átjáróként van konfigurálva. Emellett egy alsóbb rétegbeli eszköz is regisztrálva van az IoT hub-ban, és az átjáró eszközére mutat.

Ezután az alsóbb rétegbeli eszközt úgy kell konfigurálni, hogy megbízzon az átjáró-eszközön, és biztonságos módon kapcsolódjon. Folytassa az átlátszó átjáró sorozat következő cikkével, [csatlakoztasson egy alsóbb rétegbeli eszközt egy Azure IoT Edge átjáróhoz](how-to-connect-downstream-device.md).
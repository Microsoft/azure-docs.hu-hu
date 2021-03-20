---
title: X. 509 tanúsítvánnyal rendelkező eszközök csatlakoztatása Azure IoT Central-alkalmazásokban
description: X. 509 tanúsítvánnyal rendelkező eszközök csatlakoztatása a IoT Central alkalmazáshoz készült Node.js eszközoldali SDK-val
author: dominicbetts
ms.author: dobett
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: cf0db71600c9350b4d70e6375f509a6e88709f70
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100378332"
---
# <a name="how-to-connect-devices-with-x509-certificates-using-nodejs-device-sdk-for-iot-central-application"></a>X. 509 tanúsítvánnyal rendelkező eszközök csatlakoztatása a IoT Central alkalmazáshoz készült Node.js eszközoldali SDK-val

IoT Central támogatja a közös hozzáférésű aláírások (SAS) és az X. 509 tanúsítványok használatát az eszköz és az alkalmazás közötti kommunikáció biztonságossá tételéhez. Az [ügyfélalkalmazás létrehozása és összekapcsolása az Azure IoT Central Application](./tutorial-connect-device.md) oktatóanyagban sas-t használ. Ebből a cikkből megtudhatja, hogyan módosíthatja a kód mintát az X. 509 használatára.  Az X. 509 tanúsítványokat éles környezetekben ajánlott használni. További információ: [Csatlakozás az Azure IoT Centralhoz](./concepts-get-connected.md).

Ez a cikk két módszert mutat be az X. 509 – [csoportos regisztrációk](how-to-connect-devices-x509.md#use-a-group-enrollment) jellemzően éles környezetben való használatát, valamint a teszteléshez hasznos [Egyéni regisztrációkat](how-to-connect-devices-x509.md#use-an-individual-enrollment) .

A cikkben szereplő kódrészletek JavaScriptet használnak. Más nyelveken a kód mintáit lásd:

- [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_ll_client_x509_sample)
- [C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/X509DeviceCertWithChainSample)
- [Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
- [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples)

## <a name="prerequisites"></a>Előfeltételek

- [Ügyfélalkalmazás létrehozásának és összekapcsolásának befejezése az Azure IoT Central Application (JavaScript)](./tutorial-connect-device.md) oktatóanyagban.
- [Git](https://git-scm.com/download/).
- Töltse le és telepítse az [OpenSSL](https://www.openssl.org/)-t. Ha Windows rendszert használ, a fájlok a [SourceForge-on lévő OpenSSL lapról](https://sourceforge.net/projects/openssl/)is használhatók.

## <a name="use-a-group-enrollment"></a>Csoportos regisztráció használata

Használjon X. 509 tanúsítványokat egy csoportos regisztrációval éles környezetben. A csoportos regisztráció során adjon hozzá egy gyökérszintű vagy köztes X. 509 tanúsítványt a IoT Central alkalmazáshoz. A legfelső szintű vagy köztes tanúsítványból származtatott levél tanúsítvánnyal rendelkező eszközök csatlakozhatnak az alkalmazáshoz.

## <a name="generate-root-and-device-cert"></a>Gyökér-és eszköz-tanúsítvány előállítása

Ebben a szakaszban egy X. 509 tanúsítvány használatával csatlakozik egy olyan eszközhöz, amely a beléptetési csoport tanúsítványa alapján származtatott tanúsítvánnyal rendelkezik, amely csatlakozhat a IoT Central alkalmazáshoz.

> [!WARNING]
> Az X. 509 tanúsítványok létrehozásának így csak tesztelésre van lehetőség. Éles környezetben a tanúsítvány generálásához a hivatalos, biztonságos mechanizmust kell használnia.

1. Nyisson meg egy parancssort. A tanúsítvány-létrehozási parancsfájlok GitHub-tárházának klónozása:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node.git
    ```

1. Navigáljon a Certificate Generator parancsfájlhoz, és telepítse a szükséges csomagokat:

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

1. Hozzon létre egy főtanúsítványt, majd származtatja az eszköz tanúsítványát a szkript futtatásával:

    ```cmd/sh
    node create_test_cert.js root mytestrootcert
    node create_test_cert.js device sample-device-01 mytestrootcert
    ```

    > [!TIP]
    > Az eszköz AZONOSÍTÓi betűket, számokat és karaktert tartalmazhatnak `-` .

Ezek a parancsok három fájlt hoznak létre a gyökérhez és az eszköz tanúsítványához.

fájlnév | tartalmát
-------- | --------
\<name\>_cert. PEM | Az X509-tanúsítvány nyilvános része
\<name\>_key. PEM | Az X509-tanúsítvány titkos kulcsa
\<name\>_fullchain. PEM | Az X509-tanúsítvány teljes kulcstartója.

## <a name="create-a-group-enrollment"></a>Csoportos regisztráció létrehozása

1. Nyissa meg IoT Central alkalmazást, és a bal oldali ablaktáblában navigáljon a **felügyelet**  elemre, és válassza az **eszköz-kapcsolódás** lehetőséget.

1. Válassza a **+ beléptetési csoport létrehozása** lehetőséget, és hozzon létre egy új, _MyX509Group_ nevű regisztrációs csoportot tanúsítványok igazolási típusával **(X. 509)**.

1. Nyissa meg a létrehozott beléptetési csoportot, és válassza az **elsődleges kezelése** lehetőséget.

1. Válassza a fájl lehetőséget, és töltse fel a korábban létrehozott _mytestrootcert_cert. PEM_ nevű főtanúsítvány-fájlt:

    ![Tanúsítvány feltöltése](./media/how-to-connect-devices-x509/certificate-upload.png)

1. Az ellenőrzés befejezéséhez hozza létre az ellenőrző kódot, másolja, majd használja egy X. 509 ellenőrző tanúsítvány létrehozásához a parancssorban:

    ```cmd/sh
    node create_test_cert.js verification --ca mytestrootcert_cert.pem --key mytestrootcert_key.pem --nonce  {verification-code}
    ```

1. Töltse fel az aláírt ellenőrző tanúsítványt _verification_cert. PEM_ az ellenőrzés befejezéséhez:

    ![Ellenőrzött tanúsítvány](./media/how-to-connect-devices-x509/verified.png)

Mostantól az elsődleges főtanúsítványból származtatott X. 509 tanúsítvánnyal rendelkező eszközöket is összekapcsolhat.

A beléptetési csoport mentése után jegyezze fel az azonosító hatókörét.

## <a name="run-sample-device-code"></a>Minta eszköz kódjának futtatása

1. Másolja a **sampleDevice01_key. PEM** és **sampleDevice01_cert. PEM** fájlokat az _Azure-IOT-SDK-Node/Device/Samples/PnP_ mappába, amely tartalmazza a **simple_thermostat.js** alkalmazást. Ezt az alkalmazást az [eszköz csatlakoztatása (JavaScript) oktatóanyag](./tutorial-connect-device.md)befejezése után használta.

1. Navigáljon az _Azure-IOT-SDK-Node/Device/Samples/PnP_ mappára, amely tartalmazza a **simple_thermostat.js** alkalmazást, és futtassa a következő parancsot az X. 509 csomag telepítéséhez:

    ```cmd/sh
    npm install azure-iot-security-x509 --save
    ```

1. Nyissa meg a **simple_thermostat.js** fájlt egy szövegszerkesztőben.

1. Szerkessze az `require` utasításokat, hogy az tartalmazza a következőket:

    ```javascript
    const fs = require('fs');
    const X509Security = require('azure-iot-security-x509').X509Security;
    ```

1. Adja hozzá a következő négy sort a "DPS kapcsolódási adatok" szakaszhoz a változó inicializálásához `deviceCert` :

    ```javascript
    const deviceCert = {
      cert: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_CERT).toString(),
      key: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_KEY).toString()
    };
    ```

1. Szerkessze az `provisionDevice` ügyfelet létrehozó függvényt úgy, hogy lecseréli az első sort a következőre:

    ```javascript
    var provSecurityClient = new X509Security(registrationId, deviceCert);
    ```

1. Ugyanebben a függvényben módosítsa a változót a következőképpen beállító sort `deviceConnectionString` :

    ```javascript
    deviceConnectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';x509=true';
    ```

1. A `main` függvényben adja hozzá a következő sort a meghívása után `Client.fromConnectionString` :

    ```javascript
    client.setOptions(deviceCert);
    ```

1. A rendszerhéj-környezetben állítsa be a következő két környezeti változót:

    ```cmd/sh
    set IOTHUB_DEVICE_X509_CERT=sampleDevice01_cert.pem
    set IOTHUB_DEVICE_X509_KEY=sampleDevice01_key.pem
    ```

    > [!TIP]
    > A többi szükséges környezeti változót akkor kell beállítania, amikor végrehajtotta az [ügyfélalkalmazás létrehozása és összekötése az Azure IoT Central alkalmazáshoz](./tutorial-connect-device.md) oktatóanyagot.

1. Futtassa a szkriptet, és győződjön meg róla, hogy az eszközt sikeresen kiosztották:

    ```cmd/sh
    node simple_thermostat.js
    ```

    Azt is ellenőrizheti, hogy a telemetria megjelenik-e az irányítópulton.

    ![Az eszköz telemetria ellenőrzése](./media/how-to-connect-devices-x509/telemetry.png)

## <a name="use-an-individual-enrollment"></a>Egyéni regisztráció használata

Az eszköz és a megoldás teszteléséhez használjon X. 509 tanúsítványokat egyéni regisztrációval. Egyéni regisztráció esetén nincs root vagy Intermediate X. 509 tanúsítvány a IoT Central alkalmazásban. Az eszközök önaláírt X. 509 tanúsítványt használnak az alkalmazáshoz való kapcsolódáshoz.

## <a name="generate-self-signed-device-cert"></a>Önaláírt eszköz tanúsítványának előállítása

Ebben a szakaszban egy önaláírt X. 509 tanúsítványt használ az eszközök egyéni regisztrációhoz való csatlakoztatásához, amelyek egyetlen eszköz regisztrálására szolgálnak. Az önaláírt tanúsítványok csak tesztelésre szolgálnak.

Hozzon létre egy önaláírt X. 509 eszköz-tanúsítványt a szkript futtatásával. Ügyeljen arra, hogy csak kisbetűs alfanumerikus karaktereket és kötőjeleket használjon a tanúsítvány neveként:

  ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    node create_test_cert.js device mytestselfcertprimary
    node create_test_cert.js device mytestselfcertsecondary 
  ```

## <a name="create-individual-enrollment"></a>Egyéni regisztráció létrehozása

1. Az Azure IoT Central alkalmazásban válassza az **eszközök** lehetőséget, és hozzon létre egy új eszközt az **eszköz azonosítójával** a _mytestselfcertprimary_ a termosztát-eszköz sablonjában. Jegyezze fel az **azonosító hatókörét**, amelyet később használni fog.

1. Nyissa meg a létrehozott eszközt, és válassza a **Csatlakoztatás** lehetőséget.

1. Válassza az **Egyéni regisztrációk** a **csatlakozási módszer** és a **tanúsítványok (X. 509)** lehetőséget a következő mechanizmusként:

    ![Egyéni regisztráció](./media/how-to-connect-devices-x509/individual-device-connect.png)

1. Válassza a fájl lehetőséget az elsődleges területen, és töltse fel a korábban létrehozott _mytestselfcertprimary_cert. PEM_ nevű tanúsítványfájl-fájlt.

1. Válassza a fájl lehetőséget a másodlagos tanúsítványhoz, és töltse fel a _mytestselfcertsecondary_cert. PEM_ nevű tanúsítványfájl-fájlt. Ezután válassza a **Mentés** lehetőséget:

    ![Egyéni beléptetési tanúsítvány feltöltése](./media/how-to-connect-devices-x509/individual-enrollment.png)

Az eszköz most már X. 509 tanúsítvánnyal lett kiépítve.

## <a name="run-a-sample-individual-enrollment-device"></a>Minta egyéni beléptetési eszköz futtatása

1. Másolja a _mytestselfcertprimary_key. PEM_ és _mytestselfcertprimary_cert. PEM_ fájlokat az _Azure-IOT-SDK-Node/Device/Samples/PnP_ mappába, amely tartalmazza a **simple_thermostat.js** alkalmazást. Ezt az alkalmazást az [eszköz csatlakoztatása (JavaScript) oktatóanyag](./tutorial-connect-device.md)befejezése után használta.

1. Módosítsa a fenti mintában használt környezeti változókat a következőképpen:

    ```cmd/sh
    set IOTHUB_DEVICE_DPS_DEVICE_ID=mytestselfcertprimary
    set IOTHUB_DEVICE_X509_CERT=mytestselfcertprimary_cert.pem
    set IOTHUB_DEVICE_X509_KEY=mytestselfcertprimary_key.pem
    ```

1. Futtassa a szkriptet, és győződjön meg róla, hogy az eszközt sikeresen kiosztották:

    ```cmd/sh
    node environmentalSensor.js
    ```

    Azt is ellenőrizheti, hogy a telemetria megjelenik-e az irányítópulton.

    ![Telemetria egyéni regisztráció](./media/how-to-connect-devices-x509/telemetry-primary.png)

A fenti lépéseket a _mytestselfcertsecondary_ -tanúsítványhoz is megismételheti.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan csatlakoztathatók az eszközök az X. 509 tanúsítványokkal, a javasolt következő lépés az [eszköz kapcsolatának figyelése az Azure CLI használatával](howto-monitor-devices-azure-cli.md)

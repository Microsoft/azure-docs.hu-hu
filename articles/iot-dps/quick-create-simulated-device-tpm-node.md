---
title: Gyors útmutató – szimulált TPM-eszköz kiépítése az Azure-IoT Hub Node.js használatával
description: Rövid útmutató – szimulált TPM-eszköz létrehozása és kiépítése az Azure IoT Hub Device Provisioning Service (DPS) Node.js Device SDK-val. Ez a rövid útmutató egyéni regisztrációkat használ.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom:
- mvc
- amqp
- mqtt
- devx-track-js
ms.openlocfilehash: 6e7e986f658570553763001afdd58d7bb1880f94
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94968179"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-nodejs-device-sdk-for-iot-hub-device-provisioning-service"></a>Rövid útmutató: szimulált TPM-eszköz létrehozása és kiépítése a IoT Hub Device Provisioning Service Node.js eszközoldali SDK-val

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Ebben a rövid útmutatóban egy szimulált IoT-eszközt hoz létre egy Windows rendszerű számítógépen. A szimulált eszköz hardveres biztonsági modulként (HSM) tartalmazza a TPM-szimulátort. A szimulált eszköz és az IoT hub között az eszköz kiépítési szolgáltatásával (DPS) való egyéni regisztráció használatával csatlakoztathatja ezt a szimulált eszközt az eszközhöz Node.js kódot használva.

## <a name="prerequisites"></a>Előfeltételek

- Ismerje meg a [kiépítési](about-iot-dps.md#provisioning-process) fogalmakat.
- [A IoT hub Device Provisioning Service beállításának befejezése a Azure Portal](./quick-setup-auto-provision.md).
- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Node.js v 4.0 +](https://nodejs.org).
- [Git](https://git-scm.com/download/).

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>A környezet előkészítése 

1. Győződjön meg arról, hogy a [Node.js 4.0-s vagy újabb verziója](https://nodejs.org) telepítve van a gépén.

1. Győződjön meg arról, hogy a(z) `git` telepítve van a gépen, és a parancsablakból elérhető környezeti változókhoz van adva. A [Software Freedom Conservancy's Git ügyfél eszközeiben](https://git-scm.com/download/) találja a telepíteni kívánt `git` eszközök legújabb verzióját, amely tartalmazza a **Git Bash** eszközt, azt a parancssori alkalmazást, amellyel kommunikálhat a helyi Git-adattárral. 


## <a name="simulate-a-tpm-device"></a>TPM-eszköz szimulálása

1. Nyisson meg egy parancssort vagy a Git Basht. Klónozza a `azure-utpm-c` GitHub-adattárat:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-utpm-c.git --recursive
    ```

1. Navigáljon a GitHub gyökérkönyvtárába, és futtassa a [TPM](/windows/device-security/tpm/trusted-platform-module-overview) -szimulátort a szimulált eszköz [HSM](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) -ként való futtatásához. A 2321-es és a 2322-es portokon lévő szoftvercsatornán keresztül figyel. Ne zárjuk be a parancssorablakot; ezt a szimulátort a rövid útmutató végéig kell megtartania: 

    ```cmd/sh
    .\azure-utpm-c\tools\tpm_simulator\Simulator.exe
    ```

1. Hozzon létre egy új, **registerdevice** nevű üres mappát. A **registerdevice** mappában hozzon létre egy package.json fájlt úgy, hogy beírja a következő parancsot a parancssorba. Válaszolja meg az `npm` által feltett összes kérdést, vagy fogadja el az alapértelmezett értékeket, ha azok megfelelnek Önnek:
   
    ```cmd/sh
    npm init
    ```

1. Telepítse a következő előzetes csomagokat:

    ```cmd/sh
    npm install node-gyp -g
    npm install ffi -g
    ```

    > [!NOTE]
    > A fenti csomagok telepítése során ismert problémák fordulhatnak elő. Ezen problémák elhárításához futtassa az `npm install --global --production windows-build-tools` parancsot a parancssorból **Futtatás rendszergazdaként** módban, majd futtassa a `SET VCTargetsPath=C:\Program Files (x86)\MSBuild\Microsoft.Cpp\v4.0\V140` parancsot, amelyben a telepített verziónak megfelelően cserélje le az útvonalat, végül pedig futtassa újra a fenti telepítési parancsokat.
    >

1. Telepítse a következő csomagokat, amelyek a regisztráció során használt összetevőket tartalmazzák:

   - TPM-mel együttműködő biztonsági ügyfél: `azure-iot-security-tpm`
   - átviteli összetevő, amellyel az eszköz csatlakozhat az eszközkiépítési szolgáltatáshoz: `azure-iot-provisioning-device-http` vagy `azure-iot-provisioning-device-amqp`
   - ügyfél az átvitel és a biztonsági ügyfél használatához: `azure-iot-provisioning-device`

     Miután megtörtént az eszköz regisztrálása, a szokásos IoT Hub eszközügyfél-csomagokkal csatlakoztathatja az eszközt a regisztráció során megadott hitelesítő adatok használatával. A következőkre lesz szükség:

   - az eszközügyfél: `azure-iot-device`
   - átviteli összetevő: `azure-iot-device-amqp`, `azure-iot-device-mqtt` vagy `azure-iot-device-http`
   - a már telepített biztonsági ügyfél: `azure-iot-security-tpm`

     > [!NOTE]
     > Az alábbi minták az `azure-iot-provisioning-device-http` és az `azure-iot-device-mqtt` átviteli összetevőket használják.
     > 

     A következő parancs parancssorbeli futtatásával egyszerre telepítheti ezen csomagok mindegyikét a **registerdevice** mappába:

       ```cmd/sh
       npm install --save azure-iot-device azure-iot-device-mqtt azure-iot-security-tpm azure-iot-provisioning-device-http azure-iot-provisioning-device
       ```

1. Egy szövegszerkesztővel hozzon létre egy új **ExtractDevice.js** fájlt a **registerdevice** mappában.

1. Adja hozzá a következő `require` utasításokat az **ExtractDevice.js** fájl elejéhez:
   
    ```
    'use strict';

    var tpmSecurity = require('azure-iot-security-tpm');
    var tssJs = require("tss.js");

    var myTpm = new tpmSecurity.TpmSecurityClient(undefined, new tssJs.Tpm(true));
    ```

1. Adja hozzá az alábbi függvényt a metódus megvalósításához:
   
    ```
    myTpm.getEndorsementKey(function(err, endorsementKey) {
      if (err) {
        console.log('The error returned from get key is: ' + err);
      } else {
        console.log('the endorsement key is: ' + endorsementKey.toString('base64'));
        myTpm.getRegistrationId((getRegistrationIdError, registrationId) => {
          if (getRegistrationIdError) {
            console.log('The error returned from get registration id is: ' + getRegistrationIdError);
          } else {
            console.log('The Registration Id is: ' + registrationId);
            process.exit();
          }
        });
      }
    });
    ```

1. Mentse és zárja be az **ExtractDevice.js** fájlt. Minta futtatása:

    ```cmd/sh
    node ExtractDevice.js
    ```

1. A kimeneti ablak megjeleníti az eszközök regisztrálásához szükséges **_hátirat kulcsot_** és **_regisztrációs azonosítót_** . Jegyezze fel ezeket az értékeket. 


## <a name="create-a-device-entry"></a>Eszközbejegyzés létrehozása

Az Azure IoT Device Provisioning Service kétféle típusú regisztrációt támogat:

- [Regisztrációs csoportok](concepts-service.md#enrollment-group): Több kapcsolódó eszköz regisztrálásához.
- [Egyéni regisztrációk](concepts-service.md#individual-enrollment): egyetlen eszköz regisztrálására szolgál.

Ez a cikk az egyéni regisztrációkat mutatja be.

1. Jelentkezzen be a Azure Portalba, majd a bal oldali menüben kattintson a **minden erőforrás** gombra, és nyissa meg az eszköz kiépítési szolgáltatását.

1. Az eszközök kiépítési szolgáltatásának menüjében válassza a **regisztrációk kezelése** lehetőséget. Válassza az **Egyéni regisztrációk** fület, és válassza az **Egyéni regisztráció hozzáadása** gombot a felső részen. 

1. A **beléptetés hozzáadása** panelen adja meg a következő adatokat:
   - Válassza a **TPM** elemet az identitás igazolási *Mechanizmusaként*.
   - Adja meg a TPM-eszköz *regisztrációs azonosítóját* és a *jóváhagyás kulcsát* a korábban feljegyzett értékek közül.
   - Válassza ki a kiépítési szolgáltatáshoz kapcsolódó egyik IoT hubot.
   - Ha kívánja, megadhatja az alábbi információkat is:
       - Adjon meg egy egyedi *azonosítót*. Ne használjon bizalmas adatokat az eszköz elnevezésekor. Ha úgy dönt, hogy nem ad meg egyet, a rendszer a regisztrációs azonosítót fogja használni az eszköz azonosítására.
       - Frissítse az **Eszköz kezdeti ikerállapotát** az eszköz kívánt kezdeti konfigurációjával.
   - Ha elkészült, kattintson a **Save (Mentés** ) gombra. 

     ![Írja be az eszköz beléptetési információit a portál panelén](./media/quick-create-simulated-device/enter-device-enrollment.png)  

   Sikeres beléptetés esetén az eszköz *Regisztrációs azonosítója* megjelenik az *Egyéni regisztrációk* lapon lévő listában. 


## <a name="register-the-device"></a>Eszköz regisztrálása

1. A Azure Portal válassza ki az eszköz kiépítési szolgáltatásának **Áttekintés** paneljét, és jegyezze fel a **_globális eszköz végpontját_** és az **_azonosító hatókörének_** értékeit.

    ![Az eszközkiépítési szolgáltatás végpontadatainak kinyerése a portál paneljéről](./media/quick-create-simulated-device/extract-dps-endpoints.png) 

1. Egy szövegszerkesztővel hozzon létre egy új **RegisterDevice.js** fájlt a **registerdevice** mappában.

1. Adja hozzá a következő `require` utasításokat a **RegisterDevice.js** fájl elejéhez:
   
    ```
    'use strict';

    var ProvisioningTransport = require('azure-iot-provisioning-device-http').Http;
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var tpmSecurity = require('azure-iot-security-tpm');
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

    > [!NOTE]
    > Az **Azure IoT SDK for Node.js** támogatja az olyan további protokollokat, mint az _AMQP_, az _AMQP WS_ és az _MQTT WS_.  További példák: [Device Provisioning Service SDK for Node.js-minták](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/device/samples).
    > 

1. Adjon hozzá egy **globalDeviceEndpoint** és egy **idScope** változót, és ezek használatával hozzon létre egy **ProvisioningDeviceClient** példányt. A **{globalDeviceEndpoint}** és az **{idScope}** elemeket cserélje le az **1. lépésből** származó **_Globális eszközvégpont_** és **_Azonosító hatókörére_** elemekre:
   
    ```
    var provisioningHost = '{globalDeviceEndpoint}';
    var idScope = '{idScope}';

    var tssJs = require("tss.js");
    var securityClient = new tpmSecurity.TpmSecurityClient('', new tssJs.Tpm(true));
    // if using non-simulated device, replace the above line with following:
    //var securityClient = new tpmSecurity.TpmSecurityClient();

    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), securityClient);
    ```

1. Adja hozzá az alábbi függvényt a metódus megvalósításához az eszközön:
   
    ```
    provisioningClient.register(function(err, result) {
      if (err) {
        console.log("error registering device: " + err);
      } else {
        console.log('registration succeeded');
        console.log('assigned hub=' + result.registrationState.assignedHub);
        console.log('deviceId=' + result.registrationState.deviceId);
        var tpmAuthenticationProvider = tpmSecurity.TpmAuthenticationProvider.fromTpmSecurityClient(result.registrationState.deviceId, result.registrationState.assignedHub, securityClient);
        var hubClient = Client.fromAuthenticationProvider(tpmAuthenticationProvider, iotHubTransport);

        var connectCallback = function (err) {
          if (err) {
            console.error('Could not connect: ' + err.message);
          } else {
            console.log('Client connected');
            var message = new Message('Hello world');
            hubClient.sendEvent(message, printResultFor('send'));
          }
        };

        hubClient.open(connectCallback);

        function printResultFor(op) {
          return function printResult(err, res) {
            if (err) console.log(op + ' error: ' + err.toString());
            if (res) console.log(op + ' status: ' + res.constructor.name);
            process.exit(1);
          };
        }
      }
    });
    ```

1. Mentse és zárja be a **RegisterDevice.js** fájlt. Minta futtatása:

    ```cmd/sh
    node RegisterDevice.js
    ```

1. Figyelje meg az eszköz rendszerindítását szimuláló és az eszközkiépítési szolgáltatáshoz az IoT Hub információk lekérése érdekében kapcsolódó üzeneteket. Ha sikeresen kiépíti a szimulált eszközt a kiépítési szolgáltatáshoz kapcsolódó IoT hub-ra, az eszköz azonosítója megjelenik a hub **IoT-eszközök** paneljén. 

    ![Az eszköz regisztrálva van az IoT Hubbal](./media/quick-create-simulated-device/hub-registration.png) 

    Ha módosította az *Eszköz kezdeti ikerállapota* alapértelmezett értékét az eszköz beléptetési bejegyzésében, az lekérheti és felhasználhatja a kívánt ikerállapotot a központból. További információ: [az eszközök ikrek megismerése és használata IoT hub](../iot-hub/iot-hub-devguide-device-twins.md)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a munkát, és megkeresi az eszköz ügyféloldali mintáját, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a műveletet, a következő lépésekkel törölheti az ebben a rövid útmutatóban létrehozott összes erőforrást.

1. Zárja be az eszközügyfél minta kimeneti ablakát a gépen.
1. Zárja be a TPM szimulátor ablakát a gépen.
1. A Azure Portal bal oldali menüjében válassza a **minden erőforrás** lehetőséget, majd válassza ki az eszköz kiépítési szolgáltatását. Nyissa meg a szolgáltatás **regisztrációk kezelése** paneljét, majd válassza az **Egyéni regisztrációk** lapot. Jelölje be az ebben a rövid útmutatóban regisztrált eszköz *regisztrációs azonosítójának* melletti jelölőnégyzetet, majd kattintson a panel tetején található **Törlés** gombra. 
1. A Azure Portal bal oldali menüjében válassza a **minden erőforrás** lehetőséget, majd válassza ki az IoT hubot. Nyissa meg a **IoT-eszközök** panelt, jelölje be az ebben a rövid útmutatóban regisztrált eszköz *azonosítója* melletti jelölőnégyzetet, majd kattintson a panel tetején található **Törlés** gombra.


## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy TPM-mel szimulált eszközt a gépen, és kiosztotta azt az IoT hubhoz a IoT Hub Device Provisioning Service használatával. A TPM-eszköz programozott módon történő regisztrálásának megismeréséhez folytassa a TPM-eszköz programozott beléptetését bemutató rövid útmutatóval. 

> [!div class="nextstepaction"]
> [Azure rövid útmutató – TPM-eszköz regisztrálása az Azure-ban IoT Hub Device Provisioning Service](quick-enroll-device-tpm-node.md)
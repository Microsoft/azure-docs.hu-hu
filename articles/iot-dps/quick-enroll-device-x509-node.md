---
title: Rövid útmutató – X. 509 eszközök regisztrálása az Azure Device kiépítési szolgáltatásba Node.js használatával
description: Ez a rövid útmutató csoportos regisztrációkat használ. Ebben a rövid útmutatóban X. 509 eszközöket fog regisztrálni az Azure IoT Hub Device Provisioning Service (DPS) az Node.js Service SDK használatával
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: nodejs
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 0fba755053aa2be371a942698213055c640205fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94959832"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-nodejs"></a>Rövid útmutató: X.509-eszközök regisztrációja a Device Provisioning Service-be a Node.js használatával

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

Ebben a rövid útmutatóban Node.js használatával programozott módon hozhat létre egy köztes vagy legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI X. 509 tanúsítványokat használó regisztrációs csoportot. A regisztrációs csoport létrehozásához a Node.js-hez készült IoT SDK-t és egy Node.js-mintaalkalmazást használunk.

## <a name="prerequisites"></a>Előfeltételek

- A [IoT hub Device Provisioning Service beállításának befejezése a Azure Portal](./quick-setup-auto-provision.md).
- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Node.js v 4.0 +](https://nodejs.org). Ez a rövid útmutató telepíti az alábbi [Node.jsIOT SDK ](https://github.com/Azure/azure-iot-sdk-node) -t.
- [Git](https://git-scm.com/download/).
- [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c).

## <a name="prepare-test-certificates"></a>Teszttanúsítványok előkészítése

A rövid útmutatóhoz szükség van egy .pem vagy .cer fájlra, amely tartalmazza egy köztes vagy legfelső szintű hitelesítésszolgáltatói X.509-tanúsítvány nyilvános részét. A tanúsítványnak a kiépítési szolgáltatásba feltöltöttnek és a szolgáltatás által ellenőrzöttnek kell lennie.

További információ az X.509-tanúsítványon alapuló nyilvánoskulcs-infrastruktúra (PKI) az Azure IoT Hubbal és a Device Provisioning Service-szel való használatáról: [X.509 hitelesítésszolgáltatói tanúsítványok biztonsági áttekintése](../iot-hub/iot-hub-x509ca-overview.md).

Az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) tartalmazza azokat a teszteszközöket, amelyek segítségével létrehozhat egy X.509-tanúsítványláncot, feltölthet egy legfelsőbb szintű vagy köztes tanúsítványt a láncból, valamint végrehajthat egy tulajdonlástanúsítási eljárást a szolgáltatással a tanúsítvány hitelesítéséhez. Az SDK-eszközkészlettel létrehozott tanúsítványokat csak **fejlesztési és tesztelési célokra** tervezték. Ezeket a tanúsítványokat **nem lehet termelési környezetben használni**. Nem módosítható jelszavakat tartalmaznak („1234”), amelyek 30 nap után lejárnak. A termelési használathoz megfelelő tanúsítványok beszerzésével kapcsolatos további információt az Azure IoT Hub dokumentációjának [X.509 hitelesítésszolgáltatói tanúsítvány beszerzése](../iot-hub/iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate) című részében talál.

A teszteszköz segítségével a következő lépésekkel állíthat elő tanúsítványokat:
 
1. Keresse meg az Azure IoT C SDK [legújabb kiadásához](https://github.com/Azure/azure-iot-sdk-c/releases/latest) tartozó címke nevét.

2. Nyisson meg egy parancssort vagy a Git Bash-felületet, és lépjen egy, a gépen található munkamappába. Futtassa az alábbi parancsokat az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-tárház legújabb kiadásának klónozásához. Használja az előző lépésben megtalált címkét a paraméter értékeként `-b` :

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Ez a művelet várhatóan több percig is eltarthat.

   A teszteszköz a klónozott adattár *azure-iot-sdk-c/tools/CACertificates* mappájában található.

3. Kövesse a [mintákhoz és oktatóanyagokhoz készült hitelesítésszolgáltatói tanúsítványok kezeléséről](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) szóló cikk lépéseit. 



## <a name="create-the-enrollment-group-sample"></a>A regisztrációs csoport mintájának létrehozása 

Az Azure IoT Device Provisioning Service kétféle típusú regisztrációt támogat:

- [Regisztrációs csoportok](concepts-service.md#enrollment-group): Több kapcsolódó eszköz regisztrálásához.
- [Egyéni regisztrációk](concepts-service.md#individual-enrollment): egyetlen eszköz regisztrálására szolgál.

Egy regisztrációs csoport a tanúsítványláncukban ugyanazon aláíró tanúsítvánnyal rendelkező eszközök kiépítési szolgáltatáshoz való hozzáférését szabályozza. További tudnivalókért lásd: [Eszközök kiépítési szolgáltatáshoz való hozzáférésének szabályozása X.509-tanúsítványokkal](./concepts-x509-attestation.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).
 
1. A munkamappában egy parancsablakból futtassa a következőt:
  
     ```cmd\sh
     npm install azure-iot-provisioning-service
     ```  

2. Egy szövegszerkesztővel hozzon létre egy **create_enrollment_group.js** fájlt a munkamappában. Adja hozzá a következő kódot a fájlhoz, és mentse:

    ```
    'use strict';
    var fs = require('fs');

    var provisioningServiceClient = require('azure-iot-provisioning-service').ProvisioningServiceClient;

    var serviceClient = provisioningServiceClient.fromConnectionString(process.argv[2]);

    var enrollment = {
      enrollmentGroupId: 'first',
      attestation: {
        type: 'x509',
        x509: {
          signingCertificates: {
            primary: {
              certificate: fs.readFileSync(process.argv[3], 'utf-8').toString()
            }
          }
        }
      },
      provisioningStatus: 'disabled'
    };

    serviceClient.createOrUpdateEnrollmentGroup(enrollment, function(err, enrollmentResponse) {
      if (err) {
        console.log('error creating the group enrollment: ' + err);
      } else {
        console.log("enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
        enrollmentResponse.provisioningStatus = 'enabled';
        serviceClient.createOrUpdateEnrollmentGroup(enrollmentResponse, function(err, enrollmentResponse) {
          if (err) {
            console.log('error updating the group enrollment: ' + err);
          } else {
            console.log("updated enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
          }
        });
      }
    });
    ```

## <a name="run-the-enrollment-group-sample"></a>A regisztrációs mintacsoport futtatása
 
1. A minta futtatásához szüksége lesz a kiépítési szolgáltatás kapcsolati sztringjére. 
    1. Jelentkezzen be a Azure Portalba, majd a bal oldali menüben kattintson a **minden erőforrás** gombra, és nyissa meg az eszköz kiépítési szolgáltatását. 
    2. Kattintson a **megosztott hozzáférési házirendek** elemre, majd válassza ki a tulajdonságok megnyitásához használni kívánt hozzáférési szabályzatot. A **Hozzáférési szabályzat** ablakban másolja és jegyezze fel az elsődleges kulcs kapcsolati sztringjét. 

       ![A kiépítési szolgáltatás kapcsolati sztringjének lekérése a portálról](./media/quick-enroll-device-x509-node/get-service-connection-string.png) 


3. Amint azt a [Teszttanúsítványok előkészítése](quick-enroll-device-x509-node.md#prepare-test-certificates) részben már említettük, egy .pem fájlra is szüksége lesz, amely egy köztes vagy legfelső szintű hitelesítésszolgáltatói X.509-tanúsítványt tartalmaz, amely már fel lett töltve és hitelesítve lett az eszközkiépítési szolgáltatás által. A tanúsítvány feltöltésének és ellenőrzésének ellenőrzéséhez a Azure Portal eszköz kiépítési szolgáltatásának összefoglalás lapján válassza a **tanúsítványok** lehetőséget. Keresse meg a csoportos regisztrációhoz használni kívánt tanúsítványt, és győződjön meg arról, hogy az állapotának értéke *Hitelesítve*.

    ![Hitelesített tanúsítvány a portálon](./media/quick-enroll-device-x509-node/verify-certificate.png) 

1. A tanúsítványhoz tartozó [regisztrációs csoport](concepts-service.md#enrollment-group) létrehozásához futtassa a következő parancsot (a parancs argumentumai körüli idézőjeleket is beleértve):
 
     ```cmd\sh
     node create_enrollment_group.js "<the connection string for your provisioning service>" "<your certificate's .pem file>"
     ```
 
3. Sikeres létrehozás esetén a parancsablakban az új regisztrációs csoport tulajdonságai jelennek meg.

    ![A regisztráció tulajdonságai a parancskimenetben](./media/quick-enroll-device-x509-node/sample-output.png) 

4. Ellenőrizze, hogy a regisztrációs csoport létrejött-e. Az Azure Portal Device Provisioning Service összefoglalási panelén válassza a **Regisztrációk kezelése** lehetőséget. Válassza a **Regisztrációs csoportok** lapot, és ellenőrizze, hogy az új regisztrációs bejegyzés (*első*) megjelenik-e.

    ![A regisztráció tulajdonságai a portálon](./media/quick-enroll-device-x509-node/verify-enrollment-portal.png) 
 
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha azt tervezi, hogy vizsgálja meg a Node.js szolgáltatás mintáit, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a műveletet, a következő lépésekkel törölheti az ebben a rövid útmutatóban létrehozott összes Azure-erőforrást.
 
1. Zárja be a Node.js-minta kimeneti ablakát a gépen.
2. Navigáljon a Azure Portal eszköz kiépítési szolgáltatásához, válassza a **regisztrációk kezelése** lehetőséget, majd válassza a **regisztrációs csoportok** lapot. Jelölje be az ebben a rövid útmutatóban regisztrált X. 509 eszközök *csoport neve* melletti jelölőnégyzetet, majd kattintson a panel tetején található **Törlés** gombra.    
3. A Azure Portal eszköz kiépítési szolgáltatásában válassza ki a **tanúsítványok** lehetőséget, válassza ki a rövid útmutatóhoz feltöltött tanúsítványt, majd kattintson a **tanúsítvány részletei** ablak tetején található **Törlés** gombra.  
 
## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy csoportos regisztrációt hozott létre egy X. 509 közbenső vagy legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványhoz az Azure IoT Hub Device Provisioning Service használatával. Ha mélyebben szeretné megismerni az eszközkiépítést, folytassa az Azure Portalon az eszközkiépítési szolgáltatás beállításának oktatóanyagával. 

Lásd még: [Node.js Device kiépítési minta](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/device/samples).
 
> [!div class="nextstepaction"]
> [Azure IoT Hub eszközkiépítési szolgáltatás oktatóanyagai](./tutorial-set-up-cloud.md)
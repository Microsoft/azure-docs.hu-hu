---
title: Biztonsági IoT Edge modul üzembe helyezése
description: Útmutató a Defender for IoT biztonsági ügynök üzembe helyezéséhez a IoT Edge.
ms.topic: conceptual
ms.date: 04/21/2021
ms.openlocfilehash: 71efb0bb12d1e20f918481a086fd411d3a237e33
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813595"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Biztonsági modul üzembe helyezése IoT Edge eszközön

**A Defender for IoT-modul** átfogó biztonsági megoldást biztosít a IoT Edge számára.
A biztonsági modul az operációs rendszerből és a tárolórendszerből származó nyers biztonsági adatokat gyűjti, összesíti és elemzi a beavatkozást lehetővé tő biztonsági javaslatok és riasztások alapján.
További információ: [Biztonsági modul a IoT Edge.](security-edge-architecture.md)

Ebből a cikkből megtudhatja, hogyan helyezhet üzembe egy biztonsági modult a IoT Edge eszközén.

## <a name="deploy-security-module"></a>Biztonsági modul üzembe helyezése

A következő lépésekkel helyezhet üzembe egy Defender for IoT biztonsági modult a IoT Edge.

### <a name="prerequisites"></a>Előfeltételek

1. Győződjön meg IoT Hub, hogy az eszköz regisztrálva van IoT Edge [eszközként.](../iot-edge/how-to-register-device.md#register-a-new-device)

1. A Defender IoT Edge modulhoz az [auditd](https://linux.die.net/man/8/auditd) keretrendszernek telepítve kell lenni az IoT Edge eszközön.

    - Telepítse a keretrendszert a következő parancs futtatásával a IoT Edge eszközön:

    `sudo apt-get install auditd audispd-plugins`

    - A következő parancs futtatásával ellenőrizze, hogy az AuditD aktív-e:

    `sudo systemctl status auditd`<br>
    - A várt válasz a következő: `active (running)`

### <a name="deployment-using-azure-portal"></a>Üzembe helyezés Azure Portal

1. A Azure Portal nyissa meg a **Marketplace-t.**

1. Válassza **eszközök internetes hálózata** lehetőséget, majd keressen rá **Azure Security Center az IoT** kifejezésre, és válassza ki.

   :::image type="content" source="media/howto/edge-onboarding-8.png" alt-text="A Defender kiválasztása az IoT-hez":::

1. Az üzembe **helyezés konfiguráláshoz** válassza a Létrehozás lehetőséget.

1. Válassza ki a saját **előfizetéséhez** IoT Hub Azure-előfizetést, majd válassza ki **IoT Hub.**<br>Válassza a Deploy to a device (Üzembe helyezés **eszközre)** lehetőséget egyetlen eszköz megcélzához, vagy válassza a **Deploy at Scale to target** multiple devices (Üzembe helyezés skálázva több eszközre) lehetőséget, majd válassza a Create (Létrehozás) **lehetőséget.** További információ a nagy léptékű üzembe helyezésről: [How to deploy](../iot-edge/how-to-deploy-at-scale.md).

    >[!Note]
    >Ha az Üzembe **helyezés skálázva** beállítást választotta,  adja meg az eszköz nevét és adatait, mielőtt továbblép a Modulok hozzáadása lapra a következő utasításokban.

Az egyes lépéseket a Defender for IoT IoT Edge üzembe helyezésének befejezéséhez kell végrehajtania.

#### <a name="step-1-modules"></a>1. lépés: Modulok

1. Válassza ki az **AzureSecurityCenterforIoT** modult.
1. A Modul **beállításai lapon**  módosítsa a nevet **az azureiotsecurity névre.**
1. A Környezeti **változók** lapon szükség esetén adjon hozzá egy  változót (például hozzáadhat hibakeresési szintet, és beállíthatja a következő értékek egyikére: "Végzetes", "Hiba", "Figyelmeztetés" vagy "Információ").
1. A **Tároló-létrehozási beállítások lapon** adja hozzá a következő konfigurációt:

    ``` json
    {
        "NetworkingConfig": {
            "EndpointsConfig": {
                "host": {}
            }
        },
        "HostConfig": {
            "Privileged": true,
            "NetworkMode": "host",
            "PidMode": "host",
            "Binds": [
                "/:/host"
            ]
        }
    }
    ```

1. A **Modulikon beállításai lapon** adja hozzá a következő konfigurációt:

   Ikermodul-tulajdonság:
   
   ``` json
     "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration"
   ```

   Ikermodul-tulajdonság tartalma: 

   ```json
     {

     }
   ```
    
   Az ügynök konfigurálásával kapcsolatos további információkért lásd: Configure security agents (Biztonsági [ügynökök konfigurálása).](./how-to-agent-configuration.md)

1. Válassza a **Frissítés** lehetőséget.

#### <a name="step-2-runtime-settings"></a>2. lépés: Futásidejű beállítások

1. Válassza **a Futásidejű beállítások lehetőséget.**
2. Az **Edge Hub alatt** módosítsa a **rendszerképet a** **következőre: mcr.microsoft.com/azureiotedge-hub:1.0.8.3.**

    >[!Note]
    > Jelenleg az 1.0.8.3-as vagy régebbi verzió támogatott.

3. Ellenőrizze, **hogy a Létrehozási** beállítások beállítás a következő konfigurációra van-e állítva:

    ``` json
    {
       "HostConfig":{
          "PortBindings":{
             "8883/tcp":[
                {
                   "HostPort":"8883"
                }
             ],
             "443/tcp":[
                {
                   "HostPort":"443"
                }
             ],
             "5671/tcp":[
                {
                   "HostPort":"5671"
                }
             ]
          }
       }
    }
    ```

4. Kattintson a **Mentés** gombra.

5. Kattintson a **Tovább** gombra.

#### <a name="step-3-specify-routes"></a>3. lépés: Útvonalak megadása

1. Az **Útvonalak megadása** lapon győződjön meg arról, hogy van egy (explicit vagy implicit)  útvonala, amely az **azureiotsecurity** modul üzeneteit az $upstream szerint továbbítja. Csak akkor válassza a Tovább lehetőséget, ha az útvonal a **helyén van.**

   Példaútvonalak:

    ```Default implicit route
    "route": "FROM /messages/* INTO $upstream"
    ```

    ```Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ```

1. Kattintson a **Tovább** gombra.

#### <a name="step-4-review-deployment"></a>4. lépés: Az üzembe helyezés áttekintése

- Az Üzembe **helyezés áttekintése lapon** tekintse át az üzembe helyezési adatokat, majd válassza a **Létrehozás** lehetőséget az üzembe helyezés befejezéséhez.

## <a name="diagnostic-steps"></a>Diagnosztikai lépések

Ha problémába ütközik, a tárolónaplók a legjobb módszer arra, hogy megismerj egy IoT Edge modulos eszköz állapotát. Az ebben a szakaszban szereplő parancsokkal és eszközökkel további információt gyűjthet.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Ellenőrizze, hogy a szükséges tárolók telepítve vannak-e, és a várt módon működnek-e

1. Futtassa a következő parancsot a IoT Edge eszközön:

    `sudo docker ps`

1. Ellenőrizze, hogy futnak-e a következő tárolók:

   | Name | KÉP |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.2 |
   | edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | edgeAgent (edgeAgent) | mcr.microsoft.com/azureiotedge-agent:1.0.1 |

   Ha a minimálisan szükséges tárolók nincsenek jelen, ellenőrizze, hogy az üzembehely IoT Edge jegyzékfájlja összhangban van-e az ajánlott beállításokkal. További információ: [Deploy IoT Edge module](#deployment-using-azure-portal).

### <a name="inspect-the-module-logs-for-errors"></a>Hibák vizsgálata a modulnaplókban

1. Futtassa a következő parancsot a IoT Edge eszközön:

   `sudo docker logs azureiotsecurity`

1. Részletes naplókért adja hozzá a következő környezeti változót az **azureiotsecurity modul** üzemelő példányhoz: `logLevel=Debug` .

## <a name="next-steps"></a>Következő lépések

A konfigurációs beállításokkal kapcsolatos további információkért folytassa a modulkonfiguráció útmutatójában.
> [!div class="nextstepaction"]
> [Modulkonfiguráció – útmutató](./how-to-agent-configuration.md)
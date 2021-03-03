---
title: IoT Edge biztonsági modul üzembe helyezése
description: Ismerje meg, hogyan helyezhet üzembe egy Defendert a IoT biztonsági ügynökön IoT Edgeon.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: cd79c928afdb3563b47374869cff577f9221d360
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101705772"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Biztonsági modul üzembe helyezése a IoT Edge eszközön

A **Defender for IoT** modul átfogó biztonsági megoldást nyújt a IoT Edge-eszközökhöz.
A biztonsági modul az operációs rendszer és a tárolórendszer nyers biztonsági adatainak gyűjtését, összesítését és elemzését végezheti el a gyakorlatban használható biztonsági javaslatokkal és riasztásokkal.
További információ: [IoT Edge biztonsági modulja](security-edge-architecture.md).

Ebből a cikkből megtudhatja, hogyan helyezhet üzembe egy biztonsági modult a IoT Edge eszközön.

## <a name="deploy-security-module"></a>Biztonsági modul telepítése

A következő lépésekkel telepítheti a Defendert a IoT biztonsági moduljának IoT Edge.

### <a name="prerequisites"></a>Előfeltételek

1. A IoT Hub ellenőrizze, hogy az eszköz IoT Edge- [eszközként](../iot-edge/how-to-register-device.md#register-a-new-device)van-e regisztrálva.

1. A IoT Edge modulhoz tartozó Defender számára szükséges, hogy a [naplózott keretrendszer](https://linux.die.net/man/8/auditd) telepítve legyen a IoT Edge eszközön.

    - A keretrendszer telepítéséhez futtassa a következő parancsot a IoT Edge eszközön:

    `sudo apt-get install auditd audispd-plugins`

    - A naplózott ellenőrzés aktív a következő parancs futtatásával:

    `sudo systemctl status auditd`<br>
    - A várt válasz: `active (running)`

### <a name="deployment-using-azure-portal"></a>Üzembe helyezés Azure Portal használatával

1. A Azure Portal nyissa meg a **Marketplace**-t.

1. Válassza a **eszközök internetes hálózata** lehetőséget, majd keresse meg a **IoT Azure Security Center** , és válassza ki.

   :::image type="content" source="media/howto/edge-onboarding-8.png" alt-text="IoT-védő kiválasztása":::

1. A telepítés konfigurálásához válassza a **Létrehozás** lehetőséget.

1. Válassza ki a IoT Hub Azure- **előfizetését** , majd válassza ki a **IoT hub**.<br>Válassza a **telepítés egy eszközre** lehetőséget egyetlen eszköz kiválasztásához, vagy válassza a nagy **méretű üzembe helyezés** lehetőséget a több eszköz megcélzásához, majd válassza a **Létrehozás** lehetőséget. További információ a nagy léptékű üzembe helyezésről: a [központi](../iot-edge/how-to-deploy-at-scale.md)telepítés.

    >[!Note]
    >Ha a **központi telepítés méretezése** lehetőséget választotta, adja hozzá az eszköz nevét és részleteit, mielőtt továbblép a **modulok hozzáadása** lapra az alábbi utasításokban.

Hajtsa végre az egyes lépéseket, hogy elvégezze a IoT IoT Edge-telepítésének befejezését.

#### <a name="step-1-modules"></a>1. lépés: modulok

1. Válassza ki a **AzureSecurityCenterforIoT** modult.
1. A **modul beállításai** lapon módosítsa a **nevet** a **azureiotsecurity** értékre.
1. A **környezeti változók** lapon szükség esetén vegyen fel egy változót (például a *hibakeresési szintet* , majd állítsa be a következő értékek egyikére: "végzetes", "hiba", "figyelmeztetés" vagy "információ").
1. A **tároló létrehozása beállítások** lapon adja hozzá a következő konfigurációt:

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

1. A **modul Twin beállítások** lapján adja hozzá a következő konfigurációt:

   Modul Twin tulajdonsága:
   
   ``` json
     "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration"
   ```

   Modul Twin tulajdonságának tartalma: 

   ```json
     {

     }
   ```
    
   Az ügynök konfigurálásával kapcsolatos további információkért lásd: [biztonsági ügynökök konfigurálása](./how-to-agent-configuration.md).

1. Válassza a **Frissítés** lehetőséget.

#### <a name="step-2-runtime-settings"></a>2. lépés: futtatókörnyezet beállításai

1. Válassza a **futtatókörnyezet beállításait**.
2. Az **Edge hub** alatt módosítsa a **képet** **MCR.microsoft.com/azureiotedge-hub:1.0.8.3**.

    >[!Note]
    > Jelenleg a 1.0.8.3 vagy régebbi verziója támogatott.

3. A **létrehozási beállítások** ellenőrzése a következő konfigurációra van beállítva:

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

#### <a name="step-3-specify-routes"></a>3. lépés: útvonalak meghatározása

1. Az **útvonalak meghatározása** lapon győződjön meg arról, hogy van olyan útvonala (explicit vagy implicit), amely továbbítja az üzeneteket a **azureiotsecurity** modulból az alábbi példáknak megfelelően **$upstream** . Csak ha az útvonal van érvényben, válassza a **tovább** lehetőséget.

   Példa útvonalak:

    ```Default implicit route
    "route": "FROM /messages/* INTO $upstream"
    ```

    ```Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ```

1. Kattintson a **Tovább** gombra.

#### <a name="step-4-review-deployment"></a>4. lépés: az üzembe helyezés áttekintése

- A központi telepítés **áttekintése** lapon tekintse át a központi telepítési adatokat, majd válassza a **Létrehozás** lehetőséget a telepítés befejezéséhez.

## <a name="diagnostic-steps"></a>Diagnosztikai lépések

Ha problémába ütközik, a tároló naplói a legjobb módszer a IoT Edge biztonsági modul eszközének állapotának megismerésére. Az ebben a szakaszban szereplő parancsokkal és eszközökkel további információt gyűjthet.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Ellenőrizze, hogy a szükséges tárolók telepítve vannak-e, és megfelelően működnek-e.

1. Futtassa a következő parancsot a IoT Edge eszközön:

    `sudo docker ps`

1. Ellenőrizze, hogy a következő tárolók futnak-e:

   | Name | KÉP |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.2 |
   | edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0.1 |

   Ha a minimálisan szükséges tárolók nem jelennek meg, ellenőrizze, hogy a IoT Edge központi telepítési jegyzékfájlja az ajánlott beállításokkal van-e igazítva. További információ: [IoT Edge modul üzembe helyezése](#deployment-using-azure-portal).

### <a name="inspect-the-module-logs-for-errors"></a>A modul naplófájljainak vizsgálata hibák esetén

1. Futtassa a következő parancsot a IoT Edge eszközön:

   `sudo docker logs azureiotsecurity`

1. A részletes naplókhoz adja hozzá a következő környezeti változót a **azureiotsecurity** modul telepítéséhez: `logLevel=Debug` .

## <a name="next-steps"></a>Következő lépések

A konfigurációs beállításokkal kapcsolatos további információkért folytassa a modul konfigurálásának útmutatójában.
> [!div class="nextstepaction"]
> [Útmutató a modul konfigurálásához](./how-to-agent-configuration.md)
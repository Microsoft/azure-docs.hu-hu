---
title: Az eszköz frissítésének kiépítés az Azure IoT Hub-ügynökhöz | Microsoft Docs
description: Eszköz frissítésének kiépítés az Azure IoT Hub Agenthez
author: ValOlson
ms.author: valls
ms.date: 2/16/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: e778c7ee14d2115bf6d8cf7f12ceaa61e364a4a2
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106120178"
---
# <a name="device-update-agent-provisioning"></a>Eszköz frissítési ügynökének kiépítés

Az eszköz frissítési moduljának ügynöke más rendszerfolyamatok és [IoT Edge olyan modulok](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules) mellett is futtatható, amelyek ugyanazon logikai eszköz részeként csatlakoznak a IoT hubhoz. Ez a szakasz azt ismerteti, hogyan lehet az eszköz frissítési ügynökét modul-identitásként kiépíteni. 


## <a name="module-identity-vs-device-identity"></a>Modul identitása vs eszköz identitása

IoT Hub minden eszköz identitása alatt létrehozhat akár 50 modul-identitást is. Minden modul identitása implicit módon létrehoz egy külön modult. Az eszköz oldalon a IoT Hub eszköz SDK-k lehetővé teszik, hogy olyan modulokat hozzon létre, amelyekben mindegyik egy független kapcsolódást nyit meg a IoT Hubhoz. A modul identitása és a modul Twin a hasonló képességeket biztosítja, mint az eszköz identitása és az eszközök Twin, de finomabb részletességgel. [További információ a modulbeli identitásokról IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins)


## <a name="support-for-device-update"></a>Az eszköz frissítésének támogatása

A következő IoT-típusú eszközök jelenleg támogatottak az eszköz frissítésével:

* Linuxos eszközök (IoT Edge és nem IoT Edge eszközök):
    * A/B rendszerkép frissítése:
        - Yocto-ARM64 (hivatkozási kép), nyílt forráskód használatával bővíthető, és igény szerint [saját lemezképeket hozhat létre](device-update-agent-provisioning.md#how-to-build-and-run-device-update-agent) más architektúrához.
        - Ubuntu 18,04 szimulátor
       
    * A csomag ügynök által támogatott buildek a következő platformokhoz/architektúrához:
        - Ubuntu Server 18,04 x64 csomag ügynök 
        - Debian 9 
        
* Korlátozott eszközök:
    * AzureRTOS-eszköz frissítési ügynökének mintái: [azure IoT hub-oktatóanyag az Azure-hoz – valós idejű operációs rendszer](device-update-azure-real-time-operating-system.md)

* Leválasztott eszközök: 
    * [A leválasztott eszköz frissítésének támogatása](connected-cache-disconnected-device-update.md)


## <a name="prerequisites"></a>Előfeltételek  

Ha a IoT eszköz/IoT Edge eszközt [csomag alapú frissítésekhez](https://docs.microsoft.com/azure/iot-hub-device-update/understand-device-update#support-for-a-wide-range-of-update-artifacts)állítja be, a következő lépésekkel adja hozzá a packages.microsoft.com a gép adattárházához:

1. Jelentkezzen be arra a gépre vagy IoT-eszközre, amelyre telepíteni kívánja az eszköz frissítési ügynökét.

1. Nyisson meg egy terminál ablakot.

1. Telepítse az eszköz operációs rendszeréhez illő adattár-konfigurációt.
    ```shell
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
    ```
    
1. Másolja a generált listát a sources. list. d könyvtárba.
    ```shell
    sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
    ```
    
1. Telepítse a Microsoft GPG nyilvános kulcsot.
    ```shell
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    ```

    ```shell
    sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
    ```

## <a name="how-to-provision-the-device-update-agent-as-a-module-identity"></a>Az eszköz frissítési ügynökének kiépítése modul-identitásként

Ez a szakasz azt ismerteti, hogyan lehet az eszköz frissítési ügynökét modul-identitásként kiépíteni IoT Edge engedélyezett eszközökön, a nem peremhálózati IoT-eszközökön és más IoT-eszközökön.


### <a name="on-iot-edge-enabled-devices"></a>IoT Edge engedélyezett eszközökön

Az alábbi utasításokat követve kiépítheti az eszköz frissítési ügynökét [IoT Edge engedélyezett eszközökön](https://docs.microsoft.com/azure/iot-edge).

1. Az [Azure IoT Edge futtatókörnyezet telepítéséhez és üzembe](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge?view=iotedge-2020-11&preserve-view=true)helyezéséhez kövesse az utasításokat.

1. Ezután telepítse az eszköz frissítési ügynökét [az összetevőkből, és most](https://github.com/Azure/iot-hub-device-update/releases) már készen áll az eszköz frissítési ügynökének elindítására a IoT Edge eszközön.


### <a name="on-non-edge-iot-linux-devices"></a>Nem peremhálózati IoT Linux-eszközökön

Ezeket az utasításokat követve kiépítheti az eszköz frissítési ügynökét a IoT Linux-eszközökön.

1. Telepítse a IoT Identity Service-t, és adja hozzá a legújabb verziót a IoT-eszközhöz. 
    1. Jelentkezzen be a gépre vagy a IoT eszközre.
    1. Nyisson meg egy terminálablakot.
    1.  Telepítse a legújabb [IoT Identity Service](https://github.com/Azure/iot-identity-service/blob/main/docs/packaging.md#installing-and-configuring-the-package) -t a IoT-eszközön a következő paranccsal:
    
        ```shell
        sudo apt-get install aziot-identity-service
        ```
        
1. A IoT Identity Service kiépítés a IoT-eszköz adatainak beszerzéséhez.
    * Hozzon létre egy egyéni másolatot a konfigurációs sablonról, hogy hozzá lehessen adni a kiépítési adatokat. Egy terminálon írja be az alábbi parancsot.
      
        ```shell
        sudo cp /etc/aziot/config.toml.template /etc/aziot/config.toml 
        ```
   
1. Ezután szerkessze a konfigurációs fájlt, és adja meg annak az eszköznek a kapcsolódási karakterláncát, amelyet az eszköz vagy a gép számára kiosztottként kíván használni. Egy terminálon írja be az alábbi parancsot.

    ```shell
    sudo nano /etc/aziot/config.toml
    ```
   
1. A következő példához hasonló üzenetnek kell megjelennie:

    :::image type="content" source="media/understand-device-update/config.png" alt-text="A IoT Identity Service konfigurációs fájljának ábrája." lightbox="media/understand-device-update/config.png":::

    1. Ugyanebben a nano-ablakban keresse meg a "manuális kiépítés a kapcsolatok karakterláncával" blokkot.
    1. Az ablakban törölje a "#" szimbólumot a "kiépítés" előtt.
    1. A "forrás" előtt törölje a "#" szimbólumot az ablakban. 
    1. Az ablakban a "connection_string" előtt törölje a "#" szimbólumot.
    1. Az ablakban törölje a karakterláncot az idézőjelek között a "connection_string" jobb oldalán, majd adja hozzá a kapcsolódási karakterláncot 
    1. Mentse a fájl módosításait a "CTRL + X", majd az "Y" értékre, és nyomja meg a "Enter" billentyűt a módosítások mentéséhez. 
    
1.  Most alkalmazza, majd indítsa újra a IoT Identity Service-t az alábbi paranccsal. Ekkor a "kész!" üzenet jelenik meg a nyomtatás azt jelenti, hogy sikeresen konfigurálta a IoT-identitás szolgáltatást.

    > [!Note]
    > A IoT Identity szolgáltatás jelenleg a szimmetrikus kulcsok használatával regisztrálja a modul identitásait IoT Hub.
    
    ```shell
    sudo aziotctl config apply
    ```
    
1.  Végül telepítse az eszköz frissítési ügynökét [az összetevőkből, és most](https://github.com/Azure/iot-hub-device-update/releases) már készen áll az eszköz frissítési ügynökének elindítására a IoT Edge eszközön.


### <a name="other-iot-devices"></a>Egyéb IoT-eszközök

Az eszköz frissítési ügynöke a IoT Identity Service nélkül is konfigurálható teszteléshez vagy korlátozott eszközökhöz. A következő lépések végrehajtásával kiépítheti az eszköz frissítési ügynökét egy kapcsolódási karakterlánc használatával (a modulból vagy eszközből).

1.  Telepítse [az eszköz](https://github.com/Azure/iot-hub-device-update/releases)frissítési ügynökét az összetevőkből.

1.  Jelentkezzen be a gépre, vagy IoT Edge eszköz-vagy IoT-eszközre.
    
1.  Nyisson meg egy terminálablakot.

1.  Adja hozzá a kapcsolódási karakterláncot az [eszköz frissítési konfigurációs fájljához](device-update-configuration-file.md):
    1. Adja meg az alábbi parancsot a terminál ablakban:
        - [Csomagok frissítéseinek](device-update-ubuntu-agent.md) használata: sudo nano/etc/adu/adu-conf.txt
        - [Rendszerkép-frissítések](device-update-raspberry-pi.md) használata: sudo nano/adu/adu-conf.txt
       
    1. Ekkor meg kell jelennie egy, a szövegben megnyíló ablaknak. Törölje a "connection_String =" következő teljes karakterláncot, amikor először kiépíti az eszköz frissítési ügynökét a IoT-eszközön. Csak a tulajdonos szövegét helyezi el.
    
    1. A terminálban cserélje le a <a-kapcsolatok-string> az eszközhöz tartozó, az eszköz frissítési ügynökéhez tartozó kapcsolatok karakterláncára.
    
        > [!Important]
        > Ne adjon hozzá idézőjeleket a kapcsolatok karakterlánca körül.
        
        - connection_string =<a-kapcsolatok-karakterláncot>
       
    1. Adja meg és mentse a értéket.
    
1.  Most már készen áll az eszköz frissítési ügynökének elindítására a IoT Edge eszközön. 


## <a name="how-to-start-the-device-update-agent"></a>Az eszköz frissítési ügynökének elindítása

Ez a szakasz azt ismerteti, hogyan indítható el és ellenőrizhető az eszköz frissítési ügynöke a IoT-eszközön sikeresen futó modul-identitásként.

1.  Jelentkezzen be arra a gépre vagy eszközre, amelyen telepítve van az eszköz frissítési ügynöke.

1.  Nyisson meg egy terminál ablakot, és írja be az alábbi parancsot.
    ```shell
    sudo systemctl restart adu-agent
    ```
    
1.  Az ügynök állapotát az alábbi parancs használatával tekintheti meg. Ha problémákat tapasztal, tekintse meg ezt a [hibaelhárítási útmutatót](troubleshoot-device-update.md).
    ```shell
    sudo systemctl status adu-agent
    ```
    
    Meg kell jelennie az állapotnak.

1.  A IoT Hub-portálon lépjen a IoT eszköz vagy IoT Edge eszközök elemre, és keresse meg az eszköz frissítési ügynökével konfigurált eszközt. Itt látható az eszköz frissítési ügynöke, amely modulként fut. Például:

    :::image type="content" source="media/understand-device-update/device-update-module.png " alt-text="Az eszköz frissítési moduljának neve." lightbox="media/understand-device-update/device-update-module.png":::


## <a name="how-to-build-and-run-device-update-agent"></a>Az eszköz frissítési ügynökének összeállítása és futtatása

Saját ügyfél-eszköz frissítési ügynökét is létrehozhatja és módosíthatja.

Kövesse az eszköz frissítési ügynökének forrásból való [létrehozásához](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md) szükséges utasításokat.

Az ügynök sikeres felépítése után ideje [futtatni](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) az ügynököt.

Most végezze el a szükséges módosításokat, hogy az ügynököt beépítse a rendszerképbe.  Tekintse át az eszköz frissítési ügynökének [módosítását](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-modify-the-agent-code.md) ismertető útmutatót.


## <a name="troubleshooting-guide"></a>Hibaelhárítási útmutató

Ha problémákba ütközik, tekintse át az eszköz frissítését IoT Hub [hibaelhárítási útmutatót](troubleshoot-device-update.md) , amely segítséget nyújt a lehetséges problémák blokkolásához és a szükséges információk összegyűjtéséhez a Microsoftnak.


## <a name="next-steps"></a>Következő lépések

A következő, előre elkészített lemezképeket és bináris fájlokat használhatja a IoT Hub eszköz frissítésének egyszerű szemléltetéséhez:

- [Rendszerkép frissítése: első lépések a málna PI 3 B + referenciával](device-update-raspberry-pi.md) a nyílt forráskódtal bővíthető Yocto-lemezképet, ha szükséges, saját rendszerképeket hozhat létre más architektúrához.

- [Első lépések az Ubuntu (18,04 x64) szimulátor-hivatkozási ügynök használatával](device-update-simulator.md)

- [Csomag frissítése: Első lépések az Ubuntu Server 18,04 x64 Package Agent használatával](device-update-ubuntu-agent.md)

- [Azure IoT Hub-oktatóanyag az Azure-hoz – valós idejű – operációs rendszer](device-update-azure-real-time-operating-system.md)


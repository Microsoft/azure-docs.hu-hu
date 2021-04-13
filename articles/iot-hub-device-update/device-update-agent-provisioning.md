---
title: Eszközfrissítés üzembe Azure IoT Hub ügynökhöz| Microsoft Docs
description: Eszközfrissítés üzembe Azure IoT Hub ügynökhöz
author: ValOlson
ms.author: valls
ms.date: 2/16/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 812de4850c6c3577346915a0072ea11c60f7ba73
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365450"
---
# <a name="device-update-agent-provisioning"></a>Eszközfrissítési ügynök üzembeása

Az eszközfrissítési modul ügynöke más rendszerfolyamatokkal és IoT Edge modulokkal is futtatható, amelyek ugyanannak IoT Hub logikai eszköznek a részeként csatlakoznak az eszközhöz. [](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules) Ez a szakasz azt ismerteti, hogyan lehet modulidentitásként kiépíteni az eszközfrissítési ügynököt. 


## <a name="module-identity-vs-device-identity"></a>Modulidentitás és eszközidentitás

A IoT Hub az egyes eszközidentitások alatt legfeljebb 50 modulidentitást hozhat létre. Minden modulidentitás implicit módon létrehoz egy modulikont. Az eszközoldalon a IoT Hub eszközoldali SZOFTVERDK-k lehetővé teszik olyan modulok létrehozásához, amelyekben mindegyik független kapcsolatot nyit a IoT Hub. A modulidentitás és a moduliker hasonló képességeket biztosít, mint az eszközidentitás és az ikereszköz, de részletesebben. [További információ a modul identitási IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins)


## <a name="support-for-device-update"></a>Eszközfrissítés támogatása

Az eszközfrissítés jelenleg a következő IoT-eszköztípusokat támogatja:

* Linux-eszközök (IoT Edge és nem IoT Edge eszközök):
    * A/B kép frissítése:
        - Yocto – ARM64 (referenciakép), amely nyílt forráskódúan is elérhető, így szükség szerint saját rendszerképeket építhet más architektúrákhoz. [](device-update-agent-provisioning.md#how-to-build-and-run-device-update-agent)
        - Ubuntu 18.04-szimulátor
       
    * A Package Agent a következő platformokhoz/architektúrákhoz támogatott buildeket támogatja:
        - Ubuntu Server 18.04 x64 csomagügynök 
        - Debian 9 
        
* Korlátozott eszközök:
    * AzureRTOS device update agent samples: [Device Update for Azure IoT Hub tutorial for Azure-Real-Time-Operating-System (Azure-beli valós](device-update-azure-real-time-operating-system.md) idejű operációs rendszer) oktatóanyag

* Leválasztott eszközök: 
    * [A leválasztott eszközfrissítések támogatásának](connected-cache-disconnected-device-update.md)


## <a name="prerequisites"></a>Előfeltételek  

Ha az IoT-eszközt/IoT Edge-eszközt csomagalapú frissítésekhez adja [hozzá,](https://docs.microsoft.com/azure/iot-hub-device-update/understand-device-update#support-for-a-wide-range-of-update-artifacts)packages.microsoft.com a következő lépésekkel adhat hozzá packages.microsoft.com-t a gép adattárában:

1. Jelentkezzen be arra a gépre vagy IoT-eszközre, amelyre telepíteni kívánja az eszközfrissítési ügynököt.

1. Nyisson meg egy Terminál ablakot.

1. Telepítse az eszköz operációs rendszerének megfelelő adattár-konfigurációt.
    ```shell
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
    ```
    
1. Másolja a létrehozott listát a sources.list.d könyvtárba.
    ```shell
    sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
    ```
    
1. Telepítse a Microsoft GPG nyilvános kulcsát.
    ```shell
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    ```

    ```shell
    sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
    ```

## <a name="how-to-provision-the-device-update-agent-as-a-module-identity"></a>Az eszközfrissítési ügynök kiépítése modulidentitásként

Ez a szakasz azt ismerteti, hogyan lehet modulidentitásként kiépíteni az eszközfrissítési ügynököt IoT Edge, nem Edge IoT-eszközökön és más IoT-eszközökön.


### <a name="on-iot-edge-enabled-devices"></a>Engedélyezett IoT Edge eszközökön

Kövesse ezeket az utasításokat az eszközfrissítési ügynök üzembe IoT Edge [eszközökön.](https://docs.microsoft.com/azure/iot-edge)

1. Kövesse az utasításokat [a Azure IoT Edge telepítéséhez és üzembe Azure IoT Edge telepítéséhez.](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge?view=iotedge-2020-11&preserve-view=true)

1. Az eszközfrissítési rendszerkép-frissítési ügynök telepítése
    - Az [Artifactsben](https://github.com/Azure/iot-hub-device-update/releases) mintaképeket biztosítunk, hogy különböző verziókban próbálja ki a rendszerképek frissítését egy alapként használt rendszerkép (adu-base-image) és egy frissítési rendszerkép (adu-update-image) használatával. Tekintse meg a kép saját eszközön való flash [IoT Hub példát.](https://docs.microsoft.com/azure/iot-hub-device-update/device-update-raspberry-pi#flash-sd-card-with-image)  

1. Az eszközfrissítési csomag frissítési ügynökének telepítése  
    - A legújabb ügynökverziók packages.miscrosoft.com: Csomaglisták frissítése az eszközön, és telepítse az eszközfrissítési ügynökcsomagot és annak függőségeit a következő használatával:   
    ```shell
    sudo apt-get update
    ```
    
    ```shell
    sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt
    ```
    
    - Az [Artifacts](https://github.com/Azure/iot-hub-device-update/releases) következő kiadásra jelölt verzióihoz: Töltse le a .dep fájlt arra a gépre, amelyre telepíteni szeretné az eszközfrissítési ügynököt, majd:
     ```shell
    Sudo apt-get install -y ./"<PATH TO FILE>"/"<.DEP FILE NAME>"
     ```
    
1. Most már készen áll arra, hogy elindítsa az eszközfrissítési ügynököt a IoT Edge eszközén. 

### <a name="on-non-edge-iot-linux-devices"></a>Nem Edge IoT Linux-eszközökön

Az alábbi utasításokat követve kiépítheti az eszközfrissítési ügynököt az IoT Linux-eszközein.

1. Telepítse az IoT Identity Service-t, és adja hozzá a legújabb verziót az IoT-eszközhöz. 
    1. Jelentkezzen be a gépre vagy az IoT-eszközre.
    1. Nyisson meg egy terminálablakot.
    1.  Telepítse a legújabb [IoT Identity Service-t](https://github.com/Azure/iot-identity-service/blob/main/docs/packaging.md#installing-and-configuring-the-package) az IoT-eszközön a következő paranccsal:
    
        ```shell
        sudo apt-get install aziot-identity-service
        ```
        
1. IoT-identitásszolgáltatás kiépítése az IoT-eszközinformációk lekért érdekében.
    * Hozzon létre egy egyéni másolatot a konfigurációs sablonról, hogy hozzáadjuk a kiépítési adatokat. Írja be az alábbi parancsot egy terminálba.
      
        ```shell
        sudo cp /etc/aziot/config.toml.template /etc/aziot/config.toml 
        ```
   
1. Ezután szerkessze a konfigurációs fájlt, hogy tartalmazza annak az eszköznek a kapcsolati sztringét, amely az eszköz vagy gép kiépítési eszközeként működik. Írja be az alábbi parancsot egy terminálba.

    ```shell
    sudo nano /etc/aziot/config.toml
    ```
   
1. Az alábbi példához hasonló üzenetet kell látnia:

    :::image type="content" source="media/understand-device-update/config.png" alt-text="Az IoT Identity Service konfigurációs fájlját ábrázoló diagram." lightbox="media/understand-device-update/config.png":::

    1. Ugyanabban a nano ablakban keresse meg a "Manual provisioning with connection string" (Manuális kiépítés kapcsolati sztring segítségével) blokkot.
    1. Az ablakban törölje a "#" szimbólumot a "kiépítés" előtt
    1. Az ablakban törölje a "#" szimbólumot a "source" előtt 
    1. Az ablakban törölje a "#" szimbólumot a "connection_string" előtt
    1. Az ablakban törölje a "connection_string" jobb oldalon található idézőjelek között található sztringet, majd adja hozzá a kapcsolati sztringet 
    1. Mentse a fájl módosításait a Ctrl+X, majd az Y billentyűkombinációval, majd nyomja le az Enter billentyűt a módosítások mentéshez. 
    
1.  Most alkalmazza és indítsa újra az IoT-identitásszolgáltatást az alábbi paranccsal. Most egy "Kész!" A printout azt jelenti, hogy sikeresen konfigurálta az IoT Identity Service-t.

    > [!Note]
    > Az IoT-identitásszolgáltatás jelenleg szimmetrikus kulcsokkal regisztrál modulidentitásokat IoT Hub-identitásokkal.
    
    ```shell
    sudo aziotctl config apply
    ```
    
1.  Végül telepítse az eszközfrissítési ügynököt. Az [Artifactsben](https://github.com/Azure/iot-hub-device-update/releases) mintaképeket biztosítunk, hogy különböző verziókra próbálja ki a rendszerképek frissítését egy alapként használt rendszerkép (adu-base-image) és egy frissítési rendszerkép (adu-update-image) használatával. Tekintse meg a képnek a saját eszközén [való IoT Hub példát.](https://docs.microsoft.com/azure/iot-hub-device-update/device-update-raspberry-pi#flash-sd-card-with-image)

1.  Most már készen áll arra, hogy elindítsa az eszközfrissítési ügynököt az IoT-eszközön. 

### <a name="other-iot-devices"></a>Egyéb IoT-eszközök

Az eszközfrissítési ügynök az IoT Identity szolgáltatás nélkül is konfigurálható teszteléshez vagy korlátozott eszközökön. Kövesse az alábbi lépéseket az eszközfrissítési ügynök kapcsolati sztring használatával (a modulból vagy az eszközből) való telepítéséhez.

1.  Az [Artifactsben](https://github.com/Azure/iot-hub-device-update/releases) mintaképeket biztosítunk, hogy különböző verziókra próbálja ki a rendszerképek frissítését egy alapként használt rendszerkép (adu-base-image) és egy frissítési rendszerkép (adu-update-image) használatával. Tekintse meg a képnek a saját eszközén [való IoT Hub példát.](https://docs.microsoft.com/azure/iot-hub-device-update/device-update-raspberry-pi#flash-sd-card-with-image)

1.  Jelentkezzen be a gépre vagy IoT Edge/IoT-eszközre.
    
1.  Nyisson meg egy terminálablakot.

1.  Adja hozzá a kapcsolati sztringet az [Eszközfrissítés konfigurációs fájlhoz:](device-update-configuration-file.md)
    1. Írja be az alábbiakat a terminálablakba:
        - [Csomagfrissítések](device-update-ubuntu-agent.md) használata: sudo nano /etc/adu/adu-conf.txt
        - [Képfrissítések](device-update-raspberry-pi.md) használata: sudo nano /adu/adu-conf.txt
       
    1. Meg kell nyitnia egy ablakot, amely szöveget tartalmaz. Törölje a teljes sztringet a "connection_String=" karakterlánc után, amikor először kiépíti az eszközfrissítési ügynököt az IoT-eszközön. Ez csak egy helytartószöveg.
    
    1. A terminálban cserélje le a "<your-connection-string>" helyére az eszköz eszközfrissítési ügynökpéldányának kapcsolati sztringjére.
    
        > [!Important]
        > Ne adjon idézőjeleket a kapcsolati sztringhez.
        ```shell
        connection_string=<ADD CONNECTION STRING HERE>
        ```
       
    1. Írja be és mentse a gombra.
    
1.  Most már készen áll arra, hogy elindítsa az eszközfrissítési ügynököt az IoT-eszközön. 


## <a name="how-to-start-the-device-update-agent"></a>Az eszközfrissítési ügynök elindítani

Ez a szakasz azt ismerteti, hogyan indíthatja el és ellenőrizheti az eszközfrissítési ügynököt modulidentitásként, amely sikeresen fut az IoT-eszközön.

1.  Jelentkezzen be arra a gépre vagy eszközre, amelyre telepítve van az eszközfrissítési ügynök.

1.  Nyisson meg egy Terminál ablakot, és írja be az alábbi parancsot.
    ```shell
    sudo systemctl restart adu-agent
    ```
    
1.  Az ügynök állapotát az alábbi paranccsal ellenőrizheti. Ha bármilyen problémát lát, tekintse meg ezt a [hibaelhárítási útmutatót.](troubleshoot-device-update.md)
    ```shell
    sudo systemctl status adu-agent
    ```
    
    Az OK állapotnak kell látsza.

1.  A IoT Hub az IoT-eszköz vagy IoT Edge eszközök között keresse meg az eszközfrissítési ügynökkel konfigurált eszközt. Itt láthatja, hogy az eszközfrissítési ügynök modulként fut. Például:

    :::image type="content" source="media/understand-device-update/device-update-module.png " alt-text="Az eszközfrissítési modul nevének ábrája." lightbox="media/understand-device-update/device-update-module.png":::


## <a name="how-to-build-and-run-device-update-agent"></a>Eszközfrissítési ügynök létrehozása és futtatása

Saját ügyfél eszközfrissítési ügynökét is felépítheti és módosíthatja.

Kövesse az utasításokat az [eszközfrissítési](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md) ügynök forrásból való felépítéséhez.

Az ügynök sikeres kiépítése után ideje futtatni [az](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) ügynököt.

Most tegye meg a szükséges módosításokat, hogy az ügynököt beépítse a rendszerképbe.  Útmutatásért nézze meg, [hogyan](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-modify-the-agent-code.md) módosíthatja az eszközfrissítési ügynököt.


## <a name="troubleshooting-guide"></a>Hibaelhárítási útmutató

Ha problémákba fog belefutni, tekintse át [](troubleshoot-device-update.md) az eszközfrissítéssel kapcsolatos IoT Hub hibaelhárítási útmutatót a lehetséges problémák elhárításához, és gyűjtse össze a Microsoftnak való tájékoztatáshoz szükséges adatokat.


## <a name="next-steps"></a>Következő lépések

A következő előre felépített rendszerképeket és bináris fájlokat használhatja az eszközfrissítés egyszerű bemutatójaként a IoT Hub:

- [Képfrissítés: Ismerkedés a Raspberry Pi 3 B+](device-update-raspberry-pi.md) referencia Yocto-képkiépítéssel nyílt forráskódú megoldással, hogy szükség szerint saját rendszerképeket hoz létre más architektúrákhoz.

- [Ismerkedés az Ubuntu (18.04 x64) szimulátor referenciaügynökének használatával](device-update-simulator.md)

- [Csomagfrissítés:Az Ubuntu Server 18.04 x64 csomagügynök használatának első lépések](device-update-ubuntu-agent.md)

- [Eszközfrissítés Azure IoT Hub azure-beli valós idejű operációs rendszerhez](device-update-azure-real-time-operating-system.md)


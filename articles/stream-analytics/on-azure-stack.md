---
title: Azure Stream Analytics futtatása Azure Stack
description: Hozzon létre egy Azure Stream Analytics Edge-feladatot, és telepítse azt Azure Stack hubhoz a IoT Edge Runtime használatával.
ms.service: stream-analytics
author: an-emma
ms.author: raan
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18
ms.openlocfilehash: 315b2efa042721a19fa779794c4921f9ced1fc83
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589803"
---
# <a name="run-azure-stream-analytics-on-azure-stack"></a>Azure Stream Analytics futtatása Azure Stack 

A Azure Stream Analytics Azure Stack hub-on IoT Edge modulként is futtatható. A konfigurációk hozzá lettek adva a IoT Edge modulhoz, amely lehetővé teszi, hogy egy Azure Stack hub-előfizetésben futó blob Storage-, Event Hubs-és IoT-Hubokkal működjön együtt azáltal, hogy engedélyezi az összes Azure Stack hub-beli alkalmazásban található Egyéni URL-címeket

A Stream Analytics on Azure Stack segítségével valóban hibrid architektúrákat építhet ki a saját privát, autonóm felhőben, amelyek a helyszíni konzisztens Azure-szolgáltatások használatával csatlakoztathatók vagy lekapcsolhatók a Felhőbeli natív alkalmazásokkal. 

Ebből a cikkből megtudhatja, hogyan továbbíthat IoT Hub vagy Event hub adatait egy másik Event hubhoz, vagy Blob Storage egy Azure Stack hub-előfizetésben, és hogyan dolgozza fel a Stream Analytics.

## <a name="set-up-environments"></a>Környezetek létrehozása

A Azure Stream Analytics a Azure Stack hub hibrid szolgáltatása. Ez egy IoT Edge modul, amely az Azure-ban van konfigurálva, de Azure Stack hub-on is futtatható.  

Ha új Azure Stack hub vagy IoT Edge esetében, kövesse az alábbi utasításokat a környezetek beállításához.

### <a name="prepare-the-azure-stack-hub-environment"></a>Az Azure Stack hub-környezet előkészítése

Hozzon létre egy Azure Stack hub-előfizetést. További információ: [Azure stack hub-előfizetés létrehozásának oktatóanyaga.](/azure-stack/user/azure-stack-subscribe-services/)

Ha szeretné kiértékelni Azure Stack hubot a saját kiszolgálóján, használhatja a Azure Stack Development Kitt (ASDK).  A ASDK kapcsolatos további információkért tekintse meg a [ASDK áttekintését](/azure-stack/asdk/).

### <a name="install-the-iot-edge-runtime"></a>Az IoT Edge-futtatókörnyezet telepítése

A Azure Stream Analytics Azure Stack hub-on való futtatásához az eszköznek rendelkeznie kell a IoT Edge futtatókörnyezettel, és hálózati kapcsolattal kell rendelkeznie az Azure Stack hubhoz, vagy az Azure Stack hub-on futó virtuális gépnek kell lennie. Az IoT Edge futtatókörnyezet lehetővé teszi, hogy az Stream Analytics Edge-feladatok integrálva legyenek az Azure Storage szolgáltatással és az Azure Event Hubs, amelyek az Azure Stack hub-on futnak. További információ: [IoT Edge-eszközökön futó Azure stream Analytics](stream-analytics-edge.md) 

Az Azure Stack hub erőforrásaihoz való hálózati hozzáférés mellett a IoT Edge eszköznek (vagy virtuális gépnek) hozzá kell férnie az Azure IoT Hub az Azure nyilvános felhőben a Stream Analytics modul konfigurálásához. 

A következő útmutatók bemutatják, hogyan állíthatja be az IoT Edge futtatókörnyezetet az eszközön vagy a virtuális gépen:

* [Az Azure IoT Edge-futtatókörnyezet telepítése Windows rendszeren](../iot-edge/how-to-install-iot-edge.md)
* [Az Azure IoT Edge-futtatókörnyezet telepítése Debian-alapú Linux rendszereken](../iot-edge/how-to-install-iot-edge.md)


## <a name="create-an-azure-stream-analytics-edge-job"></a>Azure Stream Analytics Edge-feladatok létrehozása

Az ASA Edge-feladatok a Azure IoT Edge eszközökön üzembe helyezett tárolókban futnak. Ezek két részből állnak:
* A feladattípusért felelős Felhőbeli rész: a felhasználók bemeneteket, kimeneteket, lekérdezéseket és egyéb beállításokat határoznak meg a felhőben.
* Egy modul, amely a IoT-eszközökön fut. Ez tartalmazza az ASA-motort, és a feladattípust fogadja a felhőből.

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

Amikor Azure Stream Analytics-feladatot hoz létre egy IoT Edge-eszköz futtatásához, azt úgy kell tárolni, hogy meghívható legyen az eszközről. Használhat meglévő Azure Storage-fiókot, vagy létrehozhat egy újat.
1. A Azure Portal válassza az **erőforrás létrehozása > > Storage-fiók – blob, fájl, tábla, üzenetsor** lehetőséget.
2. Adja meg a következő értékeket a tárfiók létrehozásához:

   | Mező | Érték |
   | --- | --- |
   | Név | Adja meg a tárfiók egyedi nevét. |
   | Hely | Válassza ki az Önhöz legközelebb eső helyet.|
   | Előfizetés | Válassza ugyanazt az előfizetést, mint az IoT Hub esetében.|
   | Erőforráscsoport | Azt javasoljuk, hogy ugyanazt az erőforráscsoportot használja az [IoT Edge](../iot-edge/quickstart.md) rövid útmutatók és oktatóanyagok során létrehozott összes tesztelési erőforráshoz. Például: **IoTEdgeResources**. |

3. Fogadja el az alapértelmezett értékeket a többi mezőben, és válassza a **Létrehozás** lehetőséget.


### <a name="create-a-new-job"></a>Új feladat létrehozása

1. A Azure Portal válassza az **erőforrás létrehozása > eszközök internetes hálózata > stream Analytics feladatot**.
2. Adja meg a következő értékeket a tárfiók létrehozásához:

   | Mező | Érték |
   | --- | --- |
   | Feladat neve | Adja meg a feladat nevét. Például: **IoTEdgeJob** |
   | Előfizetés | Válassza ugyanazt az előfizetést, mint az IoT Hub esetében.|
   | Erőforráscsoport | Azt javasoljuk, hogy ugyanazt az erőforráscsoportot használja az [IoT Edge](../iot-edge/quickstart.md) rövid útmutatók és oktatóanyagok során létrehozott összes tesztelési erőforráshoz. Például: **IoTEdgeResources**. |
   | Hely | Válassza ki az Önhöz legközelebb eső helyet. |
   | Üzemeltetési környezet | Válassza az **Edge** lehetőséget. |

3. Válassza a **Létrehozás** lehetőséget.

### <a name="configure-your-job"></a>A feladat konfigurálása

Miután létrejött a Stream Analytics-feladat az Azure Portalon, konfigurálhatja azt egy bemenettel, egy kimenettel és egy lekérdezéssel az áthaladó adatokon való futtatáshoz. Egy Azure Stack hub-előfizetésben manuálisan is megadhat bemeneteket egy IoT Hub vagy egy Event hub használatával.

1. Keresse meg a Stream Analytics-feladatot az Azure Portalon.
2. A **Konfigurálás** területen válassza a **Storage-fiók beállításai** lehetőséget, majd válassza ki az előző lépésben létrehozott Storage-fiókot.
   > [!div class="mx-imgBorder"]
   > [A ![ feladatok tárolási fiókjának beállítása ](media/on-azure-stack/storage-account-settings.png)](media/on-azure-stack/storage-account-settings.png#lightbox)
3. A **feladatok topológiája** területen válassza a **bemenetek** lehetőséget, majd adja meg az **adatfolyam bemenetét.**
4. A legördülő listából válassza az **IoT hub**, az **Event hub** vagy az **Edge hub** elemet. 
5. Ha a bemenet egy Event hub vagy IoT Hub Azure Stack hub-előfizetésben, adja meg az adatokat manuálisan az alább látható módon.

   #### <a name="event-hub"></a>Eseményközpont

   | Mező | Érték |
   | --- | --- |
   | Bemeneti alias | A feladathoz tartozó lekérdezésben használt rövid név, amely erre a bemenetre hivatkozik. |
   | Service Bus névtér | A névtér egy üzenetküldési entitások készletének tárolója. Új Event hub létrehozásakor a rendszer létrehozza a névteret is. (Példa: *SB:// <Event Hub Name> . eventhub.Shanghai.azurestack.Corp.microsoft.com*) |
   | Eseményközpont neve | Az Event hub bemenetként használandó neve. |
   | Eseményközpont szabályzatának neve | Az Event hub elérését biztosító közös hozzáférési szabályzat. Minden megosztott hozzáférési házirend rendelkezik egy névvel, a beállított engedélyekkel és a hozzáférési kulcsokkal. Ez a beállítás automatikusan ki van töltve, kivéve, ha az Event hub beállításainak manuális megadását választja. |
   | Event hub-házirend kulcsa | Az Event hub elérésének engedélyezéséhez használt megosztott elérési kulcs. Ezt a beállítást automatikusan kitölti a rendszer, hacsak nem kiválasztja az Event hub-beállítások manuális megadásának lehetőségét. Az Event hub beállításaiban találhatja meg. |
   | Event hub fogyasztói csoport (nem kötelező) | Kifejezetten ajánlott külön fogyasztói csoportot használni minden Stream Analytics feladathoz. Ez a karakterlánc azonosítja az Event hub adatainak betöltéséhez használandó fogyasztói csoportot. Ha nincs megadva fogyasztói csoport, a Stream Analytics-feladatokhoz a $Default fogyasztói csoportot használja. |
   | Partíciók száma | A partíciók száma az Event hub partícióinak száma. |

   > [!div class="mx-imgBorder"]
   > [![Event hub-bemenet ](media/on-azure-stack/event-hub-input.png)](media/on-azure-stack/event-hub-input.png#lightbox)

   #### <a name="iot-hub"></a>IoT Hub

   | Mező | Érték |
   | --- | --- |
   | Bemeneti alias | A feladathoz tartozó lekérdezésben használt rövid név, amely erre a bemenetre hivatkozik. |
   | IoT Hub | A bemenetként használandó IoT Hub neve. (Példa:*<IoT Hub Name> . Shanghai.azurestack.Corp.microsoft.com*) |
   | Megosztott hozzáférési szabályzat neve | A IoT Hub elérését biztosító közös hozzáférési szabályzat. Minden megosztott hozzáférési házirend rendelkezik egy névvel, a beállított engedélyekkel és a hozzáférési kulcsokkal. |
   | Megosztott elérési házirend kulcsa | A IoT Hub elérésének engedélyezéséhez használt megosztott elérési kulcs. Ezt a beállítást automatikusan kitölti a rendszer, hacsak nem kiválasztja a IOT hub beállításainak manuális megadására szolgáló lehetőséget. |
   | Fogyasztói csoport (nem kötelező) | Javasoljuk, hogy minden Stream Analytics feladatokhoz használjon egy másik fogyasztói csoportot. A fogyasztói csoport a IoT Hub adatainak betöltésére szolgál. Stream Analytics a $Default fogyasztói csoportot használja, hacsak nem ad meg mást. |
   | Partíciók száma | A partíciók száma az Event hub partícióinak száma. |

   > [!div class="mx-imgBorder"]
   > [![IoT hub bemenet ](media/on-azure-stack/iot-hub-input.png)](media/on-azure-stack/iot-hub-input.png#lightbox)

6. Hagyja változatlanul a többi mező értékét, és válassza a Mentés lehetőséget.
7. A Feladattopológia területen nyissa meg a Kimenetek pontot, és válassza a Hozzáadás elemet.
8. A legördülő listából válassza az Blob Storage, az Event hub vagy az Edge hub elemet.
9. Ha a kimenet egy Event hub vagy Blob Storage egy Azure Stack hub-előfizetésben, adja meg az adatokat manuálisan az alább látható módon.

   #### <a name="event-hub"></a>Eseményközpont

   | Mező | Érték |
   | --- | --- |
   | Kimeneti alias | Egy rövid név, amelyet a lekérdezések a lekérdezés kimenetének az Event hub-ba történő irányításához használnak. |
   | Service Bus névtér | Az üzenetküldési entitások készletének tárolója. Új Event hub létrehozásakor létrehozott egy Service Bus-névteret is. (Példa: *SB:// <Event Hub Name> . eventhub.Shanghai.azurestack.Corp.microsoft.com*) |
   | Eseményközpont neve | Az Event hub kimenetének neve. |
   | Eseményközpont szabályzatának neve | A megosztott elérési házirend, amelyet az Event hub configure (Konfigurálás) lapján lehet létrehozni. Minden megosztott hozzáférési házirend rendelkezik egy névvel, a beállított engedélyekkel és a hozzáférési kulcsokkal. |
   | Event hub-házirend kulcsa | Az Event hub-névtérhez való hozzáférés hitelesítéséhez használt megosztott elérési kulcs. |

   > [!div class="mx-imgBorder"]
   > [![Event hub-kimenet ](media/on-azure-stack/event-hub-output.png)](media/on-azure-stack/event-hub-output.png#lightbox)

   #### <a name="blob-storage"></a>Blob Storage 

   | Mező | Érték |
   | --- | --- |
   | Kimeneti alias | Egy rövid név, amely lekérdezésekben a lekérdezés kimenetének a blob-tárolóba történő irányítására szolgál. |
   | A(z) | Annak a Storage-fióknak a neve, ahová a kimenetet küldi. (Példa: *<Storage Account Name> . blob.Shanghai.azurestack.Corp.microsoft.com*) |
   | Storage-fiók kulcsa | A Storage-fiókhoz társított titkos kulcs. Ezt a beállítást automatikusan kitölti a rendszer, hacsak nem kiválasztja a blob Storage-beállítások manuális megadásának lehetőségét. |

> [!NOTE]
> Azure Stack hub peremhálózati feladatai esetében nem támogatott a parketta formátuma. A minimális sorok és a maximális idő mezőben adja meg a 0 értéket, vagy hagyja üresen.


## <a name="deploy-stream-analytics-on-a-vm-or-device-connected-to-azure-stack"></a>Stream Analytics üzembe helyezése egy virtuális gépen vagy Azure Stackhoz csatlakoztatott eszközön

1. A Azure Portal nyissa meg IoT Hub. Navigáljon **IoT Edgere** , és kattintson arra az eszközre (VM), amelyet a központi telepítéshez szeretne célozni.
2. Válassza a **Set modules** (Modulok beállítása) lehetőséget. Ezután válassza a **+ Hozzáadás** lehetőséget, és válassza **Azure stream Analytics modult**. 
3. Válassza ki az előfizetést és a Steam Analytics Edge-feladatot, amelyet Ön hozott létre. Kattintson a **Mentés** gombra, és válassza a **Tovább: útvonalak** lehetőséget.

   > [!div class="mx-imgBorder"]
   > [![Modulok ](media/on-azure-stack/edge-modules.png) hozzáadása](media/on-azure-stack/edge-modules.png#lightbox)

4. Kattintson a **felülvizsgálat + >létrehozása** lehetőségre.
5. A **felülvizsgálat + létrehozás** lépésben válassza a **Létrehozás** lehetőséget. 
   > [!div class="mx-imgBorder"]
   > [![Jegyzékfájl ](media/on-azure-stack/module-content.png)](media/on-azure-stack/module-content.png#lightbox)
6. Győződjön meg arról, hogy a modul hozzá van adva a listához.
   > [!div class="mx-imgBorder"]
   > [![Üzembe helyezés lap ](media/on-azure-stack/edge-deployment.png)](media/on-azure-stack/edge-deployment.png#lightbox)

## <a name="next-steps"></a>Következő lépések
- [IoT Edge-eszközökön futó Azure Stream Analytics](./stream-analytics-edge.md)
- [Stream Analytics Edge-feladatok fejlesztése](/stream-analytics-query/stream-analytics-query-language-reference)

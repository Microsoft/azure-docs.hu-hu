---
title: Anomália-detektor futtatása IoT Edge
titleSuffix: Azure Cognitive Services
description: Az anomália-detektor modul üzembe helyezése IoT Edge.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 12/03/2020
ms.author: mbullwin
ms.openlocfilehash: b4153b07b153a9ee0b16dc032ab5e7810e236d7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "98936269"
---
# <a name="deploy-an-anomaly-detector-module-to-iot-edge"></a>Rendellenesség-Kiderítő modul üzembe helyezése IoT Edge

Megtudhatja, hogyan helyezheti üzembe a Cognitive Services [anomália detektor](../anomaly-detector-container-howto.md) modult egy IoT Edge eszközön. A IoT Edgebe való üzembe helyezése után a modul IoT Edge más modulokkal együtt, tároló példányként fut. Ez ugyanazokat az API-kat mutatja be, mint a standard Docker-tároló környezetében futó anomália detektor tároló példánya. 

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés használata. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free).
* Telepítse az [Azure CLI](/cli/azure/install-azure-cli)-t.
* Egy [IoT hub](../../../iot-hub/iot-hub-create-through-portal.md) és egy [IoT Edge](../../../iot-edge/quickstart-linux.md) eszköz.

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

## <a name="deploy-the-anomaly-detection-module-to-the-edge"></a>Az anomáliák észlelése modul üzembe helyezése az Edge-ben

1. A Azure Portal írja be az **anomália detektort IoT Edge** a keresésbe, és nyissa meg az Azure Piactéri eredményt.
2. Ekkor megjelenik a Azure Portal [IoT Edge moduljának megcélzott eszközei](https://portal.azure.com/#create/azure-cognitive-service.edge-anomaly-detector). Adja meg az alábbi szükséges adatokat.

    1. Válassza ki előfizetését.

    1. Válassza ki a IoT Hub.

    1. Válassza az **eszköz keresése** lehetőséget, és keressen egy IoT Edge eszközt.

3. Válassza a **Létrehozás** gombot.

4. Válassza ki a **AnomalyDetectoronIoTEdge** modult.

    :::image type="content" source="../media/deploy-anomaly-detection-on-iot-edge/iot-edge-modules.png" alt-text="IoT Edge modulok felhasználói felületének képe, amely a AnomalyDetectoronIoTEdge hivatkozással van kiemelve egy piros mezővel, jelezve, hogy ez a kiválasztható elem.":::

5. Lépjen a **Környezeti változók** területre, és adja meg az alábbi információkat.

    1.  Tartsa meg a végfelhasználói licencszerződés **elfogadására** vonatkozó értéket.

    1. A **Számlázás** területen adja meg a Cognitive Services-végpontot.

    1. Az **ApiKey** területen adja meg a Cognitive Services API-kulcsot.

    :::image type="content" source="../media/deploy-anomaly-detection-on-iot-edge/environment-variables.png" alt-text="Környezeti változók a végpontok és API-kulcsok kitöltéséhez szükséges értékeket tartalmazó piros mezőkkel":::

6. Válassza a **Frissítés** lehetőséget

7. Válassza a **Tovább: útvonalak** lehetőséget az útvonal definiálásához. Itt megszabhatja, hogy az összes üzenet minden modulból az Azure IoT Hubba érkezzen.

8. Válassza a **Tovább: Ellenőrzés és létrehozás** lehetőségre. Megtekintheti annak a JSON-fájlnak az előnézetét, amely meghatározza az IoT Edge-eszközön üzembe helyezett összes modult.
    
9. A modul üzembe helyezési folyamatának elindításához válassza a **Létrehozás** gombot.

10. A modul üzembe helyezésének befejezése után visszatérhet az IoT Hub IoT Edge lapjára. A részletek megtekintéséhez válassza ki az eszközt a IoT Edge-eszközök listájából.

11. Görgessen lefelé, és tekintse meg a felsorolt modulokat. Győződjön meg arról, hogy az új modul futásidejű állapota fut. 

A IoT Edge eszköz futtatókörnyezeti állapotának hibaelhárításához tekintse meg a [hibaelhárítási útmutatót](../../../iot-edge/troubleshoot.md) .

## <a name="test-anomaly-detector-on-an-iot-edge-device"></a>Anomália-detektor tesztelése IoT Edge eszközön

Egy HTTP-hívást intéz az Azure Cognitive Services-szolgáltatás által futtatott Azure IoT Edge-eszközhöz. A tároló REST-alapú Endpoint API-kat biztosít. Használja a gazdagépet, a `http://<your-edge-device-ipaddress>:5000` modul API-jait.

Ha a peremhálózati eszköz még nem engedélyezi a bejövő kommunikációt az 5000-as porton, létre kell hoznia egy új **bejövő portszabály-szabályt**. 

Egy Azure-beli virtuális gép esetén ez a **virtuálisgép**-  >  **Beállítások**  >  **hálózatkezelés**  >  **bejövő port szabályának**  >  **hozzáadása a bejövő porthoz szabály hozzáadásával** állítható be.

Több módon is ellenőrizheti, hogy a modul fut-e. Keresse meg a szóban forgó peremhálózati eszköz *külső IP-* címét és az elérhető portot, és nyissa meg a kedvenc böngészőjét. Az alábbi kérelmek URL-címeivel ellenőrizheti, hogy a tároló fut-e. Az alábbi példa a kérelem URL-címeit tartalmazza `http://<your-edge-device-ipaddress:5000` , de az adott tároló eltérő lehet. Ne feledje, hogy a peremhálózati eszköz *külső IP-* címét kell használnia.

| Kérelem URL-címe | Cél |
|:-------------|:---------|
| `http://<your-edge-device-ipaddress>:5000/` | A tároló egy kezdőlappal rendelkezik. |
| `http://<your-edge-device-ipaddress>:5000/status` | A GET-mel is kérték, ezzel ellenőrzi, hogy a tároló indításához használt API-kulcs érvényes-e végponti lekérdezés nélkül. Ez a kérelem az Kubernetes [és készültségi](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)tesztekhez használható. |
| `http://<your-edge-device-ipaddress>:5000/swagger` | A tároló átfogó dokumentációval is rendelkezik a végpontokhoz, valamint egy **kipróbálás** funkcióval is. Ezzel a funkcióval megadhatja a beállításokat egy webalapú HTML-űrlapon, és anélkül teheti meg a lekérdezést, hogy kódot kellene írnia. A lekérdezés visszaadása után egy példa CURL-parancs van megadva a szükséges HTTP-fejlécek és-szövegtörzs bemutatásához. |

![Tároló kezdőlapja](../../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png)

## <a name="next-steps"></a>Következő lépések

* A tároló rendszerképének áthúzásához és a tároló futtatásához tekintse át a tárolók [telepítése és futtatása](../anomaly-detector-container-configuration.md)
* A [tárolók konfigurálásának](../anomaly-detector-container-configuration.md) áttekintése konfigurációs beállításokhoz
* [További információ az anomália-érzékelő API szolgáltatásáról](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

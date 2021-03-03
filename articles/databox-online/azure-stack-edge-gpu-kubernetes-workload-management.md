---
title: A Kubernetes számítási feladatok kezelésének megismerése Azure Stack Edge Pro-eszközön | Microsoft Docs
description: Ismerteti, hogyan kezelhetők a Kubernetes számítási feladatok a Azure Stack Edge Pro-eszközön.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: aac4278c6ce03c43418e99978cd039e24dc01194
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101719270"
---
# <a name="kubernetes-workload-management-on-your-azure-stack-edge-pro-device"></a>Kubernetes a számítási feladatok kezelése a Azure Stack Edge Pro-eszközön

A Azure Stack Edge Pro-eszközön a számítási szerepkör konfigurálásakor létrejön egy Kubernetes-fürt. A Kubernetes-fürt létrehozása után a tároló alkalmazások a Kubernetes-fürtön helyezhetők üzembe a hüvelyben. A számítási feladatokat különböző módokon lehet üzembe helyezni a Kubernetes-fürtön. 

Ez a cikk a munkaterhelések Azure Stack Edge Pro-eszközön való üzembe helyezéséhez használható különböző módszereket ismerteti.

## <a name="workload-types"></a>Számítási feladatok típusai

Az Azure Stack Edge Pro-eszközön üzembe helyezhető munkaterhelések két gyakori típusa állapot nélküli alkalmazások vagy állapot-nyilvántartó alkalmazások.

- Az **állapot nélküli alkalmazások** nem őrzik meg az állapotukat, és nem mentik az adatvesztést az állandó tárterületre. Az összes felhasználói és munkamenet-adatmennyiség az ügyféllel együtt marad. Néhány példa az állapot nélküli alkalmazásokra, például az Nginx és más webalkalmazások webes felületére.

    Az állapot nélküli alkalmazások fürtön való üzembe helyezéséhez létrehozhat egy Kubernetes-telepítést. 

- Az **állapot-nyilvántartó alkalmazások** megkövetelik, hogy az állapotuk mentése megtörténjen. Az állapot-nyilvántartó alkalmazások állandó tárterületet (például állandó köteteket) használnak a kiszolgáló vagy más felhasználók általi használatra való adatmentéshez. Az állapot-nyilvántartó alkalmazások például olyan adatbázisok, mint például az [Azure SQL Edge](../azure-sql-edge/overview.md) és a MongoDB.

    Az állapot-nyilvántartó alkalmazások üzembe helyezéséhez létrehozhat egy Kubernetes-telepítést. 

## <a name="deployment-flow"></a>Üzembe helyezési folyamat

Az alkalmazások Azure Stack Edge Pro-eszközön való üzembe helyezéséhez kövesse az alábbi lépéseket: 
 
1. **Hozzáférés konfigurálása**: először a PowerShell-RunSpace használatával hozzon létre egy felhasználót, hozzon létre egy névteret, és adja meg a felhasználói hozzáférést a névtérhez.
2. **Tároló konfigurálása**: ezután a Azure Portal Azure stack Edge erőforrását fogja használni az állandó kötetek létrehozásához, statikus vagy dinamikus kiépítés használatával a telepítendő állapot-nyilvántartó alkalmazások számára.
3. **Hálózatkezelés konfigurálása**: Végezetül a szolgáltatásokkal külső és a Kubernetes-fürtön is elérhetővé teheti az alkalmazásokat.
 
## <a name="deployment-types"></a>Központi telepítési típusok

A munkaterhelések üzembe helyezésének három fő módja van. Az üzembe helyezési módszerek mindegyike lehetővé teszi, hogy az eszköz egy különálló névteréhez kapcsolódjon, majd állapot nélküli vagy állapot-nyilvántartó alkalmazásokat helyezzen üzembe.

![Kubernetes munkaterhelés üzembe helyezése](./media/azure-stack-edge-gpu-kubernetes-workload-management/kubernetes-workload-management-1.png)

- **Helyi telepítés**: Ez a központi telepítés a parancssori hozzáférési eszközön keresztül történik, például `kubectl` lehetővé teszi a Kubernetes üzembe helyezését `yamls` . A Kubernetes-fürtöt egy fájlon keresztül érheti el a Azure Stack Edge Pro-ban `kubeconfig` . További információért látogasson el a [Kubernetes-fürt eléréséhez a kubectl-on keresztül](azure-stack-edge-gpu-create-kubernetes-cluster.md).

- **IoT Edge** üzemelő példány: ez a IoT Edgeon keresztül történik, amely az Azure IoT hubhoz csatlakozik. A Kubernetes-fürthöz a névtéren keresztül csatlakozik a Azure Stack Edge Pro-eszközön `iotedge` . Az ebben a névtérben üzembe helyezett IoT Edge-ügynökök felelősek az Azure-ral kialakított kapcsolatért. A `IoT Edge deployment.json` konfigurációt az Azure DEVOPS CI/CD használatával alkalmazhatja. A névtér és a IoT Edge kezelése a Cloud Operator használatával történik.

- **Azure arc-kompatibilis Kubernetes üzembe helyezése**: az Azure arc enabled Kubernetes egy hibrid felügyeleti eszköz, amely lehetővé teszi alkalmazások üzembe helyezését a Kubernetes-fürtökön. A Azure Stack Edge Pro-eszközön keresztül csatlakozik a Kubernetes-fürthöz `azure-arc namespace` . Az ebben a névtérben üzembe helyezett ügynökök felelősek az Azure-hoz való csatlakozásért. A központi telepítési konfigurációt a GitOps-alapú konfiguráció-kezelés használatával alkalmazhatja. 
    
    Az Azure arc-kompatibilis Kubernetes azt is lehetővé teszi, hogy a tárolók Azure Monitor használatával megtekinthesse és figyelje a fürtöt. További információért látogasson el az [Azure arc-kompatibilis Kubernetes?](../azure-arc/kubernetes/overview.md)című témakörre.
    
    Március 2021-én az Azure arc-kompatibilis Kubernetes általánosan elérhető lesz a felhasználók számára, és a szokásos használati díjak érvényesek. Az előzetes verziójú ügyfélként az Azure arc-kompatibilis Kubernetes az Azure Stack Edge-eszköz (ök) számára ingyenesen elérhető lesz. Az előzetes verziójú ajánlat kihasználása érdekében hozzon létre egy [support Request](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest):

    1. A **Probléma típusa** területen válassza a **Számlázás** lehetőséget.
    2. Az **Előfizetés** alatt válassza ki az előfizetését.
    3. A **szolgáltatás** területen válassza **a saját szolgáltatások**, majd az **Azure stack Edge** lehetőséget.
    4. Az **erőforrás** területen válassza ki az erőforrást.
    5. Az **Összefoglalás** területen adja meg a probléma leírását.
    6. A **probléma típusa** területen válassza a **váratlan díjak** lehetőséget.
    7. A **probléma altípusa** alatt válassza **az ingyenes próbaverzióban a díjak megismerése** lehetőséget.


## <a name="choose-the-deployment-type"></a>A központi telepítés típusának kiválasztása

Az alkalmazások központi telepítése során vegye figyelembe a következő információkat:

- Egy **vagy több típus**: egyetlen központi telepítési lehetőség vagy különböző telepítési lehetőségek kombinációja is kiválasztható.
- A felhő és a **helyi** környezet: az alkalmazástól függően a helyi telepítést a kubectl vagy a Cloud deployment használatával IoT Edge és az Azure arc segítségével választhatja ki. 
    - Ha helyi központi telepítést választ, arra a hálózatra korlátozódik, amelyben az Azure Stack Edge Pro-eszköz telepítve van.
    - Ha van olyan felhőalapú ügynöke, amelyet üzembe helyezhet, üzembe helyezheti a Felhőbeli operátort, és használhatja a felhőalapú felügyeletet.
- **IoT vs Azure arc**: az üzembe helyezés megválasztása a termék forgatókönyvének szándékával is függ. Ha olyan alkalmazásokat vagy tárolókat helyez üzembe, amelyek mélyebb integrációt végeznek a IoT vagy a IoT ökoszisztémával, válassza az IoT Edge lehetőséget az alkalmazások telepítéséhez. Ha már rendelkezik Kubernetes üzemelő példányokkal, az Azure arc az előnyben részesített választás lenne.


## <a name="next-steps"></a>Következő lépések

Ha helyileg szeretné telepíteni az alkalmazást a kubectl-on keresztül, olvassa el a következőt:

- [Állapot nélküli alkalmazás üzembe helyezése az Azure stack Edge Pro-n keresztül a kubectl-on keresztül](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).

Alkalmazás IoT Edgeon keresztüli üzembe helyezéséhez lásd:

- [Helyezzen üzembe egy minta-modult az Azure stack Edge Pro-ban IoT Edge használatával](azure-stack-edge-gpu-deploy-sample-module.md).

Az alkalmazások Azure arc használatával történő üzembe helyezéséhez lásd:

- [Alkalmazás üzembe helyezése az Azure arc használatával](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md).

---
title: A Kubernetes-fürt áttekintése Microsoft Azure Stack Edge Pro-eszközön | Microsoft Docs
description: Ismerteti, hogyan történik a Kubernetes megvalósítása a Azure Stack Edge Pro-eszközön.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 72ba07090e6ce67501761d97876aa136f146d61c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102437927"
---
# <a name="kubernetes-on-your-azure-stack-edge-pro-gpu-device"></a>Kubernetes az Azure Stack Edge Pro GPU-eszközön

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

A Kubernetes egy népszerű, nyílt forráskódú platform a tároló alkalmazások előkészítéséhez. Ez a cikk áttekintést nyújt a Kubernetes, majd leírja, hogy a Kubernetes hogyan működik a Azure Stack Edge Pro-eszközön. 

## <a name="about-kubernetes"></a>A Kubernetes névjegye 

A Kubernetes egyszerű és megbízható platformot biztosít a tároló-alapú alkalmazások és a hozzájuk társított hálózatkezelési és tárolási összetevők kezeléséhez. A Kubernetes segítségével gyorsan kialakíthatja, továbbíthatja és méretezheti a tároló alkalmazásokat.

Nyílt platformként a Kubernetes használatával hozhat létre alkalmazásokat az előnyben részesített programozási nyelvvel, az operációs rendszer könyvtáraival vagy az üzenetkezelési busszal. A kiadások bevezetéséhez és üzembe helyezéséhez a Kubernetes integrálható a meglévő folyamatos integrációs és folyamatos kézbesítési eszközökkel.

További információ: [how Kubernetes Works](https://www.youtube.com/watch?v=q1PcAawa4Bg&list=PLLasX02E8BPCrIhFrc_ZiINhbRkYMKdPT&index=2&t=0s).

## <a name="kubernetes-on-azure-stack-edge-pro"></a>Kubernetes Azure Stack Edge Pro-ban

A Azure Stack Edge Pro-eszközön létrehozhat egy Kubernetes-fürtöt a számítás konfigurálásával. Ha a számítási szerepkör konfigurálva van, a Kubernetes-fürt, beleértve a főkiszolgálói és a feldolgozó csomópontokat, minden üzembe van állítva és konfigurálva van. Ezt a fürtöt a rendszer felhasználja a munkaterhelések üzembe helyezéséhez `kubectl` , IoT Edge vagy Azure-ív használatával.

Az Azure Stack Edge Pro-eszköz 1 csomópontos konfigurációként érhető el, amely az infrastruktúra-fürtöt képezi. A Kubernetes-fürt elkülönül az infrastruktúra-fürttől, és az infrastruktúra-fürtön van üzembe helyezve. Az infrastruktúra-fürt biztosítja a Azure Stack Edge Pro-eszköz állandó tárterületét, miközben a Kubernetes-fürt kizárólag az alkalmazások előkészítésére szolgál. 

Ebben az esetben a Kubernetes-fürt fő csomópontja és egy feldolgozó csomópontja van. A fürtben található Kubernetes-csomópontok olyan virtuális gépek, amelyek az alkalmazásokat és a Felhőbeli munkafolyamatokat futtatják. 

A Kubernetes fő csomópontja felelős a fürt kívánt állapotának fenntartásáért. A fő csomópont a feldolgozó csomópontot is szabályozza, amely a tárolóban lévő alkalmazásokat futtatja. 

A következő ábra szemlélteti a Kubernetes megvalósítását egy 1 csomópontos Azure Stack Edge Pro-eszközön. Az 1 csomópontos eszköz nem nagyon elérhető, és ha az egyetlen csomópont meghibásodik, az eszköz leáll. A Kubernetes-fürt is leáll.

![Kubernetes architektúra egy 1 csomópontos Azure Stack Edge Pro-eszközhöz](media/azure-stack-edge-gpu-kubernetes-overview/kubernetes-architecture-1-node.png)

A Kubernetes-fürt architektúrával kapcsolatos további információkért látogasson el a [Kubernetes Core fogalmak](https://kubernetes.io/docs/concepts/architecture/)témakörre.


<!--The Kubernetes cluster control plane components make global decisions about the cluster. The control plane has:

- *kubeapiserver* that is the front end of the Kubernetes API and exposes the API.
- *etcd* that is a highly available key value store that backs up all the Kubernetes cluster data.
- *kube-scheduler* that makes scheduling decisions.
- *kube-controller-manager* that runs controller processes such as those for node controllers, replications controllers, endpoint controllers, and service account and token controllers. -->

## <a name="storage-volume-provisioning"></a>Tárolási kötetek kiépítés

Az alkalmazások számítási feladatainak támogatásához csatlakoztathatja az állandó adatok tárolási köteteit az Azure Stack Edge Pro-eszközök megosztásán. Statikus és dinamikus kötetek egyaránt használhatók. 

További információ: Storage-létesítési beállítások a Kubernetes-tárolóban lévő alkalmazások számára az [Azure stack Edge Pro-eszközhöz](azure-stack-edge-gpu-kubernetes-storage.md).

## <a name="networking"></a>Hálózatkezelés

A Kubernetes hálózatkezelés lehetővé teszi a Kubernetes-hálózaton belüli kommunikáció konfigurálását, többek között a tárolók közötti hálózatkezelést, a pod-Pod hálózatkezelést, a pod-szolgáltatást és az internetről a szolgáltatásba történő hálózatkezelést. További információ: hálózati modell a [Kubernetes hálózatkezelésben az Azure stack Edge Pro-eszközhöz](azure-stack-edge-gpu-kubernetes-networking.md).

## <a name="updates"></a>Frissítések

Mivel az új Kubernetes-verziók elérhetővé válnak, a fürt frissíthető az Azure Stack Edge Pro-eszközéhez elérhető standard frissítésekkel. A frissítésével kapcsolatos lépésekért tekintse meg [a Azure stack Edge Pro frissítéseinek alkalmazása](azure-stack-edge-gpu-install-update.md)című témakört.

## <a name="access-monitoring"></a>Hozzáférés, figyelés

A Kubernetes-fürt az Azure Stack Edge Pro-eszközön lehetővé teszi a Kubernetes szerepköralapú hozzáférés-vezérlését (Kubernetes RBAC). További információ: [Kubernetes szerepköralapú hozzáférés-vezérlés a Azure stack Edge Pro GPU-eszközön](azure-stack-edge-gpu-kubernetes-rbac.md).

A fürt és az erőforrások állapotát a Kubernetes irányítópulton is nyomon követheti. A tároló naplói is elérhetők. További információ: [a Kubernetes-irányítópult használata a Kubernetes-fürt állapotának figyelésére a Azure stack Edge Pro-eszközön](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).

A Azure Monitor a tárolók, csomópontok és vezérlők állapotának begyűjtésére szolgáló kiegészítő szolgáltatásként is elérhető. További információ: [Azure monitor áttekintése](../azure-monitor/overview.md)

<!--## Private container registry

Kubernetes on Azure Stack Edge Pro device allows for the private storage of your images by providing a local container registry.-->

## <a name="application-management"></a>Alkalmazáskezelés

Miután létrehozta a Kubernetes-fürtöt az Azure Stack Edge Pro-eszközön, a következő módszerek bármelyikével kezelheti a fürtön üzembe helyezett alkalmazásokat:

- Natív hozzáférés keresztül `kubectl`
- IoT Edge 
- Azure Arc

Ezeket a módszereket a következő részben ismertetjük.


### <a name="kubernetes-and-kubectl"></a>Kubernetes és kubectl

Miután telepítette a Kubernetes-fürtöt, a fürtön helyileg telepített alkalmazásokat az ügyfélgépről kezelheti. Az alkalmazásokkal való kommunikációhoz natív eszközt (például *kubectl* ) használhat a parancssorból. 

A Kubernetes-fürt üzembe helyezésével kapcsolatos további információkért látogasson el a [Kubernetes-fürt üzembe helyezése az Azure stack Edge Pro-eszközre](azure-stack-edge-gpu-create-kubernetes-cluster.md). További információ a felügyeletről: a [Kubectl használata a Kubernetes-fürt felügyeletéhez Azure stack Edge Pro-eszközön](azure-stack-edge-gpu-create-kubernetes-cluster.md).


### <a name="kubernetes-and-iot-edge"></a>Kubernetes és IoT Edge

A Kubernetes olyan Azure Stack Edge Pro-eszközön is integrálható IoT Edge számítási feladatokkal, ahol a Kubernetes méretezhető, az ökoszisztéma és a IoT pedig biztosítja a IoT központilag elérhető ökoszisztémáját. A Kubernetes réteg infrastruktúra-rétegként szolgál Azure IoT Edge számítási feladatok telepítéséhez. A modul élettartamát és a hálózati terheléselosztást a Kubernetes felügyeli, míg az Edge-alkalmazás platformját a IoT Edge felügyeli.

Az alkalmazások Kubernetes-fürtön IoT Edge használatával történő központi telepítésével kapcsolatos további információkért lépjen a következőre: 

- [Állapot nélküli alkalmazások közzététele Azure stack Edge Pro-eszközön IoT Edge használatával](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md).


### <a name="kubernetes-and-azure-arc"></a>Kubernetes és az Azure arc

Az Azure arc egy hibrid felügyeleti eszköz, amely lehetővé teszi, hogy alkalmazásokat helyezzen üzembe a Kubernetes-fürtökön. Az Azure arc azt is lehetővé teszi, hogy a tárolók Azure Monitor használatával megtekinthesse és figyelje a fürtöket. További információért látogasson el a [Mi az Azure-Arc enabled Kubernetes?](../azure-arc/kubernetes/overview.md)című témakörben. Az Azure arc díjszabásával kapcsolatos információkért látogasson el az [Azure arc díjszabására](https://azure.microsoft.com/services/azure-arc/#pricing).

Március 2021-én az Azure arc-kompatibilis Kubernetes általánosan elérhető lesz a felhasználók számára, és a szokásos használati díjak érvényesek. Az előzetes verziójú ügyfélként az Azure arc-kompatibilis Kubernetes az Azure Stack Edge-eszköz (ök) számára ingyenesen elérhető lesz. Az előzetes verziójú ajánlat kihasználása érdekében hozzon létre egy [support Request](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest):

1. A **Probléma típusa** területen válassza a **Számlázás** lehetőséget.
2. Az **Előfizetés** alatt válassza ki az előfizetését.
3. A **szolgáltatás** területen válassza **a saját szolgáltatások**, majd az **Azure stack Edge** lehetőséget.
4. Az **erőforrás** területen válassza ki az erőforrást.
5. Az **Összefoglalás** területen adja meg a probléma leírását.
6. A **probléma típusa** területen válassza a **váratlan díjak** lehetőséget.
7. A **probléma altípusa** alatt válassza **az ingyenes próbaverzióban a díjak megismerése** lehetőséget.


## <a name="next-steps"></a>Következő lépések

- További információ a Kubernetes Storage szolgáltatásról [Azure stack Edge Pro-eszközön](azure-stack-edge-gpu-kubernetes-storage.md).
- A Kubernetes hálózati modell megismerése [Azure stack Edge Pro-eszközön](azure-stack-edge-gpu-kubernetes-networking.md).
- [Azure stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md) üzembe helyezése Azure Portalban.

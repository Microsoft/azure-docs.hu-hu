---
title: Az Azure Stack Edge Pro-eszköz monitorozása a Kubernetes-irányítópulton keresztül | Microsoft Docs
description: Ismerteti, hogyan érheti el és használhatja a Kubernetes irányítópultot az Azure Stack Edge Pro-eszköz monitorozásához.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 19b3595228c29814e42af88696972fd81b156190
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443046"
---
# <a name="use-kubernetes-dashboard-to-monitor-your-azure-stack-edge-pro-gpu-device"></a>A Kubernetes-irányítópult használata az Azure Stack Edge Pro GPU-eszköz figyeléséhez

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Ez a cikk azt ismerteti, hogyan érheti el és használhatja a Kubernetes irányítópultot a Azure Stack Edge Pro GPU-eszköz monitorozásához. Az eszköz figyeléséhez használhatja a Azure Portal diagramokat, megtekintheti a Kubernetes irányítópultját, vagy futtathat `kubectl` parancsokat az eszköz PowerShell-felületén keresztül. 

Ez a cikk csak a Kubernetes-irányítópulton végrehajtható figyelési feladatokra koncentrál.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
>
> * A Kubernetes-irányítópult elérése az eszközön
> * Az eszközön üzembe helyezett modulok megtekintése
> * Az eszközön üzembe helyezett alkalmazások IP-címének lekérése
> * Az eszközön üzembe helyezett modulok tároló-naplófájljainak megtekintése


## <a name="about-kubernetes-dashboard"></a>Tudnivalók a Kubernetes-irányítópultról

A Kubernetes-irányítópult egy webes felhasználói felület, amely a tárolóba helyezett alkalmazások hibaelhárításához használható. A Kubernetes irányítópult a Kubernetes parancssorának felhasználói felületén alapuló alternatívája `kubectl` . További információkért lásd: [Kubernetes-irányítópult](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/). 

Az Azure Stack Edge Pro-eszközön *csak olvasható* módban használhatja a Kubernetes irányítópultot, hogy áttekintést kapjon az Azure stack Edge Pro-eszközön futó alkalmazásokról, megtekintheti a Kubernetes-fürt erőforrásainak állapotát, és megtekintheti az eszközön bekövetkezett hibákat.

## <a name="access-dashboard"></a>Hozzáférés az irányítópulthoz

A Kubernetes irányítópult írásvédett, és az 31000 *-* es porton futtatja a Kubernetes fő csomópontját. Az irányítópult eléréséhez kövesse az alábbi lépéseket: 

1. Az eszköz helyi felhasználói felületén nyissa meg az **eszközt** , majd válassza az **eszköz végpontok** lehetőséget. 
1. Válassza a **konfiguráció letöltése** lehetőséget a letöltéséhez `kubeconfig` , amely lehetővé teszi az irányítópult elérését. Mentse a `config.json` fájlt a helyi rendszerbe.
1. A Kubernetes irányítópult URL-címére kattintva nyissa meg az irányítópultot egy böngészőben.

    ![Kubernetes irányítópult URL-címe az eszköz lapján helyi felhasználói felületen](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-url-local-ui-1.png)

1. A **Kubernetes-irányítópult bejelentkezési** oldalán:
    
    1. Válassza a **kubeconfig** lehetőséget. 
        ![Válassza a kubeconfig lehetőséget](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-sign-in-1.png) 
    1. Válassza ki a három pontot **.**.. Tallózással keresse meg a `kubeconfig` korábban letöltött helyi rendszerét, és mutasson rá. Válassza a **Bejelentkezés** lehetőséget.
        ![Tallózással keresse meg a kubeconfig fájlt](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-sign-in-2.png)    

6. Most már csak olvasható módban tekintheti meg az Azure Stack Edge Pro-eszköz Kubernetes irányítópultját.

    ![Kubernetes-irányítópult főoldala](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-main-page-1.png)

## <a name="view-module-status"></a>Modul állapotának megtekintése

A számítási modulok olyan tárolók, amelyeken a rendszer üzleti logikát alkalmaz. Az irányítópulton ellenőrizheti, hogy a számítási modul üzembe helyezése sikeresen megtörtént-e a Azure Stack Edge Pro-eszközön.

A modul állapotának megtekintéséhez kövesse az alábbi lépéseket az irányítópulton:

1. Az irányítópult bal oldali ablaktáblájában lépjen a **névtér** elemre. Szűrje azt a névteret, amelyben IoT Edge modulok jelennek meg, ebben az esetben a **iotedge**.
1. A bal oldali ablaktáblán lépjen a **Munkaterhelések > központi telepítések** elemre.
1. A jobb oldali panelen megjelenik az eszközre telepített összes modul. Ebben az esetben egy GettingStartedWithGPU-modul lett üzembe helyezve a Azure Stack Edge Pro-ban. Láthatja, hogy a modul üzembe lett helyezve.

    ![Modul központi telepítésének megtekintése](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-module-deployment-1.png)

 
## <a name="get-ip-address-for-services-or-modules"></a>Szolgáltatások vagy modulok IP-címének lekérése

Az irányítópult használatával lekérheti azon szolgáltatások vagy modulok IP-címeit, amelyeket ki szeretne állítani a Kubernetes-fürtön kívül. 

A külső szolgáltatások IP-tartományát az eszköz helyi webes FELÜLETén keresztül rendelheti hozzá a **számítási hálózati beállítások** lapon. IoT Edge modulok üzembe helyezésekor előfordulhat, hogy egy adott modulhoz vagy szolgáltatáshoz rendelt IP-címet szeretné beszerezni. 

Az IP-cím lekéréséhez kövesse az alábbi lépéseket az irányítópulton:

1. Az irányítópult bal oldali ablaktáblájában lépjen a **névtér** elemre. Szűrje azt a névteret, amelyben a külső szolgáltatás telepítve van, ebben az esetben a **iotedge**.
1. A bal oldali ablaktáblán lépjen a **felderítés és terheléselosztás > szolgáltatások** elemre.
1. A jobb oldali panelen a `iotedge` Azure stack Edge Pro-eszközön található névtérben futó összes szolgáltatást látni fogja.

    ![Külső szolgáltatások IP-címének beolvasása](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-get-ip-external-service-1.png)

## <a name="view-container-logs"></a>Tárolónaplók megtekintése

Vannak olyan példányok, ahol meg kell tekintenie a tároló naplóit. Az irányítópult használatával lekérheti a Kubernetes-fürtön üzembe helyezett adott tárolóhoz tartozó naplókat.

A tároló naplófájljainak megtekintéséhez kövesse az alábbi lépéseket az irányítópulton:

1. Az irányítópult bal oldali ablaktáblájában lépjen a **névtér** elemre. Szűrje a névteret, ahol a IoT Edge modulokat üzembe helyezi, ebben az esetben a **iotedge**.
1. A bal oldali ablaktáblán lépjen a **Munkaterhelések > hüvelyek** elemre.
1. A jobb oldali ablaktáblán látni fogja az eszközön futó összes hüvelyt. Azonosítsa a modult futtató Pod-t, amelyre vonatkozóan meg szeretné tekinteni a naplókat. Válassza ki az azonosított Pod függőleges három pontját, és a helyi menüben válassza a **naplók** lehetőséget.

    ![Tárolói naplók megtekintése 1](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)

1. A naplók az irányítópultba beépített naplók megjelenítőben jelennek meg. Letöltheti a naplókat is.

    ![Tárolói naplók megtekintése 2](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)
    

## <a name="view-cpu-memory-usage"></a>CPU megtekintése, memóriahasználat

A Azure Stack Edge Pro-eszközhöz készült Kubernetes-irányítópult olyan [metrikai kiszolgáló-bővítményt](https://kubernetes.io/docs/tasks/debug-application-cluster/resource-metrics-pipeline/) is tartalmaz, amely összesíti a CPU-és memóriahasználat a Kubernetes-erőforrások között.
 
Megtekintheti például a PROCESSZORt és a memóriát a központi telepítések között az összes névtérben. 

![A processzor és a memória használatának megtekintése az összes üzemelő példányon](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/view-cpu-memory-all-1.png)

Egy adott névtér alapján is szűrhető. A következő példában megtekintheti a CPU-és a memória-használatot csak az Azure arc üzemelő példányok esetében.  

![Az Azure arc üzemelő példányok CPU-és memóriahasználat megtekintése](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/view-cpu-memory-azure-arc-1.png)

Az Kubernetes mérőszámok kiszolgálója olyan automatikus skálázási folyamatokat biztosít, mint például a [horizontális Pod automatikus méretezés](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/).


## <a name="next-steps"></a>Következő lépések

Útmutató az [eszközök problémáinak elhárításához](azure-stack-edge-gpu-troubleshoot.md).

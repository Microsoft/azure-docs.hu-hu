---
title: PHP-alkalmazás üzembe helyezése `Guestbook` az arc-kompatibilis Kubernetes Azure stack Edge Pro GPU-eszközön | Microsoft Docs
description: Leírja, hogyan helyezhet üzembe egy PHP `Guestbook` állapot nélküli alkalmazást a Redis használatával a GitOps-mel a Azure stack Edge Pro-eszközön lévő arc-kompatibilis Kubernetes-fürtön.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 920f7912a1289fe92618d893b94943784e4a9a3a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102520727"
---
# <a name="deploy-a-php-guestbook-stateless-application-with-redis-on-arc-enabled-kubernetes-cluster-on-azure-stack-edge-pro-gpu"></a>PHP állapot nélküli alkalmazás üzembe helyezése a `Guestbook` Redis on arc enabled Kubernetes-fürtön Azure stack Edge Pro GPU-val

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Ez a cikk bemutatja, hogyan hozhat létre és helyezhet üzembe egy egyszerű, többrétegű webes alkalmazást a Kubernetes és az Azure arc használatával. Ez a példa a következő összetevőkből áll:

- Egypéldányos Redis-főkiszolgáló a bejegyzések tárolásához `guestbook`
- Több replikált Redis-példány az olvasások kiszolgálására
- Több webes frontend-példány

Az üzembe helyezés a Azure Stack Edge Pro-eszközön a GitOps használatával történik az arc-kompatibilis Kubernetes-fürtön. 

Ez az eljárás azoknak a felhasználóknak készült, akik áttekintették a Kubernetes számítási feladatait [Azure stack Edge Pro-eszközön](azure-stack-edge-gpu-kubernetes-workload-management.md) , és megismerhetik az [Azure arc-kompatibilis Kubernetes (előzetes verzió)](../azure-arc/kubernetes/overview.md)fogalmait.

> [!NOTE]
> Ez a cikk a Slave kifejezésre mutató hivatkozásokat tartalmaz, amelyek egy kifejezés, amelyet a Microsoft már nem használ. Ha a rendszer eltávolítja a kifejezést a szoftverből, azt a cikkből távolítjuk el.

## <a name="prerequisites"></a>Előfeltételek

Az állapot nélküli alkalmazás üzembe helyezése előtt győződjön meg arról, hogy végrehajtotta a következő előfeltételeket az eszközön és az ügyfélen, amelyet az eszköz eléréséhez fog használni:

> [!NOTE]
> Ez a cikk a Slave kifejezésre mutató hivatkozásokat tartalmaz, amelyek egy kifejezés, amelyet a Microsoft már nem használ. Ha a rendszer eltávolítja a kifejezést a szoftverből, azt a cikkből távolítjuk el.

### <a name="for-device"></a>Az eszköz esetén

1. A bejelentkezési hitelesítő adatok egy 1 csomópontos Azure Stack Edge Pro-eszközhöz tartoznak.
    1. Az eszköz aktiválva van. Lásd: [az eszköz aktiválása](azure-stack-edge-gpu-deploy-activate.md).
    1. Az eszközön a Azure Portal-n keresztül konfigurált számítási szerepkör és egy Kubernetes-fürt van konfigurálva. Lásd: [számítás konfigurálása](azure-stack-edge-gpu-deploy-configure-compute.md).

1. Engedélyezte az Azure arc használatát a meglévő Kubernetes-fürtön az eszközön, és a Azure Portal megfelelő Azure arc-erőforrással rendelkezik. A részletes lépésekért lásd: az [Azure arc engedélyezése Azure stack Edge Pro-eszközön](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md).

### <a name="for-client-accessing-the-device"></a>Az eszközt elérő ügyfél

1. Van egy Windows-ügyfélrendszer, amely az Azure Stack Edge Pro-eszköz elérésére szolgál majd.
  
    - Az ügyfél Windows PowerShell 5,0-es vagy újabb verzióját futtatja. A Windows PowerShell legújabb verziójának letöltéséhez nyissa meg a következőt: [install Windows PowerShell](/powershell/scripting/install/installing-windows-powershell).
    
    - Bármely más ügyfél [támogatott operációs rendszerrel](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) is rendelkezhet. Ez a cikk a Windows-ügyfelek használatakor követendő eljárást ismerteti. 
    
1. Végrehajtotta az [Azure stack Edge Pro-eszközön a Kubernetes-fürt eléréséhez](azure-stack-edge-gpu-create-kubernetes-cluster.md)című témakörben leírt eljárást. A következőket teheti:
    
    - Telepítve van `kubectl` az ügyfélen. <!--and saved the `kubeconfig` file with the user configuration to C:\\Users\\&lt;username&gt;\\.kube. -->
    
    - Győződjön meg arról, hogy az `kubectl` ügyfél verziószáma nem több, mint egy, a Azure stack Edge Pro-eszközön futó Kubernetes-verzió. 
      - Ezzel a paranccsal `kubectl version` ellenőrizhető az ügyfélen futó kubectl verziója. Jegyezze fel a teljes verziót.
      - Az Azure Stack Edge Pro-eszköz helyi felhasználói felületén lépjen az **Áttekintés** elemre, és jegyezze fel a Kubernetes-szoftver számát. 
      - Ellenőrizze ezt a két verziót a támogatott Kubernetes-verzióban megadott leképezéssel való kompatibilitás érdekében. <!--insert link-->

1. Rendelkezik egy [GitOps-konfigurációval, amely Azure arc-telepítés futtatására használható](https://github.com/kagoyal/dbehaikudemo). Ebben a példában a következő fájlokat fogja használni a `yaml` Azure stack Edge Pro-eszközön való üzembe helyezéshez.

    - `frontend-deployment.yaml`<!-- - The guestbook application has a web frontend serving the HTTP requests written in PHP. It is configured to connect to the redis-master Service for write requests and the redis-slave service for Read requests. This file describes a deployment that runs the frontend of the guestbook application.-->
    - `frontend-service.yaml` <!-- - This allows you to configure an externally visible frontend Service that can be accessed from outside the Kubernetes cluster on your device.-->
    - `redis-master-deployment.yaml` <!-- - This deployment file runs a single replica Redis master Pod. The guestbook application uses Redis to store its data. It writes its data to a Redis master instance and reads data from multiple Redis slave instances.-->
    - `redis-master-service.yaml` <!-- - The guestbook application needs to communicate to the Redis master to write its data. You need to apply a Service to proxy the traffic to the Redis master Pod. A Service defines a policy to access the Pods.-->
    - `redis-slave-deployment.yaml` <!-- - Although the Redis master is a single pod, you can make it highly available to meet traffic demands by adding replica Redis slaves. The Redis Slave Deployment specifies two replicas.-->
    - `redis-slave-service.yaml` <!-- - The guestbook application needs to communicate to Redis slaves to read data. To make the Redis slaves discoverable, you need to set up a Service. A Service provides transparent load balancing to a set of Pods.-->

<!-- az cli version requirements-->


## <a name="deploy-configuration"></a>Az üzembe helyezés konfigurálása

A következő lépésekkel konfigurálhatja az Azure arc-erőforrást a GitOps-konfiguráció üzembe helyezéséhez a Azure Portal használatával: 

1. A Azure Portal nyissa meg az Azure arc-erőforrást, amelyet akkor hozott létre, amikor engedélyezte az Azure arc használatát a Kubernetes-fürtön az eszközön. 

    ![Ugrás az Azure arc-erőforrásra](media/azure-stack-edge-gpu-connect-powershell-interface/verify-azure-arc-enabled-1.png)

1. Lépjen a **konfigurációk** elemre, és válassza a **+ konfiguráció hozzáadása** elemet.

    ![A képernyőképen az Azure arc használatára képes Kubernetes-fürt látható a konfiguráció hozzáadása lehetőség kiválasztásával.](media/azure-stack-edge-gpu-connect-powershell-interface/select-configurations-1.png)

1. A **konfiguráció hozzáadása** mezőben adja meg a mezők megfelelő értékeit, majd kattintson az **alkalmaz** gombra.

    |Paraméter  |Leírás |
    |---------|---------|
    |Konfiguráció neve     | A konfigurációs erőforrás neve.        |
    |Operátor példányának neve     |Az operátor példányának neve egy adott konfiguráció azonosításához. A név a legfeljebb 253 karakterből álló karakterlánc, amely csak kisbetűket, alfanumerikus karaktereket, kötőjelet és pontot tartalmazhat.         |
    |Operátor névtere     | Állítsa a **demotestguestbook** értékre úgy, hogy az megfeleljen az üzemelő példányban megadott névtérnek `yaml` . <br> A mező határozza meg azt a névteret, amelyben a kezelő telepítve van. A név a legfeljebb 253 karakterből álló karakterlánc, amely csak kisbetűket, alfanumerikus karaktereket, kötőjelet és pontot tartalmazhat.         |
    |Tárház URL-címe     |<br>A git-tárház elérési útja `http://github.com/username/repo` vagy `git://github.com/username/repo` formátuma, ahol a GitOps-konfiguráció található.         |
    |Operátor hatóköre     | Válassza a **névtér** lehetőséget. <br>Ez a paraméter határozza meg azt a hatókört, amelyen a kezelő telepítve van. Válassza a névtér lehetőséget az operátor telepítéséhez a telepítési YAML-fájlokban megadott névtérben.       |
    |Operátor típusa     | Alapértelmezés szerint hagyja. <br>Ez a paraméter határozza meg az operátor típusát – alapértelmezés szerint a Flux értékre állítva.        |
    |Operátori paraméterek     | Hagyja üresen. <br>Ez a paraméter a Flux-kezelőnek átadandó paramétereket tartalmaz.        |
    |Helm     | Állítsa a paramétert **Letiltva** értékre. <br>Akkor engedélyezze ezt a beállítást, ha diagram alapú központi telepítéseket hajt végre.        |


    ![Konfiguráció hozzáadása](media/azure-stack-edge-gpu-connect-powershell-interface/add-configuration-1.png)


1. A konfiguráció központi telepítése elindul, és a **kezelő állapota** **függőben** jelenik meg. 

    ![Képernyőfelvétel: az Azure arc-kompatibilis Kubernetes-fürt függő állapotban van, mivel frissül.](media/azure-stack-edge-gpu-connect-powershell-interface/view-configurations-1.png)

1. Az üzembe helyezés pár percet vesz igénybe. Az üzembe helyezés befejeztével a **kezelő állapota** **telepítettként** jelenik meg.

    ![Képernyőfelvétel: az Azure arc-kompatibilis Kubernetes-fürt telepített állapotban van.](media/azure-stack-edge-gpu-connect-powershell-interface/view-configurations-2.png)

## <a name="verify-deployment"></a>Az üzembe helyezés ellenőrzése

A GitOps-konfiguráción keresztüli központi telepítés létrehoz egy névteret a git-tárházban `demotestguestbook` található központi telepítési `yaml` fájlokban megadott módon.

1. A GitOps-konfiguráció alkalmazása után [kapcsolódjon az eszköz PowerShell-felületéhez](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. Futtassa a következő parancsot az üzemelő `demotestguestbook` példányhoz tartozó névtérben futó hüvelyek listázásához.

    `kubectl get pods -n <your-namespace>`
  
    Íme egy minta kimenet.
  
    ```powershell
    [10.128.44.240]: PS>kubectl get pods -n demotestguestbook
    NAME                            READY   STATUS    RESTARTS   AGE
    aseoperator1-5569658644-cqtb5   1/1     Running   0          91m
    frontend-6cb7f8bd65-4xb4f       1/1     Running   0          91m
    frontend-6cb7f8bd65-q9cxj       1/1     Running   0          91m
    frontend-6cb7f8bd65-xpzs6       1/1     Running   0          91m
    memcached-86bdf9f56b-5l2fq      1/1     Running   0          91m
    redis-master-7db7f6579f-2z29w   1/1     Running   0          91m
    redis-slave-7664787fbc-lgr2n    1/1     Running   0          91m
    redis-slave-7664787fbc-vlvzn    1/1     Running   0          91m
    [10.128.44.240]: PS>
    ```  

1. Ebben a példában a frontend szolgáltatást a következő típusként telepítettük: terheléselosztó. Meg kell keresnie ennek a szolgáltatásnak az IP-címét a megtekintéséhez `guestbook` . Futtassa az alábbi parancsot.

    `kubectl get service -n <your-namespace>`
    
    ```powershell
    [10.128.44.240]: PS>kubectl get service -n demotestguestbook
    NAME           TYPE           CLUSTER-IP       EXTERNAL-IP     PORT(S)        AGE
    frontend       LoadBalancer   10.96.79.38      10.128.44.245   80:31238/TCP   85m
    memcached      ClusterIP      10.102.47.75     <none>          11211/TCP      85m
    redis-master   ClusterIP      10.104.32.99     <none>          6379/TCP       85m
    redis-slave    ClusterIP      10.104.215.146   <none>          6379/TCP       85m
    [10.128.44.240]: PS>
    ```
1. A előtér-szolgáltatás `type:LoadBalancer` külső IP-címmel rendelkezik. Ez az IP-cím a külső szolgáltatásokhoz megadott IP-címtartomány alapján történik a számítási hálózati beállítások az eszközön való konfigurálásakor. Ezzel az IP-címmel megtekintheti az `guestbook` at URL-címét: `https://<external-IP-address>` .

    ![Vendégkönyv megtekintése](media/azure-stack-edge-gpu-connect-powershell-interface/view-guestbook-1.png)

## <a name="delete-deployment"></a>Központi telepítés törlése

A központi telepítés törléséhez törölheti a konfigurációt a Azure Portalból. A konfiguráció törlése törli a létrehozott objektumokat, beleértve a központi telepítéseket és a szolgáltatásokat is.

1. A Azure Portalban nyissa meg az Azure arc-erőforrás > konfigurációit. 
1. Keresse meg a törölni kívánt konfigurációt. Válassza a... a helyi menü meghívásához és a **Törlés** lehetőség kiválasztásához.
    ![Konfiguráció törlése](media/azure-stack-edge-gpu-connect-powershell-interface/delete-configuration-1.png)

A konfiguráció törléséhez több percet is igénybe vehet.
 
<!--```powershell
kubectl delete deployment <deployment-name> -n <your-namespace>
kubectl delete service <service-name> -n <your-namespace>
```

Here is a sample output of when you delete the deployments and the services.

```powershell
C:\Users\user>kubectl delete deployment,svc mysql -n userns1
deployment.apps "mysql" deleted
service "mysql" deleted
C:\Users\user>
```-->


## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [figyelheti a Kubernetes irányítópultot a Azure stack Edge Pro-eszközön üzemelő példányok figyelésére](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)
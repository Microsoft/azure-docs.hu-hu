---
title: Microsoft Azure Stack Edge Pro GPU-eszköz csatlakoztatása és kezelése a Windows PowerShell felületén keresztül | Microsoft Docs
description: Ismerteti, hogyan csatlakozhat, majd felügyelheti Azure Stack Edge Pro GPU-t a Windows PowerShell felületén keresztül.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 580e5aab7b7ac1edcfee58345291afcb9eb0e977
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103562161"
---
# <a name="manage-an-azure-stack-edge-pro-gpu-device-via-windows-powershell"></a>Azure Stack Edge Pro GPU-eszköz kezelése a Windows PowerShell használatával

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro-megoldás lehetővé teszi az adatfeldolgozást és a hálózaton keresztüli küldését az Azure-ba. Ez a cikk az Azure Stack Edge Pro-eszköz konfigurációs és felügyeleti feladatait ismerteti. Az eszköz kezeléséhez használhatja a Azure Portal, a helyi webes felhasználói felületet vagy a Windows PowerShell felületét is.

Ebből a cikkből megtudhatja, hogyan kapcsolódhat az eszköz PowerShell-felületéhez és az ezzel a felülettel elvégezhető feladatokhoz. 


## <a name="connect-to-the-powershell-interface"></a>Csatlakozás a PowerShell-felülethez

[!INCLUDE [Connect to admin runspace](../../includes/azure-stack-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Támogatási csomag létrehozása

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]


## <a name="view-device-information"></a>Eszköz adatainak megtekintése
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="view-gpu-driver-information"></a>GPU-illesztőprogram adatainak megtekintése

Ha a számítási szerepkör konfigurálva van az eszközön, a GPU-illesztőprogram adatait a PowerShell felületén keresztül is lekérheti. 

1. [Kapcsolódjon a PowerShell felületéhez](#connect-to-the-powershell-interface).
2. A használatával `Get-HcsGpuNvidiaSmi` lekérheti az eszköz GPU-illesztőprogramjának adatait.

    A következő példa a parancsmag használatát mutatja be:

    ```powershell
    Get-HcsGpuNvidiaSmi
    ```
    Jegyezze fel az illesztőprogram adatait a parancsmag mintájának kimenetéről.

    ```powershell    
    +-----------------------------------------------------------------------------+    
    | NVIDIA-SMI 440.64.00    Driver Version: 440.64.00    CUDA Version: 10.2     |    
    |-------------------------------+----------------------+----------------------+    
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |    
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |    
    |===============================+======================+======================|    
    |   0  Tesla T4            On   | 000029CE:00:00.0 Off |                    0 |    
    | N/A   60C    P0    29W /  70W |   1539MiB / 15109MiB |      0%      Default |    
    +-------------------------------+----------------------+----------------------+    
    |   1  Tesla T4           On  | 0000AD50:00:00.0 Off |                    0 |
    | N/A   58C    P0    29W /  70W |    330MiB / 15109MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    ```

## <a name="enable-multi-process-service-mps"></a>Többfolyamatos szolgáltatás (MPS) engedélyezése

A többfolyamatos szolgáltatás (mp) az NVIDIA GPU-ban olyan mechanizmust biztosít, amelyben több feladat is megoszthatja a GPU-kat, ahol az egyes feladatok a GPU erőforrásainak bizonyos hányadát foglalják magukban. Az MPS a Azure Stack Edge Pro GPU-eszköz előzetes verziójának funkciója. Az alábbi lépéseket követve engedélyezheti az MPS-t az eszközön:

[!INCLUDE [Enable MPS](../../includes/azure-stack-edge-gateway-enable-mps.md)]


## <a name="reset-your-device"></a>Eszköz alaphelyzetbe állítása

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Számítási naplók beolvasása

Ha a számítási szerepkör konfigurálva van az eszközön, a számítási naplókat a PowerShell felületén keresztül is lekérheti.

1. [Kapcsolódjon a PowerShell felületéhez](#connect-to-the-powershell-interface).
2. Az `Get-AzureDataBoxEdgeComputeRoleLogs` eszközhöz tartozó számítási naplók beszerzéséhez használja a következőt:.

    A következő példa a parancsmag használatát mutatja be:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection    
    ```

    Itt látható a parancsmaghoz használt paraméterek leírása:
    - `Path`: Adja meg annak a megosztásnak a hálózati elérési útját, ahol létre szeretné hozni a számítási napló csomagot.
    - `Credential`: Adja meg a hálózati megosztáshoz tartozó felhasználónevet. A parancsmag futtatásakor meg kell adnia a megosztási jelszót.
    - `FullLogCollection`: Ez a paraméter biztosítja, hogy a naplófájl tartalmazza az összes számítási naplót. Alapértelmezés szerint a naplófájl csak a naplók egy részhalmazát tartalmazza.


## <a name="change-kubernetes-pod-and-service-subnets"></a>Kubernetes-pod és szolgáltatási alhálózatok módosítása

Alapértelmezés szerint a Kubernetes az Azure Stack Edge-eszközön a 172.27.0.0/16 és a 172.28.0.0/16 alhálózatokat használja a pod és a Service-hez. Ha ezek az alhálózatok már használatban vannak a hálózatban, akkor a parancsmag futtatásával `Set-HcsKubeClusterNetworkInfo` módosíthatja ezeket az alhálózatokat.

Ezt a konfigurációt a Azure Portal számítási beállításainak konfigurálása előtt szeretné elvégezni, mivel ebben a lépésben a Kubernetes-fürt jön létre.

1. [Kapcsolódjon az eszköz PowerShell-felületéhez](#connect-to-the-powershell-interface).
1. Az eszköz PowerShell-felületéről futtassa a következőt:

    `Set-HcsKubeClusterNetworkInfo -PodSubnet <subnet details> -ServiceSubnet <subnet details>`

    Cserélje le a <subnet details> -t a használni kívánt alhálózati tartományra. 

1. A parancs futtatása után a `Get-HcsKubeClusterNetworkInfo` paranccsal ellenőrizheti, hogy megváltozott-e a pod és a szolgáltatás alhálózatai.

Íme egy minta kimenet a parancshoz.

```powershell
[10.100.10.10]: PS>Set-HcsKubeClusterNetworkInfo -PodSubnet 10.96.0.1/16 -ServiceSubnet 10.97.0.1/16
[10.100.10.10]: PS>Get-HcsKubeClusterNetworkInfo

Id                                   PodSubnet    ServiceSubnet
--                                   ---------    -------------
6dbf23c3-f146-4d57-bdfc-76cad714cfd1 10.96.0.1/16 10.97.0.1/16
[10.100.10.10]: PS>
```

## <a name="debug-kubernetes-issues-related-to-iot-edge"></a>Kubernetes kapcsolatos hibák elhárítása IoT Edge

Mielőtt elkezdené, a következőket kell tennie:

- A számítási hálózat konfigurálva van. Lásd [: a Network for Azure stack Edge Pro és a GPU közötti konfigurálásának bemutatója](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
- Az eszközön konfigurált számítási szerepkör.
    
Olyan Azure Stack Edge Pro-eszközön, amelyen a számítási szerepkör konfigurálva van, az eszközt két különböző paranccsal lehet elhárítani vagy figyelni.

- `iotedge`Parancsok használata. Ezek a parancsok alapszintű műveletekhez érhetők el az eszközön.
- `kubectl`Parancsok használata. Ezek a parancsok az eszközhöz tartozó műveletek széles köréhez érhetők el.

A fenti parancsok egyikének végrehajtásához [kapcsolódnia kell a PowerShell felületéhez](#connect-to-the-powershell-interface).

### <a name="use-iotedge-commands"></a>`iotedge`Parancsok használata

Az elérhető parancsok listájának megtekintéséhez [kapcsolódjon a PowerShell-felülethez](#connect-to-the-powershell-interface) , és használja a `iotedge` függvényt.

```powershell
[10.100.10.10]: PS>iotedge -?                                                                                                                           
Usage: iotedge COMMAND

Commands:
   list
   logs
   restart

[10.100.10.10]: PS>
```

A következő táblázat a rendelkezésre álló parancsok rövid leírását tartalmazza `iotedge` :

|command  |Leírás |
|---------|---------|
|`list`     | Modulok listázása         |
|`logs`     | Modul naplóinak beolvasása        |
|`restart`     | Modul leállítása és újraindítása         |

#### <a name="list-all-iot-edge-modules"></a>Az összes IoT Edge-modul listázása

Az eszközön futó összes modul listázásához használja az `iotedge list` parancsot.

Íme egy példa a parancs kimenetére. Ez a parancs felsorolja az összes modult, a társított konfigurációt és a modulokhoz társított külső IP-címeket. A **webkiszolgáló** alkalmazást például a következő helyen érheti el: `https://10.128.44.244` . 


```powershell
[10.100.10.10]: PS>iotedge list

NAME                   STATUS  DESCRIPTION CONFIG                                             EXTERNAL-IP
----                   ------  ----------- ------                                             -----
gettingstartedwithgpus Running Up 10 days  mcr.microsoft.com/intelligentedge/solutions:latest
iotedged               Running Up 10 days  azureiotedge/azureiotedge-iotedged:0.1.0-beta10    <none>
edgehub                Running Up 10 days  mcr.microsoft.com/azureiotedge-hub:1.0             10.128.44.243
edgeagent              Running Up 10 days  azureiotedge/azureiotedge-agent:0.1.0-beta10
webserverapp           Running Up 10 days  nginx:stable                                       10.128.44.244

[10.100.10.10]: PS>
```
#### <a name="restart-modules"></a>Modulok újraindítása

A `list` parancs használatával listázhatja az eszközön futó összes modult. Ezután azonosítsa annak a modulnak a nevét, amelyet újra szeretne indítani, és használja azt a `restart` paranccsal.

Íme egy minta kimenet, amely bemutatja, hogyan lehet újraindítani a modult. Annak leírása alapján, hogy mennyi ideig fut a modul, láthatja, hogy az `cuda-sample1` újraindult.

```powershell
[10.100.10.10]: PS>iotedge list

NAME         STATUS  DESCRIPTION CONFIG                                          EXTERNAL-IP PORT(S)
----         ------  ----------- ------                                          ----------- -------
edgehub      Running Up 5 days   mcr.microsoft.com/azureiotedge-hub:1.0          10.57.48.62 443:31457/TCP,5671:308
                                                                                             81/TCP,8883:31753/TCP
iotedged     Running Up 7 days   azureiotedge/azureiotedge-iotedged:0.1.0-beta13 <none>      35000/TCP,35001/TCP
cuda-sample2 Running Up 1 days   nvidia/samples:nbody
edgeagent    Running Up 7 days   azureiotedge/azureiotedge-agent:0.1.0-beta13
cuda-sample1 Running Up 1 days   nvidia/samples:nbody

[10.100.10.10]: PS>iotedge restart cuda-sample1
[10.100.10.10]: PS>iotedge list

NAME         STATUS  DESCRIPTION  CONFIG                                          EXTERNAL-IP PORT(S)
----         ------  -----------  ------                                          ----------- -------
edgehub      Running Up 5 days    mcr.microsoft.com/azureiotedge-hub:1.0          10.57.48.62 443:31457/TCP,5671:30
                                                                                              881/TCP,8883:31753/TC
                                                                                              P
iotedged     Running Up 7 days    azureiotedge/azureiotedge-iotedged:0.1.0-beta13 <none>      35000/TCP,35001/TCP
cuda-sample2 Running Up 1 days    nvidia/samples:nbody
edgeagent    Running Up 7 days    azureiotedge/azureiotedge-agent:0.1.0-beta13
cuda-sample1 Running Up 4 minutes nvidia/samples:nbody

[10.100.10.10]: PS>

```

#### <a name="get-module-logs"></a>Modulok naplóinak beolvasása

A `logs` paranccsal lekérheti az eszközön futó IoT Edge-modulok naplóit. 

Ha hiba történt a tároló rendszerképének létrehozásakor vagy a rendszerkép húzása közben, futtassa a parancsot `logs edgeagent` . `edgeagent` a IoT Edge futtatókörnyezet tárolója, amely más tárolók kiépítési feladata. Mivel az `logs edgeagent` összes naplót kiírja, jó módszer a legutóbbi hibák megtekintésére, ha a 0 lehetőséget használja `--tail ` . 

Íme egy minta kimenet.

```powershell
[10.100.10.10]: PS>iotedge logs cuda-sample2 --tail 10
[10.100.10.10]: PS>iotedge logs edgeagent --tail 10
<6> 2021-02-25 00:52:54.828 +00:00 [INF] - Executing command: "Report EdgeDeployment status: [Success]"
<6> 2021-02-25 00:52:54.829 +00:00 [INF] - Plan execution ended for deployment 11
<6> 2021-02-25 00:53:00.191 +00:00 [INF] - Plan execution started for deployment 11
<6> 2021-02-25 00:53:00.191 +00:00 [INF] - Executing command: "Create an EdgeDeployment with modules: [cuda-sample2, edgeAgent, edgeHub, cuda-sample1]"
<6> 2021-02-25 00:53:00.212 +00:00 [INF] - Executing command: "Report EdgeDeployment status: [Success]"
<6> 2021-02-25 00:53:00.212 +00:00 [INF] - Plan execution ended for deployment 11
<6> 2021-02-25 00:53:05.319 +00:00 [INF] - Plan execution started for deployment 11
<6> 2021-02-25 00:53:05.319 +00:00 [INF] - Executing command: "Create an EdgeDeployment with modules: [cuda-sample2, edgeAgent, edgeHub, cuda-sample1]"
<6> 2021-02-25 00:53:05.412 +00:00 [INF] - Executing command: "Report EdgeDeployment status: [Success]"
<6> 2021-02-25 00:53:05.412 +00:00 [INF] - Plan execution ended for deployment 11
[10.100.10.10]: PS>
```

### <a name="use-kubectl-commands"></a>Kubectl parancsok használata

Egy olyan Azure Stack Edge Pro-eszközön, amelyen a számítási szerepkör konfigurálva van, az összes `kubectl` parancs elérhető a modulok figyeléséhez vagy megoldásához. Az elérhető parancsok listájának megjelenítéséhez futtassa `kubectl --help` a parancsot a parancsablakban.

```PowerShell
C:\Users\myuser>kubectl --help

kubectl controls the Kubernetes cluster manager.

Find more information at: https://kubernetes.io/docs/reference/kubectl/overview/

Basic Commands (Beginner):
    create         Create a resource from a file or from stdin.
    expose         Take a replication controller, service, deployment or pod and expose it as a new Kubernetes Service
    run            Run a particular image on the cluster
    set            Set specific features on objects
    run-container  Run a particular image on the cluster. This command is deprecated, use "run" instead
==============CUT=============CUT============CUT========================

Usage:
    kubectl [flags] [options]

Use "kubectl <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all commands).

C:\Users\myuser>
```

A parancsok átfogó listáját itt tekintheti `kubectl` meg: [ `kubectl` súgókártya](https://kubernetes.io/docs/reference/kubectl/cheatsheet/).


#### <a name="to-get-ip-of-service-or-module-exposed-outside-of-kubernetes-cluster"></a>A Kubernetes-fürtön kívül elérhető szolgáltatás vagy modul beszerzése

A terheléselosztási szolgáltatás vagy a Kubernetes kívül elérhető modulok IP-címének lekéréséhez futtassa a következő parancsot:

`kubectl get svc -n iotedge`

A következő példa a Kubernetes-fürtön kívül elérhető összes szolgáltatás vagy modul kimenetét jeleníti meg. 


```powershell
[10.100.10.10]: PS>kubectl get svc -n iotedge
NAME           TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                                       AGE
edgehub        LoadBalancer   10.103.52.225   10.128.44.243   443:31987/TCP,5671:32336/TCP,8883:30618/TCP   34h
iotedged       ClusterIP      10.107.236.20   <none>          35000/TCP,35001/TCP                           3d8h
webserverapp   LoadBalancer   10.105.186.35   10.128.44.244   8080:30976/TCP                                16h

[10.100.10.10]: PS>
```
A külső IP-oszlop IP-címe a szolgáltatás vagy a modul külső végpontjának felel meg. [A külső IP-címet a Kubernetes irányítópulton](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules)is lekérheti.


#### <a name="to-check-if-module-deployed-successfully"></a>Annak ellenőrzését, hogy a modul telepítése sikerült-e

A számítási modulok olyan tárolók, amelyeken a rendszer üzleti logikát alkalmaz. A Kubernetes Pod több tárolót is tartalmazhat. 

Ha szeretné megnézni, hogy a számítási modul telepítése sikeresen megtörtént-e, kapcsolódjon az eszköz PowerShell-felületéhez.
Futtassa a `get pods` parancsot, és ellenőrizze, hogy fut-e a tároló (a számítási modulnak megfelelő).

Egy adott névtérben futó összes hüvely listájának lekéréséhez futtassa a következő parancsot:

`get pods -n <namespace>`

A IoT Edge használatával üzembe helyezett modulok vizsgálatához futtassa a következő parancsot:

`get pods -n iotedge`

A következő példa a névtérben futó összes hüvely kimenetét jeleníti meg `iotedge` .

```
[10.100.10.10]: PS>kubectl get pods -n iotedge
NAME                        READY   STATUS    RESTARTS   AGE
edgeagent-cf6d4ffd4-q5l2k   2/2     Running   0          20h
edgehub-8c9dc8788-2mvwv     2/2     Running   0          56m
filemove-66c49984b7-h8lxc   2/2     Running   0          56m
iotedged-675d7f4b5f-9nml4   1/1     Running   0          20h

[10.100.10.10]: PS>
```

Az állapot **állapota** azt jelzi, hogy a névtérben lévő összes hüvely fut, a **kész** érték pedig a pod-ban üzembe helyezett tárolók számát jelzi. Az előző mintában az összes hüvely fut, és az egyes hüvelyekben üzembe helyezett modulok futnak. 

Az Azure arc használatával üzembe helyezett modulok vizsgálatához futtassa a következő parancsot:

`get pods -n azure-arc`

Azt is megteheti, hogy [csatlakozik a Kubernetes-irányítópulthoz, és megtekintheti IoT Edge vagy az Azure arc üzemelő példányait](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#view-module-status).


Ahhoz, hogy egy adott Pod részletes kimenetet kapjon egy adott névtérhez, futtathatja a következő parancsot:

`kubectl describe pod <pod name> -n <namespace>` 

A minta kimenete itt látható.

```
[10.100.10.10]: PS>kubectl describe pod filemove-66c49984b7 -n iotedge
Name:           filemove-66c49984b7-h8lxc
Namespace:      iotedge
Priority:       0
Node:           k8s-1hwf613cl-1hwf613/10.139.218.12
Start Time:     Thu, 14 May 2020 12:46:28 -0700
Labels:         net.azure-devices.edge.deviceid=myasegpu-edge
                net.azure-devices.edge.hub=myasegpu2iothub.azure-devices.net
                net.azure-devices.edge.module=filemove
                pod-template-hash=66c49984b7
Annotations:    net.azure-devices.edge.original-moduleid: filemove
Status:         Running
IP:             172.17.75.81
IPs:            <none>
Controlled By:  ReplicaSet/filemove-66c49984b7
Containers:
    proxy:
    Container ID:   docker://fd7975ca78209a633a1f314631042a0892a833b7e942db2e7708b41f03e8daaf
    Image:          azureiotedge/azureiotedge-proxy:0.1.0-beta8
    Image ID:       docker://sha256:5efbf6238f13d24bab9a2b499e5e05bc0c33ab1587d6cf6f289cdbe7aa667563
    Port:           <none>
    Host Port:      <none>
    State:          Running
        Started:      Thu, 14 May 2020 12:46:30 -0700
    Ready:          True
    Restart Count:  0
    Environment:
        PROXY_LOG:  Debug
=============CUT===============================CUT===========================
Volumes:
    config-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-config
    Optional:  false
    trust-bundle-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-trust-bundle
    Optional:  false
    myasesmb1local:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1local
    ReadOnly:   false
    myasesmb1:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1
    ReadOnly:   false
    filemove-token-pzvw8:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  filemove-token-pzvw8
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                    node.kubernetes.io/unreachable:NoExecute for 300s
Events:          <none>


[10.100.10.10]: PS>
```

#### <a name="to-get-container-logs"></a>A tároló naplófájljainak beolvasása

Egy modul naplóinak lekéréséhez futtassa a következő parancsot az eszköz PowerShell-felületéről:

`kubectl logs <pod_name> -n <namespace> --all-containers` 

Mivel a `all-containers` jelölő kiírja az összes tároló összes naplóját, jó módszer a legutóbbi hibák megtekintésére, ha a kapcsolót használja `--tail 10` .

A következő egy minta kimenet. 

```
[10.100.10.10]: PS>kubectl logs filemove-66c49984b7-h8lxc -n iotedge --all-containers --tail 10
DEBUG 2020-05-14T20:40:42Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:12Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
05/14/2020 19:46:44: Info: Opening module client connection.
05/14/2020 19:46:45: Info: Open done.
05/14/2020 19:46:45: Info: Initializing with input: /home/input, output: /home/output, protocol: Amqp.
05/14/2020 19:46:45: Info: IoT Hub module client initialized.

[10.100.10.10]: PS>
```

### <a name="change-memory-processor-limits-for-kubernetes-worker-node"></a>Memória módosítása, Kubernetes-feldolgozó csomópontra vonatkozó korlátozások

A Kubernetes Worker csomópont memória-vagy processzor-korlátainak módosításához hajtsa végre a következő lépéseket:

1. [Kapcsolódjon az eszköz PowerShell-felületéhez](#connect-to-the-powershell-interface).
1. A munkavégző csomópont aktuális erőforrásainak és a szerepkör beállításainak lekéréséhez futtassa a következő parancsot:

    `Get-AzureDataBoxEdgeRole`

    Íme egy minta kimenet. Jegyezze fel a és a szakaszban szereplő értékeket `Name` `Compute` `Resources` . `MemoryInBytes` és `ProcessorCount` a Kubernetes Worker csomóponthoz tartozó aktuálisan hozzárendelt értékek memóriáját és processzorának darabszámát jelöli.  

    ```powershell
    [10.100.10.10]: PS>Get-AzureDataBoxEdgeRole
    ImageDetail                : Name:mcr.microsoft.com/azureiotedge-agent
                                 Tag:1.0
                                 PlatformType:Linux
    EdgeDeviceConnectionString :
    IotDeviceConnectionString  :
    HubHostName                : ase-srp-007.azure-devices.net
    IotDeviceId                : srp-007-storagegateway
    EdgeDeviceId               : srp-007-edge
    Version                    :
    Id                         : 6ebeff9f-84c5-49a7-890c-f5e05520a506
    Name                       : IotRole
    Type                       : IOT
    Resources                  : Compute:
                                 MemoryInBytes:34359738368
                                 ProcessorCount:12
                                 VMProfile:
    
                                 Storage:
                                 EndpointMap:
                                 EndpointId:c0721210-23c2-4d16-bca6-c80e171a0781
                                 TargetPath:mysmbedgecloudshare1
                                 Name:mysmbedgecloudshare1
                                 Protocol:SMB
    
                                 EndpointId:6557c3b6-d3c5-4f94-aaa0-6b7313ab5c74
                                 TargetPath:mysmbedgelocalshare
                                 Name:mysmbedgelocalshare
                                 Protocol:SMB
                                 RootFileSystemStorageSizeInBytes:0
    
    HostPlatform               : KubernetesCluster
    State                      : Created
    PlatformType               : Linux
    HostPlatformInstanceId     : 994632cb-853e-41c5-a9cd-05b36ddbb190
    IsHostPlatformOwner        : True
    IsCreated                  : True    
    [10.100.10.10]: PS>
    ```
    
1. A következő parancs futtatásával módosíthatja a munkavégző csomópont memóriájának és processzorának értékeit:

    Set-AzureDataBoxEdgeRoleCompute – név <Name value from the output of Get-AzureDataBoxEdgeRole> – memória <Value in Bytes> -ProcessorCount <nem. magok>

    Íme egy minta kimenet. 
    
    ```powershell
    [10.100.10.10]: PS>Set-AzureDataBoxEdgeRoleCompute -Name IotRole -MemoryInBytes 32GB -ProcessorCount 16
    
    ImageDetail                : Name:mcr.microsoft.com/azureiotedge-agent
                                 Tag:1.0
                                 PlatformType:Linux
    
    EdgeDeviceConnectionString :
    IotDeviceConnectionString  :
    HubHostName                : ase-srp-007.azure-devices.net
    IotDeviceId                : srp-007-storagegateway
    EdgeDeviceId               : srp-007-edge
    Version                    :
    Id                         : 6ebeff9f-84c5-49a7-890c-f5e05520a506
    Name                       : IotRole
    Type                       : IOT
    Resources                  : Compute:
                                 MemoryInBytes:34359738368
                                 ProcessorCount:16
                                 VMProfile:
    
                                 Storage:
                                 EndpointMap:
                                 EndpointId:c0721210-23c2-4d16-bca6-c80e171a0781
                                 TargetPath:mysmbedgecloudshare1
                                 Name:mysmbedgecloudshare1
                                 Protocol:SMB
    
                                 EndpointId:6557c3b6-d3c5-4f94-aaa0-6b7313ab5c74
                                 TargetPath:mysmbedgelocalshare
                                 Name:mysmbedgelocalshare
                                 Protocol:SMB
    
                                 RootFileSystemStorageSizeInBytes:0
    
    HostPlatform               : KubernetesCluster
    State                      : Created
    PlatformType               : Linux
    HostPlatformInstanceId     : 994632cb-853e-41c5-a9cd-05b36ddbb190
    IsHostPlatformOwner        : True
    IsCreated                  : True
    
    [10.100.10.10]: PS>    
    ```

A memória és a processzor használatának módosításakor kövesse az alábbi irányelveket.

- Az alapértelmezett memória az eszköz specifikációjának 25%-a.
- Az alapértelmezett processzorok száma az eszköz specifikációjának 30%-a.
- A memória és a processzor értékének módosításakor azt javasoljuk, hogy az értékek 15% – 60%-a az eszköz memóriája és a processzorok száma között legyenek módosítva. 
- Az 60%-os felső korlátot javasoljuk, hogy elegendő erőforrás legyen a rendszerösszetevőkhöz. 

## <a name="connect-to-bmc"></a>Kapcsolódás BMC-hez

A alaplapi felügyeleti vezérlő (BMC) használatával távolról figyelheti és kezelheti az eszközt. Ez a szakasz azokat a parancsmagokat ismerteti, amelyek segítségével kezelhető a BMC-konfiguráció. A parancsmagok bármelyikének futtatása előtt [kapcsolódjon az eszköz PowerShell-felületéhez](#connect-to-the-powershell-interface).

- `Get-HcsNetBmcInterface`: Ezzel a parancsmaggal kérheti le a BMC hálózati konfigurációs tulajdonságait, például:,,, `IPv4Address` `IPv4Gateway` `IPv4SubnetMask` `DhcpEnabled` . 
    
    Itt látható egy mintakimenet:
    
    ```powershell
    [10.100.10.10]: PS>Get-HcsNetBmcInterface
    IPv4Address   IPv4Gateway IPv4SubnetMask DhcpEnabled
    -----------   ----------- -------------- -----------
    10.128.53.186 10.128.52.1 255.255.252.0        False
    [10.100.10.10]: PS>
    ```
- `Set-HcsNetBmcInterface`: Ezt a parancsmagot a következő két módon használhatja.

    - A (z) parancsmag használatával engedélyezze vagy tiltsa le a BMC DHCP-konfigurációját a megfelelő értékkel a `UseDhcp` paraméterhez. 

        ```powershell
        Set-HcsNetBmcInterface -UseDhcp $true
        ```

        Itt látható egy mintakimenet: 

        ```powershell
        [10.100.10.10]: PS>Set-HcsNetBmcInterface -UseDhcp $true
        [10.100.10.10]: PS>Get-HcsNetBmcInterface
        IPv4Address IPv4Gateway IPv4SubnetMask DhcpEnabled
        ----------- ----------- -------------- -----------
        10.128.54.8 10.128.52.1 255.255.252.0         True
        [10.100.10.10]: PS>
        ```

    - Ezzel a parancsmaggal konfigurálhatja a BMC statikus konfigurációját. Megadhatja a, a és a értékeit is `IPv4Address` `IPv4Gateway` `IPv4SubnetMask` . 
    
        ```powershell
        Set-HcsNetBmcInterface -IPv4Address "<IPv4 address of the device>" -IPv4Gateway "<IPv4 address of the gateway>" -IPv4SubnetMask "<IPv4 address for the subnet mask>"
        ```        
        
        Itt látható egy mintakimenet: 

        ```powershell
        [10.100.10.10]: PS>Set-HcsNetBmcInterface -IPv4Address 10.128.53.186 -IPv4Gateway 10.128.52.1 -IPv4SubnetMask 255.255.252.0
        [10.100.10.10]: PS>Get-HcsNetBmcInterface
        IPv4Address   IPv4Gateway IPv4SubnetMask DhcpEnabled
        -----------   ----------- -------------- -----------
        10.128.53.186 10.128.52.1 255.255.252.0        False
        [10.100.10.10]: PS>
        ```    

- `Set-HcsBmcPassword`: Ezzel a parancsmaggal módosíthatja a BMC-jelszavát `EdgeUser` . A Felhasználónév – `EdgeUser` megkülönbözteti a kis-és nagybetűket.

    Itt látható egy mintakimenet: 

    ```powershell
    [10.100.10.10]: PS> Set-HcsBmcPassword -NewPassword "Password1"
    [10.100.10.10]: PS>
    ```

## <a name="exit-the-remote-session"></a>Kilépés a távoli munkamenetből

A távoli PowerShell-munkamenetből való kilépéshez zárja be a PowerShell ablakát.

## <a name="next-steps"></a>Következő lépések

- [Azure stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md) üzembe helyezése Azure Portalban.
---
title: Azure arc-adatkezelő üzembe helyezése az Azure Stack Edge Pro GPU-eszközön | Microsoft Docs
description: Ismerteti, hogyan helyezhető üzembe egy Azure-beli ív-adatkezelő és Azure Data Services az Azure Stack Edge Pro GPU-eszközön.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: 3ff07c773a2976a296d13510a3ddd7b41217aaa2
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102437621"
---
# <a name="deploy-azure-data-services-on-your-azure-stack-edge-pro-gpu-device"></a>Az Azure Data Services üzembe helyezése az Azure Stack Edge Pro GPU-eszközön

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Ez a cikk ismerteti az Azure-beli ív-adatkezelő létrehozásának folyamatát, majd az Azure Data Services üzembe helyezését az Azure Stack Edge Pro GPU-eszközön. 

Az Azure arc-adatkezelő a helyi vezérlési sík, amely lehetővé teszi az Azure Data Services használatát az ügyfél által felügyelt környezetekben. Miután létrehozta az Azure arc-adatkezelőt az Azure Stack Edge Pro-eszközön futó Kubernetes-fürtön, üzembe helyezheti az Azure Data Services, például az SQL felügyelt példányát (előzetes verzió) az adatvezérlőn.

Az adatkezelő létrehozásának eljárása, majd az SQL felügyelt példányok üzembe helyezése a PowerShell és a `kubectl` -egy natív eszköz használatával történik, amely parancssori hozzáférést biztosít a Kubernetes-fürthöz az eszközön.


## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Egy Azure Stack Edge Pro-eszközhöz fér hozzá, és aktiválta az eszközt a [Azure stack Edge Pro aktiválása](azure-stack-edge-gpu-deploy-activate.md)című témakörben leírtak szerint.

1. Engedélyezte a számítási szerepkört az eszközön. Egy Kubernetes-fürt is létrejött az eszközön, amikor az eszközön a számítás [konfigurálása az Azure stack Edge Pro-eszközön](azure-stack-edge-gpu-deploy-configure-compute.md)című témakör útmutatása szerint konfigurálta az eszközt.

1. A Kubernetes API-végpont a helyi webes felhasználói felület **eszköz** lapján található. További információkért tekintse meg az [KUBERNETES API-végpont beolvasása](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints)című témakör utasításait.

1. Olyan ügyfélhez fér hozzá, amely csatlakozni fog az eszközhöz. 
    1. Ez a cikk a PowerShell 5,0-es vagy újabb verzióját futtató Windows rendszerű ügyfélprogramot használ az eszköz eléréséhez. Bármely más ügyfél [támogatott operációs rendszerrel](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)használható. 
    1. Telepítse `kubectl` az ügyfelet. Az ügyfél verziószáma:
        1. Azonosítsa az eszközre telepített Kubernetes-kiszolgáló verzióját. Az eszköz helyi felhasználói felületén válassza a **szoftverfrissítések** lapot. Jegyezze fel a **Kubernetes-kiszolgáló verziószámát** ezen a lapon.
        1. Olyan ügyfelet töltsön le, amely legfeljebb egy alverzióval tér el a főverziótól. Az ügyfél verziója, de a főkiszolgálót akár egy alverzión is elvezethetik. A v 1.3-as főkiszolgáló például a v 1.1, v 1.2 és v 1.3 csomópontokkal működik együtt, és működnie kell a v 1.2, v 1.3 és v 1.4 rendszerű ügyfelekkel. A Kubernetes-ügyfélszoftversel kapcsolatos további információkért lásd: [a Kubernetes verziója és verziója](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-version-skew).
    
1. Opcionálisan [telepítheti az Azure arc-kompatibilis adatszolgáltatások üzembe helyezésére és felügyeletére szolgáló ügyféleszközök eszközeit](../azure-arc/data/install-client-tools.md). Ezek az eszközök nem szükségesek, de ajánlottak.  
1. Győződjön meg arról, hogy elegendő erőforrás áll rendelkezésre az eszközön egy adatkezelő és egy felügyelt SQL-példány kiépítéséhez. Az adatkezelő és egy felügyelt SQL-példány esetében legalább 16 GB RAM-mal és 4 CPU-maggal kell rendelkeznie. Részletes útmutatásért lépjen az [Azure arc-kompatibilis adatszolgáltatások telepítéséhez szükséges minimális követelményekre](../azure-arc/data/sizing-guidance.md#minimum-deployment-requirements).


## <a name="configure-kubernetes-external-service-ips"></a>Kubernetes külső szolgáltatás IP-címeinek konfigurálása

1. Nyissa meg az eszköz helyi webes FELÜLETét, és válassza a **számítás** lehetőséget.
1. Válassza ki a számításhoz engedélyezett hálózatot. 

    ![Számítási oldal a helyi felhasználói felületen 2](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-1.png)

1. Győződjön meg arról, hogy három további Kubernetes külső szolgáltatási IP-címet biztosít (a más külső szolgáltatásokhoz vagy tárolóhoz már konfigurált IP-címeken kívül). Az adatkezelő két szolgáltatási IP-címet használ, és a harmadik IP-címet a felügyelt SQL-példányok létrehozásakor használja a rendszer. Az üzembe helyezni kívánt további adatszolgáltatásokhoz egy IP-címet kell telepítenie. 

    ![Számítási lap a helyi felhasználói felületen 3](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-2.png)

1. Alkalmazza a beállításokat, és ezek az új IP-címek azonnal érvénybe lépnek egy már meglévő Kubernetes-fürtön. 


## <a name="deploy-azure-arc-data-controller"></a>Az Azure arc-adatkezelő üzembe helyezése

Az adatkezelő üzembe helyezése előtt létre kell hoznia egy névteret.

### <a name="create-namespace"></a>Névtér létrehozása 

Hozzon létre egy új, dedikált névteret, amelyben üzembe helyezi az adatkezelőt. Emellett létre kell hoznia egy felhasználót is, majd a felhasználó számára hozzáférést kell adnia a létrehozott névtérhez. 

> [!NOTE]
> A névtér és a felhasználónevek esetében a [DNS-altartományok elnevezési konvenciói](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names) érvényesek.

1. [Kapcsolódjon a PowerShell felületéhez](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. Hozzon létre egy névteret. Típus:

    `New-HcsKubernetesNamespace -Namespace <Name of namespace>`

1. Hozzon létre egy felhasználót. Típus: 

    `New-HcsKubernetesUser -UserName <User name>`

1. A konfigurációs fájlok egyszerű szövegben jelennek meg. Másolja ezt a fájlt, és mentse *konfigurációs* fájlként. 

    > [!IMPORTANT]
    > Ne mentse a konfigurációs fájlt *. txt* fájlként, mentse a fájlt fájlkiterjesztés nélkül.

1. A konfigurációs fájlnak a `.kube` helyi számítógépen lévő felhasználói profil mappájában kell lennie. Másolja a fájlt a mappába a felhasználói profilban.

    ![A konfigurációs fájl helye az ügyfélen](media/azure-stack-edge-j-series-create-kubernetes-cluster/location-config-file.png)
1. Adja meg a felhasználó hozzáférését a létrehozott névtérhez. Típus: 

    `Grant-HcsKubernetesNamespaceAccess -Namespace <Name of namespace> -UserName <User name>`

    Itt látható az előző parancsok mintájának kimenete. Ebben a példában létrehozunk egy `myadstest` névteret, egy `myadsuser` felhasználót, és hozzáférést biztosítunk a felhasználónak a névtérhez.
    
    ```powershell
    [10.100.10.10]: PS>New-HcsKubernetesNamespace -Namespace myadstest
    [10.100.10.10]: PS>New-HcsKubernetesUser -UserName myadsuser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: LS0tLS1CRUdJTiBD=======//snipped//=======VSVElGSUNBVEUtLS0tLQo=
        server: https://compute.myasegpudev.wdshcsso.com:6443
      name: kubernetes
    contexts:
    - context:
        cluster: kubernetes
        user: myadsuser
      name: myadsuser@kubernetes
    current-context: myadsuser@kubernetes
    kind: Config
    preferences: {}
    users:
    - name: myadsuser
      user:
        client-certificate-data: LS0tLS1CRUdJTiBDRV=========//snipped//=====EE9PQotLS0kFURSBLRVktLS0tLQo=
    
    [10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace myadstest -UserName myadsuser
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcDataController -SubscriptionId db4e2fdb-6d80-4e6e-b7cd-736098270664 -ResourceGroupName myasegpurg -Location "EastUS" -UserName myadsuser -Password "Password1" -DataControllerName "arctestcontroller" -Namespace myadstest
    [10.100.10.10]: PS>
    ```
1. Adjon hozzá egy DNS-bejegyzést a gazdagépek fájljához a rendszeren. 

    1. Futtassa rendszergazdaként a jegyzettömböt, és nyissa meg a `hosts` következő helyen található fájlt: `C:\windows\system32\drivers\etc\hosts` . 
    2. A helyi felhasználói felület **eszköz** lapján mentett információk alapján hozza létre a bejegyzést a Hosts fájlban. 

        Másolja például ezt a végpontot a `https://compute.myasegpudev.microsoftdatabox.com/[10.100.10.10]` következő bejegyzés létrehozásához az eszköz IP-címe és a DNS-tartomány használatával: 

        `10.100.10.10 compute.myasegpudev.microsoftdatabox.com`

1. Annak ellenőrzéséhez, hogy tud-e csatlakozni a Kubernetes hüvelyéhez, indítson el egy parancssort vagy egy PowerShell-munkamenetet. Típus:
    
    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n "myadstest"
    No resources found.
    PS C:\WINDOWS\system32>
    ```
Most már üzembe helyezheti az adatkezelőt és az adatszolgáltatási alkalmazásokat a névtérben, majd megtekintheti az alkalmazásokat és a naplókat.

### <a name="create-data-controller"></a>Adatkezelő létrehozása

Az adatkezelő a Kubernetes-fürtön üzembe helyezett hüvelyek gyűjteménye, amely API-t, a vezérlő szolgáltatást, a bootstrapper, valamint a figyelési adatbázisokat és irányítópultokat biztosítja. Az alábbi lépéseket követve hozzon létre egy adatvezérlőt a korábban létrehozott névtérben lévő Azure Stack Edge-eszközön található Kubernetes-fürtön.   

1. Gyűjtse össze a következő adatokat, amelyeket létre kell hoznia egy adatkezelő létrehozásához:

    
    |1\. oszlop  |2\. oszlop  |
    |---------|---------|
    |Adatkezelő neve     |Az adatvezérlő leíró neve. Például: `arctestdatacontroller`.         |
    |Adatkezelő felhasználóneve     |Az adatkezelő rendszergazda felhasználójának felhasználóneve. Az adatkezelő felhasználónevét és jelszavát a rendszer az adatkezelő API-hoz történő hitelesítésre használja a rendszergazdai függvények végrehajtásához.          |
    |Adatvezérlő jelszava     |Az adatkezelő rendszergazda felhasználójának jelszava. Válassza ki a biztonságos jelszót, és ossza meg csak azokkal, akiknek szükségük van a fürt rendszergazdai jogosultságára.         |
    |A Kubernetes-névtér neve     |Annak a Kubernetes-névtérnek a neve, amelyben létre kívánja hozni az adatvezérlőt.         |
    |Azure-előfizetés azonosítója     |Az Azure-előfizetéshez tartozó GUID, ahol szeretné létrehozni az adatvezérlő erőforrását az Azure-ban.         |
    |Azure-erőforráscsoport neve     |Azon erőforráscsoport neve, amelyben létre szeretné hozni az adatvezérlő erőforrását az Azure-ban.         |
    |Azure-beli hely     |Az Azure-beli hely, ahol az adatkezelő erőforrás-metaadatai az Azure-ban lesznek tárolva. Az elérhető régiók listájáért lásd: Azure globális infrastruktúra/termékek régiónként.|


1. Csatlakozzon a PowerShell-felülethez. Az adatvezérlő létrehozásához írja be a következőt: 

    ```powershell
    Set-HcsKubernetesAzureArcDataController -SubscriptionId <Subscription ID> -ResourceGroupName <Resource group name> -Location <Location without spaces> -UserName <User you created> -Password <Password to authenticate to Data Controller> -DataControllerName <Data Controller Name> -Namespace <Namespace you created>    
    ```
    Itt látható az előző parancsok mintájának kimenete.

    ```powershell
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcDataController -SubscriptionId db4e2fdb-6d80-4e6e-b7cd-736098270664 -ResourceGroupName myasegpurg -Location "EastUS" -UserName myadsuser -Password "Password1" -DataControllerName "arctestcontroller" -Namespace myadstest
    [10.100.10.10]: PS> 
    ```
    
    Az üzembe helyezés körülbelül 5 percet is igénybe vehet.

    > [!NOTE]
    > Az Azure Stack Edge Pro-eszközön a Kubernetes-fürtön létrehozott adatkezelő csak az aktuális kiadás leválasztott módjában működik.

### <a name="monitor-data-creation-status"></a>Az adatlétrehozási állapot figyelése

1. Nyisson meg egy másik PowerShell-ablakot.
1. A következő `kubectl` parancs használatával figyelheti az adatvezérlő létrehozási állapotát. 

    ```powershell
    kubectl get datacontroller/<Data controller name> --namespace <Name of your namespace>
    ```
    A vezérlő létrehozásakor az állapotnak kell lennie `Ready` .
    Az alábbi példa az előző parancs kimenetét jeleníti meg:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get datacontroller/arctestcontroller --namespace myadstest
    NAME                STATE
    arctestcontroller   Ready
    PS C:\WINDOWS\system32>
    ```
1. Az adatvezérlőn futó külső szolgáltatásokhoz hozzárendelt IP-címek azonosításához használja az `kubectl get svc -n <namespace>` parancsot. Itt látható egy mintakimenet:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get svc -n myadstest
    NAME                      TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                                       AGE
    controldb-svc             ClusterIP      172.28.157.130   <none>        1433/TCP,8311/TCP,8411/TCP                    3d21h
    controller-svc            ClusterIP      172.28.123.251   <none>        443/TCP,8311/TCP,8301/TCP,8411/TCP,8401/TCP   3d21h
    controller-svc-external   LoadBalancer   172.28.154.30    10.57.48.63   30080:31090/TCP                               3d21h
    logsdb-svc                ClusterIP      172.28.52.196    <none>        9200/TCP,8300/TCP,8400/TCP                    3d20h
    logsui-svc                ClusterIP      172.28.85.97     <none>        5601/TCP,8300/TCP,8400/TCP                    3d20h
    metricsdb-svc             ClusterIP      172.28.255.103   <none>        8086/TCP,8300/TCP,8400/TCP                    3d20h
    metricsdc-svc             ClusterIP      172.28.208.191   <none>        8300/TCP,8400/TCP                             3d20h
    metricsui-svc             ClusterIP      172.28.158.163   <none>        3000/TCP,8300/TCP,8400/TCP                    3d20h
    mgmtproxy-svc             ClusterIP      172.28.228.229   <none>        443/TCP,8300/TCP,8311/TCP,8400/TCP,8411/TCP   3d20h
    mgmtproxy-svc-external    LoadBalancer   172.28.166.214   10.57.48.64   30777:30621/TCP                               3d20h
    sqlex-svc                 ClusterIP      None             <none>        1433/TCP                                      3d20h
    PS C:\WINDOWS\system32>
    ```

## <a name="deploy-sql-managed-instance"></a>Felügyelt SQL-példány üzembe helyezése

Az adatkezelő sikeres létrehozása után sablon használatával telepítheti az SQL felügyelt példányát az adatkezelőn.

### <a name="deployment-template"></a>Üzembehelyezési sablon

A következő üzembe helyezési sablonnal telepítheti a felügyelt SQL-példányokat az eszköz adatvezérlőjén.

```yml
apiVersion: v1
data:
    password: UGFzc3dvcmQx
    username: bXlhZHN1c2Vy
kind: Secret
metadata:
    name: sqlex-login-secret
type: Opaque
---
apiVersion: sql.arcdata.microsoft.com/v1alpha1
kind: sqlmanagedinstance
metadata:
    name: sqlex
spec:
    limits:
    memory: 4Gi
    vcores: "4"
    requests:
    memory: 2Gi
    vcores: "1"
    service:
    type: LoadBalancer
    storage:
    backups:
        className: ase-node-local
        size: 5Gi
    data:
        className: ase-node-local
        size: 5Gi
    datalogs:
        className: ase-node-local
        size: 5Gi
    logs:
        className: ase-node-local
        size: 1Gi
```


#### <a name="metadata-name"></a>Metaadatok neve
    
A metaadatok neve a felügyelt SQL-példány neve. Az előzőben szereplő társított Pod `deployment.yaml` neve a következő lesz: `sqlex-n` (az `n` alkalmazáshoz társított hüvelyek száma). 
    
#### <a name="password-and-username-data"></a>Jelszó-és Felhasználónév-adatértékek

Az adatkezelő felhasználónevét és jelszavát a rendszer az adatkezelő API-hoz történő hitelesítésre használja a rendszergazdai függvények végrehajtásához. A központi telepítési sablonban található adatvezérlő felhasználónevének és jelszavának Kubernetes-titka Base64 kódolású karakterlánc. 

Az online eszközzel Base64-kódolást használhat a kívánt felhasználónévvel és jelszóval, vagy a platformtól függően beépített CLI-eszközöket is használhat. Ha online Base64 kódolású eszközt használ, adja meg a Felhasználónév és a jelszó karakterláncot (amelyet az adatvezérlő létrehozásakor adott meg) az eszközön, és az eszköz létrehozza a megfelelő Base64 kódolású karakterláncokat.
    
#### <a name="service-type"></a>Szolgáltatás típusa

A szolgáltatástípus értékének a következőnek kell lennie: `LoadBalancer` .
    
#### <a name="storage-class-name"></a>Tárolási osztály neve

Azonosíthatja a tárolási osztályt az Azure Stack Edge-eszközön, amelyet a központi telepítés az adatokat, a biztonsági másolatokat, az adatnaplókat és a naplókat fogja használni. Használja az  `kubectl get storageclass` parancsot az eszközön üzembe helyezett tárolási osztály beszerzéséhez.

```powershell
PS C:\WINDOWS\system32> kubectl get storageclass
NAME             PROVISIONER      RECLAIMPOLICY  VOLUMEBINDINGMODE     ALLOWVOLUMEEXPANSION   AGE
ase-node-local   rancher.io/local-path   Delete  WaitForFirstConsumer  false                  5d23h
PS C:\WINDOWS\system32>
```
A fenti kimenetben a `ase-node-local` sablonban meg kell adni az eszköz tárolási osztályát.
 
#### <a name="spec"></a>Spec

SQL felügyelt példány létrehozásához a Azure Stack Edge-eszközön megadhatja a memória-és a CPU-követelményeket a specifikáció szakaszában `deployment.yaml` . A felügyelt SQL-példányoknak legalább 2 GB memóriával és 1 CPU-mag kell rendelkezniük az alábbi példában látható módon. 

```yml
spec:
    limits:
    memory: 4Gi
    vcores: "4"
    requests:
    memory: 2Gi
    vcores: "1"
```  

Az adatkezelő és az 1 SQL felügyelt példány részletes méretezési útmutatójának áttekintéséhez tekintse át az [SQL felügyelt példányának méretezési részleteit](../azure-arc/data/sizing-guidance.md#sql-managed-instance-sizing-details).

### <a name="run-deployment-template"></a>Központi telepítési sablon futtatása

Futtassa a `deployment.yaml` következő parancsot a használatával:

```powershell
kubectl create -n <Name of namespace that you created> -f <Path to the deployment yaml> 
```

A következő parancs egy minta kimenetét jeleníti meg:

```powershell
PS C:\WINDOWS\system32> kubectl get pods -n "myadstest"
No resources found.
PS C:\WINDOWS\system32> 
PS C:\WINDOWS\system32> kubectl create -n myadstest -f C:\azure-arc-data-services\sqlex.yaml  secret/sqlex-login-secret created
sqlmanagedinstance.sql.arcdata.microsoft.com/sqlex created
PS C:\WINDOWS\system32> kubectl get pods --namespace myadstest
NAME                 READY   STATUS    RESTARTS   AGE
bootstrapper-mv2cd   1/1     Running   0          83m
control-w9s9l        2/2     Running   0          78m
controldb-0          2/2     Running   0          78m
controlwd-4bmc5      1/1     Running   0          64m
logsdb-0             1/1     Running   0          64m
logsui-wpmw2         1/1     Running   0          64m
metricsdb-0          1/1     Running   0          64m
metricsdc-fb5r5      1/1     Running   0          64m
metricsui-56qzs      1/1     Running   0          64m
mgmtproxy-2ckl7      2/2     Running   0          64m
sqlex-0              3/3     Running   0          13m
PS C:\WINDOWS\system32>
```

A `sqlex-0` minta kimenetben található Pod a felügyelt SQL-példány állapotát jelzi.

## <a name="remove-data-controller"></a>Adatkezelő eltávolítása

Az adatkezelő eltávolításához törölje azt a dedikált névteret, amelyben üzembe helyezte.


```powershell
kubectl delete ns <Name of your namespace>
```


## <a name="next-steps"></a>Következő lépések

- [Állapot nélküli alkalmazás üzembe helyezése a Azure stack Edge Pro](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md)-ban.

---
title: Üzembe helyezhet egy Azure Arc-adatkezelőt a Azure Stack Edge Pro GPU-eszközön| Microsoft Docs
description: Leírja, hogyan helyezhet üzembe egy Azure Arc-vezérlőt és az Azure-Data Services a Azure Stack Edge Pro GPU-eszközön.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/15/2021
ms.author: alkohli
ms.openlocfilehash: d56e03cd650032a775c30b02d939cf934f384fae
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568607"
---
# <a name="deploy-azure-data-services-on-your-azure-stack-edge-pro-gpu-device"></a>Az Azure Data Services üzembe helyezése Azure Stack Edge Pro GPU-eszközön

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Ez a cikk azt ismerteti, hogyan lehet létrehozni egy Azure Arc adatkezelőt, majd üzembe helyezni az Azure Data Services gpu Azure Stack Edge Pro eszközön. 

Azure Arc Adatkezelő az a helyi vezérlősík, amely lehetővé teszi az Azure Data Services az ügyfél által felügyelt környezetekben. Miután létrehozta az Azure Arc Data Controllert a Azure Stack Edge Pro GPU-eszközön futó Kubernetes-fürtön, üzembe helyezheti az Azure Data Services-t ( például SQL Managed Instance (előzetes verzió) az adatkezelőn.

Az adatkezelő létrehozására, majd az SQL Managed Instance üzembe helyezésére vonatkozó eljárás magában foglalja a PowerShell és egy olyan natív eszköz használatát, amely parancssori hozzáférést biztosít az eszközön található `kubectl` Kubernetes-fürthöz.


## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Hozzáféréssel rendelkezik egy Azure Stack Edge Pro GPU-eszközhöz, és aktiválta az eszközt az [Activate Azure Stack Edge Pro ( Aktiválása) Azure Stack Edge Pro.](azure-stack-edge-gpu-deploy-activate.md)

1. Engedélyezte a számítási szerepkört az eszközön. A Kubernetes-fürt akkor is létrejött az eszközön, amikor a számítást konfigurálta az eszközön a Számítás konfigurálása az Azure Stack Edge Pro [GPU-eszközön című útmutatónak megfelelően.](azure-stack-edge-gpu-deploy-configure-compute.md)

1. A Kubernetes API-végpont a **helyi** webes felhasználói felület Eszköz lapján található. További információkért lásd a [Kubernetes API-végpont lekért utasításait.](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints)

1. Rendelkezik hozzáféréssel egy ügyfélhez, amely csatlakozik az eszközhöz. 
    1. Ez a cikk egy PowerShell 5.0-s vagy újabb verziójú Windows-ügyfélrendszert használ az eszköz eléréséhez. Bármely más ügyfelet használhat támogatott [operációs rendszerrel.](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) 
    1. Telepítse `kubectl` az ügyfelen. Az ügyfél verziója:
        1. Azonosítsa az eszközön telepített Kubernetes-kiszolgáló verzióját. Az eszköz helyi felhasználói felületén válassza a **Szoftverfrissítések** lapot. Figyelje meg ezen az oldalon **a Kubernetes-kiszolgáló** verzióját.
        1. Olyan ügyfelet töltsön le, amely legfeljebb egy alverzióval tér el a főverziótól. Az ügyfél verziója, de akár egy alverzióval is vezetheti a főkiszolgálót. Az 1.3-as főkiszolgálónak például működnie kell az 1.1-es, 1.2-es és 1.3-as vagy 1.3-as csomópontokkal, valamint az 1.2-es, 1.3-as és 1.4-es ügyfelekkel. A Kubernetes-ügyfél verziójával kapcsolatos további információkért lásd a [Kubernetes](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-version-skew)verzió- és verzió-elágazító támogatási szabályzatát.
    
1. Ha szükséges, [telepítse az ügyféleszközöket](../azure-arc/data/install-client-tools.md)a központi telepítéséhez és kezeléséhez Azure Arc-kompatibilis adatszolgáltatások. Ezek az eszközök nem szükségesek, de ajánlottak.  
1. Győződjön meg arról, hogy az eszközön elegendő erőforrás áll rendelkezésre egy adatkezelő és egy SQL Managed Instance. Az adatkezelőhöz és egy SQL Managed Instance legalább 16 GB RAM és 4 processzormag szükséges. Részletes útmutatás: A központi telepítés [minimális Azure Arc-kompatibilis adatszolgáltatások meg.](../azure-arc/data/sizing-guidance.md#minimum-deployment-requirements)


## <a name="configure-kubernetes-external-service-ips"></a>A Kubernetes külső szolgáltatás IP-i-iinek konfigurálása

1. Az eszköz helyi webes felhasználói felületén válassza a Számítás **adatokat.**
1. Válassza ki a számításhoz engedélyezett hálózatot. 

    ![Számítás lap a helyi felhasználói felületen 2](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-1.png)

1. Győződjön meg arról, hogy további három további Kubernetes külső szolgáltatási IP-t ad meg (a más külső szolgáltatásokhoz vagy tárolókhoz már konfigurált IP-k mellett). Az adatkezelő két szolgáltatási IP-címet fog használni, a harmadik IP-címet pedig a szolgáltatás SQL Managed Instance. Minden további üzembe helyezett Data Service-hez egy IP-cím szükséges. 

    ![Számítás lap a helyi felhasználói felületen 3](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-2.png)

1. Alkalmazza a beállításokat, és ezek az új IP-k azonnal hatnak egy már meglévő Kubernetes-fürtre. 


## <a name="deploy-azure-arc-data-controller"></a>Az Azure Arc üzembe helyezése

Az adatkezelő üzembe helyezése előtt létre kell hoznia egy névteret.

### <a name="create-namespace"></a>Névtér létrehozása 

Hozzon létre egy új, dedikált névteret, amelyben üzembe fogja helyezni az adatkezelőt. Létrehoz egy felhasználót is, majd hozzáférést biztosít a felhasználónak a létrehozott névtérhez. 

> [!NOTE]
> A névtérre és a felhasználónevekre is érvényesek a [DNS-altartományok elnevezési konvenciói.](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names)

1. [Csatlakozzon a PowerShell felülethez.](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)
1. Hozzon létre egy névteret. Típus:

    `New-HcsKubernetesNamespace -Namespace <Name of namespace>`

1. Hozzon létre egy felhasználót. Típus: 

    `New-HcsKubernetesUser -UserName <User name>`

1. Egy konfigurációs fájl egyszerű szövegként jelenik meg. Másolja ki ezt a fájlt, és mentse konfigurációs *fájlként.* 

    > [!IMPORTANT]
    > Ne mentse a konfigurációs fájlt *.txt* fájlként, és fájlkiterjesztés nélkül mentse a fájlt.

1. A konfigurációs fájlnak a helyi gép felhasználói profiljának mappájában `.kube` kell lennie. Másolja a fájlt a felhasználói profiljában erre a mappára.

    ![Konfigurációs fájl helye az ügyfélen](media/azure-stack-edge-gpu-create-kubernetes-cluster/location-config-file.png)
1. Adjon hozzáférést a felhasználónak a létrehozott névtérhez. Típus: 

    `Grant-HcsKubernetesNamespaceAccess -Namespace <Name of namespace> -UserName <User name>`

    Példa az előző parancsok kimenetére. Ebben a példában létrehozunk egy névteret, egy felhasználót, és hozzáférést biztosítunk a felhasználónak a `myadstest` `myadsuser` névtérhez.
    
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
1. Adjon hozzá egy DNS-bejegyzést a gazdagépfájlhoz a rendszeren. 

    1. Futtassa rendszergazdaként a Jegyzettömböt, és nyissa meg `hosts` a következő helyen található fájlt: `C:\windows\system32\drivers\etc\hosts` . 
    2. A gazdagépfájlban a  helyi felhasználói felületen az Eszköz lapról mentett adatokat (előfeltétel) használhatja a bejegyzés létrehozásához. 

        Például másolja ezt a végpontot a következő bejegyzés létrehozásához az eszköz `https://compute.myasegpudev.microsoftdatabox.com/[10.100.10.10]` IP-címével és DNS-tartományával: 

        `10.100.10.10 compute.myasegpudev.microsoftdatabox.com`

1. Annak ellenőrzéséhez, hogy tud-e csatlakozni a Kubernetes-podhoz, indítson el egy parancssort vagy egy PowerShell-munkamenetet. Típus:
    
    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n "myadstest"
    No resources found.
    PS C:\WINDOWS\system32>
    ```
Most már üzembe helyezheti az adatkezelő és az adatszolgáltatások alkalmazásait a névtérben, majd megtekintheti az alkalmazásokat és a naplókat.

### <a name="create-data-controller"></a>Adatkezelő létrehozása

Az adatkezelő a Kubernetes-fürtön üzembe helyezett podok gyűjteménye, amelyek API-t, vezérlőszolgáltatást, bootstrappert, valamint monitorozási adatbázisokat és irányítópultokat biztosítanak. Az alábbi lépéseket követve hozzon létre egy adatkezelőt a kubernetes-fürtön, amely a Azure Stack Edge a korábban létrehozott névtérben található a saját eszközén.   

1. Gyűjtse össze az alábbi információkat, amelyekre szüksége lesz az adatkezelő létrehozásához:

    
    |1\. oszlop  |2\. oszlop  |
    |---------|---------|
    |Adatkezelő neve     |Az adatkezelő leíró neve. Például: `arctestdatacontroller`.         |
    |Adatkezelő felhasználóneve     |Az adatkezelő rendszergazdai felhasználójának bármely felhasználóneve. Az adatkezelő felhasználónevével és jelszavával hitelesíti magát az adatkezelő API-ban a rendszergazdai feladatok elvégzéséhez.          |
    |Adatkezelő jelszava     |Az adatkezelő rendszergazdai felhasználójának jelszava. Válasszon egy biztonságos jelszót, és ossza meg csak olyanokkal, akiknek fürt-rendszergazdai jogosultságokkal kell rendelkezik.         |
    |A Kubernetes-névtér neve     |Annak a Kubernetes-névtérnek a neve, amelybe az adatkezelőt létre szeretné hozni.         |
    |Azure-előfizetés azonosítója     |Az Azure-előfizetés GUID-ja, ahol létre szeretné hozatni az Adatkezelő erőforrást az Azure-ban.         |
    |Azure-erőforráscsoport neve     |Annak az erőforráscsoportnak a neve, ahol létre szeretné hozatni az Adatkezelő erőforrást az Azure-ban.         |
    |Azure-hely     |Az Azure-hely, ahol az adatkezelő erőforrás-metaadatait a rendszer az Azure-ban tárolja. Az elérhető régiók listájáért lásd: Azure globális infrastruktúra /Termékek régiónként.|


1. Csatlakozzon a PowerShell-felülethez. Az adatkezelő létrehozásához írja be a következőt: 

    ```powershell
    Set-HcsKubernetesAzureArcDataController -SubscriptionId <Subscription ID> -ResourceGroupName <Resource group name> -Location <Location without spaces> -UserName <User you created> -Password <Password to authenticate to Data Controller> -DataControllerName <Data Controller Name> -Namespace <Namespace you created>    
    ```
    Példa az előző parancsok kimenetére.

    ```powershell
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcDataController -SubscriptionId db4e2fdb-6d80-4e6e-b7cd-736098270664 -ResourceGroupName myasegpurg -Location "EastUS" -UserName myadsuser -Password "Password1" -DataControllerName "arctestcontroller" -Namespace myadstest
    [10.100.10.10]: PS> 
    ```
    
    Az üzembe helyezés körülbelül 5 percet vehet igénybe.

    > [!NOTE]
    > A kubernetes-fürtön létrehozott adatkezelő Azure Stack Edge Pro GPU-eszköz csak leválasztott módban működik az aktuális kiadásban. A leválasztott mód az adatkezelőre, és nem az eszközére van be stb.

### <a name="monitor-data-creation-status"></a>Adat-létrehozási állapot figyelése

1. Nyisson meg egy másik PowerShell-ablakot.
1. A következő `kubectl` paranccsal figyelheti az adatkezelő létrehozási állapotát. 

    ```powershell
    kubectl get datacontroller/<Data controller name> --namespace <Name of your namespace>
    ```
    A vezérlő létrehozásakor az állapotnak a következőnek kell lennie: `Ready` .
    Példa az előző parancs kimenetére:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get datacontroller/arctestcontroller --namespace myadstest
    NAME                STATE
    arctestcontroller   Ready
    PS C:\WINDOWS\system32>
    ```
1. Az adatkezelőn futó külső szolgáltatásokhoz rendelt IP-k azonosításához használja az `kubectl get svc -n <namespace>` parancsot. Itt látható egy mintakimenet:

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

Miután sikeresen létrehozta az adatkezelőt, egy sablonnal üzembe helyezhet egy SQL Managed Instance az adatkezelőn.

### <a name="deployment-template"></a>Üzembehelyezési sablon

Az alábbi üzembe helyezési sablonnal üzembe helyezhet egy SQL Managed Instance az eszköz adatkezelőjére.

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
    
A metaadatok neve a SQL Managed Instance. Az előzőben társított pod neve a következő `deployment.yaml` lesz: `sqlex-n` ( az `n` alkalmazáshoz társított podok száma). 
    
#### <a name="password-and-username-data"></a>Jelszó- és felhasználónév-adatok

Az adatkezelő felhasználónevével és jelszavával hitelesítheti magát az adatkezelő API-ban a felügyeleti feladatok elvégzéséhez. Az üzembe helyezési sablonban az adatkezelő felhasználónevéhez és jelszavához tartozó Kubernetes titkos kód base64 kódolású sztring. 

Egy online eszközzel base64 használatával kódolhatja a kívánt felhasználónevet és jelszót, vagy használhat beépített CLI-eszközöket a platformtól függően. Online Base64-kódoló eszköz használata esetén adja meg az eszközben a felhasználónév és a jelszó sztringeket (amelyek az adatkezelő létrehozásakor vannak megadva), és az eszköz létrehozza a megfelelő Base64 kódolású sztringeket.
    
#### <a name="service-type"></a>Szolgáltatás típusa

A szolgáltatástípust a következőre kell `LoadBalancer` beállítani: .
    
#### <a name="storage-class-name"></a>Tárolási osztály neve

A tárolóeszközön azonosíthatja a tárolóosztályt Azure Stack Edge az üzembe helyezés adatokat, biztonsági másolatokat, adatnaplókat és naplókat fog használni. Az  `kubectl get storageclass` paranccsal leküldheti az eszközön üzembe helyezett tárolási osztályt.

```powershell
PS C:\WINDOWS\system32> kubectl get storageclass
NAME             PROVISIONER      RECLAIMPOLICY  VOLUMEBINDINGMODE     ALLOWVOLUMEEXPANSION   AGE
ase-node-local   rancher.io/local-path   Delete  WaitForFirstConsumer  false                  5d23h
PS C:\WINDOWS\system32>
```
Az előző példakimenetben az eszközön található tárolási osztályt `ase-node-local` meg kell adni a sablonban.
 
#### <a name="spec"></a>Spec

Ha új SQL Managed Instance a Azure Stack Edge eszközén, a memória- és CPU-követelményeket a specifikáció szakaszában adhatja `deployment.yaml` meg. Minden felügyelt SQL-példánynak legalább 2 GB memóriát és 1 processzormagot kell igényelnie az alábbi példában látható módon. 

```yml
spec:
    limits:
    memory: 4Gi
    vcores: "4"
    requests:
    memory: 2Gi
    vcores: "1"
```  

Az adatkezelő részletes méretezési útmutatóját és az 1 SQL Managed Instance SQL Managed [Instance méretezési részleteit ismertető cikk tartalmazza.](../azure-arc/data/sizing-guidance.md#sql-managed-instance-sizing-details)

### <a name="run-deployment-template"></a>Üzembe helyezési sablon futtatása

Futtassa `deployment.yaml` a parancsot a következő paranccsal:

```powershell
kubectl create -n <Name of namespace that you created> -f <Path to the deployment yaml> 
```

Az alábbi parancs egy példakimenete:

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

A minta kimenetében a pod `sqlex-0` jelzi a SQL Managed Instance.

## <a name="remove-data-controller"></a>Adatkezelő eltávolítása

Az adatkezelő eltávolításához törölje azt a dedikált névteret, amelyben üzembe helyezett.


```powershell
kubectl delete ns <Name of your namespace>
```


## <a name="next-steps"></a>Következő lépések

- [Állapot nélküli alkalmazás üzembe helyezése a Azure Stack Edge Pro.](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md)

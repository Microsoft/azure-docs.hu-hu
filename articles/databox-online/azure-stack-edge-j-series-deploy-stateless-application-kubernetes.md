---
title: Kubernetes állapot nélküli alkalmazás üzembe helyezése Azure Stack Edge Pro GPU-eszközön a kubectl használatával | Microsoft Docs
description: Ismerteti, hogyan hozhat létre és kezelhet Kubernetes állapot nélküli alkalmazások telepítését Microsoft Azure Stack Edge Pro-eszközön a kubectl használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: bc8d14f3d4621a41c2d2845802313033d0809b4b
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102442128"
---
# <a name="deploy-a-kubernetes-stateless-application-via-kubectl-on-your-azure-stack-edge-pro-gpu-device"></a>Kubernetes állapot nélküli alkalmazás üzembe helyezése az Azure Stack Edge Pro GPU-eszközön a kubectl használatával

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Ez a cikk azt ismerteti, hogyan helyezhet üzembe egy állapot nélküli alkalmazást egy meglévő Kubernetes-fürtön lévő kubectl-parancsokkal. Ez a cikk végigvezeti a hüvelyek állapot nélküli alkalmazásokban való létrehozásának és beállításának folyamatán is.

## <a name="prerequisites"></a>Előfeltételek

A Kubernetes-fürt létrehozása és a `kubectl` parancssori eszköz használata előtt gondoskodnia kell a következőkről:

- A bejelentkezési hitelesítő adatok egy 1 csomópontos Azure Stack Edge Pro-eszközhöz tartoznak.

- A Windows PowerShell 5,0-es vagy újabb verziója Windows-ügyfélre van telepítve az Azure Stack Edge Pro-eszköz eléréséhez. Bármely más ügyfél támogatott operációs rendszerrel is rendelkezhet. Ez a cikk a Windows-ügyfelek használatakor követendő eljárást ismerteti. A Windows PowerShell legújabb verziójának letöltéséhez nyissa meg a [Windows PowerShell telepítését](/powershell/scripting/install/installing-windows-powershell?view=powershell-7&preserve-view=true).

- A számítási funkció engedélyezve van az Azure Stack Edge Pro-eszközön. A számítás engedélyezéséhez lépjen a **számítási** lapra az eszköz helyi felhasználói felületén. Ezután válasszon ki egy hálózati adaptert, amelyet engedélyezni szeretne a számítási feladatokhoz. Válassza az **Engedélyezés** lehetőséget. A számítási eredmények lehetővé teszik, hogy az eszközön egy virtuális kapcsolót hozzanak létre az adott hálózati adapteren. További információ: a [számítási hálózat engedélyezése a Azure stack Edge Pro](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)-ban.

- Az Azure Stack Edge Pro-eszközön egy, a v 1.9-es vagy újabb verzióját futtató Kubernetes-fürt található. További információ: [Kubernetes-fürt létrehozása és kezelése Microsoft Azure stack Edge Pro-eszközön](azure-stack-edge-gpu-create-kubernetes-cluster.md).

- Telepítette `kubectl` .

## <a name="deploy-a-stateless-application"></a>Állapot nélküli alkalmazás üzembe helyezése

A Kezdés előtt a következőket kell tennie:

1. Létrehozott egy Kubernetes-fürtöt.
2. Hozzon létre egy névteret.
3. Egy felhasználó társítva a névtérhez.
4. A felhasználó konfigurációjának mentése a következőre: `C:\Users\<username>\.kube` .
5. Telepítve `kubectl` .

Most már megkezdheti az állapot nélküli alkalmazások üzembe helyezésének és kezelésének megkezdését egy Azure Stack Edge Pro rendszerű eszközön. A használatának megkezdése előtt `kubectl` ellenőriznie kell, hogy a megfelelő verziójú-e `kubectl` .

### <a name="verify-you-have-the-correct-version-of-kubectl-and-set-up-configuration"></a>Ellenőrizze, hogy rendelkezik-e a kubectl megfelelő verziójával, és állítsa be a konfigurációt

A verziójának ellenőrzését `kubectl` :

1. Ellenőrizze, hogy a verziószáma `kubectl` nagyobb vagy egyenlő-e, mint 1,9:

   ```powershell
   kubectl version
   ```
    
   Alább látható egy példa a kimenetre:
    
   ```powershell
   PS C:\WINDOWS\system32> C:\windows\system32\kubectl.exe version
   Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.2", GitCommit:"f6278300bebbb750328ac16ee6dd3aa7d3549568", GitTreeState:"clean", BuildDate:"2019-08-05T09:23:26Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"windows/amd64"}
   Server Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.1", GitCommit:"4485c6f18cee9a5d3c3b4e523bd27972b1b53892", GitTreeState:"clean", BuildDate:"2019-07-18T09:09:21Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
   ```

   Ebben az esetben a kubectl ügyfél-verziója v 1.15.2, és kompatibilis a folytatással.

2. A Kubernetes-fürtön futó hüvelyek listájának beolvasása. A pod egy alkalmazás-tároló vagy folyamat, amely a Kubernetes-fürtön fut.

   ```powershell
   kubectl get pods -n <namespace-string>
   ```
    
   Alább látható egy példa a parancs használatára:
    
   ```powershell
   PS C:\WINDOWS\system32> kubectl get pods -n "test1"
   No resources found.
   PS C:\WINDOWS\system32>
   ```
    
   A kimenetnek olyannak kell lennie, hogy nem található erőforrás (hüvely), mert a fürtön nincsenek alkalmazások.

   A parancs feltölti a "C:\Users username. Kube" nevű címtár-struktúrát a \\ &lt; &gt; \\ \" konfigurációs fájlokkal. A kubectl parancssori eszköz ezeket a fájlokat fogja használni az állapot nélküli alkalmazások Kubernetes-fürtön való létrehozásához és kezeléséhez.

3. Manuálisan ellenőrizze a "C:\Users \\ &lt; username. Kube" könyvtár-struktúráját &gt; \\ \" annak ellenőrzéséhez, hogy a *kubectl* a következő almappákkal van-e feltöltve:

   ```powershell
   PS C:\Users\username> ls .kube
    
    
      Directory: C:\Users\user\.kube
    
   Mode                LastWriteTime         Length Name
   ----                -------------         ------ ----
   d-----         2/18/2020 11:05 AM                cache
   d-----         2/18/2020 11:04 AM                http-cache
   -a----         2/18/2020 10:41 AM           5377 config
   ```

> [!NOTE]
> Az összes kubectl-parancs listájának megtekintéséhez írja be a következőt: `kubectl --help` .

### <a name="create-a-stateless-application-using-a-deployment"></a>Állapot nélküli alkalmazás létrehozása központi telepítéssel

Most, hogy ellenőrizte, hogy a kubectl parancssori verziója helyes-e, és hogy rendelkezik-e a szükséges konfigurációs fájlokkal, létrehozhat egy állapot nélküli alkalmazás-telepítést.

A pod egy Kubernetes-alkalmazás alapszintű végrehajtási egysége, a Kubernetes objektummodell legkisebb és legegyszerűbb egysége, amelyet Ön hozott létre vagy telepít. A pod a tárolási erőforrásokat, az egyedi hálózati IP-címet és a tároló (k) futtatását szabályozó beállításokat is magában foglal.

A létrehozott állapot nélküli alkalmazás típusa egy Nginx webkiszolgáló-telepítés.

Az állapot nélküli alkalmazások központi telepítésének létrehozásához és kezeléséhez használt összes kubectl-parancsnak meg kell adnia a konfigurációhoz társított névteret. A névteret a Azure Stack Edge Pro eszközön a fürthöz való csatlakozáskor hozta létre a [Kubernetes-fürt létrehozása és kezelése Microsoft Azure stack Edge Pro-eszközön](azure-stack-edge-gpu-create-kubernetes-cluster.md) című oktatóanyagban `New-HcsKubernetesNamespace` .

A névtér kubectl-parancsban való megadásához használja a következőt: `kubectl <command> -n <namespace-string>` .

Az alábbi lépéseket követve hozzon létre egy Nginx-telepítést:

1. Állapot nélküli alkalmazás alkalmazása Kubernetes központi telepítési objektum létrehozásával:

   ```powershell
   kubectl apply -f <yaml-file> -n <namespace-string>
   ```

   Ebben a példában az alkalmazás YAML-fájljának elérési útja külső forrás.

   Íme egy példa a parancs használatára és kimenetére:

   ```powershell
   PS C:\WINDOWS\system32> kubectl apply -f https://k8s.io/examples/application/deployment.yaml -n "test1"
    
   deployment.apps/nginx-deployment created
   ```

   Azt is megteheti, hogy a következő Markdown menti a helyi gépre, és az *-f* paraméterben lecseréli az elérési utat és a fájlnevet. Például: "C:\Kubernetes\deployment.yaml". Az alkalmazás központi telepítésének konfigurációja a következő lesz:

   ```markdown
   apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
   kind: Deployment
   metadata:
     name: nginx-deployment
   spec:
     selector:
       matchLabels:
         app: nginx
     replicas: 2 # tells deployment to run 2 pods matching the template
     template:
       metadata:
         labels:
           app: nginx
       spec:
         containers:
         - name: nginx
           image: nginx:1.7.9
           ports:
           - containerPort: 80
   ```

   Ez a parancs egy alapértelmezett Nginx-telepítést hoz létre, amely két hüvelyből áll az alkalmazás futtatásához.

2. Szerezze be a létrehozott Kubernetes Nginx-Telepítés leírását:

   ```powershell
   kubectl describe deployment nginx-deployment -n <namespace-string>
   ```

   Az alábbi, kimenettel rendelkező parancs minta-használata alább látható:
    
   ```powershell
   PS C:\Users\user> kubectl describe deployment nginx-deployment -n "test1"
    
   Name:                   nginx-deployment
   Namespace:              test1
   CreationTimestamp:      Tue, 18 Feb 2020 13:35:29 -0800
   Labels:                 <none>
   Annotations:            deployment.kubernetes.io/revision: 1
                           kubectl.kubernetes.io/last-applied-configuration:
                             {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"nginx-deployment","namespace":"test1"},"spec":{"repl...
   Selector:               app=nginx
   Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
   StrategyType:           RollingUpdate
   MinReadySeconds:        0
   RollingUpdateStrategy:  25% max unavailable, 25% max surge
   Pod Template:
      Labels:  app=nginx
      Containers:
       nginx:
        Image:        nginx:1.7.9
        Port:         80/TCP
        Host Port:    0/TCP
        Environment:  <none>
        Mounts:       <none>
      Volumes:        <none>
   Conditions:
      Type           Status  Reason
      ----           ------  ------
      Available      True    MinimumReplicasAvailable
      Progressing    True    NewReplicaSetAvailable
   OldReplicaSets:  <none>
   NewReplicaSet:   nginx-deployment-5754944d6c (2/2 replicas created)
   Events:
     Type    Reason             Age    From                   Message
     ----    ------             ----   ----                   -------
     Normal  ScalingReplicaSet  2m22s  deployment-controller  Scaled up replica set nginx-deployment-5754944d6c to 2
   ```

   A *replikák* beállításnál a következő jelenik meg:
    
   ```powershell
   Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
   ```

   A *replikák* beállítás azt jelzi, hogy a központi telepítési specifikáció két hüvelyt igényel, és hogy ezek a hüvelyek létrejöttek és frissültek, és készen állnak a használatra.

   > [!NOTE]
   > Egy replikakészlet olyan hüvelyeket cserél le, amelyeket bármilyen okból törölnek vagy leállítottak, például az eszköz csomópontjának meghibásodása vagy egy zavaró eszköz frissítése esetén. Ezért azt javasoljuk, hogy akkor is használjon replikakészlet-készletet, ha az alkalmazás csak egyetlen Pod-t igényel.

3. Az üzemelő példányban található hüvelyek listázása:

   ```powershell
   kubectl get pods -l app=nginx -n <namespace-string>
   ```
    
   Az alábbi, kimenettel rendelkező parancs minta-használata alább látható:
    
   ```powershell
   PS C:\Users\user> kubectl get pods -l app=nginx -n "test1"
    
   NAME                                READY   STATUS    RESTARTS   AGE
   nginx-deployment-5754944d6c-7wqjd   1/1     Running   0          3m13s
   nginx-deployment-5754944d6c-nfj2h   1/1     Running   0          3m13s
   ```

   A kimenet ellenőrzi, hogy van-e két olyan egyedi névvel rendelkező hüvely, amely hivatkozhat a kubectl használatával.

4. Az üzemelő példányban található egyedi Pod-adatok megtekintése:

   ```powershell
   kubectl describe pod <podname-string> -n <namespace-string>
   ```

  Az alábbi, kimenettel rendelkező parancs minta-használata alább látható:

   ```powershell
   PS C:\Users\user> kubectl describe pod "nginx-deployment-5754944d6c-7wqjd" -n "test1"

   Name:           nginx-deployment-5754944d6c-7wqjd
   Namespace:      test1
   Priority:       0
   Node:           k8s-1d9qhq2cl-n1/10.128.46.184
   Start Time:     Tue, 18 Feb 2020 13:35:29 -0800
   Labels:         app=nginx
                   pod-template-hash=5754944d6c
   Annotations:    <none>
   Status:         Running
   IP:             172.17.246.200
   Controlled By:  ReplicaSet/nginx-deployment-5754944d6c
    Containers:
      nginx:
        Container ID:   docker://280b0f76bfdc14cde481dc4f2b8180cf5fbfc90a084042f679d499f863c66979
        Image:          nginx:1.7.9
        Image ID:       docker-pullable://nginx@sha256:e3456c851a152494c3e4ff5fcc26f240206abac0c9d794affb40e0714846c451
        Port:           80/TCP
        Host Port:      0/TCP
        State:          Running
          Started:      Tue, 18 Feb 2020 13:35:35 -0800
        Ready:          True
        Restart Count:  0
        Environment:    <none>
        Mounts:
          /var/run/secrets/kubernetes.io/serviceaccount from default-token-8gksw (ro)
    Conditions:
      Type              Status
      Initialized       True
      Ready             True
      ContainersReady   True
      PodScheduled      True
    Volumes:
      default-token-8gksw:
        Type:        Secret (a volume populated by a Secret)
        SecretName:  default-token-8gksw
        Optional:    false
    QoS Class:       BestEffort
    Node-Selectors:  <none>
    Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                     node.kubernetes.io/unreachable:NoExecute for 300s
    Events:
      Type    Reason     Age    From                       Message
      ----    ------     ----   ----                       -------
      Normal  Scheduled  4m58s  default-scheduler          Successfully assigned test1/nginx-deployment-5754944d6c-7wqjd to k8s-1d9qhq2cl-n1
      Normal  Pulling    4m57s  kubelet, k8s-1d9qhq2cl-n1  Pulling image "nginx:1.7.9"
      Normal  Pulled     4m52s  kubelet, k8s-1d9qhq2cl-n1  Successfully pulled image "nginx:1.7.9"
      Normal  Created    4m52s  kubelet, k8s-1d9qhq2cl-n1  Created container nginx
      Normal  Started    4m52s  kubelet, k8s-1d9qhq2cl-n1  Started container nginx
   ```

### <a name="rescale-the-application-deployment-by-increasing-the-replica-count"></a>Az alkalmazás központi telepítésének átméretezése a replikák számának növelésével

Mindegyik pod egy adott alkalmazás egyetlen példányának futtatását jelenti. Ha horizontálisan szeretné több példány futtatására méretezni az alkalmazást, a hüvelyek számát megnövelheti egy értékre az egyes példányok esetében. A Kubernetes-ben ez az úgynevezett replikálás.
Egy új YAML-fájl alkalmazásával növelheti a hüvelyek számát az alkalmazás központi telepítésében. A YAML fájl a replikák beállítását 4 értékre módosítja, ami növeli a telepítésben lévő hüvelyek számát négy hüvelyre. A 2 és 4 közötti hüvelyek számának növeléséhez:

```powershell
PS C:\WINDOWS\system32> kubectl apply -f https://k8s.io/examples/application/deployment-scale.yaml -n "test1"
```

Azt is megteheti, hogy a következő Markdown menti a helyi gépre, és az *-f* paraméter elérési útját és fájlnevét kicseréli `kubectl apply` . Például: "C:\Kubernetes\deployment-scale.yaml". Az alkalmazás központi telepítési skálázásának konfigurációja a következő:

```markdown
apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 4 # Update the replicas from 2 to 4
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.8
        ports:
        - containerPort: 80
```

Annak ellenőrzéséhez, hogy a központi telepítés négy hüvelyből áll:

```powershell
kubectl get pods -l app=nginx
```

Példa a két-négy hüvelyre történő átméretezési üzembe helyezés kimenetére alább látható:

```powershell
PS C:\WINDOWS\system32> kubectl get pods -l app=nginx

NAME                               READY     STATUS    RESTARTS   AGE
nginx-deployment-148880595-4zdqq   1/1       Running   0          25s
nginx-deployment-148880595-6zgi1   1/1       Running   0          25s
nginx-deployment-148880595-fxcez   1/1       Running   0          2m
nginx-deployment-148880595-rwovn   1/1       Running   0          2m
```

Ahogy a kimenetben látható, már négy hüvelye van az üzembe helyezésben, amely képes futtatni az alkalmazást.

### <a name="delete-a-deployment"></a>Központi telepítés törlése

Az üzemelő példány törléséhez, beleértve az összes hüvelyt is, futtatnia kell a `kubectl delete deployment` telepítési *Nginx-Telepítés* és a névtér nevének megadásával. Az üzemelő példány törlése:

   ```powershell
   kubectl delete deployment nginx-deployment -n <namespace-string>
   ```

Alább látható a parancs használatának példája, amelynek kimenete a következő:

```powershell
PS C:\Users\user> kubectl delete deployment nginx-deployment -n "test1"
deployment.extensions "nginx-deployment" deleted
```

## <a name="next-steps"></a>Következő lépések

[A Kubernetes áttekintése](azure-stack-edge-gpu-kubernetes-overview.md)
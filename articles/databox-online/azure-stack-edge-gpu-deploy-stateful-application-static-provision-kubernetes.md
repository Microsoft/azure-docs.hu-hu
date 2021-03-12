---
title: A kubectl használata a Kubernetes állapot-nyilvántartó alkalmazás üzembe helyezéséhez statikusan kiosztott megosztáson keresztül Azure Stack Edge Pro-eszközön | Microsoft Docs
description: Ismerteti, hogyan hozható létre és kezelhető egy Kubernetes állapot-nyilvántartó alkalmazás üzembe helyezése statikusan kiosztott megosztáson keresztül a kubectl használatával egy Azure Stack Edge Pro GPU-eszközön.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/09/2021
ms.author: alkohli
ms.openlocfilehash: 01ba8e1f22deb376fd461be24d3f66f0a7f5e1ae
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102610484"
---
# <a name="use-kubectl-to-run-a-kubernetes-stateful-application-with-a-persistentvolume-on-your-azure-stack-edge-pro-device"></a>Kubernetes állapot-nyilvántartó alkalmazás futtatása a kubectl használatával a Azure Stack Edge Pro-eszközön PersistentVolume

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Ez a cikk bemutatja, hogyan helyezhet üzembe egy egypéldányos állapotú alkalmazást a Kubernetes egy PersistentVolume (PV) és egy központi telepítés használatával. A központi telepítés `kubectl` egy meglévő Kubernetes-fürtön lévő parancsokat használ, és üzembe helyezi a MySQL-alkalmazást. 

Ez az eljárás azon felhasználók számára készült, akik áttekintették a [Kubernetes-tárolót Azure stack Edge Pro-eszközön](azure-stack-edge-gpu-kubernetes-storage.md) , és ismerik a [Kubernetes-tároló](https://kubernetes.io/docs/concepts/storage/)fogalmait. 

A Azure Stack Edge Pro támogatja az Azure SQL Edge-tárolók futtatását is, és ezek a MySQL-hez hasonló módon telepíthetők. További információ: [Azure SQL Edge](../azure-sql-edge/overview.md).


## <a name="prerequisites"></a>Előfeltételek

Az állapot-nyilvántartó alkalmazás üzembe helyezése előtt végezze el a következő előfeltételeket az eszközön és az ügyfélen, amelyet az eszköz eléréséhez fog használni:

### <a name="for-device"></a>Az eszköz esetén

- A bejelentkezési hitelesítő adatok egy 1 csomópontos Azure Stack Edge Pro-eszközhöz tartoznak.
    - Az eszköz aktiválva van. Lásd: [az eszköz aktiválása](azure-stack-edge-gpu-deploy-activate.md).
    - Az eszközön a Azure Portal-n keresztül konfigurált számítási szerepkör és egy Kubernetes-fürt van konfigurálva. Lásd: [számítás konfigurálása](azure-stack-edge-gpu-deploy-configure-compute.md).

### <a name="for-client-accessing-the-device"></a>Az eszközt elérő ügyfél

- Van egy Windows-ügyfélrendszer, amely az Azure Stack Edge Pro-eszköz elérésére szolgál majd.
    - Az ügyfél Windows PowerShell 5,0-es vagy újabb verzióját futtatja. A Windows PowerShell legújabb verziójának letöltéséhez nyissa meg a következőt: [install Windows PowerShell](/powershell/scripting/install/installing-windows-powershell).
    
    - Bármely más ügyfél [támogatott operációs rendszerrel](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) is rendelkezhet. Ez a cikk a Windows-ügyfelek használatakor követendő eljárást ismerteti. 
    
    - Végrehajtotta az [Azure stack Edge Pro-eszközön a Kubernetes-fürt eléréséhez](azure-stack-edge-gpu-create-kubernetes-cluster.md)című témakörben leírt eljárást. A következőket teheti:
      - `userns1`A parancs használatával létrehozott egy névteret `New-HcsKubernetesNamespace` . 
      - `user1`A parancs használatával létrehozott egy felhasználót `New-HcsKubernetesUser` . 
      - Hozzáférést kapott a `user1` `userns1` `Grant-HcsKubernetesNamespaceAccess` parancson keresztül.       
      - Telepítse az `kubectl` ügyfelet, és mentse a `kubeconfig` fájlt a felhasználói konfiguráció C: \\ Users \\ &lt; username &gt; \\ . Kube fájlba. 
    
    - Győződjön meg arról, hogy az `kubectl` ügyfél verziószáma nem több, mint egy, a Azure stack Edge Pro-eszközön futó Kubernetes-verzió. 
        - Ezzel a paranccsal `kubectl version` ellenőrizhető az ügyfélen futó kubectl verziója. Jegyezze fel a teljes verziót.
        - Az Azure Stack Edge Pro-eszköz helyi felhasználói felületén lépjen az **Áttekintés** elemre, és jegyezze fel a Kubernetes-szoftver számát. 
        - Ellenőrizze ezt a két verziót a támogatott Kubernetes-verzióban megadott leképezéssel való kompatibilitás érdekében.<!-- insert link--> 


Készen áll egy állapot-nyilvántartó alkalmazás üzembe helyezésére az Azure Stack Edge Pro-eszközön. 

## <a name="provision-a-static-pv"></a>Statikus PV kiépítése

A PV statikus kiépítéséhez létre kell hoznia egy megosztást az eszközön. Az alábbi lépéseket követve kiépítheti az SMB-megosztást. 

> [!NOTE]
> - Az ebben a útmutatóban használt példa nem működik az NFS-megosztásokkal. Az NFS-megosztások általában az Azure Stack Edge-eszközön, nem adatbázis-alkalmazásokkal is üzembe helyezhetők.
> - A tárolási köteteket használó állapot-nyilvántartó alkalmazások a tartós tárolás biztosításához való üzembe helyezéséhez ajánlott a használata `StatefulSet` . Ez a példa `Deployment` csak egy replikát használ, és fejlesztésre és tesztelésre alkalmas. 

1. Válassza ki, hogy szeretne-e peremhálózati megosztást vagy peremhálózati helyi megosztást létrehozni. Megosztás létrehozásához kövesse a [megosztás hozzáadása](azure-stack-edge-manage-shares.md#add-a-share) című témakör utasításait. Ügyeljen arra, hogy jelölje be a **megosztás Edge-számítással való használatának** jelölőnégyzetét.

    ![Edge helyi megosztás a PV-hez](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/edge-local-share-static-provision-1.png)

    1. Új megosztás létrehozása helyett, ha úgy dönt, hogy meglévő megosztást használ, csatlakoztatnia kell a megosztást.
    
        Az Azure Stack Edge-erőforrás Azure Portal lépjen a **megosztások** elemre. A meglévő megosztások listájában válassza ki a használni kívánt megosztást, és kattintson rá.

        ![Meglévő helyi megosztás kiválasztása a PV-hez](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/mount-edge-share-1.png)

    1. Ha a rendszer kéri, válassza a **Csatlakoztatás** és a csatlakoztatás megerősítése lehetőséget.  

        ![Meglévő helyi megosztás csatlakoztatása a PV-hez](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/mount-edge-share-2.png)

1. Jegyezze fel a megosztás nevét. A megosztás létrehozásakor a rendszer állandó kötet objektumot hoz létre a létrehozott SMB-megosztásnak megfelelő Kubernetes-fürtben. 

## <a name="deploy-mysql"></a>A MySQL üzembe helyezése

Most egy állapot-nyilvántartó alkalmazást fog futtatni egy Kubernetes-telepítés létrehozásával és a korábbi lépésben a PersistentVolumeClaim (PVC) használatával létrehozott PV-vel való csatlakoztatásával. 

Az `kubectl` állapot-nyilvántartó alkalmazások központi telepítésének létrehozásához és kezeléséhez használt összes parancsnak meg kell adnia a konfigurációhoz társított névteret. A névtér kubectl-parancsban való megadásához használja a következőt: `kubectl <command> -n <your-namespace>` .

1. Szerezze be a Kubernetes-fürtön futó hüvelyek listáját a névtérben. A pod egy alkalmazás-tároló vagy folyamat, amely a Kubernetes-fürtön fut.

   ```powershell
   kubectl get pods -n <your-namespace>
   ```
    
   Íme egy példa a parancs használatára:
    
   ```powershell
    C:\Users\user>kubectl get pods -n "userns1"
    No resources found in userns1 namespace.    
    C:\Users\user>
   ```
    
   A kimenetnek olyannak kell lennie, hogy nem található erőforrás (hüvely), mert a fürtön nincsenek alkalmazások.

1. A következő YAML-fájlokat fogja használni. A `mysql-deployment.yml` fájl leírja a MySQL-t futtató központi telepítést, és a PVC-re hivatkozik. A fájl határozza meg a kötet csatlakoztatását `/var/lib/mysql` , majd létrehoz egy olyan PVC-t, amely egy 20 GB-os kötetet keres. 

    Ezt a jogcímet olyan meglévő PV-nek kell megfelelnie, amelyet statikusan kiosztottak, amikor a megosztást az előző lépésben hozta létre. Az eszközön az egyes megosztásokhoz egy nagy PV 32 TB jön létre. A PV megfelel a PVC által meghatározott követelményeknek, és a PVC-t ehhez a PV-hez kell kötni.

    Másolja és mentse a következő `mysql-deployment.yml` fájlt egy olyan mappába, amelyet a Windows-ügyfélen használ az Azure stack Edge Pro-eszköz eléréséhez.
    
    ```yml
    apiVersion: v1
    kind: Service
    metadata:
      name: mysql
    spec:
      ports:
      - port: 3306
      selector:
        app: mysql
      clusterIP: None
    ---
    apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
    kind: Deployment
    metadata:
      name: mysql
    spec:
      selector:
        matchLabels:
          app: mysql
      strategy:
        type: Recreate
      template:
        metadata:
          labels:
            app: mysql
        spec:
          containers:
          - image: mysql:5.6
            name: mysql
            env:
              # Use secret in real usage
            - name: MYSQL_ROOT_PASSWORD
              value: password
            ports:
            - containerPort: 3306
              name: mysql
            volumeMounts:
            - name: mysql-persistent-storage
              mountPath: /var/lib/mysql
          volumes:
          - name: mysql-persistent-storage
            persistentVolumeClaim:
              claimName: mysql-pv-claim
    ```
    
2. Másolja és mentse fájlként egy olyan `mysql-pv.yml` mappába, ahová a fájlt mentette `mysql-deployment.yml` . Ahhoz, hogy használni lehessen a korábban létrehozott SMB-megosztást `kubectl` , állítsa be a `volumeName` PVC-objektum mezőjét a megosztás nevére. 

    > [!NOTE] 
    > Győződjön meg arról, hogy a YAML-fájlok helyes behúzással rendelkeznek. A [YAML](http://www.yamllint.com/) az ellenőrzéshez és a mentéshez használhatja.
   
    ```yml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: mysql-pv-claim
    spec:
      volumeName: <smb-share-name-here>
      storageClassName: ""
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 20Gi
    ```

3. Telepítse a `mysql-pv.yaml` fájlt.

    `kubectl apply -f <URI path to the mysql-pv.yml file> -n <your-user-namespace>`
    
    Íme egy példa az üzemelő példány kimenetére.

    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-pv.yml" -n userns1
    persistentvolumeclaim/mysql-pv-claim created
    
    C:\Users\user>
    ```
   Jegyezze fel a létrehozott PVC nevét. Ezt egy későbbi lépésben fogja használni. 

4. Telepítse a `mysql-deployment.yml` fájl tartalmát.

    `kubectl apply -f <URI path to mysql-deployment.yml file> -n <your-user-namespace>`

    Íme egy példa az üzemelő példány kimenetére.
    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-deployment.yml" -n userns1
        service/mysql created
        deployment.apps/mysql created
    ```
    
5. A központi telepítésre vonatkozó információk megjelenítése.

    `kubectl describe deployment <app-label> -n <your-user-namespace>`
    
    ```powershell
    C:\Users\user>kubectl describe deployment mysql -n userns1
    Name:               mysql
    Namespace:          userns1
    CreationTimestamp:  Tue, 18 Aug 2020 09:44:58 -0700
    Labels:             <none>
    Annotations:        deployment.kubernetes.io/revision: 1
                        kubectl.kubernetes.io/last-applied-configuration:
                          {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"mysql","namespace":"userns1"},"spec":{"selector":{"matchL...
    Selector:           app=mysql
    Replicas:           1 desired | 1 updated | 1 total | 1 available | 0 unavailable
    StrategyType:       Recreate
    MinReadySeconds:    0
    Pod Template:
      Labels:  app=mysql
      Containers:
       mysql:
        Image:      mysql:5.6
        Port:       3306/TCP
        Host Port:  0/TCP
        Environment:
          MYSQL_ROOT_PASSWORD:  password
        Mounts:
          /var/lib/mysql from mysql-persistent-storage (rw)
      Volumes:
       mysql-persistent-storage:
        Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
        ClaimName:  mysql-pv-claim
        ReadOnly:   false
    Conditions:
      Type           Status  Reason
      ----           ------  ------
      Progressing    True    NewReplicaSetAvailable
      Available      True    MinimumReplicasAvailable
    OldReplicaSets:  <none>
    NewReplicaSet:   mysql-c85f7f79c (1/1 replicas created)
    Events:
      Type    Reason             Age   From                   Message
      ----    ------             ----  ----                   -------
      Normal  ScalingReplicaSet  10m   deployment-controller  Scaled up replica set mysql-c85f7f79c to 1
    
    C:\Users\user>
    ```
    

6. Az üzemelő példány által létrehozott hüvelyek listázása.

    `kubectl get pods -l <app=label> -n <your-user-namespace>`

    Íme egy példa kimenet.

    
    ```powershell
    C:\Users\user>kubectl get pods -l app=mysql -n userns1
    NAME                    READY   STATUS    RESTARTS   AGE
    mysql-c85f7f79c-vzz7j   1/1     Running   1          14m
    
    C:\Users\user>
    ```
    
7. Vizsgálja meg a PersistentVolumeClaim.

    `kubectl describe pvc <your-pvc-name>`

    Íme egy példa kimenet.

    
    ```powershell
    C:\Users\user>kubectl describe pvc mysql-pv-claim -n userns1
    Name:          mysql-pv-claim
    Namespace:     userns1
    StorageClass:
    Status:        Bound
    Volume:        mylocalsmbshare1
    Labels:        <none>
    Annotations:   kubectl.kubernetes.io/last-applied-configuration:
                     {"apiVersion":"v1","kind":"PersistentVolumeClaim","metadata":{"annotations":{},"name":"mysql-pv-claim","namespace":"userns1"},"spec":{"acc...
                   pv.kubernetes.io/bind-completed: yes
    Finalizers:    [kubernetes.io/pvc-protection]
    Capacity:      32Ti
    Access Modes:  RWO,RWX
    VolumeMode:    Filesystem
    Mounted By:    mysql-c85f7f79c-vzz7j
    Events:        <none>
    
    C:\Users\user>
    ```
    

## <a name="verify-mysql-is-running"></a>Ellenőrizze, hogy fut-e a MySQL


Ha MySQL-t futtató Pod tárolón szeretne futtatni egy parancsot, írja be a következőt:

`kubectl exec <your-pod-with-the-app> -i -t -n <your-namespace> -- mysql`

Íme egy példa kimenet.

```powershell
C:\Users\user>kubectl exec mysql-c85f7f79c-vzz7j -i -t -n userns1 -- mysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 1
Server version: 5.6.49 MySQL Community Server (GPL)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

## <a name="delete-a-deployment"></a>Központi telepítés törlése

A központi telepítés törléséhez törölje a központilag telepített objektumokat a név alapján. Ezek az objektumok a következőket tartalmazzák: üzembe helyezés, szolgáltatás és PVC.
 
```powershell
kubectl delete deployment <deployment-name>,svc <service-name> -n <your-namespace>
kubectl delete pvc <your-pvc-name> -n <your-namespace>
```

Itt látható a kimenete, amikor törli az üzemelő példányt és a szolgáltatást.

```powershell
C:\Users\user>kubectl delete deployment,svc mysql -n userns1
deployment.apps "mysql" deleted
service "mysql" deleted
C:\Users\user>
```
A következő minta kimenete látható a PVC törlésekor.

```powershell
C:\Users\user>kubectl delete pvc mysql-pv-claim -n userns1
persistentvolumeclaim "mysql-pv-claim" deleted
C:\Users\user>
```

A PV már nem kötődik a PVC-hez, mert a PVC törölve lett. Mivel a rendszer a megosztás létrehozásakor kiosztotta a PV-t, törölnie kell a megosztást. Kövesse az alábbi lépéseket:

1. Válassza le a megosztást. Azure Portal az **Azure stack Edge erőforrás > megosztások** területen válassza ki, majd kattintson a leválasztani kívánt megosztásra. Válassza a **Leválasztás** lehetőséget, és erősítse meg a műveletet. Várjon, amíg a megosztás le van választva. A leválasztás megszünteti a megosztást (és így a társított PersistentVolume) a Kubernetes-fürtből. 

    ![A PV helyi megosztásának leválasztása](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/unmount-edge-local-share-1.png)

1. Mostantól **a törlés** lehetőségre kattintva törölheti a megosztást. Emellett törölje a megosztást és a megfelelő PV-t is.

    ![Helyi megosztás törlése a PV-hez](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/delete-edge-local-share-1.png)


## <a name="next-steps"></a>Következő lépések

A tárolók dinamikus kiépítésének megismeréséhez tekintse [meg az állapot-nyilvántartó alkalmazások üzembe helyezése dinamikus kiépítés Azure stack Edge Pro-eszközön keresztül](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md) című témakört.

---
title: A kubectl használata a Kubernetes állapot-nyilvántartó alkalmazás üzembe helyezéséhez dinamikusan kiosztott megosztáson keresztül Azure Stack Edge Pro GPU-eszközön | Microsoft Docs
description: Ismerteti, hogyan hozható létre és kezelhető egy Kubernetes állapot-nyilvántartó alkalmazás központi telepítése egy Microsoft Azure Stack Edge Pro GPU-eszközön a kubectl használatával dinamikusan kiosztott megosztáson keresztül.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 160ba6014bbb2d5cb3ed4e8e4b28a61fe5e8d4cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102520693"
---
# <a name="use-kubectl-to-run-a-kubernetes-stateful-application-with-storageclass-on-your-azure-stack-edge-pro-gpu-device"></a>A kubectl használata Kubernetes állapot-nyilvántartó alkalmazások futtatásához a StorageClass-mel a Azure Stack Edge Pro GPU-eszközön

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Ebből a cikkből megtudhatja, hogyan helyezhet üzembe egy egypéldányos állapotú alkalmazást a Kubernetes-ben egy StorageClass használatával a tárolók és a központi telepítés dinamikus kiépítéséhez. A központi telepítés `kubectl` egy meglévő Kubernetes-fürtön lévő parancsokat használ, és üzembe helyezi a MySQL-alkalmazást. 

Ez az eljárás azon felhasználók számára készült, akik áttekintették a [Kubernetes-tárolót Azure stack Edge Pro-eszközön](azure-stack-edge-gpu-kubernetes-storage.md) , és ismerik a [Kubernetes-tároló](https://kubernetes.io/docs/concepts/storage/)fogalmait.


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
        - Ellenőrizze, hogy ez a két verzió kompatibilis-e a támogatott Kubernetes-verzióban megadott leképezéssel<!-- insert link-->. 


Készen áll egy állapot-nyilvántartó alkalmazás üzembe helyezésére az Azure Stack Edge Pro-eszközön. 


## <a name="deploy-mysql"></a>A MySQL üzembe helyezése

Most egy állapot-nyilvántartó alkalmazást fog futtatni egy Kubernetes-telepítés létrehozásával és a beépített StorageClass való csatlakoztatásával a PersistentVolumeClaim (PVC) használatával. 

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

1. A következő YAML-fájlokat fogja használni. A `mysql-deployment.yml` fájl leírja a MySQL-t futtató központi telepítést, és a PVC-re hivatkozik. A fájl határozza meg a kötet csatlakoztatását `/var/lib/mysql` , majd létrehoz egy olyan PVC-t, amely egy 20 GB-os kötetet keres. A dinamikus PV kiépítve, a PVC pedig ehhez a PV-hez van kötve.

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
              claimName: mysql-pv-claim-sc
    ```
    
2. Másolja és mentse fájlként egy olyan `mysql-pvc.yml` mappába, ahová a fájlt mentette `mysql-deployment.yml` . Ha olyan beépített StorageClass szeretne használni, amely Azure Stack Edge Pro-eszközt egy csatolt adatlemezen, állítsa be a PVC objektum mezőjét a (z) értékre, és a accessModes értéknek kell `storageClassName` `ase-node-local` lennie `ReadWriteOnce` . 

    > [!NOTE] 
    > Győződjön meg arról, hogy a YAML-fájlok helyes behúzással rendelkeznek. A [YAML](http://www.yamllint.com/) az ellenőrzéshez és a mentéshez használhatja.
   
    ```yml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: mysql-pv-claim-sc
    spec:
      storageClassName: ase-node-local
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 20Gi
    ```

3. Telepítse a `mysql-pvc.yaml` fájlt.

    `kubectl apply -f <URI path to the mysql-pv.yml file> -n <your-user-namespace>`
    
    Íme egy példa az üzemelő példány kimenetére.

    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-pvc.yml" -n userns1
    persistentvolumeclaim/mysql-pv-claim-sc created
    C:\Users\user>
    ```
   Jegyezze fel a létrehozott PVC nevét – ebben a példában a következőt: `mysql-pv-claim-sc` . Ezt egy későbbi lépésben fogja használni.

4. Telepítse a `mysql-deployment.yml` fájl tartalmát.

    `kubectl apply -f <URI path to mysql-deployment.yml file> -n <your-user-namespace>`

    Íme egy példa az üzemelő példány kimenetére.
    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-deployment.yml" -n userns1
    service/mysql created
    deployment.apps/mysql created
    C:\Users\user>
    ```
    
5. A központi telepítésre vonatkozó információk megjelenítése.

    `kubectl describe deployment <app-label> -n <your-user-namespace>`
    
    ```powershell
    C:\Users\user>kubectl describe deployment mysql -n userns1
    Name:               mysql
    Namespace:          userns1
    CreationTimestamp:  Thu, 20 Aug 2020 11:14:25 -0700
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
        ClaimName:  mysql-pv-claim-sc
        ReadOnly:   false
    Conditions:
      Type           Status  Reason
      ----           ------  ------
      Available      True    MinimumReplicasAvailable
      Progressing    True    NewReplicaSetAvailable
    OldReplicaSets:  <none>
    NewReplicaSet:   mysql-695c4d9dcd (1/1 replicas created)
    Events:
      Type    Reason             Age   From                   Message
      ----    ------             ----  ----                   -------
      Normal  ScalingReplicaSet  24s   deployment-controller  Scaled up replica set mysql-695c4d9dcd to 1
    C:\Users\user>
    ```
    

6. Az üzemelő példány által létrehozott hüvelyek listázása.

    `kubectl get pods -l <app=label> -n <your-user-namespace>`

    Íme egy példa kimenet.

    
    ```powershell
    C:\Users\user>kubectl get pods -l app=mysql -n userns1
    NAME                     READY   STATUS    RESTARTS   AGE
    mysql-695c4d9dcd-rvzff   1/1     Running   0          40s
    C:\Users\user>
    ```
    
7. Vizsgálja meg a PersistentVolumeClaim.

    `kubectl describe pvc <your-pvc-name>`

    Íme egy példa kimenet.

    
    ```powershell
    C:\Users\user>kubectl describe pvc mysql-pv-claim-sc -n userns1
    Name:          mysql-pv-claim-sc
    Namespace:     userns1
    StorageClass:  ase-node-local
    Status:        Bound
    Volume:        pvc-dc48253c-82dc-42a4-a7c6-aaddc97c9b8a
    Labels:        <none>
    Annotations:   kubectl.kubernetes.io/last-applied-configuration:
                     {"apiVersion":"v1","kind":"PersistentVolumeClaim","metadata":{"annotations":{},"name":"mysql-pv-claim-sc","namespace":"userns1"},"spec":{"...
                   pv.kubernetes.io/bind-completed: yes
                   pv.kubernetes.io/bound-by-controller: yes
                   volume.beta.kubernetes.io/storage-provisioner: rancher.io/local-path
                   volume.kubernetes.io/selected-node: k8s-3q7lhq2cl-3q7lhq2
    Finalizers:    [kubernetes.io/pvc-protection]
    Capacity:      20Gi
    Access Modes:  RWO
    VolumeMode:    Filesystem
    Mounted By:    mysql-695c4d9dcd-rvzff
    Events:
      Type    Reason                 Age                From                                                                                                Message
      ----    ------                 ----               ----                                                                                                -------
      Normal  WaitForFirstConsumer   71s (x2 over 77s)  persistentvolume-controller                                                                         waiting for first consumer to be created before binding
      Normal  ExternalProvisioning   62s                persistentvolume-controller                                                                         waiting for a volume to be created, either by external provisioner "rancher.io/local-path" or manually created by system administrator
      Normal  Provisioning           62s                rancher.io/local-path_local-path-provisioner-6b84988bf9-tx8mz_1896d824-f862-4cbf-912a-c8cc0ca05574  External provisioner is provisioning volume for claim "userns1/mysql-pv-claim-sc"
      Normal  ProvisioningSucceeded  60s                rancher.io/local-path_local-path-provisioner-6b84988bf9-tx8mz_1896d824-f862-4cbf-912a-c8cc0ca05574  Successfully provisioned volume pvc-dc48253c-82dc-42a4-a7c6-aaddc97c9b8a
    C:\Users\user>
    ```
    

## <a name="verify-mysql-is-running"></a>Ellenőrizze, hogy fut-e a MySQL

Az alkalmazás futásának ellenőrzéséhez írja be a következőt:

`kubectl exec <your-pod-with-the-app> -i -t -n <your-namespace> -- mysql -p`

Ha a rendszer kéri, adja meg a jelszót. A jelszó a fájlban található `mysql-deployment` .

Íme egy példa kimenet.

```powershell
C:\Users\user>kubectl exec mysql-695c4d9dcd-rvzff -i -t -n userns1 -- mysql -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
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
C:\Users\user>kubectl delete pvc mysql-pv-claim-sc -n userns1
persistentvolumeclaim "mysql-pv-claim-sc" deleted
C:\Users\user>
```                                                                                         


## <a name="next-steps"></a>Következő lépések

A hálózatkezelés kubectl-n keresztüli konfigurálásával kapcsolatban lásd: [állapot nélküli alkalmazás üzembe helyezése Azure stack Edge Pro-eszközön](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md)
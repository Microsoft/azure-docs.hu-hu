---
title: Azure SQL Edge-tároló üzembe helyezése a Kubernetes-ben – Azure SQL Edge
description: Tudnivalók az Azure SQL Edge-tároló üzembe helyezéséről a Kubernetes-ben
keywords: SQL Edge, tároló, kubernetes
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 31a454c93ad5192f387306a8ec557c4e4d3ae991
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93395291"
---
# <a name="deploy-an-azure-sql-edge-container-in-kubernetes"></a>Azure SQL Edge-tároló üzembe helyezése a Kubernetes-ben

Az Azure SQL Edge egy Kubernetes-fürtön is üzembe helyezhető, IoT Edge-modulként a Kubernetes-on vagy önálló Container Pod-on futó Azure IoT Edge. A cikk további részében az önálló tároló üzembe helyezésére fogunk összpontosítani egy kubernetes-fürtön. A Azure IoT Edge Kubernetes-on való üzembe helyezésével kapcsolatos információkért tekintse [meg a Azure IoT Edge on Kubernetes (előzetes verzió)](https://microsoft.github.io/iotedge-k8s-doc/introduction.html)című témakört.

Ez az oktatóanyag bemutatja, hogyan konfigurálhat egy magasan elérhető Azure SQL Edge-példányt egy kubernetes-fürtben található tárolóban. 

> [!div class="checklist"]
> * SA-jelszó létrehozása
> * Tároló létrehozása
> * A központi telepítés létrehozása
> * Kapcsolódjon SQL Server Management Studio (SSMS)
> * A hiba és a helyreállítás ellenőrzése

A Kubernetes 1,6-es és újabb verziói támogatják a [tárolási osztályokat](https://kubernetes.io/docs/concepts/storage/storage-classes/), az [állandó mennyiségi jogcímeket](https://kubernetes.io/docs/concepts/storage/storage-classes/#persistentvolumeclaims)és az [Azure-lemez kötetének típusát](https://github.com/kubernetes/examples/tree/master/staging/volumes/azure_disk). Az Azure SQL Edge-példányokat a Kubernetes-ben natív módon hozhatja létre és kezelheti. A cikkben szereplő példa azt mutatja be, hogyan hozható létre egy [központi telepítés](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) , amely a megosztott lemezes feladatátvevő fürt példányához hasonló magas rendelkezésre állású konfiguráció elérésére használható. Ebben a konfigurációban a Kubernetes a fürt Orchestrator szerepét játssza le. Ha egy tárolóban egy Azure SQL Edge-példány meghibásodik, a Orchestrator a tároló egy másik példányát, amely ugyanahhoz az állandó tárolóhoz csatolja.

![Azure SQL Edge egy Kubernetes-fürtben](media/deploy-kubernetes/kubernetes-sql-edge.png)

Az előző ábrán egy `azure-sql-edge` [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/)tároló található. A Kubernetes összehangolja a fürt erőforrásait. Egy [replikakészlet](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) biztosítja, hogy a pod automatikusan helyreálljon a csomópont meghibásodása után. Az alkalmazások a szolgáltatáshoz csatlakoznak. Ebben az esetben a szolgáltatás olyan terheléselosztó, amely egy olyan IP-címet tárol, amely a meghibásodása után azonos marad `azure-sql-edge` .

A következő ábrán a `azure-sql-edge` tároló nem sikerült. A Kubernetes garantálja a Orchestrator található kifogástalan állapotú példányok helyes számát, és a konfigurációnak megfelelően elindítja az új tárolót. A Orchestrator egy új Pod-t indít el ugyanazon a csomóponton, és `azure-sql-edge` újra csatlakozik ugyanahhoz az állandó tárolóhoz. A szolgáltatás csatlakozik az újból létrehozott létrehozáshoz `azure-sql-edge` .

![Azure SQL Edge egy Kubernetes-fürtön a pod-meghibásodás után](media/deploy-kubernetes/kubernetes-sql-edge-after-pod-fail.png)

A következő ábrán a tárolót üzemeltető csomópont `azure-sql-edge` meghiúsult. A Orchestrator elindítja az új Pod-t egy másik csomóponton, és `azure-sql-edge` újra csatlakozik ugyanahhoz az állandó tárolóhoz. A szolgáltatás csatlakozik az újból létrehozott létrehozáshoz `azure-sql-edge` .

![Azure SQL Edge egy Kubernetes-fürtben a csomópont meghibásodása után](media/deploy-kubernetes/kubernetes-sql-edge-after-node-fail.png)

## <a name="prerequisites"></a>Előfeltételek

* **Kubernetes-fürt**
   - Az oktatóanyaghoz Kubernetes-fürt szükséges. A lépések a [kubectl](https://kubernetes.io/docs/user-guide/kubectl/) használatával kezelik a fürtöt. 

   - Ebben az oktatóanyagban az Azure Kubernetes Service-t fogjuk használni az Azure SQL Edge üzembe helyezéséhez. Lásd: [Azure Kubernetes-szolgáltatás (ak) fürt üzembe helyezése](../aks/tutorial-kubernetes-deploy-cluster.md) egy egycsomópontos Kubernetes-fürt létrehozásához és kapcsolódásához a-ben `kubectl` . 

   >[!NOTE]
   >A csomópontok meghibásodása elleni védelem érdekében a Kubernetes-fürtök több csomópontot igényelnek.

* **Azure CLI**
   - Az oktatóanyag utasításait az Azure CLI-2.10.1 ellenőrizték.

## <a name="create-a-kubernetes-namespace-for-sql-edge-deployment"></a>Kubernetes-névtér létrehozása az SQL Edge telepítéséhez

Hozzon létre egy új névteret a kubernetes-fürtben. Ez a névtér fogja használni az SQL Edge és az összes szükséges összetevő üzembe helyezését. A Kubernetes névterekkel kapcsolatos további információkért tekintse át a [névtereket](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/).

   ```azurecli
   kubectl create namespace <namespace name>
   ```  

## <a name="create-an-sa-password"></a>SA-jelszó létrehozása

Hozzon létre egy SA-jelszót a Kubernetes-fürtben. A Kubernetes a bizalmas konfigurációs adatokat, például a jelszavakat [titkos kulcsként](https://kubernetes.io/docs/concepts/configuration/secret/)kezelheti.

A következő parancs egy jelszót hoz létre az SA-fiókhoz:

   ```azurecli
   kubectl create secret generic mssql --from-literal=SA_PASSWORD="MyC0m9l&xP@ssw0rd" -n <namespace name>
   ```  

   Cserélje le a változót `MyC0m9l&xP@ssw0rd` egy összetett jelszóra.

## <a name="create-storage"></a>Tároló létrehozása

Állítson be [állandó kötetet](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) és [állandó mennyiségi jogcímet](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistent-volume-claim-protection) a Kubernetes-fürtben. Végezze el a következő lépéseket: 

1. Hozzon létre egy jegyzékfájlt a tárolási osztály és az állandó mennyiségi jogcím definiálásához.  A jegyzékfájl meghatározza a tárolási kiépítési, a paramétereket és a [visszaigénylési házirendet](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#reclaiming). A Kubernetes-fürt ezt a jegyzékfájlt használja az állandó tárterület létrehozásához. 

   A következő YAML példa egy tárolási osztályt és egy állandó mennyiségi jogcímet határoz meg. `azure-disk`Ez a Kubernetes-fürt az Azure-beli tárolási osztály. A Storage-fiók típusa: `Standard_LRS` . A rendszer elnevezte az állandó kötet jogcímet `mssql-data` . Az állandó kötet jogcímek metaadatainak részét képezi egy megjegyzés, amely a tárolási osztályhoz kapcsolódik vissza. 

   ```yaml
   kind: StorageClass
   apiVersion: storage.k8s.io/v1
   metadata:
        name: azure-disk
   provisioner: kubernetes.io/azure-disk
   parameters:
     storageaccounttype: Standard_LRS
     kind: managed
   ---
   kind: PersistentVolumeClaim
   apiVersion: v1
   metadata:
     name: mssql-data
     annotations:
       volume.beta.kubernetes.io/storage-class: azure-disk
   spec:
     accessModes:
     - ReadWriteOnce
     resources:
       requests:
         storage: 8Gi
   ```

   Mentse a fájlt (például: **PVC. YAML**).

2. Hozza létre az állandó kötet jogcímet a Kubernetes-ben.

   ```azurecli
   kubectl apply -f <Path to pvc.yaml file> -n <namespace name>
   ```

   `<Path to pvc.yaml file>` az a hely, ahová a fájlt mentette.

   Az állandó kötet automatikusan Azure Storage-fiókként jön létre, és az állandó mennyiségi jogcímhez van kötve. 

    ![Képernyőkép az állandó mennyiségi jogcím parancsról](media/deploy-kubernetes/pvc-cmd.png)

3. Ellenőrizze az állandó kötet jogcímet.

   ```azurecli
   kubectl describe pvc <PersistentVolumeClaim>  -n <name of the namespace>
   ```

   `<PersistentVolumeClaim>` az állandó mennyiségi jogcím neve.

   Az előző lépésben a rendszer elnevezte az állandó kötet jogcímet `mssql-data` . Az állandó mennyiségi jogcímek metaadatainak megjelenítéséhez futtassa a következő parancsot:

   ```azurecli
   kubectl describe pvc mssql-data  -n <namespace name>
   ```

   A visszaadott metaadatok tartalmazzák a nevű értéket `Volume` . Ez az érték a blob nevét képezi le.

   ![Képernyőfelvétel a visszaadott metaadatokról, beleértve a kötetet](media/deploy-kubernetes/describe-volume.png)

4. Ellenőrizze az állandó kötetet.

   ```azurecli
   kubectl describe pv -n <namespace name>
   ```

   `kubectl` azokat a metaadatokat adja vissza az állandó kötethez, amelyeket a rendszer automatikusan hozott létre, és az állandó mennyiségi jogcímhez kötött. 

## <a name="create-the-deployment"></a>A központi telepítés létrehozása

Ebben a példában az Azure SQL Edge-példányt futtató tároló Kubernetes telepítési objektumként van leírva. A központi telepítés létrehoz egy replikakészlet-készletet. A replikakészlet létrehozza a hüvelyt. 

Ebben a lépésben létrehoz egy jegyzékfájlt, amely leírja a tárolót az Azure SQL Edge Docker-rendszerkép alapján. A jegyzékfájl az `mssql-data` állandó kötetre vonatkozó jogcímet és a `mssql` Kubernetes-fürtön már alkalmazott titkos kulcsot hivatkozik. A jegyzékfájl egy [szolgáltatást](https://kubernetes.io/docs/concepts/services-networking/service/)is tartalmaz. Ez a szolgáltatás egy Load Balancer. A terheléselosztó garantálja, hogy az IP-cím megmarad az Azure SQL Edge-példány helyreállítása után. 

1. Hozzon létre egy jegyzékfájlt (YAML-fájlt) a telepítés leírásához. Az alábbi példa egy központi telepítést ismertet, amely az Azure SQL Edge-tároló rendszerképén alapuló tárolót is tartalmaz.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sqledge-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sqledge
  template:
    metadata:
      labels:
        app: sqledge
    spec:
      volumes:
        - name: sqldata
          persistentVolumeClaim:
            claimName: mssql-data
      containers:
        - name: azuresqledge
          image: mcr.microsoft.com/azure-sql-edge:latest
          ports:
            - containerPort: 1433
          volumeMounts:
            - name: sqldata
              mountPath: /var/opt/mssql
          env:
            - name: MSSQL_PID
              value: "Developer"
            - name: ACCEPT_EULA
              value: "Y"
            - name: SA_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mssql
                  key: SA_PASSWORD
            - name: MSSQL_AGENT_ENABLED
              value: "TRUE"
            - name: MSSQL_COLLATION
              value: "SQL_Latin1_General_CP1_CI_AS"
            - name: MSSQL_LCID
              value: "1033"
      terminationGracePeriodSeconds: 30
      securityContext:
        fsGroup: 10001
---
apiVersion: v1
kind: Service
metadata:
  name: sqledge-deployment
spec:
  selector:
    app: sqledge
  ports:
    - protocol: TCP
      port: 1433
      targetPort: 1433
      name: sql
  type: LoadBalancer
```

   Másolja az előző kódot egy nevű új fájlba `sqldeployment.yaml` . Frissítse a következő értékeket: 

   * MSSQL_PID `value: "Developer"` : beállítja a tárolót az Azure SQL Edge Developer Edition futtatásához. A fejlesztői kiadás nem rendelkezik licenccel az éles adatkezeléshez. Ha az üzemelő példány éles használatra készült, állítsa a kiadást a következőre: `Premium` . 

      >[!NOTE]
      >További információ: [How to License Azure SQL Edge](https://azure.microsoft.com/pricing/details/sql-edge/).

   * `persistentVolumeClaim`: Ehhez az értékhez egy `claimName:` , az állandó mennyiségi jogcímhez használt névhez tartozó bejegyzés szükséges. Ebben az oktatóanyagban a következőt használjuk: `mssql-data`. 

   * `name: SA_PASSWORD`: Konfigurálja a tároló rendszerképét az SA-jelszó beállítására, az ebben a szakaszban meghatározottak szerint.

     ```yaml
     valueFrom:
       secretKeyRef:
         name: mssql
         key: SA_PASSWORD 
     ```

     Amikor a Kubernetes üzembe helyezi a tárolót, a nevű titkra hivatkozik a `mssql` jelszó értékének beolvasásához. 

   >[!NOTE]
   >A `LoadBalancer` szolgáltatás típusának használatával az Azure SQL Edge-példány távolról (az interneten keresztül) elérhető az 1433-as porton.

   Mentse a fájlt (például **sqledgedeploy. YAML**).

2. Hozza létre a központi telepítést.

   ```azurecli
   kubectl apply -f <Path to sqledgedeploy.yaml file> -n <namespace name>
   ```

   `<Path to sqldeployment.yaml file>` az a hely, ahová a fájlt mentette.

   ![Képernyőkép az üzembe helyezési parancsról](media/deploy-kubernetes/deploy-sql-cmd.png)

   A rendszer létrehozza az üzembe helyezést és a szolgáltatást. Az Azure SQL Edge-példány olyan tárolóban található, amely állandó tárterülethez csatlakozik.

   A pod állapotának megtekintéséhez írja be a következőt: `kubectl get pod -n <namespace name>` .

   ![Képernyőkép a Get Pod parancsról](media/deploy-kubernetes/get-sql-pod-cmd.png)

   Az előző képen a pod állapota `Running` . Ez az állapot azt jelzi, hogy a tároló készen áll. A folyamat befejezése eltarthat néhány percig.

   >[!NOTE]
   >Az üzembe helyezés létrehozása után néhány percet is igénybe vehet, amíg a pod látható. A késés azért van, mert a fürt lekéri az Azure SQL Edge-tároló rendszerképét a Docker hub-ból. A rendszerkép első kihúzása után a további központi telepítések gyorsabbak lehetnek, ha az üzemelő példány olyan csomópontra esik, amelyen már van gyorsítótárazva a rendszerkép. 

3. Ellenőrizze, hogy a szolgáltatások futnak-e. Futtassa az alábbi parancsot:

   ```azurecli
   kubectl get services -n <namespace name>
   ```

   Ez a parancs a-t futtató szolgáltatásokat, valamint a szolgáltatások belső és külső IP-címeit adja vissza. Jegyezze fel a szolgáltatás külső IP-címét `mssql-deployment` . Ezt az IP-címet használhatja az Azure SQL Edge-hez való kapcsolódáshoz. 

   ![A szolgáltatás lekérése parancs képernyőképe](media/deploy-kubernetes/get-service-cmd.png)

   A Kubernetes-fürt objektumainak állapotával kapcsolatos további információkért futtassa a következőt:

   ```azurecli
   az aks browse --resource-group <MyResourceGroup> --name <MyKubernetesClustername>
   ```  

## <a name="connect-to-the-azure-sql-edge-instance"></a>Kapcsolódás az Azure SQL Edge-példányhoz

Ha a tárolót a leírt módon konfigurálta, az Azure Virtual networken kívülről is csatlakozhat egy alkalmazáshoz. Használja a `sa` fiókot és a szolgáltatás külső IP-címét. Használja a Kubernetes-titokként konfigurált jelszót. Az Azure SQL Edge-példányokhoz való csatlakozással kapcsolatos további információkért tekintse meg az [Azure SQL Edge](connect.md)-hez való csatlakozást ismertető témakört.

## <a name="verify-failure-and-recovery"></a>A hiba és a helyreállítás ellenőrzése

A hiba és a helyreállítás ellenőrzéséhez törölheti a hüvelyt. Hajtsa végre a következő lépéseket:

1. Az Azure SQL Edge-t futtató Pod listázása.

   ```azurecli
   kubectl get pods -n <namespace name>
   ```

   Jegyezze fel az Azure SQL Edge-t futtató Pod nevét.

2. Törölje a hüvelyt.

   ```azurecli
   kubectl delete pod sqledge-deployment-7df66c9999-rc9xl
   ```
   `sqledge-deployment-7df66c9999-rc9xl` az előző lépésből visszaadott érték a pod neve számára. 

A Kubernetes automatikusan újra létrehozza a pod-t egy Azure SQL Edge-példány helyreállításához, és csatlakozik az állandó tárolóhoz. `kubectl get pods`A használatával ellenőrizheti, hogy telepítve van-e az új Pod. Ezzel a paranccsal `kubectl get services` ellenőrizheti, hogy az új tároló IP-címe azonos-e. 

## <a name="summary"></a>Összefoglalás

Ebben az oktatóanyagban megtanulta, hogyan helyezhet üzembe Azure SQL Edge-tárolókat egy Kubernetes-fürtön a magas rendelkezésre állás érdekében. 

> [!div class="checklist"]
> * SA-jelszó létrehozása
> * Tároló létrehozása
> * A központi telepítés létrehozása
> * Kapcsolódjon az Azure SQL Edge Management studióhoz (SSMS)
> * A hiba és a helyreállítás ellenőrzése

## <a name="next-steps"></a>Következő lépések

- [Bevezetés a Kubernetes használatába](../aks/intro-kubernetes.md)
- [Machine learning és mesterséges intelligencia a ONNX az SQL Edge-ben](onnx-overview.md).
- [Végpontok közötti IoT-megoldás létrehozása az SQL Edge használatával IoT Edge segítségével](tutorial-deploy-azure-resources.md).
- [Adatfolyamok az Azure SQL Edge-ben](stream-data.md)
---
title: 'Rövid útmutató: AKS-fürt üzembe helyezése bizalmas számítási csomópontokkal az Azure CLI használatával'
description: Megtudhatja, hogyan hozhat létre Azure Kubernetes Service (AKS-) fürtöt bizalmas csomópontokkal, és hogyan helyezhet üzembe Hello World alkalmazásokat az Azure CLI használatával.
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: quickstart
ms.date: 04/08/2021
ms.author: amgowda
ms.custom: contentperf-fy21q3, devx-track-azurecli
ms.openlocfilehash: b1ef397c1a3f6770d197ca8fd0faa83c9e8360f5
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816457"
---
# <a name="quickstart-deploy-an-aks-cluster-with-confidential-computing-nodes-by-using-the-azure-cli"></a>Rövid útmutató: AKS-fürt üzembe helyezése bizalmas számítási csomópontokkal az Azure CLI használatával

Ebben a rövid útmutatóban az Azure CLI használatával fog üzembe helyezni egy Azure Kubernetes Service- (AKS-) fürtöt bizalmas számítási (DCsv2) csomópontokkal. Ezután futtatni fog egy egyszerű Hello World egy enklávéban. Fürtöt is kiépíthet, és bizalmas számítási csomópontokat adhat hozzá a Azure Portal, de ez a rövid útmutató az Azure CLI-t használja.

Az AKS egy felügyelt Kubernetes-szolgáltatás, amely lehetővé teszi a fejlesztők vagy fürtüzemeltetők számára a fürtök gyors üzembe helyezését és kezelését. További tudnivalókért olvassa el az [AKS bevezetését](../aks/intro-kubernetes.md) és a bizalmas [AKS-csomópontok áttekintését.](confidential-nodes-aks-overview.md)

A bizalmas számítási csomópontok funkciói a következők:

- Linux-tárolókat támogató Linux feldolgozó csomópontok.
- 2. generációs virtuális gép (VM) Ubuntu 18.04 virtuálisgép-csomópontokkal.
- Intel SGX-kompatibilis PROCESSZOR, amely segít a tárolók titkosság által védett enklávéban való futtatásában, kihasználva a titkosított lapgyorsítótár memóriáját (EPC). További információ: Gyakori kérdések az [Azure Confidential Computing szolgáltatásról.](./faq.md)
- Intel SGX DCAP-illesztő előre telepítette a bizalmas számítási csomópontokon. További információ: Gyakori kérdések az [Azure Confidential Computing szolgáltatásról.](./faq.md)

> [!NOTE]
> A DCsv2 virtuális gépek speciális hardvert használnak, amely magasabb díjszabás és a régiónkénti rendelkezésre állás függvényében áll rendelkezésre. További információkért lásd az elérhető [SKUs-okat és a támogatott régiókat.](virtual-machine-solutions.md)

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

- Aktív Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a feladatok megkezdése előtt.
- Az Azure CLI 2.0.64-es vagy újabb verziója van telepítve és konfigurálva az üzembe helyezési gépen. 

  A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](../container-registry/container-registry-get-started-azure-cli.md) ismertető cikket.
- Legalább hat, az előfizetésben elérhető DCsv2 mag. 

  Alapértelmezés szerint a bizalmas számítás azure-előfizetésenkénti kvótája nyolc virtuálisgép-mag. Ha egy nyolcnál több magot igénylő fürtöt [](../azure-portal/supportability/per-vm-quota-requests.md) tervez üzembe venni, kövesse ezeket az utasításokat a kvóta növelésével kapcsolatos jegy létrehozása érdekében.

## <a name="create-an-aks-cluster-with-confidential-computing-nodes-and-add-on"></a>AKS-fürt létrehozása bizalmas számítási csomópontokkal és bővítményekkel

Az alábbi utasítások segítségével hozzon létre egy AKS-fürtöt engedélyezett bizalmas számítástechnikai bővítménysel, adjon hozzá egy csomópontkészletet a fürthöz, és ellenőrizze, hogy mit hozott létre.

### <a name="create-an-aks-cluster-with-a-system-node-pool"></a>AKS-fürt létrehozása rendszer-csomópontkészletekkel

> [!NOTE]
> Ha már rendelkezik olyan AKS-fürttel, amely [](#add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster) megfelel a korábban felsorolt előfeltételeknek, ugorjon a következő szakaszra egy bizalmas számítási csomópontkészlet hozzáadásához.

Először hozzon létre egy erőforráscsoportot a fürthöz az [az group create paranccsal.][az-group-create] Az alábbi példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot a *westus2 régióban:*

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Most hozzon létre egy AKS-fürtöt engedélyezett bizalmas számítási bővítménysel az [az aks create paranccsal:][az-aks-create]

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```

### <a name="add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster"></a>Bizalmas számítási képességekkel rendelkezik felhasználói csomópontkészlet hozzáadása az AKS-fürthöz 

A következő parancs futtatásával adjon hozzá egy három csomópontot használó felhasználói csomópontkészletet `Standard_DC2s_v2` az AKS-fürthöz. A támogatott [DCsv2](../virtual-machines/dcv2-series.md)termékváltozatok és régiók listájából másik termékváltozatot is választhat.

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```

A parancs futtatása után egy új, DCsv2-t is tartalmazó csomópontkészletnek kell láthatónak lennie a Confidential Computing bővítmény daemonSets (SGX eszköz beépülő[modulja) segítségével.](confidential-nodes-aks-overview.md#confidential-computing-add-on-for-aks)

### <a name="verify-the-node-pool-and-add-on"></a>A csomópontkészlet és a bővítmény ellenőrzése

Az az [aks get-credentials][az-aks-get-credentials] paranccsal szerezze be az AKS-fürt hitelesítő adatait:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A paranccsal ellenőrizze, hogy a csomópontok megfelelően létrejöttek-e, és hogy az SGX-hez kapcsolódó démonkészletek futnak-e a `kubectl get pods` DCsv2 csomópontkészleten:

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Ha a kimenet megegyezik az előző kóddal, az AKS-fürt most már készen áll a bizalmas alkalmazások futtatására.

A rövid útmutató [Deploy Hello World from an isolated enclave application](#hello-world) (Alkalmazás üzembe helyezése elkülönített enklávéból) című szakasza segítségével tesztelhet egy alkalmazást egy enklávéban. Az alábbi utasításokat követve további csomópontkészleteket is hozzáadhat az AKS-hez. (Az AKS támogatja az SGX-csomópontkészletek és a nem SGX-csomópontkészletek keverését.)

## <a name="add-a-confidential-computing-node-pool-to-an-existing-aks-cluster"></a>Bizalmas számítási csomópontkészlet hozzáadása meglévő AKS-fürthöz<a id="existing-cluster"></a>

Ez a szakasz feltételezi, hogy már futtat egy olyan AKS-fürtöt, amely megfelel a rövid útmutató korábbi részében felsorolt előfeltételeknek.

### <a name="enable-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>A bizalmas számítási AKS-bővítmény engedélyezése a meglévő fürtön

Futtassa a következő parancsot a bizalmas számítási bővítmény engedélyezéséhez:

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```

### <a name="add-a-dcsv2-user-node-pool-to-the-cluster"></a>Csv2 felhasználói csomópontkészlet hozzáadása a fürthöz

> [!NOTE]
> A bizalmas számítási képesség csak akkor használható, ha a meglévő AKS-fürt legalább egy, ACsv2 virtuálisgép-termékváltozaton alapuló csomópontkészletet tartalmaz. A bizalmas számításhoz használható DCs-v2 virtuális gépek SKUs-okkal kapcsolatos további információkért tekintse meg az elérhető SKUs-okat és [a támogatott régiókat.](virtual-machine-solutions.md)

Futtassa a következő parancsot egy csomópontkészlet létrehozásához:

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2
```

Ellenőrizze, hogy létrejött-e a *confcompool1* nevű új csomópontkészlet:

```azurecli-interactive
az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

### <a name="verify-that-daemonsets-are-running-on-confidential-node-pools"></a>Annak ellenőrzése, hogy a DaemonSets bizalmas csomópontkészleten fut-e

A következő ellenőrzés végrehajtásához jelentkezzen be a meglévő AKS-fürtbe:

```console
kubectl get nodes
```

A kimenetnek meg kell mutatnia az újonnan *hozzáadott confcompool1* készletet az AKS-fürtön. Más daemonSeteket is láthat.

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Ha a kimenet megegyezik az előző kóddal, az AKS-fürt most már készen áll a bizalmas alkalmazások futtatására. 

## <a name="deploy-hello-world-from-an-isolated-enclave-application"></a>Alkalmazás Hello World enklávéalkalmazásból való üzembe helyezése <a id="hello-world"></a>
Most már készen áll egy tesztalkalmazás üzembe helyezésére. 

Hozzon létre egy *hello-world-enclave.yaml* nevű fájlt, és illessze be a következő YAML-jegyzékfájlt. Ez a mintaalkalmazás kódja az [Open Enclave projektben található.](https://github.com/openenclave/openenclave/tree/master/samples/helloworld) Ez az üzembe helyezés feltételezi, hogy telepítette a *confcom* bővítményt.

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
  labels:
    app: sgx-test
spec:
  template:
    metadata:
      labels:
        app: sgx-test
    spec:
      containers:
      - name: sgxtest
        image: oeciteam/sgx-test:1.0
        resources:
          limits:
            sgx.intel.com/epc: 5Mi # This limit will automatically place the job into a confidential computing node and mount the required driver volumes. Alternatively, you can target deployment to node pools with node selector.
      restartPolicy: Never
  backoffLimit: 0
  ```

Most az paranccsal hozzon létre egy biztonságos enklávéban megnyílik minta feladatot, ahogyan az az alábbi `kubectl apply` példakimenetben látható:

```console
$ kubectl apply -f hello-world-enclave.yaml

job "sgx-test" created
```

A következő parancsok futtatásával meggyőződhet arról, hogy a számítási feladat sikeresen létrehozott egy megbízható végrehajtási környezetet (enklávét):

```console
$ kubectl get jobs -l app=sgx-test

NAME       COMPLETIONS   DURATION   AGE
sgx-test   1/1           1s         23s
```

```console
$ kubectl get pods -l app=sgx-test

NAME             READY   STATUS      RESTARTS   AGE
sgx-test-rchvg   0/1     Completed   0          25s
```

```console
$ kubectl logs -l app=sgx-test

Hello world from the enclave
Enclave called into host to print: Hello World!
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a rövid útmutatóban létrehozott bizalmas számítási csomópontkészlet eltávolításához használja a következő parancsot: 

```azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup
```

Az AKS-fürt törléséhez használja a következő parancsot: 

```azurecli-interactive
az aks delete --resource-group myResourceGroup --cluster-name myAKSCluster
```

## <a name="next-steps"></a>Következő lépések

* Python-, Node- vagy egyéb alkalmazásokat futtathat bizalmas tárolókon keresztül a GitHub bizalmas [tárolómintákkal.](https://github.com/Azure-Samples/confidential-container-samples)

* Futtatassa az enklávéra ható alkalmazásokat a [GitHub enklávé-felhasználásával használható Azure-tárolóminták használatával.](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/)

<!-- LINKS -->
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials

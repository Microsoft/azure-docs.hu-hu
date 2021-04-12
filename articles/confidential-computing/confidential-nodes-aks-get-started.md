---
title: 'Gyors útmutató: AK-fürt üzembe helyezése bizalmas számítástechnikai csomópontokkal az Azure CLI használatával'
description: Megtudhatja, hogyan hozhat létre bizalmas csomópontokkal rendelkező Azure Kubernetes Service (ak) fürtöt, és hogyan helyezhet üzembe egy Hello World alkalmazást az Azure CLI használatával.
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: quickstart
ms.date: 04/08/2021
ms.author: amgowda
ms.custom: contentperf-fy21q3
ms.openlocfilehash: b012a8a5856b344b366f1ddd89fc5059a6f3c8ae
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107283524"
---
# <a name="quickstart-deploy-an-aks-cluster-with-confidential-computing-nodes-by-using-the-azure-cli"></a>Gyors útmutató: AK-fürt üzembe helyezése bizalmas számítástechnikai csomópontokkal az Azure CLI használatával

Ebben a rövid útmutatóban az Azure CLI használatával helyezheti üzembe a bizalmas számítástechnikai (DCsv2) csomópontokkal rendelkező Azure Kubernetes-szolgáltatás (ak) fürtjét. Ezután egy egyszerű Hello World alkalmazást fog futtatni egy enklávéban. Létrehozhat egy fürtöt is, és hozzáadhat bizalmas számítástechnikai csomópontokat a Azure Portalból, de ez a rövid útmutató az Azure CLI-re koncentrál.

Az AK egy felügyelt Kubernetes szolgáltatás, amely lehetővé teszi a fejlesztők és a fürtök számára a fürtök gyors üzembe helyezését és kezelését. További információért olvassa el az [AK-bevezetést](../aks/intro-kubernetes.md) és az AK-beli [bizalmas csomópontok áttekintését](confidential-nodes-aks-overview.md)ismertető cikket.

A bizalmas számítástechnikai csomópontok szolgáltatásai többek között a következők:

- Linux-tárolókat támogató linuxos munkavégző csomópontok.
- 2. generációs virtuális gép (VM) Ubuntu 18,04 VM-csomópontokkal.
- Intel SGX ENKLÁVÉHOZ-kompatibilis CPU, amely segít a tárolók futtatásában a titkosított oldal gyorsítótár-memóriája (EPC) titkosításával védett enklávéban. További információ: [Gyakori kérdések az Azure bizalmas számítástechnikai szolgáltatásáról](./faq.md).
- Az Intel SGX ENKLÁVÉHOZ DCAP illesztőprogramja előre telepítve van a bizalmas számítástechnikai csomópontokon. További információ: [Gyakori kérdések az Azure bizalmas számítástechnikai szolgáltatásáról](./faq.md).

> [!NOTE]
> A DCsv2 virtuális gépek speciális hardvert használnak, amely a magasabb díjszabás és a régió rendelkezésre állása alá tartozik. További információkért tekintse meg az [elérhető SKU-ket és a támogatott régiókat](virtual-machine-solutions.md).

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

- Aktív Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a feladatok megkezdése előtt.
- Az Azure CLI 2.0.64 vagy újabb verziója telepítve van és konfigurálva van az üzembe helyezési gépen. 

  A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](../container-registry/container-registry-get-started-azure-cli.md) ismertető cikket.
- Az előfizetésében legalább hat DCsv2 mag érhető el. 

  Alapértelmezés szerint az Azure-előfizetések által biztosított bizalmas számítástechnikai kvóta nyolc virtuálisgép-magot biztosít. Ha nyolcnál több magot igénylő fürtöt szeretne kiépíteni, az [alábbi útmutatást](../azure-portal/supportability/per-vm-quota-requests.md) követve növelheti a kvótát.

## <a name="create-an-aks-cluster-with-confidential-computing-nodes-and-add-on"></a>AK-fürt létrehozása bizalmas számítástechnikai csomópontokkal és bővítményekkel

Az alábbi útmutatást követve hozzon létre egy AK-fürtöt a bizalmas számítástechnikai bővítmény engedélyezésével, adjon hozzá egy csomópont-készletet a fürthöz, és ellenőrizze, hogy mit hozott létre.

### <a name="create-an-aks-cluster-with-a-system-node-pool"></a>AK-fürt létrehozása rendszercsomópont-készlettel

> [!NOTE]
> Ha már van egy AK-fürtje, amely megfelel a korábban felsorolt előfeltételek feltételeinek, [ugorjon a következő szakaszra](#add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster) egy bizalmas számítástechnikai csomópont-készlet hozzáadásához.

Először hozzon létre egy erőforráscsoportot a fürthöz az az [Group Create][az-group-create] parancs használatával. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *westus2* régióban:

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Most hozzon létre egy AK-fürtöt, amely lehetővé teszi a bizalmas számítástechnikai bővítmény használatát az az [AK Create][az-aks-create] paranccsal:

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```

### <a name="add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster"></a>Bizalmas számítástechnikai képességekkel rendelkező felhasználói csomópont-készlet hozzáadása az AK-fürthöz 

Futtassa a következő parancsot egy olyan méretű felhasználói csomópont-készlet hozzáadásához, amely `Standard_DC2s_v2` három csomóponttal rendelkezik az AK-fürthöz. Választhat egy másik SKU-t is a [támogatott DCsv2 SKU-i és régiók listájából](../virtual-machines/dcv2-series.md).

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```

A parancs futtatása után egy új, DCsv2 rendelkező csomópont-készletnek láthatónak kell lennie a bizalmas számítástechnikai bővítmény DaemonSets ([SGX enklávéhoz-eszköz beépülő](confidential-nodes-aks-overview.md#confidential-computing-add-on-for-aks)modul).

### <a name="verify-the-node-pool-and-add-on"></a>A csomópont-készlet és a bővítmény ellenőrzése

Kérje le az AK-fürt hitelesítő adatait az az az [AK Get-hitelesítőadats][az-aks-get-credentials] parancs használatával:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A `kubectl get pods` paranccsal ellenőrizheti, hogy a csomópontok megfelelően lettek-e létrehozva, és a SGX enklávéhoz kapcsolódó DaemonSets a DCsv2 csomópont-készleteken futnak-e:

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Ha a kimenet megegyezik az előző kóddal, az AK-fürt most már készen áll a bizalmas alkalmazások futtatására.

Ebben a rövid útmutatóban a [Hello World üzembe helyezése egy elkülönített enklávé alkalmazásból](#hello-world) című szakasza tartalmaz egy alkalmazást egy enklávéban. Vagy az alábbi útmutatást követve további csomópont-készleteket vehet fel az AK-ra. (Az AK támogatja a SGX ENKLÁVÉHOZ-csomópontok és a nem SGX ENKLÁVÉHOZ csomópont-készletek keverését.)

## <a name="add-a-confidential-computing-node-pool-to-an-existing-aks-cluster"></a>Bizalmas számítástechnikai csomópont-készlet hozzáadása meglévő AK-fürthöz<a id="existing-cluster"></a>

Ez a szakasz azt feltételezi, hogy már futtatott egy AK-fürtöt, amely megfelel az ebben a rövid útmutatóban korábban felsorolt előfeltételek feltételeinek.

### <a name="enable-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>A bizalmas számítástechnikai AK-bővítmény engedélyezése a meglévő fürtön

Futtassa a következő parancsot a bizalmas számítástechnikai bővítmény engedélyezéséhez:

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```

### <a name="add-a-dcsv2-user-node-pool-to-the-cluster"></a>DCsv2 felhasználói csomópont-készlet hozzáadása a fürthöz

> [!NOTE]
> A bizalmas számítástechnikai képesség használatához a meglévő AK-fürtnek legalább egy, a DCsv2 VM SKU-ra épülő csomópont-készlettel kell rendelkeznie. Ha többet szeretne megtudni a DCs-v2 virtuális gépekről a bizalmas számítástechnika érdekében, tekintse meg az [elérhető SKU-ket és a támogatott régiókat](virtual-machine-solutions.md).

A következő parancs futtatásával hozzon létre egy csomópont-készletet:

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2
```

Győződjön meg arról, hogy a *confcompool1* nevű új csomópont-készlet lett létrehozva:

```azurecli-interactive
az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

### <a name="verify-that-daemonsets-are-running-on-confidential-node-pools"></a>Annak ellenőrzése, hogy a DaemonSets a bizalmas csomópont-készleteken futnak-e

A következő ellenőrzés végrehajtásához jelentkezzen be a meglévő AK-fürtbe:

```console
kubectl get nodes
```

A kimenetnek az AK-fürtön az újonnan hozzáadott *confcompool1* -készletet kell megjelenítenie. Más DaemonSets is találkozhat.

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Ha a kimenet megegyezik az előző kóddal, az AK-fürt most már készen áll a bizalmas alkalmazások futtatására. 

## <a name="deploy-hello-world-from-an-isolated-enclave-application"></a>Hello World üzembe helyezése elkülönített enklávé-alkalmazásból <a id="hello-world"></a>
Most már készen áll egy tesztelési alkalmazás üzembe helyezésére. 

Hozzon létre egy *Hello-World-enklávé. YAML* nevű fájlt, és illessze be a következő YAML-jegyzékbe. Ez a minta alkalmazás-kód az [Open enklávé projektben](https://github.com/openenclave/openenclave/tree/master/samples/helloworld)található. Ez a központi telepítés feltételezi, hogy telepítette a *confcom* -bővítményt.

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

Most a `kubectl apply` paranccsal hozzon létre egy olyan mintát, amely egy biztonságos enklávéban fog megnyílni, ahogy az alábbi példában is látható:

```console
$ kubectl apply -f hello-world-enclave.yaml

job "sgx-test" created
```

A következő parancsok futtatásával ellenőrizheti, hogy a munkaterhelés sikeresen létrehozott-e megbízható végrehajtási környezetet (enklávé):

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

Az ebben a rövid útmutatóban létrehozott bizalmas számítástechnikai csomópont-készlet eltávolításához használja a következő parancsot: 

```azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup
```

Az AK-fürt törléséhez használja a következő parancsot: 

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

## <a name="next-steps"></a>Következő lépések

* A Python, a Node vagy más alkalmazások bizalmas tárolón keresztüli futtatásával a [githubon a bizalmas tárolók mintáit](https://github.com/Azure-Samples/confidential-container-samples)használják.

* Az enklávét támogató [Azure Container Samples használatával a githubon is futtathat olyan alkalmazásokat, amelyek az enklávéban](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/)használhatók.

<!-- LINKS -->
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials

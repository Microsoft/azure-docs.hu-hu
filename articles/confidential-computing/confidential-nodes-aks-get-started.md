---
title: 'Gyors útmutató: Azure Kubernetes-szolgáltatás (ak) fürt üzembe helyezése az Azure CLI és a bizalmas számítástechnikai csomópontok használatával'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy, a bizalmas csomópontokkal rendelkező AK-fürtöt, és hogyan helyezhet üzembe egy Hello World alkalmazást az Azure CLI használatával.
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: quickstart
ms.date: 03/18/2020
ms.author: amgowda
ms.custom: contentperf-fy21q3
ms.openlocfilehash: 73770acefc8a153e4a2f2fde146f9afd4c319cd3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933134"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-with-confidential-computing-nodes-dcsv2-using-azure-cli"></a>Gyors útmutató: Azure Kubernetes Service (ak) fürt üzembe helyezése bizalmas számítástechnikai csomópontokkal (DCsv2) az Azure CLI használatával

Ez a rövid útmutató olyan fejlesztők vagy fürtök számára készült, akik gyorsan létrehozhatnak egy AK-fürtöt, és üzembe helyezhetik az alkalmazásokat az Azure felügyelt Kubernetes szolgáltatásának használatával. Emellett kiépítheti a fürtöt, és hozzáadhat bizalmas számítástechnikai csomópontokat Azure Portalból.

## <a name="overview"></a>Áttekintés

Ebből a rövid útmutatóból megtudhatja, hogyan helyezhet üzembe egy Azure Kubernetes Service-(ak-) fürtöt a bizalmas számítástechnikai csomópontokkal az Azure CLI használatával, és hogyan futtathat egy egyszerű Hello World alkalmazást egy enklávéban. Az AK egy felügyelt Kubernetes szolgáltatás, amely lehetővé teszi fürtök gyors üzembe helyezését és kezelését. További információért olvassa el az [AK bevezetését](../aks/intro-kubernetes.md) és az AK-beli [bizalmas csomópontok áttekintését](confidential-nodes-aks-overview.md).

> [!NOTE]
> A bizalmas számítástechnikai DCsv2 virtuális gépek olyan speciális hardvereket használnak, amelyek a magasabb díjszabás és a régió rendelkezésre állása alá esnek. További információ: a virtuális gépek oldal a [rendelkezésre álló SKU-és támogatott régiókhoz](virtual-machine-solutions.md).

### <a name="confidential-computing-node-features-dcsv2"></a>Bizalmas számítástechnikai csomópont-funkciók (DCsv2)

1. Linux-tárolókat támogató linuxos munkavégző csomópontok.
1. 2. generációs VM Ubuntu 18,04 Virtual Machines csomópontokkal.
1. Intel SGX ENKLÁVÉHOZ-alapú CPU titkosított oldal gyorsítótár-memóriával (EPC). További tudnivalók [itt](./faq.md).
1. A Kubernetes-verzió és az 1.16 + támogatása.
1. Az Intel SGX ENKLÁVÉHOZ DCAP illesztőprogramja előre telepítve van az AK-csomópontokon. További tudnivalók [itt](./faq.md).

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

1. Aktív Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a feladatok megkezdése előtt.
1. Az Azure CLI verziójának 2.0.64 vagy újabb verziója telepítve van és konfigurálva van a telepítési gépen ( `az --version` a verzió megkereséséhez futtassa a parancsot. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](../container-registry/container-registry-get-started-azure-cli.md).
1. Az előfizetésben legalább hat **DCsv2** -mag használható. Alapértelmezés szerint az Azure-előfizetések esetében a virtuális gép magok kvótája nyolc mag. Ha nyolcnál több magot igénylő fürtöt szeretne kiépíteni, a kvóta növeléséhez kövesse az [alábbi](../azure-portal/supportability/per-vm-quota-requests.md) utasításokat.

## <a name="create-a-new-aks-cluster-with-confidential-computing-nodes-and-add-on"></a>Hozzon létre egy új AK-fürtöt bizalmas számítástechnikai csomópontokkal és bővítményekkel

Kövesse az alábbi utasításokat a bizalmas számítástechnikai képességgel rendelkező csomópontok hozzáadásához.

### <a name="create-an-aks-cluster-with-a-system-node-pool"></a>AK-fürt létrehozása rendszercsomópont-készlettel

Ha már rendelkezik egy AK-fürttel, amely megfelel a fenti követelményeknek, [ugorjon a meglévő fürt szakaszra](#existing-cluster) egy új bizalmas számítástechnikai csomópont-készlet hozzáadásához.

Először hozzon létre egy erőforráscsoportot a fürthöz az az [Group Create][az-group-create] paranccsal. A következő példa létrehoz egy erőforráscsoport-nevet *myResourceGroup* a *westus2* régióban:

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Most hozzon létre egy AK-fürtöt az az [AK Create][az-aks-create] paranccsal:

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```

A fenti létrehoz egy új AK-fürtöt egy olyan rendszercsomópont-készlettel, amelyen engedélyezve van a bővítmény. Ezután adjon hozzá egy olyan felhasználói csomópontot, amely bizalmas számítástechnikai képességekkel rendelkezik az AK-fürthöz.

### <a name="add-a-confidential-computing-node-pool-to-the-aks-cluster"></a>Bizalmas számítástechnikai csomópont-készlet hozzáadása az AK-fürthöz 

A következő parancs futtatásával vegye fel a három csomóponttal rendelkező felhasználói csomópontok készletét `Standard_DC2s_v2` . Választhat egy másik SKU-t is a [DCsv2 SKU-i és régiók](../virtual-machines/dcv2-series.md)támogatott listájából.

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```

A futtatást követően a **DCsv2** rendelkező új csomópont-készletnek láthatónak kell lennie a bizalmas számítástechnikai kiegészítő Daemonsets ([SGX enklávéhoz-eszköz beépülő modullal](confidential-nodes-aks-overview.md#sgx-plugin)).

### <a name="verify-the-node-pool-and-add-on"></a>A csomópont-készlet és a bővítmény ellenőrzése

Kérje le az AK-fürt hitelesítő adatait az az az [AK Get-hitelesítőadats][az-aks-get-credentials] parancs használatával:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Ellenőrizze, hogy a csomópontok megfelelően lettek-e létrehozva, és a SGX ENKLÁVÉHOZ-hez kapcsolódó **daemonsets a kubectl** Get hüvelyek & csomópontok paranccsal futnak az alábbi ábrán látható módon:

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Ha a kimenet megfelel a fentieknek, akkor az AK-fürt most már készen áll a bizalmas alkalmazások futtatására.

Az alkalmazás egy enklávéban való teszteléséhez lépjen az enklávé üzembe helyezési szakaszának [Hello World](#hello-world) . Vagy kövesse az alábbi utasításokat további Node-készletek hozzáadásához az AK-ban (az AK támogatja a SGX ENKLÁVÉHOZ-csomópontok és a nem SGX ENKLÁVÉHOZ csomópont-készletek keverését).

## <a name="add-a-confidential-computing-node-pool-to-an-existing-aks-cluster"></a>Bizalmas számítástechnikai csomópont-készlet hozzáadása meglévő AK-fürthöz<a id="existing-cluster"></a>

Ez a szakasz azt feltételezi, hogy van egy már működő AK-fürt, amely megfelel az előfeltételek szakaszban felsorolt feltételeknek (a bővítményre vonatkozik).

### <a name="enable-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>A bizalmas számítástechnikai AK-bővítmény engedélyezése a meglévő fürtön

Futtassa a következő parancsot a bizalmas számítástechnikai bővítmény engedélyezéséhez:

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```

### <a name="add-a-dcsv2-user-node-pool-to-the-cluster"></a>**DCsv2** felhasználói csomópont-készlet hozzáadása a fürthöz

> [!NOTE]
> A bizalmas számítástechnikai képesség használatához a meglévő AK-fürtnek legalább egy **DCsv2** VM SKU-alapú csomópont-készlettel kell rendelkeznie. A bizalmas számítástechnikai DCs-v2 virtuális gépek SKU-jának megismeréséhez tekintse meg a [rendelkezésre álló SKU-k és a támogatott régiók](virtual-machine-solutions.md)című témakört.

A következő parancs futtatásával hozzon létre egy új csomópont-készletet:

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2
```

Győződjön meg arról, hogy az új, confcompool1 nevű csomópont-készlet lett létrehozva:

```azurecli-interactive
az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

### <a name="verify-that-daemonsets-are-running-on-confidential-node-pools"></a>Annak ellenőrzése, hogy a daemonsets a bizalmas csomópont-készleteken futnak-e

A következő ellenőrzés végrehajtásához jelentkezzen be a meglévő AK-fürtbe.

```console
kubectl get nodes
```

A kimenetnek az AK-fürtön az újonnan hozzáadott confcompool1 kell mutatnia. Más daemonsets is láthat.

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Ha a kimenet megfelel a fentieknek, akkor az AK-fürt most már készen áll a bizalmas alkalmazások futtatására. A tesztelési alkalmazások üzembe helyezéséhez kövesse az alábbi utasításokat.

## <a name="hello-world-from-isolated-enclave-application"></a>Hello World izolált enklávé alkalmazásból <a id="hello-world"></a>
Hozzon létre egy *Hello-World-enklávé. YAML* nevű fájlt, és illessze be a következő YAML-jegyzékbe. Ez a nyílt enklávé-alapú minta alkalmazás kódja az [Open enklávé projektben](https://github.com/openenclave/openenclave/tree/master/samples/helloworld)található. A következő üzemelő példány feltételezi, hogy telepítette a "confcom" addon.

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
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 5 # This limit will automatically place the job into confidential computing node. Alternatively you can target deployment to nodepools
      restartPolicy: Never
  backoffLimit: 0
  ```

Most az kubectl Apply paranccsal hozzon létre egy olyan mintát, amely egy biztonságos enklávéban fog elindulni, ahogy az a következő példában látható:

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

A társított csomópont-készletek eltávolításához vagy az AK-fürt törléséhez használja a következő parancsokat:

### <a name="remove-the-confidential-computing-node-pool"></a>A bizalmas számítástechnikai csomópont-készlet eltávolítása

```azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name myNodePoolName --resource-group myResourceGroup
```

### <a name="delete-the-aks-cluster"></a>Az AK-fürt törlése

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

## <a name="next-steps"></a>Következő lépések

* A bizalmas tárolók használatával bizalmasan kezelheti a Pythont, a csomópontot stb. az alkalmazásokat a bizalmas [tárolókban](https://github.com/Azure-Samples/confidential-container-samples).

* Az enklávéban az [Azure Container Samples](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/)használatával meglátogatható alkalmazások is futtathatók az enklávéban.

<!-- LINKS -->
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
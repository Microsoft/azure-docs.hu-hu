---
title: 'Gyors útmutató: Azure Kubernetes-szolgáltatás (ak) fürt üzembe helyezése az Azure CLI és a bizalmas számítástechnikai csomópontok használatával'
description: Ismerje meg, hogyan hozhat létre egy olyan AK-fürtöt, amely bizalmas csomópontokkal rendelkezik, és hogyan helyezhet üzembe egy Hello World alkalmazást az Azure CLI használatával
author: agowdamsft
ms.service: container-service
ms.topic: quickstart
ms.date: 2/8/2020
ms.author: amgowda
ms.openlocfilehash: 866c8340cf9c16d768f4035326aa2ec52dbf1401
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653363"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-with-confidential-computing-nodes-dcsv2-using-azure-cli"></a>Gyors útmutató: Azure Kubernetes Service (ak) fürt üzembe helyezése bizalmas számítástechnikai csomópontokkal (DCsv2) az Azure CLI használatával

Ez a rövid útmutató olyan fejlesztők vagy fürtök számára készült, akik gyorsan létrehozhatnak egy AK-fürtöt, és üzembe helyezhetik az alkalmazásokat az Azure felügyelt Kubernetes szolgáltatásának használatával. Emellett kiépítheti a fürtöt, és hozzáadhat bizalmas számítástechnikai csomópontokat Azure Portalból.

## <a name="overview"></a>Áttekintés

Ebből a rövid útmutatóból megtudhatja, hogyan helyezhet üzembe egy Azure Kubernetes Service-(ak-) fürtöt a bizalmas számítástechnikai csomópontokkal az Azure CLI használatával, és hogyan futtathat egy egyszerű Hello World alkalmazást egy enklávéban. Az AK egy felügyelt Kubernetes szolgáltatás, amely lehetővé teszi fürtök gyors üzembe helyezését és kezelését. További információ [az AK-](../aks/intro-kubernetes.md)ról.

> [!NOTE]
> A bizalmas számítástechnikai DCsv2 virtuális gépek olyan speciális hardvereket használnak, amelyek a magasabb díjszabás és a régió rendelkezésre állása alá esnek. További információ: a virtuális gépek oldal a [rendelkezésre álló SKU-és támogatott régiókhoz](virtual-machine-solutions.md).

### <a name="confidential-computing-node-features-dcxs-v2"></a>A bizalmas számítástechnikai csomópont funkciói (DC <x> s-v2)

1. Csak Linux-tárolókat támogató linuxos munkavégző csomópontok
1. 2. generációs VM Ubuntu 18,04 Virtual Machines csomópontokkal
1. Intel SGX ENKLÁVÉHOZ-alapú CPU titkosított oldal gyorsítótár-memóriával (EPC). [További információk](./faq.md)
1. Támogató Kubernetes-verzió 1.16 +
1. Az Intel SGX ENKLÁVÉHOZ DCAP illesztőprogramja előre telepítve van az AK-csomópontokon. [További információk](./faq.md)

## <a name="deployment-prerequisites"></a>Üzembehelyezési előfeltételek
Az üzembe helyezési oktatóanyaghoz az alábbiak szükségesek:

1. Aktív Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot a](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) Kezdés előtt
1. Az Azure CLI verziójának 2.0.64 vagy újabb verziója telepítve van és konfigurálva van a telepítési gépen ( `az --version` a verzió megkereséséhez futtassa a parancsot. Ha telepíteni vagy frissíteni szeretne, tekintse meg az [Azure CLI telepítését](../container-registry/container-registry-get-started-azure-cli.md) ismertető témakört.
1. Azure [AK – előzetes verziójú bővítmény](https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview) minimális verziója 0.5.0
1. Az előfizetésében legalább hat **DC <x> s-v2** mag használható. Alapértelmezés szerint a virtuális gép az Azure-előfizetések 8 maggal kapcsolatos bizalmas számítástechnikai kvótáját is felszámítja. Ha olyan fürtöt szeretne kiépíteni, amely több mint 8 magot igényel, kövesse az [alábbi](../azure-portal/supportability/per-vm-quota-requests.md) utasításokat a kvóta növeléséhez

## <a name="cli-based-preparation-steps-required-for-add-on-in-preview---optional-but-recommended"></a>CLI-alapú előkészítési lépések (az előzetes verzióhoz szükséges bővítményhez – nem kötelező, de ajánlott)
Az alábbi útmutatást követve engedélyezheti a bizalmas számítástechnikai bővítményt az AK-on.

### <a name="step-1-installing-the-cli-prerequisites"></a>1. lépés: a parancssori felület előfeltételeinek telepítése

A következő Azure CLI-parancsokkal telepítheti a 0.5.0-bővítményt vagy újabb verziót:

```azurecli-interactive
az extension add --name aks-preview
az extension list
```
A következő Azure CLI-parancsokkal frissítheti az AK-előnézeti CLI-bővítményt:

```azurecli-interactive
az extension update --name aks-preview
```
### <a name="step-2-azure-confidential-computing-addon-feature-registration-on-azure"></a>2. lépés: az Azure bizalmas számítástechnikai addon funkcióinak regisztrálása az Azure-ban
A AKS-ConfidentialComputingAddon regisztrálása az Azure-előfizetésben. Ez a funkció hozzáadja a SGX ENKLÁVÉHOZ-eszköz beépülő modul daemonset elemet a részletek [itt](./confidential-nodes-aks-overview.md#confidential-computing-add-on-for-aks)tárgyalt módon:
1. SGX ENKLÁVÉHOZ illesztőprogram beépülő modul
```azurecli-interactive
az feature register --name AKS-ConfidentialComputingAddon --namespace Microsoft.ContainerService
```
Több percet is igénybe vehet, amíg az állapot regisztrálva jelenik meg. A regisztrációs állapotot az "az Feature List" parancs használatával tekintheti meg. Ez a szolgáltatás regisztrációja csak egyszer érhető el az előfizetések esetében. Ha korábban már regisztrálták, ugorja át a fenti lépést:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-ConfidentialComputingAddon')].{Name:name,State:properties.state}"
```
Ha az állapot regisztrálva értékre van állítva, frissítse a Microsoft. Tárolószolgáltatás erőforrás-szolgáltató regisztrációját az "az Provider Register" parancs használatával:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```
## <a name="creating-new-aks-cluster-with-confidential-computing-nodes-and-add-on"></a>Új AK-fürt létrehozása bizalmas számítástechnikai csomópontokkal és bővítményekkel
Kövesse az alábbi utasításokat a bizalmas számítástechnikai képességgel rendelkező csomópontok hozzáadásához.

### <a name="step-1-creating-an-aks-cluster-with-system-node-pool"></a>1. lépés: AK-fürt létrehozása a rendszercsomópont-készlettel

Ha már rendelkezik egy AK-fürttel, amely megfelel a fenti követelményeknek, [ugorjon a meglévő fürt szakaszra](#existing-cluster) egy új bizalmas számítástechnikai csomópont-készlet hozzáadásához.

Először hozzon létre egy erőforráscsoportot a fürthöz az az Group Create paranccsal. A következő példa létrehoz egy erőforráscsoport-nevet *myResourceGroup* a *westus2* régióban:

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Most hozzon létre egy AK-fürtöt az az AK Create paranccsal.

```azurecli-interactive
# Create a new AKS cluster with system node pool with Confidential Computing addon enabled
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```
A fenti létrehoz egy új AK-fürtöt a rendszercsomópont-készlettel a bővítmény engedélyezésével. Most folytassa a bizalmas számítástechnikai Nodepool típusú felhasználói csomópont hozzáadását az AK-ban (DCsv2)

### <a name="step-2-adding-confidential-computing-node-pool-to-aks-cluster"></a>2. lépés: a bizalmas számítástechnikai csomópont-készlet hozzáadása az AK-fürthöz 

Futtassa az alábbi parancsot egy olyan felhasználói nodepool, amely `Standard_DC2s_v2` 3 csomóponttal rendelkezik. A DCsv2 SKU-i és régiók más támogatott listáját [itt](../virtual-machines/dcv2-series.md)választhatja ki:

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```
A fenti parancs egy, a **DC <x> s-v2-** vel rendelkező új csomópont-készletet hajt végre, amely bizalmas számítástechnikai bővítmény daemonsets ([SGX enklávéhoz-eszköz beépülő modul](confidential-nodes-aks-overview.md#sgx-plugin)
 
### <a name="step-3-verify-the-node-pool-and-add-on"></a>3. lépés: a csomópont-készlet és a bővítmény ellenőrzése
Kérje le az AK-fürt hitelesítő adatait az az az AK Get-hitelesítőadats parancs használatával:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```
Győződjön meg arról, hogy a csomópontok megfelelően lettek létrehozva, és a SGX ENKLÁVÉHOZ-hez kapcsolódó daemonsets **DC <x> s-v2** csomópont-készleteken futnak a kubectl get hüvelyek & Nodes parancs használatával, az alábbiak szerint:

```console
$ kubectl get pods --all-namespaces

output
kube-system     sgx-device-plugin-xxxx     1/1     Running
```
Ha a kimenet megfelel a fentinek, akkor az AK-fürt most már készen áll a bizalmas alkalmazások futtatására.

Nyissa meg [Hello World az enklávé](#hello-world) üzembe helyezése szakaszát, hogy tesztelje az alkalmazást egy enklávéban. Vagy kövesse az alábbi utasításokat további Node-készletek hozzáadásához az AK-ban (az AK támogatja a SGX ENKLÁVÉHOZ-csomópontok és a nem SGX ENKLÁVÉHOZ csomópont-készletek keverését)

## <a name="adding-confidential-computing-node-pool-to-existing-aks-cluster"></a>Bizalmas számítástechnikai csomópont-készlet hozzáadása meglévő AK-fürthöz<a id="existing-cluster"></a>

Ez a szakasz azt feltételezi, hogy van egy már működő AK-fürt, amely megfelel az előfeltételek szakaszban felsorolt feltételeknek (a bővítményre vonatkozik).

### <a name="step-1-enabling-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>1. lépés: a bizalmas számítástechnikai AK-bővítmény engedélyezése a meglévő fürtön

Futtassa az alábbi parancsot a bizalmas számítástechnikai bővítmény engedélyezéséhez

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```
### <a name="step-2-add-dcxs-v2-user-node-pool-to-the-cluster"></a>2. lépés: **DC <x> s-v2** felhasználói csomópont-készlet hozzáadása a fürthöz
    
> [!NOTE]
> Ahhoz, hogy használni lehessen a bizalmas számítástechnikai funkciót, a meglévő AK-fürtnek legalább egy **DC <x> s-v2** VM SKU-alapú csomópont-készlettel kell rendelkeznie. További információ a bizalmas számítástechnikai DCsv2 VM SKU itt [elérhető SKU-k és támogatott régiók](virtual-machine-solutions.md).
    
  ```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2

output node pool added

Verify

az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```
a fenti parancsnak fel kell sorolnia a confcompool1 nevű legutóbbi csomópont-készletet.

### <a name="step-3-verify-that-daemonsets-are-running-on-confidential-node-pools"></a>3. lépés: annak ellenőrzése, hogy a daemonsets a bizalmas csomópontok készletén futnak-e

Jelentkezzen be a meglévő AK-fürtbe az alábbi ellenőrzés végrehajtásához. 

```console
kubectl get nodes
```
A kimenetnek az AK-fürtön az újonnan hozzáadott confcompool1 kell mutatnia.

```console
$ kubectl get pods --all-namespaces

output (you may also see other daemonsets along SGX daemonsets as below)
kube-system     sgx-device-plugin-xxxx     1/1     Running
```
Ha a kimenet megfelel a fentinek, akkor az AK-fürt most már készen áll a bizalmas alkalmazások futtatására. Kövesse az alkalmazás tesztelési célú üzembe helyezését.

## <a name="hello-world-from-isolated-enclave-application"></a>Hello World izolált enklávé alkalmazásból <a id="hello-world"></a>
Hozzon létre egy *Hello-World-enklávé. YAML* nevű fájlt, és illessze be a következő YAML-jegyzékbe. Ez a nyílt enklávé-alapú minta alkalmazás kódja az [Open enklávé projektben](https://github.com/openenclave/openenclave/tree/master/samples/helloworld)található. Az alábbi központi telepítés feltételezi, hogy telepítette a "confcom" addon.

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
```

```console
$ kubectl get jobs -l app=sgx-test
NAME       COMPLETIONS   DURATION   AGE
sgx-test   1/1           1s         23s
```

```console
$ kubectl get pods -l app=sgx-test
```

```console
$ kubectl get pods -l app=sgx-test
NAME             READY   STATUS      RESTARTS   AGE
sgx-test-rchvg   0/1     Completed   0          25s
```

```console
$ kubectl logs -l app=sgx-test
```

```console
$ kubectl logs -l app=sgx-test
Hello world from the enclave
Enclave called into host to print: Hello World!
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A társított csomópont-készletek eltávolításához vagy az AK-fürt törléséhez használja az alábbi parancsokat:

Az AK-fürt törlése
``````azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```
Removing the confidential computing node pool

``````azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name myNodePoolName --resource-group myResourceGroup
``````

## <a name="next-steps"></a>Következő lépések

Python, node stb. futtatása Bizalmas tárolókban bizalmasan kezelheti az alkalmazásokat a [bizalmas tárolók mintáinak](https://github.com/Azure-Samples/confidential-container-samples)meglátogatásával.

Az enklávéban az [Azure Container Samples](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/)használatával meglátogatható alkalmazások is futtathatók az enklávéban.

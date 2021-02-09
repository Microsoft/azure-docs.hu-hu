---
title: 'Gyors útmutató: Azure Kubernetes-szolgáltatás (ak) fürt üzembe helyezése az Azure CLI és a bizalmas számítástechnikai csomópontok használatával'
description: Ismerje meg, hogyan hozhat létre egy olyan AK-fürtöt, amely bizalmas csomópontokkal rendelkezik, és hogyan helyezhet üzembe egy Hello World alkalmazást az Azure CLI használatával
author: agowdamsft
ms.service: container-service
ms.topic: quickstart
ms.date: 2/5/2020
ms.author: amgowda
ms.openlocfilehash: b6fe8f4fe34799a71d59b7487d96217b4ac6a429
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833203"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-with-confidential-computing-nodes-dcsv2-using-azure-cli-preview"></a>Gyors útmutató: Azure Kubernetes Service (ak) fürt üzembe helyezése bizalmas számítástechnikai csomópontokkal (DCsv2) az Azure CLI használatával (előzetes verzió)

Ez a rövid útmutató olyan fejlesztők vagy fürtök számára készült, akik gyorsan létrehozhatnak egy AK-fürtöt, és üzembe helyezhetik az alkalmazásokat az Azure felügyelt Kubernetes szolgáltatásának használatával.

## <a name="overview"></a>Áttekintés

Ebből a rövid útmutatóból megtudhatja, hogyan helyezhet üzembe egy Azure Kubernetes Service-(ak-) fürtöt az Azure CLI használatával, és hogyan futtathat egy Hello World alkalmazást egy enklávéban. Az AK egy felügyelt Kubernetes szolgáltatás, amely lehetővé teszi fürtök gyors üzembe helyezését és kezelését. További információ [az AK-](../aks/intro-kubernetes.md)ról.

> [!NOTE]
> A bizalmas számítástechnikai DCsv2 virtuális gépek olyan speciális hardvereket használnak, amelyek a magasabb díjszabás és a régió rendelkezésre állása alá esnek. További információ: a virtuális gépek oldal a [rendelkezésre álló SKU-és támogatott régiókhoz](virtual-machine-solutions.md).

> A DCsv2 a 2. generációs Virtual Machines az Azure-ban, ez a 2. generációs virtuális gép előzetes verziójú szolgáltatás, amely az AK-t használja. 

### <a name="deployment-pre-requisites"></a>Üzembe helyezési előfeltételek
A telepítési utasítások a következőket feltételezik:

1. Aktív Azure-előfizetéssel rendelkezik. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot a](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) Kezdés előtt
1. Az Azure CLI 2.0.64 vagy újabb verzióját kell telepítenie és konfigurálnia az üzembe helyezési gépen (Futtatás a `az --version` verzió megkereséséhez). Ha telepíteni vagy frissíteni szeretne, tekintse meg az [Azure CLI telepítését](../container-registry/container-registry-get-started-azure-cli.md) ismertető témakört.
1. [AK – előzetes verziójú bővítmény](https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview) minimális verziója 0.4.62 
1. A virtuálisgép-magok kvótájának rendelkezésre állása. Legalább hat **DC <x> s-v2** mag érhető el az előfizetésben a használathoz. Alapértelmezés szerint a virtuális gép az Azure-előfizetések 8 maggal kapcsolatos bizalmas számítástechnikai kvótáját is felszámítja. Ha olyan fürtöt szeretne kiépíteni, amely több mint 8 magot igényel, kövesse az [alábbi](../azure-portal/supportability/per-vm-quota-requests.md) utasításokat a kvóta növeléséhez

### <a name="confidential-computing-node-features-dcxs-v2"></a>A bizalmas számítástechnikai csomópont funkciói (DC <x> s-v2)

1. Csak Linux-tárolókat támogató linuxos munkavégző csomópontok
1. 2. generációs VM Ubuntu 18,04 Virtual Machines csomópontokkal
1. Intel SGX ENKLÁVÉHOZ-alapú CPU titkosított oldal gyorsítótár-memóriával (EPC). [További információk](./faq.md)
1. Támogató Kubernetes-verzió 1.16 +
1. Az Intel SGX ENKLÁVÉHOZ DCAP illesztőprogramja előre telepítve van az AK-csomópontokon. [További információk](./faq.md)
1. Az előzetes verzióban üzembe helyezett CLI-támogatás a portálon alapuló kiépítés után.


## <a name="installing-the-cli-pre-requisites"></a>A CLI Előfeltételek telepítése

A következő Azure CLI-parancsokkal telepítheti a 0.4.62-bővítményt vagy újabb verziót:

```azurecli-interactive
az extension add --name aks-preview
az extension list
```
A következő Azure CLI-parancsokkal frissítheti az AK-előnézeti CLI-bővítményt:

```azurecli-interactive
az extension update --name aks-preview
```
### <a name="generation-2-vms-feature-registration-on-azure"></a>2. generációs virtuális gépek funkciójának regisztrálása az Azure-ban
Regisztrálja a Gen2VMPreview az Azure-előfizetésben. Ez a funkció lehetővé teszi, hogy a 2. generációs Virtual Machinest AK-beli csomópont-készletekként kiépítse:

```azurecli-interactive
az feature register --name Gen2VMPreview --namespace Microsoft.ContainerService
```
Több percet is igénybe vehet, amíg az állapot regisztrálva jelenik meg. A regisztrációs állapotot az "az Feature List" parancs használatával tekintheti meg. Ez a szolgáltatás regisztrációja csak egyszer érhető el az előfizetések esetében. Ha korábban már regisztrálták, ugorja át a fenti lépést:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/Gen2VMPreview')].{Name:name,State:properties.state}"
```
Ha az állapot regisztrálva értékre van állítva, frissítse a Microsoft. Tárolószolgáltatás erőforrás-szolgáltató regisztrációját az "az Provider Register" parancs használatával:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="azure-confidential-computing-feature-registration-on-azure-optional-but-recommended"></a>Azure bizalmas számítástechnikai funkciók regisztrálása az Azure-ban (nem kötelező, de ajánlott)
A AKS-ConfidentialComputingAddon regisztrálása az Azure-előfizetésben. Ez a funkció két daemonsets fog felvenni, ahogy az [itt](./confidential-nodes-aks-overview.md#aks-provided-daemon-sets-addon)látható a részletek között:
1. SGX ENKLÁVÉHOZ illesztőprogram beépülő modul
2. SGX ENKLÁVÉHOZ-igazolási ajánlat segítője

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

## <a name="creating-an-aks-cluster"></a>AKS-fürt létrehozása

Ha már rendelkezik egy AK-fürttel, amely megfelel a fenti követelményeknek, [ugorjon a meglévő fürt szakaszra](#existing-cluster) egy új bizalmas számítástechnikai csomópont-készlet hozzáadásához.

Először hozzon létre egy erőforráscsoportot a fürthöz az az Group Create paranccsal. A következő példa létrehoz egy erőforráscsoport-nevet *myResourceGroup* a *westus2* régióban:

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Most hozzon létre egy AK-fürtöt az az AK Create paranccsal.

```azurecli-interactive
# Create a new AKS cluster with  system node pool with Confidential Computing addon enabled
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```
A fenti létrehoz egy új AK-fürtöt a rendszercsomópont-készlettel. Most folytassa a bizalmas számítástechnikai Nodepool típusú felhasználói csomópont hozzáadását az AK-ban (DCsv2)

Az alábbi példa egy felhasználói nodepool hoz létre, amelynek mérete 3 csomópont `Standard_DC2s_v2` . A DCsv2 SKU-i és régiók más támogatott listáját [itt](../virtual-machines/dcv2-series.md)választhatja ki:

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2 --aks-custom-headers usegen2vm=true
```
A fenti parancsnak hozzá kell adnia egy új, **DC <x> s-v2** típusú csomópontot, amely automatikusan futtat két daemonsets ezen a csomópont-készleten – ([SGX enklávéhoz-eszköz beépülő modul](confidential-nodes-aks-overview.md#sgx-plugin)  &  [SGX enklávéhoz-ajánlat segítője](confidential-nodes-aks-overview.md#sgx-quote))

Kérje le az AK-fürt hitelesítő adatait az az az AK Get-hitelesítőadats parancs használatával:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```
Győződjön meg arról, hogy a csomópontok megfelelően lettek létrehozva, és a SGX ENKLÁVÉHOZ-hez kapcsolódó daemonsets **DC <x> s-v2** csomópont-készleteken futnak a kubectl get hüvelyek & Nodes parancs használatával, az alábbiak szerint:

```console
$ kubectl get pods --all-namespaces

output
kube-system     sgx-device-plugin-xxxx     1/1     Running
kube-system     sgx-quote-helper-xxxx      1/1     Running
```
Ha a kimenet megfelel a fentinek, akkor az AK-fürt most már készen áll a bizalmas alkalmazások futtatására.

Nyissa meg [Hello World az enklávé](#hello-world) üzembe helyezése szakaszát, hogy tesztelje az alkalmazást egy enklávéban. Vagy kövesse az alábbi utasításokat további Node-készletek hozzáadásához az AK-ban (az AK támogatja a SGX ENKLÁVÉHOZ-csomópontok és a nem SGX ENKLÁVÉHOZ csomópont-készletek keverését)

## <a name="adding-confidential-computing-node-pool-to-existing-aks-cluster"></a>Bizalmas számítástechnikai csomópont-készlet hozzáadása meglévő AK-fürthöz<a id="existing-cluster"></a>

Ez a szakasz azt feltételezi, hogy rendelkezik egy már működő AK-fürttel, amely megfelel az előfeltételek szakaszban felsorolt feltételeknek.

Először is lehetővé teszi a funkció hozzáadását az Azure-előfizetéshez

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

Ezután engedélyezheti a bizalmas számítástechnikagel kapcsolatos AK-bővítményeket a meglévő fürtön:

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```
Most adjon hozzá egy **DC <x> s-v2** felhasználói csomópont-készletet a fürthöz
    
> [!NOTE]
> Ahhoz, hogy használni lehessen a bizalmas számítástechnikai funkciót, a meglévő AK-fürtnek legalább egy **DC <x> s-v2** VM SKU-alapú csomópont-készlettel kell rendelkeznie. További információ a bizalmas számítástechnikai DCsv2 VM SKU itt [elérhető SKU-k és támogatott régiók](virtual-machine-solutions.md).
    
  ```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2 --aks-custom-headers usegen2vm=true

output node pool added

Verify

az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

```console
kubectl get nodes
```
A kimenetnek az AK-fürtön az újonnan hozzáadott confcompool1 kell mutatnia.

```console
$ kubectl get pods --all-namespaces

output (you may also see other daemonsets along SGX daemonsets as below)
kube-system     sgx-device-plugin-xxxx     1/1     Running
kube-system     sgx-quote-helper-xxxx      1/1     Running
```
Ha a kimenet megfelel a fentinek, akkor az AK-fürt most már készen áll a bizalmas alkalmazások futtatására.

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

---
title: Több csomópontkészlet használata a Azure Kubernetes Service (AKS)
description: Megtudhatja, hogyan hozhat létre és kezelhet több csomópontkészletet egy fürthöz a Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 02/11/2021
ms.openlocfilehash: b7b54ccf6662e172ebfe95a84189df5e8e6e990f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832247"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Egy fürthöz több csomópontkészlet létrehozása és felügyelete az Azure Kubernetes Service (AKS) szolgáltatásban

Az Azure Kubernetes Service (AKS) szolgáltatásban az azonos konfigurációban lévő csomópontok csomópontkészletekbe *vannak csoportosítva.* Ezek a csomópontkészletek tartalmazzák az alkalmazásokat futtató mögöttes virtuális gépeket. A csomópontok kezdeti számát és méretét (SKU) az AKS-fürt létrehozásakor kell meghatározni, amely létrehoz egy [rendszer-csomópontkészletet.][use-system-pool] A különböző számítási vagy tárolási igényekkel rendelkezik alkalmazások támogatásához létrehozhat további felhasználói *csomópontkészleteket.* A rendszercsomópont-készletek elsődleges rendeltetése az olyan kritikus rendszerpodok üzemeltetése, mint a CoreDNS és a tunnelfront. A felhasználói csomópontkészletek szolgálják az alkalmazáspodok üzemeltetése elsődleges célját. Az alkalmazáspodok azonban ütemezheti a rendszer-csomópontkészleteket, ha csak egy készletet szeretne az AKS-fürtben. A felhasználói csomópontkészletek az alkalmazásspecifikus podok helyére valók. Ezekkel a további felhasználói csomópontkészletek használatával például GPU-kat biztosíthat nagy számítási igényű alkalmazásokhoz vagy nagy teljesítményű SSD-tárolókhoz.

> [!NOTE]
> Ez a funkció lehetővé teszi több csomópontkészlet létrehozása és kezelése nagyobb szintű vezérlését. Ennek eredményeképpen külön parancsokra van szükség a létrehozáshoz/frissítéshez/törléshez. Korábban a managedCluster API-n keresztül vagy annak használatával történt fürtműveleteket, és ez volt az egyetlen lehetőség a vezérlősík és `az aks create` `az aks update` egyetlen csomópontkészlet módosításakor. Ez a funkció egy külön műveletkészletet ad meg az ügynökkészletek számára az agentPool API-n keresztül, és a parancskészletet kell használnia a műveletek végrehajtásához egy adott `az aks nodepool` csomópontkészleten.

Ez a cikk bemutatja, hogyan hozhat létre és kezelhet több csomópontkészletet egy AKS-fürtben.

## <a name="before-you-begin"></a>Előkészületek

Az Azure CLI 2.2.0-s vagy újabb verziójának telepítve és konfigurálva kell. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="limitations"></a>Korlátozások

A több csomópontkészletet támogató AKS-fürtök létrehozásakor és kezelésekor a következő korlátozások érvényesek:

* Lásd: [Kvóták, virtuálisgép-méretkorlátozások és][quotas-skus-regions]régiónkénti rendelkezésre állás Azure Kubernetes Service (AKS) .
* A rendszer-csomópontkészletek akkor törölhetők, ha van egy másik rendszer-csomópontkészlete, amely az AKS-fürtön veszi át a helyét.
* A rendszerkészletek legalább egy csomópontot tartalmaznak, a felhasználói csomópontkészletek pedig nulla vagy több csomópontot is tartalmazhatnak.
* Az AKS-fürtnek a Standard termékváltozatú terheléselosztást kell használnia több csomópontkészlet használatához, ez a funkció alapszintű termékváltozatú terheléselosztással nem támogatott.
* Az AKS-fürtnek virtuálisgép-méretezési csoportokat kell használnia a csomópontokhoz.
* A csomópontkészletek neve csak kisbetűs alfanumerikus karaktereket tartalmazhat, és kisbetűvel kell kezdődnie. Linux-csomópontkészletek esetén a hossz 1–12 karakter, Windows-csomópontkészletek esetén 1–6 karakter hosszúságú lehet.
* Minden csomópontkészletnek ugyanabban a virtuális hálózatban kell lennie.
* Amikor több csomópontkészletet hoz létre a fürt létrehozásakor, a csomópontkészletek által használt összes Kubernetes-verziónak meg kell egyeznie a vezérlősíkhoz beállított verzióval. Ez a fürt csomópontkészletenkénti műveletekkel való kiépítése után frissíthető.

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

> [!Important]
> Ha éles környezetben egyetlen rendszer-csomópontkészletet futtat az AKS-fürthöz, javasoljuk, hogy legalább három csomópontot használjon a csomópontkészlethez.

Első lépésekként hozzon létre egy AKS-fürtöt egyetlen csomópontkészletben. Az alábbi példa az [az group create parancsot használja][az-group-create] egy *myResourceGroup* nevű erőforráscsoport létrehozásához az *eastus régióban.* Ezután létrejön egy *myAKSCluster* nevű AKS-fürt az [az aks create paranccsal.][az-aks-create]

> [!NOTE]
> Az *alapszintű* terheléselosztási termékváltozat **több** csomópontkészlet használata esetén nem támogatott. Alapértelmezés szerint az AKS-fürtök az Azure CLI *Standard* terheléselosztási termékváltozatával vannak létrehozva, és Azure Portal.

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 2 \
    --generate-ssh-keys \
    --load-balancer-sku standard
```

A fürt létrehozása néhány percet vesz igénybe.

> [!NOTE]
> A fürt megbízható működése érdekében legalább 2 (két) csomópontot kell futtatnia az alapértelmezett csomópontkészletben, mivel az alapvető rendszerszolgáltatások ezen a csomópontkészleten futnak.

Ha a fürt készen áll, az [az aks get-credentials][az-aks-get-credentials] paranccsal lekérte a fürt hitelesítő adatait a használatával való használatra: `kubectl`

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Csomópontkészlet hozzáadása

Az előző lépésben létrehozott fürt egyetlen csomópontkészletet tartalmaz. Adjunk hozzá egy második csomópontkészletet [az az aks nodepool add paranccsal.][az-aks-nodepool-add] Az alábbi példa egy *mynodepool* nevű csomópontkészletet hoz létre, amely *3 csomópontot* futtat:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3
```

> [!NOTE]
> A csomópontkészletek nevének kisbetűvel kell kezdődnie, és csak alfanumerikus karaktereket tartalmazhat. Linux-csomópontkészletek esetén a hossz 1 és 12 karakter között lehet, Windows-csomópontkészletek esetén a hossza 1 és 6 karakter között lehet.

A csomópontkészletek állapotának megtekintése az [az aks node pool list paranccsal][az-aks-nodepool-list] és az erőforráscsoport és a fürt nevének megadásával:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

Az alábbi példakimenet azt mutatja, hogy *a mynodepool* sikeresen létrejött három csomóponttal a csomópontkészletben. Az AKS-fürt előző lépésben való létrehozásakor létrejött egy alapértelmezett *nodepool1,* *2 csomópontszámmal.*

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> Ha a csomópontkészlet hozzáadásakor nincs megadva a *VmSize,* az alapértelmezett  Standard_D2s_v3 a Windows-csomópontkészletek és a Standard_DS2_v2 csomópontkészletek esetén.  Ha nincs *megadva OrchestratorVersion,* az alapértelmezett verziója megegyezik a vezérlősík verziójával.

### <a name="add-a-node-pool-with-a-unique-subnet-preview"></a>Csomópontkészlet hozzáadása egyedi alhálózattal (előzetes verzió)

A számítási feladatokhoz szükség lehet a fürt csomópontjainak különálló készletekre való felosztására a logikai elkülönítés érdekében. Ez az elkülönítés a fürt egyes csomópontkészletei számára dedikált külön alhálózatokkal is támogatható. Ez olyan követelményeket elégíthet ki, mint például a csomópontkészletek közötti felosztáshoz szükséges nem összefüggő virtuális hálózati címtér.

#### <a name="limitations"></a>Korlátozások

* A csomópontkészlethez rendelt összes alhálózatnak ugyanannak a virtuális hálózatnak kell lennie.
* A rendszerpodoknak hozzá kell férnie a fürt összes csomópontjához/podjához, hogy kritikus funkciókat, például DNS-feloldás és kubectl-naplók/exec/port továbbító proxy bújtatását biztosítják.
* Ha a fürt létrehozása után kibővíti a virtuális hálózatát, frissítenie kell a fürtöt (bármilyen felügyelt klónozási műveletet végre kell hajtanunk, de a csomópontkészlettel kapcsolatos műveletek nem számítanak), mielőtt hozzáadunk egy, az eredeti cidr-n kívüli alhálózatot. Az AKS hibát jelez az ügynökkészlet hozzáadásakor, bár eredetileg engedélyezve volt. Ha nem tudja, hogyan egyeztetni a fürtöt támogatási jegyként. 
* A calico hálózati szabályzat nem támogatott. 
* Az Azure Network Policy nem támogatott.
* A Kube-proxy egyetlen összefüggő cidr-t vár, és ezt használja három optmization-hez. Lásd ezt a [K.E.P.](https://github.com/kubernetes/enhancements/tree/master/keps/sig-network/2450-Remove-knowledge-of-pod-cluster-CIDR-from-iptables-rules) A részletekért itt [a](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-proxy/) és a --cluster-cidr adatokat. Az azure cni az első csomópontkészlet alhálózatát a kube-proxynak fogja adni. 

Ha dedikált alhálózattal hoz létre csomópontkészletet, adja át az alhálózati erőforrás-azonosítót további paraméterként a csomópontkészlet létrehozásakor.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --vnet-subnet-id <YOUR_SUBNET_RESOURCE_ID>
```

## <a name="upgrade-a-node-pool"></a>Csomópontkészlet frissítése

> [!NOTE]
> Fürtön vagy csomópontkészleten a frissítési és skálázható műveletek nem egyidejűleg következhetnek be, ha a rendszer hibát ad vissza. Ehelyett minden művelettípusnak be kell fejeződnie a célerőforráson az adott erőforrásra vonatkozó következő kérés előtt. Erről a hibaelhárítási útmutatóban [olvashat bővebben.](./troubleshooting.md#im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-upgraded-or-has-failed-upgrade)

Az ebben a szakaszban található parancsok egy adott csomópontkészlet frissítését ismertetik. A vezérlősík Kubernetes-verziójának és a csomópontkészletnek a frissítése közötti kapcsolatot az alábbi [szakaszban ismertetjük.](#upgrade-a-cluster-control-plane-with-multiple-node-pools)

> [!NOTE]
> A csomópontkészlet operációsrendszer-verziószáma a fürt Kubernetes-verziójához van kötve. A fürtfrissítést követően csak az operációs rendszer rendszerképének frissítései lesznek lekért verziók.

Mivel ebben a példában két csomópontkészlet van, az [az aks nodepool upgrade][az-aks-nodepool-upgrade] használatával kell frissítenünk egy csomópontkészletet. Az elérhető frissítések az [az aks get-upgrades használatával érhetők el][az-aks-get-upgrades]

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Frissítjük a *mynodepool csomópontkészletet.* Az [az aks nodepool upgrade paranccsal][az-aks-nodepool-upgrade] frissítse a csomópontkészletet az alábbi példában látható módon:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version KUBERNETES_VERSION \
    --no-wait
```

Listsa újra a csomópontkészletek állapotát [az az aks node pool list paranccsal.][az-aks-nodepool-list] Az alábbi példa azt mutatja, hogy *a mynodepool* frissítés *állapotban* van *KUBERNETES_VERSION:*

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "KUBERNETES_VERSION",
    ...
    "provisioningState": "Upgrading",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

A csomópontok frissítése a megadott verzióra néhány percet vesz igénybe.

Ajánlott eljárásként frissítse az AKS-fürt összes csomópontkészletét ugyanerre a Kubernetes-verzióra. A alapértelmezett viselkedése az, hogy az igazítás érdekében az összes csomópontkészletet a `az aks upgrade` vezérlősíkkal együtt frissíti. Az egyes csomópontkészletek frissítésének lehetősége lehetővé teszi működés közbeni frissítés elvégzését és podok ütemezését a csomópontkészletek között, hogy fenntartsa az alkalmazás üzemidejét a fenti korlátozásokon belül.

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>Fürtvezérlő sík frissítése több csomópontkészletből

> [!NOTE]
> A Kubernetes a szabványos [szemantikus verziószámozási](https://semver.org/) verziószámozási sémát használja. A verziószám *x.y.z,* ahol *x* a főverzió, *az y* az alverzió, a *z* pedig a javítás verziója. Az *1.12.6* verzióban például az 1 a főverzió, a 12 az alverzió, a 6 pedig a javítás verziója. A vezérlősík Kubernetes-verziója és a kezdeti csomópontkészlet beállítása a fürt létrehozásakor történik. Minden további csomópontkészlet Kubernetes-verziója be van állítva, amikor hozzáadja őket a fürthöz. A Kubernetes-verziók eltérőek lehetnek a csomópontkészletek, valamint a csomópontkészletek és a vezérlősík között.

Az AKS-fürtök két fürterőforrás-objektummal és társított Kubernetes-verzióval vannak társítva.

1. Egy fürtvezérlő sík Kubernetes-verziója.
2. Kubernetes-verziójú csomópontkészlet.

A vezérlősík egy vagy több csomópontkészlethez van leképezve. A frissítési műveletek viselkedése attól függ, hogy melyik Azure CLI-parancsot használja a rendszer.

Az AKS-vezérlősík frissítéséhez a használatával kell `az aks upgrade` frissíteni. Ez a parancs frissíti a vezérlősík verzióját és a fürt összes csomópontkészletét.

A parancs `az aks upgrade` jelölővel való kiadása csak `--control-plane-only` a fürtvezérlő síkot frissíti. A fürthöz társított csomópontkészletek egyike sem módosul.

Az egyes csomópontkészletek frissítéséhez a használatával kell `az aks nodepool upgrade` frissíteni. Ez a parancs csak a megadott Kubernetes-verzióval frissíti a célcsomópontkészletet

### <a name="validation-rules-for-upgrades"></a>A frissítések érvényesítési szabályai

A fürt vezérlősíkjának és csomópontkészletének érvényes Kubernetes-frissítését a következő szabálykészletek ellenőrzik.

* A csomópontkészletek frissítésének érvényes verzióira vonatkozó szabályok:
   * A csomópontkészlet verziójának a vezérlősík *főverzióval* azonosnak kell lennie.
   * A csomópontkészlet *alverziójának* a vezérlősík *verziójának* két alverzióján belül kell lennie.
   * A csomópontkészlet verziója nem lehet nagyobb a vezérlő `major.minor.patch` verziójánál.

* Frissítési műveletre vonatkozó szabályok:
   * A vezérlősík vagy a csomópontkészlet Kubernetes-verziója nem lehet korábbi verzióra.
   * Ha nincs megadva kubernetes-verzió csomópontkészlet, a viselkedés a használt ügyféltől függ. A deklaráció a Resource Manager-sablonok a csomópontkészlethez definiált meglévő verzióra esnek vissza, ha használva van, ha nincs ilyen, a vezérlősík verziója lesz használva a visszaeséshez.
   * Egy adott időpontban frissíthet vagy skálázhat egy vezérlősíkot vagy egy csomópontkészletet, de nem küldhet egyszerre több műveletet egyetlen vezérlősíkon vagy csomópontkészlet-erőforráson.

## <a name="scale-a-node-pool-manually"></a>Csomópontkészlet manuális skálázása

Az alkalmazás számítási feladatainak változásával előfordulhat, hogy skálázni kell a csomópontkészletben lévő csomópontok számát. A csomópontok száma felfelé vagy lefelé skálázható.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

A csomópontkészletekben található csomópontok számának skálázása az [az aks node pool scale paranccsal][az-aks-nodepool-scale] történik. Az alábbi példa a *mynodepool* csomópontjainak számát *5-re skálázja:*

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Listsa újra a csomópontkészletek állapotát [az az aks node pool list paranccsal.][az-aks-nodepool-list] Az alábbi példa azt mutatja, hogy  a *mynodepool* méretezési állapotban van, *5 csomóponttal:*

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Scaling",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

A skáláztatási művelet befejezése néhány percet vesz igénybe.

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>Adott csomópontkészlet automatikus skálázása az automatikus fürtméretozó engedélyezésével

Az AKS egy külön szolgáltatást kínál a csomópontkészletek automatikus méretezéséhez egy automatikus fürtméretozó [nevű funkcióval.](cluster-autoscaler.md) Ez a szolgáltatás csomópontkészletenként engedélyezhető egyedi minimális és maximális skálázható számokkal csomópontkészletenként. Ismerje meg, hogyan [használhatja az automatikus fürtméretozót csomópontkészletenként.](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)

## <a name="delete-a-node-pool"></a>Csomópontkészlet törlése

Ha már nincs szüksége egy készletre, törölheti, és eltávolíthatja a mögöttes virtuálisgép-csomópontokat. Csomópontkészlet törléséhez használja az [az aks node pool delete][az-aks-nodepool-delete] parancsot, és adja meg a csomópontkészlet nevét. Az alábbi példa törli az előző lépésekben létrehozott *mynoodepool-t:*

> [!CAUTION]
> A csomópontkészlet törlésekor nem állnak rendelkezésre adatvesztési helyreállítási lehetőségek. Ha a podok nem ütemezhetőek más csomópontkészleten, akkor ezek az alkalmazások nem érhetők el. Ügyeljen arra, hogy ne töröljön csomópontkészletet, ha a használatban található alkalmazások nem tartalmaznak biztonsági másolatokat az adatokról, vagy hogy futtathatók-e a fürt más csomópontkészletei.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

Az [az aks node pool list][az-aks-nodepool-list] parancs következő példakimenete azt mutatja, hogy a *mynodepool* *Törlés állapotban* van:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Deleting",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

A csomópontok és a csomópontkészlet törlése néhány percet vesz igénybe.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Virtuálisgép-méret megadása csomópontkészlethez

Az előző példákban a csomópontkészlet létrehozásához a rendszer a fürtben létrehozott csomópontok alapértelmezett virtuálisgép-méretét használta. Egy gyakoribb forgatókönyv, ha különböző méretű és képességekkel rendelkezik csomópontkészleteket hoz létre. Létrehozhat például egy olyan csomópontkészletet, amely nagy mennyiségű CPU-val vagy memóriával rendelkezik, vagy egy olyan csomópontkészletet, amely GPU-támogatást biztosít. A következő lépésben fertőzöttekkel és [toleransokkal](#setting-nodepool-taints) tudatja a Kubernetes-ütemezővel, hogy hogyan korlátozza a hozzáférést az ezeken a csomópontokon futtatható podokhoz.

A következő példában egy GPU-alapú csomópontkészletet hozunk létre, amely a virtuális *gép Standard_NC6* használja. Ezeket a virtuális gépeket az NVIDIA Tesla K80 kártya működteti. Az elérhető virtuálisgép-méretekkel kapcsolatos információkért lásd: Sizes for Linux virtual machines in Azure (Linux rendszerű virtuális gépek [méretei az Azure-ban).][vm-sizes]

Hozzon létre egy csomópontkészletet [az az aks node pool add paranccsal.][az-aks-nodepool-add] Ezúttal adja meg a *gpunodepool* nevet, és használja a paramétert a Standard_NC6 `--node-vm-size` megadásához: 

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

Az [az aks node pool list][az-aks-nodepool-list] parancs alábbi példakimenete azt mutatja, hogy a *gpunodepool* *a Csomópontok* létrehozása a megadott *VmSize-sel:*

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "gpunodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "vmSize": "Standard_NC6",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

A *gpunodepool* létrehozása néhány percet vesz igénybe.

## <a name="specify-a-taint-label-or-tag-for-a-node-pool"></a>Fertőzöttság, címke vagy címke megadása egy csomópontkészlethez

Csomópontkészlet létrehozásakor fertőzötteket, címkéket vagy címkéket adhat hozzá a csomópontkészlethez. Fertőzött, címke vagy címke hozzáadásakor a csomópontkészletben lévő összes csomópont is ezt a fertőzött, címkét vagy címkét kap.

> [!IMPORTANT]
> Fertőzöttek, címkék vagy címkék csomópontokhoz való hozzáadását a teljes csomópontkészlethez a használatával kell `az aks nodepool` tenni. Nem ajánlott fertőzöttségeket, tesztkörnyezeteket vagy címkéket alkalmazni a csomópontkészlet egyes csomópontjaira a `kubectl` használatával.  

### <a name="setting-nodepool-taints"></a>Nodepool-fertőzöttek beállítása

Fertőzött csomópontkészlet létrehozásához használja az [az aks nodepool add et.][az-aks-nodepool-add] Adja meg a *taintnp* nevet, és a paraméterrel adja meg a `--node-taints` *sku=gpu:NoSchedule* értéket a fertőzöttsághoz.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name taintnp \
    --node-count 1 \
    --node-taints sku=gpu:NoSchedule \
    --no-wait
```

> [!NOTE]
> Fertőzöttság csak a csomópontkészletek létrehozásakor beállítható a csomópontkészletek számára.

Az [az aks nodepool list][az-aks-nodepool-list] parancs alábbi példakimenete azt mutatja, hogy *a taintnp* *a Csomópontok* létrehozása a megadott *nodeTaints értékekkel:*

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "taintnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeTaints":  [
      "sku=gpu:NoSchedule"
    ],
    ...
  },
 ...
]
```

A fertőzöttség adatai láthatók a Kubernetesben a csomópontok ütemezési szabályainak kezeléséhez. A Kubernetes-ütemező a fertőzöttek és a toleransok használatával korlátozhatja, hogy milyen számítási feladatok futnak a csomópontokon.

* A **rendszer egy fertőzött csoportot** alkalmaz egy csomópontra, amely azt jelzi, hogy csak adott podok ütemezheti őket.
* Ezután **egy leranítást** alkalmazunk egy podra, amely lehetővé teszi *számukra* a csomópontok fertőzöttségének tűrését.

A Kubernetes speciális ütemezett funkcióinak használatával kapcsolatos további információkért lásd: Az [AKS][taints-tolerations] speciális ütemező funkcióinak ajánlott eljárásai

Az előző lépésben az *sku=gpu:NoSchedule* fertőzött csoportot alkalmazta a csomópontkészlet létrehozásakor. Az alábbi egyszerű YAML-jegyzékfájl egy tűrőt használ annak érdekében, hogy a Kubernetes ütemezője NGINX-podot futtason a csomópontkészlet egyik csomópontján.

Hozzon létre egy nevű `nginx-toleration.yaml` fájlt, és másolja be az alábbi YAML-példát:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 1
        memory: 2G
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Ütemezi a podot az `kubectl apply -f nginx-toleration.yaml` paranccsal:

```console
kubectl apply -f nginx-toleration.yaml
```

A pod ütemezése és az NGINX-rendszerkép lehozása néhány másodpercet vesz igénybe. A [pod állapotának megtekintéséhez][kubectl-describe] használja a kubectl describe pod parancsot. Az alábbi rövid példakimeneten az *sku=gpu:NoSchedule toleration* van alkalmazva. Az események szakaszban az ütemező hozzárendelte a podot az *aks-taintnp-28993262-vmss0000000 csomóponthoz:*

```console
kubectl describe pod mypod
```

```output
[...]
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 sku=gpu:NoSchedule
Events:
  Type    Reason     Age    From                Message
  ----    ------     ----   ----                -------
  Normal  Scheduled  4m48s  default-scheduler   Successfully assigned default/mypod to aks-taintnp-28993262-vmss000000
  Normal  Pulling    4m47s  kubelet             pulling image "mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine"
  Normal  Pulled     4m43s  kubelet             Successfully pulled image "mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine"
  Normal  Created    4m40s  kubelet             Created container
  Normal  Started    4m40s  kubelet             Started container
```

Csak azok a podok ütemezhetőek a taintnp csomópontjaira, amelyeken alkalmazva van ez a *leranítás.* Minden más pod a *nodepool1 csomópontkészletben* lenne ütemezve. Ha további csomópontkészleteket hoz létre, további fertőzöttek és toleranák használatával korlátozhatja, hogy milyen podok ütemezhetőek az adott csomópont-erőforrásokon.

### <a name="setting-nodepool-labels"></a>Csomópontkészlet-címkék beállítása

A csomópontkészlet létrehozása során címkéket is hozzáadhat a csomópontkészlethez. A csomópontkészletben beállított címkéket a csomópontkészlet minden csomópontja hozzáadja. Ezek [a címkék láthatók a Kubernetesben][kubernetes-labels] a csomópontok ütemezési szabályainak kezeléséhez.

Ha címkével szeretne csomópontkészletet létrehozni, használja [az az aks nodepool add et.][az-aks-nodepool-add] Adja meg a *labelnp* nevet, és a paraméterrel adja meg `--labels` *a dept=IT* és *costcenter=9999* értéket a címkékhez.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name labelnp \
    --node-count 1 \
    --labels dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> Címkét csak a csomópontkészletek létrehozásakor lehet beállítani a csomópontkészletek számára. A címkéknek kulcs/érték párnak is kell lennie, és érvényes [szintaxissal kell bírni.][kubernetes-label-syntax]

Az [az aks nodepool list parancs][az-aks-nodepool-list] alábbi példakimenete azt mutatja, hogy a *labelnp* *a Csomópontok* létrehozása a megadott *nodeLabels használatával:*

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "labelnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeLabels":  {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

### <a name="setting-nodepool-azure-tags"></a>Nodepool Azure-címkék beállítása

Azure-címkét alkalmazhat az AKS-fürt csomópontkészleteire. A csomópontkészletre alkalmazott címkéket a rendszer a csomópontkészlet minden csomópontjára alkalmazza, és a frissítésekkel megmarad. A címkéket a rendszer a csomópontkészlethez adott új csomópontokra is alkalmazza a felskálásos műveletek során. A címke hozzáadása segíthet az olyan feladatokban, mint a szabályzatok nyomon követése vagy a költségbecslés.

Az Azure-címkék olyan kulcsokkal is vannak, amelyek nem érzékenyek a műveletekhez, például amikor a kulcsban keresve leküld egy címkét. Ebben az esetben a rendszer az adott kulccsal címkét frissít vagy lekér, a kis- és a kis- és akódtól függetlenül. A címkeértékek megkülönböztetik a kis- és nagybetűket.

Ha az AKS-ben több címke is azonos kulcsokkal van beállítva, de eltérő kis- és nagy betűkkel, akkor a használt címke az első betűrendben. Az eredmény `{"Key1": "val1", "kEy1": "val2", "key1": "val3"}` például és lesz `Key1` `val1` beállítva.

Hozzon létre egy csomópontkészletet [az az aks nodepool add használatával.][az-aks-nodepool-add] Adja meg a *tagnodepool* nevet, és a paraméterrel adja meg `--tag` *a dept=IT* és *costcenter=9999* címkéket.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name tagnodepool \
    --node-count 1 \
    --tags dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> A paramétert az `--tags` [az aks nodepool update][az-aks-nodepool-update] parancs, valamint a fürt létrehozása során is használhatja. A fürt létrehozása során a paraméter alkalmazza a címkét a fürthöz `--tags` létrehozott kezdeti csomópontkészletre. Minden címkenévnek meg kell felelnie az Azure-erőforrások címkék [használatával való rendszerezése korlátozásának.][tag-limitation] A csomópontkészlet paraméterrel való frissítése frissíti a meglévő címkeértékeket, `--tags` és hozzáfűzi az új címkéket. Ha például a csomópontkészlet a *dept=IT* and *costcenter=9999* for tags paramétert tartalmazza, és a *team=dev* és *costcenter=111* paraméterekkel frissítette a címkéket, akkor a nodepool *dept=IT,* *costcenter=111* és *team=dev* címkékkel rendelkezik.

Az [az aks nodepool list parancs][az-aks-nodepool-list] következő példakimenete azt mutatja, hogy a *tagnodepool* *a Csomópontok* létrehozása a megadott *címkével:*

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "tagnodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "tags": {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Csomópontkészletek kezelése Resource Manager sablonnal

Ha egy Azure Resource Manager és felügyelt erőforrásokat hoz létre, általában frissítheti a sablon beállításait, és újra üzembe használhatja az erőforrás frissítéséhez. Az AKS-csomópontkészletek esetén a kezdeti csomópontkészlet-profil nem frissíthető az AKS-fürt létrehozása után. Ez a viselkedés azt jelenti, hogy nem frissíthet meglévő Resource Manager sablont, nem módosíthatja a csomópontkészleteket, és nem tudja újra üzembe tenni. Ehelyett létre kell hoznia egy külön Resource Manager, amely csak egy meglévő AKS-fürt csomópontkészleteit frissíti.

Hozzon létre például egy sablont, `aks-agentpools.json` és illessze be az alábbi példajegyzéket. Ez a példasablon a következő beállításokat konfigurálja:

* Frissíti a *myagentpool nevű Linux-csomópontkészletet* három csomópont futtatásához. 
* A csomópontkészletben lévő csomópontokat a Kubernetes *1.15.7-es* verziójának futtatására állítja be.
* A csomópont méretét a következőként *határozza Standard_DS2_v2.*

Szükség szerint szerkessze ezeket az értékeket a csomópontkészletek frissítéséhez, hozzáadásához vagy törléséhez:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterName": {
            "type": "string",
            "metadata": {
                "description": "The name of your existing AKS cluster."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "The location of your existing AKS cluster."
            }
        },
        "agentPoolName": {
            "type": "string",
            "defaultValue": "myagentpool",
            "metadata": {
                "description": "The name of the agent pool to create or update."
            }
        },
        "vnetSubnetId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The Vnet subnet resource ID for your existing AKS cluster."
            }
        }
    },
    "variables": {
        "apiVersion": {
            "aks": "2020-01-01"
        },
        "agentPoolProfiles": {
            "maxPods": 30,
            "osDiskSizeGB": 0,
            "agentCount": 3,
            "agentVmSize": "Standard_DS2_v2",
            "osType": "Linux",
            "vnetSubnetId": "[parameters('vnetSubnetId')]"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-01-01",
            "type": "Microsoft.ContainerService/managedClusters/agentPools",
            "name": "[concat(parameters('clusterName'),'/', parameters('agentPoolName'))]",
            "location": "[parameters('location')]",
            "properties": {
                "maxPods": "[variables('agentPoolProfiles').maxPods]",
                "osDiskSizeGB": "[variables('agentPoolProfiles').osDiskSizeGB]",
                "count": "[variables('agentPoolProfiles').agentCount]",
                "vmSize": "[variables('agentPoolProfiles').agentVmSize]",
                "osType": "[variables('agentPoolProfiles').osType]",
                "storageProfile": "ManagedDisks",
                "type": "VirtualMachineScaleSets",
                "vnetSubnetID": "[variables('agentPoolProfiles').vnetSubnetId]",
                "orchestratorVersion": "1.15.7"
            }
        }
    ]
}
```

A sablon üzembe helyezése az [az deployment group create paranccsal,][az-deployment-group-create] az alábbi példában látható módon. A rendszer rákérdez a meglévő AKS-fürt nevére és helyére:

```azurecli-interactive
az deployment group create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

> [!TIP]
> A csomópontkészlethez úgy adhat hozzá címkét, hogy hozzáadja a *címke* tulajdonságot a sablonhoz, ahogy az az alábbi példában látható.
> 
> ```json
> ...
> "resources": [
> {
>   ...
>   "properties": {
>     ...
>     "tags": {
>       "name1": "val1"
>     },
>     ...
>   }
> }
> ...
> ```

Az AKS-fürt frissítése a csomópontkészlet beállításaitól és a sablonban megadott műveletektől függően eltarthat néhány Resource Manager.

## <a name="assign-a-public-ip-per-node-for-your-node-pools"></a>Nyilvános IP-cím hozzárendelése csomópontonként a csomópontkészletek számára

Az AKS-csomópontoknak nincs szükségük saját nyilvános IP-címre a kommunikációhoz. Előfordulhat azonban, hogy a forgatókönyvek megkövetelik, hogy egy csomópontkészlet csomópontjai saját dedikált nyilvános IP-címeket fogadjanak. Gyakori forgatókönyv a játékokkal kapcsolatos számítási feladatok esetében, ahol a konzolnak közvetlen kapcsolatot kell létesítenie egy felhőbeli virtuális géppel az ugrások minimalizálása érdekében. Ez a forgatókönyv a csomópont nyilvános IP-címének használatával érhető el az AKS-sel.

Először hozzon létre egy új erőforráscsoportot.

```azurecli-interactive
az group create --name myResourceGroup2 --location eastus
```

Hozzon létre egy új AKS-fürtöt, és csatoljon egy nyilvános IP-címet a csomópontokhoz. A csomópontkészlet minden csomópontja egyedi nyilvános IP-címet kap. Ezt a Virtuálisgép-méretezési készlet példányai között ellenőrizheti.

```azurecli-interactive
az aks create -g MyResourceGroup2 -n MyManagedCluster -l eastus  --enable-node-public-ip
```

Meglévő AKS-fürtök esetén új csomópontkészletet is hozzáadhat, és csatolhat egy nyilvános IP-címet a csomópontokhoz.

```azurecli-interactive
az aks nodepool add -g MyResourceGroup2 --cluster-name MyManagedCluster -n nodepool2 --enable-node-public-ip
```

### <a name="use-a-public-ip-prefix"></a>Nyilvános IP-előtag használata

A nyilvános IP-előtag használata számos [előnnyel jár.][public-ip-prefix-benefits] Az AKS támogatja a meglévő nyilvános IP-előtagok címeinek a csomópontok számára való használatát azáltal, hogy új fürt létrehozásakor vagy csomópontkészlet hozzáadásakor az erőforrás-azonosítót a jelzővel adja `node-public-ip-prefix` át.

Először hozzon létre egy nyilvános [IP-előtagot az az network public-ip prefix create használatával:][az-public-ip-prefix-create]

```azurecli-interactive
az network public-ip prefix create --length 28 --location eastus --name MyPublicIPPrefix --resource-group MyResourceGroup3
```

Tekintse meg a kimenetet, és jegyezze fel az `id` előtaghoz a előtagot:

```output
{
  ...
  "id": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup3/providers/Microsoft.Network/publicIPPrefixes/MyPublicIPPrefix",
  ...
}
```

Végül új fürt létrehozásakor vagy új csomópontkészlet hozzáadásakor használja a jelzőt, és adja meg az `node-public-ip-prefix` előtag erőforrás-azonosítóját:

```azurecli-interactive
az aks create -g MyResourceGroup3 -n MyManagedCluster -l eastus --enable-node-public-ip --node-public-ip-prefix /subscriptions/<subscription-id>/resourcegroups/MyResourceGroup3/providers/Microsoft.Network/publicIPPrefixes/MyPublicIPPrefix
```

### <a name="locate-public-ips-for-nodes"></a>Csomópontok nyilvános IP-ip-jainak megkeresása

A csomópontok nyilvános IP-eit többféleképpen is megkeresheti:

* Használja az [az vmss list-instance-public-ips Azure CLI-parancsot.][az-list-ips]
* [PowerShell- vagy Bash-parancsokat használjon.][vmss-commands] 
* A nyilvános IP-eket a virtuálisgép-méretezési Azure Portal virtuálisgép-méretezési készlet példányainak megtekintésével is megtekintheti.

> [!Important]
> A [csomóponti erőforráscsoport tartalmazza][node-resource-group] a csomópontokat és azok nyilvános IP-eit. A csomópont-erőforráscsoportot a parancsok végrehajtásakor használja a csomópontok nyilvános IP-ip-einek megkeresésében.

```azurecli
az vmss list-instance-public-ips -g MC_MyResourceGroup2_MyManagedCluster_eastus -n YourVirtualMachineScaleSetName
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben a cikkben egy GPU-alapú csomópontokat tartalmazó AKS-fürtöt hozott létre. A szükségtelen költségek csökkentése érdekében törölheti a *gpunodepool-t* vagy a teljes AKS-fürtöt.

A GPU-alapú csomópontkészlet törléséhez használja az [az aks nodepool delete][az-aks-nodepool-delete] parancsot az alábbi példában látható módon:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Magának a fürtnek a törléséhez használja [az az group delete][az-group-delete] parancsot az AKS-erőforráscsoport törléséhez:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

Törölheti a csomópontkészletek nyilvános IP-címéhez létrehozott további fürtöt is.

```azurecli-interactive
az group delete --name myResourceGroup2 --yes --no-wait
```

## <a name="next-steps"></a>Következő lépések

További információ a [rendszer-csomópontkészletről:][use-system-pool].

Ebben a cikkben megtanulta, hogyan hozhat létre és kezelhet több csomópontkészletet egy AKS-fürtben. A podok csomópontkészletek közötti szabályozásával kapcsolatos további információkért tekintse meg az AKS speciális ütemezői funkcióinak [ajánlott eljárásait.][operator-best-practices-advanced-scheduler]

Windows Server-tárolócsomópontkészletek létrehozásához és használathoz lásd: Windows Server-tároló létrehozása az [AKS-ban.][aks-windows]

Használjon [közelségi elhelyezési csoportokat][reduce-latency-ppg] az AKS-alkalmazások késésének csökkentéséhez.

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_get_credentials
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_create
[az-aks-get-upgrades]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_get_upgrades
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_delete
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest&preserve-view=true#az_extension_add
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest&preserve-view=true#az_extension_update
[az-group-create]: /cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_create
[az-group-delete]: /cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete
[az-deployment-group-create]: /cli/azure/deployment/group?view=azure-cli-latest&preserve-view=true#az_deployment_group_create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/management/tag-resources.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/sizes.md
[use-system-pool]: use-system-pools.md
[ip-limitations]: ../virtual-network/virtual-network-ip-addresses-overview-arm#standard
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[vmss-commands]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine
[az-list-ips]: /cli/azure/vmss?view=azure-cli-latest&preserve-view=true#az_vmss_list_instance_public_ips
[reduce-latency-ppg]: reduce-latency-ppg.md
[public-ip-prefix-benefits]: ../virtual-network/public-ip-address-prefix.md#why-create-a-public-ip-address-prefix
[az-public-ip-prefix-create]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest&preserve-view=true#az_network_public_ip_prefix_create

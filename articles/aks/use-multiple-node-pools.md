---
title: Több Node-készlet használata az Azure Kubernetes szolgáltatásban (ak)
description: Ismerje meg, hogyan hozhat létre és kezelhet több Node-készletet egy fürthöz az Azure Kubernetes szolgáltatásban (ak)
services: container-service
ms.topic: article
ms.date: 04/08/2020
ms.openlocfilehash: 07c4628a17d2c76e8e4608c9c6d059a81a9c378f
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182859"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Egy fürthöz több csomópontkészlet létrehozása és felügyelete az Azure Kubernetes Service (AKS) szolgáltatásban

Az Azure Kubernetes szolgáltatásban (ak) az azonos konfiguráció csomópontjai a *csomópont-készletekbe* vannak csoportosítva. Ezek a csomópont-készletek az alkalmazásokat futtató mögöttes virtuális gépeket tartalmazzák. A csomópontok kezdeti száma és a mérete (SKU) akkor van meghatározva, amikor egy AK-fürtöt hoz létre, amely létrehoz egy [rendszercsomópont-készletet][use-system-pool]. A különböző számítási vagy tárolási igényekkel rendelkező alkalmazások támogatásához további *felhasználói csomópont-készleteket* is létrehozhat. A rendszercsomópont-készletek a kritikus rendszerhüvelyek, például a CoreDNS és a tunnelfront üzemeltetésének elsődleges célját szolgálják ki. A felhasználói csomópontok készletei az alkalmazás-hüvelyek üzemeltetésének elsődleges céljaként szolgálnak. Az Application hüvelyek azonban a rendszercsomópont-készletekbe ütemezhetők, ha csak egy készletet szeretne használni az AK-fürtben. A felhasználói csomópontok készletei az alkalmazás-specifikus hüvelyek elhelyezésére szolgálnak. Ezekkel a további felhasználói csomópont-készletekkel például GPU-t biztosíthat a nagy számítási igényű alkalmazásokhoz, vagy hozzáférhet a nagy teljesítményű SSD-tárolóhoz.

> [!NOTE]
> Ez a funkció lehetővé teszi a több csomópontos készlet létrehozását és kezelését. Ennek eredményeképpen külön parancsok szükségesek a létrehozás/frissítés/törlés számára. Korábban `az aks create` a MANAGEDCLUSTER API-val vagy annak használatával végzett fürtműveleteket, `az aks update` és az egyetlen lehetőség, hogy megváltoztassa a vezérlő síkot és egyetlen csomópontos készletet. Ez a funkció az agentPool API-n keresztül egy külön műveleti készletet tesz elérhetővé az ügynökök számára, és a parancs használatát igényli a `az aks nodepool` műveletek végrehajtásához egy adott csomópont-készleten.

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet több Node-készletet egy AK-fürtben.

## <a name="before-you-begin"></a>Előkészületek

Szüksége lesz az Azure CLI-es vagy újabb verziójára, amely telepítve van és konfigurálva van. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="limitations"></a>Korlátozások

A több csomópontot támogató AK-fürtök létrehozásakor és kezelésekor a következő korlátozások érvényesek:

* Tekintse [meg a kvótákat, a virtuális gépek méretére vonatkozó korlátozásokat és a régió elérhetőségét az Azure Kubernetes szolgáltatásban (ak)][quotas-skus-regions].
* A rendszercsomópont-készleteket törölheti, ha rendelkezik egy másik rendszercsomópont-készlettel, hogy az AK-fürtön legyen a helyük.
* A rendszerkészleteknek legalább egy csomópontot tartalmazniuk kell, és a felhasználói csomópontok készletei nulla vagy több csomópontot is tartalmazhatnak.
* Az AK-fürtnek a standard SKU Load balancert kell használnia több Node-készlet használatához, a szolgáltatás alapszintű SKU-terheléselosztó esetén nem támogatott.
* Az AK-fürtnek virtuálisgép-méretezési csoportokat kell használnia a csomópontokhoz.
* A csomópontok készletének neve csak kisbetűket és kisbetűs karaktereket tartalmazhat. A Linux-csomópontok készletei esetében a hossznak 1 és 12 karakter közöttinek kell lennie, a Windows-csomópontok esetében a hossznak 1 és 6 karakter közöttinek kell lennie.
* Az összes csomópont-készletnek ugyanabban a virtuális hálózatban kell lennie.
* Ha több csomópont-készletet hoz létre a fürt létrehozásakor, a csomópontok által használt összes Kubernetes-verziónak meg kell egyeznie a vezérlési síkon beállított verzióval. Ezt azután frissítheti, ha a fürtöt csomópont-készletezési műveletek használatával állították ki.

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

> [!Important]
> Ha az AK-fürthöz egyetlen rendszercsomópont-készletet futtat éles környezetben, javasoljuk, hogy legalább három csomópontot használjon a csomópont-készlethez.

Első lépésként hozzon létre egy AK-fürtöt egyetlen csomópontos készlettel. Az alábbi példa az az [Group Create][az-group-create] paranccsal létrehoz egy *myResourceGroup* nevű erőforráscsoportot a *eastus* régióban. Ezután létrejön egy *myAKSCluster* nevű AK-fürt az az [AK Create][az-aks-create] paranccsal.

> [!NOTE]
> Az *alapszintű* Load Balancer SKU **nem támogatott** több csomópontos készlet használata esetén. Alapértelmezés szerint az AK-fürtök a *standard* Load Balancer SKU-val jönnek létre az Azure CLI-ből és Azure Portal.

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
> Annak biztosítása érdekében, hogy a fürt megbízhatóan működjön, legalább 2 (két) csomópontot kell futtatnia az alapértelmezett csomópont-készletben, mivel az alapvető rendszerszolgáltatások ezen a csomópont-készleten futnak.

Ha a fürt elkészült, használja az az [AK Get-hitelesítőadats][az-aks-get-credentials] parancsot a fürt hitelesítő adatainak lekéréséhez a következővel való használatra `kubectl` :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Csomópontkészlet hozzáadása

Az előző lépésben létrehozott fürt egyetlen csomópont-készlettel rendelkezik. Hozzunk létre egy második csomópont-készletet az az [AK nodepool Add][az-aks-nodepool-add] paranccsal. A következő példa egy *mynodepool* nevű csomópont-készletet hoz létre, amely *3* csomópontot futtat:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3
```

> [!NOTE]
> A csomópont-készlet nevének kisbetűvel kell kezdődnie, és csak alfanumerikus karaktereket tartalmazhat. A Linux-csomópontok készletei esetében a hossznak 1 és 12 karakter közöttinek kell lennie, a Windows-csomópontok esetében a hossznak 1 és 6 karakter közöttinek kell lennie.

A csomópont-készletek állapotának megtekintéséhez használja az az [AK Node Pool List][az-aks-nodepool-list] parancsot, és adja meg az erőforráscsoportot és a fürt nevét:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

A következő példa kimenete azt mutatja, hogy a *mynodepool* sikeresen létrejött a csomópont-készletben lévő három csomóponttal. Ha az AK-fürt az előző lépésben lett létrehozva, a rendszer egy alapértelmezett *nodepool1* hozott létre, amelynek a csomópontja *2*.

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
> Ha nem ad meg *VmSize* a csomópont hozzáadásakor, az alapértelmezett méret *Standard_D2s_v3* a Windows-csomópontok készletei számára, és a Linux-csomópontok *Standard_DS2_v2* . Ha nincs megadva *OrchestratorVersion* , az alapértelmezett érték a vezérlő síkjával megegyező verzió.

### <a name="add-a-node-pool-with-a-unique-subnet-preview"></a>Csomópont-készlet hozzáadása egyedi alhálózattal (előzetes verzió)

A számítási feladatok esetében előfordulhat, hogy a fürt csomópontjait különálló készletekbe kell osztani a logikai elkülönítéshez. Ez az elkülönítés a fürt egyes csomópontjaihoz dedikált külön alhálózatokkal is támogatott. Ez olyan követelményeket is képes kezelni, mint például a nem összefüggő virtuális hálózati címtartomány a csomópont-készletek közötti felosztásához.

#### <a name="limitations"></a>Korlátozások

* A nodepools rendelt összes alhálózatnak ugyanahhoz a virtuális hálózathoz kell tartoznia.
* A rendszerhüvelyeknek hozzá kell férniük a fürt összes csomópontja számára a kritikus funkciók, például a coreDNS keresztüli DNS-feloldás biztosításához.
* A csomópont-készlet egyedi alhálózatának hozzárendelése az előzetes verzióban az Azure CNI korlátozódik.
* Az előzetes verzióban nem támogatott a hálózati házirendek egyedi alhálózattal való használata.

Ha egy dedikált alhálózattal rendelkező csomópont-készletet szeretne létrehozni, adja át az alhálózati erőforrás-azonosítót további paraméterként egy csomópont-készlet létrehozásakor.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --vnet-subnet-id <YOUR_SUBNET_RESOURCE_ID>
```

## <a name="upgrade-a-node-pool"></a>Csomópont-készlet frissítése

> [!NOTE]
> Fürtön vagy csomóponton található műveletek frissítése és méretezése nem végezhető el egyszerre, ha a rendszer hibát adott vissza. Ehelyett minden Művelettípus a következő, ugyanazon az erőforráson megjelenő kérelem előtt fejeződik be a cél erőforráson. Erről a [hibaelhárítási útmutatóban](./troubleshooting.md#im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-upgraded-or-has-failed-upgrade)olvashat bővebben.

Az ebben a szakaszban szereplő parancsok azt ismertetik, hogyan lehet frissíteni egy adott csomópont-készletet. A vezérlési sík és a Kubernetes verziójának frissítése közötti kapcsolatot az [alábbi szakasz](#upgrade-a-cluster-control-plane-with-multiple-node-pools)ismerteti.

> [!NOTE]
> A Node Pool operációsrendszer-rendszerkép verziója a fürt Kubernetes-verziójához van kötve. A fürt frissítését követően csak az operációs rendszer rendszerképének frissítését fogja kérni.

Mivel ebben a példában két csomópont-készlet található, a csomópont-készlet frissítéséhez [az az AK nodepool upgrade][az-aks-nodepool-upgrade] -t kell használnia. Az elérhető frissítések megtekintéséhez használja az [az AK Get-Upgrades][az-aks-get-upgrades]

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Frissítse a *mynodepool*. Az az [AK nodepool upgrade][az-aks-nodepool-upgrade] paranccsal frissítse a csomópont-készletet, ahogy az az alábbi példában is látható:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version KUBERNETES_VERSION \
    --no-wait
```

Sorolja fel újra a csomópont-készletek állapotát az az [AK Node Pool List][az-aks-nodepool-list] parancs használatával. Az alábbi példa azt mutatja, hogy a *mynodepool* a *frissítési* állapotban van *KUBERNETES_VERSION*:

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

A csomópontok a megadott verzióra való frissítése néhány percet vesz igénybe.

Ajánlott eljárásként egy AK-fürt összes csomópont-készletét ugyanarra a Kubernetes-verzióra kell frissíteni. Az alapértelmezett viselkedése az, `az aks upgrade` Ha az összes csomópont-készletet a vezérlési síkkal együtt frissíti az igazítás eléréséhez. Az egyes csomópont-készletek verziófrissítése lehetővé teszi a működés közbeni frissítés végrehajtását és a csomópontok közötti ütemezést, hogy a fent említett korlátozásokon belül fenntartsa az alkalmazás üzemidőét.

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>Fürt vezérlőelem síkja több Node-készlettel

> [!NOTE]
> A Kubernetes a szabványos [szemantikai verziószámozási](https://semver.org/) sémát használja. A verziószám *x. y. z* értékként van kifejezve, ahol az *x* a főverzió, az *y* az alverzió, a *z* pedig a javítás verziója. Például a *1.12.6* verzióban az 1 a főverzió, a 12 a másodlagos verzió, a 6 pedig a javítás verziója. A Kubernetes-verzió és a kezdeti csomópont-készlet is be van állítva a fürt létrehozásakor. Az összes további csomópont-készlet Kubernetes-verziója be van állítva a fürthöz való hozzáadásakor. A Kubernetes-verziók eltérőek lehetnek a csomópont-készletek, valamint a csomópont-készlet és a vezérlő síkja között.

Az AK-fürtök két fürterőforrás-objektummal rendelkeznek, amelyek Kubernetes-verzióval vannak társítva.

1. A fürt vezérlő síkja Kubernetes verziója.
2. Csomópont-készlet Kubernetes-verzióval.

A vezérlő síkja egy vagy több csomópont-készletet képez le. A frissítési művelet viselkedése attól függ, hogy melyik Azure CLI-parancsot használja a rendszer.

Az AK-vezérlési sík frissítéséhez a-t kell használnia `az aks upgrade` . Ez a parancs frissíti a vezérlési sík verzióját és a fürt összes csomópont-készletét.

A `az aks upgrade` parancs és a `--control-plane-only` jelző kiadása csak a fürt vezérlőjét frissíti. A fürthöz társított csomópont-készletek egyike sincs módosítva.

Az egyes csomópont-készletek frissítéséhez a használata szükséges `az aks nodepool upgrade` . Ez a parancs csak a cél csomópont-készletet frissíti a megadott Kubernetes-verzióval.

### <a name="validation-rules-for-upgrades"></a>A frissítésekre vonatkozó érvényesítési szabályok

A fürt Kubernetes és a csomópont-készletek érvényes frissítéseit az alábbi szabályok határozzák meg.

* A csomópont-készletek frissítésének érvényes verzióira vonatkozó szabályok:
   * A csomópont-készlet verziójának *ugyanazzal a* főverzióval kell rendelkeznie, mint a vezérlő síkja.
   * A csomópont-készlet alverziójának a vezérlési sík verziójának *két* *alverzióján* belül kell lennie.
   * A csomópont-készlet verziója nem lehet nagyobb, mint a vezérlő `major.minor.patch` verziója.

* A frissítési művelet elküldésére vonatkozó szabályok:
   * A vezérlő síkja vagy a Node Pool Kubernetes verziója nem minősíthető vissza.
   * Ha nincs megadva a csomópont-készlet Kubernetes verziója, a viselkedés a használt ügyféltől függ. A Resource Manager-sablonokban szereplő deklaráció visszakerül a csomópont-készlethez definiált meglévő verzióra, ha a nincs beállítva, ha a vezérlési sík verziója visszaesik.
   * Egy adott időpontban frissítheti vagy méretezheti a vezérlési síkot vagy a csomópont-készletet, nem küldhet egyszerre több műveletet egyetlen vezérlő síkon vagy Node Pool-erőforráson.

## <a name="scale-a-node-pool-manually"></a>Csomópont-készlet manuális skálázása

Az alkalmazás számítási feladatainak változása miatt szükség lehet a csomópontok számának skálázására. A csomópontok száma növelhető felfelé vagy lefelé.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

A csomópontok számának skálázásához használja az az [AK Node Pool Scale][az-aks-nodepool-scale] parancsot. Az alábbi példa a *mynodepool* csomópontok számát *5*-re méretezi:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Sorolja fel újra a csomópont-készletek állapotát az az [AK Node Pool List][az-aks-nodepool-list] parancs használatával. Az alábbi példa azt mutatja, hogy a *mynodepool* a *skálázási* állapotban van, és új számú *5* csomóponttal rendelkezik:

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

A skálázási művelet elvégzése néhány percet vesz igénybe.

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>A fürt automatikus méretezésének engedélyezésével automatikusan méretezhető egy adott csomópont-készlet

Az AK egy külön funkciót kínál, amellyel automatikusan méretezheti a csomópont-készleteket a [fürt automatikus méretezés](cluster-autoscaler.md)nevű funkciójával. Ez a funkció egy csomópont-készleten engedélyezhető, és a csomópontok egyedi minimális és maximális skálázási száma. Megtudhatja, hogyan [használhatja a fürt automéretezőjét egy csomópontos készleten](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled).

## <a name="delete-a-node-pool"></a>Csomópont-készlet törlése

Ha már nincs szüksége a készletre, törölheti, és eltávolíthatja a mögöttes virtuálisgép-csomópontokat. Egy csomópont-készlet törléséhez használja az az [AK Node Pool delete][az-aks-nodepool-delete] parancsot, és adja meg a csomópont-készlet nevét. A következő példa törli az előző lépésekben létrehozott *mynoodepool* :

> [!CAUTION]
> A csomópontok törlésekor előfordulhat, hogy az adatvesztéshez nincs helyreállítási lehetőség. Ha a hüvely nem ütemezhető más csomópont-készletekre, ezek az alkalmazások nem érhetők el. Győződjön meg arról, hogy nem törli a csomópont-készletet, ha a használatban lévő alkalmazásokban nincsenek biztonsági másolatok, vagy a fürt más csomópontjain is futtathatók.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

Az az [AK Node Pool List][az-aks-nodepool-list] parancs kimenete az alábbi példa alapján mutatja  , hogy a mynodepool *törlési* állapotban van:

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

A csomópontok és a csomópont-készlet törlése néhány percet vesz igénybe.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Virtuális gép méretének megadása egy csomópont-készlethez

Az előző példákban egy csomópont-készlet létrehozásához a fürtben létrehozott csomópontok alapértelmezett virtuálisgép-méretét használták. A leggyakoribb forgatókönyv az, hogy a különböző virtuálisgép-méretekkel és képességekkel rendelkező csomópont-készleteket hozzon létre. Létrehozhat például egy olyan csomópont-készletet, amely nagy mennyiségű PROCESSZORral vagy memóriával rendelkező csomópontokat tartalmaz, vagy egy olyan csomópont-készletet, amely GPU-támogatást biztosít. A következő lépésben a [szennyező és a tolerancia használatával](#setting-nodepool-taints) közli, hogy a Kubernetes ütemező hogyan korlátozza az ezeken a csomópontokon futtatható hüvelyek elérését.

Az alábbi példában hozzon létre egy GPU-alapú csomópont-készletet, amely a *Standard_NC6* VM-méretet használja. Ezeket a virtuális gépeket az NVIDIA Tesla K80 kártya működteti. A rendelkezésre álló virtuálisgép-méretekkel kapcsolatos információkért lásd: [a Linux rendszerű virtuális gépek méretei az Azure-ban][vm-sizes].

Hozzon létre egy csomópont-készletet az az [AK Node Pool Add][az-aks-nodepool-add] paranccsal. Ezúttal adja meg a *gpunodepool* nevet, és használja a `--node-vm-size` paramétert a *Standard_NC6* méretének megadásához:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

Az az [AK Node Pool List][az-aks-nodepool-list] parancs kimenete az alábbi példa alapján mutatja, hogy a *Gpunodepool* csomópontokat *hoz létre* a megadott *VmSize*:

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

A *gpunodepool* sikeres létrehozása néhány percet vesz igénybe.

## <a name="specify-a-taint-label-or-tag-for-a-node-pool"></a>Válassza ki a csomópont-készlethez tartozó Taint, címkét vagy címkét

### <a name="setting-nodepool-taints"></a>Nodepool-tainok beállítása

A csomópontok létrehozásakor megadhatja az adott csomópont-készlethez tartozó adatterületeket, címkéket vagy címkéket. Ha egy Taint, címkét vagy címkét ad hozzá, az adott csomóponton belüli összes csomópont a megromlást, címkét vagy címkét is megkapja.

Ha a csomópontot szennyező elemekkel szeretné létrehozni, használja [az az AK nodepool Add][az-aks-nodepool-add]. Adja meg a *taintnp* nevet, és a `--node-taints` paraméter használatával adja meg az *SKU = GPU:* a Taint a szennyező számára.

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
> A csomópont-készlet létrehozásakor a rendszer csak a csomópontok készletei számára állíthatja be a megromlást.

Az az [AK nodepool List][az-aks-nodepool-list] parancs kimenetének következő példája azt mutatja, hogy a *Taintnp* csomópontokat *hoz létre* a megadott *nodeTaints*:

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

A szennyező adatok láthatók a Kubernetes a csomópontok ütemezési szabályainak kezelésére. A Kubernetes-ütemező használatával megtilthatja, hogy milyen számítási feladatok futhatnak a csomópontokon.

* A rendszer egy olyan csomópontra alkalmazza a **megromlást** , amely azt jelzi, hogy csak bizonyos hüvelyek ütemezhetők.
* A **rendszer egy olyan** Pod-ra alkalmazza a betartást, amely lehetővé *teszi a* csomópontok megromlását.

További információ a speciális Kubernetes ütemezett funkcióinak használatáról: [ajánlott eljárások a speciális Scheduler-funkciókhoz az AK-ban][taints-tolerations]

Az előző lépésben alkalmazta a *SKU = GPU:* a nem ütemezett romlást a csomópont létrehozásakor. A következő alapszintű példa YAML a tolerancia használatával lehetővé teszi, hogy a Kubernetes-ütemező egy NGINX Pod-t futtasson egy csomóponton az adott csomóponton.

Hozzon létre egy nevű fájlt `nginx-toleration.yaml` , és másolja a következő példában szereplő YAML:

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

A pod a parancs használatával ütemezhet `kubectl apply -f nginx-toleration.yaml` :

```console
kubectl apply -f nginx-toleration.yaml
```

Eltarthat néhány másodpercig a pod beütemezett és az NGINX-rendszerkép lekéréséhez. A hüvely állapotának megtekintéséhez használja az [kubectl Leírás Pod][kubectl-describe] parancsot. A következő összehasonlított példa kimenete az *SKU = GPU:* a nem ütemezett tolerancia alkalmazását mutatja. Az Events (események) szakaszban az ütemező hozzárendelte a hüvelyt az *AK-taintnp-28993262-vmss000000* csomóponthoz:

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

A *taintnp* csomópontjain csak azok a hüvelyek ütemezhetők, amelyeken ez a tolerancia alkalmazva van. Minden egyéb Pod a *nodepool1* csomópont-készletben lesz ütemezve. Ha további csomópont-készleteket hoz létre, további megterhelések és a megromlások használatával korlátozhatja, hogy milyen hüvelyek ütemezhetők a csomópont-erőforrásokon.

### <a name="setting-nodepool-labels"></a>Nodepool-címkék beállítása

A csomópont-készlet létrehozása során címkéket is hozzáadhat egy csomópont-készlethez. A csomópont-készleten beállított címkéket a rendszer hozzáadja a csomópont-készlet minden egyes csomópontjára. Ezek a [címkék láthatók a Kubernetes][kubernetes-labels] a csomópontok ütemezési szabályainak kezelésére.

Ha címkével rendelkező csomópont-készletet szeretne létrehozni, használja [az az AK nodepool Add][az-aks-nodepool-add]. Adja meg a *labelnp* nevet, és a `--labels` paraméter használatával adja meg a *dept = it* és a *costcenter = 9999* címkét.

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
> A csomópont-készlet létrehozásakor csak a csomópont-készletek esetében lehet beállítani a címkét. A címkéknek is kulcs/érték pároknak kell lenniük, és [érvényes szintaxissal][kubernetes-label-syntax]kell rendelkezniük.

Az az [AK nodepool List][az-aks-nodepool-list] parancs kimenetének következő példája azt mutatja, hogy a *Labelnp* csomópontokat *hoz létre* a megadott *nodeLabels*:

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

Egy Azure-címkét alkalmazhat a Node-készletekre az AK-fürtben. A csomópont-készletre alkalmazott címkéket a rendszer a csomóponton belüli összes csomópontra alkalmazza, és a frissítésekkel megőrzi őket. A címkéket a rendszer a csomópont-készlethez hozzáadott új csomópontokra is alkalmazza a kibővíthető műveletek során. A címkék hozzáadásával olyan feladatok segíthetnek, mint például a szabályzatok nyomon követése vagy a költségbecslés.

Az Azure-címkék olyan kulcsokkal rendelkeznek, amelyek kis-és nagybetűket nem érintenek a műveletekhez, például ha egy címke beolvasása a kulcs alapján történik. Ebben az esetben a megadott kulccsal rendelkező címkét a rendszer a burkolattól függetlenül frissíti vagy lekéri. A címke értékei megkülönböztetik a kis-és nagybetűket.

Az AK-ban, ha több címke azonos kulcsokkal van beállítva, de eltérő a burkolat, a használt címke az első betűrendben szerepel. Például a `{"Key1": "val1", "kEy1": "val2", "key1": "val3"}` `Key1` és a beállítás eredménye `val1` .

Hozzon létre egy csomópont-készletet az az [AK nodepool Add][az-aks-nodepool-add]paranccsal. Adja meg a *tagnodepool* nevet, és a `--tag` paraméter használatával adja meg a *dept = it* és a *costcenter = 9999* címkét.

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
> Használhatja a `--tags` paramétert is, ha az [az AK nodepool Update][az-aks-nodepool-update] parancsot használja, valamint a fürt létrehozásakor. A fürt létrehozása során a `--tags` paraméter alkalmazza a címkét a fürttel létrehozott kezdeti csomópont-készletre. Az [Azure-erőforrások rendszerezéséhez][tag-limitation]az összes címke nevének be kell tartania a használati címkék korlátozásait. Ha a (z) paraméterrel frissíti egy csomópont-készletet, a `--tags` meglévő kódelemek összes értékének frissítése és az új címkék hozzáfűzése. Ha például a csomópont-készletben a *dept = it* és a *costcenter = 9999* szerepel a címkéknél, és frissítette a *Team = dev* és a *costcenter = 111* címkével, akkor a címkékhez nodepool a *dept = it*, a *costcenter = 111* és a *Team = dev* .

Az az [AK nodepool List][az-aks-nodepool-list] parancs kimenetének következő példája azt mutatja, hogy a *Tagnodepool* csomópontokat *hoz létre* a megadott *címkével*:

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

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Csomópont-készletek kezelése Resource Manager-sablonnal

Ha Azure Resource Manager sablont használ az erőforrások létrehozásához és kezeléséhez, általában frissíti a sablon beállításait, és újból üzembe helyezi az erőforrás frissítéséhez. Az AK-ban található Node-készletekkel a kezdeti csomópont-készlet profilja nem frissíthető az AK-fürt létrehozása után. Ez azt jelenti, hogy nem frissítheti a meglévő Resource Manager-sablonokat, módosíthatja a csomópont-készleteket, és újból üzembe helyezheti azokat. Ehelyett létre kell hoznia egy különálló Resource Manager-sablont, amely csak a meglévő AK-fürtökhöz tartozó csomópont-készleteket frissíti.

Hozzon létre egy sablont, például `aks-agentpools.json` illessze be a következő példát a jegyzékbe. A példában szereplő sablon a következő beállításokat konfigurálja:

* Frissíti a *myagentpool* nevű *Linux* Node-készletet három csomópont futtatásához.
* Beállítja a csomópont-készlet csomópontjait a Kubernetes-verzió *1.15.7* futtatásához.
* Meghatározza a csomópont méretét *Standard_DS2_v2ként*.

Szükség szerint szerkessze ezeket az értékeket a csomópont-készletek frissítéséhez, hozzáadásához vagy törléséhez:

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

Telepítse ezt a sablont az az [Deployment Group Create][az-deployment-group-create] paranccsal, az alábbi példában látható módon. A rendszer kéri a meglévő AK-fürt nevének és helyének megadását:

```azurecli-interactive
az deployment group create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

> [!TIP]
> A következő példában látható módon hozzáadhat egy címkét a csomópont-készlethez a sablon *címke* tulajdonságának hozzáadásával.
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

A Resource Manager-sablonban definiált csomópont-készlet beállításaitól és műveleteitől függően néhány percet is igénybe vehet az AK-fürt frissítése.

## <a name="assign-a-public-ip-per-node-for-your-node-pools-preview"></a>Nyilvános IP-cím társítása a csomópont-készletek számára (előzetes verzió)

> [!WARNING]
> A CLI előzetes verziójának 0.4.43 vagy újabb verzióját kell telepítenie a nyilvános IP-cím/csomópont szolgáltatás használatára.

Az AK-csomópontok nem igénylik a saját nyilvános IP-címeiket a kommunikációhoz. A forgatókönyvek azonban megkövetelhetik a csomópontok csomópontjait, hogy megkapják a saját dedikált nyilvános IP-címeiket. Gyakori forgatókönyv a játékok számítási feladataihoz, ahol a konzolnak közvetlen kapcsolatot kell létesítenie egy felhőalapú virtuális géppel a ugrások csökkentése érdekében. Ez a forgatókönyv egy előzetes verziójú funkció, a csomópont nyilvános IP-címe (előzetes verzió) regisztrálásával érhető el az AK-on.

A legújabb AK-előnézet bővítmény telepítéséhez és frissítéséhez használja az alábbi Azure CLI-parancsokat:

```azurecli
az extension add --name aks-preview
az extension update --name aks-preview
az extension list
```

Regisztráljon a Node Public IP szolgáltatásra a következő Azure CLI-paranccsal:

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```
A szolgáltatás regisztrálása több percet is igénybe vehet.  Az állapotot a következő paranccsal tekintheti meg:

```azurecli-interactive
 az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/NodePublicIPPreview')].{Name:name,State:properties.state}"
```

A sikeres regisztráció után hozzon létre egy új erőforráscsoportot.

```azurecli-interactive
az group create --name myResourceGroup2 --location eastus
```

Hozzon létre egy új AK-fürtöt, és csatoljon egy nyilvános IP-címet a csomópontjaihoz. A csomópont-készlet minden csomópontja egyedi nyilvános IP-címet kap. Ezt a virtuálisgép-méretezési csoport példányainak megtekintésével ellenőrizheti.

```azurecli-interactive
az aks create -g MyResourceGroup2 -n MyManagedCluster -l eastus  --enable-node-public-ip
```

Meglévő AK-fürtök esetében hozzáadhat egy új csomópont-készletet is, és csatlakoztathat egy nyilvános IP-címet a csomópontjaihoz.

```azurecli-interactive
az aks nodepool add -g MyResourceGroup2 --cluster-name MyManagedCluster -n nodepool2 --enable-node-public-ip
```

> [!Important]
> Az előzetes verzióban az Azure Instance Metadata Service jelenleg nem támogatja a nyilvános IP-címek lekérését a standard szintű VM SKU-hoz. Ennek a korlátozásnak köszönhetően a kubectl parancsok nem használhatók a csomópontokhoz rendelt nyilvános IP-címek megjelenítéséhez. Az IP-címek azonban hozzá vannak rendelve, és a kívánt módon működnek. A csomópontjaihoz tartozó nyilvános IP-címek a virtuálisgép-méretezési csoport példányaihoz vannak csatolva.

A csomópontok nyilvános IP-címeit többféleképpen is megtalálhatja:

* Az Azure CLI parancs használata az [vmss List-instance-Public-IPS][az-list-ips]
* Használjon [PowerShell-vagy bash-parancsokat][vmss-commands]. 
* A Azure Portal a nyilvános IP-címeket a virtuálisgép-méretezési csoport példányainak megtekintésével is megtekintheti.

> [!Important]
> A [csomópont-erőforráscsoport][node-resource-group] tartalmazza a csomópontokat és azok nyilvános IP-címeit. A csomópontok erőforráscsoporthoz a csomópontok nyilvános IP-címeinek megkereséséhez használja a parancsok végrehajtását.

```azurecli
az vmss list-instance-public-ips -g MC_MyResourceGroup2_MyManagedCluster_eastus -n YourVirtualMachineScaleSetName
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben a cikkben létrehozta a GPU-alapú csomópontokat tartalmazó AK-fürtöt. A szükségtelen díjak csökkentése érdekében érdemes törölni a *gpunodepool* vagy a teljes Kabai fürtöt.

A GPU-alapú csomópont-készlet törléséhez használja az az [AK nodepool delete][az-aks-nodepool-delete] parancsot az alábbi példában látható módon:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

A fürt törléséhez használja az az [Group delete][az-group-delete] parancsot az AK-erőforráscsoport törléséhez:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

A csomópont-készletek esetében a nyilvános IP-címhez létrehozott további fürtöt is törölheti.

```azurecli-interactive
az group delete --name myResourceGroup2 --yes --no-wait
```

## <a name="next-steps"></a>Következő lépések

További információ a [rendszercsomópont-készletekről][use-system-pool].

Ebben a cikkben megtanulta, hogyan hozhat létre és kezelhet több Node-készletet egy AK-fürtben. További információ a hüvelyek csomópontok közötti szabályozásáról: [ajánlott eljárások a speciális Scheduler-funkciókhoz az AK-ban][operator-best-practices-advanced-scheduler].

A Windows Server Container Node-készletek létrehozásához és használatához lásd: [Windows Server-tároló létrehozása az AK-ban][aks-windows].

A [közeli elhelyezési csoportok][reduce-latency-ppg] használatával csökkentheti az AK-alkalmazások késését.

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
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
[az-list-ips]: /cli/azure/vmss.md#az-vmss-list-instance-public-ips
[reduce-latency-ppg]: reduce-latency-ppg.md
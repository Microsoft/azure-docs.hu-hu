---
title: Dedikált készlet használata feladat futtatásához – Feladatok
description: Állítson be egy dedikált számítási készletet (ügynökkészletet) a beállításjegyzékben egy Azure Container Registry futtatásához.
ms.topic: article
ms.date: 10/12/2020
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 21db066b3f18106938d11fbd8e2cfe688c1ef276
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389553"
---
# <a name="run-an-acr-task-on-a-dedicated-agent-pool"></a>ACR-feladat futtatása dedikált ügynökkészleten

Állítson be egy Azureáltal felügyelt virtuálisgép-készletet [(ügynökkészletet),][acr-tasks] amely lehetővé teszi a Azure Container Registry feladatok dedikált számítási környezetben való futtatását. Miután konfigurált egy vagy több készletet a beállításjegyzékben, kiválaszthatja azt a készletet, amely a szolgáltatás alapértelmezett számítási környezete helyén futtat egy feladatot.

Az ügynökkészletek a következő eket biztosítják:

- **Virtuális hálózatok támogatása** – Ügynökkészlet hozzárendelése egy Azure-beli virtuális hálózathoz, amely hozzáférést biztosít a virtuális hálózat erőforrásaihoz, például egy tároló-beállításjegyzékhez, kulcstartóhoz vagy tárolóhoz.
- **Igény szerint skálázhat** – Növelheti az ügynökkészletben található példányok számát a nagy számítási igényű feladatokhoz, vagy nullára skálázhat. A számlázás a készlet lefoglalásán alapul. Részletekért lásd: [Díjszabás.](https://azure.microsoft.com/pricing/details/container-registry/)
- **Rugalmas lehetőségek** – A különböző [készletszintek](#pool-tiers) és skálázási lehetőségek közül választhat, hogy megfeleljen a feladat számítási feladatainak.
- **Azure-felügyelet** – A feladatkészleteket az Azure javítja és tartja karban, így fenntartott foglalást biztosít anélkül, hogy az egyes virtuális gépek karbantartására lenne szükség.

Ez a szolgáltatás a **Prémium** tároló-beállításjegyzék szolgáltatási szinten érhető el. További információ a beállításjegyzék szolgáltatási rétegeiről és korlátairól: Azure Container Registry [SKU-k.][acr-tiers]

> [!IMPORTANT]
> Ez a funkció jelenleg előzetes verzióban érhető el, és bizonyos [korlátozások érvényesek.](#preview-limitations) Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.
>

## <a name="preview-limitations"></a>Az előzetes verzió korlátozásai

- A feladatügynök-készletek jelenleg a Linux-csomópontokat támogatják. A Windows-csomópontok jelenleg nem támogatottak.
- A feladatügynök-készletek az alábbi régiókban érhetők el előzetes verzióban: USA 2. nyugati régiója, USA déli középső régiója, USA 2. keleti régiója, USA keleti régiója, USA középső régiója, Nyugat-Európa, Észak-Európa, Közép-Kanada, USGov Arizona, USGov Texas és USGov Virginia.
- Az egyes beállításjegyzékek esetében az alapértelmezett teljes vCPU- (mag-) kvóta 16 az összes standard ügynökkészlet esetében, és 0 az izolált ügynökkészletek esetében. Nyisson [meg egy támogatási kérést][open-support-ticket] a további lefoglaláshoz.
- Ügynökkészleten futó feladat jelenleg nem szakítható meg.

## <a name="prerequisites"></a>Előfeltételek

* A cikkben található Azure CLI-lépésekhez az Azure CLI 2.3.1-es vagy újabb verziójára van szükség. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli]. Vagy futtassa a [következőt a Azure Cloud Shell:](../cloud-shell/quickstart.md).
* Ha még nem rendelkezik tároló-beállításjegyzéktel, [][create-reg-cli] hozzon létre egyet (prémium szint szükséges) egy előzetes verziójú régióban.

## <a name="pool-tiers"></a>Készletszintek

Az ügynökkészletek rétegei a következő erőforrásokat biztosítják példányonként a készletben.

|Szint    | Típus  |  CPU  |Memória (GB)  |
|---------|---------|---------|---------|
|S1     |  Standard    | 2       |    3     |
|S2     |  Standard    | 4       |    8     |
|S3     |  Standard    | 8       |   16     |
|I6     |  Elszigetelt    | 64     |   216     |


## <a name="create-and-manage-a-task-agent-pool"></a>Feladatügynök-készlet létrehozása és kezelése

### <a name="set-default-registry-optional"></a>Alapértelmezett beállításjegyzék beállítása (nem kötelező)

A következő Azure CLI-parancsok egyszerűsítéséhez állítsa be az alapértelmezett beállításjegyzéket az [az configure parancs futtatásával:][az-configure]

```azurecli
az configure --defaults acr=<registryName>
```

Az alábbi példák feltételezik, hogy beállította az alapértelmezett beállításjegyzéket. Ha nem, minden parancsban `--registry <registryName>` át kell adni egy `az acr` paramétert.

### <a name="create-agent-pool"></a>Ügynökkészlet létrehozása

Hozzon létre egy ügynökkészletet [az az acr agentpool create paranccsal.][az-acr-agentpool-create] Az alábbi példa egy S2 szintű készletet (4 PROCESSZOR/példány) hoz létre. Alapértelmezés szerint a készlet 1 példányt tartalmaz.

```azurecli
az acr agentpool create \
    --name myagentpool \
    --tier S2
```

> [!NOTE]
> Az ügynökkészlet és más készletkezelési műveletek létrehozása több percet is igénybe vehet.

### <a name="scale-pool"></a>Készlet méretezése

Skálázja fel vagy le a készlet méretét [az az acr agentpool update paranccsal.][az-acr-agentpool-update] Az alábbi példa 2 példányra méretezi a készletet. 0 példányra skálázhat.

```azurecli
az acr agentpool update \
    --name myagentpool \
    --count 2
```

## <a name="create-pool-in-a-virtual-network"></a>Készlet létrehozása virtuális hálózaton

### <a name="add-firewall-rules"></a>Tűzfalszabályok hozzáadása

A feladatügynök-készleteknek hozzá kell férni a következő Azure-szolgáltatásokhoz. Az alábbi tűzfalszabályokat hozzá kell adni a meglévő hálózati biztonsági csoportokhoz vagy felhasználó által megadott útvonalakhoz.

| Irány | Protokoll | Forrás         | Forrásport | Cél          | Dest Port | Használt    |
|-----------|----------|----------------|-------------|----------------------|-----------|---------|
| Kimenő  | TCP      | VirtualNetwork | Bármelyik         | AzureKeyVault        | 443       | Alapértelmezett |
| Kimenő  | TCP      | VirtualNetwork | Bármelyik         | Tárolás              | 443       | Alapértelmezett |
| Kimenő  | TCP      | VirtualNetwork | Bármelyik         | EventHub             | 443       | Alapértelmezett |
| Kimenő  | TCP      | VirtualNetwork | Bármelyik         | AzureActiveDirectory | 443       | Alapértelmezett |
| Kimenő  | TCP      | VirtualNetwork | Bármelyik         | AzureMonitor         | 443       | Alapértelmezett |

> [!NOTE]
> Ha a feladatokhoz további, a nyilvános internetről származó erőforrásokra van szükség, adja hozzá a megfelelő szabályokat. Például további szabályokra van szükség egy docker buildfeladat futtatásához, amely lekérte az alapként Docker Hub, vagy visszaállít egy NuGet-csomagot.

### <a name="create-pool-in-vnet"></a>Készlet létrehozása virtuális hálózatban

A következő példa létrehoz egy ügynökkészletet a *myvnet hálózat mysubnet* *alhálózatában:*

```azurecli
# Get the subnet ID
subnetId=$(az network vnet subnet show \
        --resource-grop myresourcegroup \
        --vnet-name myvnet \
        --name mysubnetname \
        --query id --output tsv)

az acr agentpool create \
    --name myagentpool \
    --tier S2 \
    --subnet-id $subnetId
```

## <a name="run-task-on-agent-pool"></a>Feladat futtatása az ügynökkészleten

Az alábbi példák bemutatják, hogyan adható meg egy ügynökkészlet egy feladat várólistára való végrehajtásához.

> [!NOTE]
> Ha egy ACR-feladatban ügynökkészletet használ, győződjön meg arról, hogy a készlet legalább 1 példányt tartalmaz.
>

### <a name="quick-task"></a>Gyors feladat

Az az [acr build][az-acr-build] paranccsal várakozási sorba kell ásni egy gyors feladatot az ügynökkészleten, és át kell adni a következő `--agent-pool` paramétert:

```azurecli
az acr build \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --file Dockerfile \
    https://github.com/Azure-Samples/acr-build-helloworld-node.git#main
```

### <a name="automatically-triggered-task"></a>Automatikusan aktivált feladat

Például hozzon létre egy ütemezett feladatot az ügynökkészleten [az az acr task create paraméter][az-acr-task-create] `--agent-pool` átadásával.

```azurecli
az acr task create \
    --name mytask \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --schedule "0 21 * * *" \
    --file Dockerfile \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --commit-trigger-enabled false
```

A feladat beállításának ellenőrzéséhez futtassa [az az acr task run futtatását:][az-acr-task-run]

```azurecli
az acr task run \
    --name mytask
```

### <a name="query-pool-status"></a>Lekérdezéskészlet állapota

Az ügynökkészleten jelenleg ütemezett futtatásszámok számának megkeresését az [az acr agentpool show futtatásával találhatja meg.][az-acr-agentpool-show]

```azurecli
az acr agentpool show \
    --name myagentpool \
    --queue-count
```

## <a name="next-steps"></a>Következő lépések

A tároló rendszerkép-buildjére és a felhőben való karbantartásra vonatkozó további példákért tekintse meg a ACR-feladatok [oktatóanyag-sorozatot.](container-registry-tutorial-quick-task.md)



[acr-tasks]:           container-registry-tasks-overview.md
[acr-tiers]:           container-registry-skus.md
[azure-cli]:           /cli/azure/install-azure-cli
[open-support-ticket]: https://aka.ms/acr/support/create-ticket
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[az-configure]: /cli/azure#az-configure
[az-acr-agentpool-create]: /cli/azure/acr/agentpool#az-acr-agentpool-create
[az-acr-agentpool-update]: /cli/azure/acr/agentpool#az-acr-agentpool-update
[az-acr-agentpool-show]: /cli/azure/acr/agentpool#az-acr-agentpool-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[create-reg-cli]: container-registry-get-started-azure-cli.md

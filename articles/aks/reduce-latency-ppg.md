---
title: Közelségi elhelyezési csoportok használata a Azure Kubernetes Service (AKS-) fürtök késésének csökkentéséhez
description: Ismerje meg, hogyan használhatja a közelségi elhelyezési csoportokat az AKS-fürt számítási feladatai késésének csökkentésére.
services: container-service
manager: gwallace
ms.topic: article
ms.date: 10/19/2020
ms.openlocfilehash: fbcff37185b2cba71c405e917653d52397479007
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779596"
---
# <a name="reduce-latency-with-proximity-placement-groups"></a>Késés csökkentése közelségi elhelyezési csoportokkal

> [!Note]
> Ha közelségi elhelyezési csoportokat használ az AKS-hez, a közös elhelyezés csak az ügynökcsomópontokra vonatkozik. A csomópontok és a podok közötti megfelelő üzemeltetett podok késése javul. A közös elhelyezés nincs hatással a fürt vezérlősíkján való elhelyezésre.

Az alkalmazás Azure-ban való üzembe helyezésekor a virtuálisgép-példányok régiók vagy rendelkezésre állási zónák közötti terjesztése hálózati késést eredményez, ami hatással lehet az alkalmazás általános teljesítményére. A közelségi elhelyezési csoport olyan logikai csoportosítás, amely az Azure-beli számítási erőforrások fizikai közelében helyezkedik el. Egyes alkalmazások, például a játékok, a mérnöki szimulációk és a nagy gyakoriságú kereskedelem (HFT) kis késést és gyorsan befejeződő feladatokat igényelnek. Az ilyen nagy teljesítményű számítási (HPC-) forgatókönyvek [](../virtual-machines/co-location.md#proximity-placement-groups) esetében érdemes lehet közelségi elhelyezési csoportokat (PPG-ket) használni a fürt csomópontkészleteihez.

## <a name="before-you-begin"></a>Előkészületek

Ehhez a cikkhez az Azure CLI 2.14-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

### <a name="limitations"></a>Korlátozások

* Egy közelségi elhelyezési csoport leképezhet legalább egy rendelkezésre állási zónára.
* A csomópontkészletnek a Virtual Machine Scale Sets kell használnia a közelségi elhelyezési csoport társításhoz.
* A csomópontkészletek közelségi elhelyezési csoportot társíthatnak csak a csomópontkészlet létrehozási idejekor.

## <a name="node-pools-and-proximity-placement-groups"></a>Csomópontkészletek és közelségi elhelyezési csoportok

A közelségi elhelyezési csoporttal üzembe helyezett első erőforrás egy adott adatközponthoz van csatolva. Az azonos közelségi elhelyezési csoporttal üzembe helyezett további erőforrások ugyanabban az adatközpontban vannak elosztva. Miután a közelségi elhelyezési csoportot használó összes erőforrás le lett állítva (felszabadítva) vagy törölve lett, az már nem lesz csatolva.

* Számos csomópontkészlet társítható egyetlen közelségi elhelyezési csoporthoz.
* A csomópontkészletek csak egy közelségi elhelyezési csoporthoz társíthatóak.

### <a name="configure-proximity-placement-groups-with-availability-zones"></a>Közelségi elhelyezési csoportok konfigurálása rendelkezésre állási zónákkal

> [!NOTE]
> Bár a közelségi elhelyezési csoportokhoz egy csomópontkészletnek legalább egy rendelkezésre állási zónát kell használnia, az Azure-beli virtuális gépek [99,9%-os](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) SLA-jának alapszintje továbbra is érvényben van az egy zónában található virtuális gépek esetében.

A közelségi elhelyezési csoportok olyan csomópontkészlet-fogalmak, amelyek minden egyes csomópontkészlethez társítva vannak. A PPG-erőforrások használata nincs hatással az AKS-vezérlősík elérhetőségére. Ez befolyásolhatja a fürt zónákkal való tervezését. A fürt több zónában való elosztásának biztosítása érdekében a következő kialakítás ajánlott.

* Fürt kiépítése az első rendszerkészlethez 3 zóna használatával, és nincs hozzárendelve közelségi elhelyezési csoport. Ez biztosítja, hogy a rendszerpodok egy dedikált csomópontkészletbe, amely több zónában fog elterjedni.
* Adjon hozzá további felhasználói csomópontkészleteket, amelyek mindegyik készlethez egyedi zónával és közelségi elhelyezési csoporttal vannak társítva. Erre példa a nodepool1 az 1. zónában és a PPG1, a nodepool2 a 2. zónában és a PPG2, a nodepool3 a 3. zónában a PPG3-val. Ez biztosítja, hogy a fürtök szintjén a csomópontok több zónában elosztásra, és minden egyes csomópontkészlet a kijelölt zónában van elosztva egy dedikált PPG-erőforrással.

## <a name="create-a-new-aks-cluster-with-a-proximity-placement-group"></a>Új AKS-fürt létrehozása közelségi elhelyezési csoporttal

Az alábbi példa az [az group create paranccsal][az-group-create] hoz létre egy *myResourceGroup* nevű erőforráscsoportot a *központi régióban.* Ezután létrejön egy *myAKSCluster* nevű AKS-fürt az [az aks create paranccsal.][az-aks-create]

A gyorsított hálózattal nagy mértékben javítható a virtuális gépek hálózati teljesítménye. Ideális esetben használjon közelségi elhelyezési csoportokat a gyorsított hálózattal együtt. Alapértelmezés szerint az AKS gyorsított [](../virtual-network/create-vm-accelerated-networking-cli.md?toc=/azure/virtual-machines/linux/toc.json#limitations-and-constraints)hálózatépítést használ a támogatott virtuálisgép-példányok esetében, amelyek a legtöbb Azure-beli virtuális gépet két vagy több vCPU-val tartalmazzák.

Hozzon létre egy új AKS-fürtöt az első rendszercsomópont-készlethez társított közelségi elhelyezési csoporttal:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```
Futtassa a következő parancsot, és tárolja a visszaadott azonosítót:

```azurecli-interactive
# Create proximity placement group
az ppg create -n myPPG -g myResourceGroup -l centralus -t standard
```

A parancs kimenetet hoz  létre, amely tartalmazza a következő CLI-parancsokhoz szükséges azonosítóértéket:

```output
{
  "availabilitySets": null,
  "colocationStatus": null,
  "id": "/subscriptions/yourSubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Compute/proximityPlacementGroups/myPPG",
  "location": "centralus",
  "name": "myPPG",
  "proximityPlacementGroupType": "Standard",
  "resourceGroup": "myResourceGroup",
  "tags": {},
  "type": "Microsoft.Compute/proximityPlacementGroups",
  "virtualMachineScaleSets": null,
  "virtualMachines": null
}
```

Használja a közelségi elhelyezési csoport erőforrás-azonosítóját a *myPPGResourceID* értékhez az alábbi parancsban:

```azurecli-interactive
# Create an AKS cluster that uses a proximity placement group for the initial system node pool only. The PPG has no effect on the cluster control plane.
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --ppg myPPGResourceID
```

## <a name="add-a-proximity-placement-group-to-an-existing-cluster"></a>Közelségi elhelyezési csoport hozzáadása meglévő fürthöz

Közelségi elhelyezési csoportot egy új csomópontkészlet létrehozásával adhat hozzá egy meglévő fürthöz. A meglévő számítási feladatokat igény szerint át is minálhatja az új csomópontkészletbe, majd törölheti az eredeti csomópontkészletet.

Használja a korábban létrehozott közelségi elhelyezési csoportot, és ezzel biztosíthatja, hogy az AKS-fürt mindkét csomópontkészletének ügynökcsomópontjai fizikailag ugyanabban az adatközpontban helyezkednek el.

Használja a korábban létrehozott közelségi elhelyezési csoport erőforrás-azonosítóját, és adjon hozzá egy új csomópontkészletet az [`az aks nodepool add`][az-aks-nodepool-add] paranccsal:

```azurecli-interactive
# Add a new node pool that uses a proximity placement group, use a --node-count = 1 for testing
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 1 \
    --ppg myPPGResourceID
```

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A fürt törléséhez használja az parancsot az [`az group delete`][az-group-delete] AKS-erőforráscsoport törléséhez:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Következő lépések

* További információ a [közelségi elhelyezési csoportokról.][proximity-placement-groups]

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az_aks_get_upgrades
[az-aks-upgrade]: /cli/azure/aks#az_aks_upgrade
[az-aks-show]: /cli/azure/aks#az_aks_show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[proximity-placement-groups]: ../virtual-machines/co-location.md#proximity-placement-groups
[az-aks-create]: /cli/azure/aks#az_aks_create
[system-pool]: ./use-system-pools.md
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az_aks_nodepool_add
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
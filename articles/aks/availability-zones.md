---
title: Rendelkezésre állási zónák használata Azure Kubernetes Service (AKS)
description: Megtudhatja, hogyan hozhat létre olyan fürtöt, amely csomópontokat oszt el a rendelkezésre állási zónák között Azure Kubernetes Service (AKS)
services: container-service
ms.custom: fasttrack-edit, references_regions, devx-track-azurecli
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: 796cb0e2b76dc2a04834df61c053c5ad2ceb25fd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769624"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Rendelkezésre állási zónákat használó Azure Kubernetes Service (AKS-) fürt létrehozása

A Azure Kubernetes Service (AKS-) fürt elosztja az erőforrásokat, például a csomópontokat és a tárterületet a mögöttes Azure-infrastruktúra logikai szakaszai között. Ez az üzembe helyezési modell a rendelkezésre állási zónák használata esetén biztosítja, hogy az adott rendelkezésre állási zónában lévő csomópontok fizikailag elkülönülnek a másik rendelkezésre állási zónában definiáltaktól. A fürtökön több rendelkezésre állási zónával üzembe helyezett AKS-fürtök magasabb rendelkezésre állást biztosítanak a hardverhibák vagy tervezett karbantartási események elleni védelem érdekében.

Ha több zónára is kiható csomópontkészleteket definiál egy fürtben, egy adott csomópontkészlet csomópontjai akkor is képesek folytatni az üzemeltetést, ha egyetlen zóna leállt. Az alkalmazások akkor is továbbra is elérhetők maradnak, ha egyetlen adatközpontban fizikai hiba történik, ha a vezénylés a csomópontok egy részkészletének meghibásodását tűri.

Ez a cikk bemutatja, hogyan hozhat létre egy AKS-fürtöt, és hogyan terjesztheti a csomópont-összetevőket a rendelkezésre állási zónák között.

## <a name="before-you-begin"></a>Előkészületek

Az Azure CLI 2.0.76-os vagy újabb verzióját kell telepítenie és konfigurálnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="limitations-and-region-availability"></a>Korlátozások és a régiók rendelkezésre állása

Az AKS-fürtök jelenleg a következő régiókban, rendelkezésre állási zónák használatával is létre lehet hozva:

* Kelet-Ausztrália
* Dél-Brazília
* Közép-Kanada
* USA középső régiója
* USA keleti régiója 
* USA 2. keleti régiója
* Közép-Franciaország
* Nyugat-Németország – Középső régió
* Kelet-Japán
* Észak-Európa
* Délkelet-Ázsia
* USA déli középső régiója
* Az Egyesült Királyság déli régiója
* USA-beli államigazgatás – Virginia
* Nyugat-Európa
* USA 2. nyugati régiója

Az AKS-fürtök rendelkezésre állási zónákkal való létrehozásakor a következő korlátozások érvényesek:

* Rendelkezésre állási zónákat csak a fürt vagy a csomópontkészlet létrehozásakor határozhat meg.
* A rendelkezésre állási zóna beállításai nem frissíthetők a fürt létrehozása után. Meglévő, nem rendelkezésre állási zónafürtöt sem frissíthet a rendelkezésre állási zónák használatára.
* A kiválasztott csomópontméretnek (VM termékváltozat) elérhetőnek kell lennie az összes kiválasztott rendelkezésre állási zónában.
* Az engedélyezett rendelkezésre állási zónákkal rendelkező fürtök esetében az Azure Standard Load Balancer használata szükséges a zónák közötti elosztáshoz. Ez a terheléselelosztási típus csak a fürt létrehozásakor határozható meg. További információkért és a standard terheléselosztás korlátaiért lásd: [Az Azure Load Balancer standard termékváltozatának korlátozásai.][standard-lb-limitations]

### <a name="azure-disks-limitations"></a>Az Azure-lemezek korlátozásai

Az Azure-beli felügyelt lemezeket használó kötetek jelenleg nem zónaredundáns erőforrások. A kötetek nem csatolhatóak zónák között, és ugyanabban a zónában kell elhelyezniük, mint a célpodot üzemeltető csomópontnak.

A Kubernetes az 1.12-es verzió óta ismeri az Azure rendelkezésre állási zónákat. Üzembe helyezhet egy Többzónás AKS-fürtben lévő Azure Managed Diskre hivatkozó PersistentVolumeClaim objektumot, és a [Kubernetes](https://kubernetes.io/docs/setup/best-practices/multiple-zones/#storage-access-for-zones) gondoskodik az olyan podok ütemezéséről, amelyek a megfelelő rendelkezésre állási zónában igénylést küldik a MEGFELELŐ RENDELKEZÉSRE állási zónába.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Az AKS-fürtök rendelkezésre állási zónáinak áttekintése

A rendelkezésre állási zónák magas rendelkezésre állású ajánlatok, amelyek megvédik az alkalmazásokat és az adatokat az adatközpontok meghibásodásaitól. A zónák egyedi fizikai helyek egy Azure-régión belül. Minden rendelkezésreállási zóna egy vagy több, független áramforrással, hűtéssel és hálózatkezelési megoldással ellátott adatközpontból áll. A rugalmasság biztosítása érdekében minden zóna kompatibilis régiójában mindig több zóna található. A rendelkezésreállási zónák régión belüli fizikai elkülönítése védelmet biztosít az alkalmazások és az adatok számára az adatközpont meghibásodása esetén.

További információ: Mik azok a rendelkezésre [állási zónák az Azure-ban?][az-overview].

A rendelkezésre állási zónákkal üzembe helyezett AKS-fürtök egyetlen régión belül több zónában is eloszthatja a csomópontokat. Például az  *USA 2.* keleti régiójában lévő fürtök létrehozhatnak csomópontokat mindhárom rendelkezésre állási zónában az   USA 2. keleti *régiójában.* Az AKS-fürterőforrások ezen elosztása javítja a fürtök rendelkezésre állását, mivel rugalmasan ellenállnak egy adott zóna meghibásodásának.

![AKS-csomópontok elosztása rendelkezésre állási zónák között](media/availability-zones/aks-availability-zones.png)

Ha egy zóna elérhetetlenné válik, az alkalmazások továbbra is futnak, ha a fürt több zónában van elterjedve.

## <a name="create-an-aks-cluster-across-availability-zones"></a>AKS-fürt létrehozása rendelkezésre állási zónák között

Amikor az [az aks create][az-aks-create] paranccsal hoz létre egy fürtöt, a paraméter határozza meg, hogy mely zónák ügynökcsomópontjai `--zones` vannak üzembe adva. A vezérlősík összetevői, például az etcd vagy az API a régióban rendelkezésre álló zónák között érhetők el, ha a paramétert a fürt `--zones` létrehozásakor határozza meg. A vezérlősík összetevői között elterjedő zónák függetlenek a kezdeti csomópontkészlethez kiválasztott explicit zónáktól.

Ha az AKS-fürt létrehozásakor nem határoz meg zónákat az alapértelmezett ügynökkészlethez, a vezérlősík összetevői nem határozzák meg garantáltan a rendelkezésre állási zónák közötti elosztást. Az [az aks nodepool add][az-aks-nodepool-add] paranccsal további csomópontkészleteket adhat hozzá, és megadhatja az új csomópontokat, de ez nem módosítja a vezérlősík zónák közötti `--zones` terjesztését. A rendelkezésre állási zóna beállításai csak a fürtök vagy csomópontkészletek létrehozásakor határozhatóak meg.

A következő példa létrehoz egy *myAKSCluster* nevű AKS-fürtöt a *myResourceGroup nevű erőforráscsoportban.* Összesen *3* csomópont jön létre – egy ügynök az *1.* zónában, egy *a 2-ben,* egy pedig a *3-ban.*

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --generate-ssh-keys \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --node-count 3 \
    --zones 1 2 3
```

Az AKS-fürt létrehozása eltarthat néhány percig.

Amikor eldönti, hogy egy új csomópont melyik zónához tartozna, egy adott AKS-csomópontkészlet a mögöttes [Azure-csomópontok][vmss-zone-balancing]által kínált legjobb Virtual Machine Scale Sets. Egy adott AKS-csomópontkészlet akkor minősül "kiegyensúlyozottnak", ha minden zónában azonos számú virtuális gép vagy + 1 virtuális gép található a méretezési csoport összes többi \- zónájában.

## <a name="verify-node-distribution-across-zones"></a>Csomópontok zónák közötti elosztásának ellenőrzése

Ha a fürt elkészült, listából láthatja, hogy melyik rendelkezésre állási zónában vannak üzembe állítva a méretezési csoportban lévő ügynökcsomópontok.

Először szerezze be az AKS-fürt hitelesítő adatait [az az aks get-credentials paranccsal:][az-aks-get-credentials]

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Ezután használja a [kubectl describe][kubectl-describe] parancsot a fürt csomópontjainak listához, és szűrje a failure-domain.beta.kubernetes.io/zone *értékét.* Az alábbi példa egy Bash-rendszerhéjra mutat be.

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

Az alábbi példakimenet a megadott régió és rendelkezésre állási zónák között elosztott három csomópontot mutatja be, például *eastus2-1* az első rendelkezésre állási zónában, az *eastus2-2* csomópontot pedig a második rendelkezésre állási zónában:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

Amikor további csomópontokat ad hozzá egy ügynökkészlethez, az Azure platform automatikusan elosztja a mögöttes virtuális gépeket a megadott rendelkezésre állási zónák között.

Vegye figyelembe, hogy az újabb Kubernetes-verziókban (1.17.0-s és újabb verziókban) az AKS az elavult mellett az újabb címkét `topology.kubernetes.io/zone` `failure-domain.beta.kubernetes.io/zone` használja. A fentivel azonos eredményt kaphat a következő szkript futtatásával:

```console
kubectl get nodes -o custom-columns=NAME:'{.metadata.name}',REGION:'{.metadata.labels.topology\.kubernetes\.io/region}',ZONE:'{metadata.labels.topology\.kubernetes\.io/zone}'
```

Ez tömörebb kimenetet ad:

```console
NAME                                REGION   ZONE
aks-nodepool1-34917322-vmss000000   eastus   eastus-1
aks-nodepool1-34917322-vmss000001   eastus   eastus-2
aks-nodepool1-34917322-vmss000002   eastus   eastus-3
```

## <a name="verify-pod-distribution-across-zones"></a>Podok zónák közötti elosztásának ellenőrzése

A [Well-Known Labels, Annotations and Taints][kubectl-well_known_labels]című dokumentumban dokumentált módon a Kubernetes a címkével automatikusan elosztja a podokat egy replikációs vezérlőben vagy szolgáltatásban az elérhető különböző zónák `failure-domain.beta.kubernetes.io/zone` között. Ennek teszteléséhez 3 csomópontról 5 csomópontra skálázhatja fel a fürtöt a podok megfelelő elosztásának ellenőrzéséhez:

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

Ha a skálázandó művelet néhány perc után befejeződik, a Bash-rendszerhéj parancsának a következő mintához hasonló kimenetet `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"` kell visszaadni:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
Name:       aks-nodepool1-28993262-vmss000003
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000004
            failure-domain.beta.kubernetes.io/zone=eastus2-2
```

Most már két további csomópontunk van az 1. és a 2. zónában. Üzembe helyezhet egy három replikából álló alkalmazást. Példaként az NGINX-et fogjuk használni:

```console
kubectl create deployment nginx --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
kubectl scale deployment nginx --replicas=3
```

A podokat futtató csomópontok megtekintésével láthatja, hogy a podok három különböző rendelkezésre állási zónának megfelelő csomópontokon futnak. Ha például a parancsot `kubectl describe pod | grep -e "^Name:" -e "^Node:"` egy Bash-rendszerhéjban van, az alábbihoz hasonló kimenetet kap:

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

Ahogy az előző kimenetből látható, az első pod a 0. csomóponton fut, amely a rendelkezésre állási zónában `eastus2-1` található. A második pod a 2. csomóponton fut, amely a következőnek felel meg: , a harmadik pedig a `eastus2-3` 4. csomópontban, a `eastus2-2` -ben. További konfiguráció nélkül a Kubernetes megfelelően elterjed a podokat mindhárom rendelkezésre állási zónában.

## <a name="next-steps"></a>Következő lépések

Ez a cikk részletesen bemutatja, hogyan hozhat létre rendelkezésre állási zónákat használó AKS-fürtöt. A magas rendelkezésre álló fürtökre vonatkozó további szempontokért tekintse meg az AKS üzletmenet-folytonossági és [vészhelyreállítási ajánlott eljárásait.][best-practices-bc-dr]

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-overview]: ../availability-zones/az-overview.md
[best-practices-bc-dr]: operator-best-practices-multi-region.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[standard-lb-limitations]: load-balancer-standard.md#limitations
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[vmss-zone-balancing]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-well_known_labels]: https://kubernetes.io/docs/reference/labels-annotations-taints/

---
title: Rendelkezésre állási zónák használata az Azure Kubernetes szolgáltatásban (ak)
description: Megtudhatja, hogyan hozhat létre olyan fürtöt, amely a csomópontokat a rendelkezésre állási zónák között osztja el az Azure Kubernetes szolgáltatásban (ak)
services: container-service
ms.custom: fasttrack-edit, references_regions, devx-track-azurecli
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: 4c5b0ceb3f8e0b96f18a67ed0c7dbf1b56ac30da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104583547"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Rendelkezésre állási zónákat használó Azure Kubernetes-szolgáltatásbeli (ak-) fürt létrehozása

Az Azure Kubernetes-szolgáltatás (ak) fürtje a mögöttes Azure-infrastruktúra logikai részein keresztül osztja el az erőforrásokat, például a csomópontokat és a tárolókat. Ez a telepítési modell rendelkezésre állási zónák használata esetén biztosítja, hogy az adott rendelkezésre állási zónában lévő csomópontok fizikailag el legyenek különítve a másik rendelkezésre állási zónában definiált A fürtön keresztül konfigurált több rendelkezésre állási zónával üzembe helyezett AK-fürtök magasabb szintű rendelkezésre állást biztosítanak a hardverhiba vagy a tervezett karbantartási események elleni védelemhez.

Ha a fürtben több zónára kiterjedő csomópont-készleteket határoz meg, az adott csomópont csomópontjain továbbra is folytatódhat a működés, még akkor is, ha egyetlen zóna leállt. Az alkalmazások továbbra is elérhetők maradnak akkor is, ha egy adott adatközpontban fizikai hiba történik, ha a rendszer a csomópontok egy részhalmazának meghibásodását szeretné elviselni.

Ez a cikk bemutatja, hogyan hozhat létre egy AK-fürtöt, és hogyan oszthatja szét a csomópont-összetevőket a rendelkezésre állási zónák között.

## <a name="before-you-begin"></a>Előkészületek

Szüksége lesz az Azure CLI-verzió 2.0.76 vagy újabb verziójára, és konfigurálva van. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="limitations-and-region-availability"></a>Korlátozások és régiók rendelkezésre állása

Az AK-fürtök jelenleg rendelkezésre állási zónák használatával hozhatók létre a következő régiókban:

* Kelet-Ausztrália
* Dél-Brazília
* Közép-Kanada
* USA középső régiója
* USA keleti régiója 
* USA 2. keleti régiója
* Közép-Franciaország
* Középnyugat-Németország
* Kelet-Japán
* Észak-Európa
* Délkelet-Ázsia
* USA déli középső régiója
* Az Egyesült Királyság déli régiója
* USA-beli államigazgatás – Virginia
* Nyugat-Európa
* USA 2. nyugati régiója

A következő korlátozások érvényesek az AK-fürtök rendelkezésre állási zónák használatával történő létrehozásakor:

* A rendelkezésre állási zónák csak a fürt vagy a csomópont-készlet létrehozásakor adhatók meg.
* A rendelkezésre állási zóna beállításai nem frissíthetők a fürt létrehozása után. A rendelkezésre állási zónák használatához nem lehet frissíteni egy meglévő, nem rendelkezésre állási zóna fürtöt is.
* A kiválasztott csomópont-méretnek (VM SKU) elérhetőnek kell lennie az összes kiválasztott rendelkezésre állási zónában.
* A rendelkezésre állási zónákat engedélyező fürtök esetében az Azure standard Load Balancer használata szükséges a zónák közötti elosztáshoz. Ezt a terheléselosztó-típust csak a fürt létrehozási idején lehet definiálni. További információ és a standard Load Balancer korlátai: az [Azure Load Balancer standard SKU-korlátai][standard-lb-limitations].

### <a name="azure-disks-limitations"></a>Azure-lemezek korlátozásai

Az Azure Managed Disks-t használó kötetek jelenleg nem redundáns erőforrások. A kötetek nem csatlakoztathatók több zónához, és ugyanabban a zónában kell lenniük, mint a cél pod-t üzemeltető adott csomópont.

A Kubernetes a 1,12-es verzió óta ismeri az Azure rendelkezésre állási zónáit. Telepíthet olyan PersistentVolumeClaim objektumot, amely egy Azure felügyelt lemezre hivatkozik egy többzónás AK-fürtben, és a Kubernetes gondoskodik a megfelelő rendelkezésre állási zónában a PVC-t igénylő Pod [ütemezéséről](https://kubernetes.io/docs/setup/best-practices/multiple-zones/#storage-access-for-zones) .

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Az AK-fürtök rendelkezésre állási zónáinak áttekintése

A rendelkezésre állási zónák olyan magas rendelkezésre állású ajánlat, amely védelmet nyújt alkalmazásai és adatai számára az adatközpont hibáiból. A zónák egy Azure-régióban található egyedi fizikai helyszínek. Minden rendelkezésreállási zóna egy vagy több, független áramforrással, hűtéssel és hálózatkezelési megoldással ellátott adatközpontból áll. A rugalmasság biztosítása érdekében mindig több zóna van az összes zónában engedélyezett régióban. A rendelkezésreállási zónák régión belüli fizikai elkülönítése védelmet biztosít az alkalmazások és az adatok számára az adatközpont meghibásodása esetén.

További információ: [Mik a rendelkezésre állási zónák az Azure-ban?][az-overview].

A rendelkezésre állási zónák használatával üzembe helyezett AK-fürtök több, egyetlen régióban található zónában terjeszthetik a csomópontokat. Az  *USA 2. keleti* régiójában található fürt például az   *USA 2. keleti* régiójában mindhárom rendelkezésre állási zónában hozhat létre csomópontokat. Az AK-beli fürt erőforrásainak ezen eloszlása javítja a fürt rendelkezésre állását, mivel azok egy adott zóna meghibásodása esetén rugalmasak.

![AK-csomópont eloszlása a rendelkezésre állási zónák között](media/availability-zones/aks-availability-zones.png)

Ha egyetlen zóna elérhetetlenné válik, az alkalmazások továbbra is futnak, ha a fürt több zónában van elosztva.

## <a name="create-an-aks-cluster-across-availability-zones"></a>AK-fürt létrehozása rendelkezésre állási zónák között

Amikor az az [AK Create][az-aks-create] paranccsal hoz létre fürtöt, a paraméter határozza meg, hogy a rendszer `--zones` mely zónákat telepíti a-ben. A vezérlő sík összetevői, például a etcd vagy az API a régió elérhető zónái között oszlanak meg, ha a `--zones` paramétert fürt létrehozási időpontjában határozzák meg. Azok a zónák, amelyeknek a vezérlési sík összetevői, egymástól függetlenek, függetlenül attól, hogy milyen explicit zónák vannak kiválasztva a kezdeti csomópont-készlethez.

Ha nem ad meg zónát az alapértelmezett ügynök készletéhez, amikor egy AK-fürtöt hoz létre, a vezérlési sík összetevői nem garantáltak a rendelkezésre állási zónák között. További csomópont-készleteket az [az AK nodepool Add][az-aks-nodepool-add] paranccsal adhat hozzá, és megadhatja `--zones` az új csomópontokat, de a vezérlő síkja nem változik meg a zónák közötti eloszlásban. A rendelkezésre állási zóna beállításai csak a fürt vagy a csomópont-készlet létrehozási ideje alatt definiálhatók.

A következő példában létrehozunk egy *myAKSCluster* nevű AK-fürtöt az *myResourceGroup* nevű erőforráscsoport-csoportban. Összesen *3* csomópont jön létre – egy ügynök az *1*. zónában, egyet *2*-ban, majd egyet *3*-ban.

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

Annak eldöntése során, hogy az új csomópont melyik zónába tartozik, az adott AK-beli csomópont-készlet az [alapul szolgáló Azure-Virtual Machine Scale sets által kínált legjobb erőkifejtési zónát][vmss-zone-balancing]fogja használni. Egy adott AK-beli csomópont-készlet "kiegyensúlyozottnak" minősül, ha minden zónában azonos számú virtuális gép vagy + \- 1 virtuális gép található a méretezési csoport minden más zónájában.

## <a name="verify-node-distribution-across-zones"></a>Csomópontok eloszlásának ellenőrzése a zónák között

Ha a fürt elkészült, sorolja fel a méretezési csoport ügynök-csomópontjait, hogy megtekintse, milyen rendelkezésre állási zónákat telepítenek a rendszerbe.

Először szerezze be az AK-fürt hitelesítő adatait az az az [AK Get-hitelesítőadats][az-aks-get-credentials] paranccsal:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Ezután a [kubectl Leírás][kubectl-describe] parancs használatával sorolja fel a fürt csomópontjait, és szűrje a *failure-domain.Beta.kubernetes.IO/Zone* értéket. A következő példa egy bash-rendszerhéjra mutat.

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

A következő példa kimenete a megadott régió és rendelkezésre állási zónák között elosztott három csomópontot mutatja be, például a *eastus2-1* értéket az első rendelkezésre állási zónához, és a *eastus2-2* a második rendelkezésre állási zónához:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

Ha további csomópontokat ad hozzá egy ügynök-készlethez, az Azure platform automatikusan elosztja a mögöttes virtuális gépeket a megadott rendelkezésre állási zónák között.

Vegye figyelembe, hogy az újabb verziókban (1.17.0 és újabb verziók) az Kubernetes az újabb címkét használja az `topology.kubernetes.io/zone` elavult érték mellett `failure-domain.beta.kubernetes.io/zone` . A következő szkript futtatásával ugyanaz az eredmény adható meg:

```console
kubectl get nodes -o custom-columns=NAME:'{.metadata.name}',REGION:'{.metadata.labels.topology\.kubernetes\.io/region}',ZONE:'{metadata.labels.topology\.kubernetes\.io/zone}'
```

Ez egy tömörebb kimenetet nyújt:

```console
NAME                                REGION   ZONE
aks-nodepool1-34917322-vmss000000   eastus   eastus-1
aks-nodepool1-34917322-vmss000001   eastus   eastus-2
aks-nodepool1-34917322-vmss000002   eastus   eastus-3
```

## <a name="verify-pod-distribution-across-zones"></a>A pod-eloszlás ellenőrzése a zónák között

Amint azt a [jól ismert címkék, jegyzetek és Adatszennyező objektumok][kubectl-well_known_labels]dokumentálják, a Kubernetes a `failure-domain.beta.kubernetes.io/zone` címkével automatikusan terjeszti a hüvelyeket egy replikációs vezérlőben vagy szolgáltatásban az elérhető különböző zónák között. Ennek teszteléséhez 3 – 5 csomópontra méretezheti a fürtöt a helyes Pod-terjesztés ellenőrzéséhez:

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

Ha a skálázási művelet néhány perc elteltével befejeződik, a `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"` bash-rendszerhéjban lévő parancsnak a következőhöz hasonló kimenetet kell adnia:

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

Most két további csomópont található az 1. és a 2. zónában. Három replikából álló alkalmazást is telepíthet. Az NGINX-et példaként fogjuk használni:

```console
kubectl create deployment nginx --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
kubectl scale deployment nginx --replicas=3
```

A hüvelyeket futtató csomópontok megtekintésével láthatja, hogy a hüvelyek a három különböző rendelkezésre állási zónának megfelelő csomópontokon futnak. Például `kubectl describe pod | grep -e "^Name:" -e "^Node:"` egy bash-rendszerhéjban a paranccsal a következőhöz hasonló kimenetet kaphat:

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

Ahogy az előző kimenetben látható, az első Pod a 0. csomóponton fut, amely a rendelkezésre állási zónában található `eastus2-1` . A második Pod a 2. csomóponton fut, amely a (z `eastus2-3` ) és a (z) 4. csomópontjában található `eastus2-2` . További konfiguráció nélkül a Kubernetes a három rendelkezésre állási zónában helyesen terjeszti a hüvelyeket.

## <a name="next-steps"></a>Következő lépések

Ez a cikk részletesen ismerteti, hogyan hozhat létre rendelkezésre állási zónákat használó AK-fürtöt. A magasan elérhető fürtökkel kapcsolatos további szempontokat lásd: [ajánlott eljárások az üzletmenet folytonossága és a vész-helyreállítás az AK-ban][best-practices-bc-dr].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-overview]: ../availability-zones/az-overview.md
[best-practices-bc-dr]: operator-best-practices-multi-region.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[standard-lb-limitations]: load-balancer-standard.md#limitations
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[vmss-zone-balancing]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-well_known_labels]: https://kubernetes.io/docs/reference/labels-annotations-taints/

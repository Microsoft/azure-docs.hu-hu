---
title: Virtuális csomópontok használata
titleSuffix: Azure Kubernetes Service
description: A virtuális csomópontok Azure Kubernetes-szolgáltatásokkal (ak) való használatának áttekintése
services: container-service
ms.topic: conceptual
ms.date: 02/17/2021
ms.custom: references_regions
ms.openlocfilehash: 3bba1155ec57db67968aec95d1d3386fc6cda006
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100634447"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes"></a>Azure Kubernetes Services (ak) fürt létrehozása és konfigurálása virtuális csomópontok használatához

Az alkalmazások számítási feladatainak egy AK-fürtön való gyors méretezéséhez virtuális csomópontokat használhat. A virtuális csomópontokkal gyorsan kiépítheti a hüvelyeket, és a végrehajtásuk ideje alatt csak másodpercenként kell fizetnie. A további hüvelyek futtatásához nem kell várnia, hogy a Kubernetes-fürt autoskálázása virtuálisgép-számítási csomópontokat helyezzen üzembe. A virtuális csomópontok csak Linux-hüvelyek és-csomópontok esetén támogatottak.

Az AK-hoz készült Virtual Nodes bővítmény a nyílt forráskódú projekt [virtuális Kubelet][virtual-kubelet-repo]alapul.

Ez a cikk áttekintést nyújt a régió rendelkezésre állásáról és a virtuális csomópontok használatának hálózati követelményeiről, valamint az ismert korlátozásokról.

## <a name="regional-availability"></a>Regionális elérhetőség

A virtuális csomópontok üzemelő példányai támogatják az összes régiót, ahol az ACI támogatja az VNET SKU-t.

Az egyes régiókban rendelkezésre álló CPU-és memória-SKU-ban tekintse meg a [Azure Container instances erőforrás rendelkezésre állását az Azure Regions – Linux Container groups szolgáltatásban Azure Container instances](../container-instances/container-instances-region-availability.md#linux-container-groups)

## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

A virtuális csomópontok hálózati kommunikációt tesznek lehetővé Azure Container Instances (ACI) és az AK-fürtön futó hüvelyek között. A kommunikáció biztosításához létre kell hozni egy virtuális hálózati alhálózatot, és hozzá kell rendelni a delegált engedélyeket. A virtuális csomópontok csak a *speciális* hálózatkezelés (Azure CNI) használatával létrehozott AK-fürtökkel működnek. Alapértelmezés szerint az AK-fürtök *alapszintű* hálózatkezeléssel (kubenet) jönnek létre.

Az Azure Container Instancesban (ACI) futó hüvelyeknek hozzáférést kell biztosítaniuk az AK API-kiszolgálói végponthoz a hálózatkezelés konfigurálásához.

## <a name="known-limitations"></a>Ismert korlátozások

A virtuális csomópontok funkciói nagy mértékben függenek az ACI funkciójának. A [Azure Container instances kvótái és korlátai](../container-instances/container-instances-quotas.md)mellett a virtuális csomópontok még nem támogatják a következő forgatókönyveket:

* Egyszerű szolgáltatásnév használata az ACR-lemezképek lekéréséhez. [Megkerülő megoldás](https://github.com/virtual-kubelet/azure-aci/blob/master/README.md#private-registry) a [Kubernetes-titkok](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line) használata
* [Virtual Network korlátozásokat](../container-instances/container-instances-vnet.md) , beleértve a VNet-társítást, a Kubernetes hálózati házirendeket és az internetre irányuló kimenő adatforgalmat hálózati biztonsági csoportokkal.
* Init-tárolók
* [Gazdagép-aliasok](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* A exec [argumentumai](../container-instances/container-instances-exec.md#restrictions) az ACI-ban
* A [DaemonSets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) nem helyezi üzembe a hüvelyeket a virtuális csomópontokon
* A virtuális csomópontok támogatják a Linux-hüvelyek ütemezését. Manuálisan is telepítheti a nyílt forráskódú [virtuális KUBELET ACI](https://github.com/virtual-kubelet/azure-aci) -szolgáltatót a Windows Server-tárolók ACI-ra való beosztásához.
* A virtuális csomópontok az Azure CNI hálózatkezeléssel rendelkező AK-fürtöket igényelnek.
* API-kiszolgáló által jóváhagyott IP-címtartományok használata az AK-hoz.
* A Volume Mount Azure Files-megosztás támogatja az [általános célú v1-](../storage/common/storage-account-overview.md#types-of-storage-accounts)et. A [kötetek Azure Files megosztással való](azure-files-volume.md) csatlakoztatásának utasításait követve
* Az IPv6 használata nem támogatott.

## <a name="next-steps"></a>Következő lépések

Virtuális csomópontok konfigurálása a fürtökhöz:

- [Virtuális csomópontok létrehozása az Azure CLI-vel](virtual-nodes-cli.md)
- [Virtuális csomópontok létrehozása a portál használatával az Azure Kubernetes Servicesben (ak)](virtual-nodes-portal.md)

A virtuális csomópontok gyakran az AK-ban lévő skálázási megoldás egyik összetevője. A megoldások méretezésével kapcsolatos további információkért tekintse meg a következő cikkeket:

- [A Kubernetes vízszintes Pod automéretező használata][aks-hpa]
- [A Kubernetes-fürt automéretező használata][aks-cluster-autoscaler]
- [Tekintse meg a virtuális csomópontok méretezési mintáját][virtual-node-autoscale]
- [További információ a virtuális Kubelet nyílt forráskódú könyvtáráról][virtual-kubelet-repo]

<!-- LINKS - external -->
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet

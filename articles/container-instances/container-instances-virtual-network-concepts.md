---
title: Virtuális hálózat használatára vonatkozó forgatókönyvek
description: Forgatókönyvek, erőforrások és korlátozások a tároló-csoportok Azure-beli virtuális hálózatra való üzembe helyezéséhez.
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 20c2b4fe2f19402d6647f398a9696b7e16550d8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104606888"
---
# <a name="virtual-network-scenarios-and-resources"></a>Virtuális hálózati forgatókönyvek és erőforrások

Az [azure Virtual Network](../virtual-network/virtual-networks-overview.md) biztonságos, privát hálózatkezelést biztosít az Azure-hoz és a helyszíni erőforrásokhoz. Ha tároló-csoportokat telepít egy Azure-beli virtuális hálózatba, a tárolók biztonságosan kommunikálhatnak a virtuális hálózat többi erőforrásával. 

Ez a cikk a virtuális hálózati forgatókönyvekről, korlátozásokról és erőforrásokról nyújt hátteret. Az Azure CLI használatával történő üzembe helyezési példákat lásd: [Container instances telepítése Azure-beli virtuális hálózatba](container-instances-vnet.md).

> [!IMPORTANT]
> A tároló csoportok üzembe helyezése virtuális hálózatra általánosan elérhető Linux-tárolók esetén, ahol a Azure Container Instances elérhető. Részletekért lásd: [régiók és erőforrás rendelkezésre állása](container-instances-region-availability.md). 

## <a name="scenarios"></a>Forgatókönyvek

Az Azure-beli virtuális hálózatba üzembe helyezett tároló-csoportok a következőkhöz hasonló forgatókönyveket tesznek lehetővé:

* Azonos alhálózaton található tároló csoportok közötti közvetlen kommunikáció
* [Feladat-alapú munkaterhelés-](container-instances-restart-policy.md) kimenet küldése a tároló példányaiból a virtuális hálózatban található adatbázisba
* Tároló-példányok tartalmának beolvasása a virtuális hálózat [szolgáltatásbeli végpontján](../virtual-network/virtual-network-service-endpoints-overview.md)
* A tárolók kommunikációjának engedélyezése a helyszíni erőforrásokkal [VPN-átjárón](../vpn-gateway/vpn-gateway-about-vpngateways.md) vagy [ExpressRoute](../expressroute/expressroute-introduction.md) keresztül
* Integrálás a [Azure Firewall](../firewall/overview.md) a tárolóból származó kimenő forgalom azonosításához 
* Nevek feloldása belső Azure DNS használatával a virtuális hálózatban lévő Azure-erőforrásokkal való kommunikációhoz, például virtuális gépekhez
* NSG szabályok használata az alhálózatokhoz vagy más hálózati erőforrásokhoz való hozzáférés vezérléséhez

## <a name="unsupported-networking-scenarios"></a>Nem támogatott hálózatkezelési forgatókönyvek 

* **Azure Load Balancer** – a hálózati tárolóban található tároló példányai előtt egy Azure Load Balancer elhelyezése nem támogatott
* **Globális virtuális hálózati** társítás – globális társítás (virtuális hálózatok összekapcsolása az Azure-régiók között) nem támogatott
* A virtuális hálózatra telepített **nyilvános IP-cím vagy DNS** -tároló csoportok jelenleg nem támogatják a tárolók közvetlen közzétételét nyilvános IP-címmel vagy teljes tartománynévvel.
* **Virtual Network NAT** – a virtuális hálózatra telepített tároló-csoportok jelenleg nem TÁMOGATJÁK a NAT-átjáró erőforrásait a kimenő internetkapcsolathoz.

## <a name="other-limitations"></a>Egyéb korlátozások

* Jelenleg csak a Linux-tárolók támogatottak egy virtuális hálózaton üzembe helyezett tároló-csoportban.
* A tároló-csoportok alhálózatra történő telepítéséhez az alhálózat nem tartalmazhat más típusú erőforrásokat. Távolítsa el a meglévő alhálózatból az összes meglévő erőforrást a tároló-csoportok üzembe helyezése előtt, vagy hozzon létre egy új alhálózatot.
* Nem használhat [felügyelt identitást](container-instances-managed-identity.md) egy virtuális hálózatra központilag telepített tároló csoportba.
* Egy virtuális hálózatra központilag üzembe helyezett tároló csoportban nem engedélyezhető az [élő](container-instances-liveness-probe.md) vagy a [készültségi](container-instances-readiness-probe.md) mintavétel.
* Az érintett további hálózati erőforrások miatt a virtuális hálózatra történő központi telepítések általában lassabbak, mint a standard Container-példányok üzembe helyezése.
* Ha egy Azure Storage-fiókhoz csatlakoztatja a tároló-csoportot, hozzá kell adnia egy [szolgáltatási végpontot](../virtual-network/virtual-network-service-endpoints-overview.md) az erőforráshoz.

[!INCLUDE [container-instances-restart-ip](../../includes/container-instances-restart-ip.md)]

## <a name="required-network-resources"></a>Szükséges hálózati erőforrások

A tároló-csoportok virtuális hálózathoz való telepítéséhez három Azure Virtual Network erőforrás szükséges: maga a [virtuális hálózat](#virtual-network) , a virtuális hálózaton belüli [delegált alhálózat](#subnet-delegated) és egy [hálózati profil](#network-profile). 

### <a name="virtual-network"></a>Virtuális hálózat

A virtuális hálózat határozza meg azt a címtartományt, amelyben egy vagy több alhálózatot hoz létre. Ezután üzembe helyezheti az Azure-erőforrásokat (például a Container groups-t) a virtuális hálózat alhálózatai között.

### <a name="subnet-delegated"></a>Alhálózat (delegált)

Az alhálózatok a virtuális hálózatot különálló, a bennük található Azure-erőforrások által használható címekre osztják. Létrehoz egy vagy több alhálózatot egy virtuális hálózaton belül.

A tárolók csoportjaihoz használt alhálózat csak tároló csoportokat tartalmazhat. Amikor először telepít egy tároló csoportot egy alhálózatra, az Azure delegálja az alhálózatot Azure Container Instances. A delegálás után az alhálózat csak a tároló csoportok esetében használható. Ha a tárolói csoportokon kívül más erőforrásokat is telepít egy delegált alhálózatra, a művelet sikertelen lesz.

### <a name="network-profile"></a>Hálózati profil

A hálózati profil az Azure-erőforrások hálózati konfigurációs sablonja. Megadja az erőforrás bizonyos hálózati tulajdonságait, például azt az alhálózatot, amelybe központilag telepíteni kívánja. Amikor először az az [Container Create][az-container-create] paranccsal telepít egy tároló csoportot egy alhálózatra (és így egy virtuális hálózatra), az Azure létrehoz egy hálózati profilt. Ezt a hálózati profilt ezután a jövőbeli központi telepítések számára is használhatja az alhálózathoz. 

Ha Resource Manager-sablont, YAML-fájlt vagy programozott módszert szeretne használni egy alhálózatra, meg kell adnia egy hálózati profil teljes Resource Manager erőforrás-AZONOSÍTÓját. Használhat korábban [az az Container Create][az-container-create]paranccsal létrehozott profilt, vagy létrehozhat egy profilt egy Resource Manager-sablonnal (lásd a [sablon példáját](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet) és a [hivatkozást](/azure/templates/microsoft.network/networkprofiles)). Egy korábban létrehozott profil AZONOSÍTÓjának lekéréséhez használja az az [Network profil List][az-network-profile-list] parancsot. 

A következő ábrán számos tároló csoport lett telepítve egy Azure Container Instances-ra delegált alhálózatra. Miután telepített egy tároló csoportot egy alhálózatra, további tároló csoportokat is telepíthet rá, ha ugyanazt a hálózati profilt megadja.

![Virtuális hálózaton belüli tároló-csoportok][aci-vnet-01]

## <a name="next-steps"></a>Következő lépések

* Az Azure CLI-vel történő üzembe helyezési példákat lásd: [Container instances telepítése Azure-beli virtuális hálózatba](container-instances-vnet.md).
* Ha új virtuális hálózatot, alhálózatot, hálózati profilt és tároló csoportot szeretne üzembe helyezni Resource Manager-sablonnal, tekintse meg [Az Azure Container Group létrehozása a VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)használatával című témakört.
* Ha a [Azure Portal](container-instances-quickstart-portal.md) használatával hoz létre egy tároló-példányt, megadhat egy új vagy exsting virtuális hálózat beállításait is a **hálózatkezelés** lapon.


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-virtual-network-concepts/aci-vnet-01.png

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list

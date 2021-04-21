---
title: Virtuális hálózat használatára vonatkozó forgatókönyvek
description: A tárolócsoportok Azure-beli virtuális hálózaton való üzembe helyezésének forgatókönyvei, erőforrásai és korlátozásai.
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 6de99c68c3f05e4734dd46a579d28a6f1a3b824e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763774"
---
# <a name="virtual-network-scenarios-and-resources"></a>Virtuális hálózati forgatókönyvek és erőforrások

[Az Azure Virtual Network](../virtual-network/virtual-networks-overview.md) biztonságos magánhálózatot biztosít az Azure-beli és a helyszíni erőforrások számára. Ha tárolócsoportokat helyez üzembe egy Azure-beli virtuális hálózatban, a tárolók biztonságosan kommunikálhatnak a virtuális hálózat más erőforrásaival. 

Ez a cikk háttérinformációkat nyújt a virtuális hálózati forgatókönyvekről, korlátozásokról és erőforrásokról. Példák az Azure CLI használatával történő üzembe helyezésre: Deploy container instances into an Azure virtual network (Tárolópéldányok üzembe helyezése [Azure virtuális hálózaton).](container-instances-vnet.md)

> [!IMPORTANT]
> A tárolócsoport virtuális hálózaton való üzembe helyezése általánosan elérhető a Linux-tárolókhoz, és a legtöbb régióban, ahol Azure Container Instances elérhető. Részletekért lásd: [Régiók és erőforrások rendelkezésre állása.](container-instances-region-availability.md) 

## <a name="scenarios"></a>Forgatókönyvek

Az Azure-beli virtuális hálózatban üzembe helyezett tárolócsoportok például a következő forgatókönyveket teszik lehetővé:

* Közvetlen kommunikáció ugyanazon az alhálózaton található tárolócsoportok között
* Feladatalapú [számítási feladat kimenetének](container-instances-restart-policy.md) küldése tárolópéldányból egy, a virtuális hálózaton található adatbázisba
* Tárolópéldányok tartalmának lekérése a [virtuális hálózat szolgáltatásvégpontjaiból](../virtual-network/virtual-network-service-endpoints-overview.md)
* Tárolók és helyszíni erőforrásokkal való kommunikáció engedélyezése [VPN-átjárón vagy](../vpn-gateway/vpn-gateway-about-vpngateways.md) [ExpressRoute-on keresztül](../expressroute/expressroute-introduction.md)
* Integrálás [](../firewall/overview.md) Azure Firewall tárolóból származó kimenő forgalom azonosításához 
* Nevek feloldása a belső Azure DNS a virtuális hálózatBan lévő Azure-erőforrásokkal, például virtuális gépekkel való kommunikációhoz
* Az alhálózatok vagy más hálózati erőforrások tárolókhoz való hozzáférésének szabályozása NSG-szabályokkal

## <a name="unsupported-networking-scenarios"></a>Nem támogatott hálózati forgatókönyvek 

* **Azure Load Balancer** – A Azure Load Balancer tárolópéldányok elé helyezés hálózati tárolócsoportban nem támogatott
* **Globális virtuális hálózatok közötti** társviszony-létesítés – A globális társviszony-létesítés (a virtuális hálózatok Azure-régiók közötti összekapcsolása) nem támogatott
* **Nyilvános IP-** vagy DNS-címke – A virtuális hálózaton üzembe helyezett tárolócsoportok jelenleg nem támogatják a tárolók nyilvános IP-címmel vagy teljes tartománynévvel való közvetlen elérhetővé helyezését az interneten
* **Virtual Network NAT** – A virtuális hálózaton üzembe helyezett tárolócsoportok jelenleg nem támogatják a NAT-átjáróerőforrás kimenő internetkapcsolathoz való használatát.

## <a name="other-limitations"></a>Egyéb korlátozások

* Jelenleg csak a Linux-tárolók támogatottak a virtuális hálózaton üzembe helyezett tárolócsoportban.
* A tárolócsoportok alhálózaton való üzembe helyezéséhez az alhálózat nem tartalmazhat más erőforrástípusokat. Távolítsa el az összes meglévő erőforrást egy meglévő alhálózatból, mielőtt tárolócsoportokat helyezne üzembe rajta, vagy hozzon létre egy új alhálózatot.
* A virtuális hálózaton [](container-instances-managed-identity.md) üzembe helyezett tárolócsoportban nem használhat felügyelt identitást.
* Virtuális hálózaton üzembe [](container-instances-liveness-probe.md) helyezett tárolócsoportban nem engedélyezhető élességi mintavétel vagy készenlét-mintavétel. [](container-instances-readiness-probe.md)
* A további hálózati erőforrások miatt a virtuális hálózaton való üzembe helyezés általában lassabb, mint egy standard tárolópéldány üzembe helyezése.
* Ha a tárolócsoportot egy Azure Storage-fiókhoz csatlakoztatja, hozzá kell adni egy szolgáltatásvégpontot [az](../virtual-network/virtual-network-service-endpoints-overview.md) erőforráshoz.

[!INCLUDE [container-instances-restart-ip](../../includes/container-instances-restart-ip.md)]

## <a name="required-network-resources"></a>Szükséges hálózati erőforrások

A tárolócsoportok virtuális hálózaton való üzembe helyezéséhez három Azure Virtual Network-erőforrás szükséges: maga a virtuális hálózat, [a](#subnet-delegated) virtuális hálózaton belüli delegált alhálózat és egy [hálózati profil.](#network-profile) [](#virtual-network) 

### <a name="virtual-network"></a>Virtuális hálózat

A virtuális hálózat határozza meg azt a címterületet, amelyben egy vagy több alhálózatot hoz létre. Ezután azure-erőforrásokat (például tárolócsoportokat) helyez üzembe a virtuális hálózat alhálózatán.

### <a name="subnet-delegated"></a>Alhálózat (delegált)

Az alhálózatok a virtuális hálózatot külön címterekre szegmentálják, amelyek a bennük lévő Azure-erőforrások által használhatók. Egy vagy több alhálózatot a virtuális hálózaton belül hozhat létre.

A tárolócsoportokhoz használt alhálózat csak tárolócsoportokat tartalmazhat. Amikor először helyez üzembe egy tárolócsoportot egy alhálózaton, az Azure delegálja az alhálózatot a Azure Container Instances. A delegálás után az alhálózat csak tárolócsoportokhoz használható. Ha tárolócsoportokon kívül más erőforrásokat próbál üzembe helyezni egy delegált alhálózaton, a művelet sikertelen lesz.

### <a name="network-profile"></a>Hálózati profil

A hálózati profil az Azure-erőforrások hálózati konfigurációs sablonja. Megad bizonyos hálózati tulajdonságokat az erőforráshoz, például azt az alhálózatot, amelyben üzembe kell helyezni. Amikor először az [az container create][az-container-create] paranccsal helyez üzembe egy tárolócsoportot egy alhálózaton (és így egy virtuális hálózaton), az Azure létrehoz egy hálózati profilt. Ezután használhatja ezt a hálózati profilt az alhálózaton való későbbi üzembe helyezéshez. 

Ha egy Resource Manager-sablont, YAML-fájlt vagy programozott módszert használ egy tárolócsoport alhálózaton való üzembe helyezéséhez, meg kell adnia egy hálózati profil teljes Resource Manager erőforrás-azonosítóját. Használhatja a korábban létrehozott profilt az [az container create][az-container-create]használatával, vagy létrehozhat egy profilt egy Resource Manager sablon használatával (lásd a [példasablont](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet) és a [hivatkozást).](/azure/templates/microsoft.network/networkprofiles) Egy korábban létrehozott profil azonosítóját az [az network profile list paranccsal használhatja.][az-network-profile-list] 

Az alábbi ábrán több tárolócsoport is üzembe lett helyezni egy alhálózaton, amely a Azure Container Instances. Miután üzembe helyezett egy tárolócsoportot egy alhálózaton, további tárolócsoportokat is üzembe helyezhet, ha megadja ugyanazt a hálózati profilt.

![Tárolócsoportok egy virtuális hálózaton belül][aci-vnet-01]

## <a name="next-steps"></a>Következő lépések

* Példák az Azure CLI üzembe helyezésére: [Tárolópéldányok üzembe helyezése Azure-beli virtuális hálózatban.](container-instances-vnet.md)
* Új virtuális hálózat, alhálózat, hálózati profil és tárolócsoport Resource Manager sablonnal való üzembe helyezéséhez lásd: Azure-tárolócsoport létrehozása [virtuális hálózattal.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)
* Ha a [Azure Portal](container-instances-quickstart-portal.md) tárolópéldányt hoz létre, a Hálózat lapon új vagy exsting virtuális hálózat beállításait **is meg lehet** adni.


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-virtual-network-concepts/aci-vnet-01.png

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-network-profile-list]: /cli/azure/network/profile#az_network_profile_list

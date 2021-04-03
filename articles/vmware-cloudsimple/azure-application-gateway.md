---
title: Az Azure Application Gateway használata VMware virtuális gépekkel
description: Leírja, hogyan használhatja az Azure Application Gatewayt a VMware virtuális gépeken futó webkiszolgálók bejövő webes forgalmának kezeléséhez, hogy megnyerje a CloudSimple privát felhőalapú környezetét
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d14fdc4e2b2b1dfca8194077acc0453808611946
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97897943"
---
# <a name="use-azure-application-gateway-with-vmware-virtual-machines-in-the-cloudsimple-private-cloud-environment"></a>Az Azure Application Gateway használata VMware virtuális gépekkel a CloudSimple privát felhőalapú környezetében

Az Azure Application Gateway használatával felügyelheti a VMware virtuális gépeken futó webkiszolgálók bejövő webes forgalmát a CloudSimple saját felhőalapú környezetében.

Az Azure-Application Gateway nyilvános hibrid környezetekben való kihasználása révén kezelheti az alkalmazásaira irányuló webes forgalmat, biztonságos kezelőfelületet biztosíthat, és kioszthatja a TLS-feldolgozást a VMware-környezetben futó szolgáltatásai számára. Az Azure Application Gateway a konfigurált szabályok és állapot-mintavételek alapján átirányítja a bejövő webes forgalmat a VMware-környezetekben található háttér-készlet példányaihoz.

Ehhez az Azure Application Gateway-megoldáshoz a következőket kell tennie:

* Azure-előfizetés.
* Hozzon létre és konfiguráljon egy Azure-beli virtuális hálózatot és egy alhálózatot a virtuális hálózaton belül.
* Hozza létre és konfigurálja a NSG-szabályokat és a vNet a ExpressRoute használatával a CloudSimple saját felhője számára.
* Hozzon létre & konfigurálja saját Felhőjét.
* Hozzon létre & konfigurálja az Azure-Application Gateway.

## <a name="azure-application-gateway-deployment-scenario"></a>Azure Application Gateway üzembe helyezési forgatókönyv

Ebben az esetben az Azure Application Gateway az Azure Virtual Networkben fut. A virtuális hálózat egy ExpressRoute áramkörön keresztül csatlakozik a saját felhőhöz. A privát felhőben lévő összes alhálózat IP-címe elérhető a virtuális hálózat alhálózatai között.

![Azure Load Balancer az Azure Virtual Networkben](media/load-balancer-use-case.png)

## <a name="how-to-deploy-the-solution"></a>A megoldás üzembe helyezése

Az üzembe helyezési folyamat a következő feladatokből áll:

1. [Ellenőrizze, hogy teljesülnek-e az Előfeltételek](#1-verify-prerequisites)
2. [Azure-beli virtuális kapcsolat csatlakoztatása a privát felhőhöz](#2-connect-your-azure-virtual-network-to-your-private-cloud)
3. [Azure Application Gateway üzembe helyezése](#3-deploy-an-azure-application-gateway)
4. [Webkiszolgáló virtuális gép készletének létrehozása és konfigurálása a saját felhőben](#4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud)

## <a name="1-verify-prerequisites"></a>1. Előfeltételek ellenőrzése

Ellenőrizze, hogy teljesülnek-e az előfeltételek:

* Egy Azure Resource Manager és egy virtuális hálózat már létre van hozva.
* Az Azure-beli virtuális hálózaton belül egy dedikált alhálózat (Application Gateway) már létre van hozva.
* Már létre lett hozva egy CloudSimple saját felhő.
* A virtuális hálózat és a privát felhőben lévő alhálózatok IP-alhálózatai között nincs IP-ütközés.

## <a name="2-connect-your-azure-virtual-network-to-your-private-cloud"></a>2. az Azure-beli virtuális hálózat összekötése a saját felhővel

Ha az Azure-beli virtuális hálózatot a privát felhőhöz szeretné kapcsolni, kövesse ezt a folyamatot.

1. A [CloudSimple-portálon másolja a ExpressRoute-peering-információkat](virtual-network-connection.md).

2. [Konfiguráljon egy virtuális hálózati átjárót az Azure-beli virtuális hálózathoz](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).

3. A [virtuális hálózat összekapcsolása a CloudSimple ExpressRoute-áramkörrel](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription).

4. [A virtuális hálózat ExpressRoute-áramkörhöz való kapcsolásához használja a vágólapra másolt egyenrangú adatokat](virtual-network-connection.md).

## <a name="3-deploy-an-azure-application-gateway"></a>3. Azure Application Gateway üzembe helyezése

Az erre vonatkozó részletes utasítások az [Application Gateway létrehozása elérésiút-alapú útválasztási szabályokkal a Azure Portal használatával](../application-gateway/create-url-route-portal.md)érhetők el. Itt látható a szükséges lépések összegzése:

1. Hozzon létre egy virtuális hálózatot az előfizetésben és az erőforráscsoporthoz.
2. Hozzon létre egy alhálózatot (amelyet dedikált alhálózatként kíván használni) a virtuális hálózaton belül.
3. Hozzon létre egy szabványos Application Gateway (opcionálisan engedélyezze a WAF): a Azure Portal kezdőlapon kattintson az **erőforrás**  >  **hálózatkezelés**  >  **Application Gateway** elemre a lap bal felső részén. Válassza ki a szabványos SKU-t és méretet, és adja meg az Azure-előfizetést, az erőforráscsoport és a hely adatait. Ha szükséges, hozzon létre egy új nyilvános IP-címet az Application Gateway számára, és adja meg a virtuális hálózat és az Application Gateway dedikált alhálózata adatait.
4. Vegyen fel egy háttér-készletet a virtuális gépekkel, és adja hozzá az Application gatewayhez.

## <a name="4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud"></a>4. webkiszolgálós virtuálisgép-készlet létrehozása és konfigurálása a saját felhőben

A vCenter-ben hozzon létre virtuális gépeket az Ön által választott operációs rendszerrel és webkiszolgálóval (például Windows/IIS vagy Linux/Apache). Válasszon egy alhálózatot/VLAN-t, amely a saját felhőben a webes szinten van kijelölve. Ellenőrizze, hogy a webkiszolgáló virtuális gépei legalább egy vNIC szerepelnek-e a webes réteg alhálózatán.

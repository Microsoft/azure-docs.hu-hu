---
title: Az Azure-szolgáltatások hálózati elkülönítésének virtuális hálózati integrációja
titlesuffix: Azure Virtual Network
description: Ez a cikk az Azure-szolgáltatások virtuális hálózatra való integrálásának különböző módszereit ismerteti, amelyek lehetővé teszik az Azure-szolgáltatás biztonságos elérését.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: kumud
ms.openlocfilehash: 2c0c4bec93b8fa61275c376fbae2a3a063e72a6f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98785532"
---
# <a name="integrate-azure-services-with-virtual-networks-for-network-isolation"></a>Azure-szolgáltatások integrálása virtuális hálózatokkal hálózati elkülönítéshez

A Virtual Network (VNet) Azure-szolgáltatásokhoz való integrációja lehetővé teszi, hogy a szolgáltatáshoz való hozzáférést csak a virtuális hálózati infrastruktúrához lehessen lezárni. A VNet-infrastruktúra többek között a virtuális hálózatokat és a helyszíni hálózatokat is tartalmazza.

A VNet-integráció biztosítja az Azure-szolgáltatásokat a hálózat elkülönítésének előnyeivel, és az alábbi módszerek közül egyet vagy többet is végrehajthat:
- [A szolgáltatás dedikált példányainak üzembe helyezése virtuális hálózatban](virtual-network-for-azure-services.md). A szolgáltatások ezután a virtuális hálózaton és a helyszíni hálózatokon is elérhetők.
- Privát [végpont](../private-link/private-endpoint-overview.md) használata, amely az [Azure Private-kapcsolaton](../private-link/private-link-overview.md)keresztül kapcsolódik a szolgáltatáshoz, és biztonságosan csatlakozik az Ön számára. A privát végpont egy magánhálózati IP-címet használ a VNet, és hatékonyan hozza a szolgáltatást a virtuális hálózatba.
- A szolgáltatás elérése nyilvános végpontokkal a virtuális hálózatnak a szolgáltatáshoz való kiterjesztésével, [szolgáltatási végpontokon](virtual-network-service-endpoints-overview.md)keresztül. A szolgáltatási végpontok lehetővé teszik a szolgáltatási erőforrások védelmét a virtuális hálózattal.
- A [szolgáltatás-címkék](service-tags-overview.md) használatával engedélyezheti vagy megtagadhatja az Azure-erőforrásokra irányuló forgalmat a nyilvános IP-végpontokra.

## <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>Dedikált Azure-szolgáltatások üzembe helyezése virtuális hálózatokon

Amikor dedikált Azure-szolgáltatásokat helyez üzembe egy virtuális hálózaton, privát IP-címeken keresztül kommunikálhat a szolgáltatási erőforrásokkal.

![Dedikált Azure-szolgáltatások üzembe helyezése virtuális hálózatokon](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Egy dedikált Azure-szolgáltatás üzembe helyezése a virtuális hálózaton a következő képességeket biztosítja:
- A virtuális hálózaton belül található erőforrások privát IP-címeken keresztül kommunikálhatnak egymással. Például közvetlenül az HDInsight és a virtuális gépen futó SQL Server közötti adatátvitel a virtuális hálózaton.
- A helyszíni erőforrások a virtuális hálózatban lévő erőforrásokhoz magánhálózati IP-címek használatával férhetnek hozzá a helyek közötti VPN (VPN Gateway) vagy a ExpressRoute számára.
- A virtuális hálózatok lehetővé teszik, hogy a virtuális hálózatok erőforrásai a magánhálózati IP-címek használatával kommunikáljanak egymással.
- A virtuális hálózatban lévő szolgáltatási példányokat általában teljes mértékben az Azure-szolgáltatás kezeli. Ide tartozik az erőforrások állapotának figyelése és a terheléssel való skálázás.
- A szolgáltatási példányok egy virtuális hálózatban lévő alhálózatba vannak telepítve. Az alhálózat bejövő és kimenő hálózati hozzáférését hálózati biztonsági csoportokon keresztül kell megnyitni a szolgáltatás által biztosított útmutatás alapján.
- Bizonyos szolgáltatások korlátozásokat is bevezetnek az általuk üzembe helyezett alhálózatra, a házirendek alkalmazásának korlátozására, a virtuális gépek és a szolgáltatások erőforrásainak egyazon alhálózaton belüli kikapcsolására. Az egyes szolgáltatásokra vonatkozó korlátozásokat az idő múlásával változhat. Ilyen szolgáltatás például Azure NetApp Files, dedikált HSM, Azure Container Instances, App Service.
- Opcionálisan előfordulhat, hogy a szolgáltatások delegált alhálózatot igényelnek explicit azonosítóként, amelyet egy alhálózat egy adott szolgáltatás üzemeltetésére használhat. A delegálással a szolgáltatások explicit engedélyeket kapnak a szolgáltatásra jellemző erőforrások létrehozásához a delegált alhálózatban.
- Tekintse meg REST API választ egy virtuális hálózaton delegált alhálózattal. A delegált alhálózati modellt használó szolgáltatások átfogó listája az elérhető delegálások API-n keresztül szerezhető be.

A virtuális hálózatban üzembe helyezhető szolgáltatások listáját lásd: [dedikált Azure-szolgáltatások üzembe](virtual-network-for-azure-services.md)helyezése virtuális hálózatokon.

## <a name="private-link-and-private-endpoints"></a>Privát kapcsolat és privát végpontok

A privát végpontok segítségével közvetlenül a virtuális hálózatról az Azure-erőforrásba való belépést engedélyezheti a privát kapcsolaton keresztül anélkül, hogy a nyilvános interneten kellene áthaladnia. A privát végpont egy speciális hálózati adapter a virtuális hálózatban lévő Azure-szolgáltatásokhoz. Amikor létrehoz egy privát végpontot az Azure-erőforráshoz, biztonságos kapcsolatot biztosít a virtuális hálózat és az Azure-erőforrás ügyfelei között. A magánhálózati végpont IP-címet kap a virtuális hálózat IP-címének tartományához. A magánhálózati végpont és az Azure-szolgáltatás közötti kapcsolat biztonságos privát hivatkozást használ.

Az alábbi példa egy Event Grid erőforrás privát végpontjának privát hozzáférését mutatja be, amely biztonságos kapcsolatot biztosít a virtuális hálózatban lévő ügyfelek és Event Grid erőforrás között.

![SQL adatbázis-erőforrás magánhálózati elérése magánhálózati végpont használatával](./media/network-isolation/architecture-diagram.png)

További információ a privát hivatkozásról és a támogatott Azure-szolgáltatások listájáról: [Mi az a privát hivatkozás?](../private-link/private-link-overview.md)

## <a name="service-endpoints"></a>Szolgáltatásvégpontok
A VNet szolgáltatás végpontja biztonságos és közvetlen kapcsolatot biztosít az Azure-szolgáltatásokkal az Azure gerinces hálózaton keresztül optimalizált útvonalon. A végpontok segítségével biztosíthatja, hogy kritikus fontosságú Azure-szolgáltatási erőforrásai csak a virtuális hálózatain legyenek elérhetőek. A szolgáltatási végpontok lehetővé teszik, hogy a VNet magánhálózati IP-címei elérjék az Azure-szolgáltatások végpontját anélkül, hogy a VNet nyilvános IP-címet kellene megadniuk.

![Azure-szolgáltatások biztosítása virtuális hálózatokhoz](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

További információ: [Virtual Network szolgáltatás-végpontok](virtual-network-service-endpoints-overview.md)

## <a name="service-tags"></a>Szolgáltatáscímkék

A szolgáltatás címkéje egy adott Azure-szolgáltatás IP-címeinek egy csoportját jelöli. A szolgáltatás-címkék használatával hálózati [biztonsági csoportokon](./network-security-groups-overview.md#security-rules) vagy [Azure Firewallon](../firewall/service-tags.md)is meghatározhat hálózati hozzáférés-vezérlést. A szolgáltatási címke nevének (például AzureEventGrid) megadásával a szabály megfelelő forrás vagy cél mezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát.

![Forgalom engedélyezése vagy megtagadása szolgáltatás-címkék használatával](./media/network-isolation/service-tags.png)

A szolgáltatás-címkék használatával hálózati elkülönítést érhet el, és biztosíthatja az Azure-erőforrásokat az általános internetről, miközben nyilvános végpontokkal rendelkező Azure-szolgáltatásokhoz fér hozzá. Bejövő/kimenő hálózati biztonsági csoportokra vonatkozó szabályok létrehozása az **internetre** irányuló adatforgalom megtagadásához, valamint az adott Azure-szolgáltatások **AzureCloud** vagy más [elérhető szolgáltatás-címkévé](service-tags-overview.md#available-service-tags) tételének engedélyezése.

További információ az azokat támogató szolgáltatási címkékről és Azure-szolgáltatásokról: a [szolgáltatási címkék áttekintése](service-tags-overview.md)

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [integrálhatja alkalmazását egy Azure-hálózattal](../app-service/web-sites-integrate-with-vnet.md).
- Megtudhatja, hogyan [korlátozhatja az erőforrásokhoz való hozzáférést a szolgáltatás-címkék használatával](tutorial-restrict-network-access-to-resources.md).
- Ismerje meg, hogyan csatlakozhat az Azure [Cosmos-fiókhoz az Azure Private link használatával](../private-link/tutorial-private-endpoint-cosmosdb-portal.md).
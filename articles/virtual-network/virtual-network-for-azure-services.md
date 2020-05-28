---
title: Virtuális hálózat az Azure-szolgáltatásokhoz
titlesuffix: Azure Virtual Network
description: Ismerje meg, hogy milyen előnyökkel jár az erőforrások virtuális hálózatra történő telepítése. A virtuális hálózatokban lévő erőforrások kommunikálhatnak egymással és helyszíni erőforrásokkal anélkül, hogy a forgalom az interneten halad át.
services: virtual-network
documentationcenter: na
author: malopMSFT
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: malop
ms.reviewer: kumud
ms.openlocfilehash: 5b9d18d47c1430023dc9d892ce25389810690a4d
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84021636"
---
# <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>Dedikált Azure-szolgáltatások üzembe helyezése virtuális hálózatokon

Amikor dedikált Azure-szolgáltatásokat helyez üzembe egy [virtuális hálózaton](virtual-networks-overview.md), privát IP-címeken keresztül kommunikálhat a szolgáltatási erőforrásokkal.

![Virtuális hálózatban üzembe helyezett szolgáltatások](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

A szolgáltatások virtuális hálózaton belüli üzembe helyezése a következő képességeket biztosítja:

- A virtuális hálózaton belül található erőforrások privát IP-címeken keresztül kommunikálhatnak egymással. Például közvetlenül az HDInsight és a virtuális gépen futó SQL Server közötti adatátvitel a virtuális hálózaton.
- A helyszíni erőforrások a virtuális hálózatban lévő erőforrásokhoz magánhálózati IP-címek használatával férhetnek hozzá a [helyek közötti VPN (VPN Gateway)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) vagy a [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)számára.
- A virtuális hálózatok lehetővé teszik [, hogy a](virtual-network-peering-overview.md) virtuális hálózatok erőforrásai a magánhálózati IP-címek használatával kommunikáljanak egymással.
- A virtuális hálózatban lévő szolgáltatási példányokat általában teljes mértékben az Azure-szolgáltatás kezeli. Ide tartozik az erőforrások állapotának figyelése és a terheléssel való skálázás.
- A szolgáltatási példányok egy virtuális hálózatban lévő alhálózatba vannak telepítve. Az alhálózat bejövő és kimenő hálózati hozzáférését [hálózati biztonsági csoportokon](security-overview.md#network-security-groups)keresztül kell megnyitni a szolgáltatás által biztosított útmutatás alapján.
- Bizonyos szolgáltatások korlátozásokat is bevezetnek az általuk üzembe helyezett alhálózatra, a házirendek alkalmazásának korlátozására, a virtuális gépek és a szolgáltatások erőforrásainak egyazon alhálózaton belüli kikapcsolására. Az egyes szolgáltatásokra vonatkozó korlátozásokat az idő múlásával változhat. Ilyen szolgáltatás például Azure NetApp Files, dedikált HSM, Azure Container Instances, App Service. 
- Opcionálisan előfordulhat, hogy a szolgáltatások [delegált alhálózatot](virtual-network-manage-subnet.md#add-a-subnet) igényelnek explicit azonosítóként, amelyet egy alhálózat egy adott szolgáltatás üzemeltetésére használhat. A delegálással a szolgáltatások explicit engedélyeket kapnak a szolgáltatásra jellemző erőforrások létrehozásához a delegált alhálózatban.
- Tekintse meg REST API választ egy [virtuális hálózaton delegált alhálózattal](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/get#get-virtual-network-with-a-delegated-subnet). A delegált alhálózati modellt használó szolgáltatások átfogó listája az [elérhető delegálások](https://docs.microsoft.com/rest/api/virtualnetwork/availabledelegations/list) API-n keresztül szerezhető be.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>A virtuális hálózatokban üzembe helyezhető szolgáltatások

|Kategória|Szolgáltatás| Dedikált<sup>1</sup>sup>1 </sup> alhálózat
|-|-|-|
| Compute | Virtual Machines: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Virtuálisgép-méretezési csoportok](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Cloud Service](https://msdn.microsoft.com/library/azure/jj156091): csak virtuális hálózat (klasszikus)<br/> [Azure Batch](../batch/nodes-and-pools.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| Nem <br/> Nem <br/> Nem <br/> Nem<sup>2</sup>sup>2</sup>
| Network (Hálózat) | [Application Gateway – WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Hálózati virtuális berendezések](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn) | Igen <br/> Igen <br/> Igen <br/> Nem
|Adatok|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Felügyelt Azure SQL Database-példány](../azure-sql/managed-instance/connectivity-architecture-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| Igen <br/> Igen <br/> 
|Elemzés | [Azure-HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Nincs<sup>2</sup>sup>2</Su<sup>2</sup>> <br/> Nem<sup>2</sup> <br/> 
| Identitás | [Azure Active Directory tartományi szolgáltatások](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Nem <br/>
| Containers | [Azure Kubernetes Service (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Container Instance (ACI)](https://www.aka.ms/acivnet)<br/>[Azure Container Service motor](https://github.com/Azure/acs-engine) az Azure Virtual Network CNI [beépülő modullal](https://github.com/Azure/acs-engine/tree/master/examples/vnet)<br/>[Azure Functions](../azure-functions/functions-networking-options.md#virtual-network-integration) |Nem<sup>2</sup>sup>2</sup><br/> Igen <br/><br/> Nem <br/> Igen
| Web | [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Web Apps](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[App Service-környezet](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Igen <br/> Igen <br/> Igen <br/> Igen
| Szolgáltatott | [Azure Dedicated HSM](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Igen <br/> Igen <br/>
| | |

<sup>1</sup> a "dedikált" kifejezés azt jelenti, hogy csak az adott alhálózaton üzemelő szolgáltatás-erőforrások helyezhetők üzembe, és nem kombinálhatók az ügyfél virtuális géppel/VMSSs <br/> 
<sup>2</sup> ajánlott eljárás, hogy ezeket a szolgáltatásokat dedikált alhálózatban lehessen használni, de a szolgáltatás nem kötelező előírásait.

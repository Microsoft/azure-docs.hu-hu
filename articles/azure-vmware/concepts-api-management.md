---
title: Fogalmak – API Management
description: Ismerje meg, hogyan védi a API Management az Azure VMware-megoldású virtuális gépeken futó API-kat
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 958cc52c48d1121a69dca2fc901289ad1ed671cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94541963"
---
# <a name="api-management-to-publish-and-protect-apis-running-on-azure-vmware-solution-based-vms"></a>Az Azure VMware megoldáson alapuló virtuális gépeken futó API-k közzétételének és védelemének API Management

Microsoft Azure [API Management](https://azure.microsoft.com/services/api-management/) lehetővé teszi a biztonságos közzétételt a belső vagy külső felhasználók számára.  Csak a fejlesztői és prémium SKU-k teszik lehetővé az Azure Virtual Network integrációját az Azure VMware-megoldás munkaterheléseken futó API-k közzétételéhez.  Mindkét SKU biztonságosan engedélyezi API Management szolgáltatás és a háttér közötti kapcsolatot. 

>[!NOTE]
>A fejlesztői SKU fejlesztésre és tesztelésre szolgál, míg a prémium SKU éles környezetben üzemel.

A API Management konfiguráció ugyanaz, mint a háttér-szolgáltatások, amelyek az Azure VMware megoldás virtuális gépei (VM) és a helyszínen futnak. Mindkét központi telepítés esetében API Management a virtuális IP-címet (VIP) a terheléselosztó végpontján konfigurálja, ha a háttér-kiszolgáló egy NSX-Load Balancer mögé kerül az Azure VMware megoldásban. 


## <a name="external-deployment"></a>Külső üzembe helyezés

Egy külső telepítés közzéteszi a külső felhasználók által a nyilvános végpont használatával felhasznált API-kat. A fejlesztők és a DevOps mérnökök a Azure Portal vagy a PowerShell, valamint a API Management fejlesztői portál segítségével kezelhetik az API-kat.

A külső telepítési diagramon a teljes folyamat és az érintett szereplők láthatók (fent láthatók). A szereplők a következők:

- **Rendszergazda (k):** A rendszergazda vagy a DevOps csapatot jelöli, amely az Azure VMware-megoldást az Azure Portal-és Automation-mechanizmusok, például a PowerShell vagy az Azure DevOps segítségével kezeli.

- **Felhasználók:**  A kitett API-k fogyasztókat jelöli, és az API-kat használó felhasználókat és szolgáltatásokat képviseli.

A forgalom áthalad API Management-példányon, amely elkerüli a háttér-szolgáltatásokat, és csatlakoztatva van a hub virtuális hálózathoz. A ExpressRoute-átjáró átirányítja a forgalmat a ExpressRoute Global Reach csatornára, és elér egy NSX Load Balancer a bejövő forgalmat a különböző háttér-szolgáltatási példányokra osztja.

API Management rendelkezik Azure nyilvános API-val, és az Azure DDOS Protection szolgáltatás aktiválása ajánlott. 

:::image type="content" source="media/api-management/external-deployment.png" alt-text="Külső üzembe helyezés – Azure VMware-megoldás API Management":::


## <a name="internal-deployment"></a>Belső telepítés

A belső telepítés a belső felhasználók vagy rendszerek által felhasznált API-kat teszi közzé. A DevOps-csapat és az API-fejlesztők ugyanazokat a felügyeleti eszközöket és fejlesztői portált használják, mint a külső telepítésben.

A belső telepítések az [Azure Application Gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md) használatával létrehozhatók egy nyilvános és biztonságos VÉGPONT az API-hoz.  Az átjáró képességei olyan hibrid központi telepítés létrehozására használhatók, amely különböző forgatókönyveket tesz lehetővé.  

* Ugyanazt a API Management erőforrást használja a belső és külső felhasználók általi felhasználáshoz.

* Egyetlen API Management erőforrással rendelkezik, és a külső fogyasztók számára elérhető API-k egy részhalmazát kell megadni.

* A nyilvános internetről be-és kikapcsolhatja a API Management hozzáférésének egyszerű módját.

Az alábbi üzembe helyezési ábrán láthatók a belső és a külső felhasználók, valamint az azonos vagy a különböző API-khoz hozzáférő típusok.

Belső telepítés esetén az API-k ugyanazon API Management-példányon lesznek elérhetők. A API Management előtt Application Gateway az Azure webalkalmazás-tűzfal (WAF) funkciójának aktiválása aktiválva lesz. A rendszer emellett a HTTP-figyelők és a forgalom szűrésére szolgáló szabályok készletét is telepíti, így csak az Azure VMware-megoldáson futó háttér-szolgáltatások egy részhalmazát teszi elérhetővé.


* A belső forgalmi útvonalak a ExpressRoute-átjárón keresztül Azure Firewall, majd API Management, közvetlenül vagy forgalmi szabályokon keresztül.   

* A külső forgalom az Azure-ba Application Gatewayon keresztül lép be, amely a API Management külső védelmi rétegét használja.


:::image type="content" source="media/api-management/internal-deployment.png" alt-text="Belső üzembe helyezés – Azure VMware-megoldás API Management" lightbox="media/api-management/internal-deployment.png":::
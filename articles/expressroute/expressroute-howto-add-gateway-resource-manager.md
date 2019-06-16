---
title: 'Az expressroute-hoz, adja hozzá egy virtuális hálózati átjárót egy virtuális hálózathoz: PowerShell: Azure | Microsoft Docs'
description: Ez a cikk segít egy már létrehozott Resource Manager virtuális hálózathoz, az ExpressRoute virtuális hálózati átjáró hozzáadása.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 02/21/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 6ab701adcc2122270c607604f222ac5edbceeb37
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66730504"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Az ExpressRoute virtuális hálózati átjáróinak konfigurálása a PowerShell-lel
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasszikus – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Videó – Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Ez a cikk segít hozzáadása, átméretezése és egy már meglévő virtuális hálózat (VNet) virtuális hálózati átjáró eltávolítása. A konfiguráció lépései a alkalmazni a Resource Manager üzemi modell használatával egy ExpressRoute-konfigurációt a létrehozott virtuális hálózatok. További információkért lásd: [kapcsolatos az ExpressRoute virtuális hálózati átjárók](expressroute-about-virtual-network-gateways.md).

## <a name="before-beginning"></a>Mielőtt hozzálát

### <a name="working-with-powershell"></a>A PowerShell használata

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloud-shell-powershell-about.md)]

### <a name="configuration-reference-list"></a>Konfigurációs referencialistája

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>További lépések
Miután létrehozta a virtuális hálózati átjáró, kapcsolat a virtuális hálózat csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz. Lásd: [virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoport](expressroute-howto-linkvnet-arm.md).

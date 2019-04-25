---
title: 'Az ExpressRoute - klasszikus virtuális hálózati átjáró konfigurálása: Azure PowerShell-lel |} A Microsoft Docs'
description: Klasszikus üzemi modellben a virtuális hálózati átjáró konfigurálása virtuális hálózathoz a PowerShell használatával egy ExpressRoute-konfiguráció modell.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 11/05/2018
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 101e03d07a15e9058ef236a575251b052017db32
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60365149"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell-classic"></a>PowerShell (klasszikus) használatával ExpressRoute virtuális hálózati átjáró konfigurálása
> [!div class="op_single_selector"]
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasszikus – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Videó – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Ez a cikk végigvezeti a lépéseket adja hozzá, átméretezése és egy már meglévő virtuális hálózat (VNet) virtuális hálózati átjáró eltávolítása. Ez a konfiguráció lépései a kifejezetten a használatával létrehozott virtuális hálózatok vannak a **klasszikus üzemi modell** és a egy ExpressRoute-konfigurációban használni kívánt. 

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Tudnivalók az Azure üzembe helyezési modelljeiről**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>Mielőtt hozzálát
Győződjön meg arról, hogy telepítette-e ehhez a konfigurációhoz szükséges az Azure PowerShell-parancsmagok (1.0.2-es vagy újabb). Ha még nem telepítette a parancsmagok, szüksége lesz erre a konfigurációs lépések megkezdése előtt. Azure PowerShell telepítésével kapcsolatos további információkért lásd: [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/overview).

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>További lépések
Miután létrehozta a virtuális hálózati átjáró, kapcsolat a virtuális hálózat csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz. Lásd: [virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoport](expressroute-howto-linkvnet-classic.md).


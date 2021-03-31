---
title: 'Azure ExpressRoute: átjáró hozzáadása VNet: klasszikus'
description: VNet-átjáró konfigurálása klasszikus üzemi modell VNet a PowerShell használatával egy ExpressRoute-konfigurációhoz.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/06/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 0f42a7d030f45be1b96728fdcac7a13cf797ed34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89396403"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell-classic"></a>ExpressRoute virtuális hálózati átjáró konfigurálása a PowerShell (klasszikus) használatával
> [!div class="op_single_selector"]
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasszikus – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Videó – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Ebből a cikkből megtudhatja, hogyan adhat hozzá, méretezheti át és távolíthat el egy virtuális hálózati (VNet-) átjárót egy már meglévő VNet. A konfiguráció lépései kifejezetten a **klasszikus üzemi modellel** létrehozott és a ExpressRoute-konfigurációban használt virtuális hálózatok vonatkoznak. 

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Tudnivalók az Azure üzembehelyezési modellekről**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>Mielőtt hozzálát
Ellenőrizze, hogy telepítette-e az ehhez a konfigurációhoz szükséges Azure PowerShell-parancsmagokat.

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>Következő lépések
Miután létrehozta a VNet-átjárót, összekapcsolhatja a VNet egy ExpressRoute-áramkörrel. Lásd: [Virtual Network csatolása ExpressRoute-áramkörhöz](expressroute-howto-linkvnet-classic.md).


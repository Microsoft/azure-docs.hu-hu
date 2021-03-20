---
title: Örökölt közvetlen társítás átalakítása Azure-erőforrásra a PowerShell használatával
titleSuffix: Azure
description: Örökölt közvetlen társítás átalakítása Azure-erőforrásra a PowerShell használatával
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d3e7cdf11e1e1e033b4e72b9579d8c63b28e6c88
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89071686"
---
# <a name="convert-a-legacy-direct-peering-to-an-azure-resource-by-using-powershell"></a>Örökölt közvetlen társítás átalakítása Azure-erőforrásra a PowerShell használatával

Ez a cikk bemutatja, hogyan alakíthat át egy meglévő örökölt közvetlen társítást egy Azure-erőforrásra PowerShell-parancsmagok használatával.

Ha szeretné, ezt az útmutatót az Azure [Portalon](howto-legacy-direct-portal.md)végezheti el.

## <a name="before-you-begin"></a>Előkészületek
* A konfigurálás megkezdése előtt tekintse át az [előfeltételeket](prerequisites.md) és a [közvetlen](walkthrough-direct-all.md) társítási útmutatót.

### <a name="work-with-azure-powershell"></a>Azure PowerShell használata
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-direct-peering-to-an-azure-resource"></a>Örökölt közvetlen társak konvertálása Azure-erőforrásra

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Jelentkezzen be az Azure-fiókjába, és válassza ki az előfizetését
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-a-legacy-direct-peering-for-conversion"></a><a name= get></a>Örökölt közvetlen társítás beszerzése átalakításhoz
Ebből a példából megtudhatja, hogyan szerezhet be egy örökölt közvetlen társas kapcsolatot a Seattle-alapú társas helyen.

```powershell
$legacyPeering = Get-AzLegacyPeering `
    -Kind Direct -PeeringLocation "Seattle"
$legacyPeering
```

Íme egy példa egy válaszra:
```powershell
Name                       :
Sku                        : Basic_Direct_Free
Kind                       : Direct
PeeringLocation            : Seattle
UseForPeeringService       : False
PeerAsn.Id                 :
Connection                 : ------------------------
PeeringDBFacilityId        : 71
SessionPrefixIPv4          : 4.71.156.72/30
PeerSessionIPv4Address     : 4.71.156.73
MicrosoftIPv4Address       : 4.71.156.74
SessionStateV4             : Established
MaxPrefixesAdvertisedV4    : 20000
SessionPrefixIPv6          : 2001:1900:2100::1e10/126
MaxPrefixesAdvertisedV6    : 2000
ConnectionState            : Active
BandwidthInMbps            : 0
ProvisionedBandwidthInMbps : 20000
Connection                 : ------------------------
PeeringDBFacilityId        : 71
SessionPrefixIPv4          : 4.68.70.140/30
PeerSessionIPv4Address     : 4.68.70.141
MicrosoftIPv4Address       : 4.68.70.142
SessionStateV4             : Established
MaxPrefixesAdvertisedV4    : 20000
SessionPrefixIPv6          : 2001:1900:4:3::cc/126
PeerSessionIPv6Address     : 2001:1900:4:3::cd
MicrosoftIPv6Address       : 2001:1900:4:3::ce
SessionStateV6             : Established
MaxPrefixesAdvertisedV6    : 2000
ConnectionState            : Active
BandwidthInMbps            : 0
ProvisionedBandwidthInMbps : 20000
ProvisioningState          : Succeeded
```

### <a name="convert-a-legacy-direct-peering"></a>Örökölt közvetlen társak konvertálása

&nbsp;
> [!IMPORTANT]
> Ha örökölt társítást alakít át egy Azure-erőforrásra, a módosítások nem támogatottak. &nbsp;

Ezzel a paranccsal egy örökölt közvetlen társat alakíthat át egy Azure-erőforrásra:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleDirectPeering" `
    -ResourceGroupName "PeeringResourceGroup" `

```

Íme egy példa egy válaszra:

```powershell
Name                 : SeattleDirectPeering
Sku.Name             : Basic_Direct_Free
Kind                 : Direct
Connections          : {11, 11}
PeerAsn.Id           : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/{asnNumber}
UseForPeeringService : False
PeeringLocation      : Seattle
ProvisioningState    : Succeeded
Location             : centralus
Id                   : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleDirectPeering
Type                 : Microsoft.Peering/peerings
Tags                 : {}
```

## <a name="additional-resources"></a>További források
Az összes paraméter részletes leírását a következő parancs futtatásával érheti el:

```powershell
Get-Help Get-AzPeering -detailed
```

További információ: [internetes peering GYIK](faqs.md).

## <a name="next-steps"></a>Következő lépések

* [Közvetlen társrendszer létrehozása vagy módosítása a PowerShell használatával](howto-direct-powershell.md)

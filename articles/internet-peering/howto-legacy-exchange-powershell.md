---
title: Örökölt Exchange-társ konvertálása Azure-erőforrásra a PowerShell használatával
titleSuffix: Azure
description: Örökölt Exchange-társ konvertálása Azure-erőforrásra a PowerShell használatával
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: acc32f4916f5f7f8fe22eebdd1e72db297cac94c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97590205"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-powershell"></a>Örökölt Exchange-társ konvertálása Azure-erőforrásra a PowerShell használatával

Ez a cikk bemutatja, hogyan alakíthat át egy meglévő örökölt Exchange-társat egy Azure-erőforrásra PowerShell-parancsmagok használatával.

Ha szeretné, ezt az útmutatót az Azure [Portalon](howto-legacy-exchange-portal.md)végezheti el.

## <a name="before-you-begin"></a>Előkészületek
* A konfigurálás megkezdése előtt tekintse át az [előfeltételeket](prerequisites.md) és az [Exchange](walkthrough-exchange-all.md) -társítási útmutatót.

### <a name="work-with-azure-powershell"></a>Azure PowerShell használata
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>Örökölt Exchange-társ konvertálása Azure-erőforrásra

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Jelentkezzen be az Azure-fiókjába, és válassza ki az előfizetését
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-legacy-exchange-peering-for-conversion"></a><a name= get></a>Örökölt Exchange-társítás beolvasása átalakításhoz
Ebből a példából megtudhatja, hogyan szerezheti be az örökölt Exchange-kapcsolatot a Seattle-alapú társas helyen:

```powershell
$legacyPeering = Get-AzLegacyPeering -Kind Exchange -PeeringLocation "Seattle"
$legacyPeering
```

A válasz az alábbi példához hasonlóan néz ki:
```powershell
    Kind                     : Exchange
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```

### <a name="convert-legacy-peering"></a>Örökölt társak konvertálása
Ezzel a paranccsal átalakíthatja az örökölt Exchange-társítást egy Azure-erőforrásra:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup"

```

&nbsp;
> [!IMPORTANT] 
> Ha az örökölt társ-összevonást egy Azure-erőforrásra konvertálja, a módosítások nem támogatottak.
&nbsp;

Ez a példa a végpontok közötti kiépítés sikeres befejezését mutatja be:

```powershell
    Name                     : SeattleExchangePeering
    Kind                     : Exchange
    Sku                      : Basic_Exchange_Free
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```
## <a name="additional-resources"></a>További források
Az összes paraméter részletes leírását a következő parancs futtatásával érheti el:

```powershell
Get-Help Get-AzPeering -detailed
```
További információ: [internetes peering GYIK](faqs.md).

## <a name="next-steps"></a>Következő lépések

* [Exchange-társ létrehozása vagy módosítása a PowerShell használatával](howto-exchange-powershell.md)

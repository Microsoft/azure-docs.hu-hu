---
title: 'Sikertelen áramkör alaphelyzetbe állítása – ExpressRoute: PowerShell: Azure | Microsoft Docs'
description: Ez a cikk segítséget nyújt egy hibás állapotú ExpressRoute-áramkör alaphelyzetbe állításához.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/07/2021
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 84c8275b7a7257530a735e8612047ef42e2f8a7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98011339"
---
# <a name="reset-a-failed-expressroute-circuit"></a>Állítsa alaphelyzetbe a hibás ExpressRoute-kapcsolatcsoportot

Ha egy ExpressRoute-áramkörön végzett művelet nem fejeződik be sikeresen, az áramkör "sikertelen" állapotba kerülhet. Ez a cikk segítséget nyújt egy sikertelen Azure ExpressRoute-áramkör alaphelyzetbe állításához.

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="reset-a-circuit"></a>Áramkör alaphelyzetbe állítása

1. Telepítse az Azure Resource Manager PowerShell-parancsmagjainak legújabb verzióját. További információk: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-az-ps).

2. Nyissa meg emelt szintű jogosultságokkal a PowerShell konzolt, és csatlakozzon a fiókjához. A következő példa segít a kapcsolódásban:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
3. Ha több Azure-előfizetéssel is rendelkezik, ellenőrizze a fiók előfizetéseit.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
4. Válassza ki a használni kívánt előfizetést.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
5. Futtassa a következő parancsokat egy hibás állapotú áramkör alaphelyzetbe állításához:

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

Az áramkörnek most kifogástalannak kell lennie. Nyisson meg egy támogatási jegyet a [Microsoft támogatási](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) szolgálatával, ha az áramkör még sikertelen állapotban van.

## <a name="next-steps"></a>Következő lépések

Ha továbbra is problémákat tapasztal, nyisson meg egy támogatási jegyet a [Microsoft ügyfélszolgálatával](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .

---
title: Webalkalmazási tűzfal szabályzatának hozzárendelése meglévő Azure-Application Gateway
description: Megtudhatja, hogyan társíthat egy webalkalmazási tűzfal-házirendet egy meglévő Azure-Application Gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: 1ed2e0cf8cc8cd841d8779462d62ba4852774a3a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "74083905"
---
# <a name="associate-a-waf-policy-with-an-existing-application-gateway"></a>WAF szabályzat hozzárendelése meglévő Application Gateway

A Azure PowerShell használatával [létrehozhat egy WAF-házirendet](create-waf-policy-ag.md), de lehet, hogy már rendelkezik Application Gateway, és csak egy WAF-szabályzatot szeretne hozzárendelni hozzá. Ebben a cikkben a következő műveleteket hajtja végre: létrehoz egy WAF házirendet, és hozzárendeli egy már meglévő Application Gatewayhoz. 

1. Szerezze be a Application Gateway és a tűzfal házirendjét. Ha nem rendelkezik meglévő tűzfal-házirenddel, tekintse meg a 2. lépést. 

   ```azurepowershell-interactive
      Connect-AzAccount
      Select-AzSubscription -Subscription "<sub id>"`

      #Get Application Gateway and existing policy object or create new`
      $gw = Get-AzApplicationGateway -Name <Waf v2> -ResourceGroupName <RG name>`
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```

2. Választható Hozzon létre egy tűzfal-házirendet.

   ```azurepowershell-interactive
      New-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>'
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```
   > [!NOTE]
   > Ha úgy hozza létre ezt a WAF-szabályzatot, hogy egy WAF-konfigurációból egy WAF-házirendre váltson át, akkor a házirendnek a régi konfiguráció pontos másolatát kell használnia. Ez azt jelenti, hogy minden kizárási, egyéni szabály, letiltott szabálykészlet stb. pontosan meg kell egyeznie a WAF konfigurációban találhatótal.
3. Választható A WAF házirendet az igényeinek megfelelően konfigurálhatja. Ez magában foglalja az egyéni szabályokat, a szabályok/szabályok letiltását, a kizárásokat, a fájlfeltöltés korlátozásának beállítását stb. Ha kihagyja ezt a lépést, az összes alapértelmezett érték lesz kiválasztva. 
   
4. Mentse a szabályzatot, és csatolja a Application Gatewayhoz. 
   
   ```azurepowershell-interactive
      #Save the policy itself
      Set-AzApplicationGatewayFirewallPolicy -InputObject $policy`
   
      #Attach the policy to an Application Gateway
      $gw.FirewallPolicy = $policy`
   
      #Save the Application Gateway
      Set-AzApplicationGateway -ApplicationGateway $gw`
   ```

## <a name="next-steps"></a>Következő lépések
[További információ az egyéni szabályokról.](configure-waf-custom-rules.md)

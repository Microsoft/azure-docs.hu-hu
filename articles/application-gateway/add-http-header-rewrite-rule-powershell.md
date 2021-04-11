---
title: HTTP-fejlécek újraírása az Azure-ban Application Gateway
description: Ez a cikk a HTTP-fejlécek átírását mutatja be az Azure Application Gatewayban Azure PowerShell használatával
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: b674bdf59959715b666c521c0d0631f86f71b1d3
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106093724"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>HTTP-kérelem és válasz-fejlécek újraírása az Azure Application Gateway-Azure PowerShell

Ez a cikk azt ismerteti, hogyan használható a Azure PowerShell egy [Application Gateway v2 SKU](./application-gateway-autoscaling-zone-redundant.md) -példány konfigurálásához a HTTP-fejlécek újraírásához a kérelmekben és a válaszokban.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="before-you-begin"></a>Előkészületek

- A cikk lépéseinek elvégzéséhez a Azure PowerShell helyileg kell futtatnia. Emellett az az modul Version 1.0.0 vagy újabb verziójára van szükség. Futtassa `Import-Module Az` a parancsot, majd `Get-Module Az` határozza meg a telepített verziót. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. A PowerShell-verzió ellenőrzése után futtassa az `Login-AzAccount` parancsot az Azure-hoz való kapcsolódáshoz.
- Rendelkeznie kell egy Application Gateway v2 SKU-példánnyal. Az Újraírási fejlécek nem támogatottak a v1 SKU-ban. Ha nem rendelkezik a v2 SKU-val, hozzon létre egy [Application Gateway v2 SKU](./tutorial-autoscale-ps.md) -példányt a Kezdés előtt.

## <a name="create-required-objects"></a>Szükséges objektumok létrehozása

A HTTP-fejléc újraírásának konfigurálásához el kell végeznie ezeket a lépéseket.

1. Hozza létre a HTTP-fejléc újraírásához szükséges objektumokat:

   - **RequestHeaderConfiguration**: az újraírni kívánt kérelem fejléc mezőinek, valamint a fejlécek új értékének megadására szolgál.

   - **ResponseHeaderConfiguration**: az újraírni kívánt válasz fejléc mezőinek, valamint a fejlécek új értékének megadására szolgál.

   - **ActionSet**: a korábban megadott kérelem és válasz fejlécek konfigurációit tartalmazza.

   - **Feltétel**: opcionális konfiguráció. Az Újraírási feltételek kiértékelik a HTTP (S) kérelmek és válaszok tartalmát. Az újraírás művelet akkor fordul elő, ha a HTTP (S) kérelem vagy válasz megfelel az Újraírási feltételnek.

     Ha egynél több feltételt társít egy művelethez, a művelet csak akkor lép fel, ha az összes feltétel teljesül. Más szóval a művelet logikai és művelet.

   - **RewriteRule**: több Újraírási művelet/Újraírási feltétel kombinációt tartalmaz.

   - **RuleSequence**: opcionális konfiguráció, amely segít meghatározni, hogy a rendszer milyen sorrendben hajtsa végre az Újraírási szabályokat. Ez a konfiguráció akkor hasznos, ha több Újraírási szabály található egy Újraírási készletben. Az alacsonyabb szabálykészlet-értékkel rendelkező Újraírási szabály először fut. Ha ugyanazokat a szabálygyűjtemény-értékeket rendeli hozzá két Újraírási szabályhoz, a végrehajtás sorrendje nem determinisztikus.

     Ha nem adja meg explicit módon a RuleSequence, a rendszer az alapértelmezett 100-as értéket állítja be.

   - **RewriteRuleSet**: több Újraírási szabályt tartalmaz, amelyek egy kérelem-útválasztási szabályhoz lesznek társítva.

2. Csatolja a RewriteRuleSet egy útválasztási szabályhoz. Az Újraírási konfiguráció a forrás figyelőhöz van csatolva az útválasztási szabály segítségével. Alapszintű útválasztási szabály használata esetén a fejléc-Újraírási konfiguráció egy forrás-figyelőhöz van társítva, és a globális fejléc újraírása. Elérésiút-alapú útválasztási szabály használata esetén a fejléc-Újraírási konfiguráció az URL-cím elérési útja alapján van definiálva. Ebben az esetben csak a hely adott elérési útja területére vonatkozik.

Több, a HTTP-fejléc Újraírási készletét is létrehozhatja, és az egyes újraírásokat több figyelőre alkalmazhatja. De csak egy Újraírási készletet alkalmazhat egy adott figyelőre.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>A HTTP-fejléc Újraírási szabály konfigurációjának meghatározása

Ebben a példában egy átirányítási URL-címet módosítunk a HTTP-válasz Location (hely) fejlécének újraírásával, amikor a hely fejléce a azurewebsites.net mutató hivatkozást tartalmaz. Ehhez hozzáadunk egy feltételt, amely kiértékeli, hogy a válaszban található azurewebsites.net tartalmaz-e. Ezt a mintát fogjuk használni `(https?)://.*azurewebsites.net(.*)$` . És `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` a fejléc értékeként fogjuk használni. Ez az érték a *azurewebsites.net* és a *contoso.com* értéket fogja lecserélni a Location fejlécben.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet -Condition $condition
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-the-configuration-of-your-application-gateway"></a>Az Application Gateway konfigurációjának beolvasása

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>A kérelem-útválasztási szabály konfigurációjának beolvasása

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Az Application Gateway frissítése a HTTP-fejlécek újraírására szolgáló konfigurációval

Ebben a példában az Újraírási készlet azonnal társítva lesz egy alapszintű útválasztási szabályhoz. Elérésiút-alapú útválasztási szabály esetén a társítás alapértelmezés szerint nem engedélyezhető. Az Újraírási készlet engedélyezhető a portálon keresztül alkalmazni kívánt elérési utak ellenőrzésével vagy egy URL-elérésiút-hozzárendelési konfiguráció megadásával, amely megadja a RewriteRuleSet az egyes elérésiút-beállítások alapján.  

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name $rewriteRuleSet.Name  -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name $reqRoutingRule.Name -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>Újraírási szabály törlése

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "LocationHeaderRewrite" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>Következő lépések

A gyakori használati esetek beállításával kapcsolatos további tudnivalókért tekintse meg a [gyakori fejléc-Újraírási forgatókönyvek](./rewrite-http-headers.md)című témakört.
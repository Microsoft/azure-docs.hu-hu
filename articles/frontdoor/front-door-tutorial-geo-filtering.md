---
title: Oktatóanyag – geo-szűrési WAF házirend konfigurálása – Azure bejárati ajtó
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy geo-szűrési WAF szabályzatot, és hogyan társíthatja a házirendet a meglévő előtér-gazdagéphez.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: duau
ms.openlocfilehash: f9b5869f7dd472c50ffb2c1c0dce765200f53882
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91324026"
---
# <a name="tutorial-how-to-set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Oktatóanyag: Geo-szűrési WAF szabályzat beállítása a bejárati ajtóhoz
Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre egy egyszerű geoszűrési szabályzatot, és társíthatja azt a meglévő előtérbeli Front Door-gazdagéphez az Azure PowerShell-lel. Ez a minta geo-szűrési házirend letiltja az összes többi országból/régiótól érkező kéréseket Egyesült Államok kivételével.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> - A földrajzi szűrés egyeztetési feltételének meghatározása.
> - Geo-szűrési egyeztetési feltétel hozzáadása egy szabályhoz.
> - Szabályok hozzáadása egy házirendhez.
> - Csatolja a WAF szabályzatot a FrontDoor frontend-gazdagéphez.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek
* Mielőtt elkezdi a Geo-szűrő házirend beállítását, állítsa be a PowerShell-környezetet, és hozzon létre egy bejárati profilt.
* Hozzon létre egy bejárati ajtót a következő témakörben ismertetett utasításokat követve [: első ajtós profil létrehozása](quickstart-create-front-door.md).

## <a name="define-geo-filtering-match-condition"></a>Geo-szűrési egyeztetési feltétel meghatározása

Hozzon létre egy minta egyeztetési feltételt, amely kiválasztja a "US" helyett a [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) paramétert az egyeztetési feltétel létrehozásakor. [Itt](front-door-geo-filtering.md)jelennek meg az ország/régió-hozzárendelések két betűs országa/régiója.

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>A geoszűrési egyeztetési feltétel hozzáadása egy szabályhoz egy művelettel és egy prioritással

Hozzon létre egy CustomRule objektumot `nonUSBlockRule` az egyeztetési feltétel, egy művelet és egy prioritás alapján a [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject)használatával.  Egy CustomRule (egyéni szabály) objektum több MatchCondition (egyeztetési feltétel) objektummal is rendelkezhet.  Ebben a példában a Művelet a Blokkolás értékre, a Prioritás pedig az 1 értékre, a legmagasabb prioritásra van állítva.

```
$nonUSBlockRule = New-AzFrontDoorWafCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```
## <a name="add-rules-to-a-policy"></a>Szabályok hozzáadása egy szabályzathoz
Keresse meg annak az erőforráscsoportnak a nevét, amely az első ajtó profilját tartalmazza a használatával `Get-AzResourceGroup` . Ezután hozzon létre egy olyan `geoPolicy` házirend-objektumot, amely a `nonUSBlockRule` [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) -t használja a megadott erőforráscsoporthoz, amely az előtérben profilt tartalmazza. Meg kell adnia a Geo-szűrési házirend egyedi nevét. 

Az alábbi példa az erőforráscsoport nevét használja *FrontDoorQS_rg0* azzal a feltételezéssel, hogy létrehozta a bevezető ajtó profilt a rövid útmutatóban található utasításokkal [: hozzon létre egy bejárati ajtót](quickstart-create-front-door.md) . Az alábbi példában cserélje le a *geoPolicyAllowUSOnly* szabályzat nevét egy egyedi házirend-névre.

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName FrontDoorQS_rg0 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```
## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>WAF házirend csatolása egy előtér-gazdagéphez
Csatolja a WAF házirend-objektumot a meglévő előtér-gazdagéphez, és frissítse a bejárati ajtó tulajdonságait. 

Ehhez először a [Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor)használatával olvassa be az előtérben lévő objektumot. 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName FrontDoorQS_rg0
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```
Ezután állítsa a WebApplicationFirewallPolicyLink tulajdonságot a `geoPolicy` [set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor)resourceId.

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Csak egyszer kell beállítania a WebApplicationFirewallPolicyLink tulajdonságot, ha egy WAF-szabályzatot szeretne csatlakoztatni egy előtér-előtérben lévő gazdagéphez. A következő házirend-frissítéseket a rendszer automatikusan alkalmazza a előtér-gazdagépre.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben egy WAF-házirendhez társított geo-szűrési szabályt konfigurált. Ezután összekapcsolta a szabályzatot egy előtér-állomáshoz. Ha már nincs szüksége a Geo-szűrési szabályra vagy a WAF házirendre, először el kell távolítania a házirendet a előtér-gazdagépről a WAF-házirend törlése előtt.

:::image type="content" source="media/front-door-geo-filtering/front-door-disassociate-policy.png" alt-text="WAF házirend hozzárendelésének megszüntetése":::

## <a name="next-steps"></a>Következő lépések

Ha meg szeretné tudni, hogyan konfigurálhat egy webalkalmazási tűzfalat az előtérben, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Webalkalmazási tűzfal a Front Dooron](front-door-waf.md)

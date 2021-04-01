---
title: WAF-korlátozási szabály konfigurálása a bejárati ajtóhoz – Azure PowerShell
description: Megtudhatja, hogyan konfigurálhat egy kamatláb-korlátozási szabályt egy meglévő bejárati végponthoz.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 02/26/2020
ms.author: victorh
ms.openlocfilehash: 23b893bad591af5f1e923b68e8d30453f859792b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94563477"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>Webalkalmazási tűzfal sebességének korlátozási szabályának konfigurálása Azure PowerShell használatával
Az Azure-beli előtérben lévő Azure webalkalmazási tűzfal (WAF) díjszabási szabálya az ügyfelek által az egyperces időtartam alatt engedélyezett kérelmek számát szabályozza.
Ez a cikk bemutatja, hogyan konfigurálhat egy WAF-korlátozási szabályt, amely meghatározza az ügyfelek által az Azure PowerShell használatával az URL-címben szereplő */promo* tartalmazó webalkalmazásoknak engedélyezett kérelmek számát.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Minden ügyfél IP-címére érvényesek a díjszabási korlátok. Ha több ügyfél fér hozzá a bejárati ajtóhoz különböző IP-címekről, a rendszer a saját díjszabási korlátját alkalmazza.

## <a name="prerequisites"></a>Előfeltételek
Mielőtt elkezdi a díjszabási szabályzat beállítását, állítsa be a PowerShell-környezetet, és hozzon létre egy bejárati profilt.
### <a name="set-up-your-powershell-environment"></a>A PowerShell-környezet beállítása
Az Azure PowerShell olyan parancsmagok készletét kínálja, amelyek az [Azure Resource Manager](../../azure-resource-manager/management/overview.md) modellt használják az Azure-erőforrások kezeléséhez. 

Az [Azure PowerShellt](/powershell/azure/) telepítheti a helyi számítógépen és bármely PowerShell-munkamenetben használhatja. Kövesse az oldalon megjelenő utasításokat, és jelentkezzen be az Azure-beli hitelesítő adataival, és telepítse az az PowerShell-modult.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Kapcsolódás az Azure-hoz egy interaktív párbeszédablak a bejelentkezéshez
```
Connect-AzAccount

```
Mielőtt telepítené a bejárati modult, győződjön meg arról, hogy a PowerShellGet aktuális verziója telepítve van. Futtassa a következő parancsot, majd nyissa meg újra a PowerShellt.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Telepítés az. FrontDoor modul 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Bejárati ajtó profiljának létrehozása
Hozzon létre egy bevezető ajtót a következő témakörben ismertetett utasításokat követve [: első ajtós profil létrehozása](../../frontdoor/quickstart-create-front-door.md)

## <a name="define-url-match-conditions"></a>URL-egyeztetési feltételek meghatározása
Definiáljon egy URL-egyeztetési feltételt (az URL-cím/promo tartalmaz) a [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject)használatával.
A következő példa a *RequestUri* változó értékének megfelelő */promo* illeszkedik:

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>Egyéni díjszabási szabály létrehozása
Adja meg a díjszabási korlátot a [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject)használatával. A következő példában a korlát értéke 1000. Minden ügyféltől a promóciós oldalra érkező, a 1000-es időszakot meghaladó, egy percen belül érkező kéréseket a rendszer a következő percben letiltva

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorWafCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>Biztonsági házirend konfigurálása

Keresse meg annak az erőforráscsoportnak a nevét, amely az első ajtó profilját tartalmazza a használatával `Get-AzureRmResourceGroup` . Ezután állítson be egy biztonsági házirendet egyéni díjszabási szabályként a [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) használatával a megadott erőforráscsoport, amely tartalmazza az előtérben profilt.

Az alábbi példa a *myResourceGroupFD1* nevű erőforráscsoport-nevet használja azzal a feltételezéssel, hogy létrehozta a bevezető ajtót, amely a gyors üzembe helyezési útmutatóban szereplő utasítások alapján hozza létre a [bevezető](../../frontdoor/quickstart-create-front-door.md) ajtaját: [új AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)használatával.

```powershell-interactive
   $ratePolicy = New-AzFrontDoorWafPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>Házirend csatolása egy előtér-előtéri gazdagéphez
Csatolja a biztonsági házirend objektumot egy meglévő előtér-előtéri gazdagéphez, és frissítse a bejárati ajtó tulajdonságait. Először a [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) parancs használatával olvassa be az előtérben lévő objektumot.
Ezután állítsa be az előtér- *WebApplicationFirewallPolicyLink* tulajdonságot az előző lépésben létrehozott "$RatePolicy" *resourceId* a [set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) parancs használatával. 

Az alábbi példa a *myResourceGroupFD1* nevű erőforráscsoport-nevet használja azzal a feltételezéssel, hogy létrehozta a bevezető ajtaját a gyors útmutató [: Create a](../../frontdoor/quickstart-create-front-door.md) bejárati ajtóról szóló cikket. Emellett az alábbi példában cserélje le a $frontDoorName nevet a bejárati ajtó profiljának nevére. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> A *WebApplicationFirewallPolicyLink* tulajdonságot csak egyszer kell beállítania, hogy egy biztonsági szabályzatot egy előtér-előtérben csatoljon. A következő házirend-frissítéseket a rendszer automatikusan alkalmazza az előtérben.

## <a name="next-steps"></a>Következő lépések

- További információ a [bejárati ajtóról](../../frontdoor/front-door-overview.md).
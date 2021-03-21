---
title: Egyéni WAF-szabályok konfigurálása az Azure bejárati ajtóhoz & alapértelmezett szabálykészlet
description: Megtudhatja, hogyan konfigurálhat egy WAF házirendet egy meglévő bejárati végpontra vonatkozó egyéni és felügyelt szabályokból.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 9a0e262db9f5c37189a589eefc451a88dd5ea8c6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94563409"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>Webalkalmazási tűzfal házirend konfigurálása Azure PowerShell használatával

Az Azure webalkalmazási tűzfal (WAF) szabályzata meghatározza a szükséges ellenőrzéseket, amikor egy kérés érkezik a bejárati ajtón.
Ez a cikk bemutatja, hogyan konfigurálhat olyan WAF szabályzatot, amely néhány egyéni szabályt és az Azure által felügyelt alapértelmezett szabálykészlet használatát teszi lehetővé.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdi a díjszabási szabályzat beállítását, állítsa be a PowerShell-környezetet, és hozzon létre egy bejárati profilt.

### <a name="set-up-your-powershell-environment"></a>A PowerShell-környezet beállítása

Az Azure PowerShell olyan parancsmagok készletét kínálja, amelyek az [Azure Resource Manager](../../azure-resource-manager/management/overview.md) modellt használják az Azure-erőforrások kezeléséhez. 

Az [Azure PowerShellt](/powershell/azure/) telepítheti a helyi számítógépen és bármely PowerShell-munkamenetben használhatja. Kövesse az oldalon megjelenő utasításokat, és jelentkezzen be az Azure-beli hitelesítő adataival, és telepítse az az PowerShell-modult.

#### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

```
Connect-AzAccount

```
A Front Door-modul telepítése előtt győződjön meg arról, hogy telepítve van a PowerShellGet aktuális verziója. Futtassa az alábbi parancsot, majd nyissa meg a PowerShellt újból.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Telepítés az. FrontDoor modul 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Bejárati ajtó profiljának létrehozása

Hozzon létre egy bevezető ajtót a következő témakörben ismertetett utasításokat követve [: első ajtós profil létrehozása](../../frontdoor/quickstart-create-front-door.md)

## <a name="custom-rule-based-on-http-parameters"></a>Egyéni szabály http-paraméterek alapján

Az alábbi példa bemutatja, hogyan konfigurálhat egy egyéni szabályt két egyeztetési feltétellel a [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject)használatával. A kérések egy megadott helyről származnak a referrer által meghatározott módon, és a lekérdezési karakterlánc nem tartalmazza a "password" karaktert. 

```powershell-interactive
$referer = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorWafMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorWafCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>Egyéni szabály http-kérési módszer alapján

Hozzon létre egy szabályt, amely blokkolja a "PUT" metódust a [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) használatával, a következőképpen:

```powershell-interactive
$put = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorWafCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>Egyéni szabály létrehozása a méret megkötése alapján

Az alábbi példa egy olyan szabályt hoz létre, amely a 100 karakternél hosszabb URL-lel rendelkező kérelmeket blokkol Azure PowerShell használatával:
```powershell-interactive
$url = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorWafCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>Felügyelt alapértelmezett szabálykészlet hozzáadása

A következő példa létrehoz egy felügyelt alapértelmezett szabálykészlet Azure PowerShell használatával:
```powershell-interactive
$managedRules =  New-AzFrontDoorWafManagedRuleObject -Type DefaultRuleSet -Version 1.0
```
## <a name="configure-a-security-policy"></a>Biztonsági házirend konfigurálása

Keresse meg annak az erőforráscsoportnak a nevét, amely az első ajtó profilját tartalmazza a használatával `Get-AzResourceGroup` . Ezután állítson be egy biztonsági szabályzatot az előző lépésekben létrehozott szabályokkal az [új-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) használatával a megadott erőforráscsoporthoz, amely tartalmazza az előtérben profilt.

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorWafPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>Házirend csatolása egy előtér-előtéri gazdagéphez

Csatolja a biztonsági házirend objektumot egy meglévő előtér-előtéri gazdagéphez, és frissítse a bejárati ajtó tulajdonságait. Először a [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)paranccsal kérje le az előtérben lévő objektumot.
Ezután állítsa be az előtér- *WebApplicationFirewallPolicyLink* tulajdonságot az előző lépésben létrehozott "$myWAFPolicy $" *resourceId* a [set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)használatával. 

Az alábbi példa a *myResourceGroupFD1* nevű erőforráscsoport-nevet használja azzal a feltételezéssel, hogy létrehozta a bevezető ajtaját a gyors útmutató [: Create a](../../frontdoor/quickstart-create-front-door.md) bejárati ajtóról szóló cikket. Emellett az alábbi példában cserélje le a $frontDoorName nevet a bejárati ajtó profiljának nevére. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> A *WebApplicationFirewallPolicyLink* tulajdonságot csak egyszer kell beállítania, hogy egy biztonsági szabályzatot egy előtér-előtérben csatoljon. A következő házirend-frissítéseket a rendszer automatikusan alkalmazza az előtérben.

## <a name="next-steps"></a>Következő lépések

- További információ a [bejárati ajtóról](../../frontdoor/front-door-overview.md) 
- További információ a [WAF a bejárati ajtóval](afds-overview.md)
---
title: A webalkalmazási tűzfal (WAF) egyéni válaszának konfigurálása az Azure bejárati ajtóval
description: Megtudhatja, hogyan konfigurálhat egyéni hibakódot és üzenetet, ha WAF blokkol egy kérést.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 06/10/2020
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: 8fc6e71494df36cd6f823661b18e4a3d8ce2938c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94563681"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall-waf"></a>Egyéni válasz konfigurálása az Azure webalkalmazási tűzfal (WAF) számára

Ha a WAF egy egyeztetett szabály miatt blokkol egy kérést, alapértelmezés szerint egy 403-es állapotkódot ad vissza, amely **a kérésben blokkolt** üzenet. Az alapértelmezett üzenet tartalmazza a követési hivatkozási karakterláncot is, amely a kérelem [bejegyzéseihez](./waf-front-door-monitor.md) való hivatkozással használható.  A használati esethez beállíthat egy egyéni válasz állapotkódot és egy egyéni üzenetet, amely hivatkozási karakterláncot tartalmaz. Ez a cikk azt ismerteti, hogyan konfigurálható egy egyéni válasz oldal, ha a WAF egy kérést blokkol.

## <a name="configure-custom-response-status-code-and-message-use-portal"></a>Az egyéni válasz állapotának beállítása és az üzenet használata portál

A WAF-portálon a "házirend-beállítások" területen konfigurálhatja az egyéni válasz állapotkódot és törzsét.

:::image type="content" source="../media/waf-front-door-configure-custom-response-code/custom-response-settings.png" alt-text="WAF házirend-beállítások":::

A fenti példában megtartottuk a 403-as hibakódot, és egy rövid "Kérjük, vegye fel velünk a kapcsolatot" üzenet jelenik meg az alábbi képen látható módon:

:::image type="content" source="../media/waf-front-door-configure-custom-response-code/custom-response.png" alt-text="Egyéni válasz – példa":::

a (z) {{Azure-ref}} beilleszti az egyedi hivatkozási karakterláncot a válasz törzsében. Az érték megegyezik a és a naplók TrackingReference mezőjével `FrontdoorAccessLog` `FrontdoorWebApplicationFirewallLog` .

## <a name="configure-custom-response-status-code-and-message-use-powershell"></a>Az egyéni válasz állapotkód és az üzenet konfigurálása a PowerShell használatával

### <a name="set-up-your-powershell-environment"></a>A PowerShell-környezet beállítása

Az Azure PowerShell olyan parancsmagok készletét kínálja, amelyek az [Azure Resource Manager](../../azure-resource-manager/management/overview.md) modellt használják az Azure-erőforrások kezeléséhez. 

Az [Azure PowerShellt](/powershell/azure/) telepítheti a helyi számítógépen és bármely PowerShell-munkamenetben használhatja. Kövesse az oldalon megjelenő utasításokat, és jelentkezzen be az Azure-beli hitelesítő adataival, és telepítse az az PowerShell-modult.

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Kapcsolódás az Azure-hoz interaktív párbeszédablak a bejelentkezéshez

```
Connect-AzAccount
Install-Module -Name Az

```
Győződjön meg arról, hogy a PowerShellGet aktuális verziója telepítve van. Futtassa az alábbi parancsot, majd nyissa meg a PowerShellt újból.
```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>Telepítés az. FrontDoor modul 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-ban kapcsolódó erőforrásokat oszt ki egy erőforráscsoporthoz. Itt hozzunk létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup)használatával.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

### <a name="create-a-new-waf-policy-with-custom-response"></a>Új WAF-házirend létrehozása egyéni választal 

Az alábbi példa egy új WAF szabályzat létrehozását mutatja be, amely az egyéni válasz állapotkód 405 értékre van állítva, az üzenetet pedig **letiltotta.** a [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)

```azurepowershell
# WAF policy setting
New-AzFrontDoorWafPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 405 `
-CustomBlockResponseBody "<html><head><title>You are blocked.</title></head><body></body></html>"
```

Módosítsa egy meglévő WAF-házirend egyéni válasz kódját vagy válasz törzsének beállításait az [Update-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy)használatával.

```azurepowershell
# modify WAF response code
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 403
```

```azurepowershell
# modify WAF response body
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-CustomBlockResponseBody "<html><head><title>Forbidden</title></head><body>{{azure-ref}}</body></html>"
```

## <a name="next-steps"></a>Következő lépések
- További információ a [webalkalmazási tűzfalról az Azure bejárati ajtóval](../afds/afds-overview.md)
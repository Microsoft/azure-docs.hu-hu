---
title: Testre szabhatja a webalkalmazási tűzfalszabályok az Azure Application Gatewayjel – PowerShell |} A Microsoft Docs
description: Ez a cikk webalkalmazási tűzfalszabályok az Application Gatewayen PowerShell-lel testreszabása ismertetése.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: victorh
ms.openlocfilehash: dfcd82a17a399f213f5c4e32326a8995d26e8458
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2018
ms.locfileid: "51218269"
---
# <a name="customize-web-application-firewall-rules-through-powershell"></a>Webalkalmazási tűzfalszabályok Powershellen keresztül testreszabása

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Azure CLI](application-gateway-customize-waf-rules-cli.md)

Az Azure Application Gateway webalkalmazási tűzfala (WAF) védelmet kínál a webes alkalmazásokhoz. Ezek a védelmi által az Open Web Application Security Project (OWASP) Core szabály beállítása (CRS) vannak megadva. Néhány szabály által kiváltott hamis pozitív okozhat, és valódi adatforgalmat. Ebből kifolyólag az Application Gateway lehetővé teszi a csoportok és a szabályok testreszabása. Az adott csoportok és a szabályok további információkért lásd: [webes alkalmazás tűzfal CRS-szabálycsoportjainak és szabályok listája](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>A szabály csoportok megtekintése és szabályok

A következő hitelesítésikód-példák bemutatják, hogyan-szabályok és konfigurálható a WAF-kompatibilis application gatewayen megtekintéséhez.

### <a name="view-rule-groups"></a>A szabály csoportok megtekintése

Az alábbi példa bemutatja, hogyan szabály csoportok megtekintése:

```powershell
Get-AzureRmApplicationGatewayAvailableWafRuleSets
```

A következő kimenet az előző példából csonkolt választ:

```
OWASP (Ver. 3.0):

    General:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            200004     Possible Multipart Unmatched Boundary.

    REQUEST-911-METHOD-ENFORCEMENT:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            911011     Rule 911011
            911012     Rule 911012
            911100     Method is not allowed by policy
            911013     Rule 911013
            911014     Rule 911014
            911015     Rule 911015
            911016     Rule 911016
            911017     Rule 911017
            911018     Rule 911018

    REQUEST-913-SCANNER-DETECTION:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            913011     Rule 913011
            913012     Rule 913012
            913100     Found User-Agent associated with security scanner
            913110     Found request header associated with security scanner
            913120     Found request filename/argument associated with security scanner
            913013     Rule 913013
            913014     Rule 913014
            913101     Found User-Agent associated with scripting/generic HTTP client
            913102     Found User-Agent associated with web crawler/bot
            913015     Rule 913015
            913016     Rule 913016
            913017     Rule 913017
            913018     Rule 913018

            ...        ...
```

## <a name="disable-rules"></a>Szabályok letiltása

A következő példa letiltja a szabályok `911011` és `911012` egy application gateway-en:

```powershell
$disabledrules=New-AzureRmApplicationGatewayFirewallDisabledRuleGroupConfig -RuleGroupName REQUEST-911-METHOD-ENFORCEMENT -Rules 911011,911012
Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -ApplicationGateway $gw -Enabled $true -FirewallMode Detection -RuleSetVersion 3.0 -RuleSetType OWASP -DisabledRuleGroups $disabledrules
Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

## <a name="next-steps"></a>További lépések

Miután konfigurálta a letiltott szabályok, megismerheti a WAF-naplók megtekintéséhez. További információkért lásd: [Application Gateway-diagnosztika](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png

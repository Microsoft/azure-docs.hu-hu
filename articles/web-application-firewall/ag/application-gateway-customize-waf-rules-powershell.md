---
title: Szabályok testreszabása a PowerShell használatával
titleSuffix: Azure Web Application Firewall
description: Ez a cikk azt ismerteti, hogyan szabható testre a webalkalmazási tűzfalszabályok a Application Gateway a PowerShell használatával.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 55eea15da8c3a10b0421ff1576082d6b42fc7c56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "74048513"
---
# <a name="customize-web-application-firewall-rules-using-powershell"></a>Webalkalmazási tűzfalszabályok testreszabása a PowerShell használatával

Az Azure Application Gateway webalkalmazási tűzfal (WAF) védelmet biztosít a webalkalmazások számára. Ezeket a védelmet a nyílt Web Application Security Project (OWASP) alapszintű szabálykészlet (CRS) adja meg. Bizonyos szabályok hamis pozitív és valós adatforgalom blokkolására vezethetnek. Emiatt a Application Gateway lehetővé teszi a szabályok csoportjának és szabályainak testreszabását. További információ az adott szabályok csoportjairól és szabályairól: a [webalkalmazási TŰZFAL CRS-szabályait tartalmazó csoportok és szabályok listája](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Szabálykészlet és szabályok megtekintése

A következő példák azt mutatják be, hogyan tekinthetők meg a WAF-kompatibilis Application Gateway-ben konfigurálható szabályok és szabálykészlet.

### <a name="view-rule-groups"></a>Szabálykészlet megtekintése

Az alábbi példa bemutatja, hogyan lehet megtekinteni a szabályok csoportjait:

```powershell
Get-AzApplicationGatewayAvailableWafRuleSets
```

A következő kimenet egy csonkolt válasz az előző példából:

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

A következő példa letiltja a szabályokat `911011` és az `911012` Application Gateway-t:

```powershell
$disabledrules=New-AzApplicationGatewayFirewallDisabledRuleGroupConfig -RuleGroupName REQUEST-911-METHOD-ENFORCEMENT -Rules 911011,911012
Set-AzApplicationGatewayWebApplicationFirewallConfiguration -ApplicationGateway $gw -Enabled $true -FirewallMode Detection -RuleSetVersion 3.0 -RuleSetType OWASP -DisabledRuleGroups $disabledrules
Set-AzApplicationGateway -ApplicationGateway $gw
```

## <a name="mandatory-rules"></a>Kötelező szabályok

Az alábbi lista olyan feltételeket tartalmaz, amelyek hatására a WAF megakadályozhatja a kérést a megelőzési módban (az észlelési módban, kivételként naplózva). Ezeket nem lehet konfigurálni vagy letiltani:

* A kérés törzsének elemzése nem sikerült, mert a rendszer letiltja a kérést, kivéve, ha a test ellenőrzése ki van kapcsolva (XML, JSON, Form)
* A kérelem törzse (fájlok nélkül) az adathossz nagyobb a beállított határértéknél.
* A kérelem törzse (beleértve a fájlokat) nagyobb, mint a korlát
* Belső hiba történt a WAF motorban

CRS 3. x-specifikus:

* A bejövő anomália pontszáma túllépte a küszöbértéket

## <a name="next-steps"></a>Következő lépések

A letiltott szabályok konfigurálása után megtudhatja, hogyan tekintheti meg a WAF-naplókat. További információ: [Application Gateway diagnosztika](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png

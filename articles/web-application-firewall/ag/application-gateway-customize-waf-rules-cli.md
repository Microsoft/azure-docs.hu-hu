---
title: Szabályok testreszabása a parancssori felület használatával – Azure webalkalmazási tűzfal
description: Ez a cikk azt ismerteti, hogyan lehet testre szabni a webalkalmazási tűzfal szabályait Application Gateway az Azure CLI-vel.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 8e8aaa9458619bc937c5bb11c450f3197b92f451
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "74048536"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-cli"></a>Webalkalmazási tűzfalszabályok testreszabása az Azure CLI használatával

Az Azure Application Gateway webalkalmazási tűzfal (WAF) védelmet biztosít a webalkalmazások számára. Ezeket a védelmet a nyílt Web Application Security Project (OWASP) alapszintű szabálykészlet (CRS) adja meg. Bizonyos szabályok hamis pozitív és valós adatforgalom blokkolására vezethetnek. Emiatt a Application Gateway lehetővé teszi a szabályok csoportjának és szabályainak testreszabását. További információ az adott szabályok csoportjairól és szabályairól: a [webalkalmazási TŰZFAL CRS-szabályait tartalmazó csoportok és szabályok listája](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Szabálykészlet és szabályok megtekintése

A következő példák bemutatják, hogyan tekintheti meg a konfigurálható szabályokat és szabálykészlet-csoportokat.

### <a name="view-rule-groups"></a>Szabálykészlet megtekintése

Az alábbi példa bemutatja, hogyan lehet megtekinteni a szabályok csoportjait:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --type OWASP
```

A következő kimenet egy csonkolt válasz az előző példából:

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  },
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_2.2.9",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
   "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "crs_20_protocol_violations",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "2.2.9",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

### <a name="view-rules-in-a-rule-group"></a>Szabálykészlet szabályainak megtekintése

Az alábbi példa azt szemlélteti, hogyan lehet megtekinteni a szabályokat egy adott szabálykészlet szerint:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --group "REQUEST-910-IP-REPUTATION"
```

A következő kimenet egy csonkolt válasz az előző példából:

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": [
          {
            "description": "Rule 910011",
            "ruleId": 910011
          },
          ...
        ]
      }
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

## <a name="disable-rules"></a>Szabályok letiltása

A következő példa letiltja a szabályokat `910018` és az `910017` Application Gateway-t:

```azurecli-interactive
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
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

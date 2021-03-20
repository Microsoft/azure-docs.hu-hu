---
title: WAF az Azure Application Gateway bot Protection áttekintése
titleSuffix: Azure Web Application Firewall
description: Ez a cikk áttekintést nyújt a webalkalmazási tűzfalról (WAF) a Application Gateway bot Protection szolgáltatásban
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 05/20/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: e01f9ac8966223e11ad218af7bf6fbb2462f28f6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "83714899"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Azure webalkalmazási tűzfal az Azure-ban Application Gateway bot Protection – áttekintés

Az internetes forgalom körülbelül 20%-a rossz robotoktól származik. Olyan dolgok, mint a kaparás, a vizsgálat és a biztonsági rések keresése a webalkalmazásban. Ha ezek a robotok le vannak állítva a webalkalmazási tűzfalon (WAF), nem tudnak megtámadni. Emellett nem használhatják fel erőforrásait és szolgáltatásait, például a háttérrendszer és más mögöttes infrastruktúra használatát.

A WAF esetében engedélyezhet egy felügyelt robotvédelmi szabálykészletet, amely blokkolja vagy naplózza az ismert kártékony IP-címekről érkező kéréseket. Az IP-címek forrása a Microsoft fenyegetésfelderítő hírcsatornája. A Microsoft veszélyforrás-felderítését az Intelligent Security Graph működteti, és azt több szolgáltatás, például az Azure Security Center is használja.

> [!IMPORTANT]
> A robot Protection-szabálykészlet jelenleg nyilvános előzetes verzióban érhető el, és az előzetes verziójú szolgáltatói szerződéssel rendelkezik. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részletekért tekintse meg a [Microsoft Azure előzetes verziójának kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)   .

## <a name="use-with-owasp-rulesets"></a>Használat a OWASP szabályrendszerek

A robotvédelmi szabálykészlet bármelyik OWASP-szabálykészlettel (2.2.9, 3.0 vagy 3.1) együtt használható. Egy adott időpontban csak egyetlen OWASP-szabálykészlet használható. A robotvédelmi szabálykészlet tartalmaz egy további szabályt, amely a saját szabálykészletében jelenik meg. A cím **Microsoft_BotManagerRuleSet_0 1**, és engedélyezheti vagy letilthatja a többi OWASP-szabályhoz hasonlóan.

![Robot-szabályrendszert](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>Szabályrendszer frissítése

A robotalapú kárenyhítési szabálykészlet kártékony IP-címeket tartalmazó listája naponta többször frissül a Microsoft fenyegetésfelderítő hírcsatornájából, hogy szinkronban maradjon a robotokkal. Webalkalmazásainak védelme folyamatosan biztosított, még akkor is, ha a robotok támadási vektorai módosulnak.

## <a name="log-example"></a>Példa a naplóra

Íme egy példa a bot-védelemre:

```
{
        "timeStamp": "0000-00-00T00:00:00+00:00",
            "resourceId": "appgw",
            "operationName": "ApplicationGatewayFirewall",
            "category": "ApplicationGatewayFirewallLog",
            "properties": {
            "instanceId": "vm1",
                "clientIp": "1.2.3.4",
                "requestUri": "/hello.php?arg1=aaaaaaabccc",
                "ruleSetType": "MicrosoftBotProtection",
                "message": "IPReputationTriggered",
                "action": "Blocked",
                "hostname": "example.com",
                "transactionId": "abc",
                "policyId": "waf policy 1",
                "policyScope": "Global",
                "policyScopeName": "Default Policy",
                "engine": "Azwaf"
        }
    }
```

## <a name="next-steps"></a>Következő lépések

- [Robotvédelem konfigurálása a webalkalmazási tűzfal esetében az Azure Application Gatewayen (előzetes verzió)](bot-protection.md)

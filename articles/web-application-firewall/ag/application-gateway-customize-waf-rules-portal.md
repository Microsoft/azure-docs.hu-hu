---
title: Szabályok testreszabása a portálon – Azure webalkalmazási tűzfal
description: Ez a cikk azt ismerteti, hogyan lehet testre szabni a webalkalmazási tűzfalszabályok szabályait a Azure Portal Application Gateway.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: c4635333614ee1c0fd0322c29a659380fb4315c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "74048377"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-portal"></a>Webalkalmazási tűzfalszabályok testreszabása a Azure Portal használatával

Az Azure Application Gateway webalkalmazási tűzfal (WAF) védelmet biztosít a webalkalmazások számára. Ezeket a védelmet a nyílt Web Application Security Project (OWASP) alapszintű szabálykészlet (CRS) adja meg. Bizonyos szabályok hamis pozitív és valós adatforgalom blokkolására vezethetnek. Emiatt a Application Gateway lehetővé teszi a szabályok csoportjának és szabályainak testreszabását. További információ az adott szabályok csoportjairól és szabályairól: a [webalkalmazási TŰZFAL CRS-szabályait tartalmazó csoportok és szabályok listája](application-gateway-crs-rulegroups-rules.md).

>[!NOTE]
> Ha az Application Gateway nem a WAF szintet használja, akkor a jobb oldali ablaktáblán megjelenik az Application Gateway WAF szintre való frissítésének lehetősége. 

![WAF engedélyezése][fig1]

## <a name="view-rule-groups-and-rules"></a>Szabálykészlet és szabályok megtekintése

**A szabálykészlet és a szabályok megtekintése**
1. Keresse meg az Application Gatewayt, majd válassza a **webalkalmazási tűzfal** lehetőséget.  
2. Válassza ki a **WAF szabályzatot**.
2. Válassza a **felügyelt szabályok** elemet.

   Ez a nézet a kiválasztott szabálykészlet által biztosított összes szabálykészlet oldalának táblázatát jeleníti meg. Az összes szabály jelölőnégyzete be van jelölve.

## <a name="disable-rule-groups-and-rules"></a>Szabályok csoportjai és szabályainak letiltása

> [!IMPORTANT]
> A szabályok csoportjai vagy szabályai letiltásával körültekintően járjon el. Ez növelheti a biztonsági kockázatokat.

A szabályok letiltásakor letilthatja a teljes szabálykészlet vagy az adott szabályok egy vagy több szabálykészlet alatt történő letiltását. 

**A szabálykészlet vagy a megadott szabályok letiltása**

   1. Keressen rá a letiltani kívánt szabályokra vagy szabálykészlet-csoportokra.
   2. Jelölje be a letiltani kívánt szabályok jelölőnégyzeteit. 
   3. Válassza ki a műveletet a lap tetején (Engedélyezés/Letiltás) a kiválasztott szabályoknál.
   2. Kattintson a **Mentés** gombra. 

![Módosítások mentése][3]

## <a name="mandatory-rules"></a>Kötelező szabályok

Az alábbi lista olyan feltételeket tartalmaz, amelyek hatására a WAF megakadályozhatja a kérést a megelőzési módban. Észlelési módban a rendszer kivételként naplózza őket.

Ezeket nem lehet konfigurálni vagy letiltani:

* A kérés törzsének elemzése nem sikerült, mert a rendszer letiltja a kérést, kivéve, ha a test ellenőrzése ki van kapcsolva (XML, JSON, Form)
* A kérelem törzse (fájlok nélkül) az adathossz nagyobb a beállított határértéknél.
* A kérelem törzse (beleértve a fájlokat) nagyobb, mint a korlát
* Belső hiba történt a WAF motorban

CRS 3. x-specifikus:

* A bejövő anomália pontszáma túllépte a küszöbértéket

## <a name="next-steps"></a>Következő lépések

A letiltott szabályok konfigurálása után megtudhatja, hogyan tekintheti meg a WAF-naplókat. További információ: [Application Gateway diagnosztika](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ../media/application-gateway-customize-waf-rules-portal/1.png
[3]: ../media/application-gateway-customize-waf-rules-portal/figure3.png

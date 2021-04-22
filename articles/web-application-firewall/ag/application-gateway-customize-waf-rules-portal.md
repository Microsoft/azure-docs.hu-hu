---
title: Szabályok testreszabása a portálon – Azure Web Application Firewall
description: Ez a cikk arról nyújt tájékoztatást, hogyan szabhatja Web Application Firewall szabályok Application Gateway a Azure Portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 04/21/2021
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 0ab122d178e5390a53e5a3a39f1b7763b298dc6d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878325"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-portal"></a>A Web Application Firewall testreszabása a Azure Portal

A Azure Application Gateway Web Application Firewall (WAF) védelmet nyújt a webalkalmazások számára. Ezeket a védelmet a Open Web Application Security Project (OWASP) alapvető szabálykészlete (CRS) biztosítja. Egyes szabályok téves riasztásokat okozhatnak, és blokkolják a valós forgalmat. Emiatt a Application Gateway lehetővé teszi a szabálycsoportok és szabályok testreszabását. Az egyes szabálycsoportokkal és szabályokkal kapcsolatos további információkért lásd a [crs Web Application Firewall és szabályok](application-gateway-crs-rulegroups-rules.md)listáját.

>[!NOTE]
> Ha az alkalmazásátjáró nem a WAF-szintet használja, a jobb oldali panelen megjelenik az alkalmazásátjáró WAF-szintre való frissítésének lehetősége. 

:::image type="content" source="../media/application-gateway-customize-waf-rules-portal/1.png" alt-text="WAF engedélyezése"::: 

## <a name="view-rule-groups-and-rules"></a>Szabálycsoportok és szabályok megtekintése

**Szabálycsoportok és szabályok megtekintése**
1. Keresse meg az Application Gatewayt, majd válassza a **Webalkalmazási tűzfal lehetőséget.**  
2. Válassza ki a **WAF-szabályzatot.**
2. Válassza a **Felügyelt szabályok lehetőséget.**

   Ez a nézet egy táblázatot mutat a kiválasztott szabálykészlethez megadott összes szabálycsoport oldalán. A szabály összes jelölőnégyzete be van jelölve.

## <a name="disable-rule-groups-and-rules"></a>Szabálycsoportok és szabályok letiltása

> [!IMPORTANT]
> Körültekintően járjon el szabálycsoportok vagy szabályok letiltásakor. Ez fokozott biztonsági kockázatot jelenthet.

**Szabálycsoportok vagy adott szabályok letiltása**

   1. Keresse meg a letiltani kívánt szabályokat vagy szabálycsoportokat.
   2. Jelölje be a letiltani kívánt szabályok jelölőnégyzetét. 
   3. Válassza ki az oldal tetején található műveletet (engedélyezés/letiltás) a kiválasztott szabályokhoz.
   2. Kattintson a **Mentés** gombra.
    :::image type="content" source="../media/application-gateway-customize-waf-rules-portal/figure3.png" alt-text="Letiltott szabályok mentése"::: 

## <a name="mandatory-rules"></a>Kötelező szabályok

Az alábbi lista olyan feltételeket tartalmaz, amelyek miatt a WAF megelőzési módban blokkolja a kérést. Észlelési módban a rendszer kivételként naplózza őket.

Ezek nem konfigurálhatók vagy tilthatók le:

* Ha a kérelem törzse nem elemezhető, a kérés blokkolva lesz, kivéve, ha a törzsvizsgálat ki van kapcsolva (XML, JSON, űrlapadatok)
* A kérelem törzse (fájlok nélkül) az adatok hossza nagyobb, mint a beállított korlát
* A kérelem törzse (beleértve a fájlokat is) nagyobb, mint a korlát
* Belső hiba történt a WAF-motorban

CRS 3.x-specifikus:

* A bejövő anomáliapontszám túllépte a küszöbértéket

## <a name="next-steps"></a>Következő lépések

A letiltott szabályok konfigurálása után megtudhatja, hogyan használhatja a WAF-naplókat. További információ: Application Gateway [diagnosztika.](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)
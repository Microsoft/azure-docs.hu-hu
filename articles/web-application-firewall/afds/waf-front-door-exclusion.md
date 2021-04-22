---
title: Webalkalmazási tűzfalak kizárási listái a Azure Front Door – Azure Portal
description: Ez a cikk az Azure Front kizárási listák konfigurációját és a Azure Portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/10/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 83baf03c414d9b0f7acb6a93db03794a539a3c58
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107860832"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>Web Application Firewall (WAF) Front Door szolgáltatáskizárási listákkal 

Egyes Web Application Firewall (WAF) blokkolhatja az alkalmazás számára engedélyezni kívánt kéréseket. Például a Active Directory a hitelesítéshez használt jogkivonatokat szúr be. Ezek a jogkivonatok speciális karaktereket tartalmazhatnak, amelyek téves riasztást aktiválhatnak a WAF-szabályok alapján. A WAF kizárási listái lehetővé teszik bizonyos kérelemattribútumok kihagyését a WAF-értékelésekből.  Kizárási lista konfigurálható a  [PowerShell,](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject) [az Azure CLI,](/cli/azure/network/front-door/waf-policy/managed-rules/exclusion#az_network_front_door_waf_policy_managed_rules_exclusion_add)a [Rest API](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)vagy a Azure Portal. Az alábbi példa a Azure Portal mutatja be. 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>Kizárási listák konfigurálása a Azure Portal
**A kizárások kezelése** a WAF-portálon érhető el a **Felügyelt szabályok alatt**

![Kizárások ](../media/waf-front-door-exclusion/exclusion1.png)
 ![ kezelése – Exclusion_add](../media/waf-front-door-exclusion/exclusion2.png)

 Példa kizárási lista: Az exclusion_define ![](../media/waf-front-door-exclusion/exclusion3.png)

Ez a példa kizárja a felhasználói fejléc *mezőben* található értéket. Érvényes kérés lehet  az SQL-injektálást kiváltó sztringet tartalmazó felhasználói mező is. Ebben az esetben kizárhatja *a felhasználói* paramétert, hogy a WAF-szabály semmit se értékeljen ki a mezőben.

A következő attribútumok név szerint hozzáadhatóak a kizárási listákhoz. A használt mezők értékeit a rendszer nem a WAF-szabályok alapján értékeli ki, hanem a neveket. A kizárási listák eltávolítják a mező értékének vizsgálatát.

* Kérelemfejléc neve
* Cookie-név kérése
* Lekérdezési sztring args neve
* Kérés törzse az args neve után

Pontos kérelemfejlécet, -törzset, cookie-t vagy lekérdezési sztringattribútum-egyezést is megadhat.  Vagy megadhatja a részleges egyezéseket is. A támogatott egyeztetési feltételek a következő operátorok:

- **Egyenlő:** Ez az operátor pontos egyezéshez használatos. Ha például ki kell választania egy **bearerToken** nevű fejlécet, használja az equals operátort úgy, hogy a **választó értéke bearerToken**.
- **Kezdete:** Ez az operátor megfelel minden olyan mezőnek, amely a megadott választóértékkel kezdődik.
- **Vége:**: Ez az operátor az összes olyan kérelemmezővel egyezik, amely a megadott választóértékre végződik.
- **Contains**: Ez az operátor megfelel minden olyan kérelemmezőnek, amely a megadott választóértéket tartalmazza.
- **Bármely egyenlő:** Ez az operátor az összes kérelemmezővel egyezik. * a választó értéke.

A fejléc- és cookie-nevek nem érzékenyek.

Ha egy fejlécérték, cookie-érték, argumentum utáni érték vagy lekérdezési argumentum értéke egyes szabályok esetén téves pozitív eredményt ad, a szabály a kérelem adott részét kizárhatja a megfontolásból:


|matchVariableName a WAF-naplókból  |Szabálykizárás a portálon  |
|---------|---------|
|CookieValue:SOME_NAME        |Request cookie name Equals SOME_NAME|
|HeaderValue:SOME_NAME        |Kérelemfejléc neve Egyenlő SOME_NAME|
|PostParamValue:SOME_NAME     |Kérelem törzse az args name Equals SOME_NAME|
|QueryParamValue:SOME_NAME    |Lekérdezési sztring args neve Egyenlő SOME_NAME|


Jelenleg csak a fenti matchVariableNames szabálykizárásokat támogatjuk a WAF-naplóikban. Minden más matchVariableNames esetén le kell tiltania a téves riasztásokat létrehozására vonatkozó szabályokat, vagy létre kell hoznia egy egyéni szabályt, amely kifejezetten engedélyezi ezeket a kéréseket. Ha a matchVariableName a CookieName, HeaderName, PostParamName vagy QueryParamName, az azt jelenti, hogy maga a név aktiválja a szabályt. A szabálykizárás jelenleg nem támogatja ezeket a matchVariableNames paramétert.


Ha kizár egy *FOO* nevű kérelem törzsét, egyetlen szabály sem mutathatja a PostParamValue:FOO paramétert matchVariableName értékként a WAF-naplókban. Előfordulhat azonban, hogy továbbra is létezik egy matchVariableName InitialBodyContents nevű szabály, amely megfelel a FOO post paraméter értékének, mivel a post paraméterértékek az InitialBodyContents részei.

Kizárási listákat alkalmazhat a felügyelt szabálykészleten belüli összes szabályra, egy adott szabálycsoport szabályaira vagy egyetlen szabályra, ahogyan az előző példában látható.

## <a name="define-exclusion-based-on-web-application-firewall-logs"></a>Kizárás meghatározása a naplók Web Application Firewall alapján
 [Azure Web Application Firewall monitorozás és naplózás a](waf-front-door-monitor.md) blokkolt kérés egyező adatait jeleníti meg. Ha egy fejlécérték, cookie-érték, argumentum utáni érték vagy lekérdezési argumentum értéke egyes szabályoknál téves pozitív eredményt ad, kizárhatja, hogy a kérés adott részét a szabály figyelembe foglalja. Az alábbi táblázat a WAF-naplókból származó példaértékeket és a vonatkozó kizárási feltételeket mutatja be.

|matchVariableName a WAF-naplókból    |Szabálykizárás a portálon|
|--------|------|
|CookieValue:SOME_NAME  |Request cookie name Equals SOME_NAME|
|HeaderValue:SOME_NAME  |Kérelemfejléc neve Egyenlő SOME_NAME|
|PostParamValue:SOME_NAME|  Kérelem törzse az args name Equals SOME_NAME|
|QueryParamValue:SOME_NAME| Lekérdezési sztring args neve Egyenlő SOME_NAME|


## <a name="next-steps"></a>Következő lépések

A WAF-beállítások konfigurálása után megtudhatja, hogyan megtekintheti a WAF-naplókat. További információ: Front Door [diagnosztikát.](../afds/waf-front-door-monitor.md)
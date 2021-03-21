---
title: Webalkalmazási tűzfal kizárási listája az Azure bejárati ajtóban – Azure Portal
description: Ez a cikk információt nyújt a kizárási listáról az Azure-ban a Azure Portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/10/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: a92679bb3114c4a60870424f3ec68a8de7b303da
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499917"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>Webalkalmazási tűzfal (WAF) a bejárati ajtó szolgáltatás kizárási listájával 

Előfordulhat, hogy a webalkalmazási tűzfal (WAF) blokkolni kívánja az alkalmazás számára engedélyezni kívánt kérelmet. A Active Directory például beszúrja a hitelesítéshez használt jogkivonatokat. Ezek a tokenek olyan speciális karaktereket tartalmazhatnak, amelyek a WAF-szabályokból hamis pozitív riasztást indíthatnak. A WAF kizárási listája lehetővé teszi bizonyos WAF kiértékelését.  A kizárási lista a  [PowerShell](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject), az [Azure CLI](/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion#ext-front-door-az-network-front-door-waf-policy-managed-rules-exclusion-add), a [REST API](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)vagy a Azure Portal használatával konfigurálható. A következő példa a Azure Portal konfigurációját mutatja be. 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>Kizárási listák konfigurálása a Azure Portal használatával
A **kizárások kezelése** a WAF-portálról a **felügyelt szabályok** területen érhető el

![Kizárások ](../media/waf-front-door-exclusion/exclusion1.png)
 ![ kezelése exclusion_add kezelése](../media/waf-front-door-exclusion/exclusion2.png)

 Példa kizárási listára: ![ exclusion_define kezelése](../media/waf-front-door-exclusion/exclusion3.png)

Ez a példa a *felhasználói* fejléc mezőben lévő értéket zárja ki. Egy érvényes kérelem tartalmazhatja az SQL-injektálási szabályt kiváltó karakterláncot tartalmazó *felhasználói* mezőt is. Ebben az esetben kizárhatja a *felhasználói* paramétereket, így a WAF-szabály nem értékel semmit a mezőben.

A következő attribútumok adhatók hozzá a kizárási listához név szerint. A rendszer nem értékeli ki a használt mezők értékeit a WAF-szabályok alapján, de a nevük kiértékelése megtörténik. A kizárási listán el kell távolítani a mező értékének ellenőrzését.

* Kérelem fejlécének neve
* Kérelem cookie-neve
* Lekérdezési karakterlánc-argumentumok neve
* Kérelem törzse post argumentumok neve

Megadhat egy pontos kérelem fejlécét, törzsét, cookie-t vagy lekérdezési karakterlánc-attribútumát.  Másik lehetőségként megadhatja a részleges egyezéseket is. A támogatott egyeztetési feltételek a következő operátorok:

- **Egyenlő**: ez az operátor pontos egyezést használ. Például egy **bearerToken** nevű fejléc kiválasztásához használja az Equals operátort a választó **bearerToken**.
- A következővel **kezdődik**: ez az operátor megegyezik a megadott választó értékkel kezdődő összes mezővel.
- **Végződik**: ez az operátor a megadott választó értékkel végződő összes kérelem mezőre illeszkedik.
- **Tartalmazza**: ez az operátor megfelel az összes olyan kérelem mezőnek, amely tartalmazza a megadott választó értéket.
- **Egyenlő**: ez az operátor megfelel az összes kérelem mezőnek. * a választó értéke.

A fejléc és a cookie neve nem megkülönbözteti a kis-és nagybetűket.

Ha egy fejléc értéke, a cookie értéke, a post argumentum értéke vagy a lekérdezési argumentum értéke hamis pozitív értéket hoz létre egyes szabályokhoz, kizárhatja a kérelem adott részét a szabálytól:


|matchVariableName a WAF-naplókból  |Szabály kizárása a portálon  |
|---------|---------|
|CookieValue: SOME_NAME        |A kérelem cookie-neve egyenlő SOME_NAME|
|HeaderValue: SOME_NAME        |A kérelem fejlécének neve egyenlő SOME_NAME|
|PostParamValue: SOME_NAME     |Kérelem törzse post ARG neve egyenlő SOME_NAME|
|QueryParamValue: SOME_NAME    |A lekérdezési karakterlánc-argumentumok neve egyenlő SOME_NAME|


Jelenleg csak a fenti matchVariableNames vonatkozó szabályok kizárását támogatják a WAF-naplókban. Bármely más matchVariableNames esetében le kell tiltania a hamis pozitív értéket biztosító szabályokat, vagy létre kell hoznia egy egyéni szabályt, amely explicit módon engedélyezi ezeket a kéréseket. Különösen, ha a matchVariableName CookieName, HeaderName, PostParamName vagy QueryParamName, az azt jelenti, hogy maga a név indítja el a szabályt. A szabály kizárása jelenleg nem támogatja ezeket a matchVariableNames.


Ha a kérelem törzsét egy *FOO* nevű argumentummal zárja ki, akkor egyetlen szabálynak sem kell megjelenítenie a POSTPARAMVALUE: Foo nevet a WAF-naplók matchVariableName. Előfordulhat azonban, hogy továbbra is megjelenik egy olyan szabály, amely a matchVariableName InitialBodyContents, amely megfelel a post param FOO értékének, mivel a post param értéke a InitialBodyContents részét képezi.

Kizárási listát alkalmazhat a felügyelt szabálykészlet összes szabályára, egy adott szabálykészlet szabályaira vagy egy olyan szabályra, amely az előző példában is látható.

## <a name="define-exclusion-based-on-web-application-firewall-logs"></a>Kizárás definiálása a webalkalmazási tűzfal naplófájljai alapján
 Az [Azure webalkalmazási tűzfal figyelése és naplózása](waf-front-door-monitor.md) egy letiltott kérelem részletes adatait jeleníti meg. Ha egy fejléc értéke, a cookie értéke, a post argumentum értéke vagy a lekérdezési argumentum értéke hamis pozitív értéket hoz létre egyes szabályokhoz, kizárhatja a kérelemnek a szabály általi megítélésének a részét. Az alábbi táblázat a WAF-naplókból és a megfelelő kizárási feltételekből származó értékeket mutatja be.

|matchVariableName a WAF-naplókból    |Szabály kizárása a portálon|
|--------|------|
|CookieValue: SOME_NAME  |A kérelem cookie-neve egyenlő SOME_NAME|
|HeaderValue: SOME_NAME  |A kérelem fejlécének neve egyenlő SOME_NAME|
|PostParamValue: SOME_NAME|  Kérelem törzse post ARG neve egyenlő SOME_NAME|
|QueryParamValue: SOME_NAME| A lekérdezési karakterlánc-argumentumok neve egyenlő SOME_NAME|


## <a name="next-steps"></a>Következő lépések

A WAF beállításainak konfigurálása után megtudhatja, hogyan tekintheti meg a WAF-naplókat. További információ: a [bejárati ajtó diagnosztikája](../afds/waf-front-door-monitor.md).
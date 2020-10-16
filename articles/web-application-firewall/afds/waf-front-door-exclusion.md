---
title: Webalkalmazási tűzfal kizárási listája az Azure bejárati ajtóban – Azure Portal
description: Ez a cikk információt nyújt a kizárási listáról az Azure-ban a Azure Portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 10/05/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 73372f3c38e12d0d4ac972a569da36a04ad533da
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92125815"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>Webalkalmazási tűzfal (WAF) a bejárati ajtó szolgáltatás kizárási listájával 

Előfordulhat, hogy a webalkalmazási tűzfal (WAF) blokkolni kívánja az alkalmazás számára engedélyezni kívánt kérelmet. A Active Directory például beszúrja a hitelesítéshez használt jogkivonatokat. Ezek a tokenek olyan speciális karaktereket tartalmazhatnak, amelyek a WAF-szabályokból hamis pozitív riasztást indíthatnak. A WAF kizárási listája lehetővé teszi bizonyos WAF kiértékelését.  A kizárási lista a  [PowserShell](https://docs.microsoft.com/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject?view=azps-3.5.0), az [Azure CLI](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-managed-rules-exclusion-add), a [REST API](https://docs.microsoft.com/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)vagy a Azure Portal használatával konfigurálható. A következő példa a Azure Portal konfigurációját mutatja be. 
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

- **Egyenlő**: ez az operátor pontos egyezést használ. Például egy **bearerToken**nevű fejléc kiválasztásához használja az Equals operátort a választó **bearerToken**.
- A következővel **kezdődik**: ez az operátor megegyezik a megadott választó értékkel kezdődő összes mezővel.
- **Végződik**: ez az operátor a megadott választó értékkel végződő összes kérelem mezőre illeszkedik.
- **Tartalmazza**: ez az operátor megfelel az összes olyan kérelem mezőnek, amely tartalmazza a megadott választó értéket.
- **Egyenlő**: ez az operátor megfelel az összes kérelem mezőnek. * a választó értéke.

A fejléc és a cookie neve nem megkülönbözteti a kis-és nagybetűket.

Kizárási listát alkalmazhat a felügyelt szabálykészlet összes szabályára, egy adott szabálykészlet szabályaira vagy egyetlen, az előző példában látható szabályra. 

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

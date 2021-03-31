---
title: A webalkalmazási tűzfal házirend-beállításai az Azure bejárati ajtaján
description: A webalkalmazási tűzfal (WAF) megismerése.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 08b21ccd7f7958f00546583f680ecb8cde4a20c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "75932619"
---
# <a name="policy-settings-for-web-application-firewall-on-azure-front-door"></a>A webalkalmazási tűzfal házirend-beállításai az Azure-beli bejárati ajtón

A webalkalmazási tűzfal (WAF) házirendje lehetővé teszi, hogy egyéni és felügyelt szabályok alapján vezérelje a webalkalmazásokhoz való hozzáférést. A WAF-szabályzat nevének egyedinek kell lennie. Ha meglévő nevet próbál használni, a rendszer érvényesítési hibát fog kapni. Az ebben a cikkben leírtak szerint több házirendi szintű beállítás is érvényes az adott szabályzathoz megadott összes szabályra.

## <a name="waf-state"></a>WAF állapota

A WAF házirendje a következő két állapot egyike lehet:
- **Engedélyezve:** Ha egy házirend engedélyezve van, a WAF aktívan ellenőrzi a bejövő kérelmeket, és a szabályok definíciói szerint végrehajtja a megfelelő műveleteket.
- **Letiltva:** – ha egy házirend le van tiltva, a WAF-ellenőrzés szünetel. A bejövő kérések megkerülik a WAF, és a rendszer az előtér-útválasztás alapján küldi el a háttér-végpontoknak.

## <a name="waf-mode"></a>WAF mód

A WAF házirend a következő két módban való futtatásra konfigurálható:

- **Észlelési mód** Az észlelési módban való futtatáskor a WAF nem hajt végre semmilyen műveletet a figyeléstől, és naplózza a kérést és a megegyező WAF szabályt a WAF-naplókhoz. A naplózási diagnosztika bekapcsolása a bejárati ajtóhoz (a portál használata esetén ez a **diagnosztika** szakaszban érhető el a Azure Portal).

- **Megelőzési mód** Ha a beállítás a megelőzési módban való futtatásra van konfigurálva, a WAF végrehajtja a megadott műveletet, ha egy kérelem megfelel egy szabálynak. A rendszer a WAF-naplókban is naplózza a megfeleltetett kérelmeket.

## <a name="waf-response-for-blocked-requests"></a>WAF válasz a blokkolt kérelmekhez

Alapértelmezés szerint, ha a WAF egy egyeztetett szabály miatt blokkol egy kérést, a egy 403-as állapotkódot ad vissza – **a kérés blokkolva** üzenet. A rendszer a naplózáshoz is visszaadja a hivatkozási karakterláncot.

Megadhat egy egyéni válasz állapotkódot és válaszüzenetet, ha a WAF letiltotta a kérelmet. A következő egyéni állapotkódok támogatottak:

- 200 OK
- 403 Tiltott
- 405 metódus nem engedélyezett
- 406 nem fogadható el
- 429 túl sok kérés

Az egyéni válasz állapotkód és a válaszüzenet egy házirendi szintű beállítás. Miután konfigurálta, az összes letiltott kérelem ugyanazt az egyéni választ kapja és válaszüzenetet kap.

## <a name="uri-for-redirect-action"></a>Átirányítási művelet URI-ja

Meg kell határoznia egy URI-t, hogy átirányítsa a kérelmeket, ha az **átirányítás** művelet be van JELÖLVE a WAF szabályzatban foglalt szabályok bármelyikéhez. Az átirányítási URI-nak érvényes HTTP (S) helynek kell lennie, és a konfigurálást követően az "átirányítás" művelettel egyező szabályokat tartalmazó kérelmeket a rendszer átirányítja a megadott helyre.


## <a name="next-steps"></a>Következő lépések
- Ismerje meg, hogyan határozhatja meg a WAF [Egyéni válaszait](waf-front-door-configure-custom-response-code.md)

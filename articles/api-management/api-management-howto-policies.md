---
title: Szabályzatok az Azure API Managementban | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre, szerkeszthet és konfigurálhat házirendeket API Managementban. Tekintse meg a példákat, és tekintse meg a további rendelkezésre álló erőforrásokat.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/29/2017
ms.author: apimpm
ms.openlocfilehash: 37ac6369790ed526fd923819558863ae84432aed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358116"
---
# <a name="policies-in-azure-api-management"></a>Az Azure API Management szabályzatai

Az Azure API Management (APIM) szolgáltatásban a házirendek a rendszer hatékony funkciója, amely lehetővé teszi, hogy a közzétevő a konfiguráción keresztül módosítsa az API viselkedését. A szabályzatok olyan utasítások gyűjteményei, amelyeket az API-k kérelmén vagy válaszán egymás után hajtanak végre. A népszerű utasítások közé tartoznak az XML-ből a JSON-ra való konvertálás és a hívások gyakoriságának korlátozása, hogy korlátozzák a beérkező hívások mennyiségét a fejlesztőtől. Számos további szabályzat érhető el a mezőben.

A szabályzatok az API-fogyasztó és a felügyelt API között található átjárón belül vannak alkalmazva. Az átjáró fogadja az összes kérelmet, és általában változatlanul továbbítja őket a mögöttes API-hoz. A házirend azonban a bejövő kérelemre és a kimenő válaszra is alkalmazhat módosításokat.

A házirend-kifejezéseket attribútumértékekként vagy szövegértékekként lehet használni bármelyik API Management házirendben, hacsak a házirend másként nem rendelkezik. Néhány házirend, például a [Vezérlés folyamata][Control flow] és a [Változó beállítása][Set variable] házirend-kifejezéseken alapul. További információ: [Speciális szabályzatok][Advanced policies] és [Szabályzatkifejezések][Policy expressions].

## <a name="understanding-policy-configuration"></a><a name="sections"> </a>A házirend-konfiguráció ismertetése

A házirend-definíció egy egyszerű XML-dokumentum, amely a bejövő és kimenő utasítások sorát ismerteti. Az XML szerkeszthető közvetlenül a definíciós ablakban. Az aktuális hatókörre vonatkozó utasítások listáját a jobb oldalon lehet kijelölni, és kiemelve.

Az engedélyezett utasításokra kattintva a rendszer hozzáadja a megfelelő XML-t a kurzor helyén a definíció nézetben. 

> [!NOTE]
> Ha a hozzáadni kívánt házirend nincs engedélyezve, győződjön meg arról, hogy a szabályzatnak megfelelő hatókörrel rendelkezik. Minden házirend-utasítás bizonyos hatókörökben és házirend-szakaszban való használatra lett kialakítva. A szabályzatok szabályzat-szakaszainak és hatókörének áttekintéséhez tekintse meg a házirend- [hivatkozás][Policy Reference] **használati** szakaszát.
> 
> 

A konfiguráció a,, és rendszerre van osztva `inbound` `backend` `outbound` `on-error` . A rendszer a megadott házirend-utasítások sorozatát hajtja végre egy kérelem és egy válasz megadásához.

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is forwarded to 
         the backend service go here -->
  </backend>
  <outbound>
    <!-- statements to be applied to the response go here -->
  </outbound>
  <on-error>
    <!-- statements to be applied if there is an error condition go here -->
  </on-error>
</policies> 
```

Ha hiba történik a kérelem feldolgozása során, a, a, a és a szakaszok hátralévő `inbound` lépései `backend` `outbound` kimaradnak, és a végrehajtás a szakaszban szereplő utasításokra ugrik `on-error` . Ha házirend-utasításokat helyez a `on-error` szakaszba, áttekintheti a hibát a `context.LastError` tulajdonság használatával, megvizsgálhatja és testreszabhatja a hibát a szabályzat használatával, `set-body` és konfigurálhatja, hogy mi történjen, ha hiba történik. A beépített lépések és a házirend-utasítások feldolgozása során esetlegesen előforduló hibák esetén hibakódok találhatók. További információ: hibakezelés [API Management házirendekben](./api-management-error-handling-policies.md).

## <a name="how-to-configure-policies"></a><a name="scopes"> </a>Szabályzatok konfigurálása

A házirendek konfigurálásával kapcsolatos információkért lásd: [házirendek beállítása vagy szerkesztése](set-edit-policies.md).

## <a name="policy-reference"></a>Házirend-hivatkozás

A szabályzatokra vonatkozó utasítások és azok beállításainak teljes listájáért tekintse meg a [házirend-referenciát](./api-management-policies.md) .

## <a name="policy-samples"></a>Szabályzatminták

További [példákat a](./policy-reference.md) szabályzatok című témakörben talál.

## <a name="examples"></a>Példák

### <a name="apply-policies-specified-at-different-scopes"></a>Különböző hatókörökben megadott házirendek alkalmazása

Ha globális szintű szabályzattal és egy API-ra konfigurált szabályzattal rendelkezik, akkor az adott API-t mindkét esetben alkalmazza a rendszer. API Management lehetővé teszi a kombinált házirend-utasítások determinisztikus rendelését az alapelemen keresztül. 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

A fenti példában szereplő házirend-definícióban az `cross-domain` utasítás végrehajtása előtt végre kell hajtania az utasítást, amelyet a szabályzat követ `find-and-replace` . 

### <a name="restrict-incoming-requests"></a>Bejövő kérelmek korlátozása

Ha új utasítást szeretne hozzáadni a bejövő kérelmek megadott IP-címekre való korlátozásához, vigye a kurzort az `inbound` XML-elem tartalmába, és kattintson a **hívó IP** -címeinek korlátozása elemre.

![Korlátozási szabályzatok][policies-restrict]

Ez egy XML-kódrészletet ad hozzá az `inbound` elemhez, amely útmutatást nyújt az utasítás konfigurálásához.

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

Ha korlátozni szeretné a bejövő kérelmeket, és csak a 1.2.3.4 IP-címéről fogadja el a beállításokat, módosítsa az XML-t a következőképpen:

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

## <a name="next-steps"></a>Következő lépések

További információ a házirendek használatáról:

+ [API-k átalakítása](transform-api.md)
+ Házirend- [hivatkozás](./api-management-policies.md) a szabályzat-utasítások és azok beállításainak teljes listájához
+ [Szabályzatminták](./policy-reference.md)   

[Policy Reference]: ./api-management-policies.md
[Product]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md
[Operation]: ./mock-api-responses.md

[Advanced policies]: ./api-management-advanced-policies.md
[Control flow]: ./api-management-advanced-policies.md#choose
[Set variable]: ./api-management-advanced-policies.md#set-variable
[Policy expressions]: ./api-management-policy-expressions.md

[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png

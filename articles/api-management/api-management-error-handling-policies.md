---
title: Hibakezelés az Azure API Management-szabályzatokban | Microsoft Docs
description: Megtudhatja, hogyan reagálhat a kérelmek Azure API Management-ban való feldolgozása során felmerülő hibákra.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 3c777964-02b2-4f55-8731-8c3bd3c0ae27
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: a3b6f90d0aa26b478c0f2fcefac55dcd509da437
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92070945"
---
# <a name="error-handling-in-api-management-policies"></a>Hibakezelés az API Management-szabályzatokban

Egy objektum megadásával az `ProxyError` Azure API Management lehetővé teszi a kiadók számára, hogy válaszolják meg a hibákra vonatkozó feltételeket, amelyek a kérelmek feldolgozása során fordulhatnak elő. Az `ProxyError` objektum a környezeten keresztül érhető el [. A LastError](api-management-policy-expressions.md#ContextVariables) tulajdonságot, amelyet a házirendek a szabályzat `on-error` szakaszban használhatnak. Ez a cikk az Azure-API Management hibakezelés funkcióinak hivatkozását ismerteti.

## <a name="error-handling-in-api-management"></a>Hibakezelés a API Managementban

Az Azure API Management házirendjei az `inbound` `backend` alábbi példában látható módon vannak felosztva,, `outbound` és `on-error` szakaszokra bontva.

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is
         forwarded to the backend service go here -->
    </backend>
    <outbound>
      <!-- statements to be applied to the response go here -->
    </outbound>
    <on-error>
        <!-- statements to be applied if there is an error
             condition go here -->
  </on-error>
</policies>
```

A kérelem feldolgozása során a beépített lépések minden olyan szabályzattal együtt futnak, amely a kérelem hatókörében van. Ha hiba történik, a feldolgozás azonnal beugrik a `on-error` házirend szakaszra.
A `on-error` szabályzat szakasz bármely hatókörben használható. Az API-közzétevők egyéni viselkedést állíthatnak be, például naplózzák a hibát az Event hubokba, vagy létrehozhatnak egy új választ a hívónak való visszatéréshez.

> [!NOTE]
> A `on-error` szakasz alapértelmezés szerint nem szerepel a szabályzatokban. A szakasz szabályzathoz való hozzáadásához `on-error` Keresse meg a kívánt szabályzatot a szabályzat-szerkesztőben, és adja hozzá. A házirendek konfigurálásával kapcsolatos további információkért lásd: [szabályzatok API Management](./api-management-howto-policies.md).
>
> Ha nincs `on-error` szakasz, a hívók 400 vagy 500 http-válaszüzenetet kapnak, ha hiba történik.

### <a name="policies-allowed-in-on-error"></a>Hiba esetén engedélyezett házirendek

A házirend szakaszban a következő szabályzatok használhatók `on-error` .

-   [Válassza](api-management-advanced-policies.md#choose)
-   [változó beállítása](api-management-advanced-policies.md#set-variable)
-   [Keresés és csere](api-management-transformation-policies.md#Findandreplacestringinbody)
-   [visszatérési válasz](api-management-advanced-policies.md#ReturnResponse)
-   [fejléc beállítása](api-management-transformation-policies.md#SetHTTPheader)
-   [set-Method](api-management-advanced-policies.md#SetRequestMethod)
-   [állapot beállítása](api-management-advanced-policies.md#SetStatus)
-   [kérelem küldése](api-management-advanced-policies.md#SendRequest)
-   [egyirányú küldési kérelem](api-management-advanced-policies.md#SendOneWayRequest)
-   [eventhub](api-management-advanced-policies.md#log-to-eventhub)
-   [JSON – XML](api-management-transformation-policies.md#ConvertJSONtoXML)
-   [XML – JSON](api-management-transformation-policies.md#ConvertXMLtoJSON)
-   [korlátozás – Egyidejűség](api-management-advanced-policies.md#LimitConcurrency)
-   [modell – válasz](api-management-advanced-policies.md#mock-response)
-   [Próbálja megismételni](api-management-advanced-policies.md#Retry)
-   [nyomkövetési](api-management-advanced-policies.md#Trace)

## <a name="lasterror"></a>LastError

Ha hiba lép fel, és a vezérlő a `on-error` házirend szakaszra ugrik, a rendszer a hibát a [kontextusban tárolja. LastError](api-management-policy-expressions.md#ContextVariables) tulajdonság, amely a szakasz szabályzatai alapján érhető el `on-error` . A LastError a következő tulajdonságokkal rendelkezik.

| Név       | Típus   | Leírás                                                                                               | Kötelező |
| ---------- | ------ | --------------------------------------------------------------------------------------------------------- | -------- |
| `Source`   | sztring | Annak az elemnek a neve, ahol a hiba történt. Lehet házirend vagy beépített folyamat lépésének neve.      | Yes      |
| `Reason`   | sztring | Számítógép-barát hibakód, amely a hibakezelés során használható.                                       | No       |
| `Message`  | sztring | Ember által olvasható hiba leírása.                                                                         | Yes      |
| `Scope`    | sztring | Annak a hatókörnek a neve, ahol a hiba történt, és a "globális", "termék", "API" vagy "művelet" lehet. | No       |
| `Section`  | sztring | A szakasz neve, ahol a hiba történt. Lehetséges értékek: "bejövő", "háttér", "kimenő" vagy "on-Error".      | No       |
| `Path`     | sztring | Beágyazott szabályzatot határoz meg, például "válasszon [3]/when [2]".                                                 | No       |
| `PolicyId` | sztring | Az attribútum értéke `id` , ha az ügyfél megadja azt a házirendet, ahol a hiba történt             | No       |

> [!TIP]
> Az állapotkódot a kontextusban érheti el. Response. StatusCode.

> [!NOTE]
> Az összes házirendhez választható `id` attribútum tartozik, amely a házirend gyökérszintű eleméhez adható hozzá. Ha ez az attribútum hiba esetén a szabályzatban szerepel, az attribútum értéke a tulajdonság használatával kérhető le `context.LastError.PolicyId` .

## <a name="predefined-errors-for-built-in-steps"></a>Beépített lépések előre megadott hibák

A következő hibák előre definiálva vannak a beépített feldolgozási lépések kiértékelése során felmerülő hibákra vonatkozó feltételekhez.

| Forrás        | Feltétel                                 | Ok                  | Üzenet                                                                                                                |
| ------------- | ----------------------------------------- | ----------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| konfiguráció | Az URI nem felel meg egyetlen API-nak vagy műveletnek sem | OperationNotFound       | Nem lehet egyeztetni a bejövő kérést egy művelethez.                                                                      |
| engedélyezés | Nincs megadva az előfizetési kulcs             | SubscriptionKeyNotFound | A hozzáférés megtagadva a hiányzó előfizetési kulcs miatt. Ügyeljen arra, hogy előfizetési kulcsot tartalmazzon az API-nak küldött kérésekhez. |
| engedélyezés | Az előfizetési kulcs értéke érvénytelen.         | SubscriptionKeyInvalid  | A hozzáférés megtagadva, mert érvénytelen az előfizetési kulcs. Győződjön meg arról, hogy érvényes kulcsot ad meg egy aktív előfizetéshez.            |
| multiple (többszörös) | Az ügyfél megszakította az alsóbb rétegbeli kapcsolatokat (az ügyfélről egy API Management átjáróra), amíg a kérelem függőben volt. | ClientConnectionFailure | multiple (többszörös) |
| multiple (többszörös) | A felsőbb rétegbeli kapcsolatok (API Management átjáróról a háttérbeli szolgáltatásra) nem jöttek meg, vagy a háttér megszakította. | BackendConnectionFailure | multiple (többszörös) |
| multiple (többszörös) | Futásidejű kivétel történt egy adott kifejezés kiértékelése során. | ExpressionValueEvaluationFailure | multiple (többszörös) |

## <a name="predefined-errors-for-policies"></a>Szabályzatok előre megadott hibái

A következő hibák előre definiálva vannak a szabályzat kiértékelése során felmerülő hibákra vonatkozóan.

| Forrás       | Feltétel                                                       | Ok                    | Üzenet                                                                                                                              |
| ------------ | --------------------------------------------------------------- | ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| korlát mértéke   | Túllépte a díjszabási korlátot                                             | RateLimitExceeded         | Túllépte a díjszabási korlátot                                                                                                               |
| kvóta        | Kvóta túllépve                                                  | QuotaExceeded             | A csomagba foglalt lebeszélhető percek elfogytak. A kvóta a XX: xx: xx nyelven lesz feltöltve. -vagy a sávszélesség-kvóta. A kvóta a XX: xx: xx nyelven lesz feltöltve. |
| jsnop támogatással        | A visszahívási paraméter értéke érvénytelen (helytelen karaktereket tartalmaz) | CallbackParameterInvalid  | A (z) {callback-paraméter-Name} visszahívási paraméter értéke nem érvényes JavaScript-azonosító.                                          |
| IP-szűrő    | Nem sikerült elemezni a hívó IP-címét a kérelemből                          | FailedToParseCallerIP     | Nem sikerült a hívó IP-címének létrehozása. A hozzáférés megtagadva.                                                                        |
| IP-szűrő    | A hívó IP-címe nem engedélyezett a listában                                | CallerIpNotAllowed        | A hívó IP-címe ({IP-cím}) nem engedélyezett. A hozzáférés megtagadva.                                                                        |
| IP-szűrő    | A hívó IP-címe tiltott listán van                                    | CallerIpBlocked           | A hívó IP-címe le van tiltva. A hozzáférés megtagadva.                                                                                         |
| fejléc bejelölése | A kötelező fejléc nem jelenik meg, vagy hiányzik az érték               | HeaderNotFound            | A kérelemben nem található a (z) {header-Name} fejléc. A hozzáférés megtagadva.                                                                    |
| fejléc bejelölése | A kötelező fejléc nem jelenik meg, vagy hiányzik az érték               | HeaderValueNotAllowed     | A (z) {header-Value} fejlécben szereplő {header-Name} érték nem engedélyezett. A hozzáférés megtagadva.                                                          |
| ellenőrzés – JWT | A kérelemben hiányzik az JWT token.                                 | TokenNotFound             | A JWT nem található a kérelemben. A hozzáférés megtagadva.                                                                                         |
| ellenőrzés – JWT | Az aláírás ellenőrzése nem sikerült                                     | TokenSignatureInvalid     | <üzenet a JWT könyvtárából \> . A hozzáférés megtagadva.                                                                                          |
| ellenőrzés – JWT | Érvénytelen célközönség                                                | TokenAudienceNotAllowed   | <üzenet a JWT könyvtárából \> . A hozzáférés megtagadva.                                                                                          |
| ellenőrzés – JWT | Érvénytelen kiállító                                                  | TokenIssuerNotAllowed     | <üzenet a JWT könyvtárából \> . A hozzáférés megtagadva.                                                                                          |
| ellenőrzés – JWT | Lejárt a jogkivonat.                                                   | TokenExpired              | <üzenet a JWT könyvtárából \> . A hozzáférés megtagadva.                                                                                          |
| ellenőrzés – JWT | Az aláírási kulcs nem lett feloldva azonosító alapján                            | TokenSignatureKeyNotFound | <üzenet a JWT könyvtárából \> . A hozzáférés megtagadva.                                                                                          |
| ellenőrzés – JWT | A szükséges jogcímek hiányoznak a jogkivonatból                          | TokenClaimNotFound        | Az JWT tokenből hiányzik a következő jogcímek: <C1 \> , <C2 \> ,... A hozzáférés megtagadva.                                                            |
| ellenőrzés – JWT | Nem egyező jogcím-értékek                                           | TokenClaimValueNotAllowed | A (z) {jogcím-Value} jogcím {jogcím-Name} értéke nem engedélyezett. A hozzáférés megtagadva.                                                             |
| ellenőrzés – JWT | Egyéb érvényesítési hibák                                       | JwtInvalid                | Üzenet <a JWT könyvtárából\>                                                                                                          |
| továbbítás – kérelem vagy küldési kérelem | A HTTP-válasz állapotkód és a fejlécek nem érkeztek meg a háttérben a beállított időkorláton belül | Időtúllépés | multiple (többszörös) |

## <a name="example"></a>Példa

API-szabályzat beállítása a következőre:

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <set-header name="ErrorSource" exists-action="override">
            <value>@(context.LastError.Source)</value>
        </set-header>
        <set-header name="ErrorReason" exists-action="override">
            <value>@(context.LastError.Reason)</value>
        </set-header>
        <set-header name="ErrorMessage" exists-action="override">
            <value>@(context.LastError.Message)</value>
        </set-header>
        <set-header name="ErrorScope" exists-action="override">
            <value>@(context.LastError.Scope)</value>
        </set-header>
        <set-header name="ErrorSection" exists-action="override">
            <value>@(context.LastError.Section)</value>
        </set-header>
        <set-header name="ErrorPath" exists-action="override">
            <value>@(context.LastError.Path)</value>
        </set-header>
        <set-header name="ErrorPolicyId" exists-action="override">
            <value>@(context.LastError.PolicyId)</value>
        </set-header>
        <set-header name="ErrorStatusCode" exists-action="override">
            <value>@(context.Response.StatusCode.ToString())</value>
        </set-header>
        <base />
    </on-error>
</policies>
```

a jogosulatlan kérések küldése a következő választ eredményezi:

![Jogosulatlan hiba válasza](media/api-management-error-handling-policies/error-response.png)

## <a name="next-steps"></a>Következő lépések

További információ a házirendek használatáról:

-   [Szabályzatok API Management](api-management-howto-policies.md)
-   [API-k átalakítása](transform-api.md)
-   Házirend- [hivatkozás](./api-management-policies.md) a szabályzat-utasítások és azok beállításainak teljes listájához
-   [Szabályzatminták](./policy-reference.md)
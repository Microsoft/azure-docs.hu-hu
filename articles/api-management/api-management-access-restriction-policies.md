---
title: Az Azure API Management hozzáférés-korlátozási szabályzatok |} A Microsoft Docs
description: Ismerje meg a hozzáférés-korlátozási szabályzatok az Azure API Management használható.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 034febe3-465f-4840-9fc6-c448ef520b0f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: apimpm
ms.openlocfilehash: 8947637a42adfca12268c3f84e208079768870e0
ms.sourcegitcommit: d73c46af1465c7fd879b5a97ddc45c38ec3f5c0d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65921217"
---
# <a name="api-management-access-restriction-policies"></a>Az API Management hozzáférés-korlátozási szabályzatok

Ez a témakör egy hivatkozást kínál a következő az API Management házirendek. Hozzáadása és házirendek konfigurálásával kapcsolatos tudnivalókat lásd: [az API Management házirendek](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="AccessRestrictionPolicies"></a> Hozzáférés-korlátozási szabályzatok

-   [Ellenőrizze a HTTP-fejléc](api-management-access-restriction-policies.md#CheckHTTPHeader) -kikényszeríti a létezik-e és/vagy a HTTP-fejléc értékét.
-   [A hívások sebességének korlátozása előfizetés](api-management-access-restriction-policies.md#LimitCallRate) – megakadályozza, hogy API-használat hirtelen megugró kihasználtság, a hívások sebességének, az minden előfizetés korlátozásával.
-   [A hívások sebességének korlátozása a kulcs által](#LimitCallRateByKey) – megakadályozza, hogy API-használat hirtelen megugró kihasználtság, a hívások sebességének per kulcs alapon korlátozásával.
-   [A hívó IP-címek korlátozása](api-management-access-restriction-policies.md#RestrictCallerIPs) – meghatározott IP-címek és/vagy címtartományokat (engedélyezi vagy megtagadja) szűrők hívásait.
-   [Előfizetés-használati kvóta beállítása](api-management-access-restriction-policies.md#SetUsageQuota) – lehetővé teszi a megújuló vagy élettartama hívás kötet és/vagy a sávszélesség kvóta, minden előfizetés érvényesítését.
-   [Használati kvóta beállítása kulcs által](#SetUsageQuotaByKey) – lehetővé teszi a megújuló vagy élettartama hívás kötet és/vagy a sávszélesség kvóta, per kulcs alapon kikényszerítésére.
-   [Ellenőrizze a JWT](api-management-access-restriction-policies.md#ValidateJWT) -kényszerít létezését és a jwt-t, vagy egy adott HTTP-fejléc, vagy egy megadott lekérdezési paraméter kinyert érvényességét.

## <a name="CheckHTTPHeader"></a> Ellenőrizze a HTTP-fejléc

Használja a `check-header` szabályzat kényszerítéséhez, hogy egy kérés rendelkezik-e a megadott HTTP-fejléc. Szükség esetén ellenőrizheti, hogy a fejléc megtalálható egy adott érték vagy egy tartományt az engedélyezett keresése. Ha az ellenőrzés sikertelen, a szabályzat a kérelem feldolgozása leáll, és üzenetet adja vissza a HTTP állapot kód és a hiba a szabályzat által megadott.

### <a name="policy-statement"></a>Házirendutasítás

```xml
<check-header name="header name" failed-check-httpcode="code" failed-check-error-message="message" ignore-case="true">
    <value>Value1</value>
    <value>Value2</value>
</check-header>
```

### <a name="example"></a>Példa

```xml
<check-header name="Authorization" failed-check-httpcode="401" failed-check-error-message="Not authorized" ignore-case="false">
    <value>f6dc69a089844cf6b2019bae6d36fac8</value>
</check-header>
```

### <a name="elements"></a>Elemek

| Name (Név)         | Leírás                                                                                                                                   | Szükséges |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| ellenőrzés-fejléc | A gyökérelem.                                                                                                                                 | Igen      |
| value        | Engedélyezett HTTP-fejléc értéke. Ha több elemet is érték meg van adva, az ellenőrzés sikeres számít az értékek közül bármelyik egyezés esetén. | Nem       |

### <a name="attributes"></a>Attribútumok

| Name (Név)                       | Leírás                                                                                                                                                            | Kötelező | Alapértelmezett |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| failed-check-error-message | Hibaüzenet jelenik meg, a HTTP-válasz törzsében ad vissza, ha a fejléc nem létezik vagy érvénytelen értékkel rendelkezik. Ez az üzenet blobnév különleges karaktereket kell rendelkeznie. | Igen      | –     |
| failed-check-httpcode      | HTTP-állapot kódot ad vissza, ha a fejléc nem létezik, vagy má neplatnou hodnotu.                                                                                        | Igen      | –     |
| header-name                | Ellenőrizze, hogy a HTTP-fejléc neve.                                                                                                                                  | Igen      | –     |
| ignore-case                | Akkor állítható a True vagy FALSE (hamis). Ha esetben igaz értékre kell állítani a rendszer figyelmen kívül hagyja, a fejléc értéke ellenében elfogadható értékek képest.                                    | Igen      | –     |

### <a name="usage"></a>Használat

Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **A házirend-szakaszok:** bejövő, kimenő

-   **A házirend-hatókörök:** globális, termék, API-művelet

## <a name="LimitCallRate"></a> A hívások sebességének korlátozása előfizetés szerint

A `rate-limit` szabályzat megakadályozza, hogy minden előfizetés az API használati csúcsok által megadott szám egy megadott időszak a hívások sebességének korlátozása. Ez a szabályzat akkor aktiválódik, amikor a hívó kap egy `429 Too Many Requests` válasz állapotkódja.

> [!IMPORTANT]
> Ez a szabályzat csak egyszer használhatók egy házirend-dokumentum.
>
> [Házirend-kifejezések](api-management-policy-expressions.md) nem használható fel a házirend-attribútumokat az ehhez a szabályzathoz.

> [!CAUTION]
> Elosztott jellege miatt szabályozás architektúra sebességkorlátozást soha nem teljesen pontos. A különbség a között konfigurálva, és valós számát engedélyezett kérelmek eltérőek kérelem mennyiségi és sebessége, háttéralkalmazás késése és egyéb tényezők alapján.

### <a name="policy-statement"></a>Házirendutasítás

```xml
<rate-limit calls="number" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />
    </api>
</rate-limit>
```

### <a name="example"></a>Példa

```xml
<policies>
    <inbound>
        <base />
        <rate-limit calls="20" renewal-period="90" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elemek

| Name (Név)      | Leírás                                                                                                                                                                                                                                                                                              | Szükséges |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| korlát beállítása | A gyökérelem.                                                                                                                                                                                                                                                                                            | Igen      |
| api       | Adjon hozzá egy vagy több ezeknek az elemeknek hívások sebessége korlátozza az API-k a terméken belüli. Termék- és API-t hívja meg sebesség függetlenül korlátokat alkalmazza. API-t is lehet hivatkozni, akár `name` vagy `id`. Ha az attribútumok vannak megadva, `id` fogja használni, és `name` figyelmen kívül hagyja.                    | Nem       |
| művelet | Adjon hozzá egy vagy több ezeknek az elemeknek az API műveleteit hívás sebessége korlátozza. Termék, API és művelet meghívása a sebesség függetlenül korlátokat alkalmazza. Művelet lehet akár hivatkozott `name` vagy `id`. Ha az attribútumok vannak megadva, `id` fogja használni, és `name` figyelmen kívül hagyja. | Nem       |

### <a name="attributes"></a>Attribútumok

| Name (Név)           | Leírás                                                                                           | Kötelező | Alapértelmezett |
| -------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| név           | Az API-t, amelyhez a alkalmazni a sávszélesség-korlátjának neve.                                                | Igen      | –     |
| hívás          | A megadott időtartam során megengedett maximális száma a `renewal-period`. | Igen      | –     |
| megújítási időszak | Másodperc elteltével a kvóta alaphelyzetbe állítja az adott időszakban.                                              | Igen      | –     |

### <a name="usage"></a>Használat

Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **A házirend-szakaszok:** bejövő

-   **A házirend-hatókörök:** termék

## <a name="LimitCallRateByKey"></a> A hívások sebességének korlátozása a kulcs szerint

> [!IMPORTANT]
> Ez a funkció nem érhető el a **fogyasztás** szinten az API Management.

A `rate-limit-by-key` házirend miatt API használati csúcsok per kulcs alapon által megadott szám egy megadott időszak a hívások sebességének korlátozása. A kulcs egy tetszés szerinti karakterlánc értéke lehet, és általában biztosított egy házirend-kifejezés használatával. Adja meg, mely kérelmek kell számolni a korlát felé választható növekmény feltétel is hozzáadhatók. Ez a szabályzat akkor aktiválódik, amikor a hívó kap egy `429 Too Many Requests` válasz állapotkódja.

További információk és példák a szabályzat: [speciális kérésszabályzás az Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).

> [!CAUTION]
> Elosztott jellege miatt szabályozás architektúra sebességkorlátozást soha nem teljesen pontos. A különbség a között konfigurálva, és valós számát engedélyezett kérelmek eltérőek kérelem mennyiségi és sebessége, háttéralkalmazás késése és egyéb tényezők alapján.

### <a name="policy-statement"></a>Házirendutasítás

```xml
<rate-limit-by-key calls="number"
                   renewal-period="seconds"
                   increment-condition="condition"
                   counter-key="key value" />

```

### <a name="example"></a>Példa

A következő példában a sávszélesség-korlátjának ellátva a hívó IP-címe.

```xml
<policies>
    <inbound>
        <base />
        <rate-limit-by-key  calls="10"
              renewal-period="60"
              increment-condition="@(context.Response.StatusCode == 200)"
              counter-key="@(context.Request.IpAddress)"/>
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elemek

| Name (Név)      | Leírás   | Kötelező |
| --------- | ------------- | -------- |
| korlát beállítása | A gyökérelem. | Igen      |

### <a name="attributes"></a>Attribútumok

| Name (Név)                | Leírás                                                                                           | Kötelező | Alapértelmezett |
| ------------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| hívás               | A megadott időtartam során megengedett maximális száma a `renewal-period`. | Igen      | –     |
| másik kulcsot         | A Sebességkorlát-szabályzat használandó kulcs.                                                             | Igen      | –     |
| increment-condition | A logikai kifejezés megadása, ha a kérés kell számolni a kvóta felé (`true`).        | Nem       | –     |
| megújítási időszak      | Másodperc elteltével a kvóta alaphelyzetbe állítja az adott időszakban.                                              | Igen      | –     |

### <a name="usage"></a>Használat

Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **A házirend-szakaszok:** bejövő

-   **A házirend-hatókörök:** globális, termék, API-művelet

## <a name="RestrictCallerIPs"></a> A hívó IP-címek korlátozása

A `ip-filter` házirend szűri az adott IP-címek és/vagy címtartományokat (engedélyezi vagy megtagadja) hívásait.

### <a name="policy-statement"></a>Házirendutasítás

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address" />
</ip-filter>
```

### <a name="example"></a>Példa

A következő példában a szabályzat csak lehetővé teszi, hogy az egyetlen IP-cím vagy IP-címtartomány a megadott érkező kéréseket

```xml
<ip-filter action="allow">
    <address>13.66.201.169</address>
    <address-range from="13.66.140.128" to="13.66.140.143" />
</ip-filter>
```

### <a name="elements"></a>Elemek

| Name (Név)                                      | Leírás                                         | Szükséges                                                       |
| ----------------------------------------- | --------------------------------------------------- | -------------------------------------------------------------- |
| ip-filter                                 | A gyökérelem.                                       | Igen                                                            |
| cím                                   | Itt adhatja meg a szűrni kívánt egyetlen IP-címet.   | Legalább egy `address` vagy `address-range` elem megadása kötelező. |
| address-range from="address" to="address" | Szűrni kívánt az IP-címet ad meg. | Legalább egy `address` vagy `address-range` elem megadása kötelező. |

### <a name="attributes"></a>Attribútumok

| Name (Név)                                      | Leírás                                                                                 | Kötelező                                           | Alapértelmezett |
| ----------------------------------------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------- | ------- |
| address-range from="address" to="address" | Engedélyezi vagy megtagadja a hozzáférést egy IP-címeket.                                        | Szükséges, ha a `address-range` elem szolgál. | –     |
| IP-szűrési művelet = "engedélyezése &#124; megtiltsa"    | Megadja a hívások engedélyezni kell-e, vagy a megadott IP-címek és tartományok esetében nem. | Igen                                                | –     |

### <a name="usage"></a>Használat

Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **A házirend-szakaszok:** bejövő
-   **A házirend-hatókörök:** globális, termék, API-művelet

## <a name="SetUsageQuota"></a> Előfizetés-használati kvóta beállítása

A `quota` a szabályzat kötelezővé teszi a megújuló vagy élettartama hívás kötet és/vagy a sávszélesség kvóta, minden előfizetés.

> [!IMPORTANT]
> Ez a szabályzat csak egyszer használhatók egy házirend-dokumentum.
>
> [Házirend-kifejezések](api-management-policy-expressions.md) nem használható fel a házirend-attribútumokat az ehhez a szabályzathoz.

### <a name="policy-statement"></a>Házirendutasítás

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />
    </api>
</quota>
```

### <a name="example"></a>Példa

```xml
<policies>
    <inbound>
        <base />
        <quota calls="10000" bandwidth="40000" renewal-period="3600" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elemek

| Name (Név)      | Leírás                                                                                                                                                                                                                                                                                  | Kötelező |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| kvóta     | A gyökérelem.                                                                                                                                                                                                                                                                                | Igen      |
| api       | Adjon hozzá egy vagy több ezeknek az elemeknek írnak elő a terméken belüli API-k a hívási kvótát. Egymástól függetlenül termék- és API-hívás kvóták érvényesek. API-t is lehet hivatkozni, akár `name` vagy `id`. Ha az attribútumok vannak megadva, `id` fogja használni, és `name` figyelmen kívül hagyja.                    | Nem       |
| művelet | Adjon hozzá egy vagy több ezeknek az elemeknek írnak elő a hívási kvótát az API műveleteit. Termék, API és művelet hívás kvótái egymástól függetlenül vonatkoznak. Művelet lehet akár hivatkozott `name` vagy `id`. Ha az attribútumok vannak megadva, `id` fogja használni, és `name` figyelmen kívül hagyja. | Nem       |

### <a name="attributes"></a>Attribútumok

| Name (Név)           | Leírás                                                                                               | Szükséges                                                         | Alapértelmezett |
| -------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| név           | Az API-t vagy a kvóta vonatkozik, amelyhez a művelet neve.                                             | Igen                                                              | –     |
| Sávszélesség      | A megadott időtartam során engedélyezett kilobájt maximális száma a `renewal-period`. | Mindkét `calls`, `bandwidth`, vagy együttesen kell megadni. | –     |
| hívás          | A megadott időtartam során megengedett maximális száma a `renewal-period`.     | Mindkét `calls`, `bandwidth`, vagy együttesen kell megadni. | –     |
| megújítási időszak | Másodperc elteltével a kvóta alaphelyzetbe állítja az adott időszakban.                                                  | Igen                                                              | –     |

### <a name="usage"></a>Használat

Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **A házirend-szakaszok:** bejövő
-   **A házirend-hatókörök:** termék

## <a name="SetUsageQuotaByKey"></a> Kulcs használati kvóta beállítása

> [!IMPORTANT]
> Ez a funkció nem érhető el a **fogyasztás** szinten az API Management.

A `quota-by-key` a szabályzat kötelezővé teszi a megújuló vagy élettartama hívás kötet és/vagy a sávszélesség kvóta, kulcs szerint történik. A kulcs egy tetszés szerinti karakterlánc értéke lehet, és általában biztosított egy házirend-kifejezés használatával. Adja meg, mely kérelmek kell számolni a kvóta felé választható növekmény feltétel is hozzáadhatók. Ha több házirend ugyanazt a kulcsérték szeretné növelni, azt minden csak egyszer kérelmenként. A hívás korlát elérésekor a hívó kap egy `403 Forbidden` válasz állapotkódja.

További információk és példák a szabályzat: [speciális kérésszabályzás az Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).

### <a name="policy-statement"></a>Házirendutasítás

```xml
<quota-by-key calls="number"
              bandwidth="kilobytes"
              renewal-period="seconds"
              increment-condition="condition"
              counter-key="key value" />

```

### <a name="example"></a>Példa

A következő példában a kvóta a hívó IP-címe van ellátva.

```xml
<policies>
    <inbound>
        <base />
        <quota-by-key calls="10000" bandwidth="40000" renewal-period="3600"
                      increment-condition="@(context.Response.StatusCode >= 200 && context.Response.StatusCode < 400)"
                      counter-key="@(context.Request.IpAddress)" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elemek

| Name (Név)  | Leírás   | Kötelező |
| ----- | ------------- | -------- |
| kvóta | A gyökérelem. | Igen      |

### <a name="attributes"></a>Attribútumok

| Name (Név)                | Leírás                                                                                               | Szükséges                                                         | Alapértelmezett |
| ------------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| Sávszélesség           | A megadott időtartam során engedélyezett kilobájt maximális száma a `renewal-period`. | Mindkét `calls`, `bandwidth`, vagy együttesen kell megadni. | –     |
| hívás               | A megadott időtartam során megengedett maximális száma a `renewal-period`.     | Mindkét `calls`, `bandwidth`, vagy együttesen kell megadni. | –     |
| másik kulcsot         | A kvóta szabályzathoz használandó kulcs.                                                                      | Igen                                                              | –     |
| increment-condition | A logikai kifejezés megadása, ha a kérés kell számolni a kvóta felé (`true`)             | Nem                                                               | –     |
| megújítási időszak      | Másodperc elteltével a kvóta alaphelyzetbe állítja az adott időszakban.                                                  | Igen                                                              | –     |

### <a name="usage"></a>Használat

Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **A házirend-szakaszok:** bejövő
-   **A házirend-hatókörök:** globális, termék, API-művelet

## <a name="ValidateJWT"></a> JWT ellenőrzése

A `validate-jwt` a szabályzat kötelezővé teszi létezik-e, és a jwt-t érvényességét kinyert vagy egy meghatározott HTTP-fejléc vagy a megadott lekérdezési paraméter.

> [!IMPORTANT]
> A `validate-jwt` szabályzat megköveteli, hogy a `exp` regisztrált jogcím szerepel a JWT jogkivonat, kivéve, ha `require-expiration-time` attribútum van megadva, és állítsa `false`.
> A `validate-jwt` szabályzat HS256 és RS256 aláírási algoritmusokat támogatja. HS256 a kulcsot meg kell adni a házirend a base64-kódolású képernyőn belül beágyazott. A kulcs RS256 kell adnia egy Open ID konfigurációs végponton keresztül rendelkezik.
> A `validate-jwt` szabályzat titkosítja a szimmetrikus kulcsok használata a következő titkosítási algoritmusok A128CBC-HS256, A192CBC-HS384, A256CBC-HS512 jogkivonatokat támogatja.

### <a name="policy-statement"></a>Házirendutasítás

```xml
<validate-jwt
    header-name="name of http header containing the token (use query-parameter-name attribute if the token is passed in the URL)"
    failed-validation-httpcode="http status code to return on failure"
    failed-validation-error-message="error message to return on failure"
    token-value="expression returning JWT token as a string"
    require-expiration-time="true|false"
    require-scheme="scheme"
    require-signed-tokens="true|false"
    clock-skew="allowed clock skew in seconds"
    output-token-variable-name="name of a variable to receive a JWT object representing successfully validated token">
  <issuer-signing-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </issuer-signing-keys>
  <decryption-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </decryption-keys>
  <audiences>
    <audience>audience string</audience>
    <!-- if there are multiple possible audiences, then add additional audience elements -->
  </audiences>
  <issuers>
    <issuer>issuer string</issuer>
    <!-- if there are multiple possible issuers, then add additional issuer elements -->
  </issuers>
  <required-claims>
    <claim name="name of the claim as it appears in the token" match="all|any" separator="separator character in a multi-valued claim">
      <value>claim value as it is expected to appear in the token</value>
      <!-- if there is more than one allowed values, then add additional value elements -->
    </claim>
    <!-- if there are multiple possible allowed values, then add additional value elements -->
  </required-claims>
  <openid-config url="full URL of the configuration endpoint, e.g. https://login.constoso.com/openid-configuration" />
  <zumo-master-key id="key identifier">key value</zumo-master-key>
</validate-jwt>

```

### <a name="examples"></a>Példák

#### <a name="simple-token-validation"></a>Egyszerű jogkivonat érvényesítése

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key>  <!-- signing key specified as a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>  <!-- audience is set to API Management host name -->
    </audiences>
    <issuers>
        <issuer>http://contoso.com/</issuer>
    </issuers>
</validate-jwt>
```

#### <a name="azure-active-directory-token-validation"></a>Az Azure Active Directory-jogkivonat érvényesítésére

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration" />
    <audiences>
        <audience>25eef6e4-c905-4a07-8eb4-0d08d5df8b3f</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="azure-active-directory-b2c-token-validation"></a>Az Azure Active Directory B2C jogkivonat érvényesítése

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/b2c_1_signin/v2.0/.well-known/openid-configuration" />
    <audiences>
        <audience>d313c4e4-de5f-4197-9470-e509a2f0b806</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="authorize-access-to-operations-based-on-token-claims"></a>A jogkivonat alapján műveleteket hozzáférés engedélyezése

Ez a példa bemutatja, hogyan használhatja a [ellenőrzése JWT](api-management-access-restriction-policies.md#ValidateJWT) műveletek való hozzáférés engedélyezése a házirend jogkivonat érték alapján.

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer" output-token-variable-name="jwt">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key> <!-- signing key is stored in a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>
    </audiences>
    <issuers>
        <issuer>contoso.com</issuer>
    </issuers>
    <required-claims>
        <claim name="group" match="any">
            <value>finance</value>
            <value>logistics</value>
        </claim>
    </required-claims>
</validate-jwt>
<choose>
    <when condition="@(context.Request.Method == "POST" && !((Jwt)context.Variables["jwt"]).Claims["group"].Contains("finance"))">
        <return-response>
            <set-status code="403" reason="Forbidden" />
        </return-response>
    </when>
</choose>
```

#### <a name="azure-mobile-services-token-validation"></a>Az Azure Mobile Services jogkivonat érvényesítése

```xml
<validate-jwt header-name="x-zumo-auth" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Supplied access token is invalid.">
    <issuers>
        <issuer>urn:microsoft:windows-azure:zumo</issuer>
    </issuers>
    <audiences>
        <audience>Facebook</audience>
    </audiences>
    <issuer-signing-keys>
        <zumo-master-key id="0">insert key here</zumo-master-key>
    </issuer-signing-keys>
</validate-jwt>
```

### <a name="elements"></a>Elemek

| Elem             | Leírás                                                                                                                                                                                                                                                                                                                                           | Szükséges |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-jwt        | A gyökérelem.                                                                                                                                                                                                                                                                                                                                         | Igen      |
| Célközönség           | Elfogadható célközönség lehet, hogy a jogkivonatban található jogcímeket listáját tartalmazza. Ha több célközönségértékek találhatók, akkor minden értéket a rendszer megpróbálkozik amíg újra nem indítják az összes kimerültek (ebben az esetben az érvényesítés sikertelen), vagy amíg sikerrel. Legalább egy célközönség meg kell adni.                                                                     | Nem       |
| issuer-signing-keys | Aláírt jogkivonatokat érvényesítéséhez használt Base64-kódolású biztonsági kulcsok listája. Ha több biztonsági kulcsok jelen, akkor minden kulcs a rendszer megpróbálkozik amíg újra nem indítják az összes kimerültek (ebben az esetben az érvényesítés sikertelen), vagy amíg sikerrel (token helyettesítő hasznos). Fő elemei rendelkezik egy nem kötelező `id` szűréséhez használt attribútum `kid` jogcím.               | Nem       |
| visszafejtési kulcsok     | A jogkivonatok visszafejtését teszi Base64-kódolású kulcsok listája. Ha több biztonsági kulcs megadva, majd minden egyes kulcs próbálkozik erejéig vagy az összes kulcs (ebben az esetben az érvényesítés sikertelen), vagy amíg be nem fejeződik a kulcsot. Fő elemei rendelkezik egy nem kötelező `id` szűréséhez használt attribútum `kid` jogcím.                                                 | Nem       |
| tanúsítványkibocsátók             | Amely kiállította a jogkivonatot elfogadható rendszerbiztonsági tagok listáját. Ha több kiállítók a következők találhatók, akkor a rendszer megpróbálkozik az egyes értékek amíg újra nem indítják az összes kimerültek (ebben az esetben az érvényesítés sikertelen), vagy amíg sikerrel.                                                                                                                                         | Nem       |
| openid-config       | Az elem, amely aláírási kulcsokat és a kiállító szerezhető be megfelelő Open ID konfigurációs végpont megadására használt.                                                                                                                                                                                                                        | Nem       |
| szükséges jogcímek     | A jogcímeket kell lennie ahhoz, hogy nem érvényes a jogkivonat található listáját tartalmazza. Ha a `match` attribútum `all` a szabályzat minden jogcím értéke sikeres ellenőrzés céljából a jogkivonatban jelen kell lennie. Ha a `match` attribútum `any` legalább egy jogcím sikeres ellenőrzés céljából a jogkivonatban jelen kell lennie. | Nem       |
| zumo-master-key     | Az Azure Mobile Services által kiállított jogkivonatokban főkulcs                                                                                                                                                                                                                                                                                                 | Nem       |

### <a name="attributes"></a>Attribútumok

| Name (Név)                            | Leírás                                                                                                                                                                                                                                                                                                                                                                                                                                            | Kötelező                                                                         | Alapértelmezett                                                                           |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| időbeállításainak                      | Időtartam. Adja meg a maximális várt időeltérése a jogkivonat kibocsátója rendszerideje és az API Management-példány használatával.                                                                                                                                                                                                                                                                                                               | Nem                                                                               | 0 másodperc                                                                         |
| failed-validation-error-message | A HTTP-válasz törzsében adja vissza, ha a JWT nem felel meg az érvényesítési hibaüzenet. Ez az üzenet blobnév különleges karaktereket kell rendelkeznie.                                                                                                                                                                                                                                                                                                 | Nem                                                                               | Alapértelmezett hibaüzenet függ érvényesítési probléma, például "JWT nem található." |
| failed-validation-httpcode      | HTTP-állapot kódot ad vissza, ha a JWT fennakadt érvényesítése.                                                                                                                                                                                                                                                                                                                                                                                         | Nem                                                                               | 401                                                                               |
| header-name                     | A HTTP-fejlécet a tokent tároló neve.                                                                                                                                                                                                                                                                                                                                                                                                         | Egyik `header-name`, `query-parameter-name` vagy `token-value` meg kell adni. | –                                                                               |
| query-parameter-name            | A lekérdezési paraméter, a tokent tároló neve.                                                                                                                                                                                                                                                                                                                                                                                                     | Egyik `header-name`, `query-parameter-name` vagy `token-value` meg kell adni. | –                                                                               |
| jogkivonat-érték                     | JWT jogkivonatot tartalmazó karakterlánc visszaadó kifejezés                                                                                                                                                                                                                                                                                                                                                                                                     | Egyik `header-name`, `query-parameter-name` vagy `token-value` meg kell adni. | –                                                                               |
| azonosító                              | A `id` az attribútum a `key` elem lehetővé teszi, hogy adja meg a sztringet, amelyet a rendszer összeveti `kid` jogcímet a token (ha van ilyen) ismerje meg, hogy a megfelelő kulcsot az aláírás-ellenőrzés céljából.                                                                                                                                                                                                                                           | Nem                                                                               | –                                                                               |
| egyezés                           | A `match` az attribútum a `claim` elem azt határozza meg, hogy a szabályzat minden jogcím értéke a jogkivonat az érvényesítés sikeres jelen kell lennie. Lehetséges értékek a következők:<br /><br /> - `all` – a szabályzat minden jogcím értéke sikeres ellenőrzés céljából a jogkivonatban jelen kell lennie.<br /><br /> - `any` -legalább egy jogcím értéke sikeres ellenőrzés céljából a jogkivonatban jelen kell lennie.                                                       | Nem                                                                               | mind                                                                               |
| require-expiration-time         | Logikai érték. Megadja, hogy szükséges-e a jogkivonat lejárati jogcím.                                                                                                                                                                                                                                                                                                                                                                               | Nem                                                                               | true                                                                              |
| szükséges rendszer                  | A token neve séma, például: "Tulajdonos". Ez az attribútum beállításra kerül, ha a házirend biztosítja, hogy a megadott séma szerepel az engedélyezési fejléc értéke.                                                                                                                                                                                                                                                                                    | Nem                                                                               | –                                                                               |
| igényelnek-aláírt-jogkivonatok           | Logikai érték. Megadja, hogy a jogkivonat szükséges alá legyen írva.                                                                                                                                                                                                                                                                                                                                                                                           | Nem                                                                               | true                                                                              |
| elválasztó                       | karakterlánc. Adja meg az elválasztó (pl. ",") értékek kinyerését többértékű jogcím használható.                                                                                                                                                                                                                                                                                                                                          | Nem                                                                               | –                                                                               |
| url                             | Nyissa meg az azonosító konfigurációs végpont URL-címe, amennyiben szerezhető be Open ID konfigurációs metaadatok. A válaszban meg kell felelnie a vonatkozó műszaki adatok, meghatározott URL-címen:`https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata`. Az Azure Active Directory a következő URL-cím használata: `https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration` és helyettesítse be a directory-bérlő neve, pl. `contoso.onmicrosoft.com`. | Igen                                                                              | –                                                                               |
output-token-variable-name|karakterlánc. Környezeti változó, amely a token értéket kap olyan típusú objektum nevét [ `Jwt` ](api-management-policy-expressions.md) sikeres jogkivonat érvényesítése után|Nem|–

### <a name="usage"></a>Használat

Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **A házirend-szakaszok:** bejövő
-   **A házirend-hatókörök:** globális, termék, API-művelet

## <a name="next-steps"></a>További lépések

Házirendek használata további információkért lásd:

-   [Az API Management házirendek](api-management-howto-policies.md)
-   [API-k átalakítása](transform-api.md)
-   [Házirend-referencia](api-management-policy-reference.md) házirend-utasítások és a beállítások teljes listáját
-   [A házirend-minták](policy-samples.md)

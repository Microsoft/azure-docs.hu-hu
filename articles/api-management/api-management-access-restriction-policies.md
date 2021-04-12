---
title: Azure API Management hozzáférési korlátozási szabályzatok | Microsoft Docs
description: Ismerje meg az Azure API Management használható hozzáférés-korlátozási szabályzatokat.
services: api-management
documentationcenter: ''
author: vladvino
ms.assetid: 034febe3-465f-4840-9fc6-c448ef520b0f
ms.service: api-management
ms.topic: article
ms.date: 02/26/2021
ms.author: apimpm
ms.openlocfilehash: 882d96271b6976db1ffc0dde181d5699c5cc27de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101688246"
---
# <a name="api-management-access-restriction-policies"></a>API Management hozzáférés-korlátozási szabályzatok

Ez a témakör az alábbi API Management szabályzatokra mutató hivatkozást tartalmaz. A házirendek hozzáadásával és konfigurálásával kapcsolatos információkért lásd: [szabályzatok API Management](./api-management-policies.md).

## <a name="access-restriction-policies"></a><a name="AccessRestrictionPolicies"></a> Hozzáférés-korlátozási szabályzatok

-   [Http](#CheckHTTPHeader) -fejléc keresése – egy HTTP-fejléc létezésének és/vagy értékének betartatása.
-   A [hívások sebességének korlátozása előfizetéssel](#LimitCallRate) – MEGAKADÁLYOZZA az API-használat csúcsait a hívások sebességének korlátozásával, előfizetések alapján.
-   A [hívások gyakoriságának korlátozása kulcs szerint](#LimitCallRateByKey) – MEGAKADÁLYOZZA az API-használat csúcsait a hívások sebességének korlátozásával, kulcs alapján.
-   A [hívó IP](#RestrictCallerIPs) -címeinek korlátozása (engedélyezi vagy megtagadja) a HÍVÁSOKAT adott IP-címekről és/vagy címtartományból.
-   [Használati kvóta beállítása előfizetéssel](#SetUsageQuota) – lehetővé teszi, hogy előfizetések alapján kikényszerítse a megújítható vagy az élettartam szerinti hívások mennyiségét és/vagy sávszélesség-kvótáját.
-   [Használati kvóta beállítása kulccsal](#SetUsageQuotaByKey) – lehetővé teszi a megújítható vagy élettartamos hívások mennyiségi és/vagy sávszélesség-kvótájának kikényszeríthető kulcs alapján.
-   [JWT ellenőrzése](#ValidateJWT) – egy adott http-fejlécből vagy egy megadott lekérdezési paraméterből kinyert JWT létezését és érvényességét kényszeríti ki.

> [!TIP]
> A hozzáférési korlátozási házirendeket különböző hatókörökben használhatja különböző célokra. A teljes API-t biztonságossá teheti a HRE-hitelesítéssel, ha a `validate-jwt` szabályzatot az API szintjén alkalmazza, vagy az API-műveleti szinten alkalmazhatja, és `claims` részletesebb szabályozást is használhat.

## <a name="check-http-header"></a><a name="CheckHTTPHeader"></a> HTTP-fejléc keresése

A `check-header` szabályzat használatával kényszerítheti ki, hogy egy kérelemnek van egy megadott http-fejléce. Ha szeretné, megtekintheti, hogy a fejléc adott értékkel rendelkezik-e, vagy hogy engedélyezett-e az értékek tartománya. Ha az ellenőrzés sikertelen, a házirend leállítja a kérelmek feldolgozását, és visszaadja a szabályzat által megadott HTTP-állapotkódot és hibaüzenetet.

### <a name="policy-statement"></a>Szabályzati utasítás

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

| Név         | Leírás                                                                                                                                   | Kötelező |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| fejléc bejelölése | Gyökérelem.                                                                                                                                 | Yes      |
| érték        | Engedélyezett HTTP-fejléc értéke. Ha több Value elem van megadva, az ellenőrzése sikeresnek tekintendő, ha az értékek bármelyike egyezés. | No       |

### <a name="attributes"></a>Attribútumok

| Név                       | Leírás                                                                                                                                                            | Kötelező | Alapértelmezett |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| sikertelen művelet – ellenőrzési hiba – üzenet | A HTTP-válasz törzsében visszaadott hibaüzenet, ha a fejléc nem létezik, vagy érvénytelen értékkel rendelkezik. Az üzenetnek megfelelő speciális karakterekkel kell elmenekülnie. | Yes      | N/A     |
| Sikertelen bejelentkezés – httpcode      | HTTP-állapotkód, amely akkor tér vissza, ha a fejléc nem létezik vagy érvénytelen értékkel rendelkezik.                                                                                        | Yes      | N/A     |
| fejléc neve                | Az ellenőriznie kívánt HTTP-fejléc neve.                                                                                                                                  | Yes      | N/A     |
| Mellőzés – eset                | Értéke TRUE (igaz) vagy FALSE (hamis) lehet. Ha a True (igaz) értékre van állítva, a rendszer figyelmen kívül hagyja, ha a fejléc értékét összehasonlítja az elfogadható értékek halmazával.                                    | Yes      | N/A     |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.

-   **Házirend fejezetei:** bejövő, kimenő

-   **Házirend-hatókörök:** az összes hatókör

## <a name="limit-call-rate-by-subscription"></a><a name="LimitCallRate"></a> A hívások sebességének korlátozása előfizetés szerint

A `rate-limit` házirend előfizetések alapján megakadályozza az API-használati tüskéket azáltal, hogy a megadott időszakra korlátozza a hívások sebességét egy megadott számra. Ha túllépi a hívási arányt, a hívó megkapja a `429 Too Many Requests` Válasz állapotkódot.

> [!IMPORTANT]
> Ez a szabályzat csak egyszer használható házirend-dokumentumként.
>
> A szabályzat [kifejezései](api-management-policy-expressions.md) nem használhatók a házirend egyik házirend-attribútumában sem.

> [!CAUTION]
> A szabályozási architektúra elosztott jellege miatt a díjszabási korlátozás soha nem teljesen pontos. A konfigurált és a valós idejű kérelmek száma közötti különbség a kérés mennyisége és sebessége, a háttérbeli késés és egyéb tényezők alapján változhat.

> [!NOTE]
> A díjszabási korlátok és kvóták közötti különbség megismeréséhez [lásd a díjszabási korlátokat és a kvótákat.](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<rate-limit calls="number" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" 
        retry-after-header-name="header name" 
        retry-after-variable-name="policy expression variable name"
        remaining-calls-header-name="header name"  
        remaining-calls-variable-name="policy expression variable name"
        total-calls-header-name="header name"/>
    </api>
</rate-limit>
```

### <a name="example"></a>Példa

A következő példában az előfizetések maximális száma 20 hívás/90 másodperc. Az egyes szabályzatok végrehajtása után az adott időszakban engedélyezett fennmaradó hívásokat a változó tárolja `remainingCallsPerSubscription` .

```xml
<policies>
    <inbound>
        <base />
        <rate-limit calls="20" renewal-period="90" remaining-calls-variable-name="remainingCallsPerSubscription"/>
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elemek

| Név       | Leírás                                                                                                                                                                                                                                                                                              | Kötelező |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| korlát mértéke | Gyökérelem.                                                                                                                                                                                                                                                                                            | Yes      |
| api        | Vegyen fel egy vagy több ilyen elemet a terméken belüli API-k hívási arányának korlátozására. A termék-és API-hívások díjszabását a rendszer egymástól függetlenül alkalmazza. Az API-t a vagy a használatával lehet hivatkozni `name` `id` . Ha mindkét attribútum meg van adva, a rendszer `id` ezt fogja használni, és `name` figyelmen kívül hagyja.                    | No       |
| művelet  | Vegyen fel egy vagy több ilyen elemet egy API-n belüli műveletek hívási sebességének korlátozására. A termék, az API és a műveleti hívási sebesség korlátai egymástól függetlenül érvényesek. A műveletet a vagy a használatával lehet hivatkozni `name` `id` . Ha mindkét attribútum meg van adva, a rendszer `id` ezt fogja használni, és `name` figyelmen kívül hagyja. | No       |

### <a name="attributes"></a>Attribútumok

| Név           | Leírás                                                                                           | Kötelező | Alapértelmezett |
| -------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| name           | Annak az API-nak a neve, amelyre alkalmazni szeretné a díjszabási korlátot.                                                | Yes      | N/A     |
| hívások          | A által megadott időintervallumban engedélyezett hívások maximális száma összesen `renewal-period` | Yes      | N/A     |
| megújítás – időszak | A lecsúszó ablak hossza (másodpercben), amely alatt az engedélyezett kérelmek száma nem haladhatja meg a ben megadott értéket `calls` .                                              | Yes      | N/A     |
| újrapróbálkozást követő fejléc neve    | Annak a válasz fejlécnek a neve, amelynek értéke az ajánlott újrapróbálkozási időköz másodpercben a megadott hívási arány túllépése után. |  No | N/A  |
| újrapróbálkozást követő változó neve    | Egy olyan házirend-kifejezési változó neve, amely a megadott hívási arány túllépése után másodpercek alatt tárolja a javasolt újrapróbálkozási időközt. |  No | N/A  |
| hátralévő – hívások – fejléc – név    | A válasz fejlécének neve, amelynek értéke az egyes házirendek végrehajtása után a által megadott időintervallumban engedélyezett hátralévő hívások száma `renewal-period` . |  No | N/A  |
| fennmaradó hívások – változó – név    | Egy olyan házirend-kifejezési változó neve, amely az egyes házirendek végrehajtása után a alkalmazásban megadott időintervallumban engedélyezett hátralévő hívások számát tárolja `renewal-period` . |  No | N/A  |
| összes hívás – fejléc-név    | A válasz fejlécének neve, amelynek értéke a ben megadott érték `calls` . |  No | N/A  |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.

-   **Házirend fejezetei:** bejövő

-   **Házirend-hatókörök:** termék, API, művelet

## <a name="limit-call-rate-by-key"></a><a name="LimitCallRateByKey"></a> A hívások sebességének korlátozása kulcs szerint

> [!IMPORTANT]
> Ez a funkció nem érhető el a API Management **felhasználási** szintjein.

A `rate-limit-by-key` házirend a megadott időszakra korlátozva meggátolja a hívások sebességét egy adott számra. A kulcs tetszőleges karakterlánc-értékkel rendelkezhet, és általában egy házirend-kifejezés használatával adható meg. Opcionális növekményes feltétel adható meg annak megadásához, hogy mely kérelmeket kell figyelembe venni a korláton. Ha túllépi a hívási arányt, a hívó megkapja a `429 Too Many Requests` Válasz állapotkódot.

A szabályzattal kapcsolatos további információkért és Példákért lásd: [speciális kérelmek szabályozása az Azure API Management](./api-management-sample-flexible-throttling.md).

> [!CAUTION]
> A szabályozási architektúra elosztott jellege miatt a díjszabási korlátozás soha nem teljesen pontos. A konfigurált és a valós idejű kérelmek száma közötti különbség a kérés mennyisége és sebessége, a háttérbeli késés és egyéb tényezők alapján változhat.

> [!NOTE]
> A díjszabási korlátok és kvóták közötti különbség megismeréséhez [lásd a díjszabási korlátokat és a kvótákat.](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<rate-limit-by-key calls="number"
                   renewal-period="seconds"
                   increment-condition="condition"
                   counter-key="key value" 
                   retry-after-header-name="header name" retry-after-variable-name="policy expression variable name"
                   remaining-calls-header-name="header name"  remaining-calls-variable-name="policy expression variable name"
                   total-calls-header-name="header name"/> 

```

### <a name="example"></a>Példa

A következő példában a hívó IP-címe szerint a 10 hívás/60 másodpercenkénti sebességre vonatkozó korlátot a rendszer megadja. Az egyes szabályzatok végrehajtása után az adott időszakban engedélyezett fennmaradó hívásokat a változó tárolja `remainingCallsPerIP` .

```xml
<policies>
    <inbound>
        <base />
        <rate-limit-by-key  calls="10"
              renewal-period="60"
              increment-condition="@(context.Response.StatusCode == 200)"
              counter-key="@(context.Request.IpAddress)"
              remaining-calls-variable-name="remainingCallsPerIP"/>
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elemek

| Név              | Leírás   | Kötelező |
| ----------------- | ------------- | -------- |
| mérték – korlát – kulcs | Gyökérelem. | Yes      |

### <a name="attributes"></a>Attribútumok

| Név                | Leírás                                                                                           | Kötelező | Alapértelmezett |
| ------------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| hívások               | A által megadott időintervallumban engedélyezett hívások maximális száma `renewal-period` . | Yes      | N/A     |
| számláló – kulcs         | A díjszabási szabályzathoz használandó kulcs.                                                             | Yes      | N/A     |
| növekmény – feltétel | Az a logikai kifejezés, amely megadja, hogy a kérést a ráta () felé kell-e számítani `true` .        | No       | N/A     |
| megújítás – időszak      | A lecsúszó ablak hossza (másodpercben), amely alatt az engedélyezett kérelmek száma nem haladhatja meg a ben megadott értéket `calls` .                                           | Yes      | N/A     |
| újrapróbálkozást követő fejléc neve    | Annak a válasz fejlécnek a neve, amelynek értéke az ajánlott újrapróbálkozási időköz másodpercben a megadott hívási arány túllépése után. |  No | N/A  |
| újrapróbálkozást követő változó neve    | Egy olyan házirend-kifejezési változó neve, amely a megadott hívási arány túllépése után másodpercek alatt tárolja a javasolt újrapróbálkozási időközt. |  No | N/A  |
| hátralévő – hívások – fejléc – név    | A válasz fejlécének neve, amelynek értéke az egyes házirendek végrehajtása után a által megadott időintervallumban engedélyezett hátralévő hívások száma `renewal-period` . |  No | N/A  |
| fennmaradó hívások – változó – név    | Egy olyan házirend-kifejezési változó neve, amely az egyes házirendek végrehajtása után a alkalmazásban megadott időintervallumban engedélyezett hátralévő hívások számát tárolja `renewal-period` . |  No | N/A  |
| összes hívás – fejléc-név    | A válasz fejlécének neve, amelynek értéke a ben megadott érték `calls` . |  No | N/A  |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.

-   **Házirend fejezetei:** bejövő

-   **Házirend-hatókörök:** az összes hatókör

## <a name="restrict-caller-ips"></a><a name="RestrictCallerIPs"></a> Hívó IP-címeinek korlátozása

A `ip-filter` szabályzat szűrői (engedélyezheti/megtagadja) a hívásokat adott IP-címekről, illetve címtartományból.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address" />
</ip-filter>
```

### <a name="example"></a>Példa

A következő példában a házirend csak az egyetlen IP-címről vagy a megadott IP-címtartományból érkező kéréseket engedélyezi

```xml
<ip-filter action="allow">
    <address>13.66.201.169</address>
    <address-range from="13.66.140.128" to="13.66.140.143" />
</ip-filter>
```

### <a name="elements"></a>Elemek

| Név                                      | Leírás                                         | Kötelező                                                       |
| ----------------------------------------- | --------------------------------------------------- | -------------------------------------------------------------- |
| IP-szűrő                                 | Gyökérelem.                                       | Yes                                                            |
| address                                   | Egyetlen IP-címet ad meg a szűréshez.   | Legalább egy `address` `address-range` elemet kötelező megadni. |
| címtartomány a következőből: = "címe" – = "címe" | A szűrni kívánt IP-címtartomány megadása. | Legalább egy `address` `address-range` elemet kötelező megadni. |

### <a name="attributes"></a>Attribútumok

| Név                                      | Leírás                                                                                 | Kötelező                                           | Alapértelmezett |
| ----------------------------------------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------- | ------- |
| címtartomány a következőből: = "címe" – = "címe" | IP-címek tartománya, amely engedélyezi vagy megtagadja a hozzáférést.                                        | A `address-range` elem használatakor szükséges. | N/A     |
| IP-szűrési művelet = "&#124; tiltás engedélyezése"    | Megadja, hogy a hívások engedélyezettek-e, vagy sem a megadott IP-címekhez és tartományokhoz. | Yes                                                | N/A     |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.

-   **Házirend fejezetei:** bejövő
-   **Házirend-hatókörök:** az összes hatókör

## <a name="set-usage-quota-by-subscription"></a><a name="SetUsageQuota"></a> Használati kvóta beállítása előfizetés szerint

A `quota` szabályzat előfizetése alapján kikényszeríti a megújítható vagy életprevalencia-hívások mennyiségét és/vagy sávszélesség-kvótáját.

> [!IMPORTANT]
> Ez a szabályzat csak egyszer használható házirend-dokumentumként.
>
> A szabályzat [kifejezései](api-management-policy-expressions.md) nem használhatók a házirend egyik házirend-attribútumában sem.

> [!NOTE]
> A díjszabási korlátok és kvóták közötti különbség megismeréséhez [lásd a díjszabási korlátokat és a kvótákat.](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)

### <a name="policy-statement"></a>Szabályzati utasítás

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

| Név      | Leírás                                                                                                                                                                                                                                                                                  | Kötelező |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| kvóta     | Gyökérelem.                                                                                                                                                                                                                                                                                | Yes      |
| api       | Vegyen fel egy vagy több ilyen elemet a terméken belüli API-kra vonatkozó hívási kvóta kiszabásához. A termék-és API-hívások kvótái egymástól függetlenül vannak alkalmazva. Az API-t a vagy a használatával lehet hivatkozni `name` `id` . Ha mindkét attribútum meg van adva, a rendszer `id` ezt fogja használni, és `name` figyelmen kívül hagyja.                    | No       |
| művelet | Vegyen fel egy vagy több ilyen elemet egy API-n belüli műveletekre vonatkozó hívási kvóta kiszabásához. A termék-, API-és műveleti hívások kvótái egymástól függetlenül vannak alkalmazva. A műveletet a vagy a használatával lehet hivatkozni `name` `id` . Ha mindkét attribútum meg van adva, a rendszer `id` ezt fogja használni, és `name` figyelmen kívül hagyja. | No      |

### <a name="attributes"></a>Attribútumok

| Név           | Leírás                                                                                               | Kötelező                                                         | Alapértelmezett |
| -------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| name           | Annak az API-nak vagy műveletnek a neve, amelyre a kvóta vonatkozik.                                             | Yes                                                              | N/A     |
| sávszélesség      | A-ben megadott időintervallumban engedélyezett maximálisan megengedett kilobájtok száma `renewal-period` . | `calls`Vagy, `bandwidth` vagy mindkettőt meg kell adni. | N/A     |
| hívások          | A által megadott időintervallumban engedélyezett hívások maximális száma `renewal-period` .     | `calls`Vagy, `bandwidth` vagy mindkettőt meg kell adni. | N/A     |
| megújítás – időszak | Az az időtartam másodpercben, amely után a kvóta alaphelyzetbe áll. Ha `0` a pontra van állítva, a végtelen értékre van állítva. | Yes                                                              | N/A     |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.

-   **Házirend fejezetei:** bejövő
-   **Házirend-hatókörök:** termék

## <a name="set-usage-quota-by-key"></a><a name="SetUsageQuotaByKey"></a> Használati kvóta beállítása kulcs szerint

> [!IMPORTANT]
> Ez a funkció nem érhető el a API Management **felhasználási** szintjein.

A `quota-by-key` szabályzat a megújítható vagy az élettartam szerinti hívások mennyiségét és/vagy sávszélességét kényszeríti ki a kulcs alapján. A kulcs tetszőleges karakterlánc-értékkel rendelkezhet, és általában egy házirend-kifejezés használatával adható meg. Opcionális növekményes feltétel adható meg annak megadásához, hogy mely kérelmeket kell figyelembe venni a kvóta felé. Ha több házirend is megnöveli a kulcs értékét, akkor a kérések száma csak egyszer növekszik. Ha túllépi a hívási arányt, a hívó megkapja a `403 Forbidden` Válasz állapotkódot.

A szabályzattal kapcsolatos további információkért és Példákért lásd: [speciális kérelmek szabályozása az Azure API Management](./api-management-sample-flexible-throttling.md).

> [!NOTE]
> A díjszabási korlátok és kvóták közötti különbség megismeréséhez [lásd a díjszabási korlátokat és a kvótákat.](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<quota-by-key calls="number"
              bandwidth="kilobytes"
              renewal-period="seconds"
              increment-condition="condition"
              counter-key="key value" />

```

### <a name="example"></a>Példa

A következő példában a kvóta a hívó IP-címe alapján van megjelölve.

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

| Név  | Leírás   | Kötelező |
| ----- | ------------- | -------- |
| kvóta | Gyökérelem. | Yes      |

### <a name="attributes"></a>Attribútumok

| Név                | Leírás                                                                                               | Kötelező                                                         | Alapértelmezett |
| ------------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| sávszélesség           | A-ben megadott időintervallumban engedélyezett maximálisan megengedett kilobájtok száma `renewal-period` . | `calls`Vagy, `bandwidth` vagy mindkettőt meg kell adni. | N/A     |
| hívások               | A által megadott időintervallumban engedélyezett hívások maximális száma `renewal-period` .     | `calls`Vagy, `bandwidth` vagy mindkettőt meg kell adni. | N/A     |
| számláló – kulcs         | A kvóta-házirendhez használandó kulcs.                                                                      | Yes                                                              | N/A     |
| növekmény – feltétel | A logikai kifejezés, amely megadja, hogy a kérést a kvóta felé kell-e számítani ( `true` )             | No                                                               | N/A     |
| megújítás – időszak      | Az az időtartam másodpercben, amely után a kvóta alaphelyzetbe áll. Ha `0` a pontra van állítva, a végtelen értékre van állítva.                                                   | Yes                                                              | N/A     |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.

-   **Házirend fejezetei:** bejövő
-   **Házirend-hatókörök:** az összes hatókör

## <a name="validate-jwt"></a><a name="ValidateJWT"></a> JWT ellenőrzése

A `validate-jwt` szabályzat egy adott http-fejlécből vagy egy megadott lekérdezési paraméterből kinyert JSON webes jogkivonat (JWT) létezését és érvényességét kényszeríti ki.

> [!IMPORTANT]
> A `validate-jwt` szabályzat megköveteli, hogy a `exp` regisztrált jogcím SZEREPELJEN az JWT-tokenben, kivéve, ha az `require-expiration-time` attribútum meg van adva, és a értékre van állítva `false` .
> A `validate-jwt` szabályzat támogatja a HS256 és a RS256 aláírási algoritmusokat. A HS256 a kulcsot a Base64 kódolású űrlapon belül kell megadni a szabályzatban. A RS256 a kulcs egy nyitott azonosító konfigurációs végponton keresztül vagy egy olyan feltöltött tanúsítvány AZONOSÍTÓjának megadásával biztosítható, amely tartalmazza a nyilvános kulcs vagy modulus-kitevő párt.
> A `validate-jwt` házirend támogatja a szimmetrikus kulcsokkal titkosított tokeneket a következő titkosítási algoritmusok használatával: A128CBC-HS256, A192CBC-HS384, A256CBC-HS512.

### <a name="policy-statement"></a>Szabályzati utasítás

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
  <openid-config url="full URL of the configuration endpoint, e.g. https://login.constoso.com/openid-configuration" />
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
</validate-jwt>

```

### <a name="examples"></a>Példák

#### <a name="simple-token-validation"></a>Egyszerű jogkivonat ellenőrzése

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

#### <a name="token-validation-with-rsa-certificate"></a>Jogkivonat-érvényesítés RSA-tanúsítvánnyal

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer">
    <issuer-signing-keys>
        <key certificate-id="my-rsa-cert" />  <!-- signing key specified as certificate ID, enclosed in double-quotes -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>  <!-- audience is set to API Management host name -->
    </audiences>
    <issuers>
        <issuer>http://contoso.com/</issuer>
    </issuers>
</validate-jwt>
```

#### <a name="azure-active-directory-token-validation"></a>Azure Active Directory jogkivonat ellenőrzése

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

#### <a name="azure-active-directory-b2c-token-validation"></a>Azure Active Directory B2C jogkivonat ellenőrzése

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

#### <a name="authorize-access-to-operations-based-on-token-claims"></a>A jogkivonatok jogcímein alapuló műveletekhez való hozzáférés engedélyezése

Ez a példa azt mutatja be, hogyan használható a [JWT-érvényesítési](api-management-access-restriction-policies.md#ValidateJWT) házirend a műveletekhez való hozzáférés engedélyezésére a jogkivonat-jogcímek értéke alapján.

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

### <a name="elements"></a>Elemek

| Elem             | Leírás                                                                                                                                                                                                                                                                                                                                           | Kötelező |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| ellenőrzés – JWT        | Gyökérelem.                                                                                                                                                                                                                                                                                                                                         | Yes      |
| közönség           | A jogkivonatban található elfogadható célközönségi jogcímek listáját tartalmazza. Ha több célközönség érték van megadva, akkor minden érték az összes Kimerítés után próbálkozik (ebben az esetben az ellenőrzés meghiúsul), vagy amíg az egyik sikeres. Legalább egy célközönséget meg kell adni.                                                                     | No       |
| kiállító – aláíró kulcsok | Az aláírt tokenek ellenőrzéséhez használt Base64 kódolású biztonsági kulcsok listája. Ha több biztonsági kulcs is van, akkor a rendszer minden kulcsot megpróbál, amíg az összes kimerül (ebben az esetben az ellenőrzés meghiúsul), vagy az egyik sikeres (a jogkivonat-rollover esetében hasznos). A kulcsfontosságú elemekhez választható `id` attribútum tartozik, amely a `kid` jogcímek közötti egyezésre szolgál. <br/><br/>Alternatív megoldásként adja meg a kiállítói aláíró kulcsot a alábbiak használatával:<br/><br/> - `certificate-id` a `<key certificate-id="mycertificate" />` API Managementba [feltöltött](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity#Add) tanúsítvány-entitás azonosítójának megadásához formátummal<br/>-RSA-modulus `n` és kitevő `e` pár formátumban az `<key n="<modulus>" e="<exponent>" />` RSA-paraméterek base64url-kódolású formátumban való megadásához               | No       |
| visszafejtés – kulcsok     | A jogkivonatok visszafejtéséhez használt Base64 kódolású kulcsok listája. Ha több biztonsági kulcs is van, akkor a rendszer minden kulcsot megpróbálkozik, amíg az összes kulcs ki nem merül (ebben az esetben az ellenőrzés meghiúsul), vagy a kulcs sikeres lehet. A kulcsfontosságú elemekhez választható `id` attribútum tartozik, amely a `kid` jogcímek közötti egyezésre szolgál.<br/><br/>Alternatív megoldásként adja meg a visszafejtési kulcsot a használatával:<br/><br/> - `certificate-id` a `<key certificate-id="mycertificate" />` API Managementba [feltöltött](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity#Add) tanúsítvány-entitás azonosítójának megadásához formátummal                                                 | No       |
| kibocsátók             | A jogkivonatot kiállító elfogadható rendszerbiztonsági tag listája. Ha több kiállítói érték is létezik, akkor minden egyes értéket megpróbál a rendszer, amíg az összes ki nem fejeződik (ebben az esetben az ellenőrzés meghiúsul), vagy amíg az egyik sikeres.                                                                                                                                         | No       |
| OpenID – konfiguráció       | A megfelelőségi azonosító konfigurációs végpontjának megadásához használt elem, amelyből megszerezhetők az aláíró kulcsok és a kibocsátók.                                                                                                                                                                                                                        | No       |
| kötelező – jogcímek     | Azon jogcímek listáját tartalmazza, amelyeknek a jogkivonatban szerepelniük kell, hogy az érvényes legyen. Ha az `match` attribútum minden jogcím értékre van beállítva, akkor a `all` jogkivonatban jelen kell lennie ahhoz, hogy az érvényesítés sikeres legyen. Ha az `match` attribútum értéke `any` legalább egy jogcím, akkor a jogkivonatban jelen kell lennie ahhoz, hogy az érvényesítés sikeres legyen. | No       |

### <a name="attributes"></a>Attribútumok

| Név                            | Leírás                                                                                                                                                                                                                                                                                                                                                                                                                                            | Kötelező                                                                         | Alapértelmezett                                                                           |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| óra – döntés                      | TimeSpan. Ezzel a beállítással adható meg a jogkivonat-kiállító és a API Management-példány rendszerórája közötti maximális várható időeltérés.                                                                                                                                                                                                                                                                                                               | No                                                                               | 0 másodperc                                                                         |
| sikertelen – ellenőrzés – hiba – üzenet | A HTTP-válasz törzsében visszaadott hibaüzenet, ha a JWT nem ad át érvényesítést. Az üzenetnek megfelelő speciális karakterekkel kell elmenekülnie.                                                                                                                                                                                                                                                                                                 | No                                                                               | Az alapértelmezett hibaüzenet az érvényesítési problémától függ, például "a JWT nem jelennek meg". |
| sikertelen – érvényesítés – httpcode      | HTTP-állapotkód, amely akkor tér vissza, ha a JWT nem felel meg az érvényesítésnek.                                                                                                                                                                                                                                                                                                                                                                                         | No                                                                               | 401                                                                               |
| fejléc neve                     | A jogkivonatot birtokló HTTP-fejléc neve.                                                                                                                                                                                                                                                                                                                                                                                                         | Az egyiket `header-name` , `query-parameter-name` vagy `token-value` meg kell adni. | N/A                                                                               |
| lekérdezés-paraméter-neve            | A jogkivonatot tároló lekérdezési paraméter neve.                                                                                                                                                                                                                                                                                                                                                                                                     | Az egyiket `header-name` , `query-parameter-name` vagy `token-value` meg kell adni. | N/A                                                                               |
| jogkivonat-érték                     | JWT tokent tartalmazó sztringet visszaadó kifejezés                                                                                                                                                                                                                                                                                                                                                                                                     | Az egyiket `header-name` , `query-parameter-name` vagy `token-value` meg kell adni. | N/A                                                                               |
| id                              | Az `id` elem attribútuma `key` lehetővé teszi annak a karakterláncnak a megadását, amely `kid` a jogkivonatban található jogcímek között szerepel (ha van), hogy kiderítse az aláírás-ellenőrzéshez használandó megfelelő kulcsot.                                                                                                                                                                                                                                           | No                                                                               | N/A                                                                               |
| mérkőzés                           | Az `match` elem attribútuma `claim` azt határozza meg, hogy a házirendben szereplő összes jogcím értékének szerepelnie kell-e a jogkivonatban az érvényesítés sikerességéhez. Lehetséges értékek:<br /><br /> - `all` – a szabályzatban szereplő összes jogcím értékének jelen kell lennie a jogkivonatban, hogy az érvényesítés sikeres legyen.<br /><br /> - `any` – legalább egy jogcím értékének szerepelnie kell a jogkivonatban, hogy sikeres legyen az érvényesítés.                                                       | No                                                                               | összes                                                                               |
| lejárati idő megkövetelése         | Logikai. Meghatározza, hogy szükséges-e lejárati jogcím a jogkivonatban.                                                                                                                                                                                                                                                                                                                                                                               | No                                                                               | true                                                                              |
| szükséges – séma                  | A jogkivonat-séma neve, például "tulajdonos". Ha ez az attribútum be van állítva, akkor a házirend biztosítja, hogy a megadott séma megtalálható legyen az engedélyezési fejléc értékében.                                                                                                                                                                                                                                                                                    | No                                                                               | N/A                                                                               |
| kötelező aláírású tokenek           | Logikai. Megadja, hogy szükséges-e a jogkivonat aláírása.                                                                                                                                                                                                                                                                                                                                                                                           | No                                                                               | true                                                                              |
| elválasztó                       | Sztring. Meghatározza a többértékű jogcímből származó értékek kinyeréséhez használandó elválasztó karaktert (például ",").                                                                                                                                                                                                                                                                                                                                          | No                                                                               | N/A                                                                               |
| url                             | Nyissa meg az azonosító konfigurációs végpontjának URL-címét, amelyből megkérheti a nyitott azonosító konfigurációs metaadatait. A válasznak a következő URL-címen definiált specifikációknak kell megjelennie: `https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata` . Azure Active Directory használja a következő URL-címet: `https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration` a címtár-bérlő nevének helyettesítése, például `contoso.onmicrosoft.com` . | Yes                                                                              | N/A                                                                               |
| output-token-változó-neve      | Sztring. Annak a környezeti változónak a neve, amely a jogkivonat értékét a [`Jwt`](api-management-policy-expressions.md) sikeres jogkivonat-ellenőrzés után egy típusú objektumként fogja fogadni                                                                                                                                                                                                                                                                                     | No                                                                               | N/A                                                                               |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.

-   **Házirend fejezetei:** bejövő
-   **Házirend-hatókörök:** az összes hatókör

## <a name="next-steps"></a>Következő lépések

További információ a házirendek használatáról:

-   [Szabályzatok API Management](api-management-howto-policies.md)
-   [API-k átalakítása](transform-api.md)
-   Házirend- [hivatkozás](./api-management-policies.md) a szabályzat-utasítások és azok beállításainak teljes listájához
-   [Szabályzatminták](./policy-reference.md)

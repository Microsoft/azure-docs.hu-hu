---
title: Azure API Management gyorsítótárazási szabályzatok | Microsoft Docs
description: Ismerje meg az Azure API Management használható gyorsítótárazási házirendeket. Tekintse át a példákat, és tekintse meg a további elérhető erőforrásokat
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 8147199c-24d8-439f-b2a9-da28a70a890c
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/13/2020
ms.author: apimpm
ms.openlocfilehash: bd3a63db7dd4946a9836b3978992fb544b9ab0ab
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101688042"
---
# <a name="api-management-caching-policies"></a>API Management – Gyorsítótárazási szabályzatok
Ez a témakör az alábbi API Management szabályzatokra mutató hivatkozást tartalmaz. A házirendek hozzáadásával és konfigurálásával kapcsolatos információkért lásd: [szabályzatok API Management](./api-management-policies.md).

> [!IMPORTANT]
> A beépített gyorsítótár változékony, és az azonos régióban található összes egység ugyanazon a API Management szolgáltatásban van megosztva.

## <a name="caching-policies"></a><a name="CachingPolicies"></a> Gyorsítótárazási házirendek

- Válasz gyorsítótárazási házirendjei
    - [Beolvasás gyorsítótárból](api-management-caching-policies.md#GetFromCache) – a gyorsítótár végrehajtása megkeresi és érvényes gyorsítótárazott válaszokat ad vissza, ha elérhető.
    - [Tárolás a gyorsítótárba](api-management-caching-policies.md#StoreToCache) – a gyorsítótárak a megadott gyorsítótár-vezérlési konfigurációnak megfelelően gyorsítótárazzák a válaszokat.
- Érték gyorsítótárazási házirendjei
    - [Érték lekérése a gyorsítótárból](#GetFromCacheByKey) – a gyorsítótárazott elemek kulcs szerinti beolvasása.
    - [Tárolási érték](#StoreToCacheByKey) a gyorsítótárban – a gyorsítótárban lévő elemek tárolása kulcs alapján.
    - [Érték eltávolítása a gyorsítótárból](#RemoveCacheByKey) – a kulcsban lévő elem eltávolítása a gyorsítótárból.

## <a name="get-from-cache"></a><a name="GetFromCache"></a> Beolvasás a gyorsítótárból
A `cache-lookup` házirend használatával hajtsa végre a gyorsítótár megkeresését, és ha elérhető, érvényes gyorsítótárazott választ ad vissza. Ezt a szabályzatot olyan esetekben lehet alkalmazni, amikor a válasz tartalma egy adott időszakban statikus marad. A válasz gyorsítótárazása csökkenti a háttér-webkiszolgálón kiszabott sávszélesség-és feldolgozási követelményeket, és csökkenti az API-felhasználók által észlelt késéseket.

> [!NOTE]
> Ennek a szabályzatnak megfelelő tárolóval kell rendelkeznie a [gyorsítótárazási](api-management-caching-policies.md#StoreToCache) házirendhez.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" caching-type="prefer-external | external | internal" downstream-caching-type="none | private | public" must-revalidate="true | false" allow-private-response-caching="@(expression to evaluate)">
  <vary-by-header>Accept</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Accept-Charset</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Authorization</vary-by-header>
  <!-- should be present when allow-private-response-caching is "true"-->
  <vary-by-header>header name</vary-by-header>
  <!-- optional, can repeated several times -->
  <vary-by-query-parameter>parameter name</vary-by-query-parameter>
  <!-- optional, can repeated several times -->
</cache-lookup>
```

### <a name="examples"></a>Példák

#### <a name="example"></a>Példa

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none" must-revalidate="true" caching-type="internal" >
            <vary-by-query-parameter>version</vary-by-query-parameter>
        </cache-lookup>
    </inbound>
    <outbound>
        <cache-store duration="seconds" />
        <base />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>Példa házirend-kifejezések használatával
Ez a példa bemutatja, hogyan konfigurálhatja API Management válasz gyorsítótárazási időtartamát, amely megfelel a háttérrendszer által a háttérrendszer által megadott irányelv által meghatározott válasz-gyorsítótárazásnak `Cache-Control` . A szabályzat konfigurálásának és használatának bemutatását lásd: a [Cloud Cover 177-es epizódja: további API Management szolgáltatások a Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) és a 25:25-es gyors előretekeréssel.

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

További információ: [Policy Expressions](api-management-policy-expressions.md) and [Context változó](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elemek

|Név|Leírás|Kötelező|
|----------|-----------------|--------------|
|gyorsítótár – keresés|Gyökérelem.|Igen|
|változó – fejléc|A gyorsítótárazási válaszok megkezdése megadott fejléc alapján, például elfogadás, elfogadás – karakterkészlet, elfogadás – kódolás, elfogadás – nyelv, engedélyezés, elvárt, feladó, gazdagép, if-Match.|Nem|
|változó-by-Query-paraméter|A gyorsítótárazási válaszok indítása a megadott lekérdezési paraméterek értékével. Adjon meg egy vagy több paramétert. Pontosvesszőt használjon elválasztóként. Ha nincs megadva, a rendszer az összes lekérdezési paramétert használja.|Nem|

### <a name="attributes"></a>Attribútumok

| Név                           | Leírás                                                                                                                                                                                                                                                                                                                                                 | Kötelező | Alapértelmezett           |
|--------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| a-Private-Response-gyorsítótárazás engedélyezése | Ha a értékre `true` van állítva, lehetővé teszi az engedélyezési fejlécet tartalmazó kérelmek gyorsítótárazását.                                                                                                                                                                                                                                                                        | Nem       | hamis             |
| gyorsítótárazás – típus               | Válasszon az attribútum következő értékei közül:<br />- `internal` a beépített API Management cache használatához<br />- `external` a külső gyorsítótár használata a [külső Azure cache használata az Azure-beli Redis-ben](api-management-howto-cache-external.md)című témakörben leírtak szerint, API Management<br />- `prefer-external` Ha a külső gyorsítótárat más módon konfigurált vagy belső gyorsítótárral szeretné használni. | Nem       | `prefer-external` |
| alsóbb réteg – gyorsítótárazási típus        | Ezt az attribútumot az alábbi értékek egyikére kell beállítani.<br /><br /> -nincs – az alsóbb rétegbeli gyorsítótárazás nem engedélyezett.<br />– a privát alsóbb rétegbeli magánhálózati gyorsítótárazás engedélyezett.<br />– a nyilvános és a megosztott alsóbb rétegbeli gyorsítótárazás engedélyezett.                                                                                                          | Nem       | Nincs              |
| újra kell érvényesíteni                | Ha az alsóbb rétegbeli gyorsítótárazás engedélyezve van, ez az attribútum be-vagy kikapcsolja az `must-revalidate` átjáróra adott válaszokban lévő Cache Control direktívát.                                                                                                                                                                                                                      | Nem       | true              |
| változó – fejlesztő              | Állítsa be a () értékre `true` úgy, hogy a kérésben szereplő [előfizetési kulcsot](./api-management-subscriptions.md) birtokló fejlesztői fiókon keresztül gyorsítótárazza a válaszokat.                                                                                                                                                                                                                                                                                                  | Igen      |         Hamis          |
| változó – fejlesztői csoportok       | A beállítás értékeként `true` a rendszer [felhasználói csoportonként](./api-management-howto-create-groups.md)gyorsítótárazza a válaszokat.                                                                                                                                                                                                                                                                                                             | Igen      |       Hamis            |

### <a name="usage"></a>Használat
Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.

- **Házirend fejezetei:** bejövő
- **Házirend-hatókörök:** az összes hatókör

## <a name="store-to-cache"></a><a name="StoreToCache"></a> Tárolás a gyorsítótárba
A `cache-store` házirend a megadott gyorsítótár-beállításoknak megfelelően gyorsítótárazza a válaszokat. Ezt a szabályzatot olyan esetekben lehet alkalmazni, amikor a válasz tartalma egy adott időszakban statikus marad. A válasz gyorsítótárazása csökkenti a háttér-webkiszolgálón kiszabott sávszélesség-és feldolgozási követelményeket, és csökkenti az API-felhasználók által észlelt késéseket.

> [!NOTE]
> Ennek a szabályzatnak szerepelnie kell egy megfelelő [beolvasási gyorsítótár-](api-management-caching-policies.md#GetFromCache) házirenddel.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<cache-store duration="seconds" />
```

### <a name="examples"></a>Példák

#### <a name="example"></a>Példa

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" downstream-caching-type="none | private | public" must-revalidate="true | false">
            <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->
        </cache-lookup>
    </inbound>
    <outbound>
        <base />
        <cache-store duration="3600" />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>Példa házirend-kifejezések használatával
Ez a példa bemutatja, hogyan konfigurálhatja API Management válasz gyorsítótárazási időtartamát, amely megfelel a háttérrendszer által a háttérrendszer által megadott irányelv által meghatározott válasz-gyorsítótárazásnak `Cache-Control` . A szabályzat konfigurálásának és használatának bemutatását lásd: a [Cloud Cover 177-es epizódja: további API Management szolgáltatások a Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) és a 25:25-es gyors előretekeréssel.

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

További információ: [Policy Expressions](api-management-policy-expressions.md) and [Context változó](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elemek

|Név|Leírás|Kötelező|
|----------|-----------------|--------------|
|gyorsítótár-tároló|Gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

| Név             | Leírás                                                                                                                                                                                                                                                                                                                                                 | Kötelező | Alapértelmezett           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| duration         | A gyorsítótárazott bejegyzések a másodpercben megadott élettartama.                                                                                                                                                                                                                                                                                                   | Igen      | N/A               |

### <a name="usage"></a>Használat
Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.

- **Házirend fejezetei:** kimenő
- **Házirend-hatókörök:** az összes hatókör

## <a name="get-value-from-cache"></a><a name="GetFromCacheByKey"></a> Érték lekérése a gyorsítótárból
A `cache-lookup-value` házirend használatával hajtsa végre a kulcsok gyorsítótárbeli keresését, és egy gyorsítótárazott értéket ad vissza. A kulcs tetszőleges karakterlánc-értékkel rendelkezhet, és általában egy házirend-kifejezés használatával adható meg.

> [!NOTE]
> Ennek a szabályzatnak megfelelő [tárolási értékkel kell rendelkeznie a gyorsítótár-](#StoreToCacheByKey) házirendben.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<cache-lookup-value key="cache key value"
    default-value="value to use if cache lookup resulted in a miss"
    variable-name="name of a variable looked up value is assigned to"
    caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Példa
A szabályzattal kapcsolatos további információkért és példákért tekintse [meg az egyéni gyorsítótárazás az Azure API Management-ban](./api-management-sample-cache-by-key.md)című témakört.

```xml
<cache-lookup-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    variable-name="userprofile" />

```

### <a name="elements"></a>Elemek

|Név|Leírás|Kötelező|
|----------|-----------------|--------------|
|gyorsítótár-keresési érték|Gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

| Név             | Leírás                                                                                                                                                                                                                                                                                                                                                 | Kötelező | Alapértelmezett           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| gyorsítótárazás – típus | Válasszon az attribútum következő értékei közül:<br />- `internal` a beépített API Management cache használatához<br />- `external` a külső gyorsítótár használata a [külső Azure cache használata az Azure-beli Redis-ben](api-management-howto-cache-external.md)című témakörben leírtak szerint, API Management<br />- `prefer-external` Ha a külső gyorsítótárat más módon konfigurált vagy belső gyorsítótárral szeretné használni. | Nem       | `prefer-external` |
| alapértelmezett érték    | Egy érték, amely akkor lesz hozzárendelve a változóhoz, ha a gyorsítótár kulcsának keresése kihagyott eredményt eredményezett. Ha ez az attribútum nincs megadva, `null` a hozzá van rendelve.                                                                                                                                                                                                           | Nem       | `null`            |
| kulcs              | A kereséshez használni kívánt gyorsítótár-kulcs értéke.                                                                                                                                                                                                                                                                                                                       | Igen      | N/A               |
| változó – név    | Annak a [környezeti változónak](api-management-policy-expressions.md#ContextVariables) a neve, amelyhez a keresett érték hozzá lesz rendelve, ha a keresés sikeres. Ha a keresés kimarad, a változó az attribútum értékét fogja hozzárendelni, `default-value` vagy `null` Ha az `default-value` attribútum nincs megadva.                                       | Igen      | N/A               |

### <a name="usage"></a>Használat
Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.

- **Házirend fejezetei:** bejövő, kimenő, háttérbeli, hiba esetén
- **Házirend-hatókörök:** az összes hatókör

## <a name="store-value-in-cache"></a><a name="StoreToCacheByKey"></a> Tárolási érték a gyorsítótárban
A `cache-store-value` gyorsítótár-tárolást végez a kulcs alapján. A kulcs tetszőleges karakterlánc-értékkel rendelkezhet, és általában egy házirend-kifejezés használatával adható meg.

> [!NOTE]
> Az értéknek a házirend által végrehajtott gyorsítótárban való tárolása aszinkron módon történik. A tárolt érték lekérése a [lekérési érték használatával a gyorsítótár-](#GetFromCacheByKey) házirendből. Előfordulhat azonban, hogy a tárolt érték nem érhető el azonnal a lekéréshez, mert a gyorsítótárban lévő értéket tároló aszinkron művelet továbbra is folyamatban van. 

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<cache-store-value key="cache key value" value="value to cache" duration="seconds" caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Példa
A szabályzattal kapcsolatos további információkért és példákért tekintse [meg az egyéni gyorsítótárazás az Azure API Management-ban](./api-management-sample-cache-by-key.md)című témakört.

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />

```

### <a name="elements"></a>Elemek

|Név|Leírás|Kötelező|
|----------|-----------------|--------------|
|cache-Store-Value|Gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

| Név             | Leírás                                                                                                                                                                                                                                                                                                                                                 | Kötelező | Alapértelmezett           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| gyorsítótárazás – típus | Válasszon az attribútum következő értékei közül:<br />- `internal` a beépített API Management cache használatához<br />- `external` a külső gyorsítótár használata a [külső Azure cache használata az Azure-beli Redis-ben](api-management-howto-cache-external.md)című témakörben leírtak szerint, API Management<br />- `prefer-external` Ha a külső gyorsítótárat más módon konfigurált vagy belső gyorsítótárral szeretné használni. | Nem       | `prefer-external` |
| duration         | Az érték a megadott időtartamnál (másodpercben) lesz gyorsítótárazva.                                                                                                                                                                                                                                                                                 | Igen      | N/A               |
| kulcs              | Gyorsítótár-kulcs az érték a alatt lesz tárolva.                                                                                                                                                                                                                                                                                                                   | Igen      | N/A               |
| érték            | A gyorsítótárazni kívánt érték.                                                                                                                                                                                                                                                                                                                                     | Igen      | N/A               |
### <a name="usage"></a>Használat
Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.

- **Házirend fejezetei:** bejövő, kimenő, háttérbeli, hiba esetén
- **Házirend-hatókörök:** az összes hatókör

## <a name="remove-value-from-cache"></a><a name="RemoveCacheByKey"></a> Érték eltávolítása a gyorsítótárból
A `cache-remove-value` törli a kulcsával azonosított gyorsítótárazott elemeket. A kulcs tetszőleges karakterlánc-értékkel rendelkezhet, és általában egy házirend-kifejezés használatával adható meg.

#### <a name="policy-statement"></a>Szabályzati utasítás

```xml

<cache-remove-value key="cache key value" caching-type="prefer-external | external | internal"  />

```

#### <a name="example"></a>Példa

```xml

<cache-remove-value key="@("userprofile-" + context.Variables["enduserid"])"/>

```

#### <a name="elements"></a>Elemek

|Név|Leírás|Kötelező|
|----------|-----------------|--------------|
|cache-Remove-Value|Gyökérelem.|Igen|

#### <a name="attributes"></a>Attribútumok

| Név             | Leírás                                                                                                                                                                                                                                                                                                                                                 | Kötelező | Alapértelmezett           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| gyorsítótárazás – típus | Válasszon az attribútum következő értékei közül:<br />- `internal` a beépített API Management cache használatához<br />- `external` a külső gyorsítótár használata a [külső Azure cache használata az Azure-beli Redis-ben](api-management-howto-cache-external.md)című témakörben leírtak szerint, API Management<br />- `prefer-external` Ha a külső gyorsítótárat más módon konfigurált vagy belső gyorsítótárral szeretné használni. | Nem       | `prefer-external` |
| kulcs              | A korábban gyorsítótárazott, a gyorsítótárból eltávolítandó érték kulcsa.                                                                                                                                                                                                                                                                                        | Igen      | N/A               |

#### <a name="usage"></a>Használat
Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes) használható.

- **Házirend fejezetei:** bejövő, kimenő, háttérbeli, hiba esetén
- **Házirend-hatókörök:** az összes hatókör

## <a name="next-steps"></a>Következő lépések

További információ a házirendek használatáról:

+ [Szabályzatok API Management](api-management-howto-policies.md)
+ [API-k átalakítása](transform-api.md)
+ Házirend- [hivatkozás](./api-management-policies.md) a szabályzat-utasítások és azok beállításainak teljes listájához
+ [Szabályzatminták](./policy-reference.md)
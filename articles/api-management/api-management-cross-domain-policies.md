---
title: Az Azure API Management szabályzatok tartományközi | Microsoft Docs
description: Ismerje meg az Azure-ban használható tartományközi szabályzatokat API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 7689d277-8abe-472a-a78c-e6d4bd43455d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/01/2021
ms.author: apimpm
ms.openlocfilehash: 6f074ff389971fa56da7838a9a46ec5c4d42dc5a
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739097"
---
# <a name="api-management-cross-domain-policies"></a>Az API Management tartományközi házirendjei
Ez a témakör a következő szabályzatok referenciáját API Management tartalmazza. További információ a szabályzatok hozzáadásáról és konfigurálásról: [Házirendek a API Management.](./api-management-policies.md)

## <a name="cross-domain-policies"></a><a name="CrossDomainPolicies"></a> Tartományközi szabályzatok

- [Tartományok közötti hívások engedélyezése](api-management-cross-domain-policies.md#AllowCrossDomainCalls) – Elérhetővé teszi az API-t az Adobe Flash és a Microsoft Silverlight böngészőalapú ügyfelei számára.
- [CORS](api-management-cross-domain-policies.md#CORS) – Az eredetközi erőforrás-megosztás (CORS) támogatását hozzáadja egy művelethez vagy egy API-hoz, amely lehetővé teszi a böngészőalapú ügyfelektől származó tartományok közötti hívásokat.
- [JSONP](api-management-cross-domain-policies.md#JSONP) – JSON-t ad hozzá kitöltési (JSONP) támogatással egy művelethez vagy egy API-hoz a JavaScript böngészőalapú ügyfelekről származó tartományok közötti hívások engedélyezése érdekében.

## <a name="allow-cross-domain-calls"></a><a name="AllowCrossDomainCalls"></a> Tartományok közötti hívások engedélyezése
A `cross-domain` szabályzattal elérhetővé teszi az API-t az Adobe Flash és a Microsoft Silverlight böngészőalapú ügyfelek számára.

### <a name="policy-statement"></a>Szabályzat utasítása

```xml
<cross-domain>
    <!-Policy configuration is in the Adobe cross-domain policy file format,
        see https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html-->
</cross-domain>
```

### <a name="example"></a>Példa

```xml
<cross-domain>
        <allow-http-request-headers-from domain='*' headers='*' />
</cross-domain>
```

### <a name="elements"></a>Elemek

|Név|Leírás|Kötelező|
|----------|-----------------|--------------|
|tartományok közötti|Gyökérelem. A gyermekelemeknek meg kell felelniük az [Adobe tartományközi szabályzatfájl-specifikációnak.](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html)|Yes|

### <a name="usage"></a>Használat
Ez a szabályzat a következő [](./api-management-howto-policies.md#sections) szabályzatszakaszban és [hatókörökben használható.](./api-management-howto-policies.md#scopes)

- **Szabályzatszakaszok:** bejövő
- **Szabályzathatókörök:** minden hatókör

## <a name="cors"></a><a name="CORS"></a> CORS
A `cors` szabályzat az eredetközi erőforrás-megosztás (CORS) támogatását is hozzáadja egy művelethez vagy egy API-hoz, hogy lehetővé tegye a böngészőalapú ügyfelektől származó tartományok közötti hívásokat. 

> [!NOTE]
> Ha a kérelem megfelel egy műveletnek az API-ban meghatározott OPTIONS metódussal, a CORS-szabályzatokkal társított, előzetes kérésfeldolgozási logika nem lesz végrehajtva. Ezért az ilyen műveletek felhasználhatók az egyéni, előre repülőjárat-feldolgozási logika implementálja.

A CORS lehetővé teszi, hogy a böngésző és a kiszolgáló interakcióba lépjen, és meghatározza, hogy engedélyezi-e az adott eredetközi kéréseket (például a JavaScriptből a weblapon más tartományokba beérkező XMLHttpRequests-hívásokat). Ez nagyobb rugalmasságot tesz lehetővé, mint az azonos eredetű kérések, de biztonságosabb, mint az összes különböző eredetű kérés.

A CORS-szabályzatot alkalmaznia kell az interaktív konzol fejlesztői portálon való engedélyezéséhez. A részletekért tekintse meg [a fejlesztői portál](./developer-portal-faq.md#cors) dokumentációját.

### <a name="policy-statement"></a>Policy utasítás

```xml
<cors allow-credentials="false|true" terminate-unmatched-request="true|false">
    <allowed-origins>
        <origin>origin uri</origin>
    </allowed-origins>
    <allowed-methods preflight-result-max-age="number of seconds">
        <method>http verb</method>
    </allowed-methods>
    <allowed-headers>
        <header>header name</header>
    </allowed-headers>
    <expose-headers>
        <header>header name</header>
    </expose-headers>
</cors>
```

### <a name="example"></a>Példa
Ez a példa bemutatja, hogyan támogathatja az előzetes kéréseket, például a GET és a POST fejléctől vagy metódustól különböző egyéni fejlécekkel vagy metódusokkal. Az egyéni fejlécek és további HTTP-műveletek támogatásához használja a és a szakaszt az alábbi `allowed-methods` `allowed-headers` példában látható módon.

```xml
<cors allow-credentials="true">
    <allowed-origins>
        <!-- Localhost useful for development -->
        <origin>http://localhost:8080/</origin>
        <origin>http://example.com/</origin>
    </allowed-origins>
    <allowed-methods preflight-result-max-age="300">
        <method>GET</method>
        <method>POST</method>
        <method>PATCH</method>
        <method>DELETE</method>
    </allowed-methods>
    <allowed-headers>
        <!-- Examples below show Azure Mobile Services headers -->
        <header>x-zumo-installation-id</header>
        <header>x-zumo-application</header>
        <header>x-zumo-version</header>
        <header>x-zumo-auth</header>
        <header>content-type</header>
        <header>accept</header>
    </allowed-headers>
    <expose-headers>
        <!-- Examples below show Azure Mobile Services headers -->
        <header>x-zumo-installation-id</header>
        <header>x-zumo-application</header>
    </expose-headers>
</cors>
```

### <a name="elements"></a>Elemek

|Név|Leírás|Kötelező|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|cors|Gyökérelem.|Yes|N/A|
|engedélyezett eredetek|A tartományok közötti kérések engedélyezett eredetét `origin` leíró elemeket tartalmaz. `allowed-origins` A tartalmazhat egyetlen elemet, amely meghatározza, hogy engedélyezi-e a forrást, vagy egy vagy több `origin` `*` `origin` URI-t tartalmazó elemet.|Yes|N/A|
|forrás (origin)|Az érték lehet az összes forrás engedélyezése, vagy egy `*` URI, amely egyetlen forrást ad meg. Az URI-nak tartalmaznia kell egy sémát, egy gazdagépet és egy portot.|Yes|Ha a port nincs megadva az URI-ban, a 80-as portot használja a HTTP, a 443-as portot pedig a HTTPS-hez.|
|engedélyezett metódusok|Ez az elem akkor szükséges, ha a GET és a POST helyett más metódusok is engedélyezettek. A `method` támogatott HTTP-műveleteket megszabadó elemeket tartalmazza. Az érték `*` az összes metódust jelzi.|No|Ha ez a szakasz nincs jelen, a GET és a POST támogatott.|
|method|Http-műveleteket ad meg.|Legalább egy `method` elem szükséges, ha a `allowed-methods` szakasz jelen van.|N/A|
|engedélyezett fejlécek|Ez az elem olyan elemeket tartalmaz, amelyek a kérésben szereplő fejlécek nevét `header` határozzák meg.|No|N/A|
|expose-headers (fejlécek elérhetővé tevése)|Ez az elem olyan elemeket tartalmaz, amelyek az ügyfél számára elérhető `header` fejlécek nevét határozzák meg.|No|N.A.|
|fejléc|Megad egy fejlécnevet.|A vagy a legalább egy elemét meg `header` kell `allowed-headers` `expose-headers` adni, ha a szakasz jelen van.|N/A|

### <a name="attributes"></a>Attribútumok

|Név|Leírás|Kötelező|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|hitelesítő adatok engedélyezése|Az előzetes válasz fejléce ennek az attribútumnak az értékére lesz beállítva, és hatással van az ügyfélnek a hitelesítő adatok tartományközi kérésekbe való `Access-Control-Allow-Credentials` elküldére.|No|hamis|
|terminate-unmatched-request|Ez az attribútum a CORS-szabályzat beállításainak nem megfelelő, különböző eredetű kérések feldolgozását szabályozza. Ha az OPTIONS kérést előzetes kérésként feldolgozták, és nem felel meg a CORS-szabályzat beállításainak: Ha az attribútum értéke , azonnal állítsa le a kérést egy üres `true` 200 OK válaszsal; Ha az attribútum értéke , akkor ellenőrizze, hogy vannak-e bejövő forgalomban olyan, hatókörbe tartozó CORS-szabályzatok, amelyek a bejövő elem közvetlen gyermekei, és `false` alkalmazza őket.  Ha nem található CORS-szabályzat, a 200 OK üres válaszával szakossa meg a kérést. Ha a GET vagy HEAD kérelem tartalmazza az Origin fejlécet (ezért a rendszer egy eredetközi kérésként lesz feldolgozva), és nem felel meg a CORS-szabályzat beállításainak: Ha az attribútum értéke , azonnal állítsa le a kérést egy üres `true` 200 OK válaszsal; Ha az attribútum értéke , engedélyezze a kérés normál továbblépését, és ne adjon CORS-fejléceket a `false` válaszhoz.|No|true|
|preflight-result-max-age|Az előzetes válasz fejléce ennek az attribútumnak az értékére lesz beállítva, és befolyásolja, hogy a felhasználói ügynök képes `Access-Control-Max-Age` gyorsítótárazza-e az előzetes választ.|No|0|

### <a name="usage"></a>Használat
Ez a szabályzat a következő [](./api-management-howto-policies.md#sections) szabályzatszakaszban és [hatókörökben használható.](./api-management-howto-policies.md#scopes)

- **Szabályzatszakaszok:** bejövő
- **Szabályzathatókörök:** az összes hatókör

## <a name="jsonp"></a><a name="JSONP"></a> Jsonp
A szabályzat JSON-t ad hozzá kitöltési (JSONP) támogatással egy művelethez vagy egy API-hoz a JavaScript böngészőalapú ügyfelek tartományközi hívásának `jsonp` engedélyezése érdekében. A JSONP egy olyan metódus, amely a JavaScript-programokban egy másik tartományban található kiszolgálóról kér adatokat. A JSONP megkerüli a legtöbb webböngésző által érvényesített korlátozást, ahol a weboldalakhoz való hozzáférésnek ugyanabban a tartományban kell lennie.

### <a name="policy-statement"></a>Policy utasítás

```xml
<jsonp callback-parameter-name="callback function name" />
```

### <a name="example"></a>Példa

```xml
<jsonp callback-parameter-name="cb" />
```

Ha a metódust a ?cb=XXX visszahívási paraméter nélkül hívja meg, az egyszerű JSON-t ad vissza (függvényhívás-burkoló nélkül).

Ha hozzáadja a visszahívási paramétert, az JSONP-eredményt ad vissza, és az eredeti JSON-eredményeket a visszahívási függvény köré `?cb=XXX` csomagolja, például: `XYZ('<json result goes here>');`

### <a name="elements"></a>Elemek

|Név|Leírás|Kötelező|
|----------|-----------------|--------------|
|Jsonp|Gyökérelem.|Yes|

### <a name="attributes"></a>Attribútumok

|Név|Leírás|Kötelező|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|callback-parameter-name|A tartományközi JavaScript-függvényhívás előtagja a függvényt tartományvezérlő teljes tartománynév.|Yes|N/A|

### <a name="usage"></a>Használat
Ez a szabályzat a következő [](./api-management-howto-policies.md#sections) szabályzatszakaszban és [hatókörökben használható.](./api-management-howto-policies.md#scopes)

- **Szabályzatszakaszok:** kimenő
- **Szabályzathatókörök:** az összes hatókör

## <a name="next-steps"></a>Következő lépések

A szabályzatokkal kapcsolatos további információkért lásd:

+ [Szabályzatok a API Management](api-management-howto-policies.md)
+ [API-k átalakítása](transform-api.md)
+ [Szabályzati referencia](./api-management-policies.md) a szabályzati utasítások és azok beállításainak teljes listájához
+ [Szabályzatminták](./policy-reference.md)

---
title: Azure API Management tartományok közötti házirendek | Microsoft Docs
description: Ismerkedjen meg az Azure API Managementban használható tartományok házirendjeivel.
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
ms.openlocfilehash: 85abf30d792b24b92685e191f5b460a42dc29142
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101688416"
---
# <a name="api-management-cross-domain-policies"></a>Az API Management tartományközi házirendjei
Ez a témakör az alábbi API Management szabályzatokra mutató hivatkozást tartalmaz. A házirendek hozzáadásával és konfigurálásával kapcsolatos információkért lásd: [szabályzatok API Management](./api-management-policies.md).

## <a name="cross-domain-policies"></a><a name="CrossDomainPolicies"></a> Tartományok közötti házirendek

- Tartományok [közötti hívások engedélyezése](api-management-cross-domain-policies.md#AllowCrossDomainCalls) – elérhetővé teszi az API-t az Adobe Flash és a Microsoft Silverlight böngésző alapú ügyfeleitől.
- [CORS](api-management-cross-domain-policies.md#CORS) – a több eredetű erőforrás-megosztási (CORS) támogatás egy művelethez vagy API-hoz, amely lehetővé teszi a tartományok közötti hívásokat a böngészőalapú ügyfelektől.
- [Jsnop támogatással](api-management-cross-domain-policies.md#JSONP) – egy művelet vagy API számára lehetővé teszi a JSON-t a kitöltési (jsnop támogatással) támogatással, hogy engedélyezze a tartományok közötti hívásokat a JavaScript böngésző alapú ügyfelektől.

## <a name="allow-cross-domain-calls"></a><a name="AllowCrossDomainCalls"></a> Tartományok közötti hívások engedélyezése
A `cross-domain` szabályzat segítségével elérhetővé teheti az API-t az Adobe Flash és a Microsoft Silverlight böngésző alapú ügyfeleitől.

### <a name="policy-statement"></a>Szabályzati utasítás

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
|tartományok közötti|Gyökérelem. A gyermek elemeknek meg kell felelniük az [Adobe tartományok közötti házirend fájljának specifikációjának](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html).|Yes|

### <a name="usage"></a>Használat
Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.

- **Házirend fejezetei:** bejövő
- **Házirend-hatókörök:** az összes hatókör

## <a name="cors"></a><a name="CORS"></a> CORS
A `cors` házirend egy művelethez vagy API-hoz ad hozzá több eredetű erőforrás-megosztási (CORS) támogatást, amely lehetővé teszi a tartományok közötti hívásokat a böngészőalapú ügyfelektől. 

> [!NOTE]
> Ha a kérelem megegyezik egy olyan művelettel, amely az API-ban definiált OPTIONs metódussal rendelkezik, a rendszer nem hajtja végre a CORS-házirendekhez rendelt előrepülési kérelem feldolgozási logikáját. Ezért az ilyen műveletek használhatók az egyéni repülés előtti feldolgozási logika megvalósításához.

A CORS lehetővé teszi, hogy a böngésző és a kiszolgáló interakcióba lépjen, és meghatározza, hogy engedélyezi-e a különböző eltérő eredetű kérelmeket (például az XMLHttpRequest-hívásokat weblapokon a JavaScriptből más tartományokra). Ez nagyobb rugalmasságot tesz lehetővé, mint az azonos eredetű kérések engedélyezése, de biztonságosabb, mint az összes eltérő eredetű kérelem.

Az interaktív konzolnak a fejlesztői portálon való engedélyezéséhez a CORS szabályzatot kell alkalmaznia. A részletekért tekintse meg a [fejlesztői portál dokumentációját](./api-management-howto-developer-portal.md#cors) .

### <a name="policy-statement"></a>Szabályzati utasítás

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
Ez a példa azt mutatja be, hogyan lehet támogatni a repülés előtti kérelmeket, például az egyéni fejlécekkel vagy a GET és a POST metódustól eltérő módszerekkel. Az egyéni fejlécek és a további HTTP-műveletek támogatásához használja a `allowed-methods` és a `allowed-headers` szakaszt az alábbi példában látható módon.

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
|CORS|Gyökérelem.|Yes|N/A|
|engedélyezett – eredetek|Olyan `origin` elemeket tartalmaz, amelyek leírják a tartományok közötti kérelmek engedélyezett eredetét. `allowed-origins` tartalmazhat egy olyan `origin` elemet `*` , amely engedélyezi bármely forrás használatát, vagy egy vagy több URI-t `origin` tartalmazó elemet.|Yes|N/A|
|forrás (origin)|Az érték lehet az `*` összes eredet engedélyezése, vagy egy URI, amely egyetlen forrást határoz meg. Az URI-nak tartalmaznia kell egy sémát, egy gazdagépet és egy portot.|Yes|Ha a portot kihagyja egy URI-ban, a 80-es portot használja a rendszer a HTTP protokollhoz, és a 443-es portot használja a HTTPS protokollhoz.|
|engedélyezett – metódusok|Ez az elem akkor szükséges, ha a GET vagy a POST metódustól eltérő módszerek engedélyezettek. `method`A támogatott http-műveleteket megadó elemeket tartalmazza. Az érték az `*` összes metódust jelzi.|No|Ha ez a szakasz nem létezik, a GET és a POST is támogatott.|
|method|HTTP-műveletet ad meg.|`method`Ha a szakasz jelen van, legalább egy elem megadása kötelező `allowed-methods` .|N/A|
|engedélyezett – fejlécek|Ez az elem olyan elemeket tartalmaz, `header` amelyek megadják a kérésben szerepeltethető fejlécek nevét.|No|N/A|
|fejlécek közzététele|Ez az elem olyan elemeket tartalmaz, `header` amelyek az ügyfél által elérhető fejlécek nevét határozzák meg.|No|N.A.|
|fejléc|Megadja a fejléc nevét.|Legalább egy `header` elemet meg kell adni, `allowed-headers` vagy `expose-headers` Ha a szakasz jelen van.|N/A|

### <a name="attributes"></a>Attribútumok

|Név|Leírás|Kötelező|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|hitelesítő adatok engedélyezése|Az `Access-Control-Allow-Credentials` elővizsgálati válasz fejléce az attribútum értékére lesz állítva, és hatással van arra, hogy az ügyfél képes legyen hitelesítő adatokat küldeni a tartományok közötti kérelmekben.|No|hamis|
|megszakítás – nem egyező – kérelem|Ez az attribútum a CORS házirend-beállításoknak nem megfelelő eltérő eredetű kérelmek feldolgozását vezérli. Ha a OPTIONs kérést repülés előtti kérelemként dolgozza fel, és nem felel meg a CORS házirend-beállításoknak: Ha az attribútum értéke, a rendszer `true` azonnal leállítja a kérést üres 200 OK-válaszsal. Ha az attribútum értéke, akkor `false` ellenőrizze a bejövő elemek közvetlen gyermekeit, és alkalmazza azokat a beérkező más hatókörű CORS-házirendekhez.  Ha nem található CORS-házirend, a kérést üres 200 OK-választal szakítja meg. Ha a GET vagy HEAD kérelem magában foglalja a forrás fejlécet (és ezért a rendszer az eltérő eredetű kérelemként dolgozza fel), és nem felel meg a CORS házirend-beállításoknak: Ha az attribútum értéke, a rendszer `true` azonnal leállítja a kérést üres 200 OK-válaszsal. Ha az attribútum értéke `false` , engedélyezze a kérést a szokásos módon, és ne adjon hozzá CORS-fejléceket a válaszhoz.|No|true|
|Elővizsgálat – eredmény-Max-Age|Az `Access-Control-Max-Age` elővizsgálati válasz fejléce az attribútum értékére lesz állítva, és hatással van a felhasználói ügynöknek a repülés előtti válasz gyorsítótárazására.|No|0|

### <a name="usage"></a>Használat
Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.

- **Házirend fejezetei:** bejövő
- **Házirend-hatókörök:** az összes hatókör

## <a name="jsonp"></a><a name="JSONP"></a> JSNOP támogatással
A `jsonp` szabályzat egy művelet vagy egy API számára lehetővé teszi a JSON-t a kitöltő (jsnop támogatással) támogatással, hogy engedélyezze a tartományok közötti hívásokat a JavaScript böngésző alapú ügyfelektől. A JSNOP támogatással egy JavaScript-programokban használt metódus, amely egy másik tartományban lévő kiszolgálótól kér le adatait. A JSNOP támogatással megkerüli a legtöbb webböngésző által kényszerített korlátozásokat, ahol a weblapokhoz való hozzáférésnek ugyanabban a tartományban kell lennie.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<jsonp callback-parameter-name="callback function name" />
```

### <a name="example"></a>Példa

```xml
<jsonp callback-parameter-name="cb" />
```

Ha a metódust a visszahívási paraméter nélkül hívja meg? CB = XXX – egyszerű JSON-t ad vissza (függvényhívás burkolója nélkül).

Ha hozzáadja a visszahívási paramétert `?cb=XXX` , a visszaadja a jsnop támogatással eredményét, és becsomagolja az eredeti JSON-eredményeket a visszahívási függvény köré, például: `XYZ('<json result goes here>');`

### <a name="elements"></a>Elemek

|Név|Leírás|Kötelező|
|----------|-----------------|--------------|
|jsnop támogatással|Gyökérelem.|Yes|

### <a name="attributes"></a>Attribútumok

|Név|Leírás|Kötelező|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|visszahívás – paraméter – név|A tartományok közötti JavaScript-függvény hívása a teljes tartománynévvel, ahol a függvény található.|Yes|N/A|

### <a name="usage"></a>Használat
Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.

- **Házirend fejezetei:** kimenő
- **Házirend-hatókörök:** az összes hatókör

## <a name="next-steps"></a>Következő lépések

További információ a házirendek használatáról:

+ [Szabályzatok API Management](api-management-howto-policies.md)
+ [API-k átalakítása](transform-api.md)
+ Házirend- [hivatkozás](./api-management-policies.md) a szabályzat-utasítások és azok beállításainak teljes listájához
+ [Szabályzatminták](./policy-reference.md)

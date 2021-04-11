---
title: Azure API Management érvényesítési szabályzatok | Microsoft Docs
description: Ismerje meg az Azure API Managementban használható szabályzatokat a kérések és válaszok ellenőrzéséhez.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 03/12/2021
ms.author: apimpm
ms.openlocfilehash: 1a835d26b4c41c92b9849856a2f31b3550947bd8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801893"
---
# <a name="api-management-policies-to-validate-requests-and-responses"></a>API Management házirendek a kérelmek és válaszok ellenőrzéséhez

Ez a cikk a következő API Management szabályzatokra mutató hivatkozást tartalmaz. A házirendek hozzáadásával és konfigurálásával kapcsolatos információkért lásd: [szabályzatok API Management](./api-management-policies.md).

Az ellenőrzési házirendek segítségével érvényesítheti az API-kérelmeket és a válaszokat egy OpenAPI-sémán, és védelmet biztosíthat a biztonsági rések, például a fejlécek vagy a hasznos adattartalom Nem helyettesíti a webalkalmazási tűzfalat, az érvényesítési házirendek rugalmasságot biztosítanak a további, a statikus, előre meghatározott szabályokra támaszkodó biztonsági termékek által nem érintett veszélyforrások kezelésére.

## <a name="validation-policies"></a>Ellenőrzési házirendek

- [Tartalom érvényesítése](#validate-content) – egy kérelem vagy válasz törzsének méretét vagy JSON-sémáját ellenőrzi az API-sémán. 
- [Paraméterek érvényesítése](#validate-parameters) – a kérelem fejlécének, lekérdezésének vagy elérési útjának paramétereinek ellenőrzése az API-sémán keresztül.
- [Fejlécek ellenőrzése](#validate-headers) – ellenőrzi a válasz fejléceit az API-sémán.
- [Állapotkód ellenőrzése](#validate-status-code) – érvényesíti a http-ÁLLAPOTKÓDOT az API-sémára adott válaszokban.

> [!NOTE]
> Az érvényesítési házirend által használható API-séma maximális mérete 4 MB. Ha a séma túllépi ezt a korlátot, az érvényesítési házirendek hibát jeleznek a futtatókörnyezetben. A növeléshez forduljon az [ügyfélszolgálathoz](https://azure.microsoft.com/support/options/). 

## <a name="actions"></a>Műveletek

Minden egyes érvényesítési házirend tartalmaz egy olyan műveletet, amely egy API-kérelemben vagy az API-sémán keresztüli válaszban szereplő entitás érvényesítése során API Management. Megadható egy művelet az API-sémában megjelenített elemekhez, és a házirendtől függően az API-sémában nem szereplő elemek esetében. Egy házirend alárendelt elemében megadott művelet felülbírálja a szülőhöz megadott műveletet.

Elérhető műveletek:

| Művelet         | Leírás          |                                                                                                                         
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- |
| figyelmen kívül | Az érvényesítés kihagyása. |
| megakadályozzák | Blokkolja a kérelem vagy a válasz feldolgozását, naplózza a részletes érvényesítési hibát, és hibát ad vissza. A rendszer megszakítja a feldolgozást, ha az első hibák észlelhetők. |
| detect | Ellenőrzési hibák naplózása a kérelem vagy a válasz feldolgozásának megszakítása nélkül. |

## <a name="logs"></a>Naplók

A házirend-végrehajtás során az érvényesítési hibák részleteit a rendszer a `context.Variables` `errors-variable-name` házirend gyökérszintű elemében található attribútumban megadott változóra naplózza. Ha egy `prevent` műveletben van konfigurálva, egy érvényesítési hiba blokkolja a további kérelmeket és a válaszadások feldolgozását, és a tulajdonságot is propagálja `context.LastError` . 

A hibák kivizsgálásához használjon [nyomkövetési](api-management-advanced-policies.md#Trace) szabályzatot, hogy naplózza a hibákat a környezeti változóktól [Application Insightsra](api-management-howto-app-insights.md).

## <a name="performance-implications"></a>A teljesítmény szempontjai

Az érvényesítési házirendek hozzáadása hatással lehet az API átviteli sebességére. A következő általános elvek érvényesek:
* Minél nagyobb az API-séma mérete, annál kisebb lesz az átviteli sebesség. 
* Minél nagyobb a tartalom egy kérelemben vagy válaszban, annál kisebb lesz az átviteli sebesség. 
* Az API-séma mérete nagyobb hatással van a teljesítményre, mint a hasznos adatok mérete. 
* A több megabájtos méretű API-sémák ellenőrzése bizonyos körülmények között a kérelmek vagy válaszok időtúllépését eredményezheti. A hatás a szolgáltatás  **fogyasztási** és **fejlesztői** szintjein is nagyobb. 

Javasoljuk, hogy terhelési teszteket hajtson végre a várt üzemi számítási feladatokkal, hogy felmérje az érvényesítési szabályzatok API átviteli sebességre gyakorolt hatását

## <a name="validate-content"></a>Tartalom ellenőrzése

A `validate-content` házirend ellenőrzi a kérelem vagy válasz törzsének méretét vagy JSON-sémáját az API-sémán. A JSON-tól eltérő formátumok nem támogatottak.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<validate-content unspecified-content-type-action="ignore|prevent|detect" max-size="size in bytes" size-exceeded-action="ignore|prevent|detect" errors-variable-name="variable name">
    <content type="content type string, for example: application/json, application/hal+json" validate-as="json" action="ignore|prevent|detect" />
</validate-content>
```

### <a name="example"></a>Példa

A következő példában a kérelmekben és a válaszokban szereplő JSON-adattartalom érvényesítése észlelési módban történik. A 100 KB-nál nagyobb adattartalommal rendelkező üzenetek blokkolva vannak. 

```xml
<validate-content unspecified-content-type-action="prevent" max-size="102400" size-exceeded-action="prevent" errors-variable-name="requestBodyValidation">
    <content type="application/json" validate-as="json" action="detect" />
    <content type="application/hal+json" validate-as="json" action="detect" />
</validate-content>

```

### <a name="elements"></a>Elemek

| Név         | Leírás                                                                                                                                   | Kötelező |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| ellenőrzés – tartalom | Gyökérelem.                                                                                                                               | Yes      |
| tartalom | Vegyen fel egy vagy több ilyen elemet a kérelemben vagy válaszban szereplő tartalomtípus érvényesítéséhez, és végezze el a megadott műveletet.  | No |

### <a name="attributes"></a>Attribútumok

| Név                       | Leírás                                                                                                                                                            | Kötelező | Alapértelmezett |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| meghatározatlan – Content-Type-Action | Az API-sémában nem megadott tartalomtípusú kérelmek vagy válaszok esetén végrehajtandó [művelet](#actions) . |  Yes     | N/A   |
| maximális méret | A kérelem vagy válasz törzsének maximális hossza bájtban, a fejlécben bejelölve `Content-Length` . Ha a kérelem törzse vagy a válasz törzse tömörítve van, ez az érték a kibontott hossz. Maximálisan megengedett érték: 102 400 bájt (100 KB).  | Yes       | N/A   |
| méret túllépése – művelet | Azon kérésekhez vagy válaszokhoz végrehajtandó [művelet](#actions) , amelynek törzse meghaladja a ben megadott méretet `max-size` . |  Yes     | N/A   |
| hibák – változó – név | Annak a változónak a neve, amelybe `context.Variables` be szeretné jelentkezni az érvényesítési hibákat.  |   Yes    | N/A   |
| típus | A törzs érvényesítésének végrehajtásához használt tartalomtípus a `Content-Type` fejlécben. Ez az érték megkülönbözteti a kis-és nagybetűket. Ha üres, akkor az az API-sémában megadott összes tartalomtípusra vonatkozik. |   No    |  N/A  |
| érvényesítés – as | A kérelem vagy válasz törzsének a megfelelő tartalomtípussal való érvényesítéséhez használandó ellenőrző motor. Jelenleg az egyetlen támogatott érték a "JSON".   |  Yes     |  N/A  |
| művelet | Olyan kérésekhez vagy válaszokhoz végrehajtandó [művelet](#actions) , amelynek a törzse nem felel meg a megadott tartalomtípusnak.  |  Yes      | N/A   |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.

-   **Házirend fejezetei:** bejövő, kimenő, on-Error

-   **Házirend-hatókörök:** az összes hatókör

## <a name="validate-parameters"></a>Paraméterek ellenőrzése

A `validate-parameters` házirend ellenőrzi a fejléc, a lekérdezés vagy az elérési út paramétereit az API-sémában található kérelmekben.

> [!IMPORTANT]
> Ha a előtti felügyeleti API-verzió használatával importált egy API `2021-01-01-preview` -t, előfordulhat, hogy a `validate-parameters` házirend nem működik. Előfordulhat, hogy újra kell [importálnia az API](/rest/api/apimanagement/2021-01-01-preview/apis/createorupdate) -t a felügyeleti API `2021-01-01-preview` vagy újabb verzió használatával.


### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<validate-parameters specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect" errors-variable-name="variable name"> 
    <headers specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </headers>
    <query specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </query>
    <path specified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </path>
</validate-parameters>
```

### <a name="example"></a>Példa

Ebben a példában a rendszer az összes lekérdezési és elérési utat ellenőrzi a megelőzési módban, valamint a fejléceket az észlelési módban. Az érvényesítés felülbírálva van több fejléc paraméternél:

```xml
<validate-parameters specified-parameter-action="prevent" unspecified-parameter-action="prevent" errors-variable-name="requestParametersValidation"> 
    <headers specified-parameter-action="detect" unspecified-parameter-action="detect">
        <parameter name="Authorization" action="prevent" />
        <parameter name="User-Agent" action="ignore" />
        <parameter name="Host" action="ignore" />
        <parameter name="Referrer" action="ignore" />
    </headers>   
</validate-parameters>
```

### <a name="elements"></a>Elemek

| Név         | Leírás                                                                                                                                   | Kötelező |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| érvényesítés – paraméterek | Gyökérelem. Megadja a kérelmekben szereplő összes paraméter alapértelmezett érvényesítési műveleteit.                                                                                                                              | Yes      |
| fejlécek | Adja hozzá ezt az elemet a kérelmekben szereplő fejléc-paraméterek alapértelmezett érvényesítési műveleteinek felülbírálásához.   | No |
| lekérdezés | Adja hozzá ezt az elemet a kérelmekben szereplő lekérdezési paraméterek alapértelmezett érvényesítési műveleteinek felülbírálásához.  | No |
| path | Adja hozzá ezt az elemet a kérelmekben szereplő URL-elérésiút-paraméterek alapértelmezett érvényesítési műveleteinek felülbírálásához.  | No |
| parameter | Az érvényesítési műveletek magasabb szintű konfigurációjának felülbírálásához vegyen fel egy vagy több megnevezett paramétert tartalmazó elemet. | No |

### <a name="attributes"></a>Attribútumok

| Név                       | Leírás                                                                                                                                                            | Kötelező | Alapértelmezett |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| megadott paraméter – művelet | Az API-sémában megadott kérelmek paramétereinek végrehajtásához végrehajtandó [művelet](#actions) . <br/><br/> Ha egy `headers` , `query` vagy `path` elemben van megadva, az érték felülbírálja a `specified-parameter-action` elem értékét `validate-parameters` .  |  Yes     | N/A   |
| meghatározatlan – paraméter – művelet | Az API-sémában nem megadott kérelmek paramétereinek végrehajtásához szükséges [művelet](#actions) . <br/><br/>Ha egy `headers` vagy `query` elemben van megadva, az érték felülbírálja a `unspecified-parameter-action` elemben lévő értéket `validate-parameters` . |  Yes     | N/A   |
| hibák – változó – név | Annak a változónak a neve, amelybe `context.Variables` be szeretné jelentkezni az érvényesítési hibákat.  |   Yes    | N/A   |
| name | Annak a paraméternek a neve, amely felülbírálja az érvényesítési műveletet. Ez az érték megkülönbözteti a kis-és nagybetűket.  | Yes | N/A |
| művelet | Az egyező nevű paraméterhez végrehajtandó [művelet](#actions) . Ha a paraméter meg van adva az API-sémában, ez az érték felülbírálja a magasabb szintű `specified-parameter-action` konfigurációt. Ha a paraméter nincs megadva az API-sémában, ez az érték felülbírálja a magasabb szintű `unspecified-parameter-action` konfigurációt.| Yes | N/A | 

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.

-   **Házirend fejezetei:** bejövő

-   **Házirend-hatókörök:** az összes hatókör

## <a name="validate-headers"></a>Fejlécek ellenőrzése

A `validate-headers` szabályzat ellenőrzi a válasz fejléceit az API-sémán.

> [!IMPORTANT]
> Ha a előtti felügyeleti API-verzió használatával importált egy API `2021-01-01-preview` -t, előfordulhat, hogy a `validate-headers` házirend nem működik. Előfordulhat, hogy újra kell importálnia az API-t a felügyeleti API `2021-01-01-preview` vagy újabb verzió használatával.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<validate-headers specified-header-action="ignore|prevent|detect" unspecified-header-action="ignore|prevent|detect" errors-variable-name="variable name">
    <header name="header name" action="ignore|prevent|detect" />
</validate-headers>
```

### <a name="example"></a>Példa

```xml
<validate-headers specified-header-action="ignore" unspecified-header-action="prevent" errors-variable-name="responseHeadersValidation" />
```
### <a name="elements"></a>Elemek

| Név         | Leírás                                                                                                                                   | Kötelező |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| érvényesítés – fejlécek | Gyökérelem. Meghatározza a válaszokban szereplő összes fejléc alapértelmezett érvényesítési műveleteit.                                                                                                                              | Yes      |
| fejléc | Adja hozzá a megnevezett fejlécek egy vagy több elemét a válaszokban lévő fejlécek alapértelmezett érvényesítési műveleteinek felülbírálásához. | No |

### <a name="attributes"></a>Attribútumok

| Név                       | Leírás                                                                                                                                                            | Kötelező | Alapértelmezett |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| megadott fejléc – művelet | Az API-sémában megadott válasz fejléceken végrehajtandó [művelet](#actions) .  |  Yes     | N/A   |
| meghatározatlan – fejléc – művelet | Az API-sémában nem megadott válasz fejléceken végrehajtandó [művelet](#actions) .  |  Yes     | N/A   |
| hibák – változó – név | Annak a változónak a neve, amelybe `context.Variables` be szeretné jelentkezni az érvényesítési hibákat.  |   Yes    | N/A   |
| name | Azon fejléc neve, amely felülbírálja az érvényesítési műveletet. Ez az érték megkülönbözteti a kis-és nagybetűket. | Yes | N/A |
| művelet | A megfelelő nevű fejléchez végrehajtandó [művelet](#actions) . Ha a fejléc meg van adva az API-sémában, ez az érték felülbírálja `specified-header-action` az `validate-headers` elem értékét. Ellenkező esetben a felülbírálja az `unspecified-header-action` érvényesítési fejlécek elem értékét. | Yes | N/A | 

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.

-   **Házirend fejezetei:** kimenő, on-Error

-   **Házirend-hatókörök:** az összes hatókör

## <a name="validate-status-code"></a>Állapotkód ellenőrzése

A `validate-status-code` házirend érvényesíti az API-sémára adott válaszokban szereplő HTTP-állapotkódot. Ez a szabályzat felhasználható a háttérbeli hibák szivárgásának megelőzésére, amely tartalmazhat verem-nyomkövetéseket is. 

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<validate-status-code unspecified-status-code-action="ignore|prevent|detect" errors-variable-name="variable name">
    <status-code code="HTTP status code number" action="ignore|prevent|detect" />
</validate-status-code>
```

### <a name="example"></a>Példa

```xml
<validate-status-code unspecified-status-code-action="prevent" errors-variable-name="responseStatusCodeValidation" />
```

### <a name="elements"></a>Elemek

| Név         | Leírás                                                                                                                                   | Kötelező |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| érvényesítés-állapot-kód | Gyökérelem.                                                                                                | Yes      |
| állapot kódja | Adja hozzá a HTTP-állapotkódok egy vagy több elemét, hogy felülírja a válaszokban szereplő állapotkódok alapértelmezett érvényesítési műveletét. | No |

### <a name="attributes"></a>Attribútumok

| Név                       | Leírás                                                                                                                                                            | Kötelező | Alapértelmezett |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| meghatározatlan állapot-kód – művelet | Az API-sémában nem megadott válaszok HTTP-állapotkódok esetében végrehajtandó [művelet](#actions) .  |  Yes     | N/A   |
| hibák – változó – név | Annak a változónak a neve, amelybe `context.Variables` be szeretné jelentkezni az érvényesítési hibákat.  |   Yes    | N/A   |
| code | HTTP-állapotkód az érvényesítési művelet felülbírálásához. | Yes | N/A |
| művelet | A megfelelő állapotkód számára végrehajtandó [művelet](#actions) , amely nincs megadva az API-sémában. Ha az állapotkód meg van adva az API-sémában, ez a felülbírálás nem lép érvénybe. | Yes | N/A | 

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.

-   **Házirend fejezetei:** kimenő, on-Error

-   **Házirend-hatókörök:** az összes hatókör


## <a name="validation-errors"></a>Érvényesítési hibák
A következő táblázat felsorolja az ellenőrzési házirendek összes lehetséges hibáját. 

* **Részletek** – a hibák vizsgálatára használható. Nem jelent nyilvánosan megosztva.
* **Nyilvános válasz** – az ügyfélnek visszaadott hiba. A nem szivárog be a megvalósítás részleteibe.

| **Név**                             | **Típus**                                                        | **Érvényesítési szabály** | **Részletek**                                                                                                                                       | **Nyilvános válasz**                                                                                                                       | **Művelet**           |
|----|----|---|---|---|----|
| **ellenőrzés – tartalom** |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| |RequestBody                                                     | SizeLimit           | A kérelem törzse {size} bájt hosszú, és meghaladja a beállított korlátot ({maxSize} bájt).                                                       | A kérelem törzse {size} bájt hosszú, és túllépi a korlátot ({maxSize} bájt).                                                          | észlelés/megelőzés |
||ResponseBody                                                    | SizeLimit           | A válasz törzse {size} bájt hosszú, és meghaladja a beállított korlátot ({maxSize} bájt).                                                      | Belső hiba miatt nem sikerült feldolgozni a kérést. Forduljon az API-tulajdonoshoz.                                                       | észlelés/megelőzés |
| {messageContentType}                 | RequestBody                                                     | Meghatározatlan         | A nem meghatározott tartalomtípus ({messageContentType}) nem engedélyezett.                                                                                     | A nem meghatározott tartalomtípus ({messageContentType}) nem engedélyezett.                                                                             | észlelés/megelőzés |
| {messageContentType}                 | ResponseBody                                                    | Meghatározatlan         | A nem meghatározott tartalomtípus ({messageContentType}) nem engedélyezett.                                                                                     | Belső hiba miatt nem sikerült feldolgozni a kérést. Forduljon az API-tulajdonoshoz.                                                       | észlelés/megelőzés |
| | ApiSchema                                                       |                     | Az API sémája nem létezik, vagy nem oldható fel.                                                                                          | Belső hiba miatt nem sikerült feldolgozni a kérést. Forduljon az API-tulajdonoshoz.                                                       | észlelés/megelőzés |
|                                      | ApiSchema                                                       |                     | Az API sémája nem határoz meg definíciókat.                                                                                                        | Belső hiba miatt nem sikerült feldolgozni a kérést. Forduljon az API-tulajdonoshoz.                                                       | észlelés/megelőzés |
| {messageContentType}                 | RequestBody / ResponseBody                                      | MissingDefinition   | Az API sémája nem tartalmaz {definitionName} definíciót, amely társítva van a következő tartalomtípushoz: {messageContentType}.                        | Belső hiba miatt nem sikerült feldolgozni a kérést. Forduljon az API-tulajdonoshoz.                                                       | észlelés/megelőzés |
| {messageContentType}                 | RequestBody                                                     | IncorrectMessage    | A kérelem törzse nem felel meg a ({definitionName}) definíciónak, amely társítva van a következő tartalomtípushoz: {messageContentType}.<br/><br/>{valError. Message} sor: {valError. LineNumber}, pozíció: {valError. LinePosition}                  | A kérelem törzse nem felel meg a ({definitionName}) definíciónak, amely társítva van a következő tartalomtípushoz: {messageContentType}.<br/><br/>{valError. Message} sor: {valError. LineNumber}, pozíció: {valError. LinePosition}            | észlelés/megelőzés |
| {messageContentType}                 | ResponseBody                                                    | IncorrectMessage    | A válasz törzse nem felel meg a ({definitionName}) definíciónak, amely társítva van a következő tartalomtípushoz: {messageContentType}.<br/><br/>{valError. Message} sor: {valError. LineNumber}, pozíció: {valError. LinePosition}                                       | Belső hiba miatt nem sikerült feldolgozni a kérést. Forduljon az API-tulajdonoshoz.                                                       | észlelés/megelőzés |
|                                      | RequestBody                                                     | ValidationException | A kérelem törzse nem érvényesíthető a következő tartalomtípushoz: {messageContentType}.<br/><br/>{kivétel részletei}                                                                | Belső hiba miatt nem sikerült feldolgozni a kérést. Forduljon az API-tulajdonoshoz.                                                       | észlelés/megelőzés |
|                                      | ResponseBody                                                    | ValidationException | A válasz törzse nem érvényesíthető a következő tartalomtípushoz: {messageContentType}.<br/><br/>{kivétel részletei}                                                                | Belső hiba miatt nem sikerült feldolgozni a kérést. Forduljon az API-tulajdonoshoz.                                                       | észlelés/megelőzés |
| **ellenőrzés – paraméterek/érvényesítés – fejlécek** |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| {paramName}/{headerName}           | QueryParameter / PathParameter / RequestHeader                  | Meghatározatlan         | A nem meghatározott {Path paraméter/lekérdezési paraméter/header} {paramName} nem engedélyezett.                                                               | A nem meghatározott {Path paraméter/lekérdezési paraméter/header} {paramName} nem engedélyezett.                                                       | észlelés/megelőzés |
| {headerName}                         | ResponseHeader                                                  | Meghatározatlan         | A nem meghatározott fejléc ({headerName}) nem engedélyezett.                                                                                                   | Belső hiba miatt nem sikerült feldolgozni a kérést. Forduljon az API-tulajdonoshoz.                                                       | észlelés/megelőzés |
|                                      |ApiSchema                                                       |                     | Az API sémája nem létezik, vagy nem oldható fel.                                                                                            | Belső hiba miatt nem sikerült feldolgozni a kérést. Forduljon az API-tulajdonoshoz.                                                       | észlelés/megelőzés |
|                                       | ApiSchema                                                       |                     | Az API-séma nem határoz meg definíciókat.                                                                                                          | Belső hiba miatt nem sikerült feldolgozni a kérést. Forduljon az API-tulajdonoshoz.                                                       | észlelés/megelőzés |
| ParamName                          | QueryParameter / PathParameter / RequestHeader / ResponseHeader | MissingDefinition   | Az API sémája nem tartalmazza a (z) {definitionName} definíciót, amely a (z) {Query paraméter/Path paraméter/header} {paramName} elemhez van társítva.  | Belső hiba miatt nem sikerült feldolgozni a kérést. Forduljon az API-tulajdonoshoz.                                                       | észlelés/megelőzés |
| ParamName                          | QueryParameter / PathParameter / RequestHeader                  | IncorrectMessage    | A kérelem nem tartalmazhat több értéket a (z) {Query paraméter/Path paraméter/header} {paramName} esetében.                                           | A kérelem nem tartalmazhat több értéket a (z) {Query paraméter/Path paraméter/header} {paramName} esetében.                                   | észlelés/megelőzés |
| {headerName}                         | ResponseHeader                                                  | IncorrectMessage    | A válasz nem tartalmazhat több értéket a következő fejléchez: {headerName}.                                                                              | Belső hiba miatt nem sikerült feldolgozni a kérést. Forduljon az API-tulajdonoshoz.                                                       | észlelés/megelőzés |
| ParamName                          | QueryParameter / PathParameter / RequestHeader                  | IncorrectMessage    | A {Query paraméter/Path paraméter/header} {paramName} értéke nem felel meg a definíciónak.<br/><br/>{valError. Message} sor: {valError. LineNumber}, pozíció: {valError. LinePosition}                                          | A (z) {Query paraméter/Path paraméter/header} {paramName} értéke nem felel meg a definíciónak.<br/><br/>{valError. Message} sor: {valError. LineNumber}, pozíció: {valError. LinePosition}                              | észlelés/megelőzés |
| {headerName}                         | ResponseHeader                                                  | IncorrectMessage    | A következő fejléc értéke nem felel meg a definíciónak: {headerName}.<br/><br/>{valError. Message} sor: {valError. LineNumber}, pozíció: {valError. LinePosition}                                                                              | Belső hiba miatt nem sikerült feldolgozni a kérést. Forduljon az API-tulajdonoshoz.                                                       | észlelés/megelőzés |
| ParamName                          | QueryParameter / PathParameter / RequestHeader                  | IncorrectMessage    | A {Query paraméter/Path paraméter/header} {paramName} értéke nem elemezhető a definíció alapján. <br/><br/>például. Üzenetet                                | Nem lehetett elemezni a (z) {Query paraméter/Path paraméter/header} {paramName} értékét a definíció alapján. <br/><br/>például. Üzenetet                      | észlelés/megelőzés |
| {headerName}                         | ResponseHeader                                                  | IncorrectMessage    | Nem lehetett elemezni a ({headerName}) fejléc értékét a definíció alapján.                                                                  | Belső hiba miatt nem sikerült feldolgozni a kérést. Forduljon az API-tulajdonoshoz.                                                       | észlelés/megelőzés |
| ParamName                          | QueryParameter / PathParameter / RequestHeader                  | ValidationError     | {Lekérdezési paraméter/elérési út paraméter/fejléc} a (paramName}) nem érvényesíthető.<br/><br/>{kivétel részletei}                                                                      | Belső hiba miatt nem sikerült feldolgozni a kérést. Forduljon az API-tulajdonoshoz.                                                       | észlelés/megelőzés |
| {headerName}                         | ResponseHeader                                                  | ValidationError     | A (headerName}) fejléc nem érvényesíthető.<br/><br/>{kivétel részletei}                                                                                                          | Belső hiba miatt nem sikerült feldolgozni a kérést. Forduljon az API-tulajdonoshoz.                                                       | észlelés/megelőzés |
| **érvényesítés-állapot-kód**             |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| {Status-Code}                        | StatusCode                                                      | Meghatározatlan         | A válasz állapotkód {Status-Code} nem engedélyezett.                                                                                                | Belső hiba miatt nem sikerült feldolgozni a kérést. Forduljon az API-tulajdonoshoz.                                                       | észlelés/megelőzés |


A következő táblázat felsorolja az érvényesítési hibák lehetséges okait, valamint a lehetséges üzenetek értékeit:

|  **Ok**         |    **Üzenetet** |
|---|---|  
| Hibás kérelem       |     {Details} környezeti változóhoz, {Public Response} ügyfélhez|
| A válasz nem engedélyezett  | {Details} környezeti változóhoz, {Public Response} ügyfélhez |






## <a name="next-steps"></a>Következő lépések

A házirendek használatáról további információt a következő témakörben talál:

-   [Szabályzatok API Management](api-management-howto-policies.md)
-   [API-k átalakítása](transform-api.md)
-   Házirend- [hivatkozás](./api-management-policies.md) a szabályzat-utasítások és azok beállításainak teljes listájához
-   [Szabályzatminták](./policy-reference.md)
-   [Hibakezelés](./api-management-error-handling-policies.md)

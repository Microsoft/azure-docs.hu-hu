---
title: Entitások keresése a Bing Entity Search API
titleSuffix: Azure Cognitive Services
description: A Bing Entity Search API használatával kinyerheti és keresheti meg a keresési lekérdezések entitásait és helyét.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 9dabceda17defb24f2a916cd641f625feb551c6a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96353289"
---
# <a name="searching-for-entities-with-the-bing-entity-api"></a>Entitások keresése a Bing Entity API-val

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

## <a name="suggest-search-terms-with-the-bing-autosuggest-api"></a>Keresési kifejezések ajánlása a Bing Autosuggest API

Ha biztosít egy olyan keresőmezőt, ahol a felhasználók megadhatják a keresőkifejezést, a [Bing Autosuggest API](../../bing-autosuggest/get-suggested-search-terms.md) használatával kényelmesebbé teheti a felhasználói élményt. Az API javasolt lekérdezési sztringeket ad vissza a részleges keresőkifejezések alapján, miközben a felhasználó gépel.

Miután a felhasználó megadja a keresőkifejezést, kódolja URL-címként a karakterláncot a [q](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query) lekérdezési paraméter beállítása előtt. Ha például a felhasználó a *Marcus Appel* kifejezésre keres, a `q` értéke legyen *Marcus+Appel* vagy *Marcus%20Appel*.

Ha a keresőkifejezésben elírás van, a keresésre adott válasz tartalmaz egy [QueryContext](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#querycontext) objektumot. Az objektum az eredeti és a javított helyesírást is mutatja, amelyet a Bing a keresés során használt.

```json
"queryContext": {
    "originalQuery": "hollo wrld",
    "alteredQuery": "hello world",
    "alterationOverrideQuery": "+hollo wrld",
    "adultIntent": false
}
```

## <a name="the-bing-entity-search-api-response"></a>A Bing Entity Search API válasz

Az API-válasz [SearchResponse](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#searchresponse) objektumot tartalmaz. Ha a Bing talál egy vonatkozó entitást vagy helyet, az objektum tartalmazza az `entities` mezőt, a `places` mezőt, vagy mindkettőt. Ha nem talál, akkor a válaszobjektum egyik mezőt sem tartalmazza.
> [!NOTE]
> Az entitásválaszok több piacot is támogatnak, de a „helyek” típusú válaszok csak az Egyesült Államokban található vállalkozásokat támogatják. 

Az `entities` mező egy [EntityAnswer](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) objektum, amely az [Entitás](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity) objektumok listáját tartalmazza (lásd: `value` mező). A lista tartalmazhat egyetlen domináns entitást, több egyértelműsítő entitást, vagy mindkettőt. 

A rendszer akkor adja vissza az erőfölénnyel rendelkező entitást, ha a Bing úgy véli, hogy az egyetlen olyan entitás, amely megfelel a kérésnek (nem egyértelmű, hogy melyik entitás teljesíti a kérelmet). Ha több entitás is megfelelhet a kérelemnek, a lista több egyértelműsítő entitást tartalmaz. Ha például a kérelem egy filmes franchise általános címét tartalmazza, a lista nagy valószínűséggel egyértelműsítő entitásokat fog tartalmazni. De ha a kérelem a franchise egyik filmjének a pontos címét adja meg, a lista valószínűleg egyetlen domináns entitásból fog állni.

Az entitások lehetnek közismert személyek, például énekesek, színészek, sportolók, modellek stb.; helyek és nevezetességek, például a Mount Rainier vagy a Lincoln-emlékmű; valamint dolgok, például egy banán, goldendoodle kutya, könyv- vagy filmcím. Az [entityPresentationInfo](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entitypresentationinfo) mező tartalmazza az entitás típusát azonosító mutatókat. Meghatározza például, hogy az entitás egy személy, film, állat vagy látnivaló. A lehetséges típusok listájáért lásd az [entitások különböző típusait](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity-types) ismertető szakaszt.

```json
"entityPresentationInfo": {
    "entityScenario": "DominantEntity",
    "entityTypeHints": ["Attraction"],
    "entityTypeDisplayHint": "Mountain"
}, ...
```

Az alábbiakban egy olyan válasz látható, amely egy domináns és egy egyértelműsítő entitást tartalmaz.

```json
{
    "_type": "SearchResponse",
    "queryContext": {
        "originalQuery": "Mount Rainier"
    },
    "entities": {
        "value": [{
            "contractualRules": [{
                "_type": "ContractualRules/LicenseAttribution",
                "targetPropertyName": "description",
                "mustBeCloseToContent": true,
                "license": {
                    "name": "CC-BY-SA",
                    "url": "https://creativecommons.org/licenses/by-sa/3.0/"
                },
                "licenseNotice": "Text under CC-BY-SA license"
            },
            {
                "_type": "ContractualRules/LinkAttribution",
                "targetPropertyName": "description",
                "mustBeCloseToContent": true,
                "text": "contoso.com",
                "url": "http://contoso.com/mount_rainier"
            },
            {
                "_type": "ContractualRules/MediaAttribution",
                "targetPropertyName": "image",
                "mustBeCloseToContent": true,
                "url": "http://contoso.com/mount-rainier"
            }],
            "webSearchUrl": "https://www.bing.com/search?q=Mount%20Rainier...",
            "name": "Mount Rainier",
            "url": "http://www.northwindtraders.com/",
            "image": {
                "name": "Mount Rainier",
                "thumbnailUrl": "https://www.bing.com/th?id=A4ae343983daa4...",
                "provider": [{
                    "_type": "Organization",
                    "url": "http://contoso.com/mount_rainier"
                }],
                "hostPageUrl": "http://contoso.com/commons/7/72/mount_rain...",
                "width": 110,
                "height": 110
            },
            "description": "Mount Rainier is 14,411 ft tall and the highest mountain...",
            "entityPresentationInfo": {
                "entityScenario": "DominantEntity",
                "entityTypeHints": ["Attraction"]
            },
            "bingId": "38b9431e-cf91-93be-0584-c42a3ecbfdc7"
        },
        {
            "contractualRules": [{
                "_type": "ContractualRules/MediaAttribution",
                "targetPropertyName": "image",
                "mustBeCloseToContent": true,
                "url": "http://contoso.com/mount_rainier_national_park"
            }],
            "webSearchUrl": "https://www.bing.com/search?q=Mount%20Rainier%20National...",
            "name": "Mount Rainier National Park",
            "url": "http://worldwideimporters.com/",
            "image": {
                "name": "Mount Rainier National Park",
                "thumbnailUrl": "https://www.bing.com/th?id=A91bdc5a1b648a695a39...",
                "provider": [{
                    "_type": "Organization",
                    "url": "http://contoso.com/mount_rainier_national_park"
                }],
                "hostPageUrl": "http://contoso.com/en/7/7a...",
                "width": 50,
                "height": 50
            },
            "description": "Mount Rainier National Park is a United States National Park...",
            "entityPresentationInfo": {
                "entityScenario": "DisambiguationItem",
                "entityTypeHints": ["Organization"]
            },
            "bingId": "29d4b681-227a-3924-7bb1-8a54e8666b8c"
        }]
    }
}
```

Az entitás tartalmaz egy `name`, `description` és `image` mezőt. Amikor megjeleníti ezeket a mezőket a felhasználók számára, meg kell adnia a forrásukat. A `contractualRules` mező az alkalmazandó forrásmegjelölések listáját tartalmazza. A szerződéses szabály azonosítja azt a mezőt, amelyre a forrásmegjelölés vonatkozik. A forrásmegjelölés alkalmazására vonatkozó információért lásd a [forrásmegjelöléssel](#data-attribution) foglalkozó témakört.

```json
"contractualRules": [{
    "_type": "ContractualRules/LicenseAttribution",
    "targetPropertyName": "description",
    "mustBeCloseToContent": true,
    "license": {
        "name": "CC-BY-SA",
        "url": "https://creativecommons.org/licenses/by-sa/3.0/"
    },
    "licenseNotice": "Text under CC-BY-SA license"
},
{
    "_type": "ContractualRules/LinkAttribution",
    "targetPropertyName": "description",
    "mustBeCloseToContent": true,
    "text": "contoso.com",
    "url": "http://contoso.com/wiki/Mount_Rainier"
},
{
    "_type": "ContractualRules/MediaAttribution",
    "targetPropertyName": "image",
    "mustBeCloseToContent": true,
    "url": "http://contoso.com/wiki/Mount_Rainier"
}], ...
```

Az entitásinformáció (név, leírás és kép) megjelenítésekor a `webSearchUrl` mezőben lévő URL-címmel hivatkozni kell a Bing-keresés találati oldalára, amely tartalmazza az entitást.

## <a name="find-places"></a>Helyek keresése

A `places` mező egy [LocalEntityAnswer](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) objektum, amely a [hely](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#place) objektumainak listáját tartalmazza (további információt az [entitások típusaiban](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity-types) talál). A lista egy vagy több, a kérésre választ adó helyi entitást tartalmaz.

A helyek lehetnek éttermek, szállodák vagy különböző helyi vállalkozások. Az [entityPresentationInfo](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entitypresentationinfo) mező tartalmazza a helyi entitás típusát azonosító mutatókat. Ilyen mutató lehet például a Place (hely), LocaLBusiness (helyi vállalkozás), Restaurant (étterem). Az egymást követő mutatók leszűkítik az entitás típusát. A lehetséges típusok listájáért lásd az [entitások különböző típusait](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity-types) ismertető szakaszt.

```json
"entityPresentationInfo": {
    "entityScenario": "ListItem",
    "entityTypeHints": ["Place",
    "LocalBusiness",
    "Restaurant"]
}, ...
```
> [!NOTE]
> Az entitásválaszok több piacot is támogatnak, de a „helyek” típusú válaszok csak az Egyesült Államokban található vállalkozásokat támogatják. 

A helyfüggő entitáslekérdezésekben, amely például *a közeli éttermekre* vonatkozhat, a pontos találatokhoz ismerni kell a felhasználó tartózkodási helyét. A kérelemnek mindig használnia kell az X-Search-Location és az X-MSEdge-ClientIP fejléceket a felhasználó helyzetének meghatározásához. Ha a Bing úgy véli, hogy a lekérdezéshez érdemes lehet ismerni a felhasználó tartózkodási helyét, beállítja a [QueryContext](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#querycontext)`askUserForLocation` mezőjét **igaz** értékre. 

```json
{
    "_type": "SearchResponse",
    "queryContext": {
        "originalQuery": "Sinful Bakery and Cafe",
        "askUserForLocation": true
    },
    ...
}
```

A helytalálat tartalmazza a hely nevét, címét és telefonszámát, valamint az entitás webhelyének URL-címét. Az entitásadatok megjelenítésekor a `webSearchUrl` mezőben lévő URL-címmel hivatkozni kell a Bing-keresés találati oldalára, amely tartalmazza az entitást.

```json
"places": {
    "value": [{
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Sinful%20Bakery...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "http://libertysdelightfulsinfulbakeryandcafe.com/",
        "entityPresentationInfo": {
            "entityScenario": "ListItem",
            "entityTypeHints": ["Place",
            "LocalBusiness",
            "Restaurant"]
        },
        "address": {
            "addressLocality": "Seattle",
            "addressRegion": "WA",
            "postalCode": "98112",
            "addressCountry": "US",
            "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
    }]
}
```

> [!NOTE]
> Ön vagy az Ön nevében eljáró harmadik fél nem használhatja, őrizheti meg, tárolhatja, gyorsítótárazhatja, oszthatja meg vagy terjesztheti az Entities API-ról szerzett adatokat tesztelés, fejlesztés, képzés, terjesztés vagy bármely nem Microsoft szolgáltatás vagy funkció elérhetővé tétele céljából.  

## <a name="data-attribution"></a>Adatok forrásmegjelölése

A Bing Entity API által adott válaszok külső felek tulajdonában álló információkat tartalmaznak. Az Ön felelőssége az információk megfelelő használatának biztosítása, például azzal, hogy megfelel a felhasználói felületeihez és folyamataihoz igénybe vett Creative Commons-licencek feltételeinek.

Ha valamely válasz vagy eredmény tartalmazza az `contractualRules`, `attributions` vagy `provider` mezőket, az adatok forrását meg kell jelölnie. Ha a válasz egyiket sem tartalmazza, nem kell megjelölni a forrást. Ha a válasz a `contractualRules` mezőt, valamint az `attributions` és/vagy a `provider` mezőt tartalmazza, az adatok forrásának megjelöléséhez a szerződéses szabályokat kell alkalmaznia.

A következő példa bemutat egy entitást, amely tartalmazza a MediaAttribution (médiatartalmak forrásmegjelölésére vonatkozó) szerződéses szabályt, valamint egy képet, amely egy `provider` mezőt tartalmaz. A MediaAttribution szabály megállapítja, hogy a szabály a képre is vonatkozik, így a kép `provider` mezője figyelmen kívül hagyható, és helyette a MediaAttribution szabály alkalmazható a forrás megjelöléséhez.  

```json
"value": [{
    "contractualRules": [
        ...
        {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "http://contoso.com/mount_rainier"
        }
    ],
    ...
    "image": {
        "name": "Mount Rainier",
        "thumbnailUrl": "https://www.bing.com/th?id=A46378861201...",
        "provider": [{
            "_type": "Organization",
            "url": "http://contoso.com/mount_rainier"
        }],
        "hostPageUrl": "http://www.graphicdesigninstitute.com/Uploaded...",
        "width": 110,
        "height": 110
    },
    ...
}]
```

Ha egy szerződéses szabály tartalmazza a `targetPropertyName` mezőt, a szabály csak az érintett mezőkre vonatkozik. Ha nem, akkor a szabály a `contractualRules` mezőt tartalmazó szülőobjektumra vonatkozik.

A következő példában a `LinkAttribution` szabály tartalmazza a `targetPropertyName` mezőt, így a szabály a `description` mezőre vonatkozik. Az egyes mezőkre vonatkozó szabályoknál be kell szúrnia egy sort közvetlenül a célzott adatok után, amely a szolgáltató webhelyére mutató hivatkozást tartalmaz. A leírás forrásának megjelöléséhez például szúrjon be egy sort közvetlenül a leírás szövege után a szolgáltató webhelyére mutató hivatkozással (esetünkben ez a contoso.com).

```json
"entities": {
    "value": [{
            ...
            "description": "Marcus Appel is a former American....",
            ...
            "contractualRules": [{
                    "_type": "ContractualRules/LinkAttribution",
                    "targetPropertyName": "description",
                    "mustBeCloseToContent": true,
                    "text": "contoso.com",
                    "url": "http://contoso.com/cr?IG=B8AD73..."
                 },
            ...
  
```

### <a name="license-attribution"></a>Licencek forrásmegjelölése

Ha a szerződéses szabályok listája tartalmaz egy [LicenseAttribution](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#licenseattribution) szabályt, a licencre vonatkozó tájékoztatást közvetlenül azután a tartalom után kell megjeleníteni, amelyre a licenc vonatkozik. A `LicenseAttribution` szabály a `targetPropertyName` mező segítségével azonosítja a tulajdonságot, amelyre a licenc vonatkozik.

Az alábbi példa tartalmaz egy `LicenseAttribution` szabályt.

![Licencek forrásmegjelölése](../media/cognitive-services-bing-entities-api/licenseattribution.png)

A megjelenített licencadatok között kell lennie egy hivatkozásnak, amely a licenc információit tartalmazó webhelyre mutat. Általában a licenc nevét szokás hivatkozásként szerepeltetni. Például ha a tájékoztatás így szól: **A szövegre a CC-BY-SA licenc vonatkozik**, és a CC-BY-SA a licenc neve, a CC-BY-SA nevet érdemes hivatkozássá tenni.

### <a name="link-and-text-attribution"></a>Hivatkozások és szövegek forrásmegjelölése

A [LinkAttribution](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#linkattribution) és [TextAttribution](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#textattribution) szabályokat általában az adatszolgáltató azonosítására alkalmazzuk. A `targetPropertyName` mező azonosítja a mezőt, amelyre a szabály vonatkozik.

A szolgáltató megjelölése érdekében szúrjon be egy sort közvetlenül a tartalom után, amelyre a forrásmegjelölés vonatkozik (például a célmező után). A sorban egyértelműen jelezni kell, hogy a szolgáltató az adatok forrása. Egy erre alkalmas megfogalmazás például a következő: „Az adatok forrása: contoso.com”. A `LinkAttribution` szabályoknál létre kell hoznia egy hivatkozást, amely a szolgáltató webhelyére mutat.

Az alábbi példa `LinkAttribution` és `TextAttribution` szabályokat is tartalmaz.

![Hivatkozások és szövegek forrásmegjelölése](../media/cognitive-services-bing-entities-api/linktextattribution.png)

### <a name="media-attribution"></a>Médiatartalmak forrásmegjelölése

Ha az entitás képet tartalmaz, amelyet megjelenít, egy, a szolgáltató webhelyére mutató átkattintásos hivatkozást kell beszúrnia. Ha az entitás tartalmaz egy [MediaAttribution](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#mediaattribution) szabályt, a szabály URL-címe alapján kell létrehozni az átkattintásos hivatkozást. Ha nem tartalmaz, használja a kép `provider` mezőjében lévő URL-címet.

Az alábbi példa egy kép `provider` mezőjét és szerződéses szabályait mutatja be. Mivel a példa tartalmazza a szerződéses szabályt, a kép `provider` mezőjét figyelmen kívül kell hagyni, és a `MediaAttribution` szabályt kell alkalmazni.

![Médiatartalmak forrásmegjelölése](../media/cognitive-services-bing-entities-api/mediaattribution.png)

### <a name="search-or-search-like-experience"></a>Keresési vagy kereséshez hasonló funkciók

A Bing Web Search API-hoz hasonlóan a Bing Entity Search API is használható úgy, hogy csak a közvetlen felhasználói lekérdezésekre vagy keresésekre adjon ki eredményt, de úgy is beállítható, hogy egy alkalmazáson vagy funkción belüli olyan műveletekre is reagáljon, amelyek felhasználói keresési kérelemként is értelmezhetők. Szemléltetés céljából a következő esetek ilyen keresési vagy kereséshez hasonló funkciókra szolgálnak például.

- A felhasználó megad egy lekérdezést közvetlenül egy alkalmazás keresőmezőjében.
- A felhasználó kiválaszt egy meghatározott szöveget vagy képet, és „több információt” vagy „további információt” kér.
- A felhasználó egy keresési robotot kérdez egy adott témáról.
- A felhasználó egy meghatározott objektummal vagy entitással foglalkozik egy vizuális keresés során.

Amennyiben nem biztos benne, hogy egy művelet vagy szándék felfogható-e kereséshez hasonló funkcióként, ajánlott egyeztetni a Microsofttal.

## <a name="throttling-requests"></a>Kérelmek szabályozása

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Következő lépések

* [Próbáljon ki egy rövid](../quickstarts/csharp.md) útmutatót, amellyel megkezdheti az entitások keresését a Bing Entity Search API.
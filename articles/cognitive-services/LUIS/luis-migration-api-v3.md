---
title: Az előrejelzési végpont módosításai a V3 API-ban
description: Megváltoztak a lekérdezés-előrejelzési végpont V3 API-jai. Ebből az útmutatóból megtudhatja, hogyan telepítheti át a 3. verziójú Endpoint API-kat.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 59dfa439f6428f2db972a8f848887e1a74bc2622
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98624303"
---
# <a name="prediction-endpoint-changes-for-v3"></a>Előrejelzési végpont módosításai v3 esetén

Megváltoztak a lekérdezés-előrejelzési végpont V3 API-jai. Ebből az útmutatóból megtudhatja, hogyan telepítheti át a 3. verziójú Endpoint API-kat.

**Általánosan elérhető állapot** – ez a V3 API jelentős JSON-kérést és válasz-változásokat tartalmaz a v2 API-ból.

A V3 API a következő új funkciókat biztosítja:

* [Külső entitások](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)
* [Dinamikus felsorolások](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Előre elkészített entitás JSON-módosításai](#prebuilt-entity-changes)

Az előrejelzési végpontra vonatkozó [kérelem](#request-changes) és [Válasz](#response-changes) jelentős módosításokat tartalmaz a fent felsorolt új funkciók támogatásához, beleértve a következőket:

* [Válasz objektum változásai](#top-level-json-changes)
* [Entitás-szerepkör neve hivatkozás az entitás neve helyett](#entity-role-name-instead-of-entity-name)
* [Entitások megjelölésére szolgáló tulajdonságok a hosszúságú kimondott szöveg](#marking-placement-of-entities-in-utterances)

A [hivatkozás dokumentációja](https://aka.ms/luis-api-v3) a v3 verzióhoz érhető el.

## <a name="v3-changes-from-preview-to-ga"></a>V3 változások az előzetes verzióról a GA-ra

V3 a következő módosításokat hajtotta végre a GA-ra való áttérés részeként:

* A következő előre összeállított entitások különböző JSON-válaszokat tartalmaznak:
    * [OrdinalV1](luis-reference-prebuilt-ordinal.md)
    * [GeographyV2](luis-reference-prebuilt-geographyv2.md)
    * [DatetimeV2](luis-reference-prebuilt-datetimev2.md)
    * Mérhető egység kulcsának neve a `units` -ből `unit`

* Kérelem törzse JSON-változás:
    * innen `preferExternalEntities` : `preferExternalEntities`
    * választható `score` paraméter külső entitásokhoz

* A válasz törzsének JSON-módosításai:
    * `normalizedQuery` távolítva

## <a name="suggested-adoption-strategy"></a>Javasolt bevezetési stratégia

Ha bot Framework-t, Bing Spell Check v7-t vagy csak a LUIS-alkalmazás készítését szeretné áttelepíteni, folytassa a v2-végpont használatát.

Ha tudja, hogy egyik ügyfélalkalmazás vagy integráció (bot Framework és Bing Spell Check v7) sem érintett, és a LUIS-alkalmazás létrehozása és az előrejelzési végpontja is kényelmesen áttelepíthető a v3 előrejelzési végpont használatával. A v2 előrejelzési végpont továbbra is elérhető lesz, és jó visszaesési stratégia.


## <a name="not-supported"></a>Nem támogatott

### <a name="bing-spell-check"></a>Bing – Helyesírás-ellenőrzés

Ez az API nem támogatott a v3 előrejelzési végpontban – továbbra is használja a v2 API-előrejelzési végpontot a helyesírási helyesbítésekhez. Ha a V3 API használatakor helyesírási korrekcióra van szüksége, az ügyfélalkalmazás meghívja a [Bing Spell Check](../bing-spell-check/overview.md) API-t, és a szöveget a megfelelő helyesírásra módosítja, mielőtt elküldené a szöveget a Luis API-nak.

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>A bot Framework és Azure Bot Service ügyfélalkalmazások

Továbbra is használja a v2 API-előrejelzési végpontot, amíg megjelent a bot Framework V 4.7-es verziója.


## <a name="endpoint-url-changes"></a>Végpont URL-címének módosítása

### <a name="changes-by-slot-name-and-version-name"></a>Változások a tárolóhely neve és a verzió neve alapján

A [v3-VÉGPONT http](developer-reference-resource.md#rest-endpoints) -hívásának formátuma megváltozott.

Ha verzió alapján szeretne lekérdezni, először közzé kell tennie az [API-n keresztül](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) a használatával `"directVersionPublish":true` . A tárolóhely neve helyett a verziószámra hivatkozó végpont lekérdezése.

|Érvényes értékek a következőhöz: `SLOT-NAME`|
|--|
|`production`|
|`staging`|

## <a name="request-changes"></a>Módosítási kérelmek

### <a name="query-string-changes"></a>Lekérdezési karakterlánc módosításai

[!INCLUDE [V3 query params](./includes/v3-prediction-query-params.md)]

### <a name="v3-post-body"></a>V3 POST törzs

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "preferExternalEntities": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|Tulajdonság|Típus|Verzió|Alapértelmezett|Cél|
|--|--|--|--|--|
|`dynamicLists`|array|Csak v3|Nem szükségesek.|A [dinamikus listák](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time) segítségével kiterjesztheti a már meglévő betanított és közzétett lista entitást, amely már a Luis-alkalmazásban van.|
|`externalEntities`|array|Csak v3|Nem szükségesek.|A [külső entitások](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time) lehetővé teszi, hogy a Luis-alkalmazás képes legyen az entitások azonosítására és címkézésére a futtatókörnyezet során, amely funkciókként használható a meglévő entitásokhoz. |
|`options.datetimeReference`|sztring|Csak v3|Nincs alapértelmezett érték|A [datetimeV2 eltolásának](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)meghatározására szolgál. A datetimeReference formátuma [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).|
|`options.preferExternalEntities`|boolean|Csak v3|hamis|Meghatározza, hogy a felhasználó [külső entitása (a meglévő entitás nevével megegyező névvel)](schema-change-prediction-runtime.md#override-existing-model-predictions) van-e használva, vagy a modellben lévő meglévő entitást használja-e a rendszer az előrejelzéshez. |
|`query`|sztring|Csak v3|Kötelező.|A **v2-ben** az előre jelzett érték a `q` paraméterben szerepel. <br><br>A **v3**-as verzióban a funkció a `query` paraméterben lesz átadva.|

## <a name="response-changes"></a>Válasz módosításai

A lekérdezési válasz JSON-je úgy módosult, hogy a leggyakrabban használt adattartalomhoz való nagyobb programozási hozzáférést engedélyezzen.

### <a name="top-level-json-changes"></a>Legfelső szintű JSON-változások



A v2 leggyakoribb JSON-tulajdonságai az `verbose` igaz értékre vannak állítva, amely visszaadja az összes leképezést és azok pontszámait a `intents` tulajdonságban:

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

A v3 leggyakoribb JSON-tulajdonságai a következők:

```JSON
{
    "query": "this is your utterance you want predicted",
    "prediction":{
        "topIntent": "intent-name-1",
        "intents": {},
        "entities":{}
    }
}
```

Az `intents` objektum egy Rendezetlen lista. Ne tegyük fel, hogy az első gyermek a megfelel a következőnek: `intents` `topIntent` . Ehelyett használja az `topIntent` értéket a pontszám megkereséséhez:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

A válasz JSON-sémájának módosítása az alábbiakat teszi lehetővé:

* Törölje az eredeti Kimondás és a `query` visszaadott előrejelzés közötti különbséget `prediction` .
* Egyszerűbb programozott hozzáférés az előre jelzett adatértékekhez. A v2-ben a tömbön keresztüli számbavétel helyett a szándékok és az entitások **nevével** is elérheti az értékeket. Az előre jelzett entitási szerepkörök esetében a rendszer a szerepkör nevét adja vissza, mert az a teljes alkalmazásban egyedi.
* Az adattípusok, ha meg vannak határozva, figyelembe veszik azokat. A numerikus értékeket a rendszer már nem adja vissza karakterláncként.
* Az első prioritású előrejelzési információk és az objektumban visszaadott további metaadatok megkülönböztetése `$instance` .

### <a name="entity-response-changes"></a>Entitások válaszának módosításai

#### <a name="marking-placement-of-entities-in-utterances"></a>Entitások elhelyezésének megjelölése a hosszúságú kimondott szöveg-ben

**A v2-ben** egy entitást jelölt ki a és a kifejezéssel `startIndex` `endIndex` .

A **v3**-as verzióban az entitás a és a jelöléssel van megjelölve `startIndex` `entityLength` .

#### <a name="access-instance-for-entity-metadata"></a>Hozzáférés `$instance` az entitás metaadatainak számára

Ha entitás-metaadatokra van szüksége, a lekérdezési karakterláncnak a jelzőt kell használnia, `verbose=true` és a válasz tartalmazza a metaadatokat az `$instance` objektumban. A következő részben a JSON-válaszokban látható példák.

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>Minden előre jelzett entitás tömbként jelenik meg

Az `prediction.entities.<entity-name>` objektum egy tömböt tartalmaz, mert az egyes entitások többször is megtekinthetők a teljes kiosztásban.

<a name="prebuilt-entities-with-new-json"></a>

#### <a name="prebuilt-entity-changes"></a>Előre összeépített entitások módosításai

A v3 válasz objektum az előre elkészített entitások módosításait tartalmazza. Tekintse át az [adott előre összeépített entitásokat](luis-reference-prebuilt-entities.md) további információért.

#### <a name="list-entity-prediction-changes"></a>Entitások előrejelzési változásainak listázása

A lista entitások előrejelzéséhez tartozó JSON a tömbök tömbje lett:

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Az egyes belső tömbök a teljes szövegen belüli szövegnek felelnek meg. A belső objektum egy tömb, mert ugyanaz a szöveg a lista entitásának több allistájában is megjelenhet.

Az objektum és az objektum közötti leképezéskor `entities` `$instance` a rendszer megőrzi az objektumok sorrendjét az entitások listázása során.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

#### <a name="entity-role-name-instead-of-entity-name"></a>Entitás-szerepkör neve az entitás neve helyett

A v2-ben a `entities` tömb az összes előre jelzett entitást visszaadotta, az entitás neve pedig az egyedi azonosító. Ha a v3-as verzióban az entitás szerepköröket használ, és az előrejelzés egy entitási szerepkörhöz tartozik, az elsődleges azonosító a szerepkör neve. Ez azért lehetséges, mert az entitás-szerepkörök nevének egyedinek kell lennie a teljes alkalmazásban, beleértve a más modelleket (szándékok, entitások) nevét.

A következő példában a szöveget tartalmazó kifejezésnek kell megfontolnia: `Yellow Bird Lane` . Ezt a szöveget a rendszer az egyéni `Location` entitás szerepköre alapján Jósolja meg `Destination` .

|Teljes szöveg|Entitás neve|Szerepkörnév|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

A v2-ben az entitás neve azonosítja az entitás _nevét_ az objektum tulajdonságának megfelelően:

```JSON
"entities":[
    {
        "entity": "Yellow Bird Lane",
        "type": "Location",
        "startIndex": 13,
        "endIndex": 20,
        "score": 0.786378264,
        "role": "Destination"
    }
]
```

A v3-as verzióban az entitásra az _entitás szerepkör_ hivatkozik, ha az előrejelzés a következő szerepkörre vonatkozik:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

A v3-as verzióban Ugyanez az eredmény szerepel az `verbose` entitás metaadatainak visszaadására szolgáló jelzővel:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ],
    "$instance":{
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "Yellow Bird Lane",
                "startIndex": 25,
                "length":16,
                "score": 0.9837309,
                "modelTypeId": 1,
                "modelType": "Entity Extractor"
            }
        ]
    }
}
```

<a name="external-entities-passed-in-at-prediction-time"></a>
<a name="override-existing-model-predictions"></a>

## <a name="extend-the-app-at-prediction-time"></a>Az alkalmazás kiterjesztése előrejelzési időpontban

[Megtudhatja](schema-change-prediction-runtime.md) , hogyan bővítheti az alkalmazást az előrejelzési futtatókörnyezetben.


## <a name="next-steps"></a>Következő lépések

A V3 API dokumentációjának használatával frissítheti a meglévő REST-hívásokat a LUIS [Endpoint](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/operations/5cb0a9459a1fe8fa44c28dd8) API-kra.

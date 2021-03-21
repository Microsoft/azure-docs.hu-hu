---
title: Vizsgálja meg a v2 API által létrehozott Video Indexer kimenetet – Azure
titleSuffix: Azure Media Services
description: Ez a témakör a v2 API által előállított Azure Media Services Video Indexer kimenetét vizsgálja.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/16/2020
ms.author: juliako
ms.openlocfilehash: 2ac7c3c2149ce43c860c7726381733ef377de8d3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100530739"
---
# <a name="examine-the-video-indexer-output"></a>Vizsgálja meg a Video Indexer kimenetét

Ha egy videó indexelve van, Video Indexer létrehozza a megadott videó-felismerés részleteit tartalmazó JSON-tartalmat. Az elemzések a következők: átiratok, felismerése, arcok, témakörök, blokkok stb. Az egyes betekintési típusok olyan időtartomány-példányokat tartalmaznak, amelyek azt mutatják be, hogy mikor jelenik meg a videóban. 

A videó összefoglaló adatait a [video Indexer](https://www.videoindexer.ai/) webhelyén található videó **Lejátszás** gombjára kattintva vizuálisan megvizsgálhatja. 

Az API-t úgy is használhatja, hogy meghívja a **video index beolvasása** API-t, és a válasz állapota ok, részletes JSON-kimenetet kap a válasz tartalmaként.

![Insights](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

Ez a cikk a Video Indexer kimenetét (JSON-tartalmat) vizsgálja. <br/>További információ arról, hogy milyen funkciók és információk érhetők el Önnek: [video Indexer](video-indexer-overview.md#video-insights)az információkat.

> [!NOTE]
> Video Indexer összes hozzáférési jogkivonatának lejárata egy óra.

## <a name="get-the-insights"></a>Az ismeretek beszerzése

### <a name="insightsoutput-produced-in-the-websiteportal"></a>A webhelyen/portálon létrehozott bepillantások/kimenetek

1. Nyissa meg a [Video Indexer](https://www.videoindexer.ai/) webhelyét, és jelentkezzen be.
1. Keressen egy videót, amelynek kimenetét meg szeretné vizsgálni.
1. Kattintson a **Lejátszás** gombra.
1. Válassza ki **az észlelések lapot (** összefoglaló bepillantások) vagy az **Idősor** fület (lehetővé teszi a releváns adatfelismerések szűrését).
1. Az összetevők letöltése és azok ismertetése.

További információ: [video-információk megtekintése és szerkesztése](video-indexer-view-edit.md).

## <a name="insightsoutput-produced-by-api"></a>Az API által létrehozott bepillantások/kimenetek

1. A JSON-fájl lekéréséhez hívja meg a [video index API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?) -t.
1. Ha az adott összetevőkre is kíváncsi, hívja le a [videó-összetevő letöltése URL API-t](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Artifact-Download-Url?)

    Az API-hívásban adja meg a kért összetevő típusát (OCR, Faces, Key Frames stb.)

## <a name="root-elements-of-the-insights"></a>Az adatfelismerések gyökerének elemei

|Név|Leírás|
|---|---|
|accountId|A lista VI-fiókjának azonosítója.|
|id|A lista azonosítója.|
|name|A lista neve.|
|leírás|A lista leírása.|
|userName (Felhasználónév)|A listát létrehozó felhasználó neve.|
|létrehozott|A lista létrehozási ideje.|
|privacyMode|A lista adatvédelmi módja (magán/nyilvános).|
|állapot|A lista (feltöltve, feldolgozás, feldolgozás, sikertelen, karanténba helyezve).|
|isOwned|Azt jelzi, hogy a listát az aktuális felhasználó hozta-e létre.|
|isEditable|Azt jelzi, hogy az aktuális felhasználó rendelkezik-e a lista szerkesztéséhez szükséges engedélyekkel.|
|isBase|Azt jelzi, hogy a lista egy alapszintű lista (videó) vagy más videókból (származtatott) készült lejátszási lista.|
|durationInSeconds|A lejátszási lista teljes időtartama.|
|summarizedInsights|Egy [summarizedInsights](#summarizedinsights)tartalmaz.
|videók|A lista összeállítására szolgáló [videók](#videos) listája.<br/>Ha ez a lista más videók (származtatott) időtartományait tartalmazza, az ebben a listában szereplő videók csak a befoglalt időtartományokból származó adatokkal fognak szerepelni.|

```json
{
  "accountId": "bca61527-1221-bca6-1527-a90000002000",
  "id": "abc3454321",
  "name": "My first video",
  "description": "I am trying VI",
  "userName": "Some name",
  "created": "2018/2/2 18:00:00.000",
  "privacyMode": "Private",
  "state": "Processed",
  "isOwned": true,
  "isEditable": false,
  "isBase": false,
  "durationInSeconds": 120, 
  "summarizedInsights" : { . . . }
  "videos": [{ . . . }]
}
```

## <a name="summarizedinsights"></a>summarizedInsights

Ez a szakasz az információk összegzését jeleníti meg.

|Attribútum | Leírás|
|---|---|
|name|A videó neve. Például Azure Monitor.|
|id|A videó azonosítója. Például: 63c6d532ff.|
|privacyMode|A részletezés az alábbi módok egyikével rendelkezhet: **Private**, **Public**. **Nyilvános** – a videó a fiókjában mindenki számára látható, és a videóra mutató hivatkozást is tartalmaz. **Magánjellegű** – a videó mindenki számára látható a fiókjában.|
|duration|Egy időtartamot tartalmaz, amely leírja a betekintési időt. Az időtartam másodpercben.|
|thumbnailVideoId|Annak a videónak az azonosítója, amelyből a miniatűr készült.
|thumbnailId|A videó miniatűr-azonosítója A tényleges miniatűr beszerzéséhez hívja meg a [Get-miniatűrt](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) , és adja át a thumbnailVideoId és a thumbnailId.|
|arcok/animatedCharacters|Nulla vagy több arcot is tartalmazhat. Részletesebb információ: [Faces/animatedCharacters](#facesanimatedcharacters).|
|kulcsszavak|Nulla vagy több kulcsszót is tartalmazhat. Részletesebb információ: [kulcsszavak](#keywords).|
|érzelmeket|Nulla vagy több érzelem is szerepelhet. Részletesebb információ: [érzelmek](#sentiments).|
|audioEffects| Nulla vagy több audioEffects is tartalmazhat. Részletesebb információ: [audioEffects](#audioeffects-public-preview).|
|Címkék| Nulla vagy több címkét is tartalmazhat. További információ: [címkék](#labels).|
|márkák| Nulla vagy több márkát is tartalmazhat. Részletesebb információ: [Brands](#brands).|
|statisztikák | Részletesebb információ: [statisztika](#statistics).|
|érzelmeket| Nulla vagy több érzelem is szerepelhet. Részletesebb információ: [érzelmek](#emotions).|
|témakörök|Nulla vagy több témakört is tartalmazhat. A [témakörök](#topics) betekintést nyújtanak.|

## <a name="videos"></a>videók

|Név|Leírás|
|---|---|
|accountId|A videó VI-fiókjának azonosítója.|
|id|A videó azonosítója.|
|name|A videó neve.
|állapot|A videó állapota (feltöltött, feldolgozás, feldolgozott, sikertelen, karanténba helyezve).|
|processingProgress|A feldolgozási folyamat a feldolgozás során (például 20%).|
|failureCode|Nem sikerült feldolgozni a hibakódot (például: "UnsupportedFileType").|
|failureMessage|Nem sikerült feldolgozni a hibaüzenetet.|
|externalId|A videó külső azonosítója (ha a felhasználó adja meg).|
|Alkalmazásazonosítójának|A videó külső URL-címe (ha a felhasználó adja meg).|
|metaadatok|A videó külső metaadatai (ha a felhasználó adja meg).|
|isAdult|Azt jelzi, hogy a videó manuálisan lett-e áttekintve és azonosítva felnőtt videóként.|
|betekintést|Az adatfelismerési objektum. [További információ: az](#insights)információk.|
|thumbnailId|A videó miniatűr-azonosítója A tényleges miniatűr hívás Get [-thumbnail](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) és a videó azonosítójának és thumbnailId átadása.|
|publishedUrl|A videó továbbítására szolgáló URL-cím.|
|publishedUrlProxy|Egy URL-cím, amely továbbítja a videót (Apple-eszközök esetén).|
|viewToken|Rövid életű megtekintési jogkivonat a videó folyamatos átviteléhez.|
|sourceLanguage|A videó forrásának nyelve|
|language|A videó tényleges nyelve (fordítás).|
|indexingPreset|A videó indexeléséhez használt beállításkészlet.|
|streamingPreset|A videó közzétételéhez használt beállításkészlet.|
|linguisticModelId|A videó átírásához használt CRIS-modell.|
|statisztikák | További információ: [statisztika](#statistics).|

```json
{
    "videos": [{
        "accountId": "2cbbed36-1972-4506-9bc7-55367912df2d",
        "id": "142a356aa6",
        "state": "Processed",
        "privacyMode": "Private",
        "processingProgress": "100%",
        "failureCode": "General",
        "failureMessage": "",
        "externalId": null,
        "externalUrl": null,
        "metadata": null,
        "insights": {. . . },
        "thumbnailId": "89d7192c-1dab-4377-9872-473eac723845",
        "publishedUrl": "https://videvmediaservices.streaming.mediaservices.windows.net:443/d88a652d-334b-4a66-a294-3826402100cd/Xamarine.ism/manifest",
        "publishedProxyUrl": null,
        "viewToken": "Bearer=<token>",
        "sourceLanguage": "En-US",
        "language": "En-US",
        "indexingPreset": "Default",
        "linguisticModelId": "00000000-0000-0000-0000-000000000000"
    }],
}
```
### <a name="insights"></a>betekintést

Az egyes elemzések (például a átirat sorai, az arcok, a márkák stb.) tartalmazzák az egyedi elemek listáját (például face1, face2, face3), és mindegyik elemhez saját metaadatok tartoznak, valamint a példányok listája (amely a további opcionális metaadatokat tartalmazó időtartományok).

Az arcok rendelkezhetnek AZONOSÍTÓval, névvel, miniatűrvel, egyéb metaadatokkal és az ideiglenes példányaik listájával (például: 00:00:05 – 00:00:10, 00:01:00-00:02:30 és 00:41:21 – 00:41:49). Minden ideiglenes példányhoz további metaadatok tartozhatnak. Például az arc téglalapjának koordinátái (20230, 60, 60).

|Verzió|A kód verziója|
|---|---|
|sourceLanguage|A videó forrásának nyelve (feltételezve, hogy egy fő nyelv). [BCP-47](https://tools.ietf.org/html/bcp47) karakterlánc formájában.|
|language|Az adatfelismerés nyelve (a forrás nyelvétől lefordítva). [BCP-47](https://tools.ietf.org/html/bcp47) karakterlánc formájában.|
|átirat|Az [átirat](#transcript) betekintést nyújt.|
|OCR|Az [OCR](#ocr) -betekintést.|
|kulcsszavak|A [kulcsszavak](#keywords) betekintése.|
|blocks|Egy vagy több [blokkot](#blocks) is tartalmazhat|
|arcok/animatedCharacters|Az [arcok/animatedCharacters](#facesanimatedcharacters) betekintés.|
|Címkék|A [címkék](#labels) betekintést nyernek.|
|lövések|A [felvételek](#shots) betekintést nyernek.|
|márkák|A [márkák](#brands) betekintése.|
|audioEffects|A [audioEffects](#audioeffects-public-preview) betekintést nyújt.|
|érzelmeket|Az [érzelmek](#sentiments) betekintése.|
|visualContentModeration|A [visualContentModeration](#visualcontentmoderation) betekintést nyújt.|
|textualContentModeration|A [textualContentModeration](#textualcontentmoderation) betekintést nyújt.|
|érzelmeket| Az [érzelmek](#emotions) betekintése.|
|témakörök|A [témakörök](#topics) betekintést nyújtanak.|
|hangszórók|A [hangszórók](#speakers) betekintést nyernek.|

Példa:

```json
{
  "version": "0.9.0.0",
  "sourceLanguage": "en-US",
  "language": "es-ES",
  "transcript": ...,
  "ocr": ...,
  "keywords": ...,
  "faces": ...,
  "labels": ...,
  "shots": ...,
  "brands": ...,
  "audioEffects": ...,
  "sentiments": ...,
  "visualContentModeration": ...,
  "textualContentModeration": ...
}
```

#### <a name="blocks"></a>blocks

Attribútum | Leírás
---|---
id|A blokk azonosítója.|
esetben|A blokk időtartományait tartalmazó lista.|

#### <a name="transcript"></a>átirat

|Név|Leírás|
|---|---|
|id|A sor azonosítója.|
|szöveg|Maga a átirat.|
|megbízhatóság|A átirat pontosságának megbízhatósága.|
|speakerId|A beszélő azonosítója.|
|language|A átirat nyelve. Az olyan átiratok támogatását célozza, ahol az egyes sorok más nyelven is rendelkezhetnek.|
|esetben|Az időtartományok listája, ahol ez a sor megjelent. Ha a példány átirat, akkor csak 1 példánya lesz.|

Példa:

```json
"transcript":[
{
  "id":1,
  "text":"Well, good morning everyone and welcome to",
  "confidence":0.8839,
  "speakerId":1,
  "language":"en-US",
  "instances":[
     {
    "adjustedStart":"0:00:10.21",
    "adjustedEnd":"0:00:12.81",
    "start":"0:00:10.21",
    "end":"0:00:12.81"
     }
  ]
},
{
  "id":2,
  "text":"ignite 2016. Your mission at Microsoft is to empower every",
  "confidence":0.8944,
  "speakerId":2,
  "language":"en-US",
  "instances":[
     {
    "adjustedStart":"0:00:12.81",
    "adjustedEnd":"0:00:17.03",
    "start":"0:00:12.81",
    "end":"0:00:17.03"
     }
  ]
},
```

#### <a name="ocr"></a>OCR

|Név|Leírás|
|---|---|
|id|Az OCR-sor azonosítója.|
|szöveg|Az OCR szövege.|
|megbízhatóság|Az elismerés megbízhatósága.|
|language|Az OCR nyelve.|
|esetben|Azon időtartományok listája, amelyekben ez az OCR megjelent (ugyanaz az OCR többször is megjelenhet).|
|magasság|Az OCR-négyszög magassága|
|top|A legfelső szintű hely a px-ban|
|balra| A bal oldali hely a px-ban|
|szélesség|Az OCR-négyszög szélessége|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 675.971,
      "height": 35,
      "language": "en-US",
      "left": 31,
      "top": 97,
      "width": 400,      
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    }
  ],
```

#### <a name="keywords"></a>kulcsszavak

|Név|Leírás|
|---|---|
|id|A kulcsszó azonosítója.|
|szöveg|A kulcsszó szövege.|
|megbízhatóság|A kulcsszó felismerési megbízhatósága.|
|language|A kulcsszó nyelve (fordításkor).|
|esetben|Azon időtartományok listája, amelyekben ez a kulcsszó megjelent (a kulcsszó többször is megjelenhet).|

```json
{
    id: 0,
    text: "technology",
    confidence: 1,
    language: "en-US",
    instances: [{
            adjustedStart: "0:05:15.782",
            adjustedEnd: "0:05:16.249",
            start: "0:05:15.782",
            end: "0:05:16.249"
    },
    {
            adjustedStart: "0:04:54.761",
            adjustedEnd: "0:04:55.228",
            start: "0:04:54.761",
            end: "0:04:55.228"
    }]
}
```

#### <a name="facesanimatedcharacters"></a>arcok/animatedCharacters

`animatedCharacters` az elem `faces` abban az esetben lép fel, ha a videó egy animált karakteres modellel lett indexelve. Ez a Custom Vision egyéni modelljével történik, Video Indexer a kulcsképek használatával futtatja.

Ha az arcok (nem animált karakterek) jelennek meg, Video Indexer az összes videó keretén található Face API használja az arcok és hírességek észlelésére.

|Név|Leírás|
|---|---|
|id|A Face azonosító.|
|name|Az arc neve. Ez lehet ismeretlen #0, egy azonosított híresség vagy egy felhasználó által betanított személy.|
|megbízhatóság|Az arc azonosításának megbízhatósága.|
|leírás|A híresség leírása. |
|thumbnailId|Az adott arc miniatűrje.|
|knownPersonId|Ha ismert személy, a belső azonosítója.|
|referenceId|Ha ez egy Bing-híresség, a Bing-azonosítója.|
|referenceType|Jelenleg csak a Bing.|
|cím|Ha ez egy híresség, a címe (például "a Microsoft VEZÉRIGAZGATÓJA").|
|imageUrl|Ha ez egy híresség, a képe URL-címe.|
|esetben|Ezek a példányok, ahol az arc megjelent a megadott időtartományban. Minden példányhoz tartozik egy thumbnailsId is. |

```json
"faces": [{
    "id": 2002,
    "name": "Xam 007",
    "confidence": 0.93844,
    "description": null,
    "thumbnailId": "00000000-aee4-4be2-a4d5-d01817c07955",
    "knownPersonId": "8340004b-5cf5-4611-9cc4-3b13cca10634",
    "referenceId": null,
    "title": null,
    "imageUrl": null,
    "instances": [{
        "thumbnailsIds": ["00000000-9f68-4bb2-ab27-3b4d9f2d998e",
        "cef03f24-b0c7-4145-94d4-a84f81bb588c"],
        "adjustedStart": "00:00:07.2400000",
        "adjustedEnd": "00:00:45.6780000",
        "start": "00:00:07.2400000",
        "end": "00:00:45.6780000"
    },
    {
        "thumbnailsIds": ["00000000-51e5-4260-91a5-890fa05c68b0"],
        "adjustedStart": "00:10:23.9570000",
        "adjustedEnd": "00:10:39.2390000",
        "start": "00:10:23.9570000",
        "end": "00:10:39.2390000"
    }]
}]
```

#### <a name="labels"></a>Címkék

|Név|Leírás|
|---|---|
|id|A címke azonosítója|
|name|A címke neve (például "számítógép", "TV").|
|language|A címke nevének nyelve (fordításkor). BCP-47|
|esetben|Azon időtartományok listája, amelyekben ez a címke megjelent (a címke többször is megjelenhet). Minden példány megbízhatósági mezővel rendelkezik. |


```json
"labels": [
    {
      "id": 0,
      "name": "person",
      "language": "en-US",
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 00.0000000",
          "end": "00: 00: 25.6000000"
        },
        {
          "confidence": 1.0,
          "start": "00: 01: 33.8670000",
          "end": "00: 01: 39.2000000"
        }
      ]
    },
    {
      "name": "indoor",
      "language": "en-US",
      "id": 1,
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 06.4000000",
          "end": "00: 00: 07.4670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 09.6000000",
          "end": "00: 00: 10.6670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 11.7330000",
          "end": "00: 00: 20.2670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 21.3330000",
          "end": "00: 00: 25.6000000"
        }
      ]
    }
  ] 
```

#### <a name="scenes"></a>jelenetek

|Név|Leírás|
|---|---|
|id|A jelenet azonosítója.|
|esetben|A jelenet időtartományának listája (a jelenet csak 1 példányt tartalmazhat).|

```json
"scenes":[  
    {  
      "id":0,
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },
    {  
      "id":1,
      "instances":[  
          {  
            "start":"0:00:06.34",
            "end":"0:00:47.047",
            "duration":"0:00:40.707"
          }
      ]
    },

]
```

#### <a name="shots"></a>lövések

|Név|Leírás|
|---|---|
|id|A shot azonosítója.|
|Kulcsképek|A lövésen belüli kulcsképek listája (mindegyik rendelkezik AZONOSÍTÓval és a példányok időtartományának listájával). Mindegyik kulcsképek-példányhoz tartozik egy thumbnailId mező, amely tartalmazza a kulcs miniatűrje AZONOSÍTÓját.|
|esetben|A lelőtt időtartományok listája (a shot csak 1 példányt tartalmazhat).|

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

#### <a name="brands"></a>márkák

Az üzleti és a termék márkanevei a szöveg átiratának és/vagy a videó OCR-nek a beszédében észlelhetők. Ez nem tartalmazza a márkák vagy embléma-észlelés vizualizációs felismerését.

|Név|Leírás|
|---|---|
|id|A márka azonosítója.|
|name|A márkák neve.|
|referenceId | A márka wikipedia URL-címének utótagja Például a "Target_Corporation" a utótagja [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation) .
|referenceUrl | A márka wikipedia URL-címe, ha van ilyen. Például: [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
|leírás|A márkák leírása.|
|tags|A márkához társított előre definiált címkék listája.|
|megbízhatóság|A Video Indexer Brand detektor (0-1) megbízhatósági értéke.|
|esetben|A márka időtartományának listája. Minden példány rendelkezik egy brandType, amely azt jelzi, hogy ez a márka szerepel-e az átiratban vagy az OCR-ben.|

```json
"brands": [
{
    "id": 0,
    "name": "MicrosoftExcel",
    "referenceId": "Microsoft_Excel",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft_Excel",
    "referenceType": "Wiki",
    "description": "Microsoft Excel is a sprea..",
    "tags": [],
    "confidence": 0.975,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 00: 31.3000000",
        "end": "00: 00: 39.0600000"
    }
    ]
},
{
    "id": 1,
    "name": "Microsoft",
    "referenceId": "Microsoft",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft",
    "description": "Microsoft Corporation is...",
    "tags": [
    "competitors",
    "technology"
    ],
    "confidence": 1.0,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 01: 44",
        "end": "00: 01: 45.3670000"
    },
    {
        "brandType": "Ocr",
        "start": "00: 01: 54",
        "end": "00: 02: 45.3670000"
    }
    ]
}
]
```

#### <a name="statistics"></a>statisztikák

|Név|Leírás|
|---|---|
|CorrespondenceCount|A videóban található Levelezések száma.|
|SpeakerWordCount|A beszélő szavak száma.|
|SpeakerNumberOfFragments|A szónak a videóban található töredékek mennyisége.|
|SpeakerLongestMonolog|A beszélő leghosszabb monologot. Ha a beszélő a monologot belül csendben van, a rendszer belefoglalja. A rendszer eltávolítja a monologot elején és végén található csendet.| 
|SpeakerTalkToListenRatio|A számítás a beszélő monologot töltött idő (a között a csend nélkül) alapján történik, a videó teljes időpontjára bontva. Az idő a harmadik tizedes pontra van kerekítve.|

#### <a name="audioeffects-public-preview"></a>audioEffects (nyilvános előzetes verzió)

|Név|Leírás
|---|---|
|id|Az audio Effect azonosító|
|típus|A hang effektusának típusa|
|esetben|Azon időtartományok listája, amelyekben ez a hanghatás megjelent. Minden példány megbízhatósági mezővel rendelkezik.|

```json
"audioEffects": [
{
    "id": 0,
    "type": "Siren",
    "instances": [
    {
       "confidence": 0.87,
        "start": "00:00:00",
        "end": "00:00:03"
    },
    {
       "confidence": 0.87,
       "start": "00:01:13",
        "end": "00:01:21"
    }
    ]
}
]
```

#### <a name="sentiments"></a>érzelmeket

Az érzelmeket a sentimentType mező alapján összesítjük (pozitív/semleges/negatív). Például: 0-0,1, 0,1-0.2.

|Név|Leírás|
|---|---|
|id|Az érzelmi azonosító.|
|averageScore |Az adott érzelmi típus összes példányának átlaga – pozitív/semleges/negatív|
|esetben|Azon időtartományok listája, amelyekben ez a hangulat megjelent.|
|sentimentType |A típus "pozitív", "semleges" vagy "negatív" lehet.|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
```

#### <a name="visualcontentmoderation"></a>visualContentModeration

A visualContentModeration blokk olyan időtartományokat tartalmaz, amelyeknek a Video Indexer valószínűleg felnőtt tartalommal rendelkeznek. Ha a visualContentModeration üres, nincs azonosított felnőtt tartalom.

A felnőtt vagy zamatos tartalmat tartalmazó videók csak privát nézethez érhetők el. A felhasználóknak lehetősége van arra, hogy a tartalom emberi felülvizsgálatára vonatkozó kérelmet küldjenek, amely esetben a IsAdult attribútum az emberi felülvizsgálat eredményét fogja tartalmazni.

|Név|Leírás|
|---|---|
|id|A vizuális tartalom moderálásának azonosítója.|
|adultScore|A felnőtt pontszám (a tartalom moderátora).|
|racyScore|A zamatos pontszám (a tartalom moderálása alapján).|
|esetben|Azon időtartományok listája, amelyekben a vizualizációs tartalom moderálása megmutatkozott.|

```json
"VisualContentModeration": [
{
    "id": 0,
    "adultScore": 0.00069,
    "racyScore": 0.91129,
    "instances": [
    {
        "start": "00:00:25.4840000",
        "end": "00:00:25.5260000"
    }
    ]
},
{
    "id": 1,
    "adultScore": 0.99231,
    "racyScore": 0.99912,
    "instances": [
    {
        "start": "00:00:35.5360000",
        "end": "00:00:35.5780000"
    }
    ]
}
] 
```

#### <a name="textualcontentmoderation"></a>textualContentModeration 

|Név|Leírás|
|---|---|
|id|A szöveges tartalom moderálásának azonosítója.|
|bannedWordsCount |A tiltott szavak száma.|
|bannedWordsRatio |A szavak összesített számának aránya.|

#### <a name="emotions"></a>érzelmeket

A Video Indexer az érzelmeket beszéd és hangjelek alapján azonosítja. Az azonosított érzelem a következőket jelenthetheti: Joy, szomorúság, harag vagy félelem.

|Név|Leírás|
|---|---|
|id|Az érzelem azonosítója.|
|típus|A beszéd-és hangjelzések alapján azonosított érzelem pillanata. Az érzelem lehet: Joy, szomorúság, düh vagy félelem.|
|esetben|Azon időtartományok listája, amelyekben ez az érzelem megjelent.|

```json
"emotions": [{
    "id": 0,
    "type": "Fear",
    "instances": [{
      "adjustedStart": "0:00:39.47",
      "adjustedEnd": "0:00:45.56",
      "start": "0:00:39.47",
      "end": "0:00:45.56"
    },
    {
      "adjustedStart": "0:07:19.57",
      "adjustedEnd": "0:07:23.25",
      "start": "0:07:19.57",
      "end": "0:07:23.25"
    }]
  },
  {
    "id": 1,
    "type": "Anger",
    "instances": [{
      "adjustedStart": "0:03:55.99",
      "adjustedEnd": "0:04:05.06",
      "start": "0:03:55.99",
      "end": "0:04:05.06"
    },
    {
      "adjustedStart": "0:04:56.5",
      "adjustedEnd": "0:05:04.35",
      "start": "0:04:56.5",
      "end": "0:05:04.35"
    }]
  },
  {
    "id": 2,
    "type": "Joy",
    "instances": [{
      "adjustedStart": "0:12:23.68",
      "adjustedEnd": "0:12:34.76",
      "start": "0:12:23.68",
      "end": "0:12:34.76"
    },
    {
      "adjustedStart": "0:12:46.73",
      "adjustedEnd": "0:12:52.8",
      "start": "0:12:46.73",
      "end": "0:12:52.8"
    },
    {
      "adjustedStart": "0:30:11.29",
      "adjustedEnd": "0:30:16.43",
      "start": "0:30:11.29",
      "end": "0:30:16.43"
    },
    {
      "adjustedStart": "0:41:37.23",
      "adjustedEnd": "0:41:39.85",
      "start": "0:41:37.23",
      "end": "0:41:39.85"
    }]
  },
  {
    "id": 3,
    "type": "Sad",
    "instances": [{
      "adjustedStart": "0:13:38.67",
      "adjustedEnd": "0:13:41.3",
      "start": "0:13:38.67",
      "end": "0:13:41.3"
    },
    {
      "adjustedStart": "0:28:08.88",
      "adjustedEnd": "0:28:18.16",
      "start": "0:28:08.88",
      "end": "0:28:18.16"
    }]
  }
],
```

#### <a name="topics"></a>témakörök

Video Indexer a fő témaköröket a átiratokból származtatja. Ha lehetséges, a 2. szintű [IPTC](https://iptc.org/standards/media-topics/) -besorolás szerepel. 

|Név|Leírás|
|---|---|
|id|A témakör azonosítója.|
|name|A témakör neve, például: "gyógyszeripar".|
|referenceId|A témakörök hierarchiáját tükröző zsemlemorzsa. Például: "Health and jólét/orvostudomány és egészségügy/gyógyszeripar".|
|megbízhatóság|A megbízhatósági pontszám a tartományban [0, 1]. A magasabb szintű bizalom.|
|language|A témakörben használt nyelv.|
|iptcName|Az IPTC adathordozó-kód neve, ha észlelve van.|
|esetben |A Video Indexer jelenleg nem indexeli a témakört az időintervallumok szerint, így a teljes videó az intervallumként lesz használva.|

```json
"topics": [{
    "id": 0,
    "name": "INTERNATIONAL RELATIONS",
    "referenceId": "POLITICS AND GOVERNMENT/FOREIGN POLICY/INTERNATIONAL RELATIONS",
    "referenceType": "VideoIndexer",
    "confidence": 1,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}, {
    "id": 1,
    "name": "Politics and Government",
    "referenceType": "VideoIndexer",
    "iptcName": "Politics",
    "confidence": 0.9041,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}]
. . .
```

#### <a name="speakers"></a>hangszórók

|Név|Leírás|
|---|---|
|id|A hangszóró azonosítója.|
|name|A beszélő neve "Speaker #" formában, *<number>* például: "speaker #1".|
|esetben |Azon időtartományok listája, amelyekben ez a beszélő megjelent.|

```json
"speakers":[
{
  "id":1,
  "name":"Speaker #1",
  "instances":[
     {
    "adjustedStart":"0:00:10.21",
    "adjustedEnd":"0:00:12.81",
    "start":"0:00:10.21",
    "end":"0:00:12.81"
     }
  ]
},
{
  "id":2,
  "name":"Speaker #2",
  "instances":[
     {
    "adjustedStart":"0:00:12.81",
    "adjustedEnd":"0:00:17.03",
    "start":"0:00:12.81",
    "end":"0:00:17.03"
     }
  ]
},
` ` `
```
## <a name="next-steps"></a>Következő lépések

[Video Indexer fejlesztői portál](https://api-portal.videoindexer.ai)

További információ a widgetek alkalmazásban való beágyazásáról: [video Indexer widgetek beágyazása az alkalmazásokba](video-indexer-embed-widgets.md). 


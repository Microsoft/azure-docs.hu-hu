---
title: A v2 API Video Indexer által előállított kimeneti adatok vizsgálata – Azure
titleSuffix: Azure Media Services
description: Ez a témakör a Azure Media Services Video Indexer API által előállított kimeneti adatokat vizsgálja meg.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/16/2020
ms.author: juliako
ms.openlocfilehash: 84bb4766b3a896823dd0bef023f8042965a85846
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532860"
---
# <a name="examine-the-video-indexer-output"></a>A Video Indexer vizsgálata

Egy videó indexeléséhez a Video Indexer állítja elő a megadott videóelemzések részleteit tartalmazó JSON-tartalmat. A betekintő adatok a következők: átiratok, OCR-ok, arcok, témakörök, blokkok stb. Minden elemzéstípus időtartomány-példányokat tartalmaz, amelyek azt mutatják, hogy mikor jelenik meg a videóban a elemzés. 

A videó összesített elemzésének vizuális vizsgálathoz  nyomja le a videó Lejátszás gombját a Video Indexer [webhelyén.](https://www.videoindexer.ai/) 

Az API-t a **Get Video Index** API hívásával is használhatja, és a válasz állapota OK. A válasz tartalma egy részletes JSON-kimenet lesz.

![Insights](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

Ez a cikk a Video Indexer (JSON-tartalom) tartalmát vizsgálja. <br/>Az elérhető funkciókkal és elemzésekkel kapcsolatos információkért lásd: Video Indexer [betekintő adatok.](video-indexer-overview.md#video-insights)

> [!NOTE]
> A hozzáférési jogkivonatok lejárata a Video Indexer egy óra.

## <a name="get-the-insights"></a>Az elemzések lekért adatai

### <a name="insightsoutput-produced-in-the-websiteportal"></a>A webhelyen/portálon előállított elemzések/kimenetek

1. Nyissa meg a [Video Indexer](https://www.videoindexer.ai/) webhelyét, és jelentkezzen be.
1. Keresse meg azt a videót, amelynek kimenetét meg szeretné vizsgálni.
1. Kattintson a **Lejátszás** gombra.
1. Válassza az **Elemzések** lapot (összesített  elemzések) vagy az Idősor lapot (lehetővé teszi a releváns elemzések szűrését).
1. Az összetevők és a bennük való tartalom letöltése.

További információ: [Videóelemzések megtekintése és szerkesztése.](video-indexer-view-edit.md)

## <a name="insightsoutput-produced-by-api"></a>Az API által előállított elemzések/kimenetek

1. A JSON-fájl lekéréshez hívja meg a [Get Video Index API-t](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Index)
1. Ha konkrét összetevők is érdeklik, hívja meg a [Get Video Artifact Download URL API-t](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Artifact-Download-Url)

    Az API-hívásban adja meg a kért összetevőtípust (OCR, Arcok, Kulcskeretek stb.)

## <a name="root-elements-of-the-insights"></a>Az elemzések gyökérelemei

|Név|Leírás|
|---|---|
|accountId|A lejátszási lista VI-fiókazonosítója.|
|id|A lejátszási lista azonosítója.|
|name|A lejátszási lista neve.|
|leírás|A lejátszási lista leírása.|
|userName (Felhasználónév)|A lejátszási listát létrehozó felhasználó neve.|
|Létrehozott|A lejátszási lista létrehozásának ideje.|
|privacyMode (adatvédelmi mód)|A lejátszási lista adatvédelmi módja (Privát/Nyilvános).|
|állapot|A lejátszási lista (feltöltés, feldolgozás, feldolgozás, sikertelen, karanténba helyezett) listája.|
|isOwned (Tulajdonos)|Azt jelzi, hogy a lejátszási listát az aktuális felhasználó hozta-e létre.|
|isEditable|Jelzi, hogy az aktuális felhasználó jogosult-e a lejátszási lista szerkesztésére.|
|isBase|Azt jelzi, hogy a lejátszási lista egy alap lejátszási lista (videó) vagy más videókból (származtatott) lejátszási lista.|
|durationInSeconds (durationInSeconds)|A lejátszási lista teljes időtartama.|
|summarizedInsights|Egy [summarizedInsights adatokat tartalmaz.](#summarizedinsights)
|Videók|A lejátszási listát [összeező](#videos) videók listája.<br/>Ha ez a lejátszási lista más (származtatott) videók időtartományaiból épül fel, akkor a listában szereplő videók csak a benne foglalt időtartományokból származó adatokat fogják tartalmazni.|

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

Ez a szakasz az elemzések összegzését mutatja be.

|Attribútum | Leírás|
|---|---|
|name|A videó neve. Például: Azure Monitor.|
|id|A videó azonosítója. Például: 63c6d532ff.|
|privacyMode (adatvédelmi mód)|A lebontás a következő módok egyikével lehet: **Private (Privát)** és **Public (Nyilvános).** **Nyilvános** – a videó mindenki számára látható a fiókjában és mindenki számára, aki rendelkezik a videóra mutató hivatkozással. **Privát** – a videó a fiókjában mindenki számára látható.|
|duration|Egy időtartamot tartalmaz, amely a betekintés idejét írja le. Az időtartam másodpercben van megsz.|
|thumbnailVideoId|Annak a videónak az azonosítója, amelyből a miniatűr készült.
|thumbnailId (miniatűrazonosító)|A videó miniatűr-azonosítója. A tényleges miniatűr lehíváshoz hívja meg a [Get-Thumbnailt,](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Thumbnail) és adja át neki a thumbnailVideoId és a thumbnailId értékeket.|
|arcok/animáltdiagramok|Nulla vagy több arcot tartalmazhat. Részletesebb információkért lásd az [arcokat/animált diagramokat.](#facesanimatedcharacters)|
|kulcsszavak|Nulla vagy több kulcsszót tartalmazhat. Részletesebb információkért lásd a [kulcsszavakat.](#keywords)|
|Érzelmeket|Nulla vagy több hangulat is lehet. Részletesebb információkért lásd: [hangulatok.](#sentiments)|
|audioEffects| Nulla vagy több audioEffect-et tartalmazhat. További információ: [audioEffects](#audioeffects-public-preview).|
|Címkék| Nulla vagy több címkét tartalmazhat. További információ: [címkék.](#labels)|
|Márkák| Nulla vagy több márkát tartalmazhat. Részletesebb információkért lásd: [márkák.](#brands)|
|Statisztikák | Részletesebb információkért lásd a [statisztikákat.](#statistics)|
|Érzelmek| Nulla vagy több érzelmeket tartalmazhat. További információ: [érzelmek.](#emotions)|
|Témák|Nulla vagy több témakört tartalmazhat. A [témakörökre való rálátás.](#topics)|

## <a name="videos"></a>Videók

|Név|Leírás|
|---|---|
|accountId|A videó VI-fiókazonosítója.|
|id|A videó azonosítója.|
|name|A videó neve.
|állapot|A videó állapota (feltöltve, feldolgozva, feldolgozva, sikertelen, karanténba töltve).|
|processingProgress (feldolgozás, forgalom)|A feldolgozási folyamat előrehaladása a feldolgozás során (például 20%).|
|failureCode (hibakód)|A hibakód, ha a feldolgozás nem sikerült (például "UnsupportedFileType").|
|failureMessage|Ha a feldolgozás sikertelen volt, a hibaüzenet.|
|externalId|A videó külső azonosítója (ha a felhasználó adja meg).|
|externalUrl|A videó külső URL-címe (ha a felhasználó adja meg).|
|metaadatok|A videó külső metaadatai (ha a felhasználó adja meg).|
|isAdult (isAdult)|Azt jelzi, hogy a videó manuálisan lett-e felülvizsgálva és felnőtt videóként lett-e azonosítva.|
|Betekintést|Az insights objektum. További információkért lásd az [elemzéseket.](#insights)|
|thumbnailId (miniatűrazonosító)|A videó miniatűr-azonosítója. A tényleges miniatűrhívás [Get-Thumbnail](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Thumbnail) hívásának lehívása, majd a videó azonosítója és a thumbnailId átadás.|
|publishedUrl|Egy URL-cím, amely streameli a videót.|
|publishedUrlProxy|Egy URL-cím, amelyből streamelni lehet a videót (Apple-eszközök esetén).|
|viewToken (nézettoken)|Egy rövid életű megtekintési jogkivonat a videó streamelése érdekében.|
|sourceLanguage (forrás nyelv)|A videó forrásnyelve.|
|language|A videó tényleges nyelve (fordítása).|
|indexingPreset|A videó indexeléhez használt előzetes beállítás.|
|streamingPreset|A videó közzétételéhez használt előzetes beállítás.|
|linguisticModelId (nyelvi modellazonosító)|A videó átírása a CRIS-modellel.|
|Statisztikák | További információ: [statisztika.](#statistics)|

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
### <a name="insights"></a>Betekintést

Minden elemzés (például átiratok, arcok, márkák stb.) egyedi elemek listáját tartalmazza (például face1, face2, face3), és mindegyik elem saját metaadatokkal és példányainak listájával rendelkezik (amelyek további választható metaadatokkal együtt időtartományok).

Az arcok azonosítóval, névvel, miniatűrvel, egyéb metaadatokkal és historikus példányok listájával (például: 00:00:05 – 00:00:10, 00:01:00 - 00:02:30 és 00:41:21 – 00:41:49.) Minden historikus példány további metaadatokkal is lehet. Például az arc téglalapkoordinátái (20 230,60,60).

|Verzió|A kód verziója|
|---|---|
|sourceLanguage (forrás nyelvének)|A videó forrásnyelve (egy fő nyelvet feltételezve). [BCP-47 sztring formájában.](https://tools.ietf.org/html/bcp47)|
|language|Az elemzési nyelv (a forrásnyelvről lefordítva). [BCP-47 sztring formájában.](https://tools.ietf.org/html/bcp47)|
|Átirat|Az [átirat](#transcript) elemzés.|
|Ocr|Az [OCR-elemzés.](#ocr)|
|kulcsszavak|A [kulcsszóttekintés.](#keywords)|
|blocks|Egy vagy több [blokkot](#blocks) tartalmazhat|
|arcok/animáltdiagramok|Az [arcok/animáltDiagramok elemzés.](#facesanimatedcharacters)|
|Címkék|A [címkék elemzés.](#labels)|
|Lövések|A [felvételekre adott](#shots) elemzés.|
|Márkák|A [márkabetekintés.](#brands)|
|audioEffects|Az [audioEffects elemzés.](#audioeffects-public-preview)|
|Érzelmeket|A [hangulatelemzés.](#sentiments)|
|visualContentModeration|A [visualContentModeration elemzés.](#visualcontentmoderation)|
|textualContentModeration|A [textualContentModeration elemzés.](#textualcontentmoderation)|
|Érzelmek| Az [érzelmekkel való](#emotions) elemzés.|
|Témák|A [témakör-elemzés.](#topics)|
|Hangszórók|A [beszélők meglátása.](#speakers)|

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
Példányok|A blokk időtartományának listája.|

#### <a name="transcript"></a>Átirat

|Név|Leírás|
|---|---|
|id|A sorazonosító.|
|szöveg|Magát az átiratot.|
|megbízhatóság|Az átirat pontosságának megbízhatósága.|
|speakerId (beszélőazonosító)|A beszélő azonosítója.|
|language|Az átirat nyelve. Olyan átiratok támogatásához lett szánva, amelyekben minden sor különböző nyelvvel is lehet.|
|Példányok|Azon időtartományok listája, ahol ez a sor megjelent. Ha a példány átirat, csak 1 példánya lesz.|

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

#### <a name="ocr"></a>Ocr

|Név|Leírás|
|---|---|
|id|Az OCR-sorazonosító.|
|szöveg|Az OCR-szöveg.|
|megbízhatóság|A felismerés megbízhatósága.|
|language|Az OCR nyelv.|
|Példányok|Időtartományok listája, ahol ez az OCR megjelent (ugyanaz az OCR többször is megjelenhet).|
|magasság|Az OCR-téglalap magassága|
|top|A felső hely a px-ben|
|balra| A bal oldali hely px-ben|
|szélesség|Az OCR-téglalap szélessége|

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
|Példányok|Azon időtartományok listája, ahol ez a kulcsszó megjelent (egy kulcsszó többször is megjelenhet).|

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

#### <a name="facesanimatedcharacters"></a>arcok/animáltdiagramok

`animatedCharacters` A elem lecseréli a elemet arra az esetre, ha a videó `faces` animált karaktermodellel lett indexelve. Ez egy egyéni modell használatával történik a Custom Vision, Video Indexer kulcsképkockákon futtatja.

Ha arcok (nem animált karakterek) vannak jelen, Video Indexer Face API-t használ a videó összes képkockán az arcok és hírességek észlelésére.

|Név|Leírás|
|---|---|
|id|Az arcazonosító.|
|name|Az arc neve. Ez lehet "Ismeretlen #0, azonosított híresség vagy egy ügyfél által betanított személy.|
|megbízhatóság|Az arcazonosítás megbízhatósága.|
|leírás|A híresség leírása. |
|thumbnailId (miniatűrazonosító)|Az arc miniatűrje azonosítója.|
|knownPersonId (ismertpersonId)|Ha ismert személy, a belső azonosítója.|
|referenceId (referenciaazonosító)|Ha bingi híresség, akkor a Bing-azonosítója.|
|referenceType (referenciatípus)|Jelenleg csak a Bing.|
|cím|Ha hírességről van szó, akkor a beosztása (például "a Microsoft vezérigazgatója").|
|imageUrl|Hírességek esetén a kép URL-címe.|
|Példányok|Ezek azok a példányok, ahol az arc megjelent az adott időtartományban. Minden példányhoz tartozik egy thumbnailsId is. |

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
|id|A címke azonosítója.|
|name|A címke neve (például Számítógép, TV).|
|language|A címke nevének nyelve (fordításkor). BCP-47|
|Példányok|Azon időtartományok listája, ahol ez a címke megjelent (egy címke többször is megjelenhet). Minden példány rendelkezik egy megbízhatósági mezővel. |


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

#### <a name="scenes"></a>Jelenetek

|Név|Leírás|
|---|---|
|id|A jelenet azonosítója.|
|Példányok|A jelenet időtartományának listája (egy jelenetnek csak 1 példánya lehet).|

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

#### <a name="shots"></a>Lövések

|Név|Leírás|
|---|---|
|id|A felvétel azonosítója.|
|Kulcsképek|A felvételen belüli kulcskeretek listája (mindegyik rendelkezik egy azonosítóval és a példányok időtartományainak listájával). Minden keyFrame-példány rendelkezik egy thumbnailId mezővel, amely a keyFrame miniatűr-azonosítóját tartalmazza.|
|Példányok|A felvétel időtartományának listája (egy felvételnek csak 1 példánya lehet).|

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

#### <a name="brands"></a>Márkák

A beszéd szöveges átiratában és/vagy a video OCR-ben észlelt üzleti és termék márkaneveket. Ez nem vonatkozik a márka- vagy emblémafelismerés vizuális felismerésére.

|Név|Leírás|
|---|---|
|id|A márkaazonosító.|
|name|A márkanév.|
|referenceId (referenciaazonosító) | A márka wikipédia URL-címének utótagja. A "Target_Corporation" például a [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation) utótagja.
|referenceUrl | A márka Wikipédia URL-címe, ha létezik. Például: [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
|leírás|A márka leírása.|
|tags|Az ehhez a márkához társított előre definiált címkék listája.|
|megbízhatóság|A márkaérzékelő megbízhatósági Video Indexer (0-1).|
|Példányok|A márka időtartományának listája. Minden példány rendelkezik egy brandType típussal, amely azt jelzi, hogy ez a márka megjelent-e az átiratban vagy az OCR-ben.|

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

#### <a name="statistics"></a>Statisztikák

|Név|Leírás|
|---|---|
|CorrespondenceCount (Megfeleltetés száma)|A videóban a megfeleltetések száma.|
|SpeakerWordCount|A szavak száma beszélőnként.|
|SpeakerNumberOfFragments|A videóban a beszélő töredékeinek mennyisége.|
|SpeakerLongestMonolog|A beszélő leghosszabb monologja. Ha a beszélő csendet tartalmaz a monologban, akkor az is benne van. A monolog elején és végén álló csend el lesz távolítva.| 
|SpeakerTalkToListenRatio|A számítás a beszélő monologja (a közöttük álló csend nélkül) és a videó teljes ideje között töltött idő alapján van kiszámítva. Az idő a harmadik tizedespontra van kerekítve.|

#### <a name="audioeffects-public-preview"></a>audioEffects (nyilvános előzetes verzió)

|Név|Leírás
|---|---|
|id|A hanghatás azonosítója|
|típus|A hanghatás típusa|
|Példányok|Azon időtartományok listája, ahol ez a hanghatás megjelent. Minden példány rendelkezik egy megbízhatósági mezővel.|

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

#### <a name="sentiments"></a>Érzelmeket

A hangulatokat a rendszer a sentimentType mező alapján összesíti (pozitív/semleges/negatív). Például: 0-0.1, 0.1-0.2.

|Név|Leírás|
|---|---|
|id|A hangulatazonosító.|
|averageScore (átlag pontszám) |Az adott hangulattípus összes előfordulásának átlaga – pozitív/semleges/negatív|
|Példányok|Azon időtartományok listája, ahol ez a hangulat megjelent.|
|sentimentType (hangulattípus) |A típus lehet "Pozitív", "Semleges" vagy "Negatív".|

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

A visualContentModeration blokk olyan időtartományokat tartalmaz Video Indexer amelyek vélhetően felnőtt tartalommal is fertőzöttek. Ha a visualContentModeration üres, nem található azonosított felnőtt tartalom.

Előfordulhat, hogy a felnőtteknek szóló vagy ritka tartalmú videók csak privát nézetben érhetők el. A felhasználók kérhetik a tartalom emberi felülvizsgálatára vonatkozó kérést, amely esetben az IsAdult attribútum tartalmazza az emberi felülvizsgálat eredményét.

|Név|Leírás|
|---|---|
|id|A vizualizáció tartalommoderálási azonosítója.|
|adultScore (felnőtt pontszám)|A felnőtt pontszám (a content moderatorból).|
|racyScore (racyScore)|A racy pontszám (tartalommoderálásból).|
|Példányok|Időtartományok listája, ahol ez a vizuális tartalommoderálás megjelent.|

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
|id|A szöveges tartalom moderálási azonosítója.|
|bannedWordsCount |A tiltott szavak száma.|
|bannedWordsRatio |A szavak teljes számával való arány.|

#### <a name="emotions"></a>Érzelmek

Video Indexer beszéd és hang jel alapján azonosítja az érzelmeket. Az azonosított érzelmek a következőek lehetnek: szomorúság, szomorúság, szomorúság vagy félelem.

|Név|Leírás|
|---|---|
|id|Az érzelemazonosító.|
|típus|Az érzelmek pillanata, amely beszéd és hang jel alapján lett azonosítva. Az érzelmek a következőek lehetnek: szomorúság, szomorúság, szomorúság vagy félelem.|
|Példányok|Azon időtartományok listája, ahol ez az érzelem megjelent.|

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

#### <a name="topics"></a>Témák

Video Indexer fő témaköröket az átiratokból lehet kihozni. Ha lehetséges, a rendszer a második szintű IPTC-taxonómiát is tartalmazza. [](https://iptc.org/standards/media-topics/) 

|Név|Leírás|
|---|---|
|id|A témakör azonosítója.|
|name|A témakör neve, például: "Gyógyszeripar".|
|referenceId (referenciaazonosító)|A témakörök hierarchiáját tükröző navigációs felület. Például: "Health and wellbeing / Medicine and healthcare / Gyógyszeripar".|
|megbízhatóság|A [0,1] tartomány megbízhatósági pontszáma. A magasabb szint magabiztosabb.|
|language|A témakörben használt nyelv.|
|iptcName|Az IPTC-médiakód neve, ha észlelhető.|
|Példányok |Jelenleg Video Indexer nem indexel egy témakört időintervallumok szerint, így a teljes videó lesz az időköz.|

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

#### <a name="speakers"></a>Hangszórók

|Név|Leírás|
|---|---|
|id|A beszélő azonosítója.|
|name|A beszélő neve "Speaker # *<number>* " alakban, például: "Speaker #1".|
|Példányok |Azon időtartományok listája, ahol a beszélő megjelent.|

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

További információ a widgetek alkalmazásba való beágyazásával kapcsolatban: [Video Indexer-widgetek beágyazása az alkalmazásokba.](video-indexer-embed-widgets.md) 


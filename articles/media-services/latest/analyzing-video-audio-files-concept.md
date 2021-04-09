---
title: Videó-és hangfájlok elemzése
description: Ismerje meg, hogyan elemezheti a hang-és videotartalom-tartalmakat a Azure Media Services AudioAnalyzerPreset és VideoAnalyzerPreset használatával.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 03/22/2021
ms.author: inhenkel
ms.openlocfilehash: e062eb1ecc37a60567a6b8af0d4e0f27b1e73c36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863343"
---
# <a name="analyze-video-and-audio-files-with-azure-media-services"></a>Videó-és hangfájlok elemzése Azure Media Services

[!INCLUDE [regulation](../video-indexer/includes/regulation.md)]

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A Azure Media Services v3 lehetővé teszi a videó-és hangfájlokból származó elemzések kinyerését Video Indexer segítségével. Ez a cikk az adatok kinyeréséhez használt Media Services v3 Analyzer-előállítók leírását ismerteti. Ha részletesebb megállapításokra van szüksége, használja közvetlenül a Video Indexert. Ha szeretné megismerni, hogy mikor érdemes használni a Video Indexer vs. Media Services Analyzer-készleteket, tekintse meg az [összehasonlító dokumentumot](../video-indexer/compare-video-indexer-with-media-services-presets.md).

Az audio Analyzer beállításkészletének két módja van: alapszintű és standard. Tekintse meg az alábbi táblázatban szereplő különbségek leírását.

Ha Media Services v3-es előkészletekből szeretné elemezni a tartalmakat, hozzon létre egy **átalakítót** , és küldjön el egy olyan **feladatot** , amely a következő beállításkészletek egyikét használja: [VideoAnalyzerPreset](/rest/api/media/transforms/createorupdate#videoanalyzerpreset) vagy **AudioAnalyzerPreset**. A **VideoAnalyzerPreset** használatát bemutató oktatóanyagért lásd: [videók elemzése Azure Media Servicesokkal](analyze-videos-tutorial-with-api.md).

## <a name="compliance-privacy-and-security"></a>Megfelelőség, adatvédelem és biztonság

Fontos megjegyezni, hogy meg kell felelnie a Video Indexer használatának összes vonatkozó törvényének, és előfordulhat, hogy nem használja Video Indexer vagy bármely más Azure-szolgáltatást olyan módon, amely sérti mások jogait, vagy más személyeknek is ártalmas lehet. Mielőtt bármilyen videót feltölt, beleértve a biometrikus adatokat is a Video Indexer szolgáltatásba feldolgozásra és tárolásra, az összes megfelelő jogosultsággal kell rendelkeznie, beleértve a megfelelő hozzájárulásokat is a videóban szereplő személy (ek) hoz. A megfelelőség, az adatvédelem és a biztonság Video Indexer az Azure [Cognitive Services feltételeinek](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)megismerése. A Microsoft adatvédelmi kötelezettségei és az adatok kezelése érdekében tekintse át a Microsoft [adatvédelmi nyilatkozatát](https://privacy.microsoft.com/PrivacyStatement), az [online szolgáltatások használati feltételeit](https://www.microsoft.com/licensing/product-licensing/products) ("Ost") és az [adatfeldolgozási kiegészítést](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Az adatmegőrzéssel, törléssel és megsemmisítéssel kapcsolatos további adatvédelmi információk az OST-ben és [itt](../video-indexer/faq.md)érhetők el. Video Indexer használatával Ön vállalja, hogy az Cognitive Services feltételek, az OST, a DPA és az adatvédelmi nyilatkozat köti magát.

## <a name="built-in-presets"></a>Beépített beállításkészletek

A Media Services jelenleg a következő beépített analizátor-beállításkészleteket támogatja:  

|**Előre definiált név**|**Forgatókönyv/mód**|**Részletek**|
|---|---|---|
|[AudioAnalyzerPreset](/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|Szabványos hangalapú mód elemzése|Az előre definiált AI-alapú elemzési műveletek, beleértve a beszédfelismerést, előre meghatározott készletet alkalmaznak. Jelenleg az előre definiált tartalom egyetlen hangfelvételsel támogatja a tartalmat, amely egyetlen nyelven tartalmaz beszédet. Megadhatja a bemenetben lévő hangtartalom nyelvét a "Language tag-Region" BCP-47 formátumával. A támogatott nyelvek: angol ("en-US", "en-GB" és "en-AU"), spanyol ("es-ES" és "es-MX"), francia ("fr-FR" és "fr-CA"), olasz ("It"), Japán ("ja-JP"), Portugál ("PT-BR"), Kínai ("zh-CN"), német ("de-DE"), Arab ("AR-BH", "ar-", "AR-IQ", "AR-JO", "AR-KW", "AR-LB ', ' AR-OM ', ' AR-QA ', ' AR-SA ' és ' AR-SY '), Orosz (' ru-RU '), hindi (' Hi-IN '), Koreai (' ko-KR '), dán (" da-DK "), norvég (" NB-NO "), svéd (" SV-SE "), finn (" fi-FI "), thai (" th-TH ") és török (" TR-TR ").<br/><br/> Ha a nyelv nincs megadva vagy NULL értékre van állítva, az automatikus nyelvfelismerés kiválasztja az első nyelvet, és a fájl időtartamára folytatja a kiválasztott nyelvet. Az automatikus nyelvfelismerés funkció jelenleg a következőket támogatja: angol, kínai, francia, német, olasz, Japán, spanyol, orosz és portugál. Az első nyelv észlelése után nem támogatja a nyelvek közötti dinamikus váltást. Az automatikus nyelvfelismerés funkció a hangfelvételek és a jól felismerhető beszédek esetében a legjobban működik. Ha az automatikus nyelvfelismerés nem találja a nyelvet, az átirat visszatérhet az angol nyelvre.|
|[AudioAnalyzerPreset](/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|Az alapszintű hang mód elemzése|Ez az előre beállított mód a beszéd-szöveg átírását és a VTT alcím/Caption fájljának létrehozását végzi. A mód kimenete tartalmaz egy bepillantást nem tartalmazó JSON-fájlt, amely csak a kulcsszavakat, az átírást és az időzítési adatokat tartalmazza. Ez a mód nem tartalmazza az automatikus nyelvfelismerés és a hangszórók diarization. A támogatott nyelvek listája megegyezik a fenti standard móddal.|
|[VideoAnalyzerPreset](/rest/api/media/transforms/createorupdate#videoanalyzerpreset)|Hang és videó elemzése|Kinyeri az elemzéseket (gazdag metaadatokat) a hang-és a videóból, és egy JSON formátumú fájlt ad kimenetként. Megadhatja, hogy a videofájl feldolgozásakor csak hangelemzéseket szeretne-e kinyerni. További információt a [videó elemzése](analyze-videos-tutorial-with-api.md)című témakörben talál.|
|[FaceDetectorPreset](/rest/api/media/transforms/createorupdate#facedetectorpreset)|A videóban lévő arcok észlelése|Ismerteti azokat a beállításokat, amelyeket a rendszer a videó elemzésekor használ a jelen lévő összes arc észleléséhez.|

### <a name="audioanalyzerpreset-standard-mode"></a>AudioAnalyzerPreset standard üzemmód

A beállításkészlet lehetővé teszi, hogy egy hang-vagy videofájl használatával több hangelemzést is kinyerjen.

A kimenet tartalmaz egy JSON-fájlt (az összes elemzéssel) és a VTT-fájlt a hangátirathoz. Ez a beállításkészlet egy olyan tulajdonságot fogad el, amely a bemeneti fájl nyelvét adja meg [BCP47](https://tools.ietf.org/html/bcp47) karakterlánc formájában. Az audio-elemzések a következők:

* **Hang átirata**: a kimondott szavak átirata időbélyeggel. Több nyelv is támogatott.
* **Beszélő indexelése**: a hangszórók és a hozzájuk tartozó szóbeli szavak leképezése.
* **Beszéd hangulat elemzése**: az érzelmek elemzésének kimenete a hang átírásakor történik.
* **Kulcsszavak**: a hangátiratból kinyert kulcsszavak.

### <a name="audioanalyzerpreset-basic-mode"></a>AudioAnalyzerPreset alapszintű mód

A beállításkészlet lehetővé teszi, hogy egy hang-vagy videofájl használatával több hangelemzést is kinyerjen.

A kimenet tartalmaz egy JSON-fájlt és egy VTT-fájlt a hangátirathoz. Ez a beállításkészlet egy olyan tulajdonságot fogad el, amely a bemeneti fájl nyelvét adja meg [BCP47](https://tools.ietf.org/html/bcp47) karakterlánc formájában. A kimenet a következőket tartalmazza:

* **Hang átirata**: a kimondott szavak átirata időbélyeggel. Több nyelv is támogatott, de a rendszer nem tartalmazza az automatikus nyelvfelismerés és a hangszórók diarization.
* **Kulcsszavak**: a hangátiratból kinyert kulcsszavak.

### <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

A beállításkészlet lehetővé teszi több hang-és videó-elemzés kinyerését egy videofájl használatával. A kimenet tartalmaz egy JSON-fájlt (az összes bepillantással), egy VTT-fájlt a videó átiratához, valamint egy miniatűr-gyűjteményt. Ez a beállításkészlet egy [BCP47](https://tools.ietf.org/html/bcp47) -karakterláncot is elfogad (amely a videó nyelvét jelöli) tulajdonságként. A videós elemzések tartalmazzák a fent említett összes hangelemzést, valamint a következő további elemeket:

* **Arcfelismerés**: az az idő, amely alatt az arcok jelen vannak a videóban. Minden arc rendelkezik egy arc-AZONOSÍTÓval és egy hozzá tartozó bélyegképek gyűjteményével.
* **Vizualizáció szövege**: az optikai karakterfelismerésen keresztül észlelt szöveg. A szöveg időbélyegzővel van elválasztva, és a kulcsszavak kinyeréséhez is használatos (a hangátiraton kívül).
* **Kulcsképek**: a videóból kinyert kulcsképek gyűjteménye.
* **Vizuális tartalmak moderálása**: a videók azon része, amelyet felnőttként vagy zamatként megjelöltek a természetben.
* **Megjegyzés**: a videók előre definiált objektummodell alapján való megjegyzésének eredménye

## <a name="insightsjson-elements"></a>Elemek insights.js

A kimenet tartalmaz egy JSON-fájlt (insights.json) a videóban vagy hangban található összes elemzéssel. A JSON a következő elemeket tartalmazhatja:

### <a name="transcript"></a>átirat

|Név|Leírás|
|---|---|
|id|A sor azonosítója.|
|szöveg|Maga a átirat.|
|language|A átirat nyelve. Az olyan átiratok támogatását célozza, ahol az egyes sorok más nyelven is rendelkezhetnek.|
|esetben|Az időtartományok listája, ahol ez a sor megjelent. Ha a példány átirat, akkor csak 1 példánya lesz.|

Példa:

```json
"transcript": [
{
    "id": 0,
    "text": "Hi I'm Doug from office.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    }
    ]
},
{
    "id": 1,
    "text": "I have a guest. It's Michelle.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:02.7200000",
        "end": "00:00:03.9600000"
    }
    ]
}
] 
```

### <a name="ocr"></a>OCR

|Név|Leírás|
|---|---|
|id|Az OCR-sor azonosítója.|
|szöveg|Az OCR szövege.|
|megbízhatóság|Az elismerés megbízhatósága.|
|language|Az OCR nyelve.|
|esetben|Azon időtartományok listája, amelyekben ez az OCR megjelent (ugyanaz az OCR többször is megjelenhet).|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 0.91,
      "language": "en-US",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    },
    {
      "id": 1,
      "text": "NOTICIAS EN VIVO",
      "confidence": 0.9,
      "language": "es-ES",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:28"
        },
        {
          "start": "00:00:32",
          "end": "00:00:38"
        }
      ]
    }
  ],
```

### <a name="faces"></a>arcok

|Név|Leírás|
|---|---|
|id|A Face azonosító.|
|name|Az arc neve. Ez lehet "ismeretlen #0", egy azonosított híresség vagy egy felhasználó által betanított személy.|
|megbízhatóság|Az arc azonosításának megbízhatósága.|
|leírás|A híresség leírása. |
|thumbnailId|Az adott arc miniatűrje.|
|knownPersonId|A belső azonosító (ha ismert személy).|
|referenceId|A Bing-azonosító (ha egy Bing híresség).|
|referenceType|Jelenleg csak Bing.|
|cím|A cím (ha ez egy híresség – például "a Microsoft VEZÉRIGAZGATÓJA").|
|imageUrl|A képurl-cím, ha a híresség.|
|esetben|Azok a példányok, amelyeken az arc szerepelt a megadott időtartományban. Minden példányhoz tartozik egy thumbnailsId is. |

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

### <a name="shots"></a>lövések

|Név|Leírás|
|---|---|
|id|A shot azonosítója.|
|Kulcsképek|A shot keretén belüli kulcstárolók listája (mindegyik rendelkezik egy AZONOSÍTÓval és egy példányok időtartományával). A kulcstároló-példányok egy thumbnailId-mezővel rendelkeznek, amely a kulcs miniatűrjét AZONOSÍTÓval rendelkezik.|
|esetben|A lövés időtartományának listája (a felvételek csak 1 példányt tartalmazhatnak).|

```json
"Shots": [
    {
      "id": 0,
      "keyFrames": [
        {
          "id": 0,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",
              "start": "00: 00: 00.1670000",
              "end": "00: 00: 00.2000000"
            }
          ]
        }
      ],
      "instances": [
        {
            "thumbnailId": "00000000-0000-0000-0000-000000000000",  
          "start": "00: 00: 00.2000000",
          "end": "00: 00: 05.0330000"
        }
      ]
    },
    {
      "id": 1,
      "keyFrames": [
        {
          "id": 1,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",      
              "start": "00: 00: 05.2670000",
              "end": "00: 00: 05.3000000"
            }
          ]
        }
      ],
      "instances": [
        {
          "thumbnailId": "00000000-0000-0000-0000-000000000000",
          "start": "00: 00: 05.2670000",
          "end": "00: 00: 10.3000000"
        }
      ]
    }
  ]
```

### <a name="statistics"></a>statisztikák

|Név|Leírás|
|---|---|
|CorrespondenceCount|A videóban található Levelezések száma.|
|WordCount|A beszélő szavak száma.|
|SpeakerNumberOfFragments|A szónak a videóban található töredékek mennyisége.|
|SpeakerLongestMonolog|A beszélő leghosszabb monologot. Ha a beszélő a monologot belül csendben van. A rendszer eltávolítja a monologot elején és végén található csendet.|
|SpeakerTalkToListenRatio|A számítás a beszélő monologot töltött idő (a között a csend nélkül) alapján történik, a videó teljes időpontjára bontva. Az idő a harmadik tizedes pontra van kerekítve.|


### <a name="sentiments"></a>érzelmeket

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

### <a name="labels"></a>Címkék

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

### <a name="keywords"></a>kulcsszavak

|Név|Leírás|
|---|---|
|id|A kulcsszó azonosítója.|
|szöveg|A kulcsszó szövege.|
|megbízhatóság|A kulcsszó felismerési megbízhatósága.|
|language|A kulcsszó nyelve (fordításkor).|
|esetben|Azon időtartományok listája, amelyekben ez a kulcsszó megjelent (a kulcsszó többször is megjelenhet).|

```json
"keywords": [
{
    "id": 0,
    "text": "office",
    "confidence": 1.6666666666666667,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    },
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    }
    ]
},
{
    "id": 1,
    "text": "icons",
    "confidence": 1.4,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    },
    {
        "start": "00:00:13.9900000",
        "end": "00:00:15.6100000"
    }
    ]
}
] 
```

#### <a name="visualcontentmoderation"></a>visualContentModeration

A visualContentModeration blokk olyan időtartományokat tartalmaz, amelyeknek a Video Indexer valószínűleg felnőtt tartalommal rendelkeznek. Ha a visualContentModeration üres, nincs azonosított felnőtt tartalom.

A felnőtt vagy zamatos tartalmat tartalmazó videók csak privát nézethez érhetők el. A felhasználók a tartalom emberi felülvizsgálatára vonatkozó kérelmet küldhetnek, amely esetben az attribútum az `IsAdult` emberi felülvizsgálat eredményét fogja tartalmazni.

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
## <a name="next-steps"></a>Következő lépések

[Oktatóanyag: Videók elemzése az Azure Media Services segítségével](analyze-videos-tutorial-with-api.md)

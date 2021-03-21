---
title: Kulcs kifejezésének kinyerése a Text Analytics használatával REST API
titleSuffix: Azure Cognitive Services
description: A legfontosabb kifejezések kinyerése az Azure Cognitive Services Text Analytics REST API használatával.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 12/17/2020
ms.author: aahi
ms.openlocfilehash: 91e10c25d2c3bef9c1ca20e3e5737a326d45997c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97654778"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>Példa: a fő kifejezések kinyerése Text Analytics használatával

A [Key Phrase Extraction API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) strukturálatlan szöveget értékel ki és minden JSON-dokumentumra visszaadja a kulcsszavak listáját.

Ez a funkció akkor hasznos, ha szeretné gyorsan azonosítani a dokumentum gyűjtemény fő témáit. Például „Az étel finom, a személyzet nagyszerű volt” bemeneti szövegre a szolgáltatás visszaadja a fő kulcsszavakat: „étel” és „nagyszerű személyzet”.

További információt a [támogatott nyelvek](../language-support.md) ismertetésében talál.

> [!TIP]
> * A Text Analytics egy Linux-alapú Docker-tároló rendszerképet is biztosít a fő kifejezés kinyeréséhez, így az adatokhoz [az Text Analytics tárolót is telepítheti és futtathatja](text-analytics-how-to-install-containers.md) .
> * Ezt a funkciót a végpont használatával [aszinkron módon](text-analytics-how-to-call-api.md) is használhatja `/analyze` .

## <a name="preparation"></a>Előkészítés

A Key kifejezés kinyerése a legjobban akkor működik, ha nagyobb mennyiségű szöveget ad meg a munkához. Ez az érzelmek elemzésével ellentétes, ami jobb a kisebb mennyiségű szövegnél. Annak érdekében, hogy mindkét művelet a legjobb eredményt hozza, érdemes lehet ennek megfelelően átszervezni a bemeneteket.

A JSON-dokumentumoknak ebben a formátumban kell szerepelniük: azonosító, szöveg, nyelv

A dokumentum méretének 5 120 vagy kevesebb karakterből kell állnia, és egy gyűjteményben legfeljebb 1 000 elem (azonosító) adható meg. A kollekció elküldése a kérelem törzsében történik. A következő példa egy kulcsszókeresésre beküldhető szöveget mutat be. 

További információ a kérések és válaszok objektumairól: [a Text Analytics API meghívása](text-analytics-how-to-call-api.md) .  

### <a name="example-synchronous-request-object"></a>Példa szinkron kérési objektumra


```json
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```

### <a name="example-asynchronous-request-object"></a>Példa aszinkron kérelem objektumra

A `v3.1-preview.3` verziótól kezdve a végpont használatával aszinkron módon küldhet adatfogadási kérelmeket `/analyze` .


```json
{
    "displayName": "My Job",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1",
                "text": "It's incredibly sunny outside! I'm so happy"
            },
            {
                "id": "doc2",
                "text": "Pike place market is my favorite Seattle attraction."
            }
        ]
    },
    "tasks": {
        "keyPhraseExtractionTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }],
    }
}
```

## <a name="step-1-structure-the-request"></a>1. lépés: A kérés felépítése

További információ a kérelem meghatározásáról: [a Text Analytics API meghívása](text-analytics-how-to-call-api.md). A következő pontokat a kényelem kedvéért itt megismételjük:

+ Hozzon létre egy **POST** kérést. Tekintse át a kérelem API-dokumentációját: [Key kifejezések API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases).

+ Állítsa be a HTTP-végpontot a kulcsfontosságú kifejezés kinyeréséhez az Azure-ban vagy egy példányban [text Analytics tárolóban](text-analytics-how-to-install-containers.md)Text Analytics erőforrás használatával. Ha szinkron módon használja az API-t, akkor `/text/analytics/v3.0/keyPhrases` az URL-címet is meg kell adnia. Példa: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`.

+ Állítsa be a kérelem fejlécét, hogy tartalmazza a Text Analytics műveletekhez tartozó [hozzáférési kulcsot](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) .

+ A kérelem törzsében adja meg az elemzéshez előkészített JSON-dokumentum kollekciót.

> [!Tip]
> Használható a [Postman](text-analytics-how-to-call-api.md) vagy nyissa meg az **API teszt konzolt** a [dokumentációban](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) a kérés felépítéséhez és a szolgáltatásnak történő POST elküldéséhez.

## <a name="step-2-post-the-request"></a>2. lépés: A kérés elküldése

Az elemzés a kérelem megkapásakor történik meg. A percenként vagy másodpercenként küldendő kérelmek méretével és számával kapcsolatos információkért tekintse meg az áttekintés című témakör [adatkorlátok](../overview.md#data-limits) című szakaszát.

Ne felejtse, hogy a szolgáltatás állapot nélküli. A fiókban nem tárol semmilyen adatot. Az eredményeket azonnal visszaadja a válaszban.

## <a name="step-3-view-results"></a>3. lépés: Eredmények megtekintése

Minden POST kérés egy JSON formátumú választ ad vissza az azonosítókkal és az észlelt tulajdonságokkal. A visszaadott kulcsokra vonatkozó kifejezések sorrendjét a modell határozza meg belsőleg.

A kimenetet visszaadása azonnali. Az eredmények adatfolyamát JSON elfogadó alkalmazáshoz küldheti vagy a kimenetet elmentheti fájlba a helyi rendszeren, majd importálható az adatokat rendezni, keresni és kezelni képes alkalmazásba.

Itt látható egy példa a fő kifejezés kinyerésének eredményére a v 3.1-es verzióról. 2 végpont a következő:

### <a name="synchronous-result"></a>Szinkron eredmény

```json
    {
       "documents":[
          {
             "id":"1",
             "keyPhrases":[
                "year",
                "trail",
                "trip",
                "views",
                "hike"
             ],
             "warnings":[]
          },
          {
             "id":"2",
             "keyPhrases":[
                "marked trails",
                "Worst hike",
                "goners"
             ],
             "warnings":[]
          },
          {
             "id":"3",
             "keyPhrases":[
                "trail",
                "small children",
                "family"
             ],
             "warnings":[]
          },
          {
             "id":"4",
             "keyPhrases":[
                "spectacular views",
                "trail",
                "Worth",
                "area"
             ],
             "warnings":[]
          },
          {
             "id":"5",
             "keyPhrases":[
                "places",
                "beautiful views",
                "favorite trail",
                "rest"
             ],
             "warnings":[]
          }
       ],
       "errors":[],
       "modelVersion":"2020-07-01"
    }

```
Ahogy azt a feljegyezte, az elemző megkeresi és elveti a nem alapvető szavakat, és megtartja a mondat tárgyát vagy tárgyát képező egyszeres kifejezéseket vagy kifejezéseket.

### <a name="asynchronous-result"></a>Aszinkron eredmény

Ha az `/analyze` aszinkron művelethez a végpontot használja, az API-nak küldött feladatokat tartalmazó választ fog kapni.

```json
{
  "displayName": "My Analyze Job",
  "jobId": "dbec96a8-ea22-4ad1-8c99-280b211eb59e_637408224000000000",
  "lastUpdateDateTime": "2020-11-13T04:01:14Z",
  "createdDateTime": "2020-11-13T04:01:13Z",
  "expirationDateTime": "2020-11-14T04:01:13Z",
  "status": "running",
  "errors": [],
  "tasks": {
      "details": {
          "name": "My Analyze Job",
          "lastUpdateDateTime": "2020-11-13T04:01:14Z"
      },
      "completed": 1,
      "failed": 0,
      "inProgress": 2,
      "total": 3,
      "keyPhraseExtractionTasks": [
          {
              "name": "My Analyze Job",
              "lastUpdateDateTime": "2020-11-13T04:01:14.3763516Z",
              "results": {
                  "inTerminalState": true,
                  "documents": [
                      {
                          "id": "doc1",
                          "keyPhrases": [
                              "sunny outside"
                          ],
                          "warnings": []
                      },
                      {
                          "id": "doc2",
                          "keyPhrases": [
                              "favorite Seattle attraction",
                              "Pike place market"
                          ],
                          "warnings": []
                      }
                  ],
                  "errors": [],
                  "modelVersion": "2020-07-01"
              }
          }
      ]
  }
}
```


## <a name="summary"></a>Összefoglalás

Ebben a cikkben megtanulta a kulcsfontosságú kifejezések kinyeréséhez szükséges fogalmakat és munkafolyamatokat a Cognitive Services Text Analytics használatával. Összegezve:

+ A [Kulcsszókeresés API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) csak egyes nyelvekre érhető el.
+ A kérelem törzsében található JSON-dokumentumok közé tartozik az azonosító, a szöveg és a nyelvi kód.
+ A POST kérelem egy `/keyphrases` vagy `/analyze` végpontra vonatkozik, amely egy személyre szabott [hozzáférési kulcsot és egy](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) , az előfizetéséhez érvényes végpontot használ.
+ A válasz kimenete, amely az egyes dokumentumok AZONOSÍTÓinak legfontosabb szavaiból és kifejezésből áll, továbbítható bármely olyan alkalmazásnak, amely elfogadja a JSON-t, beleértve a Microsoft Office Excel és Power BI is, hogy csak néhányat említsünk.

## <a name="see-also"></a>Lásd még

 [Text Analytics áttekintése –](../overview.md) [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)</br>
 [Text Analytics termékoldala](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Következő lépések

* [A Text Analytics áttekintése](../overview.md)
* [Az Text Analytics ügyféloldali kódtár használata](../quickstarts/client-libraries-rest-api.md)
* [Újdonságok](../whats-new.md)

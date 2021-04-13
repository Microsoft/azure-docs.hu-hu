---
title: Frissítés a Computer Vision API Read 3.0-s Computer Vision verzióra
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan frissítheti a Computer Vision Read API 3.0-s verzióra a 2.0-s/2.1-es verzióról.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 08/11/2020
ms.author: pafarley
ROBOTS: NOINDEX
ms.openlocfilehash: 7a05b04872b4f957e879d93972edc45e2932d059
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364090"
---
# <a name="upgrade-from-read-v2x-to-read-v3x"></a>Frissítés Olvasás v2.x-ről Read v3.x-re

Ez az útmutató bemutatja, hogyan frissítheti meglévő tároló- vagy felhőalapú API-kódját Read v2.x-ről Read v3.x-re.

## <a name="determine-your-api-path"></a>Az API elérési útjának meghatározása
Az alábbi táblázat segítségével  meghatározhatja az API elérési útjának verziósringét a Read 3.x verzió alapján, amelybe az áttelepítést szeretné.

|Terméktípus| Verzió | Verziósring a 3.x API-útvonalon |
|:-----|:----|:----|
|Szolgáltatás | Olvasás 3.0 vagy 3.1 | **v3.0** vagy **v3.1** |
|Szolgáltatás | 3.2-es előzetes verzió olvasása | **v3.2-preview.1** |
|Tároló | Read 3.0 preview or Read 3.1 preview | **v3.0** vagy **v3.1-preview.2** |


Ezután a következő szakaszok segítségével leszűkítheti  a műveleteket, és lecserélheti az API elérési útjának verziósringjét a táblában található értékre. A Felhő és **tárolók 3.2-es** előzetes verziójának olvasása esetén például frissítse az API elérési útját **a következőre: https://{endpoint}/vision/v3.2-preview.1/read/analyze[?language]**.

## <a name="servicecontainer"></a>Szolgáltatás/tároló

### `Batch Read File`

|Olvasás 2.x |Olvasás 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/read/core/asyncBatchAnalyze**     |https://{endpoint}/vision/<**verziósring**>/read/analyze[?language]|
    
Elérhető egy új _választható nyelvi_ paraméter. Ha nem ismeri a dokumentum nyelvét, vagy többnyelvű lehet, ne foglalja bele. 

### `Get Read Results`

|Olvasás 2.x |3.x olvasása  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/read/operations**/{operationId}     |https://{endpoint}/vision/<**verziósring**>/read/analyzeResults/{operationId}|

### <a name="get-read-operation-result-status-flag"></a>`Get Read Operation Result` állapotjelző

Ha a hívása sikeres, egy állapotsring mezőt `Get Read Operation Result` ad vissza a JSON-törzsben.
 
|Olvasás 2.x |3.x olvasása  |
|----------|-----------|
|`"NotStarted"` |    `"notStarted"`|
|`"Running"` | `"running"`|
|`"Failed"` | `"failed"`|
|`"Succeeded"` | `"succeeded"`|
    
### <a name="api-response-json"></a>API-válasz (JSON) 

Figyelje meg a json következő módosításait:
* A v2.x-ben a `Get Read Operation Result` az OCR-felismerési JSON-t adja vissza, ha az állapot `Succeeded"` . A 3.0-s v-ben ez a mező `succeeded` a következő: .
* Az oldaltömb gyökerének lekért értékhez módosítsa a json-hierarchiát értékről `recognitionResults` a következőre: `analyzeResult` / `readResults` . Az oldalonkénti sor és a szavak json-hierarchiája változatlan marad, ezért nem kell módosítani a kódot.
* Az oldal szöge új nevet ad, a tartomány pedig `clockwiseOrientation` `angle` 0–360 fokról -180 fokra módosult. A kódtól függően előfordulhat, hogy módosításokat kell eszközlnie, mivel a legtöbb matematikai függvény mindkét tartományt képes kezelni.

A 3.0-s verziós API a következő fejlesztéseket is bevezeti, amelyek választhatóan használhatók:
* `createdDateTime` A `lastUpdatedDateTime` és a hozzá van adva, így nyomon követheti a feldolgozás időtartamát. További részletekért tekintse meg a dokumentációt. 
* `version` az eredmények létrehozásához használt API verzióját jelzi
* Hozzáadtunk egy `confidence` szóra jutó szót. Ez az érték úgy van beállítva, hogy a 0,95 érték azt jelenti, hogy a felismerés 95%osan helyes. A megbízhatósági pontszámmal kiválaszthatja, hogy melyik szöveget küldje el az emberi felülvizsgálatra. 
    
    
A 2.X-ben a kimeneti formátum a következő: 
    
```json
    {
        {
                "status": "Succeeded",
                "recognitionResults": [
                    {
                    "page": 1,
                    "language": "en",
                    "clockwiseOrientation": 349.59,
                    "width": 2661,
                    "height": 1901,
                    "unit": "pixel",
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
}
```
    
A 3.0-s v-ben ezt módosította:
    
```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "language": "en",
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
```

## <a name="service-only"></a>Csak szolgáltatás

### `Recognize Text`
`Recognize Text`A egy *előzetes* verziójú művelet, amely az API összes *verziójában Computer Vision elavult.* A-ről a `Recognize Text` `Read` (3.0-s) vagy `Batch Read File` (2.0-s, 2.1-es vagy 2.1-es) gépekre kell mirateálnia. A 3.0-s újabb, jobb szövegfelismerési modelleket és további funkciókat tartalmaz, ezért `Read` ajánlott. A verzióról a `Recognize Text` verzióra való `Read` frissítéshez:

|szövegfelismerés 2.x |Olvasás 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/recognizeText[?mode]**|https://{endpoint}/vision/</read/analyze[?language]>verziós sztringje |
    
A _mode_ paraméter nem támogatott a `Read` -ban. A kézzel írt és a nyomtatott szöveg is automatikusan támogatott lesz.
    
A 3.0-s v-ben elérhető egy új választható nyelvi paraméter.  Ha nem ismeri a dokumentum nyelvét, vagy esetleg többnyelvű, ne foglalja bele. 

### `Get Recognize Text Operation Result`

|szövegfelismerés 2.x |Olvasás 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/textOperations/**{operationId}|https://{endpoint}/vision/<verziósring **>** /read/analyzeResults/{operationId}|

### <a name="get-recognize-text-operation-result-status-flags"></a>`Get Recognize Text Operation Result` állapotjelzők
Ha a hívása sikeres, egy állapotsring mezőt `Get Recognize Text Operation Result` ad vissza a JSON-törzsben. 
 
|szövegfelismerés 2.x |3.x olvasása  |
|----------|-----------|
|`"NotStarted"` |    `"notStarted"`|
|`"Running"` | `"running"`|
|`"Failed"` | `"failed"`|
|`"Succeeded"` | `"succeeded"`|

### <a name="api-response-json"></a>API-válasz (JSON)

Figyelje meg a json következő módosításait:    
* A v2.x-ben a `Get Read Operation Result` az OCR-felismerési JSON-t adja vissza, ha az állapot `Succeeded` . A v3.x-hez ez a mező a `succeeded` következő: .
* Az oldaltömb gyökerének lekért értékhez módosítsa a json-hierarchiát értékről `recognitionResult` a következőre: `analyzeResult` / `readResults` . Az oldalonkénti sor és a szavak json-hierarchiája változatlan marad, ezért nem kell módosítani a kódot.

A 3.0-s verziós API a következő fejlesztéseket is bevezeti, amelyek opcionálisan használhatók. További részletekért tekintse meg az API-referenciát:
* `createdDateTime` A `lastUpdatedDateTime` és a hozzá van adva, így nyomon követheti a feldolgozás időtartamát. További részletekért tekintse meg a dokumentációt. 
* `version` az eredmények létrehozásához használt API verzióját jelzi
* Szónkénti `confidence` érték lett hozzáadva. Ez az érték úgy van beállítva, hogy a 0,95 érték azt jelenti, hogy a felismerés helyes 95%. A megbízhatósági pontszámmal kiválaszthatja, hogy melyik szöveget küldje el az emberi felülvizsgálatra. 
* `angle` a szöveg általános tájolása az óramutató járásával megegyező irányban, fokban mérve a következő között: (-180, 180].
* `width` és megadja a dokumentum dimenzióit, és megadja ezeknek a dimenzióknak az egységét (képpontok vagy hüvelyk, a dokumentum `"height"` `"unit"` típusától függően).)
* `page` A többoldalas dokumentumok támogatottak
* `language`a dokumentum bemeneti nyelve (a választható _nyelvi paraméterből).)_


A 2.X-ben a kimeneti formátum a következő: 
    
```json
    {
        {
                "status": "Succeeded",
                "recognitionResult": [
                    {
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
    }
```
    
A v3.x-et módosította:
    
```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
```

## <a name="container-only"></a>Csak tároló

### `Synchronous Read`

|Olvasás 2.0 |Olvasás 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/read/core/Analyze**     |https://{endpoint}/vision/<**verziósring**>/read/syncAnalyze[?language]|

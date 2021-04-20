---
title: Szövegelemzési API hívása
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan hívhatja meg a Azure Cognitive Services Text Analytics REST API Postmant.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: 5790c7c62b9d97df9683773170301b6e09a47667
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728481"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>A Text Analytics REST API

Ebben a cikkben a Text Analytics REST API és [a Postman](https://www.postman.com/downloads/) használatával mutatjuk be a legfontosabb fogalmakat. Az API számos szinkron és aszinkron végpontot biztosít a szolgáltatás funkcióinak felhasználásával. 

## <a name="create-a-text-analytics-resource"></a>Erőforrás Text Analytics létrehozása

> [!NOTE]
> * A vagy a Text Analytics standard [(S)](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) tarifacsomagot használó erőforrásra lesz `/analyze` `/health` szüksége. A `/analyze` végpont a tarifacsomag [része.](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)

A Text Analytics API használata előtt létre kell hoznia egy Azure-erőforrást egy kulccsal és végponttal az alkalmazásai számára. 

1.  Először is, a Azure Portal [hozzon](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) létre egy új Text Analytics erőforrást, ha még nem rendelkezik ilyen erőforrással. Válasszon [tarifacsomagot.](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)

2.  Válassza ki a végponthoz használni kívánt régiót.  Vegye figyelembe, hogy a és a végpont csak a következő régiókban érhető `/analyze` `/health` el: USA 2. nyugati régiója, USA 2. keleti régiója, USA középső régiója, Észak-Európa és Nyugat-Európa.

3.  Hozza létre Text Analytics erőforrást, és a lap bal oldalán található "kulcsok és végpont panel" gombra. Másolja ki a kulcsot, hogy később, az API-k hívatáskor használni tudja. Ezt később fogja hozzáadni a fejléc `Ocp-Apim-Subscription-Key` értékeként.

4. A saját erőforrásával elküldött szöveges rekordok számának Text Analytics ellenőrzéshez:

    1. Lépjen a Text Analytics erőforráshoz a Azure Portal. 
    2. Kattintson **a Bal oldali navigációs** menü **Figyelés** területén található Metrikák elemre. 
    3. A *Metrika legördülő* listában válassza a Feldolgozott szöveges **rekordok lehetőséget.**
    
A szöveges rekord 1000 karakterből áll.

## <a name="change-your-pricing-tier"></a>Tarifacsomag módosítása 

Ha már van S0 Text Analytics S4 tarifacsomagot használó meglévő erőforrása, frissítse a Standard (S) [tarifacsomag használatára.](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) Az S0–S4 tarifacsomagok ki lesznekvezetve. Az erőforrás díjszabásának frissítése:

1. Keresse meg a Text Analytics erőforrást a [Azure Portal.](https://portal.azure.com/)
2. A **bal oldali navigációs menüben** válassza a Tarifacsomag lehetőséget. Ez az **ERŐFORRÁS-KEZELÉS alatt lesz.** 
3. Válassza a Standard (S) tarifacsomagot. Ezután kattintson a **Kiválasztás** elemre.

A Standard (S) tarifacsomaggal új Text Analytics is létrehozhat, és az alkalmazások áttelepítése az új erőforrás hitelesítő adatainak használatára. 

## <a name="using-the-api-synchronously"></a>Az API szinkron használata

Az alkalmazásokat szinkron Text Analytics hívhatja (kis késésű forgatókönyvek esetén). A szinkron API-k használata esetén mindegyik API-t (funkciót) külön kell hívnia. Ha több funkciót is meg kell hívnia, tekintse meg az alábbi szakaszt a Text Analytics hívását. 

## <a name="using-the-api-asynchronously"></a>Az API aszinkron használata

A 3.1-preview.3-as verziótól kezdődően a Text Analytics API két aszinkron végpontot biztosít: 

* A végpont Text Analytics lehetővé teszi ugyanazon szöveges dokumentumok elemzését több szövegelemzési `/analyze` funkcióval egyetlen API-hívásban. Korábban több funkcióhoz külön API-hívásokat kellett volna kezdeményezni minden művelethez. Ezt a képességet akkor érdemes figyelembe venni, ha nagy méretű dokumentumkészleteket kell elemeznie egynél több Text Analytics funkcióval.

* Az egészségügyi Text Analytics végpontja, amely képes kinyerni és címkézni a klinikai dokumentumokból a releváns egészségügyi `/health` adatokat.  

Vegye figyelembe, hogy az /analyze és /health végpontok csak a következő régiókban érhetők el: USA 2. nyugati régiója, USA 2. keleti régiója, USA középső régiója, Észak-Európa és Nyugat-Európa.

Az alábbi táblázatban láthatja, hogy mely funkciók használhatók aszinkron módon. Vegye figyelembe, hogy a végpontról csak néhány funkció `/analyze` hívható meg. 

| Szolgáltatás | Szinkron | Aszinkron |
|--|--|--|
| Nyelvfelismerés | ✔ |  |
| Hangulatelemzés | ✔ |  |
| Véleménybányászat | ✔ |  |
| Kulcskifejezések kinyerése | ✔ | ✔* |
| nevesített entitások felismerése (beleértve a PII-t és a PHI-t) | ✔ | ✔* |
| Entitás-összekapcsolás | ✔ | ✔* |
| Text Analytics állapota (tároló) | ✔ |  |
| Text Analytics állapota (API) |  | ✔  |

`*` – Aszinkron módon, a végponton `/analyze` keresztül hívva.


[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

<a name="json-schema"></a>

## <a name="api-request-formats"></a>API-kérelemformátumok

Szinkron és aszinkron hívásokat is küldhet a Text Analytics API-nak.

#### <a name="synchronous"></a>[Szinkron](#tab/synchronous)

### <a name="synchronous-requests"></a>Szinkron kérések

Az API-kérések formátuma minden szinkron művelet esetén ugyanaz. A dokumentumokat a JSON-objektumok nyers, strukturálatlan szövegként küldik el. Az XML nem támogatott. A JSON-séma az alább leírt elemekből áll.

| Elem | Érvényes értékek | Kötelező? | Használat |
|---------|--------------|-----------|-------|
|`id` |Az adattípus sztring, a gyakorlatban azonban a dokumentum-adatokat általában egész számként adja meg. | Kötelező | A rendszer az Ön által a kimenet struktúraként használt adatokat használja. A kérésben minden azonosítóhoz létrejönnek nyelvkódok, kulcskifejezések és hangulati pontszámok.|
|`text` | Strukturálatlan nyers szöveg, legfeljebb 5120 karakter hosszú lehet. | Kötelező | A nyelvfelismeréshez a szöveg bármilyen nyelven kifejezható. Hangulatelemzéshez, kulcskifejezések kinyeréséhez és entitásazonosításhoz a szövegnek támogatott nyelven [kell lennie.](../language-support.md) |
|`language` | 2 karakteres [ISO 639-1 kód](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) támogatott [nyelvhez](../language-support.md) | Változó | Hangulatelemzéshez, kulcskifejezések kinyeréséhez és entitás-összekapcsoláshoz szükséges; nyelvfelismeréshez nem kötelező. Ha kizárja, nincs hiba, de az elemzés nélkül is el lesz ásva. A nyelvi kódnak meg kell felelnie az `text` Ön által adott kódnak. |

Az alábbi példa egy API-kérést mutat be a szinkron Text Analytics végpontokra. 

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Sample text to be sent to the text analytics api."
    }
  ]
}
```

#### <a name="asynchronous"></a>[Aszinkron](#tab/asynchronous)

### <a name="asynchronous-requests-to-the-analyze-endpoint"></a>A végpontra vonatkozó aszinkron `/analyze` kérések

> [!NOTE]
> Az ügyféloldali kódtár legújabb előzetes Text Analytics lehetővé teszi az Asynchronous Analyze műveletek hívását egy ügyfélobjektummal. Példákat a GitHubon találhat:
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

A végponttal kiválaszthatja, hogy a támogatott Text Analytics mely funkciókat szeretné `/analyze` használni egyetlen API-hívásban. Ez a végpont jelenleg a következő eket támogatja:

* Kulcskifejezések kinyerése 
* nevesített entitások felismerése (beleértve a PII-t és a PHI-t)
* Entitáskapcsolás

| Elem | Érvényes értékek | Kötelező? | Használat |
|---------|--------------|-----------|-------|
|`displayName` | Sztring | Választható | A feladat egyedi azonosítójának megjelenítendő neveként használatos.|
|`analysisInput` | Tartalmazza az `documents` alábbi mezőt | Kötelező | A küldeni kívánt dokumentumok adatait tartalmazza. |
|`documents` | Az alábbi `id` és `text` mezőket tartalmazza | Kötelező | Minden elküldött dokumentumra és a dokumentum nyers szövegére vonatkozó információkat tartalmaz. |
|`id` | Sztring | Kötelező | A rendszer az Ön által használt adatokat használja a kimenet struktúraként. |
|`text` | Strukturálatlan nyers szöveg, legfeljebb 125 000 karakter hosszú lehet. | Kötelező | Angol nyelvűnek kell lennie, amely jelenleg csak ezt a nyelvet támogatja. |
|`tasks` | A következő Text Analytics tartalmazza: `entityRecognitionTasks` , `entityLinkingTasks` `keyPhraseExtractionTasks` vagy `entityRecognitionPiiTasks` . | Kötelező | Egy vagy több Text Analytics használni kívánt funkciókat. Vegye figyelembe, hogy van egy választható paramétere, amely vagy értékére, valamint a kiválasztott entitástípusok `entityRecognitionPiiTasks` `domain` `pii` `phi` `pii-categories` észlelésére használható. Ha a paraméter nincs meghatározva, a rendszer alapértelmezés szerint `domain` a következőt használja: `pii` . |
|`parameters` | Az alábbi `model-version` és `stringIndexType` mezőket tartalmazza | Kötelező | Ez a mező a fenti választott funkciófeladatok közé tartozik. Információkat tartalmaznak a használni kívánt modellverzióról és az indextípusról. |
|`model-version` | Sztring | Kötelező | Adja meg, hogy a modell melyik verzióját szeretné használni.  |
|`stringIndexType` | Sztring | Kötelező | Adja meg a programozási környezetnek megfelelő szövegdekódert.  A támogatott típusok a `textElement_v8` következőek: (alapértelmezett), `unicodeCodePoint` , `utf16CodeUnit` . További információért tekintse meg [a Szövegeltolások](../concepts/text-offsets.md#offsets-in-api-version-31-preview) cikket.  |
|`domain` | Sztring | Választható | Csak paraméterként vonatkozik a `entityRecognitionPiiTasks` feladatra, és beállítható vagy `pii` `phi` értékre. Ha nincs megadva, alapértelmezés szerint a értéket `pii` használja.  |

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
        "entityRecognitionTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "stringIndexType": "TextElements_v8"
                }
            }
        ],
        "entityLinkingTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "stringIndexType": "TextElements_v8"
                }
            }
        ],
        "keyPhraseExtractionTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }],
        "entityRecognitionPiiTasks": [{
            "parameters": {
                "model-version": "latest",
                "stringIndexType": "TextElements_v8",
                "domain": "phi",
                "pii-categories":"default"
            }
        }]
    }
}

```

### <a name="asynchronous-requests-to-the-health-endpoint"></a>Aszinkron kérések a `/health` végpontra

Az állapot üzemeltetett API Text Analytics API-hoz való API-kérések formátuma megegyezik a tárolójának formátumával. A dokumentumokat a JSON-objektumok nyers, strukturálatlan szövegként küldik el. Az XML nem támogatott. A JSON-séma az alább leírt elemekből áll.  Töltse ki és küldje el a Cognitive Services [kérelem űrlapját,](https://aka.ms/csgate) hogy hozzáférést kérjen a Text Analytics nyilvános előzetes verzióhoz. Az állapothasználatért nem Text Analytics számlát. 

| Elem | Érvényes értékek | Kötelező? | Használat |
|---------|--------------|-----------|-------|
|`id` |Az adattípus sztring, a gyakorlatban azonban a dokumentum-adatokat általában egész számként adja meg. | Kötelező | A rendszer az Ön által a kimenet struktúraként használt adatokat használja. |
|`text` | Strukturálatlan nyers szöveg, legfeljebb 5120 karakter hosszú lehet. | Kötelező | Vegye figyelembe, hogy jelenleg csak az angol nyelvű szöveg támogatott. |
|`language` | 2 karakteres [ISO 639-1 kód](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) támogatott [nyelvhez](../language-support.md) | Kötelező | Jelenleg `en` csak a támogatott. |

Az alábbiakban egy példát mutatunk be az állapotvégpontokat Text Analytics API-kérésre. 

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

---

>[!TIP]
> Az [adatküldés sebességére](../concepts/data-limits.md) és méretére vonatkozó korlátozásokról az Adat- és sebességkorlátok a Text Analytics API-nak.


## <a name="set-up-a-request"></a>Kérés beállítása 

A Postmanben (vagy egy másik webes API-teszteszközben) adja hozzá a használni kívánt funkció végpontját. Az alábbi táblázat segítségével keresse meg a megfelelő végpontformátumot, és cserélje le a helyére `<your-text-analytics-resource>` az erőforrás-végpontot. Például:

`https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0/languages`

#### <a name="synchronous"></a>[Szinkron](#tab/synchronous)

### <a name="endpoints-for-sending-synchronous-requests"></a>Szinkron kérések küldésének végpontjai

| Szolgáltatás | Kérelemtípus | Erőforrás-végpontok |
|--|--|--|
| Nyelvfelismerés | POST | `<your-text-analytics-resource>/text/analytics/v3.0/languages` |
| Hangulatelemzés | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment` |
| Véleménybányászat | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment?opinionMining=true` |
| Kulcskifejezések kinyerése | POST | `<your-text-analytics-resource>/text/analytics/v3.0/keyPhrases` |
| Elnevezett entitások felismerése – általános | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/general` |
| Nevestű entitások felismerése – SZEMÉLYI- | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii` |
| Megnevezett entitások felismerése – PHI | POST |  `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii?domain=phi` |

#### <a name="asynchronous"></a>[Aszinkron](#tab/asynchronous)

### <a name="endpoints-for-sending-asynchronous-requests-to-the-analyze-endpoint"></a>Aszinkron kérések végpontok küldése a `/analyze` végpontnak

| Szolgáltatás | Kérelemtípus | Erőforrás-végpontok |
|--|--|--|
| Elemzési feladat elküldése | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/analyze` |
| Az elemzés állapotának és eredményeinek lekérte | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/analyze/jobs/<Operation-Location>` |

### <a name="endpoints-for-sending-asynchronous-requests-to-the-health-endpoint"></a>Végpontok aszinkron kérések végpontra való `/health` küldését

| Szolgáltatás | Kérelemtípus | Erőforrás-végpontok |
|--|--|--|
| Állapot Text Analytics feladatra vonatkozó kérelem elküldése  | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/entities/health/jobs` |
| Feladat állapotának és eredményeinek leése | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/entities/health/jobs/<Operation-Location>` |
| Feladat megszakítása | DELETE | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/entities/health/jobs/<Operation-Location>` |

--- 

Miután megvan a végpont, a Postmanben (vagy egy másik webes API-teszteszközben):

1. Válassza ki a használni kívánt funkció kéréstípusát.
2. Illessze be a megfelelő művelet végpontját a fenti táblázatból.
3. Állítsa be a három kérelemfejlécet:

   + `Ocp-Apim-Subscription-Key`: a hozzáférési kulcs, amely a Azure Portal
   + `Content-Type`: application/json
   + `Accept`: application/json

    Ha Postmant használ, a kérelemnek az alábbi képernyőképhez hasonlóan kell kinéznie `/keyPhrases` végpontot feltételezve.
    
    ![Képernyőkép kérése végponttal és fejlécekkel](../media/postman-request-keyphrase-1.png)
    
4. Válassza **a nyers** lehetőséget a törzs **formátumaként**
    
    ![Képernyőkép kérése a törzs beállításaival](../media/postman-request-body-raw.png)

5. Illesszen be néhány JSON-dokumentumot érvényes formátumban. Használja a fenti  API-kérelemformátumban található példákat, további információért és példákért tekintse meg az alábbi témaköröket:

      + [Nyelvfelismerés](text-analytics-how-to-language-detection.md)
      + [Kulcskifejezések kinyerése](text-analytics-how-to-keyword-extraction.md)
      + [Hangulatelemzés](text-analytics-how-to-sentiment-analysis.md)
      + [Entitások felismerése](text-analytics-how-to-entity-linking.md)

## <a name="send-the-request"></a>A kérés küldése

Küldje el az API-kérelmet. Ha a hívást szinkron végpontra tette, a válasz azonnal megjelenik egyetlen JSON-dokumentumként, a kérésben megadott minden dokumentumazonosítóhoz egy-egy elemet.

Ha az aszinkron vagy végpontokra hívja meg a `/analyze` `/health` hívást, ellenőrizze, hogy kapott-e 202-es válaszkódot. az eredmények megtekintéséhez a választ kell kapnia:

1. Az API-válaszban keresse meg a kódot a fejlécben, amely azonosítja `Operation-Location` az API-nak elküldött feladatot. 
2. Hozzon létre egy GET kérést a használt végponthoz. tekintse meg [a fenti táblázatban](#set-up-a-request) a végpont formátumát, és tekintse át az [API-referenciadokumentációt.](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/AnalyzeStatus) Például:

    `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/analyze/jobs/<Operation-Location>`

3. Adja hozzá `Operation-Location` a et a kérelemhez.

4. A válasz egyetlen JSON-dokumentum lesz, a kérésben megadott minden dokumentumazonosítóhoz egy-egy elem.

Vegye figyelembe, hogy az aszinkron és a műveletek esetén is a 2. lépésben található GET kérés eredményei a feladat létrehozása után `/analyze` `/health` 24 óráig érhetők el.  Ezt az időt a `expirationDateTime` GET-válaszban megadott érték jelzi.  Ezt az időszakot követően a rendszer kiüríti az eredményeket, és már nem érhetők el lekérésre.    

## <a name="example-api-responses"></a>API-válaszok példái
 
# <a name="synchronous"></a>[Szinkron](#tab/synchronous)

### <a name="example-responses-for-synchronous-operation"></a>Példaválaszok szinkron művelethez

A szinkron végpontválaszok a használt végponttól függően változnak. Válaszokért tekintse meg a következő cikkeket.

+ [Nyelvfelismerés](text-analytics-how-to-language-detection.md#step-3-view-the-results)
+ [Kulcskifejezések kinyerése](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Hangulatelemzés](text-analytics-how-to-sentiment-analysis.md#view-the-results)
+ [Entitások felismerése](text-analytics-how-to-entity-linking.md#view-results)

# <a name="asynchronous"></a>[Aszinkron](#tab/asynchronous)

### <a name="example-responses-for-asynchronous-operations"></a>Példaválaszok aszinkron műveletekhez

Ha a művelet sikeres, a végpontra vonatkozó GET-kérés egy objektumot ad vissza, `/analyze` amely a hozzárendelt feladatokat tartalmazza. Például: `keyPhraseExtractionTasks`. Ezek a feladatok a megfelelő szolgáltatás válaszobjektumát Text Analytics tartalmazzák. További információt az alábbi cikkekben talál.

+ [Kulcskifejezések kinyerése](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Entitásfelismerés](text-analytics-how-to-entity-linking.md#view-results)
+ [Egészségügyi Text Analytics](text-analytics-for-health.md#hosted-asynchronous-web-api-response)

--- 

## <a name="see-also"></a>Lásd még

* [A Text Analytics áttekintése](../overview.md)
* [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)</br>
* [Text Analytics termékoldala](//go.microsoft.com/fwlink/?LinkID=759712)
* [A Text Analytics ügyféloldali kódtár használata](../quickstarts/client-libraries-rest-api.md)
* [Újdonságok](../whats-new.md)

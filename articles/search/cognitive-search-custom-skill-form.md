---
title: Űrlap-felismerő egyéni jártassága (C#)
titleSuffix: Azure Cognitive Search
description: Ismerje meg, hogyan hozhat létre egy űrlap-felismerő egyéni képességet a C# és a Visual Studio használatával.
manager: nitinme
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: a3b073cdb90e0c427bfbca15c1440b9122672610
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880135"
---
# <a name="example-create-a-form-recognizer-custom-skill"></a>Példa: űrlap-felismerő egyéni képességének létrehozása

Ebben az Azure Cognitive Search készségkészlet példaként megtudhatja, hogyan hozhat létre egy űrlap-felismerő egyéni képességet a C# és a Visual Studio használatával. Az űrlap-felismerő elemzi a dokumentumokat, és Kinyeri a kulcs/érték párokat és a táblákat. Ha az űrlap-felismerőt az [egyéni képességek felületére](cognitive-search-custom-skill-interface.md)csomagolja, ezt a képességet a végpontok közötti dúsítási folyamat lépéseként is hozzáadhatja. A folyamat ezután betölti a dokumentumokat, és más átalakításokat is végrehajthat.

## <a name="prerequisites"></a>Előfeltételek

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (bármely kiadás).
- Legalább öt azonos típusú űrlap. A jelen útmutatóban elérhető mintaadatok használhatók.

## <a name="create-a-form-recognizer-resource"></a>Űrlap-felismerő erőforrás létrehozása

[!INCLUDE [create resource](../cognitive-services/form-recognizer/includes/create-resource.md)]

## <a name="train-your-model"></a>A modell betanítása

Ennek a képességnek a használata előtt be kell tanítania egy űrlap-felismerő modellt a bemeneti űrlapjaira. Kövesse a [curl](../cognitive-services/form-recognizer/quickstarts/client-library.md?pivots=programming-language-rest-api) rövid útmutatóját, és ismerkedjen meg a modellek betanításával. Használhatja az ebben a rövid útmutatóban található mintaűrlapok használatát, vagy használhatja a saját adatait is. A modell betanítása után másolja az azonosító értékét egy biztonságos helyre.

## <a name="set-up-the-custom-skill"></a>Az egyéni képesség beállítása

Ez az oktatóanyag a [AnalyzeForm](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Vision/AnalyzeForm) projektet használja az [Azure Search Power Skills](https://github.com/Azure-Samples/azure-search-power-skills) GitHub-tárházban. A tárház klónozása a helyi gépre, és a projekt eléréséhez navigáljon a **jövőkép/AnalyzeForm/** gombra. Ezután nyissa meg a _AnalyzeForm. csproj_ a Visual Studióban. Ez a projekt létrehoz egy Azure Function-erőforrást, amely megfelel az [Egyéni képességi felületnek](cognitive-search-custom-skill-interface.md) , és használható az Azure Cognitive Search-bővítéshez. A dokumentum bemenetként jeleníti meg a dokumentumokat, és a megadott kulcs/érték párokat (szövegként) adja eredményként.

Először adja hozzá a projekt szintű környezeti változókat. Keresse meg a **AnalyzeForm** projektet a bal oldali ablaktáblán, kattintson rá a jobb gombbal, és válassza a **Tulajdonságok** lehetőséget. A **Tulajdonságok** ablakban kattintson a **hibakeresés** lapra, majd keresse meg a **környezeti változók** mezőt. A **Hozzáadás** gombra kattintva adja hozzá a következő változókat:
* `FORMS_RECOGNIZER_ENDPOINT_URL` a végpont URL-címéhez megadott értékkel.
* `FORMS_RECOGNIZER_API_KEY` az előfizetési kulcshoz beállított értékkel.
* `FORMS_RECOGNIZER_MODEL_ID` a betanított modell AZONOSÍTÓjának értékeként megadva.
* `FORMS_RECOGNIZER_RETRY_DELAY` a 1000 értékre van beállítva. Ez az érték azt az időpontot ezredmásodpercben, ameddig a program megvárja a lekérdezés újbóli megkísérlése előtt.
* `FORMS_RECOGNIZER_MAX_ATTEMPTS` a 100 értékre van beállítva. Ez az érték az a szám, ahányszor a program lekérdezi a szolgáltatást, miközben megpróbál sikeres választ kapni.

Ezután nyissa meg a _AnalyzeForm.cs_ , és keresse meg a `fieldMappings` változót, amely a fájl *field-mappings.js* hivatkozik. Ez a fájl (és az azt hivatkozó változó) meghatározza az űrlapokból kinyerni kívánt kulcsok listáját, valamint az egyes kulcsok egyéni címkéjét. Egy érték például `{ "Address:", "address" }, { "Invoice For:", "recipient" }` azt jelenti, hogy a parancsfájl csak az észlelt és a mezők értékeit fogja menteni `Address:` `Invoice For:` , és az értékeket a és a értékre fogja címkézni `"address"` `"recipient"` .

Végezetül jegyezze fel a `contentType` változót. Ez a parancsfájl az URL-cím által hivatkozott távoli dokumentumokon futtatja a megadott űrlap-felismerő modellt, így a tartalomtípus a következő: `application/json` . Ha a helyi fájlokat a HTTP-kérelmekben foglalt bájtok alapján szeretné elemezni, akkor módosítania kell a `contentType` fájlt a megfelelő [MIME-típusra](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Complete_list_of_MIME_types) .

## <a name="test-the-function-from-visual-studio"></a>A függvény tesztelése a Visual studióból

A projekt szerkesztése után mentse azt, és állítsa be a **AnalyzeForm** projektet indítási projektként a Visual Studióban (ha még nincs beállítva). Ezután nyomja le az **F5** billentyűt a függvény futtatásához a helyi környezetben. Használjon REST-szolgáltatást, mint a [Poster](https://www.postman.com/) a függvény meghívásához.

### <a name="http-request"></a>HTTP-kérelem

A függvény meghívásához a következő kérést kell elvégeznie.

```HTTP
POST https://localhost:7071/api/analyze-form
```

### <a name="request-body"></a>A kérés törzse

Kezdje a kérelem szövegtörzs-sablonnal.

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": { 
                "formUrl": "<your-form-url>",
                "formSasToken": "<your-sas-token>"
            }
        }
    ]
}
```

Itt meg kell adnia egy olyan űrlap URL-címét, amelynek a típusa megegyezik a betanított űrlapokkal. Tesztelési célból használhatja a betanítási űrlapok egyikét. Ha követte a cURL gyors üzembe helyezését, az űrlapokat egy Azure Blob Storage-fiókban fogja elhelyezni. Nyissa meg Azure Storage Explorer, keresse meg az űrlapot, kattintson rá a jobb gombbal, majd válassza a **közös hozzáférésű aláírás beolvasása** elemet. A következő párbeszédablak egy URL-címet és egy SAS-tokent fog biztosítani. Adja meg ezeket a karakterláncokat a `"formUrl"` `"formSasToken"` kérelem törzsében és mezőiben.

> [!div class="mx-imgBorder"]
> ![Azure Storage Explorer; egy PDF-dokumentum van kiválasztva](media/cognitive-search-skill-form/form-sas.png)

Ha olyan távoli dokumentumot szeretne elemezni, amely nem az Azure Blob Storage-ban található, illessze be az URL-címet a `"formUrl"` mezőbe, és hagyja `"formSasToken"` üresen a mezőt.

> [!NOTE]
> Ha a képesség integrálva van egy készségkészlet, az URL-címet és a jogkivonatot a Cognitive Search fogja biztosítani.

### <a name="response"></a>Reagálás

Az alábbi példához hasonló válasznak kell megjelennie:

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": {
                "address": "1111 8th st. Bellevue, WA 99501 ",
                "recipient": "Southridge Video 1060 Main St. Atlanta, GA 65024 "
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>A függvény közzététele az Azure-ban

Ha elégedett a funkció működésével, közzéteheti.

1. A Visual Studióban a **megoldáskezelő** kattintson a jobb gombbal a projektre, és válassza a **Közzététel** lehetőséget. Válassza az **új**  >  **Közzététel** létrehozása lehetőséget.
1. Ha még nem csatlakoztatta a Visual studiót az Azure-fiókjához, válassza a **fiók hozzáadása...** lehetőséget.
1. Kövesse a képernyőn megjelenő utasításokat. Adjon egyedi nevet az App Service, az Azure-előfizetés, az erőforráscsoport, a üzemeltetési csomag és a használni kívánt Storage-fiók számára. Létrehozhat egy új erőforráscsoportot, egy új üzemeltetési csomagot és egy új Storage-fiókot, ha még nem rendelkezik ezekkel. Ha elkészült, válassza a **Létrehozás** lehetőséget.
1. A telepítés befejezése után figyelje meg a webhely URL-címét. Ez az URL-cím az Azure-beli Function-alkalmazás címe. Mentse egy ideiglenes helyre.
1. A [Azure Portal](https://portal.azure.com)navigáljon az erőforráscsoporthoz, és keresse meg a `AnalyzeForm` közzétett funkciót. A **kezelés** szakaszban látnia kell a gazdagép kulcsait. Másolja az *alapértelmezett* gazdagépet, és mentse egy ideiglenes helyre.

## <a name="connect-to-your-pipeline"></a>Kapcsolódás a folyamathoz

Ahhoz, hogy ezt a képességet egy Cognitive Search folyamat használja, hozzá kell adnia egy képzettségi definíciót a készségkészlet. A következő JSON-blokk egy példa a szaktudás meghatározására (a bemenetek és kimenetek frissítése az adott forgatókönyv-és készségkészlet-környezetnek megfelelően). Cserélje le a `AzureFunctionEndpointUrl` függvény URL-címét, és cserélje le a helyére a `AzureFunctionDefaultHostKey` gazdagép kulcsával.

```json
{ 
  "description":"Skillset that invokes the Form Recognizer custom skill",
  "skills":[ 
    "[... your existing skills go here]",
    { 
      "@odata.type":"#Microsoft.Skills.Custom.WebApiSkill",
      "name":"formrecognizer",
      "description":"Extracts fields from a form using a pre-trained form recognition model",
      "uri":"[AzureFunctionEndpointUrl]/api/analyze-form?code=[AzureFunctionDefaultHostKey]",
      "httpMethod":"POST",
      "timeout":"PT30S",
      "context":"/document",
      "batchSize":1,
      "inputs":[ 
        { 
          "name":"formUrl",
          "source":"/document/metadata_storage_path"
        },
        { 
          "name":"formSasToken",
          "source":"/document/metadata_storage_sas_token"
        }
      ],
      "outputs":[ 
        { 
          "name":"address",
          "targetName":"address"
        },
        { 
          "name":"recipient",
          "targetName":"recipient"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>További lépések

Ebben az útmutatóban létrehozott egy egyéni képességet az Azure Form-felismerő szolgáltatásból. Az egyéni ismeretekkel kapcsolatos további tudnivalókért tekintse meg a következő forrásokat. 

* [Azure Search energiaellátási készségek: az egyéni képességek tárháza](https://github.com/Azure-Samples/azure-search-power-skills)
* [Egyéni képesség hozzáadása egy mesterséges intelligencia-bővítési folyamathoz](cognitive-search-custom-skill-interface.md)
* [Képességcsoport megadása](cognitive-search-defining-skillset.md)
* [Készségkészlet létrehozása (REST)](/rest/api/searchservice/create-skillset)
* [Gazdagított mezők leképezése](cognitive-search-output-field-mapping.md)
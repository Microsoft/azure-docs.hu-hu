---
title: Egyéni ügyességi példa Bing Entity Search API használatával
titleSuffix: Azure Cognitive Search
description: A Bing Entity Search szolgáltatás használatát mutatja be az Cognitive Search Azure-beli mesterséges intelligenciát használó indexelési folyamathoz hozzárendelt egyéni szakértelemben.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 5755e14e53d359fd8b322939bf1325d21536d593
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89020184"
---
# <a name="example-create-a-custom-skill-using-the-bing-entity-search-api"></a>Példa: egyéni képesség létrehozása a Bing Entity Search API használatával

Ebből a példából megtudhatja, hogyan hozhat létre egyéni webes API-képességet. Ez a képesség elfogadja a helyszíneket, a nyilvános adatokat és a szervezeteket, valamint a hozzájuk tartozó leírásokat. A példa egy [Azure-függvényt](https://azure.microsoft.com/services/functions/) használ a [Bing Entity Search API](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) becsomagolásához, hogy az megvalósítsa az egyéni ügyességi felületet.

## <a name="prerequisites"></a>Előfeltételek

+ Ha nem ismeri az egyéni szaktudás megvalósításához szükséges bemeneti/kimeneti felületet, olvassa el az [Egyéni ügyességi felületről](cognitive-search-custom-skill-interface.md) szóló cikket.

+ [!INCLUDE [cognitive-services-bing-entity-search-signup-requirements](../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

+ Telepítse a [Visual Studio 2019](https://www.visualstudio.com/vs/) -es vagy újabb verzióját, beleértve az Azure-fejlesztési munkaterhelést is.

## <a name="create-an-azure-function"></a>Azure-függvény létrehozása

Bár ez a példa egy Azure-függvényt használ a webes API-k üzemeltetéséhez, nem szükséges.  Ha megfelel a [kognitív képességek felületi követelményeinek](cognitive-search-custom-skill-interface.md), az Ön által felhasználható megközelítés nem lényeges. Azure Functions azonban egyszerűvé teheti az egyéni képességek létrehozását.

### <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

1. A Visual Studio Fájl menüjében válassza az **Új** > **Projekt** lehetőséget.

1. Az új projekt párbeszédpanelen válassza a **telepített** elemet, majd a **Visual C#**  >  **Cloud** elemet, válassza a **Azure functions** lehetőséget, írja be a projekt nevét, majd kattintson **az OK gombra**. A Function app neve csak C# névtérként érvényes, ezért ne használjon aláhúzást, kötőjelet vagy más, nem alfanumerikus karaktereket.

1. Válassza a **Azure functions v2 (.net Core)** lehetőséget. Azt is megteheti az 1. verzióban, de az alább írt kód a v2-sablonon alapul.

1. A **http-trigger** típusának kiválasztása

1. A Storage-fiók esetében válassza a **nincs** lehetőséget, mivel ehhez a függvényhez nem lesz szüksége tárterületre.

1. Kattintson az **OK** gombra a függvény projekt és a http által aktivált függvény létrehozásához.

### <a name="modify-the-code-to-call-the-bing-entity-search-service"></a>A kód módosítása a Bing Entity Search szolgáltatás meghívásához

A Visual Studio létrehoz egy projektet, benne egy olyan osztállyal, amely tartalmazza a kiválasztott függvénytípus sablonkódját. A metódus *FunctionName* attribútuma adja meg a függvény nevét. A *HttpTrigger* attribútum adja meg, hogy a függvényt egy HTTP-kérelem aktiválja.

Most cserélje le a *Function1. cs* fájl összes tartalmát a következő kódra:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

namespace SampleSkills
{
    /// <summary>
    /// Sample custom skill that wraps the Bing entity search API to connect it with a 
    /// AI enrichment pipeline.
    /// </summary>
    public static class BingEntitySearch
    {
        #region Credentials
        // IMPORTANT: Make sure to enter your credential and to verify the API endpoint matches yours.
        static readonly string bingApiEndpoint = "https://api.cognitive.microsoft.com/bing/v7.0/entities/";
        static readonly string key = "<enter your api key here>";  
        #endregion

        #region Class used to deserialize the request
        private class InputRecord
        {
            public class InputRecordData
            {
                public string Name { get; set; }
            }

            public string RecordId { get; set; }
            public InputRecordData Data { get; set; }
        }

        private class WebApiRequest
        {
            public List<InputRecord> Values { get; set; }
        }
        #endregion

        #region Classes used to serialize the response

        private class OutputRecord
        {
            public class OutputRecordData
            {
                public string Name { get; set; } = "";
                public string Description { get; set; } = "";
                public string Source { get; set; } = "";
                public string SourceUrl { get; set; } = "";
                public string LicenseAttribution { get; set; } = "";
                public string LicenseUrl { get; set; } = "";
            }

            public class OutputRecordMessage
            {
                public string Message { get; set; }
            }

            public string RecordId { get; set; }
            public OutputRecordData Data { get; set; }
            public List<OutputRecordMessage> Errors { get; set; }
            public List<OutputRecordMessage> Warnings { get; set; }
        }

        private class WebApiResponse
        {
            public List<OutputRecord> Values { get; set; }
        }
        #endregion

        #region Classes used to interact with the Bing API
        private class BingResponse
        {
            public BingEntities Entities { get; set; }
        }
        private class BingEntities
        {
            public BingEntity[] Value { get; set; }
        }

        private class BingEntity
        {
            public class EntityPresentationinfo
            {
                public string[] EntityTypeHints { get; set; }
            }

            public class License
            {
                public string Url { get; set; }
            }

            public class ContractualRule
            {
                public string _type { get; set; }
                public License License { get; set; }
                public string LicenseNotice { get; set; }
                public string Text { get; set; }
                public string Url { get; set; }
            }

            public ContractualRule[] ContractualRules { get; set; }
            public string Description { get; set; }
            public string Name { get; set; }
            public EntityPresentationinfo EntityPresentationInfo { get; set; }
        }
        #endregion

        #region The Azure Function definition

        [FunctionName("EntitySearch")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("Entity Search function: C# HTTP trigger function processed a request.");

            var response = new WebApiResponse
            {
                Values = new List<OutputRecord>()
            };

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            var data = JsonConvert.DeserializeObject<WebApiRequest>(requestBody);

            // Do some schema validation
            if (data == null)
            {
                return new BadRequestObjectResult("The request schema does not match expected schema.");
            }
            if (data.Values == null)
            {
                return new BadRequestObjectResult("The request schema does not match expected schema. Could not find values array.");
            }

            // Calculate the response for each value.
            foreach (var record in data.Values)
            {
                if (record == null || record.RecordId == null) continue;

                OutputRecord responseRecord = new OutputRecord
                {
                    RecordId = record.RecordId
                };

                try
                {
                    responseRecord.Data = GetEntityMetadata(record.Data.Name).Result;
                }
                catch (Exception e)
                {
                    // Something bad happened, log the issue.
                    var error = new OutputRecord.OutputRecordMessage
                    {
                        Message = e.Message
                    };

                    responseRecord.Errors = new List<OutputRecord.OutputRecordMessage>
                    {
                        error
                    };
                }
                finally
                {
                    response.Values.Add(responseRecord);
                }
            }

            return (ActionResult)new OkObjectResult(response);
        }

        #endregion

        #region Methods to call the Bing API
        /// <summary>
        /// Gets metadata for a particular entity based on its name using Bing Entity Search
        /// </summary>
        /// <param name="entityName">The name of the entity to extract data for.</param>
        /// <returns>Asynchronous task that returns entity data. </returns>
        private async static Task<OutputRecord.OutputRecordData> GetEntityMetadata(string entityName)
        {
            var uri = bingApiEndpoint + "?q=" + entityName + "&mkt=en-us&count=10&offset=0&safesearch=Moderate";
            var result = new OutputRecord.OutputRecordData();

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage {
                Method = HttpMethod.Get,
                RequestUri = new Uri(uri)
            })
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                HttpResponseMessage response = await client.SendAsync(request);
                string responseBody = await response?.Content?.ReadAsStringAsync();

                BingResponse bingResult = JsonConvert.DeserializeObject<BingResponse>(responseBody);
                if (bingResult != null)
                {
                    // In addition to the list of entities that could match the name, for simplicity let's return information
                    // for the top match as additional metadata at the root object.
                    return AddTopEntityMetadata(bingResult.Entities?.Value);
                }
            }

            return result;
        }

        private static OutputRecord.OutputRecordData AddTopEntityMetadata(BingEntity[] entities)
        {
            if (entities != null)
            {
                foreach (BingEntity entity in entities.Where(
                    entity => entity?.EntityPresentationInfo?.EntityTypeHints != null
                        && (entity.EntityPresentationInfo.EntityTypeHints[0] == "Person"
                            || entity.EntityPresentationInfo.EntityTypeHints[0] == "Organization"
                            || entity.EntityPresentationInfo.EntityTypeHints[0] == "Location")
                        && !String.IsNullOrEmpty(entity.Description)))
                {
                    var rootObject = new OutputRecord.OutputRecordData
                    {
                        Description = entity.Description,
                        Name = entity.Name
                    };

                    if (entity.ContractualRules != null)
                    {
                        foreach (var rule in entity.ContractualRules)
                        {
                            switch (rule._type)
                            {
                                case "ContractualRules/LicenseAttribution":
                                    rootObject.LicenseAttribution = rule.LicenseNotice;
                                    rootObject.LicenseUrl = rule.License.Url;
                                    break;
                                case "ContractualRules/LinkAttribution":
                                    rootObject.Source = rule.Text;
                                    rootObject.SourceUrl = rule.Url;
                                    break;
                            }
                        }
                    }

                    return rootObject;
                }
            }

            return new OutputRecord.OutputRecordData();
        }
        #endregion
    }
}
```

Ügyeljen arra, hogy a saját *kulcs* értékét az állandó értéken adja meg a `key` BING Entity Search API-ra való regisztráció során kapott kulcs alapján.

Ez a minta minden szükséges kódot tartalmaz egyetlen fájlban a kényelem érdekében. [Az energiagazdálkodási képességek tárházában](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Text/BingEntitySearch)megtalálhatja ugyanezen képességek egy kicsit strukturált verzióját is.

Természetesen előfordulhat, hogy átnevezi a fájlt a verzióra `Function1.cs` `BingEntitySearch.cs` .

## <a name="test-the-function-from-visual-studio"></a>A függvény tesztelése a Visual studióból

Nyomja le az **F5** billentyűt a program és a teszt funkció viselkedésének futtatásához. Ebben az esetben az alábbi függvényt fogjuk használni két entitás kereséséhez. A Poster vagy a Hegedűs használatával adja ki az alábbihoz hasonló hívást:

```http
POST https://localhost:7071/api/EntitySearch
```

### <a name="request-body"></a>A kérés törzse
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "name":  "Pablo Picasso"
            }
        },
        {
            "recordId": "e2",
            "data":
            {
                "name":  "Microsoft"
            }
        }
    ]
}
```

### <a name="response"></a>Reagálás
Az alábbi példához hasonló válasznak kell megjelennie:

```json
{
    "values": [
        {
            "recordId": "e1",
            "data": {
                "name": "Pablo Picasso",
                "description": "Pablo Ruiz Picasso was a Spanish painter [...]",
                "source": "Wikipedia",
                "sourceUrl": "http://en.wikipedia.org/wiki/Pablo_Picasso",
                "licenseAttribution": "Text under CC-BY-SA license",
                "licenseUrl": "http://creativecommons.org/licenses/by-sa/3.0/"
            },
            "errors": null,
            "warnings": null
        },
        "..."
    ]
}
```

## <a name="publish-the-function-to-azure"></a>A függvény közzététele az Azure-ban

Ha elégedett a funkció működésével, közzéteheti.

1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **Publish** (Közzététel) lehetőséget. Válassza az **új**  >  **Közzététel** létrehozása lehetőséget.

1. Ha még nem csatlakoztatta a Visual studiót az Azure-fiókjához, válassza a **fiók hozzáadása...** lehetőséget.

1. Kövesse a képernyőn megjelenő utasításokat. A rendszer megkéri, hogy adjon egyedi nevet az App Service-nek, az Azure-előfizetésnek, az erőforráscsoport, a üzemeltetési csomagnak és a használni kívánt Storage-fióknak. Ha még nem rendelkezik ezekkel, létrehozhat egy új erőforráscsoportot, egy új üzemeltetési csomagot és egy Storage-fiókot is. Ha elkészült, válassza a **Létrehozás** lehetőséget.

1. A telepítés befejezése után figyelje meg a webhely URL-címét. Ez az Azure-beli Function-alkalmazás címe. 

1. A [Azure Portal](https://portal.azure.com)navigáljon az erőforráscsoporthoz, és keresse meg a `EntitySearch` közzétett funkciót. A **kezelés** szakaszban látnia kell a gazdagép kulcsait. Válassza ki az *alapértelmezett* gazda kulcs **másolási** ikonját.  

## <a name="test-the-function-in-azure"></a>A függvény tesztelése az Azure-ban

Most, hogy rendelkezik az alapértelmezett gazdagép kulccsal, tesztelje a függvényt a következőképpen:

```http
POST https://[your-entity-search-app-name].azurewebsites.net/api/EntitySearch?code=[enter default host key here]
```

### <a name="request-body"></a>Kérelem törzse
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "name":  "Pablo Picasso"
            }
        },
        {
            "recordId": "e2",
            "data":
            {
                "name":  "Microsoft"
            }
        }
    ]
}
```

Ebben a példában ugyanazt az eredményt kell megadnia, amelyet korábban a függvény a helyi környezetben való futtatásakor látott.

## <a name="connect-to-your-pipeline"></a>Kapcsolódás a folyamathoz
Most, hogy már rendelkezik egy új egyéni képességgel, hozzáadhatja a készségkészlet. Az alábbi példa azt mutatja be, hogyan hívhatja meg a képességet, hogy leírást adjon hozzá a szervezetekhez a dokumentumban (ezt kiterjesztheti a helyszíneken és személyeken is.) A helyére írja `[your-entity-search-app-name]` be az alkalmazás nevét.

```json
{
    "skills": [
      "[... your existing skills remain here]",  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new Bing entity search custom skill",
        "uri": "https://[your-entity-search-app-name].azurewebsites.net/api/EntitySearch?code=[enter default host key here]",
          "context": "/document/merged_content/organizations/*",
          "inputs": [
            {
              "name": "name",
              "source": "/document/merged_content/organizations/*"
            }
          ],
          "outputs": [
            {
              "name": "description",
              "targetName": "description"
            }
          ]
      }
  ]
}
```

Itt számítjuk fel a beépített [entitás-felismerési képességet](cognitive-search-skill-entity-recognition.md) , hogy a készségkészlet jelen legyen, és hogy a dokumentumot a szervezetek listájával gazdagítsa. Az alábbiakban egy olyan entitás-kinyerési képesség-konfiguráció látható, amely elegendő lenne a szükséges adatlétrehozáshoz:

```json
{
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "name": "#1",
    "description": "Organization name extraction",
    "context": "/document/merged_content",
    "categories": [ "Organization" ],
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/merged_content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "organizations",
            "targetName": "organizations"
        }
    ]
},
```

## <a name="next-steps"></a>Következő lépések
Gratulálunk! Létrehozta az első egyéni szaktudását. Mostantól ugyanezt a mintát követheti saját egyéni funkcióinak hozzáadásával is. További információért kattintson az alábbi hivatkozásokra.

+ [Energiaellátási készségek: az egyéni képességek tárháza](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Egyéni képesség hozzáadása egy mesterséges intelligencia-bővítési folyamathoz](cognitive-search-custom-skill-interface.md)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
+ [Készségkészlet létrehozása (REST)](/rest/api/searchservice/create-skillset)
+ [A dúsított mezők leképezése](cognitive-search-output-field-mapping.md)
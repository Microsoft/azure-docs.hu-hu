---
title: Fejlesztői erőforrások – Language Understanding
description: Az SDK-k, a REST API-k, a CLI, a programozási nyelvben Language Understanding (LUIS) alkalmazások fejlesztését segítik. Kezelheti Azure-erőforrásait és a LUIS-előrejelzéseket.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 01/12/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 08a1965aadf8f50625e54d7c5e3b3f6a44c0bbd4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946178"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>SDK, REST és CLI fejlesztői erőforrások a Language Understandinghoz (LUIS)

Az SDK-k, a REST API-k, a CLI, a programozási nyelvben Language Understanding (LUIS) alkalmazások fejlesztését segítik. Kezelheti Azure-erőforrásait és a LUIS-előrejelzéseket.

## <a name="azure-resource-management"></a>Azure-erőforrások kezelése

Az Azure Cognitive Services Felügyeleti réteg használatával hozhatja létre, szerkesztheti, listázhatja és törölheti a Language Understanding vagy a kognitív szolgáltatás erőforrását.

Az eszközön alapuló dokumentáció keresése:

* [Azure CLI](/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](/powershell/module/azurerm.cognitiveservices/#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>Szerzői és előrejelzési kérelmek Language Understanding

A Language Understanding szolgáltatás elérhető egy Azure-erőforrásból, amelyet létre kell hoznia. Két erőforrás létezik:

* A **szerzői** erőforrással a létrehozás, szerkesztés, betanítás és közzététel lehetőséget használhatja.
* A futásidejű **előrejelzéssel** elküldheti a felhasználó szövegét, és előrejelzést kaphat.

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

[Cognitive Services mintakód](https://github.com/Azure-Samples/cognitive-services-quickstart-code) használatával megismerheti és használhatja a leggyakoribb feladatokat.

### <a name="rest-specifications"></a>REST-specifikációk

A [Luis Rest-specifikációk](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/cognitiveservices/data-plane/LUIS), valamint az összes [Azure Rest-specifikáció](https://github.com/Azure/azure-rest-api-specs)nyilvánosan elérhető a githubon.

### <a name="rest-apis"></a>REST API-k

A szerzői és előrejelzési végpont API-jai a REST API-kon keresztül érhetők el:

|Típus|Verzió|
|--|--|
|Tartalomkészítés|[V2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[előzetes verzió – v3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|Előrejelzés|[V2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="rest-endpoints"></a>REST-végpontok

A LUIS jelenleg két típusú végpontot tartalmaz:

* **szerzői műveletek** a betanítási végponton
* lekérdezési **Előrejelzés** a futásidejű végponton.

|Rendeltetés|URL-cím|
|--|--|
|V2 authoring a betanítási végponton|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|V3 authoring a betanítási végponton|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/authoring/v3.0-preview/apps/{appID}/`|
|V2 előrejelzés – a futásidejű végpont összes előrejelzése|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|
|V3 előrejelzés – verziók előrejelzése futásidejű végponton|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/versions/{versionId}/predict?query={query}[&verbose][&log][&show-all-intents]`|
|V3 előrejelzés – a bővítőhelyek előrejelzése futásidejű végponton|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/slots/{slotName}/predict?query={query}[&verbose][&log][&show-all-intents]`|

Az alábbi táblázat az előző táblázatban szereplő, kapcsos zárójelekkel ellátott paramétereket ismerteti `{}` .

|Paraméter|Cél|
|--|--|
|`your-resource-name`|Azure-Erőforrás neve|
|`q` vagy `query`|az ügyfélalkalmazás, például a csevegési bot által eljuttatott teljes szöveg|
|`version`|10 karakteres verzió neve|
|`slot`| `production` vagy `staging`|

### <a name="rest-query-string-parameters"></a>REST lekérdezési karakterlánc paraméterei

[!INCLUDE [V3 query params](./includes/v3-prediction-query-params.md)]

## <a name="app-schema"></a>Alkalmazásséma

Az [alkalmazás sémája](app-schema-definition.md) importálva van, `.json` vagy formátumba van exportálva `.lu` .

### <a name="language-based-sdks"></a>Language-alapú SDK-k

|Nyelv |Dokumentáció|Csomag|Rövid útmutatók|
|--|--|--|--|
|C#|[Tartalomkészítés](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring)</br>[Jóslás](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime)|[NuGet készítése](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[NuGet előrejelzése](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[Tartalomkészítés](./client-libraries-rest-api.md?pivots=rest-api)<br>[Lekérdezések előrejelzése](./client-libraries-rest-api.md?pivots=rest-api)|
|Go|[Szerzői műveletek és előrejelzések](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)||
|Java|[Szerzői műveletek és előrejelzések](/java/api/overview/azure/cognitiveservices/client/languageunderstanding)|[Maven-készítés](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Maven-előrejelzés](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|
|JavaScript|[Tartalomkészítés](/javascript/api/@azure/cognitiveservices-luis-authoring/)<br>[Jóslás](/javascript/api/@azure/cognitiveservices-luis-runtime/)|[NPM készítése](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[NPM előrejelzése](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[Tartalomkészítés](./client-libraries-rest-api.md?pivots=rest-api)<br>[Jóslás](./client-libraries-rest-api.md?pivots=rest-api)|
|Python|[Szerzői műveletek és előrejelzések](./client-libraries-rest-api.md?pivots=rest-api)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Tartalomkészítés](./client-libraries-rest-api.md?pivots=rest-api)<br>[Jóslás](./client-libraries-rest-api.md?pivots=rest-api)|


### <a name="containers"></a>Tárolók

A Language Understanding (LUIS) egy [tárolót](luis-container-howto.md) biztosít az alkalmazás helyszíni és a benne foglalt verziói számára.

### <a name="export-and-import-formats"></a>Exportálási és importálási formátumok

Language Understanding lehetővé teszi, hogy az alkalmazás és a modelljét JSON formátumban, a `.LU` ([LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)) formátumban és a Language Understanding tároló tömörített csomagjával kezelje.

Ezen formátumok importálása és exportálása az API-kon és a LUIS-portálon érhető el. A portál az alkalmazások listájának és a verziók listájának részeként biztosítja az importálást és az exportálást.

## <a name="workshops"></a>Workshopokat

* GitHub: (workshop) [társalgási-AI: NLU Luis használatával](https://github.com/GlobalAICommunity/Workshop-Conversational-AI)

## <a name="continuous-integration-tools"></a>Folyamatos integrációs eszközök

* GitHub: (előzetes [verzió) Luis-alkalmazás fejlesztése a DevOps Practices használatával](https://github.com/Azure-Samples/LUIS-DevOps-Template)
* GitHub: [NLU. DevOps](https://github.com/microsoft/NLU.DevOps) – a NLU-szolgáltatások folyamatos integrációját és üzembe helyezését támogató eszközök.

## <a name="bot-framework-tools"></a>A bot Framework eszközei

A bot Framework számos különböző nyelven és szolgáltatásként, [Azure bot Service](https://dev.botframework.com/)használatával érhető el [SDK](https://github.com/Microsoft/botframework) -val.

A bot Framework [számos eszközt](https://github.com/microsoft/botbuilder-tools) biztosít a Language Understandinghoz, beleértve a következőket:
* [Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases) – egy asztali alkalmazás, amely lehetővé teszi a robot-fejlesztők számára a bot Framework SDK használatával létrehozott robotok tesztelését és hibakeresését
* A [robot Framework zeneszerzője](https://github.com/microsoft/BotFramework-Composer/blob/stable/README.md) – integrált fejlesztői eszköz a fejlesztők és a több-fegyelmi csapatok számára a robotok és a társalgási tapasztalatok létrehozásához a Microsoft bot Framework használatával
* [Robot Framework-minták](https://github.com/microsoft/botbuilder-samples) – #C, JavaScript, írógéppel és Python

## <a name="next-steps"></a>Következő lépések

* A gyakori http- [hibakódok](luis-reference-response-codes.md) ismertetése
* Az API-k és SDK-k [hivatkozási dokumentációja](../../index.yml)
* [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) és [Azure bot Service](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Kognitív tárolók](../cognitive-services-container-support.md)

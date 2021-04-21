---
title: Fejlesztői erőforrások – Language Understanding
description: Az SDK-k, a REST API-k és a CLI segítségével Language Understanding (LUIS) alkalmazásokat fejleszthet a programozási nyelvben. Az Azure-erőforrások és a LUIS-előrejelzések kezelése.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 01/12/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 04c7d4a7e725d99c7dba94779d365312f8b960af
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787106"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>SDK, REST és CLI fejlesztői erőforrások Language Understanding (LUIS)

Az SDK-k, a REST API-k és a CLI segítségével Language Understanding (LUIS) alkalmazásokat fejleszthet a programozási nyelvben. Az Azure-erőforrások és a LUIS-előrejelzések kezelése.

## <a name="azure-resource-management"></a>Azure-erőforráskezelés

A Azure Cognitive Services réteg használatával hozza létre, szerkessze, listába sorolja és törölje Language Understanding cognitive services-erőforrást.

Az eszköz alapján referenciadokumentációt talál:

* [Azure CLI](/cli/azure/cognitiveservices#az_cognitiveservices_list)

* [Azure RM PowerShell](/powershell/module/azurerm.cognitiveservices/#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>Language Understanding és előrejelzési kérések lekérése

A Language Understanding szolgáltatás egy olyan Azure-erőforrásból érhető el, amit létre kell hoznia. Két erőforrás van:

* Használja a **szerzői erőforrást** a betanításhoz létrehozáshoz, szerkesztéshez, betanításhoz és közzétételhez.
* A **futtatás előrejelzésének** használatával küldje el a felhasználó szövegét, és fogadtassa az előrejelzést.

Tudnivalók a [V3 előrejelzési végpontról.](luis-migration-api-v3.md)

A [Cognitive Services kód használatával](https://github.com/Azure-Samples/cognitive-services-quickstart-code) megismeri és használhatja a leggyakoribb feladatokat.

### <a name="rest-specifications"></a>REST-specifikációk

A [LUIS REST-specifikációi](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/cognitiveservices/data-plane/LUIS), valamint az [összes Azure REST-specifikáció](https://github.com/Azure/azure-rest-api-specs)nyilvánosan elérhetők a GitHubon.

### <a name="rest-apis"></a>REST API-k

A szerzői és előrejelzési végponti API-k REST API-kból is elérhetők:

|Típus|Verzió|
|--|--|
|Tartalomkészítés|[V2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[előzetes verzió: V3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|Előrejelzés|[V2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="rest-endpoints"></a>REST-végpontok

A LUIS jelenleg 2 végponttípust használ:

* **szerzői a** betanítás végponton
* lekérdezés **előrejelzése** a futásidejű végponton.

|Rendeltetés|URL-cím|
|--|--|
|V2-authoring on training endpoint (V2-írás betanítás végponton)|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|V3 Authoring on training endpoint|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/authoring/v3.0-preview/apps/{appID}/`|
|V2 előrejelzés – az összes előrejelzés a futásidejű végponton|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|
|V3 előrejelzés – verziók előrejelzése a futásidejű végponton|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/versions/{versionId}/predict?query={query}[&verbose][&log][&show-all-intents]`|
|V3 előrejelzés – tárolóhely-előrejelzés a futásidejű végponton|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/slots/{slotName}/predict?query={query}[&verbose][&log][&show-all-intents]`|

Az alábbi táblázat az előző táblázatban található kapcsos zárójelekkel () jelöli `{}` a paramétereket.

|Paraméter|Cél|
|--|--|
|`your-resource-name`|Azure-erőforrás neve|
|`q` vagy `query`|az ügyfélalkalmazásból, például csevegő robotból küldött kimondott szöveg|
|`version`|10 karakteres verziónév|
|`slot`| `production` vagy `staging`|

### <a name="rest-query-string-parameters"></a>REST lekérdezési sztring paraméterei

[!INCLUDE [V3 query params](./includes/v3-prediction-query-params.md)]

## <a name="app-schema"></a>Alkalmazásséma

Az [alkalmazássémát](app-schema-definition.md) a rendszer vagy formátumban importálja és `.json` `.lu` exportálja.

### <a name="language-based-sdks"></a>Nyelvalapú SDK-k

|Nyelv |Referenciadokumentáció|Csomag|Rövid útmutatók|
|--|--|--|--|
|C#|[Tartalomkészítés](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring)</br>[Jóslás](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime)|[NuGet-szerzői](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[NuGet-előrejelzés](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[Tartalomkészítés](./client-libraries-rest-api.md?pivots=rest-api)<br>[Lekérdezés-előrejelzés](./client-libraries-rest-api.md?pivots=rest-api)|
|Go|[Szerzői és előrejelzési](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[Sdk](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)||
|Java|[Szerzői és előrejelzési](/java/api/overview/azure/cognitiveservices/client/languageunderstanding)|[Maven-szerzői](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Maven-előrejelzés](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|
|JavaScript|[Tartalomkészítés](/javascript/api/@azure/cognitiveservices-luis-authoring/)<br>[Jóslás](/javascript/api/@azure/cognitiveservices-luis-runtime/)|[NPM-szerzői](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[NPM-előrejelzés](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[Tartalomkészítés](./client-libraries-rest-api.md?pivots=rest-api)<br>[Jóslás](./client-libraries-rest-api.md?pivots=rest-api)|
|Python|[Szerzői és előrejelzési](./client-libraries-rest-api.md?pivots=rest-api)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Tartalomkészítés](./client-libraries-rest-api.md?pivots=rest-api)<br>[Jóslás](./client-libraries-rest-api.md?pivots=rest-api)|


### <a name="containers"></a>Tárolók

Language Understanding (LUIS) egy [](luis-container-howto.md) tárolót biztosít az alkalmazás helyszíni és tartalmazott verzióinak szolgáltatáshoz.

### <a name="export-and-import-formats"></a>Exportálási és importálási formátumok

Language Understanding lehetővé teszi az alkalmazás és a modellek JSON formátumban, ( LUDown ) formátumban és tömörített csomagként való kezelését az Language Understanding `.LU` tárolóhoz.[](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)

A formátumok importálása és exportálása az API-kból és a LUIS portálról érhető el. A portál az Alkalmazások és verziók lista részeként biztosítja az importálást és az exportálást.

## <a name="workshops"></a>Műhelyek

* GitHub: (Workshop) [Conversational-AI: NLU a LUIS használatával](https://github.com/GlobalAICommunity/Workshop-Conversational-AI)

## <a name="continuous-integration-tools"></a>Folyamatos integrációs eszközök

* GitHub: (Előzetes verzió) [LUIS-alkalmazás fejlesztése DevOps-eljárásokkal](https://github.com/Azure-Samples/LUIS-DevOps-Template)
* GitHub: [NLU. DevOps –](https://github.com/microsoft/NLU.DevOps) Az NLU-szolgáltatások folyamatos integrációját és üzembe helyezését támogató eszközök.

## <a name="bot-framework-tools"></a>Bot Framework eszközök

A bot framework [](https://github.com/Microsoft/botframework) SDK-ként érhető el különböző nyelveken és szolgáltatásként a [Azure Bot Service.](https://dev.botframework.com/)

A Bot Framework [számos eszközt biztosít a](https://github.com/microsoft/botbuilder-tools) Language Understanding, például:
* [Bot Framework emulátor –](https://github.com/Microsoft/BotFramework-Emulator/releases) egy asztali alkalmazás, amely lehetővé teszi a robotfejlesztők számára az Bot Framework SDK-val készült robotok tesztelését és hibakeresését
* [Bot Framework Composer](https://github.com/microsoft/BotFramework-Composer/blob/stable/README.md) – integrált fejlesztőeszköz fejlesztők és multidiszciplináris csapatok számára, amely robotokat és beszélgetési élményeket hoz létre a Microsoft Bot Framework
* [Bot Framework –](https://github.com/microsoft/botbuilder-samples) a #C, JavaScript, TypeScript és Python nyelven

## <a name="next-steps"></a>Következő lépések

* A gyakori [HTTP-hibakódok megismerása](luis-reference-response-codes.md)
* [Az összes API](../../index.yml) és SDK referenciadokumentációja
* [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) és [Azure Bot Service](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Cognitive Containers](../cognitive-services-container-support.md)

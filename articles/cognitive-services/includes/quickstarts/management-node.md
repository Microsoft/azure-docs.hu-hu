---
title: 'Gyors útmutató: Azure Management ügyféloldali kódtár Node.js'
description: Ebben a rövid útmutatóban megismerheti a Node.js Azure Management ügyféloldali függvénytárának első lépéseit.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: eddee6bc0fdcd636b7b9ffb81b121052499251b2
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/23/2020
ms.locfileid: "97745717"
---
[Dokumentáció](/javascript/api/@azure/arm-cognitiveservices/?view=azure-node-latest)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices)  |  [Csomag (NPM)](https://www.npmjs.com/package/@azure/arm-cognitiveservices)  |  [Példák](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices#sample-code)

## <a name="javascript-prerequisites"></a>JavaScript-előfeltételek

* Érvényes Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/).
* A [Node.js](https://nodejs.org/) aktuális verziója

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Egy konzolablak (például a cmd, a PowerShell vagy a bash) ablakban hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá. 

```console
mkdir myapp && cd myapp
```

Futtassa a `npm init` parancsot egy Node-alkalmazás fájlhoz való létrehozásához `package.json` . 

```console
npm init
```

A folytatás előtt hozzon létre egy _index.js_ nevű fájlt.

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Telepítse a következő NPM-csomagokat:

```console
npm install @azure/arm-cognitiveservices
npm install @azure/ms-rest-js
npm install @azure/ms-rest-nodeauth
```

Az alkalmazás `package.json` fájlja a függőségekkel lesz frissítve.

### <a name="import-libraries"></a>Kódtárak importálása

Nyissa meg _index.js_ -parancsfájlját, és importálja a következő könyvtárakat.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_imports)]

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Adja hozzá a következő mezőket a parancsfájl gyökeréhez, és töltse ki az értékeket a létrehozott egyszerű szolgáltatás és az Azure-fiók adatai alapján.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_constants)]

Ezután adja hozzá a következő `quickstart` függvényt a program fő munkájának kezeléséhez. A kód első blokkja egy **CognitiveServicesManagementClient** objektumot hoz létre a fent megadott hitelesítőadat-változók használatával. Ez az objektum szükséges az összes Azure-felügyeleti művelethez.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main_auth)]

## <a name="call-management-functions"></a>Hívás-felügyeleti függvények

Adja hozzá a következő kódot a függvény végéhez a `quickstart` rendelkezésre álló erőforrások listázásához, egy minta erőforrás létrehozásához, a saját tulajdonban lévő erőforrások listázásához, majd a minta erőforrás törléséhez. Ezeket a függvényeket a következő lépésekben fogja meghatározni.

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services-erőforrás létrehozása

Új Cognitive Services erőforrás létrehozásához és előfizetéséhez használja a **create** függvényt. Ez a függvény új számlázható erőforrást hoz létre az Ön által beadott erőforráscsoporthoz. Az új erőforrás létrehozásakor ismernie kell a használni kívánt szolgáltatás típusát, valamint az árképzési szintet (vagy SKU-t) és egy Azure-helyet. A következő függvény végrehajtja az összes ilyen argumentumot, és létrehoz egy erőforrást.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>Válasszon ki egy szolgáltatást és egy díjszabási szintet

Új erőforrás létrehozásakor ismernie kell a használni kívánt szolgáltatás típusát, valamint a kívánt [árképzési szintet](https://azure.microsoft.com/pricing/details/cognitive-services/) (vagy SKU-t). Ezt és egyéb információkat paraméterekként fogja használni az erőforrás létrehozásakor. A következő függvény felsorolja az elérhető kognitív szolgáltatások "típusait".

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>Erőforrások megtekintése

Az Azure-fiókban lévő összes erőforrás megtekintéséhez (az összes erőforráscsoport esetében) használja a következő függvényt:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list)]

## <a name="delete-a-resource"></a>Erőforrás törlése

A következő függvény törli a megadott erőforrást az adott erőforráscsoporthoz.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_delete)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Adja hozzá a következő kódot a parancsfájl aljához, hogy a fő `quickstart` függvényt a hibakezelés során hívja meg.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main)]

Ezután a konzol ablakban futtassa az alkalmazást a `node` paranccsal.

```console
node index.js
```

## <a name="see-also"></a>További információ

* [Az Azure Management SDK dokumentációja](/javascript/api/@azure/arm-cognitiveservices/?view=azure-node-latest)
* [Mi az Azure Cognitive Services?](../../what-are-cognitive-services.md)
* [Kérelmek hitelesítése az Azure Cognitive Services](../../authentication.md)
* [Új erőforrás létrehozása az Azure Portal használatával](../../cognitive-services-apis-create-account.md)
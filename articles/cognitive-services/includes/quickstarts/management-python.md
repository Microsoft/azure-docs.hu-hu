---
title: 'Gyors útmutató: Azure Management ügyféloldali kódtár a Pythonhoz'
description: Ebben a rövid útmutatóban megismerheti a Pythonhoz készült Azure Management ügyféloldali kódtárat.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: fb908cdcf3e235654effc043de29e599a48179d4
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104879644"
---
[Dokumentáció](/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices)  |  [Csomag (PyPi)](https://pypi.org/project/azure-mgmt-cognitiveservices/)  |  [Példák](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices/tests)

## <a name="python-prerequisites"></a>A Python előfeltételei

* Érvényes Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/).
* [Python 3.x](https://www.python.org/)

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

Hozzon létre egy új Python-alkalmazást az előnyben részesített szerkesztőben vagy az IDE-ben, és navigáljon a projekthez egy konzol ablakban.

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Az ügyféloldali kódtár a használatával telepíthető:

```console
pip install azure-mgmt-cognitiveservices
```

Ha a Visual Studio IDE-t használja, az ügyféloldali kódtár letölthető NuGet-csomagként érhető el.

### <a name="import-libraries"></a>Kódtárak importálása

Nyissa meg a Python-parancsfájlt, és importálja a következő könyvtárakat.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_imports)]

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Adja hozzá a következő mezőket a parancsfájl gyökeréhez, és töltse ki az értékeket a létrehozott egyszerű szolgáltatás és az Azure-fiók adatai alapján.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_constants)]

Ezután adja hozzá a következő kódot egy **CognitiveServicesManagementClient** objektum létrehozásához. Ez az objektum szükséges az összes Azure-felügyeleti művelethez.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_auth)]

## <a name="create-a-cognitive-services-resource-python"></a>Cognitive Services-erőforrás létrehozása (Python)

Új Cognitive Services erőforrás létrehozásához és előfizetéséhez használja a **create** függvényt. Ez a függvény új számlázható erőforrást hoz létre az Ön által beadott erőforráscsoporthoz. Az új erőforrás létrehozásakor ismernie kell a használni kívánt szolgáltatás típusát, valamint az árképzési szintet (vagy SKU-t) és egy Azure-helyet. A következő függvény végrehajtja az összes ilyen argumentumot, és létrehoz egy erőforrást.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>Válasszon ki egy szolgáltatást és egy díjszabási szintet

Új erőforrás létrehozásakor ismernie kell a használni kívánt szolgáltatás típusát, valamint a kívánt [árképzési szintet](https://azure.microsoft.com/pricing/details/cognitive-services/) (vagy SKU-t). Ezt és egyéb információkat paraméterekként fogja használni az erőforrás létrehozásakor. A következő függvény felsorolja az elérhető kognitív szolgáltatások "típusait".

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>Erőforrások megtekintése

Az Azure-fiókban lévő összes erőforrás megtekintéséhez (az összes erőforráscsoport esetében) használja a következő függvényt:

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_list)]

## <a name="delete-a-resource"></a>Erőforrás törlése

A következő függvény törli a megadott erőforrást az adott erőforráscsoporthoz.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_delete)]

## <a name="call-management-functions"></a>Hívás-felügyeleti függvények

Adja hozzá a következő kódot a szkript aljához a fenti függvények meghívásához. Ez a kód felsorolja az elérhető erőforrásokat, létrehoz egy minta-erőforrást, felsorolja a tulajdonában lévő erőforrásokat, majd törli a minta erőforrást.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_calls)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a parancssorból a `python` paranccsal.

```console
python <your-script-name>.py
```

## <a name="see-also"></a>Lásd még

* Lásd: **[kérelmek hitelesítése az Azure-Cognitive Services](../../authentication.md)** a Cognitive Services biztonságos kezeléséhez.
* Ismerje meg, **[Mi az Azure Cognitive Services?](../../what-are-cognitive-services.md)** a különböző kategóriák listájának lekéréséhez Cognitive Serviceson belül.
* A Cognitive Services által támogatott természetes nyelvek listájának megjelenítéséhez tekintse meg a **[természetes nyelvi támogatás](../../language-support.md)** című témakört.
* Lásd: a **[Cognitive Services használata tárolóként](../../cognitive-services-container-support.md)** a Cognitive Services helyszíni használatának megismeréséhez.
* A Cognitive Services használatának költségeinek **[kiszámításához tekintse meg a Cognitive Services költségeinek tervezése és kezelése](../../plan-manage-costs.md)** című témakört.
* A Management SDK-val kapcsolatos további részletekért tekintse meg az **[Azure Management SDK dokumentációját](/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices)** .

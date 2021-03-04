---
title: Cognitive Services erőforrás létrehozása a Azure Portal
titleSuffix: Azure Cognitive Services
description: Az Azure Cognitive Services megismeréséhez hozzon létre és iratkozzon fel a Azure Portal egyik erőforrására.
services: cognitive-services
author: aahill
manager: nitinme
keywords: kognitív szolgáltatások, kognitív intelligencia, kognitív megoldások, AI-szolgáltatások
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 09/14/2020
ms.author: aahi
ms.openlocfilehash: 0ad2d0a10b20aa049147d61bd4e8168ea956392c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031922"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-portal"></a>Gyors útmutató: Cognitive Services-erőforrás létrehozása a Azure Portal használatával

Ez a rövid útmutató az Azure Cognitive Services használatának megkezdésére használható. Miután létrehozta a kognitív szolgáltatás erőforrását a Azure Portalban, egy végpontot és egy kulcsot fog kapni az alkalmazások hitelesítéséhez.

Az Azure Cognitive Services a REST API-kkal rendelkező felhőalapú szolgáltatások és az ügyféloldali kódtár SDK-k, amelyek segítségével a fejlesztők kognitív intelligenciát hozhatnak létre az alkalmazásokban anélkül, hogy közvetlen mesterséges intelligencia (AI) vagy adattudományi ismereteket vagy ismereteiket kellene létrehozniuk. Az Azure Cognitive Services lehetővé teszi, hogy a fejlesztők könnyen hozzá tudják adni a kognitív funkciókat az alkalmazásaikban olyan kognitív megoldásokkal, amelyek megtekinthetik, meghallgatják, megértették, megértik, sőt, akár


[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Előfeltételek

* Érvényes Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/).

## <a name="create-a-new-azure-cognitive-services-resource"></a>Új Azure Cognitive Services-erőforrás létrehozása

1. Erőforrásokat hozhat létre.

    #### <a name="multi-service-resource"></a>[Több szolgáltatásból álló erőforrás](#tab/multiservice)

    A Multi-Service erőforrás neve **Cognitive Services** a portálon. [Cognitive Services erőforrás létrehozása](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne).

    A több szolgáltatásból álló erőforrás jelenleg a következő Cognitive Serviceshoz biztosít hozzáférést:

    - Computer Vision
    - Content Moderator
    - Face
    - Language Understanding (LUIS)
    - Szövegelemzés
    - Fordító
    - Bing Search v7 <br>(Web, kép, hírek, videó, vizualizáció)
    - Bing – Egyéni keresés
    - Bing – Entitáskeresés
    - Bing – Automatikus kiegészítés
    - Bing – Helyesírás-ellenőrzés

    #### <a name="single-service-resource"></a>[Egy szolgáltatásból származó erőforrás](#tab/singleservice)

    Az alábbi hivatkozásokat követve hozhat létre erőforrást az elérhető Cognitive Serviceshoz:

    | Látás                      | Beszéd                  | Nyelv                          | Döntés             | Keresés                 |
    |-----------------------------|-------------------------|-----------------------------------|----------------------|------------------------|
    | [Számítógép jövőképe](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [Beszédfelismerési szolgáltatások](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [Magával ragadó olvasó](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [Anomáliadetektor](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | [Bing Search API v7](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7) |
    | [Egyéni jövőkép szolgáltatás](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) | [Speaker Recognition](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeakerRecognition) | [Language Understanding (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | [Bing – Egyéni keresés](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingCustomSearch) |
    | [Face](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [Personalizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     | [Bing – Entitáskeresés](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingEntitySearch) |
    | [Ink Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesInkRecognizer)        |                         | [Text Analytics](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |  [Metrics Advisor](https://go.microsoft.com/fwlink/?linkid=2142156)                    | [Bing – Helyesírás-ellenőrzés](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSpellCheck-v7)   |
    |           |                         | [Translator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)               |                      | [Bing Autosuggest](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingAutosuggest-v7)                       |

    ***

3. A **Létrehozás** lapon adja meg a következő információkat:

    #### <a name="multi-service-resource"></a>[Több szolgáltatásból álló erőforrás](#tab/multiservice)

    |    |    |
    |--|--|
    | **Név** | A kognitív szolgáltatások erőforrásának leíró neve. Például: *MyCognitiveServicesResource*. |
    | **Előfizetés** | Válassza ki az egyik elérhető Azure-előfizetést. |
    | **Hely** | A kognitív szolgáltatás példányának helye. A különböző helyek késést okozhatnak, de nincs hatással az erőforrás futásidejű rendelkezésre állására. |
    | **Tarifacsomag** | A Cognitive Services fiókjának díja a választott beállításoktól és a használattól függ. További információt az API [díjszabását](https://azure.microsoft.com/pricing/details/cognitive-services/)ismertető témakörben talál.
    | **Erőforráscsoport** | Az Azure-erőforráscsoport, amely a Cognitive Services erőforrást fogja tartalmazni. Létrehozhat egy új csoportot, vagy hozzáadhatja egy már meglévő csoporthoz is. |

    ![Több szolgáltatás erőforrás-erőforrás-létrehozási képernyője](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    Kattintson a **Létrehozás** lehetőségre.

    #### <a name="single-service-resource"></a>[Egy szolgáltatásból származó erőforrás](#tab/singleservice)

    |    |    |
    |--|--|
    | **Név** | A kognitív szolgáltatások erőforrásának leíró neve. Például: *TextAnalyticsResource*. |
    | **Előfizetés** | Válassza ki az egyik elérhető Azure-előfizetést. |
    | **Hely** | A kognitív szolgáltatás példányának helye. A különböző helyek késést okozhatnak, de nincs hatással az erőforrás futásidejű rendelkezésre állására. |
    | **Tarifacsomag** | A Cognitive Services fiókjának díja a választott beállításoktól és a használattól függ. További információt az API [díjszabását](https://azure.microsoft.com/pricing/details/cognitive-services/)ismertető témakörben talál.
    | **Erőforráscsoport** | Az Azure-erőforráscsoport, amely a Cognitive Services erőforrást fogja tartalmazni. Létrehozhat egy új csoportot, vagy hozzáadhatja egy már meglévő csoporthoz is. |

    ![Egy szolgáltatásbeli erőforrás-létrehozási képernyő](media/cognitive-services-apis-create-account/resource_create_screen.png)

    Kattintson a **Létrehozás** lehetőségre.

    ***

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]

## <a name="get-the-keys-for-your-resource"></a>Az erőforrás kulcsainak beolvasása

1. Az erőforrás sikeres üzembe helyezése után kattintson az **Ugrás az erőforráshoz** lehetőségre a **következő lépések** alatt.

    ![Cognitive Services keresése](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. A megnyíló Gyorsindítás ablaktáblán elérheti a kulcsot és a végpontot.

    ![Kulcs és végpont beolvasása](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlése a csoportban található egyéb erőforrásokat is törli.

1. Az Azure Portalon bontsa ki a bal oldalon a szolgáltatásmenüt, és válassza az **Erőforráscsoportok** lehetőséget az erőforráscsoportok listájának megjelenítéséhez.
2. Keresse meg a törölni kívánt erőforrást tartalmazó erőforráscsoportot.
3. Kattintson a jobb gombbal az erőforráscsoport listájára. Válassza az **Erőforráscsoport törlése** elemet, és erősítse meg a választását.

## <a name="see-also"></a>Lásd még

* [Kérelmek hitelesítése az Azure Cognitive Services](authentication.md)
* [Mi az Azure Cognitive Services?](./what-are-cognitive-services.md)
* [Új erőforrás létrehozása az Azure felügyeleti ügyféloldali kódtár használatával](.\cognitive-services-apis-create-account-client-library.md)
* [Természetes nyelvi támogatás](language-support.md)
* [Docker-tárolók támogatása](cognitive-services-container-support.md)
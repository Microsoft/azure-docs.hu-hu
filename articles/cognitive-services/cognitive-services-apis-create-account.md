---
title: Cognitive Services erőforrás létrehozása a Azure Portal
titleSuffix: Azure Cognitive Services
description: Az Azure Cognitive Services megismeréséhez hozzon létre és iratkozzon fel a Azure Portal egyik erőforrására.
services: cognitive-services
author: aahill
manager: nitinme
keywords: kognitív szolgáltatások, kognitív intelligencia, kognitív megoldások, AI-szolgáltatások
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: aahi
ms.openlocfilehash: 115457180efab719b406c4e1e021234fa99736ab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670507"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-portal"></a>Gyors útmutató: Cognitive Services-erőforrás létrehozása a Azure Portal használatával

Ez a rövid útmutató az Azure Cognitive Services használatának megkezdésére használható. Miután létrehozta a kognitív szolgáltatás erőforrását a Azure Portalban, egy végpontot és egy kulcsot fog kapni az alkalmazások hitelesítéséhez.

Az Azure Cognitive Services a REST API-kkal rendelkező felhőalapú szolgáltatások és az ügyféloldali kódtár SDK-k, amelyek segítségével a fejlesztők kognitív intelligenciát hozhatnak létre az alkalmazásokban anélkül, hogy közvetlen mesterséges intelligencia (AI) vagy adattudományi ismereteket vagy ismereteiket kellene létrehozniuk. Az Azure Cognitive Services lehetővé teszi, hogy a fejlesztők könnyen hozzá tudják adni a kognitív funkciókat az alkalmazásaikban olyan kognitív megoldásokkal, amelyek megtekinthetik, meghallgatják, megértették, megértik, sőt, akár

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Előfeltételek

* Érvényes Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/).

## <a name="create-a-new-azure-cognitive-services-resource"></a>Új Azure Cognitive Services-erőforrás létrehozása

1. Erőforrásokat hozhat létre.

### <a name="multi-service-resource"></a>[Több szolgáltatásból álló erőforrás](#tab/multiservice)

A Multi-Service erőforrás neve **Cognitive Services** a portálon. [Cognitive Services erőforrás létrehozása](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne).

A több szolgáltatásból álló erőforrás jelenleg a következő Cognitive Serviceshoz biztosít hozzáférést:

* **Vízió** – Computer Vision, Custom Vision, űrlap-felismerő, Face
* **Beszéd** – beszéd
* **Language** -Language UNDERSTANDING (Luis), Text Analytics, Translator
* **Döntés** – személyre szabás, Content moderator

### <a name="single-service-resource"></a>[Egy szolgáltatásból származó erőforrás](#tab/singleservice)

Az alábbi hivatkozásokat követve hozhat létre erőforrást az elérhető Cognitive Serviceshoz:

| Látás                      | Beszéd                  | Nyelv                          | Döntés             |
|-----------------------------|-------------------------|-----------------------------------|----------------------|
| [Számítógép jövőképe](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [Beszédfelismerési szolgáltatások](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [Magával ragadó olvasó](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [Anomáliadetektor](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | 
| [Egyéni jövőkép szolgáltatás](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) |  | [Language Understanding (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | 
| [Face](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [Personalizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     |
| [Form Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)        |                         | [Text Analytics](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |  [Metrics Advisor](https://go.microsoft.com/fwlink/?linkid=2142156)                    |
| | | [Translator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) | |

---

2. A **Létrehozás** lapon adja meg a következő információkat:
<!-- markdownlint-disable MD024 -->

### <a name="multi-service-resource"></a>[Több szolgáltatásból álló erőforrás](#tab/multiservice)

|Projekt részletei| Leírás   |
|--|--|
| **Előfizetés** | Válassza ki az egyik elérhető Azure-előfizetést. |
| **Erőforráscsoport** | Az Azure-erőforráscsoport, amely a Cognitive Services erőforrást fogja tartalmazni. Létrehozhat egy új csoportot, vagy hozzáadhatja egy már meglévő csoporthoz is. |
| **Régió** | A kognitív szolgáltatás példányának helye. A különböző helyek késést okozhatnak, de nincs hatással az erőforrás futásidejű rendelkezésre állására. |
| **Név** | A kognitív szolgáltatások erőforrásának leíró neve. Például: *MyCognitiveServicesResource*. |
| **Tarifacsomag** | A Cognitive Services fiókjának díja a választott beállításoktól és a használattól függ. További információt az API [díjszabását](https://azure.microsoft.com/pricing/details/cognitive-services/)ismertető témakörben talál.

<!--![Multi-service resource creation screen](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)-->
:::image type="content" source="media/cognitive-services-apis-create-account/resource_create_screen-multi.png" alt-text="Több szolgáltatás erőforrás-létrehozási képernyője":::

Olvassa el és fogadja el a feltételeket (ha alkalmazható), majd válassza a **felülvizsgálat + létrehozás** lehetőséget.

### <a name="single-service-resource"></a>[Egy szolgáltatásból származó erőforrás](#tab/singleservice)

|Projekt részletei| Leírás   |
|--|--|
| **Előfizetés** | Válassza ki az egyik elérhető Azure-előfizetést. |
| **Erőforráscsoport** | Az Azure-erőforráscsoport, amely a Cognitive Services erőforrást fogja tartalmazni. Létrehozhat egy új csoportot, vagy hozzáadhatja egy már meglévő csoporthoz is. |
| **Régió** | A kognitív szolgáltatás példányának helye. A különböző helyek késést okozhatnak, de nincs hatással az erőforrás futásidejű rendelkezésre állására. |
| **Név** | A kognitív szolgáltatások erőforrásának leíró neve. Például: *MyCognitiveServicesResource*. |
| **Tarifacsomag** | A Cognitive Services fiókjának díja a választott beállításoktól és a használattól függ. További információt az API [díjszabását](https://azure.microsoft.com/pricing/details/cognitive-services/)ismertető témakörben talál.

<!--![Single-service resource creation screen](media/cognitive-services-apis-create-account/resource_create_screen.png)-->
:::image type="content" source="media/cognitive-services-apis-create-account/resource_create_screen.png" alt-text="Egy szolgáltatásbeli erőforrás-létrehozási képernyő":::

Válassza a Next (tovább) lehetőséget **: Virtual Network** , és válassza ki az erőforráshoz engedélyezni kívánt hálózati hozzáférés típusát, majd kattintson a **felülvizsgálat + létrehozás** gombra.

---

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

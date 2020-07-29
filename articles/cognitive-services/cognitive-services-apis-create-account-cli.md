---
title: Cognitive Services-erőforrás létrehozása az Azure CLI használatával
titleSuffix: Azure Cognitive Services
description: Az Azure Cognitive Services használatának első lépései az Azure parancssori felülettel létrehozott és előfizetéssel ellátott erőforrásokra való feliratkozással.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: aahi
ms.openlocfilehash: b77d69248059a494d823afcd149382dd52b0e49d
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87317127"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>Cognitive Services-erőforrás létrehozása az Azure parancssori felületének (CLI) használatával

Ez a rövid útmutató az Azure Cognitive Services [Azure parancssori felületen (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)való használatának megkezdéséhez használható. A Cognitive Services az Azure-előfizetésében létrehozott Azure- [erőforrások](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) jelölik. Az erőforrás létrehozása után az Ön által létrehozott kulcsokkal és végponttal hitelesítheti alkalmazásait. 


Ebből a rövid útmutatóból megtudhatja, hogyan regisztrálhat az Azure Cognitive Servicesra, és hogyan hozhat létre egy olyan fiókot, amely az [Azure parancssori felületének (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)használatával egyetlen szolgáltatást vagy többszolgáltatásos előfizetéssel rendelkezik. Ezeket a szolgáltatásokat az Azure- [erőforrások](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)képviselik, amelyek lehetővé teszik egy vagy több azure-Cognitive Services API-khoz való kapcsolódást.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Előfeltételek

* Érvényes Azure-előfizetés – [hozzon létre egyet](https://azure.microsoft.com/free/) ingyen.
* Az [Azure parancssori felülete (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="install-the-azure-cli-and-sign-in"></a>Az Azure CLI telepítése és bejelentkezés 

Telepítse az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)-t. Ha be szeretné jelentkezni a CLI helyi telepítésére, futtassa az az [login](https://docs.microsoft.com/cli/azure/reference-index#az-login) parancsot:

```azurecli-interactive
az login
```

A parancsok a böngészőben való futtatásához a zöld **kipróbálom** gombot is használhatja.
 
## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Új Azure Cognitive Services erőforráscsoport létrehozása

Cognitive Services erőforrás létrehozása előtt rendelkeznie kell egy Azure-erőforrással, amely tartalmazza az erőforrást. Új erőforrás létrehozásakor létrehozhat egy új erőforráscsoportot, vagy használhat egy meglévőt. Ez a cikk bemutatja, hogyan hozhat létre egy új erőforráscsoportot.

### <a name="choose-your-resource-group-location"></a>Válassza ki az erőforráscsoport helyét

Erőforrás létrehozásához szüksége lesz az előfizetéséhez elérhető Azure-helyekre. Az elérhető helyszínek listáját az az [Account List-Locations](/cli/azure/account#az-account-list-locations) paranccsal kérheti le. A legtöbb Cognitive Services több helyről is elérhető. Válassza ki az Önhöz legközelebb lévőt, vagy tekintse meg a szolgáltatáshoz elérhető helyeit.

> [!IMPORTANT]
> * Vegye figyelembe az Azure-beli helyét, mivel szüksége lesz rá az Azure-Cognitive Services meghívásakor.
> * Egyes Cognitive Servicesok rendelkezésre állása régiónként eltérő lehet. További információ: Azure- [termékek régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services).  

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Az Azure-beli hely létrehozása után hozzon létre egy új erőforráscsoportot az Azure CLI-ben az az [Group Create](/cli/azure/group#az-group-create) paranccsal.

Az alábbi példában cserélje le az Azure-helyet az `westus2` előfizetéséhez elérhető Azure-helyek egyikére.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services-erőforrás létrehozása

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Válasszon ki egy kognitív szolgáltatást és egy díjszabási szintet

Új erőforrás létrehozásakor ismernie kell a használni kívánt szolgáltatás típusát, valamint a kívánt [árképzési szintet](https://azure.microsoft.com/pricing/details/cognitive-services/) (vagy SKU-t). Ezt és egyéb információkat paraméterekként fogja használni az erőforrás létrehozásakor.

### <a name="multi-service"></a>Több szolgáltatás

| Szolgáltatás                    | Altípus                      |
|----------------------------|---------------------------|
| Több szolgáltatás. További részletekért tekintse meg a [díjszabási](https://azure.microsoft.com/pricing/details/cognitive-services/) oldalt.            | `CognitiveServices`     |


> [!NOTE]
> Az alábbi Cognitive Services számos olyan ingyenes szintet használhat, amellyel kipróbálhatja a szolgáltatást. Az ingyenes szintet az `F0` erőforráshoz tartozó SKU-ként használhatja.

### <a name="vision"></a>Látás

| Szolgáltatás                    | Altípus                      |
|----------------------------|---------------------------|
| Számítógépes látástechnológia            | `ComputerVision`          |
| Custom Vision – előrejelzés | `CustomVision.Prediction` |
| Custom Vision – képzés   | `CustomVision.Training`   |
| Arcfelismerés                       | `Face`                    |
| Form Recognizer            | `FormRecognizer`          |
| Ink Recognizer             | `InkRecognizer`           |

### <a name="search"></a>Keresés

| Szolgáltatás            | Altípus                  |
|--------------------|-----------------------|
| Bing – Automatikus kiegészítés   | `Bing.Autosuggest.v7` |
| Bing – Egyéni keresés | `Bing.CustomSearch`   |
| Bing – Entitáskeresés | `Bing.EntitySearch`   |
| Bing kereső        | `Bing.Search.v7`      |
| Bing – Helyesírás-ellenőrzés   | `Bing.SpellCheck.v7`  |

### <a name="speech"></a>Speech

| Szolgáltatás            | Altípus                 |
|--------------------|----------------------|
| Beszédszolgáltatások    | `SpeechServices`     |
| Beszédfelismerés | `SpeakerRecognition` |

### <a name="language"></a>Nyelv

| Szolgáltatás            | Altípus                |
|--------------------|---------------------|
| Űrlap megértése | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Szövegelemzés     | `TextAnalytics`     |
| Szövegfordítás   | `TextTranslation`   |

### <a name="decision"></a>Döntés

| Szolgáltatás           | Altípus               |
|-------------------|--------------------|
| Anomáliadetektor  | `AnomalyDetector`  |
| Tartalommoderátor | `ContentModerator` |
| Personalizer      | `Personalizer`     |

Az elérhető kognitív szolgáltatás "típusai" listáját az az [cognitiveservices Account List-kinds](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-kinds) paranccsal találja:

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Új erőforrás hozzáadása az erőforráscsoporthoz

Új Cognitive Services erőforrás létrehozásához és előfizetéséhez használja az az [cognitiveservices Account Create](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create) parancsot. Ezzel a paranccsal új számlázandó erőforrást adhat hozzá a korábban létrehozott erőforráscsoporthoz. Az új erőforrás létrehozásakor ismernie kell a használni kívánt szolgáltatás típusát, valamint az árképzési szintet (vagy SKU-t) és egy Azure-helyet:

Az alábbi paranccsal létrehozhat egy F0 (ingyenes) erőforrást az anomália-detektorhoz `anomaly-detector-resource` .

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]

## <a name="get-the-keys-for-your-resource"></a>Az erőforrás kulcsainak beolvasása

A parancssori felület (CLI) helyi telepítésére való bejelentkezéshez használja az az [login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) parancsot.

```azurecli-interactive
az login
```

A kognitív szolgáltatás erőforrásaihoz tartozó kulcsok beszerzéséhez használja az az [cognitiveservices Account Keys List](https://docs.microsoft.com/cli/azure/cognitiveservices/account/keys?view=azure-cli-latest#az-cognitiveservices-account-keys-list) parancsot.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Árképzési szintek és számlázás

A díjszabási szintek (és a felszámított összeg) a hitelesítési adatok használatával elküldött tranzakciók számától függenek. Az egyes díjszabási szintek a következőket határozzák meg:
* a másodpercenként engedélyezett tranzakciók maximális száma (TPS).
* a szolgáltatási funkciók a díjszabási szinten engedélyezve vannak.
* Az előre meghatározott mennyiségű tranzakció díja. Ezen összeg fölött a szolgáltatás [díjszabása](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) szerint külön díjat számítunk fel.

## <a name="get-current-quota-usage-for-your-resource"></a>Az erőforrás aktuális kvótájának beolvasása

Használja az az [cognitiveservices Account List-](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-usage) use parancsot a kognitív szolgáltatás erőforrásai használatának megszerzéséhez.

```azurecli-interactive
az cognitiveservices account list-usage \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --subscription subscription-name
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné egy Cognitive Services erőforrást, törölheti azt vagy az erőforráscsoportot. Az erőforráscsoport törlése a csoportban található egyéb erőforrásokat is törli.

Az erőforráscsoport és a hozzá tartozó erőforrások eltávolításához használja az az Group delete parancsot.

```azurecli-interactive
az group delete --name cognitive-services-resource-group
```

## <a name="see-also"></a>További információ

* [Kérelmek hitelesítése az Azure Cognitive Services](authentication.md)
* [Mi az Azure Cognitive Services?](Welcome.md)
* [Természetes nyelvi támogatás](language-support.md)
* [Docker-tárolók támogatása](cognitive-services-container-support.md)

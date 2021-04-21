---
title: Erőforrás Cognitive Services az Azure CLI használatával
titleSuffix: Azure Cognitive Services
description: A Azure Cognitive Services az Azure parancssori felülettel történő erőforrás-létrehozással és -előfizetéssel.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
keywords: cognitive services, kognitív intelligencia, kognitív megoldások, ai-szolgáltatások
ms.topic: quickstart
ms.date: 3/22/2021
ms.author: aahi
ms.openlocfilehash: 26e3b264b7268f7a9ffdb592beef7d76844646f5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789140"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>Rövid útmutató: Erőforrás-Cognitive Services létrehozása az Azure Command-Line Interface (CLI) használatával

Ebből a rövid útmutatóból első lépésekben Azure Cognitive Services Azure parancssori felület [(CLI) használatával.](/cli/azure/install-azure-cli)

Azure Cognitive Services rest API-kat és ügyféloldali kódtári SZOFTVEREKET is tartalmaznak, amelyek segítségével a fejlesztők kognitív intelligenciát építenek be alkalmazásokba közvetlen mesterséges intelligencia (AI) vagy adattudományi készségek vagy ismeretek nélkül. Azure Cognitive Services lehetővé teszi a fejlesztők számára, hogy kognitív funkciókat adjanak az alkalmazásaikhoz olyan kognitív megoldásokkal, amelyek látnak, hallanak, beszélnek, értenek, és még az érveléshez is hozzáfognak.

Cognitive Services azure-erőforrásokat az [](../azure-resource-manager/management/manage-resources-portal.md) Azure-előfizetésben létrehozott Azure-erőforrások képviselik. Az erőforrás létrehozása után használja a létrehozott kulcsokat és végpontot az alkalmazások hitelesítéséhez.

Ebből a rövid útmutatóból megtudhatja, hogyan regisztrálható az Azure Cognitive Services-ba, és hogyan hozhat létre egy egyszolgáltatású vagy többszolgáltatású előfizetéssel rendelkezik az Azure parancssori felület [(CLI) használatával.](/cli/azure/install-azure-cli) Ezeket a szolgáltatásokat [Azure-erőforrások](../azure-resource-manager/management/manage-resources-portal.md)képviselik, amelyek lehetővé teszik, hogy csatlakozzon egy vagy több Azure Cognitive Services API-hoz.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Előfeltételek

* Érvényes Azure-előfizetés – [Hozzon létre egyet](https://azure.microsoft.com/free/cognitive-services) ingyenesen.
* Az [Azure parancssori felület (CLI)](/cli/azure/install-azure-cli)

## <a name="install-the-azure-cli-and-sign-in"></a>Az Azure CLI telepítése és bejelentkezés

Telepítse az [Azure CLI-t.](/cli/azure/install-azure-cli) A parancssori felület helyi telepítésére való bejelentkezéshez futtassa [az az login](/cli/azure/reference-index#az_login) parancsot:

```azurecli-interactive
az login
```

A zöld Try **It (Próbálja** ki) gombbal is futtathatja ezeket a parancsokat a böngészőben.

## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Új erőforráscsoport Azure Cognitive Services létrehozása

Mielőtt létrehoz Cognitive Services erőforrást, az erőforrást egy Azure-erőforráscsoporttal kell létrehoznia. Új erőforrás létrehozásakor létrehozhat egy új erőforráscsoportot, vagy használhat egy meglévőt. Ez a cikk bemutatja, hogyan hozhat létre új erőforráscsoportot.

### <a name="choose-your-resource-group-location"></a>Válassza ki az erőforráscsoport helyét

Erőforrás létrehozásához szüksége lesz az előfizetéséhez elérhető Azure-helyek egyikén. Az elérhető helyek listáját az [az account list-locations](/cli/azure/account#az_account_list_locations) paranccsal lehet lekérni. A Cognitive Services több helyről is elérhetők. Válassza ki az Önhöz legközelebbi helyet, vagy tekintse meg, hogy mely helyek érhetők el a szolgáltatáshoz.

> [!IMPORTANT]
> * Jegyezd meg az Azure helyét, mert szüksége lesz rá a virtuális Azure Cognitive Services.
> * Egyes szolgáltatások Cognitive Services régiónként eltérőek lehetnek. További információ: [Azure-termékek régiónként.](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Az Azure-hely létrehozása után hozzon létre egy új erőforráscsoportot az Azure CLI-ban [az az group create paranccsal.](/cli/azure/group#az_group_create)

Az alábbi példában cserélje le az Azure-helyet az előfizetéséhez elérhető `westus2` Azure-helyek egyikével.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services-erőforrás létrehozása

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Kognitív szolgáltatás és tarifacsomag kiválasztása

Új erőforrás létrehozásakor szükség lesz a használni kívánt szolgáltatás "fajtája" és a kívánt tarifacsomag [(vagy](https://azure.microsoft.com/pricing/details/cognitive-services/) termékváltozat) használatára. Ezt és az egyéb információkat paraméterekként fogja használni az erőforrás létrehozásakor.

### <a name="multi-service"></a>Többszolgáltatású

| Szolgáltatás                    | Altípus                      |
|----------------------------|---------------------------|
| Több szolgáltatás. További [részletekért tekintse](https://azure.microsoft.com/pricing/details/cognitive-services/) meg a díjszabást tartalmazó oldalt.            | `CognitiveServices`     |


> [!NOTE]
> Az alábbi Cognitive Services közül sok ingyenes szinten próbálhatja ki a szolgáltatást. Az ingyenes szint az `F0` erőforrás termékváltozataként használható.

### <a name="vision"></a>Látás

| Szolgáltatás                    | Altípus                      |
|----------------------------|---------------------------|
| Computer Vision            | `ComputerVision`          |
| Custom Vision – Előrejelzés | `CustomVision.Prediction` |
| Custom Vision – Betanítás   | `CustomVision.Training`   |
| Arcfelismerés                       | `Face`                    |
| Form Recognizer            | `FormRecognizer`          |
| Ink Recognizer             | `InkRecognizer`           |

### <a name="speech"></a>Speech

| Szolgáltatás            | Altípus                 |
|--------------------|----------------------|
| Beszédfelismerési szolgáltatások    | `SpeechServices`     |
| Beszédfelismerés | `SpeakerRecognition` |

### <a name="language"></a>Nyelv

| Szolgáltatás            | Altípus                |
|--------------------|---------------------|
| Form Understanding | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Szövegelemzés     | `TextAnalytics`     |
| Szövegfordítás   | `TextTranslation`   |

### <a name="decision"></a>Döntés

| Szolgáltatás           | Altípus               |
|-------------------|--------------------|
| Anomáliadetektor  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizer      | `Personalizer`     |

Az elérhető Cognitive Services-szolgáltatások "fajtáit" az [az cognitiveservices account list-kinds](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_list_kinds) paranccsal találhatja meg:

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Új erőforrás hozzáadása az erőforráscsoporthoz

Új erőforrás létrehozásához és előfizetéshez Cognitive Services [az az cognitiveservices account create parancsot.](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_create) Ez a parancs egy új számlázható erőforrást ad hozzá a korábban létrehozott erőforráscsoporthoz. Az új erőforrás létrehozásakor szüksége lesz a használni kívánt szolgáltatás "fajtáját" a tarifacsomagra (vagy termékváltozatra) és egy Azure-helyre:

Létrehozhat egy F0 (ingyenes) erőforrást a anomáliadetektor nevű erőforráshoz `anomaly-detector-resource` az alábbi paranccsal.

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

## <a name="get-the-keys-for-your-resource"></a>Az erőforrás kulcsának lekért neve

A Command-Line Interface (CLI) helyi telepítésére való bejelentkezéshez használja az [az login](/cli/azure/reference-index#az_login) parancsot.

```azurecli-interactive
az login
```

Az [az cognitiveservices account keys list paranccsal](/cli/azure/cognitiveservices/account/keys#az_cognitiveservices_account_keys_list) szerezze be a Cognitive Service-erőforrás kulcsait.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Tarifacsomagok és számlázás

A tarifacsomagok (és a kiszámlázett összeg) a hitelesítési adatokkal küldött tranzakciók számán alapulnak. Minden tarifacsomag a következő értékeket határozza meg:
* a másodpercenként engedélyezett tranzakciók maximális száma (TPS).
* A tarifacsomagon belül engedélyezett szolgáltatási funkciók.
* A tranzakciók előre meghatározott számának költsége. Ha ezt az összeget meghaladja, további díjat számítunk fel a szolgáltatás [díjszabási](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) adataiban megadottak szerint.

## <a name="get-current-quota-usage-for-your-resource"></a>Az erőforrás aktuális kvótahasználatának lekért használata

Az [az cognitiveservices account list-usage](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_list_usage) paranccsal lekérte a Cognitive Service-erőforrás használatát.

```azurecli-interactive
az cognitiveservices account list-usage \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --subscription subscription-name
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni vagy eltávolítani szeretne egy Cognitive Services erőforrást, törölheti azt vagy az erőforráscsoportot. Az erőforráscsoport törlése a csoportban lévő többi erőforrást is törli.

Az erőforráscsoport és a hozzá tartozó erőforrások eltávolításához használja az az group delete parancsot.

```azurecli-interactive
az group delete --name cognitive-services-resource-group
```

## <a name="see-also"></a>Lásd még

* A **[biztonságos munka Azure Cognitive Services](authentication.md)** kérések hitelesítése a Cognitive Services.
* A **[What are Azure Cognitive Services?](./what-are-cognitive-services.md)** (Mik a Azure Cognitive Services? ? ) a különböző kategóriák listáját a Cognitive Services.
* A **[természetes nyelvi támogatással](language-support.md)** a támogatott természetes nyelvek listáját Cognitive Services meg.
* A **[Cognitive Services](cognitive-services-container-support.md)** használatának Cognitive Services lásd: A Cognitive Services használata tárolóként.
* A **[költségbecsléshez tekintse](plan-manage-costs.md)** meg a Cognitive Services tervezésével és kezelésével kapcsolatos Cognitive Services.

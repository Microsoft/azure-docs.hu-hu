---
title: 'Gyors útmutató: Azure Cognitive Services-erőforrás létrehozása ARM-sablonok használatával | Microsoft Docs'
description: Ismerkedjen meg Azure Resource Manager sablonnal egy Cognitive Services-erőforrás üzembe helyezéséhez.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 07/27/2020
ms.author: aahi
ms.custom: subject-armqs
ms.openlocfilehash: 9ecbd7778480d37fb0a0cf135d3cc5db48bf2add
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87323655"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-an-arm-template"></a>Rövid útmutató: Cognitive Services-erőforrás létrehozása ARM-sablonnal

Ebből a cikkből megtudhatja, hogyan hozhat létre és helyezhet üzembe egy Cognitive Services erőforrást egy Azure Resource Manager sablon (ARM-sablon) használatával. A több szolgáltatásból álló erőforrás a következőket teszi lehetővé:
* Több Azure-Cognitive Services elérése egyetlen kulccsal és végponttal.
* Konszolidálja a számlázási szolgáltatásokat a használt szolgáltatásokból.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cognitive-services-universalkey%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services).

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-cognitive-services-universalkey/) közül származik.

:::code language="json" source="~/quickstart-templates/101-cognitive-services-universalkey/azuredeploy.json" highlight="27-41":::

A sablonban egyetlen Azure-erőforrás van definiálva:
* [Microsoft. CognitiveServices/fiókok](https://docs.microsoft.com/azure/templates/microsoft.cognitiveservices/accounts): Cognitive Services erőforrást hoz létre.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. Kattintson az **üzembe helyezés az Azure-** ban gombra.

    [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cognitive-services-universalkey%2Fazuredeploy.json)

2. Írja be a következő értékeket.
    
    |Érték  |Leírás  |
    |---------|---------|
    | **Előfizetés** | Válasszon ki egy Azure-előfizetést. |
    | **Erőforráscsoport** | Válassza az **új létrehozása**lehetőséget, adjon meg egy egyedi nevet az erőforráscsoport számára, majd kattintson **az OK**gombra. |
    | **Régió** | Válasszon régiót.  Például: **USA keleti** régiója |
    | **Kognitív szolgáltatás neve** | A helyére írja be az erőforrás egyedi nevét. A telepítés érvényesítése után a következő szakaszban kell megadnia a nevet. |
    | **Hely** | Cserélje le a helyére a fent használt régiót. |
    | **SKU** | Az erőforrás [díjszabási szintje](https://azure.microsoft.com/pricing/details/cognitive-services/) . |
    
    :::image type="content" source="media/arm-template/universal-key-portal-template.png" alt-text="Erőforrás-létrehozási képernyő.":::

3. Válassza az **Áttekintés + létrehozás**, majd a **Létrehozás** lehetőséget. Miután az erőforrás sikeresen befejezte az üzembe helyezést, ki lesz emelve a **Ugrás erőforrásra** gomb.

# <a name="azure-cli"></a>[Azure CLI](#tab/CLI)

> [!NOTE]
> `az deployment group`a létrehozáshoz az Azure CLI 2,6-es vagy újabb verziójára lesz szükség. A verzió típusának megjelenítéséhez `az --version` . További információkért tekintse meg a [dokumentációt](https://docs.microsoft.com/cli/azure/deployment/group).

Futtassa a következő szkriptet az Azure parancssori felületének (CLI) használatával a [helyi gépen](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), vagy egy böngészőben a **TRY IT (kipróbálás** ) gombbal. Adja meg az új erőforráscsoport nevét és helyét (például `centralus` ), az ARM-sablont pedig Cognitive Services erőforrás üzembe helyezésére fogja használni. Jegyezze fel a használni kívánt nevet. Ezt később fogja használni a telepítés ellenőrzéséhez.


```azurecli-interactive
read -p "Enter a name for your new resource group:" resourceGroupName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cognitive-services-universalkey/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

---

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]


## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

# <a name="portal"></a>[Portál](#tab/portal)

Az üzembe helyezés befejeződése után az új erőforrás megjelenítéséhez kattintson az **Ugrás erőforrásra** gombra. Az erőforráscsoportot az alábbiak szerint is megtalálhatja:

1. Az **erőforráscsoportok** kiválasztása a bal oldali navigációs menüből.
2. Válassza ki az erőforráscsoport nevét.

# <a name="azure-cli"></a>[Azure CLI](#tab/CLI)

Az Azure CLI használatával futtassa a következő parancsfájlt, és adja meg a korábban létrehozott erőforráscsoport nevét.

```azurecli-interactive
echo "Enter the resource group where the Cognitive Services resource exists:" &&
read resourceGroupName &&
az cognitiveservices account list -g $resourceGroupName
```

---


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlése a csoportban található egyéb erőforrásokat is törli.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. Az Azure Portalon bontsa ki a bal oldalon a szolgáltatásmenüt, és válassza az **Erőforráscsoportok** lehetőséget az erőforráscsoportok listájának megjelenítéséhez.
2. Keresse meg a törölni kívánt erőforrást tartalmazó erőforráscsoportot.
3. Kattintson a jobb gombbal az erőforráscsoport listájára. Válassza az **Erőforráscsoport törlése** elemet, és erősítse meg a választását.

# <a name="azure-cli"></a>[Azure CLI](#tab/CLI)

Az Azure CLI használatával futtassa a következő parancsfájlt, és adja meg a korábban létrehozott erőforráscsoport nevét. 

```azurecli-interactive
echo "Enter the resource group name, for deletion:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="next-steps"></a>További lépések

* [Kérelmek hitelesítése az Azure Cognitive Services](authentication.md)
* [Mi az Azure Cognitive Services?](Welcome.md)
* [Természetes nyelvi támogatás](language-support.md)
* [Docker-tárolók támogatása](cognitive-services-container-support.md)

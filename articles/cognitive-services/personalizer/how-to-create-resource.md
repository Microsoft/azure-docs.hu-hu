---
title: Személyre szabott erőforrás létrehozása
description: Ebből a cikkből megtudhatja, hogyan hozhat létre személyre szabott erőforrást a Azure Portal minden visszajelzési hurokhoz.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0c75d917f1abe72af2f4aa56b0f67dbb7bcd24a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "91303556"
---
# <a name="create-a-personalizer-resource"></a>Személyre szabott erőforrás létrehozása

A személyre szabott erőforrás ugyanaz, mint a személyre szabott tanulási hurok. Minden egyes tulajdonos tartományhoz vagy tartalmi területhez létrejön egy erőforrás vagy egy tanulási hurok. Ne használjon több tartalmi területet ugyanabban a hurokban, mert ez megzavarja a tanulási ciklust, és gyenge előrejelzéseket biztosít.

Ha azt szeretné, hogy a személyre szabott tartalom legyen kiválasztva egy weblap több tartalmi területéhez, használjon egy másik tanulási ciklust.


## <a name="create-a-resource-in-the-azure-portal"></a>Erőforrás létrehozása az Azure Portalon

Hozzon létre egy személyre szabott erőforrást minden visszajelzési hurokhoz.

1. Jelentkezzen be [Azure Portalba](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Az előző hivatkozás a személyre szabási szolgáltatás **Létrehozás** lapjára lép.
1. Adja meg a szolgáltatás nevét, válassza ki az előfizetést, a helyet, az árképzési szintet és az erőforráscsoportot.

    > [!div class="mx-imgBorder"]
    > ![A Azure Portal használatával személyre szabott erőforrásokat hozhat létre, más néven tanulási ciklust.](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. Válassza a **Létrehozás** lehetőséget az erőforrás létrehozásához.

1. Az erőforrás üzembe helyezése után válassza az **Ugrás erőforrásra** gombot, és lépjen a személyre szabott erőforráshoz.

1. Válassza ki az erőforrás **gyors üzembe helyezés** lapját, majd másolja a végpont és a kulcs értékeit. A Rank és a jutalom API-k használatához mind az erőforrás-végpontot, mind a kulcsot kell használnia.

1. Válassza ki az új erőforrás **konfigurációs** lapját a [tanulási hurok konfigurálásához](how-to-settings.md).

## <a name="create-a-resource-with-the-azure-cli"></a>Erőforrás létrehozása az Azure CLI-vel

1. Jelentkezzen be az Azure CLI-be a következő paranccsal:

    ```azurecli-interactive
    az login
    ```

1. Hozzon létre egy erőforráscsoportot, amely a személyre szabott erőforrással használni kívánt összes Azure-erőforrás kezelésére szolgáló logikai csoport.


    ```azurecli-interactive
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. Hozzon létre egy új személyre szabott erőforrást, egy _tanulási ciklust_ a következő paranccsal egy meglévő erőforráscsoporthoz.

    ```azurecli-interactive
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```

    Ez egy JSON-objektumot ad vissza, amely tartalmazza az **erőforrás-végpontot**.

1. Használja az alábbi Azure CLI-parancsot az **erőforrás-kulcs** beszerzéséhez.

    ```azurecli-interactive
        az cognitiveservices account keys list \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group
    ```

    A Rank és a jutalom API-k használatához mind az erőforrás-végpontot, mind a kulcsot kell használnia.

## <a name="next-steps"></a>Következő lépések

* [Konfigurálás](how-to-settings.md) Személyre szabott tanulási hurok

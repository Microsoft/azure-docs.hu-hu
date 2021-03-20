---
title: LUIS-erőforrás létrehozása
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/20/2020
ms.author: aahi
ms.openlocfilehash: ee7fd384a198c5eff672b14b6cb479aac26cfe54
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95972512"
---
<a name="create-luis-resources"></a>

## <a name="create-luis-resources-in-the-azure-portal"></a>LUIS-erőforrások létrehozása a Azure Portalban

1. [Ezzel a hivatkozással](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) megkezdheti a Luis-erőforrások létrehozását a Azure Portalban.

1. Adja meg az összes szükséges beállítást:

    |Name|Cél|
    |--|--|
    |Előfizetés | Az erőforrásért fizetendő előfizetés.|
    |Erőforráscsoport| Az Ön által kiválasztott vagy létrehozott egyéni erőforráscsoport-név. Az erőforráscsoportok lehetővé teszik az Azure-erőforrások csoportosítását a hozzáféréshez és a felügyelethez.|
    |Name| A kiválasztott egyéni név. A szerzői műveletek és előrejelzési végpontok lekérdezéséhez a rendszer egyéni altartományként használja.|
    |Szerzői hely|A modellhez társított régió.|
    |A szerzői díjak szintjei|Meghatározza a másodpercenkénti és havi maximális tranzakciókat.|
    |Előrejelzés helye|A közzétett előrejelzési végpont futtatókörnyezetéhez társított régió.|
    |Előrejelzések díjszabási szintje|Meghatározza a másodpercenkénti és havi maximális tranzakciókat.|

    > [!div class="mx-imgBorder"]
    > [![Képernyőkép, amely az alapvető beállítások lapot mutatja a létrehozás alatt.](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png)](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png#lightbox)

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget, és várjon, amíg a rendszer létrehozza az erőforrást.
1. Miután mindkét erőforrás létrejött, még mindig a Azure Portalban, válassza ki az új szerzői erőforrást. Ezután válassza a **kulcsok és végpont** lehetőséget a szerzői **VÉGPONT URL-címének** és **kulcsának** beszerzéséhez programozott módon.

> [!TIP]
> Az erőforrások használatához a LUIS portálon [rendelje hozzá az erőforrásokat](../luis-how-to-azure-subscription.md#assign-an-authoring-resource-in-the-luis-portal-for-all-apps).

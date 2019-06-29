---
title: Erőforrás és kulcskezelési – QnA Maker
titleSuffix: Azure Cognitive Services
description: A QnA Maker szolgáltatást, előfizetési kulcsok és végpont kulcsokat kétféle foglalkozik.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/04/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b9be1db9be1d4dd57994e101c07ed430425a5912
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447425"
---
# <a name="how-to-manage-keys-in-qna-maker"></a>A QnA Maker kulcsok kezelése

A QnA Maker szolgáltatás kezelje a kulcs, kétféle **előfizetési kulcsok** és **végpont kulcsok**.

![Kulcskezelés](../media/qnamaker-how-to-key-management/key-management.png)

1. **Előfizetői azonosítók**: Ezekkel a kulcsokkal való hozzáférést a [QnA Maker management szolgáltatás API-k](https://go.microsoft.com/fwlink/?linkid=2092179). Ezen API-k lehetővé teszik a Tudásbázis szerkesztéséhez.  

2. **Végpont kulcsok**: Ezekkel a kulcsokkal kapott választ a felhasználó kérdés Tudásbázis-végpontot. Általában akkor érdemes használnia ezt a végpontot a csevegőrobot, vagy az ügyfél alkalmazáskóddal, amelyhez a QnA Maker szolgáltatást használ fel.
 
## <a name="subscription-keys"></a>Előfizetői azonosítók
Megtekintheti, és az előfizetési kulcsok az Azure Portalon, ahol létrehozta a QnA Maker erőforrás alaphelyzetbe állítása. 
1. Nyissa meg a QnA Maker erőforrás az Azure Portalon.

    ![Rendszererőforrás-lista a QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Lépjen a **kulcsok**.

    ![Előfizetői azonosító](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="endpoint-keys"></a>Végpont kulcsok

Végpont kulcsok kezelhető a [QnA Maker portal](https://qnamaker.ai).

1. Jelentkezzen be a [QnA Maker portal](https://qnamaker.ai), nyissa meg a profil, és kattintson **Szolgáltatásbeállítások**.

    ![Végponti kulcs](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Megtekintheti, vagy visszaállíthatja a kulcsokat.

    ![Endpoint-kezelő](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Frissítse a kulcsokat, ha úgy érzi, hogy sérült a biztonsága. Ez lehet szükség az ügyfélalkalmazás vagy a robot kód a megfelelő módosításokat.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis létrehozása egy másik nyelven](./language-knowledge-base.md)

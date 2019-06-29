---
title: A nem angol nyelvű Tudásbázis – QnA Maker
titleSuffix: Azure Cognitive Services
description: A QnA Maker számos nyelvet támogatja a Tudásbázis-tartalmat. Azonban a QnA Maker mindegyikük számára lefoglalt egyetlen nyelvet. A létrehozott egy adott QnA Maker szolgáltatás célzó első Tudásbázis adott nyelv beállítása.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: diberry
ms.openlocfilehash: 1203748b43ea5911d4717f451bca2cbe82753386
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447419"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Nyelvi támogatás a Tudásbázis-tartalmat a QnA Maker
A QnA Maker számos nyelvet támogatja a Tudásbázis-tartalmat. Azonban a QnA Maker mindegyikük számára lefoglalt egyetlen nyelvet. A létrehozott egy adott QnA Maker szolgáltatás célzó első Tudásbázis adott nyelv beállítása. Lásd: [Itt](../Overview/languages-supported.md) támogatott nyelvek listáját.

A nyelvet a rendszer automatikusan felismeri a tartalom kibontása közben adatforrások. Ha a szolgáltatás hoz létre egy új QnA Maker szolgáltatást és a egy új Tudásbázis, ellenőrizheti, hogy a nyelvi megfelelően van beállítva.

1. Keresse meg a [az Azure Portal](https://portal.azure.com/).

2. Válassza ki **erőforráscsoportok** , és keresse meg az erőforráscsoport, amelyben a QnA Maker szolgáltatást üzembe helyezte, és válassza ki a **Azure Search** erőforrás.

    ![Válassza ki az Azure Search-erőforrás](../media/qnamaker-how-to-language-kb/select-azsearch.png)

3. Válassza ki a **testkb** index. Az Azure Search-index mindig az elsőt létrehozott és mentett tartalmát a szolgáltatás tudásbázisok tartalmaz. 

    ![Válassza ki a teszt KB](../media/qnamaker-how-to-language-kb/select-testkb.png)

4. Válassza ki **mezők** testkb részleteit bemutató szakaszt.

    ![Mezők kiválasztása](../media/qnamaker-how-to-language-kb/selectfields.png)

5. Jelölje be a **Analyzer** nyelvre vonatkozó részletek megtekintéséhez.

    ![Válassza ki az elemző](../media/qnamaker-how-to-language-kb/select-analyzer.png)

6. Keresse meg, hogy az elemző egy adott nyelvre van beállítva. Ez a nyelv a rendszer automatikusan észlelte a Tudásbázis létrehozási lépés során. Ez a nyelv nem módosítható, az erőforrás létrehozása után.

    ![Kijelölt elemző](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A QnA robotot létrehozása az Azure Bot Service](../Tutorials/create-qna-bot.md)

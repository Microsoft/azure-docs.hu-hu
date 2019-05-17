---
title: 'Gyors útmutató: A QnA Maker első válasz a Tudásbázis - REST, a Python-'
titlesuffix: Azure Cognitive Services
description: A Python REST-alapú rövid útmutató végigvezeti egy válasz lekérése a Tudásbázis programozott módon.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 154b02cba41d685803d06bc3c9c24db2779434e5
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792123"
---
# <a name="get-answers-to-a-question-from-a-knowledge-base-with-python"></a>Válaszok a kérdést a Tudásbázis Python használatával

Ez a rövid útmutató végigvezeti programozott módon első válasz a QnA Maker közzétett Tudásbázis. A Tudásbázis kérdéseket tartalmaz, valamint választ ad a [adatforrások](../Concepts/data-sources-supported.md) például – gyakori kérdések. A [kérdés](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) a QnA Maker szolgáltatásnak továbbítja. A [válasz](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) tartalmazza a top előre jelzett választ. 

## <a name="prerequisites"></a>Előfeltételek

* [A Python 3.6-os vagy újabb](https://www.python.org/downloads/)
* [Visual Studio Code](https://code.visualstudio.com/)
* Rendelkeznie kell [QnA Maker-szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) is. A kulcs lekéréséhez válassza ki **kulcsok** alatt **erőforrás-kezelés** az Azure irányítópultján a QnA Maker erőforrás. 
* **Közzététel** beállítások lapon. Közzétett Tudásbázis nem rendelkezik, hozzon létre egy üres Tudásbázis, akkor a Tudásbázis importálja a **beállítások** lapon, majd közzéteheti. Letöltheti és használhatja [Ez a alapvető Tudásbázis](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv). 

    A közzétételi oldalon beállítások közé tartozik a POST útvonal, gazdagép értékét, és EndpointKey értékkel. 

    ![Közzétételi beállítások](../media/qnamaker-quickstart-get-answer/publish-settings.png)

Ez a rövid útmutató kódja megtalálható a [ https://github.com/Azure-Samples/cognitive-services-qnamaker-python ](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/tree/master/documentation-samples/quickstarts/get-answer) tárház. 

## <a name="create-a-python-file"></a>Hozzon létre egy Python-fájlt

Nyissa meg a VSCode, és hozzon létre egy új fájlt `get-answer-3x.py`.

## <a name="add-the-required-dependencies"></a>A szükséges függőségek hozzáadása

Felső részén a `get-answer-3x.py` fájlt, adja hozzá a projekthez szükséges függőséget:

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=1-2 "Add the required dependencies")]

A gazdagép és a route különböznek, hogyan jelennek meg, mint a **közzététel** lapot. Ennek oka az, a python-kódtár nem teszi lehetővé bármilyen útválasztás a gazdagépen. Az útválasztás, mely megjelenik az **közzététel** lapon, a gazdagép részeként került, az útvonal.

## <a name="add-the-required-constants"></a>A szükséges konstansok hozzáadása

Adja hozzá a szükséges állandókat a QnA Maker eléréséhez. A rendszer ezeket az értékeket a **közzététel** lapon a Tudásbázis közzététele után. 

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=5-25 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>Adjon hozzá egy POST-kérés küldése a kérdés és válasz

A következő kódot egy HTTPS-kérést küld a QnA Maker API, a kérdés küldeni a Tudásbázis és a választ kap:

[!code-python[Add a POST request to send question to knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=27-48 "Add a POST request to send question to knowledge base")]

A `Authorization` fejléc értékének tartalmazza a karakterláncot `EndpointKey`. 

## <a name="run-the-program"></a>A program futtatása

A program futtatása a parancssorból. A kérelem automatikusan elküldi a QnA Maker API, majd a konzolablakban nyomtatási.

Futtassa a fájlt:

```bash
python get-answer-3x.py
```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)] 

Tudjon meg többet a [kérelem](../how-to/metadata-generateanswer-usage.md#generateanswer-request) és [válasz](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](https://go.microsoft.com/fwlink/?linkid=2092179)

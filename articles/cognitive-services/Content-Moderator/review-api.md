---
title: Felülvizsgálatok, munkafolyamatok és feladatok – fogalmak Content Moderator
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megismerheti a felülvizsgálati eszköz alapvető fogalmait; felülvizsgálatok, munkafolyamatok és feladatok.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 69a66c49d64ab9ba589050e1f20399f7f193b76e
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2020
ms.locfileid: "92912956"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>Tartalom-moderálási felülvizsgálatok, munkafolyamatok és feladatok

Content Moderator kombinálja a gépi támogatással rendelkező moderálást emberi-in-the-loop képességekkel, hogy optimális moderálási folyamatot hozzon létre a valós forgatókönyvek esetében. Ez a felhőalapú [felülvizsgálati eszközön](https://contentmoderator.cognitive.microsoft.com)keresztül történik. Ebben az útmutatóban megismerheti a felülvizsgálati eszköz alapvető fogalmait: értékelések, munkafolyamatok és feladatok.

## <a name="reviews"></a>Felülvizsgálatok

A felülvizsgálat során a rendszer feltölti a tartalmat a felülvizsgálati eszközre, és a **felülvizsgálat** lapon jelenik meg. Innen a felhasználók módosíthatják az alkalmazott címkéket, és szükség szerint alkalmazhatják a saját egyéni címkéit. Amikor egy felhasználó elküld egy felülvizsgálatot, az eredményeket egy megadott visszahívási végpontra küldi a rendszer, és eltávolítja a tartalmat a helyről.

![Tekintse meg az eszköz webhelyét böngészőben, a felülvizsgálat lapon](./Review-Tool-user-Guide/images/image-workflow-review.png)

Az áttekintések létrehozásával kapcsolatos lépésekért tekintse meg a [felülvizsgálati eszköz útmutatót](./review-tool-user-guide/review-moderated-images.md) , vagy tekintse meg a [REST API útmutatót](./try-review-api-review.md) , amelyből megtudhatja, hogyan teheti meg a programozott módon.

## <a name="workflows"></a>Munkafolyamatok

A munkafolyamat egy felhőalapú, testreszabott szűrő a tartalomhoz. A munkafolyamatok számos szolgáltatáshoz kapcsolódhatnak, hogy különböző módokon szűrje a tartalmakat, majd a megfelelő műveletet hajtsa végre. A Content Moderator-összekötővel a munkafolyamatok automatikusan alkalmazhatnak moderálási címkéket, és az elküldött tartalommal hozhatnak létre értékeléseket.

### <a name="view-workflows"></a>Munkafolyamatok megtekintése

A meglévő munkafolyamatok megtekintéséhez nyissa meg a [felülvizsgálati eszközt](https://contentmoderator.cognitive.microsoft.com/) , és válassza a **Beállítások**  >  **munkafolyamatok** lehetőséget.

![Alapértelmezett munkafolyamat](images/default-workflow-listed.PNG)

A munkafolyamatokat JSON-karakterláncként lehet teljes mértékben leképezni, ami programozott módon elérhetővé teszi őket. Ha a munkafolyamat **szerkesztési** lehetőségét választja, majd kiválasztja a **JSON** fület, akkor egy JSON-kifejezés jelenik meg, például az alábbiak szerint:

```json
{
    "Type": "Logic",
    "If": {
        "ConnectorName": "moderator",
        "OutputName": "isAdult",
        "Operator": "eq",
        "Value": "true",
        "Type": "Condition"
        },
    "Then": {
    "Perform": [
    {
        "Name": "createreview",
        "CallbackEndpoint": null,
        "Tags": []
    }
    ],
    "Type": "Actions"
    }
}
```

A munkafolyamatok létrehozásának és használatának megkezdéséhez tekintse meg az [eszköz áttekintése útmutatót](./review-tool-user-guide/workflows.md) , vagy tekintse meg a [REST API útmutatót](./try-review-api-workflow.md) , amelyből megtudhatja, hogyan teheti meg a programozott módon.

## <a name="jobs"></a>Feladatok

A moderálási feladatok egyfajta burkolóként szolgálnak a tartalom moderálása, a munkafolyamatok és a felülvizsgálatok működéséhez. A feladat átvizsgálja a tartalmat a Content Moderator képmoderálás API-val vagy szöveges moderálási API-val, majd ellenőrzi a kijelölt munkafolyamattal. A munkafolyamat eredményei alapján előfordulhat, hogy nem hozza létre a felülvizsgálati [eszköz](./review-tool-user-guide/human-in-the-loop.md)tartalmának felülvizsgálatát. Noha mind a felülvizsgálatok, mind a munkafolyamatok létrehozhatók és konfigurálhatók a megfelelő API-kkal, a feladatok API lehetővé teszi a teljes folyamat részletes jelentésének beszerzését (amely a megadott visszahívási végpontnak küldhető el).

A feladatok használatának megkezdéséhez tekintse meg a [REST API útmutatót](./try-review-api-job.md) .

## <a name="next-steps"></a>Következő lépések

* Tesztelje a [feladatok API-konzolját](try-review-api-job.md), és használja a REST API-kód mintáit. Ha már ismeri a Visual studiót és a C#-ot, tekintse meg a [Jobs .net](moderation-jobs-quickstart-dotnet.md)gyors útmutatóját is. 
* Felülvizsgálatok esetén Ismerkedjen meg a [felülvizsgálati API-konzollal](try-review-api-review.md), és használja a REST API-kód mintáit. Ezután tekintse meg a [.net](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)gyors útmutatójának értékelések szakaszát.
* Videós [felülvizsgálatok](video-reviews-quickstart-dotnet.md)esetén használja a videó-felülvizsgálati útmutatót, és Ismerje meg, hogyan [adhat hozzá átiratokat a videó felülvizsgálatához](video-transcript-reviews-quickstart-dotnet.md).
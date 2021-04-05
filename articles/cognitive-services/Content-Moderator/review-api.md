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
ms.openlocfilehash: 43c39c40af6e02861211a8666fefa57c34072f32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96905196"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>Tartalom-moderálási felülvizsgálatok, munkafolyamatok és feladatok

Content Moderator kombinálja a gépi támogatással rendelkező moderálást emberi-in-the-loop képességekkel, hogy optimális moderálási folyamatot hozzon létre a valós forgatókönyvek esetében. Ez a felhőalapú [felülvizsgálati eszközön](https://contentmoderator.cognitive.microsoft.com)keresztül történik. Ebben az útmutatóban megismerheti a felülvizsgálati eszköz alapvető fogalmait: értékelések, munkafolyamatok és feladatok.

## <a name="reviews"></a>Felülvizsgálatok

A felülvizsgálat során a rendszer feltölti a tartalmat a felülvizsgálati eszközre. Megtekintheti a tartalom típusára kattintva az irányítópult **Áttekintés** lapján. A felülvizsgálati képernyőn módosíthatja az alkalmazott címkéket, és szükség szerint alkalmazhatja a saját egyéni címkéit. A felülvizsgálat elküldésekor a rendszer elküldi az eredményeket egy megadott visszahívási végpontnak, és eltávolítja a tartalmat a helyről.

> [!div class="mx-imgBorder"]
> ![A felülvizsgálat legördülő menü kiemelve. A következő tartalomtípusokat jeleníti meg: képek, szövegek és videók.](./Review-Tool-user-Guide/images/review-tab.png)

### <a name="manage-reviews"></a>Értékelések kezelése

Az irányítópulton navigáljon a **rendszergazda**  ->  **kezelése felülvizsgálatok** elemre a felügyeleti képernyő megtekintéséhez. Itt láthatja az összes felülvizsgálat listáját (függőben és befejezve).

Az egyes felülvizsgálatokban a három ponttal elvégezhető **műveletek** gomb segítségével megtekintheti a felülvizsgálati képernyőt, vagy megvizsgálhatja a felülvizsgálat előzményeit.

> [!div class="mx-imgBorder"]
> ![Az eszköz webhelyének áttekintése a felülvizsgálati képernyőn](./Review-Tool-user-Guide/images/manage-reviews.png)

A **keresési** eszköztár segítségével számos különböző kategóriába rendezheti az értékeléseket, például a felülvizsgálati állapotot, a címkéket, a tartalomtípust, az alcsapatot, a hozzárendelt felhasználókat és a létrehozott/módosított dátumot.

> [!div class="mx-imgBorder"]
> ![Megjelenik a keresési eszköztár. Különböző kombinált listákat tartalmaz a keresési feltételek megadásához, például a felülvizsgálati állapotot és a címkéket.](./Review-Tool-user-Guide/images/review-search.png)

Tekintse át az útmutató a [felülvizsgálati útmutatóhoz című útmutatót](./review-tool-user-guide/review-moderated-images.md) , vagy tekintse meg az [API-konzol útmutatóját](./try-review-api-review.md) , amelyből megtudhatja, hogyan teheti meg a programozott módon.

## <a name="workflows"></a>Munkafolyamatok

A munkafolyamat egy felhőalapú, testreszabott szűrő a tartalomhoz. A munkafolyamatok számos szolgáltatáshoz kapcsolódhatnak, hogy különböző módokon szűrje a tartalmakat, majd a megfelelő műveletet hajtsa végre. A Content Moderator-összekötővel a munkafolyamatok automatikusan alkalmazhatnak moderálási címkéket, és az elküldött tartalommal hozhatnak létre értékeléseket.

### <a name="view-workflows"></a>Munkafolyamatok megtekintése

A meglévő munkafolyamatok megtekintéséhez nyissa meg a [felülvizsgálati eszközt](https://contentmoderator.cognitive.microsoft.com/) , és válassza a **felügyeleti**  >  **munkafolyamatok** lehetőséget.

> [!div class="mx-imgBorder"]
> ![Alapértelmezett munkafolyamat](images/default-workflow-list.png)

A munkafolyamatok JSON-karakterláncként vannak definiálva, ami programozott módon elérhetővé teszi őket. Ha a munkafolyamat **szerkesztési** lehetőségét választja, majd kiválasztja a **JSON** fület, akkor egy JSON-kifejezés jelenik meg, például az alábbiak szerint:

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

A munkafolyamatok létrehozásának és használatának megkezdéséhez tekintse meg az [eszköz áttekintése útmutatót](./review-tool-user-guide/workflows.md) , vagy tekintse meg az [API-konzol útmutatóját](./try-review-api-workflow.md) , amelyből megtudhatja, hogyan teheti meg a programozott módon.

## <a name="jobs"></a>Feladatok

A moderálási feladatok egyfajta burkolóként szolgálnak a tartalom moderálása, a munkafolyamatok és az értékelések működéséhez. A feladat átvizsgálja a tartalmat a Content Moderator képmoderálás API-val vagy szöveges moderálási API-val, majd ellenőrzi a kijelölt munkafolyamattal. A munkafolyamat eredményei alapján előfordulhat, hogy nem hozza létre a felülvizsgálati [eszköz](./review-tool-user-guide/human-in-the-loop.md)tartalmának felülvizsgálatát. Noha mind a felülvizsgálatok, mind a munkafolyamatok létrehozhatók és konfigurálhatók a megfelelő API-kkal, a feladatok API lehetővé teszi a teljes folyamat részletes jelentésének beszerzését (amely a megadott visszahívási végpontnak küldhető el).

A feladatok használatának megkezdéséhez tekintse meg az [API-konzol útmutatóját](./try-review-api-job.md) .

## <a name="next-steps"></a>Következő lépések

* Tesztelje a [feladatok API-konzolját](try-review-api-job.md), és használja a REST API-kód mintáit. Ha már ismeri a Visual studiót és a C#-ot, tekintse meg a [Jobs .net](moderation-jobs-quickstart-dotnet.md)gyors útmutatóját is. 
* Felülvizsgálatok esetén Ismerkedjen meg a [felülvizsgálati API-konzollal](try-review-api-review.md), és használja a REST API-kód mintáit. Ezután tekintse meg a [.net](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)gyors útmutatójának értékelések szakaszát.
* Videós [felülvizsgálatok](video-reviews-quickstart-dotnet.md)esetén használja a videó-felülvizsgálati útmutatót, és Ismerje meg, hogyan [adhat hozzá átiratokat a videó felülvizsgálatához](video-transcript-reviews-quickstart-dotnet.md).
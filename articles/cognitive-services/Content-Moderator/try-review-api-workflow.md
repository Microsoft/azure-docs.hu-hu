---
title: Moderálási munkafolyamatok definiálása a REST API-konzollal – Content Moderator
titleSuffix: Azure Cognitive Services
description: Az Azure Content Moderator Review API-k segítségével egyéni munkafolyamatokat és küszöbértékeket határozhat meg a tartalmi szabályzatok alapján.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 79749533d636f4b73ff3bef6b12d9e842ac485ea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96905166"
---
# <a name="define-and-use-moderation-workflows-api-console"></a>Moderálási munkafolyamatok definiálása és használata (API-konzol)

A munkafolyamatok olyan felhőalapú testreszabott szűrők, amelyek segítségével hatékonyabban kezelheti a tartalmakat. A munkafolyamatok számos szolgáltatáshoz kapcsolódhatnak, hogy különböző módokon szűrje a tartalmakat, majd a megfelelő műveletet hajtsa végre. Ez az útmutató bemutatja, hogyan használhatja a munkafolyamat REST API-ját az API-konzolon munkafolyamatok létrehozásához és használatához. Miután megértette az API-k szerkezetét, könnyedén elvégezheti ezeket a hívásokat a REST-kompatibilis platformokra.

## <a name="prerequisites"></a>Előfeltételek

- Jelentkezzen be, vagy hozzon létre egy fiókot a Content Moderator [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com/) webhelyén.

## <a name="create-a-workflow"></a>Munkafolyamat létrehozása

Munkafolyamatok létrehozásához vagy frissítéséhez lépjen a **[munkafolyamat – létrehozás vagy frissítés API-](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** hivatkozás lapra, és válassza ki a kulcsfontosságú régióhoz tartozó gombot. A régiót a végpont URL-címében találja a [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com/) **hitelesítő adatok** lapján. Ezzel elindítja az API-konzolt, ahol könnyedén hozhat létre és futtathat REST API hívásokat.

![Munkafolyamat – oldal régiójának létrehozása vagy frissítése](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Adja meg a REST-hívás paramétereit

Adja meg a **Team**, a **Workflowname** és a **OCP-APIM-Subscription-Key** értékeket:

- **csapat**: a [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com/) fiókjának beállításakor létrehozott csoport azonosítója (a felülvizsgálati eszköz hitelesítő adatai képernyő **azonosító** mezőjében található).
- **workflowname**: a hozzáadandó új munkafolyamat neve (vagy egy meglévő név, ha frissíteni kíván egy meglévő munkafolyamatot).
- **OCP-APIM-előfizetés-kulcs**: a Content moderator kulcsa. Ezt a kulcsot a [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com) **Beállítások** lapján találja.

![Munkafolyamat – konzol lekérdezési paramétereinek és fejlécének létrehozása vagy frissítése](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>Munkafolyamat-definíció megadása

1. A **kérelem törzse** mező szerkesztésével adja meg a JSON-kérést a **Leírás** és a **típus** részleteivel ( `Image` vagy `Text` ).
2. A **kifejezés** mezőben másolja az alapértelmezett munkafolyamat JSON-kifejezését. A végső JSON-sztringnek így kell kinéznie:

```json
{
  "Description":"<A description for the Workflow>",
  "Type":"Text",
  "Expression":{
    "Type":"Logic",
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isAdult",
      "Operator":"eq",
      "Value":"true",
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    }
  }
}
```

> [!NOTE]
> Az API használatával egyszerű, összetett és akár beágyazott kifejezéseket is meghatározhat munkafolyamataihoz. A [munkafolyamat-létrehozási vagy frissítési](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) dokumentáció példákat tartalmaz összetettebb logikára.

### <a name="submit-your-request"></a>A kérés elküldése
  
Kattintson a **Küldés** gombra. Ha a művelet sikeres, a **Válasz állapota** `200 OK` , a **válasz tartalma** mező pedig megjelenik `true` .

### <a name="examine-the-new-workflow"></a>Vizsgálja meg az új munkafolyamatot

A [felülvizsgálati eszközben](https://contentmoderator.cognitive.microsoft.com/)válassza a **Beállítások**  >  **munkafolyamatok** lehetőséget. Az új munkafolyamatnak meg kell jelennie a listában.

![A munkafolyamatok listájának áttekintése](images/workflow-console-new-workflow.PNG)

Válassza ki a munkafolyamat **szerkesztési** beállítását, és nyissa meg a **tervező** lapot. Itt látható a JSON-logika intuitív ábrázolása.

![Tervező lap a kiválasztott munkafolyamathoz](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>Munkafolyamat részleteinek beolvasása

Egy meglévő munkafolyamat részleteinek lekéréséhez nyissa meg a **[munkafolyamat-beolvasás API-](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** referenciát, és válassza ki a régióhoz tartozó gombot (az a régió, amelyben a kulcsot felügyeli).

![Munkafolyamat – régió kiválasztásának beolvasása](images/test-drive-region.png)

Adja meg a REST-hívás paramétereit a fenti szakaszban leírtak szerint. Győződjön meg arról, hogy ez az idő a **workflowname** egy meglévő munkafolyamat neve.

![Lekérdezési paraméterek és fejlécek lekérése](images/workflow-get-default.PNG)

Kattintson a **Küldés** gombra. Ha a művelet sikeres, a **Válasz állapota** `200 OK` , a **válasz tartalma** pedig JSON formátumban jeleníti meg a munkafolyamatot, a következő példához hasonlóan:

```json
{
  "Name":"default",
  "Description":"Default",
  "Type":"Image",
  "Expression":{
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isadult",
      "Operator":"eq",
      "Value":"true",
      "AlternateInput":null,
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "Subteam":null,
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    },
    "Else":null,
    "Type":"Logic"
  }
}
```

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan használhatja a [tartalmi moderálási feladatokkal](try-review-api-job.md)rendelkező munkafolyamatokat.
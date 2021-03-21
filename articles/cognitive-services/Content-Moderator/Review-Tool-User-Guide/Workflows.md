---
title: Tartalom-munkafolyamatok definiálása és használata a felülvizsgálati eszközön – Content Moderator
titleSuffix: Azure Cognitive Services
description: Az Azure Content Moderator Munkafolyamat-tervezővel egyéni munkafolyamatokat és küszöbértékeket adhat meg a tartalmi szabályzatok alapján.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: pafarley
ms.openlocfilehash: 6eb2a2d2762b60a12bb9a24b92e2edae4b846cd1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96904142"
---
# <a name="define-and-use-moderation-workflows"></a>Moderálási munkafolyamatok definiálása és használata

Ebből az útmutatóból megtudhatja, hogyan állíthatja be és használhatja a [munkafolyamatokat](../review-api.md#workflows) a [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com) webhelyén. A munkafolyamatok olyan felhőalapú testreszabott szűrők, amelyek segítségével hatékonyabban kezelheti a tartalmakat. A munkafolyamatok számos szolgáltatáshoz kapcsolódhatnak, hogy különböző módokon szűrje a tartalmakat, majd a megfelelő műveletet hajtsa végre. Ez az útmutató bemutatja, hogyan használhatja a Content Moderator-összekötőt (amely alapértelmezés szerint megtalálható) a tartalom szűréséhez és az emberi felülvizsgálatok egy tipikus moderálási forgatókönyvben való beállításához.

## <a name="create-a-new-workflow"></a>Új munkafolyamat létrehozása

Lépjen a [Content moderator felülvizsgálati eszközre](https://contentmoderator.cognitive.microsoft.com/) , és jelentkezzen be. A **Beállítások** lapon válassza a **munkafolyamatok** lehetőséget.

![Munkafolyamatok beállítása](images/2-workflows-0.png)

A következő képernyőn válassza a **munkafolyamat hozzáadása** elemet.

![Munkafolyamat hozzáadása](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>Név és leírás kiosztása

Nevezze el a munkafolyamatot, adjon meg egy leírást, és válassza ki, hogy a munkafolyamat képeket vagy szöveget fog-e kezelni.

![Munkafolyamat neve és leírása](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>Értékelési feltételek meghatározása

A következő képernyőn lépjen az **IF** szakaszra. A felső legördülő menüben válassza a **feltétel** elemet. Ez lehetővé teszi a munkafolyamat által végrehajtandó feltétel konfigurálását. Ha több feltételt szeretne használni, válassza a **kombináció** helyet. 

Ezután válasszon egy összekötőt. Ez a példa **Content moderator** használ. A választott összekötőtől függően különböző beállításokat fog kapni az adatkimenethez. További összekötők beállításának megismeréséhez tekintse meg az eszközök áttekintése című útmutató [Összekötők](./configure.md#connectors) szakaszát.

![Munkafolyamat-összekötő kiválasztása](images/image-workflow-connect-to.PNG)

Válassza ki a használni kívánt kimenetet, és adja meg a feltételeket, hogy ellenőrizni lehessen.

![Munkafolyamat-feltétel definiálása](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>A művelet megadása

Lépjen a következő **szakaszra** , ahol kiválaszthat egy műveletet. Az alábbi példa egy rendszerkép-áttekintést hoz létre, és hozzárendel egy címkét. Opcionálisan hozzáadhat egy alternatív (más) útvonalat is, és beállíthat egy műveletet is.

![Munkafolyamat-művelet definiálása](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>A munkafolyamat mentése

Jegyezze fel a munkafolyamat nevét; a (z) munkafolyamat-API-val való moderálási feladatok elindításához a névnek kell megfelelnie (lásd alább). Végül mentse a munkafolyamatot a lap tetején található **Save (Mentés** ) gomb használatával.

## <a name="test-the-workflow"></a>A munkafolyamat tesztelése

Most, hogy definiált egy egyéni munkafolyamatot, tesztelje a minta tartalmával. Nyissa meg a **munkafolyamatokat** , és válassza ki a megfelelő **végrehajtási munkafolyamat** gombot.

![Munkafolyamat-teszt](images/image-workflow-execute.PNG)

Mentse a [mintaképet](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg) a helyi meghajtóra. Ezután válassza a **fájl (ok) kiválasztása** lehetőséget, és töltse fel a rendszerképet a munkafolyamatba.

![Egy, a képre kiszabott idézettel rendelkező Runner](images/sample-text.jpg)

### <a name="track-progress"></a>Követés állapota

A munkafolyamat állapotát a következő előugró ablakban tekintheti meg.

![Munkafolyamat-végrehajtás nyomon követése](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>Munkafolyamat-művelet ellenőrzése

Lépjen a **képek** lapra az **Áttekintés** területen, és ellenőrizze, hogy van-e újonnan létrehozott rendszerkép-áttekintés.

![Rendszerképek felülvizsgálata](images/image-workflow-review.PNG)

## <a name="next-steps"></a>Következő lépések

Ebből az útmutatóból megtudhatta, hogyan állíthatja be és használhatja a moderációs munkafolyamatokat a Content Moderator [felülvizsgálati eszközről](https://contentmoderator.cognitive.microsoft.com). Következő lépésként tekintse meg az [API-konzol útmutatóját](../try-review-api-workflow.md) , amelyből megtudhatja, hogyan hozhat létre programozott módon a munkafolyamatokat.

---
title: Tartalom-felülvizsgálatok használata a felülvizsgálati eszközön – Content Moderator
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogy a felülvizsgálati eszköz hogyan teszi lehetővé az emberi moderátorok számára a képek áttekintését egy webes portálon.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: 77d7b8a0bf4d7fe9a94a61ea3f2f4279246ffb69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96903887"
---
# <a name="create-human-reviews"></a>Emberi felülvizsgálatok létrehozása

Ebből az útmutatóból megtudhatja, hogyan állíthat be [felülvizsgálatokat a felülvizsgálati](../review-api.md#reviews) eszköz webhelyén. A értékelések az emberi moderátorok számára az értékelés céljából tárolják és jelenítik meg a tartalmakat. A moderátorok módosíthatják az alkalmazott címkéket, és szükség szerint alkalmazhatják a saját egyéni címkéit. Amikor a felhasználó befejezi a felülvizsgálatot, a rendszer elküldi az eredményeket egy megadott visszahívási végpontnak, és eltávolítja a tartalmat a helyről.

## <a name="prerequisites"></a>Előfeltételek

- Jelentkezzen be, vagy hozzon létre egy fiókot a Content Moderator [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com/) webhelyén.

## <a name="image-reviews"></a>Képekre vonatkozó vélemények

1. Lépjen a [felülvizsgálati eszközre](https://contentmoderator.cognitive.microsoft.com/), válassza a **Try (kipróbálás** ) fület, és töltsön fel néhány képet az áttekintéshez.
1. A feltöltött lemezképek feldolgozásának befejeződése után lépjen a **felülvizsgálat** lapra, és válassza a **kép** lehetőséget.

    ![Chrome böngésző, amely a felülvizsgálati eszközt jeleníti meg a kép áttekintése lehetőség kiemelésével](images/review-images-1.png)

    A képek az automatikus moderálási folyamat által hozzárendelt címkékkel jelennek meg. A felülvizsgálati eszközön keresztül elküldött rendszerképek nem láthatók más felülvizsgálók számára.

1. A képernyőn megjelenő képek számának beállításához áthelyezheti az **értékeléseket** (1). Kattintson a **címkézett** vagy **címkézetlen** gombokra (2) a képek megfelelő rendezéséhez. Kattintson a címke panelre (3) a be-vagy kikapcsolásához.

    ![Chrome-böngésző, amely a felülvizsgálati eszközt tartalmazza a címkézett rendszerképekkel a felülvizsgálathoz](images/review-images-2.png)

1. Ha további információkat szeretne látni egy képpel kapcsolatban, kattintson a három pontra a miniatűrben, és válassza a **részletek megtekintése** lehetőséget. Hozzáadhat egy rendszerképet egy alcsapathoz az **Áthelyezés** lehetőséggel (lásd a [csapatok](./configure.md#manage-team-and-subteams) szakaszt az alcsoportok megismeréséhez).

    ![Egy, a részletek megtekintése lehetőséget kiemelő rendszerkép](images/review-images-3.png)

1. A Részletek lapon keresse meg a képek moderálásával kapcsolatos információkat.

    ![Egy külön ablaktáblán felsorolt moderálási részleteket tartalmazó rendszerkép](images/review-images-4.png)

1. Miután áttekintette és frissítette a címke-hozzárendeléseket, kattintson a **tovább** gombra a felülvizsgálatok elküldéséhez. A beküldést követően öt másodpercre van szüksége, hogy **az előző gombra kattintva** térjen vissza az előző képernyőre, és tekintse át a lemezképeket. Ezt követően a lemezképek már nem szerepelnek a küldési várólistában, és az **előző** gomb már nem érhető el.

## <a name="text-reviews"></a>Szövegekre vonatkozó vélemények

A szöveges értékelések hasonlóan működnek a képelemzésekhez. A tartalom feltöltése helyett egyszerűen írhat vagy beilleszthet szöveget (legfeljebb 1 024 karakter). Ezután Content Moderator elemzi a szöveget, és alkalmazza a címkéket (az egyéb moderálási információk, például a káromkodás és a személyes adatok mellett). A szöveges felülvizsgálatok során a felülvizsgálat elküldése előtt válthat az alkalmazott címkéket és/vagy alkalmazhat egyéni címkéket.

![Képernyőkép a megjelöléssel ellátott szövegről egy Chrome-böngészőablakban](../images/reviewresults_text.png)

## <a name="next-steps"></a>Következő lépések

Ebben az útmutatóban megtanulta, hogyan állíthat be és használhat felülvizsgálatokat a Content Moderator [felülvizsgálati eszközből](https://contentmoderator.cognitive.microsoft.com). Ezután tekintse meg az [API-konzol útmutatóját](../try-review-api-review.md) vagy a [.net SDK](../client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp) rövid útmutatóját, amelyből megtudhatja, hogyan hozhat létre programozott módon az értékeléseket.
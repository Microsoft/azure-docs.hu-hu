---
title: Minták Súgó előrejelzése – LUIS
titleSuffix: Azure Cognitive Services
description: A minta lehetővé teszi, hogy nagyobb pontosságot szerezzen egy szándékhoz anélkül, hogy sok további hosszúságú kimondott szöveg lenne.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: cda71c6e825ff27ba9b03e1306ccb287663e8613
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95025959"
---
# <a name="patterns-improve-prediction-accuracy"></a>A minták az előrejelzési pontosságot javítják
A minták úgy vannak kialakítva, hogy javítsák a pontosságot, ha több hosszúságú kimondott szöveg nagyon hasonló.  A minta lehetővé teszi, hogy nagyobb pontosságot szerezzen egy szándékhoz anélkül, hogy sok további hosszúságú kimondott szöveg lenne.

## <a name="patterns-solve-low-intent-confidence"></a>Az alacsony szándékú megbízhatósági viszonyok megoldása
Vegyünk egy olyan emberi erőforrás-alkalmazást, amely jelentést készít a szervezeti diagramon egy alkalmazotthoz képest. Az alkalmazott neve és kapcsolata miatt LUIS a résztvevő alkalmazottakat adja vissza. Vegye fontolóra egy alkalmazott, Tom, az Alice nevű felettes nevét, és a neve: Michael, Rebecca és Carl.

![Szervezeti diagram képe](./media/luis-concept-patterns/org-chart.png)

|Beszédelemek|Előre jelzett szándék|Szándék pontszáma|
|--|--|--|
|Ki Tom alárendelt?|GetOrgChart|.30|
|Ki a Tom alárendeltje?|GetOrgChart|.30|

Ha egy alkalmazás 10 – 20 hosszúságú kimondott szöveg tartalmaz, különböző hosszúságú mondattal, különböző Word-sorrendtel és akár más szóval ("alárendelt", "Manage", "jelentés"), akkor a LUIS alacsony megbízhatósági pontszámot adhat vissza. Hozzon létre egy mintát, amely segít megismerni a Word-sorrend fontosságát.

A minták a következő helyzeteket oldják meg:

* A cél pontszáma alacsony
* A megfelelő szándék nem a legfelső pontszám, hanem a legfelső pontszámhoz közeledik.

## <a name="patterns-are-not-a-guarantee-of-intent"></a>A minták nem garantálják a szándékot
A minták előrejelzési technológiák kombinációját használják. A sablonhoz való Kimondás szándékának beállítása a mintázatban nem garantálja a szándék-előrejelzést, de ez egy erős jel.

<a name="patterns-do-not-improve-entity-detection"></a>

## <a name="patterns-do-not-improve-machine-learning-entity-detection"></a>A minták nem javítják a gépi tanulási entitások észlelését

A mintázat elsődleges célja, hogy segítse a szándékok és a szerepkörök előrejelzését. A _minta. minden_ entitás a szabad formátumú entitások kinyerésére szolgál. Míg a minták entitásokat használnak, a minta nem segít a gépi tanulási entitások észlelésében.

Ne várja meg a továbbfejlesztett entitások előrejelzését, ha több hosszúságú kimondott szöveg összecsuk egyetlen mintára. Az egyszerű entitások a hosszúságú kimondott szöveg hozzáadásához vagy a listához tartozó entitások használata más, mint a minta nem fog tüzet használni.

## <a name="patterns-use-entity-roles"></a>A minták entitás-szerepköröket használnak
Ha a mintában két vagy több entitás kontextussal kapcsolatos kapcsolatban áll, a minták az entitások környezetfüggő információinak kinyeréséhez használják az entitás- [szerepköröket](./luis-concept-entity-types.md) .

## <a name="prediction-scores-with-and-without-patterns"></a>Előrejelzési pontszámok és mintázatok nélkül
Mivel a hosszúságú kimondott szöveg-hez elég példa, a LUIS a minták nélkül is növelheti az előrejelzési megbízhatóságot. A minták megnövelik a megbízhatósági pontszámot anélkül, hogy annyi hosszúságú kimondott szöveg kellene biztosítania.

## <a name="pattern-matching"></a>Minta egyeztetése
A rendszer a mintázatot először a mintában lévő entitások észlelése alapján rendezi, majd ellenőrzi a minta többi szövegét és sorrendjét. Az entitásokat a minta alapján kell megfeleltetni. A minta a token szintjén, nem pedig a karakter szintjén lesz alkalmazva.

## <a name="pattern-only-apps"></a>Csak minta alkalmazások
Olyan alkalmazásokat hozhat létre, amelyek nem rendelkeznek példa hosszúságú kimondott szöveg, feltéve, hogy az egyes szándékok esetében minta van. Csak minta alkalmazás esetében a minta nem tartalmazhat gépi tanulásra vonatkozó entitásokat, mert ezekhez például hosszúságú kimondott szöveg szükséges.

## <a name="patternany-entity"></a>Pattern.any entitás

[!INCLUDE [Pattern.any entity - concepts](./includes/pattern-any-entity.md)]

## <a name="best-practices"></a>Ajánlott eljárások
Ismerje meg az [ajánlott eljárásokat](luis-concept-best-practices.md).

## <a name="pattern-syntax"></a>Minta szintaxisa

A minta szintaxisának ismertetése a [minta szintaxisának](reference-pattern-syntax.md)leírásában.

## <a name="next-steps"></a>Következő lépések

További információ a mintákról:

* [Mintázatok hozzáadása](luis-how-to-model-intent-pattern.md)
* [Minta hozzáadása bármely entitáshoz](luis-how-to-add-entities.md#add-a-patternany-entity)
* [Mintázatok szintaxisa](reference-pattern-syntax.md)

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan implementálhat mintákat ebben az oktatóanyagban](luis-tutorial-pattern.md)

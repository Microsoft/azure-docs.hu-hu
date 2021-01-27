---
title: Mi az QnA Maker szolgáltatás?
description: A QnA Maker egy felhőalapú NLP-szolgáltatás, amely könnyen létrehoz egy természetes társalgási réteget az adataiban. A felhasználó az egyéni Tudásbázis (KB) adatai alapján megkeresheti a legmegfelelőbb választ az adott természetes nyelvi bevitelhez.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 01/22/2021
ms.custom: cog-serv-seo-aug-2020
keywords: QnA-készítő, alacsony kódú csevegési robotok, több fordulatos beszélgetések
ms.openlocfilehash: a75b5939e8848ca898d7279b55ab2c4faa4ed72a
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919561"
---
# <a name="what-is-qna-maker"></a>Mi a QnA Maker?

A QnA Maker egy felhőalapú, természetes nyelvi feldolgozó (NLP) szolgáltatás, amely lehetővé teszi, hogy az adatain alapuló természetes beszélgetési réteget hozzon létre. A rendszer a legmegfelelőbb választ keresi az egyéni Tudásbázis (KB) adataiból származó adatokhoz.

A QnA Maker általában olyan társalgási ügyfélalkalmazások készítésére használják, mint például a közösségi média alkalmazásai, a csevegési robotok és a beszédfelismerést támogató asztali alkalmazások.

## <a name="when-to-use-qna-maker"></a>Mikor kell használni a QnA Maker

* **Ha statikus információval rendelkezik** – használja a QnA Maker, ha a válaszokat a Tudásbázisban statikus információkkal látja el. Ez a Tudásbázis az Ön igényeinek megfelelően egyedi, amelyet dokumentumok, például [PDF-EK és URL-címek](../Concepts/data-sources-and-content.md)használatával készített.
* **Ha ugyanazt a választ szeretné adni egy kérelemhez, kérdéshez vagy parancshoz** – ha a különböző felhasználók ugyanezt a kérdést nyújtják be, ugyanazt a választ adja vissza.
* **Ha a metaadatok alapján szeretné szűrni a statikus adatokat** – a [metaadat](../how-to/metadata-generateanswer-usage.md) -Címkék hozzáadása lehetőséggel további szűrési lehetőségeket biztosíthat az ügyfélalkalmazás felhasználóival és az információkkal kapcsolatban. A metaadatok általános információi közé tartozik a [Chit-Chat](../how-to/chit-chat-knowledge-base.md), a tartalom típusa vagy a formátum, a tartalom célja és a tartalom frissessége.
* **Ha olyan robot-beszélgetést szeretne kezelni, amely statikus adatokat tartalmaz** – a Tudásbázis egy felhasználó beszélgetési szövegét vagy parancsát veszi át, és válaszol rá. Ha a válasz egy előre meghatározott beszélgetési folyamat része, amely a Tudásbázisban a [többfordulatos kontextusban](../how-to/multiturn-conversation.md)szerepel, a robot egyszerűen megadhatja ezt a folyamatot.

## <a name="what-is-a-knowledge-base"></a>Mi az a Tudásbázis?

QnA Maker [importálja a tartalmat](../Concepts/plan.md) a kérdések és válaszok pár tudásbázisba. Az importálási folyamat kigyűjti a strukturált és a félig strukturált tartalom részei közötti kapcsolat adatait, hogy az a kérdés és a válasz párok közötti kapcsolatokat is magában foglalja. A kérdések és válaszok párokat szerkesztheti, vagy új párokat adhat hozzá.

A kérdés-válasz pár tartalma a következőket tartalmazza:
* A kérdés összes alternatív formája
* A keresés során a válaszadási lehetőségek szűréséhez használt metaadatok címkéi
* Követési kérések a keresés finomításának folytatásához

![Példa kérdésre és válaszra a metaadatokkal](../media/qnamaker-overview-learnabout/example-question-and-answer-with-metadata.png)

Miután közzétette a tudásbázist, egy ügyfélalkalmazás elküldi a felhasználó kérdését a végpontnak. A QnA Maker szolgáltatás feldolgozza a kérdést, és válaszol a legjobb válaszra.

## <a name="create-a-chat-bot-programmatically"></a>Csevegési robot létrehozása programozott módon

QnA Maker Tudásbázis közzétételét követően az ügyfélalkalmazás egy kérdést küld a Tudásbázis-végpontnak, és JSON-válaszként fogadja az eredményeket. QnA Maker egy közös ügyfélalkalmazás egy csevegési robot.

![Kérdezzen egy robotot, és kapjon választ a Tudásbázis tartalmából](../media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|Lépés|Művelet|
|:--|:--|
|1|Az ügyfélalkalmazás elküldi a felhasználó _kérdését_ (a szöveg a saját szavaival), "hogyan programozott módon frissíti a tudásbázist?" a Tudásbázis-végponthoz.|
|2|A QnA Maker a betanított Tudásbázis használatával biztosítja a megfelelő választ, valamint azokat a követési kérdéseket, amelyek a legjobb válasz keresésének pontosítására használhatók. QnA Maker egy JSON-formátumú választ ad vissza.|
|3|Az ügyfélalkalmazás a JSON-választ használva döntéseket hoz a beszélgetés folytatásához. Ezek a döntések magukban foglalhatják a legfontosabb választ, és további lehetőségeket mutatnak be a legjobb válasz keresésének pontosítására. |
|||

## <a name="build-low-code-chat-bots"></a>Alacsony kódú csevegési robotok készítése

A QnA Maker portál a teljes Tudásbázis-létrehozási élményt biztosítja. Az aktuális formában importálhatja a dokumentumokat a tudásbázisba. Ezek a dokumentumok (például a gyakori kérdések, a manuális, a számolótáblák vagy a weblapok) át lesznek alakítva kérdés-válasz párokba. Az egyes párok elemzése a követő kérések és a más párokhoz való csatlakozás alapján történik. A végső _Markdown_ formátuma támogatja a gazdag bemutatót, beleértve a képeket és a hivatkozásokat.

A Tudásbázis szerkesztése után tegye közzé a tudásbázist egy működő [Azure webalkalmazás-roboton](https://azure.microsoft.com/services/bot-service/) anélkül, hogy kódot kellene írnia. Tesztelje a robotot a [Azure Portalban](https://portal.azure.com) , vagy töltse le, és folytassa a fejlesztést.

## <a name="high-quality-responses-with-layered-ranking"></a>Magas színvonalú válaszok rétegzett rangsorolással

A QnA Maker rendszer egy rétegzett rangsorolási megközelítés. Az adattárolás az Azure Search szolgáltatásban történik, amely az első rangsorolási rétegként is szolgál. Az Azure Search legfontosabb eredményeit a rendszer átadja a QnA Maker NLP-re rangsoroló modelljének a végső eredmények és a megbízhatósági pontszám létrehozásához.

## <a name="multi-turn-conversations"></a>Többkörös beszélgetések

A QnA Maker többfunkciós és aktív tanulást tesz lehetővé az alapvető kérdések és válaszok párosításának javítása érdekében.

A **több fordulatos kérések** lehetővé teszi a kérdések és válaszok párok összekapcsolását. Ez a kapcsolódás lehetővé teszi az ügyfélalkalmazás számára a legjobb válasz megadását, és további kérdéseket biztosít a végső válasz keresésének pontosításához.

Miután a Tudásbázis a közzétett végponton lévő felhasználóktól kapott kérdéseket, QnA Maker alkalmazza az **aktív tanulást** ezekre a valós kérdésekre, hogy a minőség javítása érdekében a Tudásbázisban módosításokat javasoljon a Tudásbázisban.

## <a name="development-lifecycle"></a>Fejlesztési életciklus

A QnA Maker a szerzői műveletek, a képzés és a közzététel, valamint együttműködési engedélyek használatával integrálható a teljes fejlesztési életciklusba.

> [!div class="mx-imgBorder"]
> ![A fejlesztési ciklus fogalmi képe](../media/qnamaker-overview-learnabout/development-cycle.png)


## <a name="complete-a-quickstart"></a>Bevezetés befejezése

A legnépszerűbb programozási nyelveken olyan gyors útmutatókat találunk, amelyek az alapszintű tervezési mintákat tanítják, és kevesebb, mint 10 perc alatt futtatják a kódot. Az egyes szolgáltatásokra vonatkozó rövid útmutató az alábbi listában található.

* [Ismerkedés az QnA Maker ügyféloldali kódtáraval](../quickstarts/quickstart-sdk.md)
* [Ismerkedés a QnA Maker portálral](../quickstarts/create-publish-knowledge-base.md)

## <a name="next-steps"></a>Következő lépések
QnA Maker az egyéni Tudásbázis létrehozásához, kezeléséhez és üzembe helyezéséhez szükséges összes információt tartalmazza.

> [!div class="nextstepaction"]
> [A legutóbbi módosítások áttekintése](../whats-new.md)

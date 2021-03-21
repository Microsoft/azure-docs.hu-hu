---
title: Csevegés hozzáadása QnA Maker tudásbázishoz
titleSuffix: Azure Cognitive Services
description: Ha személyes Chit-csevegést szeretne hozzáadni a robothoz, akkor még több beszélgetést és részvételt tesz lehetővé, amikor létrehoz egy KB-ot. A QnA Maker lehetővé teszi, hogy egyszerűen vegyen fel egy előre feltöltött készletet a KB-ra.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 1208d95a282ca6e236d9d6be8013f51dead90d13
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94376658"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Chit-csevegés hozzáadása egy tudásbázishoz

A csevegést a robothoz való hozzáadásával több beszélgetést és bevonást vehet igénybe. A QnA Maker Chit-Chat funkciója lehetővé teszi, hogy könnyedén vegyen fel egy előre feltöltött készletet a tudásbázisba (KB). Ez lehet a bot személyiségének kiindulási pontja, és megtakarítja az időt és a költségeket a semmiből való írás során.

Ez az adatkészlet körülbelül 100 forgatókönyvet kínál a több persona, például a Professional, a barátságos és a szellemes beszélgetés hangjaként. Válassza ki azt a Personát, amely a legszorosabban hasonlít a robotja hangjára. A felhasználó lekérdezése miatt QnA Maker megpróbálja egyeztetni a legközelebbi ismert Chit-Chat-QnA.

Néhány példa a különböző személyiségekre. A személyiségi [adatkészleteket](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets) a személyes adatokkal együtt tekintheti meg.

A felhasználói lekérdezéséhez `When is your birthday?` minden személynek van egy stílusú válasza:

<!-- added quotes so acrolinx doesn't score these sentences -->
|Személyiség|Példa|
|--|--|
|Professional|A kor nem igazán vonatkozik rám.|
|Rövid|Nem igazán van kora.|
|Szellemes|Ingyenes.|
|Gondoskodó|Nincs életkorom.|
|Lelkes|Robot vagyok, úgyhogy nem vagyok kora.|
||


## <a name="language-support"></a>Nyelvi támogatás

A Chit-Chat adatkészletek a következő nyelveken támogatottak:

|Nyelv|
|--|
|Kínai|
|Angol|
|Francia|
|Németország|
|Olasz|
|Japán|
|Koreai|
|Portugál|
|Spanyol|


## <a name="add-chit-chat-during-kb-creation"></a>Chit-Chat hozzáadása a KB létrehozásakor
A Tudásbázis létrehozása során a forrás URL-címek és fájlok hozzáadása után lehetőség van a Chit-Chat hozzáadására. Válassza ki a kívánt személyiséget a Chit-Chat alapjaként. Ha nem kívánja felvenni a Chit-chatet, vagy ha már rendelkezik az adatforrások támogatásával, válassza a **nincs lehetőséget**.

## <a name="add-chit-chat-to-an-existing-kb"></a>Chit-csevegés hozzáadása meglévő KB-hoz
Válassza ki a KB-ot, és navigáljon a **Beállítások** lapra. A megfelelő **. TSV** formátumú összes Chit-Chat-adatkészletre mutató hivatkozás. Töltse le a kívánt személyiséget, majd töltse fel a fájlt forrásként. A fájl letöltésekor és feltöltésekor ügyeljen rá, hogy ne szerkessze a formátumot vagy a metaadatokat.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

![Chit-csevegés hozzáadása meglévő TUDÁSBÁZIShoz](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)

![Csevegés hozzáadása meglévő TUDÁSBÁZIS előzetes kiadásához](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset-v2.png)

---

## <a name="edit-your-chit-chat-questions-and-answers"></a>A Chit-csevegéssel kapcsolatos kérdések és válaszok szerkesztése
Ha szerkeszti a KB-ot, egy új forrást fog látni a Chit-Chat számára a kiválasztott személyiség alapján. Mostantól bármilyen más forráshoz hasonlóan hozzáadhat megváltoztatott kérdéseket vagy szerkesztheti a válaszokat is.

![A Chit-Chat QnAs szerkesztése](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

A metaadatok megtekintéséhez válassza az eszköztár **beállítások megtekintése** elemét, majd válassza a **metaadatok megjelenítése** lehetőséget.

## <a name="add-additional-chit-chat-questions-and-answers"></a>További Chit-csevegési kérdések és válaszok hozzáadása
Hozzáadhat egy olyan új QnA-párt, amely nem szerepel az előre definiált adatkészletben. Győződjön meg arról, hogy nem duplikál olyan QnA párt, amely már szerepel a Chit-Chat készletben. Új Chit-Chat-QnA hozzáadásakor a rendszer bekerül a **szerkesztői** forrásba. Annak érdekében, hogy a Ranger megértse, hogy ez a Chit-Chat, adja hozzá a "Vezércikk: chitchat" metaadat-kulcs/érték párokat, ahogy az alábbi képen látható:

:::image type="content" source="../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png" alt-text="Chit-Chat QnAs hozzáadása" lightbox="../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png":::

## <a name="delete-chit-chat-from-an-existing-kb"></a>Chit-csevegés törlése meglévő KB-ból
Válassza ki a KB-ot, és navigáljon a **Beállítások** lapra. Az adott Chit-Chat-forrás fájlként van listázva a kiválasztott személyiség nevével. Ezt forrásfájlként is törölheti.

![A KB-ból származó chit-csevegés törlése](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Tudásbázis importálása](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>Lásd még

[A QnA Maker áttekintése](../Overview/overview.md)
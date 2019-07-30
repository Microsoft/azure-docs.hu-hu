---
title: Kifejezések listája – LUIS
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) használja, amely javítja az észlelésük vagy szándékok és entitások előrejelzését funkciók hozzáadása, a kategóriák és minták
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 0ce1fb182320b04aebb45b0f848d8c8f6c965fd9
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638145"
---
# <a name="use-phrase-lists-to-boost-signal-of-word-list"></a>Használja a kifejezés boost jelre a word lista sorolja fel

A pontosság javítása érdekében a LUIS-alkalmazás funkciókat adhat hozzá. Szolgáltatások révén a LUIS mutatók azáltal, hogy egyes szavak és kifejezések egy alkalmazás tartomány szókincsből eredőket részét képezik. 

A [kifejezéslista](luis-concept-feature.md) tartalmaz egy csoportot az értékek (szavak vagy kifejezések), amely ugyanahhoz az osztályhoz tartozik, és hasonló módon (például az városok vagy a termék nevét) kell kezelni. Mi a LUIS megismerkedik az egyik automatikusan alkalmazza a mások számára is. Ez a lista nem ugyanaz, mint a [listában szereplő entitások](reference-entity-list.md) (pontos szöveges egyezések) a megfeleltetett szavak esetében.

Kifejezések listáját, LUIS egy második jelzés ezeket szavakkal kapcsolatos hozzáadja az jobban illeszkedhet az alkalmazás tartomány.

## <a name="add-phrase-list"></a>A kifejezés lista hozzáadása

A LUIS legfeljebb 10 kifejezési listát tesz lehetővé az alkalmazásokban. 

1. Nyissa meg az alkalmazás nevére kattintva **saját alkalmazások** lapon, és kattintson a **hozhat létre**, majd kattintson a **listák kifejezés** az alkalmazás bal oldali panelen. 

1. Az a **listák kifejezés** kattintson **új lista létrehozása a kifejezés**. 
 
1. A **kifejezések listájának hozzáadása** párbeszédpanelen írja be `Cities` a kifejezést a kifejezés lista neveként. Az a **érték** mezőbe írja be a kifejezést lista értékeit. Írjon be egy értéket egy időben, vagy értékek vesszővel elválasztva, és nyomja le az **Enter**.

    ![Lista városok kifejezés hozzáadása](./media/luis-add-features/add-phrase-list-cities.png)

1. A LUIS is javasol kapcsolódó értékeket a kifejezés listához való hozzáadásához. Kattintson a **javasoljuk** javasolt értékek a added value(s) szemantikailag kapcsolódó csoport beolvasásához. Kattintson a javasolt értékeket, vagy kattintson **adja hozzá az összes** adja hozzá őket az összes.

    ![Kifejezések listájának javasolt értékei – az összes hozzáadása](./media/luis-add-features/related-values.png)

1. Kattintson a **ezeket az értékeket felcserélhetők** Ha hozzáadott kifejezés listaértékek alternatívával felcserélhetők.

    ![Kifejezések listájának javasolt értékei – jelölje ki a felcserélhetők jelölőnégyzetet](./media/luis-add-features/interchangeable.png)

1. Kattintson a **Done** (Kész) gombra. A "Város" kifejezés helyett szerepel lista adnak hozzá a **listák kifejezés** lap.

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Törölheti, vagy a kifejezést a lista a környezetfüggő eszköztár inaktiválása a **listák kifejezés** lapot.

## <a name="next-steps"></a>További lépések

Utána hozzáadása, szerkesztése, törlése vagy kifejezés listáját inaktiválása [betanítása és az alkalmazás tesztelése](luis-interactive-test.md) ismételt használatával ellenőrizheti, ha növeli a teljesítményt.

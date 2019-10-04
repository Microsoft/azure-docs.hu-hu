---
title: Alkalmazás betanítása – LUIS
titleSuffix: Azure Cognitive Services
description: Képzési az a folyamat, a Language Understanding (LUIS) Alkalmazásverzió javítása a beszédfelismerés oktatási. A LUIS-alkalmazás betanításához például hozzáadása, szerkesztése, címkézés vagy entitásokat, a leképezések és a kimondott szöveg törlése a modellhez való frissítés után.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: b3841c9d60cf275e423024fc66c15582f95c0a10
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932755"
---
# <a name="train-your-active-version-of-the-luis-app"></a>A LUIS-alkalmazás aktív verziójának betanítása 

Képzési az a folyamat, a Language Understanding (LUIS) alkalmazásával a beszédfelismerés oktatási. A LUIS-alkalmazás betanításához például hozzáadása, szerkesztése, címkézés vagy entitásokat, a leképezések és a kimondott szöveg törlése a modellhez való frissítés után. 

<!--
When you train a LUIS app by example, LUIS generalizes from the examples you have labeled, and it learns to recognize the relevant intents and entities. This teaches LUIS to improve classification accuracy in the future. -->

Képzés és [tesztelés](luis-concept-test.md) az alkalmazás az iteratív folyamat. Miután a LUIS-alkalmazás betanításához, tesztelje azt az annak ellenőrzéséhez, hogy a szándékok és entitások helyesen ismeri a minta kimondott szöveg. Ha nem, a LUIS-alkalmazásokon, tanítási és tesztelési ellenőrizze újra frissítéseket. 

Képzési alkalmazza a rendszer az aktív verzió a LUIS-portálon. 

## <a name="how-to-train-interactively"></a>Hogyan interaktív módon betanítása

A iteratív folyamat a [LUIS portál](https://www.luis.ai), először létre kell legalább egy alkalommal betanítása a LUIS-alkalmazás. Ellenőrizze, hogy minden szándékot képzési előtt legalább egy utterance (kifejezés).

1. Az alkalmazás eléréséhez annak nevét választva a **saját alkalmazások** lapot. 

2. Jelölje be az alkalmazásba, **Train** a az ablak tetején. 

3. Képzési befejeződése után a böngésző tetején egy zöld értesítési sáv jelenik meg.

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>Ha egy vagy több leképezések az alkalmazás, amely nem tartalmaz példa utterances, az alkalmazás nem betanításához. Az összes a leképezések beszédmódok hozzáadása. További információkért lásd: [példa beszédmódok hozzáadása](luis-how-to-add-example-utterances.md).

## <a name="training-date-and-time"></a>Képzés dátuma és időpontja

A betanítás dátuma és időpontja GMT + 2. 

## <a name="train-with-all-data"></a>Az összes adat betanítása

Képzési egy kis csoportja negatív mintavételt használ. Ha szeretné az összes adat használata helyett a kisméretű negatív mintavételt, használja a [verzió beállítások API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) az a `UseAllTrainingData` állítsa az IGAZ értéket kapcsolja ki ezt a szolgáltatást. 

## <a name="unnecessary-training"></a>A szükségtelen képzés

Nem kell minden egyetlen módosítás után betanításához. Képzési módosítások csoportja érvénybe lépnek a modellt, és szeretné a következő lépés az, hogy tesztelje, vagy közzéteheti után kell elvégezni. Ha nem szeretné tesztelni, vagy közzéteheti, a képzési nem szükséges. 

## <a name="training-with-the-rest-apis"></a>A REST API-kkal képzés

A LUIS portálon képzési nyomja le az egyetlen lépésből áll a **Train** gombra. A REST API-kkal képzési két lépésből áll. Az első [képzési kérelem](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) a HTTP POST. Majd kérik a [képzési állapot](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) az HTTP Get. 

Annak érdekében, hogy ismeri a betanítási befejezésekor, hogy lekérdezik az állapot addig, amíg sikeresen képzett összes modellt. 

## <a name="next-steps"></a>További lépések

* [Az intelligens hangfelismerési szolgáltatással javasolt utterances felirat](luis-how-to-review-endpoint-utterances.md) 
* [A LUIS-alkalmazás a teljesítmény javítása szolgáltatások használata](luis-how-to-add-features.md) 

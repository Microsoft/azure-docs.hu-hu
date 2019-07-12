---
title: Beépített adatkinyerés, természetes nyelv és képfeldolgozás – Azure Search
description: Adatok kinyerése, természetes nyelv és kognitív képességeket képfeldolgozó szemantika és a struktúra hozzá nyers tartalom az Azure Search-folyamatban.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: c9dfa6af4fb13018051c06783e5ae2bc3f49c0da
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672095"
---
# <a name="predefined-skills-for-content-enrichment-azure-search"></a>A tartalom Adatbővítés (Azure Search) előre megadott képesség

Ebben a cikkben megismerkedhet az Azure Search szolgáltatással a megadott, a kognitív képességeket. A *cognitive szakértelem* egy művelet, amely valamilyen módon tartalom alakítja át. Gyakran nem egy összetevő, amely kinyeri az adatokat vagy kikövetkezteti a struktúrát, és ezért úgy bővíti a bemeneti adatok az ismertetése. Szinte mindig kimenete szöveges. A *indexmezők* gyűjteménye, amelyek meghatározzák a Adatbővítés folyamat képességek. 

> [!NOTE]
> Bontsa ki a hatókört által a feldolgozás, gyakoriságának növelése további dokumentumok hozzáadása, vagy adja hozzá a további AI-algoritmusokat, kell [számlázható Cognitive Services-erőforrás csatolása](cognitive-search-attach-cognitive-services.md). A díjakat API-k hívásakor, a Cognitive Services, valamint a lemezkép kinyerése a az Azure Search-dokumentumfeltörést fázis részeként. Nem számítunk fel díjat a szövegkinyerés dokumentumok közül.
>
> Végrehajtási beépített képességek a meglévő díjakat [használatalapú-as-, a Cognitive Services nyissa meg az árat](https://azure.microsoft.com/pricing/details/cognitive-services/). A kép kinyerési díjszabás leírása a [díjszabását ismertető oldalt az Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="predefined-skills"></a>Előre megadott képesség

Több ismeretek olyan rugalmas, mire azok felhasználását vagy előállításához. Általában a legtöbb képességek előre betanított modellek, ami azt jelenti, hogy Ön nem betanítja a modellt, a saját betanítási adatok használatával alapulnak. A következő táblázat felsorolja és ismerteti a Microsoft által biztosított képességek. 

| Szakértelem | Leírás |
|-------|-------------|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | Ezen a képzettségi pretrained modellt használ a fontos kifejezések elhelyezése kifejezés, nyelvi szabályok, más feltételek közelében, és hogyan szokatlan kifejezés belül az adatok alapján észleli. |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | A képzettségi használ egy pretrained a modell nyelvét észleléséhez használt (egy dokumentum egy nyelvi azonosító). Ha több nyelvet használ az azonos szöveg szegmensen belül, a kimenete a túlnyomórészt használt nyelv az LCID.|
| [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md) | Összesíti a szöveget a mezők gyűjteménye egyetlen mezőbe.  |
| [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) | Ezen a képzettségi pretrained modellt használ a kategóriák rögzített készletének entitások létrehozására: személyek, tartózkodási hely, a szervezeten belül, e-mailt küld, URL-címek, datetime mezők. |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | Szakértelem pretrained modellt használ, rekord által rekord alapján pontszámot rendelni az pozitív vagy negatív véleményeket. A pontszám értéke 0 és 1. Semleges pontszámok a NULL értékű mindkét esetben fordulhat elő, ha a vélemény nem észlelhető, és a szöveg, amely tekinthető semleges.  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | Felosztja a szöveget oldalakat, hogy bővítését, vagy bővítésével fokozatosan a tartalmat. |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Szakértelem kép tartalma azonosításához, és a egy leírás létrehozása lemezkép észlelési algoritmust használ. |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | Optikai karakterfelismerés. |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | A Maps kimenet a komplex típus (a többrészes adattípusúvá, amely a teljes nevet, egy többsoros cím vagy vezetéknév és a egy személyes azonosító kombinációja használható.) |
| [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md) | Lehetővé teszi, hogy a cognitive search folyamat bővíthetőség azáltal, hogy az egyéni Web API egy HTTP-hívással |


Létrehozásával kapcsolatos útmutató a [egyéni ismeretek](cognitive-search-custom-skill-web-api.md), lásd: [egy egyéni felületen definiálása](cognitive-search-custom-skill-interface.md) és [példa: Cognitive Search egyéni műveleteket létrehozása](cognitive-search-create-custom-skill-example.md).

## <a name="see-also"></a>Lásd még

+ [Hogyan képességcsoport megadása](cognitive-search-defining-skillset.md)
+ [Egyéni ismeretek interface definition](cognitive-search-custom-skill-interface.md)
+ [Oktatóanyag: Bővített indexelés kognitív kereséssel](cognitive-search-tutorial-blob.md)

---
title: Felnőtt, pikáns, véres tartalom – Computer Vision
titleSuffix: Azure Cognitive Services
description: Az Computer Vision API használatával a képeken a felnőtt tartalom észlelésével kapcsolatos fogalmak.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 5d5961ecae2fbc154ae6f1acd74df2bb74024fa1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96532618"
---
# <a name="detect-adult-content"></a>Felnőtt tartalom észlelése

A Computer Vision képes felderíteni a képeken elérhető felnőtt anyagokat, hogy a fejlesztők a szoftverben korlátozzák a képek megjelenítését. A tartalom jelzői a nulla és az egyik pontszám között lesznek alkalmazva, így a fejlesztők a saját preferenciáiknak megfelelően tudják értelmezni az eredményeket.

> [!NOTE]
> A funkció nagy részét az [Azure Content moderator](../content-moderator/overview.md) szolgáltatás kínálja. Tekintse meg ezt az alternatívát a megoldásokhoz a szigorúbb tartalomkezelési forgatókönyvek, például a szöveges moderálás és az emberi felülvizsgálati munkafolyamatok terén.

## <a name="content-flag-definitions"></a>Content Flag-definíciók

A "felnőtt" besorolás számos különböző kategóriát tartalmaz:

- A **felnőtt** képek kifejezetten szexuális jellegűek, és gyakran a meztelenség és a szexuális tevékenységek láthatók.
- A **zamatos** képek szexuálisan szuggesztív jellegűek, és gyakran kevésbé szexuálisan explicit tartalmú tartalmat tartalmaznak, mint a **felnőttként** megjelölt képek.
- **Véres** képek: Blood/Gore.

## <a name="use-the-api"></a>Az API használata

A felnőtt tartalmat a [képek elemzése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API-val ismerheti meg. Amikor hozzáadja a értéket `Adult` a **visualFeatures** lekérdezési paraméterhez, az API három logikai tulajdonságot ad vissza, &mdash; `isAdultContent` és a `isRacyContent` `isGoryContent` &mdash; JSON-válaszában. A metódus a megfelelő tulajdonságokat is adja vissza, &mdash; `adultScore` `racyScore` `goreScore` &mdash; amelyek a nulla és az egyik kategóriába tartozó megbízhatósági pontszámokat jelölik.

- [Rövid útmutató: Computer Vision REST API vagy ügyféloldali kódtárak](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

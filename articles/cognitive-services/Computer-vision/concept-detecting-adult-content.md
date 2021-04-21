---
title: Felnőtteknek szóló, ritka tartalmú tartalmak – Computer Vision
titleSuffix: Azure Cognitive Services
description: A felnőtt tartalom képeken való észlelésével kapcsolatos fogalmak a Computer Vision API-val.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ceef604fe07a11be89376e26c6fecc49298ebacf
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778858"
---
# <a name="detect-adult-content"></a>Felnőtteknek szóló tartalom észlelése

Computer Vision a képeken felnőtt anyagokat, így a fejlesztők korlátozhatják ezeknek a képeknek a megjelenítését a szoftverükben. A tartalomjelölők nulla és egy közötti pontszámmal vannak alkalmazva, így a fejlesztők saját preferenciáik szerint értelmezik az eredményeket.

> [!NOTE]
> Ennek a funkciónak a nagy része az [Azure Content Moderator](../content-moderator/overview.md) nyújt. Tekintse meg ezt az alternatívát a szigorúbb tartalommoderálási forgatókönyvek, például a szövegmoderálás és az emberi felülvizsgálati munkafolyamatok megoldásaival.

## <a name="content-flag-definitions"></a>Tartalomjelölő-definíciók

A "felnőtt" besorolás több különböző kategóriát tartalmaz:

- **A** felnőtt képek explicit módon szexuális jellegűek, és gyakran mutatnak nuditást és szexuális viselkedést.
- **A racy (racy)** képek szexuális javaslatokkal tartalmúak, és gyakran kevésbé szexuálisan explicit tartalmat tartalmaznak, mint a Felnőtt címkével **ellátott képek.**
- **A gory** képeken a vörösvérvér/vörösvért látható.

## <a name="use-the-api"></a>Az API használata

A felnőtt tartalmakat a Analyze Image API-val [észlelheti.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) Amikor hozzáadja a értéket a visualFeatures lekérdezési paraméterhez, az API három logikai tulajdonságot ad vissza , és a `Adult`  &mdash; `isAdultContent` `isRacyContent` `isGoryContent` &mdash; JSON-válaszában. A metódus a megfelelő , és tulajdonságokat is visszaadja, amelyek a nulla és egy közötti megbízhatósági pontszámokat jelölik &mdash; `adultScore` minden egyes `racyScore` `goreScore` &mdash; kategóriához.

- [Rövid útmutató: Computer Vision REST API ügyfélkódtárak létrehozása](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

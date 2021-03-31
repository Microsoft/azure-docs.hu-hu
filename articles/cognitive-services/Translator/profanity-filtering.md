---
title: Káromkodás-szűrés – Translator
titleSuffix: Azure Cognitive Services
description: A káromkodás szűrésével meghatározhatja az Azure Cognitive Services Translatorben lévő szövegben lefordított káromkodás szintjét.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 9f389d669e69dbfa6ec6d4d0b4716d2367443f17
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "98896799"
---
# <a name="add-profanity-filtering-with-the-translator"></a>Káromkodás-szűrés hozzáadása a fordítóval

A fordítói szolgáltatás általában megőrzi a fordításban található forrásban lévő káromkodást. A trágárság foka és az olyan kontextus, amely a szavak káromkodását teszi lehetővé a különböző kultúrák között. Ennek eredményeképpen a megcélzott nyelvben a káromkodás mértéke felerősíthető vagy csökkenthető.

Ha nem szeretné megtekinteni a káromkodást a fordításban, akkor is, ha a forrás szövegében a káromkodás szerepel, használja a translate () metódusban elérhető káromkodás-szűrési lehetőséget. Ezzel a beállítással megadhatja, hogy szeretné-e megtekinteni a káromkodás törlését, a megfelelő címkék jelölését vagy a művelet elvégzését.

A translate () metódus a "Options" paramétert veszi fel, amely tartalmazza az új "ProfanityAction" elemet. A ProfanityAction elfogadott értékei a következők: "nincs művelet", "megjelölt" és "törölve".

## <a name="accepted-values-of-profanityaction-and-examples"></a>ProfanityAction és példák elfogadott értékei
|ProfanityAction érték | Művelet | Példa: forrás – Japán | Példa: cél-angol|
| :---|:---|:---|:---|
| NoAction | Default (Alapértelmezett): Ugyanaz, mint a beállítás beállítása. A káromkodás a forrás és a cél között halad át. | 彼は変態です 。 | Egy bunkó. |
| Megjelölve | A profán szavakat XML-címkék veszik körül.. \<profanity> . \</profanity> | 彼は変態です 。 | Egy \<profanity> bunkó \</profanity> . |
| Törölve | A rendszer eltávolítja a profán szavakat a kimenetből a csere nélkül. | 彼は 。 | Ő a. |

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Káromkodás-szűrés alkalmazása a fordítói hívással](reference/v3-0-translate.md)

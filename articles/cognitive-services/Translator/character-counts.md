---
title: Karakterek száma – Translator
titleSuffix: Azure Cognitive Services
description: Ez a cikk azt ismerteti, hogy az Azure Cognitive Services Translator hogyan számolja a karaktereket, hogy megtudja, hogyan tölti be a tartalmat.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 6e81736e3151c9e97a8926b1f67c0a7a0d4c2f3d
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895883"
---
# <a name="how-the-translator-counts-characters"></a>A fordító karakterének megszámlálása

A fordító a bemeneti szöveg minden Unicode-kódját karakterként számítja. Egy szöveg minden fordítása külön fordításként számít, még akkor is, ha a kérést egyetlen API-hívásban adták át, több nyelvre lefordítva. A válasz hossza nem számít.

Az alábbiak száma:

* A kérelem törzsében a fordítónak átadott szöveg
   * `Text` Fordítási, Átbetűzés és szótári keresési módszerek használatakor
   * `Text` és `Translation` a szótár példái alapján
* Az összes Markup: HTML, XML-címke stb. a kérelem törzsének Text mezőjén belül. A kérelem létrehozásához használt JSON-jelölés (például "text:") nem számít.
* Egyéni levél
* Központozási
* A szóköz, a Tab, a Markup és bármilyen típusú szóköz karakter
* Minden Unicode-ban definiált kód pont
* Ismétlődő fordítás, még akkor is, ha korábban már lefordította ugyanazt a szöveget

A ideograms (például kínai és Japán kanji) alapuló szkriptek esetében a Translator Service továbbra is megszámolja az Unicode-kódok számát, a ideogram egy karaktert. Kivétel: az Unicode-helyettesítő karakterek két karakternek számítanak.

A kérések száma, a szavak, a bájtok vagy a mondatok nem relevánsak a karakterek számában.

Az észlelési és BreakSentence metódusokra irányuló hívások nem számítanak bele a karakteres felhasználásba. Azonban azt várjuk, hogy az észlelési és a BreakSentence módszerek meghívása ésszerű arányban van a többi megszámolt függvény használatával. Ha az észlelési vagy BreakSentence hívások száma meghaladja a többi megszámolt módszer 100 alkalommal történő számát, a Microsoft fenntartja a jogot az észlelési és BreakSentence módszerek használatának korlátozására.

A karakterek számával kapcsolatos további információ a [Translator GYIK](https://www.microsoft.com/en-us/translator/faq.aspx)-ban található.

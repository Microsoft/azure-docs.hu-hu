---
title: Karakterszámok – Translator
titleSuffix: Azure Cognitive Services
description: Ez a cikk azt ismerteti, Azure Cognitive Services Translator hogyan számolja meg a karaktereket, hogy megértse, hogyan képes tartalmat befogni.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 53fc22e1dbdac3240f72e8d64fbaee690597950f
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107373927"
---
# <a name="how-the-translator-counts-characters"></a>Hogyan számolja a Translator a karaktereket?

A Translator a bemeneti szöveg minden Unicode-kódpontját karakterként számolja meg. Egy szöveg fordítása egy nyelvre külön fordításnak számít, még akkor is, ha a kérés egyetlen API-hívásban lett meg, és több nyelvre lett lefordítva. A válasz hossza nem számít.

A darabszám a következő:

* A kérelem törzsében a Translatornak átadott szöveg
   * `Text` a Translate, Transliterate és Dictionary Lookup metódusok használata esetén
   * `Text` és `Translation` a Dictionary Examples metódus használata esetén
* Minden jelölő: HTML-, XML-címkék stb. a kérelem törzsének szövegmezőben. A kérés felépítéséhez használt JSON-notation (például "Text:") nem számít bele.
* Egyéni betű
* Írásjelek
* Szóköz, tabulátor, jelölő és bármilyen térköz karakter
* Minden Unicode-ban meghatározott kódpont
* Ismételt fordítás, még akkor is, ha korábban már lefordította ugyanazt a szöveget

Az olyan ideogramon alapuló szkriptek, mint a kínai és a japán Kanji, a Translator szolgáltatás továbbra is megszámolja az Unicode-kódpontok számát, ideogramonként egy karaktert. Kivétel: A Unicode-helyettesek két karakternek számítanak.

A kérések, szavak, bájtok vagy mondatok száma a karakterszám szempontjából irreleváns.

A Detect és BreakSentence metódusok hívásai nem számítanak bele a karakterfelhasználásba. Azonban arra számítunk, hogy a Detect és BreakSentence metódusok hívásai ésszerű arányban vannak más megszámolt függvények használatához. Ha az Észlelt vagy a BreakSentence hívások száma 100-szor meghaladja a többi megszámolt metódus számát, a Microsoft fenntartja a jogot a Detect és BreakSentence metódusok használatának korlátozására.

A translate függvénybe beküldött összes karakter akkor is beleszámul, ha a tartalom nem módosult, vagy ha a forrás- és célnyelv azonos.

A karakterszámokkal kapcsolatos további információkért olvassa el a [Translator GYIK című témakört.](https://www.microsoft.com/en-us/translator/faq.aspx)

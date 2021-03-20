---
title: Szöveg eltolása a Text Analytics APIban
titleSuffix: Azure Cognitive Services
description: A többnyelvű és Emoji kódolások által okozott eltolások ismertetése.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 03/09/2020
ms.author: aahi
ms.reviewer: jdesousa
ms.openlocfilehash: f5b63503792b13e089568004ba67e5be8a3d0c7f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98932360"
---
# <a name="text-offsets-in-the-text-analytics-api-output"></a>Szöveg eltolása a Text Analytics API kimenetében

A többnyelvű és Emoji támogatás olyan Unicode-kódolást eredményezett, amely egynél több [adatpontot](https://wikipedia.org/wiki/Code_point) használ egy megjelenített karakter (Graféma) megjelenítéséhez. Például a hangulatjelek, mint a 🌷, és 👍 több karaktert is használhatnak a vizuális attribútumok (például a bőr tónusa) további karakterekkel való összeállításához. Hasonlóképpen, a hindi szó `अनुच्छेद` öt betűt és három kombinációt jelöl.

A lehetséges többnyelvű és Emoji kódolások különböző hossza miatt a Text Analytics API a válaszban visszaadott eltolásokat adhat vissza.

## <a name="offsets-in-the-api-response"></a>Eltolások az API-válaszban. 

Ha az eltolásokat az API válasza adja vissza, például [elnevezett entitások felismerése](../how-tos/text-analytics-how-to-entity-linking.md) vagy [Hangulatelemzés](../how-tos/text-analytics-how-to-sentiment-analysis.md), jegyezze fel a következőket:

* A válasz elemei a hívott végpontra jellemzőek lehetnek. 
* A HTTP POST/GET adattartalmak [UTF-8](https://www.w3schools.com/charsets/ref_html_utf8.asp)kódolással vannak kódolva, ami esetleg nem lehet az alapértelmezett karakterkódolás az ügyféloldali fordító vagy operációs rendszer esetében.
* Az eltolások a Graféma a [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0) standard alapján, nem a karakterek számán alapulnak.

## <a name="extracting-substrings-from-text-with-offsets"></a>Alsztringek kibontása a szövegből eltolásokkal

Az eltolások problémákat okozhatnak a karakteres alsztring metódusok használatakor, például a .NET [alsztring ()](/dotnet/api/system.string.substring) metódust. Az egyik probléma az, hogy az eltolás egy alkarakterlánc-metódus befejezését eredményezheti egy többkarakteres Graféma-kódolás közepén, a végpont helyett.

A .NET-ben érdemes lehet a [StringInfo](/dotnet/api/system.globalization.stringinfo) osztályt használni, amely lehetővé teszi, hogy egy karakterláncot használjon szöveges elemekként, nem pedig az egyes karakteres objektumokat. Az előnyben részesített szoftveres környezetben is megkeresheti a Graféma-elosztó kódtárait. 

A Text Analytics API ezeket a szöveges elemeket is visszaadja az egyszerűség kedvéért.

## <a name="offsets-in-api-version-31-preview"></a>Eltolások az API 3,1-es verziójában – előzetes verzió

Az API 3,1-es verziójától kezdődően az előzetes verzió. 1, az eltolást visszaadó összes Text Analytics API-végpont támogatja a `stringIndexType` paramétert. Ez a paraméter módosítja az `offset` `length` API kimenetének és attribútumait, hogy azok megfeleljenek a kért karakterlánc-iterációs sémának. Jelenleg három típust támogatunk:

1. `textElement_v8` (alapértelmezett): iterációk a graphemes felett a [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0) standard által meghatározott módon
2. `unicodeCodePoint`: az Unicode- [hibakódok](http://www.unicode.org/versions/Unicode13.0.0/ch02.pdf#G25564), a Python 3 alapértelmezett sémája
3. `utf16CodeUnit`: iteráció az [UTF-16 kód egységeken](https://unicode.org/faq/utf_bom.html#UTF16), a JavaScript, a Java és a .net alapértelmezett sémája

Ha a `stringIndexType` kért programozási környezet megfelel a szükségesnek, az alkarakterlánc kinyerése szabványos alkarakterlánc vagy szelet metódusok használatával végezhető el. 

## <a name="see-also"></a>Lásd még

* [A Text Analytics áttekintése](../overview.md)
* [Hangulatelemzés](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Entitások felismerése](../how-tos/text-analytics-how-to-entity-linking.md)
* [Nyelv felismerése](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Nyelvi felismerés](../how-tos/text-analytics-how-to-language-detection.md)

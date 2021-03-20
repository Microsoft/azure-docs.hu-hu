---
title: Aktív tanulási javaslatok – QnA Maker
description: Az aktív tanulási javaslatok lehetővé teszik a Tudásbázis minőségének javítását azáltal, hogy a felhasználói beadványok alapján alternatív kérdéseket javasolnak a kérdés és a válasz párok számára.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: e1a8043912c984be46f85bd384a7049da27028b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96353238"
---
# <a name="active-learning-suggestions"></a>Aktív tanulási javaslatok

Az _aktív tanulási javaslatok_ funkció lehetővé teszi a Tudásbázis minőségének javítását azáltal, hogy a felhasználói beadványok alapján alternatív kérdéseket javasol a kérdés és a válasz párok számára. Ezeket a javaslatokat megtekintheti, vagy hozzáadhatja őket a meglévő kérdésekhez, vagy elutasíthatja őket.

A Tudásbázis nem változik automatikusan. A módosítások érvénybe léptetéséhez el kell fogadnia a javaslatokat. Ezek a javaslatok kérdéseket tesznek fel, de nem módosítják vagy nem távolítják el a meglévő kérdéseket.

## <a name="what-is-active-learning"></a>Mi az az aktív tanulás?

A QnA Maker implicit és explicit visszajelzéssel tanulja meg az új kérdések változatait.

* [Implicit visszajelzés](#how-qna-makers-implicit-feedback-works) – a Ranger tudomásul veszi, hogy ha egy felhasználó kérdése több választ tartalmaz, és a pontszámok nagyon közel vannak, és ezt visszajelzésnek tekinti. Ehhez semmit nem kell tennie.
* [Explicit visszajelzés](#how-you-give-explicit-feedback-with-the-train-api) – ha a Tudásbázisban több, kis eltéréssel rendelkező válasz érkezik, az ügyfélalkalmazás megkérdezi a felhasználót, hogy melyik kérdés a helyes kérdés. A rendszer elküldi a felhasználó explicit visszajelzését QnA Maker a [Train API](../How-to/improve-knowledge-base.md#train-api)-val.

Mindkét módszer biztosítja a rangsort a fürtözött hasonló lekérdezésekkel.

## <a name="how-active-learning-works"></a>Az aktív tanulás működése

Az aktív tanulás a QnA Maker által visszaadott leggyakoribb válaszok pontszámai alapján aktiválódik. Ha a pontszám különbsége a lekérdezésnek megfelelő QnA párok között egy kis hatótávolságon belül van, akkor a lekérdezés a lehetséges QnA-párokra vonatkozó lehetséges javaslatnak (alternatív kérdés) minősül. Miután elfogadta a javasolt kérdést egy adott QnA pár esetében, a rendszer elutasítja a többi párt. A javaslatok elfogadása után emlékezni kell a mentésre és a betanításra.

Az aktív tanulás a lehető legjobb javaslatokat kínálja azokban az esetekben, amikor a végpontok ésszerű mennyiségű és sokféle használati lekérdezést kapnak. Ha 5 vagy több hasonló lekérdezés van csoportosítva, 30 percenként QnA Maker javasolja, hogy a Tudásbázis tervezője a felhasználó-alapú kérdéseket fogadja el vagy utasítsa el. Az összes javaslat összevonása hasonló módon történik, és az alternatív kérdések leggyakoribb javaslatai a végfelhasználók által megadott lekérdezések gyakorisága alapján jelennek meg.

Ha kérdése van a QnA Maker portálon, tekintse át és fogadja el ezeket a javaslatokat. Nincs API a javaslatok kezeléséhez.

## <a name="turn-on-active-learning"></a>Az aktív tanulás bekapcsolása

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

Alapértelmezés szerint az aktív tanulás **ki van kapcsolva**.
Az aktív tanulás használatához:
* [Be kell kapcsolnia az aktív tanulást](../How-To/use-active-learning.md#turn-on-active-learning-for-alternate-questions) , hogy QnA Maker a Tudásbázisban alternatív kérdéseket gyűjtsön.
* A javasolt alternatív kérdések megtekintéséhez a szerkesztési lapon [használja a megtekintési beállításokat](../How-To/improve-knowledge-base.md#view-suggested-questions) .

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)

Alapértelmezés **szerint az Active** learning QnA Maker felügyelt (előzetes verzió). A javasolt alternatív kérdések megtekintéséhez a szerkesztési lapon [használja a megtekintési beállításokat](../How-To/improve-knowledge-base.md#view-suggested-questions) .

---

## <a name="how-qna-makers-implicit-feedback-works"></a>QnA Maker implicit visszajelzésének működése

QnA Maker implicit visszajelzése egy algoritmus használatával határozza meg a pontszám közelségét, majd aktív tanulási javaslatokat tesz. A közelség megállapításának algoritmusa nem egyszerű számítás. A következő példában szereplő tartományokat nem szabad kijavítani, hanem útmutatóként kell használni az algoritmus hatásának megismeréséhez.

Ha egy kérdés pontszáma nagyon magabiztos, például 80%, akkor az aktív tanuláshoz tartozó pontszámok köre nagyjából 10%-ban. Mivel a megbízhatósági pontszám csökken, például 40%, a pontszámok köre is csökken, körülbelül 4%-kal.

A következő JSON-válaszban QnA Maker generateAnswer, az A, B és C pontszámai közel vannak, és javaslatokként lesznek értelmezve.

```json
{
  "activeLearningEnabled": true,
  "answers": [
    {
      "questions": [
        "Q1"
      ],
      "answer": "A1",
      "score": 80,
      "id": 15,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q2"
      ],
      "answer": "A2",
      "score": 78,
      "id": 16,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q3"
      ],
      "answer": "A3",
      "score": 75,
      "id": 17,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q4"
      ],
      "answer": "A4",
      "score": 50,
      "id": 18,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    }
  ]
}
```

QnA Maker nem fogja tudni, hogy melyik válasz a legjobb válasz. A QnA Maker portál javaslatai közül válassza ki újra a legjobb választ és a betanítást.


## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>A Train API-val kapcsolatos explicit visszajelzések

QnA Maker explicit visszajelzést kell kapnia arról, hogy a válaszok közül melyik a legjobb válasz. A legjobb válasz meghatározása az alábbiak szerint történik:

* Felhasználói visszajelzések, kiválasztva az egyik választ.
* Üzleti logika, például egy elfogadható pontszám tartományának meghatározása.
* A felhasználói visszajelzések és az üzleti logika kombinációja.

A [Train API](/rest/api/cognitiveservices/qnamaker4.0/runtime/train) -val elküldheti a megfelelő választ QnA Makerre, miután a felhasználó kiválasztja.

## <a name="next-step"></a>Következő lépés

> [!div class="nextstepaction"]
> [A Tudásbázis lekérdezése](query-knowledge-base.md)
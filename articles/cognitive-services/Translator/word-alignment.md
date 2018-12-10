---
title: A Word igazítás – Translator Text API
titlesuffix: Azure Cognitive Services
description: A Translator Text API word igazítás adatok fogadására.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: v-jansko
ms.custom: seodec18
ms.openlocfilehash: 74e26d4bb9b9c901524a85248dbf7f9d6ead5e95
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53091475"
---
# <a name="how-to-receive-word-alignment-information"></a>Word igazítás információk fogadása

## <a name="receiving-word-alignment-information"></a>Rendszer adatokat küldjön a word igazítása
Igazítás információk fogadását, a fordítás módszert használja, és a választható includeAlignment paramétert.

## <a name="alignment-information-format"></a>Zarovnání információ formátuma
Igazítás, egy karakterlánc értékét a forrás minden szót a következő formátumban adja vissza. Az információk minden egyes szó egy szóközt, beleértve a nem szóközzel elválasztott nyelvet (szkriptek) kínai választják el:

[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]] *

Példa igazítás karakterlánc: "0:0-7:10 1:2-11:20 3:4 – 0:3 3:4-4:6 5:5 – 21:21".

Más szóval a kettőspont elválasztja a kezdő és záró index, a dash elkülöníti a nyelveket, és terület a szavakat osztja szét. Több szóból előfordulhat, hogy összhangba nulla, egy vagy több szóból más nyelven, és lehet, hogy a igazított szavak nem összefüggő. Zarovnání információ nem érhető el, ha az igazítási elem üres lesz. A metódus nincs hiba ebben az esetben adja vissza.

## <a name="restrictions"></a>Korlátozások
Zarovnání csak ezen a ponton visszaadott a nyelvi párok részéhez:
* a más nyelvre; angol nyelven
* az angol, egyszerűsített kínai, hagyományos kínai és lett az angol nyelvű bármilyen más nyelven
* Japán, koreai, illetve japán, koreai nem kap igazítás információk jelzésének fordítás a mondat helyett szerepel-e. Jelzésének fordítását. például a "Ez a tesztüzenet", "Szeretek,", és más nagy gyakoriságú mondatokat.

## <a name="example"></a>Példa

Példa JSON-ban

```json
[
  {
    "translations": [
      {
        "text": "Kann ich morgen Ihr Auto fahren?",
        "to": "de",
        "alignment": {
          "proj": "0:2-0:3 4:4-5:7 6:10-25:30 12:15-16:18 17:19-20:23 21:28-9:14 29:29-31:31"
        }
      }
    ]
  }
]
```

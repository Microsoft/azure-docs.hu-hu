---
title: Word-igazítás – Translator
titleSuffix: Azure Cognitive Services
description: Az igazítási adatok fogadásához használja a fordítási módszert, és adja meg a választható includeAlignment paramétert.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 8368ca0ca4c3f2f0ab3cb88a5d54295d71451636
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98898019"
---
# <a name="how-to-receive-word-alignment-information"></a>A Word igazítási információinak fogadása

## <a name="receiving-word-alignment-information"></a>Szó-igazítási információk fogadása
Az igazítási adatok fogadásához használja a fordítási módszert, és adja meg a választható includeAlignment paramétert.

## <a name="alignment-information-format"></a>Igazítási információ formátuma
Az igazítás a forrás minden szavához a következő formátumú karakterlánc-értékként lesz visszaadva. Az egyes szavak adatait szóközzel elválasztva, beleértve a nem szóközzel elválasztott nyelveket (parancsfájlokat), például a következőt:

[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]] *

Példa az igazítási karakterláncra: "0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21".

Más szóval a kettőspont elkülöníti a kezdő és a záró indexet, a kötőjel elválasztja a nyelveket, és a szóköz elválasztja a szavakat. Előfordulhat, hogy egy szó nulla, egy vagy több Szóval van igazítva a másik nyelven, és az illesztett szavak nem folytonos. Ha nincs elérhető igazítási információ, az igazítási elem üres lesz. A metódus nem ad vissza hibát ebben az esetben.

## <a name="restrictions"></a>Korlátozások
Az igazítás csak a nyelvi párok egy részhalmazára tér vissza ezen a ponton:
* Angolról bármely más nyelvre;
* bármely más nyelvről angolra, kivéve a kínai egyszerűsített, a kínai hagyományos és a lett angol nyelven
* Japánról koreaira vagy Koreairól japánra, nem kap igazítási információt, ha a mondat egy konzerv fordítás. A konzerv fordítás például "Ez egy teszt", "szeretlek", és más nagy gyakoriságú mondatok.

## <a name="example"></a>Példa

Példa JSON-ra

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

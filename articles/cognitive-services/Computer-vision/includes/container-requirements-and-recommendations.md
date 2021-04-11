---
title: A tárolóra vonatkozó követelmények és javaslatok
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/23/2020
ms.author: aahi
ms.openlocfilehash: 2a399b683dc9596d3514ce7d3be1fa2444449e2a
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284940"
---
> [!NOTE]
> A követelmények és javaslatok a másodpercenként egy egyszeri kéréssel rendelkező teljesítményteszteken alapulnak, és a beolvasott üzleti levél 8 MB-os képét, amely 29 sort és összesen 803 karaktert tartalmaz.

Az alábbi táblázat az egyes olvasási OCR-tárolók minimális és ajánlott erőforrás-elosztását ismerteti.

| Tároló | Minimális | Ajánlott |
|-----------|---------|-------------|
| Olvasás 2,0 – előzetes verzió | 1 mag, 8 GB memória |    8 mag, 16 GB memória |
| Olvasás 3,2 – előzetes verzió | 8 mag, 16 GB memória | 8 mag, 24 GB memória |

* Minden mag legalább 2,6 gigahertz (GHz) vagy gyorsabb lehet.

Az alap és a memória a `--cpus` `--memory` parancs részeként használt és beállításoknak felel meg `docker run` .

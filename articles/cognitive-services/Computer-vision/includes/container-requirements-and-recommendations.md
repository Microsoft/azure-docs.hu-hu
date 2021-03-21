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
ms.openlocfilehash: 4697be519eee96778eecdf37f7b358a88ad886c6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96006907"
---
> [!NOTE]
> A követelmények és javaslatok a másodpercenként egy egyszeri kéréssel rendelkező teljesítményteszteken alapulnak, és a beolvasott üzleti levél 8 MB-os képét, amely 29 sort és összesen 803 karaktert tartalmaz.

Az alábbi táblázat az egyes olvasási tárolók minimális és ajánlott erőforrás-elosztását ismerteti.

| Tároló | Minimális | Ajánlott |
|-----------|---------|-------------|
| Olvasás 2,0 – előzetes verzió | 1 mag, 8 GB memória |  8 mag, 16 GB memória |
| Olvasás 3,2 – előzetes verzió | 8 mag, 16 GB memória | 8 mag, 24 GB memória |

* Minden mag legalább 2,6 gigahertz (GHz) vagy gyorsabb lehet.

Az alap és a memória a `--cpus` `--memory` parancs részeként használt és beállításoknak felel meg `docker run` .

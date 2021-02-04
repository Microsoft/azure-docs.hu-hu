---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: 5508199212b788e13a27b3c97b8fb7174dc92f32
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530961"
---
Legalább azt javasoljuk, hogy a kezdeti betanítási készletben címkével legalább 30 képet használjon. A modell kiképzése után néhány további képet is szeretne gyűjteni.

A modell hatékony betanításához használjon vizuálisan változó rendszerképeket. Válassza ki azokat a lemezképeket, amelyek az alábbiak szerint változnak:
* kamera szöge
* világítás
* háttér
* vizualizáció stílusa
* Egyéni/csoportosított tárgy (ok)
* size
* típus

Továbbá győződjön meg arról, hogy az összes betanítási rendszerkép megfelel a következő feltételeknek:
* . jpg,. png,. bmp vagy. gif formátum
* nem nagyobb, mint a 6MB mérete (4MB nál)
* nem kevesebb, mint 256 képpont a legrövidebb szegélynél. az ennél rövidebb képeket a Custom Vision Service automatikusan felskálázásra kerül

> [!NOTE]
> Szüksége van egy szélesebb körű rendszerképekre a képzés befejezéséhez? A Microsoft garázs-projekt, amely lehetővé teszi, hogy beszerezze a betanítási célokra szánt lemezképek készleteit. A képek összegyűjtése után letöltheti őket, majd a szokásos módon importálhatja őket a Custom Vision-projektbe. További információért látogasson el az [adattárház oldalára](https://www.microsoft.com/en-us/ai/trove?activetab=pivot1:primaryr3) .
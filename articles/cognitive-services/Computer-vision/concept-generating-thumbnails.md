---
title: Intelligens vágású miniatűrök – Computer Vision
titleSuffix: Azure Cognitive Services
description: A képek bélyegképek létrehozásával kapcsolatos fogalmak a Computer Vision API használatával.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4874910f37b49990a659b48af0cf27921c3fcd5e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "68945234"
---
# <a name="generating-smart-cropped-thumbnails-with-computer-vision"></a>Intelligens vágású miniatűrök létrehozása Computer Vision

A miniatűr a képek csökkentett méretű ábrázolása. A miniatűröket a képek és egyéb adatelemek a könnyebben kezelhető, elrendezésre alkalmas módon jelölik. A Computer Vision API az intelligens levágást és a rendszerkép átméretezését használja egy adott rendszerkép intuitív bélyegképek létrehozásához.

A Computer Vision miniatűr létrehozási algoritmus a következőképpen működik:

1. Távolítsa el az elemeket a rendszerképből, és azonosítsa _annak a_ &mdash; képnek a területét, amelyben a fő objektum (ok) megjelenik.
1. A kép levágása az azonosított _terület_ alapján.
1. Módosítsa a méretarányt úgy, hogy az illeszkedjen a cél miniatűr méreteihez.

## <a name="area-of-interest"></a>Érdeklődési terület

Amikor feltölt egy rendszerképet, a Computer Vision API elemzi a fontos *terület* meghatározásához. Ennek a régiónak a segítségével meghatározhatja, hogyan vágja le a rendszerképet. A levágási művelet azonban mindig megegyezik a kívánt oldalaránysal, ha van ilyen.

A **areaOfInterest** API meghívásával is lekérheti az azonos *területhez* tartozó nyers határoló mezők koordinátáit. Ezt az információt használhatja, ha szeretné, hogy az eredeti rendszerkép módosítható legyen.

## <a name="examples"></a>Példák

A generált miniatűr az alábbi képen látható módon változhat, attól függően, hogy mit határoz meg a magasság, a szélesség és az intelligens levágás számára.

![Egy hegyi rendszerkép a különböző vágási konfigurációk mellett](./Images/thumbnail-demo.png)

Az alábbi táblázat a Computer Vision által generált tipikus miniatűröket mutatja be a képekhez. A bélyegképek egy adott célként megadott magasságra és 50 képpont szélességre lettek létrehozva, és az intelligens vágás engedélyezve van.

| Kép | Miniatűr |
|-------|-----------|
|![Kültéri hegyi naplemente, egy személy sziluettje](./Images/mountain_vista.png) | ![A kültéri hegy és a naplemente miniatűrje, a személy sziluettje](./Images/mountain_vista_thumbnail.png) |
|![Fehér virág Zöld háttérrel](./Images/flower.png) | ![A virág miniatűr nézetének elemzése](./Images/flower_thumbnail.png) |
|![Egy lakás-épület tetején található nő](./Images/woman_roof.png) | ![egy lakás épületének tetején található nő miniatűrje](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>Következő lépések

A [képek címkézésének](concept-tagging-images.md) és a [képek kategorizálásának](concept-categorizing-images.md)megismerése.

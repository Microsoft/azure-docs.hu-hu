---
title: Tartalom fordításának megakadályozása – fordító
titleSuffix: Azure Cognitive Services
description: A tartalom fordításának megakadályozása a fordítóval. A fordító lehetővé teszi a tartalom címkézését, hogy az ne legyen lefordítva.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: e89ff3f4c1f54d9e137d3dd51e325b908c826b03
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897476"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator"></a>A tartalom fordításának megakadályozása a fordítóval

A fordító lehetővé teszi a tartalom címkézését, hogy az ne legyen lefordítva. Érdemes lehet például kódokat, márkaneveket vagy olyan szavakat/kifejezéseket megjelölni, amelyek a fordításban értelmüket vesztik.

## <a name="methods-for-preventing-translation"></a>A fordítás megakadályozásának módszerei

1. A tartalom címkézése a alkalmazással `notranslate` . Úgy tervezték, hogy ez csak akkor működik, ha a bemeneti textType HTML-ként van beállítva

   Példa:

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

2. A tartalom címkézése a alkalmazással `translate="no"` . Ez csak akkor működik, ha a bemeneti textType HTML-ként van beállítva

   Példa:

   ```html
   <span translate="no">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div translate="no">This will not be translated.</div>
   <div>This will be translated. </div>
   ```
   
3. A [dinamikus szótár](dynamic-dictionary.md) használatával egy adott fordítást írhat elő.

4. Ne adja át a karakterláncot a fordítónak a fordításhoz.

5. Egyéni fordító: használjon egy [szótárt az egyéni fordítóban](custom-translator/what-is-dictionary.md) egy kifejezés fordításának megírásához 100%-os valószínűséggel.


## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Szöveg lefordítása a fordítási művelettel](reference/v3-0-translate.md)

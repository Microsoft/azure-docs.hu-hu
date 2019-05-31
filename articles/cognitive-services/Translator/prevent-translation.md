---
title: Megakadályozza a tartalom fordítási – Translator Text API
titlesuffix: Azure Cognitive Services
description: Megakadályozza a tartalom a Translator Text API-jának.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-pawal
ms.openlocfilehash: ec47c22ed38b7de261653eeeeafa4540b5d2164c
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66387612"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Tartalom a Translator Text API-jának megakadályozása

A Translator Text API lehetővé teszi a címke tartalmat úgy, hogy nem a lefordított. Például érdemes címkézése a kódot, márkanév vagy a szó vagy kifejezés, amely nem érthető, ha honosítva legyen.

## <a name="methods-for-preventing-translation"></a>A fordítási módszerek
1. Escape-Twitter címkére @somethingtopassthrough vagy #somethingtopassthrough. Megszünteti – escape-fordítás után.

2. Tartalom megjelölése `notranslate`.

   Példa:

   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

3. Használja a [dinamikus szótár](dynamic-dictionary.md) határozza meg a meghatározott fordítását.

4. A karakterlánc nem adja át a Translator Text API a fordítás.

5. Egyéni a fordítót: Használja a [egyéni a fordítót a szótár](custom-translator/what-is-dictionary.md) 100 %-os valószínűséget az határozza meg a kifejezések fordítása.


## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [A Translator API-hívás a fordítási elkerülése](reference/v3-0-translate.md)

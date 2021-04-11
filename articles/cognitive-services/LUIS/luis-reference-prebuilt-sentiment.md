---
title: Hangulat elemzése – LUIS
titleSuffix: Azure Cognitive Services
description: Ha a hangulati elemzés konfigurálva van, a LUIS JSON-válasz tartalmazza a hangulat elemzését.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/06/2021
ms.openlocfilehash: 7524644b34a6fd479c08b9ce6418c547c836add5
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554025"
---
# <a name="sentiment-analysis"></a>Hangulatelemzés
Ha a hangulati elemzés konfigurálva van, a LUIS JSON-válasz tartalmazza a hangulat elemzését. További információ az [text Analytics](../text-analytics/index.yml) dokumentációjában található érzelmek elemzéséről.

A LUIS Text Analytics v2 protokollt használ. 

Az Hangulatelemzés az alkalmazás közzétételekor van konfigurálva. További információért lásd: [alkalmazás közzététele](./luis-how-to-publish-app.md) .

## <a name="resolution-for-sentiment"></a>A hangulat feloldása

Az érzelmi adatmennyiség egy 1 és 0 közötti pontszám, amely az adatmennyiség pozitív (közelebbről 1) vagy negatív (közelebbről 0) hangulatát jelzi.

#### <a name="english-language"></a>[Angol nyelv](#tab/english)

Ha a kulturális környezet `en-us` , a válasz:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languages"></a>[További nyelvek](#tab/other-languages)

Az összes többi kulturális környezet esetében a válasz a következő:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * *

## <a name="next-steps"></a>Következő lépések

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

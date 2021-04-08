---
title: Szándék beszerzése a REST API használatával
description: Ebben a cikkben egy elérhető nyilvános LUIS-alkalmazás használatával határozhatja meg, hogy a felhasználó milyen szándéka van a társalgási szövegben.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: b49d90ffe2a262895727fdd4ef5f8a7950104102
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96437014"
---
# <a name="how-to-get-an-intent-using-the-rest-apis"></a>Szándék beszerzése a REST API-k használatával

Ebben a cikkben egy LUIS-alkalmazást fog használni, amellyel meghatározhatja, hogy a felhasználó milyen szándéka legyen a társalgási szövegből. Küldje el a felhasználó szándékát szövegként a pizza-alkalmazás HTTP-előrejelzési végpontjának. A-végponton LUIS a pizza-alkalmazás modelljét alkalmazza, hogy elemezze a szöveg természetes nyelvét, és meghatározza az alkalmazás tárgyi tartományára vonatkozó általános szándékot és kinyeri az adatokat.

Ehhez a cikkhez egy ingyenes [LUIS](https://www.luis.ai)-fiókra van szüksége.

<a name="create-luis-subscription-key"></a>

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# and REST](./includes/get-started-get-intent-rest-csharp.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [Get intent with Go and REST](./includes/get-started-get-intent-rest-go.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Get intent with Java and REST](./includes/get-started-get-intent-rest-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js and REST](./includes/get-started-get-intent-rest-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python and REST](./includes/get-started-get-intent-rest-python.md)]
::: zone-end

---
title: 'Rövid útmutató: Leképezés rest API-kkal - LUIS'
titleSuffix: Azure Cognitive Services
description: Ebben a REST API-gyorsindításban egy elérhető nyilvános LUIS-alkalmazás segítségével határozza meg a felhasználó szándékát a társalgási szövegből.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 02/14/2020
ms.author: diberry
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: 58f164f1522975faa2e7a7e7d7ed08145a6c5889
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77524648"
---
# <a name="quickstart-change-model-with-rest-apis"></a>Rövid útmutató: Modell módosítása REST API-kkal

Ebben a rövid útmutatóban adja hozzá a példakimondott szövegeket egy Utazási ügynök alkalmazáshoz, és tanítsa be az alkalmazást. A kimondott példaszövegek szándékhoz rendelt beszélgetésre szánt felhasználói szövegek. A szándékok kimondott példaszövegeinek megadásával megtanítja a LUIS-t arra, hogy milyen típusú felhasználói szöveg mely szándékhoz tartozik.

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# and REST](./includes/get-started-get-model-rest-csharp.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Get intent with Java and REST](./includes/get-started-get-model-rest-java.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [Get intent with Go and REST](./includes/get-started-get-model-rest-go.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js and REST](./includes/get-started-get-model-rest-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python and REST](./includes/get-started-get-model-rest-python.md)]
::: zone-end

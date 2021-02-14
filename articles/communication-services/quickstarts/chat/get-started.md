---
title: Rövid útmutató – csevegés hozzáadása az alkalmazáshoz
titleSuffix: An Azure Communication Services quickstart
description: Ez a rövid útmutató bemutatja, hogyan adhat hozzá kommunikációs szolgáltatásokat az alkalmazáshoz.
author: fanche
manager: phans
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python-swift
ms.openlocfilehash: 97b9644b3d075a0d65826cbd38747ff0e45d51a4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379662"
---
# <a name="quickstart-add-chat-to-your-app"></a>Rövid útmutató: csevegés hozzáadása az alkalmazáshoz

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Ismerkedjen meg az Azure kommunikációs szolgáltatásokkal a kommunikációs szolgáltatások csevegési ügyfelének könyvtárával, és vegyen fel valós idejű csevegést az alkalmazásba. Ebben a rövid útmutatóban a csevegési ügyféloldali kódtár használatával olyan csevegési szálakat hozhat létre, amelyek lehetővé teszik, hogy a felhasználók egymással beszélgessenek. Ha többet szeretne megtudni a csevegési fogalmakról, tekintse meg a [csevegés fogalmi dokumentációját](../../concepts/chat/concepts.md).

::: zone pivot="programming-language-javascript"
[!INCLUDE [Chat with JavaScript client library](./includes/chat-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Chat with Python client library](./includes/chat-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Chat with Java client library](./includes/chat-java.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [Chat with C# client library](./includes/chat-csharp.md)]
::: zone-end

::: zone pivot="programming-language-swift"
[!INCLUDE [Chat with iOS client library](./includes/chat-swift.md)]
::: zone-end

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné a kommunikációs szolgáltatások előfizetését, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek. További információ az [erőforrások tisztításáról](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta a következőket:

> [!div class="checklist"]
> * Csevegési ügyfél létrehozása
> * Két felhasználóval rendelkező szál létrehozása
> * Üzenet küldése a szálnak
> * Üzenetek fogadása egy szálból
> * Felhasználók eltávolítása egy szálból

> [!div class="nextstepaction"]
> [A csevegési hős alkalmazás kipróbálása](../../samples/chat-hero-sample.md)

A következőket is érdemes elvégezheti:

 - Tudnivalók a [csevegési fogalmakról](../../concepts/chat/concepts.md)
 - Ismerkedjen meg a [csevegő ügyféloldali függvénytárával](../../concepts/chat/sdk-features.md)

---
title: Rövid útmutató – csevegés hozzáadása az alkalmazáshoz
titleSuffix: An Azure Communication Services quickstart
description: Ez a rövid útmutató bemutatja, hogyan adhat hozzá kommunikációs szolgáltatásokat az alkalmazáshoz.
author: fanche
manager: phans
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python-swift-android
ms.openlocfilehash: 362c8c875ab878b23cffae45f4c653255f489c81
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105107031"
---
# <a name="quickstart-add-chat-to-your-app"></a>Rövid útmutató: csevegés hozzáadása az alkalmazáshoz

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


Ismerkedés az Azure kommunikációs szolgáltatásokkal a kommunikációs szolgáltatások csevegési SDK használatával valós idejű csevegést adhat hozzá az alkalmazáshoz. Ebben a rövid útmutatóban a csevegő SDK használatával olyan csevegési szálakat hozhat létre, amelyek lehetővé teszik, hogy a felhasználók egymással beszélgessenek. Ha többet szeretne megtudni a csevegési fogalmakról, tekintse meg a [csevegés fogalmi dokumentációját](../../concepts/chat/concepts.md).

::: zone pivot="programming-language-javascript"
[!INCLUDE [Chat with JavaScript SDK](./includes/chat-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Chat with Python SDK](./includes/chat-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Chat with Java SDK](./includes/chat-java.md)]
::: zone-end

::: zone pivot="programming-language-android"
[!INCLUDE [Chat with Android SDK](./includes/chat-android.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [Chat with C# SDK](./includes/chat-csharp.md)]
::: zone-end

::: zone pivot="programming-language-swift"
[!INCLUDE [Chat with iOS SDK](./includes/chat-swift.md)]
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
 - Ismerkedjen meg a [csevegő SDK](../../concepts/chat/sdk-features.md) -val

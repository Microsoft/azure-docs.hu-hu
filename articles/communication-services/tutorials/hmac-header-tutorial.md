---
title: 'Útmutató HTTP-kérelmek aláírásához C használatával #'
titleSuffix: An Azure Communication Services tutorial
description: Ismerje meg, hogyan lehet HTTP-kéréseket aláírni az Azure kommunikációs szolgáltatásokhoz a C# használatával.
author: alexandra142
manager: soricos
services: azure-communication-services
ms.author: apistrak
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e9b24e981e472371e477cd4dd895e976f709b2fb
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103490507"
---
# <a name="sign-an-http-request"></a>HTTP-kérelem aláírása

Ebből az oktatóanyagból megtudhatja, hogyan aláírhat egy HTTP-kérelmet egy HMAC aláírással.

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


[!INCLUDE [Sign an HTTP request C#](./includes/hmac-header-csharp.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A kommunikációs szolgáltatások előfizetésének tisztításához és eltávolításához törölje az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek. További információ az [Azure kommunikációs szolgáltatások erőforrásainak tisztításáról](../quickstarts/create-communication-resource.md#clean-up-resources) és a [Azure functions erőforrások tisztításáról](../../azure-functions/create-first-function-vs-code-csharp.md#clean-up-resources).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Hanghívás hozzáadása az alkalmazáshoz](../quickstarts/voice-video-calling/getting-started-with-calling.md)

A következőket is érdemes lehet:

- [Csevegés hozzáadása az alkalmazáshoz](../quickstarts/chat/get-started.md)
- [Felhasználói hozzáférési tokenek létrehozása](../quickstarts/access-tokens.md)
- [Az ügyfél és a kiszolgáló architektúrájának megismerése](../concepts/client-and-server-architecture.md)
- [A hitelesítés ismertetése](../concepts/authentication.md)

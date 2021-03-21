---
title: Rövid útmutató – hozzáférési tokenek létrehozása és kezelése
titleSuffix: An Azure Communication Services quickstart
description: Ismerje meg, hogyan kezelheti az identitásokat és a hozzáférési jogkivonatokat az Azure kommunikációs szolgáltatások identitás-ügyféloldali kódtár használatával.
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 921934e581d9b3d32cba644d85987ebb9802f73b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103495313"
---
# <a name="quickstart-create-and-manage-access-tokens"></a>Rövid útmutató: hozzáférési tokenek létrehozása és kezelése

Ismerkedjen meg az Azure kommunikációs szolgáltatásokkal a kommunikációs szolgáltatások Identity ügyféloldali kódtár használatával. Lehetővé teszi az identitások létrehozását és a hozzáférési jogkivonatok kezelését. Az identitás az alkalmazás entitását jelöli az Azure kommunikációs szolgáltatásban (például felhasználó vagy eszköz). A hozzáférési tokenek lehetővé teszik a csevegést és az ügyféloldali kódtárak közvetlen hitelesítését az Azure kommunikációs szolgáltatásokkal szemben. Javasoljuk, hogy egy kiszolgálóoldali szolgáltatáshoz hozzáférési jogkivonatokat generáljon. A hozzáférési tokeneket a rendszer a kommunikációs szolgáltatások ügyféloldali kódtárainak inicializálására használja az eszközökön.

A jelen oktatóanyagban szereplő képekben szereplő díjak kizárólag demonstrációs célokat szolgálnak.

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/user-access-token-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/user-access-token-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/user-access-token-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/user-access-token-java.md)]
::: zone-end

Az alkalmazás kimenete az összes befejezett műveletet leírja:
<!---cSpell:disable --->
```console
Azure Communication Services - Access Tokens Quickstart

Created an identity: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502

Issued an access token with 'voip' scope that expires at Fri Nov 27 2020 16:47:05 GMT-0800 (Pacific Standard Time):
<token signature here>

Successfully revoked all access tokens for identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502

Deleted the identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502
```
<!---cSpell:enable --->

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné a kommunikációs szolgáltatások előfizetését, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek. További információ az [erőforrások tisztításáról](./create-communication-resource.md#clean-up-resources).


## <a name="next-steps"></a>Következő lépések

Ennek a rövid útmutatónak a segítségével megtanulta a következőket:

> [!div class="checklist"]
> * Identitások kezelése
> * Hozzáférési tokenek kiadása
> * A kommunikációs szolgáltatások identitás ügyféloldali kódtár használata


> [!div class="nextstepaction"]
> [Hanghívás hozzáadása az alkalmazáshoz](./voice-video-calling/getting-started-with-calling.md)

A következőket is érdemes elvégezheti:

 - [A hitelesítés ismertetése](../concepts/authentication.md)
 - [Csevegés hozzáadása az alkalmazáshoz](./chat/get-started.md)
 - [Az ügyfél és a kiszolgáló architektúrájának megismerése](../concepts/client-and-server-architecture.md)

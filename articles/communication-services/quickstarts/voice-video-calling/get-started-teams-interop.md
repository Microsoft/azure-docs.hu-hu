---
title: Gyors útmutató – csapatok együttműködése az Azure kommunikációs szolgáltatásokban
titleSuffix: An Azure Communication Services quickstart
description: Ebből a rövid útmutatóból megtudhatja, hogyan csatlakozhat egy csapathoz az Azure Communication Calling SDK-val.
author: chpalm
ms.author: chpalm
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-web-ios-android
ms.openlocfilehash: 39c35bc90986126d099e68023e15b97c58d0ac26
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105108183"
---
# <a name="quickstart-join-your-calling-app-to-a-teams-meeting"></a>Gyors útmutató: a hívó alkalmazás csatlakoztatása egy Teams-értekezlethez

> [!IMPORTANT]
> A [csapat bérlői együttműködésének](../../concepts/teams-interop.md)engedélyezéséhez vagy letiltásához fejezze be [ezt az űrlapot](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u).

Ismerkedjen meg az Azure kommunikációs szolgáltatásokkal, ha a JavaScript SDK használatával csatlakoztatja a hívó megoldását a Microsoft Teams szolgáltatáshoz.

::: zone pivot="platform-web"
[!INCLUDE [Calling with JavaScript](./includes/teams-interop-javascript.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Calling with Android](./includes/teams-interop-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Calling with iOS](./includes/teams-interop-ios.md)]
::: zone-end

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné a kommunikációs szolgáltatások előfizetését, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek. További információ az [erőforrások tisztításáról](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Következő lépések

További információért tekintse át a következő cikkeket:

- Tekintse meg a [Calling Hero-mintát](../../samples/calling-hero-sample.md)
- További információ az [SDK-képességek meghívásáról](./calling-client-samples.md)
- További információ a [hívás működéséről](../../concepts/voice-video-calling/about-call-types.md)

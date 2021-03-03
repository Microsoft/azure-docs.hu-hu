---
title: Gyors útmutató – csapatok együttműködése az Azure kommunikációs szolgáltatásokban
titleSuffix: An Azure Communication Services quickstart
description: Ebből a rövid útmutatóból megtudhatja, hogyan csatlakozhat egy csapathoz az Azure Communication Calling SDK-val.
author: matthewrobertson
ms.author: chpalm
ms.date: 10/10/2020
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-web-ios-android
ms.openlocfilehash: 6da700fdd8149a7fff92ed0edef2015e354eca05
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101660045"
---
# <a name="quickstart-join-your-calling-app-to-a-teams-meeting"></a>Gyors útmutató: a hívó alkalmazás csatlakoztatása egy Teams-értekezlethez

> [!IMPORTANT]
> A [csapat bérlői együttműködésének](../concepts/teams-interop.md)engedélyezéséhez vagy letiltásához fejezze be [ezt az űrlapot](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u).

Ismerkedjen meg az Azure kommunikációs szolgáltatásaival, ha a JavaScript ügyféloldali kódtár használatával csatlakoztatja a hívó megoldást a Microsoft Teams szolgáltatáshoz.

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
- Tudnivalók az [ügyféloldali kódtár képességeinek meghívásáról](./calling-client-samples.md)
- További információ a [hívás működéséről](../../concepts/voice-video-calling/about-call-types.md)

---
title: Ismerkedés a Teams együttműködésével az Azure kommunikációs szolgáltatásokban
titleSuffix: An Azure Communication Services quickstart
description: Ebből a rövid útmutatóból megtudhatja, hogyan csatlakozhat egy csapathoz az Azure kommunikációs csevegési ügyféloldali kódtár használatával
author: askaur
ms.author: askaur
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 75580931b2df33b02b9759b6faaa5bec548daaae
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103496174"
---
# <a name="quickstart-join-your-chat-app-to-a-teams-meeting"></a>Gyors útmutató: csatlakozás a csevegési alkalmazáshoz egy csapat-értekezleten

> [!IMPORTANT]
> A [csapat bérlői együttműködésének](../../concepts/teams-interop.md)engedélyezéséhez vagy letiltásához fejezze be [ezt az űrlapot](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u).

Ismerkedés az Azure kommunikációs szolgáltatásokkal a csevegési megoldás Microsoft Teams-hez való csatlakoztatásával a JavaScript ügyféloldali kódtár használatával. 

## <a name="prerequisites"></a>Előfeltételek 

1.  [Csapatok üzembe helyezése](/deployoffice/teams-install). 
2. Egy működő [csevegési alkalmazás](./get-started.md). 

## <a name="enable-teams-interoperability"></a>Csoportok együttműködésének engedélyezése 

A kommunikációs szolgáltatások felhasználója, amely egy vendégként működő csapathoz csatlakozik, csak akkor férhet hozzá az értekezlet csevegéséhez, ha csatlakoztak a Teams Meeting híváshoz. Tekintse meg a [csapatok együttműködési](../voice-video-calling/get-started-teams-interop.md) dokumentációját, amelyből megtudhatja, hogyan adhat hozzá kommunikációs szolgáltatásokat használó felhasználókat egy csapat-értekezlethez.

A funkció használatához mindkét entitás tulajdonos szervezetének tagjának kell lennie.

[!INCLUDE [Join Teams meetings](./includes/meeting-interop-javascript.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné a kommunikációs szolgáltatások előfizetését, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek. További információ az [erőforrások tisztításáról](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Következő lépések

További információért tekintse át a következő cikkeket:

- Tekintse meg a [csevegési hős mintáját](../../samples/chat-hero-sample.md)
- További információ a [csevegés működéséről](../../concepts/chat/concepts.md)
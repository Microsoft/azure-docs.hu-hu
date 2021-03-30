---
title: Azure Notification Hubs iOS 13 frissítések | Microsoft Docs
description: Ismerje meg az Azure-Notification Hubs iOS 13-es feltörésének változásait
author: sethmanheim
ms.author: sethm
ms.date: 10/16/2019
ms.topic: article
ms.service: notification-hubs
ms.reviewer: jowargo
ms.lastreviewed: 10/16/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: df8560bec3671a9f05628ee6ed8ea95c31e9b16f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88998050"
---
# <a name="azure-notification-hubs-updates-for-ios-13"></a>Azure Notification Hubs frissítések iOS 13 rendszerhez

Az Apple nemrég módosította a nyilvános leküldéses szolgáltatást; a módosítások többnyire az iOS 13 és a Xcode kiadásával összhangban vannak. Ez a cikk ismerteti a változások hatását az Azure Notification Hubsban.

## <a name="apns-push-payload-changes"></a>APNS leküldéses adattartalom módosítása

### <a name="apns-push-type"></a>APNS leküldéses típusa

Az Apple mostantól megköveteli, hogy a fejlesztők riasztási vagy háttérbeli értesítésként azonosítsák az értesítéseket a `apns-push-type` APNS API új fejlécében. Az [Apple dokumentációja](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns)szerint: "ennek a fejlécnek az értékének pontosan tükröznie kell az értesítési tartalom tartalmát. Ha eltérés van, vagy ha hiányzik a fejléc a szükséges rendszereken, a APNs hibát jelez, késleltetheti az értesítés kézbesítését, vagy elhúzhatja azt. "

A fejlesztőknek most be kell állítania ezt a fejlécet olyan alkalmazásokban, amelyek értesítést küldenek az Azure Notification Hubson keresztül. Technikai korlátozás miatt az ügyfeleknek jogkivonat-alapú hitelesítést kell használniuk a APNS hitelesítő adataihoz az attribútumot tartalmazó kérelmekkel. Ha tanúsítványalapú hitelesítést használ a APNS hitelesítő adataihoz, a jogkivonat-alapú hitelesítés használatára kell váltania.

Az alábbi mintakód bemutatja, hogyan állíthatja be ezt a fejléc-attribútumot az Azure Notification Hubs által küldött értesítési kérelmekben.

#### <a name="template-notifications---net-sdk"></a>Sablon értesítései – .NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var tempprop = new Dictionary<string, string> {{"message", "value"}};
var notification = new TemplateNotification(tempprop);
notification.Headers = headers;
await hub.SendNotificationAsync(notification);
```

#### <a name="native-notifications---net-sdk"></a>Natív értesítések – .NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

#### <a name="direct-rest-calls"></a>Közvetlen REST-hívások

```csharp
var request = new HttpRequestMessage(method, $"<resourceUri>?api-version=2017-04");
request.Headers.Add("Authorization", createToken(resourceUri, KEY_NAME, KEY_VALUE));
request.Headers.Add("ServiceBusNotification-Format", "apple");
request.Headers.Add("apns-push-type", "alert");
```

Ha az Azure Notification Hubs olyan értesítést észlel, amely nem rendelkezik a `apns-push-type` készlettel, a szolgáltatás kikövetkezteti a leküldéses típust az értesítési kérelemből, és automatikusan beállítja az értéket. Ne feledje, hogy az Azure Notification Hubs-t úgy kell konfigurálnia, hogy jogkivonat-alapú hitelesítést használjon a szükséges fejléc beállításához. További információ: [jogkivonat-alapú (http/2) hitelesítés a APNS](./notification-hubs-push-notification-http2-token-authentication.md).

## <a name="apns-priority"></a>APNS prioritása

Egy másik kisebb változás, de egy, a háttér-alkalmazás értesítéseket küldő hátterének módosítását igényli, hogy a `apns-priority` fejlécnek most 5 értékre kell állítania. Számos alkalmazás a `apns-priority` fejlécet 10 (azonnali kézbesítést jelző) értékre állítja be, vagy nem állítja be, és nem kapja meg az alapértelmezett értéket (amely szintén 10).

Ha ezt az értéket 10 értékre állítja, az már nem engedélyezett a háttérben futó értesítések esetében, és minden kérelemnél meg kell adnia az értéket. Ha hiányzik az érték, az Apple nem küld háttérbeli értesítéseket. Például:

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "background"}, { "apns-priority", "5" }};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

## <a name="sdk-changes"></a>SDK-változások

Az iOS-fejlesztők évek óta használták a `description` `deviceToken` leküldéses jogkivonat delegálására küldött adatok attribútumát, hogy kinyerje a háttérbeli alkalmazás által az értesítések küldéséhez használt leküldéses tokent. Az Xcode 11 esetében az `description` attribútum más formátumra változott. A meglévő kód, amelyet az attribútumhoz használt fejlesztők már megszakítottak. Frissítettük az Azure Notification Hubs SDK-t a változás befogadásához, ezért frissítse az alkalmazásai által használt SDK-t az [azure Notification Hubs iOS SDK](https://github.com/Azure/azure-notificationhubs-ios)2.0.4 vagy újabb verziójára.

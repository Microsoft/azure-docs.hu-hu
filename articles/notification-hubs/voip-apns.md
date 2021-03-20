---
title: APNS VOIP-értesítések küldése az Azure Notification Hubs
description: Ismerje meg, hogyan küldhet APNS VOIP-értesítéseket az Azure Notification Hubs (nem hivatalosan támogatott).
author: sethmanheim
ms.author: sethm
ms.date: 3/23/2020
ms.topic: how-to
ms.service: notification-hubs
ms.openlocfilehash: c99af881b8f93b75633741c2352dc5df17dd2963
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "80146887"
---
# <a name="use-apns-voip-through-notification-hubs-not-officially-supported"></a>A APNS VOIP használata Notification Hubson keresztül (hivatalosan nem támogatott)

A APNS VOIP-értesítéseket az Azure Notification Hubs használatával lehet használni; Ehhez a forgatókönyvhöz azonban nincs hivatalos támogatás.

## <a name="considerations"></a>Megfontolandó szempontok

Ha továbbra is APNS VOIP-értesítéseket küld Notification Hubson keresztül, vegye figyelembe a következő korlátozásokat:

- A VOIP-értesítések küldése megköveteli `apns-topic` , hogy a fejléc az ALKALMAZÁSCSOMAG azonosítójának és az `.voip` utótagnak legyen beállítva. Például a Bundle ID-vel rendelkező minta alkalmazás esetében `com.microsoft.nhubsample` a fejlécet a következőre `apns-topic` kell beállítani: `com.microsoft.nhubsample.voip.`

   Ez a módszer nem működik megfelelően az Azure Notification Hubsban, mert az alkalmazás köteg-AZONOSÍTÓját a hub APNS hitelesítő adatainak részeként kell konfigurálni, és az érték nem módosítható. Emellett a Notification Hubs nem teszi lehetővé a fejléc értékének `apns-topic` felülbírálását futásidőben.

   A VOIP-értesítések küldéséhez külön értesítési központot kell konfigurálnia az alkalmazáscsomag- `.voip` azonosítóval.

- A VOIP-értesítések küldése megköveteli `apns-push-type` , hogy a fejléc az értékre legyen állítva `voip` .

   Annak érdekében, hogy az ügyfelek az iOS 13 rendszerre való áttéréssel segítsenek, Notification Hubs megkísérli a fejléc helyes értékének következtetését `apns-push-type` . A következtetési logika szándékosan egyszerű, a szabványos értesítések megszakításának elkerülése érdekében. Sajnos ez a módszer a VOIP-értesítésekkel kapcsolatos problémákat okoz, mivel az Apple a VOIP-értesítéseket olyan speciális esetként kezeli, amely nem ugyanazokat a szabályokat követi, mint a normál értesítések.

   A VOIP-értesítések küldéséhez explicit értéket kell megadnia a `apns-push-type` fejléchez.

- Notification Hubs korlátozza a APNS hasznos adattartalmakat 4 KB-ra, az Apple által dokumentált módon. A VOIP-értesítések esetében az Apple legfeljebb 5 KB-os hasznos adatokat tesz lehetővé. Notification Hubs nem tesz különbséget a standard és a VOIP-értesítések között; Ezért az összes értesítés 4 KB-ra van korlátozva.

   A VOIP-értesítések küldéséhez ne lépje túl a 4 KB-os hasznos adattartalom korlátját.

## <a name="next-steps"></a>Következő lépések

További információk az alábbi hivatkozásokra kattintva érhetők el:

- [Dokumentáció `apns-topic` és `apns-push-type` fejlécek és értékek, beleértve a VoIP-értesítések speciális eseteit](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns).

- [Dokumentáció a hasznos adatok méretének korlátozásához](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification).

- [Az iOS 13 Notification Hubs frissítései](push-notification-updates-ios-13.md#apns-push-type).

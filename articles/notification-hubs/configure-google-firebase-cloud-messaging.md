---
title: A Google Firebase Cloud Messaging konfigurálása az Azure Notification Hubsban | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat egy Azure Notification hub-t a Google Firebase Cloud Messaging-beállításaival.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 08/04/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 0da103c11e2412108535ca322917632f5d95559d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96003565"
---
# <a name="configure-google-firebase-settings-for-a-notification-hub-in-the-azure-portal"></a>A Google Firebase beállításainak konfigurálása egy értesítési központhoz a Azure Portal

Ez a cikk bemutatja, hogyan konfigurálhatja a Google Firebase Cloud Messaging (FCM) beállításait egy Azure Notification hub számára a Azure Portal használatával.  

## <a name="prerequisites"></a>Előfeltételek

Ha még nem hozott létre egy értesítési központot, hozzon létre egyet most. További információ: [Azure Notification hub létrehozása a Azure Portalban](create-notification-hub-portal.md).

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>A Google Firebase Cloud Messaging (FCM) konfigurálása

Az alábbi eljárás ismerteti az értesítési központ Google Firebase Cloud Messaging (FCM) beállításainak konfigurálásához szükséges lépéseket:

1. A Azure Portal az **értesítési központ** lapon válassza a **Google (GCM/FCM)** elemet a bal oldali menüben.
2. Illessze be a korábban mentett FCM-projekthez tartozó **API-kulcsot** .
3. Kattintson a **Mentés** gombra.

   ![Képernyőkép, amely bemutatja, hogyan konfigurálható Notification Hubs a Google FCM-hez](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>Következő lépések

Az Azure Notification Hubs és a Google Firebase Cloud Messaging használatával történő értesítések az Android-eszközökre történő küldésének részletes ismertetését lásd: [leküldéses értesítések küldése Android-eszközökre Notification Hubs és a Google FCM használatával](notification-hubs-android-push-notification-google-fcm-get-started.md).

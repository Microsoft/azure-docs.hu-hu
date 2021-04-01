---
title: A Windows leküldéses értesítési szolgáltatás konfigurálása az Azure Notification Hubsban | Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja az Azure Notification hub Windows leküldéses értesítési szolgáltatásának beállításait.
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
ms.openlocfilehash: 62d73a23fff3bd15d873a579d451b2f91509b7fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87758730"
---
# <a name="configure-windows-push-notification-service-settings-in-the-azure-portal"></a>A Windows leküldéses értesítési szolgáltatás beállításainak konfigurálása a Azure Portal

Ez a cikk bemutatja, hogyan konfigurálhatja az Azure Notification hub Windows Notification Service (WNS) beállításait a Azure Portal használatával.  

## <a name="prerequisites"></a>Előfeltételek

Ha még nem hozott létre egy értesítési központot, hozzon létre egyet most. További információ: [Azure Notification hub létrehozása a Azure Portalban](create-notification-hub-portal.md).

## <a name="configure-windows-push-notification-service-wns"></a>A Windows leküldéses értesítési szolgáltatás (WNS) konfigurálása

Az alábbi eljárás ismerteti az értesítési központ Windows leküldéses értesítési szolgáltatásának (WNS) beállításainak konfigurálásához szükséges lépéseket:

1. A Azure Portal az **értesítési központ** lapon válassza a **Windows (WNS)** lehetőséget a bal oldali menüben.
2. Adja meg a **csomag biztonsági azonosítójának** és **biztonsági kulcsának** értékeit.
3. Kattintson a **Mentés** gombra.

   ![Képernyőfelvétel a csomag biztonsági AZONOSÍTÓjának és biztonsági kulcsának mezőiről](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>Következő lépések

A leküldéses Univerzális Windows-platform értesítések az Azure Notification Hubs és a Windows leküldéses értesítési szolgáltatással (WNS) való küldésével kapcsolatos részletes utasításokért lásd: [értesítések küldése UWP-alkalmazásoknak az azure Notification Hubs használatával](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

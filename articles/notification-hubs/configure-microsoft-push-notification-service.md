---
title: A Microsoft leküldéses értesítési szolgáltatásának konfigurálása az Azure Notification Hubsban | Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja a Microsoft leküldéses értesítési szolgáltatás beállításait egy Azure Notification hub számára.
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
ms.openlocfilehash: 6784331dccd779390ace7dc632d54fe54aedc458
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87760991"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-in-the-azure-portal"></a>A Microsoft leküldéses értesítési szolgáltatás (MPNS) beállításainak konfigurálása a Azure Portal

Ez a cikk bemutatja, hogyan konfigurálhatja a Microsoft leküldéses értesítési szolgáltatás (MPNS) beállításait egy Azure Notification hub számára a Azure Portal használatával.

## <a name="prerequisites"></a>Előfeltételek

Ha még nem hozott létre egy értesítési központot, hozzon létre egyet most. További információ: [Azure Notification hub létrehozása a Azure Portalban](create-notification-hub-portal.md).

## <a name="configure-microsoft-push-notification-service-mpns"></a>A Microsoft push Notification szolgáltatás (MPNS) konfigurálása

Az alábbi eljárás ismerteti, hogyan konfigurálhatja az értesítési központ Microsoft push Notification szolgáltatás (MPNS) beállításait:

1. A Azure Portal az **értesítési központ** lapon válassza a **Windows Phone-telefon (MPNS)** elemet a bal oldali menüben.
2. Nem hitelesített vagy hitelesített leküldéses értesítések engedélyezése:

   a. A nem hitelesített leküldéses értesítések engedélyezéséhez válassza a nem **hitelesített leküldéses**  >  **Mentés** engedélyezése lehetőséget.

      ![A nem hitelesített leküldéses értesítések engedélyezését bemutató képernyőkép](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Hitelesített leküldéses értesítések engedélyezése:
      * Az eszköztáron válassza a **tanúsítvány feltöltése** lehetőséget.
      * Válassza ki a fájl ikont, majd válassza ki a tanúsítványfájl.
      * Adja meg a tanúsítványhoz tartozó jelszót.
      * Válassza az **OK** lehetőséget.
      * A **Windows Phone-telefon (MPNS)** lapon válassza a **Mentés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Az Azure Notification Hubs és a Microsoft leküldéses értesítési szolgáltatás (MPNS) használatával Windows Phone-telefon eszközökre irányuló értesítések küldésével kapcsolatos részletes utasításokért lásd: [leküldéses értesítések küldése Windows Phone-telefon alkalmazásoknak Notification Hubs használatával](notification-hubs-windows-mobile-push-notifications-mpns.md).

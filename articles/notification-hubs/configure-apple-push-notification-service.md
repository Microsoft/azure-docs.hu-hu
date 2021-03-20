---
title: Apple Push Notification Service konfigurálása az Azure Notification Hubsban | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat egy Azure Notification hub-t Apple Push Notification Service (APNS) beállításokkal.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 06/22/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 63c7e0c9569428b55420911f253deee52ce440cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85255398"
---
# <a name="configure-apple-push-notification-service-settings-for-a-notification-hub-in-the-azure-portal"></a>Egy értesítési központ Apple Push Notification Service beállításainak konfigurálása a Azure Portal

Ez a cikk bemutatja, hogyan konfigurálhatja az Azure Notification hub Apple Push Notification Service (APNS) beállításait a Azure Portal használatával.

## <a name="prerequisites"></a>Előfeltételek

Ha még nem hozott létre egy értesítési központot, hozzon létre egyet most. További információ: [Azure Notification hub létrehozása a Azure Portalban](create-notification-hub-portal.md).

## <a name="configure-apple-push-notification-service"></a>Apple Push Notification Service konfigurálása

Az alábbi eljárás az értesítési központ Apple Push Notification Service (APNS) beállításainak konfigurálásához nyújt útmutatást:

1. A Azure Portal az **értesítési központ** lapon válassza az **Apple (APNS)** lehetőséget a bal oldali menüben.

1. A **hitelesítési mód** beállításnál válassza a **tanúsítvány** vagy a **token** lehetőséget.

   - Ha a **tanúsítvány** lehetőséget választja:
      - Válassza ki a fájl ikont, majd válassza ki a feltölteni kívánt *. P12* -fájlt.
      - Adjon meg egy jelszót.
      - Válassza a **Védőfal** módot. Ha leküldéses értesítéseket szeretne küldeni az alkalmazást az áruházból megvásárolt felhasználóknak, válassza a **termelési** mód lehetőséget.

     ![Képernyőkép a APNS-tanúsítvány konfigurációjának Azure Portal](./media/configure-apple-push-notification-service/notification-hubs-apple-config-cert.png)

   - Ha a **jogkivonat** lehetőséget választja:
      - Adja meg a **kulcs azonosítójának**, **a köteg azonosítójának**, a **csoport azonosítójának** és a **tokennek** az értékeit.
      - Válassza a **Védőfal** módot. Ha leküldéses értesítéseket szeretne küldeni az alkalmazást az áruházból megvásárolt felhasználóknak, válassza a **termelési** mód lehetőséget.

     ![Képernyőkép a APNS-jogkivonat konfigurációjának Azure Portal](./media/configure-apple-push-notification-service/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>Következő lépések

Az értesítések iOS-eszközökre való küldésével kapcsolatos részletes útmutatót az alábbi cikkben találja: [leküldéses értesítések küldése iOS-alkalmazásokba az Azure Notification Hubs használatával](ios-sdk-get-started.md).

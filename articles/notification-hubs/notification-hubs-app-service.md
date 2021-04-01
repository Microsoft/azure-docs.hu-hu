---
title: Notification Hubs integráció a App Service Mobile Apps
description: Ismerje meg, hogyan működik az Azure Notification Hubs Azure App Service Mobile Apps.
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 08/06/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: e7042be7e407e8e0827e142ba6878dfff812e1f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88004063"
---
# <a name="integration-with-app-service-mobile-apps"></a>App Service Mobile Apps-integráció

Az Azure-szolgáltatások zökkenőmentes és egységesített élményének megkönnyítése érdekében [app Service Mobile apps](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) beépített támogatást nyújt az Azure Notification Hubst használó értesítések számára. A [app Service Mobile apps](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) egy nagyvállalati fejlesztőknek és rendszerintegrátoroknak szánt, rugalmasan méretezhető, globálisan elérhető mobileszköz-fejlesztési platform, amely számos képességet biztosít a mobil fejlesztők számára.

Mobile Apps fejlesztők a következő munkafolyamattal használhatják a Notification Hubst:

1. Eszköz PNS-leírójának beolvasása.
2. Az eszköz regisztrálása a Notification Hubs Mobile Apps ügyféloldali SDK regisztrációs API-k használatával.

    > [!NOTE]
    > Vegye figyelembe, hogy a Mobile Apps biztonsági okokból eltávolítja az összes regisztrációs címkét. Közvetlenül a háttérrendszerről használhatja a Notification Hubst a címkék és eszközök társításához.

3. Értesítések küldése az alkalmazás hátteréből Notification Hubs.

Az integráció előnyei a következők:

- **Mobile apps ügyfél SDK**-k: ezek a többplatformos SDK-k API-kat biztosítanak a regisztrációhoz, és kommunikálnak a Mobile App szolgáltatással összekapcsolt értesítési központtal. Nincs szükség Notification Hubs hitelesítő adatokra, vagy egy további szolgáltatással kell dolgoznia.
  - *Leküldés a felhasználónak*: az SDK-k automatikusan címkézik a megadott eszközt egy Mobile apps hitelesített felhasználói azonosítóval a "leküldéses felhasználó" forgatókönyv engedélyezéséhez.
  - *Leküldés az eszközre*: az SDK-k automatikusan a Mobile apps telepítési azonosítót használják GUID-ként a Notification Hubshoz való regisztráláshoz, így nem kell több szolgáltatás-GUID-t fenntartania.
- **Telepítési modell**: a Mobile apps együttműködik a Notification Hubs legújabb leküldéses modellel, amely az eszközhöz társított összes leküldéses tulajdonságot megjeleníti egy olyan JSON-telepítésben, amely a leküldéses Notification Serviceshoz igazodik, és könnyen használható.
- **Rugalmasság**: A fejlesztők mindig választhatják a Notification Hubs közvetlen használatát, még az integráció megvalósítása után is.
- **A [Azure Portal](https://portal.azure.com)integrált felhasználói felülete**: a leküldéses funkció a Mobile apps vizuálisan ábrázolja, a fejlesztők pedig könnyedén dolgozhatnak a kapcsolódó értesítési központtal a Mobile Appson keresztül.

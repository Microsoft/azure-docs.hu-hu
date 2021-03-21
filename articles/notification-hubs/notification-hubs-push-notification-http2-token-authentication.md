---
title: Jogkivonat-alapú (HTTP/2) hitelesítés a APNS az Azure Notification Hubsban | Microsoft Docs
description: Ismerje meg, hogyan használható az új jogkivonat-hitelesítés a APNS.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 11/14/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/13/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 4f8de6389a04448579672b84e91f0bb4dd0f4ce2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96460471"
---
# <a name="token-based-http2-authentication-for-apns"></a>Jogkivonat-alapú (HTTP/2) hitelesítés a APNS

## <a name="overview"></a>Áttekintés

Ez a cikk azt ismerteti, hogyan használható az új APNS HTTP/2 protokoll jogkivonat-alapú hitelesítéssel.

Az új protokoll használatának legfontosabb előnyei a következők:

* A jogkivonat létrehozása viszonylag egyszerű (a tanúsítványokhoz képest)
* Nincs több lejárati dátum – a hitelesítési jogkivonatok és azok visszavonása felett van
* A hasznos adatok akár 4 KB-os is lehetnek
* Szinkron visszajelzés
* Az Apple legújabb protokollját használja – a tanúsítványok továbbra is a bináris protokollt használják, amely elavultként van megjelölve.

Ezt az új mechanizmust két lépésben hajthatja végre:

* Szerezze be a szükséges információkat az Apple fejlesztői fiók portálról.
* Konfigurálja az értesítési központot az új adatokkal.

A Notification Hubs most úgy van beállítva, hogy az új hitelesítési rendszer használatára APNS.

Vegye figyelembe, hogy ha a APNS tanúsítvány-hitelesítő adatok használatával migrálta a tanúsítványt, a jogkivonat tulajdonságai felülírják a tanúsítványt a rendszeren, de az alkalmazás zökkenőmentesen fogadja az értesítéseket.

## <a name="obtaining-authentication-information-from-apple"></a>Hitelesítési adatok beszerzése az Apple-től

A jogkivonat-alapú hitelesítés engedélyezéséhez a következő tulajdonságok szükségesek az Apple Developer-fiókjából:

### <a name="key-identifier"></a>Kulcs azonosítója

A kulcs azonosítóját a **tanúsítványok, azonosítók & profilok** elem alatti **kulcsok** lapon érheti el az Apple fejlesztői fiókjában:

![Tanúsítványok](./media/notification-hubs-push-notification-http2-token-authentification/keys.png)

![Azonosítók](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier-and-application-name"></a>Alkalmazás azonosítója és az alkalmazás neve

Az alkalmazás neve és azonosítója a fejlesztői fiók **tanúsítványok, azonosítók & profilok** lapján is elérhető:

![Tanúsítványok és azonosítók](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

### <a name="configure-via-the-net-sdk-or-the-azure-portal"></a>Konfigurálás a .NET SDK vagy a Azure Portal használatával

A központot úgy konfigurálhatja, hogy jogkivonat-alapú hitelesítést használjon a [legújabb ÜGYFÉLOLDALI SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs)-val vagy a Azure Portal. Ha engedélyezni szeretné a jogkivonat-alapú hitelesítést a portálon, jelentkezzen be a Azure Portalba, és lépjen az értesítési központ **settings > Apple (APNS)** panelre. A **hitelesítési mód** tulajdonságból válassza ki a **tokent** , hogy az összes releváns jogkivonat-tulajdonsággal frissítse a hubot.

![Jogkivonat konfigurálása](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Adja meg az Apple Developer-fiókjából beolvasott tulajdonságokat.
* Válassza ki az alkalmazási módot (**éles** vagy **homokozó**).
* Kattintson a **Save (Mentés** ) gombra a APNS hitelesítő adatainak frissítéséhez.

A jogkivonat-alapú hitelesítő adatok a következő mezőkből állnak:

* **Key ID**: az Apple fejlesztői portálon létrehozott titkos kulcs azonosítója; például: `2USFGKSKLT` .
* **Csoport azonosítója**: "előtag" vagy "alkalmazás előtagja". Ez a szervezet azonosítója az Apple fejlesztői portálon; például: `S4V3D7CHJR` .
* **Bundle-azonosító**: az "app id" néven is ismert. Ez az alkalmazás köteg-azonosítója; például: `com.example.myapp` . Vegye figyelembe, hogy egy alkalmazáshoz csak egy kulcsot használhat. Ez az érték leképezi a `apns-topic` http-fejlécet egy értesítés küldésekor, és az adott alkalmazás célzására szolgál. Explicit módon nem állítható be a megadott érték `apns-topic` .
* **Token**: "Key" vagy "Private Key" néven is ismert. Ez az Apple fejlesztői portálon létrehozott. P8 fájlból szerezhető be. A kulcsnak engedélyezve kell lennie a APNS (amely a kulcs létrehozásakor az Apple fejlesztői portálon van kiválasztva). Az értéknek az NH Portal/API-hoz való megadásakor el kell távolítania a PEM fejlécét vagy láblécét.
* **Végpont**: ez egy váltógomb a Notification Hubs-portál panelen és egy karakterlánc-mező az API-ban. Az érvényes értékek a következők: `https://api.development.push.apple.com:443/3/device` vagy `https://api.sandbox.push.apple.com:443/3/device` . Az Notification Hubs ezt az értéket használja az éles vagy a homokozó környezethez az értesítések küldéséhez. Ennek meg kell egyeznie az `aps-environment` alkalmazás jogosultságával, ellenkező esetben a GENERÁLT APNS-eszközök jogkivonatai nem egyeznek a környezettel, és az értesítések nem lesznek elküldve.

A helyes használatot bemutató kód minta:

```csharp
NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
string token = "YOUR PRIVATE KEY HERE";
string keyId = "YOUR KEY ID HERE";
string appName = "YOUR APP NAME HERE";
string appId = "YOUR APP ID HERE";
NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
nm.UpdateNotificationHubAsync(desc);
```

## <a name="next-steps"></a>Következő lépések

* [Azure Notification Hub-központ létrehozása az Azure Portalon](create-notification-hub-portal.md)
* [Értesítési központ konfigurálása a Azure Portal](create-notification-hub-portal.md)

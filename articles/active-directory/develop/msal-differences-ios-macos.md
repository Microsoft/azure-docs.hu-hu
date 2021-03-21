---
title: MSAL iOS-& macOS-különbségek | Azure
titleSuffix: Microsoft identity platform
description: Ismerteti a Microsoft Authentication Library (MSAL) használatának eltéréseit az iOS és a macOS között.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 59e4111b6dda386777e820c9be16ace9ff01135c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98064915"
---
# <a name="microsoft-authentication-library-for-ios-and-macos-differences"></a>Microsoft Authentication Library iOS és macOS rendszerekre

Ez a cikk az iOS és a macOS rendszerhez készült Microsoft Authentication Library (MSAL) közötti különbségeket ismerteti.

> [!NOTE]
> A Mac gépen a MSAL csak a macOS-alkalmazásokat támogatja.

## <a name="general-differences"></a>Általános különbségek

A macOS-hez készült MSAL az iOS-hez elérhető funkciók egy részhalmaza.

A macOS rendszerhez készült MSAL nem támogatja a következőket:

- különböző böngésző-típusok, például: `ASWebAuthenticationSession` , `SFAuthenticationSession` `SFSafariViewController` .
- a Microsoft Authenticator alkalmazáson keresztül felügyelt hitelesítés macOS rendszeren nem támogatott.

Az azonos közzétevőtől származó alkalmazások közötti kulcstartó-megosztás a macOS 10,14-es és korábbi verzióiban korlátozott. A [hozzáférés-vezérlési listában](https://developer.apple.com/documentation/security/keychain_services/access_control_lists?language=objc) megadhatja az alkalmazások elérési útját, amelyeknek meg kell osztaniuk a kulcstartót. Előfordulhat, hogy a felhasználó további kulcstartó-kéréseket lát.

MacOS 10.15 + esetén a MSAL viselkedése ugyanaz, mint az iOS és a macOS között. A MSAL kulcstartó- [hozzáférési csoportokat](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) használ a kulcstartó megosztásához. 

### <a name="conditional-access-authentication-differences"></a>Feltételes hozzáférési hitelesítés eltérései

A feltételes hozzáférési forgatókönyvek esetében kevesebb felhasználó fog megjelenni, amikor MSAL használ az iOS-hez. Ennek az az oka, hogy az iOS a közvetítő alkalmazást (Microsoft Authenticator) használja, amely megtagadja, hogy bizonyos esetekben fel kell kérni a felhasználót.

### <a name="project-setup-differences"></a>A projekt beállításának eltérései

**macOS**

- Ha macOS rendszeren állítja be a projektet, győződjön meg arról, hogy az alkalmazás érvényes fejlesztési vagy éles tanúsítvánnyal van aláírva. A MSAL továbbra is az aláíratlan módban működik, de a gyorsítótár-megőrzés tekintetében eltérően fog működni. Az alkalmazás csak akkor futtatható, ha a hibakeresési célból csak aláíratlan. Ha nincs aláírva az alkalmazás, az alábbiakat fogja elosztani:
1. A 10,14-es és korábbi verzióiban a MSAL minden alkalommal kérni fogja a felhasználótól a kulcstartó jelszavát, amikor újraindítják az alkalmazást.
2. A 10.15 + MSAL minden jogkivonat beszerzése esetén felszólítja a felhasználót a hitelesítő adatokra. 

- a macOS-alkalmazásoknak nincs szükségük a AppDelegate-hívás megvalósítására.

**iOS**

- A projekt beállításához további lépések szükségesek a hitelesítési közvetítő folyamatának támogatásához. A lépéseket az oktatóanyag ismerteti.
- az iOS-projekteknek egyéni sémákat kell regisztrálniuk az info. plist fájlban. Ez a macOS rendszeren nem szükséges.

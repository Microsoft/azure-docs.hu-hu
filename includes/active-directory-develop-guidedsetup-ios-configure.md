---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: include file
ms.openlocfilehash: a4b0c2e2d7732c6d2a4f57411731f7145cf3e73e
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967671"
---
## <a name="register-your-application"></a>Alkalmazás regisztrálása

Két módon regisztrálhatja alkalmazását, a következő két szakasz leírtak szerint.

### <a name="option-1-express-mode"></a>Option 1: Az Expressz mód

Most kell regisztrálnia az alkalmazást a *Microsoft alkalmazásregisztrációs portálon*:

1. Regisztrálja az alkalmazást a [Microsoft alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=ios&step=configure).
2. Adjon meg egy nevet az alkalmazás és az e-maileket.
3. Győződjön meg arról, hogy az interaktív telepítés esetén alkalmazott beállítás be van jelölve.
4. Kövesse az utasításokat beszerzéséhez, és illessze be a kódot.

### <a name="option-2-advanced-mode"></a>Option 2: Speciális mód

1. Lépjen a [Microsoft alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/portal/register-app).
2. Adja meg az alkalmazás nevét.
3. Ellenőrizze, hogy az interaktív telepítés a beállítás nincs bejelölve.
4. Válassza ki `Add Platform` majd `Native Application`.
5. Válassza a(z) `Save` lehetőséget.
6. Vissza az xcode-ban. A `ViewController.swift`, cserélje le a sort, kezdve "`let kClientID`" az imént regisztrált alkalmazás azonosítójával:

```swift
let kClientID = "Your_Application_Id_Here"
```

<!-- Workaround for Docs conversion bug -->
<ol start="6">
<li>
CTRL + kattintson <code>Info.plist</code> a helyi menü megjelenítéséhez, majd: <code>Open As</code> > <code>Source Code</code>
</li>
<li>
Alatt a <code>dict</code> gyökércsomópont, adja hozzá a következő:
</li>
</ol>

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msal[Your_Application_Id_Here]</string>
        </array>
    </dict>
</array>
```
<ol start="8">
<li>
Cserélje le <i> <code>[Your_Application_Id_Here]</code> </i> az imént regisztrált alkalmazás azonosítójával
</li>
</ol>

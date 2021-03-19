---
title: Megosztott eszköz mód iOS-eszközökhöz
titleSuffix: Microsoft identity platform | Azure
description: Megtudhatja, hogyan engedélyezheti a megosztott eszközök üzemmódját, hogy az élvonalbeli feldolgozók megosszák egy iOS-eszközt
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: c67c5d7b46c04e7f1aea020127ee798878c43d60
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578787"
---
# <a name="shared-device-mode-for-ios-devices"></a>Megosztott eszköz mód iOS-eszközökhöz

>[!IMPORTANT]
> Ez a funkció [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

A feldolgozók, például a kiskereskedelmi munkatársak, a hajózószemélyzet tagjai és a helyszíni szolgáltatások dolgozói gyakran megosztott mobileszköz használatával végzik munkájukat. Ezek a megosztott eszközök biztonsági kockázatokat jelenthetnek, ha a felhasználók a jelszavukat vagy PIN-fájljaikat szándékosan vagy nem használják, hogy hozzáférjenek a megosztott eszközön lévő ügyfél-és üzleti adataihoz.

A megosztott eszköz mód lehetővé teszi az iOS 13 vagy újabb rendszerű eszközök konfigurálását, hogy könnyebben és biztonságosan lehessen megosztani az alkalmazottakat. Az alkalmazottak gyorsan jelentkezhetnek be és érhetik el az ügyfelek adatait. Ha végzett a váltással vagy a feladattal, kijelentkezhetnek az eszközről, és azonnal készen állnak a következő alkalmazott általi használatra.

A megosztott eszköz mód a Microsoft identitás-vezérlésű felügyeletét is biztosítja az eszköz számára.

Ez a funkció a [Microsoft Authenticator alkalmazást](../user-help/user-help-auth-app-overview.md) használja az eszközön lévő felhasználók felügyeletéhez, valamint az [Apple-eszközökhöz készült Microsoft Enterprise SSO beépülő modul](apple-sso-plugin.md)terjesztéséhez.

## <a name="create-a-shared-device-mode-app"></a>Megosztott eszköz módú alkalmazás létrehozása

Megosztott eszköz üzemmódú alkalmazás létrehozásához a fejlesztők és a felhőalapú eszközök rendszergazdái együttesen működnek:

1. Az **alkalmazások fejlesztői** egy egyfiókos alkalmazást írnak (a több fiókból álló alkalmazások nem támogatottak a megosztott eszköz üzemmódban), és kódot írhatnak a megosztott eszközök kijelentkezéséhez hasonló dolgok kezelésére.

1. Az eszközök **rendszergazdái** megoszthatják az eszközt egy mobileszköz-felügyeleti (Mdm-) szolgáltató használatával, mint a Microsoft Intune a szervezet eszközeinek kezeléséhez. A MDM leküldi a Microsoft Authenticator alkalmazást az eszközökre, és bekapcsolja a "Shared Mode" (megosztott mód) lehetőséget az eszközön a profil frissítése során. Ez a megosztott üzemmódú beállítás a támogatott alkalmazások viselkedését módosítja az eszközön. Ez a konfiguráció a MDM-szolgáltatónál beállítja az eszköz megosztott eszközének módját, és engedélyezi a [Microsoft Enterprise SSO beépülő modult az Apple-eszközökhöz](apple-sso-plugin.md) , amelyek a megosztott eszköz üzemmódhoz szükségesek.

1. [**Csak a nyilvános előzetes** verzióban szükséges] A [felhőalapú eszköz rendszergazdai](../roles/permissions-reference.md#cloud-device-administrator) szerepkörrel rendelkező felhasználónak el kell indítania a [Microsoft Authenticator alkalmazást](../user-help/user-help-auth-app-overview.md) , és csatlakoztatnia kell az eszközét a szervezethez.

    A szervezeti szerepkörök tagságának konfigurálása a Azure Portal: **Azure Active Directory**  >  **szerepkörök és rendszergazdák**  >  **Felhőbeli eszköz rendszergazdája**

A következő fejezetek segítséget nyújtanak az alkalmazás frissítésében a megosztott eszközök üzemmódjának támogatásához.

## <a name="use-intune-to-enable-shared-device-mode--sso-extension"></a>Az Intune használata a megosztott eszközök üzemmódjának & SSO-bővítményének engedélyezéséhez

> [!NOTE]
> A következő lépés csak a nyilvános előzetes verzióban szükséges.

Az eszközt úgy kell konfigurálni, hogy támogassa a megosztott eszköz üzemmódot. Telepíteni kell az iOS 13 + alkalmazást, és regisztrálni kell a MDM. A MDM-konfigurációnak engedélyeznie kell a [Microsoft Enterprise SSO beépülő modult is az Apple-eszközökhöz](apple-sso-plugin.md). Az SSO-bővítményekkel kapcsolatos további tudnivalókért tekintse meg az [Apple videót](https://developer.apple.com/videos/play/tech-talks/301/).

1. Az Intune konfigurációs portálján adja meg az eszközt, hogy engedélyezze a [Microsoft Enterprise SSO beépülő modult az Apple-eszközökhöz](apple-sso-plugin.md) a következő konfigurációval:

    - **Típus**: átirányítás
    - **BŐVÍTMÉNY azonosítója**: com. microsoft. azureauthenticator. ssoextension
    - **Csoport azonosítója**: (ez a mező nem szükséges az iOS esetében)
    - **URL-címek**:   
        - `https://login.microsoftonline.com`
        - `https://login.microsoft.com`
        - `https://sts.windows.net`
        - `https://login.partner.microsoftonline.cn`
        - `https://login.chinacloudapi.cn`
        - `https://login.microsoftonline.de`
        - `https://login.microsoftonline.us`
        - `https://login.usgovcloudapi.net`
        - `https://login-us.microsoftonline.com`
    - **További konfigurálandó információk**:
      - Kulcs: sharedDeviceMode
      - Típus: Boolean
      - Érték: true

    Az Intune-nal való konfigurálással kapcsolatos további információkért tekintse meg az [Intune konfigurációs dokumentációját](/intune/configuration/ios-device-features-settings).

1. Ezután konfigurálja úgy a MDM, hogy az MDM-profilon keresztül leküldje a Microsoft Authenticator alkalmazást az eszközre.

    A megosztott eszköz üzemmód bekapcsolásához állítsa be a következő konfigurációs beállításokat:

    - 1. konfiguráció:
      - Kulcs: sharedDeviceMode
      - Típus: Boolean
      - Érték: true

## <a name="modify-your-ios-application-to-support-shared-device-mode"></a>IOS-alkalmazás módosítása a megosztott eszközök üzemmódjának támogatásához

A felhasználók attól függnek, hogy az adatok nem szivárognak-e egy másik felhasználó felé. A következő szakaszokban olyan hasznos jelek láthatók, amelyek jelzik, hogy a változás bekövetkezett, és kezelni kell az alkalmazást.

Ön felelős az eszközön lévő felhasználó állapotának ellenőrzésében az alkalmazás minden használatakor, majd az előző felhasználó adatait törli. Ez magában foglalja a többszörös feladatokból származó háttérből való újratöltést.

A felhasználó változásakor győződjön meg arról, hogy az előző felhasználó adatai törlődnek, és az alkalmazásban megjelenő gyorsítótárazott adatfájlok törlődnek. Javasoljuk, hogy az alkalmazás frissítése után a megosztott eszköz üzemmódjának támogatásához a vállalat biztonsági felülvizsgálati folyamatot végezzen.

### <a name="detect-shared-device-mode"></a>Megosztott eszköz üzemmódjának észlelése

A megosztott eszköz mód észlelése fontos az alkalmazás számára. Számos alkalmazáshoz szükség van a felhasználói élmény (UX) módosítására, ha az alkalmazás egy megosztott eszközön van használatban. Előfordulhat például, hogy az alkalmazás "regisztráció" funkcióval rendelkezik, amely nem megfelelő egy Frontline Worker-feldolgozóhoz, mert valószínűleg már van fiókja. Ha megosztott eszköz módban van, további biztonságot is hozzáadhat az alkalmazás adatkezeléséhez.

A alkalmazásban található `getDeviceInformationWithParameters:completionBlock:` API-val `MSALPublicClientApplication` megállapíthatja, hogy egy eszköz megosztott eszköz módban fut-e.

A következő kódrészletek példákat mutatnak az `getDeviceInformationWithParameters:completionBlock:` API használatára.

#### <a name="swift"></a>Swift

```swift
application.getDeviceInformation(with: nil, completionBlock: { (deviceInformation, error) in

    guard let deviceInfo = deviceInformation else {
        return
    }

    let isSharedDevice = deviceInfo.deviceMode == .shared
    // Change your app UX if needed
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
[application getDeviceInformationWithParameters:nil
                                completionBlock:^(MSALDeviceInformation * _Nullable deviceInformation, NSError * _Nullable error)
{
    if (!deviceInformation)
    {
        return;
    }

    BOOL isSharedDevice = deviceInformation.deviceMode == MSALDeviceModeShared;
    // Change your app UX if needed
}];
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>A bejelentkezett felhasználó beolvasása, és annak megállapítása, hogy a felhasználó módosult-e az eszközön

A megosztott eszköz üzemmód támogatásának egy másik fontos része a felhasználó állapotának megállapítása az eszközön, és az alkalmazásadatok törlése, ha a felhasználó módosult, vagy ha az eszközön nincs felhasználó. Ön felelős azért, hogy gondoskodjon arról, hogy az adatvédelem ne legyen kiszivárgott egy másik felhasználónak.

Az `getCurrentAccountWithParameters:completionBlock:` API-val a jelenleg bejelentkezett fiókot lehet lekérdezni az eszközön.

#### <a name="swift"></a>Swift

```swift
let msalParameters = MSALParameters()
msalParameters.completionBlockQueue = DispatchQueue.main

application.getCurrentAccount(with: msalParameters, completionBlock: { (currentAccount, previousAccount, error) in

    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALParameters *parameters = [MSALParameters new];
parameters.completionBlockQueue = dispatch_get_main_queue();

[application getCurrentAccountWithParameters:parameters
                             completionBlock:^(MSALAccount * _Nullable account, MSALAccount * _Nullable previousAccount, NSError * _Nullable error)
{
    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
}];
```

### <a name="globally-sign-in-a-user"></a>Globális bejelentkezés felhasználóként

Ha egy eszköz megosztott eszközként van konfigurálva, az alkalmazás meghívja az `acquireTokenWithParameters:completionBlock:` API-t, hogy jelentkezzen be a fiókba. A fiók az első alkalmazásnak a fiókba való bejelentkezése után globálisan elérhető lesz az eszközön az összes jogosult alkalmazás számára.

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"api://myapi/scope"] webviewParameters:[self msalTestWebViewParameters]];

parameters.loginHint = self.loginHintTextField.text;

[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

### <a name="globally-sign-out-a-user"></a>Felhasználó globális kijelentkezése

A következő kód eltávolítja a bejelentkezett fiókot, és törli a gyorsítótárazott jogkivonatokat nem csak az alkalmazásból, hanem a megosztott eszköz módban lévő eszközről is. Azonban nem törli az alkalmazásból származó *adatok* törlését. Törölnie kell az alkalmazásból az adatait, valamint törölnie kell az alkalmazás által a felhasználónak megjelenített gyorsítótárazott adatok törlését.

#### <a name="clear-browser-state"></a>Böngésző állapotának törlése

> [!NOTE]
> A következő lépés csak a nyilvános előzetes verzióban szükséges.

Ebben a nyilvános előzetes verzióban az [Apple-eszközökhöz készült Microsoft Enterprise SSO beépülő modul](apple-sso-plugin.md) csak az alkalmazások állapotát törli. A Safari böngésző nem törli az állapotot. Javasoljuk, hogy manuálisan törölje a böngésző-munkamenetet annak biztosításához, hogy a felhasználói állapot nyomkövetése ne maradjon hátra. A `signoutFromBrowser` cookie-k törléséhez az alább látható opcionális tulajdonságot használhatja. Ez azt eredményezi, hogy a böngésző röviden elindít az eszközön.

#### <a name="swift"></a>Swift

```swift
let account = .... /* account retrieved above */

let signoutParameters = MSALSignoutParameters(webviewParameters: self.webViewParamaters!)
signoutParameters.signoutFromBrowser = true // Only needed for Public Preview.

application.signout(with: account, signoutParameters: signoutParameters, completionBlock: {(success, error) in

    if let error = error {
        // Signout failed
        return
    }

    // Sign out completed successfully
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALAccount *account = ... /* account retrieved above */;

MSALSignoutParameters *signoutParameters = [[MSALSignoutParameters alloc] initWithWebviewParameters:webViewParameters];
signoutParameters.signoutFromBrowser = YES; // Only needed for Public Preview.

[application signoutWithAccount:account signoutParameters:signoutParameters completionBlock:^(BOOL success, NSError * _Nullable error)
{
    if (!success)
    {
        // Signout failed
        return;
    }

    // Sign out completed successfully
}];
```

## <a name="next-steps"></a>Következő lépések

Ha szeretné megtekinteni a megosztott eszköz üzemmódot a műveletben, a GitHubon az alábbi mintakód-minta egy olyan példát mutat be, amely egy, az iOS-eszközön lévő, megosztott eszközön futó munkavégző alkalmazást

[MSAL iOS Swift Microsoft Graph API-minta](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc)

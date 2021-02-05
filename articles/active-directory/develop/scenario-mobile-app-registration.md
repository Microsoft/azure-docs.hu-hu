---
title: Webes API-kat meghívó mobileszközök regisztrálása | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre webes API-kat meghívó mobil alkalmazást (az alkalmazás regisztrációja)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 902348063a24b50d1de0a821d9d6e67931cc53cd
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582536"
---
# <a name="register-mobile-apps-that-call-web-apis"></a>Webes API-kat meghívó mobileszközök regisztrálása

Ez a cikk a létrehozott Mobile-alkalmazások regisztrálásához nyújt útmutatást.

## <a name="supported-account-types"></a>Támogatott fióktípusok

A mobileszközök által támogatott fióktípus attól függ, hogy milyen élményt szeretne engedélyezni, és milyen folyamatokat kíván használni.

### <a name="audience-for-interactive-token-acquisition"></a>Célközönség az interaktív jogkivonat-beszerzéshez

A legtöbb Mobile-alkalmazás interaktív hitelesítést használ. Ha az alkalmazás ezt a hitelesítési űrlapot használja, bármilyen [fióktípus](quickstart-register-app.md)bejelentkezhet a felhasználókba.

### <a name="audience-for-integrated-windows-authentication-username-password-and-b2c"></a>Az integrált Windows-hitelesítés, a Felhasználónév-jelszó és a B2C célközönsége

Ha Univerzális Windows-platform (UWP) alkalmazással rendelkezik, akkor az integrált Windows-hitelesítés használatával jelentkezhet be a felhasználókba. Az integrált Windows-hitelesítés vagy felhasználónév-jelszó alapú hitelesítés használatához az alkalmazásnak saját üzletági (LOB) fejlesztői bérlőben kell bejelentkeznie a felhasználókba. Független szoftvergyártói (ISV) forgatókönyv esetén az alkalmazás Azure Active Directory szervezetében is bejelentkezhet a felhasználókba. Ezek a hitelesítési folyamatok nem támogatottak a Microsoft személyes fiókjaiban.

A felhasználók olyan közösségi identitások használatával is bejelentkezhetnek, amelyek átadják a B2C-hatóságot és a szabályzatot. Ennek a módszernek a használatához csak az interaktív hitelesítés és a username-Password hitelesítés használható. A Felhasználónév-jelszó hitelesítés jelenleg csak Xamarin. iOS, Xamarin. Android és UWP esetén támogatott.

További információ: [forgatókönyvek és támogatott hitelesítési folyamatok](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows) , [forgatókönyvek és támogatott platformok és nyelvek](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages).

## <a name="platform-configuration-and-redirect-uris"></a>Platform konfigurálása és URI-k átirányítása

### <a name="interactive-authentication"></a>Interaktív hitelesítés

Ha interaktív hitelesítést használó mobil alkalmazást hoz létre, a legkritikusabb regisztrációs lépés az átirányítási URI. A hitelesítés panelen beállíthatja az interaktív hitelesítést a [platform konfigurációján  ](https://aka.ms/MobileAppReg)keresztül.

Ez a felhasználói élmény lehetővé teszi, hogy az alkalmazás egyszeri bejelentkezést (SSO) kapjon Microsoft Authenticator (és az Androidon Intune Céges portál). Emellett az eszköz-felügyeleti házirendeket is támogatni fogja.

Az alkalmazás regisztrációs portálján elérhető egy előzetes verzió, amely segítséget nyújt az iOS-és Android-alkalmazások felügyelt válasz URI-azonosítójának kiszámításához:

1. Az alkalmazás-regisztrációs portálon válassza   >  **a hitelesítés kipróbálom az új felhasználói élményt**.

   ![A hitelesítés panel, ahol új felhasználói élményt választhat](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. Válassza **a platform hozzáadása** lehetőséget.

   ![Platform hozzáadása](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. Ha a platformok listája támogatott, válassza az **iOS** lehetőséget.

   ![Mobileszköz kiválasztása](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Adja meg a köteg AZONOSÍTÓját, majd válassza a **regisztráció** lehetőséget.

   ![Adja meg a köteg AZONOSÍTÓját](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

A lépések elvégzése után az átirányítási URI-t az alábbi képen látható módon számítjuk ki.

![Az eredményül kapott átirányítási URI](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

Ha manuálisan szeretné konfigurálni az átirányítási URI-t, ezt az alkalmazás jegyzékfájlján keresztül teheti meg. A jegyzékfájl ajánlott formátuma a következő:

- **iOS**: `msauth.<BUNDLE_ID>://auth`
  - Adja meg például a következőt: `msauth.com.yourcompany.appName://auth`
- **Android**: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Az Android-aláírási kivonatot a kulcstartó parancs használatával hozhatja ki a kiadási kulccsal vagy a hibakeresési kulccsal.

### <a name="username-password-authentication"></a>Felhasználónév – jelszó-hitelesítés

Ha az alkalmazás csak a Felhasználónév-jelszó típusú hitelesítést használja, nem kell regisztrálnia az alkalmazáshoz tartozó átirányítási URI-t. Ez a folyamat egy oda-és beutazást tesz a Microsoft Identity platformra. Az alkalmazás nem hívható vissza semmilyen konkrét URI-ra.

Azonban azonosítsa az alkalmazást nyilvános ügyfélalkalmazásként. Ehhez tegye a következőket:

1. Még mindig a <a href="https://portal.azure.com/" target="_blank">Azure Portalban <span class="docon docon-navigate-external x-hidden-focus"></span></a>válassza ki az alkalmazást **Alkalmazásregisztrációk**, majd válassza a **hitelesítés** lehetőséget.
1. A **Speciális beállítások**  >  **lehetővé teszik a nyilvános ügyfél-folyamatok** számára  >  **a következő mobil-és asztali folyamatok használatát: válassza az** **Igen** lehetőséget.

   :::image type="content" source="media/scenarios/default-client-type.png" alt-text="Nyilvános ügyfél beállításainak engedélyezése a Azure Portal hitelesítés paneljén":::

## <a name="api-permissions"></a>API-engedélyek

A Mobile Applications a bejelentkezett felhasználó nevében hívja meg az API-kat. Az alkalmazásnak delegált engedélyeket kell kérnie. Ezeket az engedélyeket hatóköröknek is nevezik. A kívánt élménytől függően a delegált engedélyeket statikusan is kérheti a Azure Portalon keresztül. Vagy a futtatáskor dinamikusan is kérheti őket.

Az engedélyek statikus regisztrálásával lehetővé teszi, hogy a rendszergazdák egyszerűen jóváhagyják az alkalmazást. A statikus regisztráció ajánlott.

## <a name="next-steps"></a>Következő lépések

Lépjen be a következő cikkbe ebben a forgatókönyvben, az [alkalmazás kódjának konfigurációjában](scenario-mobile-app-configuration.md).

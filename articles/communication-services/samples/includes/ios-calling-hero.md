---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 7d39decaa6376c614e48b65ad2fc1b3043aa0a3c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101682399"
---
[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Az **iOS-hez** készült Azure kommunikációs szolgáltatások csoport azt mutatja be, hogy a kommunikációs szolgáltatások hogyan hívhatják meg az iOS-es ügyféloldali kódtárat a hangvételt és a videót tartalmazó csoportos hívás létrehozásához. Ebből a rövid útmutatóból megtudhatja, hogyan állíthatja be és futtathatja a mintát. A minta áttekintése a kontextusban.

## <a name="overview"></a>Áttekintés

A minta egy natív iOS-alkalmazás, amely az Azure kommunikációs szolgáltatások iOS-alapú ügyféloldali kódtárait használja a hang-és videohívás-hívások funkcióinak meghívásához. Az alkalmazás kiszolgálóoldali összetevő használatával kiépíti azokat a hozzáférési jogkivonatokat, amelyek az Azure kommunikációs szolgáltatások ügyféloldali kódtár inicializálásához használatosak. A kiszolgálóoldali összetevő konfigurálásához nyugodtan kövesse a [megbízható szolgáltatást Azure functions](../../tutorials/trusted-service-tutorial.md) oktatóanyaggal.

A minta így néz ki:

:::image type="content" source="../media/calling/landing-page-ios.png" alt-text="A minta alkalmazás kezdőlapját ábrázoló képernyőfelvétel":::

Ha lenyomja az új hívás indítása gombot, az iOS-alkalmazás létrehoz egy új hívást, és csatlakoztatja azt. Az alkalmazás azt is lehetővé teszi, hogy a meglévő hívás AZONOSÍTÓjának megadásával csatlakozzon egy meglévő Azure kommunikációs szolgáltatáshoz.

A híváshoz való csatlakozást követően a rendszer felszólítja, hogy adjon engedélyt az alkalmazásnak a kamerához és a mikrofonhoz való hozzáférésre. A rendszer a megjelenítendő név megadását is kéri.

:::image type="content" source="../media/calling/pre-call-ios.png" alt-text="A minta alkalmazás előhívási képernyőjét ábrázoló képernyőfelvétel.":::

A megjelenítendő név és az eszközök konfigurálása után csatlakozhat a híváshoz. Ekkor megjelenik a fő hívási vászon, ahol a legfontosabb hívási élmény él.

:::image type="content" source="../media/calling/main-app-ios.png" alt-text="A minta alkalmazás fő képernyőjét ábrázoló képernyőfelvétel.":::

A fő hívó képernyő összetevői:

- **Media Gallery**: az a fő szakasz, ahol a résztvevők megjelennek. Ha a résztvevő kamerája engedélyezve van, a videó hírcsatornája itt jelenik meg. Minden résztvevő rendelkezik egy egyéni csempével, amely megjeleníti a megjelenítendő nevüket és a video streamet (ha van ilyen). A katalógus több résztvevőt is támogat, és frissül, ha a résztvevőket hozzáadják vagy eltávolítják a híváshoz.
- **Műveleti sáv**: ez az a hely, ahol az elsődleges hívási vezérlők találhatók. Ezen vezérlőelemek lehetővé teszik a videó és a mikrofon be-és kikapcsolását, megoszthatja a képernyőt, és elhagyhatja a hívást.

Az alábbiakban további információkat talál a minta beállításához szükséges előfeltételekről és lépésekről.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. Részletekért tekintse meg a [fiók ingyenes létrehozását](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)ismertető témakört.
- Egy [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532)-t futtató Mac, valamint egy érvényes, a kulcstartóba telepített fejlesztői tanúsítvány.
- Egy Azure kommunikációs szolgáltatások erőforrása. További információ: [Azure kommunikációs erőforrás létrehozása](../../quickstarts/create-communication-resource.md).
- Egy olyan Azure-függvény, amely [megbízható szolgáltatási logikát](../../tutorials/trusted-service-tutorial.md) futtat a hozzáférési jogkivonatok beolvasásához.

## <a name="running-sample-locally"></a>Minta helyi futtatása

A csoportos hívási minta helyileg futtatható a XCode használatával. A fejlesztők a fizikai eszközét vagy emulátorát használva ellenőrizhetik az alkalmazást.

### <a name="before-running-the-sample-for-the-first-time"></a>A minta első futtatása előtt

1. A függőségek telepítése a futtatásával `pod install` .
2. Megnyitás `ACSCall.xcworkspace` a Xcode-ben.
3. Frissítés `AppSettings.plist` . A kulcs értékének megadásával adja meg `acsTokenFetchUrl` a hitelesítési végpont URL-címét.

### <a name="run-sample"></a>Minta futtatása

Hozza létre és futtassa a mintát a XCode-ben.

## <a name="optional-securing-an-authentication-endpoint"></a>Választható Hitelesítési végpont biztonságossá tétele

Demonstrációs célokra ez a példa egy nyilvánosan elérhető végpontot használ alapértelmezetten egy Azure kommunikációs szolgáltatásbeli jogkivonat beolvasásához. Éles környezetekben javasolt saját biztonságos végpontot használni a saját tokenek kiépítéséhez.

A további beállításokkal ez a minta támogatja a **Azure Active Directory** (Azure ad) védett végponthoz való csatlakozást, így a felhasználói bejelentkezés szükséges ahhoz, hogy az alkalmazás beolvassa az Azure kommunikációs szolgáltatás tokenjét. Lásd az alábbi lépéseket:

1. Azure Active Directory hitelesítés engedélyezése az alkalmazásban.  
   - [Az alkalmazás regisztrálása a Azure Active Directory alatt (iOS/macOS platform beállításainak használatával)](../../../active-directory/develop/tutorial-v2-ios.md) 
    - [App Service vagy Azure Functions alkalmazás konfigurálása az Azure AD-bejelentkezés használatára](../../../app-service/configure-authentication-provider-aad.md)
2. Lépjen a regisztrált alkalmazás áttekintése oldalra Azure Active Directory alkalmazás regisztrációi alatt. Jegyezze fel a `Application (client) ID` , `Directory (tenant) ID` , `Application ID URI`

:::image type="content" source="../media/calling/aad-overview.png" alt-text="Azure Portal Azure Active Directory konfigurálása.":::

3. Nyissa meg `AppSettings.plist` a Xcode, adja hozzá a következő kulcs-értékeket:
   - `acsTokenFetchUrl`: Az Azure Communication Services-tokent kérő URL-cím 
   - `isAADAuthEnabled`: Logikai érték, amely azt jelzi, hogy az Azure kommunikációs szolgáltatások jogkivonat-hitelesítése kötelező-e
   - `aadClientId`: Az alkalmazás (ügyfél) azonosítója
   - `aadTenantId`: A címtár (bérlő) azonosítója
   - `aadRedirectURI`: Az átirányítási URI-nak ebben a formátumban kell lennie: `msauth.<app_bundle_id>://auth`
   - `aadScopes`: A felhasználóktól az engedélyezéshez kért engedélyezési hatókörök tömbje. Hozzáadás `<Application ID URI>/user_impersonation` a tömbhöz a hitelesítési végponthoz való hozzáférés biztosításához

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné a kommunikációs szolgáltatások előfizetését, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek. További információ az [erőforrások tisztításáról](../../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Következő lépések

További információért tekintse át a következő cikkeket:

- Ismerkedjen meg [a hívó ügyféloldali kódtár használatával](../../quickstarts/voice-video-calling/calling-client-samples.md)
- További információ a [hívás működéséről](../../concepts/voice-video-calling/about-call-types.md)

### <a name="additional-reading"></a>További információ

- [Azure Communication GitHub](https://github.com/Azure/communication) – további példákat és információkat talál a hivatalos GitHub-oldalon
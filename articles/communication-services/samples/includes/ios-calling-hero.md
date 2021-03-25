---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: f1d49aa0583f34327afff229b46d7a366202aded
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105152802"
---
[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Az **iOS-hez** készült Azure kommunikációs szolgáltatások csoport azt mutatja be, hogy a kommunikációs szolgáltatások hogyan hívhatják meg az iOS SDK-t, hogy a hangvételt és a videót is magában foglaló csoportos hívást hozzon létre. Ebből a rövid útmutatóból megtudhatja, hogyan állíthatja be és futtathatja a mintát. A minta áttekintése a kontextusban.

## <a name="download-code"></a>Kód letöltése

A gyors útmutatóhoz tartozó véglegesített kód megkeresése a [githubon](https://github.com/Azure-Samples/communication-services-ios-calling-hero).

## <a name="overview"></a>Áttekintés

A minta egy natív iOS-alkalmazás, amely az Azure kommunikációs szolgáltatások iOS SDK-kat használja a hang-és videohívás-hívások funkcióinak kiépítéséhez. Az alkalmazás kiszolgálóoldali összetevő használatával kiépíti az Azure Communication Services SDK inicializálásához használt hozzáférési jogkivonatokat. A kiszolgálóoldali összetevő konfigurálásához nyugodtan kövesse a [megbízható szolgáltatást Azure functions](../../tutorials/trusted-service-tutorial.md) oktatóanyaggal.

A minta így néz ki:

:::image type="content" source="../media/calling/landing-page-ios.png" alt-text="A minta alkalmazás kezdőlapját ábrázoló képernyőfelvétel":::

Ha lenyomja az új hívás indítása gombot, az iOS-alkalmazás létrehoz egy új hívást, és csatlakoztatja azt. Az alkalmazás lehetővé teszi egy meglévő Azure kommunikációs szolgáltatáshoz való csatlakozást a meglévő hívás AZONOSÍTÓjának megadásával. A Teams Meeting szolgáltatáshoz is csatlakozhat, ha megadja az értekezlet meghívásában található JOIN hivatkozást.  (Az illesztési hivatkozás formátuma a következő: `https://teams.microsoft.com/l/meetup-join/` ). További információ a csapatok együttműködési feladatairól: [Teams együttműködés fogalmi dokumentációja](../../concepts/teams-interop.md).

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
- A [hitelesítési végpontot](../../tutorials/trusted-service-tutorial.md) futtató Azure-függvény a hozzáférési jogkivonatok beolvasására.

## <a name="running-sample-locally"></a>Minta helyi futtatása

A csoportos hívási minta helyileg futtatható a XCode használatával. A fejlesztők a fizikai eszközét vagy emulátorát használva ellenőrizhetik az alkalmazást.

### <a name="before-running-the-sample-for-the-first-time"></a>A minta első futtatása előtt

1. A függőségek telepítése a futtatásával `pod install` .
2. Megnyitás `AzureCalling.xcworkspace` a Xcode-ben.
3. Frissítés `AppSettings.plist` . A kulcs értékének megadásával adja meg `communicationTokenFetchUrl` a hitelesítési végpont URL-címét.

### <a name="run-sample"></a>Minta futtatása

Hozza létre és futtassa a mintát a XCode-ben.

## <a name="optional-securing-an-authentication-endpoint"></a>Választható Hitelesítési végpont biztonságossá tétele

Demonstrációs célokra ez a minta egy nyilvánosan elérhető végpontot használ alapértelmezetten egy Azure kommunikációs szolgáltatás hozzáférési jogkivonatának beolvasásához. Éles környezetekben javasolt saját biztonságos végpontot használni a saját tokenek kiépítéséhez.

A további beállításokkal ez a minta támogatja a **Azure Active Directory** (Azure ad) védett végponthoz való csatlakozást, így a felhasználói bejelentkezés szükséges ahhoz, hogy az alkalmazás beolvassa az Azure kommunikációs szolgáltatás hozzáférési jogkivonatát. Lásd az alábbi lépéseket:

1. Azure Active Directory hitelesítés engedélyezése az alkalmazásban.  
   - [Az alkalmazás regisztrálása a Azure Active Directory alatt (iOS/macOS platform beállításainak használatával)](../../../active-directory/develop/tutorial-v2-ios.md) 
    - [App Service vagy Azure Functions alkalmazás konfigurálása az Azure AD-bejelentkezés használatára](../../../app-service/configure-authentication-provider-aad.md)
2. Lépjen a regisztrált alkalmazás áttekintése oldalra Azure Active Directory alkalmazás regisztrációi alatt. Jegyezze fel a `Application (client) ID` , `Directory (tenant) ID` , `Application ID URI`

:::image type="content" source="../media/calling/aad-overview.png" alt-text="Azure Portal Azure Active Directory konfigurálása.":::

3. Nyissa meg `AppSettings.plist` a Xcode, adja hozzá a következő kulcs-értékeket:
   - `communicationTokenFetchUrl`: Az Azure Communication Services-tokent kérő URL-cím 
   - `isAADAuthEnabled`: Logikai érték, amely azt jelzi, hogy az Azure kommunikációs szolgáltatások jogkivonat-hitelesítése kötelező-e
   - `aadClientId`: Az alkalmazás (ügyfél) azonosítója
   - `aadTenantId`: A címtár (bérlő) azonosítója
   - `aadRedirectURI`: Az átirányítási URI-nak ebben a formátumban kell lennie: `msauth.<app_bundle_id>://auth`
   - `aadScopes`: A felhasználóktól az engedélyezéshez kért engedélyezési hatókörök tömbje. Hozzáadás `<Application ID URI>/user_impersonation` a tömbhöz a hitelesítési végponthoz való hozzáférés biztosításához

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné a kommunikációs szolgáltatások előfizetését, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek. További információ az [erőforrások tisztításáról](../../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Következő lépések

>[!div class="nextstepaction"]
>[A minta letöltése a GitHubról](https://github.com/Azure-Samples/communication-services-ios-calling-hero)

További információért tekintse át a következő cikkeket:

- Ismerkedjen meg [a Calling SDK használatával](../../quickstarts/voice-video-calling/calling-client-samples.md)
- További információ a [hívás működéséről](../../concepts/voice-video-calling/about-call-types.md)

### <a name="additional-reading"></a>További információ

- [Azure Communication GitHub](https://github.com/Azure/communication) – további példákat és információkat talál a hivatalos GitHub-oldalon
- [Minták](./../overview.md) – további mintákat és példákat talál a minták áttekintése oldalon.
- [Azure kommunikációs funkciók](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features) – további információ a Calling iOS SDK-ról –[Azure kommunikációs iOS Calling SDK](https://github.com/Azure/Communication/releases/)

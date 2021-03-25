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
ms.openlocfilehash: f71f394f190c5de1c3d64d02fd6113817ecda1ea
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105152789"
---
[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Az Azure kommunikációs szolgáltatások **csoport az Androidhoz készült Hero-minta** azt mutatja be, hogy a kommunikációs szolgáltatások hogyan hívhatják meg az Android SDK-t, hogy a hangvételt és a videót is tartalmazó csoportos hívást hozzon létre. Ebből a rövid útmutatóból megtudhatja, hogyan állíthatja be és futtathatja a mintát. A minta áttekintése a kontextusban.

## <a name="download-code"></a>Kód letöltése

A gyors útmutatóhoz tartozó véglegesített kód megkeresése a [githubon](https://github.com/Azure-Samples/communication-services-android-calling-hero).

## <a name="overview"></a>Áttekintés

A minta egy natív Android-alkalmazás, amely az Azure kommunikációs szolgáltatások Android SDK-kat használja a hang-és videohívás-hívások funkcióinak kiépítéséhez. Az alkalmazás kiszolgálóoldali összetevő használatával kiépíti az Azure Communication Services SDK inicializálásához használt hozzáférési jogkivonatokat. A kiszolgálóoldali összetevő konfigurálásához nyugodtan kövesse a [megbízható szolgáltatást Azure functions](../../tutorials/trusted-service-tutorial.md) oktatóanyaggal.

A minta így néz ki:

:::image type="content" source="../media/calling/landing-page-android.png" alt-text="A minta alkalmazás kezdőlapját ábrázoló képernyőfelvétel":::

Ha lenyomja az új hívás indítása gombot, az Android-alkalmazás létrehoz egy új hívást, és csatlakoztatja azt. Az alkalmazás azt is lehetővé teszi, hogy a meglévő hívás AZONOSÍTÓjának megadásával csatlakozzon egy meglévő Azure kommunikációs szolgáltatáshoz.

A híváshoz való csatlakozást követően a rendszer felszólítja, hogy adjon engedélyt az alkalmazásnak a kamerához és a mikrofonhoz való hozzáférésre. A rendszer a megjelenítendő név megadását is kéri.

:::image type="content" source="../media/calling/pre-call-android.png" alt-text="A minta alkalmazás előhívási képernyőjét ábrázoló képernyőfelvétel.":::

A megjelenítendő név és az eszközök konfigurálása után csatlakozhat a híváshoz. Ekkor megjelenik a fő hívási vászon, ahol a legfontosabb hívási élmény él.

:::image type="content" source="../media/calling/main-app-android.png" alt-text="A minta alkalmazás fő képernyőjét ábrázoló képernyőfelvétel.":::

A fő hívó képernyő összetevői:

- **Media Gallery**: az a fő szakasz, ahol a résztvevők megjelennek. Ha a résztvevő kamerája engedélyezve van, a videó hírcsatornája itt jelenik meg. Minden résztvevő rendelkezik egy egyéni csempével, amely megjeleníti a megjelenítendő nevüket és a video streamet (ha van ilyen). A katalógus több résztvevőt is támogat, és frissül, ha a résztvevőket hozzáadják vagy eltávolítják a híváshoz.
- **Műveleti sáv**: ez az a hely, ahol az elsődleges hívási vezérlők találhatók. Ezen vezérlőelemek lehetővé teszik a videó és a mikrofon be-és kikapcsolását, megoszthatja a képernyőt, és elhagyhatja a hívást.

Az alábbiakban további információkat talál a minta beállításához szükséges előfeltételekről és lépésekről.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. Részletekért tekintse meg a [fiók ingyenes létrehozását](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)ismertető témakört.
- [Android Studio](https://developer.android.com/studio) fut a számítógépen
- Egy Azure kommunikációs szolgáltatások erőforrása. További információ: [Azure kommunikációs erőforrás létrehozása](../../quickstarts/create-communication-resource.md).
- A [hitelesítési végpontot](../../tutorials/trusted-service-tutorial.md) futtató Azure-függvény a hozzáférési jogkivonatok beolvasására.

## <a name="running-sample-locally"></a>Minta helyi futtatása

A csoportos hívási minta helyileg futtatható Android Studio használatával. A fejlesztők a fizikai eszközét vagy emulátorát használva ellenőrizhetik az alkalmazást.

### <a name="before-running-the-sample-for-the-first-time"></a>A minta első futtatása előtt

1. Nyissa meg Android Studio és válassza a `Open an Existing Project`
2. Nyissa meg a `AzureCalling` minta letöltött kiadásában található mappát.
3. A frissítendő alkalmazás/eszközök kibontása `appSettings.properties` . Állítsa be a kulcs értékét `communicationTokenFetchUrl` úgy, hogy az előfeltételként beállított hitelesítési végpont URL-címe legyen.

### <a name="run-sample"></a>Minta futtatása

Hozza létre és futtassa a mintát Android Studioban.

## <a name="optional-securing-an-authentication-endpoint"></a>Választható Hitelesítési végpont biztonságossá tétele

Demonstrációs célokra ez a példa egy nyilvánosan elérhető végpontot használ alapértelmezetten egy Azure kommunikációs szolgáltatásbeli jogkivonat beolvasásához. Éles környezetekben javasolt saját biztonságos végpontot használni a saját tokenek kiépítéséhez.

A további beállításokkal ez a minta támogatja a **Azure Active Directory** (Azure ad) védett végponthoz való csatlakozást, így a felhasználói bejelentkezés szükséges ahhoz, hogy az alkalmazás beolvassa az Azure kommunikációs szolgáltatás tokenjét. Lásd az alábbi lépéseket:

1. Azure Active Directory hitelesítés engedélyezése az alkalmazásban.  
   - [Az alkalmazás regisztrálása a Azure Active Directory alatt (az Android platform beállításaival)](../../../active-directory/develop/tutorial-v2-android.md) 
    - [App Service vagy Azure Functions alkalmazás konfigurálása az Azure AD-bejelentkezés használatára](../../../app-service/configure-authentication-provider-aad.md)

2. Lépjen a regisztrált alkalmazás áttekintése oldalra Azure Active Directory alkalmazás regisztrációi alatt. Jegyezze fel a `Package name` , `Signature hash` , `MSAL Configutaion`

:::image type="content" source="../media/calling/aad-overview-android.png" alt-text="Azure Portal Azure Active Directory konfigurálása.":::

3. Szerkesztés `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` és beállítás `isAADAuthEnabled` a Azure Active Directory engedélyezéséhez
4. Szerkessze `AndroidManifest.xml` és állítsa be a következőt: `android:path` a tároló aláírása kivonata. Választható. Az aktuális érték kivonatot használ a kötegelt debug.-tárolóból. Ha a rendszer különböző rendszerállapot-tárolót használ, azt frissíteni kell.)
   ```
   <activity android:name="com.microsoft.identity.client.BrowserTabActivity">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data
                    android:host="com.azure.samples.communication.calling"
                    android:path="/Signature hash" <!-- do not remove /. The current hash in AndroidManifest.xml is for debug.keystore. -->
                    android:scheme="msauth" />
            </intent-filter>
        </activity>
   ```
5. Másolja a MSAL Android-konfigurációt Azure Portal és illessze be a következőre: `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` . Tartalmazza a "account_mode": "SINGLE"
   ```
      {
         "client_id": "",
         "authorization_user_agent": "DEFAULT",
         "redirect_uri": "",
         "account_mode" : "SINGLE",
         "authorities": [
            {
               "type": "AAD",
               "audience": {
               "type": "AzureADMyOrg",
               "tenant_id": ""
               }
            }
         ]
      }
   ```

6. Szerkessze `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` és állítsa be a kulcs értékét a `communicationTokenFetchUrl` biztonságos hitelesítési végpont URL-címének megadásához.
7. A `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` kulcs értékének szerkesztése és beállítása a `aadScopes` `Azure Active Directory` `Expose an API` hatókörökből

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné a kommunikációs szolgáltatások előfizetését, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek. További információ az [erőforrások tisztításáról](../../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Következő lépések

>[!div class="nextstepaction"]
>[A minta letöltése a GitHubról](https://github.com/Azure-Samples/communication-services-android-calling-hero)

További információért tekintse át a következő cikkeket:

- Ismerkedjen meg [a Calling SDK használatával](../../quickstarts/voice-video-calling/calling-client-samples.md)
- További információ a [hívás működéséről](../../concepts/voice-video-calling/about-call-types.md)

### <a name="additional-reading"></a>További információ

- [Azure Communication GitHub](https://github.com/Azure/communication) – további példákat és információkat talál a hivatalos GitHub-oldalon
- [Minták](./../overview.md) – további mintákat és példákat talál a minták áttekintése oldalon.
- [Azure Communication Calling-funkciók](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features) – további információ a hívó Android SDK-ról –[Azure Communication Android Calling SDK](https://search.maven.org/artifact/com.azure.android/azure-communication-calling)

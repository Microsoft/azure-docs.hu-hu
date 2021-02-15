---
title: Alkalmazások közötti egyszeri bejelentkezés engedélyezése Androidon a MSAL használatával | Azure
titleSuffix: Microsoft identity platform
description: Az Android rendszerhez készült Microsoft Authentication Library (MSAL) használata az alkalmazások egyszeri bejelentkezésének engedélyezéséhez.
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: how-to
ms.date: 10/15/2020
ms.author: hahamil
ms.reviewer: marsma
ms.openlocfilehash: f3d4ec8db89e9bebfdcd594e842a6c19d3d66d54
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104093"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-msal"></a>Útmutató: alkalmazások közötti egyszeri bejelentkezés engedélyezése Androidon a MSAL használatával

Az egyszeri bejelentkezés (SSO) lehetővé teszi, hogy a felhasználók csak egyszer adják meg hitelesítő adataikat, és a hitelesítő adataik automatikusan működjenek az alkalmazások között.

A [Microsoft Identity platform](./index.yml) és a Microsoft Authentication Library (MSAL) segítségével engedélyezheti az egyszeri bejelentkezést a saját alkalmazáscsomag-alkalmazásokon keresztül. A közvetítői képesség és a hitelesítő alkalmazások esetében kiterjesztheti az egyszeri bejelentkezést a teljes eszközön.

Ebben az útmutatóban megtudhatja, hogyan konfigurálhatja az alkalmazás által használt SDK-kat az egyszeri bejelentkezéshez az ügyfelek számára.

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató feltételezi, hogy tudja, hogyan:

- Az alkalmazás kiépítése a Azure Portal használatával. A témakörrel kapcsolatos további információkért tekintse meg az alkalmazások létrehozásával kapcsolatos utasításokat [az Android-oktatóanyagban](./tutorial-v2-android.md#create-a-project) .
- Az alkalmazás integrálása az [Androidhoz készült Microsoft-hitelesítési könyvtárral](https://github.com/AzureAD/microsoft-authentication-library-for-android).

## <a name="methods-for-single-sign-on"></a>Az egyszeri bejelentkezés módszerei

Az MSAL for Android használatával kétféleképpen érheti el az SSO-t:

* [Közvetítői alkalmazáson](#sso-through-brokered-authentication) keresztül
* A [rendszerböngészőn](#sso-through-system-browser) keresztül


   Javasoljuk, hogy használjon egy közvetítői alkalmazást olyan előnyökhöz, mint az eszközre kiterjedő egyszeri bejelentkezés, a fiókok kezelése és a feltételes hozzáférés. Azonban a felhasználóknak további alkalmazásokat is le kell tölteniük.

## <a name="sso-through-brokered-authentication"></a>Egyszeri bejelentkezés közvetítő hitelesítéssel

Javasoljuk, hogy a Microsoft egyik hitelesítési közvetítőjét használja az eszközre érvényes egyszeri bejelentkezéshez (SSO) való részvételhez és a szervezeti feltételes hozzáférési szabályzatok teljesítéséhez. A közvetítővel való integráció a következő előnyöket nyújtja:

- Eszköz egyszeri bejelentkezés
- Feltételes hozzáférés a következőhöz:
  - Intune-alkalmazásvédelem
  - Eszköz regisztrálása (Workplace Join)
  - Mobileszköz-kezelés
- Eszközre kiterjedő fiókok felügyelete
  -  az Android AccountManager & Fiókbeállítások
  - "Munkahelyi fiók" – egyéni fióktípus

Android rendszeren a Microsoft Authentication Broker a [Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) és [Intune céges portál](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) alkalmazások részét képező összetevő.

Az alábbi ábra az alkalmazás, a Microsoft Authentication Library (MSAL) és a Microsoft hitelesítési közvetítői közötti kapcsolatot mutatja be.

![Ábra, amely bemutatja, hogyan kapcsolódik az alkalmazás a MSAL, a Broker-alkalmazásokhoz és az Android-fiók kezelőjéhez.](./media/brokered-auth/brokered-deployment-diagram.png)

### <a name="installing-apps-that-host-a-broker"></a>Brókert futtató alkalmazások telepítése

A brókerek által üzemeltetett alkalmazások az alkalmazás-áruházból (jellemzően Google Play Áruház) telepíthetők az eszköz tulajdonosával. Egyes API-k (erőforrások) azonban olyan feltételes hozzáférési szabályzatok által védettek, amelyekhez az eszközök szükségesek:

- Regisztrálva (munkahelyhez csatlakoztatva) és/vagy
- Regisztrálva van az Eszközkezelőben vagy
- Regisztrálva van Intune App Protection

Ha egy eszközön még nincs telepítve Broker-alkalmazás, a MSAL arra utasítja a felhasználót, hogy telepítsen egyet, amint az alkalmazás egy jogkivonat interaktív lekérdezését kísérli meg. Ezután az alkalmazásnak el kell végeznie a felhasználónak a lépéseket, hogy az eszköz megfeleljen a szükséges házirendnek.

### <a name="effects-of-installing-and-uninstalling-a-broker"></a>A Broker telepítésének és eltávolításának hatásai

#### <a name="when-a-broker-is-installed"></a>Ügynök telepítésekor

Ha egy ügynök telepítve van egy eszközön, az összes további interaktív jogkivonat-kérést (hívásokat `acquireToken()` ) a közvetítő kezeli, nem pedig helyileg a MSAL. A MSAL számára korábban elérhető SSO-állapotok nem érhetők el a közvetítő számára. Ennek eredményeképpen a felhasználónak újra hitelesítenie kell magát, vagy ki kell választania egy fiókot az eszközön ismert fiókok meglévő listájából.

A Broker telepítése nem igényli, hogy a felhasználó újra bejelentkezzen. `MsalUiRequiredException`A következő kérelem csak akkor jelenik meg a közvetítőn, ha a felhasználónak meg kell oldania. `MsalUiRequiredException` több okból is elvégezhető, és interaktív módon kell feloldani. Például:

- A felhasználó megváltoztatta a fiókhoz társított jelszót.
- A felhasználó fiókja már nem felel meg a feltételes hozzáférési szabályzatnak.
- A felhasználó visszavonta az alkalmazásnak a fiókjához való társításához szükséges beleegyezését.

**Több közvetítő** – ha több közvetítő is telepítve van egy eszközön, akkor az elsőként telepített közvetítő mindig az aktív közvetítő. Egy eszközön csak egyetlen közvetítő lehet aktív.

#### <a name="when-a-broker-is-uninstalled"></a>Ügynök eltávolításakor

Ha a rendszer csak egy Broker-üzemeltető alkalmazást telepít, és a rendszer eltávolítja, akkor a felhasználónak újra be kell jelentkeznie. Az aktív közvetítő eltávolítása eltávolítja a fiókot és a hozzá tartozó jogkivonatokat az eszközről.

Ha a Intune Céges portál telepítve van, és az aktív közvetítőként működik, és Microsoft Authenticator is telepítve van, akkor ha a Intune Céges portál (Active Broker) el van távolítva, a felhasználónak újra be kell jelentkeznie. Ha ismét bejelentkeznek, a Microsoft Authenticator alkalmazás lesz az aktív közvetítő.

### <a name="integrating-with-a-broker"></a>Integráció egy közvetítővel

#### <a name="generate-a-redirect-uri-for-a-broker"></a>Átirányítási URI létrehozása egy közvetítőhöz

Regisztrálnia kell egy átirányítási URI-t, amely kompatibilis a közvetítővel. A közvetítő átirányítási URI-ja tartalmaznia kell az alkalmazás csomagjának nevét és az alkalmazás aláírásának Base64 kódolású ábrázolását.

Az átirányítási URI formátuma: `msauth://<yourpackagename>/<base64urlencodedsignature>`

A [kulcstartó használatával Base64](https://manpages.debian.org/buster/openjdk-11-jre-headless/keytool.1.en.html) -kódolású aláírási kivonatot hozhatja ki az alkalmazás aláíró kulcsaival, majd a Azure Portal használatával létrehozhatja az ÁTirányítási URI-t az adott kivonat használatával.

Linux és macOS:

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

Windows:

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

Miután létrehozta az aláírási kivonatot a (z) *eszközzel*, a Azure Portal használatával hozza létre az ÁTirányítási URI-t:

1. Jelentkezzen be a <a href="https://portal.azure.com/" target="_blank">Azure Portalba</a> , és válassza ki az Android-alkalmazást a **Alkalmazásregisztrációkban**.
1. Válassza   >  **a hitelesítés platform hozzáadása**  >  **Android** lehetőséget.
1. A megnyíló **Android-alkalmazás konfigurálása** panelen adja meg a korábban létrehozott **aláírási kivonatot** és a **csomag nevét**.
1. Válassza a **Konfigurálás** gombot.

A Azure Portal létrehozza az átirányítási URI-t, és megjeleníti azt az **Android konfigurációs** ablaktáblájának **átirányítási URI** -mezőjében.

Az alkalmazás aláírásával kapcsolatos további információkért lásd: az [alkalmazás aláírása](https://developer.android.com/studio/publish/app-signing) a Android Studio felhasználói útmutatóban.

> [!IMPORTANT]
> Használja az éles aláírási kulcsot az alkalmazás éles verziójához.

#### <a name="configure-msal-to-use-a-broker"></a>MSAL konfigurálása közvetítő használatára

Ha közvetítőt szeretne használni az alkalmazásban, igazolnia kell, hogy konfigurálta a közvetítő átirányítását. Tegyük fel például, hogy a közvetítő által engedélyezett átirányítási URI-t is tartalmazza, és azt is jelzi, hogy regisztrálta azt – a MSAL konfigurációs fájljában a következő beállításokkal:

```json
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

#### <a name="broker-related-exceptions"></a>Ügynökkel kapcsolatos kivételek

A MSAL kétféle módon kommunikál a közvetítővel:

- Ügynökhöz kötött szolgáltatás
- Androidos AccountManager

A MSAL először a közvetítő által kötött szolgáltatást használja, mert a szolgáltatás meghívásakor nincs szükség Android-engedélyekre. Ha a kötött szolgáltatás kötése meghiúsul, a MSAL az Android AccountManager API-t fogja használni. A MSAL csak akkor működik, ha az alkalmazás már megkapta az `"READ_CONTACTS"` engedélyt.

Ha `MsalClientException` hibakódot kap `"BROKER_BIND_FAILURE"` , két lehetőség közül választhat:

- Kérje meg a felhasználót, hogy tiltsa le az Microsoft Authenticator alkalmazás és a Intune Céges portál energiagazdálkodásának optimalizálását.
- Kérje meg a felhasználót, hogy adja meg az `"READ_CONTACTS"` engedélyt

### <a name="verify-broker-integration"></a>A Broker integrációjának ellenőrzése

Előfordulhat, hogy nem azonnal egyértelmű, hogy a közvetítő integrációja működik, de a következő lépésekkel ellenőrizheti:

1. Az Android-eszközön végezzen el egy kérelmet a közvetítő használatával.
1. Az Android-eszköz beállításai között keresse meg a által hitelesített fióknak megfelelő újonnan létrehozott fiókot. A fióknak *munkahelyi fiók* típusúnak kell lennie.

Ha meg szeretné ismételni a tesztet, eltávolíthatja a fiókot a beállítások közül.

## <a name="sso-through-system-browser"></a>Egyszeri bejelentkezés a rendszerböngészőn keresztül

Az Android-alkalmazások esetében lehetőség van a webnézet, a rendszerböngésző vagy a Chrome egyéni lapfülek használatára a hitelesítési felhasználói élményhez. Ha az alkalmazás nem használ felügyelt hitelesítést, akkor a natív webnézet helyett a rendszerböngészőt kell használnia az SSO eléréséhez.

### <a name="authorization-agents"></a>Engedélyezési ügynökök

Az engedélyezési ügynökökhöz tartozó konkrét stratégia kiválasztása nem kötelező, és a további funkcionalitási alkalmazásokat is testreszabhatja. A legtöbb alkalmazás a MSAL alapértelmezett beállításait fogja használni (lásd: az [androidos MSAL konfigurációs fájljának ismertetése](msal-configuration.md) a különböző alapértelmezett beállítások megjelenítéséhez).

A MSAL a `WebView` vagy a rendszerböngésző használatával támogatja az engedélyezést.  Az alábbi képen látható, hogyan néz ki a `WebView` vagy a rendszerböngésző a CustomTabs vagy CustomTabs nélkül:

![MSAL bejelentkezési példák](./media/authorization-agents/sign-in-ui.jpg)

### <a name="single-sign-on-implications"></a>Egyszeri bejelentkezés következményei

Alapértelmezés szerint a MSAL-ben integrált alkalmazások a rendszerböngésző egyéni lapjait használják az engedélyezéshez. A webnézetekkel ellentétben az egyéni lapok megosztanak egy cookie jar-t az alapértelmezett rendszerböngészővel, így kevesebb bejelentkezést tesznek lehetővé az egyéni lapokkal integrált webes vagy más natív alkalmazásokkal.

Ha az alkalmazás olyan `WebView` stratégiát használ, amely nem integrálja Microsoft Authenticator vagy céges portál támogatást az alkalmazásba, a felhasználók nem fognak egyszeri bejelentkezési élményt biztosítani az eszközön vagy a natív alkalmazások és a webalkalmazások között.

Ha az alkalmazás MSAL-t használ egy olyan közvetítővel, mint például a Microsoft Authenticator vagy a Intune Céges portál, akkor a felhasználók az alkalmazásokban egyszeri bejelentkezéses felhasználói élményt használhatnak, ha az aktív bejelentkezéssel rendelkeznek az egyik alkalmazással.

### <a name="webview"></a>WebView

A alkalmazáson belüli webnézet használatához helyezze a következő sort az App Configuration JSON-ba, amelyet a MSAL továbbít:

```json
"authorization_user_agent" : "WEBVIEW"
```

Az alkalmazáson belüli használatakor `WebView` a felhasználó közvetlenül az alkalmazásba jelentkezik be. A tokenek az alkalmazás Homokozójában maradnak, és nem érhetők el az alkalmazás cookie jar-on kívül. Ennek eredményeképpen a felhasználónak nem lehet egyszeri bejelentkezéses felhasználói felülete, kivéve, ha az alkalmazások integrálva vannak a hitelesítő vagy a céges portál.

A azonban `WebView` lehetővé teszi a bejelentkezési felhasználói felület megjelenésének és működésének testreszabását. A testreszabással kapcsolatos további tudnivalókért tekintse meg az [androidos Webnézeteket](https://developer.android.com/reference/android/webkit/WebView) .

### <a name="default-browser-plus-custom-tabs"></a>Alapértelmezett böngésző és egyéni lapok

Alapértelmezés szerint a MSAL a böngészőt és az [Egyéni lapfülek](https://developer.chrome.com/multidevice/android/customtabs) stratégiát használja. Explicit módon jelezheti ezt a stratégiát, hogy megakadályozza a jövőbeli kiadásokban történt változásokat az `DEFAULT` egyéni konfigurációs fájl következő JSON-konfigurációjának használatával:

```json
"authorization_user_agent" : "BROWSER"
```

Ezzel a megközelítéssel egyszeri bejelentkezéses felhasználói élményt biztosíthat az eszköz böngészőjében. A MSAL egy megosztott cookie jar-t használ, amely lehetővé teszi más natív alkalmazások vagy webalkalmazások számára az egyszeri bejelentkezést az eszközön a MSAL által beállított állandó munkamenet-cookie használatával.

### <a name="browser-selection-heuristic"></a>Böngésző kiválasztása – heurisztikus

Mivel a MSAL nem tudja megállapítani az androidos telefonok széles skáláján használni kívánt böngésző-csomagot, a MSAL egy olyan böngésző-kiválasztási heurisztikus szolgáltatást valósít meg, amely a legjobb eszközök közötti egyszeri bejelentkezést próbálja megadni.

A MSAL elsődlegesen lekéri az alapértelmezett böngészőt a Package managerből, és ellenőrzi, hogy a biztonságos böngészők tesztelt listáján szerepelnek-e. Ha nem, akkor a MSAL a webnézet használatával kerül vissza, és nem egy másik, nem alapértelmezett böngészőt indít el a biztonságos listából. Az alapértelmezett böngésző attól függetlenül lesz kiválasztva, hogy támogatja-e az egyéni lapokat. Ha a böngésző támogatja az egyéni lapokat, a MSAL elindítják az egyéni fület. az egyéni lapok megjelenése és a `WebView` felhasználói felület alapvető testreszabásának engedélyezése. További információért lásd: [Egyéni lapok az Androidban](https://developer.chrome.com/multidevice/android/customtabs) .

Ha nincsenek böngésző-csomagok az eszközön, a MSAL az alkalmazáson belüli alkalmazást használja `WebView` . Ha az eszköz alapértelmezett beállítása nem módosul, a bejelentkezéshez ugyanazt a böngészőt kell elindítani az egyszeri bejelentkezéses felhasználói élmény biztosítása érdekében.

#### <a name="tested-browsers"></a>Tesztelt böngészők

A következő böngészők tesztelték, hogy megfelelően átirányítva vannak-e a `"redirect_uri"` konfigurációs fájlban megadott értékre:

| Eszköz | Beépített böngésző | Chrome | Operát  | Microsoft Edge | UC böngésző | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| 4. Nexus (API 17) | pass | pass |nem alkalmazható |nem alkalmazható |nem alkalmazható |nem alkalmazható |
| Samsung S7 (API 25) | <sup>1</sup> . Pass | pass | pass | pass | sikertelen |pass |
| Huawei (API 26) |<sup>2</sup> . Pass | pass | sikertelen | pass | pass |pass |
| Vivo (API 26) |pass|pass|pass|pass|pass|sikertelen|
| 2. képpont (API 26) |pass | pass | pass | pass | sikertelen |pass |
| Ellenfél | pass | nem alkalmazható<sup>3</sup>|nem alkalmazható  |nem alkalmazható |nem alkalmazható | nem alkalmazható|
| OnePlus (25. API) |pass | pass | pass | pass | sikertelen |pass |
| Nexus (API 28) |pass | pass | pass | pass | sikertelen |pass |
|MI | pass | pass | pass | pass | sikertelen |pass |

<sup>1</sup> A Samsung beépített böngészője a Samsung Internet.<br/>
<sup>2</sup> A Huawei beépített böngészője a Huawei böngésző.<br/>
<sup>3</sup> Az alapértelmezett böngésző nem módosítható a ellenfél-eszköz beállításán belül.

## <a name="next-steps"></a>Következő lépések

Az [androidos eszközökhöz készült megosztott eszköz mód](msal-android-shared-devices.md) lehetővé teszi, hogy egy Android-eszközt úgy konfiguráljon, hogy az egyszerűen több alkalmazott számára is megosztható legyen.
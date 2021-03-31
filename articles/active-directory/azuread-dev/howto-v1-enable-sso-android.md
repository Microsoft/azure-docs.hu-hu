---
title: Alkalmazások közötti egyszeri bejelentkezés engedélyezése Androidon a ADAL használatával | Microsoft Docs
description: A ADAL SDK funkcióinak használata az alkalmazások egyszeri bejelentkezésének engedélyezéséhez.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: how-to
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: brandwe, jmprieur
ms.custom: aaddev, devx-track-java
ROBOTS: NOINDEX
ms.openlocfilehash: 9d1faf7aed5cf4c4975925c7a56d169dc99a7bd5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87313591"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-adal"></a>Útmutató: alkalmazások közötti egyszeri bejelentkezés engedélyezése Androidon a ADAL használatával

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Az egyszeri bejelentkezés (SSO) lehetővé teszi a felhasználók számára, hogy egyszer csak egyszer adják meg a hitelesítő adataikat, és hogy ezek a hitelesítő adatok automatikusan működjenek az alkalmazásokban és a más alkalmazások által használható platformok között (például Microsoft-fiókok vagy munkahelyi fiók Microsoft 365), függetlenül a közzétevőtől.

A Microsoft Identity platformja az SDK-k használatával egyszerűen engedélyezheti az egyszeri bejelentkezést a saját csomagján belül, illetve a közvetítői képességgel és a hitelesítő alkalmazásokkal a teljes eszközön.

Ebben az útmutatóban megtudhatja, hogyan konfigurálhatja az SDK-t az alkalmazáson belül, hogy SSO-t nyújtson ügyfeleinek.

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató feltételezi, hogy tudja, hogyan:

- Az alkalmazás üzembe helyezése az örökölt portálon Azure Active Directory (Azure AD) használatával. További információ: [alkalmazás regisztrálása](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
- Az alkalmazás integrálása az [Azure ad Android SDK](https://github.com/AzureAD/azure-activedirectory-library-for-android)-val.

## <a name="single-sign-on-concepts"></a>Az egyszeri bejelentkezés alapelvei

### <a name="identity-brokers"></a>Identity Broker

A Microsoft minden olyan mobil platformhoz biztosít alkalmazásokat, amely lehetővé teszi a különböző gyártóktól származó alkalmazások hitelesítő adatainak összekapcsolását, valamint olyan továbbfejlesztett funkciókat, amelyekhez egyetlen biztonságos hely szükséges a hitelesítő adatok érvényesítéséhez. Ezeket **közvetítőknek** nevezzük.

Az iOS-és Android-eszközökön a közvetítőket olyan letölthető alkalmazásokkal biztosítjuk, amelyeket az ügyfelek önállóan telepítenek vagy továbbítanak az eszközre egy olyan vállalat, aki az alkalmazottak számára az eszközöket felügyeli. A brókerek csak bizonyos alkalmazások vagy a teljes eszköz biztonságának felügyeletét támogatják a rendszergazda konfigurációja alapján. A Windowsban ezt a funkciót egy, az operációs rendszerbe beépített fiók-Chooser biztosítja, amely technikailag a webes hitelesítési közvetítőként is ismert.

#### <a name="broker-assisted-login"></a>Közvetítő által támogatott bejelentkezés

A közvetítő által támogatott bejelentkezések a közvetítő alkalmazáson belül bekövetkező bejelentkezési élmények, és a közvetítő tárterületének és biztonságának használatával megoszthatják a hitelesítő adatokat az eszközön található összes alkalmazásban, amely az Identity platformot alkalmazza. Az alkalmazások a brókertől függenek a felhasználók aláírására. Az iOS és az Android rendszerekben ezek a közvetítők olyan letölthető alkalmazásokon keresztül érhetők el, amelyeket az ügyfelek függetlenül telepítenek, vagy leküldhetik az eszközre egy olyan vállalat, aki felügyeli az eszközt a felhasználó számára. Ilyen típusú alkalmazás például az iOS rendszeren futó Microsoft Authenticator alkalmazás. A Windowsban ezt a funkciót egy, az operációs rendszerbe beépített fiók-Chooser biztosítja, amely technikailag a webes hitelesítési közvetítőként is ismert.
Az élmény a platformtól függ, és esetenként zavaró lehet a felhasználók számára, ha nem megfelelően kezeli őket. Valószínűleg ismeri ezt a mintát, ha telepítve van a Facebook-alkalmazás, és használja a Facebook-kapcsolatot egy másik alkalmazásból. Az Identity platform ugyanazt a mintát használja.

Androidon a fiók kiválasztása az alkalmazás tetején jelenik meg, ami kevésbé zavaró a felhasználó számára.

#### <a name="how-the-broker-gets-invoked"></a>A közvetítő meghívásának módja

Ha a kompatibilis közvetítő telepítve van az eszközön, például a Microsoft Authenticator alkalmazáshoz, az Identity SDK-k automatikusan elvégzik a közvetítő meghívását, ha a felhasználó azt jelzi, hogy az Identity platform bármelyik fiókjának használatával szeretne bejelentkezni.

#### <a name="how-microsoft-ensures-the-application-is-valid"></a>Hogyan biztosítja a Microsoft az alkalmazás érvényességét

Biztosítani kell, hogy a közvetítőt hívó alkalmazás identitása létfontosságú legyen a bróker által támogatott bejelentkezésekben biztosított biztonság szempontjából. az iOS és az Android nem kényszeríti ki az olyan egyedi azonosítókat, amelyek csak egy adott alkalmazás esetében érvényesek, így a rosszindulatú alkalmazások "hamisítják" a legitim alkalmazás azonosítóját, és megkapják a legitim alkalmazás számára jelentett jogkivonatokat. Annak biztosítása érdekében, hogy a Microsoft mindig a megfelelő alkalmazással kommunikáljon futásidőben, a fejlesztőnek meg kell adnia egy egyéni redirectURI, amikor regisztrálja alkalmazását a Microsofttal. **A fejlesztők számára az átirányítási URI-t az alábbi részletesen tárgyaljuk.** Ez az egyéni redirectURI tartalmazza az alkalmazás tanúsítványának ujjlenyomatát, és gondoskodik arról, hogy a Google Play Áruház egyedi legyen az alkalmazás számára. Amikor egy alkalmazás meghívja a közvetítőt, a közvetítő arra kéri az Android operációs rendszert, hogy a közvetítőnek nevezett tanúsítvány-ujjlenyomattal lássa el. A közvetítő ezt a tanúsítvány-ujjlenyomatot biztosít a Microsoftnak az Identity System hívása során. Ha az alkalmazás tanúsítványának ujjlenyomata nem egyezik meg a fejlesztő által a regisztráció során nekünk megadott tanúsítvány-ujjlenyomattal, a hozzáférés megtagadva az alkalmazás által kért erőforrás jogkivonatával. Ez az ellenőrzés biztosítja, hogy csak a fejlesztő által regisztrált alkalmazás fogadja a jogkivonatokat.

A felügyelt egyszeri bejelentkezéshez a következő előnyökkel jár:

* A felhasználó az összes alkalmazásában egyszeri bejelentkezést észlel a gyártótól függetlenül.
* Az alkalmazás olyan speciális üzleti funkciókat is használhat, mint a feltételes hozzáférés és az Intune-forgatókönyvek támogatása.
* Az alkalmazás képes a tanúsítványalapú hitelesítés támogatására az üzleti felhasználók számára.
* Biztonságosabb bejelentkezési élmény az alkalmazás identitása és a felhasználó ellenőrzése a közvetítő alkalmazás által további biztonsági algoritmusokkal és titkosítással történik.

Az alábbi példa azt szemlélteti, hogy az SDK hogyan működik együtt a közvetítő alkalmazásokkal az egyszeri bejelentkezés engedélyezéséhez:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
|  ADAL SDK  | |  ADAL SDK  |   |  ADAL SDK   |
+-----+------+-+-----+------+-  +-------+-----+
      |              |                  |
      |       +------v------+           |
      |       |             |           |
      |       | Microsoft   |           |
      +-------> Broker      |^----------+
              | Application
              |             |
              +-------------+
              |             |
              |   Broker    |
              |   Storage   |
              |             |
              +-------------+

```

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Az SSO bekapcsolása a közvetítő által támogatott egyszeri bejelentkezéshez

Alapértelmezés szerint ki van kapcsolva az alkalmazás azon képessége, hogy bármely, az eszközön telepített közvetítőt használhassanak. Ahhoz, hogy az alkalmazást a közvetítővel használhassa, további konfigurációt kell végrehajtania, és hozzá kell adnia egy kódot az alkalmazáshoz.

A követendő lépések a következők:

1. A Broker mód engedélyezése az alkalmazás kódjának az MS SDK-nak való hívásakor
2. Hozzon létre egy új átirányítási URI-t, és adja meg, hogy az alkalmazás és az alkalmazás regisztrálása is megtörténjen
3. A megfelelő engedélyek beállítása az Android-jegyzékben

#### <a name="step-1-enable-broker-mode-in-your-application"></a>1. lépés: a közvetítő mód engedélyezése az alkalmazásban

Az alkalmazás a közvetítő használatára való képessége a "beállítások" vagy a hitelesítési példány kezdeti beállításának létrehozásakor be van kapcsolva. Ehhez az alkalmazásban:

```
AuthenticationSettings.Instance.setUseBroker(true);
```

#### <a name="step-2-establish-a-new-redirect-uri-with-your-url-scheme"></a>2. lépés: hozzon létre egy új átirányítási URI-t az URL-sémával

Annak biztosítása érdekében, hogy a megfelelő alkalmazás megkapja a hitelesítő adatokhoz tartozó jogkivonatokat, meg kell győződnie arról, hogy az alkalmazás az Android operációs rendszer által ellenőrizhető módon visszahívja az alkalmazást. Az Android operációs rendszer a tanúsítvány kivonatát használja a Google Play áruházban. A tanúsítvány kivonatát nem lehet meghamisítani egy szélhámos alkalmazás. A közvetítő alkalmazás URI-ja mellett a Microsoft biztosítja, hogy a tokeneket a megfelelő alkalmazáshoz adja vissza. Az alkalmazásban regisztrálni kell egy egyedi átirányítási URI-t.

Az átirányítási URI-nak a megfelelő formában kell lennie:

`msauth://packagename/Base64UrlencodedSignature`

pl.: *msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D*

Az átirányítási URI-t regisztrálhatja az alkalmazás regisztrációjában a [Azure Portal](https://portal.azure.com/)használatával. Az Azure AD-alkalmazás regisztrálásával kapcsolatos további információkért lásd: [integráció a Azure Active Directoryával](../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

#### <a name="step-3-set-up-the-correct-permissions-in-your-application"></a>3. lépés: a megfelelő engedélyek beállítása az alkalmazásban

Az androidos Broker alkalmazás az Android operációs rendszer accounts Manager funkciójával kezeli a hitelesítő adatokat az alkalmazások között. Ahhoz, hogy használni lehessen a közvetítőt az Androidon, az alkalmazás jegyzékfájljának engedélyekkel kell rendelkeznie a AccountManager-fiókok használatához. Ezeket az engedélyeket részletesen megtárgyaljuk a [Google-dokumentációban itt: Account Manager](https://developer.android.com/reference/android/accounts/AccountManager.html)

Ezek az engedélyek különösen a következők:

```
GET_ACCOUNTS
USE_CREDENTIALS
MANAGE_ACCOUNTS
```

### <a name="youve-configured-sso"></a>Konfigurálta az SSO-t!

Most az Identity SDK automatikusan megosztja a hitelesítő adatokat az alkalmazásokban, és meghívja a közvetítőt, ha az eszközön van.

## <a name="next-steps"></a>Következő lépések

* Tudnivalók az [egyszeri bejelentkezéses SAML protokollról](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)

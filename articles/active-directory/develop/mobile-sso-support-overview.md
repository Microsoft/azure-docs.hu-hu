---
title: Az egyszeri bejelentkezés és az alkalmazás-védelmi szabályzatok támogatása a fejleszthető mobil alkalmazásokban | Azure
titleSuffix: Microsoft identity platform
description: Az egyszeri bejelentkezést és az alkalmazás-védelmi szabályzatokat támogató mobileszközök kiépítésének magyarázata és áttekintése a Microsoft Identity platform használatával és a Azure Active Directory integrálásával.
services: active-directory
author: knicholasa
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/14/2020
ms.author: nichola
ms.openlocfilehash: 4f0588667df6acb11a43e8c3469c67f65ed3cdd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98165178"
---
# <a name="support-single-sign-on-and-app-protection-policies-in-mobile-apps-you-develop"></a>Az egyszeri bejelentkezés és az alkalmazás-védelmi szabályzatok támogatása a fejleszthető mobil alkalmazásokban

Az egyszeri bejelentkezés (SSO) a Microsoft Identity platform és a Azure Active Directory egyik legfontosabb ajánlata, amely egyszerű és biztonságos bejelentkezéseket biztosít az alkalmazás felhasználói számára. Emellett az alkalmazás-védelmi szabályzatok (alkalmazás) lehetővé teszik a kulcsfontosságú biztonsági házirendek támogatását, amelyek megőrzik a felhasználó adatait. Ezek a funkciók együtt lehetővé teszik a biztonságos felhasználói bejelentkezéseket és az alkalmazás adatai felügyeletét.

> [!VIDEO https://www.youtube.com/embed/JpeMeTjQJ04]

Ez a cikk leírja, hogy az SSO és az alkalmazás miért fontos, és magas szintű útmutatást nyújt az ezeket a szolgáltatásokat támogató mobileszközök létrehozásához. Ez a telefon-és Tablet-alkalmazásokra is vonatkozik. Ha Ön rendszergazda, aki a szervezet Azure Active Directory bérlője számára szeretne SSO-t telepíteni, tekintse meg az [egyszeri bejelentkezéses telepítés megtervezésére vonatkozó útmutatást](../manage-apps/plan-sso-deployment.md) .

## <a name="about-single-sign-on-and-app-protection-policies"></a>Az egyszeri bejelentkezéssel és az alkalmazás-védelmi szabályzatokkal kapcsolatos tudnivalók

Az [egyszeri bejelentkezés (SSO)](../manage-apps/plan-sso-deployment.md) lehetővé teszi, hogy a felhasználó egyszer jelentkezzen be, és a hitelesítő adatok újbóli beírása nélkül hozzáférjen más alkalmazásokhoz. Ez megkönnyíti az alkalmazások elérését, és szükségtelenné teszi a felhasználók számára a felhasználónevek és jelszavak hosszú listáját. Az alkalmazásban való implementálása megkönnyíti az alkalmazás elérését és használatát.

Emellett az egyszeri bejelentkezés engedélyezése az alkalmazásban feloldja a modern hitelesítéssel, például a [jelszó nélküli bejelentkezésekkel](../authentication/concept-authentication-passwordless.md)létrehozott új hitelesítési mechanizmusok zárolását. A felhasználónevek és jelszavak a legnépszerűbb támadási vektorok az alkalmazásokkal szemben, és az egyszeri bejelentkezés engedélyezése lehetővé teszi, hogy a feltételes hozzáférés vagy a jelszóval nem rendelkező bejelentkezések, valamint a biztonságosabb hitelesítési mechanizmusok kikényszerítésével enyhítse ezt a kockázatot. Végezetül az egyszeri bejelentkezés engedélyezése is lehetővé teszi [az egyszeri](v2-protocols-oidc.md#single-sign-out)kijelentkezést. Ez olyan helyzetekben hasznos, mint például a megosztott eszközökön használt munkahelyi alkalmazások.

Az [alkalmazás-védelmi szabályzatok (alkalmazás)](/mem/intune/apps/app-protection-policy) gondoskodnak arról, hogy a szervezet adatbiztonsága és a benne lévők biztonságosak maradjanak. Lehetővé teszik a vállalatok számára, hogy egy alkalmazáson belül kezeljék és védjék adataikat, és lehetővé tegyék a vezérlést, hogy ki férhet hozzá az alkalmazáshoz és az adataihoz. Az alkalmazás-védelmi szabályzatok megvalósítása lehetővé teszi az alkalmazás számára a feltételes hozzáférési házirendek által védett erőforrásokhoz való kapcsolódást, valamint az adatok biztonságos átvitelét más védett alkalmazásokba. Az alkalmazás-védelmi szabályzatok által feloldott forgatókönyvek közé tartozik a PIN-kód megkövetelése egy alkalmazás megnyitásához, az adatok alkalmazások közötti megosztásának szabályozása, valamint a vállalati alkalmazásadatok személyes tárolóhelyre való mentésének megakadályozása.

## <a name="implementing-single-sign-on"></a>Egyszeri bejelentkezés megvalósítása

Javasoljuk, hogy az alkalmazás az egyszeri bejelentkezés előnyeit kihasználva engedélyezze az alábbiakat.

### <a name="use-the-microsoft-authentication-library-msal"></a>A Microsoft Authentication Library (MSAL) használata

Az egyszeri bejelentkezés az alkalmazásban való megvalósításának legjobb lehetősége a [Microsoft Authentication Library (MSAL)](msal-overview.md)használata. A MSAL használatával az alkalmazáshoz minimális kóddal és API-hívásokkal adhat hozzá hitelesítést, lekérheti a [Microsoft Identity platform](./index.yml)teljes funkcióit, és lehetővé teszi a Microsoft számára a biztonságos hitelesítési megoldás karbantartását. A MSAL alapértelmezés szerint egyszeri bejelentkezéses támogatást biztosít az alkalmazáshoz. Emellett a MSAL használata is követelmény, ha az alkalmazás-védelmi szabályzatok megvalósítását is tervezi.

> [!NOTE]
> A MSAL a beágyazott webes nézet használatára is konfigurálható. Ez megakadályozza az egyszeri bejelentkezést. Használja az alapértelmezett viselkedést (vagyis a rendszerböngészőt) az egyszeri bejelentkezés működésének biztosításához.

Ha jelenleg a [ADAL könyvtárat](../azuread-dev/active-directory-authentication-libraries.md) használja az alkalmazásban, akkor erősen ajánlott [áttelepíteni a MSAL](msal-migration.md)-be, mert a [ADAL elavult](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363).

Az iOS-alkalmazások [esetében egy rövid](quickstart-v2-ios.md) útmutató bemutatja, hogyan állíthatja be a bejelentkezéseket a MSAL használatával, valamint [útmutatást nyújt a MSAL konfigurálásához a különböző SSO-forgatókönyvekhez](single-sign-on-macos-ios.md).

Az Android-alkalmazások esetében [van egy rövid útmutató, amely](quickstart-v2-android.md) bemutatja, hogyan állíthatja be a bejelentkezéseket a MSAL használatával, valamint útmutatást nyújt az [alkalmazások közötti egyszeri bejelentkezés engedélyezéséhez az Androidon a MSAL használatával](msal-android-single-sign-on.md).

### <a name="use-the-system-web-browser"></a>A rendszerböngésző használata

Interaktív hitelesítéshez böngésző szükséges. A MSAL (azaz más OpenID Connect-vagy SAML-függvénytárak) használó modern hitelesítési kódtárakat használó Mobile apps esetében, vagy ha saját hitelesítési kódot valósít meg, akkor a rendszerböngészőt kell használnia hitelesítési felületként az egyszeri bejelentkezés engedélyezéséhez.

A Google útmutatást nyújt erre az Android-alkalmazásokban: [Chrome Custom tabs-Google Chrome](https://developer.chrome.com/multidevice/android/customtabs).

Az Apple útmutatást nyújt ennek elvégzéséhez iOS-alkalmazásokban: [felhasználó hitelesítése webszolgáltatással | Apple fejlesztői dokumentáció](https://developer.apple.com/documentation/authenticationservices/authenticating_a_user_through_a_web_service).

> [!TIP]
> Az [Apple-eszközök egyszeri bejelentkezéses beépülő modulja](apple-sso-plugin.md) lehetővé teszi az egyszeri bejelentkezést a felügyelt eszközökön az Intune használatával beágyazott webes nézeteket használó iOS-alkalmazásokhoz. Javasoljuk, hogy a MSAL és a rendszerböngészőt olyan alkalmazások fejlesztésére ajánljuk, amelyek lehetővé teszik az egyszeri bejelentkezést az összes felhasználó számára, de ez bizonyos helyzetekben lehetővé teszi az egyszeri bejelentkezést, ha egyébként nem lehetséges.

## <a name="enable-app-protection-policies"></a>Alkalmazás-védelmi házirendek engedélyezése

Az alkalmazás-védelmi szabályzatok engedélyezéséhez használja a [Microsoft Authentication Library (MSAL)](msal-overview.md)eszközt. A MSAL a Microsoft Identity platform hitelesítési és engedélyezési könyvtára, és az Intune SDK-t úgy alakítottuk ki, hogy párhuzamosan működjön.

Emellett a hitelesítéshez egy közvetítő alkalmazást kell használnia. A közvetítő megköveteli, hogy az alkalmazás megadjon egy alkalmazást és egy eszközt az alkalmazások megfelelőségének biztosításához. az iOS-felhasználók a [Microsoft Authenticator alkalmazást](../user-help/user-help-auth-app-sign-in.md) fogják használni, és az Android-felhasználók a Microsoft Authenticator alkalmazást vagy a [céges portál alkalmazást](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) felügyelt [hitelesítésre](./msal-android-single-sign-on.md)használják. Alapértelmezés szerint a MSAL egy közvetítőt használ a hitelesítési kérések teljesítéséhez, így a közvetítő hitelesítésre való használata automatikusan engedélyezve lesz az alkalmazáshoz, amikor a MSAL-t használja.

Végül [adja hozzá az INTUNE SDK](/mem/intune/developer/app-sdk-get-started) -t az alkalmazáshoz az alkalmazás-védelmi szabályzatok engedélyezéséhez. Az SDK a legtöbb esetben egy elfogási modellt követ, és automatikusan alkalmazza az alkalmazás-védelmi házirendeket annak meghatározására, hogy az alkalmazás által végzett műveletek engedélyezettek-e vagy sem. Vannak olyan API-k is, amelyekkel manuálisan hívható meg az alkalmazás, ha bizonyos műveletek korlátozásai vannak.

## <a name="additional-resources"></a>További források

- [Azure Active Directory egyszeri bejelentkezéses telepítés megtervezése](../manage-apps/plan-sso-deployment.md)
- [Útmutató: az SSO konfigurálása macOS és iOS rendszeren](single-sign-on-macos-ios.md)
- [Microsoft Enterprise SSO beépülő modul Apple-eszközökhöz (előzetes verzió)](apple-sso-plugin.md)
- [Felügyelt hitelesítés az Androidban](./msal-android-single-sign-on.md)
- [Engedélyezési ügynökök és azok engedélyezése](./msal-android-single-sign-on.md)
- [Bevezetés a Microsoft Intune App SDK használatába](/mem/intune/developer/app-sdk-get-started)
- [Az Intune App SDK-beállítások konfigurálása](/mem/intune/developer/app-sdk-ios#configure-settings-for-the-intune-app-sdk)
- [Microsoft Intune védett alkalmazások](/mem/intune/apps/apps-supported-intune-apps)

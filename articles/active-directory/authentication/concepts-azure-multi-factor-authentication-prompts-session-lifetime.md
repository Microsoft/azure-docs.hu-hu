---
title: Azure AD Multi-Factor Authentication promptok és munkamenetek élettartama
description: Ismerje meg, hogy az Azure AD Multi-Factor Authentication és a munkamenetek élettartama milyen módon legyen alkalmazva az újrahitelesítéshez.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0033b6985313de351ffdaf7028d2c1e9bd76ba72
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97963519"
---
# <a name="optimize-reauthentication-prompts-and-understand-session-lifetime-for-azure-ad-multi-factor-authentication"></a>Újrahitelesítéssel kapcsolatos kérések optimalizálása és az Azure AD-munkamenet élettartamának megértése Multi-Factor Authentication

Azure Active Directory (Azure AD) több beállítással rendelkezik, amelyek meghatározzák, hogy a felhasználóknak milyen gyakran kell újrahitelesíteniük magukat. Ez az újrahitelesítés olyan első tényező lehet, mint például a jelszó, a vagy a jelszóval nem rendelkező Microsoft Authenticator vagy a többtényezős hitelesítés (MFA) végrehajtása. Ezeket az újrahitelesítési beállításokat igény szerint konfigurálhatja a saját környezetéhez és a kívánt felhasználói élményhez.

Az Azure AD alapértelmezett konfigurációja a felhasználói bejelentkezés gyakorisága 90 napos gördülő ablak. A felhasználók a hitelesítő adatokkal való megkérdezése gyakran úgy tűnik, mint egy értelmes dolog, de nem sül el. Ha a felhasználók úgy vannak betanítva, hogy gondolkodás nélkül megadják a hitelesítő adataikat, akaratlanul is megadhatják azokat a hitelesítő adatok megadásához.

Előfordulhat, hogy a felhasználó nem kér vissza egy felhasználót, de az IT-szabályzatok megszegése visszavonja a munkamenetet. Ilyen például a jelszó módosítása, a nem megfelelő eszköz vagy a fiók letiltási művelete. Explicit módon [visszavonhatja a felhasználói munkameneteket a PowerShell használatával](/powershell/module/azuread/revoke-azureaduserallrefreshtoken).

Ez a cikk részletesen ismerteti a javasolt konfigurációkat, valamint azt, hogy a különböző beállítások hogyan működnek és hogyan hatnak egymással.

## <a name="recommended-settings"></a>Ajánlott beállítások

Annak érdekében, hogy a felhasználók a megfelelő időközönként jelentkezzenek be a biztonságra és a könnyű használatra, a következő konfigurációkat javasoljuk:

* Ha prémium szintű Azure AD:
    * Az egyszeri bejelentkezés (SSO) engedélyezése a [felügyelt eszközökön](../devices/overview.md) vagy a [zökkenőmentes egyszeri](../hybrid/how-to-connect-sso.md)bejelentkezésen keresztül az alkalmazások között.
    * Ha újrahitelesítésre van szükség, használjon feltételes hozzáférési [bejelentkezési gyakorisági szabályzatot](../conditional-access/howto-conditional-access-session-lifetime.md).
    * Azon felhasználók esetében, akik nem felügyelt eszközökről vagy mobileszköz-forgatókönyvekről jelentkeznek be, a feltételes hozzáféréssel lehetővé teszik az állandó böngésző-munkamenetek és a bejelentkezési gyakorisági házirendek használatát.
* Ha rendelkezik Microsoft 365 alkalmazás-licenccel vagy az ingyenes Azure AD-csomaggal:
    * Az egyszeri bejelentkezés (SSO) engedélyezése a [felügyelt eszközökön](../devices/overview.md) vagy a [zökkenőmentes egyszeri](../hybrid/how-to-connect-sso.md)bejelentkezésen keresztül az alkalmazások között.
    * Tartsa meg a *bejelentkezett maradás* lehetőséget, és irányítsa a felhasználókat, hogy fogadják el.
* A mobileszközök esetében ügyeljen arra, hogy a felhasználók a Microsoft Authenticator alkalmazást használják. Ez az alkalmazás közvetítőként használható más Azure AD összevont alkalmazásokhoz, és csökkenti a hitelesítési kéréseket az eszközön.

A kutatások azt mutatják, hogy ezek a beállítások a legtöbb bérlő esetében megfelelőek. Ezek a beállítások bizonyos kombinációi, például az *MFA megjegyzése* és *a megmaradt*, megadhatják, hogy a felhasználók túl gyakran hitelesítsék magukat. A rendszeres újrahitelesítési kérések helytelenek a felhasználói hatékonyság szempontjából, és sebezhetővé tehetik azokat a támadásokkal szemben.

## <a name="azure-ad-session-lifetime-configuration-settings"></a>Azure AD-munkamenet élettartamának konfigurációs beállításai

A felhasználókra vonatkozó hitelesítési kérések gyakoriságának optimalizálásához beállíthatja az Azure AD-munkamenet élettartamának beállításait. Ismerje meg a vállalat és a felhasználók igényeit, és konfigurálja a környezete számára legmegfelelőbb egyensúlyt biztosító beállításokat.

### <a name="evaluate-session-lifetime-policies"></a>Munkamenet élettartamára vonatkozó szabályzatok kiértékelése

A munkamenet-élettartam beállítása nélkül nincsenek állandó cookie-k a böngésző-munkamenetben. Minden alkalommal, amikor egy felhasználó bezárja és megnyitja a böngészőt, rákérdez az újrahitelesítésre. Az Office-ügyfelekben az alapértelmezett időtartam 90 nap. Ezzel az alapértelmezett Office-konfigurációval, ha a felhasználó alaphelyzetbe állítja a jelszavát, vagy több mint 90 nap van használatban, a felhasználónak minden szükséges tényezővel újra hitelesítenie kell magát (első és második tényező).

Előfordulhat, hogy egy felhasználó több MFA-kérést lát egy olyan eszközön, amely nem rendelkezik identitással az Azure AD-ben. Több kérdés is jár, ha mindegyik alkalmazás saját OAuth frissítési tokent használ, amely nem más ügyfélalkalmazások számára van megosztva. Ebben a forgatókönyvben az MFA többször kéri az adatfeldolgozást, mivel az egyes alkalmazások OAuth frissítési jogkivonatot igényelnek az MFA-val való ellenőrzéshez.

Az Azure AD-ben a munkamenet-élettartam legszigorúbb szabályzata határozza meg, hogy mikor kell újrahitelesíteni a felhasználót. Vegyük példaként a következő esetet:

* Az engedélyezés után a rendszer *bejelentkezett marad*, amely állandó böngésző cookie-t használ, és
* 14 napig is engedélyezheti *az MFA megemlékezését*

Ebben a példában a felhasználónak 14 naponta újra kell hitelesítenie magát. Ez a viselkedés a legszigorúbb házirendet követi, bár a saját maga által *bejelentkezett* felhasználók nem igénylik a felhasználónak a böngésző újrahitelesítését.

### <a name="managed-devices"></a>Felügyelt eszközök

Az Azure ad JOIN vagy a Hybrid Azure AD JOIN használatával csatlakoztatott eszközök az egyszeri bejelentkezést (SSO) [használják az alkalmazások](../devices/concept-primary-refresh-token.md) között. Ez a PRT lehetővé teszi, hogy a felhasználó egyszer jelentkezzen be az eszközön, és lehetővé teszi az informatikai részleg számára, hogy megfeleljen a biztonsági és megfelelőségi szabványoknak. Ha egy felhasználó számára meg kell kérni, hogy gyakrabban jelentkezzen be egy csatlakoztatott eszközön egyes alkalmazásokhoz vagy forgatókönyvekhez, ez a [feltételes hozzáférés bejelentkezési gyakorisága](../conditional-access/howto-conditional-access-session-lifetime.md)használatával érhető el.

### <a name="show-option-to-remain-signed-in"></a>A bejelentkezés megmaradása lehetőség megjelenítése

Ha a felhasználó az **Igen** lehetőséget választja a *bejelentkezett tartózkodás?* lehetőségnél, a bejelentkezés során állandó cookie van beállítva a böngészőben. Ez az állandó cookie az első és a második tényezőt is felhasználja, és csak a böngészőben megjelenő hitelesítési kérelmekre vonatkozik.

![A bejelentkezett üzenetet bemutató képernyőkép](./media/concepts-azure-multi-factor-authentication-prompts-session-lifetime/stay-signed-in-prompt.png)

Ha prémium szintű Azure AD 1 licenccel rendelkezik, javasoljuk, hogy a feltételes hozzáférési szabályzatot használja az *állandó böngésző-munkamenethez*. Ez a szabályzat felülírja a *bejelentkezett maradni?* beállítást, és továbbfejlesztett felhasználói élményt nyújt. Ha nem rendelkezik prémium szintű Azure AD 1 licenccel, javasoljuk, hogy a felhasználók számára engedélyezze a bejelentkezett maradás beállítást.

További információ a felhasználók bejelentkezésének engedélyezéséről: az [Azure ad bejelentkezési oldalának testreszabása](../fundamentals/customize-branding.md#customize-your-azure-ad-sign-in-page).

### <a name="remember-multi-factor-authentication"></a>Ne feledje Multi-Factor Authentication  

Ezzel a beállítással megadhatja az 1-365 nap közötti értékeket, és beállíthatja a böngészőben az állandó cookie-t, ha a felhasználó a bejelentkezéskor a **ne Kérdezzen újra X napra** lehetőséget választja.

![A bejelentkezési kérés jóváhagyására figyelmeztető üzenet képernyőképe](./media/concepts-azure-multi-factor-authentication-prompts-session-lifetime/approve-sign-in-request.png)

Habár ez a beállítás csökkenti a webes alkalmazások hitelesítésének számát, növeli a modern hitelesítési ügyfelek (például az Office-ügyfelek) hitelesítésének számát. Ezek az ügyfelek általában csak a jelszó alaphelyzetbe állítása vagy a 90 napos inaktivitás után kérik. Azonban az érték 90 napnál rövidebbre állítása lerövidíti az Office-ügyfelek alapértelmezett MFA-kéréseit, és növeli az újrahitelesítés gyakoriságát. A **továbbra is bejelentkezett** vagy feltételes hozzáférési szabályzatok együttes használata esetén növelheti a hitelesítési kérések számát.

Ha az *MFA* -t használja, és prémium szintű Azure ad 1 licenccel rendelkezik, érdemes lehet áttelepíteni ezeket a beállításokat a feltételes hozzáférés bejelentkezési gyakoriságára. Máskülönben érdemes lehet a *bejelentkezve maradni?* helyette.

További információ: [megjegyzés multi-Factor Authentication](howto-mfa-mfasettings.md#remember-multi-factor-authentication).

### <a name="authentication-session-management-with-conditional-access"></a>A hitelesítési munkamenet kezelése feltételes hozzáféréssel

A **bejelentkezési gyakoriság** lehetővé teszi a rendszergazda számára, hogy kiválassza a bejelentkezési gyakoriságot, amely az ügyfél és a böngésző mind az első, mind a második tényezőre vonatkozik. Javasoljuk, hogy ezeket a beállításokat a felügyelt eszközök használatával együtt használja olyan helyzetekben, amikor szükség van a hitelesítési munkamenet korlátozására, például a kritikus fontosságú üzleti alkalmazásokra.

Az **állandó böngésző-munkamenet** lehetővé teszi a felhasználók számára, hogy a böngészőablak bezárása és újbóli megnyitása után is bejelentkezve maradjanak. A *továbbra is bejelentkezett* beállításhoz hasonlóan az állandó cookie-t állít be a böngészőben. Mivel azonban a rendszergazda konfigurálja, nem szükséges, hogy a felhasználó válassza az **Igen** lehetőséget a *bejelentkezett maradás?* lehetőségnél, így jobb felhasználói élményt nyújt. Ha a *továbbra is bejelentkezett?* lehetőséget használja, javasoljuk, hogy engedélyezze az **állandó böngésző-munkamenet** -házirendet.

További tudnivalók. Lásd: [a hitelesítési munkamenetek kezelésének beállítása feltételes hozzáféréssel](../conditional-access/howto-conditional-access-session-lifetime.md).

### <a name="configurable-token-lifetimes"></a>Konfigurálható jogkivonat élettartama

Ez a beállítás lehetővé teszi a Azure Active Directory által kiállított jogkivonat élettartamának konfigurálását. Ezt a házirendet a *feltételes hozzáféréssel rendelkező hitelesítési munkamenetek kezelése váltja fel*. Ha jelenleg *konfigurálható jogkivonat-élettartamot* használ, javasoljuk, hogy a feltételes hozzáférési szabályzatok áttelepítését indítsa el.

## <a name="review-your-tenant-configuration"></a>A bérlő konfigurációjának áttekintése  

Most, hogy megértette, hogyan működik a különböző beállítások és az ajánlott konfiguráció, itt az ideje, hogy ellenőrizze a bérlők konfigurációját, és ennek megfelelően végezze el a módosításokat:

A *továbbra is bejelentkezett* lehetőség konfigurálásához vagy ellenőrzéséhez hajtsa végre a következő lépéseket:

1. Az Azure AD-portálon keresse meg és válassza ki a *Azure Active Directory*.
1. Válassza a **vállalati arculat** lehetőséget, majd az egyes területi beállításoknál válassza a **Megjelenítés lehetőséget, hogy továbbra is bejelentkezve maradjon**.
1. Válassza az *Igen*, majd a **Mentés** lehetőséget.

A többtényezős hitelesítési beállítások a megbízható eszközökön való megadásához hajtsa végre a következő lépéseket:

1. Az Azure AD-portálon keresse meg és válassza ki a *Azure Active Directory*.
1. Válassza a **Biztonság**, majd a **MFA** elemet.
1. A **Konfigurálás** területen válassza a **további felhőalapú MFA-beállítások** lehetőséget.
1. A *multi-Factor Authentication szolgáltatás beállításai* lapon görgessen a **többtényezős hitelesítési beállítások megjegyzésre**. A jelölőnégyzet bejelölésének törlésével tiltsa le a beállítást.

A bejelentkezési gyakoriság és az állandó böngésző-munkamenet feltételes hozzáférési szabályzatának konfigurálásához hajtsa végre a következő lépéseket:

1. Az Azure AD-portálon keresse meg és válassza ki a *Azure Active Directory*.
1. Válassza a **Biztonság**, majd a **feltételes hozzáférés** lehetőséget.
1. A szabályzatot a jelen cikkben ismertetett ajánlott munkamenet-kezelési beállításokkal konfigurálhatja.

A jogkivonatok élettartamának áttekintéséhez az [Azure ad PowerShell használatával kérdezheti le az Azure ad-szabályzatokat](../develop/configure-token-lifetimes.md#get-started). Tiltsa le a meglévő szabályzatokat.

Ha több beállítás van engedélyezve a bérlőben, javasoljuk, hogy az Ön számára elérhető Licencelés alapján frissítse a beállításait. Ha például az Azure AD Premium-licencekkel rendelkezik, csak a *bejelentkezési gyakoriság* és az *állandó böngésző-munkamenet* feltételes hozzáférési szabályzatát kell használnia. Ha Microsoft 365 alkalmazásait vagy az Azure AD ingyenes licenceit, használja a *továbbra is bejelentkezett?* konfigurációt.

Ha engedélyezte a konfigurálható jogkivonat élettartamát, ez a funkció hamarosan el lesz távolítva. Tervezze meg az áttelepítést egy feltételes hozzáférési szabályzatba.

A következő táblázat a licenceken alapuló ajánlásokat foglalja össze:

|              | Alkalmazások ingyenes Azure AD és Microsoft 365 | Azure AD Premium |
|------------------------------|-----------------------------------|------------------|
| **SSO**                      | [Azure ad JOIN](../devices/concept-azure-ad-join.md) vagy [Hybrid Azure ad JOIN](../devices/concept-azure-ad-join-hybrid.md)vagy [zökkenőmentes SSO](../hybrid/how-to-connect-sso.md) a nem felügyelt eszközökhöz. | Azure AD-csatlakozás<br />Hibrid Azure AD-csatlakozás |
| **Újrahitelesítés beállításai** | Továbbra is bejelentkezett                  | Feltételes hozzáférési szabályzatok használata a bejelentkezés gyakoriságához és az állandó böngésző-munkamenethez |

## <a name="next-steps"></a>Következő lépések

Az első lépésekhez fejezze be az oktatóanyagot a [felhasználói bejelentkezési események biztonságossá tételéhez az Azure ad multi-Factor Authentication](tutorial-enable-azure-mfa.md) , vagy [használjon kockázati észleléseket felhasználói bejelentkezésekhez az azure ad-multi-Factor Authentication](tutorial-risk-based-sspr-mfa.md)kiváltásához.

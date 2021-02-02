---
title: Egyszeri bejelentkezési lehetőségek az Azure AD-ben
description: Ismerje meg az egyszeri bejelentkezés (SSO) Azure Active Directoryban elérhető lehetőségeit.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: kenwith
ms.reviewer: arvindh, japere
ms.openlocfilehash: 2bf84a22a384e6079c2d85c833b34ba37eecaa46
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99252355"
---
# <a name="single-sign-on-options-in-azure-ad"></a>Egyszeri bejelentkezési lehetőségek az Azure AD-ben

Az egyszeri bejelentkezés számos előnyt biztosít a hagyományos bejelentkezési módszerekhez képest.

- **Egyszeri bejelentkezés** esetén a felhasználók egyszer jelentkeznek be egyetlen fiókkal a tartományhoz csatlakoztatott eszközök, a vállalati erőforrások, a szolgáltatott szoftver (SaaS) alkalmazások és a webalkalmazások eléréséhez. A bejelentkezést követően a felhasználó az Office 365 portálról vagy a saját alkalmazásaiból is indíthat alkalmazásokat. A rendszergazdák központilag konfigurálhatják a felhasználói fiókok felügyeletét, és a csoporttagság alapján automatikusan hozzáadhatják vagy eltávolíthatják az alkalmazásokhoz való felhasználói hozzáférést.

- **Egyszeri bejelentkezés nélkül a** felhasználóknak emlékezniük kell az alkalmazásfüggő jelszavakra, és be kell jelentkezniük minden alkalmazásba. Az informatikai részlegnek az egyes alkalmazásokhoz (például Microsoft 365, Box és Salesforce) tartozó felhasználói fiókokat kell létrehoznia és frissítenie. A felhasználóknak emlékezniük kell a jelszavukat, és időt kell fordítaniuk arra, hogy bejelentkezzenek az egyes alkalmazásokra.

További információ az egyszeri bejelentkezésről: [Mi az egyszeri bejelentkezés?](what-is-single-sign-on.md).

## <a name="choosing-a-single-sign-on-method"></a>Egyszeri bejelentkezés módszerének kiválasztása

Az egyszeri bejelentkezéshez többféleképpen is konfigurálhat egy alkalmazást. Az egyszeri bejelentkezés módszerének kiválasztása attól függ, hogy az alkalmazás hogyan van konfigurálva a hitelesítéshez.

- A felhőalapú alkalmazások OpenID Connect-, OAuth-, SAML-, jelszó-alapú, csatolt vagy letiltott metódusokat használhatnak az egyszeri bejelentkezéshez. 
- A helyszíni alkalmazások jelszó-alapú, integrált Windows-hitelesítést, fejléc-alapú, csatolt vagy letiltott metódusokat használhatnak az egyszeri bejelentkezéshez. A helyszíni döntések akkor működnek, ha az alkalmazások alkalmazásproxy használatára vannak konfigurálva.

Ez a folyamatábra segít eldönteni, hogy melyik egyszeri bejelentkezési módszer a legmegfelelőbb az adott helyzetben.

![Az egyszeri bejelentkezési módszer döntési folyamatábrája](./media/what-is-single-sign-on/choose-single-sign-on-method-040419.png)

Az alábbi táblázat összefoglalja az egyszeri bejelentkezés módszereit, és további részletekre mutató hivatkozásokat tartalmaz.

| Egyszeri bejelentkezési módszer | Alkalmazástípusok | A következő esetekben használja |
| :------ | :------- | :----- |
| [OpenID Connect és OAuth](#openid-connect-and-oauth) | Felhőbeli és helyszíni | Az OpenID Connect és a OAuth használata új alkalmazás fejlesztésekor. Ez a protokoll leegyszerűsíti az alkalmazások konfigurációját, és könnyen használható SDK-kat használ, és lehetővé teszi az alkalmazás számára az MS Graph használatát.
| [SAML](#saml-sso) | Felhőbeli és helyszíni | Válassza ki az SAML-t, amikor lehetséges olyan meglévő alkalmazások esetében, amelyek nem használnak OpenID connectet vagy OAuth. Az SAML olyan alkalmazások esetében működik, amelyek az egyik SAML protokoll használatával végzik a hitelesítést.|
| [Jelszó-alapú](#password-based-sso) | Felhőbeli és helyszíni | Válassza a jelszó-alapú lehetőséget, ha az alkalmazás hitelesíti magát a felhasználónévvel és a jelszóval. A jelszó-alapú egyszeri bejelentkezés lehetővé teszi a biztonságos alkalmazások jelszavainak tárolását és a visszajátszás használatát webböngészős bővítmény vagy mobil alkalmazás használatával. Ez a módszer az alkalmazás által biztosított meglévő bejelentkezési folyamatot használja, de lehetővé teszi a rendszergazdák számára a jelszavak kezelését. |
| [Csatolt](#linked-sign-on) | Felhőbeli és helyszíni | Válassza a csatolt bejelentkezés lehetőséget, ha az alkalmazás az egyszeri bejelentkezéshez van konfigurálva egy másik identitás-szolgáltatói szolgáltatásban. Ez a beállítás nem ad hozzá egyszeri bejelentkezést az alkalmazáshoz. Előfordulhat azonban, hogy az alkalmazás már rendelkezik egy másik szolgáltatás, például Active Directory összevonási szolgáltatások (AD FS) használatával történő egyszeri bejelentkezéssel.|
| [Disabled](#disabled-sso) | Felhőbeli és helyszíni | Válassza a letiltott egyszeri bejelentkezés lehetőséget, ha az alkalmazás nem áll készen az egyszeri bejelentkezésre való konfigurálásra. Ez az alapértelmezett mód az alkalmazás létrehozásakor.|
| [Integrált Windows-hitelesítés (IWA)](#integrated-windows-authentication-iwa-sso) | csak helyszíni | Az [integrált Windows-hitelesítést (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)vagy jogcímbarát alkalmazásokat használó alkalmazások esetében válassza az IWA egyszeri bejelentkezés lehetőséget. A IWA esetében az alkalmazásproxy-összekötők a Kerberos által korlátozott delegálás (KCD) használatával hitelesítik a felhasználókat az alkalmazásban. |
| [Fejléc-alapú](#header-based-sso) | csak helyszíni | A fejléc-alapú egyszeri bejelentkezést akkor használja, ha az alkalmazás fejléceket használ a hitelesítéshez. Az alkalmazásproxy az Azure AD használatával hitelesíti a felhasználót, majd az összekötő szolgáltatáson keresztül továbbítja a forgalmat.  |

## <a name="openid-connect-and-oauth"></a>OpenID Connect és OAuth

Új alkalmazások fejlesztésekor a modern protokollok, például az OpenID Connect és a OAuth segítségével érheti el a legjobb egyszeri bejelentkezési élményt az alkalmazáshoz több eszköz platformján. A OAuth lehetővé teszi [, hogy a](configure-user-consent.md) felhasználók vagy a rendszergazdák beleegyeznek a védett erőforrások, például a [Microsoft Graph](/graph/overview)számára. Egyszerűen bevezetheti az [SDK](../develop/reference-v2-libraries.md) -kat az alkalmazásához, és emellett az alkalmazás készen áll a [Microsoft Graph](/graph/overview)használatára.

További információkért lásd:

- [OAuth 2,0](../develop/v2-oauth2-auth-code-flow.md)
- [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)
- [A Microsoft Identity platform fejlesztői útmutatója](../develop/index.yml).

## <a name="saml-sso"></a>SAML SSO

Az **SAML egyszeri bejelentkezéssel** az Azure ad hitelesíti az alkalmazást a felhasználó Azure ad-fiókjának használatával. Az Azure AD a bejelentkezési adatokat kapcsolati protokollon keresztül továbbítja az alkalmazásnak. Az SAML-alapú egyszeri bejelentkezéssel az SAML-jogcímek által meghatározott szabályok alapján leképezheti a felhasználókat bizonyos alkalmazási szerepkörökre.

Ha az alkalmazás támogatja, válassza az SAML-alapú egyszeri bejelentkezés lehetőséget.

Az SAML-alapú egyszeri bejelentkezés a következő protokollok bármelyikét használó alkalmazások esetén támogatott:

- SAML 2.0
- WS-Federation

Az SAML-alapú egyszeri bejelentkezéshez használható SaaS-alkalmazás konfigurálásával kapcsolatban lásd: [SAML-alapú egyszeri bejelentkezés konfigurálása](configure-saml-single-sign-on.md). Emellett számos szoftveres (SaaS) alkalmazás rendelkezik egy [alkalmazásspecifikus oktatóanyaggal](../saas-apps/tutorial-list.md) , amely VÉGIGVEZETI az SAML-alapú egyszeri bejelentkezés konfigurációjának lépésein.

A WS-Federation alkalmazás konfigurálásához kövesse ugyanezt az útmutatót az alkalmazás SAML-alapú egyszeri bejelentkezéshez való konfigurálásához. Az alkalmazás Azure AD-beli használatára való konfigurálásának lépéséhez le kell cserélnie az Azure AD bejelentkezési URL-címét a WS-Federation végponthoz `https://login.microsoftonline.com/<tenant-ID>/wsfed` .

A helyszíni alkalmazások SAML-alapú egyszeri bejelentkezéshez való konfigurálásáról lásd: [SAML egyszeri bejelentkezés a helyszíni alkalmazásokhoz alkalmazásproxy](application-proxy-configure-single-sign-on-on-premises-apps.md)használatával.

További információ az SAML protokollról: [egyszeri bejelentkezéses SAML protokoll](../develop/single-sign-on-saml-protocol.md).

## <a name="password-based-sso"></a>Jelszóalapú SSO

A jelszó-alapú bejelentkezéssel a felhasználók első alkalommal jelentkeznek be az alkalmazásba egy felhasználónévvel és jelszóval. Az első bejelentkezés után az Azure AD megadja a felhasználónevet és a jelszót az alkalmazáshoz.

A jelszó alapú egyszeri bejelentkezés az alkalmazás által biztosított meglévő hitelesítési folyamatot használja. Ha engedélyezi a jelszó-egyszeri bejelentkezést az alkalmazáshoz, az Azure AD összegyűjti és biztonságosan tárolja az alkalmazáshoz tartozó felhasználóneveket és jelszavakat. A felhasználói hitelesítő adatokat a rendszer titkosított állapotban tárolja a címtárban.

Válassza a jelszó alapú egyszeri bejelentkezés lehetőséget, ha:

- Egy alkalmazás nem támogatja az SAML egyszeri bejelentkezési protokollt.
- Az alkalmazások hozzáférési tokenek és fejlécek helyett felhasználónévvel és jelszóval hitelesítve vannak.

>[!NOTE]
>A jelszó-alapú egyszeri bejelentkezéshez nem alkalmazhat feltételes hozzáférési szabályzatokat vagy többtényezős hitelesítést.

A jelszó alapú egyszeri bejelentkezés minden olyan felhőalapú alkalmazás esetében támogatott, amely HTML-alapú bejelentkezési oldallal rendelkezik. A felhasználó a következő böngészők bármelyikét használhatja:

- Internet Explorer 11 Windows 7 vagy újabb rendszeren
   > [!NOTE]
   > Az Internet Explorer korlátozott támogatást nyújt, és többé nem kap új szoftverfrissítéseket. A Microsoft Edge az ajánlott böngésző.

- Microsoft Edge a Windows 10 évfordulós kiadásán vagy később
- Microsoft Edge iOS és Android rendszerhez
- Intune Managed Browser
- Chrome a Windows 7 vagy újabb, illetve macOS X vagy újabb rendszereken
- Firefox 26,0 vagy újabb verzió Windows XP SP2 vagy újabb rendszeren, és macOS X 10,6 vagy újabb verzióban

A jelszó-alapú egyszeri bejelentkezéshez használt felhőalapú alkalmazás konfigurálásával kapcsolatban lásd: [jelszó egyszeri bejelentkezésének konfigurálása](configure-password-single-sign-on-non-gallery-applications.md).

Ha helyszíni alkalmazást szeretne konfigurálni az Application proxyn keresztüli egyszeri bejelentkezéshez, tekintse meg a [jelszó-tároló az egyszeri bejelentkezéshez az alkalmazásproxy](application-proxy-configure-single-sign-on-password-vaulting.md) használatával című témakört.

### <a name="how-authentication-works-for-password-based-sso"></a>Hogyan működik a hitelesítés a jelszó-alapú egyszeri bejelentkezéshez

Ha egy felhasználót szeretne hitelesíteni egy alkalmazásban, az Azure AD lekéri a felhasználó hitelesítő adatait a címtárból, és beírja azokat az alkalmazás bejelentkezési oldalára.  Az Azure AD biztonságosan továbbítja a felhasználói hitelesítő adatokat a webböngésző vagy a Mobile App használatával. Ez a folyamat lehetővé teszi a rendszergazdák számára a felhasználói hitelesítő adatok kezelését, és nem igénylik a felhasználóktól a jelszavuk megemlékezését.

> [!IMPORTANT]
> A hitelesítő adatokat a rendszer az automatikus bejelentkezési folyamat során eltorzítja a felhasználótól. A hitelesítő adatok azonban a webes hibakeresési eszközök használatával felderíthetők. A felhasználóknak és a rendszergazdáknak ugyanazokat a biztonsági házirendeket kell követniük, mintha közvetlenül a felhasználó megadta a hitelesítő adatokat.

### <a name="managing-credentials-for-password-based-sso"></a>Jelszó-alapú SSO hitelesítő adatainak kezelése

Az egyes alkalmazásokhoz tartozó jelszavakat az Azure AD-rendszergazda vagy a felhasználók is kezelhetik.

Amikor az Azure AD-rendszergazda kezeli a hitelesítő adatokat:  

- A felhasználónak nem kell alaphelyzetbe állítania vagy megjegyeznünk a felhasználónevet és a jelszót. A felhasználó a saját alkalmazásokban vagy egy megadott hivatkozáson keresztül érheti el az alkalmazást.
- A rendszergazda felügyeleti feladatokat végezhet a hitelesítő adatokon. A rendszergazda például a felhasználói csoport tagsága és az alkalmazott állapota alapján frissítheti az alkalmazások hozzáférését.
- A rendszergazda rendszergazdai hitelesítő adatok használatával hozzáférést biztosíthat a sok felhasználó között megosztott alkalmazásokhoz. A rendszergazda például engedélyezheti mindazoknak, akik hozzáférhetnek egy alkalmazáshoz, hogy hozzáférjenek a közösségi média vagy a dokumentum-megosztó alkalmazáshoz.

Amikor a végfelhasználó kezeli a hitelesítő adatokat:

- A felhasználók szükség szerint frissíthetik vagy törölhetik a jelszavukat.
- A rendszergazdák még mindig képesek új hitelesítő adatokat beállítani az alkalmazáshoz.

## <a name="linked-sign-on"></a>Csatolt bejelentkezés
A csatolt bejelentkezés lehetővé teszi, hogy az Azure AD egyszeri bejelentkezést biztosítson egy olyan alkalmazásnak, amely már konfigurálva van az egyszeri bejelentkezéshez egy másik szolgáltatásban. A csatolt alkalmazás az Office 365 portálon vagy az Azure AD MyApps-portálon jelenhet meg a végfelhasználók számára. Például egy felhasználó elindíthat egy olyan alkalmazást, amely az Office 365 portálon Active Directory összevonási szolgáltatások (AD FS) 2,0 (AD FS) egyszeri bejelentkezésre van konfigurálva. További jelentéskészítés is elérhető az Office 365 portálról vagy az Azure AD MyApps-portálról indított társított alkalmazásokhoz. Ha egy alkalmazást a csatolt bejelentkezéshez szeretne konfigurálni, tekintse meg a [csatolt bejelentkezés konfigurálása](configure-linked-sign-on.md)című témakört.

### <a name="linked-sign-on-for-application-migration"></a>Csatolt bejelentkezés az alkalmazás áttelepítéséhez

A csatolt bejelentkezés egységes felhasználói élményt biztosíthat az alkalmazások egy adott időszakban történő áttelepítésekor. Ha Azure Active Directoryre telepíti át az alkalmazásokat, a csatolt bejelentkezés használatával gyorsan közzéteheti az összes áttelepíteni kívánt alkalmazás hivatkozásait.  A felhasználók a [MyApps-portálon](../user-help/my-apps-portal-end-user-access.md) vagy a [Microsoft 365 Application launcherben](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a)is megtalálhatják a hivatkozásokat. A felhasználók nem tudják, hogy egy csatolt alkalmazást vagy egy áttelepített alkalmazást érnek el.  

Miután egy felhasználó hitelesített egy csatolt alkalmazással, létre kell hoznia egy fiókot ahhoz, hogy a végfelhasználó egyszeri bejelentkezéses hozzáférést biztosítson. A fiók rekordjának kiépítési folyamata automatikusan vagy a rendszergazda által manuálisan is megtörténhet.

>[!NOTE]
>Egy csatolt alkalmazásra nem alkalmazhat feltételes hozzáférési szabályzatokat vagy többtényezős hitelesítést. Ennek az az oka, hogy a csatolt alkalmazások nem biztosítanak egyszeri bejelentkezési képességeket az Azure AD-n keresztül. Ha csatolt alkalmazást konfigurál, egyszerűen hozzáadhat egy hivatkozást, amely megjelenik az App Launcher vagy a MyApps portálon. 

## <a name="disabled-sso"></a>Letiltott egyszeri bejelentkezés

A letiltott mód azt jelenti, hogy az alkalmazás nem használja az egyszeri bejelentkezést. Ha az egyszeri bejelentkezés le van tiltva, előfordulhat, hogy a felhasználóknak kétszer kell hitelesíteniük magukat. Először is a felhasználók hitelesítik magukat az Azure AD-ben, majd bejelentkeznek az alkalmazásba.

Letiltott egyszeri bejelentkezés üzemmód használata:

- Ha még nem áll készen az alkalmazás integrálására az Azure AD egyszeri bejelentkezéssel, vagy
- Ha az alkalmazás egyéb aspektusait teszteli, vagy
- Biztonsági rétegként olyan helyszíni alkalmazásokhoz, amelyeknek nincs szükségük a felhasználók hitelesítésére. Letiltva a felhasználónak hitelesítenie kell magát.

Vegye figyelembe, hogy ha konfigurálta az alkalmazást a (z) SP-ben kezdeményezett SAML-alapú egyszeri bejelentkezéshez, és az egyszeri bejelentkezés üzemmódját a Letiltás értékre módosítja, akkor nem állítja le a felhasználókat a MyApps-portálon kívüli alkalmazásba való bejelentkezésre. Ennek eléréséhez [le kell tiltania a felhasználók bejelentkezési képességét](disable-user-sign-in-portal.md)

## <a name="integrated-windows-authentication-iwa-sso"></a>Integrált Windows-hitelesítés (IWA) egyszeri bejelentkezés

Az [alkalmazásproxy](application-proxy.md) lehetővé teszi az egyszeri bejelentkezést (SSO) olyan alkalmazások számára, amelyek [integrált Windows-hitelesítést (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)vagy jogcímbarát alkalmazásokat használnak. Ha az alkalmazás a IWA-t használja, az alkalmazásproxy a Kerberos által korlátozott delegálás (KCD) használatával hitelesíti az alkalmazást az alkalmazásban. Olyan jogcímbarát alkalmazás esetében, amely megbízik a Azure Active Directoryban, az egyszeri bejelentkezés működik, mivel a felhasználó már az Azure AD használatával lett hitelesítve.

Az integrált Windows-hitelesítés egyszeri bejelentkezési módjának kiválasztásával egyszeri bejelentkezést biztosíthat egy helyszíni alkalmazás számára, amely a IWA-vel végzi a hitelesítést.

A helyszíni alkalmazások IWA való konfigurálásával kapcsolatban lásd: [Kerberos által korlátozott delegálás az alkalmazásokba való egyszeri bejelentkezéshez az alkalmazásproxy használatával](application-proxy-configure-single-sign-on-with-kcd.md).

### <a name="how-single-sign-on-with-kcd-works"></a>Az egyszeri bejelentkezés és a KCD működése
Ez a diagram ismerteti a folyamatot, amikor egy felhasználó egy IWA-t használó helyszíni alkalmazáshoz fér hozzá.

![Microsoft Azure AD hitelesítési folyamat diagramja](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. A felhasználó beírja az URL-címet a helyszíni alkalmazás alkalmazás-proxyn keresztüli eléréséhez.
1. Az alkalmazásproxy átirányítja a kérést az Azure AD hitelesítési szolgáltatásaiba az előhitelesítéshez. Ezen a ponton az Azure AD alkalmazza a vonatkozó hitelesítési és engedélyezési házirendeket, például a többtényezős hitelesítést. Ha a felhasználó ellenőrzése megtörtént, az Azure AD létrehoz egy jogkivonatot, és elküldi azt a felhasználónak.
1. A felhasználó átadja a jogkivonatot az alkalmazásproxy számára.
1. Az alkalmazásproxy érvényesíti a jogkivonatot, és lekéri az egyszerű felhasználónevet (UPN) a jogkivonatból. Ezután elküldi a kérést, az UPN-t és az egyszerű szolgáltatásnév (SPN) az összekötőnek egy kettős hitelesítésű biztonságos csatornán keresztül.
1. Az összekötő Kerberos által korlátozott delegálás (KCD) egyeztetést használ a helyszíni AD-vel, megszemélyesítve a felhasználót, hogy Kerberos-tokent kapjon az alkalmazáshoz.
1. Active Directory elküldi az alkalmazás Kerberos-tokenjét az összekötőnek.
1. Az összekötő elküldi az eredeti kérést az alkalmazáskiszolgáló számára az AD-ból kapott Kerberos-jogkivonat használatával.
1. Az alkalmazás elküldi a választ az összekötőnek, amelyet ezután visszaküld az alkalmazásproxy szolgáltatásnak, végül pedig a felhasználónak.

## <a name="header-based-sso"></a>Fejléc-alapú egyszeri bejelentkezés

A fejléc-alapú egyszeri bejelentkezés a HTTP-fejléceket használó alkalmazások esetében működik a hitelesítéshez.

Válassza a fejléc-alapú egyszeri bejelentkezés lehetőséget, ha az alkalmazásproxy konfigurálva van a helyszíni alkalmazáshoz.

További információ a fejléc-alapú hitelesítésről: [fejléc-alapú SSO](application-proxy-configure-single-sign-on-with-headers.md).


## <a name="next-steps"></a>Következő lépések
* [Gyorsindítás sorozat az alkalmazás-kezelésben](view-applications-portal.md)
* [Egyszeri bejelentkezés üzembe helyezésének tervezése](plan-sso-deployment.md)
* [Egyszeri bejelentkezés helyszíni alkalmazásokkal](application-proxy-config-sso-how-to.md)
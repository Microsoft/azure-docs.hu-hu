---
title: Egyszeri bejelentkezési lehetőségek az Azure AD-ban
description: Ismerje meg az egyszeri bejelentkezéshez (SSO) rendelkezésre álló lehetőségeket a Azure Active Directory.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: iangithinji
ms.reviewer: arvindh, japere
ms.openlocfilehash: 202ccf6f540ec78c2bb30e0f0a0173609bba578c
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751453"
---
# <a name="single-sign-on-options-in-azure-ad"></a>Egyszeri bejelentkezési lehetőségek az Azure AD-ban

Az egyszeri bejelentkezés számos előnnyel jár, mint a hagyományos bejelentkezési módszerek.

- **Egyszeri bejelentkezéssel** a felhasználók egyetlen fiókkal jelentkeznek be a tartományhoz csatlakozott eszközök, vállalati erőforrások, szolgáltatott szoftver (SaaS) alkalmazások és webalkalmazások eléréséhez. Bejelentkezés után a felhasználó elindíthat alkalmazásokat az Office 365 portálról vagy a Saját alkalmazások. A rendszergazdák központosíthatják a felhasználói fiókok kezelését, és csoporttagság alapján automatikusan hozzáadhatnak vagy eltávolíthatják az alkalmazások felhasználói hozzáférését.

- **Egyszeri bejelentkezés nélkül a** felhasználóknak meg kell emlékezniük az alkalmazásspecifikus jelszavakra, és be kell jelentkezniük az egyes alkalmazásokba. Az it-csapatnak minden alkalmazáshoz létre kell hoznia és frissítenie kell a felhasználói fiókokat, például a Microsoft 365, a Box és a Salesforce alkalmazáshoz. A felhasználóknak meg kell emlékezniük a jelszavukra, és időt kell tölteniük az egyes alkalmazásokba való bejelentkezésre.

További információ az egyszeri bejelentkezésről: Mi az az egyszeri [bejelentkezés?](what-is-single-sign-on.md).

## <a name="choosing-a-single-sign-on-method"></a>Egyszeri bejelentkezési módszer kiválasztása

Az alkalmazásokat többféleképpen is konfigurálhatja egyszeri bejelentkezéshez. Az egyszeri bejelentkezési módszer kiválasztása attól függ, hogy az alkalmazás hogyan van konfigurálva a hitelesítéshez.

- A felhőalkalmazások OpenID Connect, OAuth, SAML, jelszóalapú, csatolt vagy letiltott módszereket használhatnak az egyszeri bejelentkezéshez. 
- A helyszíni alkalmazások jelszóalapú, integrált Windows-hitelesítés, fejlécalapú, csatolt vagy letiltott módszereket használhatnak az egyszeri bejelentkezéshez. A helyszíni választási lehetőségek akkor működnek, ha az alkalmazások konfigurálva vannak alkalmazásproxy.

Ez a folyamatábra segít eldönteni, melyik egyszeri bejelentkezési módszer a legmegfelelőbb az Ön helyzetére.

![Döntési folyamatábra egyszeri bejelentkezési metódushoz](./media/what-is-single-sign-on/choose-single-sign-on-method-040419.png)

Az alábbi táblázat összefoglalja az egyszeri bejelentkezési módszereket, és további részletekre mutató hivatkozásokat tartalmaz.

| Egyszeri bejelentkezési módszer | Alkalmazástípusok | A következő esetekben használja |
| :------ | :------- | :----- |
| [OpenID Connect and OAuth](#openid-connect-and-oauth) | felhő és helyszíni | Új OpenID Connect fejlesztésekor használja a OpenID Connect és az OAuth-hitelesítést. Ez a protokoll leegyszerűsíti az alkalmazáskonfigurációt, könnyen használható SZOFTVERDK-ekkel rendelkezik, és lehetővé teszi az alkalmazás számára az MS Graph használatát.
| [SAML](#saml-sso) | felhő és helyszíni | Ha lehetséges, válassza az SAML lehetőséget olyan meglévő alkalmazásokhoz, amelyek nem OpenID Connect OAuth-hitelesítést. Az SAML olyan alkalmazásokhoz használható, amelyek az SAML-protokollok egyikének használatával hitelesítik magukat.|
| [Jelszóalapú](#password-based-sso) | felhő és helyszíni | Válassza a jelszóalapú lehetőséget, ha az alkalmazás felhasználónévvel és jelszóval hitelesíti magát. A jelszóalapú egyszeri bejelentkezés biztonságos alkalmazásjelszó-tárolást és visszajátszást tesz lehetővé webböngészőbővítmény vagy mobilalkalmazás használatával. Ez a módszer az alkalmazás által biztosított meglévő bejelentkezési folyamatot használja, de lehetővé teszi, hogy a rendszergazda kezelje a jelszavakat. |
| [Kapcsolódó](#linked-sign-on) | felhő és helyszíni | Akkor válassza a csatolt bejelentkezést, ha az alkalmazás egyszeri bejelentkezésre van konfigurálva egy másik identitásszolgáltató szolgáltatásban. Ez a lehetőség nem ad egyszeri bejelentkezést az alkalmazáshoz. Előfordulhat azonban, hogy az alkalmazáshoz már van egyszeri bejelentkezés egy másik szolgáltatás, például a Active Directory összevonási szolgáltatások (AD FS).|
| [Disabled](#disabled-sso) | felhő és helyszíni | Válassza a letiltott egyszeri bejelentkezést, ha az alkalmazás nem áll készen az egyszeri bejelentkezésre való konfigurálásra. Ez az alapértelmezett mód az alkalmazás létrehozásakor.|
| [integrált Windows-hitelesítés (IWA)](#integrated-windows-authentication-iwa-sso) | csak helyszíni | Válassza az IWA egyszeri bejelentkezést olyan alkalmazásokhoz, amelyek integrált Windows-hitelesítés [(IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)vagy jogcímeket használhatja. IWA esetén a alkalmazásproxy összekötők Kerberos által korlátozott delegálással (KCD) hitelesítik a felhasználókat az alkalmazásban. |
| [Fejlécalapú](#header-based-sso) | csak helyszíni | Fejlécalapú egyszeri bejelentkezést akkor használjon, ha az alkalmazás fejléceket használ a hitelesítéshez. alkalmazásproxy Azure AD használatával hitelesíti a felhasználót, majd továbbítja a forgalmat az összekötő szolgáltatáson keresztül.  |

## <a name="openid-connect-and-oauth"></a>OpenID Connect and OAuth

Új alkalmazások fejlesztésekor használjon olyan modern protokollokat, mint a OpenID Connect és az OAuth, hogy a legjobb egyszeri bejelentkezési élményt ér el az alkalmazáshoz több eszközplatformon. Az OAuth lehetővé teszi, hogy a felhasználók vagy a rendszergazdák [jóváhagyást](configure-user-consent.md) adjanak a védett erőforrásokhoz, például a [Microsoft Graph.](/graph/overview) Az alkalmazáshoz könnyen használható [AZDK-okat](../develop/reference-v2-libraries.md) biztosítjuk, és az alkalmazás készen áll a [Microsoft Graph.](/graph/overview)

További információkért lásd:

- [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)
- [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)
- [A Microsoft identitásplatform fejlesztői útmutatója.](../develop/index.yml)

## <a name="saml-sso"></a>SAML SSO

Az **SAML egyszeri bejelentkezéssel** az Azure AD a felhasználó Azure AD-fiókjával hitelesíti az alkalmazást. Az Azure AD egy kapcsolati protokollon keresztül kommunikálja az alkalmazással a bejelentkezési adatokat. Az SAML-alapú egyszeri bejelentkezéssel a felhasználókat az SAML-jogcímekben meghatározott szabályok alapján adott alkalmazás-szerepkörökhöz lehet leképezni.

Válassza az SAML-alapú egyszeri bejelentkezést, ha az alkalmazás támogatja.

Az SAML-alapú egyszeri bejelentkezés az alábbi protokollok bármelyikét használják:

- SAML 2.0
- WS-Federation

SaaS-alkalmazás SAML-alapú egyszeri bejelentkezéshez való konfigurálásához lásd: [SAML-alapú egyszeri bejelentkezés konfigurálása.](configure-saml-single-sign-on.md) Emellett számos SaaS-alkalmazás alkalmazásspecifikus oktatóanyagot tartalmaz, amely végigveszi Az SAML-alapú egyszeri bejelentkezés konfigurációjának lépései. [](../saas-apps/tutorial-list.md)

Ha WS-Federationre konfigurál egy alkalmazást, kövesse ugyanezt az útmutatót az SAML-alapú egyszeri bejelentkezéshez való konfiguráláshoz. Az alkalmazás Azure AD használatára való konfigurálásának lépésében le kell cserélnie az Azure AD bejelentkezési URL-címét a WS-Federation `https://login.microsoftonline.com/<tenant-ID>/wsfed` végpontra.

Helyszíni alkalmazás SAML-alapú egyszeri bejelentkezéshez való konfigurálásával lásd: [SAML single-sign-on for on-premises](application-proxy-configure-single-sign-on-on-premises-apps.md)applications with alkalmazásproxy .

További információ az SAML protokollról: Egyszeri [bejelentkezéses SAML protokoll.](../develop/single-sign-on-saml-protocol.md)

## <a name="password-based-sso"></a>Jelszóalapú SSO

Jelszóalapú bejelentkezéssel a felhasználók az első hozzáférésükkor felhasználónévvel és jelszóval jelentkeznek be az alkalmazásba. Az első bejelentkezés után az Azure AD adja meg a felhasználónevet és a jelszót az alkalmazásnak.

A jelszóalapú egyszeri bejelentkezés az alkalmazás által biztosított meglévő hitelesítési folyamatot használja. Ha engedélyezi az egyszeri jelszavas bejelentkezést egy alkalmazáshoz, az Azure AD összegyűjti és biztonságosan tárolja az alkalmazás felhasználónevét és jelszavát. A felhasználói hitelesítő adatokat a rendszer titkosított állapotban tárolja a címtárban.

Emellett a rendszergazda engedélyezheti az Azure AD feltételes hozzáférési szabályzatokat vagy a többtényezős hitelesítést a jelszóalapú SSO-hoz.

Jelszóalapú egyszeri bejelentkezés kiválasztása a következőkor:

- Az alkalmazások nem támogatják az SAML egyszeri bejelentkezési protokollt.
- Az alkalmazások a hozzáférési jogkivonatok és fejlécek helyett felhasználónévvel és jelszóval hitelesítik magukat.

A jelszóalapú egyszeri bejelentkezés minden olyan felhőalapú alkalmazáshoz támogatott, amely HTML-alapú bejelentkezési oldalval rendelkezik. A felhasználó a következő böngészők bármelyikét használhatja:

- Internet Explorer 11-es verzió Windows 7 vagy újabb rendszeren
   > [!NOTE]
   > Internet Explorer támogatás korlátozott, és már nem kap új szoftverfrissítéseket. Microsoft Edge az ajánlott böngésző.

- Microsoft Edge évfordulós Windows 10 vagy újabb kiadáson
- Microsoft Edge iOS és Android rendszerhez
- Intune Managed Browser
- Chrome Windows 7 vagy újabb, macOS X vagy újabb rendszeren
- Firefox 26.0 vagy újabb windows XP SP2 vagy újabb, illetve macOS X 10.6 vagy újabb rendszeren

A felhőalkalmazások jelszóalapú egyszeri bejelentkezéshez való konfigurálásával lásd: [Configure password single sign-on](configure-password-single-sign-on-non-gallery-applications.md)(Jelszó egyszeri bejelentkezésének konfigurálása).

Helyszíni alkalmazás egyszeri bejelentkezéshez való konfigurálás alkalmazásproxy: Jelszótartó az egyszeri bejelentkezéshez alkalmazásproxy [](application-proxy-configure-single-sign-on-password-vaulting.md)

### <a name="how-authentication-works-for-password-based-sso"></a>A hitelesítés működése jelszóalapú SSO-val

Egy felhasználó alkalmazásbeli hitelesítéséhez az Azure AD lekéri a felhasználó hitelesítő adatait a címtárból, és beírja őket az alkalmazás bejelentkezési oldalára.  Az Azure AD biztonságosan továbbítja a felhasználói hitelesítő adatokat egy webböngészőbővítményen vagy mobilalkalmazáson keresztül. Ez a folyamat lehetővé teszi, hogy a rendszergazda kezelje a felhasználói hitelesítő adatokat, és nem követeli meg, hogy a felhasználóknak meg kell jegyezni a jelszavukat.

> [!IMPORTANT]
> A hitelesítő adatokat a rendszer az automatikus bejelentkezési folyamat során eltolja a felhasználó elől. A hitelesítő adatok azonban a webes hibakeresési eszközökkel deríthetők fel. A felhasználóknak és rendszergazdáknak ugyanúgy kell követnie a biztonsági szabályzatokat, mintha a hitelesítő adatokat közvetlenül a felhasználó adta volna meg.

### <a name="managing-credentials-for-password-based-sso"></a>A jelszóalapú SSO hitelesítő adatainak kezelése

Az egyes alkalmazásokhoz a jelszavakat az Azure AD-rendszergazda vagy a felhasználók is használhatjak.

Amikor az Azure AD-rendszergazda kezeli a hitelesítő adatokat:  

- A felhasználónak nem kell alaphelyzetbe állítania vagy megjegyeznie a felhasználónevet és a jelszót. A felhasználó úgy férhet hozzá az alkalmazáshoz, hogy a saját Saját alkalmazások egy megadott hivatkozáson keresztül kattint rá.
- A rendszergazda felügyeleti feladatokat is el tud elvégezni a hitelesítő adatokon. A rendszergazda például a felhasználói csoport tagsága és az alkalmazotti állapot alapján frissítheti az alkalmazás-hozzáférést.
- A rendszergazda rendszergazdai hitelesítő adatokkal biztosíthatja a hozzáférést a számos felhasználó között megosztott alkalmazásokhoz. A rendszergazda például engedélyezheti mindenki számára, aki hozzáfér egy alkalmazáshoz, hogy hozzáférjen egy közösségi médiához vagy dokumentummegosztási alkalmazáshoz.

Amikor a végfelhasználó kezeli a hitelesítő adatokat:

- A felhasználók a jelszavukat szükség szerint frissítve vagy törölve kezelhetik.
- A rendszergazdák továbbra is beállíthatják az alkalmazás új hitelesítő adatait.

## <a name="linked-sign-on"></a>Csatolt bejelentkezés
A csatolt bejelentkezés lehetővé teszi, hogy az Azure AD egyszeri bejelentkezést biztosítson egy olyan alkalmazáshoz, amely már konfigurálva van egyszeri bejelentkezésre egy másik szolgáltatásban. A csatolt alkalmazás megjelenhet a végfelhasználók számára az Office 365 portálon vagy az Azure AD MyApps portálon. Egy felhasználó például elindíthat egy olyan alkalmazást, amely egyszeri bejelentkezésre van konfigurálva Active Directory összevonási szolgáltatások (AD FS) 2.0-s (AD FS) az Office 365 portálról. Az Office 365 portálról vagy az Azure AD MyApps portálról indított csatolt alkalmazásokhoz további jelentéskészítés is elérhető. Az alkalmazások csatolt bejelentkezéshez való konfigurálásával kapcsolatban lásd: [Csatolt bejelentkezés konfigurálása.](configure-linked-sign-on.md)

### <a name="linked-sign-on-for-application-migration"></a>Összekapcsolt bejelentkezés az alkalmazás migrálásához

A csatolt bejelentkezés konzisztens felhasználói élményt nyújthat az alkalmazások egy adott időszakon keresztüli áttelepítése során. Ha alkalmazásokat minál a Azure Active Directory, csatolt bejelentkezés használatával gyorsan közzétehet hivatkozásokat az összes átemelni kívánt alkalmazásra.  A felhasználók az összes hivatkozást megtalálják a [MyApps portálon](../user-help/my-apps-portal-end-user-access.md) vagy a Microsoft 365 [alkalmazásindítójában.](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a) A felhasználók nem fogják tudni, hogy egy csatolt vagy egy migrált alkalmazáshoz férnek hozzá.  

Miután a felhasználó hitelesített egy csatolt alkalmazással, létre kell hozva egy fiókrekordot, mielőtt a végfelhasználó egyszeri bejelentkezési hozzáférést biztosít. A fiókrekord kiépítése történhet automatikusan, vagy egy rendszergazda manuálisan.

>[!NOTE]
>Csatolt alkalmazásra nem alkalmazhat feltételes hozzáférési szabályzatokat vagy többtényezős hitelesítést. Ennek az az oka, hogy a csatolt alkalmazások nem biztosítanak egyszeri bejelentkezési képességeket az Azure AD-on keresztül. Csatolt alkalmazás konfigurálásakor egyszerűen hozzáad egy hivatkozást, amely megjelenik az alkalmazásindítóban vagy a MyApps portálon. 

## <a name="disabled-sso"></a>Letiltott SSO

A Letiltva mód azt jelenti, hogy az alkalmazás nem használ egyszeri bejelentkezést. Ha az egyszeri bejelentkezés le van tiltva, előfordulhat, hogy a felhasználóknak kétszer kell hitelesítenie magukat. Először a felhasználók hitelesítik magukat az Azure AD-ban, majd bejelentkeznek az alkalmazásba.

Letiltott egyszeri bejelentkezési mód használata:

- Ha még nem áll készen az alkalmazás Azure AD egyszeri bejelentkezéssel való integrálására, vagy
- Ha az alkalmazás más aspektusait teszteli, vagy
- Egy olyan helyszíni alkalmazás biztonsági rétegeként, amely nem követeli meg a felhasználók hitelesítését. A letiltás után a felhasználónak hitelesítenie kell magát.

Vegye figyelembe, hogy ha konfigurálta az alkalmazást az SP által kezdeményezett SAML-alapú egyszeri bejelentkezéshez, és az egyszeri bejelentkezési módot letiltja, az nem fogja megakadályozni, hogy a felhasználók a MyApps portálon kívülről jelentkezzenek be az alkalmazásba. Ehhez le kell tiltania a felhasználók [bejelentkezésének képességét](disable-user-sign-in-portal.md)

## <a name="integrated-windows-authentication-iwa-sso"></a>integrált Windows-hitelesítés (IWA) SSO

[alkalmazásproxy](application-proxy.md) egyszeri bejelentkezést (SSO) biztosít a integrált Windows-hitelesítés [(IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)vagy jogcímeket támó alkalmazásokhoz. Ha az alkalmazás IWA-t alkalmazásproxy kerberos által korlátozott delegálással (KCD) hitelesít az alkalmazásban. A megbízható jogcímeket felhasználóbarát Azure Active Directory egyszeri bejelentkezés azért működik, mert a felhasználó hitelesítése már az Azure AD használatával történt.

Válassza integrált Windows-hitelesítés egyszeri bejelentkezési módot, hogy egyszeri bejelentkezést biztosítson egy helyszíni alkalmazásba, amely IWA-hitelesítést biztosít.

Helyszíni alkalmazás IWA-hoz való konfigurálásával lásd: [Kerberos által](application-proxy-configure-single-sign-on-with-kcd.md)korlátozott delegálás az alkalmazásokba való egyszeri bejelentkezéshez a alkalmazásproxy.

### <a name="how-single-sign-on-with-kcd-works"></a>Az egyszeri bejelentkezés működése a KCD-val
Ez a diagram azt a folyamatot mutatja be, amikor egy felhasználó egy IWA-t használó helyszíni alkalmazáshoz fér hozzá.

![Microsoft Azure AD folyamatábra](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. A felhasználó beírja az URL-címet a helyszíni alkalmazás eléréséhez a alkalmazásproxy.
1. alkalmazásproxy átirányítja a kérést az Azure AD hitelesítési szolgáltatásaihoz az előhitelesítéshez. Ezen a ponton az Azure AD minden alkalmazható hitelesítési és engedélyezési szabályzatot alkalmaz, például a többtényezős hitelesítést. Ha a felhasználó érvényesítve van, az Azure AD létrehoz egy jogkivonatot, és elküldi azt a felhasználónak.
1. A felhasználó átadja a jogkivonatot a alkalmazásproxy.
1. alkalmazásproxy érvényesíti a jogkivonatot, és lekéri az egyszerű felhasználónevet (UPN) a jogkivonatból. Ezután egy kettősen hitelesített biztonságos csatornán keresztül elküldi a kérést, az egyszerű felhasználónevet és az egyszerű szolgáltatásnevet (SPN) az összekötőnek.
1. Az összekötő Kerberos által korlátozott delegálás (KCD) egyeztetést használ a helyszíni AD-val, megszemélyesítődve a felhasználót, hogy kerberos-jogkivonatot szerezze be az alkalmazáshoz.
1. Active Directory elküldi az alkalmazás Kerberos-jogkivonatát az összekötőnek.
1. Az összekötő elküldi az eredeti kérést az alkalmazáskiszolgálónak az AD-től kapott Kerberos-jogkivonattal.
1. Az alkalmazás elküldi a választ az összekötőnek, amelyet visszaad a alkalmazásproxy szolgáltatásnak, és végül a felhasználónak.

## <a name="header-based-sso"></a>Fejlécalapú SSO

A fejlécalapú egyszeri bejelentkezés olyan alkalmazások esetében működik, amelyek HTTP-fejléceket használnak a hitelesítéshez.

Akkor válassza a fejlécalapú egyszeri bejelentkezést, alkalmazásproxy konfigurálva van a helyszíni alkalmazáshoz.

A fejlécalapú hitelesítéssel kapcsolatos további információkért lásd: [Fejlécalapú SSO.](application-proxy-configure-single-sign-on-with-headers.md)


## <a name="next-steps"></a>Következő lépések
* [Gyorsútmutató-sorozat az alkalmazáskezelésről](view-applications-portal.md)
* [Egyszeri bejelentkezés üzembe helyezésének tervezése](plan-sso-deployment.md)
* [Egyszeri bejelentkezés helyszíni alkalmazásokkal](application-proxy-config-sso-how-to.md)

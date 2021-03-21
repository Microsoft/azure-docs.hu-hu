---
title: Azure AD biztonságos hibrid hozzáférés F5 VPN-nel | Microsoft Docs
description: Oktatóanyag a Azure Active Directory egyszeri bejelentkezéses (SSO) integrációhoz az F5 BIG-IP jelszó nélküli VPN-hez
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e177f1ce55d803f54bb2553078441557e5c191
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98730887"
---
# <a name="tutorial-for-azure-active-directory-single-sign-on-integration-with-f5-big-ip-for-password-less-vpn"></a>Oktatóanyag Azure Active Directory egyszeri bejelentkezéses integrációhoz az F5 BIG-IP jelszó nélküli VPN-hez

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a F5's BIG-IP-alapú Secure Socket Layer virtual private Network (SSL-VPN) megoldást a Azure Active Directory (AD) használatával a biztonságos hibrid hozzáféréshez (SHA).

A BIG IP SSL-VPN és az Azure AD integrálása [számos kulcsfontosságú előnyt](f5-aad-integration.md)biztosít, többek között:

- Javított zéró megbízhatósági szabályozás az [Azure ad előhitelesítése és engedélyezése](../../app-service/overview-authentication-authorization.md) révén

- [Jelszó nélküli hitelesítés a VPN-szolgáltatáshoz](https://www.microsoft.com/security/business/identity/passwordless)

- Identitások és hozzáférések kezelése egyetlen vezérlési síkon – a [Azure Portal](https://portal.azure.com/#home)

A nagyszerű értékek ellenére a klasszikus VPN azonban továbbra is a hálózati peremhálózat fogalmát veszi alapul, ahol a megbízható a belső és a nem megbízható kívül van. Ez a modell már nem hatékony a valódi zéró megbízhatósági állapot elérésében, mivel a vállalati eszközök már nem korlátozódnak a vállalati adatközpontok falaira, hanem több Felhőbeli környezetek esetében, rögzített határok nélkül. Ezért azt javasoljuk ügyfeleinknek, hogy az [alkalmazáson alapuló hozzáférés](../fundamentals/five-steps-to-full-application-integration-with-azure-ad.md)kezeléséhez több személyazonossági vezérelt megközelítést alkalmazzanak.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az esetben az SSL-VPN szolgáltatás BIG-IP APM-példánya SAML-szolgáltatóként (SP) lesz konfigurálva, és az Azure AD lesz a megbízható SAML-IDENTITÁSSZOLGÁLTATÓ, amely előhitelesítést biztosít. Ezt követően az Azure AD egyszeri bejelentkezést (SSO) biztosít a BIG-IP APM-re vonatkozó jogcímbarát hitelesítéssel, zökkenőmentes VPN-hozzáférési élményt biztosítva.

![A képen az SSL-VPN architektúrája látható](media/f5-sso-vpn/ssl-vpn-architecture.png)

>[!NOTE]
>Az útmutatóban hivatkozott összes példa sztringet vagy értéket le kell cserélni a tényleges környezetével.

## <a name="prerequisites"></a>Előfeltételek

A korábbi tapasztalatok vagy az F5 BIG-IP ismerete nem szükséges, azonban a következőkre lesz szüksége:

- Azure AD [ingyenes előfizetés](https://azure.microsoft.com/trial/get-started-active-directory/) vagy újabb verzió

- A felhasználói identitásokat [a helyszíni címtárból](../hybrid/how-to-connect-sync-whatis.md) az Azure ad-be kell szinkronizálni.

- Egy fiók az Azure AD-alkalmazás rendszergazdai [engedélyeivel](../roles/permissions-reference.md#application-administrator)

- Meglévő BIG-IP-infrastruktúra, amely a BIG-IP-re irányuló és onnan érkező ügyfelek forgalmát irányítja át, vagy [egy Big-IP virtuális kiadást helyez üzembe az Azure](f5-bigip-deployment-guide.md)-ban.

- A BIG-IP közzétett VPN szolgáltatáshoz tartozó rekordnak léteznie kell a nyilvános DNS-ben, vagy egy teszt ügyfél localhost fájljában a tesztelés során.

- A BIG-IP-címet a szükséges SSL-tanúsítványokkal kell kiépíteni a HTTPS protokollon keresztüli közzétételi szolgáltatásokhoz.

Az [F5 Big-IP-terminológia](https://www.f5.com/services/resources/glossary) megismerése az oktatóanyagban hivatkozott különböző összetevők megismeréséhez is segítséget nyújt.

>[!NOTE]
>Az Azure folyamatosan fejlődik, ezért ne lepődj meg, ha a jelen útmutatóban szereplő utasítások és a Azure Portalban látható információk között bármilyen árnyalatot talál. A Képernyőképek a BIG-IP v15 származnak, azonban viszonylag hasonlóak maradnak a v 13.1-től.

## <a name="add-f5-big-ip-from-the-azure-ad-gallery"></a>Az F5 BIG-IP hozzáadása az Azure AD-katalógusból

Az SAML-összevonási megbízhatóság beállítása a BIG-IP protokoll lehetővé teszi, hogy az Azure AD BIG-IP az előhitelesítést és a [feltételes hozzáférést](../conditional-access/overview.md) az Azure ad-hez adja, mielőtt hozzáférést adna a közzétett VPN-szolgáltatáshoz.

1. Jelentkezzen be az Azure AD-portálra az alkalmazás-rendszergazdai jogosultságokkal rendelkező fiók használatával

2. A bal oldali navigációs panelen válassza ki a **Azure Active Directory szolgáltatást**

3. Nyissa meg a **vállalati alkalmazásokat** , és a felső menüszalagon válassza az **új alkalmazás** lehetőséget.

4. Keresse meg az F5 billentyűt a katalógusban, és válassza az **F5 Big-IP APM Azure ad-integráció** lehetőséget.

5. Adja meg az alkalmazás nevét, majd a **Hozzáadás/létrehozás** gombra kattintva adja hozzá a bérlőhöz. A felhasználó a nevet ikonként láthatja az Azure-ban és az Office 365 alkalmazás-portálon. A névnek tükröznie kell az adott szolgáltatást. Például: VPN.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

1. Az új F5-alkalmazás tulajdonságai nézetben válassza az   >  **egyszeri bejelentkezés** kezelése lehetőséget.

2. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget. A nem gombra kattintva ugorja át a kérést az egyszeri bejelentkezési beállítások mentéséhez **, majd később mentse azt**.

3. Az **egyszeri bejelentkezés az SAML-vel** menüben válassza az **ALAPszintű SAML-konfigurációhoz** tartozó toll ikont a következő részletek megadásához:

   - Cserélje le az előre definiált **azonosító URL-címet** a Big-IP közzétett szolgáltatás URL-címére. Például: `https://ssl-vpn.contoso.com`

   - Tegye ugyanezt a **Válasz URL-címének** szövegmezővel, beleértve az SAML-végpont elérési útját. Például: `https://ssl-vpn.contoso.com/saml/sp/profile/post/acs`

   - Ebben a konfigurációban az alkalmazás IDENTITÁSSZOLGÁLTATÓ kezdeményezett módban működik, ahol az Azure AD az SAML-kijelentéssel rendelkező felhasználót a BIG-IP SAML szolgáltatásba való átirányításhoz adja. A IDENTITÁSSZOLGÁLTATÓ által kezdeményezett üzemmódot nem támogató alkalmazások esetében a BIG-IP SAML szolgáltatás **bejelentkezési URL-címét** kell megadnia. Például: `https://ssl-vpn.contoso.com`.

   - A kijelentkezési URL-cím mezőben adja meg a közzétett szolgáltatás gyobb mértékben a BIG-IP APM egyszeri kijelentkezés (SLO) végpontját. Például: `https://ssl-vpn.contoso.com/saml/sp/profile/redirect/slr`

   A SLO URL-cím megadása biztosítja, hogy a felhasználói munkamenet mindkét végén, a BIG-IP és az Azure AD-ban leálljon, miután a felhasználó kijelentkezik. A BIG-IP APM emellett [lehetőséget](https://support.f5.com/csp/article/K12056) biztosít az összes munkamenet leállítására egy adott alkalmazás URL-címének meghívásakor.

![A képen az alapszintű SAML-konfiguráció látható](media/f5-sso-vpn/basic-saml-configuration.png).

>[!NOTE]
>A TMOS v16 az SAML SLO-végpont/SAML/SP/Profile/redirect/slo értékre változott

4. Válassza a **Mentés** lehetőséget a SAML-konfiguráció menüjéből való kilépés előtt, és hagyja ki az egyszeri bejelentkezéses tesztelési kérést.

Figyelje meg a **felhasználói attribútumok & a jogcímek** szakasz tulajdonságait, mivel az Azure ad ezeket a felhasználókat a Big-IP APM-hitelesítéshez adja ki.

![A rendszerkép a felhasználói attribútumok jogcímeit jeleníti meg](media/f5-sso-vpn/user-attributes-claims.png)

Nyugodtan hozzáadhat bármilyen más, a BIG-IP-címmel közzétett szolgáltatáshoz kapcsolódó jogcímet, ugyanakkor megállapíthatja, hogy az alapértelmezett készleten kívül definiált jogcímek csak akkor lesznek kiadva, ha az Azure AD-ben szerepelnek a feltöltött attribútumok. Ugyanígy a címtárbeli [szerepköröknek vagy](../hybrid/how-to-connect-fed-group-claims.md) a csoporttagságok is meg kell határozni az Azure ad felhasználói objektumait, mielőtt jogcímeket kellene kiadniuk.

![Ábrán az összevonási metaadatok letöltési hivatkozása látható](media/f5-sso-vpn/saml-signing-certificate.png)

Az Azure AD által létrehozott SAML aláíró tanúsítványok élettartama három év, ezért az Azure AD által közzétett útmutatással kell kezelnie.

### <a name="azure-ad-authorization"></a>Azure AD-hitelesítés

Alapértelmezés szerint az Azure AD csak olyan jogkivonatokat ad ki a felhasználóknak, akik hozzáférést kaptak a szolgáltatáshoz.

1. Továbbra is az alkalmazás konfigurációs nézetében válassza a **felhasználók és csoportok** lehetőséget.

2. Válassza a **+ felhasználó hozzáadása** lehetőséget, majd a hozzárendelés hozzáadása menüben válassza a **felhasználók és csoportok** lehetőséget.

3. A **felhasználók és csoportok** párbeszédpanelen adja hozzá azokat a felhasználói csoportokat, amelyek jogosultak a VPN elérésére, majd **válassza** a  >  **hozzárendelés** lehetőséget.

![A képen megjelenik a felhasználói hivatkozás hozzáadása ](media/f5-sso-vpn/add-user-link.png)

4. Ezzel befejezte az SAML-összevonási megbízhatóság Azure AD-részét. A BIG-IP APM mostantól beállítható úgy, hogy közzéteszi az SSL-VPN szolgáltatást, és a megbízható tulajdonságok készletével konfigurálja a megbízhatóságot az SAML előhitelesítéshez.

## <a name="big-ip-apm-configuration"></a>BIG-IP APM-konfiguráció

### <a name="saml-federation"></a>SAML-összevonás

A következő szakasz létrehozza a BIG-IP SAML-szolgáltatót és a hozzá tartozó SAML-IDENTITÁSSZOLGÁLTATÓ objektumokat, amelyek szükségesek a VPN-szolgáltatás Azure AD-vel való egyesítő befejezéséhez.

1. Nyissa meg a **hozzáférés**  >  **összevonási**  >  **SAML-szolgáltató**  >  **helyi SP Services** elemet, és válassza a **Létrehozás** lehetőséget.

![A képen a BIG-IP SAML-konfiguráció látható](media/f5-sso-vpn/bigip-saml-configuration.png)

2. Adja meg a korábban az Azure AD-ben definiált **nevet** és az **entitás azonosítójának** értékét, valamint az alkalmazáshoz való KAPCSOLÓDÁSHOZ használt gazdagép teljes tartománynevét.

![A képen az új SAML SP szolgáltatás létrehozása látható](media/f5-sso-vpn/create-new-saml-sp.png)

   Az SP- **név** beállításai csak akkor szükségesek, ha az entitás azonosítója nem pontosan egyezik a közzétett URL-cím állomásnév részével, vagy ha nem az állomásnév-alapú URL-formátuma. Adja meg a közzétenni kívánt alkalmazás külső sémáját és állomásnevét, ha az entitás azonosítója `urn:ssl-vpn:contosoonline` .

3. Görgessen le az új **SAML SP-objektum** kiválasztásához, majd válassza a **bind/unbind identitásszolgáltató-összekötők** lehetőséget.

![A rendszerkép a helyi SP szolgáltatással való összevonás létrehozását mutatja be](media/f5-sso-vpn/federation-local-sp-service.png)

4. Válassza az **új identitásszolgáltató-összekötő létrehozása** lehetőséget, és a legördülő menüből válassza ki a **metaadatok** elemet.

![A képen az új IDENTITÁSSZOLGÁLTATÓ-összekötő létrehozása látható](media/f5-sso-vpn/create-new-idp-connector.png)

5. Keresse meg a korábban letöltött összevonási metaadatokat tartalmazó XML-fájlt, és adja meg annak az APM-objektumnak az **identitás-szolgáltatójának nevét** , amely a külső SAML-identitásszolgáltató képviseli.

6. Válassza az új **sor hozzáadása** lehetőséget az új Azure ad külső identitásszolgáltató-összekötő kiválasztásához.

![A képen külső IDENTITÁSSZOLGÁLTATÓ-összekötő látható](media/f5-sso-vpn/external-idp-connector.png)

7. Válassza a **frissítés** lehetőséget az SAML SP-objektum SAML identitásszolgáltató objektumhoz való kötéséhez, majd kattintson **az OK gombra**.

![A képen az SAML-IDENTITÁSSZOLGÁLTATÓ látható az SP használatával](media/f5-sso-vpn/saml-idp-using-sp.png)

### <a name="webtop-configuration"></a>WebTop-konfiguráció

A következő lépésekkel engedélyezheti az SSL-VPN használatát a felhasználóknak a BIG-IP tulajdonos webportálon keresztül.

1. **Nyissa meg a**  >  **webtops**  >  **WebTop-listát** , és válassza a **Létrehozás** lehetőséget.

2. Adja meg a portál nevét, és állítsa be a típust **teljes** értékre. Például: `Contoso_webtop`.

3. Állítsa be a hátralévő beállításokat, majd válassza a **kész** lehetőséget.

![A képen a WebTop-konfiguráció látható](media/f5-sso-vpn/webtop-configuration.png)

### <a name="vpn-configuration"></a>VPN-konfiguráció

A VPN-képesség több elemből áll, melyek mindegyike a teljes szolgáltatás különböző aspektusait vezérli.

1. Nyissa meg a **hozzáférési**  >  **kapcsolatok/VPN**  >  **hálózati hozzáférés (VPN)**  >  **IPv4-címbérletek készleteket** , és válassza a **Létrehozás** lehetőséget.

2. Adja meg a VPN-ügyfelek számára lefoglalt IP-címek készletének nevét. Például Contoso_vpn_pool

3. Állítsa a típust **IP-címtartomány** értékre, és adjon meg egy kezdő és záró IP-címet, majd **adja hozzá a Hozzáadás** **és a Befejezés értéket**.

![A rendszerkép megjeleníti a VPN-konfigurációt](media/f5-sso-vpn/vpn-configuration.png)

A hálózati hozzáférési lista a VPN-készletből, a felhasználói útválasztási engedélyekkel és a szükséges alkalmazások indításával kiépíti a szolgáltatást az IP-és DNS-beállításokkal.

1. Lépjen a **hozzáférési**  >  **kapcsolat/VPN: hálózati hozzáférés (VPN)**  >  **hálózati hozzáférési listára** , és válassza a **Létrehozás** lehetőséget.

2. Adja meg a VPN-hozzáférési lista és a felirat nevét (például: contoso-VPN), majd fejezze be a **munkát**.

![A rendszerkép megjeleníti a VPN-konfigurációt a hálózati hozzáférés listában](media/f5-sso-vpn/vpn-configuration-network-access-list.png)

3. A felső menüszalagon válassza a **hálózati beállítások** lehetőséget, és adja hozzá az alábbi beállításokat:

   • **Támogatott IP-verzió**: IPv4

   • **IPv4-címbérleti készlet**: válassza ki a korábban létrehozott VPN-készletet, például Contoso_vpn_pool

![Képen a contoso VPN-készlet látható](media/f5-sso-vpn/contoso-vpn-pool.png)

   Az ügyfélbeállítások beállításával kikényszerítheti, hogy az ügyfél-forgalom hogyan legyen átirányítva VPN-kapcsolaton keresztül.

4. Válassza a **kész** lehetőséget, és a beállítások hozzáadásához lépjen a DNS/gazdagépek lapra:

   • **IPv4 elsődleges névkiszolgáló**: a környezet DNS-kiszolgálójának IP-címe

   • **DNS alapértelmezett tartomány utótagja**: az adott VPN-kapcsolathoz tartozó tartomány utótagja. Például: contoso.com

![A rendszerkép az alapértelmezett tartományi utótagot jeleníti meg](media/f5-sso-vpn/domain-suffix.png)

Az [F5 cikk](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-network-access-11-5-0/2.html) részletesen ismerteti a többi beállítás beállítását a beállításoknak megfelelően.

A VPN-ügyfelek által támogatott VPN-ügyfélbeállítások beállításainak konfigurálásához most egy BIG-IP kapcsolati profilt kell beállítani. Például: Windows, OSX és Android.

1. Lépjen a **hozzáférési**  >  **kapcsolat/VPN-**  >  **kapcsolati**  >  **profilok** elemre, és válassza a **Hozzáadás** lehetőséget.

2. Adja meg a profil nevét, és állítsa be a szülő profilt **/Common/connectivity**(például Contoso_VPN_Profile).

![A rendszerkép megjeleníti az új kapcsolati profil létrehozása](media/f5-sso-vpn/create-connectivity-profile.png)

Az F5's [dokumentációja](https://techdocs.f5.com/kb/en-us/bigip-edge-apps.html) további részleteket tartalmaz az ügyfelek támogatásáról.

## <a name="access-profile-configuration"></a>Hozzáférési profil konfigurációja

Ha a VPN-objektumok konfigurálva vannak, a szolgáltatás SAML-hitelesítéshez való engedélyezéséhez hozzáférési házirend szükséges.

1. Nyissa meg a **hozzáférési**  >  **profilok/házirendek**  >  **hozzáférési profilok (munkamenetenkénti házirendek)** elemet, és válassza a **Létrehozás** lehetőséget.

2. Adja meg a profil nevét, és a profil típusa beállításnál válassza az **összes** lehetőséget, például Contoso_network_access

3. Görgessen le, ha legalább egy nyelvet hozzá szeretne adni az **elfogadott nyelvek** listájához, és válassza a **kész** lehetőséget.

![A képen általános tulajdonságok láthatók](media/f5-sso-vpn/general-properties.png)

4. Válassza a **Szerkesztés** lehetőséget az új hozzáférési profil Per-Session házirend mezőjében, hogy a vizualizáció-szerkesztő elindítson egy külön böngésző lapon.

![A képen egy munkamenet-szabályzat látható](media/f5-sso-vpn/per-session-policy.png)

5. Válassza ki a **+** jelet, és az előugró ablakban válassza a hitelesítési SAML- **hitelesítés**  >    >  **Hozzáadás elemet**.

6. Az SAML-hitelesítés SP konfigurációjában válassza ki a korábban létrehozott VPN SAML SP-objektumot, majd a **Mentés** lehetőséget.

![A képen SAML-hitelesítés látható](media/f5-sso-vpn/saml-authentication.png)

7. Válassza ki az **+** SAML-hitelesítés sikeres ágát.

8. A hozzárendelés lapon válassza a **speciális erőforrás hozzárendelése** lehetőséget, majd az **elem hozzáadása elemet** .

![A rendszerkép megjeleníti az előzetes erőforrás-hozzárendelést](media/f5-sso-vpn/advance-resource-assign.png)

9. Az előugró ablakban válassza az **új bejegyzés** , majd a **Hozzáadás/törlés** lehetőséget.

10. A gyermek ablakban válassza a **hálózati hozzáférés** lehetőséget, majd válassza ki a korábban létrehozott hálózati hozzáférési profilt.

![Új hálózati hozzáférési bejegyzés hozzáadásának képe](media/f5-sso-vpn/add-new-entry.png)

11. Váltson a **WebTop** lapra, és adja hozzá a korábban létrehozott WebTop objektumot.

![A képen a WebTop objektum hozzáadása látható](media/f5-sso-vpn/add-webtop-object.png)

12. Válassza a **frissítés** , majd a **Mentés** lehetőséget.

13. A felső megtagadási mezőben kattintson a hivatkozásra, hogy megváltoztassa a sikeres ágat, majd **mentse a Mentés** **lehetőséget** .

![A kép az új vizuális házirend-szerkesztőt jeleníti meg](media/f5-sso-vpn/vizual-policy-editor.png)

14. Véglegesítse ezeket a beállításokat úgy, hogy kiválasztja a **hozzáférési házirend alkalmazása** elemet, és bejelöli a Visual Policy Editor fület.

![A rendszerkép az új Access Policy Managert jeleníti meg](media/f5-sso-vpn/access-policy-manager.png)

## <a name="publish-the-vpn-service"></a>A VPN-szolgáltatás közzététele

Az összes beállítással az APM mostantól megköveteli, hogy egy előtér-virtuális kiszolgáló figyelje a VPN-hez csatlakozó ügyfeleket.

1. Válassza ki a **helyi forgalom**  >  **virtuális kiszolgálók**  >  **virtuális kiszolgálóinak listáját** , és válassza a **Létrehozás** lehetőséget.

2. Adja meg a VPN virtuális kiszolgáló **nevét** , például **VPN_Listener**.

3. Adja meg a virtuális kiszolgálót olyan nem használt **IP-címmel** , amely az ügyfél forgalmának fogadására szolgáló útválasztással rendelkezik

4. Állítsa be a szolgáltatási portot a **443 HTTPS** értékre, és győződjön meg arról, hogy az állapot **engedélyezve van**

![A rendszerkép új virtuális kiszolgálót jelenít meg](media/f5-sso-vpn/new-virtual-server.png)

5. Állítsa a **http-profilt** http-re, és adja hozzá az SSL-profilt (ügyfél) Az előfeltételként kiépített nyilvános SSL-tanúsítványhoz.

![A képen az SSL-profil látható](media/f5-sso-vpn/ssl-profile.png)

6. A hozzáférési házirend területen állítsa be a **hozzáférési profilt** és a **kapcsolati profilt** a létrehozott VPN-objektumok használatára.

![A rendszerkép megjeleníti a hozzáférési házirendet](media/f5-sso-vpn/access-policy.png)

7. Ha elkészült, válassza a **kész** lehetőséget.

8.  Az SSL-VPN szolgáltatás mostantól közvetlenül az URL-címen vagy a Microsoft alkalmazás-portálján keresztül érhető el és elérhető az SHA használatával.

## <a name="additional-resources"></a>További források

- [A jelszavak vége, jelszóval elmentve](https://www.microsoft.com/security/business/identity/passwordless)

- [Mit jelent a feltételes hozzáférés?](../conditional-access/overview.md)

- [A Microsoft Zero megbízhatósági keretrendszere a távoli munka engedélyezéséhez](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)

- [Öt lépés az Azure AD-vel való teljes alkalmazás-integrációhoz](../fundamentals/five-steps-to-full-application-integration-with-azure-ad.md)

## <a name="next-steps"></a>Következő lépések

Nyisson meg egy böngészőt egy távoli Windows-ügyfélen, és keresse meg a **Big-IP VPN szolgáltatás** URL-címét. Az Azure AD-ben végzett hitelesítés után a BIG-IP WebTop portál és a VPN-indító jelenik meg.

![A képen a VPN-indító látható](media/f5-sso-vpn/vpn-launcher.png)

A VPN-csempe kiválasztásával telepíti a BIG-IP Edge-ügyfelet, és létre kell hoznia az SHA használatára konfigurált VPN-kapcsolatot.
Az F5 VPN-alkalmazásnak is láthatónak kell lennie az Azure AD feltételes hozzáférésben célként megadott erőforrásként. Tekintse meg a feltételes hozzáférési szabályzatok létrehozásával és a felhasználók Azure AD [jelszó nélküli hitelesítéssel](https://www.microsoft.com/security/business/identity/passwordless)történő engedélyezésével kapcsolatos [útmutatást](../conditional-access/concept-conditional-access-policies.md) .
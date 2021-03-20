---
title: Kerberos-alapú egyszeri bejelentkezés (SSO) a Azure Active Directory alkalmazás-proxyval
description: Ismerteti, hogyan lehet egyszeri bejelentkezést biztosítani a Azure Active Directory Application Proxy használatával.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 08/13/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperf-fy21q2
ms.openlocfilehash: a4fdd8d16854e76cdf20d27a6048694c01de8499
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99253883"
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-sso-to-your-apps-with-application-proxy"></a>Kerberos által korlátozott delegálás egyszeri bejelentkezéshez (SSO) az alkalmazásokhoz az Application proxy használatával

Az alkalmazás-proxyn keresztül közzétett helyszíni alkalmazásokhoz egyszeri bejelentkezést is biztosíthat, amely integrált Windows-hitelesítéssel védett. Ezeknek az alkalmazásoknak Kerberos-jegyre van szükségük a hozzáféréshez. Az alkalmazásproxy a Kerberos által korlátozott delegálást (KCD) használja az alkalmazások támogatásához. 

Az alkalmazásokhoz integrált Windows-hitelesítés (IWA) használatával engedélyezheti az egyszeri bejelentkezést az alkalmazásokhoz, ha az alkalmazásproxy-összekötők számára engedélyezi Active Directory a felhasználók megszemélyesítését. Az összekötők ezt az engedélyt használják a jogkivonatok küldésére és fogadására a nevükben.

## <a name="how-single-sign-on-with-kcd-works"></a>Az egyszeri bejelentkezés és a KCD működése
Ez a diagram ismerteti a folyamatot, amikor egy felhasználó egy IWA-t használó helyszíni alkalmazáshoz próbál hozzáférni.

![Microsoft HRE-hitelesítés folyamatábrája](./media/application-proxy-configure-single-sign-on-with-kcd/authdiagram.png)

1. A felhasználó beírja az URL-címet a helyszíni alkalmazás alkalmazás-proxyn keresztüli eléréséhez.
2. Az alkalmazásproxy átirányítja a kérést az Azure AD hitelesítési szolgáltatásaiba az előhitelesítéshez. Ezen a ponton az Azure AD alkalmazza a vonatkozó hitelesítési és engedélyezési házirendeket, például a többtényezős hitelesítést. Ha a felhasználó ellenőrzése megtörtént, az Azure AD létrehoz egy jogkivonatot, és elküldi azt a felhasználónak.
3. A felhasználó átadja a jogkivonatot az alkalmazásproxy számára.
4. Az alkalmazásproxy érvényesíti a jogkivonatot, és lekérdezi az egyszerű felhasználónevet (UPN), majd az összekötő lekéri az egyszerű felhasználónevet és az egyszerű szolgáltatásnevet (SPN) egy kettős hitelesítésű biztonságos csatornán keresztül.
5. Az összekötő Kerberos által korlátozott delegálás (KCD) egyeztetést végez a helyszíni AD-vel, megszemélyesítve a felhasználót, hogy Kerberos-jogkivonatot kapjon az alkalmazáshoz.
6. Active Directory elküldi az alkalmazás Kerberos-tokenjét az összekötőnek.
7. Az összekötő elküldi az eredeti kérést az alkalmazáskiszolgáló számára az AD-ból kapott Kerberos-jogkivonat használatával.
8. Az alkalmazás elküldi a választ az összekötőnek, amelyet ezután visszaküld az alkalmazásproxy szolgáltatásnak, végül pedig a felhasználónak.

## <a name="prerequisites"></a>Előfeltételek
Mielőtt megkezdené az egyszeri bejelentkezést a IWA-alkalmazásokhoz, győződjön meg arról, hogy a környezet készen áll a következő beállításokkal és konfigurációkkal:

* Az alkalmazások, például a SharePoint Web Apps, integrált Windows-hitelesítés használatára vannak beállítva. További információkért lásd: a [Kerberos-hitelesítés támogatásának engedélyezése](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd759186(v=ws.11))vagy a sharepointhoz – lásd: a [Kerberos-hitelesítés tervezése a SharePoint 2013-ben](/SharePoint/security-for-sharepoint-server/kerberos-authentication-planning).
* Minden alkalmazás rendelkezik [egyszerű szolgáltatásnév nevével](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx).
* Az összekötőt futtató kiszolgáló és az alkalmazást futtató kiszolgáló tartományhoz van csatlakoztatva, és ugyanahhoz a tartományhoz vagy megbízó tartományhoz tartozik. További információ a tartományhoz való csatlakozásról: [számítógép csatlakoztatása tartományhoz](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dd807102(v=ws.11)).
* Az összekötőt futtató kiszolgálónak hozzáférése van a felhasználók TokenGroupsGlobalAndUniversal attribútumához attribútumának olvasásához. Lehetséges, hogy ezt az alapértelmezett beállítást befolyásolta a környezet biztonságának megerősítése.

### <a name="configure-active-directory"></a>Az Active Directory konfigurálása
A Active Directory konfiguráció attól függően változik, hogy az alkalmazásproxy-összekötő és az alkalmazáskiszolgáló ugyanabban a tartományban van-e, vagy sem.

#### <a name="connector-and-application-server-in-the-same-domain"></a>Összekötő és alkalmazáskiszolgáló ugyanabban a tartományban
1. A Active Directory területen válassza az **eszközök**  >  **felhasználók és számítógépek** lehetőséget.
2. Válassza ki az összekötőt futtató kiszolgálót.
3. Kattintson a jobb gombbal, majd válassza a **Tulajdonságok**  >  **delegálás** lehetőséget.
4. Válassza **A számítógépen csak a megadott szolgáltatások delegálhatók** lehetőséget. 
5. Válassza a **Bármely hitelesítési protokoll** lehetőséget.
6. Azon **szolgáltatások alatt, amelyekben ez a fiók delegált hitelesítő adatokat tud bemutatni** , adja hozzá az alkalmazáskiszolgáló SPN-identitásának értékét. Ez lehetővé teszi, hogy az alkalmazásproxy-összekötő megszemélyesítse a felhasználókat az AD-ben a listában meghatározott alkalmazásokban.

   ![Összekötő – PÉLDÁNYAI Tulajdonságok ablak képernyőkép](./media/application-proxy-configure-single-sign-on-with-kcd/properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>Összekötő és alkalmazáskiszolgáló különböző tartományokban
1. A tartományok közötti KCD-használat előfeltételeinek listáját a [Kerberos által korlátozott delegálás tartományok között](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831477(v=ws.11))című témakörben tekintheti meg.
2. A `principalsallowedtodelegateto` webalkalmazáshoz tartozó szolgáltatásfiók (számítógép vagy dedikált tartományi felhasználói fiók) tulajdonságával engedélyezze a Kerberos-hitelesítés delegálását az alkalmazásproxy (összekötő) használatával. Az alkalmazáskiszolgáló a `webserviceaccount` és a delegált kiszolgáló környezetében fut `connectorcomputeraccount` . Futtassa az alábbi parancsokat egy tartományvezérlőn (Windows Server 2012 R2 vagy újabb rendszert futtatva) a (z) tartományában `webserviceaccount` . Mindkét fiókhoz használjon egyszerű neveket (nem UPN).

   Ha a `webserviceaccount` számítógép-fiók, használja a következő parancsokat:

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADComputer -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADComputer webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

   Ha a `webserviceaccount` felhasználói fiók, használja a következő parancsokat:

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADUser -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADUser webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

## <a name="configure-single-sign-on"></a>Egyszeri bejelentkezés konfigurálása 
1. Tegye közzé az alkalmazást az Application [proxyval történő alkalmazások közzététele](application-proxy-add-on-premises-application.md)című részben leírt utasítások szerint. Győződjön meg arról, hogy a **Azure Active Directory** az **előhitelesítési módszerként** van kiválasztva.
2. Miután az alkalmazás megjelenik a vállalati alkalmazások listájában, jelölje ki, majd kattintson az **egyszeri bejelentkezés** elemre.
3. Az egyszeri bejelentkezési mód beállítása az **integrált Windows-hitelesítésre**.  
4. Adja meg az alkalmazáskiszolgáló **belső alkalmazásspecifikus egyszerű szolgáltatásnevet** . Ebben a példában a közzétett alkalmazás SPN-je a http/www. contoso. com. Ennek az SPN-nek szerepelnie kell azon szolgáltatások listáján, amelyekhez az összekötő delegált hitelesítő adatokat tud bemutatni. 
5. Válassza ki az összekötő **Delegált bejelentkezési identitását** a felhasználók nevében történő használathoz. További információ: [a különböző helyszíni és Felhőbeli identitások használata](#working-with-different-on-premises-and-cloud-identities)

   ![Speciális alkalmazás konfigurációja](./media/application-proxy-configure-single-sign-on-with-kcd/cwap_auth2.png)  

## <a name="sso-for-non-windows-apps"></a>SSO nem Windows-alkalmazásokhoz

Az Azure AD Application Proxy Kerberos-delegálási folyamata akkor indul el, amikor az Azure AD hitelesíti a felhasználót a felhőben. Ha a kérés a helyszínen érkezik, az Azure AD Application Proxy-összekötő egy Kerberos-jegyet bocsát ki a felhasználó nevében a helyi Active Directory való interakcióval. Ezt a folyamatot a Kerberos által korlátozott delegálásnak (KCD) nevezzük. 

A következő szakaszban egy kérést küldünk a háttérbeli alkalmazásnak ezzel a Kerberos-jegytel. 

Több mechanizmus is van, amelyek meghatározzák a Kerberos-jegy küldésének módját az ilyen kérésekben. A legtöbb nem Windows-kiszolgáló várhatóan SPNEGO-token formájában fog megjelenni. Ezt a mechanizmust az Azure AD Application Proxy támogatja, de alapértelmezés szerint le van tiltva. Egy összekötő konfigurálható SPNEGO vagy standard Kerberos-tokenhez, de mindkettőt nem.

Ha SPNEGO-összekötőt állít be, ügyeljen arra, hogy az összekötő csoport összes többi összekötője is a SPNEGO legyen konfigurálva. A standard Kerberos-tokent várt alkalmazásokat más összekötők segítségével kell átirányítani, amelyek nincsenek konfigurálva a SPNEGO. Egyes webalkalmazások mindkét formátumot elfogadják anélkül, hogy módosítani kellene a konfigurációt. 
 

A SPNEGO engedélyezése:

1. Nyisson meg egy rendszergazdaként futtatott parancssort.
2. A parancssorból futtassa a következő parancsokat a SPNEGO igénylő összekötő-kiszolgálókon.

    ```
    REG ADD "HKLM\SOFTWARE\Microsoft\Microsoft AAD App Proxy Connector" /v UseSpnegoAuthentication /t REG_DWORD /d 1
    net stop WAPCSvc & net start WAPCSvc
    ```

A nem Windows rendszerű alkalmazások általában felhasználói felhasználóneveket vagy SAM-fiókokat használnak tartománybeli e-mail címek helyett. Ha ez a helyzet az alkalmazásokra vonatkozik, konfigurálnia kell a meghatalmazott bejelentkezési azonosító mezőt a Felhőbeli identitások alkalmazás-identitásokhoz való összekapcsolásához. 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>Különböző helyszíni és Felhőbeli identitások használata
Az alkalmazásproxy feltételezi, hogy a felhasználók pontosan ugyanazzal az identitással rendelkeznek a felhőben és a helyszínen. Bizonyos környezetekben azonban a vállalati házirendek vagy az alkalmazás függőségei miatt előfordulhat, hogy a szervezeteknek alternatív azonosítókat kell használniuk a bejelentkezéshez. Ilyen esetekben továbbra is használhatja a KCD az egyszeri bejelentkezéshez. Konfiguráljon egy **meghatalmazott bejelentkezési azonosítót** az egyes alkalmazásokhoz, hogy megadják, melyik identitást kell használni az egyszeri bejelentkezés végrehajtásakor.  

Ez a funkció lehetővé teszi számos olyan szervezet számára, amely különböző helyszíni és Felhőbeli identitásokkal rendelkezik a felhőből a helyszíni alkalmazásokba való BEJELENTKEZÉShez anélkül, hogy a felhasználóknak eltérő felhasználóneveket és jelszavakat kellene megadniuk. Ide tartoznak azok a szervezetek, amelyek:

* Több tartomány belső ( joe@us.contoso.com , joe@eu.contoso.com ) és egyetlen tartománya van a felhőben ( joe@contoso.com ).
* A nem átirányítható tartománynevet ( joe@contoso.usa ) és a felhőben egy jogi felállást is tartalmaz.
* Ne használjon belső tartományneveket (Joe)
* Használjon különböző aliasokat a helyszínen és a felhőben. Például: joe-johns@contoso.com vs. joej@contoso.com  

Az alkalmazásproxy használatával kiválaszthatja, hogy melyik identitást kell használni a Kerberos-jegy beszerzéséhez. Ez a beállítás egy alkalmazás. Néhány ilyen lehetőség olyan rendszerekhez alkalmas, amelyek nem fogadják el az e-mail-címek formátumát, mások pedig alternatív bejelentkezéshez lettek kialakítva.

![A delegált bejelentkezési azonosító paraméterének képernyőképe](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_upn.png)

Ha a rendszer delegált bejelentkezési azonosítót használ, előfordulhat, hogy az érték nem egyedi a szervezet összes tartományában vagy erdője között. Ezt a problémát úgy is elkerülheti, ha két különböző összekötő-csoport használatával kétszer teszi közzé ezeket az alkalmazásokat. Mivel minden alkalmazás eltérő felhasználói célközönséggel rendelkezik, csatlakoztathatja az összekötőit egy másik tartományhoz.

Ha a bejelentkezési identitáshoz a helyszíni **SAM-fiók neve** van használatban, az összekötőt futtató számítógépet hozzá kell adni ahhoz a tartományhoz, amelyben a felhasználói fiók található.

### <a name="configure-sso-for-different-identities"></a>SSO konfigurálása különböző identitásokhoz
1. Konfigurálja Azure AD Connect beállításokat, hogy a fő identitás legyen az e-mail-cím (e-mail). Ez a testreszabási folyamat részeként a szinkronizálási beállítások **egyszerű felhasználónév** mezőjének módosításával történik. Ezek a beállítások határozzák meg azt is, hogy a felhasználók hogyan jelentkeznek be a Office 365, a Windows10-eszközökbe és az Azure AD-t használó egyéb alkalmazásokba.  
   ![Felhasználók azonosítása – képernyőfelvétel – egyszerű felhasználónév legördülő lista](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. A módosítandó alkalmazás konfigurációs beállításainál válassza ki a használni kívánt **delegált bejelentkezési azonosítót** :

   * Egyszerű felhasználónév (például joe@contoso.com )
   * Másodlagos egyszerű felhasználónév (például joed@contoso.local )
   * Az egyszerű felhasználónév része (például: Joe)
   * Felhasználónév része a másodlagos egyszerű felhasználónévnek (például joed)
   * Helyszíni SAM-fiók neve (a tartományvezérlő konfigurációjától függ)

### <a name="troubleshooting-sso-for-different-identities"></a>Egyszeri bejelentkezés hibaelhárítása különböző identitások esetén
Ha az egyszeri bejelentkezés folyamata során hiba lép fel, az az összekötő számítógép eseménynaplójában jelenik meg, a [Hibaelhárítás](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)során.
Bizonyos esetekben azonban a rendszer sikeresen elküldte a kérést a háttérbeli alkalmazásnak, miközben az alkalmazás különböző más HTTP-válaszokban válaszol. Ezeknek az eseteknek a hibaelhárításához a 24029-as számú eseményt kell megvizsgálni az alkalmazásproxy munkamenet-eseménynaplójában. A delegáláshoz használt felhasználói identitás megjelenik a "felhasználó" mezőben az esemény részletei között. A munkamenet-napló bekapcsolásához válassza az **elemzési és hibakeresési naplók megjelenítése** lehetőséget az Eseménynapló Nézet menüjében.

## <a name="next-steps"></a>Következő lépések

* [Alkalmazásproxy-alkalmazás konfigurálása a Kerberos által korlátozott delegálás használatára](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Az alkalmazásproxyval kapcsolatos problémák hibaelhárítása](application-proxy-troubleshoot.md)
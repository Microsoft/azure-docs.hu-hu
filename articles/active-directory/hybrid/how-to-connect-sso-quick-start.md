---
title: 'Azure AD Connect: zökkenőmentes egyetlen Sign-On – gyors útmutató | Microsoft Docs'
description: Ez a cikk azt ismerteti, hogyan kezdheti el az Azure Active Directory zökkenőmentes egyetlen Sign-On
services: active-directory
keywords: Mi az Azure AD Connect, az Azure AD, az egyszeri bejelentkezéshez szükséges összetevők telepítése Active Directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c320c8497506a0269e65cd79e18aea1972354729
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589522"
---
# <a name="azure-active-directory-seamless-single-sign-on-quickstart"></a>Azure Active Directory zökkenőmentes egyszeri bejelentkezés: gyors üzembe helyezés

## <a name="deploy-seamless-single-sign-on"></a>Zökkenőmentes egyszeri Sign-On üzembe helyezése

Azure Active Directory (Azure AD) zökkenőmentes, egyetlen Sign-On (zökkenőmentes SSO) automatikusan bejelentkezik a felhasználók számára, amikor a vállalati hálózathoz csatlakozó vállalati asztali számítógépeken vannak. A zökkenőmentes SSO lehetővé teszi, hogy a felhasználók könnyen hozzáférjenek a felhőalapú alkalmazásokhoz anélkül, hogy további helyszíni összetevőket kellene megadniuk.

A zökkenőmentes egyszeri bejelentkezés telepítéséhez kövesse az alábbi lépéseket.

## <a name="step-1-check-the-prerequisites"></a>1. lépés: az Előfeltételek ellenőrzése

Győződjön meg arról, hogy a következő előfeltételek vannak érvényben:

* **A Azure ad Connect-kiszolgáló beállítása**: Ha a bejelentkezési módszere [átmenő hitelesítést](how-to-connect-pta.md) használ, nincs szükség további előfeltételek ellenőrzésére. Ha a bejelentkezési módszerként [jelszó-kivonatolási szinkronizálást](how-to-connect-password-hash-synchronization.md) használ, és ha tűzfal van Azure ad Connect és az Azure ad között, ügyeljen a következőkre:
   - A Azure AD Connect 1.1.644.0 vagy újabb verzióját használja. 
   - Ha a tűzfal vagy a proxy lehetővé teszi, vegye fel a kapcsolatot az engedélyezett listához a **\* . Msappproxy.net** URL-címeken a 443-es porton keresztül. Ha egy adott URL-címet igényel, nem pedig helyettesítő karaktert a proxy konfigurálásához, konfigurálhatja a **tenantid.Registration.msappproxy.net**, ahol a tenantid a szolgáltatást konfiguráló bérlő GUID azonosítója. Ha a szervezetben nem lehetséges az URL-alapú proxy-kivételek használata, ehelyett engedélyezheti a hozzáférést az [Azure DATACENTER IP-tartományokhoz](https://www.microsoft.com/download/details.aspx?id=41653), amelyek hetente frissülnek. Ez az előfeltétel csak akkor alkalmazható, ha engedélyezi a szolgáltatást. A tényleges felhasználói bejelentkezésekhez nem szükséges.

    >[!NOTE]
    >A 1.1.557.0, a 1.1.558.0, a 1.1.561.0 és a 1.1.614.0 verziók a jelszó-kivonat szinkronizálásával kapcsolatos problémával rendelkeznek. Azure AD Connect Ha _nem_ kívánja használni a jelszó-kivonatolási szinkronizálást az átmenő hitelesítéssel együtt, olvassa el a [Azure ad Connect kibocsátási megjegyzéseit](./reference-connect-version-history.md) további információért.
    
    >[!NOTE]
    >Ha kimenő HTTP-proxyval rendelkezik, győződjön meg róla, hogy ez az URL-cím (autologon.microsoftazuread-sso.com) engedélyezve van az engedélyezési listán. Explicit módon meg kell adnia ezt az URL-címet, mert a helyettesítő karakterek nem fogadhatók el. 

* **Támogatott Azure ad Connect topológia használata**: Ügyeljen arra, hogy az [itt](plan-connect-topologies.md)leírt Azure ad Connect által támogatott topológiák egyikét használja.

    >[!NOTE]
    >A zökkenőmentes egyszeri bejelentkezés több AD-erdőt is támogat, függetlenül attól, hogy vannak-e közöttük AD-megbízhatósági kapcsolatok.

* **Tartományi rendszergazdai hitelesítő adatok beállítása**: minden Active Directory erdőhöz tartományi rendszergazdai hitelesítő adatokkal kell rendelkeznie, amely:
    * Azure AD Connect keresztül szinkronizál az Azure AD-vel.
    * Azokat a felhasználókat tartalmazza, akik számára engedélyezni szeretné a zökkenőmentes egyszeri bejelentkezést.
    
* **Modern hitelesítés engedélyezése**: a funkció működéséhez engedélyeznie kell a [modern hitelesítést](/office365/enterprise/modern-auth-for-office-2013-and-2016) a bérlőn.

* **Microsoft 365-ügyfelek legújabb verzióinak használata**: Ha csendes bejelentkezési élményt szeretne kapni Microsoft 365-ügyfelekkel (Outlook, Word, Excel és mások), a felhasználóknak a 16.0.8730. xxxx vagy újabb verziót kell használniuk.

## <a name="step-2-enable-the-feature"></a>2. lépés: a funkció engedélyezése

Zökkenőmentes egyszeri bejelentkezés engedélyezése [Azure ad Connecton](whatis-hybrid-identity.md)keresztül.

>[!NOTE]
> Ha Azure AD Connect nem felel meg a követelményeknek, a [PowerShell használatával is engedélyezheti a zökkenőmentes egyszeri bejelentkezést](tshoot-connect-sso.md#manual-reset-of-the-feature) . Akkor használja ezt a beállítást, ha Active Directory erdőben több tartománya van, és szeretné jobban megcélozni azt a tartományt, amelyen engedélyezni szeretné a zökkenőmentes SSO-t.

Ha Azure AD Connect friss telepítését végzi, válassza az [egyéni telepítési útvonalat](how-to-connect-install-custom.md). A **felhasználó bejelentkezési** oldalán jelölje be az **egyszeri bejelentkezés engedélyezése** lehetőséget.

>[!NOTE]
> A beállítás csak akkor érhető el, ha a bejelentkezési módszer a **jelszó kivonatának szinkronizálása** vagy a **továbbított hitelesítés**.

![Azure AD Connect: felhasználói bejelentkezés](./media/how-to-connect-sso-quick-start/sso8.png)

Ha már rendelkezik Azure AD Connect-telepítéssel, válassza a **felhasználó bejelentkezési** oldalának módosítása Azure ad Connect, majd a **tovább** lehetőséget. Ha Azure AD Connect 1.1.880.0 vagy újabb verziót használ, alapértelmezés szerint az **egyszeri bejelentkezés engedélyezése** lehetőség lesz kiválasztva. Ha a Azure AD Connect régebbi verzióit használja, jelölje be az **egyszeri bejelentkezés engedélyezése** lehetőséget.

![Azure AD Connect: a felhasználói bejelentkezés módosítása](./media/how-to-connect-sso-quick-start/changeusersignin.png)

Folytassa a varázslót egészen addig, amíg az **egyszeri bejelentkezés engedélyezése** lapra nem kerül. Adja meg a tartományi rendszergazdai hitelesítő adatokat minden olyan Active Directory erdőben, amely:

* Azure AD Connect keresztül szinkronizál az Azure AD-vel.
* Azokat a felhasználókat tartalmazza, akik számára engedélyezni szeretné a zökkenőmentes egyszeri bejelentkezést.

A varázsló befejezése után a zökkenőmentes egyszeri bejelentkezés engedélyezve van a bérlőn.

>[!NOTE]
> A tartományi rendszergazdai hitelesítő adatokat a rendszer nem tárolja Azure AD Connect vagy az Azure AD-ben. Ezeket csak a funkció engedélyezésére használják.

Kövesse ezeket az utasításokat annak ellenőrzéséhez, hogy megfelelően engedélyezte-e a zökkenőmentes egyszeri bejelentkezést:

1. Jelentkezzen be a [Azure Active Directory felügyeleti központba](https://aad.portal.azure.com) a bérlő globális rendszergazdai hitelesítő adataival.
2. A bal oldali ablaktáblán válassza a **Azure Active Directory** lehetőséget.
3. Válassza a **Azure ad Connect** lehetőséget.
4. Győződjön meg arról, hogy a **zökkenőmentes egyszeri bejelentkezés** funkció **engedélyezve van**.

![Azure Portal: Azure AD Connect panel](./media/how-to-connect-sso-quick-start/sso10.png)

>[!IMPORTANT]
> A zökkenőmentes egyszeri bejelentkezés minden egyes AD-erdőben létrehoz egy nevű számítógépfiókot a helyszíni `AZUREADSSOACC` Active Directoryban (ad). `AZUREADSSOACC`Biztonsági okokból szigorúan védeni kell a számítógépfiókot. Csak a Tartománygazdák kezelhetik a számítógépfiókot. Győződjön meg arról, hogy a számítógépfiók Kerberos-delegálása le van tiltva, és a Active Directory egyetlen más fiókja sincs delegálási engedélyekkel a `AZUREADSSOACC` számítógépfiók számára. Tárolja a számítógépfiókot a szervezeti egységben (OU), ahol biztonságos a véletlen törléstől, és csak a Tartománygazdák férhetnek hozzá.

>[!NOTE]
> Ha a pass-The-hash és a hitelesítőadat-lopás mérséklési architektúrát használja a helyszíni környezetben, végezze el a megfelelő módosításokat annak biztosításához, hogy a `AZUREADSSOACC` számítógépfiók ne kerüljön be a karanténba helyezett tárolóba. 

## <a name="step-3-roll-out-the-feature"></a>3. lépés: a funkció bevezetése

A zökkenőmentes SSO-t fokozatosan kiderítheti a felhasználók számára az alábbi utasítások alapján. Első lépésként adja hozzá a következő Azure AD URL-címet az összes vagy a kiválasztott felhasználó intranetes zónájának beállításaihoz a Active Directory Csoportházirend használatával:

- `https://autologon.microsoftazuread-sso.com`

Emellett engedélyeznie kell egy intranetes zóna házirend-beállítását, amelynek neve a **frissítés engedélyezése az állapotsoron parancsfájlon** keresztül csoportházirend. 

>[!NOTE]
> Az alábbi utasítások csak az Internet Explorer, a Microsoft Edge és a Google Chrome használata esetén működnek Windows rendszeren (ha a megbízható hely URL-címeit megosztják az Internet Explorerben). A következő szakaszban megtudhatja, hogyan állíthatja be a Mozilla Firefoxot és a Google Chrome-ot macOS rendszeren.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Miért kell módosítania a felhasználók intranetes zónájának beállításait?

Alapértelmezés szerint a böngésző automatikusan kiszámítja a megfelelő zónát (Internet vagy intranet) egy adott URL-címről. Például az `http://contoso/` intranet zónához, míg az `http://intranet.contoso.com/` Internet zónához való leképezés (mivel az URL-cím egy pontot tartalmaz). A böngészők nem küldenek Kerberos-jegyeket a Felhőbeli végpontoknak (például az Azure AD URL-címére), kivéve, ha explicit módon hozzáadja az URL-címet a böngésző intranetes zónájához.

A felhasználók intranetes zónájának beállításai kétféleképpen módosíthatók:

| Beállítás | Rendszergazdai megfontolás | Felhasználó felület |
| --- | --- | --- |
| Csoportházirend | A rendszergazda lezárja az intranetes zóna beállításainak szerkesztését | A felhasználók nem módosíthatják a saját beállításait |
| Csoportházirend-beállítások |  A rendszergazda engedélyezi az intranetes zóna beállításainak szerkesztését | A felhasználók módosíthatják a saját beállításait |

### <a name="group-policy-option---detailed-steps"></a>"Csoportházirend" lehetőség – részletes lépések

1. Nyissa meg az Csoportházirend-felügyeleti szerkesztő eszközt.
2. Szerkessze az egyes felhasználókra alkalmazott csoportházirendet. Ez a példa az **alapértelmezett tartományi házirendet** használja.
3. Tallózással keresse meg a **felhasználói konfigurációs**  >  **házirendeket**  >  **Felügyeleti sablonok**  >  **Windows-összetevők**  >  **Internet Explorer**  >  **Internet Control Panel**  >  **Biztonság lapján**. Ezután válassza **a hely – zóna hozzárendelési lista** lehetőséget.
    ![Képernyőfelvétel: a "biztonsági lap" a "helyről a zónákhoz való hozzárendelés" listára kiválasztva.](./media/how-to-connect-sso-quick-start/sso6.png)
4. Engedélyezze a szabályzatot, majd adja meg a következő értékeket a párbeszédpanelen:
   - **Érték neve**: a Kerberos-jegyek továbbítására szolgáló Azure ad URL-cím.
   - **Érték** (adathalmaz): **1** az intranet zónát jelzi.

     Az eredmény a következőhöz hasonló:

     Érték neve: `https://autologon.microsoftazuread-sso.com`
  
     Érték (adathalmaz): 1

   >[!NOTE]
   > Ha szeretné letiltani egyes felhasználók számára a zökkenőmentes egyszeri bejelentkezést (például ha ezek a felhasználók megosztott kioszkokra jelentkeznek be), állítsa a fenti értékeket **4** értékre. Ez a művelet hozzáadja az Azure AD URL-címét a korlátozott zónához, és minden alkalommal sikertelen lesz a zökkenőmentes egyszeri bejelentkezés.
   >

5. Válassza az **OK**, majd újra az **OK** elemet.

    ![Képernyőfelvétel: a "tartalmak megjelenítése" ablak, amelyen a zóna-hozzárendelés van kiválasztva.](./media/how-to-connect-sso-quick-start/sso7.png)

6. Tallózással keresse meg a **felhasználói konfigurációs**  >  **házirendeket**  >  **Felügyeleti sablonok**  >  **Windows-összetevők**  >  **Internet Explorer**  >  **Internet Vezérlőpult**  >  **biztonsági oldalának**  >  **intranet zónájában**. Ezután válassza **a frissítések engedélyezése állapotsoron parancsfájl használatával** lehetőséget.

    ![Képernyőkép: az "intranet zóna" oldal, amelyen a "frissítések engedélyezése az állapotsoron a szkripten keresztül" lehetőség van kiválasztva.](./media/how-to-connect-sso-quick-start/sso11.png)

7. Engedélyezze a házirend-beállítást, majd kattintson **az OK gombra**.

    ![Képernyőfelvétel: "a frissítések engedélyezése az állapotsoron parancsfájl használatával" ablak, amelyen engedélyezve van a házirend-beállítás.](./media/how-to-connect-sso-quick-start/sso12.png)

### <a name="group-policy-preference-option---detailed-steps"></a>"Csoportházirend-beállítások" beállítás – részletes lépések

1. Nyissa meg az Csoportházirend-felügyeleti szerkesztő eszközt.
2. Szerkessze az egyes felhasználókra alkalmazott csoportházirendet. Ez a példa az **alapértelmezett tartományi házirendet** használja.
3. Tallózással keresse meg a **felhasználói konfiguráció**  >  **beállításait** a  >  **Windows beállításai**  >  **beállításjegyzék**  >  **új**  >  **beállításjegyzék-eleme**.

    ![Képernyőfelvétel: a "Registry" kiválasztott és a "beállításjegyzék-elem" lehetőség be van jelölve.](./media/how-to-connect-sso-quick-start/sso15.png)

4. Adja meg a következő értékeket a megfelelő mezőkben, majd kattintson **az OK** gombra.
   - **Kulcs elérési útja**: **_Software\Microsoft\Windows\CurrentVersion\Internet Settings\ZoneMap\Domains\microsoftazuread-SSO.com\autologon_**
   - **Érték neve**: **_https_**
   - **Érték típusa**: **_REG_DWORD_**
   - **Érték**: **_00000001_**
 
     ![Képernyőkép, amely az "új beállításjegyzék tulajdonságai" ablakot jeleníti meg.](./media/how-to-connect-sso-quick-start/sso16.png)
 
     ![Egyszeri bejelentkezés](./media/how-to-connect-sso-quick-start/sso17.png)

### <a name="browser-considerations"></a>A böngésző szempontjai

#### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (minden platform)

A Mozilla Firefox nem használ automatikusan Kerberos-hitelesítést. Az alábbi lépések segítségével minden felhasználónak manuálisan hozzá kell adnia az Azure AD URL-címét a Firefox-beállításaihoz:
1. Futtassa a Firefoxot, és írja be `about:config` a címsorba. Zárja be a megjelenő értesítéseket.
2. Keressen rá a **Network. Negotiate – auth. megbízható – URI-** k beállításra. Ez a beállítás felsorolja a Firefox megbízható helyeit a Kerberos-hitelesítéshez.
3. Kattintson a jobb gombbal, és válassza a **módosítás** lehetőséget.
4. Adja meg `https://autologon.microsoftazuread-sso.com` a mezőt a mezőben.
5. Kattintson **az OK gombra** , majd nyissa meg újra a böngészőt.

#### <a name="safari-macos"></a>Safari (macOS)

Győződjön meg arról, hogy a macOS-t futtató számítógép csatlakozik az AD-hez. A macOS-eszközhöz való csatlakozásra vonatkozó utasítások a jelen cikk hatókörén kívül esnek.

#### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge a Chromium alapján (minden platform)

Ha felülbírálta a [AuthNegotiateDelegateAllowlist](/DeployEdge/microsoft-edge-policies#authnegotiatedelegateallowlist) vagy a [AuthServerAllowlist](/DeployEdge/microsoft-edge-policies#authserverallowlist) házirend-beállításait a környezetben, ügyeljen arra, hogy az Azure ad URL-címét ( `https://autologon.microsoftazuread-sso.com` ) is hozzáadja hozzájuk.

#### <a name="microsoft-edge-based-on-chromium-macos-and-other-non-windows-platforms"></a>Microsoft Edge króm alapján (macOS és egyéb nem Windows platform)

A macOS és más nem Windows rendszerű platformokon a Chromium-alapú Microsoft Edge esetén [a Chromium-szabályzatok listája alapján](/DeployEdge/microsoft-edge-policies#authserverallowlist) tájékozódhat arról, hogyan adhat hozzá az Azure ad URL-címet integrált hitelesítéshez az engedélyezési listához.

#### <a name="google-chrome-all-platforms"></a>Google Chrome (minden platform)

Ha felülbírálta a [AuthNegotiateDelegateWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthNegotiateDelegateWhitelist) vagy a [AuthServerWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthServerWhitelist) házirend-beállításait a környezetben, ügyeljen arra, hogy az Azure ad URL-címét ( `https://autologon.microsoftazuread-sso.com` ) is hozzáadja hozzájuk.

#### <a name="google-chrome-macos-and-other-non-windows-platforms"></a>Google Chrome (macOS és egyéb nem Windows platform)

A Google Chrome macOS és más nem Windows platformokon való használata esetén tekintse meg [a Chromium-projekt házirendjének listáját](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) , ahol megtudhatja, hogyan szabályozhatja az Azure ad URL-cím engedélyezési listáját az integrált hitelesítéshez.

A harmadik féltől származó Active Directory Csoportházirend bővítmények használata az Azure AD URL-címének a Firefox-ra és a Google Chrome-ra való bevezetésére Mac-felhasználókon kívül esik a jelen cikk hatókörén.

#### <a name="known-browser-limitations"></a>Böngészőkkel kapcsolatos ismert korlátozások

A zökkenőmentes egyszeri bejelentkezés nem működik a Firefox és a Microsoft Edge (örökölt) böngészők privát böngészési módjában. Emellett az Internet Explorerben nem működik, ha a böngésző fokozottan védett módban fut. A zökkenőmentes egyszeri bejelentkezés a Chromium-alapú Microsoft Edge következő verzióját támogatja, és a fejlesztés InPrivate-és vendég módban is működik.

## <a name="step-4-test-the-feature"></a>4. lépés: a funkció tesztelése

Egy adott felhasználó szolgáltatásának teszteléséhez győződjön meg arról, hogy a következő feltételek teljesülnek:
  - A felhasználó bejelentkezik egy vállalati eszközre.
  - Az eszköz csatlakoztatva van a Active Directory tartományhoz. Az eszköznek _nem_ kell csatlakoznia az [Azure ad-hez](../devices/overview.md).
  - Az eszköz közvetlen kapcsolattal rendelkezik a tartományvezérlővel (DC) a vállalati vezetékes vagy vezeték nélküli hálózaton, vagy egy távelérési kapcsolaton keresztül, például egy VPN-kapcsolaton keresztül.
  - A funkciót a Csoportházirendon keresztül [építették ki a](#step-3-roll-out-the-feature) felhasználó számára.

A forgatókönyv teszteléséhez, ahol a felhasználó csak a felhasználónevet adja meg, nem pedig a jelszót:
   - Jelentkezzen be a következőbe: https://myapps.microsoft.com/ . Ügyeljen arra, hogy törölje a böngésző gyorsítótárát, vagy használjon egy új privát böngésző-munkamenetet az egyik támogatott böngészőben a privát módban.

A következő lépések egyikével tesztelheti azt a forgatókönyvet, amelyben a felhasználónak nem kell megadnia a felhasználónevet vagy a jelszót: 
   - Jelentkezzen be, hogy `https://myapps.microsoft.com/contoso.onmicrosoft.com` törölje a böngésző gyorsítótárát, vagy használjon egy új privát böngésző-munkamenetet az összes támogatott böngészőben a privát módban. A *contoso* helyére írja be a bérlő nevét.
   - Jelentkezzen be `https://myapps.microsoft.com/contoso.com` egy új privát böngésző-munkamenetbe. Cserélje le a *contoso.com* egy ellenőrzött tartományra (nem összevont tartományra) a bérlőn.

## <a name="step-5-roll-over-keys"></a>5. lépés: kulcsok átadása

A 2. lépésben Azure AD Connect az összes olyan Active Directory erdőben hoz létre számítógépfiókokat (amely az Azure AD-t jelöli), amelyeken engedélyezve van a zökkenőmentes egyszeri bejelentkezés. További információ [: Azure Active Directory zökkenőmentes egyszeri bejelentkezés: technikai](how-to-connect-sso-how-it-works.md)részletes információk.

>[!IMPORTANT]
>Egy számítógépfiók Kerberos-visszafejtési kulcsa, ha kiszivárgott, felhasználható Kerberos-jegyek létrehozásához bármely, az AD-erdőben lévő felhasználó számára. A rosszindulatú szereplők ezután megszemélyesítheti az Azure AD-bejelentkezéseket a feltört felhasználók számára. Javasoljuk, hogy rendszeres időközönként áttekintse ezeket a Kerberos-visszafejtési kulcsokat – legalább 30 naponta egyszer.

A kulcsok átadására vonatkozó utasításokért lásd [: Azure Active Directory zökkenőmentes egyszeri bejelentkezés: gyakori kérdések](how-to-connect-sso-faq.md). Dolgozunk a kulcsok automatizált átadásának bevezetésében.

>[!IMPORTANT]
>Ezt a lépést nem kell _azonnal_ végrehajtania, miután engedélyezte a szolgáltatást. A Kerberos-visszafejtési kulcsok legalább 30 naponkénti átadása.

## <a name="next-steps"></a>Következő lépések

- Részletes [technikai](how-to-connect-sso-how-it-works.md)tudnivalók: az egyszerű Sign-On funkció működésének megismerése.
- [Gyakori kérdések](how-to-connect-sso-faq.md): válaszokat kaphat a zökkenőmentes egyszeri bejelentkezéssel kapcsolatos gyakori kérdésekre.
- [Hibaelhárítás](tshoot-connect-sso.md): megtudhatja, Hogyan oldhatók fel a zökkenőmentes egyszeri Sign-On szolgáltatással kapcsolatos gyakori problémák.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): használja a Azure Active Directory fórumot az új szolgáltatásokra vonatkozó kérelmek fájljának megjelenítéséhez.

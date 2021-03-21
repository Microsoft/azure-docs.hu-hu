---
title: Az Azure AD Multi-Factor Authentication üzembe helyezésével kapcsolatos megfontolások
description: Ismerje meg az Azure AD Multi-Factor Authentication sikeres megvalósításához szükséges telepítési szempontokat és stratégiát
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9ee81abd7cd0268a7cbd6b16aa6065ec7b54bef
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96861306"
---
# <a name="plan-an-azure-ad-multi-factor-authentication-deployment"></a>Azure AD Multi-Factor Authentication üzemelő példány megtervezése

A felhasználók egyre összetettebb helyzetekben csatlakoznak a szervezeti erőforrásokhoz. A felhasználók a vállalati hálózaton lévő, személyes és nyilvános eszközökről az intelligens telefonok, a tabletták, a számítógépek és a laptopok használatával, gyakran több platformon csatlakoznak. Ebben a mindig összekapcsolt, többeszközes és többplatformos világban a felhasználói fiókok biztonsága fontosabb, mint valaha. Az eszközökön, hálózatokon és platformokon használt jelszavak, függetlenül attól, hogy milyen összetettségük van, már nem elégségesek a felhasználói fiók biztonságának biztosításához, különösen akkor, ha a felhasználók gyakran használják a jelszavakat a fiókok között. A kifinomult adathalászat és más közösségi mérnöki támadások miatt a Felhasználónév és a jelszavak a sötét webes hálózaton való közzététel és értékesítés során is megadhatók.

Az [Azure AD multi-Factor Authentication (MFA)](concept-mfa-howitworks.md) segítségével biztosítható az adathozzáférés és az alkalmazások védelme. További biztonsági réteget biztosít a hitelesítés második formáját használva. A szervezetek [feltételes hozzáférést](../conditional-access/overview.md) használhatnak arra, hogy a megoldás illeszkedjen az adott igényeihez.

Ez az üzembe helyezési útmutató bemutatja, hogyan tervezheti meg és tesztelheti az Azure AD-Multi-Factor Authentication kiépítését.

Az Azure AD Multi-Factor Authentication működés közbeni gyors megjelenítéséhez, majd térjen vissza a további üzembe helyezési megfontolások megismeréséhez:

> [!div class="nextstepaction"]
> [Az Azure AD többtényezős hitelesítés engedélyezése](tutorial-enable-azure-mfa.md)

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD Multi-Factor Authentication üzembe helyezésének megkezdése előtt meg kell fontolnia az előfeltételként szükséges elemeket.

| Eset | Előfeltétel |
| --- | --- |
| **Csak felhőalapú** identitás-környezet modern hitelesítéssel | **Nincsenek további előfeltétel-feladatok** |
| **Hibrid** identitási forgatókönyvek | [Azure ad Connect](../hybrid/whatis-hybrid-identity.md) települ, és a felhasználói identitások szinkronizálása vagy összevonása a helyszíni Active Directory Domain Services a Azure Active Directory. |
| Felhőbeli hozzáféréshez közzétett helyszíni örökölt alkalmazások | [Az Azure ad-alkalmazásproxy üzembe](../manage-apps/application-proxy.md) helyezése megtörténik. |
| Az Azure AD MFA használata RADIUS-hitelesítéssel | A [hálózati házirend-kiszolgáló (NPS)](howto-mfa-nps-extension.md) telepítve van. |
| A felhasználók Microsoft Office 2010-es vagy korábbi verzióját, vagy az Apple Mail for iOS 11 vagy korábbi verzióját. | Frissítsen [Microsoft Office 2013-es vagy újabb](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o) verzióra, valamint az Apple Mail for iOS 12 vagy újabb verzióra. A régi hitelesítési protokollok nem támogatják a feltételes hozzáférést. |

## <a name="plan-user-rollout"></a>Felhasználói bevezetés megtervezése

Az MFA bevezetési tervének tartalmaznia kell egy próba-telepítést, amelyet a támogatási kapacitáson belül üzembe helyezési hullámok követnek. Kezdje el a bevezetést úgy, hogy a feltételes hozzáférési szabályzatokat a kísérleti felhasználók egy kis csoportjára alkalmazza. A kísérleti felhasználókra gyakorolt hatás kiértékelése, a felhasznált folyamat és a regisztrálási viselkedés alapján további csoportokat adhat hozzá a Szabályzathoz, vagy hozzáadhat további felhasználókat a meglévő csoportokhoz.

### <a name="user-communications"></a>Felhasználói kommunikáció

Fontos, hogy tájékoztassa a felhasználókat a tervezett kommunikációról, a közelgő változásokról, az Azure AD MFA regisztrációs követelményeiről és a szükséges felhasználói műveletekről. Javasoljuk, hogy a kommunikációt a szervezeten belüli képviselőivel, például a kommunikációval, a változás-kezeléssel vagy az emberi erőforrásokkal foglalkozó szervezeti egységekkel együtt fejlesszék.

A Microsoft [kommunikációs sablonokat](https://aka.ms/mfatemplates) és [végfelhasználói dokumentációt](../user-help/security-info-setup-signin.md) biztosít a kommunikáció megtervezéséhez. [https://myprofile.microsoft.com](https://myprofile.microsoft.com)Az oldalon található **biztonsági információk** hivatkozásaira kattintva a felhasználók közvetlenül regisztrálhatnak.

## <a name="deployment-considerations"></a>Telepítési szempontok

Az Azure AD Multi-Factor Authentication üzembe helyezése a házirendek feltételes hozzáféréssel való kényszerítésével történik. A feltételes hozzáférési szabályzatok megkövetelhetik a felhasználóktól a többtényezős hitelesítés végrehajtását, ha bizonyos feltételek teljesülnek, például:

* Minden felhasználó, egy adott felhasználó, egy csoport tagja vagy hozzárendelt szerepkör
* Az adott felhőalapú alkalmazás elérése folyamatban van
* Eszközplatform
* Eszköz állapota
* Hálózati hely vagy földrajzi elhelyezkedésű IP-cím
* Ügyfélalkalmazások
* Bejelentkezési kockázat (Identity Protection szükséges)
* Megfelelő eszköz
* Hibrid Azure AD-hez csatlakoztatott eszköz
* Jóváhagyott ügyfélalkalmazás

A többtényezős hitelesítés bevezetési [anyagaiban](https://www.microsoft.com/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all) a testreszabható plakátok és e-mail-sablonok segítségével hozhatja ki a szervezete többtényezős hitelesítését.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>Feltételes hozzáféréssel rendelkező Multi-Factor Authentication engedélyezése

A feltételes hozzáférési szabályzatok betartják a regisztrációt, így a regisztrációt nem igénylő felhasználókat az első bejelentkezéskor, fontos biztonsági megfontolásokból kell végrehajtani.

A [Azure ad Identity Protection](../identity-protection/howto-identity-protection-configure-risk-policies.md) a regisztrációs házirendet és az automatizált kockázatkezelési és szervizelési szabályzatokat is hozzájárul az Azure ad multi-Factor Authentication Story-hoz. A szabályzatok úgy hozhatók létre, hogy kényszerítsék a jelszó megváltoztatását, ha fennáll a veszélye a sérült identitásnak, vagy ha a bejelentkezés a következő [események](../identity-protection/overview-identity-protection.md)kockázatának minősül:

* Kiszivárgott hitelesítő adatok
* Bejelentkezések névtelen IP-címről
* Bejelentkezés szokatlan helyekről
* Bejelentkezések ismeretlen helyekről
* Bejelentkezések fertőzött eszközökről
* Gyanús tevékenységeket folytató IP-címekről érkező bejelentkezések

A Azure Active Directory Identity Protection által észlelt kockázati észlelések némelyike valós időben történik, és egyes esetekben offline feldolgozásra van szükség. A rendszergazdák dönthetnek úgy, hogy letiltják a kockázatos viselkedést kiállító felhasználókat, és manuálisan szervizelik, jelszó-módosítást igényelnek, vagy többtényezős hitelesítést igényelnek a feltételes hozzáférési szabályzatok részeként.

## <a name="define-network-locations"></a>Hálózati telephelyek definiálása

Javasoljuk, hogy a szervezetek a feltételes hozzáférés használatával definiálják a hálózatot a [nevesített helyekkel](../conditional-access/location-condition.md#named-locations). Ha a szervezete Identity Protectiont használ, érdemes lehet kockázati alapú házirendeket használni a nevesített helyszínek helyett.

### <a name="configuring-a-named-location"></a>Elnevezett hely konfigurálása

1. **Azure Active Directory** megnyitása a Azure Portal
2. **Biztonság** kiválasztása
3. A **kezelés** területen válassza az **elnevezett helyszínek** lehetőséget.
4. **Új hely** kiválasztása
5. Adjon meg egy értelmes nevet a **név** mezőben.
6. Válassza ki, hogy *IP-címtartományok* vagy *országok/régiók* használatával határozza meg a helyet
   1. *IP-címtartományok* használata esetén
      1. Döntse el, hogy megbízható helyet szeretne-e *megjelölni*. A megbízható helyről történő bejelentkezésnél kisebb a felhasználó bejelentkezési kockázata. Csak akkor adja meg ezt a helyet megbízhatónak, ha ismeri a megadott IP-címtartományok létrehozását és hitelességét a szervezetben.
      2. IP-címtartományok megadása
   2. *Országok/régiók* használata esetén
      1. Bontsa ki a legördülő menüt, és válassza ki azokat az országokat vagy régiókat, amelyeket meg szeretne határozni ehhez a megnevezett helyhez.
      2. Döntse el, hogy az *ismeretlen területeket is tartalmazza*-e. Az ismeretlen területek olyan IP-címek, amelyek nem képezhetők le országra/régióra.
7. Kattintson a **Létrehozás** elemre.

## <a name="plan-authentication-methods"></a>Hitelesítési módszerek megtervezése

A rendszergazdák kiválaszthatják a felhasználók számára elérhetővé tenni kívánt [hitelesítési módszereket](../authentication/concept-authentication-methods.md) . Fontos, hogy egynél több hitelesítési módszert engedélyezzen, hogy a felhasználók számára elérhető legyen egy biztonsági mentési módszer, ha az elsődleges metódus nem érhető el. A rendszergazdák a következő módszerekkel engedélyezhetők:

> [!TIP]
> A Microsoft a Microsoft Authenticator (Mobile App) használatát javasolja elsődleges módszerként az Azure AD-Multi-Factor Authentication számára a biztonságosabb és továbbfejlesztett felhasználói élmény érdekében. A Microsoft Authenticator alkalmazás a szabványügyi és technológiai hitelesítő szintű nemzeti intézetnek is [megfelel](https://azure.microsoft.com/resources/microsoft-nist/) . 

### <a name="notification-through-mobile-app"></a>Értesítés a Mobile App használatával

A rendszer leküldéses értesítést küld a mobileszköz Microsoft Authenticator alkalmazásának. A felhasználó megtekinti az értesítést, és kiválasztja a **jóváhagyás** lehetőséget az ellenőrzés befejezéséhez. A mobil alkalmazások leküldéses értesítései biztosítják a legkevésbé zavaró lehetőséget a felhasználók számára. Emellett a legmegbízhatóbb és biztonságos megoldás is, mivel a telefonos szolgáltatás helyett adatkapcsolatokat használnak.

> [!NOTE]
> Ha a szervezete Kínában dolgozik vagy Kínába utazik, az **Android-eszközökön** a **Mobile App metóduson keresztül küldött értesítés** nem működik az adott országban vagy régióban. Ezeket a felhasználókat alternatív módszereket kell elérhetővé tenni.

### <a name="verification-code-from-mobile-app"></a>Ellenőrző kód a Mobile appből

Egy mobil alkalmazás, például a Microsoft Authenticator alkalmazás, 30 másodpercenként létrehoz egy új eskü-ellenőrző kódot. A felhasználó beírja az ellenőrző kódot a bejelentkezési felületre. A Mobile App (mobil alkalmazás) beállítással megadható, hogy a telefon tartalmaz-e adattípust vagy mobil jelet.

### <a name="call-to-phone"></a>Telefonos hívás

A rendszer automatikusan hanghívást helyez el a felhasználó felé. A felhasználó megválaszolja a hívást, és megnyomja a **#** telefon billentyűzetén a hitelesítés jóváhagyásához. A telefon hívása nagyszerű biztonsági mentési módszer a mobil alkalmazások értesítési vagy ellenőrzési kódjához.

### <a name="text-message-to-phone"></a>SMS-üzenet a telefonra

Egy ellenőrző kódot tartalmazó szöveges üzenetet küld a rendszer a felhasználónak, és megkéri a felhasználót, hogy adja meg az ellenőrző kódot a bejelentkezési felületen.

### <a name="choose-verification-options"></a>Ellenőrzési beállítások kiválasztása

1. Tallózással keresse meg **Azure Active Directory**, **felhasználók**, **multi-Factor Authentication**.

   ![A Multi-Factor Authentication-portál elérése az Azure AD-felhasználók paneljén Azure Portal](media/howto-mfa-getstarted/users-mfa.png)

1. Az új lapon a Tallózás gombra kattintva megnyithatja a **szolgáltatás beállításait**.
1. Az **ellenőrzési beállítások** területen kattintson a felhasználók számára elérhető metódusok összes mezőjére.

   ![Ellenőrzési módszerek konfigurálása a Multi-Factor Authentication szolgáltatás beállításai lapon](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. Kattintson a **Save (Mentés**) gombra.
1. Lépjen be a **Szolgáltatásbeállítások** lapra.

## <a name="plan-registration-policy"></a>Regisztrációs házirend megtervezése

A rendszergazdáknak meg kell határozniuk, hogy a felhasználók hogyan regisztrálják a módszereiket. A szervezeteknek lehetővé kell tenniük az Azure AD MFA és az önkiszolgáló jelszó-visszaállítás (SSPR) [új, együttes regisztrációját](howto-registration-mfa-sspr-combined.md) . A SSPR lehetővé teszi a felhasználók számára, hogy a többtényezős hitelesítéshez használt módszerek használatával biztonságos módon állítsa alaphelyzetbe a jelszavukat. Ezt a közös regisztrációt javasoljuk, mivel ez nagyszerű felhasználói élményt nyújt a felhasználók számára, és mindkét szolgáltatásra egyszer regisztrálhat. A SSPR és az Azure AD MFA ugyanazon módszereinek engedélyezése lehetővé teszi a felhasználók számára, hogy mindkét funkció használatára legyenek regisztrálva.

### <a name="registration-with-identity-protection"></a>Regisztrálás az Identity Protection szolgáltatással

Ha a szervezete Azure Active Directory Identity Protectiont használ, [konfigurálja az MFA regisztrációs házirendjét](../identity-protection/howto-identity-protection-configure-mfa-policy.md) , hogy a felhasználók a következő bejelentkezés alkalmával interaktívan regisztráljanak.

### <a name="registration-without-identity-protection"></a>Regisztráció Identity Protection nélkül

Ha a szervezet nem rendelkezik az Identity Protectiont engedélyező licenccel, a rendszer felszólítja a felhasználókat, hogy regisztrálják a következő alkalommal, amikor az MFA betöltésére van szükség a bejelentkezéskor. Előfordulhat, hogy a felhasználók nem regisztrálhatnak MFA-ra, ha nem használnak MFA-védelemmel ellátott alkalmazásokat. Fontos, hogy minden felhasználó regisztrálva legyen, hogy a hibás szereplők ne tudják kitalálni egy felhasználó jelszavát, és regisztrálják az MFA-t a nevükben, és így a fiók felügyeletét ténylegesen átveszik.

#### <a name="enforcing-registration"></a>Regisztráció érvényesítése

A következő lépések végrehajtásával kényszerítheti a felhasználókat, hogy regisztráljanak Multi-Factor Authentication

1. Hozzon létre egy csoportot, és adja hozzá az összes jelenleg nem regisztrált felhasználót.
2. A feltételes hozzáférés használatával kikényszerítheti a többtényezős hitelesítést a csoport számára az összes erőforráshoz való hozzáféréshez.
3. Rendszeresen ellenőrizze a csoporttagság újraértékelését, és távolítsa el a csoportból regisztrált felhasználókat.

Azonosíthatja a regisztrált és nem regisztrált Azure AD MFA-felhasználókat olyan PowerShell-parancsokkal, amelyek a [MSOnline PowerShell-modulra](/powershell/azure/active-directory/install-msonlinev1)támaszkodnak.

#### <a name="identify-registered-users"></a>Regisztrált felhasználók azonosítása

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>Nem regisztrált felhasználók azonosítása

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Felhasználók konvertálása felhasználónkénti MFA-ből feltételes hozzáférésen alapuló MFA-ra

Ha a felhasználók engedélyezve lettek a felhasználónkénti engedélyezése és a kényszerített Azure AD Multi-Factor Authentication használatával, a következő PowerShell segítséget nyújthat a feltételes hozzáférés-alapú Azure AD Multi-Factor Authentication való átalakításhoz.

Futtassa ezt a PowerShellt egy ISE-ablakban, vagy mentse `.PS1` fájlként a helyi futtatáshoz.

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> Nemrég módosítottuk a viselkedést és a PowerShell-szkriptet a fentieknek megfelelően. Korábban a parancsfájl mentve az MFA-metódusokból, letiltotta az MFA-t, és visszaállította a metódusokat. Ez már nem szükséges ahhoz, hogy a Letiltás alapértelmezett viselkedése ne törölje a metódusokat.

## <a name="plan-conditional-access-policies"></a>Feltételes hozzáférési szabályzatok tervezése

A feltételes hozzáférési házirend stratégiájának megtervezéséhez, amely meghatározza, hogy az MFA-t és más vezérlőket is meg kell határoznia, lásd: [általános feltételes hozzáférési szabályzatok](../conditional-access/concept-conditional-access-policy-common.md).

Fontos, hogy megakadályozza, hogy véletlenül kizárja az Azure AD-bérlőt. A nem megfelelő rendszergazdai hozzáférés következményeinek enyhítése érdekében [hozzon létre két vagy több vészhelyzeti hozzáférési fiókot a bérlőn](../roles/security-emergency-access.md) , és zárja ki őket a feltételes hozzáférési szabályzatból.

### <a name="create-conditional-access-policy"></a>Feltételes hozzáférési szabályzat létrehozása

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdai fiók használatával.
1. Keresse meg **Azure Active Directory**  >  **biztonsági**  >  **feltételes hozzáférését**.
1. Válassza az **új szabályzat** lehetőséget.
   ![Feltételes hozzáférési szabályzat létrehozása a többtényezős hitelesítés engedélyezéséhez Azure Portal-felhasználók számára a kísérleti csoportban](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)
1. Adjon meg egy értelmes nevet a szabályzatnak.
1. A **felhasználók és csoportok** területen:
   * A **beágyazás** lapon jelölje be a **minden felhasználó** választógombot.
   * A **kizárás** lapon jelölje be a **felhasználók és csoportok** jelölőnégyzetet, és válassza ki a vészhelyzeti hozzáférési fiókokat.
   * Kattintson a **Kész** gombra.
1. A **Cloud apps** alatt válassza a **minden Cloud apps** választógombot.
   * OPCIONÁLISan: a **kizárás** lapon válassza ki azokat a felhőalapú alkalmazásokat, amelyekhez a szervezet nem igényel MFA-t a alkalmazáshoz.
   * Kattintson a **Kész** gombra.
1. A **feltételek** szakaszban:
   * OPCIONÁLISan: Ha engedélyezte az Azure Identity Protection szolgáltatást, kiválaszthatja, hogy a szabályzat részeként kiértékeli-e a bejelentkezési kockázatot.
   * OPCIONÁLISan: ha megbízható telephelyeket vagy elnevezett helyeket konfigurált, megadhatja, hogy a rendszer belefoglalja vagy kizárja ezeket a helyeket a szabályzatból.
1. Győződjön meg **arról, hogy** a **hozzáférés** engedélyezése választógomb be van jelölve.
    * Jelölje be a **többtényezős hitelesítés megkövetelése** jelölőnégyzetet.
    * Kattintson a **Kiválasztás** elemre.
1. Ugorja át a **munkamenet** szakaszt.
1. Állítsa be a **házirend engedélyezése** kapcsolót **be értékre.**
1. Kattintson a **Létrehozás** lehetőségre.

## <a name="plan-integration-with-on-premises-systems"></a>A helyszíni rendszerekkel való integráció megtervezése

Egyes örökölt és helyszíni alkalmazások, amelyek nem hitelesítik közvetlenül az Azure AD-t, további lépésekre van szükség az MFA használatához, beleértve a következőket:

* Örökölt helyszíni alkalmazások, amelyeknek az alkalmazásproxy használatát kell használniuk.
* Helyszíni RADIUS-alkalmazások, amelyeknek az MFA-adaptert kell használniuk az NPS-kiszolgálóval.
* Helyszíni AD FS alkalmazások, amelyeknek az MFA-adaptert AD FS 2016-as vagy újabb verzióval kell használniuk.

Az Azure AD-vel közvetlenül hitelesítő alkalmazások, valamint a modern hitelesítés (WS-fed, SAML, OAuth, OpenID Connect) közvetlenül a feltételes hozzáférési házirendeket is használhatják.

### <a name="use-azure-ad-mfa-with-azure-ad-application-proxy"></a>Az Azure AD MFA használata az Azure AD Application Proxy

A helyszínen található alkalmazások közzétehetők az Azure AD-bérlőn az Azure [ad Application Proxyon](../manage-apps/application-proxy.md) keresztül, és igénybe vehetik az Azure ad-multi-Factor Authentication előnyeit, ha az Azure ad előhitelesítés használatára vannak konfigurálva.

Ezek az alkalmazások olyan feltételes hozzáférési szabályzatok hatálya alá tartoznak, amelyek az Azure AD-Multi-Factor Authentication kényszerítik, ugyanúgy, mint bármely más Azure AD-integrált alkalmazáshoz.

Hasonlóképpen, ha az Azure AD-Multi-Factor Authentication az összes felhasználói bejelentkezésnél érvényben van, az Azure AD Application Proxy-mel közzétett helyszíni alkalmazások védelmét védeni fogjuk.

### <a name="integrating-azure-ad-multi-factor-authentication-with-network-policy-server"></a>Az Azure AD Multi-Factor Authentication integrálása a hálózati házirend-kiszolgálóval

Az Azure AD MFA hálózati házirend-kiszolgáló (NPS) bővítménye felhőalapú MFA-képességeket ad a hitelesítési infrastruktúrához a meglévő kiszolgálók használatával. A hálózati házirend-kiszolgáló bővítmény használatával telefonhívást, szöveges üzenetet vagy telefonos alkalmazást adhat hozzá a meglévő hitelesítési folyamathoz. Ez az integráció a következő korlátozásokkal rendelkezik:

* A CHAPv2 protokollal csak a hitelesítő alkalmazások leküldéses értesítései és hanghívásai támogatottak.
* A feltételes hozzáférési szabályzatok nem alkalmazhatók.

A hálózati házirend-kiszolgáló bővítmény adapterként működik a RADIUS és a felhőalapú Azure AD MFA között, hogy a hitelesítés második tényezője legyen a [VPN](howto-mfa-nps-extension-vpn.md)-, [Távoli asztali átjáró-kapcsolatok](howto-mfa-nps-extension-rdg.md)vagy más RADIUS-kompatibilis alkalmazások számára. Az Azure AD MFA-nak ebben a környezetben való regisztrálását végző felhasználókat a rendszer minden hitelesítési kísérlet esetén felhasználja, a feltételes hozzáférési szabályzatok hiánya pedig minden esetben az MFA-t jelenti.

#### <a name="implementing-your-nps-server"></a>A hálózati házirend-kiszolgáló implementálása

Ha a hálózati házirend-kiszolgáló példánya már telepítve van, és már használatban van, az [Azure ad-multi-Factor Authentication a meglévő NPS-infrastruktúra integrálásához](howto-mfa-nps-extension.md). Ha első alkalommal állítja be a hálózati házirend-kiszolgálót, akkor útmutatásért tekintse meg a [hálózati házirend-kiszolgáló (NPS)](/windows-server/networking/technologies/nps/nps-top) című témakört. A hibaelhárítási útmutató a következő cikkben található: [hibaüzenetek feloldása az Azure AD multi-Factor Authentication NPS-bővítményéről](howto-mfa-nps-extension-errors.md).

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>A hálózati házirend-kiszolgáló előkészítése az MFA-ban nem regisztrált felhasználók számára

Válassza ki, hogy mi történjen, ha az MFA-ban nem regisztrált felhasználók hitelesítése történik meg. A `REQUIRE_USER_MATCH` `HKLM\Software\Microsoft\AzureMFA` szolgáltatás működésének vezérléséhez használja a beállításjegyzékbeli elérési út beállításjegyzékbeli beállítását. Ez a beállítás egyetlen konfigurációs lehetőséggel rendelkezik.

| Kulcs | Érték | Alapértelmezett |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | IGAZ/HAMIS | Nincs beállítva (megegyezik az igaz értékkel) |

Ennek a beállításnak a célja annak meghatározása, hogy mi a teendő, ha egy felhasználó nincs regisztrálva az MFA-hoz. A beállítás módosításának hatásai az alábbi táblázatban láthatók.

| Beállítások | Felhasználói MFA-állapot | Hatásait |
| --- | --- | --- |
| A kulcs nem létezik | Nincs regisztrálva | Az MFA-kérdés nem sikerült |
| Az érték igaz/nincs beállítva | Nincs regisztrálva | Az MFA-kérdés nem sikerült |
| A kulcs hamis értékre van állítva | Nincs regisztrálva | Hitelesítés MFA nélkül |
| A kulcs hamis vagy igaz értékre van beállítva | Regisztrálva | Hitelesítést kell végezni MFA-val |

### <a name="integrate-with-active-directory-federation-services"></a>Integrálás Active Directory összevonási szolgáltatások (AD FS)

Ha a szervezete az Azure AD-vel összevont, az [Azure ad-multi-Factor Authentication használatával biztosíthatja](multi-factor-authentication-get-started-adfs.md)a helyszíni és a Felhőbeli erőforrások ad FSét is. Az Azure AD MFA segítségével csökkentheti a jelszavakat, és biztonságosabban biztosíthatja a hitelesítést. A Windows Server 2016-től kezdődően mostantól konfigurálhatja az Azure AD MFA-t az elsődleges hitelesítéshez.

A Windows Server 2012 R2 AD FSával ellentétben a AD FS 2016 Azure AD MFA-adapter közvetlenül az Azure AD-vel integrálódik, és nem igényel helyszíni Azure MFA-kiszolgálót. Az Azure AD MFA-adapter a Windows Server 2016-be van építve, és nincs szükség további telepítésre.

Ha az Azure AD MFA-t AD FS 2016-es és a célalkalmazás használatára vonatkozó feltételes hozzáférési szabályzat hatálya alá tartozik, további szempontokat is figyelembe kell venni:

* A feltételes hozzáférés akkor érhető el, ha az alkalmazás egy függő entitás az Azure AD-hoz, összevont AD FS 2016-as vagy újabb verzióval.
* A feltételes hozzáférés nem érhető el, ha az alkalmazás függő entitás AD FS 2016 vagy AD FS 2019, és felügyelt vagy összevont AD FS 2016 vagy AD FS 2019.
* A feltételes hozzáférés akkor is nem érhető el, ha AD FS 2016 vagy AD FS 2019 úgy van konfigurálva, hogy az Azure AD MFA-t használja elsődleges hitelesítési módszerként.

#### <a name="ad-fs-logging"></a>AD FS naplózás

A standard AD FS 2016 és a 2019 naplózási szolgáltatás a Windows biztonsági naplóban és a AD FS felügyeleti naplóban is tartalmaz információkat a hitelesítési kérésekről, valamint azok sikerességéről vagy meghibásodásáról. Az események eseménynaplójának adatai jelzik, hogy az Azure AD MFA használatban van-e. Az 1200 AD FS-es naplózási eseményazonosító például a következőket tartalmazhatja:

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>Tanúsítványok megújítása és kezelése

Az egyes AD FS-kiszolgálókon a helyi számítógép saját tárolójában egy önaláírt Azure AD MFA-tanúsítvány található, amely a tanúsítvány lejárati dátumát tartalmazza OU = Microsoft AD FS Azure MFA. A lejárati dátum megállapításához ellenőrizze a tanúsítvány érvényességi időtartamát az egyes AD FS-kiszolgálókon.

Ha a tanúsítványok érvényességi ideje közeledik a lejárathoz, [állítson elő és ellenőrizzen egy új MFA-tanúsítványt az egyes AD FS-kiszolgálókon](/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers).

Az alábbi útmutató ismerteti, hogyan kezelheti az Azure AD MFA-tanúsítványokat a AD FS-kiszolgálókon. Ha az Azure AD MFA-val AD FS konfigurál, a PowerShell-parancsmag használatával generált tanúsítványok `New-AdfsAzureMfaTenantCertificate` két évig érvényesek. Megújíthatja és telepítheti a megújított tanúsítványokat, mielőtt lejár az MFA-szolgáltatásban előforduló tojások megszakadásának.

## <a name="implement-your-plan"></a>A terv megvalósítása

Most, hogy megtervezte a megoldást, az alábbi lépésekkel végezheti el a megvalósítást:

1. Teljesítse a szükséges előfeltételeket
   1. [Azure ad Connect](../hybrid/whatis-hybrid-identity.md) üzembe helyezése bármilyen hibrid forgatókönyv esetén
   1. [Azure-ad Application proxy](../manage-apps/application-proxy.md) üzembe helyezése a Felhőbeli hozzáféréshez közzétett helyszíni alkalmazásokhoz
   1. [Hálózati házirend-kiszolgáló](/windows-server/networking/technologies/nps/nps-top) üzembe helyezése bármely RADIUS-hitelesítéshez
   1. Győződjön meg arról, hogy a felhasználók a modern hitelesítéssel rendelkező Microsoft Office támogatott verzióira frissítettek
1. Kiválasztott [hitelesítési módszerek](#choose-verification-options) konfigurálása
1. Megnevezett [hálózati telephelyek](../conditional-access/location-condition.md#named-locations) definiálása
1. Válassza ki a csoportokat az MFA kivezetésének megkezdéséhez.
1. [Feltételes hozzáférési szabályzatok](#create-conditional-access-policy) konfigurálása
1. Az MFA regisztrációs szabályzatának konfigurálása
   1. [Kombinált MFA és SSPR](howto-registration-mfa-sspr-combined.md)
   1. [Identitás-védelemmel](../identity-protection/howto-identity-protection-configure-mfa-policy.md)
1. Felhasználói kommunikáció küldése és a felhasználók beléptetése [https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [A regisztrált felhasználók nyomon követése](#identify-non-registered-users)

> [!TIP]
> A kormányzati felhő felhasználói regisztrálhatnak [https://aka.ms/GovtMFASetup](https://aka.ms/GovtMFASetup)

## <a name="manage-your-solution"></a>A megoldás kezelése

Jelentések az Azure AD MFA-hoz

Az Azure AD Multi-Factor Authentication jelentéseket biztosít a Azure Portalon keresztül:

| Jelentés | Hely | Description |
| --- | --- | --- |
| Használati és csalási riasztások | Azure AD > bejelentkezések | Információt nyújt a teljes használatról, a felhasználói összesítésekről és a felhasználói adatokról; valamint a megadott dátumtartomány szerint elküldött csalási riasztások előzményei. |

## <a name="troubleshoot-mfa-issues"></a>MFA-problémák elhárítása

Az Azure AD MFA gyakori problémáinak megoldásait az [Azure ad multi-Factor Authentication hibaelhárítási cikkében](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues) találja a Microsoft ügyfélszolgálata központban.

## <a name="next-steps"></a>Következő lépések

Az Azure AD Multi-Factor Authentication működés közbeni megtekintéséhez hajtsa végre a következő oktatóanyagot:

> [!div class="nextstepaction"]
> [Az Azure AD többtényezős hitelesítés engedélyezése](tutorial-enable-azure-mfa.md)

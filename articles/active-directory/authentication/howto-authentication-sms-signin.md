---
title: SMS-alapú felhasználói bejelentkezés Azure Active Directory
description: Megtudhatja, hogyan konfigurálhatja és engedélyezheti a felhasználók számára, hogy sms Azure Active Directory bejelentkeznek
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rateller
ms.collection: M365-identity-device-management
ms.openlocfilehash: b84d55e2d3a2f49a870c1e57eeed3c5c0caeba4a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530423"
---
# <a name="configure-and-enable-users-for-sms-based-authentication-using-azure-active-directory"></a>Felhasználók konfigurálása és engedélyezése SMS-alapú hitelesítéshez Azure Active Directory 

Az alkalmazásokba és szolgáltatásokba való bejelentkezés egyszerűsítése és biztonságossá Azure Active Directory (Azure AD) több hitelesítési lehetőséget is kínál. Az SMS-alapú hitelesítés lehetővé teszi, hogy a felhasználók anélkül jelentkezzenek be, hogy meg tudnák vagy akár ismerni is tudnák a felhasználónevüket és jelszavukat. Miután egy identitás-rendszergazda létrehozta a fiókját, a bejelentkezési kérésnél megadhatja a telefonszámát. A bejelentkezés befejezéséhez szöveges üzenetben kapják meg a hitelesítési kódot. Ez a hitelesítési módszer leegyszerűsíti az alkalmazásokhoz és szolgáltatásokhoz való hozzáférést, különösen a frontline-dolgozók számára.

Ez a cikk bemutatja, hogyan engedélyezheti az SMS-alapú hitelesítést egyes felhasználók vagy csoportok számára az Azure AD-ben.

## <a name="before-you-begin"></a>Előkészületek

A cikk befejezéséhez a következő erőforrásokra és jogosultságokra lesz szüksége:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Egy Azure Active Directory bérlő, amely az előfizetéséhez van társítva.
    * Szükség esetén [hozzon létre egy Azure Active Directory bérlőt,][create-azure-ad-tenant] vagy [társítsa az Azure-előfizetést a fiókjához.][associate-azure-ad-tenant]
* Az *SMS-alapú hitelesítés engedélyezéséhez* globális rendszergazdai jogosultságokkal kell rendelkezik az Azure AD-bérlőben.
* A szöveges üzenet hitelesítési módszerének szabályzatában engedélyezett összes felhasználónak licencelni kell, még akkor is, ha nem használja. Minden engedélyezett felhasználónak a következő Azure AD- és EMS-licenccel kell Microsoft 365:
    * [Microsoft 365 (M365) F1 vagy F3][m365-firstline-workers-licensing]
    * [Enterprise Mobility + Security (EMS) E3, E5][ems-licensing] vagy [Microsoft 365 (M365) E3 vagy E5][m365-licensing]
    * [Office 365 F3][o365-f3]

## <a name="limitations"></a>Korlátozások

Az SMS-alapú hitelesítésre a következő korlátozások vonatkoznak:

* Az SMS-alapú hitelesítés jelenleg nem kompatibilis az Azure AD Multi-Factor Authentication használatával.
* A Teams kivételével az SMS-alapú hitelesítés nem kompatibilis a natív Office-alkalmazásokkal.
* A B2B-fiókok esetében nem ajánlott az SMS-alapú hitelesítés.
* Az összevont felhasználók nem fognak hitelesítést végezni a saját bérlőben. Csak a felhőben hitelesítik magukat.

## <a name="enable-the-sms-based-authentication-method"></a>Az SMS-alapú hitelesítési módszer engedélyezése

Az SMS-alapú hitelesítés engedélyezésének és használatának három fő lépése van a szervezetben:

* Engedélyezze a hitelesítési módszer házirendet.
* Válassza ki az SMS-alapú hitelesítési módszert használó felhasználókat vagy csoportokat.
* Rendeljen hozzá egy telefonszámot minden felhasználói fiókhoz.
    * Ez a telefonszám hozzárendelhető a Azure Portal (amely ebben a cikkben látható), valamint a Munkatársak *saját* *fiókban.*

Először engedélyezze az SMS-alapú hitelesítést az Azure AD-bérlő számára.

1. Jelentkezzen be a [Azure Portal][azure-portal] globális *rendszergazdaként.*
1. Keresse meg és válassza ki az **Azure Active Directoryt**.
1. A navigációs ablak bal oldalán található navigációs menüben válassza Azure Active Directory a Security > Authentication methods > Policy (Hitelesítési módszer) szabályzat **lehetőséget.**

    [![Keresse meg és válassza ki a hitelesítési módszer házirend ablakát a Azure Portal.](media/howto-authentication-sms-signin/authentication-method-policy-cropped.png)](media/howto-authentication-sms-signin/authentication-method-policy.png#lightbox)

1. Az elérhető hitelesítési módszerek listájából válassza a **Szöveges üzenet lehetőséget.**
1. Állítsa **az Engedélyezést** *Igenre.*

    ![Szöveghitelesítés engedélyezése a hitelesítési módszer házirendablakában](./media/howto-authentication-sms-signin/enable-text-authentication-method.png)

    Engedélyezheti az SMS-alapú hitelesítést a *Minden* felhasználó vagy *a Felhasználók és csoportok* kiválasztása lehetőség választását. A következő szakaszban engedélyeznie kell az SMS-alapú hitelesítést egy tesztfelhasználó számára.

## <a name="assign-the-authentication-method-to-users-and-groups"></a>A hitelesítési módszer hozzárendelése felhasználókhoz és csoportokhoz

Most, hogy az AZURE AD-bérlőben engedélyezve van az SMS-alapú hitelesítés, válasszon ki néhány felhasználót vagy csoportot, akik számára engedélyezni kell a hitelesítési módszer használatát.

1. A szöveges üzenet hitelesítési házirendablakában állítsa a **Cél beállítását** a *Felhasználók kiválasztása beállításra.*
1. Válassza a **Felhasználók vagy csoportok hozzáadása** lehetőséget, majd válasszon ki egy tesztfelhasználót vagy csoportot, például *Contoso-felhasználó* vagy *Contoso SMS-felhasználók.*

    [![Válassza ki az SMS-alapú hitelesítéshez engedélyezni szükséges felhasználókat vagy csoportokat a Azure Portal.](media/howto-authentication-sms-signin/add-users-or-groups-cropped.png)](media/howto-authentication-sms-signin/add-users-or-groups.png#lightbox)

1. Miután kiválasztotta a felhasználókat vagy csoportokat, válassza a **Kiválasztás,** majd a **Frissített** hitelesítési módszer házirend mentése lehetőséget.

A szöveges üzenet hitelesítési módszerének szabályzatában engedélyezett minden felhasználót licenccel kell rendelkezőnek lennie, még akkor is, ha nem használja. Győződjön meg arról, hogy rendelkezik a megfelelő licencekkel a hitelesítési módszer házirendben engedélyezett felhasználók számára, különösen akkor, ha a funkciót nagy felhasználói csoportok számára engedélyezi.

## <a name="set-a-phone-number-for-user-accounts"></a>Telefonszám beállítása felhasználói fiókokhoz

A felhasználók mostantól engedélyezettek az SMS-alapú hitelesítéshez, de a telefonszámukat az Azure AD-beli felhasználói profilhoz kell társítanuk a bejelentkezés előtt. A felhasználó [beállíthatja ezt a](../user-help/sms-sign-in-explainer.md) telefonszámot a *Saját* fiókban, vagy hozzárendelheti a telefonszámot a Azure Portal. A telefonszámokat a globális *rendszergazdák,* a hitelesítési rendszergazdák vagy a rendszer-jogosultságú  *hitelesítéssel rendelkező rendszergazdák állíthatják be.*

Ha az SMS-signhez be van állítva egy telefonszám, az [Azure AD Multi-Factor Authentication][tutorial-azure-mfa] és az új jelszó önkiszolgáló visszaállítása esetén is [használható.][tutorial-sspr]

1. Keresse meg és válassza ki az **Azure Active Directoryt**.
1. Az ablak bal oldalán található navigációs menüben válassza a Azure Active Directory **lehetőséget.**
1. Válassza ki azt a felhasználót, akinél az előző szakaszban engedélyezte az SMS-alapú hitelesítést, például Contoso-felhasználó, majd válassza a **Hitelesítési módszerek lehetőséget.**
1. Válassza **a + Hitelesítési módszer hozzáadása lehetőséget,** majd a *Módszer* kiválasztása legördülő menüben válassza a **Telefonszám lehetőséget.**

    Adja meg a felhasználó telefonszámát az országkóddal együtt( *például +1 xxxxxxxxx).* A Azure Portal ellenőrzi, hogy a telefonszám a megfelelő formátumban van-e.

    Ezután a *Phone type (Telefon típusa)* legördülő menüben válassza a *Mobile (Mobil)*, Alternate mobile (Alternatív *mobil)* vagy Other as needed *(Egyéb)* lehetőséget.

    :::image type="content" source="media/howto-authentication-sms-signin/set-user-phone-number.png" alt-text="Az SMS-alapú hitelesítéshez használni Azure Portal felhasználó telefonszámának beállítása a fiókban":::

    A telefonszámnak egyedinek kell lennie a bérlőben. Ha ugyanazt a telefonszámot próbálja több felhasználóhoz is használni, hibaüzenet jelenik meg.

1. Ha a telefonszámot egy felhasználó fiókjára szeretne alkalmazni, válassza a Hozzáadás **lehetőséget.**

Sikeres üzembe építés esetén pipa jelenik meg, ha az *SMS-bejelentkezés engedélyezve van.*

## <a name="test-sms-based-sign-in"></a>SMS-alapú bejelentkezés tesztelése

Az SMS-alapú bejelentkezéshez engedélyezett felhasználói fiók tesztelését az alábbi lépésekkel lehet végrehajtani:

1. Nyisson meg egy új InPrivate vagy Inkognitó böngészőablakot a következőre: [https://www.office.com][office]
1. A jobb felső sarokban válassza a **Bejelentkezés lehetőséget.**
1. A bejelentkezési parancssorba írja be az előző szakaszban a felhasználóhoz társított telefonszámot, majd válassza a Tovább **lehetőséget.**

    ![Adjon meg egy telefonszámot a tesztfelhasználó bejelentkezési kérésében](./media/howto-authentication-sms-signin/sign-in-with-phone-number.png)

1. A rendszer szöveges üzenetet küld a megadott telefonszámra. A bejelentkezési folyamat befejezéséhez írja be a 6 számjegyű kódot, amely a bejelentkezési üzenetben található.

    ![Adja meg a felhasználó telefonszámának szöveges üzenetben küldött megerősítő kódot](./media/howto-authentication-sms-signin/sign-in-with-phone-number-confirmation-code.png)

1. A felhasználó most felhasználónév vagy jelszó megadása nélkül van bejelentkezve.

## <a name="troubleshoot-sms-based-sign-in"></a>SMS-alapú bejelentkezés hibaelhárítása

A következő forgatókönyvek és hibaelhárítási lépések használhatók, ha problémája van az SMS-alapú bejelentkezés engedélyezésével és használatával.

### <a name="phone-number-already-set-for-a-user-account"></a>A felhasználói fiókhoz már beállított telefonszám

Ha egy felhasználó már regisztrált az Azure AD Multi-Factor Authenticationre és/vagy önkiszolgáló jelszó-visszaállításra (SSPR), már rendelkezik a fiókjához társított telefonszámmal. Ez a telefonszám nem érhető el automatikusan SMS-alapú bejelentkezéshez.

Egy olyan felhasználó, aki már beállított telefonszámmal rendelkezik a fiókjához, megjelenik egy, az *SMS-bejelentkezés* engedélyezése gomb a **Saját profil oldalon.** Válassza ezt a gombot, és a fiók engedélyezve van az SMS-alapú bejelentkezéshez és az előző Azure AD Multi-Factor Authentication- vagy SSPR-regisztrációhoz.

További információ a végfelhasználói élményről: SMS bejelentkezési felhasználói [élmény telefonszámmal.](../user-help/sms-sign-in-explainer.md)

### <a name="error-when-trying-to-set-a-phone-number-on-a-users-account"></a>Hiba egy felhasználó fiókjának telefonszámának beállításakor

Ha hibaüzenetet kap, amikor megpróbál telefonszámot beállítani egy felhasználói fiókhoz a Azure Portal tekintse át a következő hibaelhárítási lépéseket:

1. Győződjön meg arról, hogy engedélyezve van az SMS-alapú bejelentkezés.
1. Ellenőrizze, hogy a felhasználói fiók engedélyezve van-e a *Szöveges üzenet* hitelesítési módszerének szabályzatában.
1. Győződjön meg arról, hogy a telefonszámhoz a megfelelő formázást adja meg, ahogyan az Azure Portal (például *+1 4251234567).*
1. Győződjön meg arról, hogy a telefonszámot nem használják máshol a bérlőn.
1. Ellenőrizze, hogy nincs-e hangszám beállítva a fiókban. Ha a hangszám be van állítva, törölje a adatokat, majd próbálja meg újra a telefonszámot.

## <a name="next-steps"></a>Következő lépések

Az Azure AD-be jelszó nélkül, például az Microsoft Authenticator App vagy a FIDO2 biztonsági kulcsokkal való bejelentkezés további módjaiért lásd: Az [Azure AD][concepts-passwordless]jelszó nélküli hitelesítési lehetőségei.

Az SMS-alapú Microsoft Graph REST API is [][rest-enable] engedélyezheti [][rest-disable] vagy letilthatja a bejelentkezést.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../fundamentals/active-directory-how-subscriptions-associated-directory.md
[concepts-passwordless]: concept-authentication-passwordless.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[tutorial-sspr]: tutorial-enable-sspr.md
[rest-enable]: /graph/api/phoneauthenticationmethod-enablesmssignin?tabs=http
[rest-disable]: /graph/api/phoneauthenticationmethod-disablesmssignin?tabs=http

<!-- EXTERNAL LINKS -->
[azure-portal]: https://portal.azure.com
[office]: https://www.office.com
[m365-firstline-workers-licensing]: https://www.microsoft.com/licensing/news/m365-firstline-workers
[azuread-licensing]: https://azure.microsoft.com/pricing/details/active-directory/
[ems-licensing]: https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing
[m365-licensing]: https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans
[o365-f1]: https://www.microsoft.com/microsoft-365/business/office-365-f1?market=af
[o365-f3]: https://www.microsoft.com/microsoft-365/business/office-365-f3?activetab=pivot%3aoverviewtab

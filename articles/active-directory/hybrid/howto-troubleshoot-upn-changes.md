---
title: Az Azure egyszerű felhasználónevek (UPN) változásainak megtervezése és megoldása
description: Az UPN-változások ismert problémáinak és enyhítésének megismerése
services: active-directory
ms.service: active-directory
ms.subservice: hybrid
ms.topic: how-to
ms.date: 03/13/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19e40d135316c1c7cd270d2804fff1f487937685
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96858535"
---
# <a name="plan-and-troubleshoot-user-principal-name-changes-in-azure-active-directory"></a>Az egyszerű felhasználónevek változásának megtervezése és megoldása Azure Active Directory

Az egyszerű felhasználónév (UPN) egy olyan attribútum, amely a felhasználói fiókok internetes kommunikációs szabványa. Az egyszerű felhasználónév egy UPN-előtagot (a felhasználói fiók nevét) és egy UPN-utótagot (DNS-tartománynevet) tartalmaz. Az előtag a "" szimbólum használatával csatlakozik az utótaghoz \@ . Például: someone@example.com. Az egyszerű felhasználónévnek egyedinek kell lennie az összes rendszerbiztonsági tag objektum között egy címtár-erdőben. 

**Ez a cikk azt feltételezi, hogy az UPN-t használja felhasználói azonosítóként. Az egyszerű felhasználónév megtervezése és az UPN-változások okozta problémák helyreállítása.**

> [!NOTE]
> A fejlesztők számára azt javasoljuk, hogy a felhasználói objectID a nem módosítható azonosítóként használja az egyszerű felhasználónév és az e-mail-címek helyett.


## <a name="learn-about-upns-and-upn-changes"></a>Az egyszerű felhasználónevek és az UPN-változások ismertetése
A bejelentkezési lapok gyakran kérik a felhasználókat, hogy adja meg az e-mail-címüket, ha a szükséges érték valójában az UPN. Ezért ügyeljen arra, hogy a felhasználók UPN-címét bármikor módosítsa az elsődleges e-mail-cím módosításaival.

A felhasználók elsődleges e-mail-címei több okból is változhatnak:

* vállalati alapmárka

* különböző céges részlegekre áthelyezett alkalmazottak 

* összevonások és beszerzések

* alkalmazott nevének módosítása

### <a name="types-of-upn-changes"></a>UPN-változások típusai

Az UPN-t módosíthatja az előtag, az utótag vagy mindkettő módosításával.

* **Az előtag módosítása**.

   *  Ha például egy személy neve módosult, akkor megváltoztathatja a fiók nevét:  
ide BSimon@contoso.comBJohnson@contoso.com

   * Az előtagokhoz is módosíthatja a vállalati szabványokat:  
ide Bsimon@contoso.comBritta.Simon@contoso.com

* **Az utótag módosítása** <br>

    Ha például egy személy módosította a részlegeket, megváltoztathatja a tartományt: 

   * Britta.Simon@contoso.com hogy Britta.Simon@contosolabs.com <br>
     Vagy<br>
    * Britta.Simon@corp.contoso.com hogy Britta.Simon@labs.contoso.com 

Javasoljuk, hogy minden alkalommal módosítsa a felhasználói UPN-t, amikor az elsődleges e-mail-címe frissül.

A Active Directoryról az Azure AD-be történő kezdeti szinkronizálás során győződjön meg arról, hogy a felhasználók e-mail-címe megegyezik az UPN-vel.

### <a name="upns-in-active-directory"></a>UPN-Active Directory

Active Directory az alapértelmezett UPN-utótag annak a tartománynak a DNS-neve, amelyben létrehozta a felhasználói fiókot. A legtöbb esetben ez az a tartománynév, amelyet a vállalati tartományként regisztrál az interneten. Ha a contoso.com tartományban hozza létre a felhasználói fiókot, az alapértelmezett UPN a

username@contoso.com

 Azonban Active Directory tartományok és megbízhatósági kapcsolatok használatával [további UPN-utótagokat adhat hozzá](../fundamentals/add-custom-domain.md) . 

Előfordulhat például, hogy hozzá kívánja adni a labs.contoso.com, és a felhasználók UPN-címét és e-mail-üzenetét tükrözi. Ezután

username@labs.contoso.com.

>[!IMPORTANT]
> Ha [a Active Directory utótagját módosítja](../fundamentals/add-custom-domain.md), akkor győződjön meg arról, hogy az Azure ad-ben hozzá kell adni egy egyező egyéni tartománynevet, [és ellenőrizni](../fundamentals/add-custom-domain.md)kell azt. 

![Az ellenőrzött tartományok képernyőképe](./media/howto-troubleshoot-upn-changes/custom-domains.png)

### <a name="upns-in-azure-active-directory"></a>UPN-Azure Active Directory

A felhasználók bejelentkezhetnek az Azure AD-be a userPrincipalName attribútumában szereplő értékkel. 

Ha az Azure AD-t a helyszíni Active Directory együtt használja, a felhasználói fiókok szinkronizálása a Azure AD Connect szolgáltatás használatával történik. Alapértelmezés szerint a Azure AD Connect varázsló a helyszíni Active Directory userPrincipalName attribútumát használja az Azure AD-beli UPN-ként. Az egyéni telepítés egy másik attribútumára is módosíthatja.

Fontos, hogy egy meghatározott folyamattal rendelkezzen, amikor egyetlen felhasználó egyszerű felhasználónevét (UPN) vagy a teljes szervezetet frissíti. 

Tekintse meg a jelen dokumentum ismert problémáit és megkerülő megoldásait.

Amikor Active Directoryről szinkronizálja a felhasználói fiókokat az Azure AD-be, győződjön meg arról, hogy a Active Directory az Azure AD-ben ellenőrzött tartományokhoz tartozó UPN-ket.

![Képernyőkép, amely példákat mutat be az ellenőrzött Azure A D tartományokra leképezett UPN-címekre.](./media/howto-troubleshoot-upn-changes/verified-domains.png)

Ha a userPrincipalName attribútum értéke nem felel meg egy ellenőrzött tartománynak az Azure AD-ben, a szinkronizálási folyamat az utótagot az alapértelmezett. onmicrosoft.com értékre váltja fel.


### <a name="roll-out-bulk-upn-changes"></a>Tömeges egyszerű felhasználónév módosítása

A [teszteléshez kövesse az ajánlott eljárásokat](../fundamentals/active-directory-deployment-plans.md) a csoportos UPN-módosítások megváltoztatásához. Az egyszerű felhasználónevek visszaállítására szolgáló tesztelt helyreállítási terv is van, ha olyan problémákat tapasztal, amelyek nem oldhatók fel gyorsan. A próbaüzem futtatása után megkezdheti a különböző szervezeti szerepkörökkel rendelkező kis csoportok, valamint az alkalmazások és eszközök speciális készletének megcélzását.

A felhasználók ezen első részhalmaza is jó ötlet, hogy a változás részeként milyen felhasználóknak kellene várniuk. Adja meg ezt az információt a felhasználói kommunikációhoz.

Hozzon létre egy meghatározott eljárást az egyszerű felhasználónevek egyéni felhasználókra való módosításához a normál műveletek részeként. Javasoljuk, hogy olyan tesztelt eljárást tartalmazzon, amely az ismert problémákról és a megkerülő megoldásokról tartalmaz dokumentációt.

A következő részek részletezik a lehetséges ismert problémákat és a megkerülő megoldásokat a UPN-elemek módosításakor.

## <a name="apps-known-issues-and-workarounds"></a>Alkalmazások – ismert problémák és megkerülő megoldások

A [szolgáltatott szoftver (SaaS)](https://azure.microsoft.com/overview/what-is-saas/) és üzletági (LoB) alkalmazások gyakran használják az UPN-ket a felhasználók keresésére és a felhasználói profil adatainak tárolására, beleértve a szerepköröket is. Az igény szerinti [üzembe](../app-provisioning/user-provisioning.md) helyezést használó alkalmazások felhasználói profilt hozhatnak létre, amikor a felhasználók első alkalommal jelentkeznek be az alkalmazásba, az UPN-változások befolyásolhatják.

**Ismert probléma**<br>
A felhasználó UPN-értékének módosítása megszakíthatja a kapcsolatot az Azure AD-felhasználó és az alkalmazáson létrehozott felhasználói profil között. Ha az alkalmazás igény szerinti  [kiépítés alatt](../app-provisioning/user-provisioning.md)áll, létrehozhat egy új, vadonatúj felhasználói profilt. Ehhez szükség lesz az alkalmazás rendszergazdájának, hogy manuális módosításokat hajtson végre a kapcsolat kijavítása érdekében.

**Áthidaló megoldás**<br>
Az [Azure ad automatikus felhasználói üzembe](../app-provisioning/user-provisioning.md) helyezése lehetővé teszi a felhasználói identitások automatikus létrehozását, karbantartását és eltávolítását a támogatott felhőalapú alkalmazásokban. Az automatikus felhasználó-kiépítés konfigurálása az alkalmazásokban automatikusan frissíti az UPN-ket az alkalmazásokban. Tesztelje az alkalmazásokat a fokozatos bevezetés részeként annak ellenőrzéséhez, hogy az UPN-változások nem érintik-e őket.
Ha Ön fejlesztő, érdemes SCIM-támogatást hozzáadnia az [alkalmazáshoz](../app-provisioning/use-scim-to-provision-users-and-groups.md) , hogy lehetővé váljon az automatikus felhasználó-kiépítés Azure Active Directory. 

## <a name="managed-devices-known-issues-and-workarounds"></a>Felügyelt eszközök ismert problémái és megkerülő megoldásai

Az [eszközök Azure ad-be való](../devices/overview.md)bekapcsolásával maximalizálhatja a felhasználók termelékenységét egyszeri bejelentkezéssel (SSO) a felhőben és a helyszíni erőforrásokon keresztül.

### <a name="azure-ad-joined-devices"></a>Azure AD-hez csatlakoztatott eszközök

Az [Azure ad-hez csatlakoztatott](../devices/concept-azure-ad-join.md) eszközök közvetlenül az Azure ad-hez csatlakoznak, és lehetővé teszik a felhasználók számára, hogy a szervezetük identitásával bejelentkezzenek az eszközre.

**Ismert problémák** <br>
A felhasználók egyszeri bejelentkezéssel kapcsolatos problémákat tapasztalhatnak olyan alkalmazásokkal, amelyek az Azure AD-vel való hitelesítéstől függenek.

**Resolution** (Osztás) <br>
Az ebben a szakaszban említett problémák a Windows 10 2020-es frissítésében (2004) is megoldódott.

**Áthidaló megoldás** <br>
Elég idő az egyszerű felhasználónév módosítására az Azure AD-vel való szinkronizáláshoz. Miután meggyőződött arról, hogy az új egyszerű felhasználónév megjelenik az Azure AD-portálon, kérje meg a felhasználót, hogy az új UPN-sel való bejelentkezéshez válassza az "egyéb felhasználó" csempét. A [PowerShell](/powershell/module/azuread/get-azureaduser)használatával is ellenőrizheti. Az új egyszerű felhasználónévvel való bejelentkezés után a régi UPN-re mutató hivatkozások továbbra is megjelennek a "hozzáférés munkahelyi vagy iskolai" Windows-beállításhoz.

![Az ellenőrzött tartományok képernyőképe](./media/howto-troubleshoot-upn-changes/other-user.png)


### <a name="hybrid-azure-ad-joined-devices"></a>Hibrid Azure AD-csatlakoztatott eszközök

A [hibrid Azure ad-hez csatlakoztatott](../devices/concept-azure-ad-join-hybrid.md) eszközök a Active Directory és az Azure ad-hez csatlakoznak. Hibrid Azure AD-csatlakozást is megvalósíthat, ha a környezet helyszíni Active Directory-lábnyommal rendelkezik, és az Azure AD által nyújtott képességek előnyeit is ki szeretné használni.

**Ismert problémák** 

A Windows 10 hibrid Azure AD-hez csatlakoztatott eszközei valószínűleg váratlan újraindítást és hozzáférési problémákat tapasztalnak.

Ha a felhasználók az új UPN-t az Azure AD-be való szinkronizálás előtt jelentkeznek be a Windowsba, vagy továbbra is meglévő Windows-munkamenetet használnak, akkor előfordulhat, hogy egyszeri bejelentkezéssel kapcsolatos problémák jelentkezhetnek az Azure AD-t használó alkalmazásokkal való hitelesítéshez, ha a feltételes hozzáférés úgy van konfigurálva, hogy kikényszerítse a hibrid csatlakoztatott eszközök használatát az erőforrásokhoz 

Emellett a következő üzenet jelenik meg, amely egy perc elteltével újraindítást kényszerít. 

"A számítógép egy percen belül automatikusan újraindul. A Windows hibát észlelt, és újra kell indítani. Most ezt az üzenetet kell bezárnia, és el kell mentenie a munkáját.

**Resolution** (Osztás) <br>
Az ebben a szakaszban említett problémák a Windows 10 2020-es frissítésében (2004) is megoldódott.

**Áthidaló megoldás** 

Az eszközt le kell venni az Azure AD-ből, és újra kell indítani. Az újraindítás után az eszköz ismét automatikusan csatlakozik az Azure AD-hez, és a felhasználónak be kell jelentkeznie az új UPN használatával az "egyéb felhasználó" csempére kattintva. Egy eszköz Azure AD-ból való kikapcsolásához futtassa a következő parancsot a parancssorban:

**dsregcmd /leave**

Ha használatban van, a felhasználónak [újra regisztrálnia](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-whfb-provision) kell a vállalati Windows Hello-t. Ez a probléma nem érinti a Windows 7 és a 8,1 rendszerű eszközöket az UPN módosítása után.


## <a name="microsoft-authenticator-known-issues-and-workarounds"></a>Microsoft Authenticator ismert problémák és megkerülő megoldások

Előfordulhat, hogy a szervezet a [Microsoft Authenticator alkalmazás](../user-help/user-help-auth-app-overview.md) használatával jelentkezik be, és hozzáfér a szervezeti alkalmazásokhoz és az adataihoz. Bár a Felhasználónév megjelenhet az alkalmazásban, a fiók nincs beállítva ellenőrzési módszerként, amíg a felhasználó befejezi a regisztrációs folyamatot.

A [Microsoft Authenticator alkalmazásnak](../user-help/user-help-auth-app-overview.md) négy fő funkciója van:

* Multi-Factor Authentication leküldéses értesítés vagy ellenőrző kód használatával

* Az iOS-és Android-eszközök hitelesítési közvetítője, hogy egyszeri bejelentkezést biztosítson a felügyelt [hitelesítést](../develop/msal-android-single-sign-on.md) használó alkalmazásokhoz

* Az eszköz regisztrálása (más néven Workplace Join) az Azure AD-hez, amely az olyan egyéb funkciókra vonatkozik, mint a Intune App Protection és az eszközök regisztrációja/kezelése.

* A telefonos bejelentkezéshez az MFA és az eszköz regisztrálása szükséges.

### <a name="multi-factor-authentication-with-android-devices"></a>Multi-Factor Authentication Android-eszközökkel

A Microsoft Authenticator alkalmazás sávon kívüli ellenőrzési lehetőséget kínál. Ahelyett, hogy a bejelentkezés során automatikus telefonhívást vagy SMS-t helyezzen a felhasználónak, [multi-Factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md) értesítést küld a felhasználó okostelefonján vagy táblaszámítógépén található Microsoft Authenticator alkalmazásnak. A felhasználó egyszerűen koppintson a jóváhagyásra (vagy egy PIN-kódot vagy biometrikus értéket ad meg, és a "hitelesítés") az alkalmazásban a bejelentkezés befejezéséhez.

**Ismert problémák** 

Amikor módosít egy felhasználó egyszerű felhasználónevét, a régi UPN továbbra is megjelenik a felhasználói fiókban, és előfordulhat, hogy az értesítés nem érkezik meg. Az [ellenőrző kódok](../user-help/user-help-auth-app-faq.md) továbbra is működőképesek.

**Áthidaló megoldás**

Ha értesítést kap, utasítsa a felhasználót, hogy zárja be az értesítést, nyissa meg a hitelesítő alkalmazást, koppintson az "értesítések keresése" lehetőségre, és hagyja jóvá az MFA-kérést. Ezt követően a rendszer frissíti a fiókban megjelenő egyszerű felhasználónevet. Megjegyzés: Előfordulhat, hogy a frissített UPN új fiókként jelenik meg, mert más hitelesítő funkció is használatban van. További információkért tekintse meg a jelen cikk további ismert problémáit.

### <a name="brokered-authentication"></a>Felügyelt hitelesítés

Android és iOS rendszerű brókereken, például a Microsoft Authenticator engedélyezéséhez:

* Egyszeri bejelentkezés (SSO) – a felhasználóknak nem kell bejelentkezniük minden alkalmazásba.

* Eszköz azonosítása – a közvetítő hozzáfér az eszközön létrehozott tanúsítványhoz, amikor a munkahely csatlakoztatva volt.

* Alkalmazás-azonosítás ellenőrzése – amikor egy alkalmazás meghívja a közvetítőt, átadja az átirányítási URL-címet, és a közvetítő ellenőrzi.

Emellett lehetővé teszi az alkalmazások számára, hogy olyan speciális funkciókban vegyenek részt, mint a [feltételes hozzáférés](../conditional-access/index.yml), és támogatja a [Microsoft Intune forgatókönyveket](../develop/msal-net-use-brokers-with-xamarin-apps.md).

**Ismert problémák**<br>
A felhasználó interaktív hitelesítési kéréseket jelenít meg a közvetítő által támogatott bejelentkezést használó új alkalmazásokban, az alkalmazás által átadott login_hint és a brókeren tárolt egyszerű felhasználónév közötti eltérések miatt.

**Áthidaló megoldás** <br> A felhasználónak manuálisan kell eltávolítania a fiókot a Microsoft Authenticatorból, és egy új bejelentkezést kell elindítania egy közvetítő által támogatott alkalmazásból. A rendszer automatikusan hozzáadja a fiókot a kezdeti hitelesítés után.

### <a name="device-registration"></a>Eszközregisztráció

A Microsoft Authenticator alkalmazás feladata az eszköz regisztrálása az Azure AD-ben. Az eszköz regisztrálása lehetővé teszi az eszköz számára, hogy hitelesítse magát az Azure AD-ben, és a következő esetekben követelmény:

* Intune-alkalmazásvédelem

* Intune-eszközök regisztrációja

* Telefonos bejelentkezés

**Ismert problémák**<br>
Amikor megváltoztatja az UPN-t, megjelenik egy új, az új egyszerű felhasználónévvel rendelkező fiók a Microsoft Authenticator alkalmazásban, miközben a régi UPN-vel rendelkező fiók továbbra is szerepel. Emellett a régi UPN is megjelenik az eszközbeállítások szakaszban az alkalmazás beállításainál. Az eszközök regisztrációjának vagy a függő forgatókönyveknek a szokásos funkciója nem változik.

**Áthidaló megoldás** <br> Ha el szeretné távolítani az összes, a régi egyszerű felhasználónévre mutató hivatkozást a Microsoft Authenticator alkalmazásban, utasítsa a felhasználót, hogy manuálisan távolítsa el a régi és az új fiókot a Microsoft Authenticatorről, regisztrálja újra az MFA-t, majd csatlakoztassa újra az eszközt.

### <a name="phone-sign-in"></a>Telefonos bejelentkezés

A telefonos bejelentkezés lehetővé teszi, hogy a felhasználók jelszó nélkül bejelentkezzenek az Azure AD-be. A telefonos bejelentkezés engedélyezéséhez a felhasználónak regisztrálnia kell az MFA-t a hitelesítő alkalmazás használatával, majd engedélyeznie kell a telefonos bejelentkezést közvetlenül a hitelesítő eszközön. A konfiguráció részeként az eszköz regisztrálva van az Azure AD-ben.

**Ismert problémák** <br>
A felhasználók nem tudják használni a telefonos bejelentkezést, mert nem kapnak értesítést. Ha a felhasználó a riasztások keresésekor felkeresi az értesítéseket, hibaüzenetet kap.

**Áthidaló megoldás**<br>
A felhasználónak ki kell választania a legördülő menüt a telefonos bejelentkezéshez engedélyezett fiókban, és válassza a telefonos bejelentkezés letiltása lehetőséget. Ha szükséges, a telefonos bejelentkezés újra engedélyezhető.

## <a name="security-key-fido2-known-issues-and-workarounds"></a>Biztonsági kulcs (FIDO2) – ismert problémák és megkerülő megoldások

**Ismert problémák** <br>
Ha több felhasználó van regisztrálva ugyanazon a kulcson, a bejelentkezési képernyő egy fiók kiválasztása lapot jelenít meg, ahol a régi UPN jelenik meg. A biztonsági kulcsokat használó bejelentkezéseket az UPN-változások nem érintik.  

**Áthidaló megoldás**<br>
A régi UPN-re mutató hivatkozások eltávolításához [a felhasználóknak alaphelyzetbe kell állítania a biztonsági kulcsot, és újra regisztrálnia](../authentication/howto-authentication-passwordless-security-key.md#known-issues)kell.

## <a name="onedrive-known-issues-and-workarounds"></a>OneDrive ismert problémák és megkerülő megoldások

A OneDrive-felhasználók számára ismert, hogy az UPN módosítása után problémákat tapasztalnak. További információ: [az UPN-változások hatása a OneDrive URL-címére és a OneDrive szolgáltatásaira](/onedrive/upn-changes).

## <a name="next-steps"></a>Következő lépések

Tekintse meg ezeket az erőforrásokat:
* [Azure AD Connect: tervezési fogalmak](./plan-connect-design-concepts.md)

* [Az Azure AD UserPrincipalName feltöltése](./plan-connect-userprincipalname.md)

* [Microsoft Identity platform azonosító tokenek](../develop/id-tokens.md)

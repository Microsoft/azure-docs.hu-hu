---
title: Jelszóval nem rendelkező hitelesítés központi telepítésének megtervezése az Azure AD-vel
description: Megtudhatja, hogyan tervezhet és helyezhet üzembe egy Azure Active Directory jelszó nélküli hitelesítés megvalósítását
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: baselden
author: justinha
manager: daveba
ms.reviewer: baselden, librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d4ff717a49ba9c9b4d66e54e5eae1248af3fcea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579365"
---
# <a name="plan-a-passwordless-authentication-deployment-in-azure-active-directory"></a>Jelszóval nem rendelkező hitelesítés központi telepítésének megtervezése Azure Active Directory

> [!NOTE]
> A központi telepítési terv offline verziójának létrehozásához használja a böngésző Nyomtatás PDF-funkcióját.

A legtöbb Cyber-támadás megsérült felhasználónévvel és jelszóval kezdődik. A szervezetek megpróbálják elhárítani a fenyegetést azzal, hogy a felhasználóknak az alábbi módszerek egyikét kell használniuk:

- Hosszú jelszavak
- Összetett jelszavak
- Gyakori jelszó-változtatások
- Multi-factor authentication (MFA)

A Microsoft [kutatásai azt mutatják](https://aka.ms/passwordguidance) , hogy ezek a felhasználók bántják a felhasználókat és felgyorsítják a támogatási költségeket. További információ: [a PA $ $Word nem számít](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

### <a name="benefits-of-passwordless-authentication"></a>A jelszóval nem rendelkező hitelesítés előnyei

- **Fokozott biztonság**. A jelszavak támadási felületen való eltávolításával csökkentheti az adathalászat és a jelszó-szórás elleni támadások kockázatát.
-  **Jobb felhasználói élmény**. Lehetővé teszik a felhasználók számára, hogy bárhonnan hozzáférjenek az adatokhoz. Egyszerű hozzáférést biztosíthat az alkalmazásokhoz és szolgáltatásokhoz, például az Outlookhoz, a OneDrive-hoz vagy az Office-hoz.
-  **Robusztus bepillantást nyerhet**. Betekintést nyerhet a felhasználók jelszavas tevékenységeibe robusztus naplózással és naplózással.

A jelszóval együtt a jelszót a rendszer lecseréli egy olyan elemre, amelyet Ön vagy amit ismer. A vállalati Windows Hello például olyan biometrikus kézmozdulatokat használhat, mint például az arc vagy az ujjlenyomat, vagy egy eszközre jellemző PIN-kód, amely nincs hálózaton keresztül továbbítva.

## <a name="passwordless-authentication-methods"></a>Jelszóval nem rendelkező hitelesítési módszerek
A Microsoft három, több forgatókönyvre kiterjedő, jelszóval nem rendelkező hitelesítési lehetőséget kínál. Ezek a módszerek párhuzamosan használhatók:

- A [vállalati Windows Hello](./concept-authentication-passwordless.md) a legjobb a dedikált Windows-számítógépeken lévő felhasználók számára.
- A biztonsági kulcs [FIDO2 biztonsági kulcsaival](./concept-authentication-passwordless.md) való bejelentkezés különösen hasznos azoknak a felhasználóknak, akik megosztott gépekbe, például kioszkokhoz jelentkeznek be, olyan helyzetekben, ahol a telefonok használata korlátozott, és magas jogosultsági szintű identitások esetén.
- A [Microsoft Authenticator alkalmazással](./concept-authentication-passwordless.md) való telefonos bejelentkezés hasznos lehet a mobileszközök felhasználói számára a jelszó nélküli beállítás biztosításához. A hitelesítő alkalmazás minden iOS-vagy Android-telefont erős, jelszó nélküli hitelesítő adatokra vált, mivel lehetővé teszi a felhasználók számára, hogy bármilyen platformra vagy böngészőbe bejelentkezzenek. A felhasználók bejelentkezhetnek, ha értesítést küldenek a telefonjára, és a képernyőn megjelenő számot a telefonján megjelenő telefonszámra, majd a biometrikus adatok vagy PIN-kód használatával megerősítik.

### <a name="passwordless-authentication-scenarios"></a>Jelszóval nem rendelkező hitelesítési forgatókönyvek

A Microsoft jelszavas hitelesítési módszerei különböző forgatókönyveket tesznek lehetővé. Vegye figyelembe a szervezeti igényeket, előfeltételeket és az egyes hitelesítési módszerek képességeit a jelszóval nem rendelkező hitelesítési stratégia kiválasztásához. Javasoljuk, hogy minden Windows 10-es eszközt használó szervezet a vállalati Windows Hello szolgáltatást használja. Ezután adja hozzá a telefonos bejelentkezést (a Microsoft Authenticator alkalmazással) vagy a biztonsági kulcsokat a további forgatókönyvekhez.

| Eset | Telefonos hitelesítés | Biztonsági kulcsok | Vállalati Windows Hello |
| --- | --- | --- | --- |
| **Számítógép bejelentkezés**: <br> Hozzárendelt Windows 10-es eszközről | **Nem** | **Igen** <br> Biometrikus kóddal | **Igen**<br>biometrikus felismeréssel és PIN-kóddal |
| **Számítógép bejelentkezés**: <br> Megosztott Windows 10-es eszközről | **Nem** | **Igen** <br> Biometrikus kóddal  | **Nem** |
| **Webalkalmazás-bejelentkezés**: <br>felhasználó által dedikált számítógépről | **Igen** | **Igen** <br> A megadott egyszeri bejelentkezés lehetővé teszi az alkalmazások számára a számítógép-bejelentkezést | **Igen**<br> A megadott egyszeri bejelentkezés lehetővé teszi az alkalmazások számára a számítógép-bejelentkezést |
| **Webalkalmazás-bejelentkezés**: <br> mobil vagy nem Windows rendszerű eszközről | **Igen** | **Nem** | **Nem** |
| **Számítógép bejelentkezés**: <br> Nem Windows rendszerű számítógép | **Nem** | **Nem** | **Nem** |

További információ a szervezet legjobb módszerének kiválasztásáról: [a jelszóval nem rendelkező metódusok meghatározása](./concept-authentication-passwordless.md#choose-a-passwordless-method).

## <a name="prerequisites"></a>Előfeltételek

A szervezeteknek a következő előfeltételeknek kell megfelelniük a jelszóval nem rendelkező üzembe helyezés megkezdése előtt:

| Előfeltétel | Hitelesítő alkalmazás | FIDO2 biztonsági kulcsok |
| --- | --- | --- |
| Az [Azure AD multi-Factor Authentication és az önkiszolgáló jelszó-visszaállítás (SSPR) együttes regisztrálása](howto-registration-mfa-sspr-combined.md) engedélyezve van | √ | √ |
| [A felhasználók elvégezhetik az Azure AD Multi-Factor Authentication](howto-mfa-getstarted.md) | √ | √ |
| [A felhasználók regisztrálva vannak az Azure AD Multi-Factor Authentication és a SSPR](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [A felhasználók regisztrálták a mobileszközök Azure Active Directory](../devices/overview.md) | √ |   |
| Windows 10 1809-es vagy újabb verzió egy támogatott böngészőben, például a Microsoft Edge vagy a Mozilla Firefox használatával <br> (67-es vagy újabb verzió). <br> *A Microsoft natív támogatás esetén a 1903-es vagy újabb verziót javasolja*. |   | √ |
| Kompatibilis FIDO2 biztonsági kulcsok. Győződjön meg arról, hogy [Microsoft által tesztelt és ellenőrzött](./concept-authentication-passwordless.md) FIDO2 biztonsági eszközt vagy más kompatibilis FIDO2 biztonsági eszközt használ. |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>A vállalati Windows Hello használatának előfeltételei

A Windows Hello előfeltételei nagymértékben függenek attól, hogy helyszíni, hibrid vagy kizárólag felhőalapú konfigurációban végez-e üzembe helyezést. További információkért tekintse meg a [vállalati Windows Hello előfeltételeinek teljes listáját](/windows/security/identity-protection/hello-for-business/hello-identity-verification).

### <a name="azure-ad-multi-factor-authentication"></a>Azure AD többtényezős hitelesítés

A felhasználók az Azure AD Multi-Factor Authentication regisztrációs folyamat részeként regisztrálják a jelszóval nem rendelkező metódust. A többtényezős hitelesítés a felhasználónévvel és a jelszóval együtt egy másik regisztrált módszerrel is használható tartalékként, ha bizonyos helyzetekben nem tudják használni a telefonját vagy a biztonsági kulcsát.

### <a name="licensing"></a>Licencek 
A jelszó nélküli hitelesítéshez nem kell további díjat fizetni, bár bizonyos előfeltételek esetén prémium előfizetés szükséges. A [Azure Active Directory licencelése lapon](https://azure.microsoft.com/pricing/details/active-directory/)található részletes funkció-és licencelési információk. 

## <a name="develop-a-plan"></a>Terv kidolgozása

Vegye figyelembe az üzleti igényeket és az egyes hitelesítési módszerek használati eseteit. Ezután válassza ki az igényeinek leginkább megfelelő módszert.

### <a name="use-cases"></a>Használati esetek

Az alábbi táblázat a projekt során megvalósítandó használati eseteket ismerteti.

| Terület | Description |
| --- | --- |
| **Hozzáférés** | A jelszó nélküli bejelentkezés a vállalati hálózaton belüli vagy kívüli vállalati vagy személyes eszközről érhető el. |
| **Naplózás** | A használati adatok a rendszergazdák számára a közel valós időben történő naplózáshoz érhetők el. <br> A használati adatokat legalább 29 naponta letölti a vállalati rendszerbe, vagy az SIEM eszközt használja. |
| **Szabályozás** | A megfelelő hitelesítési módszerhez és a társított csoportokhoz tartozó felhasználói hozzárendelések életciklusa definiálva és figyelve van. |
| **Biztonság** | A megfelelő hitelesítési módszerhez való hozzáférés a felhasználók és csoportok hozzárendelésein keresztül vezérelhető. <br> Csak a jogosultsággal rendelkező felhasználók használhatják a jelszó nélküli bejelentkezést. |
| **Teljesítmény** | A hozzáférési hozzárendelések terjesztési ütemterveit dokumentálják és figyelik. <br> A bejelentkezési idő mérése egyszerű használat érdekében történik. |
| **Felhasználói élmény** | A felhasználók tisztában vannak a mobil kompatibilitással. <br> A felhasználók megadhatják a hitelesítő alkalmazás jelszavas nélküli bejelentkezését. |
| **Támogatás** | A felhasználók tisztában vannak azzal, hogy miként találhatják meg a jelszó nélküli bejelentkezési problémák támogatását. |

### <a name="engage-the-right-stakeholders"></a>A megfelelő résztvevők bevonása

A technológiai projektek meghibásodása esetén általában a hatás, az eredmények és a felelősségi körök eltérő elvárásai vannak. A buktatók elkerülése érdekében [Győződjön meg arról, hogy a megfelelő érintett feleket folytatja](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders) , és hogy a projektben szereplő, az érintett felek szerepkörei jól megértettek.

### <a name="plan-communications"></a>A kommunikáció tervezése

A kommunikáció minden új szolgáltatás sikeressége szempontjából kritikus fontosságú. Proaktív módon közli, hogy a felhasználók hogyan változnak, Mikor változnak, és hogyan szerezhet támogatást, ha problémákat tapasztalnak.

A végfelhasználók felé irányuló kommunikációnak a következő információkat kell tartalmaznia:

- [A kombinált biztonsági regisztráció használatának engedélyezése](howto-authentication-passwordless-phone.md)
- [A Microsoft Authenticator alkalmazás letöltése](../user-help/user-help-auth-app-download-install.md)
- [Regisztrálás a Microsoft Authenticator alkalmazásban](howto-authentication-passwordless-phone.md)
- [Bejelentkezés telefonnal](../user-help/user-help-auth-app-sign-in.md)

A Microsoft többtényezős hitelesítési [kommunikációs sablonokat](https://aka.ms/mfatemplates), Self-Service jelszó-visszaállítási (SSPR) [kommunikációs sablonokat](https://www.microsoft.com/download/details.aspx?id=56768)és a [végfelhasználói dokumentációt](../user-help/security-info-setup-signin.md) biztosít a kommunikáció kialakításához. [https://myprofile.microsoft.com](https://myprofile.microsoft.com/)Az oldalon található **biztonsági információk** hivatkozásaira kattintva a felhasználók közvetlenül regisztrálhatnak.

### <a name="plan-to-pilot"></a>Tervezés a pilóta számára

A jelszó nélküli hitelesítés telepítésekor először engedélyeznie kell egy vagy több kísérleti csoportot. A kifejezetten erre a célra szolgáló [csoportokat hozhat létre](../fundamentals/active-directory-groups-create-azure-portal.md) . Adja hozzá azokat a felhasználókat, akik részt vesznek a pilótában a csoportokban. Ezután engedélyezze az új, jelszóval nem rendelkező hitelesítési módszereket a kiválasztott csoportokhoz.

A csoportok a helyszíni címtárból vagy az Azure AD-ből is szinkronizálhatók. Ha elégedett a pilóta eredményeivel, átválthatja a jelszó nélküli hitelesítést az összes felhasználóra.

Tekintse meg az [ajánlott eljárásokat a pilóta számára](../fundamentals/active-directory-deployment-plans.md) a központi telepítési tervek lapon.

## <a name="plan-passwordless-authentication-with-the-microsoft-authenticator-app"></a>Jelszóval nem rendelkező hitelesítés tervezése a Microsoft Authenticator alkalmazással

A Microsoft Authenticator alkalmazás ingyenesen letölthető a Google Play-ből vagy az Apple App Store áruházból. [További információ a Microsoft Authenticator alkalmazás letöltéséről](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6). A felhasználók letöltik a Microsoft Authenticator alkalmazást. és kövesse az utasításokat a telefonos bejelentkezés engedélyezéséhez. 

Minden iOS-vagy Android-telefont erős, jelszó nélküli hitelesítő adatba kapcsol. A felhasználók bármely platformra vagy böngészőbe bejelentkezhetnek, ha értesítést küldenek a telefonjára, és a képernyőn megjelenő számot a telefonján lévő egy számmal látják el, majd a biometria vagy a PIN-kód segítségével megerősítik. [Tekintse meg a Microsoft Authenticator alkalmazás működésének részleteit](./concept-authentication-passwordless.md#microsoft-authenticator-app).

![Bejelentkezés a hitelesítő alkalmazással](./media/howto-authentication-passwordless-deployment/passwordless-dp-sign-in.png)

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>Technikai megfontolások a Microsoft Authenticator alkalmazáshoz

**AD FS integráció** – ha egy felhasználó engedélyezi a jelszóval nem rendelkező Microsoft Authenticator hitelesítő adatokat, akkor az adott felhasználó számára a hitelesítés jóváhagyás céljából elküld egy értesítést. A hibrid bérlőben lévő felhasználókat a rendszer nem irányítja át az ADFS-be a bejelentkezéshez, kivéve, ha a "jelszó használata helyett" lehetőséget választja. Ez a folyamat megkerüli a helyszíni feltételes hozzáférési szabályzatokat és a továbbított hitelesítési folyamatokat is. Ha azonban *login_hint* van megadva, a rendszer továbbítja a felhasználót az ADFS-nek, és megkerüli a jelszóval nem rendelkező hitelesítő adatok használatára vonatkozó lehetőséget.

A többtényezős hitelesítéshez egy szervezet helyszíni Azure MFA-kiszolgálóján keresztül engedélyező **Azure AD multi-Factor Authentication kiszolgáló** -végfelhasználók egyetlen jelszó nélküli telefonos bejelentkezési hitelesítő adatot hozhatnak létre és használhatnak. Ha a felhasználó a hitelesítő adatokkal rendelkező Microsoft Authenticator több telepítésének (5 vagy több) frissítését kísérli meg, akkor ez a változás hibát eredményezhet.

**Eszköz regisztrálása** – a hitelesítő alkalmazás jelszavas hitelesítésre való használatához regisztrálni kell az eszközt az Azure ad-bérlőben, és nem lehet megosztott eszköz. Az eszközök csak egyetlen bérlőben regisztrálhatók. Ez a korlát azt jelenti, hogy csak egy munkahelyi vagy iskolai fiók támogatott a telefonos bejelentkezéshez a hitelesítő alkalmazás használatával.

## <a name="plan-passwordless-authentication-with-fido2-security-keys"></a>Jelszó-alapú hitelesítés tervezése FIDO2 biztonsági kulcsokkal
A biztonsági kulcsokkal háromféle jelszó nélküli bejelentkezési üzemelő példány érhető el:

-    Webalkalmazások Azure Active Directory támogatott böngészőben
-    Azure Active Directory csatlakoztatott Windows 10-es eszközök
-    Hibrid Azure Active Directory csatlakoztatott Windows 10-es eszközök
     -    Hozzáférést biztosít a felhőalapú és a helyszíni erőforrásokhoz is. További információ a helyszíni erőforrásokhoz való hozzáférésről: [egyszeri bejelentkezés a helyszíni erőforrásokhoz FIDO2-kulcsok használatával](./howto-authentication-passwordless-security-key-on-premises.md)

Engedélyeznie kell a **kompatibilis FIDO2 biztonsági kulcsokat**. A Microsoft bejelentette [a FIDO2 Key vendors-vel való együttműködését](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Microsoft-passwordless-partnership-leads-to-innovation-and-great/ba-p/566493).

**Az Azure ad Web Apps és az Azure ad Windows rendszerhez csatlakoztatott eszközök esetében**:

-    Windows 10 1809-es vagy újabb verzió egy támogatott böngészővel, például a Microsoft Edge vagy a Mozilla Firefox (67-es vagy újabb verzió) használatával. 
-    A Windows 10 1809-es verziója támogatja a FIDO2 való bejelentkezést, és szükség lehet a FIDO2 Key gyártótól származó szoftverek telepítésére. Javasoljuk, hogy a 1903-es vagy újabb verziót használja. 

**Hibrid Azure Active Directory tartományhoz csatlakoztatott eszközök esetén**: 
-    Windows 10 2004-es vagy újabb verzió
-    A Windows Server 2016-es vagy 2019-es verzióját futtató, teljes mértékben javított tartományi kiszolgálók.
-    A Azure AD Connect legújabb verziója

A követelmények teljes listáját lásd: a [jelszó nélküli biztonsági kulcs bejelentkezésének engedélyezése a Windows 10-es eszközökre Azure Active Directory használatával](./howto-authentication-passwordless-security-key-windows.md#requirements).


### <a name="security-key-life-cycle"></a>Biztonsági kulcs életciklusa

A biztonsági kulcsok lehetővé teszik az erőforrások elérését, és meg kell terveznie ezeknek a fizikai eszközöknek a felügyeletét.

1. **Kulcs terjesztése**: Tervezze meg, hogyan kell kulcsokat kiépíteni a szervezet számára. Lehet, hogy központosított kiépítési folyamattal rendelkezik, vagy lehetővé teszi a végfelhasználók számára, hogy a 2,0-kompatibilis kulcsokat vásároljanak.
1. **Kulcs aktiválása**: a végfelhasználóknak saját maguknak kell aktiválniuk a biztonsági kulcsot. A végfelhasználók az első használat során regisztrálják a biztonsági kulcsaikat [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) , és engedélyezik a második tényezőt (PIN-kódot vagy biometrikus adatokat).
1. **Kulcs letiltása**: a biztonsági kulcs funkciója az előzetes verzióban van, így a rendszergazda nem távolíthat el kulcsot egy felhasználói fiókból. A felhasználónak el kell távolítania. A kulcs elvesztése vagy ellopása esetén:
   1. Távolítsa el a felhasználót bármely olyan csoportból, amely engedélyezve van a jelszó nélküli hitelesítéshez.
   1. Ellenőrizze, hogy eltávolította-e a kulcsot hitelesítési módszerként.
   1. Adjon ki egy új kulcsot. **Kulcs cseréje**: a felhasználók egyszerre két biztonsági kulcsot engedélyezhetik. Egy biztonsági kulcs cseréjekor győződjön meg arról, hogy a felhasználó eltávolította a lecserélt kulcsot is.

### <a name="enable-windows-10-support"></a>A Windows 10-es támogatásának engedélyezése

A Windows 10 FIDO2 biztonsági kulcsokkal történő bejelentkezésének engedélyezéséhez engedélyezni kell a hitelesítő adatok szolgáltatójának működését a Windows 10 rendszerben. Válasszon egyet az alábbi lehetőségek közül:

- [Hitelesítőadat-szolgáltató engedélyezése az Intune-nal](howto-authentication-passwordless-security-key-windows.md#enable-with-intune)
   - Az Intune üzembe helyezése az ajánlott lehetőség.
- [Hitelesítőadat-szolgáltató engedélyezése kiépítési csomaggal](howto-authentication-passwordless-security-key-windows.md#enable-with-a-provisioning-package)
   - Ha az Intune üzembe helyezése nem lehetséges, a rendszergazdáknak minden gépen telepíteniük kell egy csomagot, hogy engedélyezze a hitelesítő adatok szolgáltatójának működőképességét. A csomag telepítését a következő lehetőségek egyikével végezheti el:
      - Csoportházirend vagy Configuration Manager
      - Helyi telepítés Windows 10 rendszerű gépen
- [Hitelesítőadat-szolgáltató engedélyezése Csoportházirend](howto-authentication-passwordless-security-key-windows.md#enable-with-group-policy)
   - Csak hibrid Azure AD-hez csatlakoztatott eszközök esetén támogatott.

#### <a name="enable-on-premises-integration"></a>Helyszíni integráció engedélyezése

A helyszíni erőforrásokhoz való hozzáférés engedélyezéséhez kövesse a [jelszó nélküli biztonsági kulcs bejelentkezésének engedélyezése a helyszíni erőforrásokhoz](howto-authentication-passwordless-security-key-on-premises.md)című témakör lépéseit.

> [!IMPORTANT]
> Ezeket a lépéseket minden hibrid Azure AD-hez csatlakoztatott eszközhöz is el kell végezni, hogy FIDO2 biztonsági kulcsokat lehessen használni a Windows 10-es bejelentkezéshez.

### <a name="register-security-keys"></a>Biztonsági kulcsok regisztrálása

A felhasználóknak regisztrálniuk kell a biztonsági kulcsot minden Azure Active Directory csatlakoztatott Windows 10 rendszerű gépen.

További információ: [a FIDO2 biztonsági kulcsainak felhasználói regisztrálása és kezelése](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys).


## <a name="plan-auditing-security-and-testing"></a>A naplózás, a biztonság és a tesztelés tervezése
A szervezeti és megfelelőségi keretrendszereknek megfelelő naplózás megtervezése elengedhetetlen része az üzembe helyezésnek.

### <a name="auditing-passwordless"></a>Jelszavas naplózás

Az Azure AD olyan jelentéseket tartalmaz, amelyek technikai és üzleti ismereteket biztosítanak. Az üzleti és a műszaki alkalmazások tulajdonosai a szervezet igényeinek megfelelően feltételezik és használják ezeket a jelentéseket.

A **hitelesítési** módszerek szakasz a Azure Active Directory-portálon belül, ahol a rendszergazdák engedélyezhetik és kezelhetik a jelszó nélküli hitelesítő adatok beállításait.

Az Azure AD bejegyzéseket ad hozzá a naplókhoz a következő esetekben:

- A rendszergazdák módosításokat hajtanak végre a hitelesítési módszerek szakaszban.
- A felhasználó Azure Active Directoryon belül bármilyen módosítást végez a hitelesítő adatain.

A következő táblázat néhány példát mutat be a tipikus jelentéskészítési forgatókönyvekre:

|   | A kockázatok kezelése | Termelékenység növelése | Cégirányítás és megfelelőség |
| --- | --- | --- | --- |
| **Jelentéstípusok** | Hitelesítési módszerek – a kombinált biztonsági regisztrációhoz regisztrált felhasználók | Hitelesítési módszerek – alkalmazás-értesítéshez regisztrált felhasználók | Bejelentkezések: Tekintse át, ki fér hozzá a bérlőhöz, és hogyan |
| **Lehetséges műveletek** | A célként még nem regisztrált felhasználók | Microsoft Authenticator alkalmazás vagy biztonsági kulcsok átvétele | Hozzáférés visszavonása vagy további biztonsági házirendek betartatása a rendszergazdák számára |

Az **Azure ad a legtöbb naplózási adatait 30 napra megőrzi** , és az Azure felügyeleti portálon vagy API-n keresztül elérhetővé teszi az adatait az Analysis Systems-be való letöltéshez. Ha nagyobb adatmegőrzésre van szüksége, a naplók exportálása és felhasználása SIEM-eszközön, például az [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), a splunk vagy a Sumo Logic. [További információ a hozzáférési és használati jelentések megtekintéséről](../reports-monitoring/overview-reports.md).

A felhasználók regisztrálhatják és kezelhetik a hitelesítő adataikat a alkalmazásban való navigálással [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) . Ez a hivatkozás a felhasználókat arra a végfelhasználói hitelesítőadat-kezelési felületre irányítja, amely a kombinált SSPR/multi-Factor Authentication regisztrációs felületén keresztül lett engedélyezve. Az Azure AD a FIDO2 biztonsági eszközeinek regisztrációját és a hitelesítési módszerek felhasználó általi módosítását írja le.

### <a name="plan-security"></a>A biztonság megtervezése
A bevezetési terv részeként a Microsoft azt javasolja, hogy a jelszó nélküli hitelesítés minden Kiemelt rendszergazdai fiók esetében engedélyezve legyen.

Ha a felhasználók engedélyezik vagy letiltják a fiókot egy biztonsági kulcson, vagy a Windows 10-es gépen lévő biztonsági kulcs második tényezőjét alaphelyzetbe állítják, egy bejegyzést adnak hozzá a biztonsági naplóhoz, és a következő eseményazonosító szerepelnek: *4670* és *5382*.

### <a name="plan-testing"></a>Tesztelési terv

Az üzembe helyezés minden egyes fázisában, a forgatókönyvek és az elfogadás során ellenőrizze, hogy az eredmények a várt módon működnek-e.

#### <a name="testing-the-microsoft-authenticator-app"></a>A Microsoft Authenticator alkalmazás tesztelése

Az alábbiakban a Microsoft Authenticator alkalmazással való jelszavas hitelesítésre vonatkozó példákat vizsgáljuk:

| Eset | Várt eredmények |
| --- | --- |
| A felhasználó regisztrálhat Microsoft Authenticator alkalmazást | A felhasználó regisztrálhatja az alkalmazást a aka.ms/mysecurityinfo |
| A felhasználó engedélyezheti a telefonos bejelentkezést | A munkahelyi fiókhoz konfigurált telefonos bejelentkezés |
| A felhasználó elérheti az alkalmazást telefonos bejelentkezéssel | A felhasználó a telefonos bejelentkezési folyamaton keresztül éri el az alkalmazást. |
| A telefonos bejelentkezés regisztrációjának tesztelése a Azure Active Directory portál hitelesítési módszerek képernyőjén a jelszó nélküli bejelentkezés Microsoft Authenticator kikapcsolásával | A korábban engedélyezett felhasználók nem tudják használni a jelszó nélküli bejelentkezést Microsoft Authenticator. |
| Telefonos bejelentkezés eltávolítása Microsoft Authenticator alkalmazásból | A munkahelyi fiók már nem érhető el Microsoft Authenticator |

#### <a name="testing-security-keys"></a>Biztonsági kulcsok tesztelése

A következő példák tesztelési eseteket biztosítanak a biztonsági kulcsokkal való jelszavas hitelesítéshez.

**Jelszó nélküli, Windows 10-es eszközökhöz csatlakozó Azure Active Directory**

| Eset | Várt eredmények |
| --- | --- |
| A felhasználó regisztrálhat FIDO2 eszközt (1809) | A felhasználó regisztrálhat FIDO2-eszközt a beállítások > fiókok > bejelentkezési beállítások > biztonsági kulcs |
| A felhasználó alaphelyzetbe állíthatja a FIDO2 eszközt (1809) | A felhasználó alaphelyzetbe állíthatja a FIDO2 eszközt gyártó szoftverrel |
| A felhasználó bejelentkezhet a FIDO2 eszközzel (1809) | A felhasználó a bejelentkezési ablakból választhatja ki a biztonsági kulcsot, és sikeresen bejelentkezhet. |
| A felhasználó regisztrálhat FIDO2 eszközt (1903) | A felhasználó regisztrálhat FIDO2-eszközt a beállítások > fiókok > bejelentkezési beállítások > biztonsági kulcs |
| A felhasználó alaphelyzetbe állíthatja a FIDO2 eszközt (1903) | A felhasználó alaphelyzetbe állíthatja a FIDO2 eszközt a beállítások > fiókok > bejelentkezési beállítások > biztonsági kulcs |
| A felhasználó bejelentkezhet a FIDO2 eszközzel (1903) | A felhasználó a bejelentkezési ablakból választhatja ki a biztonsági kulcsot, és sikeresen bejelentkezhet. |

**Jelszó nélküli bejelentkezés az Azure AD web appsbe**

| Eset | Várt eredmények |
| --- | --- |
| A felhasználó regisztrálhat FIDO2-eszközt a aka.ms/mysecurityinfo-on a Microsoft Edge használatával | A regisztrációnak sikeresnek kell lennie |
| A felhasználó regisztrálhat FIDO2-eszközt a aka.ms/mysecurityinfo-on a Firefox használatával | A regisztrációnak sikeresnek kell lennie |
| A felhasználó a Microsoft Edge használatával tud bejelentkezni a OneDrive online FIDO2-eszköz használatával | A bejelentkezésnek sikeresnek kell lennie |
| A felhasználó bejelentkezhet a OneDrive online FIDO2-eszköz használatával a Firefox használatával | A bejelentkezésnek sikeresnek kell lennie |
| A FIDO2-eszköz regisztrációjának visszavonása a Azure Active Directory-portál hitelesítési módszer ablakának kikapcsolásával | A rendszer felszólítja a felhasználókat, hogy jelentkezzenek be a biztonsági kulcs használatával. A felhasználók sikeresen bejelentkeznek, és hibaüzenet jelenik meg: "a céges szabályzat megköveteli, hogy más módszert használjon a bejelentkezéshez." A felhasználóknak ezután ki kell tudniuk választani egy másik metódust, és sikeresen be kell jelentkezniük. Az ablak bezárásához és újbóli bejelentkezéshez ellenőrizze, hogy nem ugyanaz a hibaüzenet jelenik-e meg. |

### <a name="plan-for-rollback"></a>A visszaállítás megtervezése

Bár a jelszó-alapú hitelesítés egy egyszerű funkció, amely minimális hatással van a végfelhasználók számára, szükséges lehet a visszaállítás.

A visszaállításhoz a rendszergazdának be kell jelentkeznie a Azure Active Directory portálra, ki kell választania a kívánt erős hitelesítési módszereket, és **nem** értékre kell állítani az Engedélyezés lehetőséget. Ez a folyamat kikapcsolja az összes felhasználó jelszavas funkcionalitását.

Azok a felhasználók, akik már regisztráltak FIDO2 biztonsági eszközöket, a következő bejelentkezéskor a biztonsági eszköz használatára kérik, majd a következő hibaüzenetet látják:

![Válasszon másik módszert a bejelentkezéshez](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

## <a name="deploy-and-troubleshoot-passwordless-authentication"></a>Jelszóval nem rendelkező hitelesítés telepítése és hibakeresése

Kövesse az alábbi, a választott módszerhez igazított lépéseket.

### <a name="required-administrative-roles"></a>Szükséges rendszergazdai szerepkörök

| Azure AD-szerepkör | Description |
| --- | --- |
| Globális rendszergazda|A legkevésbé magas jogosultsági szintű szerepkör lehetővé tenné a közös regisztrációs élmény megvalósítását. |
| Hitelesítés rendszergazdája | A minimális jogosultsági szintű szerepkör képes a hitelesítési módszerek megvalósítására és kezelésére. |
| User | A legkevesebb Kiemelt szerepkör a hitelesítő alkalmazás konfigurálásához az eszközön, vagy a biztonsági kulcs eszközének regisztrálása a web vagy a Windows 10 rendszerű bejelentkezéshez. |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>Telefonos bejelentkezés üzembe helyezése a Microsoft Authenticator alkalmazással

Kövesse a cikk lépéseit, [engedélyezze a jelszó nélküli bejelentkezést a Microsoft Authenticator alkalmazással](howto-authentication-passwordless-phone.md) , hogy az Microsoft Authenticator alkalmazás jelszavas hitelesítési módszerként legyen engedélyezve a szervezetben.

### <a name="deploy-fido2-security-key-sign-in"></a>FIDO2-alapú biztonsági kulcs bejelentkezésének üzembe helyezése

Kövesse a cikk lépéseit, [engedélyezze a jelszó nélküli biztonsági kulcs bejelentkezését az Azure ad](howto-authentication-passwordless-security-key.md) -be, hogy a FIDO2 biztonsági kulcsokat jelszó nélküli hitelesítési módszerként engedélyezze.

### <a name="troubleshoot-phone-sign-in"></a>Telefonos bejelentkezés – problémamegoldás

| Eset | Megoldás |
| --- | --- |
| A felhasználó nem tudja végrehajtani a kombinált regisztrációt. | Győződjön meg arról, hogy a [kombinált regisztráció](concept-registration-mfa-sspr-combined.md) engedélyezve van. |
| A felhasználó nem engedélyezheti a telefonos bejelentkezési hitelesítő alkalmazást. | Győződjön meg arról, hogy a felhasználó hatókörben van az üzembe helyezéshez. |
| A felhasználó nem szerepel a jelszó nélküli hitelesítés hatókörében, de a jelszó nélküli bejelentkezés lehetőséggel jelenik meg, amely nem hajtható végre. | Ez a forgatókönyv akkor fordul elő, ha a felhasználó a létrehozott házirend előtt engedélyezte a telefonos bejelentkezést az alkalmazásban. <br> A *bejelentkezés engedélyezése*: adja hozzá a felhasználót a jelszó nélküli bejelentkezéshez engedélyezett felhasználók hatóköréhez. <br> *Bejelentkezés letiltása*: a felhasználó eltávolítja a hitelesítő adatokat az alkalmazásból. |

### <a name="troubleshoot-security-key-sign-in"></a>A biztonsági kulcs bejelentkezésének hibakeresése

| Eset | Megoldás |
| --- | --- |
| A felhasználó nem tudja végrehajtani a kombinált regisztrációt. | Győződjön meg arról, hogy a [kombinált regisztráció](concept-registration-mfa-sspr-combined.md) engedélyezve van. |
| A felhasználó nem adhat hozzá biztonsági kulcsot a [biztonsági beállításokban](https://aka.ms/mysecurityinfo). | Győződjön meg arról, hogy a [biztonsági kulcsok](howto-authentication-passwordless-security-key.md) engedélyezve vannak. |
| A felhasználó nem adhat hozzá biztonsági kulcsot a Windows 10 bejelentkezési beállításaiban. | [Győződjön meg arról, hogy a Windows-bejelentkezéshez szükséges biztonsági kulcsok](./concept-authentication-passwordless.md) |
| **Hibaüzenet**: azt észlelte, hogy ez a böngésző vagy operációs rendszer nem támogatja a FIDO2 biztonsági kulcsait. | A jelszó nélküli FIDO2 biztonsági eszközöket csak a támogatott böngészőkben (a Microsoft Edge, a Firefox 67-es verziójában) lehet regisztrálni a Windows 10 1809-es vagy újabb verziójában. |
| **Hibaüzenet**: a vállalati házirend megköveteli, hogy más módszert használjon a bejelentkezéshez. | A nem biztos, hogy a biztonsági kulcsok engedélyezve vannak a bérlőben. |
| A Windows 10 1809-es verziójában a felhasználó nem tudja kezelni a biztonsági kulcsot | Az 1809-es verzió használatához a FIDO2 kulcs gyártója által biztosított biztonsági kulcs-felügyeleti szoftvert kell használnia. Támogatásért forduljon a gyártóhoz. |
| Azt hiszem, hogy a FIDO2 biztonsági kulcsa hibás lehet – hogyan tesztelhető. | Lépjen a [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/) be, adja meg egy teszt fiók hitelesítő adatait, csatlakoztassa a gyanús biztonsági kulcsot, válassza a **+** képernyő jobb felső részén látható gombot, kattintson a Létrehozás gombra, és folytassa a létrehozás folyamatát. Ha ez a forgatókönyv meghiúsul, lehetséges, hogy az eszköz hibás. |

## <a name="next-steps"></a>Következő lépések

- [Jelszó nélküli biztonsági kulcsok engedélyezése az Azure AD-ba való bejelentkezéshez](howto-authentication-passwordless-security-key.md)
- [Jelszó nélküli bejelentkezés engedélyezése a Microsoft Authenticator alkalmazással](howto-authentication-passwordless-phone.md)
- [További információ a hitelesítési módszerekről & információk](./howto-authentication-methods-activity.md)
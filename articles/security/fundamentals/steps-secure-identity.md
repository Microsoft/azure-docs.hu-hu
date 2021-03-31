---
title: Az Azure AD Identity-infrastruktúra biztonságossá tétele
titleSuffix: Azure Active Directory
description: Ez a dokumentum a rendszergazdák által megvalósított fontos műveletek listáját ismerteti, hogy segítse a szervezet védelmét az Azure AD-funkciók használatával
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: security
ms.subservice: security-fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: martinco
ms.openlocfilehash: ffc5bafca305086b0c524c76eb91b8aec2e2655d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98602440"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>Öt lépés a személyazonossági infrastruktúra biztonságossá tételéhez

Ha elolvassa ezt a dokumentumot, tisztában van a biztonság fontosságával. Valószínűleg már viseli a felelősséget a szervezet védelme érdekében. Ha meg kell győznie másokat a biztonság fontosságáról, küldje el a [Microsoft biztonsági intelligencia legújabb jelentését](https://www.microsoft.com/security/business/security-intelligence-report).

Ez a dokumentum segítséget nyújt a Azure Active Directory képességeinek biztonságosabbá tételéhez egy öt lépésből álló ellenőrzőlista használatával, amellyel a szervezetet a számítógépes támadásokkal szemben beoltották.

Ez az ellenőrzőlista segítséget nyújt a kritikus fontosságú ajánlott műveletek gyors üzembe helyezéséhez a szervezet azonnali védelméhez, amely ismerteti a következőket:

* Erősítse meg hitelesítő adatait.
* Csökkentse a támadási felületet.
* A veszélyforrások megválaszolásának automatizálása.
* Használja a Felhőbeli intelligenciát.
* Végfelhasználói önkiszolgáló engedélyezése.

Győződjön meg róla, hogy nyomon követheti, hogy mely funkciók és lépések állnak készen az ellenőrzőlista elolvasása során.

> [!NOTE]
> A jelen dokumentumban szereplő javaslatok többsége csak olyan alkalmazásokra vonatkozik, amelyek a Azure Active Directory identitás-szolgáltatóként való használatára vannak konfigurálva. Az alkalmazások egyetlen Sign-On konfigurálása biztosítja a hitelesítő adatokra vonatkozó házirendek, a veszélyforrások észlelése, a naplózás, a naplózás és az alkalmazásokhoz hozzáadott egyéb szolgáltatások előnyeit. Az [Azure ad-alkalmazás-felügyelet](../../active-directory/manage-apps/what-is-application-management.md) az a Foundation, amelyen az összes javaslat alapul.

Az ebben a dokumentumban ismertetett javaslatok az Azure AD-bérlő identitás-biztonsági konfigurációjának automatizált értékelésével vannak összhangban az [identitás biztonságos pontszámával](../../active-directory/fundamentals/identity-secure-score.md). A szervezetek az Azure AD-portálon az Identitáskezelés biztonságos pontszám lapon megtalálhatják az aktuális biztonsági konfigurációban mutatkozó hézagokat, így biztosítva, hogy az aktuális Microsoft [ajánlott biztonsági eljárásokat](identity-management-best-practices.md) követik. Az egyes javaslatok a biztonságos pontszám lapon való megvalósítása növeli a pontszámát, és lehetővé teszi az előrehaladás nyomon követését, valamint segít összehasonlítani a megvalósítást más hasonló méretű szervezetekkel vagy iparágával.

![Identitás biztonságos pontszáma](./media/steps-secure-identity/azure-ad-sec-steps0.png)

> [!NOTE]
> Az itt ismertetett funkciók közül számos prémium szintű Azure AD előfizetés szükséges, míg néhány ingyenes. További információért tekintse át a [Azure Active Directory díjszabását](https://azure.microsoft.com/pricing/details/active-directory/) és az [Azure ad üzembe helyezési ellenőrzőlistáját](../../active-directory/fundamentals/active-directory-deployment-checklist-p2.md) .

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>Előkészületek: a rendszerjogosultságú fiókok MFA-val való ellátása

Mielőtt elkezdené ezt a feladatlistát, győződjön meg róla, hogy az ellenőrzőlista elolvasása közben nem sérül. Először gondoskodnia kell a Kiemelt jogosultságú fiókok biztonságáról.

A Kiemelt fiókok felügyeletét befogadó támadók óriási károkat okozhatnak, ezért fontos, hogy először megvédje ezeket a fiókokat. Az Azure ad [multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) (MFA) engedélyezése és megkövetelése a szervezetben lévő összes rendszergazda számára az [Azure ad-beli biztonsági beállítások](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md) vagy a [feltételes hozzáférés](../../active-directory/conditional-access/plan-conditional-access.md)használatával. Ha még nem implementálta az MFA-t, tegye meg most! Ez fontos.

Minden készlet? Ismerkedjen meg az ellenőrzőlista első lépéseivel.

## <a name="step-1---strengthen-your-credentials"></a>1. lépés – a hitelesítő adatok megerősítése

A legtöbb nagyvállalati biztonsági rést egy olyan fiók okozta, amely a jelszó spray, a megsértőség vagy az adathalászat megsértésével van ellátva. További információ ezekről a támadásokról ebben a videóban (45 perc):
> [!VIDEO https://www.youtube.com/embed/uy0j1_t5Hd4]

### <a name="make-sure-your-organization-uses-strong-authentication"></a>Győződjön meg arról, hogy a szervezet erős hitelesítést használ

A kitalált jelszavak gyakorisága, az adathalászás, a kártevők ellopása vagy az újbóli használat miatt kritikus fontosságú a jelszó visszaállítása valamilyen erős hitelesítő adat használatával – további információ az [Azure AD multi-Factor Authenticationról](../../active-directory/authentication/concept-mfa-howitworks.md).

Az Identitáskezelés alapvető szintjének egyszerű engedélyezéséhez használhatja az egykattintásos engedélyezést az [Azure ad biztonsági alapértelmezett beállításaival](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md). A biztonsági alapértékek az Azure AD MFA-t a bérlő összes felhasználója számára kényszerítik, és blokkolja a régi protokolloktól a bérlőre kiterjedő bejelentkezéseket.

### <a name="start-banning-commonly-attacked-passwords-and-turn-off-traditional-complexity-and-expiration-rules"></a>Indítsa el a gyakran megtámadott jelszavak betiltását, és kapcsolja ki a hagyományos összetettséget és a lejárati szabályokat.

Számos szervezet használja a hagyományos összetettséget (speciális karakterek, számok, nagybetűs és kisbetűk megkövetelése) és a jelszó lejárati szabályait. A [Microsoft kutatási](https://aka.ms/passwordguidance) feltette ezeket a házirendeket, mert a felhasználók a könnyebben kitalált jelszavakat választják.

Az Azure AD [dinamikus tiltott jelszava](../../active-directory/authentication/concept-password-ban-bad.md) funkció a jelenlegi támadó viselkedését használja annak megakadályozása érdekében, hogy a felhasználók könnyen kitalált jelszavakat állítsanak be. Ez a funkció mindig a Felhőbeli felhasználók létrehozásakor érhető el, de a hibrid szervezetek számára is elérhető, ha az [Azure ad jelszavas védelmet telepítik a Windows Server Active Directory](../../active-directory/authentication/concept-password-ban-bad-on-premises.md). Az Azure AD jelszavas védelme megakadályozza, hogy a felhasználók kiválasszák ezeket a gyakori jelszavakat, és kiterjeszthetők a megadott egyéni kulcsszavakat tartalmazó jelszavak blokkolására. Megakadályozhatja például, hogy a felhasználók olyan jelszavakat válasszanak, amelyek a vállalat termékeit vagy egy helyi sport csapatot tartalmaznak.

A Microsoft a következő modern jelszóházirend [bevezetését](https://pages.nist.gov/800-63-3/sp800-63b.html)javasolja a NIST-útmutató alapján:

1. A jelszó megkövetelése legalább 8 karakterből állhat. Többé nem feltétlenül jobb, mert a felhasználók kiválaszthatják a prediktív jelszavakat, menthetik a jelszavakat a fájlokban, vagy leírhatják őket.
2. Letilthatja a lejárati szabályokat, amelyek segítségével a felhasználók könnyen kitalálják a jelszavakat, például a **Spring2019!**
3. Tiltsa le a karakter-összeállítási követelményeket, és akadályozza meg, hogy a felhasználók gyakran megtámadott jelszavakat válasszanak, mivel a felhasználók kiszámítható karakteres helyettesítéseket választhatnak a jelszavakban.

A PowerShell használatával [megakadályozhatja, hogy a jelszavak lejárjanak](../../active-directory/authentication/concept-sspr-policy.md) a felhasználók számára, ha közvetlenül az Azure ad-ben hoz létre identitásokat. A hibrid szervezeteknek a [tartományi csoportházirend-beállítások](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) vagy a [Windows PowerShell](/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy)használatával kell alkalmazniuk ezeket a házirendeket.

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>A kiszivárgott hitelesítő adatokkal szembeni védelem és az kimaradások elleni rugalmasság biztosítása

Ha a szervezet hibrid identitási megoldást használ átmenő hitelesítéssel vagy összevonással, akkor a jelszó-kivonatolási szinkronizálást a következő két okból kell engedélyeznie:

* A [kiszivárgott hitelesítő adatokkal rendelkező felhasználók](../../active-directory/identity-protection/overview-identity-protection.md) az Azure ad-ben figyelmeztetnek a Felhasználónév és a jelszó párokra, amelyek elérhetők a "Dark web"-ben. A rendszer az adathalászat, a kártevők és a jelszavak újbóli felhasználását a későbbiekben megsértő külső webhelyeken végezheti el. A Microsoft megkeresi ezeket a kiszivárgott hitelesítő adatokat, és tájékoztatja Önt arról, ha a szervezet hitelesítő adatai egyeznek, de csak akkor, ha [engedélyezi a jelszó-kivonatok szinkronizálását](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md) , vagy csak a csak Felhőbeli identitásokat.
* Helyszíni leállás esetén (például egy ransomware támadás esetében) a [jelszó-kivonatolási szinkronizálás használatával](../../active-directory/hybrid/choose-ad-authn.md)átválthat a Felhőbeli hitelesítés használatára. Ez a biztonsági mentési hitelesítési módszer lehetővé teszi, hogy továbbra is hozzáférjen a hitelesítéshez konfigurált alkalmazások Azure Active Directory, például a Microsoft 365hoz. Ebben az esetben az informatikai részlegnek nem kell személyes e-mail-fiókokat használnia az adatmegosztáshoz, amíg a helyszíni leállás megoldódik.

További információ a [jelszó-kivonatolási szinkronizálás](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md) működéséről.

> [!NOTE]
> Ha engedélyezi a jelszó-kivonatolási szinkronizálást, és az Azure AD tartományi szolgáltatásokat használja, a Kerberos (AES 256) kivonatok és opcionálisan NTLM (RC4, nem Salt) kivonatok is titkosítva lesznek, és szinkronizálva lesznek az Azure AD-vel.

### <a name="implement-ad-fs-extranet-smart-lockout"></a>AD FS extranetes intelligens zárolás implementálása

Szervezetek, amelyek az [Azure ad Smart zárolási](../../active-directory/authentication/concept-sspr-howitworks.md)szolgáltatásból közvetlenül az Azure ad-be való hitelesítéshez konfigurálják az alkalmazásokat. Ha AD FSt használ a Windows Server 2012R2, implementálja AD FS [extranet zárolásának védelmét](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection). Ha AD FSt használ a Windows Server 2016-ben, az [extranetes intelligens zárolást](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016)implementálja. AD FS az intelligens extranetes zárolás védelmet biztosít a találgatásos támadásokkal szemben, amelyek célja AD FS, miközben megakadályozza a felhasználók kizárását Active Directory.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>Használja ki a belsőleg biztonságos és könnyebben használható hitelesítő adatokat

A [Windows Hello](/windows/security/identity-protection/hello-for-business/hello-identity-verification)használatával a jelszavakat erős, kétfaktoros hitelesítéssel helyettesítheti a számítógépeken és mobileszközökön. Ez a hitelesítés olyan új felhasználói hitelesítő adatokból áll, amely biztonságosan kötődik az eszközhöz, és biometrikus vagy PIN-kódot használ.

## <a name="step-2---reduce-your-attack-surface"></a>2. lépés – a támadási felület csökkentése

A jelszó megtörése miatt a szervezet támadási felületének minimalizálása kritikus fontosságú. A régebbi, kevésbé biztonságos protokollok használatának, a hozzáférési pontok korlátozásának és az erőforrásokhoz való rendszergazdai hozzáférés nagyobb mértékű felügyeletének csökkentése segíthet csökkenteni a támadási felület területét.

### <a name="block-legacy-authentication"></a>Régi hitelesítési folyamat letiltása

A saját örökölt módszereiket használó alkalmazások az Azure AD-vel való hitelesítéshez és a vállalati adatszolgáltatásokhoz való hozzáféréshez egy másik veszélyforrást jelentenek. Az örökölt hitelesítést használó alkalmazások például a következők: POP3, IMAP4 vagy SMTP-ügyfelek. Az örökölt hitelesítési alkalmazások a felhasználó nevében hitelesítik magukat, és megakadályozzák, hogy az Azure AD speciális biztonsági értékeléseket végezzen. Az alternatív, modern hitelesítés csökkenti a biztonsági kockázatokat, mivel ez támogatja a többtényezős hitelesítést és a feltételes hozzáférést. A következő három műveletet javasoljuk:

1. [Az örökölt hitelesítés letiltása, ha AD FS használ](/windows-server/identity/ad-fs/operations/access-control-policies-w2k12).
2. [A SharePoint Online és az Exchange Online beállítása modern hitelesítés használatára](../../active-directory/conditional-access/block-legacy-authentication.md).
3. Ha prémium szintű Azure AD rendelkezik, használja a feltételes hozzáférési szabályzatokat az [örökölt hitelesítés blokkolására](../../active-directory/conditional-access/howto-conditional-access-policy-block-legacy.md), ellenkező esetben használja az [Azure ad biztonsági alapértelmezéseit](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md).

### <a name="block-invalid-authentication-entry-points"></a>Érvénytelen hitelesítési belépési pontok blokkolása

A szabálysértések megsértésének feltételezésével csökkentheti a feltört felhasználói hitelesítő adatok következményeit, amikor azok történnek. A környezetben lévő minden alkalmazás esetében tekintse meg a következő érvényes használati eseteket: mely csoportok, mely hálózatok, mely eszközök és egyéb elemek engedélyezettek –, majd tiltsa le a többiet. Az [Azure ad feltételes hozzáférésével](../../active-directory/conditional-access/overview.md)szabályozhatja, hogy a jogosult felhasználók hogyan férhessenek hozzá az alkalmazásaihoz és erőforrásaihoz az Ön által megadott feltételek alapján.

### <a name="restrict-user-consent-operations"></a>Felhasználói engedélyezési műveletek korlátozása

Fontos megérteni a különböző [Azure ad-alkalmazásokhoz való beleegyezési tapasztalatokat](../../active-directory/develop/application-consent-experience.md), az [engedélyek és a beleegyezés típusát](../../active-directory/develop/v2-permissions-and-consent.md), valamint a szervezet biztonsági helyzetére gyakorolt hatásukat. Alapértelmezés szerint az Azure AD minden felhasználója biztosíthat olyan alkalmazásokat, amelyek kihasználják a Microsoft Identity platformot a szervezet adatainak eléréséhez. A felhasználók saját maguk is engedélyezhetik, hogy a felhasználók egyszerűen beszerezzék a Microsoft 365-, Azure-és egyéb szolgáltatásokkal integrálható hasznos alkalmazásokat, és ez kockázatot jelenthet, ha nem használatos és nem figyeli őket körültekintően.

A Microsoft azt javasolja, hogy a felhasználói beleegyezett, hogy csökkentse a felületét, és enyhítse ezt a kockázatot. Az [alkalmazás-engedélyezési szabályzatok (előzetes verzió)](../../active-directory/manage-apps/configure-user-consent.md) is használhatók a végfelhasználói jóváhagyás korlátozására csak az ellenőrzött közzétevők számára, és csak a kiválasztott engedélyek esetében. Ha a végfelhasználói hozzájárulás korlátozott, a rendszer továbbra is tiszteletben tartja a korábbi hozzájárulási engedélyeket, de az összes jövőbeli hozzájárulási műveletet egy rendszergazdának kell elvégeznie. Korlátozott esetekben a felhasználók a rendszergazdai jogosultságokat az integrált [rendszergazdai engedélyezési kérelem munkafolyamatán](../../active-directory/manage-apps/configure-admin-consent-workflow.md) vagy a saját támogatási folyamatain keresztül kérhetik. A végfelhasználói jóváhagyás korlátozása előtt [javaslataink](../../active-directory/manage-apps/manage-consent-requests.md) alapján tervezze meg ezt a változást a szervezetében. Azon alkalmazások esetében, amelyek számára engedélyezni szeretné az összes felhasználó hozzáférését, fontolóra kell vennie az [összes felhasználó nevében való hozzájárulást](../../active-directory/develop/v2-admin-consent.md), így biztosítva, hogy a korábban még nem beleegyezett felhasználók hozzáférhessenek az alkalmazáshoz. Ha nem szeretné, hogy ezek az alkalmazások minden esetben elérhetők legyenek az összes felhasználó számára, az [alkalmazás-hozzárendelés](../../active-directory/manage-apps/assign-user-or-group-access-portal.md) és a feltételes hozzáférés használatával korlátozza a felhasználók hozzáférését az [adott alkalmazásokhoz](../../active-directory/conditional-access/concept-conditional-access-cloud-apps.md).

Győződjön meg arról, hogy a felhasználók az új alkalmazások számára rendszergazdai jóváhagyást kérhetnek a felhasználói súrlódás csökkentése érdekében, minimálisra csökkentik a kötetek támogatását, és megakadályozhatják, hogy a felhasználók nem Azure AD-beli hitelesítő adatokkal regisztráljanak. A hozzájárulási műveletek szabályozása után a rendszergazdáknak rendszeresen be kell jelentkezniük az alkalmazásra és a beleegyezési engedélyekre.


### <a name="implement-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management implementálása

A "jogsértés feltételezése" egy másik következménye annak a valószínűsége, hogy a feltört fiók valószínűleg egy kiemelt szerepkörrel működhet.

A [Azure ad Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) segítséget nyújt a fiókok minimálisra csökkentése érdekében:

* Azonosíthatja és kezelheti a rendszergazdai szerepkörökhöz rendelt felhasználókat.
* Ismerje meg a nem használt vagy túlzott jogosultságú szerepköröket, amelyeket el kell távolítania.
* Szabályokat hozhat létre annak biztosítására, hogy a Kiemelt szerepkörök védelmét a multi-Factor Authentication védi.
* Olyan szabályokat hozhat létre, amelyek biztosítják, hogy a Kiemelt szerepkörök csak elég hosszúak legyenek a kiemelt feladat végrehajtásához.

Engedélyezze az Azure AD PIM-t, majd tekintse meg a rendszergazdai szerepkörökhöz rendelt felhasználókat, és távolítsa el a szerepkörök szükségtelen fiókjait. A többi emelt szintű jogosultsággal rendelkező felhasználó számára az állandóról jogosultra helyezheti át őket. Végül hozza létre a megfelelő szabályzatokat annak biztosításához, hogy mikor van szükségük ahhoz, hogy hozzáférést kapjanak ezekhez a Kiemelt szerepkörökhöz, biztonságosan, a szükséges módosítás-ellenőrzéssel.

A rendszerjogosultságú fiók üzembe helyezésének részeként az ajánlott eljárás [szerint hozzon létre legalább két vészhelyzeti fiókot](../../active-directory/roles/security-planning.md) annak érdekében, hogy továbbra is hozzáférhessen az Azure ad-hez, ha zárolja magát.

## <a name="step-3---automate-threat-response"></a>3. lépés – a veszélyforrások megválaszolásának automatizálása

A Azure Active Directory számos képességgel rendelkezik, amelyek automatikusan feltartóztatják a támadásokat, és eltávolítják az észlelés és a válasz közötti késést. Csökkentheti a költségeket és a kockázatokat, ha csökkenti a bűnözők által a környezetbe ágyazható idő csökkentését. Itt láthatja az elvégezhető konkrét lépéseket.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>A felhasználói kockázatokra vonatkozó biztonsági házirend megvalósítása Azure AD Identity Protection használatával

A felhasználói kockázat azt jelzi, hogy a felhasználó identitása sérült, és a felhasználó identitásával kapcsolatos [felhasználói kockázati észlelések](../../active-directory/identity-protection/overview-identity-protection.md) alapján számítja ki a rendszer. A felhasználói kockázati házirend egy feltételes hozzáférési szabályzat, amely kiértékeli a kockázati szintet egy adott felhasználó vagy csoport számára. Az alacsony, közepes és magas kockázati szintű szabályok alapján a szabályzat beállítható úgy, hogy blokkolja a hozzáférést, vagy a többtényezős hitelesítés használatával biztonságos jelszó megváltoztatására van szükség. A Microsoft javaslata a nagy kockázatú felhasználók biztonságos jelszavas módosításának megkövetelése.

![Képernyőfelvétel: a felhasználó által kiválasztva a kockázatra megjelölt felhasználók.](./media/steps-secure-identity/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>A bejelentkezési kockázati szabályzat megvalósítása Azure AD Identity Protection használatával

A bejelentkezési kockázat annak a valószínűsége, hogy a fiók tulajdonosa nem az identitás használatával próbál bejelentkezni. A [bejelentkezési kockázati szabályzat](../../active-directory/identity-protection/overview-identity-protection.md) egy feltételes hozzáférési szabályzat, amely kiértékeli a kockázati szintet egy adott felhasználó vagy csoport számára. A kockázati szint (magas/közepes/alacsony) alapján a szabályzat beállítható úgy, hogy blokkolja a hozzáférést vagy kényszerítse a többtényezős hitelesítést. Győződjön meg arról, hogy a többtényezős hitelesítést közepes vagy annál nagyobb kockázatú bejelentkezésekre kényszeríti.

![Bejelentkezés névtelen IP-címekről](./media/steps-secure-identity/azure-ad-sec-steps2.png)

## <a name="step-4---utilize-cloud-intelligence"></a>4. lépés – a Cloud Intelligence kihasználása

A biztonsággal kapcsolatos események és a kapcsolódó riasztások naplózása és naplózása hatékony védelmi stratégia alapvető összetevői. A biztonsági naplók és jelentések lehetővé teszik a gyanús tevékenységek elektronikus feljegyzését, valamint a hálózat külső behatolását, illetve a belső támadásokat jelezhet minták észlelését. A naplózás használatával figyelheti a felhasználói tevékenységeket, a dokumentumok megfelelőségét, a kriminalisztikai elemzést és egyebeket. A riasztások a biztonsági események értesítéseit biztosítják.

### <a name="monitor-azure-ad"></a>Az Azure AD figyelése

Microsoft Azure szolgáltatások és szolgáltatások konfigurálható biztonsági naplózási és naplózási lehetőségeket biztosítanak, amelyek segítségével azonosíthatja a biztonsági házirendekben és mechanizmusokban mutatkozó réseket, és orvosolhatja ezeket a hiányosságokat a szabálysértések megelőzése érdekében. [A Azure Active Directory-portálon](../../active-directory/reports-monitoring/concept-audit-logs.md)az [Azure naplózási és naplózási](log-audit.md) szolgáltatásait, valamint a naplózási tevékenység jelentéseit használhatja.

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>Azure AD Connect Health monitorozása hibrid környezetekben

A [Azure ad Connect Health figyelése AD FS](../../active-directory/hybrid/how-to-connect-health-adfs.md) a lehetséges problémákkal és a AD FS-infrastruktúrával kapcsolatos támadások láthatóságával kapcsolatos további információkat nyújt. Azure AD Connect Health a riasztásokat a részletekkel, a megoldás lépéseivel és a kapcsolódó dokumentációra mutató hivatkozásokkal biztosítja; a hitelesítési forgalommal kapcsolatos számos metrika használati elemzései; Teljesítményfigyelés és jelentések.

![Azure AD Connect Health](./media/steps-secure-identity/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Azure AD Identity Protection események figyelése

A [Azure ad Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) egy értesítési, figyelési és jelentéskészítési eszköz, amellyel észlelhetők a szervezet identitásait érintő lehetséges sebezhetőségek. Észleli a kockázati észleléseket, például a kiszivárgott hitelesítő adatokat, a lehetetlen utazást és a fertőzött eszközökről érkező bejelentkezéseket, a névtelen IP-címeket, a gyanús tevékenységhez tartozó IP-címeket és az ismeretlen helyeket. Az értesítési riasztások lehetővé teszik a veszélyeztetett felhasználók és/vagy heti kivonatoló e-mailek fogadását.

Azure AD Identity Protection két fontos jelentést biztosít a napi figyeléshez:
1. A kockázatos bejelentkezési jelentések felszínre kerülnek a felhasználói bejelentkezési tevékenységek, amelyeket meg kell vizsgálnia. Előfordulhat, hogy a legitim tulajdonos nem végezte el a bejelentkezést.
2. A kockázatos felhasználói jelentések olyan felszíni felhasználói fiókokat érintenek, amelyek biztonsága esetleg sérült, például a észlelt hitelesítő adatok, vagy a felhasználó különböző helyekről való bejelentkezett, ami lehetetlenné teszi az utazási eseményt.

![A képernyőképen az Azure A D Identity Protection ablaktáblája látható a felhasználókkal és azok kockázati szintjeivel.](./media/steps-secure-identity/azure-ad-sec-steps3.png)

### <a name="audit-apps-and-consented-permissions"></a>Az alkalmazások és a beleegyezett engedélyek naplózása

A felhasználók bejelentkezhetnek egy olyan feltört webhelyre vagy alkalmazásba, amely hozzáférést szerez a profiljuk adataihoz és a felhasználói adatokhoz, például az e-mailekhez. Egy rosszindulatú színész használhatja a kapott engedélyekkel, hogy Titkosítsa a postaláda tartalmát, és megszerezze a váltságdíjat a postaláda-információk visszaszerzéséhez. A [rendszergazdáknak át kell tekinteniük és ellenőrizniük kell](/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) a felhasználók által megadott engedélyeket, vagy le kell tiltaniuk, hogy a felhasználók alapértelmezés szerint megadják a hozzájárulásukat.

A felhasználók által megadott engedélyek naplózása mellett a [kockázatos vagy nemkívánatos OAuth alkalmazásokat is megtalálhatja](/cloud-app-security/investigate-risky-oauth) prémium környezetekben.

## <a name="step-5---enable-end-user-self-service"></a>5. lépés – a végfelhasználói önkiszolgáló engedélyezése

Amennyire csak lehet, érdemes egyensúlyba állítani a biztonságot a hatékonysággal. Ha hosszú távon is megközelíti az Ön utazását, azzal a gondolkodásmódtal, hogy a biztonságra vonatkozó alapszintű biztonsági beállításokat a munkahelyen, a felhasználók továbbra is körültekintően vehetik igénybe.

### <a name="implement-self-service-password-reset"></a>Önkiszolgáló jelszó-visszaállítás implementálása

Az Azure AD önkiszolgáló [jelszó-visszaállítási szolgáltatása (SSPR)](../../active-directory/authentication/tutorial-enable-sspr.md) egyszerű módszert kínál a rendszergazdáknak arra, hogy az ügyfélszolgálat vagy a rendszergazda beavatkozása nélkül visszaállítsák vagy feloldják a jelszavukat vagy fiókjaikat. A rendszer részletes jelentéseket tartalmaz, amelyek nyomon követik, hogy a felhasználók hogyan állíthatják alaphelyzetbe a jelszavukat, valamint az értesítéseket, amelyek a visszaélés vagy a visszaélés ellen figyelmeztetnek

### <a name="implement-self-service-group-and-application-access"></a>Önkiszolgáló csoport és alkalmazás-hozzáférés megvalósítása

Az Azure AD lehetővé teszi a nem rendszergazdák számára az erőforrásokhoz való hozzáférés kezelését, a biztonsági csoportok, Microsoft 365 csoportok, az alkalmazás szerepkörei és a hozzáférési csomagok katalógusának használatát.  Az [önkiszolgáló csoportok felügyelete](../../active-directory/enterprise-users/groups-self-service-management.md) lehetővé teszi, hogy a csoport tulajdonosai a saját Csoportjaikat kezelhesse, anélkül, hogy rendszergazdai szerepkört kellene hozzárendelni. A felhasználók Microsoft 365 csoportokat is létrehozhatnak és kezelhetnek anélkül, hogy a rendszergazdákra támaszkodnia kellene a kéréseik kezelésére, és a nem használt csoportok automatikusan lejárnak.  Az [Azure ad-jogosultságok kezelése](../../active-directory/governance/entitlement-management-overview.md) lehetővé teszi a delegálást és a láthatóságot, átfogó hozzáférési kérelmek munkafolyamataival és automatikus lejáratával.  A nem rendszergazdai jogosultsággal rendelkező felhasználók a saját hozzáférési csomagjaikat a saját csoportjaihoz, csoportjaihoz, alkalmazásaihoz és a saját SharePoint Online-webhelyekhez is konfigurálhatják, a hozzáférés jóváhagyásához szükséges egyéni szabályzatokkal, többek között az alkalmazottak felettesei és az üzleti partneri szponzorok konfigurálása jóváhagyóként.

### <a name="implement-azure-ad-access-reviews"></a>Azure AD hozzáférési felülvizsgálatok megvalósítása

Az [Azure ad hozzáférési felülvizsgálatok](../../active-directory/governance/access-reviews-overview.md)segítségével kezelheti a hozzáférési csomagokat és a csoporttagságok, a vállalati alkalmazásokhoz való hozzáférést és a Kiemelt szerepkör-hozzárendeléseket, így biztosítva a biztonsági szabványok fenntartását.  A felhasználók saját maguk, az erőforrás-tulajdonosok és az egyéb felülvizsgálók rendszeres felügyelete biztosítja, hogy a felhasználók ne tartsanak meg hosszabb ideig a hozzáférést, amikor már nincs rá szükségük.

## <a name="summary"></a>Összefoglalás

A biztonságos identitás-infrastruktúrának számos aspektusa van, de ez az öt lépésből álló ellenőrzőlista segítséget nyújt a biztonságosabb és biztonságos identitás-infrastruktúra gyors megvalósításához:

* Erősítse meg hitelesítő adatait.
* Csökkentse a támadási felületet.
* A veszélyforrások megválaszolásának automatizálása.
* Használja a Felhőbeli intelligenciát.
* Az önsegítő szolgáltatással kiszámítható és teljes körű biztonságot nyújthat a végfelhasználók számára.

Nagyra értékeljük, hogy milyen komolyan vesszük a személyazonossági biztonságot, és reméljük, hogy ez a dokumentum hasznos útiterv a szervezete számára biztonságosabb testhelyzethez.

## <a name="next-steps"></a>Következő lépések

Ha segítségre van szüksége a javaslatok megtervezéséhez és üzembe helyezéséhez, tekintse meg az [Azure ad projekt üzembe helyezési terveit](../../active-directory/fundamentals/active-directory-deployment-plans.md) .

Ha biztos benne, hogy mindezek a lépések befejeződik, használja a Microsoft [Identity Secure pontszámát](../../active-directory/fundamentals/identity-secure-score.md), amely naprakészen tartja a [legújabb ajánlott eljárásokat](identity-management-best-practices.md) és biztonsági fenyegetéseket.

---
title: Alapértelmezett felhasználói engedélyek – Azure Active Directory | Microsoft Docs
description: Ismerje meg a különböző felhasználói engedélyeket, amelyek a Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: ajburnle
ms.reviewer: vincesm
ms.custom: it-pro, seodec18, contperf-fy21q1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 348f1b4e6182739b3afbc96597853a5b887877c1
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748771"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Mik az alapértelmezett felhasználói engedélyek a Azure Active Directory?
Az Azure Active Directoryban (Azure AD-ban) minden felhasználóhoz alapértelmezett engedélyek készlete tartozik. A felhasználó hozzáférése a felhasználó típusát, [](active-directory-users-assign-role-azure-portal.md)szerepkör-hozzárendelését és az egyes objektumok tulajdonjogát foglalja magában. Ez a cikk az alapértelmezett engedélyeket ismerteti, valamint összehasonlítja a tag- és vendégfelhasználók alapértelmezett beállításait. Az alapértelmezett felhasználói engedélyek csak az Azure AD felhasználói beállításaiban módosíthatók.

## <a name="member-and-guest-users"></a>Tag- és vendégfelhasználók
A kapott alapértelmezett engedélyek készlete attól függ, hogy a felhasználó a bérlő (tagfelhasználó) natív tagja-e, vagy a felhasználó egy másik címtárból B2B együttműködési vendégként (vendégfelhasználó) van-e áthozva. További információ a vendégfelhasználók hozzáadásáról: Mi az [az Azure AD B2B-együttműködés?](../external-identities/what-is-b2b.md)
* A tagfelhasználók alkalmazásokat regisztrálhatnak, kezelhetik saját profilképüket és mobiltelefonszámukat, módosíthatják jelszavukat, valamint meghívhatnak B2B-vendégfelhasználókat. Ezen kívül a felhasználók a címtár összes adatát olvashatják (néhány kivétellel). 
* A vendégfelhasználók korlátozott címtárengedélyekkel rendelkezik. Kezelhetik a saját profiljukat, megváltoztathatják a saját jelszavukat, és más felhasználókkal, csoportokkal és alkalmazásokkal kapcsolatos információkat is lekérhetnek, de nem tudják elolvasni az összes címtárinformációt. A vendégfelhasználók például nem tudják enumerálni az összes felhasználó, csoport és egyéb címtárobjektum listáját. A vendégfelhasználókhoz rendszergazdai szerepkör rendelhető, amelynek birtokában az adott szerepkör teljes körű olvasási és írási engedélyeivel rendelkeznek. A vendégek más vendégeket is meghívnak.

## <a name="compare-member-and-guest-default-permissions"></a>Tagok és vendégek alapértelmezett engedélyeinek összehasonlítása

**Terület** | **Tagfelhasználók engedélyei** | **Alapértelmezett vendégfelhasználói engedélyek** | **Korlátozott vendégfelhasználói engedélyek (előzetes verzió)**
------------ | --------- | ---------- | ----------
Felhasználók és kapcsolatok | <ul><li>Az összes felhasználó és kapcsolattartó enumerálásának listája<li>Felhasználók és kapcsolatok összes nyilvános tulajdonságának olvasása</li><li>Vendégek meghívása<li>Saját jelszó módosítása<li>Saját mobiltelefonszám kezelése<li>Saját fénykép kezelése<li>Saját frissítési jogkivonatok érvénytelenítése</li></ul> | <ul><li>Saját tulajdonságok olvasása<li>Más felhasználók és kapcsolatok megjelenített nevének, e-mail-címének, bejelentkezési nevének, fényképének, egyszerű felhasználónevének és felhasználótípus-tulajdonságainak olvasása<li>Saját jelszó módosítása<li>Másik felhasználó keresése ObjectId alapján (ha engedélyezett)<li>Más felhasználók felettesi és közvetlen jelentésinformációk olvasása</li></ul> | <ul><li>Saját tulajdonságok olvasása<li>Saját jelszó módosítása</li><li>Saját mobiltelefonszám kezelése</li></ul>
Csoportok | <ul><li>Biztonsági csoportok létrehozása<li>Új Microsoft 365 létrehozása<li>Az összes csoport enumerálásának listája<li>Minden csoporttulajdonság olvasása<li>Nem rejtett csoporttagságok olvasása<li>Az Microsoft 365 csoporttagság rejtett csoporttagságának olvasása<li>A felhasználó tulajdonában van a tulajdonságok, a tulajdonjog és a csoporttagság kezelése<li>Vendégek hozzáadása saját csoportokhoz<li>Dinamikus tagsági beállítások kezelése<li>Saját csoportok törlése<li>Saját tulajdonú Microsoft 365 visszaállítása</li></ul> | <ul><li>Nem rejtett csoportok tulajdonságainak olvasása, beleértve a tagságot és a tulajdonjogot (akár nem csatlakozott csoportok esetén is)<li>Az összekapcsolt Microsoft 365 rejtett csoporttagságának olvasása<li>Csoportok keresése megjelenített név vagy objektumazonosító alapján (ha engedélyezett)</li></ul> | <ul><li>Az összekapcsolt csoportok objektumazonosítójának olvasása<li>Egyes alkalmazásokhoz csatlakozott csoportok tagságának és tulajdonosi Microsoft 365 olvasása (ha ez engedélyezett)</li></ul>
Alkalmazások | <ul><li>Új alkalmazás regisztrálása (létrehozása)<li>Az összes alkalmazás felsorolása<li>Regisztrált és vállalati alkalmazások tulajdonságainak olvasása<li>Saját alkalmazások tulajdonságainak, hozzárendeléseinek és hitelesítő adatainak kezelése<li>Felhasználó alkalmazásjelszavának létrehozása vagy törlése<li>Saját alkalmazások törlése<li>Saját alkalmazások visszaállítása</li></ul> | <ul><li>Regisztrált és vállalati alkalmazások tulajdonságainak olvasása</li></ul> | <ul><li>Regisztrált és vállalati alkalmazások tulajdonságainak olvasása
Eszközök</li></ul> | <ul><li>Az összes eszköz enumerálásának listája<li>Minden eszköztulajdonság olvasása<li>Saját eszközök minden tulajdonságának olvasása</li></ul> | Nincs engedély | Nincs engedély
Címtár | <ul><li>Minden vállalati adat olvasása<li>Minden tartomány olvasása<li>Minden partnerszerződés olvasása</li></ul> | <ul><li>Vállalat megjelenített nevének olvasása<li>Minden tartomány olvasása</li></ul> | <ul><li>Vállalat megjelenített nevének olvasása<li>Minden tartomány olvasása</li></ul>
Szerepkörök és hatókörök | <ul><li>Minden rendszergazdai szerepkör és tagság olvasása<li>Felügyeleti egységek minden tulajdonságának és tagságának olvasása</li></ul> | Nincs engedély | Nincs engedély
Előfizetések | <ul><li>Minden előfizetés olvasása<li>Szolgáltatáscsomag-tag engedélyezése</li></ul> | Nincs engedély | Nincs engedély
Házirendek | <ul><li>Szabályzatok minden tulajdonságának olvasása<li>Saját szabályzat minden tulajdonságának kezelése</li></ul> | Nincs engedély | Nincs engedély

## <a name="restrict-member-users-default-permissions"></a>Tagfelhasználók alapértelmezett engedélyeinek korlátozása 

A tagfelhasználók alapértelmezett engedélyei a következő módokon korlátozhatóak:

Engedély | Magyarázat beállítása
---------- | ------------
A felhasználók regisztrálják az alkalmazást | A Nem beállítás megakadályozza, hogy a felhasználók alkalmazásregisztrációkat hoznak létre. Ezt a képességet később vissza lehet adni adott személyeknek, ha hozzáadja őket az Alkalmazásfejlesztői szerepkörhöz.
Munkahelyi vagy iskolai fiók LinkedInhez való csatlakoztatásának engedélyezése a felhasználók számára | A Nem beállítás megakadályozza, hogy a felhasználók összekapcsolják a munkahelyi vagy iskolai fiókjukat a LinkedIn-fiókjukkal. További információ: [LinkedIn-fiókkapcsolatok adatmegosztása és beleegyezése.](../enterprise-users/linkedin-user-consent.md)
Biztonsági csoportok létrehozásának képessége | Ha e beállítás értéke Nem, akkor a felhasználók nem hozhatnak létre biztonsági csoportokat. A globális rendszergazdák és a felhasználói rendszergazdák továbbra is létrehozhatnak biztonsági csoportokat. További tudnivalók: [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](../enterprise-users/groups-settings-cmdlets.md).
Lehetőség új Microsoft 365 létrehozására | Ha ezt a beállítást a Nem beállítással választja, a felhasználók nem Microsoft 365 csoportokat. Ha ezt a beállítást Néhányra állítva engedélyezi, hogy egy kiválasztott felhasználócsoport hozzon létre Microsoft 365 csoportokat. A globális rendszergazdák és a felhasználói rendszergazdák továbbra is létrehozhatnak Microsoft 365 csoportokat. További tudnivalók: [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](../enterprise-users/groups-settings-cmdlets.md).
Az Azure AD felügyeleti portálhoz való hozzáférés korlátozása | Ha ezt a beállítást nem, akkor a nem rendszergazdák az Azure AD felügyeleti portál használatával olvashatják és kezelhetik az Azure AD-erőforrásokat. Az Igen beállítás minden nem rendszergazda számára hozzáférést biztosít az Azure AD-adatokhoz a felügyeleti portálon.<p>**Megjegyzés:** Ez a beállítás nem korlátozza az Azure AD-adatokhoz való hozzáférést a PowerShell vagy más ügyfelek, például a Visual Studio használatával. Ha igenre van állítva, akkor egy adott nem rendszergazdai felhasználónak hozzáférést biztosít az Azure AD felügyeleti portáljának használatával bármilyen rendszergazdai szerepkör, például a Címtárolvasó szerepkör hozzárendelésére.<p>Ez a szerepkör lehetővé teszi az alapszintű címtárinformációk olvasását, mely tagfelhasználók alapértelmezés szerint (a vendégek és a szolgáltatásnév nem).
Egyéb felhasználók olvasásának képessége | Ez a beállítás csak a PowerShellben érhető el. Ha ezt a jelzőt $false beállítás megakadályozza, hogy az összes nem rendszergazda felhasználó olvassa be a felhasználói adatokat a címtárból. Ez a jelző nem akadályozza meg a felhasználói adatok olvasását más Microsoft-szolgáltatások, például az Exchange Online-ban. Ez a beállítás különleges körülmények esetén ajánlott, és ez a jelző $false nem ajánlott.

## <a name="restrict-guest-users-default-permissions"></a>Vendégfelhasználók alapértelmezett engedélyeinek korlátozása

A vendégfelhasználók alapértelmezett engedélyei a következő módokon korlátozhatóak:

>[!NOTE]
>A vendégfelhasználók engedélyeinek helyére a vendégfelhasználókra vonatkozó korlátozások **beállítás korlátozott.** A funkció használatával kapcsolatos útmutatásért lásd: Vendégelérési engedélyek korlátozása [(előzetes verzió) a Azure Active Directory.](../enterprise-users/users-restrict-guest-permissions.md)

Engedély | Magyarázat beállítása
---------- | ------------
Vendégek felhasználói hozzáférésének korlátozásai (előzetes verzió) | Ha ezt a **beállítást** úgy választja, hogy a Vendégfelhasználók ugyanazt a hozzáférést biztosítják, mint a tagok, alapértelmezés szerint minden tagfelhasználói engedélyt biztosít a vendégfelhasználóknak.<p>Ha a **vendégfelhasználói** hozzáférés beállítást a saját címtárobjektumaik tulajdonságaira és tagságára korlátozza, a vendég hozzáférése alapértelmezés szerint csak a saját felhasználói profiljukra korlátozódik. Más felhasználók hozzáférése akkor sem engedélyezett, ha egyszerű felhasználónév, objectid vagy megjelenített név alapján keres. A csoportinformációkhoz való hozzáférés, beleértve a csoporttagságokat is, szintén nem engedélyezett.<p>**Megjegyzés:** Ez a beállítás nem akadályozza meg a hozzáférést bizonyos Microsoft 365, például a Microsoft Teamsben. További [információ: Microsoft Teams vendégelérés.](/MicrosoftTeams/guest-access)<p>A vendégfelhasználók ettől függetlenül továbbra is hozzáadhatóak a rendszergazdai szerepkörökhöz.
Vendégek küldhetnek meghívót | Ennek a beállításnak az Igen beállítása lehetővé teszi a vendégek számára más vendégek meghívását. További [információ: Meghívók delegálása B2B-együttműködéshez.](../external-identities/delegate-invitations.md#configure-b2b-external-collaboration-settings)
Tagok küldhetnek meghívót | A beállítás Igen beállításával a címtár nem rendszergazdai tagjai is meghívhat vendégeket. További [információ: Meghívók delegálása B2B-együttműködéshez.](../external-identities/delegate-invitations.md#configure-b2b-external-collaboration-settings)
A vendégmeghívó szerepkörű rendszergazdák és felhasználók küldhetnek meghívót | Ennek a beállításnak az Igen beállítása lehetővé teszi, hogy a "Vendég meghívója" szerepkörben a rendszergazdák és a felhasználók vendégfelhasználókat hívjanak meg. Ha igenre van állítva, a vendégmeghívó szerepkörben a felhasználók továbbra is meghívni tudják a vendégfelhasználót, függetlenül attól, hogy a Tagok meghívhat-e beállítást. További [információ: Meghívók delegálása B2B-együttműködéshez.](../external-identities/delegate-invitations.md#assign-the-guest-inviter-role-to-a-user)

## <a name="object-ownership"></a>Objektumok tulajdonjoga

### <a name="application-registration-owner-permissions"></a>Alkalmazásregisztrációs tulajdonosi engedélyek
Az alkalmazást regisztráló felhasználó automatikusan az adott alkalmazás tulajdonosává válik. Tulajdonosként kezelheti az alkalmazás metaadatait, például annak nevét és az előírt engedélyeket. Ezen kívül kezelheti az alkalmazás nem bérlőspecifikus konfigurációját, például az egyszeri bejelentkezést és a felhasználó-hozzárendelést. A tulajdonosok ezen kívül eltávolíthatnak vagy hozzáadhatnak más tulajdonosokat. A globális rendszergazdáktól eltérően a tulajdonosok csak saját alkalmazásaikat kezelhetik.

### <a name="enterprise-application-owner-permissions"></a>Vállalati alkalmazástulajdonosi engedélyek
Amikor egy felhasználó új vállalati alkalmazást ad hozzá, automatikusan tulajdonosként lesz hozzáadva. Tulajdonosként kezelhetik az alkalmazás bérlőspecifikus konfigurációját, például az SSO-konfigurációt, a kiépítést és a felhasználó-hozzárendeléseket. A tulajdonosok ezen kívül eltávolíthatnak vagy hozzáadhatnak más tulajdonosokat. A globális rendszergazdáktól eltérően a tulajdonosok csak a saját alkalmazásokat kezelhetik.

### <a name="group-owner-permissions"></a>Csoporttulajdonosi engedélyek
A csoportot létrehozó felhasználó automatikusan az adott csoport tulajdonosává válik. Tulajdonosként kezelheti a csoport tulajdonságait, például a nevet, valamint a csoporttagságot. A tulajdonosok ezen kívül eltávolíthatnak vagy hozzáadhatnak más tulajdonosokat. A globális rendszergazdáktól és a felhasználói rendszergazdáktól eltérően a tulajdonosok csak saját csoportokat kezelnek. A csoporttulajdonosok hozzárendelésével kapcsolatban lásd: [Csoporttulajdonosok kezelése](active-directory-accessmanagement-managing-group-owners.md).

### <a name="ownership-permissions"></a>Tulajdonosi engedélyek
Az alábbi táblázatok a tagfelhasználók saját objektumokhoz Azure Active Directory adott engedélyeinek leírására vonatkoznak. A felhasználó csak a tulajdonában lévő objektumokra rendelkezik ezekkel az engedélyekkel.

#### <a name="owned-application-registrations"></a>Saját alkalmazásregisztrációk
A felhasználók a következő műveleteket végezhetik el a saját tulajdonú alkalmazásregisztrációkon.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/applications/audience/update | Frissítse az applications.audience tulajdonságot a Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Frissítse az applications.authentication tulajdonságot a Azure Active Directory. |
| microsoft.directory/applications/basic/update | Frissítse az alkalmazások alapvető tulajdonságait a Azure Active Directory. |
| microsoft.directory/applications/credentials/update | Frissítse az applications.credentials tulajdonságot a Azure Active Directory. |
| microsoft.directory/applications/delete | Törölje az alkalmazások Azure Active Directory. |
| microsoft.directory/applications/owners/update | Frissítse az applications.owners tulajdonságot a Azure Active Directory. |
| microsoft.directory/applications/permissions/update | Frissítse az applications.permissions tulajdonságot a Azure Active Directory. |
| microsoft.directory/applications/policies/update | Frissítse az applications.policies tulajdonságot a Azure Active Directory. |
| microsoft.directory/applications/restore | Alkalmazások visszaállítása a Azure Active Directory. |

#### <a name="owned-enterprise-applications"></a>Saját nagyvállalati alkalmazások
A felhasználók a következő műveleteket végezhetik el a saját vállalati alkalmazásokon. A vállalati alkalmazások szolgáltatásnévből, egy vagy több alkalmazás-szabályzatból, és néha a szolgáltatásnévvel azonos bérlőn belül egy alkalmazásobjektumból is áll.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Olvassa el az auditLogs összes tulajdonságát (beleértve a kiemelt tulajdonságokat is) a Azure Active Directory. |
| microsoft.directory/policies/basic/update | Frissítse a házirendek alapvető tulajdonságait a Azure Active Directory. |
| microsoft.directory/policies/delete | Szabályzatok törlése a Azure Active Directory. |
| microsoft.directory/policies/owners/update | Frissítse a policies.owners tulajdonságot a Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Frissítse a servicePrincipals.appRoleAssignedTo tulajdonságot a Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Frissítse a users.appRoleAssignments tulajdonságot a Azure Active Directory. |
| microsoft.directory/servicePrincipals/audience/update | Frissítse a servicePrincipals.audience tulajdonságot a Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Frissítse a servicePrincipals.authentication tulajdonságot a Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Frissítse a servicePrincipals alapvető tulajdonságait a Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Frissítse a servicePrincipals.credentials tulajdonságot a Azure Active Directory. |
| microsoft.directory/servicePrincipals/delete | Törölje a servicePrincipals adatokat a Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Frissítse a servicePrincipals.owners tulajdonságot a Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | Frissítse a servicePrincipals.permissions tulajdonságot a Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Frissítse a servicePrincipals.policies tulajdonságot a Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Olvassa el az összes tulajdonságot (a kiemelt tulajdonságokat is beleértve) a következőben: signInReports Azure Active Directory. |

#### <a name="owned-devices"></a>Saját eszközök
A felhasználók a következő műveleteket végezhetik el a saját eszközökön.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Olvassa el a devices.bitLockerRecoveryKeys tulajdonságot a Azure Active Directory. |
| microsoft.directory/devices/disable | Tiltsa le az Azure Active Directory. |

#### <a name="owned-groups"></a>Saját csoportok
A felhasználók a következő műveleteket végezhetik el a saját csoportokon.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/groups/appRoleAssignments/update | Frissítse a groups.appRoleAssignments tulajdonságot a Azure Active Directory. |
| microsoft.directory/groups/basic/update | Frissítse az alapszintű tulajdonságokat a Azure Active Directory. |
| microsoft.directory/groups/delete | Csoportok törlése a Azure Active Directory. |
| microsoft.directory/groups/members/update | Frissítse a groups.members tulajdonságot a Azure Active Directory. |
| microsoft.directory/groups/owners/update | Frissítse a groups.owners tulajdonságot a Azure Active Directory. |
| microsoft.directory/groups/restore | Csoportok visszaállítása a Azure Active Directory. |
| microsoft.directory/groups/settings/update | Frissítse a groups.settings tulajdonságot a Azure Active Directory. |

## <a name="next-steps"></a>Következő lépések

* A vendégek felhasználói hozzáférés-korlátozási beállításával kapcsolatos további információkért lásd: Vendégelérési engedélyek korlátozása [(előzetes verzió)](../enterprise-users/users-restrict-guest-permissions.md)a Azure Active Directory.
* További információ az Azure AD-rendszergazdai szerepkörök hozzárendelésről: Felhasználó hozzárendelése rendszergazdai [szerepkörökhöz a](active-directory-users-assign-role-azure-portal.md) Azure Active Directory
* Az erőforrások hozzáférésének Microsoft Azure-ban történő kezeléséről további információért lásd: [Az erőforrások hozzáférésének megismerése az Azure-ban](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* További információk az Azure Active Directory és az Azure-előfizetés kapcsolatáról: [Hogyan kapcsolódnak az Azure-előfizetések az Azure Active Directoryhoz?](active-directory-how-subscriptions-associated-directory.md)
* [Felhasználók kezelése](add-users-azure-active-directory.md)

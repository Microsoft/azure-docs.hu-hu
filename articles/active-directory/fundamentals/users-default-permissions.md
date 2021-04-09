---
title: Alapértelmezett felhasználói engedélyek – Azure Active Directory | Microsoft Docs
description: További információ a Azure Active Directoryban elérhető különböző felhasználói engedélyekről.
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
ms.openlocfilehash: eb01a3e0fc5bc11a4d3de62b16aafb7dd308e34a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724271"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Melyek a Azure Active Directory alapértelmezett felhasználói engedélyei?
Az Azure Active Directoryban (Azure AD-ban) minden felhasználóhoz alapértelmezett engedélyek készlete tartozik. A felhasználók hozzáférése a felhasználó típusától, a [szerepkörük hozzárendeléseitől](active-directory-users-assign-role-azure-portal.md), valamint az egyes objektumok tulajdonosainak. Ez a cikk az alapértelmezett engedélyeket ismerteti, valamint összehasonlítja a tag- és vendégfelhasználók alapértelmezett beállításait. Az alapértelmezett felhasználói engedélyeket csak az Azure AD felhasználói beállításaiban lehet módosítani.

## <a name="member-and-guest-users"></a>Tag- és vendégfelhasználók
Az alapértelmezett engedélyek készlete attól függ, hogy a felhasználó a bérlő (tag) natív tagja-e, vagy ha a felhasználót VÁLLALATKÖZI együttműködési vendégként (vendég felhasználóként) átveszik egy másik címtárból. Lásd: [Mi az az Azure ad B2B Collaboration?](../external-identities/what-is-b2b.md) további információ a vendég felhasználók hozzáadásáról.
* A tagfelhasználók alkalmazásokat regisztrálhatnak, kezelhetik saját profilképüket és mobiltelefonszámukat, módosíthatják jelszavukat, valamint meghívhatnak B2B-vendégfelhasználókat. Ezen kívül a felhasználók a címtár összes adatát olvashatják (néhány kivétellel). 
* A vendég felhasználók korlátozott címtárbeli engedélyekkel rendelkeznek. Kezelhetik saját profiljaikat, módosíthatják saját jelszavukat, és lekérhetnek néhány információt más felhasználókról, csoportokról és alkalmazásokról, azonban nem tudják olvasni az összes címtár-információt. Például a vendég felhasználók nem tudják enumerálni az összes felhasználót, csoportot és más címtárobjektumok listáját. A vendégfelhasználókhoz rendszergazdai szerepkör rendelhető, amelynek birtokában az adott szerepkör teljes körű olvasási és írási engedélyeivel rendelkeznek. A vendégek más vendégeket is meghívhatnak.

## <a name="compare-member-and-guest-default-permissions"></a>Tagok és vendégek alapértelmezett engedélyeinek összehasonlítása

**Terület** | **Tagfelhasználók engedélyei** | **Alapértelmezett vendég felhasználói engedélyek** | **Korlátozott vendég felhasználói engedélyek (előzetes verzió)**
------------ | --------- | ---------- | ----------
Felhasználók és kapcsolatok | <ul><li>Az összes felhasználó és névjegy listájának enumerálása<li>Felhasználók és kapcsolatok összes nyilvános tulajdonságának olvasása</li><li>Vendégek meghívása<li>Saját jelszó módosítása<li>Saját mobiltelefonszám kezelése<li>Saját fénykép kezelése<li>Saját frissítési jogkivonatok érvénytelenítése</li></ul> | <ul><li>Saját tulajdonságok olvasása<li>A megjelenítendő név, az e-mail cím, a bejelentkezési név, a fénykép, a felhasználó egyszerű neve és a felhasználói típus tulajdonságainak olvasása más felhasználók és névjegyek számára<li>Saját jelszó módosítása<li>Másik felhasználó keresése ObjectId szerint (ha engedélyezve van)<li>A kezelő és a közvetlen jelentési információk beolvasása más felhasználók számára</li></ul> | <ul><li>Saját tulajdonságok olvasása<li>Saját jelszó módosítása</li></ul>
Csoportok | <ul><li>Biztonsági csoportok létrehozása<li>Microsoft 365 csoportok létrehozása<li>Az összes csoport felsorolása<li>Minden csoporttulajdonság olvasása<li>Nem rejtett csoporttagságok olvasása<li>Rejtett Microsoft 365 csoporttagságok olvasása az összekapcsolt csoportokhoz<li>A felhasználó tulajdonában lévő csoportok tulajdonságainak, tulajdonosának és tagságának kezelése<li>Vendégek hozzáadása saját csoportokhoz<li>Dinamikus tagsági beállítások kezelése<li>Saját csoportok törlése<li>Tulajdonosi Microsoft 365 csoportok visszaállítása</li></ul> | <ul><li>Nem rejtett csoportok tulajdonságainak olvasása, beleértve a tagságot és a tulajdonjogot (még nem csatlakoztatott csoportok)<li>Rejtett Microsoft 365 csoporttagság beolvasása az összekapcsolt csoportokhoz<li>Csoportok keresése megjelenítendő név vagy ObjectId szerint (ha engedélyezve van)</li></ul> | <ul><li>Objektum azonosítójának olvasása az összekapcsolt csoportokhoz<li>Az összekapcsolt csoportok tagságának és tulajdonosának olvasása néhány Microsoft 365 alkalmazásban (ha engedélyezve van)</li></ul>
Alkalmazások | <ul><li>Új alkalmazás regisztrálása (létrehozása)<li>Az összes alkalmazás felsorolása<li>Regisztrált és vállalati alkalmazások tulajdonságainak olvasása<li>Saját alkalmazások tulajdonságainak, hozzárendeléseinek és hitelesítő adatainak kezelése<li>Felhasználó alkalmazásjelszavának létrehozása vagy törlése<li>Saját alkalmazások törlése<li>Saját alkalmazások visszaállítása</li></ul> | <ul><li>Regisztrált és vállalati alkalmazások tulajdonságainak olvasása</li></ul> | <ul><li>Regisztrált és vállalati alkalmazások tulajdonságainak olvasása
Eszközök</li></ul> | <ul><li>Az összes eszköz listájának enumerálása<li>Minden eszköztulajdonság olvasása<li>Saját eszközök minden tulajdonságának olvasása</li></ul> | Nincs engedély | Nincs engedély
Címtár | <ul><li>Minden vállalati adat olvasása<li>Minden tartomány olvasása<li>Minden partnerszerződés olvasása</li></ul> | <ul><li>Cég megjelenítendő nevének olvasása<li>Minden tartomány olvasása</li></ul> | <ul><li>Cég megjelenítendő nevének olvasása<li>Minden tartomány olvasása</li></ul>
Szerepkörök és hatókörök | <ul><li>Minden rendszergazdai szerepkör és tagság olvasása<li>Felügyeleti egységek minden tulajdonságának és tagságának olvasása</li></ul> | Nincs engedély | Nincs engedély
Előfizetések | <ul><li>Minden előfizetés olvasása<li>Szolgáltatáscsomag-tag engedélyezése</li></ul> | Nincs engedély | Nincs engedély
Házirendek | <ul><li>Szabályzatok minden tulajdonságának olvasása<li>Saját szabályzat minden tulajdonságának kezelése</li></ul> | Nincs engedély | Nincs engedély

## <a name="restrict-member-users-default-permissions"></a>A tag felhasználói alapértelmezett engedélyeinek korlátozása 

A tagok felhasználói számára a következő módokon lehet korlátozni az alapértelmezett engedélyeket:

Engedély | Magyarázat beállítása
---------- | ------------
A felhasználók regisztrálhatják az alkalmazást | Ha ezt a beállítást állítja be, a nem megakadályozza, hogy a felhasználók alkalmazás-regisztrációt hozzanak létre. Az alkalmazás fejlesztői szerepkörhöz való hozzáadásával lehetősége lesz visszaadni az adott személyeknek.
Munkahelyi vagy iskolai fióknak a LinkedIn használatával való összekapcsolásának engedélyezése a felhasználók számára | Ha ezt a beállítást választja, a nem akadályozza meg, hogy a felhasználók a saját LinkedIn-fiókjával csatlakozzanak a munkahelyi vagy iskolai fiókjához. További információ: a [LinkedIn-fiókok kapcsolatainak megosztása és beleegyezett](../enterprise-users/linkedin-user-consent.md).
Biztonsági csoportok létrehozásának képessége | Ha e beállítás értéke Nem, akkor a felhasználók nem hozhatnak létre biztonsági csoportokat. A globális rendszergazdák és a felhasználói rendszergazdák továbbra is létrehozhatnak biztonsági csoportokat. További tudnivalók: [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](../enterprise-users/groups-settings-cmdlets.md).
Lehetőség Microsoft 365 csoportok létrehozására | Ha ezt a beállítást választja, nem akadályozza meg, hogy a felhasználók Microsoft 365 csoportokat hozzanak létre. Ha ezt a beállítást választja, a kiválasztott felhasználók közül választhat Microsoft 365 csoportok létrehozásához. A globális rendszergazdák és a felhasználói rendszergazdák továbbra is létrehozhatnak Microsoft 365 csoportokat. További tudnivalók: [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](../enterprise-users/groups-settings-cmdlets.md).
Az Azure AD felügyeleti portálhoz való hozzáférés korlátozása | Ha ezt a beállítást nem teszi lehetővé, a nem rendszergazdák az Azure ad felügyeleti portálon keresztül olvashatják és kezelhetik az Azure AD-erőforrásokat. Igen – korlátozza, hogy az összes nem rendszergazda hozzáférjen az összes Azure AD-adatokhoz a felügyeleti portálon.<p>**Megjegyzés**: Ez a beállítás nem korlátozza az Azure ad-adatokhoz való hozzáférést a PowerShell vagy más ügyfelek (például a Visual Studio) használatával. Ha az Igen értékre van állítva, egy adott nem rendszergazda felhasználó számára az Azure ad felügyeleti portált olyan rendszergazdai szerepkörök hozzárendelésére használhatja, mint például a címtár-olvasók szerepkör.<p>Ez a szerepkör lehetővé teszi az alapszintű címtáradatok olvasását, amelyekhez a felhasználók alapértelmezés szerint rendelkeznek (a vendégek és az egyszerű szolgáltatások nem).
Egyéb felhasználók olvasásának képessége | Ez a beállítás csak a PowerShellben érhető el. Ha ezt a jelzőt úgy állítja be, hogy $false megakadályozza, hogy minden nem rendszergazda beolvassa a felhasználói adatokat a címtárból. Ez a jelző nem akadályozza meg a felhasználói adatok olvasását más Microsoft-szolgáltatásokban, például az Exchange Online-ban. Ez a beállítás különleges körülményeket jelent, és a jelzőt nem ajánlott $falsere beállítani.

## <a name="restrict-guest-users-default-permissions"></a>A vendég felhasználói alapértelmezett engedélyeinek korlátozása

A vendég felhasználók alapértelmezett engedélyei a következő módokon korlátozhatók:

>[!NOTE]
>A vendég felhasználói hozzáférés korlátozásai beállítás lecserélte a vendég **felhasználók engedélyeinek** korlátozása beállítást. A szolgáltatás használatával kapcsolatos útmutatásért lásd: [a vendég hozzáférési engedélyeinek korlátozása (előzetes verzió) Azure Active Directory](../enterprise-users/users-restrict-guest-permissions.md).

Engedély | Magyarázat beállítása
---------- | ------------
Vendég felhasználói hozzáférési korlátozások (előzetes verzió) | Ha ezt a beállítást szeretné beállítani a **vendég felhasználók számára, a tagoknak alapértelmezés szerint ugyanaz a hozzáférése, mint a tag** felhasználói engedélyeket.<p>Ha ezt a beállítást szeretné beállítani a **vendég felhasználói hozzáféréshez, a saját címtárobjektumok tulajdonságaira és tagságára korlátozódik** , alapértelmezés szerint csak a saját felhasználói profiljára korlátozza a vendég hozzáférését. A többi felhasználóhoz való hozzáférés már nem engedélyezett, még akkor is, ha az egyszerű felhasználónév, a ObjectId vagy a megjelenítendő név alapján keres. A csoportokhoz való hozzáféréshez, beleértve a csoportok tagságát is, már nem engedélyezett.<p>**Megjegyzés**: Ez a beállítás nem akadályozza meg, hogy egyes Microsoft 365 szolgáltatások, például a Microsoft Teams összevont csoportjaihoz hozzáférjenek. További információért tekintse meg a [Microsoft Teams vendég hozzáférését](/MicrosoftTeams/guest-access) ismertető témakört.<p>A vendég felhasználók továbbra is hozzáadhatók a rendszergazdai szerepkörökhöz az engedély beállításaitól függetlenül.
Vendégek küldhetnek meghívót | Ha ezt a beállítást választja, az Igen lehetőséget választva a vendégek más vendégeket is meghívhatnak. További információért lásd: [meghívások delegálása B2B-együttműködéshez](../external-identities/delegate-invitations.md#configure-b2b-external-collaboration-settings) .
Tagok küldhetnek meghívót | Ha ezt a beállítást Igen értékre állítja, a címtár nem rendszergazdai tagjai meghívhatják a vendégeket. További információért lásd: [meghívások delegálása B2B-együttműködéshez](../external-identities/delegate-invitations.md#configure-b2b-external-collaboration-settings) .
A vendégmeghívó szerepkörű rendszergazdák és felhasználók küldhetnek meghívót | Ha ezt a beállítást az Igen értékre állítja, a "vendég meghívó" szerepkörben a rendszergazdák és a felhasználók meghívhatják a vendégeket. Ha az Igen értékre van állítva, akkor a vendég meghívó szerepkörben lévő felhasználók továbbra is meghívhatják a vendégeket, függetlenül attól, hogy a tagok meghívhatják a beállításokat. További információért lásd: [meghívások delegálása B2B-együttműködéshez](../external-identities/delegate-invitations.md#assign-the-guest-inviter-role-to-a-user) .

## <a name="object-ownership"></a>Objektumok tulajdonjoga

### <a name="application-registration-owner-permissions"></a>Alkalmazásregisztrációs tulajdonosi engedélyek
Az alkalmazást regisztráló felhasználó automatikusan az adott alkalmazás tulajdonosává válik. Tulajdonosként kezelheti az alkalmazás metaadatait, például annak nevét és az előírt engedélyeket. Ezen kívül kezelheti az alkalmazás nem bérlőspecifikus konfigurációját, például az egyszeri bejelentkezést és a felhasználó-hozzárendelést. A tulajdonosok ezen kívül eltávolíthatnak vagy hozzáadhatnak más tulajdonosokat. A globális rendszergazdáktól eltérően a tulajdonosok csak saját alkalmazásaikat kezelhetik.

### <a name="enterprise-application-owner-permissions"></a>Vállalati alkalmazás tulajdonosának engedélyei
Amikor egy felhasználó új vállalati alkalmazást ad hozzá, a rendszer automatikusan tulajdonosként adja hozzá őket. Tulajdonosként kezelheti az alkalmazás bérlő-specifikus konfigurációját, például az egyszeri bejelentkezés konfigurációját, a létesítést és a felhasználói hozzárendeléseket. A tulajdonosok ezen kívül eltávolíthatnak vagy hozzáadhatnak más tulajdonosokat. A globális rendszergazdáktól eltérően a tulajdonosok csak a saját maguk által felügyelt alkalmazásokat kezelhetik.

### <a name="group-owner-permissions"></a>Csoporttulajdonosi engedélyek
A csoportot létrehozó felhasználó automatikusan az adott csoport tulajdonosává válik. Tulajdonosként kezelheti a csoport tulajdonságait, például a nevet, valamint a csoporttagság kezelését. A tulajdonosok ezen kívül eltávolíthatnak vagy hozzáadhatnak más tulajdonosokat. A globális rendszergazdákkal és a felhasználói rendszergazdákkal ellentétben a tulajdonosok csak a saját maguk által kezelt csoportokat kezelhetik. A csoporttulajdonosok hozzárendelésével kapcsolatban lásd: [Csoporttulajdonosok kezelése](active-directory-accessmanagement-managing-group-owners.md).

### <a name="ownership-permissions"></a>Tulajdonosi engedélyek
A következő táblázatok ismertetik, hogy Azure Active Directory tag felhasználói többek között rendelkeznek-e a tulajdonos objektumaival. A felhasználó csak az Ön tulajdonában lévő objektumokra vonatkozó engedélyekkel rendelkezik.

#### <a name="owned-application-registrations"></a>Tulajdonban lévő alkalmazások regisztrációi
A felhasználók a következő műveleteket hajthatják végre a tulajdonosi alkalmazások regisztrációján.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. Directory/alkalmazások/célközönség/frissítés | Az Applications. célközönség tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/alkalmazások/hitelesítés/frissítés | Az Applications. Authentication tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/alkalmazások/alapszintű/frissítés | Azure Active Directory alkalmazásokban lévő alkalmazások alapszintű tulajdonságainak frissítése. |
| Microsoft. Directory/alkalmazások/hitelesítő adatok/frissítés | Az Applications. hitelesítőadatok tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/alkalmazások/törlés | Alkalmazások törlése Azure Active Directory. |
| Microsoft. Directory/alkalmazások/tulajdonosok/frissítés | Az Applications. owners tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/alkalmazások/engedélyek/frissítés | Az Applications. permissions tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/alkalmazások/házirendek/frissítés | Az Applications. policies tulajdonság frissítése Azure Active Directory. |
| Microsoft. Directory/alkalmazások/visszaállítás | Alkalmazások visszaállítása a Azure Active Directoryban. |

#### <a name="owned-enterprise-applications"></a>Birtokolt vállalati alkalmazások
A felhasználók a következő műveleteket hajthatják végre a tulajdonában lévő vállalati alkalmazásokban. A vállalati alkalmazások egyszerű szolgáltatásnév, egy vagy több alkalmazás-házirend, valamint esetenként egy, az egyszerű szolgáltatással azonos bérlőn lévő alkalmazás-objektum.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. Directory/auditLogs/allProperties/READ | Olvassa el az összes tulajdonságot (beleértve a privilegizált tulajdonságokat is) a auditLogs Azure Active Directory. |
| Microsoft. Directory/házirendek/alapszintű/frissítés | Azure Active Directory szabályzatok alapszintű tulajdonságainak frissítése. |
| Microsoft. Directory/házirendek/törlés | Azure Active Directory szabályzatok törlése. |
| Microsoft. Directory/házirendek/tulajdonosok/frissítés | A policies. owners tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/servicePrincipals/appRoleAssignedTo/Update | Frissítse a servicePrincipals. appRoleAssignedTo tulajdonságot a Azure Active Directoryban. |
| Microsoft. Directory/servicePrincipals/appRoleAssignments/Update | A users. appRoleAssignments tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/servicePrincipals/hallgatóság/frissítés | A servicePrincipals. célközönség tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/servicePrincipals/hitelesítés/frissítés | Frissítse a servicePrincipals. Authentication tulajdonságot a Azure Active Directoryban. |
| Microsoft. Directory/servicePrincipals/Basic/Update | A servicePrincipals alapszintű tulajdonságainak frissítése Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/hitelesítő adatok/frissítés | Frissítse a servicePrincipals. hitelesítőadatok tulajdonságot a Azure Active Directoryban. |
| Microsoft. Directory/servicePrincipals/delete | Azure Active Directory servicePrincipals törlése. |
| Microsoft. Directory/servicePrincipals/owners/Update | A servicePrincipals. owners tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/servicePrincipals/engedélyek/frissítés | Azure Active Directory servicePrincipals. permissions tulajdonságának frissítése. |
| Microsoft. Directory/servicePrincipals/házirendek/frissítés | Frissítse a servicePrincipals. policies tulajdonságot a Azure Active Directoryban. |
| Microsoft. Directory/signInReports/allProperties/READ | Olvassa el az összes tulajdonságot (beleértve a privilegizált tulajdonságokat is) a signInReports Azure Active Directory. |

#### <a name="owned-devices"></a>Tulajdonban lévő eszközök
A felhasználók a következő műveleteket hajthatják végre a tulajdonban lévő eszközökön.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. Directory/eszközök/bitLockerRecoveryKeys/olvasás | A Devices. bitLockerRecoveryKeys tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/eszközök/letiltás | Azure Active Directory eszközök letiltása. |

#### <a name="owned-groups"></a>Birtokolt csoportok
A felhasználók a következő műveleteket hajthatják végre a tulajdonos csoportoknál.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. Directory/csoportok/appRoleAssignments/Update | A groups. appRoleAssignments tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/csoportok/alapszintű/frissítés | Azure Active Directoryban lévő csoportok alapszintű tulajdonságainak frissítése. |
| Microsoft. Directory/csoportok/törlés | Csoportok törlése Azure Active Directory. |
| Microsoft. Directory/csoportok/dynamicMembershipRule/Update | A groups. dynamicMembershipRule tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/csoportok/tagok/frissítés | A groups. Members tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/csoportok/tulajdonosok/frissítés | A groups. owners tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/csoportok/visszaállítás | Csoportok visszaállítása a Azure Active Directoryban. |
| Microsoft. Directory/csoportok/beállítások/frissítés | A groups. Settings tulajdonság frissítése Azure Active Directoryban. |

## <a name="next-steps"></a>Következő lépések

* Ha többet szeretne megtudni a vendég felhasználói hozzáférési korlátozásokról, tekintse meg a következő témakört: a [vendég hozzáférési engedélyeinek korlátozása (előzetes verzió) Azure Active Directory](../enterprise-users/users-restrict-guest-permissions.md).
* További információ az Azure AD-rendszergazdai szerepkörök hozzárendeléséről: [felhasználó társítása rendszergazdai szerepkörökhöz Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
* Az erőforrások hozzáférésének Microsoft Azure-ban történő kezeléséről további információért lásd: [Az erőforrások hozzáférésének megismerése az Azure-ban](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* További információk az Azure Active Directory és az Azure-előfizetés kapcsolatáról: [Hogyan kapcsolódnak az Azure-előfizetések az Azure Active Directoryhoz?](active-directory-how-subscriptions-associated-directory.md)
* [Felhasználók kezelése](add-users-azure-active-directory.md)
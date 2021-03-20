---
title: Naplózott tevékenységekre vonatkozó jelentések az Azure Active Directory portálon | Microsoft Docs
description: Naplózott tevékenységekre vonatkozó jelentések az Azure Active Directory portálon – bevezetés
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 09/17/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5aa8f675e3fd36fbebfecf42db0f02b0f0f00115
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95995981"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Naplózott tevékenységekre vonatkozó jelentések az Azure Active Directory portálon 

A Azure Active Directory-(Azure AD-) jelentésekkel megtekintheti a környezete működésének meghatározásához szükséges információkat.



A jelentéskészítési architektúra a következő összetevőkből áll:

- **Tevékenység** 
    - **Bejelentkezések** – a [bejelentkezési jelentés](concept-sign-ins.md) információt nyújt a felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek használatáról.
    - **Naplók** – az Azure ad-n belüli különböző szolgáltatások által végzett módosítások naplókban való nyomon követését teszi lehetővé. A naplók rögzítik például az erőforrások módosításait az Azure AD-n belül, például a felhasználók, alkalmazások, csoportok, szerepkörök és szabályzatok hozzáadását vagy eltávolítását.
    - Naplók kiépítés   -  A [kiépítési naplók](./concept-provisioning-logs.md) lehetővé teszik az ügyfelek számára, hogy a kiépítési szolgáltatás által végzett tevékenységeket figyeljenek, például egy csoport létrehozását a ServiceNow-ben, vagy egy munkanapokból importált felhasználót. 
- **Biztonság** 
    - **Kockázatos bejelentkezések** – a [kockázatos bejelentkezés](../identity-protection/overview-identity-protection.md) egy olyan bejelentkezési kísérletre utal, amelyet a felhasználói fiók jogos tulajdonosaként elvégeztek. 
    - **Kockázatnak** kitett felhasználók – a [kockázatos felhasználók](../identity-protection/overview-identity-protection.md) egy olyan felhasználói fiókra vonatkozó jelző, amely esetleg sérült.

Ez a cikk áttekintést nyújt a naplózási jelentésről.
 
## <a name="who-can-access-the-data"></a>Ki férhet hozzá az adatokhoz?

* A **biztonsági rendszergazda**, a **biztonsági olvasó**, a **jelentéskészítő olvasó** , a **globális olvasó** vagy a **globális rendszergazdai** szerepkör felhasználói

## <a name="audit-logs"></a>Naplók

Az Azure AD-naplók megfelelőségi adatokat biztosítanak a rendszertevékenységekről. A naplózási jelentés eléréséhez válassza a **naplók** lehetőséget a **Azure Active Directory** **figyelés** szakaszában. 



Az auditnapló alapértelmezett listanézete az alábbi adatokat jeleníti meg:

- az előfordulás dátuma és időpontját
- az előfordulást naplózó szolgáltatás
- a tevékenység kategóriája és neve (*mit*) 
- a tevékenység állapota (sikeres vagy sikertelen)
- a célt
- a tevékenység kezdeményezőjét / szereplőjét (ki?)

![Auditnaplók](./media/concept-audit-logs/listview.png "Naplók")

A listanézetet az eszköztár **Oszlopok** elemére kattintva lehet testre szabni.

![Naplózási oszlopok](./media/concept-audit-logs/columns.png "Naplózási oszlopok")

További mezőket jeleníthet meg, vagy eltávolíthatja a már megjelenített mezőket.

![Mezők eltávolítása](./media/concept-audit-logs/columnselect.png "Mezők eltávolítása")

Részletesebb információkhoz jelöljön ki egy elemet a listanézet nézetben.

![elem kiválasztása](./media/concept-audit-logs/details.png "Elem kiválasztása")


## <a name="filtering-audit-logs"></a>Auditnaplók szűrése

A naplózási adat a következő mezőkön szűrhető:

- Szolgáltatás
- Kategória
- Tevékenység
- Állapot
- Cél
- Kezdeményező (Szereplő)
- Dátumtartomány

![Objektum szűrése](./media/concept-audit-logs/filter.png "Objektum szűrése")

A **szolgáltatás** szűrője lehetővé teszi, hogy a következő szolgáltatások legördülő listájából válasszon ki:

- Mind
- HRE-kezelés UX
- Hozzáférési felülvizsgálatok
- Fiók kiépítése
- Alkalmazásproxy
- Hitelesítési módszerek
- B2C
- Feltételes hozzáférés
- Alapvető könyvtár
- Jogosultságok kezelése
- Hibrid hitelesítés
- Identity Protection
- Meghívott felhasználók
- MIM szolgáltatás
- MyApps
- PIM
- Önkiszolgáló csoportkezelés
- Önkiszolgáló jelszókezelés
- Használati feltételek

A **Kategória** szűrővel a következő szűrők közül választhat:

- Mind
- AdministrativeUnit
- ApplicationManagement
- Hitelesítés
- Engedélyezés
- Kapcsolattartó
- Eszköz
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- KerberosDomain
- Kulcskezelő
- Címke
- Egyéb
- PermissionGrantPolicy
- Szabályzat
- ResourceManagement
- RoleManagement
- UserManagement

A **tevékenység** szűrője az Ön által megadott kategória és tevékenység erőforrástípus alapján van kiválasztva. Választhat egy adott tevékenységet, amelyet meg szeretne tekinteni, vagy kiválaszthatja az összeset. 

Az összes naplózási tevékenység listáját a Graph API használatával szerezheti be: `https://graph.windows.net/<tenantdomain>/activities/auditActivityTypesV2?api-version=beta`

Az **állapot** szűrő lehetővé teszi a szűrést egy naplózási művelet állapota alapján. Az állapot a következők egyike lehet:

- Mind
- Siker
- Hiba

A **célként** megadott szűrő lehetővé teszi egy adott cél keresését a név vagy az egyszerű felhasználónév (UPN) megkezdésével. A cél neve és az UPN megkülönbözteti a kis-és nagybetűket. 

A **kezdeményező** szűrővel megadhatja, hogy a színész neve vagy egy univerzális egyszerű felhasználónév (UPN) kezdődjön. A név és az UPN megkülönbözteti a kis-és nagybetűket.

A **dátumtartomány** -szűrő lehetővé teszi, hogy meghatározza a visszaadott adatok időkeretét.  
Lehetséges értékek:

- 7 nap
- 24 óra
- Egyéni

Egyéni időkeret kiválasztásakor beállíthatja a kezdő és a záró időpontot.

Azt is megteheti, hogy letölti a szűrt adatokat, legfeljebb 250 000 rekordot a **Letöltés** gombra kattintva. A naplókat CSV-vagy JSON-formátumban is letöltheti. A letöltendő rekordok számát a [Azure Active Directory jelentés adatmegőrzési szabályzata](reference-reports-data-retention.md)korlátozza.

![Adatok letöltése](./media/concept-audit-logs/download.png "Adatok letöltése")

## <a name="audit-logs-shortcuts"></a>Rövidebb utak a naplók eléréséhez

Az **Azure Active Directory** mellett az Azure Portal két további lehetőséget biztosít a naplózási adatok elérésére:

- Felhasználók és csoportok
- Vállalati alkalmazások

### <a name="users-and-groups-audit-logs"></a>Felhasználók és csoportok auditnaplói

A felhasználó- és csoportalapú naplózási jelentésekkel az alábbi kérdésekre kaphat választ:

- Milyen típusú frissítések lettek alkalmazva a felhasználókra?

- Hány felhasználó lett módosítva?

- Hány jelszó lett módosítva?

- Mit csinált a rendszergazda egy adott címtárban?

- Mely csoportok lettek hozzáadva?

- Történt-e tagsági változás valamelyik csoportban?

- Változtak-e a csoportok tulajdonosai?

- Milyen licencek lettek hozzárendelve egy adott csoporthoz vagy felhasználóhoz?

Ha csak a felhasználókhoz kapcsolódó naplózási adatelemzést szeretné áttekinteni, a **felhasználók** lap **figyelés** területén a **naplók** területen található szűrt nézetet is megtalálhatja. Ennek a belépési pontnak a **UserManagement** van, mint az előválasztott kategória.

![Felhasználó](./media/concept-audit-logs/users.png "User")

Ha csak a csoportokhoz kapcsolódó naplózási adatellenőrzéseket szeretné áttekinteni, a **csoportok** lap **figyelés** területén a **naplók** területen található szűrt nézeteket is megtalálhatja. Ennek a belépési pontnak a **GroupManagement** van, mint az előválasztott kategória.

![Csoportok szűrése](./media/concept-audit-logs/groups.png "Csoportok szűrése")

### <a name="enterprise-applications-audit-logs"></a>Vállalati alkalmazások naplói

Az alkalmazásalapú naplózási jelentésekkel az alábbi kérdésekre kaphat választ:

* Mely alkalmazások lettek hozzáadva vagy frissítve?
* Mely alkalmazások lettek eltávolítva?
* Megváltoztak az alkalmazás egy egyszerű szolgáltatása?
* Történt változás az alkalmazások nevében?
* Ki hagyott jóvá egy adott alkalmazást?

Ha szeretné áttekinteni az alkalmazásokkal kapcsolatos naplózási adatait, a **vállalati alkalmazások** panel **tevékenység** szakaszában talál egy szűrt nézetet a **naplók** területen. Ehhez a belépési ponthoz az **alkalmazás típusaként** megadott **vállalati alkalmazások** vannak előválasztva.

![Vállalati alkalmazások](./media/concept-audit-logs/enterpriseapplications.png "Vállalati alkalmazások")

## <a name="microsoft-365-activity-logs"></a>Microsoft 365 tevékenység naplói

A [Microsoft 365 felügyeleti központban](/office365/admin/admin-overview/about-the-admin-center)megtekintheti Microsoft 365 tevékenység naplóit. Annak ellenére, hogy Microsoft 365 tevékenység és az Azure AD-tevékenység naplói nagy mennyiségű címtár-erőforrást osztanak meg, csak a Microsoft 365 felügyeleti központ biztosít teljes képet a Microsoft 365 tevékenység naplófájljairól. 

Az [Office 365 felügyeleti API](/office/office-365-management-api/office-365-management-apis-overview)-k használatával programozott módon is elérheti a Microsoft 365 tevékenység naplóit.

## <a name="next-steps"></a>Következő lépések

- [Azure AD naplózott tevékenységekre vonatkozó referencia](reference-audit-activities.md)
- [Azure AD-jelentések adatmegőrzési referenciája](reference-reports-data-retention.md)
- [Az Azure AD log késési referenciája](reference-reports-latencies.md)
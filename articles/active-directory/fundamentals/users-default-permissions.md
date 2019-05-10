---
title: Alapértelmezett felhasználói engedélyek – Azure Active Directory |} A Microsoft Docs
description: További információ a különböző felhasználói engedélyek elérhető az Azure Active Directoryban.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: lizross
ms.reviewer: vincesm
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb05ee4d6e05cb8b56756a761a519e5903b78bbd
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65507094"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Mik azok az alapértelmezett felhasználói engedélyek az Azure Active Directoryban?
Az Azure Active Directoryban (Azure AD-ban) minden felhasználóhoz alapértelmezett engedélyek készlete tartozik. A felhasználók hozzáférése a felhasználó típusát áll a [szerepkör-hozzárendelések](active-directory-users-assign-role-azure-portal.md), és az egyes objektumok tulajdonjogának. Ez a cikk az alapértelmezett engedélyeket ismerteti, valamint összehasonlítja a tag- és vendégfelhasználók alapértelmezett beállításait. Az alapértelmezett felhasználói engedélyek csak a felhasználói beállítások módosítható az Azure ad-ben.

## <a name="member-and-guest-users"></a>Tag- és vendégfelhasználók
A kapott alapértelmezett engedélyek készletét függ, a felhasználó a bérlő (a felhasználó tag) natív tagja-e, vagy ha a felhasználó van telepítik át egy másik címtárból B2B-együttműködés vendégként (vendégfelhasználó). Lásd: [Mi az Azure AD B2B együttműködés?](../b2b/what-is-b2b.md) vendégfelhasználók hozzáadásáról további információt.
* A tagfelhasználók alkalmazásokat regisztrálhatnak, kezelhetik saját profilképüket és mobiltelefonszámukat, módosíthatják jelszavukat, valamint meghívhatnak B2B-vendégfelhasználókat. Ezen kívül a felhasználók a címtár összes adatát olvashatják (néhány kivétellel). 
* Vendégfelhasználók korlátozta a directory-engedélyeket. A vendégfelhasználók például csak a saját profiljuk adatait látják a bérlőn. A vendégfelhasználók azonban egy másik felhasználó egyszerű felhasználónevének vagy ObjectId azonosítójának megadásával adatokat kérdezhetnek le az adott felhasználóról. Vendégfelhasználó olvashatja tartoznak, beleértve a biztonságicsoport-tagság függetlenül a csoport tulajdonságait a **a vendégfelhasználók engedélyei korlátozottak** beállítás. A vendég nem jelennek meg a többi bérlő objektum kapcsolatos információkat.

A vendégekre vonatkozó alapértelmezett engedélyek korlátozásokat tartalmaznak. A vendégfelhasználókhoz rendszergazdai szerepkör rendelhető, amelynek birtokában az adott szerepkör teljes körű olvasási és írási engedélyeivel rendelkeznek. Még egy további korlátozás áll rendelkezésre a vendégfelhasználók számára, ez pedig a más felhasználók meghívásának lehetősége. Ha a **Vendégek meghívót küldhetnek** beállítás értéke **Nem**, akkor a vendégfelhasználók nem hívhatnak meg más vendégeket. További információ: [Meghívók delegálása B2B-együttműködéshez](../b2b/delegate-invitations.md). Ha azt szeretné, hogy a vendégfelhasználók a tagfelhasználók alapértelmezett engedélyeivel azonos engedélyeket kapjanak, akkor **A vendégfelhasználók engedélyei korlátozottak** beállítás számára adjon meg **Nem** értéket. E beállítás alkalmazása esetén a vendégfelhasználók megkapják a tagfelhasználók minden alapértelmezett engedélyét, továbbá a vendégfelhasználók felvehetők lesznek a rendszergazdai szerepkörökbe is.

## <a name="compare-member-and-guest-default-permissions"></a>Tagok és vendégek alapértelmezett engedélyeinek összehasonlítása

**Terület** | **Tagfelhasználók engedélyei** | **Vendégfelhasználók engedélyei**
------------ | --------- | ----------
Felhasználók és kapcsolatok | Felhasználók és kapcsolatok összes nyilvános tulajdonságának olvasása<br>Vendégek meghívása<br>Saját jelszó módosítása<br>Saját mobiltelefonszám kezelése<br>Saját fénykép kezelése<br>Saját frissítési jogkivonatok érvénytelenítése | Saját tulajdonságok olvasása<br>Más felhasználók és kapcsolatok megjelenített nevének, e-mail-címének, bejelentkezési nevének, fényképének, egyszerű felhasználónevének, valamint felhasználótípus-tulajdonságainak olvasása<br>Saját jelszó módosítása
Csoportok | Biztonsági csoportok létrehozása<br>Office 365-csoportok létrehozása<br>Minden csoporttulajdonság olvasása<br>Nem rejtett csoporttagságok olvasása<br>Felvett csoport rejtett Office 365-csoporttagságainak olvasása<br>Tulajdonságok, tulajdonosi és a felhasználó tulajdonában lévő csoportok tagságát kezelése<br>Vendégek hozzáadása saját csoportokhoz<br>Dinamikus tagsági beállítások kezelése<br>Saját csoportok törlése<br>Saját Office 365-csoportok visszaállítása | Minden csoporttulajdonság olvasása<br>Nem rejtett csoporttagságok olvasása<br>Csatlakozott csoport rejtett Office 365-csoporttagságainak olvasása<br>Saját csoportok kezelése<br>Vendégek hozzáadása saját csoportokhoz (ha engedélyezett)<br>Saját csoportok törlése<br>Saját Office 365-csoportok visszaállítása<br>Olvassa el a tartoznak, beleértve a tagsági csoportok tulajdonságait.
Alkalmazások | Új alkalmazás regisztrálása (létrehozása)<br>Regisztrált és vállalati alkalmazások tulajdonságainak olvasása<br>Saját alkalmazások tulajdonságainak, hozzárendeléseinek és hitelesítő adatainak kezelése<br>Felhasználó alkalmazásjelszavának létrehozása vagy törlése<br>Saját alkalmazások törlése<br>Saját alkalmazások visszaállítása | Regisztrált és vállalati alkalmazások tulajdonságainak olvasása<br>Saját alkalmazások tulajdonságainak, hozzárendeléseinek és hitelesítő adatainak kezelése<br>Saját alkalmazások törlése<br>Saját alkalmazások visszaállítása
Eszközök | Minden eszköztulajdonság olvasása<br>Saját eszközök minden tulajdonságának olvasása<br> | Nincs engedély<br>Saját eszközök törlése<br>
Címtár | Minden vállalati adat olvasása<br>Minden tartomány olvasása<br>Minden partnerszerződés olvasása | Megjelenített név és ellenőrzött tartományok olvasása
Szerepkörök és hatókörök | Minden rendszergazdai szerepkör és tagság olvasása<br>Felügyeleti egységek minden tulajdonságának és tagságának olvasása | Nincs engedély 
Előfizetések | Minden előfizetés olvasása<br>Szolgáltatáscsomag-tag engedélyezése | Nincs engedély
Házirendek | Szabályzatok minden tulajdonságának olvasása<br>Saját szabályzat minden tulajdonságának kezelése | Nincs engedély

## <a name="to-restrict-the-default-permissions-for-member-users"></a>Tagfelhasználók alapértelmezett engedélyeinek korlátozása

A tagfelhasználók alapértelmezett engedélyeinek korlátozására az alábbi lehetőségek állnak rendelkezésre.

Engedély | Magyarázat beállítása
---------- | ------------
Felhasználók regisztrálhatják-alkalmazás | Ezt a beállítást nem megakadályozza, hogy a felhasználók az alkalmazást az alkalmazásregisztrációk hozza létre. Lehetővé teszi majd adható vissza a megadott személyeknek a alkalmazásfejlesztő szerepkör hozzáadásával.
Munkahelyi vagy iskolai fiók és a LinkedIn összekapcsolásának engedélyezése a felhasználóknak | Ezt a beállítást nem megakadályozza, hogy a felhasználók munkahelyi vagy iskolai fiókjával kapcsolódjon a saját LinkedIn-fiók.  Lásd: [LinkedIn-fiók kapcsolatok az adatok megosztása és jóváhagyás](https://docs.microsoft.com/azure/active-directory/users-groups-roles/linkedin-user-consent) további információt.
Biztonsági csoportok létrehozásának képessége | Ha e beállítás értéke Nem, akkor a felhasználók nem hozhatnak létre biztonsági csoportokat. A globális rendszergazdák és felhasználói rendszergazdák is létrehozhatnak biztonsági csoportokat. További tudnivalók: [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](../users-groups-roles/groups-settings-cmdlets.md).
Office 365-csoportok létrehozásának képessége | Ha e beállítás értéke Nem, akkor a felhasználók nem hozhatnak létre Office 365-csoportokat. Ha e beállítás értéke Néhány, akkor egyes kiválasztott felhasználók létrehozhatnak Office 365-csoportokat. A globális rendszergazdák és felhasználói rendszergazdák továbbra is képesek lesznek az Office 365-csoportok létrehozásához. További tudnivalók: [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](../users-groups-roles/groups-settings-cmdlets.md).
Az Azure AD felügyeleti portálhoz való hozzáférés korlátozása | Ezt a beállítást nem megakadályozza, hogy a felhasználók hozzáférését az Azure Active Directoryban.
Egyéb felhasználók olvasásának képessége | Ez a beállítás csak a PowerShellben érhető el. Ha e beállítás értéke $false, akkor a nem rendszergazda felhasználók nem olvashatják a címtárban található felhasználói adatokat. Ez nem jelenti azt, hogy ne olvashatnák egyéb Microsoft-szolgáltatások (például az Exchange Online) felhasználói adatait. Ez a beállítás speciális esetekben alkalmazandó, ajánlott értéke: $false.

## <a name="object-ownership"></a>Objektumok tulajdonjoga

### <a name="application-registration-owner-permissions"></a>Alkalmazásregisztrációs tulajdonosi engedélyek
Az alkalmazást regisztráló felhasználó automatikusan az adott alkalmazás tulajdonosává válik. Tulajdonosként kezelheti az alkalmazás metaadatait, például annak nevét és az előírt engedélyeket. Ezen kívül kezelheti az alkalmazás nem bérlőspecifikus konfigurációját, például az egyszeri bejelentkezést és a felhasználó-hozzárendelést. A tulajdonosok ezen kívül eltávolíthatnak vagy hozzáadhatnak más tulajdonosokat. A globális rendszergazdáktól eltérően a tulajdonosok csak saját alkalmazásaikat kezelhetik.

<!-- ### Enterprise application owner permissions

When a user adds a new enterprise application, they are automatically added as an owner for the tenant-specific configuration of the application. As an owner, they can manage the tenant-specific configuration of the application, such as the SSO configuration, provisioning, and user assignments. An owner can also add or remove other owners. Unlike Global Administrators, owners can manage only the applications they own. <!--To assign an enterprise application owner, see *Assigning Owners for an Application*.-->

### <a name="group-owner-permissions"></a>Csoporttulajdonosi engedélyek

A csoportot létrehozó felhasználó automatikusan az adott csoport tulajdonosává válik. Tulajdonosként azokat is, például a nevét a csoport tulajdonságainak kezelése, valamint csoporttagság kezelése. A tulajdonosok ezen kívül eltávolíthatnak vagy hozzáadhatnak más tulajdonosokat. A globális rendszergazdák és felhasználói rendszergazdák eltérően tulajdonosai csak saját csoportok kezelésére. A csoporttulajdonosok hozzárendelésével kapcsolatban lásd: [Csoporttulajdonosok kezelése](active-directory-accessmanagement-managing-group-owners.md).

## <a name="next-steps"></a>További lépések

* Az Azure AD-rendszergazdai szerepkörök hozzárendelése kapcsolatos további információkért lásd: [felhasználó az Azure Active Directory rendszergazdai szerepkörök hozzárendelése](active-directory-users-assign-role-azure-portal.md)
* Az erőforrások hozzáférésének Microsoft Azure-ban történő kezeléséről további információért lásd: [Az erőforrások hozzáférésének megismerése az Azure-ban](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* További információk az Azure Active Directory és az Azure-előfizetés kapcsolatáról: [Hogyan kapcsolódnak az Azure-előfizetések az Azure Active Directoryhoz?](active-directory-how-subscriptions-associated-directory.md)
* [Felhasználók kezelése](add-users-azure-active-directory.md)

---
title: Felhőcsoportok használata szerepkör-hozzárendelések kezeléséhez a Azure Active Directory | Microsoft Docs
description: Az egyéni Azure AD-szerepkörök előzetes verziója az identitáskezelés delegálható. Azure-beli szerepkör-hozzárendelések kezelése a Azure Portal, a PowerShellben vagy a Graph API.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1fea64305d4735c5bf1bf59b86ae5283600e622
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502198"
---
# <a name="use-cloud-groups-to-manage-role-assignments-in-azure-active-directory-preview"></a>Szerepkör-hozzárendelések kezelése felhőcsoportok használatával a Azure Active Directory (előzetes verzió)

Azure Active Directory (Azure AD) bevezet egy nyilvános előzetes verziót, amelyben felhőcsoportokat rendelhet hozzá az Azure AD beépített szerepköreihez. Ezzel a funkcióval a csoportokkal minimális erőfeszítéssel adhat rendszergazdai hozzáférést az Azure AD-hez a globális és kiemelt szerepkörű rendszergazdáktól.

Tekintsük ezt a példát: A Contoso különböző földrajzi területekre alkalmazott személyeket az Azure AD-szervezet alkalmazottainak jelszavának kezeléséhez és visszaállításához. Ahelyett, hogy megkérne egy kiemelt szerepkörű rendszergazdát vagy globális rendszergazdát, hogy minden egyes személyhez külön rendelje hozzá a rendszergazdai szerepkört, létrehozhat egy Contoso_Helpdesk_Administrators-csoportot, és hozzárendelheti a szerepkörhöz. Amikor valaki csatlakozik a csoporthoz, közvetett módon lesz hozzárendelve a szerepkör. A meglévő cégirányítási munkafolyamat ezután gondoskodhat a csoporttagság jóváhagyási folyamatáról és naplózásáról annak érdekében, hogy csak a megbízható felhasználók tagjai a csoportnak, és így az adminisztrátori szerepkörhöz legyen rendelve.

## <a name="how-this-feature-works"></a>A funkció működése

Hozzon létre egy Microsoft 365 vagy biztonsági csoportot, és állítsa az isAssignableToRole tulajdonságot "true" (igaz) értékre. Ezt a tulajdonságot akkor is engedélyezheti, amikor csoportot hoz létre a Azure Portal **az Azure AD-szerepkörök** bekapcsolásával hozzárendelhető a csoporthoz. A csoportot így is hozzárendelheti egy vagy több Azure AD-szerepkörhöz, ugyanúgy, ahogyan a szerepköröket a felhasználókhoz rendeli. Egy Azure AD-szervezetben (bérlőben) legfeljebb 250 szerepkört hozzárendelhető csoport lehet létrehozni.

Ha nem szeretné, hogy a csoport tagjai állandó hozzáféréssel hozzáférjenek a szerepkörhöz, használja a Azure AD Privileged Identity Management. Rendeljen hozzá egy csoportot egy Azure AD-szerepkör jogosult tagjaként. A csoport minden tagja ezután aktiválhatja a hozzárendelését ahhoz a szerepkörhöz, amelyhez a csoport hozzá van rendelve. Ezután aktiválni tudják a szerepkör-hozzárendelésüket egy meghatározott időtartamra.

> [!Note]
> Az Azure AD-szerepkörhöz a Privileged Identity Management kell lennie, hogy a PIM-en keresztül hozzárendelni tudja a csoportokat az Azure AD-szerepkörhöz. Lehet, hogy a PIM régebbi verzióját használja, mert az Azure AD-szervezet a Privileged Identity Management API-t használja. A szervezet áthelyezéséhez és az API frissítéséhez forduljon pim_preview@microsoft.com az aliashoz. További információ: [Azure AD-szerepkörök és -funkciók a PIM-ban.](../privileged-identity-management/azure-ad-roles-features.md)

## <a name="why-we-enforce-creation-of-a-special-group-for-assigning-it-to-a-role"></a>Miért kényszerítjük ki egy speciális csoport létrehozását a szerepkörhöz való hozzárendeléshez?

Ha egy csoporthoz hozzá van rendelve egy szerepkör, akkor a csoporttagságot kezelni tudó rendszergazdák közvetve is kezelhetik a szerepkör tagságát. Tegyük fel például, hogy egy Contoso_User_Administrators felhasználói fiók rendszergazdai szerepkörhöz van rendelve. A csoporttagságot módosítani képes Exchange-rendszergazda hozzáadhatja magát a Contoso_User_Administrators csoporthoz, és így felhasználói fiókadminisztrátának válhat. Amint látható, egy rendszergazda olyan módon emelheti a jogosultságát, ahogyan Ön nem kívánta.

Az Azure AD lehetővé teszi egy szerepkörhöz rendelt csoport védelmét egy új, isAssignableToRole nevű tulajdonság használatával a csoportok számára. Csak azok a felhőbeli csoportok rendelhetők hozzá egy szerepkörhöz, amelyek az isAssignableToRole tulajdonság "true" (igaz) értékre voltak állítva. Ez a tulajdonság nem módosítható; Ha a tulajdonság "true" (igaz) értékre van állítva, a csoport létrehozása után az nem módosítható. Meglévő csoporton nem állíthatja be a tulajdonságot.
Úgy alakítottuk ki, hogyan történjen a csoportok szerepkörökhöz való hozzárendelése, hogy megakadályozza az ilyen típusú biztonsági incidensek beesésének megelőzését:

- Csak a globális rendszergazdák és a kiemelt szerepkörű rendszergazdák hozhatnak létre szerepkörhöz hozzárendelhető csoportot (engedélyezett "isAssignableToRole" tulajdonsággal).
- Nem lehet Dinamikus Azure AD-csoport; ez azt jelenti, hogy a tagságának "Hozzárendelt" típusúnak kell lennie. A dinamikus csoportok automatizált populációja egy nemkívánatos fióknak a csoporthoz adódik, és így hozzá lesz rendelve a szerepkörhöz.
- Alapértelmezés szerint csak a globális rendszergazdák és a kiemelt szerepkörű rendszergazdák kezelhetik egy szerepkörhöz hozzárendelhető csoport tagságát, csoporttulajdonosok hozzáadásával azonban delegálhatja a szerepkörhöz hozzárendelhető csoportok felügyeletét.
- A jogosultsági szint emelésének megakadályozása érdekében a szerepkörhöz hozzárendelhető csoportok tagjainak és tulajdonosainak hitelesítő adatai csak emelt szintű jogosultságokkal rendelkező hitelesítési rendszergazda vagy globális rendszergazda.
- Nincs beágyazás. Szerepkör által hozzárendelhető csoport tagjaként nem lehet csoportot hozzáadni.

## <a name="limitations"></a>Korlátozások

Az alábbi forgatókönyvek jelenleg nem támogatottak:  

- Helyszíni csoportok hozzárendelése Azure AD-szerepkörökhöz (beépített vagy egyéni)

## <a name="known-issues"></a>Ismert problémák

- *Csak Az Azure AD P2* licenccel rendelkező ügyfelek esetében: Ne rendeljen aktív csoportokat egy szerepkörhöz az Azure AD-ben és a Privileged Identity Management (PIM-ben) keresztül. Pontosabban, ne rendeljen hozzá szerepkört szerepkör-hozzárendelhető csoporthoz  annak létrehozásakor, és később rendeljen hozzá egy szerepkört a csoporthoz a PIM használatával. Ez olyan problémákhoz vezet, amelyek miatt a felhasználók nem láthatják az aktív szerepkör-hozzárendeléseiket a PIM-ban, és nem lehet eltávolítani a PIM-hozzárendelést. Ebben a forgatókönyvben a jogosult hozzárendelések nem érintettek. Ha megkísérli ezt a hozzárendelést, váratlan viselkedést láthat, például:
  - Előfordulhat, hogy a szerepkör-hozzárendelés záró ideje helytelenül jelenik meg.
  - A PIM-portál saját szerepkörei csak egy szerepkör-hozzárendelést mutatnak, függetlenül **attól,** hogy hány módszerrel adható meg a hozzárendelés (egy vagy több csoporton keresztül és közvetlenül).
- *Csak Azure AD P2 licenccel rendelkező ügyfelek* Még a csoport törlése után is a szerepkör jogosult tagja lesz a PIM felhasználói felületén. Funkcionálisan nincs probléma; ez csak egy gyorsítótárral van probléma a Azure Portal.  
- [Csoporttagságon keresztüli](https://admin.exchange.microsoft.com/) szerepkör-hozzárendeléshez használja az új Exchange felügyeleti központot. A régi Exchange felügyeleti központ még nem támogatja ezt a funkciót. Az Exchange PowerShell-parancsmagok a várt módon fognak működni.
- Azure Information Protection portál (a klasszikus portál) még nem ismeri fel a csoporton keresztüli szerepkörtagságot. Az egységes [bizalmasság címkézési](/azure/information-protection/configure-policy-migrate-labels) platformra való áttelepítés után az Office 365 Biztonsági & megfelelőségi központ segítségével csoport-hozzárendeléseket használhat a szerepkörök kezeléséhez.
- [Az Alkalmazások felügyeleti központ](https://config.office.com/) még nem támogatja ezt a funkciót. Felhasználók hozzárendelése közvetlenül az Office-alkalmazások rendszergazdai szerepköréhez.
- [Az M365 Megfelelőségi központ](https://compliance.microsoft.com/) még nem támogatja ezt a funkciót. Felhasználók közvetlen hozzárendelése a megfelelő Azure AD-szerepkörökhöz a portál használatára.

Megoldjuk ezeket a problémákat.

## <a name="required-license-plan"></a>Szükséges licenc csomag

A funkció használatához rendelkezésre álló P1 prémium szintű Azure AD kell rendelkezésre álló Azure AD-szervezetben. A megfelelő Privileged Identity Management aktiválásához is szükség van egy P2 licenccel prémium szintű Azure AD használatára. A követelményeknek megfelelő licenc megkeresésért lásd: Az ingyenes és a prémium szintű csomagok általánosan elérhető [funkcióinak összehasonlítása.](../fundamentals/active-directory-whatis.md#what-are-the-azure-ad-licenses)

## <a name="next-steps"></a>Következő lépések

- [Szerepkörhöz hozzárendelhető csoport létrehozása](groups-create-eligible.md)
- [Szerepkör hozzárendelése szerepkörhöz hozzárendelhető csoporthoz](groups-assign-role.md)

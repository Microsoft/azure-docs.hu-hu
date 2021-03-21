---
title: Felhőalapú csoportok használata a szerepkör-hozzárendelések kezeléséhez Azure Active Directoryban | Microsoft Docs
description: Az Identitáskezelés delegálásához egyéni Azure AD-szerepköröket tekinthet meg. Az Azure szerepkör-hozzárendelések kezelése a Azure Portal, a PowerShell vagy a Graph API használatával.
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
ms.openlocfilehash: 5df7088551e7e7f616077342b762baca179f8640
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102123360"
---
# <a name="use-cloud-groups-to-manage-role-assignments-in-azure-active-directory-preview"></a>Felhőalapú csoportok használata a szerepkör-hozzárendelések kezeléséhez Azure Active Directory (előzetes verzió)

Azure Active Directory (Azure AD) egy nyilvános előzetes verzió bevezetése, amelyben hozzáadhat egy felhőalapú csoportot az Azure AD beépített szerepköreihez. Ezzel a funkcióval a csoportok segítségével rendszergazdai hozzáférést biztosíthat az Azure AD-ben a globális és a Kiemelt szerepkörű rendszergazdák minimális erőfeszítésével.

Vegyük például a következő példát: a contoso az Azure AD-szervezetében dolgozó alkalmazottak jelszavainak kezeléséhez és alaphelyzetbe állításához földrajzilag különböző személyeket bérelt fel. Ahelyett, hogy egy kiemelt szerepkörű rendszergazdának vagy globális rendszergazdának kellene kiosztania az ügyfélszolgálati rendszergazda szerepkört mindegyik személy számára, létrehozhat egy Contoso_Helpdesk_Administrators csoportot, és hozzárendelheti azt a szerepkörhöz. Ha valaki csatlakozik a csoporthoz, a szerepkört közvetve rendeli hozzá. A meglévő irányítási munkafolyamat ezután gondoskodik a csoport tagságának jóváhagyási folyamatáról és ellenőrzéséről, hogy csak a megbízható felhasználók legyenek a csoport tagjai, és így az ügyfélszolgálati rendszergazdai szerepkörhöz legyenek rendelve.

## <a name="how-this-feature-works"></a>A szolgáltatás működése

Hozzon létre egy új Microsoft 365 vagy biztonsági csoportot az "isAssignableToRole" tulajdonsággal "true" értékre. Ezt a tulajdonságot akkor is engedélyezheti, ha az **Azure ad-szerepköröket a csoporthoz való** bekapcsolásával létrehoz egy csoportot a Azure Portal. Mindkét esetben hozzárendelhet egy vagy több Azure AD-szerepkörhöz a csoportot ugyanúgy, ahogyan a szerepköröket a felhasználókhoz rendeli. Egyetlen Azure AD-szervezetben (bérlőben) legfeljebb 250 szerepkör-hozzárendelhető csoport hozható létre.

Ha nem szeretné, hogy a csoport tagjai rendelkezzenek állandó hozzáféréssel a szerepkörhöz, használhatja a Azure AD Privileged Identity Management. Rendeljen hozzá egy csoportot egy Azure AD-szerepkör jogosult tagjaként. A csoport minden tagja ezután jogosult a hozzárendelés aktiválására ahhoz a szerepkörhöz, amelyhez a csoport hozzá van rendelve. Ezután aktiválják a szerepkör-hozzárendelést egy rögzített időtartamra.

> [!Note]
> A Privileged Identity Management frissített verziójának kell lennie ahhoz, hogy a PIM használatával hozzá lehessen rendelni egy csoportot az Azure AD-szerepkörhöz. Előfordulhat, hogy a PIM régebbi verziójával rendelkezik, mert az Azure AD-szervezet kihasználja a Privileged Identity Management API-t. Lépjen kapcsolatba az aliassal a pim_preview@microsoft.com szervezet áthelyezéséhez és az API frissítéséhez. További információ: [Az Azure ad szerepkörei és funkciói a PIM-ben](../privileged-identity-management/azure-ad-roles-features.md).

## <a name="why-we-enforce-creation-of-a-special-group-for-assigning-it-to-a-role"></a>Miért kell kikényszeríteni egy speciális csoport létrehozását a szerepkörhöz való hozzárendelésre.

Ha egy csoport hozzá van rendelve egy szerepkörhöz, akkor a csoporttagság kezelésére jogosult rendszergazdák is közvetve kezelhetik a szerepkör tagságát. Tegyük fel például, hogy egy csoport Contoso_User_Administrators a felhasználói fiók rendszergazdai szerepköréhez van rendelve. A csoporttagság módosítására jogosult Exchange-rendszergazdák hozzáadhatják magukat a Contoso_User_Administrators csoporthoz, így a felhasználói fiók rendszergazdája is lehetnek. Amint láthatja, a rendszergazda a jogosultságot a nem kívánt módon emelheti ki.

Az Azure AD lehetővé teszi, hogy egy isAssignableToRole nevű új tulajdonság használatával megvédje a szerepkörökhöz rendelt csoportokat. Szerepkörhöz csak olyan felhőalapú csoportok rendelhetők hozzá, amelyek létrehozáskor a isAssignableToRole tulajdonság értéke "true" (igaz) értékre van állítva. Ez a tulajdonság nem változtatható; Ha egy csoport létrehozása ezzel a tulajdonsággal "true" értékre van állítva, akkor nem módosítható. Meglévő csoporton nem állítható be a tulajdonság.
Megalakítottuk, hogy a csoportok hogyan vannak hozzárendelve a szerepkörökhöz, hogy meggátolja a lehetséges szabálysértések előfordulását:

- Csak a globális rendszergazdák és a Kiemelt szerepkörű rendszergazdák hozhatnak létre szerepkörhöz hozzárendelhető csoportot (az "isAssignableToRole" tulajdonsággal engedélyezve).
- Nem lehet Azure AD dinamikus csoport; tehát a "hozzárendelt" tagsági típussal kell rendelkeznie. A dinamikus csoportok automatikus sokasága nem kívánt fiókot eredményezhet a csoporthoz, és így hozzá van rendelve a szerepkörhöz.
- Alapértelmezés szerint csak a globális rendszergazdák és a Kiemelt szerepkörű rendszergazdák kezelhetik a szerepkörhöz hozzárendelhető csoportok tagságát, de a szerepkörhöz hozzárendelhető csoportok felügyeletét a csoport tulajdonosainak hozzáadásával is delegálhatja.
- A jogosultság megemelésének megakadályozása érdekében a szerepkörhöz hozzárendelhető csoport tagjainak és tulajdonosainak a hitelesítő adatait csak egy kiemelt hitelesítő rendszergazda vagy egy globális rendszergazda módosíthatja.
- Nincs beágyazás. Egy csoport nem vehető fel szerepkörhöz rendelt csoport tagjaként.

## <a name="limitations"></a>Korlátozások

A következő forgatókönyvek jelenleg nem támogatottak:  

- Helyszíni csoportok társítása Azure AD-szerepkörökhöz (beépített vagy egyéni)

## <a name="known-issues"></a>Ismert problémák

- *Azure ad P2 licenccel rendelkező ügyfelek* esetében: ne rendeljen aktívként egy csoportot az Azure ad és a PRIVILEGED Identity Management (PIM) szolgáltatáson keresztüli szerepkörhöz. Konkrétan ne rendeljen hozzá szerepkört a szerepkörhöz hozzárendelhető csoporthoz, amikor a létrehozása folyamatban van, *és* a PIM használatával később hozzárendel egy szerepkört a csoporthoz. Ez olyan problémákhoz vezet, amelyekben a felhasználók nem látják az aktív szerepkör-hozzárendeléseiket a PIM-ben, és nem tudják eltávolítani a PIM-hozzárendelést. Ez a forgatókönyv nem érinti a jogosult hozzárendeléseket. Ha ezt a hozzárendelést próbálja végrehajtani, előfordulhat, hogy a rendszer váratlan viselkedést lát, például:
  - A szerepkör-hozzárendelés befejezési időpontja helytelenül jelenhet meg.
  - A PIM-portálon a **saját szerepkörök** csak egy szerepkör-hozzárendelést tudnak megjeleníteni, függetlenül attól, hogy a hozzárendelés hány módszerből áll (egy vagy több csoporton és közvetlenül).
- A **felügyelt felhasználó bejelentkezési funkciójának szakaszos** bevezetésének engedélyezése nem támogatja a hozzárendelést csoporton keresztül.
- *Azure ad P2 licenccel rendelkező ügyfeleinknek* Még a csoport törlése után is megjelenik a szerepkör jogosult tagja a PIM felhasználói felületén. Funkcionálisan nincs probléma; Ez csak egy gyorsítótár-probléma a Azure Portalban.  
- A szerepkör-hozzárendelésekhez használja az új [Exchange felügyeleti központot](https://admin.exchange.microsoft.com/) csoporttagság használatával. A régi Exchange felügyeleti központ még nem támogatja ezt a funkciót. Az Exchange PowerShell-parancsmagok a várt módon fognak működni.
- Azure Information Protection portálon (a klasszikus portálon) még nem ismeri fel a szerepkör-tagságot a csoporton keresztül. [Áttelepítheti az egységes érzékenységű címkézési platformra](/azure/information-protection/configure-policy-migrate-labels) , majd az Office 365 biztonsági & megfelelőségi központjának használatával csoportos hozzárendeléseket használhat a szerepkörök kezeléséhez.

Ezeket a problémákat kijavítja.

## <a name="required-license-plan"></a>Szükséges licencelési csomag

A szolgáltatás használatához elérhető prémium szintű Azure AD P1-licenccel kell rendelkeznie az Azure AD-szervezetben. Ahhoz, hogy az igény szerinti szerepkör-aktiváláshoz Privileged Identity Management is használhassa, elérhetőnek kell lennie prémium szintű Azure AD P2-licenccel. A követelmények megfelelő licencének megkereséséhez tekintse meg [az ingyenes és prémium csomagok általánosan elérhető funkcióinak összehasonlítását](../fundamentals/active-directory-whatis.md#what-are-the-azure-ad-licenses)ismertető témakört.

## <a name="next-steps"></a>Következő lépések

- [Szerepkörhöz hozzárendelhető csoport létrehozása](groups-create-eligible.md)
- [Szerepkör társítása szerepkörhöz hozzárendelhető csoporthoz](groups-assign-role.md)

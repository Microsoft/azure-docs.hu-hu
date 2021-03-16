---
title: A Azure Active Directory szerepköralapú hozzáférés-vezérlés (RBAC) áttekintése
description: Megtudhatja, hogyan értelmezheti a szerepkör-hozzárendelés és a korlátozott hatókör részét Azure Active Directoryban.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/20/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3fad2c683890776908afbfbf15ee91d46d564783
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/15/2021
ms.locfileid: "103466762"
---
# <a name="overview-of-role-based-access-control-in-azure-active-directory"></a>A Azure Active Directory szerepköralapú hozzáférés-vezérlésének áttekintése

Ez a cikk az Azure Active Directory (Azure AD) szerepköralapú hozzáférés-vezérlésének megismerését ismerteti. Az Azure AD-szerepkörök lehetővé teszik a bizalmas engedélyek megadását a rendszergazdák számára, a legalacsonyabb jogosultsági szint elve alapján. Az Azure AD beépített és egyéni szerepkörei olyan fogalmakon működnek, mint [Az Azure-erőforrások szerepköralapú hozzáférés-vezérlési rendszerében](../../role-based-access-control/overview.md) (Azure-szerepkörök). A [két szerepköralapú hozzáférés-vezérlési rendszer közötti különbség a következő](../../role-based-access-control/rbac-and-directory-admin-roles.md) :

- Az Azure AD-szerepkörök az Azure AD-erőforrásokhoz, például a felhasználókhoz, csoportokhoz és alkalmazásokhoz való hozzáférést szabályozzák Graph API
- Az Azure-szerepkörök az Azure Erőforrás-kezelés használatával vezérlik az Azure-erőforrásokhoz, például a virtuális gépekhez vagy a tárolóhoz

Mindkét rendszer hasonló módon használt szerepkör-definíciókat és szerepkör-hozzárendeléseket tartalmaz. Az Azure AD szerepkör engedélyei azonban nem használhatók az Azure egyéni szerepköreiben, és fordítva.

## <a name="understand-azure-ad-role-based-access-control"></a>Az Azure AD szerepköralapú hozzáférés-vezérlésének ismertetése
Az Azure AD két típusú szerepkör-definíciót támogat – 
* [Beépített szerepkörök](./permissions-reference.md)
* [Egyéni szerepkörök](./custom-create.md)

A beépített szerepkörök nem rendelkeznek rögzített engedélyekkel rendelkező szerepkörökkel. Ezek a szerepkör-definíciók nem módosíthatók. Az Azure AD számos [beépített szerepkört](./permissions-reference.md) támogat, és a lista növekszik. Az Azure AD az [Egyéni szerepköröket](./custom-create.md)is támogatja, hogy az éleket lekerekítse és megfeleljen a kifinomult követelményeknek. Az egyéni Azure AD-szerepkörök használatával történő engedélyek megadása kétlépéses folyamat, amely magában foglalja az egyéni szerepkör-definíciók létrehozását, majd a szerepkör-hozzárendelés használatával történő hozzárendelését. Az egyéni szerepkör-definíciók egy előre definiált listából felvett engedélyek gyűjteményei. Ezek az engedélyek ugyanazok, mint a beépített szerepkörökben.  

Miután létrehozta az egyéni szerepkör-definíciót (vagy egy beépített szerepkört használ), hozzárendelheti azt egy felhasználóhoz egy szerepkör-hozzárendelés létrehozásával. A szerepkör-hozzárendelések egy adott hatókörben lévő szerepkör-definícióban lévő engedélyeket biztosítanak a felhasználónak. Ez a kétlépéses folyamat lehetővé teszi, hogy egyetlen szerepkör-definíciót hozzon létre, és több alkalommal rendeljen hozzá különböző hatókörökben. A hatókör határozza meg azon Azure AD-erőforrások készletét, amelyekhez a szerepkör tagja hozzáfér. A leggyakoribb hatókör az egész szervezetre kiterjedő (szervezeti szintű) hatókör. Az egyéni szerepkör a szervezeti szintű hatókörhöz rendelhető, ami azt jelenti, hogy a szerepkör tagja rendelkezik a szervezet összes erőforrásához szükséges szerepkör-jogosultságokkal. Az objektumok hatóköréhez egyéni szerepkör is hozzárendelhető. Egy objektum hatóköre például egyetlen alkalmazás lehet. Ugyanazt a szerepkört hozzárendelheti egy felhasználóhoz a szervezeten belüli összes alkalmazáshoz, majd egy másik felhasználóhoz, amely csak a contoso költségelszámolás alkalmazás hatókörével rendelkezik.  

Az Azure AD beépített és egyéni szerepkörei az [Azure szerepköralapú hozzáférés-vezérléshez (Azure RBAC)](../develop/access-tokens.md#payload-claims)hasonló fogalmakon működnek. A [két szerepköralapú hozzáférés-vezérlési rendszer közötti különbség](../../role-based-access-control/rbac-and-directory-admin-roles.md) az, hogy az Azure RBAC az Azure-erőforrásokhoz, például a virtuális gépekhez vagy a tárolóhoz való hozzáférést az Azure Resource Management használatával szabályozza, az Azure ad egyéni szerepkörei pedig az Azure ad-erőforrásokhoz való hozzáférést Graph API használatával szabályozzák. Mindkét rendszer kihasználja a szerepkör-definíciók és a szerepkör-hozzárendelések fogalmát. Az Azure AD RBAC engedélyei nem vehetők fel az Azure-szerepkörökbe, és fordítva.

### <a name="how-azure-ad-determines-if-a-user-has-access-to-a-resource"></a>Hogyan határozza meg az Azure AD, hogy egy felhasználó hozzáfér-e egy erőforráshoz

Az Azure AD az alábbi magas szintű lépésekkel határozza meg, hogy van-e hozzáférése egy felügyeleti erőforráshoz. Ezeket az információkat a hozzáférési problémák elhárításához használhatja.

1. A felhasználó (vagy az egyszerű szolgáltatásnév) a Microsoft Graph vagy az Azure AD Graph-végponthoz szerzi be a jogkivonatot.
1. A felhasználó Azure Active Directory (Azure AD) API-hívást kezdeményez a kiállított jogkivonat használatával Microsoft Graph vagy Azure AD gráfon keresztül.
1. A körülményektől függően az Azure AD a következő műveletek egyikét veszi igénybe:
   - Kiértékeli a felhasználó szerepkör-tagságát a felhasználó hozzáférési jogkivonatában lévő [wids jogcím](../../active-directory-b2c/access-tokens.md) alapján.
   - Az összes olyan szerepkör-hozzárendelés beolvasása, amely közvetlenül vagy csoporttagság útján a felhasználóra vonatkozik, a művelet végrehajtásához szükséges erőforrásra.
1. Az Azure AD meghatározza, hogy az API-hívásban szereplő művelet tartalmazza-e a felhasználó által ehhez az erőforráshoz tartozó szerepköröket.
1. Ha a felhasználó nem rendelkezik szerepkörrel a kért hatókörben lévő művelettel, a hozzáférés nem adható meg. Egyéb esetben hozzáférést biztosít.

## <a name="role-assignment"></a>Szerepkör-hozzárendelés

A szerepkör-hozzárendelés egy olyan Azure AD-erőforrás, amely egy *szerepkör-definíciót* csatol egy *felhasználóhoz* egy adott *hatókörben* , és hozzáférést biztosít az Azure ad-erőforrásokhoz. A hozzáférés szerepkör-hozzárendelés létrehozásával biztosítható, és a szerepkör-hozzárendelés törlésével vonható vissza. Az alapszintű szerepkör-hozzárendelés három elemet tartalmaz:

- Azure AD-felhasználó
- Szerepkör-definíció
- Erőforrás hatóköre

Szerepkör- [hozzárendeléseket](custom-create.md) a Azure Portal, az Azure ad PowerShell vagy a Graph API használatával hozhat létre. [A szerepkör-hozzárendeléseket is listázhatja](view-assignments.md).

Az alábbi ábrán egy példa látható szerepkör-hozzárendelésre. Ebben a példában a Chris Green hozzá lett rendelve az alkalmazás-regisztrációs rendszergazda egyéni szerepkörhöz a contoso widget Builder alkalmazás-regisztráció hatókörében. A hozzárendelés csak az adott alkalmazás regisztrálásához engedélyezi az alkalmazás-regisztrációs rendszergazda szerepkör engedélyeit.

![A szerepkör-hozzárendelés az engedélyek kikényszerített és három részből áll](./media/custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Rendszerbiztonsági tag

A rendszerbiztonsági tag az Azure AD-erőforrásokhoz való hozzáférést biztosító felhasználót jelöli. A felhasználó olyan személy, aki Azure Active Directory felhasználói profillal rendelkezik.

### <a name="role"></a>Szerepkör

A szerepkör-definíció vagy szerepkör az engedélyek gyűjteménye. A szerepkör-definíció felsorolja az Azure AD-erőforrásokon végrehajtható műveleteket, például a létrehozás, olvasás, frissítés és törlés lehetőségeit. Az Azure AD két típusú szerepkörrel rendelkezik:

- A Microsoft által létrehozott beépített szerepkörök nem módosíthatók.
- A szervezet által létrehozott és kezelt egyéni szerepkörök.

### <a name="scope"></a>Hatókör

A hatókör egy adott Azure AD-erőforrásra vonatkozó engedélyezett műveletek korlátozása szerepkör-hozzárendelés részeként. Egy szerepkör hozzárendelésével megadhat egy hatókört, amely korlátozza a rendszergazda hozzáférését egy adott erőforráshoz. Ha például egyéni szerepkört szeretne kiadni egy fejlesztőnek, de csak egy adott alkalmazás regisztrálását szeretné kezelni, akkor a szerepkör-hozzárendelésben a megadott alkalmazás-regisztráció hatókörként is megadható.

## <a name="required-license-plan"></a>Szükséges licencelési csomag

Az Azure AD beépített szerepköreinek használata ingyenes, míg az egyéni szerepkörök prémium szintű Azure AD P1-licencet igényelnek. A követelményeinek leginkább megfelelő licenc kiválasztásáról lásd [az ingyenes, alapszintű és prémium kiadások általánosan elérhető szolgáltatásait összehasonlító cikket](https://azure.microsoft.com/pricing/details/active-directory).

## <a name="next-steps"></a>Következő lépések

- [Az Azure AD-szerepkörök ismertetése](concept-understand-roles.md)
- Egyéni szerepkör-hozzárendelések létrehozása [a Azure Portal, az Azure ad PowerShell és a Graph API](custom-create.md) használatával
- [Szerepkör-hozzárendelések felsorolása](view-assignments.md)
---
title: Azure-erőforrás szerepköreinek kiosztása a PIM-beli vendégek számára – Azure AD | Microsoft Docs
description: Ismerje meg, hogyan hívhat meg külső vendég felhasználókat, és hogyan rendelhet hozzá Azure-beli erőforrás-szerepköröket Azure AD Privileged Identity Management (PIM) szolgáltatásban.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4e2e18f3bb9d1c972d805a60493897d605921e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010639"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-privileged-identity-management"></a>Vendég felhasználók meghívása és Azure-beli erőforrás-szerepkörök kiosztása Privileged Identity Management

A Azure Active Directory (Azure AD) vendég felhasználói az Azure AD-n belül a vállalatközi (B2B) együttműködési képességek részét képezik, így a külső vendég felhasználóit és szállítóit az Azure AD-ben lévő vendégek is kezelhetik. Ha Azure AD Privileged Identity Management (PIM) használatával kombinálja a VÁLLALATKÖZI együttműködést, a megfelelőségi és irányítási követelmények kiterjeszthetők a vendégek rendelkezésére. Használhatja például az alábbi Privileged Identity Management funkciókat Azure Identity-feladatokhoz a vendégekkel:

- Hozzáférés kiosztása adott Azure-erőforrásokhoz
- Igény szerinti hozzáférés engedélyezése
- Hozzárendelés időtartamának és befejezési dátumának megadása
- Többtényezős hitelesítés megkövetelése aktív hozzárendelés vagy aktiválás esetén
- Hozzáférési felülvizsgálatok végrehajtása
- Riasztások és naplófájlok kihasználása

Ez a cikk bemutatja, hogyan hívhat meg egy vendéget a szervezet számára, és hogyan kezelheti az Azure-erőforrásokhoz való hozzáférését Privileged Identity Management használatával.

## <a name="when-would-you-invite-guests"></a>Mikor hívja meg a vendégeket?

Íme néhány példa arra, hogy mikor hívhatja meg a szervezet vendégeit:

- A projekthez tartozó Azure-erőforrásokhoz való hozzáféréshez csak egy e-mail-fiókkal rendelkező külső önálló szolgáltató engedélyezése.
- Külső partner engedélyezése egy olyan nagyvállalatnál, amely helyszíni Active Directory összevonási szolgáltatások (AD FS) használatával fér hozzá a költségelszámolás alkalmazásához.
- Lehetővé teszi, hogy a szervezeten kívüli támogatási mérnökök (például a Microsoft támogatási szolgálata) átmenetileg hozzáférjenek az Azure-erőforráshoz a hibák elhárítása érdekében.

## <a name="how-does-collaboration-using-b2b-guests-work"></a>Hogyan működik a B2B-vendégeket használó együttműködés?

Ha B2B-együttműködést használ, egy külső felhasználót is meghívhat vendégként a szervezet számára. A vendég felügyelhető felhasználóként a szervezetében, de a vendéget hitelesíteni kell a saját szervezetében, és nem az Azure AD-szervezetben. Ez azt jelenti, hogy ha a vendég már nem fér hozzá a saját szervezetéhez, akkor is elveszti a hozzáférést a szervezethez. Ha például a vendég elhagyja a szervezetét, automatikusan elveszíti az Azure AD-vel megosztott összes erőforráshoz való hozzáférést anélkül, hogy bármit el kellene végeznie. További információ a B2B együttműködésről: [Mi a vendég felhasználói hozzáférés Azure Active Directory B2B-ben?](../external-identities/what-is-b2b.md).

![Azt bemutató ábra, hogy a vendég felhasználó hogyan legyen hitelesítve a saját könyvtárában](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>Vendég együttműködési beállítások keresése

Győződjön meg arról, hogy meg tudja hívni a vendégeket a szervezetbe, ellenőrizze a vendég együttműködési beállításait.

1. Jelentkezzen be [Azure Portalba](https://portal.azure.com/).

1. Válassza a **Azure Active Directory**  >  **felhasználói beállítások** lehetőséget.

1. Válassza a **külső együttműködési beállítások kezelése** lehetőséget.

    ![Külső csoportmunka-beállítások lap, amely az engedélyek, a meghívás és az együttműködés korlátozására vonatkozó beállításokat jeleníti meg](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Győződjön meg arról, hogy a **vendég meghívó szerepkörben található rendszergazdák és felhasználók meghívhatják** a kapcsolót **Igen** értékre.

## <a name="invite-a-guest-and-assign-a-role"></a>Vendég meghívása és szerepkör kiosztása

A Privileged Identity Management használatával meghívhat egy vendéget, és jogosult lehet egy Azure-erőforrás szerepkörre.

1. Jelentkezzen be [Azure Portal](https://portal.azure.com/) egy olyan felhasználóval, aki tagja a [Kiemelt szerepkörű rendszergazda](../roles/permissions-reference.md#privileged-role-administrator) vagy a [felhasználói rendszergazda](../roles/permissions-reference.md#user-administrator) szerepkörnek.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza ki az **Azure-erőforrásokat**.

1. Az **Erőforrás-szűrő** használatával szűrheti a felügyelt erőforrások listáját.

1. Válassza ki a kezelni kívánt erőforrást, például egy erőforrást, egy erőforráscsoportot, egy előfizetést vagy egy felügyeleti csoportot.

    A hatókört csak a vendég igényeinek megfelelően állítsa be.

1. A kezelés területen válassza ki a **szerepkörök** elemet az Azure-erőforrások szerepköreinek megtekintéséhez.

    ![Az aktív és jogosult felhasználók számát mutató Azure-erőforrások szerepköreinek listája](./media/pim-resource-roles-external-users/resources-roles.png)

1. Válassza ki a minimális szerepkört, amelyhez a felhasználónak szüksége lesz.

    ![A szerepkör aktuális tagjait felsoroló kiválasztott szerepkör lap](./media/pim-resource-roles-external-users/selected-role.png)

1. A szerepkör lapon válassza a **tag hozzáadása** elemet az új hozzárendelés ablaktábla megnyitásához.

1. Kattintson **a tag vagy csoport kiválasztása** elemre.

    ![Új hozzárendelés – válasszon ki egy tag vagy csoport ablaktáblát, amely felsorolja a felhasználókat és a csoportokat, valamint egy Meghívási lehetőséget.](./media/pim-resource-roles-external-users/select-member-group.png)

1. Vendég meghívásához kattintson a **meghívás** elemre.

    ![Vendég oldal meghívása e-mail-cím megadásához és személyes üzenet megadásához](./media/pim-resource-roles-external-users/invite-guest.png)

1. A vendég kiválasztása után kattintson a **meghívás** elemre.

    A vendéget kijelölt tagként kell hozzáadni.

1. A **tag vagy csoport kiválasztása** ablaktáblán kattintson a **kiválasztás** elemre.

1. A **tagsági beállítások** ablaktáblán válassza ki a hozzárendelés típusát és időtartamát.

    ![Új hozzárendelés – tagsági beállítások lap a hozzárendelési típus, a kezdő dátum és a befejezési dátum megadására szolgáló beállításokkal](./media/pim-resource-roles-external-users/membership-settings.png)

1. A hozzárendelés befejezéséhez válassza a **kész** , majd a **Hozzáadás** lehetőséget.

    A vendég szerepkör-hozzárendelés megjelenik a szerepkör listájában.

    ![A szerepkör lap jogosultként tartalmazza a vendéget](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>Szerepkör aktiválása vendégként

Külső felhasználó esetén el kell fogadnia a meghívást, hogy vendég legyen az Azure AD-szervezetben, és esetleg aktiválja a szerepkör-hozzárendelést.

1. Nyissa meg az e-mailt a meghívóval. Az e-mail a következőhöz hasonlóan fog kinézni.

    ![E-mail meghívása a címtár nevével, személyes üzenettel és az első lépések hivatkozással](./media/pim-resource-roles-external-users/email-invite.png)

1. Válassza az **első lépések** hivatkozást az e-mailben.

1. Az engedélyek áttekintése után kattintson az **elfogadás** gombra.

    ![Tekintse át az engedélyek lapot egy böngészőben azon engedélyek listájával, amelyeket a szervezet szeretne áttekinteni](./media/pim-resource-roles-external-users/invite-accept.png)

1. Előfordulhat, hogy a rendszer felszólítja a használati feltételek elfogadására, és megadja, hogy szeretne-e bejelentkezni. Ha a Azure Portal *jogosult* szerepkörre, akkor még nem férhet hozzá az erőforrásokhoz.

1. A szerepkör-hozzárendelés aktiválásához nyissa meg az e-mailt az aktiválási szerepkör hivatkozásával. Az e-mail a következőhöz hasonlóan fog kinézni.

    ![E-mail-cím, amely azt jelzi, hogy jogosult az aktiválási szerepkörre mutató hivatkozással rendelkező szerepkörre](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Válassza a **szerepkör aktiválása** lehetőséget a jogosult szerepkörök megnyitásához Privileged Identity Managementban.

    ![A saját szerepkörök lap Privileged Identity Management a jogosult szerepkörök listázása](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. A művelet területen válassza az **aktiválás** hivatkozást.

    A szerepkör beállításaitól függően meg kell adnia néhány információt a szerepkör aktiválásához.

1. Miután megadta a szerepkör beállításait, kattintson az **aktiválás** gombra a szerepkör aktiválásához.

    ![Az oldal-lista hatókörének és beállításainak aktiválása a kezdési idő, az időtartam és az OK megadásához](./media/pim-resource-roles-external-users/activate-role.png)

    Ha a rendszergazda nem szükséges a kérelem jóváhagyásához, hozzáféréssel kell rendelkeznie a megadott erőforrásokhoz.

## <a name="view-activity-for-a-guest"></a>Vendég tevékenységének megtekintése

A naplókat megtekintve nyomon követheti, hogy a vendégek mit csinálnak.

1. Rendszergazdaként nyissa meg Privileged Identity Management, és válassza ki a megosztott erőforrást.

1. Válassza az **erőforrás-naplózás** lehetőséget az erőforrás tevékenységének megtekintéséhez. Az alábbi példa egy erőforráscsoport tevékenységére mutat példát.

    ![Azure-erőforrások – erőforrás-naplózási oldal, amely az idő, a kérelmező és a művelet listáját tartalmazza](./media/pim-resource-roles-external-users/audit-resource.png)

1. A vendég tevékenységének megtekintéséhez válassza **Azure Active Directory**  >  **felhasználó**  >  *vendégének nevét*.

1. Válassza a **naplók** lehetőséget a szervezet naplófájljainak megtekintéséhez. Szükség esetén szűrők megadására is lehetőség van.

    ![Címtár-naplózási naplók listázási dátuma, cél, kezdeményező és tevékenység](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Következő lépések

- [Azure AD-beli rendszergazdai szerepkörök kiosztása Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Mi a vendég felhasználói hozzáférés az Azure AD B2B együttműködésben?](../external-identities/what-is-b2b.md)
---
title: Azure-szerepkörök kiosztása külső vendég felhasználók számára a Azure Portal-Azure RBAC használatával
description: Megtudhatja, hogyan biztosíthat hozzáférést az Azure-erőforrásokhoz a szervezeten kívüli felhasználók számára a Azure Portal és az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.custom: it-pro
ms.openlocfilehash: d834f4ccd8dba26c895e0578f161813fc49332ea
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100556293"
---
# <a name="assign-azure-roles-to-external-guest-users-using-the-azure-portal"></a>Azure-szerepkörök kiosztása külső vendég felhasználók számára a Azure Portal használatával

Az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](overview.md) nagyobb biztonsági felügyeletet tesz lehetővé a nagyméretű szervezetek számára, valamint a kis-és közepes méretű vállalkozások számára, akik olyan külső közreműködők, szállítók vagy szabadúszók számára biztosítanak hozzáférést, amelyeknek hozzá kell férniük a környezet adott erőforrásaihoz, de nem feltétlenül a teljes infrastruktúrához vagy a számlázással kapcsolatos hatókörökhöz. A [Azure Active Directory B2B](../active-directory/external-identities/what-is-b2b.md) funkciói a külső vendég felhasználóival való együttműködéshez használhatók, és az Azure RBAC használatával csak azokat az engedélyeket adhatja meg, amelyekre a vendég felhasználóknak szüksége van a környezetében.

## <a name="prerequisites"></a>Előfeltételek

Az Azure-szerepkörök hozzárendeléséhez vagy a szerepkör-hozzárendelések eltávolításához a következőket kell tennie:

- `Microsoft.Authorization/roleAssignments/write` és `Microsoft.Authorization/roleAssignments/delete` engedélyek, például a [felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) vagy a [tulajdonos](built-in-roles.md#owner)


## <a name="when-would-you-invite-guest-users"></a>Mikor hívja meg a vendég felhasználókat?

Íme néhány példa arra, hogy a vendég felhasználóinak meghívása a szervezet számára, és engedélyek megadása:

- A projekthez tartozó Azure-erőforrásokhoz való hozzáféréshez csak egy e-mail-fiókkal rendelkező külső önálló szolgáltató engedélyezése.
- Lehetővé teszi, hogy egy külső partner bizonyos erőforrásokat vagy teljes előfizetést kezeljen.
- Lehetővé teszi, hogy a szervezeten kívüli támogatási mérnökök (például a Microsoft támogatási szolgálata) átmenetileg hozzáférjenek az Azure-erőforráshoz a hibák elhárítása érdekében.

## <a name="permission-differences-between-member-users-and-guest-users"></a>Jogosultsági különbségek a tagok és a vendég felhasználók között

A címtár (member Users) natív tagjai eltérő engedélyekkel rendelkeznek, mint a többi címtárból VÁLLALATKÖZI együttműködési vendégként (vendég felhasználók) meghívott felhasználók. A tagok felhasználója például szinte minden címtári információt beolvashat, amíg a vendég felhasználók korlátozott címtárbeli engedélyekkel rendelkeznek. További információ a tagok és a vendég felhasználókról: [Mik az alapértelmezett felhasználói engedélyek a Azure Active Directory-ben?](../active-directory/fundamentals/users-default-permissions.md).

## <a name="add-a-guest-user-to-your-directory"></a>Vendégfelhasználó hozzáadása a címtárhoz

A következő lépésekkel adhat hozzá egy vendég felhasználót a címtárhoz a Azure Active Directory oldalon.

1. Győződjön meg arról, hogy a szervezet külső együttműködési beállításai úgy vannak konfigurálva, hogy Ön is meghívja vendégeit. További információkért tekintse meg a [külső B2B-együttműködés engedélyezése és a vendégek meghívására alkalmas személyek kezelése](../active-directory/external-identities/delegate-invitations.md)című témakört.

1. A Azure Portal kattintson **Azure Active Directory**  >  **felhasználók**  >  **új vendég felhasználó** elemre.

    ![Új vendég felhasználói szolgáltatás a Azure Portal](./media/role-assignments-external-users/invite-guest-user.png)

1. Kövesse a lépéseket egy új vendég felhasználó hozzáadásához. További információ: [Azure Active Directory B2B együttműködéssel rendelkező felhasználók hozzáadása a Azure Portal](../active-directory/external-identities/add-users-administrator.md#add-guest-users-to-the-directory).

Miután hozzáadta a vendég felhasználót a címtárhoz, elküldheti a vendég felhasználó közvetlen hivatkozását egy megosztott alkalmazásra, vagy a vendég felhasználó a meghívót tartalmazó e-mailben a beváltási URL-címre is kattinthat.

![Vendég felhasználó meghívó e-mail-címe](./media/role-assignments-external-users/invite-email.png)

Ahhoz, hogy a vendég felhasználó hozzáférhessen a címtárhoz, el kell végeznie a meghívás folyamatát.

![Vendég felhasználói meghívás-felülvizsgálati engedélyek](./media/role-assignments-external-users/invite-review-permissions.png)

További információ a Meghívási folyamatról: [Azure Active Directory B2B együttműködés meghívásának beváltása](../active-directory/external-identities/redemption-experience.md).

## <a name="assign-a-role-to-a-guest-user"></a>Szerepkör társítása vendég felhasználóhoz

Az Azure RBAC a hozzáférés biztosításához rendeljen hozzá egy szerepkört. Ha szerepkört szeretne hozzárendelni egy vendég felhasználóhoz, akkor [ugyanazokat a lépéseket](role-assignments-portal.md) kell követnie, mint a felhasználó, csoport, szolgáltatásnév vagy felügyelt identitás esetében. Az alábbi lépéseket követve rendeljen hozzá egy szerepkört egy vendég felhasználóhoz különböző hatókörökben.

1. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre.

1.  Válassza ki azon erőforrások készletét, amelyekre a hozzáférés vonatkozik, más néven hatókör. Kiválaszthatja például a **felügyeleti csoportokat**, **előfizetéseket**, **erőforráscsoportokat** vagy egy erőforrást.

1. Kattintson az adott erőforrásra.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

    Az alábbi képernyőképen egy példát láthat egy erőforráscsoport Hozzáférés-vezérlés (IAM) paneljére. Ha itt bármilyen hozzáférés-vezérlési változást hajt végre, az csak az erőforráscsoporthoz fog vonatkozni.

    ![Egy erőforráscsoport hozzáférés-vezérlés (IAM) panelje](./media/role-assignments-external-users/access-control-resource-group.png)

1. A **szerepkör-hozzárendelések** lapra kattintva megtekintheti az összes szerepkör-hozzárendelést ezen a hatókörön.

1. Kattintson a **Hozzáadás** > **Szerepkör-hozzárendelés hozzáadása** lehetőségre a Szerepkör-hozzárendelés hozzáadása szakasz megnyitásához.

    Ha nem rendelkezik jogosultsággal a szerepkörök hozzárendeléséhez, a szerepkör-hozzárendelés hozzáadása lehetőség le lesz tiltva.

    ![Szerepkör-hozzárendelési menü hozzáadása](./media/shared/add-role-assignment-menu.png)

    Megnyílik a Szerepkör-hozzárendelés hozzáadása panel.

1. A **Szerepkör** legördülő listájában válasszon ki egy szerepkört, például a **Virtuális gépek közreműködője** szerepkört.

1. A **Select (kiválasztás** ) listából válassza ki a vendég felhasználót. Ha nem látja a felhasználót a listában, beírhatja a **kijelölés** szövegmezőbe a megjelenítendő nevek, e-mail-címek és objektumazonosítók kereséséhez.

   ![Szerepkör-hozzárendelési ablaktábla hozzáadása](./media/role-assignments-external-users/add-role-assignment.png)

1. Kattintson a **Mentés** gombra a szerepkör a kiválasztott hatókörhöz való hozzárendeléséhez.

    ![Szerepkör-hozzárendelés a virtuális gép közreműködői számára](./media/role-assignments-external-users/access-control-role-assignments.png)

## <a name="assign-a-role-to-a-guest-user-not-yet-in-your-directory"></a>Szerepkör kiosztása egy vendég felhasználó számára még nem a címtárban

Ha szerepkört szeretne hozzárendelni egy vendég felhasználóhoz, akkor [ugyanazokat a lépéseket](role-assignments-portal.md) kell követnie, mint a felhasználó, csoport, szolgáltatásnév vagy felügyelt identitás esetében.

Ha a vendég felhasználó még nem szerepel a címtárban, a felhasználót közvetlenül a szerepkör-hozzárendelés hozzáadása panelen hívhatja meg.

1. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre.

1.  Válassza ki azon erőforrások készletét, amelyekre a hozzáférés vonatkozik, más néven hatókör. Kiválaszthatja például a **felügyeleti csoportokat**, **előfizetéseket**, **erőforráscsoportokat** vagy egy erőforrást.

1. Kattintson az adott erőforrásra.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. A **szerepkör-hozzárendelések** lapra kattintva megtekintheti az összes szerepkör-hozzárendelést ezen a hatókörön.

1. Kattintson a **Hozzáadás** > **Szerepkör-hozzárendelés hozzáadása** lehetőségre a Szerepkör-hozzárendelés hozzáadása szakasz megnyitásához.

    ![Szerepkör-hozzárendelési menü hozzáadása](./media/shared/add-role-assignment-menu.png)

    Megnyílik a Szerepkör-hozzárendelés hozzáadása panel.

1. A **Szerepkör** legördülő listájában válasszon ki egy szerepkört, például a **Virtuális gépek közreműködője** szerepkört.

1. A **Select (kiválasztás** ) listán írja be annak a személynek az e-mail-címét, akit meg szeretne hívni, és válassza ki az adott személyt.

   ![Vendég felhasználó meghívása a szerepkör-hozzárendelés hozzáadása panelen](./media/role-assignments-external-users/add-role-assignment-new-guest.png)

1. A **Mentés** gombra kattintva adja hozzá a vendég felhasználót a címtárhoz, rendeljen hozzá egy szerepkört, és küldjön egy meghívást.

    Néhány pillanat elteltével megjelenik a szerepkör-hozzárendelésről és a meghívásról szóló információ.

    ![Szerepkör-hozzárendelés és meghívott felhasználói értesítés](./media/role-assignments-external-users/invited-user-notification.png)

1. A vendég felhasználó manuális meghívásához kattintson a jobb gombbal, és másolja a Meghívási hivatkozást az értesítésbe. Ne kattintson a meghívás hivatkozásra, mert elindítja a Meghívási folyamatot.

    A meghívó hivatkozás formátuma a következő lesz:

    `https://invitations.microsoft.com/redeem/...`

1. Küldje el a Meghívási hivatkozást a vendég felhasználónak a meghívás folyamatának befejezéséhez.

    További információ a Meghívási folyamatról: [Azure Active Directory B2B együttműködés meghívásának beváltása](../active-directory/external-identities/redemption-experience.md).

## <a name="remove-a-guest-user-from-your-directory"></a>Vendég felhasználó eltávolítása a címtárból

Mielőtt eltávolít egy vendég felhasználót egy címtárból, először el kell távolítania a szerepkör-hozzárendeléseket a vendég felhasználó számára. Az alábbi lépéseket követve távolíthatja el a vendég felhasználót egy címtárból.

1. Nyissa meg a **hozzáférés-vezérlést (iam)** egy hatókörön, például a felügyeleti csoport, az előfizetés, az erőforráscsoport vagy az erőforrás területen, ahol a vendég felhasználó szerepkör-hozzárendeléssel rendelkezik.

1. Kattintson a **szerepkör-hozzárendelések** lapra az összes szerepkör-hozzárendelés megtekintéséhez.

1. A szerepkör-hozzárendelések listájában vegyen fel egy pipát a vendég felhasználó mellett az eltávolítani kívánt szerepkör-hozzárendeléssel.

   ![Szerepkör-hozzárendelés eltávolítása](./media/role-assignments-external-users/remove-role-assignment-select.png)

1. Kattintson az **Eltávolítás** elemre.

   ![Szerepkör-hozzárendelés eltávolítási üzenete](./media/role-assignments-external-users/remove-role-assignment.png)

1. A megjelenő szerepkör-hozzárendelés eltávolítása üzenetben kattintson az **Igen** gombra.

1. A bal oldali navigációs sávon kattintson **Azure Active Directory**  >  **felhasználók** elemre.

1. Kattintson az eltávolítani kívánt vendég felhasználóra.

1. Kattintson a **Törlés** gombra.

   ![Vendég felhasználó törlése](./media/role-assignments-external-users/delete-guest-user.png)

1. A megjelenő törlési üzenetben kattintson az **Igen** gombra.

## <a name="troubleshoot"></a>Hibaelhárítás

### <a name="guest-user-cannot-browse-the-directory"></a>A vendég felhasználó nem tallózhat a címtárban

A vendég felhasználók korlátozott címtárbeli engedélyekkel rendelkeznek. A vendég felhasználók például nem tallózhatnak a címtárban, és nem kereshetnek csoportokat vagy alkalmazásokat. További információ: [Mik az alapértelmezett felhasználói engedélyek a Azure Active Directory-ben?](../active-directory/fundamentals/users-default-permissions.md).

![A vendég felhasználó nem tallózhat a címtárban lévő felhasználók között](./media/role-assignments-external-users/directory-no-users.png)

Ha egy vendég felhasználónak további jogosultságokra van szüksége a címtárban, hozzárendelhet egy címtárbeli szerepkört a vendég felhasználóhoz. Ha szeretné, hogy a vendég felhasználó teljes olvasási hozzáféréssel rendelkezzen a címtárhoz, hozzáadhatja a vendég felhasználót az Azure AD [címtár-olvasói](../active-directory/roles/permissions-reference.md) szerepköréhez. További információkért lásd: [engedélyek megadása a Azure Active Directory bérlőben található partnerszervezetek felhasználói számára](../active-directory/external-identities/add-guest-to-role.md).

![Directory-olvasók szerepkörének kiosztása](./media/role-assignments-external-users/directory-roles.png)

### <a name="guest-user-cannot-browse-users-groups-or-service-principals-to-assign-roles"></a>A vendég felhasználó nem tallózhat a felhasználók, csoportok vagy egyszerű szolgáltatások között a szerepkörök hozzárendeléséhez

A vendég felhasználók korlátozott címtárbeli engedélyekkel rendelkeznek. Akkor is, ha a vendég felhasználó egy hatókör [tulajdonosa](built-in-roles.md#owner) , ha olyan szerepkört próbálnak hozzárendelni, amely hozzáférést biztosít valaki másnak, nem tallózhatják a felhasználók, csoportok vagy egyszerű szolgáltatások listáját.

![A vendég felhasználó nem tallózhat a rendszerbiztonsági tag számára szerepkörök hozzárendeléséhez](./media/role-assignments-external-users/directory-no-browse.png)

Ha a vendég felhasználó tudja, hogy valaki pontosan bejelentkezik a címtárba, hozzáférést biztosíthat. Ha szeretné, hogy a vendég felhasználó teljes olvasási hozzáféréssel rendelkezzen a címtárhoz, hozzáadhatja a vendég felhasználót az Azure AD [címtár-olvasói](../active-directory/roles/permissions-reference.md) szerepköréhez. További információkért lásd: [engedélyek megadása a Azure Active Directory bérlőben található partnerszervezetek felhasználói számára](../active-directory/external-identities/add-guest-to-role.md).

### <a name="guest-user-cannot-register-applications-or-create-service-principals"></a>A vendég felhasználó nem regisztrálhat alkalmazásokat, és nem hozhat létre egyszerű szolgáltatásokat

A vendég felhasználók korlátozott címtárbeli engedélyekkel rendelkeznek. Ha egy vendég felhasználónak képesnek kell lennie az alkalmazások regisztrálására vagy egyszerű szolgáltatásnév létrehozására, a vendég felhasználót hozzáadhatja az [alkalmazás fejlesztői](../active-directory/roles/permissions-reference.md) szerepköréhez az Azure ad-ben. További információkért lásd: [engedélyek megadása a Azure Active Directory bérlőben található partnerszervezetek felhasználói számára](../active-directory/external-identities/add-guest-to-role.md).

![A vendég felhasználó nem tud regisztrálni alkalmazásokat](./media/role-assignments-external-users/directory-access-denied.png)

### <a name="guest-user-does-not-see-the-new-directory"></a>A vendég felhasználó nem látja az új könyvtárat

Ha a vendég felhasználó hozzáférést kapott egy címtárhoz, de nem látja a Azure Portalban felsorolt új könyvtárat, amikor megpróbálnak váltani a **címtár + előfizetés** ablaktáblán, győződjön meg arról, hogy a vendég felhasználó befejezte a Meghívási folyamatot. További információ a Meghívási folyamatról: [Azure Active Directory B2B együttműködés meghívásának beváltása](../active-directory/external-identities/redemption-experience.md).

### <a name="guest-user-does-not-see-resources"></a>A vendég felhasználó nem látja az erőforrásokat

Ha a vendég felhasználó hozzáférést kapott egy címtárhoz, de nem látja azokat az erőforrásokat, amelyekhez hozzáférést kapott a Azure Portal, győződjön meg arról, hogy a vendég felhasználó a megfelelő könyvtárat választotta. Előfordulhat, hogy A vendég felhasználó több könyvtárat is elérhet. A címtárak váltásához kattintson a bal felső sarokban található **könyvtár + előfizetés** elemre, majd kattintson a megfelelő könyvtárra.

![Könyvtárak és előfizetések panel a Azure Portal](./media/role-assignments-external-users/directory-subscription.png)

## <a name="next-steps"></a>Következő lépések

- [Azure Active Directory B2B Collaboration-felhasználók hozzáadása a Azure Portal](../active-directory/external-identities/add-users-administrator.md)
- [Egy Azure Active Directory B2B csoportmunka-felhasználó tulajdonságai](../active-directory/external-identities/user-properties.md)
- [A B2B együttműködés meghívójának e-mail-Azure Active Directory elemei](../active-directory/external-identities/invitation-email-elements.md)
- [Vendég felhasználó hozzáadása társ-rendszergazdaként](classic-administrators.md#add-a-guest-user-as-a-co-administrator)
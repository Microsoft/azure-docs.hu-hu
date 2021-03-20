---
title: Felhasználók kezelése az Azure Blockchain Workbenchben
description: Felhasználók kezelése az Azure Blockchain Workbenchben.
ms.date: 07/15/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: be078e7149ba008ab8b7ad44a016a0d1cf82df0e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91263011"
---
# <a name="manage-users-in-azure-blockchain-workbench"></a>Felhasználók kezelése az Azure Blockchain Workbenchben

Az Azure Blockchain Workbench felhasználói felügyeletet tartalmaz a konzorcium részét képező személyeknek és szervezeteknek.

## <a name="prerequisites"></a>Előfeltételek

Szükség van egy Blockchain Workbench-telepítésre. Az üzembe helyezéssel kapcsolatos részletekért tekintse meg az [Azure Blockchain Workbench üzembe helyezését](deploy.md) .

## <a name="add-azure-ad-users"></a>Azure AD-felhasználók hozzáadása

Az Azure Blockchain Workbench Azure Active Directory (Azure AD) protokollt használ a hitelesítéshez, a hozzáférés-vezérléshez és a szerepkörökhöz. A Blockchain Workbench Azure AD-bérlőben található felhasználók hitelesíthetők és használhatják a Blockchain Workbench-t. Adja hozzá a felhasználókat a rendszergazda alkalmazás szerepkörhöz a műveletek interakcióhoz és végrehajtásához.

Az Blockchain Workbench felhasználóinak az Azure AD-bérlőben kell léteznie ahhoz, hogy hozzá lehessen rendelni azokat az alkalmazásokhoz és szerepkörökhöz. Ha felhasználókat szeretne hozzáadni az Azure AD-hez, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a fiókját a jobb felső sarokban, és váltson a Blockchain Workbenchhez társított Azure AD-bérlőre.
1. Válassza **Azure Active Directory > felhasználók** lehetőséget. Megjelenik a címtárban található felhasználók listája.
1. Ha felhasználókat szeretne hozzáadni a címtárhoz, válassza az **új felhasználó** lehetőséget. Külső felhasználók esetében válassza az **új vendég felhasználó** lehetőséget.
1. Töltse ki az új felhasználóhoz szükséges mezőket. Válassza a **Létrehozás** lehetőséget.

Az Azure ad-ben található felhasználók kezelésével kapcsolatos további információkért látogasson el az [Azure ad](../../active-directory/fundamentals/add-users-azure-active-directory.md) dokumentációjában.

## <a name="manage-blockchain-workbench-administrators"></a>Blockchain Workbench-rendszergazdák kezelése

Miután hozzáadta a felhasználókat a címtárhoz, a következő lépés az, hogy kiválassza, mely felhasználók Blockchain Workbench-rendszergazdák. A **rendszergazda** csoport felhasználói a Blockchain Workbench **rendszergazdai alkalmazási szerepköréhez** vannak társítva. A rendszergazdák hozzáadhatnak vagy eltávolíthatnak felhasználókat, rendelhetnek hozzá felhasználókat adott forgatókönyvekhez, és új alkalmazásokat hozhatnak létre.

Felhasználók hozzáadása a **rendszergazda** csoporthoz az Azure ad-címtárban:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Ellenőrizze, hogy a Blockchain Workbenchhez társított Azure AD-bérlőben van-e, és válassza ki a fiókját a jobb felső sarokban.
1. Válassza **Azure Active Directory > vállalati alkalmazások** lehetőséget.
1. Módosítsa az **alkalmazás típusa** legördülő szűrőt az **összes alkalmazásra** , majd válassza az **alkalmaz** lehetőséget.
1. Azure AD-ügyfélalkalmazás kiválasztása az Azure Blockchain Workbenchhez

    ![Minden vállalati alkalmazás regisztrációja](./media/manage-users/select-blockchain-client-app.png)

1. Válassza **a felhasználók és csoportok lehetőséget > felhasználó hozzáadása** elemet.
1. A **Hozzárendelés felvétele** résznél válassza a **Felhasználók** elemet. Válassza ki vagy keresse meg azt a felhasználót, akit rendszergazdaként szeretne felvenni. Kattintson **a kiválasztás** elemre a befejezés után.

    ![Hozzárendelés hozzáadása](./media/manage-users/add-user-assignment.png)

1. A **szerepkör** ellenőrzése **rendszergazdaként**
1. Válassza a **Hozzárendelés** elemet. A hozzáadott felhasználók a listában a hozzárendelt rendszergazdai szerepkörrel jelennek meg.

    ![Blockchain ügyfélalkalmazások felhasználói](./media/manage-users/blockchain-admin-list.png)

## <a name="managing-blockchain-workbench-members"></a>Blockchain Workbench-tagok kezelése

A Blockchain Workbench alkalmazással kezelheti a konzorcium részét képező felhasználókat és szervezeteket. Hozzáadhat vagy eltávolíthat felhasználókat az alkalmazásokhoz és szerepkörökhöz.

1. [Nyissa meg a Blockchain Workbench](deploy.md#blockchain-workbench-web-url) alkalmazást a böngészőben, és jelentkezzen be rendszergazdaként.

    ![Blockchain Workbench](./media/manage-users/blockchain-workbench-applications.png)

    A tagok minden alkalmazáshoz hozzáadódnak. A tagok egy vagy több alkalmazás-szerepkörrel is rendelkezhetnek szerződések kezdeményezéséhez vagy műveletek elvégzéséhez.

1. Egy alkalmazás tagjainak kezeléséhez válasszon ki egy alkalmazást az **alkalmazások** ablaktáblán.

    A kijelölt alkalmazáshoz társított tagok száma megjelenik a tagok csempén.

    ![Alkalmazás kiválasztása](./media/manage-users/blockchain-workbench-select-application.png)


#### <a name="add-member-to-application"></a>Tag hozzáadása az alkalmazáshoz

1. Válassza ki a tag csempét az aktuális tagok listájának megjelenítéséhez.
1. Válassza a **Tagok hozzáadása** elemet.

    ![Képernyőfelvétel: az alkalmazás tagsági ablaka, a tag hozzáadása gomb kiemelve.](./media/manage-users/application-add-members.png)

1. Keresse meg a felhasználó nevét.  Csak az Blockchain Workbench-bérlőben található Azure AD-felhasználók jelennek meg. Ha a felhasználó nem található, [fel kell vennie az Azure ad-felhasználókat](#add-azure-ad-users).

    ![Tagok hozzáadása](./media/manage-users/find-user.png)

1. Válasszon ki egy **szerepkört** a legördülő menüből.

    ![Szerepkör tagjainak kiválasztása](./media/manage-users/application-select-role.png)

1. A **Hozzáadás** gombra kattintva adja hozzá a tagot a társított szerepkörrel az alkalmazáshoz.

#### <a name="remove-member-from-application"></a>Tag eltávolítása az alkalmazásból

1. Válassza ki a tag csempét az aktuális tagok listájának megjelenítéséhez.
1. Az eltávolítani kívánt felhasználóhoz válassza az **Eltávolítás** lehetőséget a szerepkör legördülő menüből.

    ![Tag eltávolítása](./media/manage-users/application-remove-member.png)

#### <a name="change-or-add-role"></a>Szerepkör módosítása vagy hozzáadása

1. Válassza ki a tag csempét az aktuális tagok listájának megjelenítéséhez.
1. A módosítani kívánt felhasználóhoz kattintson a legördülő listára, és válassza ki az új szerepkört.

    ![Szerepkör módosítása](./media/manage-users/application-change-role.png)

## <a name="next-steps"></a>Következő lépések

Ebben a útmutatóban megtanulta, hogyan kezelheti az Azure Blockchain Workbench felhasználóit. Ha szeretné megtudni, hogyan hozhat létre blockchain-alkalmazásokat, folytassa a következő útmutatók beírásával.

> [!div class="nextstepaction"]
> [Blockchain-alkalmazás létrehozása az Azure Blockchain Workbenchben](create-app.md)

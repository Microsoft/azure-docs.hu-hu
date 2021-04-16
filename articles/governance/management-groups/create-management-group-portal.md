---
title: 'Rövid útmutató: Felügyeleti csoport létrehozása a portállal'
description: Ebben a rövid útmutatóban egy felügyeleti Azure Portal hoz létre, amely erőforrás-hierarchiába rendezi az erőforrásokat.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom:
- mode-portal
ms.openlocfilehash: 1b0f7b0d98b2cde1343325d5e4a2979e3e663b68
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535903"
---
# <a name="quickstart-create-a-management-group"></a>Rövid útmutató: Felügyeleti csoport létrehozása

A felügyeleti csoportok olyan tárolók, amelyek segítségével több előfizetés hozzáférését, szabályzatát és megfelelőségét kezelheti. Hozza létre ezeket a tárolókat egy hatékony és [](../policy/overview.md) hatékony hierarchia létrehozásához, amely a Azure Policy azure-beli szerepköralapú [hozzáférés-vezérléssel együtt használható.](../../role-based-access-control/overview.md) További információ a felügyeleti csoportokról: [Erőforrások rendszerezése Azure-beli felügyeleti csoportokkal.](overview.md)

A címtárban létrehozott első felügyeleti csoport létrehozása akár 15 percet is igénybe vehet. Vannak olyan folyamatok, amelyek az első alkalommal futnak, amikor beállítják a felügyeleticsoport-szolgáltatást az Azure-ban a címtárhoz. A folyamat befejezésekor értesítést kap. További információkért lásd a [felügyeleti csoportok kezdeti beállítását.](./overview.md#initial-setup-of-management-groups)

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

- A bérlő bármely Azure AD-felhasználója létrehozhat egy felügyeleti csoportot anélkül, hogy a felügyeleti csoport írási engedélye hozzá lenne rendelve a felhasználóhoz, ha a [hierarchiavédelem](./how-to/protect-resource-hierarchy.md#setting---require-authorization) nincs engedélyezve. Ez az új felügyeleti csoport a gyökér [](./how-to/protect-resource-hierarchy.md#setting---default-management-group) felügyeleti csoport vagy az alapértelmezett felügyeleti csoport gyermeke lesz, és a létrehozó "Tulajdonos" szerepkör-hozzárendelést kap. A felügyeleticsoport-szolgáltatás lehetővé teszi ezt a képességet, így a gyökérszinten nincs szükség szerepkör-hozzárendelésre. Létrehozáskor egyetlen felhasználó sem fér hozzá a gyökér felügyeleti csoporthoz. Annak érdekében, hogy ne kelljen megtalálni az Azure AD globális rendszergazdáit a felügyeleti csoportok használatának elkezdésében, engedélyezhetjük a kezdeti felügyeleti csoportok gyökérszinten való létrehozását.

### <a name="create-in-portal"></a>Létrehozás a portálon

1. Jelentkezzen be az [Azure Portal.](https://portal.azure.com)

1. Válassza a **Minden szolgáltatás** kezelése +  >  **irányítás lehetőséget.**

1. Válassza a **Management Groups** lehetőséget.

1. Válassza **a + Felügyeleti csoport hozzáadása lehetőséget.**

   :::image type="content" source="./media/main.png" alt-text="Képernyőkép a Felügyeleti csoportok lapról, amely a gyermek felügyeleti csoportokat és előfizetéseket mutatja.":::

1. Hagyja **bejelölve az Új létrehozása** jelölőnégyzetet, és töltse ki a felügyeleti csoport azonosítója mezőt.

   - A **felügyeleti csoport azonosítója** az a könyvtár egyedi azonosítója, amely a felügyeleti csoporton parancsokat küld el. Ez az azonosító a létrehozás után nem szerkeszthető, mivel a csoport azonosítására az Azure-rendszerben használják. A [gyökér felügyeleti csoport automatikusan](./overview.md#root-management-group-for-each-directory) létrejön egy azonosítóval, amely a Azure Active Directory azonosítója. Minden más felügyeleti csoporthoz rendeljen hozzá egy egyedi azonosítót.
   - A megjelenített név mező az a név, amely megjelenik a Azure Portal. A külön megjelenített név nem kötelező mező a felügyeleti csoport létrehozásakor, és bármikor módosítható.

   :::image type="content" source="./media/create_context_menu.png" alt-text="Képernyőkép az új felügyeleti csoport létrehozására vonatkozó &quot;Felügyeleti csoport hozzáadása&quot; lehetőségről.":::

1. Kattintson a **Mentés** gombra.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A létrehozott felügyeleti csoport eltávolításához kövesse az alábbi lépéseket:

1. Válassza a **Minden szolgáltatás** kezelése +  >  **irányítás lehetőséget.**

1. Válassza a **Management Groups** lehetőséget.

1. Keresse meg a fent létrehozott felügyeleti csoportot, jelölje ki, majd válassza a név **mellett** a Részletek lehetőséget.
   Ezután válassza a **Törlés lehetőséget,** és erősítse meg a parancssort.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy felügyeleti csoportot az erőforrás-hierarchia rendszerezéséhez. A felügyeleti csoport előfizetéseket vagy más felügyeleti csoportokat is képes tartani.

Ha többet szeretne megtudni a felügyeleti csoportokról és az erőforrás-hierarchia kezelésével kapcsolatban, folytassa a következővel:

> [!div class="nextstepaction"]
> [Erőforrások kezelése felügyeleti csoportokkal](./manage.md)

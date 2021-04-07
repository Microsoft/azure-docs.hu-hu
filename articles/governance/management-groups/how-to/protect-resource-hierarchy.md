---
title: Az erőforrás-hierarchia elleni védelem – Azure-szabályozás
description: Ismerje meg, hogyan védhető az erőforrás-hierarchia olyan hierarchia-beállításokkal, amelyek tartalmazzák az alapértelmezett felügyeleti csoport beállítását.
ms.date: 02/05/2021
ms.topic: conceptual
ms.openlocfilehash: 0f0afb5401fc646d26598a211604790af191f156
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99594586"
---
# <a name="how-to-protect-your-resource-hierarchy"></a>Az erőforrás-hierarchia elleni védelem

Az erőforrások, erőforráscsoportok, előfizetések, felügyeleti csoportok és bérlők együttesen alkotják az erőforrás-hierarchiát. A gyökérszintű felügyeleti csoport (például az Azure egyéni szerepkörei vagy Azure Policy szabályzat-hozzárendelés) beállításai befolyásolhatják az erőforrás-hierarchiában lévő összes erőforrást. Fontos, hogy megvédje az erőforrás-hierarchiát olyan változásokkal szemben, amelyek negatív hatással lehetnek az összes erőforrásra.

A felügyeleti csoportok már rendelkeznek hierarchia-beállításokkal, amelyek segítségével a bérlői rendszergazda vezérelheti ezeket a viselkedéseket. Ez a cikk ismerteti az összes rendelkezésre álló hierarchia-beállítást és azok beállításának módját.

## <a name="azure-rbac-permissions-for-hierarchy-settings"></a>Az Azure RBAC engedélyei a hierarchia beállításaihoz

A hierarchia beállításainak konfigurálásához a következő két erőforrás-szolgáltató művelet szükséges a legfelső szintű felügyeleti csoportban:

- `Microsoft.Management/managementgroups/settings/write`
- `Microsoft.Management/managementgroups/settings/read`

Ezek a műveletek csak a hierarchia beállításainak olvasását és frissítését teszik lehetővé a felhasználóknak. A műveletek nem biztosítanak semmilyen más hozzáférést a felügyeleti csoport hierarchiájának vagy erőforrásainak a hierarchiában. Mindkét művelet az Azure beépített szerepkör- **hierarchiájának beállítások rendszergazdájánál** érhető el.

## <a name="setting---default-management-group"></a>Alapértelmezett felügyeleti csoport beállítása

Alapértelmezés szerint a rendszer a bérlőn belül hozzáadott új előfizetést adja hozzá a gyökérszintű felügyeleti csoport tagjaként. Ha a szabályzat-hozzárendelések, az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) és más irányítási szerkezetek hozzá vannak rendelve a legfelső szintű felügyeleti csoporthoz, azonnal érvénybe lépnek az új előfizetések. Emiatt számos szervezet nem alkalmazza ezeket a szerkezeteket a legfelső szintű felügyeleti csoportba, még akkor is, ha ez a kívánt hely a hozzárendeléshez. Más esetekben az új előfizetések esetében szigorúbban korlátozó vezérlőkre van szükség, de az összes előfizetéshez nem rendelhető hozzá. Ez a beállítás mindkét használati esetet támogatja.

Azáltal, hogy lehetővé teszi az új előfizetések alapértelmezett felügyeleti csoportjának meghatározását, a szervezeti szintű irányítási szerkezetek alkalmazhatók a legfelső szintű felügyeleti csoportra, és egy külön felügyeleti csoport, amelynek a szabályzat-hozzárendelései vagy egy új előfizetéshez jobban illeszkedő Azure-szerepkör-hozzárendelések adhatók meg.

### <a name="set-default-management-group-in-portal"></a>Alapértelmezett felügyeleti csoport beállítása a portálon

A beállítás Azure Portalban való konfigurálásához kövesse az alábbi lépéseket:

1. A keresési sáv használatával megkeresheti és kiválaszthatja a felügyeleti csoportok elemet.

1. A gyökérszintű felügyeleti csoportban válassza a **részletek** elemet a felügyeleti csoport neve mellett.

1. A **Beállítások** területen válassza a **hierarchia beállításai** lehetőséget.

1. Válassza az **alapértelmezett felügyeleti csoport módosítása** gombot.

   > [!NOTE]
   > Ha az **alapértelmezett felügyeleti csoport módosítása** gomb le van tiltva, vagy a megtekintett felügyeleti csoport nem a gyökérszintű felügyeleti csoport, vagy a rendszerbiztonsági tag nem rendelkezik a hierarchia beállításainak módosításához szükséges engedélyekkel.

1. Válasszon ki egy felügyeleti csoportot a hierarchiából, és használja a **Select (kiválasztás** ) gombot.

### <a name="set-default-management-group-with-rest-api"></a>Alapértelmezett felügyeleti csoport beállítása REST API

A beállítás REST APIsal való konfigurálásához a [hierarchia-beállítások](/rest/api/resources/hierarchysettings) végpontot nevezzük. Ehhez használja a következő REST API URI és szövegtörzs formátumot. Cserélje le a `{rootMgID}` értéket a gyökér felügyeleti csoportjának azonosítójával, és a `{defaultGroupID}` felügyeleti csoport azonosítójának használatával az alapértelmezett felügyeleti csoport lesz:

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- Kérelem törzse

  ```json
  {
      "properties": {
          "defaultManagementGroup": "/providers/Microsoft.Management/managementGroups/{defaultGroupID}"
      }
  }
  ```

Ha az alapértelmezett felügyeleti csoportot vissza szeretné állítani a gyökérszintű felügyeleti csoportra, használja ugyanazt a végpontot, és állítsa a **defaultManagementGroup** értékre `/providers/Microsoft.Management/managementGroups/{rootMgID}` .

## <a name="setting---require-authorization"></a>Beállítás – engedélyezés szükséges

Alapértelmezés szerint bármely felhasználó új felügyeleti csoportokat hozhat létre a bérlőn belül. A bérlők rendszergazdái dönthetnek úgy, hogy csak bizonyos felhasználók számára kívánják biztosítani a következetesség és a megfelelőség fenntartását a felügyeleti csoport hierarchiájában. Ha engedélyezve van, a felhasználónak a `Microsoft.Management/managementGroups/write` felügyeleti csoporton kell megadnia a műveletet az új alárendelt felügyeleti csoportok létrehozásához.

### <a name="set-require-authorization-in-portal"></a>Engedélyezés megkövetelése a portálon

A beállítás Azure Portalban való konfigurálásához kövesse az alábbi lépéseket:

1. A keresési sáv használatával megkeresheti és kiválaszthatja a felügyeleti csoportok elemet.

1. A gyökérszintű felügyeleti csoportban válassza a **részletek** elemet a felügyeleti csoport neve mellett.

1. A **Beállítások** területen válassza a **hierarchia beállításai** lehetőséget.

1. Az **új felügyeleti csoportok létrehozásához szükséges engedélyek** bekapcsolása. lehetőség bekapcsolva.

   > [!NOTE]
   > Ha az **új felügyeleti csoportok létrehozásához engedélyekre van szükség.** a váltógomb le van tiltva, vagy a megtekintett felügyeleti csoport nem a gyökérszintű felügyeleti csoport, vagy a rendszerbiztonsági tag nem rendelkezik a hierarchia beállításainak módosításához szükséges engedélyekkel.

### <a name="set-require-authorization-with-rest-api"></a>Engedélyezés megkövetelése REST API

A beállítás REST APIsal való konfigurálásához a [hierarchia-beállítások](/rest/api/resources/hierarchysettings) végpontot nevezzük. Ehhez használja a következő REST API URI és szövegtörzs formátumot. Ez az érték egy _logikai_ érték, ezért **igaz** vagy **hamis** értéket adjon meg az értékhez. A **true** értékkel engedélyezheti a felügyeleti csoport hierarchiájának védelmét:

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- Kérelem törzse

  ```json
  {
      "properties": {
          "requireAuthorizationForGroupCreation": true
      }
  }
  ```

Ha vissza szeretné kapcsolni a beállítást, használja ugyanazt a végpontot, és állítsa a **requireAuthorizationForGroupCreation** értéket **hamis** értékre.

## <a name="next-steps"></a>Következő lépések

A felügyeleti csoportokkal kapcsolatos további tudnivalókért lásd:

- [Felügyeleti csoportok létrehozása az Azure-erőforrások rendszerezéséhez](../create-management-group-portal.md)
- [Felügyeleti csoportok módosítása, törlése és kezelése](../manage.md)
---
title: Engedélyek az Azure Reservations megtekintéséhez és kezeléséhez
description: Ismerje meg, hogyan lehet megtekinteni és kezelni az Azure Reservationst a Azure Portal.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 04/15/2021
ms.author: banders
ms.openlocfilehash: fe2f36b08f98ceb2a5f6085510b589a712ff194d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780460"
---
# <a name="permissions-to-view-and-manage-azure-reservations"></a>Engedélyek az Azure Reservations megtekintéséhez és kezeléséhez

Ez a cikk bemutatja, hogyan működnek a foglalási engedélyek, és hogyan használhatja a felhasználók az Azure-foglalásokat a Azure Portal.

## <a name="who-can-manage-a-reservation-by-default"></a>Alapértelmezés szerint kik kezelhetik a foglalásokat?

Alapértelmezés szerint a következő felhasználók tekinthetik meg és kezelhetik a foglalásokat:

- A foglalást vásárló személyt és a foglalás vásárlásához használt számlázási előfizetés fiókadminisztrátorát a rendszer hozzáadja a foglalási rendeléshez.
- Nagyvállalati Szerződéssel és Microsoft Ügyfélszerződéssel rendelkező számlázási rendszergazdák.
- Emelt szintű hozzáféréssel rendelkezik az összes Azure-előfizetés és felügyeleti csoport kezeléséhez

A foglalási életciklus független az Azure-előfizetéstől, így a foglalás nem az Azure-előfizetéshez elérhető erőforrás. Ehelyett ez egy bérlőszintű erőforrás, amely saját Azure RBAC-engedéllyel rendelkezik, és elkülönül az előfizetésektől. A foglalások nem öröklik az előfizetések engedélyét a vásárlás után.

## <a name="how-billing-administrators-can-view-or-manage-reservations"></a>Hogyan használhatja a számlázási rendszergazdák a foglalásokat?

Ha Ön számlázási rendszergazda, a következő lépésekkel megtekintheti és kezelheti az összes foglalást és foglalási tranzakciót.

1. Jelentkezzen be a [Azure Portal,](https://portal.azure.com) és lépjen a **Cost Management + Billing.**
    - Ha Ön EA-rendszergazda, a bal oldali menüben válassza a **Számlázási** hatókörök lehetőséget, majd a számlázási hatókörök listájából válasszon ki egyet.
    - Ha Ön a számlázási Microsoft Ügyfélszerződés tulajdonosa, a bal oldali menüben válassza a Számlázási **profilok lehetőséget.** A számlázási profilok listájában válasszon ki egyet.
1. A bal oldali menüben válassza a **Termékek + szolgáltatások**  >  **Foglalások lehetőséget.**
1. Megjelenik az EA-regisztrációs vagy számlázási profil foglalásának teljes listája.
1. A számlázási rendszergazdák úgy veheti át a  foglalás tulajdonjogát, hogy kiválasztják azt, majd a megjelenő ablakban kiválasztják a Hozzáférés megadása gombra.

### <a name="how-to-add-billing-administrators"></a>Számlázási rendszergazdák hozzáadása

Adjon hozzá egy felhasználót számlázási rendszergazdaként egy Nagyvállalati Szerződéshez vagy egy Microsoft Ügyfélszerződéshez:

- Nagyvállalati Szerződés esetén vegye fel a felhasználókat _vállalati rendszergazda_ szerepkörbe az összes foglalási rendelés megtekintéséhez és kezeléséhez, amelyekre a Nagyvállalati Szerződés vonatkozik. A vállalati rendszergazdák megtekinthetik és kezelhetik a foglalásokat **a Cost Management + Billing.**
    - A vállalati _rendszergazda (csak olvasási)_ szerepkört használó felhasználók csak a következő webhelyről **Cost Management + Billing.** 
    - A részleg rendszergazdái és a fióktulajdonosai nem tekinthetik meg a foglalásokat _kivéve_, ha kifejezetten a hozzáférés-vezérlés (IAM) használatával lettek hozzáadva. További információért lásd: [Az Azure Enterprise szerepköreinek kezelése](../manage/understand-ea-roles.md).
- Microsoft Ügyfélszerződés esetén a számlázási profil tulajdonosi szerepkörével vagy a számlázási profil közreműködői szerepkörrel rendelkező felhasználók a számlázási profillal végzett összes foglalásvásárlást kezelhetik. A számlázási profil olvasói és a számlakezelők a számlázási profillal kifizetett összes foglalást megtekinthetik. A foglalásokat azonban nem módosíthatják.
    További információkért lásd [a számlázási profil szerepköreit és azok feladatait](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks) ismertető cikket.

## <a name="view-reservations-with-azure-rbac-access"></a>Foglalások megtekintése Azure RBAC-hozzáféréssel

Ha megvásárolta a foglalást, vagy ha hozzáadta egy foglaláshoz, a következő lépésekkel megtekintheti és kezelheti a foglalásokat.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza a **Minden szolgáltatás** > **Foglalások** lehetőséget azoknak a foglalásoknak a megtekintéséhez, amelyekhez hozzáféréssel rendelkezik.

## <a name="users-with-elevated-access-can-manage-all-azure-subscriptions-and-management-groups"></a>Az emelt szintű hozzáféréssel rendelkezik felhasználók az összes Azure-előfizetést és felügyeleti csoportot kezelhetik

A felhasználó jogosultságszintre emelheti az összes Azure-előfizetés és felügyeleti [csoport felügyeletéhez szükséges jogosultságszintet.](../../role-based-access-control/elevate-access-global-admin.md?toc=/azure/cost-management-billing/reservations/toc.json)

Miután megemelte a jogosultságszintet:

1. Lépjen a **Minden szolgáltatás**  >  **foglalása lapra,** és tekintse meg a bérlőben foglalt összes foglalást.
1. A foglalás módosításához adja hozzá magát a foglalási rendelés tulajdonosaként a Hozzáférés-vezérlés (IAM) használatával.

## <a name="give-users-azure-rbac-access-to-individual-reservations"></a>Azure RBAC-hozzáférés hozzáadása a felhasználók számára az egyes foglalások számára

Azok a felhasználók, akik tulajdonosi hozzáféréssel rendelkezik a foglalások és a számlázási rendszergazdák számára, delegálhatják a hozzáférés-kezeléseket egy adott foglalási rendeléshez.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza a **Minden szolgáltatás** > **Foglalások** lehetőséget azoknak a foglalásoknak a megtekintéséhez, amelyekhez hozzáféréssel rendelkezik.
1. Válassza ki azt a foglalást, amelyhez való hozzáférést delegálni szeretné más felhasználóknak.
1. A foglalási adatoknál válassza ki a foglalási rendelést.
1. Válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget.
1. Válassza a **Szerepkör-hozzárendelés hozzáadása** > **Szerepkör** > **Tulajdonos** lehetőséget. Ha korlátozott hozzáférést szeretne biztosítani, válasszon egy másik szerepkört.
1. Írja be annak a felhasználónak az e-mail-címét, akit tulajdonosként kíván hozzáadni.
1. Válassza ki a felhasználót, majd válassza a **Mentés** lehetőséget.

## <a name="next-steps"></a>További lépések

- [Az Azure Reservations kezelése](manage-reserved-vm-instance.md).
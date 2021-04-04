---
title: Privileged Identity Management (PIM) Azure Resource szerepköreinek naplózási jelentésének megtekintése – Azure AD | Microsoft Docs
description: Az Azure-erőforrás szerepköreinek tevékenység-és naplózási előzményeinek megtekintése Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45144f64789a19390984c3f9f6a660e3c3300215
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96002493"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-privileged-identity-management"></a>Az Azure Resource szerepköreinek tevékenység-és naplózási előzményeinek megtekintése Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) használatával megtekintheti a szervezeten belüli Azure-erőforrások szerepköreinek tevékenységeit, aktiválásait és naplózási előzményeit. Ide tartoznak az előfizetések, az erőforráscsoportok és a virtuális gépek is. Az Azure szerepköralapú hozzáférés-vezérlési funkcióit kihasználó Azure Portal összes erőforrása igénybe veheti a Privileged Identity Management biztonsági és életciklus-kezelési képességeit.

> [!NOTE]
> Ha a szervezete egy Azure-beli [delegált erőforrás-kezelést](../../lighthouse/concepts/azure-delegated-resource-management.md)használó szolgáltatónál kiszervezett felügyeleti funkciókat használ, az adott szolgáltató által meghatalmazott szerepkör-hozzárendelések nem jelennek meg.

## <a name="view-activity-and-activations"></a>Tevékenységek és aktiválások megtekintése

Ha szeretné megtekinteni, hogy egy adott felhasználó milyen műveleteket végez a különböző erőforrásokban, megtekintheti az adott aktiválási időszakhoz társított Azure-erőforrás-tevékenységeket.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza ki az **Azure-erőforrásokat**.

1. Válassza ki azt az erőforrást, amelynek a tevékenységeit és aktiválásait meg szeretné tekinteni.

1. Válasszon **szerepköröket** vagy **tagokat**.

1. Válasszon ki egy felhasználót.

    Ekkor megjelenik az Azure-erőforrások felhasználói műveleteinek összegzése dátum szerint. Emellett az adott időszakra vonatkozó legutóbbi szerepkör-aktiválásokat is megjeleníti.

    ![Felhasználói adatok az erőforrás-tevékenység összefoglalásával és a szerepkör-aktiválásokkal](media/azure-pim-resource-rbac/rbac-user-details.png)

1. Válasszon ki egy adott szerepkör-aktiválást a részletek és a kapcsolódó Azure-erőforrás-tevékenységek megtekintéséhez, amelyek a felhasználó aktív állapotban voltak.

    [![Szerepkör-aktiválás kiválasztva és tevékenység részletei](media/azure-pim-resource-rbac/export-membership.png "Szerepkör-aktiválás kiválasztva és tevékenység részletei")](media/azure-pim-resource-rbac/export-membership.png)

## <a name="export-role-assignments-with-children"></a>Szerepkör-hozzárendelések exportálása gyermekekkel

Lehet, hogy megfelelőségi követelményt kell megadnia, amelyben meg kell adnia a rendszernaplók szerepkör-hozzárendeléseinek teljes listáját. Privileged Identity Management lehetővé teszi a szerepkör-hozzárendelések lekérdezését egy adott erőforráson, amely tartalmazza az összes alárendelt erőforráshoz tartozó szerepkör-hozzárendeléseket. Korábban nehéz volt, hogy a rendszergazdák teljes listát kapjanak az előfizetéshez tartozó szerepkör-hozzárendelésekről, és minden egyes erőforráshoz hozzá kellett exportálni a szerepkör-hozzárendeléseket. A Privileged Identity Management használatával lekérdezheti az összes aktív és jogosult szerepkör-hozzárendelést egy előfizetésben, beleértve az összes erőforráscsoport és erőforrás szerepkör-hozzárendelését is.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza ki az **Azure-erőforrásokat**.

1. Válassza ki azt az erőforrást, amelyhez szerepkör-hozzárendeléseket szeretne exportálni, például egy előfizetést.

1. Válassza ki a **Members** (Tagok) elemet.

1. Válassza az **Exportálás** lehetőséget az Exportálás tagsága panel megnyitásához.

    [![Tagság exportálása ablaktábla az összes tag exportálásához](media/azure-pim-resource-rbac/export-membership.png "Tagság exportálása lap az összes tag exportálásához")](media/azure-pim-resource-rbac/export-membership.png)

1. Válassza az **összes tag exportálása** lehetőséget az összes szerepkör-hozzárendelés exportálásához egy CSV-fájlban.

    ![Exportált szerepkör-hozzárendelések CSV-fájlban az Excelben való megjelenítésként](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>Erőforrás-naplózási előzmények megtekintése

Az erőforrás-naplózás az erőforrás összes szerepkör-tevékenységének nézetét jeleníti meg.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza ki az **Azure-erőforrásokat**.

1. Válassza ki azt az erőforrást, amelyre vonatkozóan meg szeretné tekinteni a naplózási előzményeket.

1. Válassza az **erőforrás-naplózás** lehetőséget.

1. Az előzmények szűrése előre megadott dátummal vagy egyéni tartománnyal.

    [![Erőforrás-naplózási lista szűrőkkel](media/azure-pim-resource-rbac/rbac-resource-audit.png "Erőforrás-naplózási lista szűrőkkel")](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. A **naplózás típusa** beállításnál válassza az **aktiválás (hozzárendelt + aktivált)** lehetőséget.

    [![Erőforrás-naplózási lista a naplózási típus](media/azure-pim-resource-rbac/rbac-audit-activity.png "Az erőforrás-naplózási lista aktiválása alapján szűrve")](media/azure-pim-resource-rbac/rbac-audit-activity.png) ![ aktiválása alapján szűrve A naplózási típus aktiválásával szűrt erőforrás-naplózási lista](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. A **művelet** területen kattintson az **(tevékenység)** elemre a felhasználó számára, hogy megtekintse a felhasználó tevékenységének részleteit az Azure-erőforrásokban.

    ![Egy adott művelet felhasználói tevékenységének részletei](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>Saját naplózás megtekintése

A saját audit lehetővé teszi a személyes szerepkör-tevékenységek megtekintését.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza ki az **Azure-erőforrásokat**.

1. Válassza ki azt az erőforrást, amelyre vonatkozóan meg szeretné tekinteni a naplózási előzményeket.

1. Válassza **a saját naplózás** lehetőséget.

1. Az előzmények szűrése előre megadott dátummal vagy egyéni tartománnyal.

    [![Az aktuális felhasználó naplózási listája](media/azure-pim-resource-rbac/my-audit-time.png "Az aktuális felhasználó naplózási listája")](media/azure-pim-resource-rbac/my-audit-time.png)

> [!NOTE]
> A naplózási előzmények eléréséhez globális rendszergazdai vagy Kiemelt szerepkörű rendszergazdai szerepkörre van szükség.

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>Az OK, a jóváhagyó és a jegy számának beolvasása jóváhagyási eseményekhez

1. Jelentkezzen be a [Azure Portalra](https://aad.portal.azure.com) a Kiemelt szerepkörű rendszergazda szerepkör engedélyeivel, és nyissa meg az Azure ad-t.
1. Válassza a **Naplók** lehetőséget.
1. A **szolgáltatás** szűrő használatával csak a Privileged Identity Management szolgáltatáshoz tartozó naplózási eseményeket jelenítheti meg. A **naplózási naplók** lapon a következőket teheti:

    - Tekintse meg a naplózási esemény okát az **Állapot oka** oszlopban.
    - Tekintse meg a jóváhagyót a "tag hozzáadása a szerepkörhöz jóváhagyva" eseményhez a **kezdeményező (Actor)** oszlopban.

    [![A PIM szolgáltatás naplójának szűrése](media/azure-pim-resource-rbac/filter-audit-logs.png "A PIM szolgáltatás naplójának szűrése")](media/azure-pim-resource-rbac/filter-audit-logs.png)

1. Válassza ki a naplózási eseményt a **részletek** ablaktábla **tevékenység** lapján a jegy számának megtekintéséhez.
  
    [![Ellenőrizze a naplózási esemény jegyének számát](media/azure-pim-resource-rbac/audit-event-ticket-number.png "Ellenőrizze a naplózási esemény jegyének számát")](media/azure-pim-resource-rbac/audit-event-ticket-number.png)]

1. A naplózási esemény **részletek** ablaktáblájának **célok** lapján megtekintheti a kérelmezőt (a szerepkört aktiváló személyt). Az Azure erőforrás-szerepkörökhöz háromféle cél létezik:

    - A szerepkör (**típus** = szerepkör)
    - A kérelmező (**típus** = egyéb)
    - A jóváhagyó (**típus** = felhasználó)

    [![A cél típusának keresése](media/azure-pim-resource-rbac/audit-event-target-type.png "A cél típusának keresése")](media/azure-pim-resource-rbac/audit-event-target-type.png)

A jóváhagyási eseménynél közvetlenül a naplózási esemény egy esemény a "tag hozzáadása a szerepkörhöz" befejeződik, ahol a **kezdeményező (színész)** a kérelmező. A legtöbb esetben nem kell megkeresnie a kérelmezőt a jóváhagyási kérelemben egy naplózási perspektívából.

## <a name="next-steps"></a>Következő lépések

- [Azure-beli erőforrás-szerepkörök kiosztása Privileged Identity Management](pim-resource-roles-assign-roles.md)
- [Azure-beli erőforrás-szerepkörökre vonatkozó kérelmek jóváhagyása vagy elutasítása Privileged Identity Management](pim-resource-roles-approval-workflow.md)
- [Azure AD-szerepkörök naplózási előzményeinek megtekintése Privileged Identity Management](pim-how-to-use-audit-log.md)

---
title: Azure ad-szerepkörök naplózási jelentésének megtekintése az Azure AD PIM-ben | Microsoft Docs
description: Megtudhatja, hogyan tekintheti meg az Azure AD-szerepkörök naplózási naplóinak előzményeit Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 01/07/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8481c562ecbab1f26e877e55a5a9454695ddf4c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92370831"
---
# <a name="view-audit-history-for-azure-ad-roles-in-privileged-identity-management"></a>Azure AD-szerepkörök naplózási előzményeinek megtekintése Privileged Identity Management

A Privileged Identity Management (PIM) naplózási előzményeivel megtekintheti az elmúlt 30 napban lévő összes szerepkör-hozzárendelést és aktiválást az összes Kiemelt szerepkör esetében. Ha szeretné megtekinteni a Azure Active Directory (Azure AD) szervezet tevékenységének teljes naplózási előzményeit, beleértve a rendszergazdai, a végfelhasználói és a szinkronizálási tevékenységet, használhatja a [Azure Active Directory biztonsági és tevékenységi jelentéseket](../reports-monitoring/overview-reports.md).

## <a name="determine-your-version-of-pim"></a>A PIM verziójának meghatározása

A 2019-es verziótól kezdődően a Privileged Identity Management Azure AD-szerepkörök részét egy új verzióra frissíti a rendszer, amely megfelel az Azure-erőforrás-szerepkörökkel kapcsolatos élményeknek. Ez további funkciókat hoz létre, valamint [a meglévő API módosításait](azure-ad-roles-features.md#api-changes)is. Az új verzió bevezetését követően a cikkben ismertetett eljárások a jelenleg használt Privileged Identity Management verziójától függenek. Az ebben a szakaszban ismertetett lépéseket követve meghatározhatja, hogy a Privileged Identity Management melyik verzióját kell megadnia. A Privileged Identity Management-verziójának megismerése után kiválaszthatja a jelen cikkben szereplő, az adott verziónak megfelelő eljárásokat.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) egy olyan felhasználóval, aki tagja a [Kiemelt szerepkörű rendszergazda](../roles/permissions-reference.md#privileged-role-administrator) szerepkörnek.
1. Nyissa meg **Azure ad Privileged Identity Management**. Ha az Áttekintés oldal tetején található egy szalagcím, kövesse a jelen cikk **új verzió** lapján található utasításokat. Ellenkező esetben kövesse az **előző verzió** lapon megjelenő utasításokat.

    [![Az "Azure AD-szerepkörök – címtárbeli szerepkörök naplózási előzményei" lapot megjelenítő képernyőkép.](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "Válassza ki a verzióhoz tartozó fület")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

# <a name="new-version"></a>[Új verzió](#tab/new)

Kövesse az alábbi lépéseket az Azure AD-szerepkörök naplózási előzményeinek megtekintéséhez.

## <a name="view-resource-audit-history"></a>Erőforrás-naplózási előzmények megtekintése

Az erőforrás-naplózás az Azure AD-szerepkörökhöz társított összes tevékenység megtekintését teszi lehetővé.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza az **Azure ad-szerepkörök** elemet.

1. Válassza az **erőforrás-naplózás** lehetőséget.

1. Az előzmények szűrése előre megadott dátummal vagy egyéni tartománnyal.

    ![Erőforrás-naplózási lista szűrőkkel](media/azure-pim-resource-rbac/rbac-resource-audit.png)

## <a name="view-my-audit"></a>Saját naplózás megtekintése

A saját audit lehetővé teszi a személyes szerepkör-tevékenységek megtekintését.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza az **Azure ad-szerepkörök** elemet.

1. Válassza ki azt az erőforrást, amelyre vonatkozóan meg szeretné tekinteni a naplózási előzményeket.

1. Válassza **a saját naplózás** lehetőséget.

1. Az előzmények szűrése előre megadott dátummal vagy egyéni tartománnyal.

    ![Az aktuális felhasználó naplózási listája](media/azure-pim-resource-rbac/my-audit-time.png)

# <a name="previous-version"></a>[Előző verzió](#tab/previous)

## <a name="view-audit-history"></a>Naplózási előzmények megtekintése

Kövesse az alábbi lépéseket az Azure AD-szerepkörök naplózási előzményeinek megtekintéséhez.

1. Jelentkezzen be [Azure Portalba](https://portal.azure.com/) egy olyan felhasználóval, aki tagja a [Kiemelt szerepkörű rendszergazda](../roles/permissions-reference.md#privileged-role-administrator) szerepkörnek.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza az **Azure ad-szerepkörök** elemet.

1. Válassza ki a **címtárbeli szerepkörök naplózási előzményeit**.

    A naplózási előzményektől függően egy oszlopdiagram jelenik meg az összes aktiválással, a napi maximális aktiválással és napi átlagos aktiválással együtt.

    [![Azure AD-szerepkörök új verziója](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "Címtárbeli szerepkörök naplózási előzményeinek megtekintése")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    A lap alján egy tábla jelenik meg, amely a rendelkezésre álló naplózási előzményekben szereplő egyes műveletekkel kapcsolatos információkat tartalmazza. Az oszlopok a következő jelentésekkel rendelkeznek:

    | Oszlop | Leírás |
    | --- | --- |
    | Idő | Ha a művelet bekövetkezett. |
    | Requestor (Kérelmező) | A szerepkör aktiválását vagy módosítását kérő felhasználó. Ha az érték az **Azure System**, további információért tekintse meg az Azure naplózási előzményeit. |
    | Művelet | A kérelmező által végrehajtott műveletek. A műveletek lehetnek például a hozzárendelés, a hozzárendelés megszüntetése, az aktiválás, az inaktiválás vagy a AddedOutsidePIM. |
    | Tag | Az a felhasználó, aki aktivál vagy hozzárendel egy szerepkörhöz. |
    | Szerepkör | A felhasználó által hozzárendelt vagy aktivált szerepkör. |
    | Indoklása | Az aktiválás során az OK mezőbe beírt szöveg |
    | Lejárat | Ha egy aktivált szerepkör lejár. Csak a jogosult szerepkör-hozzárendelésekre vonatkozik. |

1. A naplózási előzmények rendezéséhez kattintson az **idő**, a **művelet** és a **szerepkör** gombokra.

## <a name="filter-audit-history"></a>Naplózási előzmények szűrése

1. A naplózási Előzmények lap tetején kattintson a **szűrő** gombra.

    Megjelenik a **frissítési diagram paramétereinek** panelje.

1. Az **időtartomány** területen válassza ki az időtartományt.

1. A **szerepkörök** területen jelölje be a jelölőnégyzeteket a megtekinteni kívánt szerepkörök jelzéséhez.

    ![Diagram paramétereinek frissítése panel](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. A szűrt naplózási előzmények megtekintéséhez válassza a **kész** lehetőséget.

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>Az OK, a jóváhagyó és a jegy számának beolvasása jóváhagyási eseményekhez

1. Jelentkezzen be a [Azure Portalra](https://aad.portal.azure.com) a Kiemelt szerepkörű rendszergazda szerepkör engedélyeivel, és nyissa meg az Azure ad-t.
1. Válassza a **Naplók** lehetőséget.
1. A **szolgáltatás** szűrő használatával csak a Privileged Identity Management szolgáltatáshoz tartozó naplózási eseményeket jelenítheti meg. A **naplózási naplók** lapon a következőket teheti:

    - Tekintse meg a naplózási esemény okát az **Állapot oka** oszlopban.
    - Tekintse meg a jóváhagyót a "tag hozzáadása a szerepkörhöz jóváhagyva" eseményhez a **kezdeményező (Actor)** oszlopban.

    [![A "naplók" nevű, "a kezdeményező (színész)" menü megnyitásával és a "PIM" beállítással megjelenített képernyőkép.](media/pim-how-to-use-audit-log/filter-audit-logs.png "A PIM szolgáltatás naplójának szűrése")](media/pim-how-to-use-audit-log/filter-audit-logs.png)

1. Válassza ki a naplózási eseményt a **részletek** ablaktábla **tevékenység** lapján a jegy számának megtekintéséhez.
  
    [![A "Részletek" panelen Kiemelt jegyek számát megjelenítő képernyőkép a "naplók" oldalon.](media/pim-how-to-use-audit-log/audit-event-ticket-number.png "Ellenőrizze a naplózási esemény jegyének számát")](media/pim-how-to-use-audit-log/audit-event-ticket-number.png)

1. A naplózási esemény **részletek** ablaktáblájának **célok** lapján megtekintheti a kérelmezőt (a szerepkört aktiváló személyt). Az Azure AD-szerepkörökhöz két típusú cél létezik:

    - A szerepkör (**típus** = szerepkör)
    - A kérelmező (**típus** = felhasználó)

Általában a jóváhagyási esemény felett közvetlenül a naplózási napló esemény a "tag hozzáadása a szerepkörhöz befejeződött" esemény, ahol a **kezdeményező (színész)** a kérelmező. A legtöbb esetben nem kell megkeresnie a kérelmezőt a jóváhagyási kérelemben egy naplózási perspektívából.

---

## <a name="next-steps"></a>Következő lépések

- [Az Azure Resource szerepköreinek tevékenység-és naplózási előzményeinek megtekintése Privileged Identity Management](azure-pim-resource-rbac.md)

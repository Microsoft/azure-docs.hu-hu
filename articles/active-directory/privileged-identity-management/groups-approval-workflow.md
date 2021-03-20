---
title: Privileged Identity Management-Azure AD-beli csoporttagok és tulajdonosok aktiválási kérelmének jóváhagyása
description: Megtudhatja, hogyan hagyhatja jóvá vagy tilthatja le a szerepkörhöz hozzárendelhető csoportok kéréseit Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 07/27/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a3b6dad33ce678478a201dc8b7b612f4b8507c0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91536987"
---
# <a name="approve-activation-requests-for-privileged-access-group-members-and-owners-preview"></a>Aktiválási kérelmek jóváhagyása az emelt szintű hozzáférési csoport tagjai és tulajdonosainak számára (előzetes verzió)

A Azure Active Directory (Azure AD) Privileged Identity Management (PIM) használatával konfigurálhatja a Kiemelt jogosultságú hozzáférési csoport tagjait és tulajdonosait az aktiválás jóváhagyásához, és az Azure AD-szervezet felhasználóit vagy csoportjait delegált jóváhagyóként kell kiválasztania. A Kiemelt szerepkörű rendszergazda munkaterhelésének csökkentése érdekében ajánlott két vagy több jóváhagyó kiválasztása az egyes csoportokhoz. A delegált jóváhagyók 24 órával a kérelmek jóváhagyására jogosultak. Ha a kérést 24 órán belül nem hagyták jóvá, a jogosult felhasználónak újra be kell küldenie egy új kérelmet. A 24 órás jóváhagyási idő ablak nem konfigurálható.

Az Azure-erőforrás szerepköreire vonatkozó kérések jóváhagyásához vagy elutasításához kövesse a jelen cikkben ismertetett lépéseket.

## <a name="view-pending-requests"></a>Függőben lévő kérelmek megtekintése

Meghatalmazott jóváhagyóként e-mail-értesítést fog kapni, ha az Azure-erőforrás szerepkörre vonatkozó kérelem jóváhagyásra vár. Privileged Identity Management függőben lévő kérelmeket tekinthet meg.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza a **kérelmek jóváhagyása** lehetőséget.

    ![Kérések jóváhagyása – Azure-erőforrások lap, amely a felülvizsgálati kérelmet tartalmazza](./media/groups-approval-workflow/groups-select-request.png)

    A **szerepkör-aktiválási kérések** szakaszban megtekintheti a jóváhagyásra váró kérelmek listáját.

## <a name="approve-requests"></a>Kérések jóváhagyása

1. Keresse meg és válassza ki a jóváhagyni kívánt kérést, és válassza a **jóváhagyás** lehetőséget.

    ![Képernyőkép, amely a "jóváhagyás" és a "jóváhagyás" gombokat jeleníti meg a kiemelve.](./media/groups-approval-workflow/groups-confirm-approval.png)

1. Az **indoklás** mezőben adja meg az üzleti indoklást.

1. Válassza a **Confirm (megerősítés**) lehetőséget. A jóváhagyás létrehoz egy Azure-értesítést.

## <a name="deny-requests"></a>Kérelmek megtagadása

1. Keresse meg és válassza ki a megtagadni kívánt kérelmet, és válassza a **Megtagadás** lehetőséget.

    ![Kérelmek jóváhagyása – jóváhagyás vagy megtagadás ablaktábla részletekkel és indoklással](./media/groups-approval-workflow/groups-confirm-denial.png)

1. Az **indoklás** mezőben adja meg az üzleti indoklást.

1. Válassza a **Confirm (megerősítés**) lehetőséget. Az Azure-értesítéseket a Megtagadás hozza létre.

## <a name="workflow-notifications"></a>Munkafolyamat-értesítések

Íme néhány információ a munkafolyamat-értesítésekről:

- A jóváhagyókat e-mailben értesítjük, ha a csoport-hozzárendelésre vonatkozó kérést függőben van a felülvizsgálatuk. Az e-mail-értesítések közé tartozik egy közvetlen hivatkozás a kérésre, ahol a jóváhagyó jóváhagyhatja vagy megtagadhatja a kérelmet.
- A kérelmeket az első jóváhagyó, aki jóváhagyja vagy megtagadja.
- Ha egy jóváhagyó válaszol a kérelemre, az összes jóváhagyó értesítést kap a műveletről.

>[!Note]
>Egy rendszergazda, aki úgy véli, hogy egy jóváhagyott felhasználó nem lehet aktív, el tudja távolítani az aktív csoport hozzárendelését Privileged Identity Management. Bár az erőforrás-rendszergazdák nem kapnak értesítést a függőben lévő kérelmekről, kivéve, ha a jóváhagyók, megtekinthetik és megszakítják az összes felhasználóra vonatkozó függőben lévő kérelmek megtekintését Privileged Identity Management.

## <a name="next-steps"></a>Következő lépések

- [Csoport-hozzárendelések kiterjesztése vagy megújítása Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [E-mail-értesítések Privileged Identity Management](pim-email-notifications.md)
- [Csoport-hozzárendelésekre vonatkozó kérelmek jóváhagyása vagy elutasítása Privileged Identity Management](azure-ad-pim-approval-workflow.md)

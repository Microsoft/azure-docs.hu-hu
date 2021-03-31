---
title: Azure-beli erőforrás-szerepkörökre vonatkozó kérelmek jóváhagyása a PIM-ben – Azure AD | Microsoft Docs
description: Megtudhatja, hogyan hagyhatja jóvá vagy tilthatja le a Azure AD Privileged Identity Management (PIM) Azure-beli erőforrás-szerepköreire vonatkozó kérelmeket.
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
ms.openlocfilehash: 096da0547806680f62b569f7c7809a7bbb04cb86
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "84742079"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-privileged-identity-management"></a>Azure-beli erőforrás-szerepkörökre vonatkozó kérelmek jóváhagyása vagy elutasítása Privileged Identity Management

A Azure Active Directory (Azure AD) Privileged Identity Management (PIM) szolgáltatásával konfigurálhatja a szerepköröket az aktiválás jóváhagyásához, és az Azure AD-szervezetből származó felhasználókat vagy csoportokat delegált jóváhagyóként kell kiválasztania. A Kiemelt szerepkörű rendszergazda munkaterhelésének csökkentése érdekében ajánlott két vagy több jóváhagyó kiválasztása az egyes szerepkörökhöz. A delegált jóváhagyók 24 órával a kérelmek jóváhagyására jogosultak. Ha a kérést 24 órán belül nem hagyták jóvá, a jogosult felhasználónak újra be kell küldenie egy új kérelmet. A 24 órás jóváhagyási idő ablak nem konfigurálható.

Az Azure-erőforrás szerepköreire vonatkozó kérések jóváhagyásához vagy elutasításához kövesse a jelen cikkben ismertetett lépéseket.

## <a name="view-pending-requests"></a>Függőben lévő kérelmek megtekintése

Meghatalmazott jóváhagyóként e-mail-értesítést fog kapni, ha az Azure-erőforrás szerepkörre vonatkozó kérelem jóváhagyásra vár. Ezeket a függőben lévő kérelmeket Privileged Identity Management tekintheti meg.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza a **kérelmek jóváhagyása** lehetőséget.

    ![Kérések jóváhagyása – Azure-erőforrások lap, amely a felülvizsgálati kérelmet tartalmazza](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    A **szerepkör-aktiválási kérések** szakaszban megtekintheti a jóváhagyásra váró kérelmek listáját.

## <a name="approve-requests"></a>Kérések jóváhagyása

1. Keresse meg és válassza ki a jóváhagyni kívánt kérelmet. A jóváhagyás vagy megtagadás lap jelenik meg.

    ![Kérelmek jóváhagyása – jóváhagyás vagy megtagadás ablaktábla részletekkel és indoklással](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Az **indoklás** mezőben adja meg az üzleti indoklást.

1. Válassza a **jóváhagyás** lehetőséget. A jóváhagyásról Azure-értesítést fog kapni.

    ![A kérelem jóváhagyását jelző értesítés jóváhagyása](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>Kérelmek megtagadása

1. Keresse meg és válassza ki a megtagadni kívánt kérelmet. A jóváhagyás vagy megtagadás lap jelenik meg.

    ![Kérelmek jóváhagyása – jóváhagyás vagy megtagadás ablaktábla részletekkel és indoklással](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Az **indoklás** mezőben adja meg az üzleti indoklást.

1. Válassza a **Megtagadás** lehetőséget. Megjelenik egy értesítés az elutasítással.

## <a name="workflow-notifications"></a>Munkafolyamat-értesítések

Íme néhány információ a munkafolyamat-értesítésekről:

- E-mailben értesítjük a jóváhagyókat, ha a szerepkörre vonatkozó kérések a felülvizsgálattól függőben vannak. Az e-mail-értesítések közé tartozik egy közvetlen hivatkozás a kérésre, ahol a jóváhagyó jóváhagyhatja vagy megtagadhatja a kérelmet.
- A kérelmeket az első jóváhagyó, aki jóváhagyja vagy megtagadja.
- Ha egy jóváhagyó válaszol a kérelemre, az összes jóváhagyó értesítést kap a műveletről.
- Az erőforrás-rendszergazdák értesítést kapnak, ha egy jóváhagyott felhasználó aktív lesz a szerepkörében.

>[!Note]
>Egy erőforrás-rendszergazda, aki úgy véli, hogy egy jóváhagyott felhasználó nem lehet aktív, el tudja távolítani az aktív szerepkör-hozzárendelést Privileged Identity Management. Bár az erőforrás-rendszergazdák nem kapnak értesítést a függőben lévő kérelmekről, kivéve, ha a jóváhagyók, megtekinthetik és megszakítják az összes felhasználóra vonatkozó függőben lévő kérelmek megtekintését Privileged Identity Management.

## <a name="next-steps"></a>Következő lépések

- [Azure-beli erőforrás-szerepkörök kiterjesztése vagy megújítása Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [E-mail-értesítések Privileged Identity Management](pim-email-notifications.md)
- [Azure AD-szerepkörökre vonatkozó kérelmek jóváhagyása vagy elutasítása Privileged Identity Management](azure-ad-pim-approval-workflow.md)

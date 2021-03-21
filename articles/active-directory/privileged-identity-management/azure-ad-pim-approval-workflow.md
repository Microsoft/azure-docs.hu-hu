---
title: Azure AD-szerepkörökre vonatkozó kérelmek jóváhagyása vagy elutasítása a PIM-ben – Azure AD | Microsoft Docs
description: Megtudhatja, hogyan hagyhatja jóvá vagy tilthatja le a Azure AD Privileged Identity Management (PIM) Azure AD-szerepköreire vonatkozó kérelmeket.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/07/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: d74a243ab10d0fffca960dc5149999560a7b54cd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94980586"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>Azure AD-szerepkörökre vonatkozó kérelmek jóváhagyása vagy elutasítása Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) használatával konfigurálhatja a szerepköröket az aktiválás jóváhagyásához, és egy vagy több felhasználót vagy csoportot delegált jóváhagyóként választhat ki. A delegált jóváhagyók 24 órával a kérelmek jóváhagyására jogosultak. Ha a kérést 24 órán belül nem hagyták jóvá, a jogosult felhasználónak újra be kell küldenie egy új kérelmet. A 24 órás jóváhagyási idő ablak nem konfigurálható.

## <a name="determine-your-version-of-pim"></a>A PIM verziójának meghatározása

A 2019-es verziótól kezdődően a Privileged Identity Management Azure AD-szerepkörök részét egy új verzióra frissíti a rendszer, amely megfelel az Azure-szerepkörökkel kapcsolatos élményeknek. Ez további funkciókat hoz létre, valamint [a meglévő API módosításait](azure-ad-roles-features.md#api-changes)is. Az új verzió bevezetését követően a cikkben ismertetett eljárások a jelenleg használt Privileged Identity Management verziójától függenek. Az ebben a szakaszban ismertetett lépéseket követve meghatározhatja, hogy a Privileged Identity Management melyik verzióját kell megadnia. A Privileged Identity Management-verziójának megismerése után kiválaszthatja a jelen cikkben szereplő, az adott verziónak megfelelő eljárásokat.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) egy olyan felhasználóval, aki tagja a [Kiemelt szerepkörű rendszergazda](../roles/permissions-reference.md#privileged-role-administrator) szerepkörnek.
1. Nyissa meg **Azure ad Privileged Identity Management**. Ha az Áttekintés oldal tetején található egy szalagcím, kövesse a jelen cikk **új verzió** lapján található utasításokat. Ellenkező esetben kövesse az **előző verzió** lapon megjelenő utasításokat.

    [![Válassza az Azure AD > Privileged Identity Management lehetőséget.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Kövesse a cikkben ismertetett lépéseket az Azure AD-szerepkörökre vonatkozó kérelmek jóváhagyásához vagy elutasításához.

## <a name="new-version"></a>[Új verzió](#tab/new)

### <a name="view-pending-requests"></a>Függőben lévő kérelmek megtekintése

Meghatalmazott jóváhagyóként e-mail-értesítést fog kapni, ha egy Azure AD-szerepkörre vonatkozó kérelem jóváhagyása függőben van. Ezeket a függőben lévő kérelmeket Privileged Identity Management tekintheti meg.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza a **kérelmek jóváhagyása** lehetőséget.

    ![Kérelmek jóváhagyása – az Azure AD-szerepkörök felülvizsgálatára vonatkozó kérést megjelenítő lap](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

    A **szerepkör-aktiválási kérések** szakaszban megtekintheti a jóváhagyásra váró kérelmek listáját.

### <a name="approve-requests"></a>Kérések jóváhagyása

1. Keresse meg és válassza ki a jóváhagyni kívánt kérelmet. A jóváhagyás vagy megtagadás lap jelenik meg.

    ![Képernyőkép a "kérelmek jóváhagyása – Azure AD-szerepkörök" oldalról.](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

1. Az **indoklás** mezőben adja meg az üzleti indoklást.

1. Válassza a **jóváhagyás** lehetőséget. A jóváhagyásról Azure-értesítést fog kapni.

    ![A kérelem jóváhagyását jelző értesítés jóváhagyása](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png))

### <a name="deny-requests"></a>Kérelmek megtagadása

1. Keresse meg és válassza ki a megtagadni kívánt kérelmet. A jóváhagyás vagy megtagadás lap jelenik meg.

    ![Kérelmek jóváhagyása – jóváhagyás vagy megtagadás ablaktábla részletekkel és indoklással](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Az **indoklás** mezőben adja meg az üzleti indoklást.

1. Válassza a **Megtagadás** lehetőséget. Megjelenik egy értesítés az elutasítással.

### <a name="workflow-notifications"></a>Munkafolyamat-értesítések

Íme néhány információ a munkafolyamat-értesítésekről:

- E-mailben értesítjük a jóváhagyókat, ha a szerepkörre vonatkozó kérések a felülvizsgálattól függőben vannak. Az e-mail-értesítések közé tartozik egy közvetlen hivatkozás a kérésre, ahol a jóváhagyó jóváhagyhatja vagy megtagadhatja a kérelmet.
- A kérelmeket az első jóváhagyó, aki jóváhagyja vagy megtagadja.
- Ha egy jóváhagyó válaszol a kérelemre, az összes jóváhagyó értesítést kap a műveletről.
- A globális rendszergazdák és a Kiemelt szerepkörű rendszergazdák értesítést kapnak, ha egy jóváhagyott felhasználó aktív lesz a szerepkörében.

>[!NOTE]
>Globális rendszergazda vagy Kiemelt szerepkörű rendszergazda, aki úgy véli, hogy egy jóváhagyott felhasználó nem lehet aktív, távolítsa el az aktív szerepkör-hozzárendelést Privileged Identity Management. Bár a rendszergazdák nem kapnak értesítést a függőben lévő kérelmekről, hacsak nem jóváhagyók, megtekinthetik és megtörölhetik az összes felhasználóra vonatkozó függőben lévő kéréseket a Privileged Identity Management függőben lévő kérelmek megtekintésével.

## <a name="previous-version"></a>[Előző verzió](#tab/previous)

### <a name="view-pending-requests"></a>Függőben lévő kérelmek megtekintése

Meghatalmazott jóváhagyóként e-mail-értesítést fog kapni, ha egy Azure AD-szerepkörre vonatkozó kérelem jóváhagyása függőben van. Ezeket a függőben lévő kérelmeket Privileged Identity Management tekintheti meg.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Kattintson az **Azure ad-szerepkörök** elemre.

1. Kattintson a **kérelmek jóváhagyása** gombra.

    ![Azure AD-szerepkörök – kérelmek jóváhagyása](./media/azure-ad-pim-approval-workflow/approve-requests.png)

    Ekkor megjelenik a jóváhagyásra váró kérelmek listája.

### <a name="approve-requests"></a>Kérések jóváhagyása

1. Válassza ki a jóváhagyni kívánt kéréseket, majd kattintson a **jóváhagyás** gombra a kijelölt kérések jóváhagyása panel megnyitásához.

    ![A kérelmek jóváhagyása a jóváhagyás lehetőség kijelölve](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. A **jóváhagyás oka** mezőbe írjon be egy okot.

    ![A kijelölt kérések jóváhagyása a jóváhagyás indoklásával](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Kattintson az **Approve** (Jóváhagyás) elemre.

    Az állapotjelző szimbólumot a rendszer a jóváhagyással frissíti.

    ![Kijelölt kérelmek jóváhagyása ablaktábla jóváhagyása után gombra kattintás](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

### <a name="deny-requests"></a>Kérelmek megtagadása

1. Válassza ki a megtagadni kívánt kéréseket, majd kattintson a **Megtagadás** gombra a kiválasztott kérelmek megtagadása ablaktábla megnyitásához.

    ![Megtagadási lehetőséggel rendelkező kérelmek listájának jóváhagyása kijelölve](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. A **Megtagadás oka** mezőbe írjon be egy okot.

    ![A kijelölt kérések ablaktábla megtagadása megtagadás miatt](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Válassza a **Megtagadás** lehetőséget.

    A rendszer a megtagadásával frissíti az állapotjelző szimbólumot.

---

## <a name="next-steps"></a>Következő lépések

- [E-mail-értesítések Privileged Identity Management](pim-email-notifications.md)
- [Azure-beli erőforrás-szerepkörökre vonatkozó kérelmek jóváhagyása vagy elutasítása Privileged Identity Management](pim-resource-roles-approval-workflow.md)

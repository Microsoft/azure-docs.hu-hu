---
title: Azure AD egyéni szerepkör – Privileged Identity Management (PIM) frissítése vagy eltávolítása
description: Azure AD egyéni szerepkör-hozzárendelési Privileged Identity Management (PIM) frissítése vagy eltávolítása
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a35442dd8af1cd4acf22de453c8d10460e1e39f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92371528"
---
# <a name="update-or-remove-an-assigned-azure-ad-custom-role-in-privileged-identity-management"></a>Hozzárendelt Azure AD egyéni szerepkör frissítése vagy eltávolítása Privileged Identity Management

Ez a cikk azt ismerteti, hogyan lehet a Privileged Identity Management (PIM) használatával frissíteni vagy eltávolítani az alkalmazás-felügyelethez létrehozott egyéni szerepköröket az Azure Active Directory (Azure AD) felügyeleti felületén. 

- További információ az Azure AD-ben az alkalmazások felügyeletének delegálására szolgáló egyéni szerepkörök létrehozásáról: [Azure Active Directory (előzetes verzió) – egyéni rendszergazdai szerepkörök](../roles/custom-overview.md). 
- Ha még nem használta Privileged Identity Management, további információt a [Privileged Identity Management használatának első](pim-getting-started.md)lépéseiben olvashat.

> [!NOTE]
> Az Azure AD egyéni szerepkörei nincsenek integrálva az előzetes verzióban a beépített címtárbeli szerepkörökbe. Miután a képesség általánosan elérhetővé válik, a szerepkör-kezelés a beépített szerepkörök felületén történik. Ha a következő szalagcím jelenik meg, ezeket a szerepköröket [a beépített szerepkörökkel kapcsolatos felhasználói élményben](pim-how-to-add-role-to-user.md) kell kezelni, és ez a cikk nem alkalmazható:
>
> [![Válassza az Azure AD > Privileged Identity Management lehetőséget.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="update-or-remove-an-assignment"></a>Hozzárendelés frissítése vagy eltávolítása

Az alábbi lépéseket követve frissítheti vagy eltávolíthatja a meglévő egyéni szerepkör-hozzárendeléseket.

1. Jelentkezzen be [Privileged Identity Managementra](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) a Azure Portal egy olyan felhasználói fiókkal, amely hozzá van rendelve a Kiemelt szerepkörű rendszergazda szerepkörhöz.
1. Válassza az **Egyéni Azure ad-szerepkörök (előzetes verzió)** lehetőséget.

    ![A jogosult szerepkör-hozzárendelések megtekintéséhez válassza az Azure AD egyéni szerepkörök előzetes verzióját.](./media/azure-ad-custom-roles-assign/view-custom.png)

1. Válassza ki a **szerepköröket** az Azure ad-alkalmazások egyéni szerepköreinek **hozzárendelési** listájának megtekintéséhez.

    ![Szerepkörök kiválasztása: a jogosult szerepkör-hozzárendelések listája](./media/azure-ad-custom-roles-update-remove/assignments-list.png)

1. Válassza ki a frissíteni vagy eltávolítani kívánt szerepkört.
1. Keresse meg a szerepkör-hozzárendelést a **jogosult szerepkörök** vagy az **aktív szerepkörök** lapon.
1. A szerepkör-hozzárendelés frissítéséhez vagy eltávolításához válassza a **frissítés** vagy az **Eltávolítás** lehetőséget.

    ![Válassza az Eltávolítás vagy a frissítés lehetőséget a jogosult szerepkör-hozzárendelésben](./media/azure-ad-custom-roles-update-remove/remove-update.png)

## <a name="next-steps"></a>Következő lépések

- [Egyéni Azure AD-szerepkör aktiválása](azure-ad-custom-roles-assign.md)
- [Egyéni Azure AD-szerepkör kiosztása](azure-ad-custom-roles-assign.md)
- [Egyéni Azure AD-szerepkör-hozzárendelés konfigurálása](azure-ad-custom-roles-configure.md)

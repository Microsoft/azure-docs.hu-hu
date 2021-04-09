---
title: Azure-erőforrás szerepköreinek aktiválása a PIM-ben – Azure AD | Microsoft Docs
description: Ismerje meg, hogyan aktiválhatja Azure-beli erőforrás-szerepköreit Azure AD Privileged Identity Management (PIM).
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
ms.date: 07/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a6ddde80ca554aea25d24694aff76e61e47d928
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97672457"
---
# <a name="activate-my-azure-resource-roles-in-privileged-identity-management"></a>Azure-beli erőforrás-szerepkörök aktiválása Privileged Identity Management

A Privileged Identity Management (PIM) használatával engedélyezheti az Azure-erőforrások jogosult szerepkör-tagjai számára az aktiválást egy későbbi dátumra és időpontra vonatkozóan. Kiválaszthatnak egy adott aktiválási időtartamot is a maximumon belül (a rendszergazdák által konfigurálva).

Ez a cikk azokra a tagokra vonatkozik, akiknek aktiválniuk kell az Azure-erőforrás-szerepkört Privileged Identity Managementban.

## <a name="activate-a-role"></a>Szerepkör aktiválása

Ha egy Azure-erőforrás szerepkört kell igénybe vennie, az aktiválást az Privileged Identity Management **saját szerepkörök** navigációs lehetősége használatával kérheti le.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg **Azure ad Privileged Identity Management**. További információ a Privileged Identity Management csempe irányítópulthoz való hozzáadásáról: a [Privileged Identity Management használatának megkezdése](pim-getting-started.md).

1. Válassza **a saját szerepkörök** lehetőséget.

    ![Saját szerepkörök lap, amely az aktiválható szerepköröket mutatja be](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Válassza ki az **Azure-erőforrások szerepköreit** , és tekintse meg a jogosult Azure-erőforrás-szerepkörök listáját.

    ![Saját szerepkörök – Azure Resource roles lap](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png)

1. Az **Azure erőforrás-szerepkörök** listában keresse meg az aktiválni kívánt szerepkört.

    ![Azure Resource roles – a jogosult szerepkörök listája](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Az aktiválás lap megnyitásához kattintson az **aktiválás** elemre.

    ![A megnyitott aktiválás ablaktábla hatókörrel, kezdési idővel, időtartammal és indoklással](./media/pim-resource-roles-activate-your-roles/azure-role-eligible-activate.png)

1. Ha a szerepkör többtényezős hitelesítést igényel, akkor **a folytatás előtt válassza a személyazonosság ellenőrzése** lehetőséget. Egy munkamenetben csak egyszer kell hitelesítést végezni.

    ![Identitás ellenőrzése az MFA-val a szerepkör aktiválása előtt](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Válassza a **személyazonosság ellenőrzése** lehetőséget, és kövesse az utasításokat a további biztonsági ellenőrzés megadásához.

    ![Biztonsági ellenőrzés, például PIN-kód megadására szolgáló képernyő](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Ha csökkentett hatókört szeretne megadni, válassza a **hatókör** lehetőséget az erőforrás-szűrő ablaktábla megnyitásához.

    A legjobb megoldás, ha csak a szükséges erőforrásokhoz kér hozzáférést. Az erőforrás-szűrő panelen megadhatja azokat az erőforráscsoportokat vagy erőforrásokat, amelyekhez hozzáférésre van szüksége.

    ![Aktiválás – erőforrás-szűrő ablaktábla hatókör megadásához](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. Ha szükséges, egy egyéni aktiválás kezdési idejét kell megadnia. A tag aktiválása a kiválasztott időpont után megtörtént.

1. Az **OK** mezőben adja meg az aktiválási kérelem okát.

    ![Befejezte az aktiválás panel hatókörét, kezdési idejét, időtartamát és okát](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Válassza az **aktiválás** lehetőséget.

    Ha a [szerepkör jóváhagyást kér](pim-resource-roles-approval-workflow.md) az aktiváláshoz, a böngésző jobb felső sarkában megjelenik egy értesítés, amely arról tájékoztatja, hogy a kérés jóváhagyása függőben van.

    ![Aktiválási kérelem függőben lévő jóváhagyási értesítés](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>A kérések állapotának megtekintése

A függőben lévő kérések állapotát az aktiváláshoz tekintheti meg.

1. Nyissa meg Azure AD Privileged Identity Management.

1. Válassza a **saját kérések** lehetőséget az Azure ad-szerepkör és az Azure-erőforrás-szerepkörre vonatkozó kérések listájának megtekintéséhez.

    ![Saját kérések – Azure Resource oldal, amely a függőben lévő kéréseket jeleníti meg](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Görgessen jobbra a **kérelem állapota** oszlop megtekintéséhez.

## <a name="cancel-a-pending-request"></a>Függőben lévő kérelem megszakítása

Ha nem igényel olyan szerepkör aktiválását, amely jóváhagyást igényel, bármikor lemondhatja a függőben lévő kéréseket.

1. Nyissa meg Azure AD Privileged Identity Management.

1. Válassza **a saját kérések** lehetőséget.

1. A megszakítani kívánt szerepkörhöz válassza a **Mégse** hivatkozást.

    A Mégse gombra kattintva a rendszer megszakítja a kérést. A szerepkör újbóli aktiválásához új aktiválási kérést kell benyújtania.

   ![Saját kérések listája a Mégse művelettel kiemelve](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Hibaelhárítás

### <a name="permissions-are-not-granted-after-activating-a-role"></a>A szerepkör aktiválását követően az engedélyek kiosztása nem történik meg

Amikor Privileged Identity Managementban aktiválja a szerepkört, előfordulhat, hogy az aktiválás nem terjed ki azonnal az összes olyan portálra, amelyhez Kiemelt szerepkör szükséges. Néha az is előfordul, hogy még ha a módosítás propagálása meg is történik, a portálon történő webes gyorsítótárazás miatt a módosítás nem lép azonnal életbe. Ha az aktiválás késleltetve van, a következő műveleteket kell végrehajtania.

1. Jelentkezzen ki az Azure Portalról, majd jelentkezzen be újra.
1. A Privileged Identity Managementban ellenőrizze, hogy a szerepkör tagja-e a listához.

## <a name="next-steps"></a>Következő lépések

- [Azure-beli erőforrás-szerepkörök kiterjesztése vagy megújítása Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Azure AD-szerepkörök aktiválása Privileged Identity Management](pim-how-to-activate-role.md)

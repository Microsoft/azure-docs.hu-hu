---
title: Azure-beli erőforrás-szerepkörök hozzáférési felülvizsgálatának létrehozása a PIM-ben – Azure AD | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre hozzáférési felülvizsgálatot az Azure Resource roles szolgáltatásban Azure AD Privileged Identity Management (PIM) alkalmazásban.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 03/09/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4276b48584ecbad91794de58abafd7e3367f6877
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564033"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Azure-beli erőforrás-szerepkörök hozzáférési felülvizsgálatának létrehozása Privileged Identity Management

A Kiemelt Azure-beli erőforrás-szerepkörökhöz való hozzáférésre van szükség az alkalmazottak változásainak időbeli változása miatt. Az elavult szerepkör-hozzárendelésekhez kapcsolódó kockázatok csökkentése érdekében rendszeresen tekintse át a hozzáférést. A Azure Active Directory (Azure AD) Privileged Identity Management (PIM) használatával hozzáférési felülvizsgálatokat hozhat létre az Azure-erőforrás szerepköreihez való jogosultsági szintű hozzáféréshez. Az ismétlődő hozzáférési felülvizsgálatokat is konfigurálhatja, amelyek automatikusan megtörténnek. Ez a cikk azt ismerteti, hogyan hozhat létre egy vagy több hozzáférési felülvizsgálatot.

## <a name="prerequisite-role"></a>Előfeltételként szükséges szerepkör

 Hozzáférési felülvizsgálatok létrehozásához hozzá kell rendelni a [tulajdonos](../../role-based-access-control/built-in-roles.md#owner) vagy a [felhasználói hozzáférés rendszergazdai](../../role-based-access-control/built-in-roles.md#user-access-administrator) Azure-szerepkört az erőforráshoz.

## <a name="open-access-reviews"></a>Hozzáférési felülvizsgálatok megnyitása

1. Jelentkezzen be [Azure Portalra](https://portal.azure.com/) egy olyan felhasználóval, aki hozzá van rendelve az egyik előfeltételi szerepkörhöz.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. A bal oldali menüben válassza az **Azure-erőforrások** lehetőséget.

1. Válassza ki a kezelni kívánt erőforrást, például egy előfizetést.

1. A kezelés területen válassza a **hozzáférési felülvizsgálatok** lehetőséget.

    ![Azure-erőforrások – hozzáférési felülvizsgálatok listája az összes felülvizsgálat állapotának megjelenítéséhez](./media/pim-resource-roles-start-access-review/access-reviews.png)

[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]

## <a name="start-the-access-review"></a>A hozzáférési felülvizsgálat elindítása

Miután megadta a hozzáférési felülvizsgálat beállításait, kattintson a **Start** gombra. A hozzáférési felülvizsgálat megjelenik a listában az állapotának jelzésével.

![Az elindított felülvizsgálati állapotot megjelenítő hozzáférési felülvizsgálatok listája](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

Alapértelmezés szerint az Azure AD egy e-mailt küld a felülvizsgálók számára röviddel a felülvizsgálat elindítása után. Ha úgy dönt, hogy nem szeretné elküldeni az Azure AD-t az e-mail-címre, tájékoztassa a véleményezőket arról, hogy a hozzáférési felülvizsgálat a befejezésre vár. Megtekintheti azokat az utasításokat, amelyekkel [áttekintheti az Azure-erőforrás szerepköreinek elérését](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>A hozzáférési felülvizsgálat kezelése

Nyomon követheti a folyamat előrehaladását, mivel a felülvizsgálók a hozzáférési felülvizsgálat **Áttekintés** lapján tölthetik le az értékeléseket. A címtárban nem módosulnak hozzáférési jogosultságok, amíg a felülvizsgálat be nem [fejeződik](pim-resource-roles-complete-access-review.md).

![A felülvizsgálat részleteit megjelenítő hozzáférési felülvizsgálatok áttekintése lap](./media/pim-resource-roles-start-access-review/access-review-overview.png)

Ha ez egy egyszeri felülvizsgálat, akkor a hozzáférési felülvizsgálati időszak lejárta után, vagy a rendszergazda leállítja a hozzáférési felülvizsgálatot, kövesse az [Azure-erőforrás-szerepkörök hozzáférési felülvizsgálatának befejezése](pim-resource-roles-complete-access-review.md) az eredmények megtekintéséhez és alkalmazásához című témakör lépéseit.  

A hozzáférési felülvizsgálatok sorozatának kezeléséhez navigáljon a hozzáférési felülvizsgálathoz, és keresse meg az ütemezett felülvizsgálatok közelgő eseményeit, és szerkessze a befejezési dátumot, vagy adja hozzá/távolítsa el a felülvizsgálók ennek megfelelően.

A **befejezési beállításokban megadott beállítások** alapján az automatikus alkalmazás a felülvizsgálat befejezési dátuma vagy a felülvizsgálat manuális leállítása után lesz végrehajtva. A felülvizsgálat állapota a **befejezéstől** kezdve a közbenső állapotok, például az **alkalmazás** és végül az állapot **alkalmazásával változik.** A megtagadott felhasználókat, ha vannak ilyenek, néhány percen belül el kell távolítani a szerepkörökből.

## <a name="next-steps"></a>Következő lépések

- [Az Azure-erőforrás szerepköreihez való hozzáférés áttekintése](pim-resource-roles-perform-access-review.md)
- [Azure-erőforrás-szerepkörök hozzáférési felülvizsgálatának befejezése](pim-resource-roles-complete-access-review.md)
- [Azure AD-szerepkörök hozzáférési felülvizsgálatának létrehozása](pim-how-to-start-security-review.md)

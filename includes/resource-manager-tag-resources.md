---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 03/19/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: a00291182059506aeab9cde965fa4cbd5177ecf7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "80132168"
---
Ha a felhasználó nem rendelkezik a címkék alkalmazásához szükséges hozzáféréssel, hozzárendelheti a **tag közreműködői** szerepkört a felhasználóhoz. További információ: [oktatóanyag: felhasználói hozzáférés biztosítása az Azure-erőforrásokhoz a RBAC és a Azure Portal használatával](../articles/role-based-access-control/quickstart-assign-role-user-portal.md).

1. Egy adott erőforrás vagy erőforráscsoport címkéi megtekintéséhez keresse meg a meglévő címkéket az áttekintésben. Ha eddig még nem vett fel címkéket, a lista üres.

   ![Erőforrás vagy erőforráscsoport címkéinak megtekintése](./media/resource-manager-tag-resources/view-tags.png)

1. Címke hozzáadásához válassza a **címkék hozzáadásához kattintson ide**.

1. Adjon meg egy nevet és egy értéket.

   ![Címke hozzáadása](./media/resource-manager-tag-resources/add-tag.png)

1. Szükség szerint folytassa a címkék hozzáadását. Ha elkészült, kattintson a **Mentés** elemre.

   ![Címkék mentése](./media/resource-manager-tag-resources/save-tags.png)

1. A címkék most már megjelennek az áttekintésben.

   ![Címkék megjelenítése](./media/resource-manager-tag-resources/view-new-tags.png)

1. Címke hozzáadásához vagy törléséhez válassza a **módosítás** lehetőséget.

1. Címke törléséhez válassza a Kuka ikont. Ezután válassza a **Mentés** lehetőséget.

   ![Címke törlése](./media/resource-manager-tag-resources/delete-tag.png)

Címkék tömeges kiosztása több erőforráshoz:

1. Az erőforrások listájából válassza ki a címkét hozzárendelni kívánt erőforrások jelölőnégyzetét. Ezután válassza a **címkék kiosztása** elemet.

   ![Több erőforrás kiválasztása](./media/resource-manager-tag-resources/select-multiple-resources.png)

1. Nevek és értékek hozzáadása. Ha elkészült, kattintson a **Mentés** elemre.

   ![Hozzárendelés kiválasztása](./media/resource-manager-tag-resources/select-assign.png)

A címkével rendelkező összes erőforrás megtekintése:

1. A Azure Portal menüben keressen **címkéket**. Válassza ki az elérhető lehetőségek közül.

   ![Keresés címke szerint](./media/resource-manager-tag-resources/find-tags-general.png)

1. Válassza ki a címkét az erőforrások megtekintéséhez.

   ![Címke kiválasztása](./media/resource-manager-tag-resources/select-tag.png)

1. Megjelenik az adott címkével rendelkező összes erőforrás.

   ![Erőforrások megtekintése címke szerint](./media/resource-manager-tag-resources/view-resources-by-tag.png)

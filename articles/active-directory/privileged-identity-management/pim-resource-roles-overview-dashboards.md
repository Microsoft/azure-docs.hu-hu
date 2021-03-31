---
title: Erőforrás-irányítópultok a PIM hozzáférési felülvizsgálatához – Azure AD | Microsoft Docs
description: Ismerteti, hogyan használható az erőforrás-irányítópult a Azure AD Privileged Identity Management (PIM) hozzáférési felülvizsgálatának végrehajtásához.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e95eaa5b0e86a7470fc48edc23b2dbfb47e4b10c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "84743728"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-privileged-identity-management"></a>Egy erőforrás-irányítópult használata hozzáférési felülvizsgálat elvégzésére Privileged Identity Management

Az erőforrás-irányítópult használatával hozzáférési felülvizsgálatot végezhet Privileged Identity Management (PIM) alkalmazásban. A Azure Active Directory (Azure AD) rendszergazdai nézetének irányítópultja három elsődleges összetevővel rendelkezik:

- Az erőforrás-szerepkörök aktiválásának grafikus ábrázolása
- A szerepkör-hozzárendelések hozzárendelési típus szerinti eloszlását megjelenítő diagramok
- Az új szerepkör-hozzárendelésekhez adatokat tartalmazó adatterület

![A felügyeleti nézet irányítópultjának képernyőképe, grafikonok és diagramok megjelenítése](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![A felügyeleti nézet irányítópultjának képernyőképe, amely megjeleníti az adatlistát](media/pim-resource-roles-overview-dashboards/role-settings.png)

Az erőforrás-szerepkörök aktiválásának grafikus ábrázolása az elmúlt hét napban terjed ki. Ezek az információk a kiválasztott erőforrásra vonatkoznak, és megjelenítik a leggyakoribb szerepkörök (tulajdonos, közreműködő, felhasználói hozzáférés rendszergazdája) és az összes szerepkör együttes aktiválásait.

Az aktiválási gráf egyik oldalán két diagram jeleníti meg a szerepkör-hozzárendelések hozzárendelési típus szerinti eloszlását mind a felhasználók, mind a csoportok esetében. A diagram egy szeletének kiválasztásával módosíthatja az értéket százalékra (vagy fordítva).

A diagramok alatt az új szerepkör-hozzárendeléssel rendelkező felhasználók és csoportok száma látható az elmúlt 30 napban, a szerepkörök pedig az összes hozzárendelés szerint csökkenő sorrendben rendezve jelennek meg.

## <a name="next-steps"></a>Következő lépések

- [Azure-beli erőforrás-szerepkörök hozzáférési felülvizsgálatának elindítása Privileged Identity Management](pim-resource-roles-start-access-review.md)

---
title: Az Azure-erőforrás szerepköreire vonatkozó biztonsági riasztások konfigurálása Privileged Identity Management-Azure Active Directoryban | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat biztonsági riasztásokat az Azure-erőforrás szerepköreihez a Azure AD Privileged Identity Management (PIM) szolgáltatásban.
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
ms.openlocfilehash: 0bfca096eb49ee9f1807935de1dac49151cc8ac3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "84743762"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-privileged-identity-management"></a>Azure-beli erőforrás-szerepkörökre vonatkozó biztonsági riasztások konfigurálása Privileged Identity Management

Privileged Identity Management (PIM) riasztásokat állít elő, amikor gyanús vagy nem biztonságos tevékenység van a Azure Active Directory (Azure AD) szervezetében. Riasztások aktiválásakor megjelenik a riasztások lapon.

![Azure-erőforrások – riasztások lap, a riasztás, a kockázati szint és a darabszám](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Riasztások felülvizsgálata

Válassza ki a riasztást, hogy megjelenjen egy jelentés, amely felsorolja a riasztást kiváltó felhasználókat vagy szerepköröket, valamint a Szervizelési útmutatót.

![Riasztási jelentés, amely a legutóbbi vizsgálat idejét, a leírását, a kockázatcsökkentő lépéseket, a típust, a súlyosságot, a biztonsági hatásokat és a következő idő megelőzését mutatja be](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Riasztások

| Riasztás | Súlyosság | Eseményindító | Ajánlás |
| --- | --- | --- | --- |
| **Túl sok tulajdonos van hozzárendelve egy erőforráshoz** |Közepes |Túl sok felhasználó rendelkezik tulajdonosi szerepkörrel. |Tekintse át a listában szereplő felhasználókat, és rendeljen hozzá néhányat a kevésbé Kiemelt szerepkörökhöz. |
| **Túl sok állandó tulajdonos van hozzárendelve egy erőforráshoz** |Közepes |Túl sok felhasználó van hozzá véglegesen rendelve egy szerepkörhöz. |Tekintse át a listában szereplő felhasználókat, és rendelje újra a szerepkör-használathoz szükséges aktiválást. |
| **Ismétlődő szerepkör létrehozva** |Közepes |Több szerepkör azonos feltételekkel rendelkezik. |Csak az egyik szerepkört használja. |

### <a name="severity"></a>Súlyosság

- **Magas**: a szabályzat megsértése miatt azonnali műveletre van szükség. 
- **Közepes**: nincs szükség azonnali műveletre, de az esetleges szabályzat megsértését jelzi.
- **Alacsony**: nincs szükség azonnali műveletre, de az előnyben részesített szabályzatok módosítására is javaslatot tesz.

## <a name="configure-security-alert-settings"></a>Biztonsági riasztás beállításainak konfigurálása

A riasztások lapon lépjen a **Beállítások** elemre.

![Riasztások lap Kiemelt beállításokkal](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

A különböző riasztások beállításainak testreszabásával dolgozhat a környezettel és a biztonsági célokkal.

![Riasztás beállítása a beállítások engedélyezéséhez és konfigurálásához](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>Következő lépések

- [Az Azure erőforrás-szerepkör beállításainak konfigurálása Privileged Identity Management](pim-resource-roles-configure-role-settings.md)

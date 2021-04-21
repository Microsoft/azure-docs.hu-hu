---
title: Foglalásvásárló megkeresi a Azure Monitor naplókban
description: Ez a cikk segít megtalálni a foglalás vásárlóját a naplókban Azure Monitor információkkal.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: troubleshooting
ms.date: 03/13/2021
ms.author: banders
ms.openlocfilehash: 965e90eed0690d57b6ad3cf3a1543b1329c0fe74
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773371"
---
# <a name="find-a-reservation-purchaser-from-azure-logs"></a>Foglalás megvásárlója az Azure-naplókban

Ez a cikk segít megtalálni a foglalás vásárlóját a címtárnaplókban található információkkal. A foglalásvásárlásokat Azure Monitor felhasználók e-mail-címének a naplói.

## <a name="find-the-purchaser"></a>A vásárló megkeresi

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Lépjen a   >  **Tevékenységnapló-tevékenység figyelése**  >  **lapra.**  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" alt-text="Képernyőkép a Tevékenységnapló – Tevékenységre való navigálásról." lightbox="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" :::
1. Válassza a **Címtártevékenység lehetőséget.** Ha megjelenik egy üzenet, amely szerint Engedélyre van [](../../role-based-access-control/elevate-access-global-admin.md) szüksége a címtárszintű naplók megtekintéséhez, kattintson a hivatkozásra, hogy megtudja, hogyan kérheti le az engedélyeket.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" alt-text="Képernyőkép a címtártevékenységről a napló megtekintéséhez szükséges engedély nélkül." lightbox="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" :::
1. Ha rendelkezik engedéllyel, szűrje a **bérlői erőforrás-szolgáltatót** a **Microsoft.Capacity mezőben.** A kiválasztott időtartományra vonatkozó összes foglalással kapcsolatos eseményt meg kell látnia. Ha szükséges, módosítsa az időtartományt.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" alt-text="Képernyőkép a foglalást megvásároló felhasználóról." lightbox="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" :::
    Ha szükséges, előfordulhat, hogy az által kezdeményezett **esemény kiválasztásához** oszlopok szerkesztése **szükséges.**
   A foglalást megvásároló felhasználó megjelenik a **által kezdeményezett esemény alatt.**

## <a name="next-steps"></a>Következő lépések

- Ha szükséges, a számlázási rendszergazdák [átvehetik a foglalás tulajdonjogát.](view-reservations.md#how-billing-administrators-can-view-or-manage-reservations)

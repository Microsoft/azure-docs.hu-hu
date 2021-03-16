---
title: Foglalási vásárló keresése Azure Monitor naplókból
description: Ez a cikk segít megkeresni a foglalások beszerzését Azure Monitor naplókból származó információkkal.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: troubleshooting
ms.date: 03/13/2021
ms.author: banders
ms.openlocfilehash: eedb5e7a55b50a353fd16498500b891e289e61e5
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/15/2021
ms.locfileid: "103477054"
---
# <a name="find-a-reservation-purchaser-from-azure-logs"></a>Foglalási vásárló keresése az Azure-naplókból

Ebből a cikkből megtudhatja, hogyan keresheti meg a foglalások beszerzését a címtár naplóiból származó információkkal. A Azure Monitor Directory naplói a foglalások beszerzését biztosító felhasználók e-mail-azonosítóit jelenítik meg.

## <a name="find-the-purchaser"></a>A vásárló megkeresése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Navigáljon a  >  **tevékenység naplójának** figyelése  >  **tevékenységhez**.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" alt-text="Képernyőfelvétel: Navigálás a tevékenység naplójában – tevékenység." lightbox="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" :::
1. Válassza a **címtár tevékenység** lehetőséget. Ha megjelenik egy üzenet, amely jogosult *a címtár-szintű naplók megtekintésére*, válassza a [hivatkozást](../../role-based-access-control/elevate-access-global-admin.md) , amelyből megtudhatja, hogyan szerezhet be engedélyeket.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" alt-text="Képernyőfelvétel: a címtár tevékenységének engedélye a napló megtekintéséhez." lightbox="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" :::
1. Ha rendelkezik a jogosultsággal, szűrje a **bérlői erőforrás-szolgáltatót** a **Microsoft. Capacity kapacitással**. A kiválasztott időtartományhoz tartozó összes foglalással kapcsolatos eseményt látnia kell. Ha szükséges, módosítsa az időtartományt.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" alt-text="A foglalást megvásárló felhasználót bemutató képernyőkép." lightbox="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" :::
    Szükség esetén előfordulhat, hogy **módosítania kell az oszlopokat** az **által kezdeményezett esemény** kiválasztásához.
   A foglalás megvásárlását kezdeményező felhasználó a **által kezdeményezett esemény** alatt jelenik meg.

## <a name="next-steps"></a>Következő lépések

- Ha szükséges, a számlázási rendszergazdák elérhetik a [foglalások tulajdonosát](view-reservations.md#how-billing-administrators-view-or-manage-reservations).
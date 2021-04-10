---
title: Geo-biztonsági mentések letiltása
description: Útmutató a Geo-biztonsági mentések letiltásához egy dedikált SQL-készlethez (korábban SQL DW) az Azure szinapszis Analyticsben
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 01/06/2021
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 395d5f0697138155b0bb0c629461aada9e9c18c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98739115"
---
# <a name="disable-geo-backups-for-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Geo-biztonsági mentések letiltása egy dedikált SQL-készlethez (korábban SQL DW) az Azure szinapszis Analyticsben

Ebből a cikkből megtudhatja, hogyan tilthatja le a dedikált SQL-készlet (korábban SQL DW) Azure Portal geo-biztonsági mentéseit.

## <a name="disable-geo-backups-through-azure-portal"></a>Geo-biztonsági mentések letiltása Azure Portal

A következő lépések végrehajtásával letilthatja a földrajzi biztonsági mentéseket a dedikált SQL-készlethez (korábban SQL DW):

> [!NOTE]
> Ha letiltja a Geo-biztonsági mentéseket, többé nem fogja tudni visszaállítani a dedikált SQL-készletet (korábbi nevén SQL DW) egy másik Azure-régióba. 
>

1. Jelentkezzen be [Azure Portal](https://portal.azure.com/) -fiókjába.
1. Válassza ki a dedikált SQL-készlet (korábban SQL DW) erőforrást, amelyet le szeretne tiltani a Geo-biztonsági mentések számára. 
1. A bal oldali navigációs panel **Beállítások** területén válassza a **geo-biztonsági mentési házirend** elemet.

   ![Geo-biztonsági mentés letiltása](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-1.png)

1. A Geo-biztonsági mentések letiltásához válassza a **Letiltva** lehetőséget. 

   ![Geo-biztonsági mentés letiltva](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-2.png)

1. Válassza a *Mentés* lehetőséget a beállítások mentésének biztosításához. 

   ![Geo-biztonsági mentési beállítások mentése](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-3.png)

## <a name="next-steps"></a>Következő lépések

- [Meglévő dedikált SQL-készlet visszaállítása (korábban SQL DW)](sql-data-warehouse-restore-active-paused-dw.md)
- [Törölt dedikált SQL-készlet visszaállítása (korábban SQL DW)](sql-data-warehouse-restore-deleted-dw.md)

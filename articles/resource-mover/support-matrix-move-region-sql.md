---
title: Az Azure SQL-erőforrások régiók közötti áthelyezésének támogatása az Azure-erőforrás-mozgató szolgáltatással.
description: Tekintse át az Azure SQL-erőforrások régiók közötti áthelyezésének támogatását az Azure-erőforrás-mozgató szolgáltatással.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: raynew
ms.openlocfilehash: 22a7738c2d4d3cc02c03c233e0821f07b459dd94
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96452083"
---
# <a name="support-for-moving-azure-sql-resources-between-azure-regions"></a>Azure SQL-erőforrások Azure-régiók közötti áthelyezésének támogatása

Ez a cikk az Azure SQL-erőforrások Azure-régiók közötti áthelyezésének támogatását és előfeltételeit foglalja össze az Azure-beli erőforrás-mozgató szolgáltatással.

## <a name="requirements"></a>Követelmények

A követelményeket a következő táblázat foglalja össze.

**Szolgáltatás** | **Támogatott/nem támogatott** | **Részletek**
--- | --- | ---
**Azure SQL Database nagy kapacitású** | Nem támogatott | Az adatbázisok nem helyezhetők át az Azure SQL nagy kapacitású szolgáltatási szintjébe az erőforrás-mozgató szolgáltatással.
**Zónaredundancia** | Támogatott |  Támogatott áthelyezési beállítások:<br/><br/> – A zóna redundanciát támogató régiók között.<br/><br/> – A zónák redundanciát nem támogató régiók között.<br/><br/> – Egy olyan régió között, amely támogatja a zóna-redundancia-t egy olyan régióban, amely nem támogatja a zóna-redundanciát.<br/><br/> – Egy olyan régió között, amely nem támogatja a zóna-redundancia támogatását egy olyan régióban, amely támogatja a zóna-redundanciát. 
**Adatszinkronizálás** | Központ/szinkronizálási adatbázis: nem támogatott<br/><br/> Szinkronizálási tag: támogatott. | Szinkronizálási tag áthelyezésekor be kell állítania az adatszinkronizálást az új céladatbázis számára.
**Meglévő geo-replikáció** | Támogatott | A meglévő földrajzi replikákat a rendszer a célként megadott régió új elsődleges részére rendeli hozzá.<br/><br/> A beültetést az áthelyezés után inicializálni kell. [További információ](../azure-sql/database/active-geo-replication-configure-portal.md)
**Transzparens adattitkosítás (TDE) Bring Your Own Key (BYOK)** | Támogatott | [További](../key-vault/general/move-region.md) információ a Key vaultok régiók közötti áthelyezéséről.
**TDE szolgáltatás által felügyelt kulccsal** | Támogatott. |  [További](../key-vault/general/move-region.md) információ a Key vaultok régiók közötti áthelyezéséről.
**Dinamikus adatmaszkolási szabályok** | Támogatott. | A rendszer automatikusan átmásolja a szabályokat a célként megadott régióba az áthelyezés részeként. [További információk](../azure-sql/database/dynamic-data-masking-configure-portal.md).
**Advanced Data Security** | Nem támogatott. | Áthidaló megoldás: állítsa be a SQL Server szinten a célként megadott régióban. [További információk](../azure-sql/database/azure-defender-for-sql.md).
**Tűzfalszabályok** | Nem támogatott. | Megkerülő megoldás: tűzfalszabályok beállítása a célként megadott régióban lévő SQL Serverhoz. Az adatbázis-szintű tűzfalszabályok a forráskiszolgálóról a célkiszolgálóra másolódnak. [További információk](../azure-sql/database/firewall-create-server-level-portal-quickstart.md).
**Naplózási házirendek** | Nem támogatott. | Az áthelyezés után a házirendek visszaállnak az alapértelmezett értékre. [Útmutató](../azure-sql/database/auditing-overview.md) az alaphelyzetbe állításához.
**Biztonsági mentés megőrzése** | Támogatott. | A forrásadatbázis biztonsági mentési adatmegőrzési szabályzatait a rendszer átadja a céladatbázis számára. [Megtudhatja](../azure-sql/database/long-term-backup-retention-configure.md) , hogyan módosíthatja a beállításokat az áthelyezés után.
**Automatikus hangolás** | Nem támogatott. | Áthidaló megoldás: az áthelyezés után állítsa be az automatikus finomhangolási beállításokat. [További információk](../azure-sql/database/automatic-tuning-enable.md).
**Adatbázis-riasztások** | Nem támogatott. | Megkerülő megoldás: az áthelyezés után riasztásokat állíthat be. [További információk](../azure-sql/database/alerts-insights-configure-portal.md).
**Azure SQL Server stretch Database** | Nem támogatott | Az SQL Server stretch Database-adatbázisok nem helyezhetők át az erőforrás-mozgatóval.
**Azure Synapse Analytics** | Nem támogatott | Az Azure szinapszis Analytics nem helyezhető át az erőforrás-mozgatóval.
## <a name="next-steps"></a>Következő lépések

Próbálja ki az [Azure SQL-erőforrásokat](tutorial-move-region-sql.md) egy másik régióba az erőforrás-mozgató használatával.
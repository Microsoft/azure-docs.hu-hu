---
title: A SQL Server Stretch Database Service összes témaköre | Microsoft Docs
description: A (z) SQL Server Stretch Database nevű Azure-szolgáltatás összes témakörének táblázata https://azure.microsoft.com/documentation/articles/ , címe és leírása.
services: sql-server-stretch-database
documentationcenter: ''
ms.assetid: b1718024-84d6-4f5c-a912-3a99edb3f632
ms.service: sql-server-stretch-database
ms.workload: sql-server-stretch-database
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/05/2016
author: blazem-msft
ms.author: blazem
ms.reviewer: jroth
manager: jroth
ms.openlocfilehash: 15b4a90a4e082cbe8fc0f2da1a87b6860cfb66a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95024211"
---
# <a name="all-topics-for-azure-sql-server-stretch-database-service"></a>Az Azure SQL Server Stretch Database Service összes témaköre
Ez a témakör felsorolja az Azure **SQL Server stretch Database** szolgáltatásával kapcsolatos összes témakört. A kulcsszavakat a **CTRL + F** billentyűkombinációval keresheti meg, és megkeresheti az aktuális érdeklődéshez kapcsolódó témaköröket.

## <a name="get-started"></a>Bevezetés
| Cím | Leírás |
|:--- |:--- |
|[Stretch Database adatbázisok és táblák azonosítása Stretch Database Advisor futtatásával](/sql/sql-server/stretch-database/stretch-database-databases-and-tables-stretch-database-advisor) |Megtudhatja, hogyan azonosíthatók a Stretch Database számára jelölt adatbázisok és táblák. |
|[Stretch Database korlátozásai](/sql/sql-server/stretch-database/limitations-for-stretch-database) |További információ a Stretch Database korlátozásáról. |
|[Stretch Database áttekintése](/sql/sql-server/stretch-database/stretch-database) |Ismerje meg, hogy a Stretch Database hogyan telepíti át a ritkán használt adatokat átlátható módon és biztonságosan a Microsoft Azure felhőbe. |
|[Első lépések: az Enable Database for Stretch (A Stretch Database használatának engedélyezése az adatbázison) varázsló futtatása](/sql/sql-server/stretch-database/get-started-by-running-the-enable-database-for-stretch-wizard) |Ebből a cikkből megtanulhatja, hogyan konfigurálhatja adatbázisait a Stretch Database-zel való használatra az Enable Database for Stretch (A Stretch Database használatának engedélyezése az adatbázison) varázsló futtatásával. |

## <a name="enable"></a>Engedélyezés
| Cím | Leírás |
|:--- |:--- |
|[Enable Stretch Database for a database (A Stretch Database használatának engedélyezése adatbázisokon)](/sql/sql-server/stretch-database/enable-stretch-database-for-a-database) |Megtudhatja, hogyan konfigurálhat egy adatbázist a Stretch Databasehoz. |
|[Enable Stretch Database for a table (A Stretch Database használatának engedélyezése táblákon)](/sql/sql-server/stretch-database/enable-stretch-database-for-a-table) |Megtudhatja, hogyan konfigurálhat egy táblát a Stretch Databasehoz. |
|[Az áttelepíteni kívánt sorok kiválasztása a Filter függvény használatával (Stretch Database)](/sql/sql-server/stretch-database/select-rows-to-migrate-by-using-a-filter-function-stretch-database) |Megtudhatja, hogyan választhatja ki az áttelepíteni kívánt sorokat egy Filter függvény használatával. |

## <a name="manage"></a>Kezelés
| Cím | Leírás |
|:--- |:--- |
|[A Stretch Database letiltása és a távoli adatvisszaállítás](/sql/sql-server/stretch-database/disable-stretch-database-and-bring-back-remote-data) |Megtudhatja, hogyan tilthatja le a táblák Stretch Databaseét, és szükség esetén visszaállíthatja a távoli adatvesztést. |
|[Manage and troubleshoot Stretch Database (A Stretch Database felügyelete és hibáinak elhárítása)](/sql/sql-server/stretch-database/manage-and-troubleshoot-stretch-database) |Megtudhatja, hogyan kezelheti és elháríthatja a Stretch Database. |
|[Az adatáttelepítés figyelése és hibakeresése (Stretch Database)](/sql/sql-server/stretch-database/monitor-and-troubleshoot-data-migration-stretch-database) |Megtudhatja, hogyan figyelheti az adatáttelepítés állapotát. |
|[Az adatáttelepítés szüneteltetése és folytatása (Stretch Database)](/sql/sql-server/stretch-database/pause-and-resume-data-migration-stretch-database) |Megtudhatja, hogyan szüneteltetheti vagy folytathatja az adatáttelepítést az Azure-ba. |

## <a name="security"></a>Biztonság
| Cím | Leírás |
|:--- |:--- |
|[Transzparens adattitkosítás (TDE) engedélyezése az Azure-beli Stretch Databasehoz](sql-server-stretch-database-encryption-tde.md) |Transzparens adattitkosítás (TDE) engedélyezése az Azure-beli SQL Server Stretch Databasehoz |
|[Transzparens adattitkosítás (TDE) engedélyezése az Azure-beli Stretch Database (Transact-SQL)](sql-server-stretch-database-tde-tsql.md) |Transzparens adattitkosítás (TDE) engedélyezése SQL Server Stretch Database Azure-beli TSQL |

## <a name="backup-and-recovery"></a>Biztonsági másolat és helyreállítás
| Cím | Leírás |
|:--- |:--- |
|[Backup Stretch-enabled databases (A Stretch szolgáltatást használó adatbázisok biztonsági mentése)](/sql/sql-server/stretch-database/backup-stretch-enabled-databases-stretch-database) |Ismerje meg, hogyan készíthet biztonsági másolatot a stretch \- enabled adatbázisairól. |
|[Stretch-kompatibilis adatbázisok visszaállítása](/sql/sql-server/stretch-database/restore-stretch-enabled-databases-stretch-database) |Ismerje meg, hogyan állíthatja vissza a stretch \- enabled-adatbázisokat. |
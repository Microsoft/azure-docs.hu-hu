---
title: Példák az Azure CLI parancsfájlokra
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Azure CLI-szkriptek példák a Azure SQL Database és az Azure SQL felügyelt példányának létrehozására és kezelésére
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: overview-samples, mvc, sqldbrb=2, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: 439167f29bb53d4a6e90b95826faa56e3c3170da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94563358"
---
# <a name="azure-cli-samples-for-azure-sql-database-and-sql-managed-instance"></a>Azure CLI-minták Azure SQL Database és SQL felügyelt példányhoz 
 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Az <a href="/cli/azure">Azure CLI</a>használatával konfigurálhatja a Azure SQL Database és az SQL felügyelt példányát.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Az oktatóanyaghoz az Azure CLI 2,0-es vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

# <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

Az alábbi táblázat az Azure CLI parancsfájlokra mutató hivatkozásokat tartalmaz, amelyek a Azure SQL Database egyetlen és készletezett adatbázisait kezelik. 

|Terület|Leírás|
|---|---|
|**Adatbázisok létrehozása a Azure SQL Databaseban**||
| [Önálló adatbázis létrehozása és egy tűzfalszabály konfigurálása](scripts/create-and-configure-database-cli.md) | Létrehoz egy SQL Database, és konfigurál egy kiszolgálói szintű tűzfalszabály-szabályt. |
| [Rugalmas készletek létrehozása és rugalmas készletbe helyezett adatbázisok áthelyezése](scripts/move-database-between-elastic-pools-cli.md) | Rugalmas készleteket hoz létre, áthelyezi a készletezett adatbázisokat, és megváltoztatja a számítási méreteket. |
|**Adatbázisok méretezése Azure SQL Database**||
| [Önálló adatbázis méretezése](scripts/monitor-and-scale-database-cli.md) | Az adatbázis méretére vonatkozó információk lekérdezése után a SQL Database egy másik számítási méretre méretezi az adatbázist. |
| [Rugalmas készlet méretezése](scripts/scale-pool-cli.md) | Egy rugalmas SQL-készlet méretezése más számítási méretre. |
|**Georeplikáció és feladatátvétel konfigurálása**||
| [Egyetlen adatbázis hozzáadása egy feladatátvételi csoporthoz](scripts/add-database-to-failover-group-cli.md)| Létrehoz egy adatbázist és egy feladatátvételi csoportot, hozzáadja az adatbázist a feladatátvételi csoporthoz, majd teszteli a feladatátvételt a másodlagos kiszolgálóra. |
| [Feladatátvevő csoport konfigurálása rugalmas készlethez](../../sql-database/scripts/sql-database-add-elastic-pool-to-failover-group-cli.md) | Létrehoz egy adatbázist, hozzáadja azt egy rugalmas készlethez, hozzáadja a rugalmas készletet a feladatátvételi csoporthoz, majd teszteli a feladatátvételt a másodlagos kiszolgálóra. |
| [Önálló adatbázis konfigurálása és feladatátvétele az aktív geo-replikáció használatával](../../sql-database/scripts/sql-database-setup-geodr-and-failover-database-cli.md)| Az aktív geo-replikálást konfigurálja Azure SQL Database adatbázisához, és feladatátvételt hajt végre a másodlagos replikára. |
| [Készletezett adatbázis konfigurálása és feladatátvétele az aktív geo-replikáció használatával](../../sql-database/scripts/sql-database-setup-geodr-and-failover-pool-cli.md)| Egy rugalmas készletben lévő adatbázis aktív földrajzi replikálását konfigurálja, majd a feladatátvételt a másodlagos replikára. |
| **Naplózás és fenyegetésészlelés** |
| [Naplózás és fenyegetésészlelés konfigurálása](../../sql-database/scripts/sql-database-auditing-and-threat-detection-cli.md)| A naplózási és veszélyforrás-észlelési házirendeket konfigurálja Azure SQL Database-adatbázishoz. |
| **Adatbázis biztonsági mentése, visszaállítása, másolása és importálása**||
| [Adatbázis biztonsági mentése](../../sql-database/scripts/sql-database-backup-database-cli.md)| Biztonsági másolatot készít egy adatbázisról SQL Database egy Azure Storage biztonsági mentéshez. |
| [Adatbázis visszaállítása](../../sql-database/scripts/sql-database-restore-database-cli.md)| Visszaállítja a SQL Database található adatbázist egy geo-redundáns biztonsági mentésből, és visszaállítja a törölt adatbázist a legújabb biztonsági mentésre. |
| [Adatbázis másolása új kiszolgálóra](../../sql-database/scripts/sql-database-copy-database-to-new-server-cli.md) | Egy meglévő adatbázis másolatát hozza létre SQL Database egy új kiszolgálón. |
| [Adatbázis importálása BACPAC-fájlból](../../sql-database/scripts/sql-database-import-from-bacpac-cli.md)| Adatbázis importálása SQL Database BACPAC-fájlból. |
|||

További információ az [Egyadatbázisos Azure CLI API-](single-database-manage.md#the-azure-cli)ról.

# <a name="azure-sql-managed-instance"></a>[Felügyelt Azure SQL-példány](#tab/managed-instance)

Az alábbi táblázat a felügyelt Azure SQL-példányokra mutató hivatkozásokat tartalmaz az Azure CLI parancsfájlokra.

|Terület|Leírás|
|---|---|
| **SQL felügyelt példány létrehozása**||
| [SQL felügyelt példány létrehozása](../../sql-database/scripts/sql-database-create-configure-managed-instance-cli.md)| Létrehoz egy SQL felügyelt példányt. |
| **Transzparens adattitkosítás konfigurálása (TDE)**||
| [Felügyelt SQL-példányok transzparens adattitkosításának kezelése Azure Key Vault használatával](../../sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-cli.md)| A transzparens adattitkosítás (TDE) konfigurálása az SQL felügyelt példányában Azure Key Vault különböző főbb forgatókönyvek használatával. |
|**Feladatátvételi csoport konfigurálása**||
| [Feladatátvételi csoport konfigurálása SQL felügyelt példányhoz](../../sql-database/scripts/sql-database-add-managed-instance-to-failover-group-cli.md) | Két példányban hozza létre a felügyelt SQL-példányokat, hozzáadja azokat egy feladatátvételi csoporthoz, majd a felügyelt elsődleges SQL-példányról a másodlagos SQL felügyelt példányra ellenőrzi a feladatátvételt. |
|||

További példák a felügyelt SQL-példányokra: adatbázis [létrehozása](/archive/blogs/sqlserverstorageengine/create-azure-sql-managed-instance-using-azure-cli), [frissítése](/archive/blogs/sqlserverstorageengine/modify-azure-sql-database-managed-instance-using-azure-cli), [áthelyezése](/archive/blogs/sqlserverstorageengine/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance)és parancsfájlok [használata](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) .

További információ az [SQL felügyelt példány Azure CLI API](../managed-instance/api-references-create-manage-instance.md#azure-cli-create-and-configure-managed-instances)-ról.

---
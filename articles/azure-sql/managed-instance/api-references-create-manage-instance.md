---
title: Felügyeleti API-referenciák az Azure SQL felügyelt példányaihoz
description: Tudnivalók a felügyelt Azure SQL-példányok felügyelt példányainak létrehozásáról és konfigurálásáról.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: devx-track-azurecli
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: cf100861705bf1654b7206445c884b5fe315b06d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792633"
---
# <a name="managed-api-reference-for-azure-sql-managed-instance"></a>Az Azure SQL Managed Instance felügyelt API-referenciája
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Az Azure SQL felügyelt példányainak felügyelt példányait a Azure Portal, a PowerShell, az Azure CLI, az REST API és a Transact-SQL használatával hozhatja létre és konfigurálhatja. Ebben a cikkben a felügyelt példányok létrehozásához és konfigurálásához használható függvények és API-k áttekintését találhatja.

## <a name="azure-portal-create-a-managed-instance"></a>Azure Portal: felügyelt példány létrehozása

A felügyelt példányok létrehozásának módját bemutató rövid útmutató [: a felügyelt példány létrehozása](instance-create-quickstart.md).

## <a name="powershell-create-and-configure-managed-instances"></a>PowerShell: felügyelt példányok létrehozása és konfigurálása

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Az az modul és a AzureRM modulok parancsainak argumentumai lényegében azonosak.

Felügyelt példányok Azure PowerShell használatával történő létrehozásához és kezeléséhez használja a következő PowerShell-parancsmagokat. Ha telepítenie vagy frissítenie kell a PowerShellt, tekintse meg [a Azure PowerShell modul telepítését](/powershell/azure/install-az-ps)ismertető témakört.

> [!TIP]
> A PowerShell-parancsfájlok esetében lásd [: gyors útmutató parancsfájl: felügyelt példány létrehozása PowerShell-függvénytár használatával](/archive/blogs/sqlserverstorageengine/quick-start-script-create-azure-sql-managed-instance-using-powershell).

| Parancsmag | Leírás |
| --- | --- |
|[Új – AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)|Létrehoz egy felügyelt példányt. |
|[Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)|Egy felügyelt példányra vonatkozó adatokat ad vissza.|
|[Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)|Felügyelt példány tulajdonságainak beállítása.|
|[Remove-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance)|Eltávolít egy felügyelt példányt.|
|[Get-AzSqlInstanceOperation](/powershell/module/az.sql/get-azsqlinstanceoperation)|Lekéri a felügyelt példányon vagy adott műveleten végrehajtott felügyeleti műveletek listáját.|
|[Leállítás – AzSqlInstanceOperation](/powershell/module/az.sql/stop-azsqlinstanceoperation)|A felügyelt példányon végrehajtott adott felügyeleti művelet megszakítása.|
|[Új – AzSqlInstanceDatabase](/powershell/module/az.sql/new-azsqlinstancedatabase)|Létrehoz egy SQL-alapú felügyelt példány-adatbázist.|
|[Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase)|Egy SQL felügyelt példány adatbázisával kapcsolatos információkat ad vissza.|
|[Remove-AzSqlInstanceDatabase](/powershell/module/az.sql/remove-azsqlinstancedatabase)|Eltávolít egy SQL-alapú felügyelt példány-adatbázist.|
|[Visszaállítás – AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase)|Visszaállítja a felügyelt SQL-példányok adatbázisát.|

## <a name="azure-cli-create-and-configure-managed-instances"></a>Azure CLI: felügyelt példányok létrehozása és konfigurálása

Felügyelt példányok [Azure CLI](/cli/azure)-vel történő létrehozásához és konfigurálásához használja az alábbi [Azure CLI-parancsokat az SQL felügyelt példányához](/cli/azure/sql/mi). [Azure Cloud Shell](../../cloud-shell/overview.md) használatával futtathatja a CLI-t a böngészőben, vagy [telepítheti](/cli/azure/install-azure-cli) MacOS, Linux vagy Windows rendszerre.

> [!TIP]
> Azure CLI-gyors útmutató: az [SQL felügyelt példány használata az Azure CLI használatával](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

| Parancsmag | Leírás |
| --- | --- |
|[az SQL mi Create](/cli/azure/sql/mi#az-sql-mi-create) |Létrehoz egy felügyelt példányt.|
|[az SQL mi List](/cli/azure/sql/mi#az-sql-mi-list)|Felsorolja az elérhető felügyelt példányokat.|
|[az SQL mi show](/cli/azure/sql/mi#az-sql-mi-show)|A felügyelt példány részleteinek beolvasása.|
|[az SQL mi Update](/cli/azure/sql/mi#az-sql-mi-update)|Felügyelt példány frissítése.|
|[az SQL mi delete](/cli/azure/sql/mi#az-sql-mi-delete)|Eltávolít egy felügyelt példányt.|
|[az SQL mi op List](/cli/azure/sql/mi/op#az_sql_mi_op_list)|Lekéri a felügyelt példányon végrehajtott felügyeleti műveletek listáját.|
|[az SQL mi op show](/cli/azure/sql/mi/op#az_sql_mi_op_show)|A felügyelt példányon végrehajtott adott felügyeleti művelet beolvasása.|
|[az SQL mi op Cancel](/cli/azure/sql/mi/op#az_sql_mi_op_cancel)|A felügyelt példányon végrehajtott adott felügyeleti művelet megszakítása.|
|[az SQL MidB Create](/cli/azure/sql/midb#az-sql-midb-create) |Létrehoz egy felügyelt adatbázist.|
|[az SQL MidB List](/cli/azure/sql/midb#az-sql-midb-list)|Felsorolja az elérhető felügyelt adatbázisokat.|
|[az SQL MidB Restore](/cli/azure/sql/midb#az-sql-midb-restore)|Visszaállítja a felügyelt adatbázist.|
|[az SQL MidB delete](/cli/azure/sql/midb#az-sql-midb-delete)|Eltávolít egy felügyelt adatbázist.|

## <a name="transact-sql-create-and-configure-instance-databases"></a>Transact-SQL: példány-adatbázisok létrehozása és konfigurálása

A felügyelt példány létrehozása után hozzon létre és konfiguráljon példány-adatbázisokat a következő T-SQL-parancsok használatával. Ezeket a parancsokat a Azure Portal, a [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), a [Azure Data Studio](/sql/azure-data-studio/what-is), a [Visual Studio Code](https://code.visualstudio.com/docs)vagy bármely más program használatával lehet kiadni, amely csatlakozhat egy kiszolgálóhoz, és átadhatja a Transact-SQL-parancsokat.

> [!TIP]
> A Microsoft Windows SQL Server Management Studio használatával felügyelt példányok konfigurálását és csatlakoztatását bemutató gyors útmutató: gyors üzembe helyezés az Azure [virtuális gép konfigurálása az Azure SQL felügyelt példányhoz való kapcsolódáshoz](connect-vm-instance-configure.md) és rövid útmutató [: pont – hely kapcsolat konfigurálása az Azure SQL felügyelt példányához a helyszínen](point-to-site-p2s-configure.md).

> [!IMPORTANT]
> Felügyelt példány nem hozható létre és nem törölhető a Transact-SQL használatával.

| Parancs | Leírás |
| --- | --- |
|[CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?preserve-view=true&view=azuresqldb-mi-current)|Létrehoz egy új példány-adatbázist a felügyelt SQL-példányban. Új adatbázis létrehozásához csatlakoznia kell a Master adatbázishoz.|
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-mi-current) |Módosítja egy példány-adatbázist az SQL felügyelt példányában.|

## <a name="rest-api-create-and-configure-managed-instances"></a>REST API: felügyelt példányok létrehozása és konfigurálása

Felügyelt példányok létrehozásához és konfigurálásához használja ezeket a REST API kérelmeket.

| Parancs | Leírás |
| --- | --- |
|[Felügyelt példányok – létrehozás vagy frissítés](/rest/api/sql/managedinstances/createorupdate)|Felügyelt példány létrehozása vagy frissítése.|
|[Felügyelt példányok – törlés](/rest/api/sql/managedinstances/delete)|Felügyelt példány törlése.|
|[Felügyelt példányok – Get](/rest/api/sql/managedinstances/get)|Felügyelt példány beolvasása.|
|[Felügyelt példányok – lista](/rest/api/sql/managedinstances/list)|Egy előfizetésben lévő felügyelt példányok listáját adja vissza.|
|[Felügyelt példányok – erőforráslista szerint csoportosítva](/rest/api/sql/managedinstances/listbyresourcegroup)|Egy erőforráscsoport felügyelt példányainak listáját adja vissza.|
|[Felügyelt példányok – frissítés](/rest/api/sql/managedinstances/update)|Felügyelt példány frissítése.|
|[Felügyelt példányok műveletei – lista felügyelt példány alapján](/rest/api/sql/managedinstanceoperations/listbymanagedinstance)|Lekéri a felügyelt példányon végrehajtott felügyeleti műveletek listáját.|
|[Felügyelt példányok műveletei – Get](/rest/api/sql/managedinstanceoperations/get)|A felügyelt példányon végrehajtott adott felügyeleti művelet beolvasása.|
|[Felügyelt példányok műveletei – Mégse](/rest/api/sql/managedinstanceoperations/cancel)|A felügyelt példányon végrehajtott adott felügyeleti művelet megszakítása.|

## <a name="next-steps"></a>Következő lépések

- A SQL Server-adatbázisok Azure-ba történő áttelepítésével kapcsolatos információkért lásd: [áttelepítés Azure SQL Databasere](../database/migrate-to-database-from-sql-server.md).
- A támogatott funkciókkal kapcsolatos tudnivalókat lásd: [Funkciók](../database/features-comparison.md).
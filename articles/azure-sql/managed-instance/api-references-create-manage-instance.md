---
title: Alkalmazáskezelés API referencia a Azure SQL Managed Instance
description: Útmutató felügyelt példányok létrehozásához és Azure SQL Managed Instance.
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
ms.openlocfilehash: 148b24aea42072f1901c76c7a09a126340ef9951
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784370"
---
# <a name="managed-api-reference-for-azure-sql-managed-instance"></a>Az Azure SQL Managed Instance felügyelt API-referenciája
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Felügyelt példányokat hozhat létre és konfigurálhat a Azure SQL Managed Instance a Azure Portal, a PowerShell, az Azure CLI, a REST API és a Transact-SQL használatával. Ebben a cikkben áttekintheti a felügyelt példányok létrehozásához és konfigurálához használható függvényeket és API-t.

## <a name="azure-portal-create-a-managed-instance"></a>Azure Portal: Felügyelt példány létrehozása

A felügyelt példányok létrehozásáról rövid útmutatóért lásd: Rövid [útmutató: Felügyelt példány létrehozása.](instance-create-quickstart.md)

## <a name="powershell-create-and-configure-managed-instances"></a>PowerShell: Felügyelt példányok létrehozása és konfigurálása

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager modult továbbra is támogatja a Azure SQL Database, de minden jövőbeli fejlesztés az Az.Sql modulhoz lesz. A parancsmagokért lásd: [AzureRM.Sql.](/powershell/module/AzureRM.Sql/) Az Az modulban és az AzureRM-modulokban található parancsok argumentumai lényegesen azonosak.

Felügyelt példányok létrehozásához és kezeléséhez használja Azure PowerShell PowerShell-parancsmagokat. Ha telepítenie vagy frissítenie kell a PowerShellt, tekintse meg a következő modul [telepítését Azure PowerShell:](/powershell/azure/install-az-ps).

> [!TIP]
> A PowerShell-példaszk szkriptekért lásd: Gyorsindítási parancsfájl: Felügyelt példány [létrehozása PowerShell-kódtár használatával.](/archive/blogs/sqlserverstorageengine/quick-start-script-create-azure-sql-managed-instance-using-powershell)

| Parancsmag | Leírás |
| --- | --- |
|[New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)|Létrehoz egy felügyelt példányt. |
|[Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)|Egy felügyelt példányra vonatkozó adatokat ad vissza.|
|[Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)|Beállítja egy felügyelt példány tulajdonságait.|
|[Remove-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance)|Eltávolít egy felügyelt példányt.|
|[Get-AzSqlInstanceOperation](/powershell/module/az.sql/get-azsqlinstanceoperation)|Lekérte a felügyelt példányon vagy adott műveleten végrehajtott felügyeleti műveletek listáját.|
|[Stop-AzSqlInstanceOperation](/powershell/module/az.sql/stop-azsqlinstanceoperation)|Megszakítja a felügyelt példányon végrehajtott adott felügyeleti műveletet.|
|[New-AzSqlInstanceDatabase](/powershell/module/az.sql/new-azsqlinstancedatabase)|Létrehoz egy SQL Managed Instance adatbázist.|
|[Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase)|Egy adatbázisra vonatkozó SQL Managed Instance ad vissza.|
|[Remove-AzSqlInstanceDatabase](/powershell/module/az.sql/remove-azsqlinstancedatabase)|Eltávolít egy SQL Managed Instance adatbázist.|
|[Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase)|Visszaállít egy SQL Managed Instance adatbázist.|

## <a name="azure-cli-create-and-configure-managed-instances"></a>Azure CLI: Felügyelt példányok létrehozása és konfigurálása

Felügyelt példányok Azure [CLI-val](/cli/azure)való létrehozásához és konfiguráláshoz használja a következő Azure CLI-parancsokat [a SQL Managed Instance.](/cli/azure/sql/mi) A [Azure Cloud Shell](../../cloud-shell/overview.md) a CLI böngészőben való futtatásához, vagy macOS, Linux vagy Windows rendszeren való telepítéséhez. [](/cli/azure/install-azure-cli)

> [!TIP]
> Az Azure CLI rövid útmutatóját lásd: Working with SQL Managed Instance using Azure CLI (Az Azure CLI használata a [SQL Managed Instance használatával).](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)

| Parancsmag | Leírás |
| --- | --- |
|[az sql mi create](/cli/azure/sql/mi#az_sql_mi_create) |Létrehoz egy felügyelt példányt.|
|[az sql mi list](/cli/azure/sql/mi#az_sql_mi_list)|Felsorolja az elérhető felügyelt példányokat.|
|[az sql mi show](/cli/azure/sql/mi#az_sql_mi_show)|Lekérte egy felügyelt példány részleteit.|
|[az sql mi update](/cli/azure/sql/mi#az_sql_mi_update)|Frissíti a felügyelt példányt.|
|[az sql mi delete](/cli/azure/sql/mi#az_sql_mi_delete)|Eltávolít egy felügyelt példányt.|
|[az sql mi op list](/cli/azure/sql/mi/op#az_sql_mi_op_list)|Lekérte a felügyelt példányon végrehajtott felügyeleti műveletek listáját.|
|[az sql mi op show](/cli/azure/sql/mi/op#az_sql_mi_op_show)|Lekérte a felügyelt példányon végrehajtott konkrét felügyeleti műveletet.|
|[az sql mi op cancel](/cli/azure/sql/mi/op#az_sql_mi_op_cancel)|Megszakítja a felügyelt példányon végrehajtott konkrét felügyeleti műveletet.|
|[az sql midb create](/cli/azure/sql/midb#az_sql_midb_create) |Létrehoz egy felügyelt adatbázist.|
|[az sql midb list](/cli/azure/sql/midb#az_sql_midb_list)|Listázza az elérhető felügyelt adatbázisokat.|
|[az sql midb restore](/cli/azure/sql/midb#az_sql_midb_restore)|Visszaállít egy felügyelt adatbázist.|
|[az sql midb delete](/cli/azure/sql/midb#az_sql_midb_delete)|Eltávolít egy felügyelt adatbázist.|

## <a name="transact-sql-create-and-configure-instance-databases"></a>Transact-SQL: Példányadatbázisok létrehozása és konfigurálása

A felügyelt példány létrehozása után a következő T-SQL-parancsokkal hozhat létre és konfigurálhatja a példány-adatbázisokat. Ezeket a parancsokat a Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Azure Data Studio](/sql/azure-data-studio/what-is), [Visual Studio Code](https://code.visualstudio.com/docs), vagy bármely más olyan program használatával használhatja, amely csatlakozni tud egy kiszolgálóhoz, és Transact-SQL-parancsokat tud átadni.

> [!TIP]
> A felügyelt példányok microsoftos SQL Server Management Studio használatával történő konfigurálásával és csatlakoztatásával kapcsolatos rövid útmutatókért lásd: Rövid [útmutató:](connect-vm-instance-configure.md) Azure-beli virtuális gép konfigurálása az [Azure SQL Managed Instance-hoz](point-to-site-p2s-configure.md)való csatlakozáshoz és Rövid útmutató: Pont–hely kapcsolat konfigurálása Azure SQL Managed Instance-hez helyszíni rendszerről.

> [!IMPORTANT]
> Felügyelt példány nem hozható létre és nem törölhető a Transact-SQL használatával.

| Parancs | Leírás |
| --- | --- |
|[CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?preserve-view=true&view=azuresqldb-mi-current)|Létrehoz egy új példányadatbázist a SQL Managed Instance. Új adatbázis létrehozásához a master adatbázishoz kell csatlakozva lennie.|
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-mi-current) |Módosít egy példányadatbázist a SQL Managed Instance.|

## <a name="rest-api-create-and-configure-managed-instances"></a>REST API: Felügyelt példányok létrehozása és konfigurálása

Felügyelt példányok létrehozásához és konfiguráláshoz használja ezeket REST API kéréseket.

| Parancs | Leírás |
| --- | --- |
|[Felügyelt példányok – Létrehozás vagy frissítés](/rest/api/sql/managedinstances/createorupdate)|Létrehoz vagy frissíti a felügyelt példányt.|
|[Felügyelt példányok – Törlés](/rest/api/sql/managedinstances/delete)|Töröl egy felügyelt példányt.|
|[Felügyelt példányok – Lekért](/rest/api/sql/managedinstances/get)|Lekért egy felügyelt példányt.|
|[Felügyelt példányok – Lista](/rest/api/sql/managedinstances/list)|Egy előfizetésben található felügyelt példányok listáját adja vissza.|
|[Felügyelt példányok – Listás erőforráscsoportok szerint](/rest/api/sql/managedinstances/listbyresourcegroup)|Egy erőforráscsoportban található felügyelt példányok listáját adja vissza.|
|[Felügyelt példányok – frissítés](/rest/api/sql/managedinstances/update)|Frissíti a felügyelt példányt.|
|[Felügyelt példányok műveletei – Listás felügyelt példány szerint](/rest/api/sql/managedinstanceoperations/listbymanagedinstance)|Lekérte a felügyelt példányon végrehajtott felügyeleti műveletek listáját.|
|[Felügyelt példányok műveletei – Lekért](/rest/api/sql/managedinstanceoperations/get)|Lekérte a felügyelt példányon végrehajtott konkrét felügyeleti műveletet.|
|[Felügyelt példányok műveletei – Megszakítás](/rest/api/sql/managedinstanceoperations/cancel)|Megszakítja a felügyelt példányon végrehajtott konkrét felügyeleti műveletet.|

## <a name="next-steps"></a>Következő lépések

- A virtuális adatbázis Azure SQL Server ba való áttelepítését lásd: [Áttelepítés](../database/migrate-to-database-from-sql-server.md)Azure SQL Database.
- A támogatott funkciókkal kapcsolatos tudnivalókat lásd: [Funkciók](../database/features-comparison.md).
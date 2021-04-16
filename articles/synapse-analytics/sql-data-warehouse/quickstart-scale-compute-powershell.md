---
title: 'Rövid útmutató: Számítás skálázható dedikált SQL-készlethez (korábban SQL DW) (Azure PowerShell)'
description: A dedikált SQL-készlet (korábban SQL DW) számítási kapacitását skálázhatja a Azure PowerShell.
services: synapse-analytics
author: Antvgski
ms.author: anvang
manager: craigg
ms.reviewer: igorstan
ms.date: 04/17/2018
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- seo-lt-2019
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 73718f0e8e82e7d35ed1ced98f45c1e125a55a00
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534234"
---
# <a name="quickstart-scale-compute-for-dedicated-sql-pool-formerly-sql-dw-with-azure-powershell"></a>Rövid útmutató: Számítási feladatok méretezése dedikált SQL-készlethez (korábban SQL DW) Azure PowerShell

A dedikált SQL-készlet (korábban SQL DW) számítási kapacitását skálázhatja a Azure PowerShell. [Felskálázással](sql-data-warehouse-manage-compute-overview.md) a számítások teljesítménye növelhető, leskálázással a költségek csökkenthetők.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ez a rövid útmutató feltételezi, hogy már rendelkezik egy skálázhat dedikált SQL-készlet (korábban SQL DW) használatával. Ha létre kell hoznia egyet, a Létrehozás és csatlakozás [–](create-data-warehouse-portal.md) portál használatával hozzon létre egy **mySampleDataWarehouse** nevű dedikált SQL-készletet (korábban SQL DW).

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a [Connect-AzAccount paranccsal,](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) és kövesse a képernyőn megjelenő utasításokat.

```powershell
Connect-AzAccount
```

A használt előfizetést a [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)futtatásával tudja megnézni.

```powershell
Get-AzSubscription
```

Ha az alapértelmezetttől eltérő előfizetést kell használnia, futtassa a [Set-AzContext függvényt.](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>Adattárház-információk megkeresése

Keresse meg a felfüggeszteni és folytatni tervezett adattárházhoz tartozó adatbázis nevét, a kiszolgáló nevét és az erőforráscsoportot.

Keresse meg adattárháza helyinformációit ezekkel lépésekkel.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Kattintson **Azure Synapse Analytics (korábban SQL DW)** elemre a bal oldali navigációs Azure Portal.
3. Válassza **ki a mySampleDataWarehouse** Azure Synapse Analytics **(korábban SQL DW)** oldalon az adattárház megnyitásához.

    ![Kiszolgálónév és erőforráscsoport](./media/quickstart-scale-compute-powershell/locate-data-warehouse-information.png)

4. Írja fel az adattárház nevét. Ezt fogja használni az adatbázis neveként. Ne feledje, hogy az adattárház az adatbázisok egy típusa. Írja fel a kiszolgáló nevét és az erőforráscsoportot is. A felfüggesztési és folytatási parancsokban a kiszolgálónevet és az erőforráscsoport nevét fogja használni.
5. A PowerShell-parancsmagok csak a kiszolgálónév első részét használják. Az előző képen a teljes kiszolgálónév sqlpoolservername.database.windows.net. A **PowerShell-parancsmagban az sqlpoolservername** nevet használjuk kiszolgálónévként.

## <a name="scale-compute"></a>Számítások méretezése

A dedikált SQL-készletben (korábban SQL DW) az adattárházegységek módosításával növelheti vagy csökkentheti a számítási erőforrásokat. A [Létrehozás és csatlakozás – portál](create-data-warehouse-portal.md) gyorsútmutató létrehozta a **mySampleDataWarehouse** adattárházat, és inicializálta azt 400 adattárházegységgel. Az alábbi lépésekkel módosíthatja a **mySampleDataWarehouse** adattárházban az adattárházegységek számát.

Az adattárházegységek a [Set-AzSqlDatabase PowerShell-parancsmag](/powershell/module/az.sql/set-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) használatával változtathatóak meg. Az alábbi példa DW300c adattárházegységeket állít be a **mySampleDataWarehouse** adatbázishoz, amely a(z) **sqlpoolservername** kiszolgálón található **resourcegroupname** erőforráscsoportban található.

```Powershell
Set-AzSqlDatabase -ResourceGroupName "resourcegroupname" -DatabaseName "mySampleDataWarehouse" -ServerName "sqlpoolservername" -RequestedServiceObjectiveName "DW300c"
```

## <a name="check-data-warehouse-state"></a>Az adattárház állapotának ellenőrzése

Az adattárház aktuális állapotának a [Get-AzSqlDatabase PowerShell-parancsmag](/powershell/module/az.sql/get-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) használatával használhatja. Ez a parancsmag a **mySampleDataWarehouse** adatbázis állapotát mutatja a ResourceGroup **erőforráscsoportnév** és a kiszolgálócsoport **sqlpoolservername.database.windows.net.**

```powershell
$database = Get-AzSqlDatabase -ResourceGroupName resourcegroupname -ServerName sqlpoolservername -DatabaseName mySampleDataWarehouse
```

Az eredmény a következőhöz lesz hasonló:

```powershell
ResourceGroupName             : resourcegroupname
ServerName                    : sqlpoolservername
DatabaseName                  : mySampleDataWarehouse
Location                      : North Europe
DatabaseId                    : 34d2ffb8-b70a-40b2-b4f9-b0a39833c974
Edition                       : DataWarehouse
CollationName                 : SQL_Latin1_General_CP1_CI_AS
CatalogCollation              :
MaxSizeBytes                  : 263882790666240
Status                        : Online
CreationDate                  : 11/20/2017 9:18:12 PM
CurrentServiceObjectiveId     : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
CurrentServiceObjectiveName   : DW300c
RequestedServiceObjectiveId   : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
RequestedServiceObjectiveName :
ElasticPoolName               :
EarliestRestoreDate           :
Tags                          :
ResourceId                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/
                                resourceGroups/resourcegroupname/providers/Microsoft.Sql/servers/sqlpoolservername/databases/mySampleDataWarehouse
CreateMode                    :
ReadScale                     : Disabled
ZoneRedundant                 : False
```

A kimenetben megjelenik az adatbázis **Állapota**. Ebben az esetben az látható, hogy az adatbázis online állapotban van.  Ennek a parancsnak a futtatáskor az Online, Pausing (felfüggesztés), Resuming (folytatás), Scaling (méretezés) vagy Paused (felfüggesztve) állapotértéket kell visszakapnia.

Az állapot önmagában való megjelenítéséhez használja a következő parancsot:

```powershell
$database | Select-Object DatabaseName,Status
```

## <a name="next-steps"></a>Következő lépések

Megtanulta, hogyan skálázható a számítás dedikált SQL-készlethez (korábban SQL DW). Ha többet szeretne megtudni a dedikált SQL-készletről (korábbi nevén SQL DW-ről), folytassa az adatok betöltését ismertető oktatóanyagmal.

> [!div class="nextstepaction"]
>[Adatok betöltése dedikált SQL-készletbe](load-data-from-azure-blob-storage-using-copy.md)

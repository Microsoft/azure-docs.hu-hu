---
title: 'Gyors útmutató: számítások méretezése dedikált SQL-készlethez (korábban SQL DW) (Azure PowerShell)'
description: A dedikált SQL-készlet (korábban SQL DW) számítási felskálázását Azure PowerShell használatával végezheti el.
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.openlocfilehash: 87e10740e6081431bad96daa930f61238ca495bd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96921904"
---
# <a name="quickstart-scale-compute-for-dedicated-sql-pool-formerly-sql-dw-with-azure-powershell"></a>Gyors útmutató: a kiszámítások méretezése dedikált SQL-készlethez (korábban SQL DW) Azure PowerShell

A dedikált SQL-készlet (korábban SQL DW) számítási felskálázását Azure PowerShell használatával végezheti el. [Felskálázással](sql-data-warehouse-manage-compute-overview.md) a számítások teljesítménye növelhető, leskálázással a költségek csökkenthetők.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ez a rövid útmutató azt feltételezi, hogy már rendelkezik egy olyan dedikált SQL-készlettel (korábban SQL DW), amelyet méretezhető. Ha létre kell hoznia egyet, a [create és a összekapcsolás-Portal](create-data-warehouse-portal.md) használatával hozzon létre egy **MYSAMPLEDATAWAREHOUSE** nevű dedikált SQL-készletet (korábban SQL DW).

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a [AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) parancs használatával, és kövesse a képernyőn megjelenő utasításokat.

```powershell
Connect-AzAccount
```

A [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)futtatásával megtekintheti, hogy melyik előfizetést használja.

```powershell
Get-AzSubscription
```

Ha az alapértelmezettnél eltérő előfizetést kell használnia, futtassa a [set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)parancsot.

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>Adattárház-információk megkeresése

Keresse meg a felfüggeszteni és folytatni tervezett adattárházhoz tartozó adatbázis nevét, a kiszolgáló nevét és az erőforráscsoportot.

Keresse meg adattárháza helyinformációit ezekkel lépésekkel.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Kattintson az **Azure szinapszis Analytics (korábban SQL DW)** elemre a Azure Portal bal oldali navigációs oldalán.
3. Az adatraktár megnyitásához válassza a **mySampleDataWarehouse** lehetőséget az **Azure szinapszis Analytics (korábban SQL DW)** lapon.

    ![Kiszolgálónév és erőforráscsoport](./media/quickstart-scale-compute-powershell/locate-data-warehouse-information.png)

4. Írja fel az adattárház nevét. Ezt fogja használni az adatbázis neveként. Ne feledje, hogy az adattárház az adatbázisok egy típusa. Írja fel a kiszolgáló nevét és az erőforráscsoportot is. A kiszolgáló nevét és az erőforráscsoport nevét a Szüneteltetés és folytatás parancsban fogja használni.
5. A PowerShell-parancsmagokban csak a kiszolgáló nevének első részét használja. Az előző képen a teljes kiszolgáló neve sqlpoolservername.database.windows.net. A PowerShell-parancsmagban a **sqlpoolservername** -t használjuk a kiszolgáló neveként.

## <a name="scale-compute"></a>Számítások méretezése

A dedikált SQL-készletben (korábban az SQL DW-ben) az adatraktár-egységek módosításával növelheti vagy csökkentheti a számítási erőforrásokat. A [Létrehozás és csatlakozás – portál](create-data-warehouse-portal.md) gyorsútmutató létrehozta a **mySampleDataWarehouse** adattárházat, és inicializálta azt 400 adattárházegységgel. Az alábbi lépésekkel módosíthatja a **mySampleDataWarehouse** adattárházban az adattárházegységek számát.

Az adatraktár-egységek módosításához használja a [set-AzSqlDatabase PowerShell-](/powershell/module/az.sql/set-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) parancsmagot. A következő példa a DW300c adatraktár-egységeit állítja be az adatbázis- **mySampleDataWarehouse**, amely a kiszolgáló **sqlpoolservername** erőforráscsoport- **resourcegroupname** található.

```Powershell
Set-AzSqlDatabase -ResourceGroupName "resourcegroupname" -DatabaseName "mySampleDataWarehouse" -ServerName "sqlpoolservername" -RequestedServiceObjectiveName "DW300c"
```

## <a name="check-data-warehouse-state"></a>Az adattárház állapotának ellenőrzése

Az adatraktár aktuális állapotának megtekintéséhez használja a [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell-parancsmagot. Ez a parancsmag a **mySampleDataWarehouse** -adatbázis állapotát jeleníti meg a ResourceGroup **resourcegroupname** és a kiszolgáló **sqlpoolservername.database.Windows.net**.

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

Megtanulta, hogyan méretezheti a kiszámítást a dedikált SQL-készlet (korábban SQL DW) számára. Ha többet szeretne megtudni a dedikált SQL-készletről (korábban SQL DW), folytassa a betöltési útmutatóval.

> [!div class="nextstepaction"]
>[Adat betöltése egy dedikált SQL-készletbe](load-data-from-azure-blob-storage-using-copy.md)

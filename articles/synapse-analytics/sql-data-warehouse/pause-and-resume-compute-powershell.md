---
title: 'Rövid útmutató: Számítási feladatok szüneteltetése és folytatása dedikált SQL-készletben (korábban SQL DW) Azure PowerShell'
description: A virtuális Azure PowerShell szüneteltetheti és folytathatja a dedikált SQL-készletet (korábban SQL DW). számítási erőforrásokat.
services: synapse-analytics
author: gaursa
ms.author: gaursa
manager: craigg
ms.reviewer: igorstan
ms.date: 03/20/2019
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- seo-lt-2019
- azure-synapse
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: b204132a49a8790b35cc99af8eebf465fd90f041
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536362"
---
# <a name="quickstart-pause-and-resume-compute-in-dedicated-sql-pool-formerly-sql-dw-with-azure-powershell"></a>Rövid útmutató: Számítási feladatok szüneteltetése és folytatása dedikált SQL-készletben (korábban SQL DW) Azure PowerShell

A virtuális Azure PowerShell szüneteltetheti és folytathatja a dedikált SQL-készlet (korábban SQL DW) számítási erőforrásait.
Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ez a rövid útmutató feltételezi, hogy már rendelkezik egy dedikált SQL-készlettel (korábban SQL DW), amely szüneteltetheti és folytathatja a működést. Ha létre kell hoznia egyet, a Létrehozás és csatlakozás – portál használatával létrehozhat egy **mySampleDataWarehouse** nevű dedikált SQL-készletet (korábban SQL DW). [](create-data-warehouse-portal.md)

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) paranccsal, és kövesse a képernyőn megjelenő utasításokat.

```powershell
Connect-AzAccount
```

A használt előfizetést a [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)futtatásával tudja megnézni.

```powershell
Get-AzSubscription
```

Ha az alapértelmezetttől eltérő előfizetést kell használnia, futtassa a [Set-AzContext et.](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-dedicated-sql-pool-formerly-sql-dw-information"></a>Dedikált SQL-készletre (korábban SQL DW) vonatkozó információk

Keresse meg a felfüggeszteni és folytatni tervezi dedikált SQL-készlet (korábban SQL DW) adatbázisnevét, kiszolgálónevét és erőforráscsoportját.

Kövesse az alábbi lépéseket a dedikált SQL-készlet (korábban SQL DW) helyinformációiért:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. A **Azure Synapse Analytics bal** oldalán kattintson a Azure Portal.
1. Válassza **ki a mySampleDataWarehouse** Azure Synapse Analytics **(korábban SQL DW)** oldalon. Megnyílik az SQL-készlet.

    ![Kiszolgálónév és erőforráscsoport](./media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

1. Írja fel a dedikált SQL-készlet (korábban SQL DW) nevét, amely az adatbázis neve. Írja fel a kiszolgáló nevét és az erőforráscsoportot is.
1. A PowerShell-parancsmagok csak a kiszolgálónév első részét használják. Az előző képen a teljes kiszolgálónév sqlpoolservername.database.windows.net. A **PowerShell-parancsmagban az sqlpoolservername** nevet használjuk kiszolgálónévként.

## <a name="pause-compute"></a>Számítás szüneteltetése

A költségek csökkentése érdekében igény szerint szüneteltetheti és folytathatja a számítási erőforrásokat. Ha például nem használja az adatbázist éjszaka és hétvégeken, szüneteltetheti az idő alatt, és folytathatja azt a nap folyamán.

>[!NOTE]
>Az adatbázis szüneteltetése közben a számítási erőforrások díjmentesen használhatók. A tárterületért azonban továbbra is díjat számítunk fel.

Egy adatbázis szüneteltetése a [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) parancsmag használatával. Az alábbi példa felfüggeszt egy **mySampleDataWarehouse** nevű SQL-készletet, amely egy **sqlpoolservername** nevű kiszolgálón fut. A kiszolgáló egy **myResourceGroup nevű Azure-erőforráscsoportban található.**

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
```

Az alábbi példa beolvassa az adatbázist a $database objektumba. Ezután az objektumot a [Suspend-AzSqlDatabase adatbázishoz irányitja.](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Az eredményeket a resultDatabase objektum tárolja. Az utolsó parancs megjeleníti az eredményeket.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="resume-compute"></a>Számítás folytatása

Adatbázist a [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) parancsmag használatával indít el. A következő példa elindít egy **mySampleDataWarehouse** nevű adatbázist, amely egy **sqlpoolservername** nevű kiszolgálón fut. A kiszolgáló egy **myResourceGroup nevű Azure-erőforráscsoportban található.**

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

A következő példa lekéri az adatbázist a $database objektumba. Ezután a [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) adatbázisba csöveket hoz létre az objektummal, és az eredményeket egy $resultDatabase. Az utolsó parancs megjeleníti az eredményeket.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Resume-AzSqlDatabase
$resultDatabase
```

## <a name="check-status-of-your-sql-pool-operation"></a>Ellenőrizze az SQL-készlet műveletének állapotát

A dedikált SQL-készlet (korábban SQL DW) állapotának ellenőrzéshez használja a [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/Get-AzSqlDatabaseActivity?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) parancsmagot.

```Powershell
Get-AzSqlDatabaseActivity -ResourceGroupName "myResourceGroup" -ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Díjat számítunk fel az adattárházegységekért és a dedikált SQL-készletben (korábban SQL DW) tárolt adatokért. Ezek a számítási és tárolási erőforrások elkülönítve lesznek kiszámlázva.

- Ha az adatokat a tárolóban szeretné tartani, szüneteltesse a számítást.
- Ha el szeretné távolítani a jövőbeli díjakat, törölheti az SQL-készletet.

Kövesse az alábbi lépéseket a fölöslegessé vált erőforrások eltávolítására.

1. Jelentkezzen be a [Azure Portal,](https://portal.azure.com)és kattintson az SQL-készletre.

    ![Az erőforrások eltávolítása](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. A számítási erőforrások szüneteltetéshez kattintson a **Szüneteltetés** gombra. Ha az SQL-készlet fel van függesztve, egy **Indítás gomb látható.**  A számítási erőforrások újraindításához kattintson az **Indítás** gombra.

3. Ha el szeretné távolítani az SQL-készletet, hogy ne számítson fel díjat a számítási vagy tárolási erőforrásokért, kattintson a **Törlés gombra.**

4. A létrehozott SQL-kiszolgáló eltávolításához kattintson a **sqlpoolservername.database.windows.net,** majd a **Törlés parancsra.**  A törléssel bánjon óvatosan, mivel a kiszolgálóval együtt a hozzá rendelt összes adatbázis is törölve lesz.

5. Az erőforráscsoport törléséhez kattintson a **myResourceGroup** elemre, majd az **Erőforráscsoport törlése** parancsra.

## <a name="next-steps"></a>Következő lépések

Az SQL-készletről további információt az Adatok betöltése dedikált SQL-készletbe [(korábban SQL DW) című](./load-data-from-azure-blob-storage-using-copy.md) cikkben talál. A számítási képességek kezelésével kapcsolatos további információkért tekintse meg a [Számítási feladatok kezelése – áttekintés cikket.](sql-data-warehouse-manage-compute-overview.md)

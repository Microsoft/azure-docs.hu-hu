---
title: Karbantartási időszak konfigurálása (előzetes verzió)
description: Megtudhatja, hogyan állíthatja be az időpontot, amikor a tervezett karbantartást az Azure SQL-adatbázisokon, a rugalmas készleteken és a felügyelt példány adatbázisain kell elvégezni.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/23/2021
ms.openlocfilehash: 8688458d85084f3d3dab4678fa91ed827a337739
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047351"
---
# <a name="configure-maintenance-window-preview"></a>Karbantartási időszak konfigurálása (előzetes verzió)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


Konfigurálja a [karbantartási időszakot (előzetes verzió)](maintenance-window.md) egy Azure SQL Database-adatbázishoz, egy rugalmas készlethez vagy egy Azure SQL felügyelt példány-adatbázishoz az erőforrás-létrehozás során vagy az erőforrás létrehozása után bármikor. 

A *rendszer alapértelmezett* karbantartási időszaka napi 5 – 08:00 (az erőforrást tartalmazó Azure-régió helyi ideje), hogy elkerülje a maximális munkaidő-megszakítást. Ha a *rendszer alapértelmezett* karbantartási időszaka nem a legjobb időpont, válasszon ki egy másik rendelkezésre álló karbantartási időszakot.

A másik karbantartási időszakra való váltás nem minden szolgáltatási szinthez vagy régióhoz érhető el. A rendelkezésre állással kapcsolatos részletekért lásd a [karbantartási időszak elérhetőségét](maintenance-window.md#availability)ismertető témakört.

> [!Important]
> A karbantartási időszak konfigurálása hosszú ideig futó aszinkron művelet, hasonlóan az Azure SQL-erőforrás szolgáltatási rétegének módosításához. Az erőforrás a művelet során elérhető, kivéve a művelet végén előforduló rövid újrakonfigurálást, és általában akár 8 másodpercig is tart, akár a hosszan futó tranzakciók megszakítása esetén is. Az újrakonfigurálás hatásának csökkentése érdekében a műveletet a csúcsidőben kívül kell végrehajtania.

## <a name="configure-maintenance-window-during-database-creation"></a>Karbantartási időszak konfigurálása az adatbázisok létrehozásakor 

# <a name="portal"></a>[Portál](#tab/azure-portal)

Ha az adatbázis, a rugalmas készlet vagy a felügyelt példány létrehozásakor szeretné beállítani a karbantartási időszakot, a **További beállítások** lapon állítsa be a kívánt **karbantartási** időszakot. 

## <a name="set-the-maintenance-window-while-creating-a-single-database-or-elastic-pool"></a>A karbantartási időszak beállítása egyetlen adatbázis vagy rugalmas készlet létrehozásakor

Az új adatbázis vagy készlet létrehozásával kapcsolatos részletes információkért lásd: [Azure SQL Database önálló adatbázis létrehozása](single-database-create-quickstart.md).

   :::image type="content" source="media/maintenance-window-configure/additional-settings.png" alt-text="Adatbázis további beállításainak létrehozása lap":::


## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>A karbantartási időszak beállítása felügyelt példány létrehozásakor

Az új felügyelt példányok létrehozásával kapcsolatos részletes információkért lásd: [Azure SQL felügyelt példány létrehozása](../managed-instance/instance-create-quickstart.md).

   :::image type="content" source="media/maintenance-window-configure/additional-settings-mi.png" alt-text="Felügyelt példány további beállításainak létrehozása lap":::




# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az alábbi példák bemutatják, hogyan konfigurálhatja a karbantartási időszakot a Azure PowerShell használatával. [Telepítheti Azure PowerShell](/powershell/azure/install-az-ps), vagy használhatja a Azure Cloud shell.

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell elindítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shell egy külön böngészőablakban is elindíthatja [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.


## <a name="discover-available-maintenance-windows"></a>Rendelkezésre álló karbantartási időszakok felderítése

A karbantartási időszak beállításakor az egyes régiók saját karbantartási időszakra vonatkozó beállításokkal rendelkeznek, amelyek az adatbázist vagy a készletet tartalmazó régió időzónájának felelnek meg. 

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>A SQL Database és a rugalmas készlet karbantartási időszakának felderítése 

A következő példa a *eastus2* régió rendelkezésre álló karbantartási időszakait adja vissza a [Get-AzMaintenancePublicConfiguration](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) parancsmag használatával. Adatbázisokhoz és rugalmas készletekhez állítsa a következőre: `MaintenanceScope` `SQLDB` .

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLDB"}
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>SQL felügyelt példány karbantartási ablakának felderítése 

A következő példa a *eastus2* régió rendelkezésre álló karbantartási időszakait adja vissza a [Get-AzMaintenancePublicConfiguration](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) parancsmag használatával. Felügyelt példányok esetén állítsa a következőre: `MaintenanceScope` `SQLManagedInstance` .

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLManagedInstance"}
   ```


## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>A karbantartási időszak beállítása egyetlen adatbázis létrehozásakor

Az alábbi példa egy új adatbázist hoz létre, és a [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) parancsmaggal beállítja a karbantartási időszakot. A `-MaintenanceConfigurationId` értéket az adatbázis régiójának érvényes értékére kell beállítani. A régió érvényes értékeinek lekéréséhez tekintse meg a [rendelkezésre álló karbantartási időszakok felderítése](#discover-available-maintenance-windows)című témakört.


   ```powershell-interactive
    # Set variables for your database
    $resourceGroupName = "your_resource_group_name"
    $serverName = "your_server_name"
    $databaseName = "your_db_name"
    
    # Set selected maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_1"

    Write-host "Creating a gen5 2 vCore database with maintenance window ${maintenanceConfig} ..."
    $database = New-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -Edition GeneralPurpose `
      -ComputeGeneration Gen5 `
      -VCore 2 `
      -MaintenanceConfigurationId $maintenanceConfig
    $database
   ```



## <a name="set-the-maintenance-window-while-creating-an-elastic-pool"></a>A karbantartási időszak beállítása rugalmas készlet létrehozásakor

Az alábbi példa egy új rugalmas készletet hoz létre, és beállítja a karbantartási időszakot a [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) parancsmag használatával. A karbantartási időszak a rugalmas készleten van beállítva, így a készletben lévő összes adatbázishoz a készlet karbantartási időszakának ütemezett beállítása szükséges. A `-MaintenanceConfigurationId` értéket a készlet régiójának érvényes értékére kell beállítani. A régió érvényes értékeinek lekéréséhez tekintse meg a [rendelkezésre álló karbantartási időszakok felderítése](#discover-available-maintenance-windows)című témakört.


   ```powershell-interactive
    # Set variables for your pool
    $resourceGroupName = "your_resource_group_name"
    $serverName = "your_server_name"
    $poolName = "your_pool_name"
    
    # Set selected maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_2"

    Write-host "Creating a Standard 50 pool with maintenance window ${maintenanceConfig} ..."
    $pool = New-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName `
      -Edition "Standard" `
      -Dtu 50 `
      -DatabaseDtuMin 10 `
      -DatabaseDtuMax 20 `
      -MaintenanceConfigurationId $maintenanceConfig
    $pool
   ```

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>A karbantartási időszak beállítása felügyelt példány létrehozásakor

A következő példa létrehoz egy új felügyelt példányt, és beállítja a karbantartási időszakot a [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) parancsmag használatával. A karbantartási időszak a példányra van beállítva, így a példányban lévő összes adatbázishoz a példány karbantartási időszakának ütemterve tartozik. A esetében `-MaintenanceConfigurationId` a *MaintenanceConfigName* érvényes értéknek kell lennie a példány régiójában. A régió érvényes értékeinek lekéréséhez tekintse meg a [rendelkezésre álló karbantartási időszakok felderítése](#discover-available-maintenance-windows)című témakört.


   ```powershell
   New-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -Location "your_mi_location" `
     -SubnetId /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[Parancssori felület](#tab/azure-cli)

Az alábbi példák bemutatják, hogyan konfigurálhatja a karbantartási időszakot az Azure CLI használatával. [Telepítheti az Azure CLI](/cli/azure/install-azure-cli)-t, vagy használhatja a Azure Cloud shell. 

A karbantartási időszak Azure CLI-vel való konfigurálása csak az SQL felügyelt példányai esetében érhető el.

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell elindítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shell egy külön böngészőablakban is elindíthatja [https://shell.azure.com/cli](https://shell.azure.com/cli) . A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

## <a name="discover-available-maintenance-windows"></a>Rendelkezésre álló karbantartási időszakok felderítése

A karbantartási időszak beállításakor az egyes régiók saját karbantartási időszakra vonatkozó beállításokkal rendelkeznek, amelyek az adatbázist vagy a készletet tartalmazó régió időzónájának felelnek meg.

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>A SQL Database és a rugalmas készlet karbantartási időszakának felderítése

A következő példa a *eastus2* régió számára elérhető karbantartási időszakokat adja vissza az az [karbantartás nyilvános konfigurációs List](/cli/azure/ext/maintenance/maintenance/public-configuration#ext_maintenance_az_maintenance_public_configuration_list) paranccsal. Adatbázisokhoz és rugalmas készletekhez állítsa a következőre: `maintenanceScope` `SQLDB` .

   ```azurecli
   location="eastus2"

   az maintenance public-configuration list --query "[?location=='$location'&&contains(maintenanceScope,'SQLDB')]"
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>SQL felügyelt példány karbantartási ablakának felderítése

A következő példa a *eastus2* régió számára elérhető karbantartási időszakokat adja vissza az az [karbantartás nyilvános konfigurációs List](/cli/azure/ext/maintenance/maintenance/public-configuration#ext_maintenance_az_maintenance_public_configuration_list) paranccsal. Felügyelt példányok esetén állítsa a következőre: `maintenanceScope` `SQLManagedInstance` .

   ```azurecli
   az maintenance public-configuration list --query "[?location=='eastus2'&&contains(maintenanceScope,'SQLManagedInstance')]"
   ```

## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>A karbantartási időszak beállítása egyetlen adatbázis létrehozásakor

Az alábbi példa egy új adatbázist hoz létre, és beállítja a karbantartási időszakot az az [SQL db Create](/cli/azure/sql/db#az_sql_db_create) paranccsal. A `--maint-config-id` (vagy `-m` ) értéket az adatbázis régiójának érvényes értékére kell beállítani. A régió érvényes értékeinek lekéréséhez tekintse meg a [rendelkezésre álló karbantartási időszakok felderítése](#discover-available-maintenance-windows)című témakört.


   ```azurecli
    # Set variables for your database
    resourceGroupName="your_resource_group_name"
    serverName="your_server_name"
    databaseName="your_db_name"

    # Set selected maintenance window
    maintenanceConfig="SQL_EastUS2_DB_1"

    # Create database
    az sql db create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2 \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-while-creating-an-elastic-pool"></a>A karbantartási időszak beállítása rugalmas készlet létrehozásakor

Az alábbi példa egy új rugalmas készletet hoz létre, és beállítja a karbantartási időszakot az az [SQL rugalmas készlet létrehozása](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create) parancsmag használatával. A karbantartási időszak a rugalmas készleten van beállítva, így a készletben lévő összes adatbázishoz a készlet karbantartási időszakának ütemezett beállítása szükséges. A `--maint-config-id` (vagy `-m` ) értéket a készlet régiójának érvényes értékére kell beállítani. A régió érvényes értékeinek lekéréséhez tekintse meg a [rendelkezésre álló karbantartási időszakok felderítése](#discover-available-maintenance-windows)című témakört.


   ```azurecli
    # Set variables for your pool
    resourceGroupName="your_resource_group_name"
    serverName="your_server_name"
    poolName="your_pool_name"

    # Set selected maintenance window
    maintenanceConfig="SQL_EastUS2_DB_2"

    # Create elastic pool
    az sql elastic-pool create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2 \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>A karbantartási időszak beállítása felügyelt példány létrehozásakor

Az alábbi példa egy új felügyelt példányt hoz létre, és beállítja a karbantartási időszakot az [az SQL mi Create](/cli/azure/sql/mi#az_sql_mi_create)paranccsal. A karbantartási időszak a példányra van beállítva, így a példányban lévő összes adatbázishoz a példány karbantartási időszakának ütemterve tartozik. A *MaintenanceConfigName* érvényes értéknek kell lennie a példány régiójában. A régió érvényes értékeinek lekéréséhez tekintse meg a [rendelkezésre álló karbantartási időszakok felderítése](#discover-available-maintenance-windows)című témakört.

   ```azurecli
   az sql mi create -g mygroup -n myinstance -l mylocation -i -u myusername -p mypassword --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}
   ```

-----

## <a name="configure-maintenance-window-for-existing-databases"></a>A meglévő adatbázisok karbantartási időszakának konfigurálása


Amikor a karbantartási időszakot egy adatbázisra alkalmazza, előfordulhat, hogy egy rövid újrakonfigurálás (több másodperc) is előfordulhatnak bizonyos esetekben, ahogy az Azure alkalmazza a szükséges módosításokat.

# <a name="portal"></a>[Portál](#tab/azure-portal)

A következő lépésekkel állíthatja be a karbantartási időszakot egy meglévő adatbázison, rugalmas készleten vagy felügyelt példányon a Azure Portal használatával:


## <a name="set-the-maintenance-window-for-an-existing-database-or-elastic-pool"></a>Meglévő adatbázis vagy rugalmas készlet karbantartási időszakának beállítása

1. Navigáljon ahhoz az SQL-adatbázishoz vagy rugalmas készlethez, amelyhez be szeretné állítani a karbantartási időszakot.
1. A **Beállítások** menüben válassza a **karbantartás** lehetőséget, majd válassza ki a kívánt karbantartási időszakot.

   :::image type="content" source="media/maintenance-window-configure/maintenance.png" alt-text="SQL Database-karbantartási oldal":::


## <a name="set-the-maintenance-window-for-an-existing-managed-instance"></a>Egy meglévő felügyelt példány karbantartási időszakának beállítása

1. Navigáljon ahhoz a felügyelt példányhoz, amelyhez be szeretné állítani a karbantartási időszakot.
1. A **Beállítások** menüben válassza a **karbantartás** lehetőséget, majd válassza ki a kívánt karbantartási időszakot.

   :::image type="content" source="media/maintenance-window-configure/maintenance-mi.png" alt-text="SQL felügyelt példány karbantartási lapja":::



# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="set-the-maintenance-window-for-an-existing-database"></a>Meglévő adatbázis karbantartási időszakának beállítása

A következő példa egy meglévő adatbázis karbantartási időszakát állítja be a [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) parancsmag használatával. A `-MaintenanceConfigurationId` értéket az adatbázis régiójának érvényes értékére kell beállítani. A régió érvényes értékeinek lekéréséhez tekintse meg a [rendelkezésre álló karbantartási időszakok felderítése](#discover-available-maintenance-windows)című témakört.

   ```powershell-interactive
    # Select different maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_2"

    Write-host "Changing database maintenance window to ${maintenanceConfig} ..."
    $database = Set-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -MaintenanceConfigurationId $maintenanceConfig
    $database
   ```

## <a name="set-the-maintenance-window-on-an-existing-elastic-pool"></a>A karbantartási időszak beállítása meglévő rugalmas készleten

A következő példa egy meglévő rugalmas készlet karbantartási időszakát állítja be a [set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool) parancsmag használatával. Fontos, hogy az `$maintenanceConfig` érték a készlet régiójában érvényes érték legyen.  Egy régió érvényes értékeinek lekéréséhez tekintse meg a [rendelkezésre álló karbantartási időszakok felderítése](#discover-available-maintenance-windows)című témakört.

   ```powershell-interactive
    # Select different maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_1"
    
    Write-host "Changing pool maintenance window to ${maintenanceConfig} ..."
    $pool = Set-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName `
      -MaintenanceConfigurationId $maintenanceConfig
    $pool
   ```



## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>A karbantartási időszak beállítása egy meglévő felügyelt példányon

A következő példa egy meglévő felügyelt példány karbantartási időszakát állítja be a [set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance) parancsmag használatával. Fontos ellenőrizni, hogy az `$maintenanceConfig` értéknek érvényes értéknek kell lennie a példány régiójában.  Egy régió érvényes értékeinek lekéréséhez tekintse meg a [rendelkezésre álló karbantartási időszakok felderítése](#discover-available-maintenance-windows)című témakört.


   ```powershell-interactive
   Set-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[Parancssori felület](#tab/azure-cli)

Az alábbi példák bemutatják, hogyan konfigurálhatja a karbantartási időszakot az Azure CLI használatával. [Telepítheti az Azure CLI](/cli/azure/install-azure-cli)-t, vagy használhatja a Azure Cloud shell.

## <a name="set-the-maintenance-window-for-an-existing-database"></a>Meglévő adatbázis karbantartási időszakának beállítása

A következő példa a karbantartási időszakot egy meglévő adatbázison állítja be az az [SQL db Update](/cli/azure/sql/db#az_sql_db_update) paranccsal. A `--maint-config-id` (vagy `-m` ) értéket az adatbázis régiójának érvényes értékére kell beállítani. A régió érvényes értékeinek lekéréséhez tekintse meg a [rendelkezésre álló karbantartási időszakok felderítése](#discover-available-maintenance-windows)című témakört.

   ```azurecli
    # Select different maintenance window
    maintenanceConfig="SQL_EastUS2_DB_2"

    # Update database
    az sql db update \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-on-an-existing-elastic-pool"></a>A karbantartási időszak beállítása meglévő rugalmas készleten

A következő példa a karbantartási időszakot egy meglévő rugalmas készleten állítja be az az [SQL rugalmas készlet frissítése](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) parancs használatával. Fontos, hogy az `maintenanceConfig` érték a készlet régiójában érvényes érték legyen.  Egy régió érvényes értékeinek lekéréséhez tekintse meg a [rendelkezésre álló karbantartási időszakok felderítése](#discover-available-maintenance-windows)című témakört.

   ```azurecli
    # Select different maintenance window
    maintenanceConfig="SQL_EastUS2_DB_1"

    # Update pool
    az sql elastic-pool update \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>A karbantartási időszak beállítása egy meglévő felügyelt példányon

A következő példa az az [SQL mi Update](/cli/azure/sql/mi#az_sql_mi_update)paranccsal állítja be a karbantartási időszakot. A karbantartási időszak a példányra van beállítva, így a példányban lévő összes adatbázishoz a példány karbantartási időszakának ütemterve tartozik. A esetében `-MaintenanceConfigurationId` a *MaintenanceConfigName* érvényes értéknek kell lennie a példány régiójában. A régió érvényes értékeinek lekéréséhez tekintse meg a [rendelkezésre álló karbantartási időszakok felderítése](#discover-available-maintenance-windows)című témakört.

   ```azurecli
   az sql mi update -g mygroup  -n myinstance -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MainteanceConfigName}
   ```

-----

## <a name="cleanup-resources"></a>Az erőforrások eltávolítása

A szükségtelen költségek elkerülése érdekében ne felejtse el törölni a szükségtelen erőforrásokat a velük végzett munka után.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Navigáljon a már nem szükséges SQL-adatbázishoz vagy rugalmas készlethez.
1. Az **Áttekintés** menüben válassza ki az erőforrás törlésének lehetőségét.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   # Delete database
   Remove-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName

   # Delete elastic pool
   Remove-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName
   ```

# <a name="cli"></a>[Parancssori felület](#tab/azure-cli)

   ```azurecli
   az sql db delete \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName

   az sql elastic-pool delete \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName
   ```

-----

## <a name="next-steps"></a>Következő lépések

- További információ a karbantartási időszakokról: [karbantartási időszak (előzetes verzió)](maintenance-window.md).
- További információ: karbantartási időszak – [Gyakori kérdések](maintenance-window-faq.yml).
- A teljesítmény optimalizálásával kapcsolatos további tudnivalókért lásd: [monitorozás és teljesítmény finomhangolása Azure SQL Database és az Azure SQL felügyelt példányain](monitor-tune-overview.md).

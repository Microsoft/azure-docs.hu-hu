---
title: Karbantartási időszak konfigurálása (előzetes verzió)
description: Megtudhatja, hogyan állíthatja be azt az időt, amikor tervezett karbantartást kell végrehajtania a Azure SQL adatbázisokon, rugalmas készleteken és felügyeltpéldány-adatbázisokon.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/23/2021
ms.openlocfilehash: 9771c68dda6f457586f27ea45fbc52aa118e8006
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874782"
---
# <a name="configure-maintenance-window-preview"></a>Karbantartási időszak konfigurálása (előzetes verzió)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


Konfigurálja a karbantartási [ablakot (előzetes verzió)](maintenance-window.md) egy Azure SQL-adatbázishoz, rugalmas készlethez vagy Azure SQL Managed Instance-adatbázishoz az erőforrás létrehozása során vagy az erőforrás létrehozása után bármikor. 

A *rendszer alapértelmezett* karbantartási időszaka naponta 17:00-tól 20:00-ig tart (az erőforrás az Azure-régió helyi ideje), hogy elkerülje a csúcsidők megszakítását. Ha a *Rendszer alapértelmezett* karbantartási időszaka nem a legjobb időpont, válassza ki a többi rendelkezésre álló karbantartási időszak valamelyikét.

A különböző karbantartási időszakra való váltás nem érhető el minden szolgáltatási szinten vagy minden régióban. További információ a rendelkezésre állásról: [Karbantartási időszak rendelkezésre állása.](maintenance-window.md#availability)

> [!Important]
> A karbantartási időszak konfigurálása egy hosszú ideig futó aszinkron művelet, hasonlóan ahhoz, mint az erőforrás szolgáltatási Azure SQL módosítása. Az erőforrás a művelet során érhető el, kivéve a művelet végén lekért rövid újrakonfigurálást, amely általában akár 8 másodpercig is tart, még akkor is, ha a hosszú ideig futó tranzakciók megszakadnak. Az újrakonfigurálás hatásának minimalizálása érdekében a műveletet a csúcsidőn kívül kell végrehajtania.

## <a name="configure-maintenance-window-during-database-creation"></a>Karbantartási időszak konfigurálása az adatbázis létrehozása során 

# <a name="portal"></a>[Portál](#tab/azure-portal)

Az adatbázis, rugalmas készlet vagy felügyelt példány létrehozásakor a karbantartási  időszak konfigurálához állítsa be a kívánt Karbantartási ablakot a **További beállítások lapon.** 

## <a name="set-the-maintenance-window-while-creating-a-single-database-or-elastic-pool"></a>Karbantartási időszak beállítása egyetlen adatbázis vagy rugalmas készlet létrehozása során

Az új adatbázisok vagy készlet létrehozásával kapcsolatos részletes információkért lásd: Create an Azure SQL Database single database (Adatbázis [létrehozása)](single-database-create-quickstart.md).

   :::image type="content" source="media/maintenance-window-configure/additional-settings.png" alt-text="Adatbázis további beállításainak létrehozása lap":::


## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>A karbantartási időszak beállítása felügyelt példány létrehozása során

Az új felügyelt példányok létrehozásával kapcsolatos részletes információkért lásd: Create [an Azure SQL Managed Instance.](../managed-instance/instance-create-quickstart.md)

   :::image type="content" source="media/maintenance-window-configure/additional-settings-mi.png" alt-text="Felügyelt példány létrehozása – további beállítások lap":::




# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az alábbi példák bemutatják, hogyan konfigurálhatja a karbantartási időszak Azure PowerShell. Telepítheti [a Azure PowerShell,](/powershell/azure/install-az-ps)vagy használhatja a Azure Cloud Shell.

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell elindítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A böngészőlapot Cloud Shell böngészőlapon is elindíthatja a következő gombra: [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.


## <a name="discover-available-maintenance-windows"></a>Az elérhető karbantartási ablakok felderítése

A karbantartási időszak beállításakor minden régió saját karbantartási időszakokkal rendelkezik, amelyek az adatbázis vagy készlet régiójának időzónának felelnek meg. 

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>A SQL Database és a rugalmas készlet karbantartási időszakának felderítése 

Az alábbi példa az *eastus2* régió elérhető karbantartási időszakát adja vissza a [Get-AzMaintenancePublicConfiguration](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) parancsmag használatával. Adatbázisok és rugalmas készletek számára állítsa a `MaintenanceScope` `SQLDB` beállítását.

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLDB"}
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>A SQL Managed Instance karbantartási időszak felderítése 

Az alábbi példa az *eastus2* régió elérhető karbantartási időszakát adja vissza a [Get-AzMaintenancePublicConfiguration](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) parancsmag használatával. Felügyelt példányok esetén állítsa a `MaintenanceScope` beállítását a `SQLManagedInstance` beállításra.

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLManagedInstance"}
   ```


## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>Karbantartási időszak beállítása egyetlen adatbázis létrehozásakor

Az alábbi példa létrehoz egy új adatbázist, és beállítja a karbantartási ablakot a [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) parancsmag használatával. A értékét érvényes értékre kell állítani az adatbázis `-MaintenanceConfigurationId` régiójához. A régióra vonatkozó érvényes értékekért lásd: [Elérhető karbantartási ablakok felderítése.](#discover-available-maintenance-windows)


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



## <a name="set-the-maintenance-window-while-creating-an-elastic-pool"></a>A karbantartási időszak beállítása rugalmas készlet létrehozása során

Az alábbi példa létrehoz egy új rugalmas készletet, és beállítja a karbantartási ablakot a [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) parancsmaggal. A karbantartási időszak a rugalmas készletre van beállítva, így a készletben található összes adatbázishoz tartozik a készlet karbantartási időszakának ütemezése. A értékét érvényes értékre kell állítani a készlet `-MaintenanceConfigurationId` régiójához. A régióra vonatkozó érvényes értékekért lásd: [Elérhető karbantartási ablakok felderítése.](#discover-available-maintenance-windows)


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

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Karbantartási időszak beállítása felügyelt példány létrehozása során

Az alábbi példa egy új felügyelt példányt hoz létre, és beállítja a karbantartási ablakot a [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) parancsmag használatával. A karbantartási időszak be van állítva a példányon, így a példányban található összes adatbázishoz meg van állítva a példány karbantartási időszakának ütemezése. Esetén a `-MaintenanceConfigurationId` *MaintenanceConfigName* tulajdonságnak érvényes értéknek kell lennie a példány régiójában. A régióra vonatkozó érvényes értékekért lásd: [Elérhető karbantartási ablakok felderítése.](#discover-available-maintenance-windows)


   ```powershell
   New-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -Location "your_mi_location" `
     -SubnetId /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[Parancssori felület](#tab/azure-cli)

Az alábbi példák bemutatják, hogyan konfigurálhatja a karbantartási időszakokat az Azure CLI használatával. Telepítheti [az Azure CLI-t,](/cli/azure/install-azure-cli)vagy használhatja a Azure Cloud Shell. 

A karbantartási időszak Azure CLI-val való konfigurálása csak a SQL Managed Instance.

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell elindítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A böngészőablakot Cloud Shell böngészőlapon is elindíthatja a következő gombra: [https://shell.azure.com/cli](https://shell.azure.com/cli) . A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

## <a name="discover-available-maintenance-windows"></a>Az elérhető karbantartási ablakok felderítése

A karbantartási időszak beállításakor minden régió saját karbantartási időszakokkal rendelkezik, amelyek az adatbázis vagy készlet régiójának időzónának felelnek meg.

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>A SQL Database és a rugalmas készlet karbantartási időszakának felderítése

Az alábbi példa az [az maintenance public-configuration list](/cli/azure/maintenance/public-configuration#az_maintenance_public_configuration_list) paranccsal adja vissza az *eastus2* régió elérhető karbantartási időszakát. Adatbázisokhoz és rugalmas készletekhez állítsa a `maintenanceScope` (Beállítás) `SQLDB` adatokat.

   ```azurecli
   location="eastus2"

   az maintenance public-configuration list --query "[?location=='$location'&&contains(maintenanceScope,'SQLDB')]"
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>A SQL Managed Instance időszak felderítése

Az alábbi példa az [az maintenance public-configuration list](/cli/azure/maintenance/public-configuration#az_maintenance_public_configuration_list) paranccsal adja vissza az *eastus2* régió elérhető karbantartási időszakát. Felügyelt példányok esetén állítsa a `maintenanceScope` beállítását a `SQLManagedInstance` beállításra.

   ```azurecli
   az maintenance public-configuration list --query "[?location=='eastus2'&&contains(maintenanceScope,'SQLManagedInstance')]"
   ```

## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>A karbantartási időszak beállítása egyetlen adatbázis létrehozása során

Az alábbi példa létrehoz egy új adatbázist, és beállítja a karbantartási ablakot [az az sql db create paranccsal.](/cli/azure/sql/db#az_sql_db_create) A (vagy ) értéket érvényes értékre kell állítani az adatbázis `--maint-config-id` `-m` régiójához. A régióra vonatkozó érvényes értékekért lásd: [Elérhető karbantartási ablakok felderítése.](#discover-available-maintenance-windows)


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

## <a name="set-the-maintenance-window-while-creating-an-elastic-pool"></a>A karbantartási időszak beállítása rugalmas készlet létrehozása során

Az alábbi példa létrehoz egy új rugalmas készletet, és beállítja a karbantartási ablakot [az az sql elastic-pool create](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create) parancsmaggal. A karbantartási időszak a rugalmas készletre van beállítva, így a készletben található összes adatbázishoz tartozik a készlet karbantartási időszakának ütemezése. A (vagy ) értékét érvényes értékre kell állítani a készlet `--maint-config-id` `-m` régiójához. A régióra vonatkozó érvényes értékekért lásd: [Elérhető karbantartási ablakok felderítése.](#discover-available-maintenance-windows)


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

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Karbantartási időszak beállítása felügyelt példány létrehozása során

Az alábbi példa létrehoz egy új felügyelt példányt, és beállítja a karbantartási ablakot [az az sql mi create parancs használatával.](/cli/azure/sql/mi#az_sql_mi_create) A karbantartási időszak a példányra van beállítva, így a példányban található összes adatbázishoz meg van állítva a példány karbantartási időszakának ütemezése. *A MaintenanceConfigName* tulajdonságnak érvényes értéknek kell lennie a példány régiójában. A régióra vonatkozó érvényes értékekért lásd: [Elérhető karbantartási ablakok felderítése.](#discover-available-maintenance-windows)

   ```azurecli
   az sql mi create -g mygroup -n myinstance -l mylocation -i -u myusername -p mypassword --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}
   ```

-----

## <a name="configure-maintenance-window-for-existing-databases"></a>Karbantartási időszak konfigurálása meglévő adatbázisokhoz


Amikor karbantartási időszakokat alkalmaz egy adatbázisra, előfordulhat, hogy rövid újrakonfigurálás (néhány másodperc) történik, mivel az Azure alkalmazza a szükséges módosításokat.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Az alábbi lépések egy meglévő adatbázis, rugalmas készlet vagy felügyelt példány karbantartási időszakát állítják be a Azure Portal:


## <a name="set-the-maintenance-window-for-an-existing-database-or-elastic-pool"></a>Meglévő adatbázis vagy rugalmas készlet karbantartási időszakának beállítása

1. Keresse meg azt az SQL-adatbázist vagy rugalmas készletet, amely számára be szeretné állítani a karbantartási időszakát.
1. A Beállítások **menüben** válassza a **Karbantartás lehetőséget,** majd válassza ki a kívánt karbantartási ablakot.

   :::image type="content" source="media/maintenance-window-configure/maintenance.png" alt-text="AZ SQL Database karbantartási oldala":::


## <a name="set-the-maintenance-window-for-an-existing-managed-instance"></a>Meglévő felügyelt példány karbantartási időszakának beállítása

1. Keresse meg azt a felügyelt példányt, amely számára be szeretné állítani a karbantartási időszakát.
1. A Beállítások **menüben** válassza a **Karbantartás** lehetőséget, majd válassza ki a kívánt karbantartási ablakot.

   :::image type="content" source="media/maintenance-window-configure/maintenance-mi.png" alt-text="Felügyelt SQL-példány karbantartási oldala":::



# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="set-the-maintenance-window-for-an-existing-database"></a>Meglévő adatbázis karbantartási időszakának beállítása

Az alábbi példa beállítja egy meglévő adatbázis karbantartási időszakát a [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) parancsmag használatával. A értékét érvényes értékre kell állítani az adatbázis `-MaintenanceConfigurationId` régiójához. A régióhoz érvényes értékek lekért értékeivel kapcsolatos információkért lásd: [Elérhető karbantartási ablakok felderítése.](#discover-available-maintenance-windows)

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

## <a name="set-the-maintenance-window-on-an-existing-elastic-pool"></a>Meglévő rugalmas készlet karbantartási időszakának beállítása

Az alábbi példa beállítja egy meglévő rugalmas készlet karbantartási időszakát a [Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool) parancsmaggal. Fontos, hogy az érték érvényes érték legyen a készlet `$maintenanceConfig` régiójában.  Egy régió érvényes értékeinek lekért értékeivel kapcsolatos információkért lásd: [Elérhető karbantartási ablakok felderítése.](#discover-available-maintenance-windows)

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



## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>Meglévő felügyelt példány karbantartási időszakának beállítása

Az alábbi példa beállítja egy meglévő felügyelt példány karbantartási időszakát a [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance) parancsmag használatával. Fontos, hogy az értéknek érvényes értéknek kell lennie a példány `$maintenanceConfig` régiójában.  Egy régió érvényes értékeinek lekért értékeivel kapcsolatos információkért lásd: [Elérhető karbantartási ablakok felderítése.](#discover-available-maintenance-windows)


   ```powershell-interactive
   Set-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[Parancssori felület](#tab/azure-cli)

Az alábbi példák bemutatják, hogyan konfigurálhatja a karbantartási időszakokat az Azure CLI használatával. Telepítheti [az Azure CLI-t,](/cli/azure/install-azure-cli)vagy használhatja a Azure Cloud Shell.

## <a name="set-the-maintenance-window-for-an-existing-database"></a>Meglévő adatbázis karbantartási időszakának beállítása

Az alábbi példa beállítja egy meglévő adatbázis karbantartási időszakát [az az sql db update paranccsal.](/cli/azure/sql/db#az_sql_db_update) A (vagy ) értéket érvényes értékre kell állítani az adatbázis `--maint-config-id` `-m` régiójához. A régióra vonatkozó érvényes értékekért lásd: [Elérhető karbantartási ablakok felderítése.](#discover-available-maintenance-windows)

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

## <a name="set-the-maintenance-window-on-an-existing-elastic-pool"></a>Meglévő rugalmas készlet karbantartási időszakának beállítása

Az alábbi példa beállítja egy meglévő rugalmas készlet karbantartási időszakát az [az sql elastic-pool update paranccsal.](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) Fontos, hogy az érték érvényes érték legyen a készlet `maintenanceConfig` régiójában.  Egy régió érvényes értékeinek lekért értékeivel kapcsolatos információkért lásd: [Elérhető karbantartási ablakok felderítése.](#discover-available-maintenance-windows)

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

## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>Meglévő felügyelt példány karbantartási időszakának beállítása

Az alábbi példa beállítja a karbantartási ablakot [az az sql mi update parancs használatával.](/cli/azure/sql/mi#az_sql_mi_update) A karbantartási időszak a példányra van beállítva, így a példányban található összes adatbázishoz meg van állítva a példány karbantartási időszakának ütemezése. Esetén `-MaintenanceConfigurationId` a *MaintenanceConfigName* tulajdonságnak érvényes értéknek kell lennie a példány régiójában. A régióra vonatkozó érvényes értékekért lásd: [Elérhető karbantartási ablakok felderítése.](#discover-available-maintenance-windows)

   ```azurecli
   az sql mi update -g mygroup  -n myinstance -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MainteanceConfigName}
   ```

-----

## <a name="cleanup-resources"></a>Az erőforrások eltávolítása

A szükségtelen díjak elkerülése érdekében mindenképpen törölje a felesleges erőforrásokat, miután végzett velük.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Keresse meg azt az SQL-adatbázist vagy rugalmas készletet, amelyre már nincs szüksége.
1. Az Áttekintés **menüben** válassza az erőforrás törlésére vonatkozó lehetőséget.


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

- További információ a karbantartási időszakról: [Karbantartási időszak (előzetes verzió)](maintenance-window.md).
- További információ: Karbantartási [időszak – gyakori kérdések.](maintenance-window-faq.yml)
- További információ a teljesítmény optimalizálásáról: Monitorozás és teljesítmény-finomhangolás a Azure SQL Database [és Azure SQL Managed Instance.](monitor-tune-overview.md)

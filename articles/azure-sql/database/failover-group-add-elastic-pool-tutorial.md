---
title: 'Oktatóanyag: rugalmas készlet hozzáadása feladatátvételi csoporthoz'
description: A Azure Portal, a PowerShell vagy az Azure CLI használatával hozzáadhat egy Azure SQL Database rugalmas készletet egy feladatátvételi csoporthoz.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: seo-lt-2019 sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/27/2019
ms.openlocfilehash: cdbc44158de2f24d7d33d68311979c3b8bdda85d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94593977"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Oktatóanyag: Azure SQL Database rugalmas készlet hozzáadása egy feladatátvételi csoporthoz
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Feladatátvevő csoport konfigurálása Azure SQL Database rugalmas készlethez és feladatátvételi teszt a Azure Portal használatával.  Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
>
> - Hozzon létre egy önálló adatbázist.
> - Adja hozzá az adatbázist egy rugalmas készlethez.
> - Hozzon létre egy [feladatátvételi csoportot](auto-failover-group-overview.md) két rugalmas készlethez két kiszolgáló között.
> - Feladatátvételi teszt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következőkkel:

- Azure-előfizetés. Ha még nem rendelkezik ilyennel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .

## <a name="1---create-a-single-database"></a>1 – önálló adatbázis létrehozása

[!INCLUDE [sql-database-create-single-database](../includes/sql-database-create-single-database.md)]

## <a name="2---add-the-database-to-an-elastic-pool"></a>2 – az adatbázis hozzáadása rugalmas készlethez

Ebben a lépésben létrehoz egy rugalmas készletet, és hozzáadja az adatbázishoz.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Hozzon létre rugalmas készletet a Azure Portal használatával.

1. Válassza az **Azure SQL** lehetőséget a Azure Portal bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás** lehetőséget, majd írja be az "Azure SQL" kifejezést a keresőmezőbe. Választható Válassza ki az **Azure SQL** melletti csillagot a kedvencekhez, és adja hozzá elemként a bal oldali navigációs sávon.
1. Válassza a **+ Hozzáadás** lehetőséget az **SQL-telepítés kiválasztása** lap megnyitásához. A különböző adatbázisokkal kapcsolatos további információkat az adatbázisok csempén a Részletek megjelenítése lehetőség kiválasztásával tekintheti meg.
1. Az **SQL-adatbázisok** csempén válassza a **rugalmas készlet** lehetőséget az **Erőforrás típusa** legördülő menüből. A rugalmas készlet létrehozásához válassza a **Létrehozás** lehetőséget.

    ![Rugalmas készlet kiválasztása](./media/failover-group-add-elastic-pool-tutorial/select-azure-sql-elastic-pool.png)

1. Állítsa be a rugalmas készletet a következő értékekkel:
   - **Név**: adjon egyedi nevet a rugalmas készletnek, például: `myElasticPool` .
   - **Előfizetés**: válassza ki az előfizetését a legördülő menüből.
   - **ResourceGroup**: válassza ki `myResourceGroup` a legördülő menüből az 1. szakaszban létrehozott erőforráscsoportot.
   - **Kiszolgáló**: válassza ki az 1. szakaszban létrehozott kiszolgálót a legördülő menüből.  

       ![Új kiszolgáló létrehozása rugalmas készlethez](./media/failover-group-add-elastic-pool-tutorial/use-existing-server-for-elastic-pool.png)

   - **Számítás + tárolás**: válassza a **rugalmas készlet beállítása** lehetőséget a számítási és tárolási beállítások konfigurálásához, és adja hozzá az önálló adatbázist a rugalmas készlethez. A **készlet beállításai** lapon hagyja meg az alapértelmezett Gen5, 2 virtuális mag és 32gb értékkel.

1. A **configure (Konfigurálás** ) lapon válassza az **adatbázisok** fület, majd válassza az **adatbázis hozzáadása** elemet. Válassza ki az 1. szakaszban létrehozott adatbázist, majd válassza az **alkalmaz** lehetőséget a rugalmas készlethez való hozzáadásához. A rugalmas készlet beállításainak alkalmazásához és a **configure (Konfigurálás** ) lap bezárásához kattintson ismét az **alkalmaz** gombra.

    ![Adatbázis hozzáadása a rugalmas készlethez](./media/failover-group-add-elastic-pool-tutorial/add-database-to-elastic-pool.png)

1. A rugalmas készlet beállításainak áttekintéséhez válassza a **felülvizsgálat + létrehozás** lehetőséget, majd válassza a **Létrehozás** lehetőséget a rugalmas készlet létrehozásához.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Hozzon létre rugalmas készleteket és másodlagos kiszolgálót a PowerShell használatával.

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   $poolName = "myElasticPool"
   $databaseName = "mySampleDatabase"
   $drLocation = "West US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # The ip address range that you want to allow to access your server
   # Leaving at 0.0.0.0 will prevent outside-of-azure connections
   # $startIp = "0.0.0.0"
   # $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "DR Server name is" $drServerName
   Write-host "Failover group name is" $failoverGroupName

   # Create primary Gen5 elastic 2 vCore pool
   Write-host "Creating elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool

   # Add single db into elastic pool
   Write-host "Creating elastic pool..."
   $addDatabase = Set-AzSqlDatabase -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -DatabaseName $databaseName `
       -ElasticPoolName $poolName
   $addDatabase
   ```

Az oktatóanyag ezen része a következő PowerShell-parancsmagokat használja:

| Parancs | Jegyzetek |
|---|---|
| [Új – AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Rugalmas adatbázis-készletet hoz létre egy Azure SQL Databasehoz.|
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Beállítja egy adatbázis tulajdonságait, vagy áthelyezi egy meglévő adatbázist egy rugalmas készletbe. |

---

## <a name="3---create-the-failover-group"></a>3 – a feladatátvételi csoport létrehozása

Ebben a lépésben létre fog hozni egy [feladatátvételi csoportot](auto-failover-group-overview.md) egy meglévő kiszolgáló és egy másik régió egy új kiszolgálója között. Ezután adja hozzá a rugalmas készletet a feladatátvételi csoporthoz.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Hozza létre a feladatátvételi csoportot a Azure Portal használatával.

1. Válassza az **Azure SQL** lehetőséget a [Azure Portal](https://portal.azure.com)bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás** lehetőséget, majd írja be az Azure SQL kifejezést a keresőmezőbe. Választható Válassza ki az **Azure SQL** melletti csillagot a kedvencekhez, és adja hozzá elemként a bal oldali navigációs sávon.
1. Válassza ki az előző szakaszban létrehozott rugalmas készletet, például: `myElasticPool` .
1. Az **Áttekintés** ablaktáblán válassza ki a kiszolgáló nevét a kiszolgáló **neve** alatt a kiszolgáló beállításainak megnyitásához.
  
    ![Rugalmas készlet kiszolgálójának megnyitása](./media/failover-group-add-elastic-pool-tutorial/server-for-elastic-pool.png)

1. Válassza a **Beállítások** ablaktábla **feladatátvételi csoportok** elemét, majd válassza a **Csoport hozzáadása** lehetőséget egy új feladatátvételi csoport létrehozásához.

    ![Új feladatátvételi csoport hozzáadása](./media/failover-group-add-elastic-pool-tutorial/elastic-pool-failover-group.png)

1. A **feladatátvételi csoport** lapon adja meg vagy válassza ki a következő értékeket, majd válassza a **Létrehozás** lehetőséget:
    - **Feladatátvételi csoport neve**: adjon meg egy egyedi feladatátvételi csoport nevét (például `failovergrouptutorial` ).
    - **Másodlagos kiszolgáló**: válassza a *szükséges beállítások konfigurálását* , majd válassza az **új kiszolgáló létrehozása** lehetőséget. Másik lehetőségként már meglévő kiszolgálót is választhat másodlagos kiszolgálóként. Miután megadta a következő értékeket az új másodlagos kiszolgálóhoz, válassza a **kiválasztás** lehetőséget.
        - **Kiszolgáló neve**: írjon be egy egyedi nevet a másodlagos kiszolgálónak, például: `mysqlsecondary` .
        - **Kiszolgáló-rendszergazdai bejelentkezés**: típus `azureuser`
        - **Password (jelszó**): írjon be egy olyan összetett jelszót, amely megfelel a jelszó követelményeinek.
        - **Hely**: válasszon ki egy helyet a legördülő menüből, például: `East US` . Ez a hely nem lehet ugyanazon a helyen, mint az elsődleges kiszolgáló.

       > [!NOTE]
       > A kiszolgáló bejelentkezési és tűzfalbeállítások meg kell egyeznie az elsődleges kiszolgálóval.

       ![Másodlagos kiszolgáló létrehozása a feladatátvételi csoport számára](./media/failover-group-add-elastic-pool-tutorial/create-secondary-failover-server.png)

1. Válassza ki a **csoportban lévő adatbázisokat** , majd válassza ki a 2. szakaszban létrehozott rugalmas készletet. Egy figyelmeztetésnek kell megjelennie, amely arra kéri, hogy hozzon létre egy rugalmas készletet a másodlagos kiszolgálón. Válassza ki a figyelmeztetést, majd kattintson az **OK** gombra a rugalmas készlet létrehozásához a másodlagos kiszolgálón. 

   ![Rugalmas készlet hozzáadása a feladatátvételi csoporthoz](./media/failover-group-add-elastic-pool-tutorial/add-elastic-pool-to-failover-group.png)

1. Válassza a **kiválasztás** elemet a rugalmas készlet beállításainak a feladatátvételi csoportra való alkalmazásához, majd válassza a **Létrehozás** lehetőséget a feladatátvételi csoport létrehozásához. A rugalmas készlet a feladatátvételi csoportba való felvétele automatikusan elindítja a Geo-replikálási folyamatot.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Hozzon létre egy feladatátvételi csoportot a PowerShell használatával.

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary server in the failover region..."
   New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   Write-host "Secondary server =" $drServerName

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary server..."
   New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   Write-host "Firewall configured"

   # Create secondary Gen5 elastic 2 vCore pool
   Write-host "Creating secondary elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $drServerName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool

   # Create a failover group between the servers
   Write-host "Creating failover group..."
   New-AzSqlDatabaseFailoverGroup `
     –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   Write-host "Failover group created successfully."

   # Add elastic pool to the failover group
   Write-host "Enumerating databases in elastic pool...."
   $FailoverGroup = Get-AzSqlDatabaseFailoverGroup `
                    -ResourceGroupName $resourceGroupName `
                    -ServerName $serverName `
                    -FailoverGroupName $failoverGroupName
   $databases = Get-AzSqlElasticPoolDatabase `
                  -ResourceGroupName $resourceGroupName `
                  -ServerName $serverName `
                  -ElasticPoolName $poolName
   Write-host "Adding databases to failover group..."
   $failoverGroup = $failoverGroup | Add-AzSqlDatabaseToFailoverGroup `
                                     -Database $databases
   $failoverGroup
   ```

Az oktatóanyag ezen része a következő PowerShell-parancsmagokat használja:

| Parancs | Jegyzetek |
|---|---|
| [Új – AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Adatbázisokat és rugalmas készleteket futtató kiszolgálót hoz létre. |
| [Új – AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Tűzfalszabály létrehozása egy kiszolgálóhoz. |
| [Új – AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Rugalmas készletet hoz létre egy Azure SQL Databasehoz.|
| [Új – AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Létrehoz egy új feladatátvételi csoportot. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Egy vagy több Azure SQL-adatbázis egy feladatátvételi csoportba való hozzáadására szolgál. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Lekérdezi vagy felsorolja Azure SQL Database feladatátvételi csoportokat. |

---

## <a name="4---test-failover"></a>4 – feladatátvételi teszt

Ebben a lépésben a feladatátvételi csoportot a másodlagos kiszolgálóra fogja felvenni, majd a Azure Portal használatával hajtja végre a feladatokat.

# <a name="portal"></a>[Portál](#tab/azure-portal)

A feladatátvételi csoport feladatátvételi tesztje a Azure Portal használatával.

1. Válassza az **Azure SQL** lehetőséget a [Azure Portal](https://portal.azure.com)bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás** lehetőséget, majd írja be az Azure SQL kifejezést a keresőmezőbe. Választható Válassza ki az **Azure SQL** melletti csillagot a kedvencekhez, és adja hozzá elemként a bal oldali navigációs sávon.
1. Válassza ki az előző szakaszban létrehozott rugalmas készletet, például: `myElasticPool` .
1. A kiszolgáló beállításainak megnyitásához válassza a **kiszolgáló neve alatt lévő** kiszolgáló nevét.

    ![Rugalmas készlet kiszolgálójának megnyitása](./media/failover-group-add-elastic-pool-tutorial/server-for-elastic-pool.png)

1. Válassza a **Beállítások** ablaktábla **feladatátvételi csoportok** elemét, majd válassza ki a 2. szakaszban létrehozott feladatátvételi csoportot.
  
   ![Válassza ki a feladatátvételi csoportot a portálon](./media/failover-group-add-elastic-pool-tutorial/select-failover-group.png)

1. Ellenőrizze, hogy melyik kiszolgáló elsődleges, és melyik kiszolgáló a másodlagos.
1. Válassza a **feladatátvétel** lehetőséget a feladat ablaktáblán a rugalmas készletet tartalmazó feladatátvételi csoport feladatátvételéhez.
1. Válassza az **Igen** lehetőséget arra a figyelmeztetésre, amely értesíti, hogy a TDS-munkamenetek le lesznek választva.

   ![Az adatbázist tartalmazó feladatátvételi csoport feladatátvétele](./media/failover-group-add-elastic-pool-tutorial/failover-sql-db.png)

1. Tekintse át, hogy melyik kiszolgáló elsődleges, melyik kiszolgáló a másodlagos. Ha a feladatátvétel sikeres volt, a két kiszolgálónak felcserélt szerepkörrel kell rendelkeznie.
1. Válassza újra a **feladatátvételt** , hogy a feladatátvételi csoportot vissza lehessen térni az eredeti beállításokhoz.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A feladatátvételi csoport feladatátvételi tesztje a PowerShell használatával.

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Check role of secondary replica
   Write-host "Confirming the secondary server is secondary...."
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole

   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $drServerName
   ```

A feladatátvételi csoportot a másodlagos kiszolgálóra nem lehet felvenni, majd a PowerShell használatával hajtsa végre a feladat-visszavétel.

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Check role of secondary replica
   Write-host "Confirming the secondary server is now primary"
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole

   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $serverName
   ```

Az oktatóanyag ezen része a következő PowerShell-parancsmagokat használja:

| Parancs | Jegyzetek |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Lekérdezi vagy felsorolja Azure SQL Database feladatátvételi csoportokat. |
| [Kapcsoló – AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Azure SQL Database feladatátvételi csoport feladatátvételét hajtja végre. |

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölje az erőforrásokat az erőforráscsoport törlésével.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Navigáljon az erőforráscsoporthoz a [Azure Portal](https://portal.azure.com).
1. Válassza az  **erőforráscsoport törlése** lehetőséget a csoport összes erőforrásának, valamint maga az erőforráscsoport törléséhez.
1. Írja be az erőforráscsoport nevét, a `myResourceGroup` szövegmezőbe, majd kattintson a **Törlés** elemre az erőforráscsoport törléséhez.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az erőforrások tisztítása a PowerShell használatával.

   ```powershell-interactive
   # Set variables for your server and database
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Clean up resources by removing the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

Az oktatóanyag ezen része a következő PowerShell-parancsmagot használja:

| Parancs | Jegyzetek |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Erőforráscsoport eltávolítása |

---

> [!IMPORTANT]
> Ha meg kívánja őrizni az erőforráscsoportot, de törölni szeretné a másodlagos adatbázist, akkor a törlés előtt távolítsa el a feladatátvételi csoportból. Ha egy másodlagos adatbázist töröl a feladatátvételi csoportból való eltávolítása előtt, akkor kiszámíthatatlan viselkedést okozhat.

## <a name="full-script"></a>Teljes szkript

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Új – AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Adatbázisokat és rugalmas készleteket futtató kiszolgálót hoz létre. |
| [Új – AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Tűzfalszabály létrehozása egy kiszolgálóhoz. |
| [Új – AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Létrehoz egy adatbázist. |
| [Új – AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Rugalmas adatbázis-készletet hoz létre egy Azure SQL Databasehoz.|
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Beállítja egy adatbázis tulajdonságait, vagy áthelyezi egy meglévő adatbázist egy rugalmas készletbe. |
| [Új – AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Létrehoz egy új feladatátvételi csoportot. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Egy vagy több adatbázis beolvasása SQL Database. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Egy vagy több Azure SQL-adatbázis egy feladatátvételi csoportba való hozzáadására szolgál. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Lekérdezi vagy felsorolja Azure SQL Database feladatátvételi csoportokat. |
| [Kapcsoló – AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Azure SQL Database feladatátvételi csoport feladatátvételét hajtja végre. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Erőforráscsoport eltávolítása |

# <a name="portal"></a>[Portál](#tab/azure-portal)

Nincsenek elérhető parancsfájlok a Azure Portal számára.

---

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy Azure SQL Database rugalmas készletet adott hozzá egy feladatátvételi csoporthoz, és tesztelte a feladatátvételt. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
>
> - Hozzon létre egy önálló adatbázist.
> - Adja hozzá az adatbázist egy rugalmas készlethez.
> - Hozzon létre egy [feladatátvételi csoportot](auto-failover-group-overview.md) két rugalmas készlethez két kiszolgáló között.
> - Feladatátvételi teszt.

Folytassa a következő oktatóanyaggal, amely bemutatja, hogyan migrálhat a DMS használatával.

> [!div class="nextstepaction"]
> [Oktatóanyag: SQL Server migrálása egy készletezett adatbázisba a DMS használatával](../../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)

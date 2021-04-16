---
title: 'Oktatóanyag: Rugalmas készlet hozzáadása feladatátvételi csoporthoz'
description: Adjon hozzá Azure SQL Database rugalmas készletet egy feladatátvételi csoporthoz a Azure Portal, a PowerShell vagy az Azure CLI használatával.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/27/2019
ms.openlocfilehash: dd0532469cd4390d9a72900dcebc22994239325b
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479151"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Oktatóanyag: Rugalmas Azure SQL Database hozzáadása feladatátvételi csoporthoz
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Konfiguráljon egy feladatátvételi csoportot egy Azure SQL Database készlethez, és tesztelje a feladatátvételt a Azure Portal.  Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
>
> - Hozzon létre egy adatbázist.
> - Adja hozzá az adatbázist egy rugalmas készlethez.
> - Hozzon létre [egy feladatátvételi csoportot](auto-failover-group-overview.md) két rugalmas készlethez két kiszolgáló között.
> - Feladatátvételi teszt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következőkkel:

- Azure-előfizetés. [Hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) ha még nem rendelkezik fiókkal.

## <a name="1---create-a-single-database"></a>1 – Egyetlen adatbázis létrehozása

[!INCLUDE [sql-database-create-single-database](../includes/sql-database-create-single-database.md)]

## <a name="2---add-the-database-to-an-elastic-pool"></a>2 – Az adatbázis hozzáadása egy rugalmas készlethez

Ebben a lépésben egy rugalmas készletet fog létrehozni, és hozzáadja az adatbázist.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Hozza létre a rugalmas készletet a Azure Portal.

1. A **Azure SQL** bal oldali menüjében válassza a Azure Portal. Ha **Azure SQL** nem szerepel a listában, válassza a **Minden** szolgáltatás lehetőséget, majd írja be a "Azure SQL" parancsot a keresőmezőbe. (Nem kötelező) Jelölje ki a  mellette Azure SQL csillagot, és vegye fel elemként a bal oldali navigációs sávon.
1. Válassza **a + Hozzáadás** lehetőséget az SQL üzembe **helyezési lehetőség kiválasztása lap megnyitásához.** A különböző adatbázisokról az Adatbázisok csempe Részletek megjelenítése lehetőségének kiválasztásával további információkat is megtekinthet.
1. Az **SQL Databases (SQL-adatbázisok)** csempe Resource **type** (Erőforrás típusa) legördülő menüjében válassza a Elastic **pool** (Rugalmas készlet) lehetőséget. A **rugalmas készlet** létrehozásához válassza a Létrehozás lehetőséget.

    ![Rugalmas készlet kiválasztása](./media/failover-group-add-elastic-pool-tutorial/select-azure-sql-elastic-pool.png)

1. Konfigurálja a rugalmas készletet a következő értékekkel:
   - **Név:** Adjon egyedi nevet a rugalmas készletnek, `myElasticPool` például: .
   - **Előfizetés:** Válassza ki előfizetését a legördülő menüből.
   - **ResourceGroup:** Válassza `myResourceGroup` ki a legördülő menüből az 1. szakaszban létrehozott erőforráscsoportot.
   - **Kiszolgáló:** Válassza ki az 1. szakaszban létrehozott kiszolgálót a legördülő menüből.  

       ![Új kiszolgáló létrehozása rugalmas készlethez](./media/failover-group-add-elastic-pool-tutorial/use-existing-server-for-elastic-pool.png)

   - **Számítás + tárolás:** Válassza a **Rugalmas készlet konfigurálása** lehetőséget a számítás és a tárolás konfigurálása érdekében, majd adja hozzá az egyetlen adatbázist a rugalmas készlethez. A Készlet **beállításai lapon** hagyja meg az alapértelmezett Gen5 értéket, 2 virtuális maggal és 32 gb-ossal.

1. A **Konfigurálás lapon** válassza az **Adatbázisok** lapot, majd válassza az Adatbázis **hozzáadása lehetőséget.** Válassza ki az 1. szakaszban létrehozott adatbázist, majd válassza az **Alkalmaz** lehetőséget, hogy hozzáadja a rugalmas készlethez. Válassza **ismét az Alkalmaz** lehetőséget a rugalmas készlet beállításainak alkalmazáshoz, és zárja be a **Konfigurálás** lapot.

    ![Adatbázis hozzáadása rugalmas készlethez](./media/failover-group-add-elastic-pool-tutorial/add-database-to-elastic-pool.png)

1. Válassza **az Áttekintés + létrehozás lehetőséget** a rugalmas készlet beállításainak áttekintéséhez, majd válassza a Létrehozás lehetőséget a rugalmas készlet létrehozásához. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Hozza létre a rugalmas készleteket és a másodlagos kiszolgálót a PowerShell használatával.

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
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Rugalmas adatbáziskészletet hoz létre egy Azure SQL Database.|
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Beállítja egy adatbázis tulajdonságait, vagy áthelyez egy meglévő adatbázist egy rugalmas készletbe. |

---

## <a name="3---create-the-failover-group"></a>3 – A feladatátvételi csoport létrehozása

Ebben a lépésben egy feladatátvételi csoportot hoz létre [egy](auto-failover-group-overview.md) meglévő kiszolgáló és egy másik régióban lévő új kiszolgáló között. Ezután adja hozzá a rugalmas készletet a feladatátvételi csoporthoz.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Hozza létre a feladatátvételi csoportot a Azure Portal.

1. A **Azure SQL** bal oldali menüjében válassza a Azure Portal [lehetőséget.](https://portal.azure.com) Ha **Azure SQL** nem szerepel a listában, válassza a **Minden** szolgáltatás lehetőséget, majd írja Azure SQL a keresőmezőbe. (Nem kötelező) Jelölje ki a  mellette Azure SQL csillagot, és vegye fel elemként a bal oldali navigációs sávon.
1. Válassza ki az előző szakaszban létrehozott rugalmas készletet, `myElasticPool` például: .
1. Az Áttekintés **panelen** válassza ki a kiszolgáló nevét a **Kiszolgálónév** alatt a kiszolgáló beállításainak megnyitásához.
  
    ![Kiszolgáló megnyitása rugalmas készlethez](./media/failover-group-add-elastic-pool-tutorial/server-for-elastic-pool.png)

1. A **Beállítások panelen** válassza a **Feladatátvételi** csoportok lehetőséget, majd új feladatátvételi csoport létrehozásához válassza a Csoport hozzáadása lehetőséget. 

    ![Új feladatátvételi csoport hozzáadása](./media/failover-group-add-elastic-pool-tutorial/elastic-pool-failover-group.png)

1. A **Feladatátvételi csoport lapon** adja meg vagy válassza ki a következő értékeket, majd válassza a Létrehozás **lehetőséget:**
    - **Feladatátvételi csoport neve:** Adjon meg egy egyedi feladatátvételi csoportnevet, `failovergrouptutorial` például: .
    - **Másodlagos kiszolgáló:** Válassza a kötelező beállítások *konfigurálható beállítását,* majd válassza az **Új kiszolgáló létrehozása lehetőséget.** Másik lehetőségként választhat egy már meglévő kiszolgálót is másodlagos kiszolgálóként. Miután megadta az alábbi értékeket az új másodlagos kiszolgálóhoz, válassza a **Kijelölés lehetőséget.**
        - **Kiszolgáló neve:** Adjon meg egy egyedi nevet a másodlagos kiszolgáló számára, `mysqlsecondary` például: .
        - **Kiszolgáló-rendszergazdai bejelentkezés:** Írja be a következőt: `azureuser`
        - **Jelszó:** Adjon meg egy olyan összetett jelszót, amely megfelel a jelszóra vonatkozó követelményeknek.
        - **Hely:** Válasszon egy helyet a legördülő menüből, `East US` például: . Ez a hely nem lehet ugyanaz a hely, mint az elsődleges kiszolgáló.

       > [!NOTE]
       > A kiszolgáló bejelentkezési és tűzfalbeállításának egyeznie kell az elsődleges kiszolgáló beállításaival.

       ![Másodlagos kiszolgáló létrehozása a feladatátvételi csoporthoz](./media/failover-group-add-elastic-pool-tutorial/create-secondary-failover-server.png)

1. Válassza **a csoportban található Adatbázisok lehetőséget,** majd a 2. szakaszban létrehozott rugalmas készletet. Megjelenik egy figyelmeztetés, amely arra kéri, hogy hozzon létre egy rugalmas készletet a másodlagos kiszolgálón. Válassza ki a figyelmeztetést, majd kattintson az **OK gombra** a rugalmas készlet létrehozásához a másodlagos kiszolgálón. 

   ![Rugalmas készlet hozzáadása feladatátvételi csoporthoz](./media/failover-group-add-elastic-pool-tutorial/add-elastic-pool-to-failover-group.png)

1. Válassza **a Kijelölés** lehetőséget a rugalmas készlet beállításainak  a feladatátvételi csoportra való alkalmazáshoz, majd válassza a Létrehozás lehetőséget a feladatátvételi csoport létrehozásához. A rugalmas készlet feladatátvételi csoporthoz való hozzáadása automatikusan elindítja a georeplikációs folyamatot.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Hozza létre a feladatátvételi csoportot a PowerShell használatával.

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
     â€“ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      â€“FailoverGroupName $failoverGroupName `
      â€“FailoverPolicy Automatic `
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
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Létrehoz egy adatbázisokat és rugalmas készleteket tartalmazó kiszolgálót. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Létrehoz egy tűzfalszabályt egy kiszolgálóhoz. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Rugalmas készletet hoz létre egy Azure SQL Database.|
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Új feladatátvételi csoportot hoz létre. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Hozzáad egy vagy Azure SQL adatbázist egy feladatátvételi csoporthoz. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Lekért vagy listázza Azure SQL Database feladatátvételi csoportokat. |

---

## <a name="4---test-failover"></a>4 – Feladatátvételi teszt

Ebben a lépésben feladatátvételi csoportot fog átveszni a másodlagos kiszolgálóra, majd a feladat-visszavételt a Azure Portal.

# <a name="portal"></a>[Portál](#tab/azure-portal)

A feladatátvételi csoport feladatátvételének tesztelése a Azure Portal.

1. A **Azure SQL** bal oldali menüjében válassza a Azure Portal [lehetőséget.](https://portal.azure.com) Ha **Azure SQL** nem szerepel a listában, válassza a **Minden** szolgáltatás lehetőséget, majd írja be a Azure SQL a keresőmezőbe. (Nem kötelező) Jelölje ki a  mellette Azure SQL csillagot, és vegye fel elemként a bal oldali navigációs sávon.
1. Válassza ki az előző szakaszban létrehozott rugalmas készletet, `myElasticPool` például: .
1. Válassza ki a kiszolgáló nevét a **Kiszolgálónév alatt** a kiszolgáló beállításainak megnyitásához.

    ![Kiszolgáló megnyitása rugalmas készlethez](./media/failover-group-add-elastic-pool-tutorial/server-for-elastic-pool.png)

1. A **Beállítások panelen** válassza a **Feladatátvételi** csoportok lehetőséget, majd válassza ki a 2. szakaszban létrehozott feladatátvételi csoportot.
  
   ![Válassza ki a feladatátvételi csoportot a portálon](./media/failover-group-add-elastic-pool-tutorial/select-failover-group.png)

1. Ellenőrizze, hogy melyik kiszolgáló az elsődleges, és melyik a másodlagos.
1. A **feladatpanelen** válassza a Feladatátvétel lehetőséget a rugalmas készletet tartalmazó feladatátvételi csoport feladatátvételének végrehajtásához.
1. Válassza **az Igen** lehetőséget a figyelmeztetésben, amely arról értesíti, hogy a TDS-munkamenetek megszakadnak.

   ![Az adatbázist tartalmazó feladatátvételi csoport feladatátvétele](./media/failover-group-add-elastic-pool-tutorial/failover-sql-db.png)

1. Ellenőrizze, hogy melyik kiszolgáló elsődleges, melyik a másodlagos. Ha a feladatátvétel sikeres volt, a két kiszolgálónak fel kellett cserélnie a szerepköröket.
1. Válassza **ismét a Feladatátvétel** lehetőséget a feladatátvételi csoport eredeti beállításokba való visszavételéhez.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A feladatátvételi csoport feladatátvételének tesztelése a PowerShell használatával.

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

Feladatátvételi csoport feladatátvétele a másodlagos kiszolgálóra, majd a feladat-visszavétel a PowerShell használatával.

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
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Lekért vagy listázza Azure SQL Database feladatátvételi csoportokat. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Végrehajtja egy feladatátvételi csoport Azure SQL Database feladatátvételi csoportját. |

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölje az erőforrásokat az erőforráscsoport törlésével.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Keresse meg az erőforráscsoportot a [Azure Portal.](https://portal.azure.com)
1. Válassza  **az Erőforráscsoport törlése lehetőséget** a csoport összes erőforrásának, valamint magának az erőforráscsoportnak a törléséhez.
1. Írja be az erőforráscsoport nevét () a szövegmezőbe, majd válassza a Törlés lehetőséget az `myResourceGroup` erőforráscsoport törléséhez. 

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
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Eltávolít egy erőforráscsoportot |

---

> [!IMPORTANT]
> Ha meg szeretné tartani az erőforráscsoportot, de törölni szeretné a másodlagos adatbázist, a törlés előtt távolítsa el a feladatátvételi csoportból. Egy másodlagos adatbázis törlése a feladatátvételi csoportból való eltávolítása előtt kiszámíthatatlan viselkedést okozhat.

## <a name="full-script"></a>Teljes szkript

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Létrehoz egy adatbázisokat és rugalmas készleteket tartalmazó kiszolgálót. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Létrehoz egy tűzfalszabályt egy kiszolgálóhoz. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Létrehoz egy adatbázist. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Rugalmas adatbáziskészletet hoz létre egy Azure SQL Database.|
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Beállítja egy adatbázis tulajdonságait, vagy áthelyez egy meglévő adatbázist egy rugalmas készletbe. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Létrehoz egy új feladatátvételi csoportot. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Lekért egy vagy több adatbázist a SQL Database. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Hozzáad egy vagy Azure SQL adatbázist egy feladatátvételi csoporthoz. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Lekért vagy listázza Azure SQL Database feladatátvételi csoportokat. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Végrehajtja egy feladatátvételi csoport Azure SQL Database feladatátvételi csoportját. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Eltávolít egy erőforráscsoportot |

# <a name="portal"></a>[Portál](#tab/azure-portal)

A parancsprogramhoz nem érhetők el Azure Portal.

---

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban rugalmas készletet adott Azure SQL Database egy feladatátvételi csoporthoz, és tesztelte a feladatátvételt. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
>
> - Hozzon létre egy adatbázist.
> - Adja hozzá az adatbázist egy rugalmas készlethez.
> - Hozzon létre [egy feladatátvételi csoportot](auto-failover-group-overview.md) két rugalmas készlethez két kiszolgáló között.
> - Feladatátvételi teszt.

A következő oktatóanyag a DMS használatával való áttelepítést ismertető oktatóanyagra.

> [!div class="nextstepaction"]
> [Oktatóanyag: Adatbázis SQL Server adatbázisba A DMS használatával](../../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)

---
title: 'Oktatóanyag: adatbázis hozzáadása feladatátvételi csoporthoz'
description: A Azure Portal, a PowerShell vagy az Azure CLI használatával vegyen fel egy adatbázist a Azure SQL Database egy autofeladatátvételi csoportba.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/19/2019
ms.openlocfilehash: 68fa089713c3dd89b4699011ded7d667bca6f73f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102178073"
---
# <a name="tutorial-add-an-azure-sql-database-to-an-autofailover-group"></a>Oktatóanyag: Azure SQL Database hozzáadása egy autofeladatátvételi csoporthoz
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A [feladatátvételi csoport](auto-failover-group-overview.md) egy deklaratív absztrakt réteg, amely lehetővé teszi több földrajzilag replikált adatbázis csoportosítását. Megtudhatja, hogyan konfigurálhat feladatátvételi csoportot egy Azure SQL Databasehoz, és hogyan tesztelheti a feladatátvételt a Azure Portal, a PowerShell vagy az Azure CLI használatával.  Ebből az oktatóanyagból az alábbiakat sajátíthatja el:

> [!div class="checklist"]
>
> - Adatbázis létrehozása Azure SQL Database
> - Hozzon létre egy feladatátvételi csoportot két kiszolgáló között az adatbázishoz.
> - Feladatátvételi teszt.

## <a name="prerequisites"></a>Előfeltételek

# <a name="the-portal"></a>[A portál](#tab/azure-portal)

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következőkkel:

- Azure-előfizetés. Ha még nem rendelkezik ilyennel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbi elemekkel:

- Azure-előfizetés. Ha még nem rendelkezik ilyennel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .
- [Azure PowerShell](/powershell/azure/)

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbi elemekkel:

- Azure-előfizetés. Ha még nem rendelkezik ilyennel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .
- Az [Azure CLI](/cli/azure/install-azure-cli)legújabb verziója.

---

## <a name="1---create-a-database"></a>1 – adatbázis létrehozása

[!INCLUDE [sql-database-create-single-database](../includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2 – a feladatátvételi csoport létrehozása

Ebben a lépésben létrehoz egy [feladatátvételi csoportot](auto-failover-group-overview.md) egy meglévő kiszolgáló és egy másik régió egy új kiszolgálója között. Ezután adja hozzá a mintaadatbázis a feladatátvételi csoporthoz.

# <a name="the-portal"></a>[A portál](#tab/azure-portal)

Hozza létre a feladatátvételi csoportot, és adja hozzá az adatbázist a Azure Portal használatával.

1. Válassza az **Azure SQL** lehetőséget a [Azure Portal](https://portal.azure.com)bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás** lehetőséget, majd írja be az Azure SQL kifejezést a keresőmezőbe. Választható Válassza ki az **Azure SQL** melletti csillagot a kedvencekhez, és adja hozzá elemként a bal oldali navigációs sávon.
1. Válassza ki az 1. szakaszban létrehozott adatbázist, például: `mySampleDatabase` .
1. A feladatátvételi csoportokat a kiszolgáló szintjén lehet konfigurálni. A kiszolgáló beállításainak megnyitásához válassza a **kiszolgáló neve alatt lévő** kiszolgáló nevét.

   ![Kiszolgáló megnyitása adatbázishoz](./media/failover-group-add-single-database-tutorial/open-sql-db-server.png)

1. Válassza a **Beállítások** ablaktábla **feladatátvételi csoportok** elemét, majd válassza a **Csoport hozzáadása** lehetőséget egy új feladatátvételi csoport létrehozásához.

   ![Új feladatátvételi csoport hozzáadása](./media/failover-group-add-single-database-tutorial/sqldb-add-new-failover-group.png)

1. A **feladatátvételi csoport** lapon adja meg vagy válassza ki a következő értékeket, majd válassza a **Létrehozás** lehetőséget:

   - **Feladatátvételi csoport neve**: adjon meg egy egyedi feladatátvételi csoport nevét (például `failovergrouptutorial` ).
   - **Másodlagos kiszolgáló**: válassza a *szükséges beállítások konfigurálását* , majd válassza az **új kiszolgáló létrehozása** lehetőséget. Másik lehetőségként már meglévő kiszolgálót is választhat másodlagos kiszolgálóként. A következő értékek beírása után válassza a **kiválasztás** lehetőséget.
      - **Kiszolgáló neve**: írjon be egy egyedi nevet a másodlagos kiszolgálónak, például: `mysqlsecondary` .
      - **Kiszolgáló-rendszergazdai bejelentkezés**: típus `azureuser`
      - **Password (jelszó**): írjon be egy olyan összetett jelszót, amely megfelel a jelszó követelményeinek.
      - **Hely**: válasszon ki egy helyet a legördülő menüből, például: `East US` . Ez a hely nem lehet ugyanazon a helyen, mint az elsődleges kiszolgáló.

     > [!NOTE]
     > A kiszolgáló bejelentkezési és tűzfalbeállítások meg kell egyeznie az elsődleges kiszolgálóval.

     ![Másodlagos kiszolgáló létrehozása a feladatátvételi csoport számára](./media/failover-group-add-single-database-tutorial/create-secondary-failover-server.png)

   - **Adatbázisok a csoporton belül**: Ha egy másodlagos kiszolgáló van kiválasztva, akkor ez a beállítás fel lesz oldva. Válassza ki a **hozzáadni kívánt adatbázisokat** , majd válassza ki az 1. szakaszban létrehozott adatbázist. Ha hozzáadja az adatbázist a feladatátvételi csoporthoz, a automatikusan elindítja a Geo-replikálási folyamatot.

   ![SQL Database hozzáadása a feladatátvételi csoporthoz](./media/failover-group-add-single-database-tutorial/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Hozza létre a feladatátvételi csoportot, és adja hozzá az adatbázist a PowerShell használatával.

   > [!NOTE]
   > A kiszolgáló bejelentkezési és tűzfalbeállítások meg kell egyeznie az elsődleges kiszolgálóval.

   ```powershell-interactive
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "West US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $databaseName = "mySampleDatabase"
   $drLocation = "East US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # The ip address range that you want to allow to access your server
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "DR Server name is" $drServerName
   Write-host "Failover group name is" $failoverGroupName

   # Create a secondary server in the failover region
   Write-host "Creating a secondary server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary server..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule

   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup

   # Add the database to the failover group
   Write-host "Adding the database to the failover group..."
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..."
   ```

Az oktatóanyag ezen része a következő PowerShell-parancsmagokat használja:

| Parancs | Jegyzetek |
|---|---|
| [Új – AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Létrehoz egy kiszolgálót Azure SQL Database, amely önálló adatbázisokat és rugalmas készleteket üzemeltet. |
| [Új – AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Tűzfalszabály létrehozása Azure SQL Database-kiszolgálóhoz. |
| [Új – AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Létrehoz egy új önálló adatbázist a Azure SQL Databaseban. |
| [Új – AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Új feladatátvételi csoportot hoz létre Azure SQL Databaseban. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Egy vagy több adatbázis beolvasása Azure SQL Database. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Egy vagy több adatbázis beadása egy feladatátvételi csoportba Azure SQL Databaseban. |

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Hozza létre a feladatátvételi csoportot, és adja hozzá az adatbázist az Azure CLI használatával.

   > [!NOTE]
   > A kiszolgáló bejelentkezési és tűzfalbeállítások meg kell egyeznie az elsődleges kiszolgálóval.

   ```azurecli-interactive
   #!/bin/bash
   # set variables
   $failoverLocation = "West US"
   $failoverServer = "failoverServer-$randomIdentifier"
   $failoverGroup = "failoverGroup-$randomIdentifier"

   echo "Creating a secondary server in the DR region..."
   az sql server create --name $failoverServer --resource-group $resourceGroup --location $failoverLocation --admin-user $login --admin-password $password

   echo "Creating a failover group between the two servers..."
   az sql failover-group create --name $failoverGroup --partner-server $failoverServer --resource-group $resourceGroup --server $server --add-db $database --failover-policy Automatic
   ```

Az oktatóanyag ezen része a következő Azure CLI-parancsmagokat használja:

| Parancs | Jegyzetek |
|---|---|
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Adatbázisokat és rugalmas készleteket futtató kiszolgálót hoz létre. |
| [az SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule) | Létrehoz egy kiszolgáló tűzfalszabály-szabályait. |
| [az SQL feladatátvétel-csoport létrehozása](/cli/azure/sql/failover-group#az-sql-failover-group-create) | Létrehoz egy feladatátvételi csoportot. |

---

## <a name="3---test-failover"></a>3 – feladatátvételi teszt

Ebben a lépésben a feladatátvételi csoportot átadja a másodlagos kiszolgálónak, majd visszaadja a feladatokat a Azure Portal használatával.

# <a name="the-portal"></a>[A portál](#tab/azure-portal)

Feladatátvételi teszt a Azure Portal használatával.

1. Válassza az **Azure SQL** lehetőséget a [Azure Portal](https://portal.azure.com)bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás** lehetőséget, majd írja be az Azure SQL kifejezést a keresőmezőbe. Választható Válassza ki az **Azure SQL** melletti csillagot a kedvencekhez, és adja hozzá elemként a bal oldali navigációs sávon.
1. Válassza ki a 2. szakaszban létrehozott adatbázist, például: `mySampleDatbase` .
1. A kiszolgáló beállításainak megnyitásához válassza a **kiszolgáló neve alatt lévő** kiszolgáló nevét.

   ![Kiszolgáló megnyitása adatbázishoz](./media/failover-group-add-single-database-tutorial/open-sql-db-server.png)

1. Válassza a **Beállítások** ablaktábla **feladatátvételi csoportok** elemét, majd válassza ki a 2. szakaszban létrehozott feladatátvételi csoportot.
  
   ![Válassza ki a feladatátvételi csoportot a portálon](./media/failover-group-add-single-database-tutorial/select-failover-group.png)

1. Ellenőrizze, hogy melyik kiszolgáló elsődleges, és melyik kiszolgáló a másodlagos.
1. Válassza a **feladatátvétel** lehetőséget a feladat ablaktáblán a minta-adatbázist tartalmazó feladatátvételi csoport feladatátvételéhez.
1. Válassza az **Igen** lehetőséget arra a figyelmeztetésre, amely értesíti, hogy a TDS-munkamenetek le lesznek választva.

   ![Az adatbázist tartalmazó feladatátvételi csoport feladatátvétele](./media/failover-group-add-single-database-tutorial/failover-sql-db.png)

1. Tekintse át, hogy melyik kiszolgáló legyen az elsődleges, és melyik kiszolgáló a másodlagos. Ha a feladatátvétel sikeres volt, a két kiszolgálónak felcserélt szerepkörrel kell rendelkeznie.
1. Válassza újra a **feladatátvételt** , hogy a kiszolgálók vissza tudják térni az eredeti szerepköreiknek.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Feladatátvételi teszt a PowerShell használatával.

Vizsgálja meg a másodlagos replika szerepkörét:

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...."
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

Feladatátvétel a másodlagos kiszolgálóra:

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to" $drServerName
   ```

A feladatátvételi csoport visszaállítása az elsődleges kiszolgálóra:

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully back to" $serverName
   ```

Az oktatóanyag ezen része a következő PowerShell-parancsmagokat használja:

| Parancs | Jegyzetek |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Lekérdezi vagy felsorolja Azure SQL Database feladatátvételi csoportokat. |
| [Kapcsoló – AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Azure SQL Database feladatátvételi csoport feladatátvételét hajtja végre. |

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Feladatátvételi teszt az Azure CLI használatával.

Ellenőrizze, hogy melyik kiszolgáló a másodlagos:

   ```azurecli-interactive
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list --server $server --resource-group $resourceGroup
   ```

Feladatátvétel a másodlagos kiszolgálóra:

   ```azurecli-interactive
   echo "Failing over group to the secondary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $failoverServer
   echo "Successfully failed failover group over to" $failoverServer
   ```

A feladatátvételi csoport visszaállítása az elsődleges kiszolgálóra:

   ```azurecli-interactive
   echo "Failing over group back to the primary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   echo "Successfully failed failover group back to" $server
   ```

Az oktatóanyag ezen része a következő Azure CLI-parancsmagokat használja:

| Parancs | Jegyzetek |
|---|---|
| [az SQL feladatátvételi csoport listája](/cli/azure/sql/failover-group#az-sql-failover-group-list) | Felsorolja a kiszolgálók feladatátvételi csoportjait. |
| [az SQL feladatátvétel-csoport beállítása – elsődleges](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Állítsa be a feladatátvételi csoport elsődlegesét úgy, hogy az a jelenlegi elsődleges kiszolgáló összes adatbázisát elvégzi. |

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölje az erőforrásokat az erőforráscsoport törlésével.

# <a name="the-portal"></a>[A portál](#tab/azure-portal)

Törölje az erőforráscsoportot a Azure Portal használatával.

1. Navigáljon az erőforráscsoporthoz a [Azure Portal](https://portal.azure.com).
1. Válassza az  **erőforráscsoport törlése** lehetőséget a csoport összes erőforrásának, valamint maga az erőforráscsoport törléséhez.
1. Írja be az erőforráscsoport nevét, a `myResourceGroup` szövegmezőbe, majd kattintson a **Törlés** elemre az erőforráscsoport törléséhez.  

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Törölje az erőforráscsoportot a PowerShell használatával.

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

Az oktatóanyag ezen része a következő PowerShell-parancsmagokat használja:

| Parancs | Jegyzetek |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Erőforráscsoport eltávolítása |

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Törölje az erőforráscsoportot az Azure CLI használatával.

   ```azurecli-interactive
   echo "Cleaning up resources by removing the resource group..."
   az group delete --name $resourceGroup
   echo "Successfully removed resource group" $resourceGroup
   ```

Az oktatóanyag ezen része a következő Azure CLI-parancsmagokat használja:

| Parancs | Jegyzetek |
|---|---|
| [az group delete](/cli/azure/vm/extension#az-vm-extension-set) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

---

> [!IMPORTANT]
> Ha meg kívánja őrizni az erőforráscsoportot, de törölni szeretné a másodlagos adatbázist, akkor a törlés előtt távolítsa el a feladatátvételi csoportból. Ha egy másodlagos adatbázist töröl a feladatátvételi csoportból való eltávolítása előtt, akkor kiszámíthatatlan viselkedést okozhat.

## <a name="full-scripts"></a>Teljes parancsfájlok

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add database to a failover group")]

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Új – AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Létrehoz egy kiszolgálót, amely önálló adatbázisokat és rugalmas készleteket üzemeltet Azure SQL Databaseban. |
| [Új – AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Tűzfalszabály létrehozása Azure SQL Database-kiszolgálóhoz. |
| [Új – AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Egy új adatbázist hoz létre Azure SQL Databaseban. |
| [Új – AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Új feladatátvételi csoportot hoz létre Azure SQL Databaseban. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Egy vagy több adatbázis beolvasása Azure SQL Database. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Egy vagy több adatbázis beadása egy feladatátvételi csoportba Azure SQL Databaseban. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Lekérdezi vagy listázza a feladatátvételi csoportokat a Azure SQL Databaseban. |
| [Kapcsoló – AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Feladatátvételi csoport feladatátvételét hajtja végre Azure SQL Databaseban. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Eltávolít egy erőforráscsoportot a Azure SQL Databaseban.|

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add database to a failover group")]

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az Account set](/cli/azure/account#az-account-set) | Egy előfizetést állít be az aktuális aktív előfizetésre. |
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Létrehoz egy kiszolgálót, amely önálló adatbázisokat és rugalmas készleteket üzemeltet Azure SQL Databaseban. |
| [az SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule) | Létrehozza a kiszolgálói szintű IP-tűzfalszabályok szabályait a Azure SQL Databaseban. |
| [az sql db create](/cli/azure/sql/db) | Létrehoz egy adatbázist a Azure SQL Databaseban. |
| [az SQL feladatátvétel-csoport létrehozása](/cli/azure/sql/failover-group#az-sql-failover-group-create) | Létrehoz egy feladatátvételi csoportot a Azure SQL Databaseban. |
| [az SQL feladatátvételi csoport listája](/cli/azure/sql/failover-group#az-sql-failover-group-list) | A Azure SQL Database kiszolgálójában lévő feladatátvételi csoportok felsorolása. |
| [az SQL feladatátvétel-csoport beállítása – elsődleges](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Állítsa be a feladatátvételi csoport elsődlegesét úgy, hogy az a jelenlegi elsődleges kiszolgáló összes adatbázisát elvégzi. |
| [az group delete](/cli/azure/vm/extension#az-vm-extension-set) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

# <a name="the-portal"></a>[A portál](#tab/azure-portal)

Nincsenek elérhető parancsfájlok a Azure Portal számára.

---

További Azure SQL Database szkriptek itt találhatók: [Azure PowerShell](powershell-script-content-guide.md) és [Azure CLI](az-cli-script-samples-content-guide.md).

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy Azure SQL Database-adatbázist adott hozzá egy feladatátvételi csoporthoz, és tesztelte a feladatátvételt. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
>
> - Adatbázis létrehozása Azure SQL Database
> - Hozzon létre egy feladatátvételi csoportot két kiszolgáló között az adatbázishoz.
> - Feladatátvételi teszt.

Folytassa a következő oktatóanyaggal, amely bemutatja, hogyan adhat hozzá rugalmas készletet egy feladatátvételi csoporthoz.

> [!div class="nextstepaction"]
> [Oktatóanyag: Azure SQL Database rugalmas készlet hozzáadása egy feladatátvételi csoporthoz](failover-group-add-elastic-pool-tutorial.md)
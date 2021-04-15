---
title: Feladatátvételi csoport konfigurálása
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Megtudhatja, hogyan konfigurálhat automatikus feladatátvételi csoportot egy (egyszeres és készletbe Azure SQL Database) és SQL Managed Instance-hez az Azure Portal, az Azure CLI és a PowerShell használatával.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/14/2019
ms.openlocfilehash: a2f0cb683669aa092493c8080d5e4646cf9706c3
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477934"
---
# <a name="configure-a-failover-group-for-azure-sql-database"></a>Feladatátvételi csoport konfigurálása Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Ez a témakör bemutatja, [](auto-failover-group-overview.md) hogyan konfigurálhat automatikus feladatátvételi csoportot a Azure SQL Database és Azure SQL Managed Instance.

## <a name="single-database"></a>Önálló adatbázis

Hozza létre a feladatátvételi csoportot, és adjon hozzá egy adatbázist a Azure Portal PowerShell használatával.

### <a name="prerequisites"></a>Előfeltételek

Vegye figyelembe a következő előfeltételeket:

- A másodlagos kiszolgáló bejelentkezési és tűzfalbeállításának egyeznie kell az elsődleges kiszolgáló bejelentkezési beállításaival.

### <a name="create-failover-group"></a>Feladatátvételi csoport létrehozása

# <a name="portal"></a>[Portál](#tab/azure-portal)

Hozza létre a feladatátvételi csoportot, és adja hozzá az egyetlen adatbázist a Azure Portal.

1. A **Azure SQL** bal oldali menüjében válassza a Azure Portal [lehetőséget.](https://portal.azure.com) Ha **Azure SQL** nem szerepel a listában, válassza a **Minden** szolgáltatás lehetőséget, majd írja Azure SQL a keresőmezőbe. (Nem kötelező) Jelölje ki a  mellette Azure SQL csillagot, és vegye fel elemként a bal oldali navigációs sávon.
1. Válassza ki a feladatátvételi csoporthoz hozzáadni kívánt adatbázist.
1. A kiszolgáló beállításainak megnyitásához válassza ki a kiszolgáló nevét a **Kiszolgáló** neve alatt.

   ![Kiszolgáló megnyitása egy adatbázishoz](./media/auto-failover-group-configure/open-sql-db-server.png)

1. Válassza **a Feladatátvételi csoportok** lehetőséget  a **Beállítások panelen,** majd válassza a Csoport hozzáadása lehetőséget egy új feladatátvételi csoport létrehozásához.

   ![Új feladatátvételi csoport hozzáadása](./media/auto-failover-group-configure/sqldb-add-new-failover-group.png)

1. A **Feladatátvételi csoport lapon** adja meg vagy válassza ki a szükséges értékeket, majd válassza a **Létrehozás lehetőséget.**

   - **A csoportban található adatbázisok:** Válassza ki a feladatátvételi csoporthoz hozzáadni kívánt adatbázist. Ha hozzáadja az adatbázist a feladatátvételi csoporthoz, a rendszer automatikusan elindítja a georeplikációs folyamatot.

   ![Új SQL Database hozzáadása feladatátvételi csoporthoz](./media/auto-failover-group-configure/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Hozza létre a feladatátvételi csoportot, és adja hozzá az adatbázist a PowerShell használatával.

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer

   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      â€“ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      â€“FailoverGroupName $failoverGroupName `
      â€“FailoverPolicy Automatic `
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

---

### <a name="test-failover"></a>Feladatátvétel tesztelése

A feladatátvételi csoport feladatátvételének tesztelése a Azure Portal Vagy a PowerShell használatával.

# <a name="portal"></a>[Portál](#tab/azure-portal)

A feladatátvételi csoport feladatátvételének tesztelése a Azure Portal.

1. A **Azure SQL** bal oldali menüjében válassza a Azure Portal [lehetőséget.](https://portal.azure.com) Ha **Azure SQL** nem szerepel a listában, válassza a **Minden** szolgáltatás lehetőséget, majd írja be a "Azure SQL" parancsot a keresőmezőbe. (Nem kötelező) Jelölje ki a  mellette Azure SQL csillagot, és vegye fel elemként a bal oldali navigációs sávon.
1. Válassza ki a feladatátvételi csoporthoz hozzáadni kívánt adatbázist.

   ![Kiszolgáló megnyitása egy adatbázishoz](./media/auto-failover-group-configure/open-sql-db-server.png)

1. A **Beállítások panelen** válassza a **Feladatátvételi** csoportok lehetőséget, majd válassza ki az előbb létrehozott feladatátvételi csoportot.
  
   ![Válassza ki a feladatátvételi csoportot a portálon](./media/auto-failover-group-configure/select-failover-group.png)

1. Ellenőrizze, hogy melyik kiszolgáló elsődleges és melyik másodlagos.
1. Válassza **a Feladatátvétel** lehetőséget a feladatpanelen az adatbázist tartalmazó feladatátvételi csoport feladatátvételének végrehajtásához.
1. Válassza **az Igen** lehetőséget a figyelmeztetésben, amely arról értesíti, hogy a TDS-munkamenetek megszakadnak.

   ![Az adatbázist tartalmazó feladatátvételi csoport feladatátvétele](./media/auto-failover-group-configure/failover-sql-db.png)

1. Ellenőrizze, hogy melyik kiszolgáló elsődleges, és melyik másodlagos. Ha a feladatátvétel sikeres volt, a két kiszolgálónak fel kellett cserélnie a szerepköröket.
1. Válassza **ismét a Feladatátvétel** lehetőséget a kiszolgálók eredeti szerepkörbe való visszavételéhez.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A feladatátvételi csoport feladatátvételének tesztelése a PowerShell használatával.  

Ellenőrizze a másodlagos replika szerepét:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

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
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName
   ```

A feladatátvételi csoport visszaáll az elsődleges kiszolgálóra:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to back to" $serverName
   ```

---

> [!IMPORTANT]
> Ha törölnie kell a másodlagos adatbázist, a törlés előtt távolítsa el a feladatátvételi csoportból. Egy másodlagos adatbázis törlése a feladatátvételi csoportból való eltávolítása előtt kiszámíthatatlan viselkedést okozhat.

## <a name="elastic-pool"></a>Rugalmas készlet

Hozza létre a feladatátvételi csoportot, és adjon hozzá egy rugalmas készletet a Azure Portal vagy a PowerShell használatával.  

### <a name="prerequisites"></a>Előfeltételek

Vegye figyelembe a következő előfeltételeket:

- A másodlagos kiszolgáló bejelentkezési és tűzfalbeállításának egyeznie kell az elsődleges kiszolgáló bejelentkezési beállításaival.

### <a name="create-the-failover-group"></a>A feladatátvételi csoport létrehozása

Hozza létre a feladatátvételi csoportot a rugalmas készlethez a Azure Portal PowerShell használatával.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Hozza létre a feladatátvételi csoportot, és adja hozzá a rugalmas készletet a Azure Portal.

1. A **Azure SQL** bal oldali menüjében válassza a Azure Portal [lehetőséget.](https://portal.azure.com) Ha **Azure SQL** nem szerepel a listában, válassza a **Minden** szolgáltatás lehetőséget, majd írja be a "Azure SQL" parancsot a keresőmezőbe. (Nem kötelező) Jelölje ki a  mellette Azure SQL csillagot, és vegye fel elemként a bal oldali navigációs sávon.
1. Válassza ki a feladatátvételi csoporthoz hozzáadni kívánt rugalmas készletet.
1. Az Áttekintés **panelen** válassza ki a kiszolgáló nevét a **Kiszolgálónév** alatt a kiszolgáló beállításainak megnyitásához.
  
   ![Kiszolgáló megnyitása rugalmas készlethez](./media/auto-failover-group-configure/server-for-elastic-pool.png)

1. Válassza **a Feladatátvételi csoportok** lehetőséget  a **Beállítások panelen,** majd válassza a Csoport hozzáadása lehetőséget egy új feladatátvételi csoport létrehozásához.

   ![Új feladatátvételi csoport hozzáadása](./media/auto-failover-group-configure/sqldb-add-new-failover-group.png)

1. A **Feladatátvételi csoport lapon** adja meg vagy válassza ki a szükséges értékeket, majd válassza a **Létrehozás lehetőséget.** Hozzon létre egy új másodlagos kiszolgálót, vagy válasszon ki egy meglévő másodlagos kiszolgálót.

1. Válassza **az Adatbázisok lehetőséget a csoportban,** majd válassza ki a feladatátvételi csoporthoz hozzáadni kívánt rugalmas készletet. Ha a másodlagos kiszolgálón még nem létezik rugalmas készlet, egy figyelmeztetés jelenik meg, amely arra kéri, hogy hozzon létre egy rugalmas készletet a másodlagos kiszolgálón. Válassza ki a figyelmeztetést, majd kattintson **az OK gombra** a rugalmas készlet létrehozásához a másodlagos kiszolgálón.

   ![Rugalmas készlet hozzáadása feladatátvételi csoporthoz](./media/auto-failover-group-configure/add-elastic-pool-to-failover-group.png)

1. Válassza **a Kijelölés** lehetőséget a rugalmas készlet beállításainak  a feladatátvételi csoportra való alkalmazáshoz, majd válassza a Létrehozás lehetőséget a feladatátvételi csoport létrehozásához. A rugalmas készlet feladatátvételi csoporthoz való hozzáadása automatikusan elindítja a georeplikációs folyamatot.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Hozza létre a feladatátvételi csoportot, és adja hozzá a rugalmas készletet a PowerShell használatával.

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $poolName = "myElasticPool"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

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
   Write-host "Databases added to failover group successfully."
  ```

---

### <a name="test-failover"></a>Feladatátvétel tesztelése

Tesztelje a rugalmas készlet feladatátvételét a Azure Portal PowerShell használatával.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Feladatátvételi csoport feladatátvétele a másodlagos kiszolgálóra, majd feladat-visszavétel a Azure Portal.

1. A **Azure SQL** bal oldali menüjében válassza a Azure Portal [lehetőséget.](https://portal.azure.com) Ha **Azure SQL** nem szerepel a listában, válassza a **Minden** szolgáltatás lehetőséget, majd írja be a "Azure SQL" parancsot a keresőmezőbe. (Nem kötelező) Jelölje ki a  mellette Azure SQL csillagot, és vegye fel elemként a bal oldali navigációs sávon.
1. Válassza ki a feladatátvételi csoporthoz hozzáadni kívánt rugalmas készletet.
1. Az Áttekintés **panelen** válassza ki a kiszolgáló nevét a **Kiszolgálónév** alatt a kiszolgáló beállításainak megnyitásához.

   ![Kiszolgáló megnyitása rugalmas készlethez](./media/auto-failover-group-configure/server-for-elastic-pool.png)
1. A **Beállítások panelen** válassza a **Feladatátvételi** csoportok lehetőséget, majd válassza ki a 2. szakaszban létrehozott feladatátvételi csoportot.
  
   ![Válassza ki a feladatátvételi csoportot a portálon](./media/auto-failover-group-configure/select-failover-group.png)

1. Ellenőrizze, hogy melyik kiszolgáló az elsődleges, és melyik a másodlagos.
1. A **feladatpanelen** válassza a Feladatátvétel lehetőséget a rugalmas készletet tartalmazó feladatátvételi csoport feladatátvételének végrehajtásához.
1. Válassza **az Igen** lehetőséget a figyelmeztetésben, amely arról értesíti, hogy a TDS-munkamenetek megszakadnak.

   ![Az adatbázist tartalmazó feladatátvételi csoport feladatátvétele](./media/auto-failover-group-configure/failover-sql-db.png)

1. Ellenőrizze, hogy melyik kiszolgáló elsődleges, melyik a másodlagos. Ha a feladatátvétel sikeres volt, a két kiszolgálónak fel kellett cserélnie a szerepköröket.
1. Válassza **ismét a Feladatátvétel** lehetőséget a feladatátvételi csoport eredeti beállításokba való visszavételéhez.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A feladatátvételi csoport feladatátvételének tesztelése a PowerShell használatával.

Ellenőrizze a másodlagos replika szerepét:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

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
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName
   ```

---

> [!IMPORTANT]
> Ha törölnie kell a másodlagos adatbázist, a törlés előtt távolítsa el a feladatátvételi csoportból. Egy másodlagos adatbázis törlése a feladatátvételi csoportból való eltávolítása előtt kiszámíthatatlan viselkedést okozhat.

## <a name="sql-managed-instance"></a>SQL Managed Instance

Hozzon létre egy feladatátvételi csoportot a Azure SQL Managed Instance két felügyelt példánya között a Azure Portal Vagy a PowerShell használatával.

Konfigurálnia kell az [ExpressRoute-et,](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) vagy létre kell hoznia egy átjárót az egyes átjárók SQL Managed Instance virtuális hálózatához, csatlakoztatnia kell a két átjárót, majd létre kell hoznia a feladatátvételi csoportot. 

Mindkét felügyelt példányt üzembe [helyezheti a párosított régiókban](../../best-practices-availability-paired-regions.md) teljesítménybeli okokból. A földrajzi párosított régiókban található felügyelt példányok teljesítménye sokkal jobb, mint a ki nem fizetett régiókban. 

### <a name="prerequisites"></a>Előfeltételek

Vegye figyelembe a következő előfeltételeket:

- A másodlagos felügyelt példánynak üresnek kell lennie.
- A másodlagos virtuális hálózat alhálózati tartománya nem lehet átfedésben az elsődleges virtuális hálózat alhálózati tartományában.
- A másodlagos felügyelt példány rendezésének és időzónának meg kell egyeznie az elsődleges felügyelt példány rendezésének és időzónának.
- A két átjáró összekapcsolásakor  a megosztott kulcsnak azonosnak kell lennie mindkét kapcsolat esetén.

### <a name="create-primary-virtual-network-gateway"></a>Elsődleges virtuális hálózati átjáró létrehozása

Ha nem konfigurálta az [ExpressRoute-et,](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)létrehozhatja az elsődleges virtuális hálózati átjárót a Azure Portal PowerShell használatával.

> [!NOTE]
> Az átjáró termékváltozata befolyásolja az átviteli sebesség teljesítményét. Ez a cikk egy átjárót helyez üzembe a legapvetőbb SKU-val ( `HwGw1` ). Magasabb termékváltozat (például: `VpnGw3` ) üzembe helyezése a nagyobb átviteli sebesség eléréséhez. Az összes elérhető beállításért lásd: [Átjáró-SKUS](../../vpn-gateway/vpn-gateway-about-vpngateways.md#benchmark) 

# <a name="portal"></a>[Portál](#tab/azure-portal)

Hozza létre az elsődleges virtuális hálózati átjárót a Azure Portal.

1. A [Azure Portal](https://portal.azure.com)válassza ki az elsődleges felügyelt  példány virtuális hálózati erőforrását az erőforráscsoportban.
1. A Beállítások alatt  válassza **az Alhálózatok** lehetőséget, majd adjon hozzá egy új **átjáró-alhálózatot.** Hagyja meg az alapértelmezett értékeket.

   ![Átjáró hozzáadása az elsődleges felügyelt példányhoz](./media/auto-failover-group-configure/add-subnet-gateway-primary-vnet.png)

1. Az alhálózati átjáró létrehozása után válassza **az Erőforrás** létrehozása lehetőséget a bal oldali navigációs panelen, majd írja be a következőt a `Virtual network gateway` keresőmezőbe: . Válassza ki a Microsoft **által közzétett virtuális** hálózati átjáró **erőforrást.**

   ![Új virtuális hálózati átjáró létrehozása](./media/auto-failover-group-configure/create-virtual-network-gateway.png)

1. Töltse ki a szükséges mezőket az elsődleges felügyelt példány átjáró konfigurálához.

   Az alábbi táblázat az elsődleges felügyelt példány átjárója számára szükséges értékeket mutatja be:

    | **Mező** | Érték |
    | --- | --- |
    | **Előfizetés** |  Az előfizetés, amelyben az elsődleges felügyelt példánya van. |
    | **Név** | A virtuális hálózati átjáró neve. |
    | **Régió** | Az a régió, ahol az elsődleges felügyelt példány található. |
    | **Átjáró típusa** | Válassza a **VPN lehetőséget.** |
    | **VPN típusa** | Útvonalalapú **kiválasztása** |
    | **Termékváltozat**| Hagyja meg az alapértelmezett `VpnGw1` értéket. |
    | **Hely**| A másodlagos felügyelt példány és a másodlagos virtuális hálózat helye.   |
    | **Virtuális hálózat**| Válassza ki a másodlagos felügyelt példány virtuális hálózatát. |
    | **Nyilvános IP-cím**| Válassza az **Új létrehozása** lehetőséget. |
    | **Nyilvános IP-cím neve**| Adja meg az IP-cím nevét. |
    | &nbsp; | &nbsp; |

1. A többi értéket hagyja az alapértelmezett értéken, majd válassza az Áttekintés **+** létrehozás lehetőséget a virtuális hálózati átjáró beállításainak áttekintéséhez.

   ![Elsődleges átjáró beállításai](./media/auto-failover-group-configure/settings-for-primary-gateway.png)

1. Válassza **a Létrehozás** lehetőséget az új virtuális hálózati átjáró létrehozásához.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Hozza létre az elsődleges virtuális hálózati átjárót a PowerShell használatával.

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryVnetName = "<Primary-Virtual-Network-Name>"
   $primaryGWName = "<Primary-Gateway-Name>"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000

   # Get the primary virtual network
   $vnet1 = Get-AzVirtualNetwork -Name $primaryVnetName -ResourceGroupName $primaryResourceGroupName
   $primaryLocation = $vnet1.Location

   # Create primary gateway
   Write-host "Creating primary gateway..."
   $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet1
   $gwpip1= New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $primaryResourceGroupName `
            -Location $primaryLocation -AllocationMethod Dynamic
   $gwipconfig1 = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -SubnetId $subnet1.Id -PublicIpAddressId $gwpip1.Id

   $gw1 = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -IpConfigurations $gwipconfig1 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $gw1
   ```

---

### <a name="create-secondary-virtual-network-gateway"></a>Másodlagos virtuális hálózati átjáró létrehozása

Hozza létre a másodlagos virtuális hálózati átjárót a Azure Portal PowerShell használatával.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Ismételje meg az előző szakaszban található lépéseket a virtuális hálózat alhálózatának és átjárónak a másodlagos felügyelt példányhoz való létrehozásához. Töltse ki a másodlagos felügyelt példány átjáróját konfiguráló kötelező mezőket.

Az alábbi táblázat a másodlagos felügyelt példány átjárója számára szükséges értékeket tartalmazza:

   | **Mező** | Érték |
   | --- | --- |
   | **Előfizetés** |  Az előfizetés, amelyben a másodlagos felügyelt példány van. |
   | **Név** | A virtuális hálózati átjáró neve, például `secondary-mi-gateway` . |
   | **Régió** | A régió, ahol a másodlagos felügyelt példány található. |
   | **Átjáró típusa** | Válassza a **VPN lehetőséget.** |
   | **VPN típusa** | Útvonalalapú **kiválasztása** |
   | **Termékváltozat**| Hagyja meg az alapértelmezett `VpnGw1` értéket. |
   | **Hely**| A másodlagos felügyelt példány és a másodlagos virtuális hálózat helye.   |
   | **Virtuális hálózat**| Válassza ki a 2. szakaszban létrehozott virtuális hálózatot, `vnet-sql-mi-secondary` például: . |
   | **Nyilvános IP-cím**| Válassza az **Új létrehozása** lehetőséget. |
   | **Nyilvános IP-cím neve**| Adja meg az IP-cím nevét, `secondary-gateway-IP` például: . |
   | &nbsp; | &nbsp; |

   ![Másodlagos átjáró beállításai](./media/auto-failover-group-configure/settings-for-secondary-gateway.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Hozza létre a másodlagos virtuális hálózati átjárót a PowerShell használatával.

   ```powershell-interactive
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryVnetName = "<Secondary-Virtual-Network-Name>"
   $secondaryGWName = "<Secondary-Gateway-Name>"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000

   # Get the secondary virtual network
   $vnet2 = Get-AzVirtualNetwork -Name $secondaryVnetName -ResourceGroupName $secondaryResourceGroupName
   $secondaryLocation = $vnet2.Location

   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   $subnet2 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet2
   $gwpip2= New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $secondaryResourceGroupName `
            -Location $secondaryLocation -AllocationMethod Dynamic
   $gwipconfig2 = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -SubnetId $subnet2.Id -PublicIpAddressId $gwpip2.Id

   $gw2 = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -IpConfigurations $gwipconfig2 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn

   $gw2
   ```

---

### <a name="connect-the-gateways"></a>Az átjárók csatlakoztatása

Hozzon létre kapcsolatokat a két átjáró között a Azure Portal Vagy a PowerShell használatával.

Két kapcsolatot kell létrehozni – az elsődleges átjáró és a másodlagos átjáró közötti kapcsolatot, majd a másodlagos átjáró és az elsődleges átjáró közötti kapcsolatot.

A két kapcsolathoz használt megosztott kulcsnak minden kapcsolathoz azonosnak kell lennie.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Hozzon létre kapcsolatokat a két átjáró között a Azure Portal.

1. Válassza **az Erőforrás létrehozása lehetőséget a** [Azure Portal.](https://portal.azure.com)
1. A keresőmezőbe írja be a következőt: , majd nyomja le az Enter billentyűt a kereséshez, amely a Microsoft által közzétett `connection` **Kapcsolat** erőforráshoz viszi.
1. A **kapcsolat létrehozásához** válassza a Létrehozás lehetőséget.
1. Az Alapvető **beállítások lapon** válassza ki a következő értékeket, majd kattintson az **OK gombra.**
    1. Válassza `VNet-to-VNet` a Kapcsolat típusa **lehetőséget.**
    1. Válassza ki előfizetését a legördülő listából.
    1. Válassza ki a felügyelt példány erőforráscsoportját a legördülő menüben.
    1. Válassza ki az elsődleges felügyelt példány helyét a legördülő menüből.
1. A Beállítások **lapon válassza** ki vagy adja meg a következő értékeket, majd kattintson az **OK gombra:**
    1. Válassza ki az elsődleges hálózati átjárót az Első virtuális hálózati **átjáróhoz,** `Primary-Gateway` például: .  
    1. Válassza ki a másodlagos hálózati átjárót a Második virtuális hálózati **átjáróhoz,** `Secondary-Gateway` például: .
    1. Jelölje be a Kétirányú kapcsolat létrehozása **melletti jelölőnégyzetet.**
    1. Hagyja meg az alapértelmezett elsődleges kapcsolat nevét, vagy nevezze át egy ön által választott értékre.
    1. Adjon meg egy **megosztott kulcsot (PSK)** a kapcsolathoz, például: `mi1m2psk` .

   ![Átjárókapcsolat létrehozása](./media/auto-failover-group-configure/create-gateway-connection.png)

1. Az Összefoglalás **lapon** tekintse át a kétirányú kapcsolat beállításait, majd kattintson az **OK** gombra a kapcsolat létrehozásához.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Hozzon létre kapcsolatokat a két átjáró között a PowerShell használatával.

   ```powershell-interactive
   $vpnSharedKey = "mi1mi2psk"
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryGWConnection = "<Primary-connection-name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryGWConnection = "<Secondary-connection-name>"
   $secondaryLocation = "<Secondary-Region>"
  
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway"
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $primaryResourceGroupName `
       -VirtualNetworkGateway1 $gw1 -VirtualNetworkGateway2 $gw2 -Location $primaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection

   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway"

   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $secondaryResourceGroupName `
       -VirtualNetworkGateway1 $gw2 -VirtualNetworkGateway2 $gw1 -Location $secondaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

---

### <a name="create-the-failover-group"></a>A feladatátvételi csoport létrehozása

Hozza létre a felügyelt példányok feladatátvételi csoportját a Azure Portal PowerShell használatával.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Hozza létre a felügyelt SQL-példányok feladatátvételi csoportját a Azure Portal.

1. A **Azure SQL** bal oldali menüjében válassza a Azure Portal [lehetőséget.](https://portal.azure.com) Ha **Azure SQL** nem szerepel a listában, válassza a **Minden** szolgáltatás lehetőséget, majd írja Azure SQL a keresőmezőbe. (Nem kötelező) Jelölje ki a  mellette Azure SQL csillagot, és vegye fel elemként a bal oldali navigációs sávon.
1. Válassza ki a feladatátvételi csoporthoz hozzáadni kívánt elsődleges felügyelt példányt.  
1. A **Beállítások alatt** lépjen a Példány feladatátvételi csoportjai **pontra,** majd válassza a Csoport hozzáadása lehetőséget a Példány feladatátvételi **csoport oldalának megnyitásához.** 

   ![Feladatátvételi csoport hozzáadása](./media/auto-failover-group-configure/add-failover-group.png)

1. A Példány **feladatátvételi csoportja lapon** írja be a feladatátvételi csoport nevét, majd válassza ki a másodlagos felügyelt példányt a legördülő menüből. A **feladatátvételi** csoport létrehozásához válassza a Létrehozás lehetőséget.

   ![Feladatátvételi csoport létrehozása](./media/auto-failover-group-configure/create-failover-group.png)

1. A feladatátvételi csoport üzembe helyezésének befejezése után a rendszer visszaveszi a Feladatátvételi **csoport lapra.**

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Hozza létre a felügyelt példányok feladatátvételi csoportját a PowerShell használatával.

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"

   # Create failover group
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $primaryLocation -ResourceGroupName $primaryResourceGroupName -PrimaryManagedInstanceName $primaryManagedInstance `
        -PartnerRegion $secondaryLocation -PartnerManagedInstanceName $secondaryManagedInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

---

### <a name="test-failover"></a>Feladatátvétel tesztelése

A feladatátvételi csoport feladatátvételének tesztelése a Azure Portal Vagy a PowerShell használatával.

# <a name="portal"></a>[Portál](#tab/azure-portal)

A feladatátvételi csoport feladatátvételének tesztelése a Azure Portal.

1. Lépjen a másodlagos _felügyelt_ [](https://portal.azure.com) példányra a Azure Portal majd válassza a **Példány feladatátvételi csoportok lehetőséget** a beállítások alatt.
1. Tekintse át, hogy melyik felügyelt példány az elsődleges, és melyik a másodlagos.
1. Válassza **a Feladatátvétel,** majd az **Igen** lehetőséget a TDS-munkamenetek leválasztottra vonatkozó figyelmeztetésen.

   ![Feladatátvételi csoport feladatátvétele](./media/auto-failover-group-configure/failover-mi-failover-group.png)

1. Tekintse át, hogy melyik a nem az elsődleges példány, és melyik a másodlagos. Ha a feladatátvétel sikeres volt, a két példánynak szerepköröket kellett váltania.

   ![A felügyelt példányok szerepköröket váltottak a feladatátvétel után](./media/auto-failover-group-configure/mi-switched-after-failover.png)

1. Az elsődleges _példánynak_ az  elsődleges szerepkörnek való visszavételhez válassza ismét a Feladatátvétel lehetőséget az új másodlagos felügyelt példányon.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A feladatátvételi csoport feladatátvételének tesztelése a PowerShell használatával.

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"

   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName

   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"

   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName

   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"

   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   ```

---

## <a name="use-private-link"></a>Privát kapcsolat használata

Privát kapcsolat használata lehetővé teszi, hogy logikai kiszolgálót társítson egy adott magánhálózati IP-címhez a virtuális hálózaton és az alhálózaton belül. 

Ha privát kapcsolatot használ a feladatátvételi csoporttal, tegye a következőket:

1. Győződjön meg arról, hogy az elsődleges és másodlagos kiszolgálók párosított [régióban vannak.](../../best-practices-availability-paired-regions.md) 
1. Hozza létre a virtuális hálózatot és az alhálózatot minden régióban az elsődleges és másodlagos kiszolgálók privát végpontjainak úgy való gazdagépeként, hogy ne fedje át egymást az IP-címterek. Az elsődleges virtuális hálózat címtartománya például 10.0.0.0/16, a másodlagos virtuális hálózat címtartománya pedig 10.0.0.1/16. A virtuális hálózati címtartományokkal kapcsolatos további információkért tekintse meg az Azure-beli virtuális hálózatok [tervezésével kapcsolatos blogot.](https://devblogs.microsoft.com/premier-developer/understanding-cidr-notation-when-designing-azure-virtual-networks-and-subnets/)
1. Hozzon [létre egy privát végpontot és egy Azure saját DNS-zónát az elsődleges kiszolgálóhoz.](../../private-link/create-private-endpoint-portal.md#create-a-private-endpoint) 
1. Hozzon létre egy privát végpontot a másodlagos kiszolgálóhoz is, de ezúttal használja újra ugyanazt a saját DNS zónát, amely az elsődleges kiszolgálóhoz lett létrehozva. 
1. A privát kapcsolat létrehozása után létrehozhatja a feladatátvételi csoportot a cikkben korábban ismertetett lépéseket követve. 


## <a name="locate-listener-endpoint"></a>Listener-végpont megkeresve

Miután konfigurálta a feladatátvételi csoportot, frissítse az alkalmazás kapcsolati sztringet a listener végpontra. Így az alkalmazás nem az elsődleges adatbázishoz, a rugalmas készlethez vagy a példányadatbázishoz, hanem a feladatátvételi csoport figyelőjhez csatlakozik. Így nem kell manuálisan frissítenie a kapcsolati sztringet minden alkalommal, amikor az adatbázis-entitás átveszi a feladatát, és a forgalom az aktuálisan elsődleges entitáshoz lesz irányítva.

A figyelési végpont a következő formában van: , és látható a Azure Portal a `fog-name.database.windows.net` feladatátvételi csoport megtekintésekor:

![Feladatátvételi csoport kapcsolati sztringe](./media/auto-failover-group-configure/find-failover-group-connection-string.png)

## <a name="remarks"></a>Megjegyzések

- Egyetlen vagy készletbe készletett adatbázis feladatátvételi csoportjának eltávolítása nem állítja le a replikációt, és nem törli a replikált adatbázist. Manuálisan le kell állítania a georeplikációt, és törölnie kell az adatbázist a másodlagos kiszolgálóról, ha az eltávolítása után egyetlen vagy készletbe készletett adatbázist szeretne hozzáadni egy feladatátvételi csoporthoz. Ha egyiket sem sikerül megtenni, az ahhoz hasonló hibaüzenetet eredményezhet, amikor megpróbálja hozzáadni az adatbázist a `The operation cannot be performed due to multiple errors` feladatátvételi csoporthoz.

## <a name="next-steps"></a>Következő lépések

A feladatátvételi csoportok konfigurálásával kapcsolatos részletes lépésekért tekintse meg a következő oktatóanyagokat:

- [Egyetlen adatbázis hozzáadása feladatátvételi csoporthoz](failover-group-add-single-database-tutorial.md)
- [Rugalmas készlet hozzáadása feladatátvételi csoporthoz](failover-group-add-elastic-pool-tutorial.md)
- [Felügyelt példány hozzáadása feladatátvételi csoporthoz](../managed-instance/failover-group-add-instance-tutorial.md)

A magas rendelkezésre állási Azure SQL Database áttekintését [](active-geo-replication-overview.md) lásd: georeplikáció és automatikus feladatátvételi [csoportok.](auto-failover-group-overview.md)

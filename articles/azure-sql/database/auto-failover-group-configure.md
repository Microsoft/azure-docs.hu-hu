---
title: Feladatátvételi csoport konfigurálása
titleSuffix: Azure SQL Database & SQL Managed Instance
description: A Azure Portal, az Azure CLI és a PowerShell használatával megtudhatja, hogyan konfigurálhat egy automatikus feladatátvételi csoportot egy Azure SQL Databasehoz (az önálló és a készletezett) és az SQL felügyelt példányokhoz.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/14/2019
ms.openlocfilehash: 09bb7cb2344e3e708a64842916e6e483136da3bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94594283"
---
# <a name="configure-a-failover-group-for-azure-sql-database"></a>Feladatátvételi csoport konfigurálása Azure SQL Databasehoz
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Ez a témakör bemutatja, hogyan konfigurálhat egy [automatikus feladatátvételi csoportot](auto-failover-group-overview.md) Azure SQL Database és az Azure SQL felügyelt példányaihoz.

## <a name="single-database"></a>Önálló adatbázis

Hozza létre a feladatátvételi csoportot, és vegyen fel egyetlen adatbázist a Azure Portal vagy a PowerShell használatával.

### <a name="prerequisites"></a>Előfeltételek

Vegye figyelembe a következő előfeltételeket:

- A kiszolgáló bejelentkezési és tűzfalbeállítások a másodlagos kiszolgálónak meg kell egyezniük az elsődleges kiszolgálóval.

### <a name="create-failover-group"></a>Feladatátvételi csoport létrehozása

# <a name="portal"></a>[Portál](#tab/azure-portal)

Hozza létre a feladatátvételi csoportot, és adja hozzá az önálló adatbázisát a Azure Portal használatával.

1. Válassza az **Azure SQL** lehetőséget a [Azure Portal](https://portal.azure.com)bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás** lehetőséget, majd írja be az Azure SQL kifejezést a keresőmezőbe. Választható Válassza ki az **Azure SQL** melletti csillagot a kedvencekhez, és adja hozzá elemként a bal oldali navigációs sávon.
1. Válassza ki a feladatátvételi csoportba felvenni kívánt adatbázist.
1. A kiszolgáló beállításainak megnyitásához válassza a **kiszolgáló neve alatt lévő** kiszolgáló nevét.

   ![Kiszolgáló megnyitása egyetlen adatbázishoz](./media/auto-failover-group-configure/open-sql-db-server.png)

1. Válassza a **Beállítások** ablaktábla **feladatátvételi csoportok** elemét, majd válassza a **Csoport hozzáadása** lehetőséget egy új feladatátvételi csoport létrehozásához.

   ![Új feladatátvételi csoport hozzáadása](./media/auto-failover-group-configure/sqldb-add-new-failover-group.png)

1. A **feladatátvételi csoport** lapon adja meg vagy válassza ki a szükséges értékeket, majd válassza a **Létrehozás** lehetőséget.

   - **Csoportba tartozó adatbázisok**: válassza ki a feladatátvételi csoportba felvenni kívánt adatbázist. Ha hozzáadja az adatbázist a feladatátvételi csoporthoz, a automatikusan elindítja a Geo-replikálási folyamatot.

   ![SQL Database hozzáadása a feladatátvételi csoporthoz](./media/auto-failover-group-configure/add-sqldb-to-failover-group.png)

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

---

### <a name="test-failover"></a>Feladatátvétel tesztelése

A feladatátvételi csoport feladatátvételi tesztje a Azure Portal vagy a PowerShell használatával.

# <a name="portal"></a>[Portál](#tab/azure-portal)

A feladatátvételi csoport feladatátvételi tesztje a Azure Portal használatával.

1. Válassza az **Azure SQL** lehetőséget a [Azure Portal](https://portal.azure.com)bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás** lehetőséget, majd írja be az "Azure SQL" kifejezést a keresőmezőbe. Választható Válassza ki az **Azure SQL** melletti csillagot a kedvencekhez, és adja hozzá elemként a bal oldali navigációs sávon.
1. Válassza ki a feladatátvételi csoportba felvenni kívánt adatbázist.

   ![Kiszolgáló megnyitása egyetlen adatbázishoz](./media/auto-failover-group-configure/open-sql-db-server.png)

1. Válassza a **Beállítások** ablaktábla **feladatátvételi csoportok** elemét, majd válassza ki az imént létrehozott feladatátvételi csoportot.
  
   ![Válassza ki a feladatátvételi csoportot a portálon](./media/auto-failover-group-configure/select-failover-group.png)

1. Ellenőrizze, hogy melyik kiszolgáló elsődleges, és melyik kiszolgáló a másodlagos.
1. Válassza a **feladatátvétel** lehetőséget a feladat ablaktáblán az adatbázist tartalmazó feladatátvételi csoport feladatátvételéhez.
1. Válassza az **Igen** lehetőséget arra a figyelmeztetésre, amely értesíti, hogy a TDS-munkamenetek le lesznek választva.

   ![Az adatbázist tartalmazó feladatátvételi csoport feladatátvétele](./media/auto-failover-group-configure/failover-sql-db.png)

1. Tekintse át, hogy melyik kiszolgáló legyen az elsődleges, és melyik kiszolgáló a másodlagos. Ha a feladatátvétel sikeres volt, a két kiszolgálónak felcserélt szerepkörrel kell rendelkeznie.
1. Válassza újra a **feladatátvételt** , hogy a kiszolgálók vissza tudják térni az eredeti szerepköreiknek.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A feladatátvételi csoport feladatátvételi tesztje a PowerShell használatával.  

Vizsgálja meg a másodlagos replika szerepkörét:

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

A feladatátvételi csoport visszaállítása az elsődleges kiszolgálóra:

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
> Ha törölnie kell a másodlagos adatbázist, a törlése előtt távolítsa el a feladatátvételi csoportból. Ha egy másodlagos adatbázist töröl a feladatátvételi csoportból való eltávolítása előtt, akkor kiszámíthatatlan viselkedést okozhat.

## <a name="elastic-pool"></a>Rugalmas készlet

Hozza létre a feladatátvételi csoportot, és adjon hozzá egy rugalmas készletet a Azure Portal vagy a PowerShell használatával.  

### <a name="prerequisites"></a>Előfeltételek

Vegye figyelembe a következő előfeltételeket:

- A kiszolgáló bejelentkezési és tűzfalbeállítások a másodlagos kiszolgálónak meg kell egyezniük az elsődleges kiszolgálóval.

### <a name="create-the-failover-group"></a>A feladatátvételi csoport létrehozása

Hozza létre a rugalmas készlet feladatátvételi csoportját a Azure Portal vagy a PowerShell használatával.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Hozza létre a feladatátvételi csoportot, és adja hozzá rugalmas készletét a Azure Portal használatával.

1. Válassza az **Azure SQL** lehetőséget a [Azure Portal](https://portal.azure.com)bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás** lehetőséget, majd írja be az "Azure SQL" kifejezést a keresőmezőbe. Választható Válassza ki az **Azure SQL** melletti csillagot a kedvencekhez, és adja hozzá elemként a bal oldali navigációs sávon.
1. Válassza ki a feladatátvevő csoportba felvenni kívánt rugalmas készletet.
1. Az **Áttekintés** ablaktáblán válassza ki a kiszolgáló nevét a kiszolgáló **neve** alatt a kiszolgáló beállításainak megnyitásához.
  
   ![Rugalmas készlet kiszolgálójának megnyitása](./media/auto-failover-group-configure/server-for-elastic-pool.png)

1. Válassza a **Beállítások** ablaktábla **feladatátvételi csoportok** elemét, majd válassza a **Csoport hozzáadása** lehetőséget egy új feladatátvételi csoport létrehozásához.

   ![Új feladatátvételi csoport hozzáadása](./media/auto-failover-group-configure/sqldb-add-new-failover-group.png)

1. A **feladatátvételi csoport** lapon adja meg vagy válassza ki a szükséges értékeket, majd válassza a **Létrehozás** lehetőséget. Hozzon létre egy új másodlagos kiszolgálót, vagy válasszon ki egy meglévő másodlagos kiszolgálót.

1. Válassza ki az **adatbázisokat a csoporton belül** , majd válassza ki azt a rugalmas készletet, amelyet hozzá szeretne adni a feladatátvételi csoporthoz. Ha egy rugalmas készlet még nem létezik a másodlagos kiszolgálón, egy figyelmeztetés jelenik meg, amely arra kéri, hogy hozzon létre egy rugalmas készletet a másodlagos kiszolgálón. Válassza ki a figyelmeztetést, majd kattintson az **OK** gombra a rugalmas készlet létrehozásához a másodlagos kiszolgálón.

   ![Rugalmas készlet hozzáadása a feladatátvételi csoporthoz](./media/auto-failover-group-configure/add-elastic-pool-to-failover-group.png)

1. Válassza a **kiválasztás** elemet a rugalmas készlet beállításainak a feladatátvételi csoportra való alkalmazásához, majd válassza a **Létrehozás** lehetőséget a feladatátvételi csoport létrehozásához. A rugalmas készlet a feladatátvételi csoportba való felvétele automatikusan elindítja a Geo-replikálási folyamatot.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Hozza létre a feladatátvételi csoportot, és adja hozzá rugalmas készletét a PowerShell használatával.

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
   Write-host "Databases added to failover group successfully."
  ```

---

### <a name="test-failover"></a>Feladatátvétel tesztelése

A rugalmas készlet feladatátvételének tesztelése a Azure Portal vagy a PowerShell használatával.

# <a name="portal"></a>[Portál](#tab/azure-portal)

A feladatátvételi csoport feladatátvétele a másodlagos kiszolgálóra történik, majd a Azure Portal használatával visszatérhet.

1. Válassza az **Azure SQL** lehetőséget a [Azure Portal](https://portal.azure.com)bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás** lehetőséget, majd írja be az "Azure SQL" kifejezést a keresőmezőbe. Választható Válassza ki az **Azure SQL** melletti csillagot a kedvencekhez, és adja hozzá elemként a bal oldali navigációs sávon.
1. Válassza ki a feladatátvevő csoportba felvenni kívánt rugalmas készletet.
1. Az **Áttekintés** ablaktáblán válassza ki a kiszolgáló nevét a kiszolgáló **neve** alatt a kiszolgáló beállításainak megnyitásához.

   ![Rugalmas készlet kiszolgálójának megnyitása](./media/auto-failover-group-configure/server-for-elastic-pool.png)
1. Válassza a **Beállítások** ablaktábla **feladatátvételi csoportok** elemét, majd válassza ki a 2. szakaszban létrehozott feladatátvételi csoportot.
  
   ![Válassza ki a feladatátvételi csoportot a portálon](./media/auto-failover-group-configure/select-failover-group.png)

1. Ellenőrizze, hogy melyik kiszolgáló elsődleges, és melyik kiszolgáló a másodlagos.
1. Válassza a **feladatátvétel** lehetőséget a feladat ablaktáblán a rugalmas készletet tartalmazó feladatátvételi csoport feladatátvételéhez.
1. Válassza az **Igen** lehetőséget arra a figyelmeztetésre, amely értesíti, hogy a TDS-munkamenetek le lesznek választva.

   ![Az adatbázist tartalmazó feladatátvételi csoport feladatátvétele](./media/auto-failover-group-configure/failover-sql-db.png)

1. Tekintse át, hogy melyik kiszolgáló elsődleges, melyik kiszolgáló a másodlagos. Ha a feladatátvétel sikeres volt, a két kiszolgálónak felcserélt szerepkörrel kell rendelkeznie.
1. Válassza újra a **feladatátvételt** , hogy a feladatátvételi csoportot vissza lehessen térni az eredeti beállításokhoz.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A feladatátvételi csoport feladatátvételi tesztje a PowerShell használatával.

Vizsgálja meg a másodlagos replika szerepkörét:

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
> Ha törölnie kell a másodlagos adatbázist, a törlése előtt távolítsa el a feladatátvételi csoportból. Ha egy másodlagos adatbázist töröl a feladatátvételi csoportból való eltávolítása előtt, akkor kiszámíthatatlan viselkedést okozhat.

## <a name="sql-managed-instance"></a>SQL Managed Instance

A Azure Portal vagy a PowerShell használatával hozzon létre egy feladatátvételi csoportot a felügyelt Azure SQL-példány két felügyelt példánya között.

Konfigurálnia kell a [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) -t, vagy létre kell hoznia egy átjárót az egyes FELÜGYELt SQL-példányok virtuális hálózatához, össze kell kapcsolnia a két átjárót, majd létre kell hoznia a feladatátvételi csoportot. 

A felügyelt példányokat a [párosított régiók](../../best-practices-availability-paired-regions.md) számára is üzembe helyezheti teljesítménybeli okokból. A földrajzilag párosított régiókban található felügyelt példányok sokkal jobb teljesítményt biztosítanak a nem párosított régiókhoz képest. 

### <a name="prerequisites"></a>Előfeltételek

Vegye figyelembe a következő előfeltételeket:

- A másodlagos felügyelt példánynak üresnek kell lennie.
- A másodlagos virtuális hálózat alhálózati tartománya nem fedi át az elsődleges virtuális hálózat alhálózatának tartományát.
- A másodlagos felügyelt példány rendezésének és időzónájának meg kell egyeznie az elsődleges felügyelt példánysal.
- A két átjáró csatlakoztatásakor a **megosztott kulcsnak** mindkét kapcsolat esetében azonosnak kell lennie.

### <a name="create-primary-virtual-network-gateway"></a>Elsődleges virtuális hálózati átjáró létrehozása

Ha még nem konfigurálta a [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md), az elsődleges virtuális hálózati átjárót a Azure Portal vagy a PowerShell segítségével hozhatja létre.

> [!NOTE]
> Az átjáró SKU-jának hatással van az átviteli teljesítményre. Ez a cikk egy átjárót helyez üzembe a legalapvetőbb SKU ( `HwGw1` ) használatával. Helyezzen üzembe egy magasabb SKU-t (például: `VpnGw3` ) a nagyobb átviteli sebesség eléréséhez. Az összes elérhető lehetőség esetében lásd: [átjáró SKU](../../vpn-gateway/vpn-gateway-about-vpngateways.md#benchmark) -i 

# <a name="portal"></a>[Portál](#tab/azure-portal)

Hozza létre az elsődleges virtuális hálózati átjárót a Azure Portal használatával.

1. A [Azure Portal](https://portal.azure.com)nyissa meg az erőforráscsoportot, és válassza ki a **virtuális hálózati** erőforrást az elsődleges felügyelt példányhoz.
1. Válassza ki az **alhálózatok** lehetőséget a **Beállítások** területen, majd válassza a lehetőséget egy új **átjáró-alhálózat** hozzáadásához. Hagyja meg az alapértelmezett értékeket.

   ![Átjáró hozzáadása az elsődleges felügyelt példányhoz](./media/auto-failover-group-configure/add-subnet-gateway-primary-vnet.png)

1. Az alhálózat-átjáró létrehozása után válassza az **erőforrás létrehozása** lehetőséget a bal oldali navigációs ablaktáblán, majd írja be `Virtual network gateway` a keresőmezőbe a kifejezést. Válassza ki a **Microsoft** által közzétett **Virtual Network Gateway** -erőforrást.

   ![Új virtuális hálózati átjáró létrehozása](./media/auto-failover-group-configure/create-virtual-network-gateway.png)

1. Töltse ki a kötelező mezőket az elsődleges felügyelt példány átjárójának konfigurálásához.

   Az alábbi táblázat az elsődleges felügyelt példány átjárója számára szükséges értékeket tartalmazza:

    | **Mező** | Érték |
    | --- | --- |
    | **Előfizetés** |  Az az előfizetés, amelyben az elsődleges felügyelt példánya. |
    | **Név** | A virtuális hálózati átjáró neve. |
    | **Régió** | Az a régió, ahol az elsődleges felügyelt példánya van. |
    | **Átjáró típusa** | Válassza a **VPN** lehetőséget. |
    | **VPN-típus** | **Útvonal-alapú** |
    | **Termékváltozat**| Hagyja meg az alapértelmezett értéket `VpnGw1` . |
    | **Hely**| A másodlagos felügyelt példány és a másodlagos virtuális hálózat helye.   |
    | **Virtuális hálózat**| Válassza ki a virtuális hálózatot a másodlagos felügyelt példányhoz. |
    | **Nyilvános IP-cím**| Válassza az **Új létrehozása** lehetőséget. |
    | **Nyilvános IP-cím neve**| Adja meg az IP-cím nevét. |
    | &nbsp; | &nbsp; |

1. Hagyja meg a többi értéket alapértelmezettként, majd válassza a **felülvizsgálat + létrehozás** lehetőséget a virtuális hálózati átjáró beállításainak áttekintéséhez.

   ![Elsődleges átjáró beállításai](./media/auto-failover-group-configure/settings-for-primary-gateway.png)

1. Válassza a **Létrehozás** lehetőséget az új virtuális hálózati átjáró létrehozásához.

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

Hozza létre a másodlagos virtuális hálózati átjárót a Azure Portal vagy a PowerShell használatával.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Az előző szakaszban leírt lépések megismétlésével hozza létre a virtuális hálózati alhálózatot és az átjárót a másodlagos felügyelt példányhoz. Adja meg a szükséges mezőket a másodlagos felügyelt példány átjárójának konfigurálásához.

A következő táblázat a másodlagos felügyelt példány átjárója számára szükséges értékeket tartalmazza:

   | **Mező** | Érték |
   | --- | --- |
   | **Előfizetés** |  Az előfizetés, amelyben a másodlagos felügyelt példánya van. |
   | **Név** | A virtuális hálózati átjáró neve, például: `secondary-mi-gateway` . |
   | **Régió** | Az a régió, amelyben a másodlagos felügyelt példány található. |
   | **Átjáró típusa** | Válassza a **VPN** lehetőséget. |
   | **VPN-típus** | **Útvonal-alapú** |
   | **Termékváltozat**| Hagyja meg az alapértelmezett értéket `VpnGw1` . |
   | **Hely**| A másodlagos felügyelt példány és a másodlagos virtuális hálózat helye.   |
   | **Virtuális hálózat**| Válassza ki a 2. szakaszban létrehozott virtuális hálózatot, például: `vnet-sql-mi-secondary` . |
   | **Nyilvános IP-cím**| Válassza az **Új létrehozása** lehetőséget. |
   | **Nyilvános IP-cím neve**| Adja meg az IP-cím nevét, például: `secondary-gateway-IP` . |
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

### <a name="connect-the-gateways"></a>Az átjárók összekötése

Hozzon létre kapcsolatokat a két átjáró között a Azure Portal vagy a PowerShell használatával.

Két kapcsolatot kell létrehoznia: az elsődleges átjáró és a másodlagos átjáró közötti kapcsolat, majd a másodlagos átjáróról az elsődleges átjáró felé irányuló kapcsolat.

Mindkét kapcsolathoz ugyanazt a megosztott kulcsot kell használni, mint az egyes kapcsolatok esetében.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Hozzon létre kapcsolatokat a két átjáró között a Azure Portal használatával.

1. Válassza az **erőforrás létrehozása** lehetőséget a [Azure Portal](https://portal.azure.com).
1. Írja be `connection` a keresőmezőbe, majd nyomja le az ENTER billentyűt a kereséshez, amely a Microsoft által közzétett **kapcsolódási** erőforrásra viszi.
1. Válassza a **Létrehozás** lehetőséget a kapcsolódás létrehozásához.
1. Az **alapvető beállítások** lapon válassza ki a következő értékeket, majd kattintson az **OK gombra**.
    1. Válassza `VNet-to-VNet` a **kapcsolattípus** lehetőséget.
    1. Válassza ki előfizetését a legördülő listából.
    1. Válassza ki a felügyelt példányhoz tartozó erőforráscsoportot a legördülő menüből.
    1. Válassza ki az elsődleges felügyelt példány helyét a legördülő menüből.
1. A **Beállítások** lapon válassza ki vagy adja meg a következő értékeket, majd kattintson az **OK gombra**:
    1. Válassza ki az **első virtuális hálózati átjáró** elsődleges hálózati átjáróját, például: `Primary-Gateway` .  
    1. Válassza ki a **második virtuális hálózati átjáró** másodlagos hálózati átjáróját, például: `Secondary-Gateway` .
    1. Jelölje be a **kétirányú kapcsolat létesítése** melletti jelölőnégyzetet.
    1. Hagyja meg az alapértelmezett elsődleges kapcsolódási nevet, vagy nevezze át tetszőleges értékre.
    1. Adjon meg egy **megosztott kulcsot (PSK)** a kapcsolatban, például: `mi1m2psk` .

   ![Átjáró-kapcsolatok létrehozása](./media/auto-failover-group-configure/create-gateway-connection.png)

1. Az **Összefoglalás** lapon tekintse át a kétirányú kapcsolatok beállításait, majd kattintson **az OK** gombra a kapcsolódás létrehozásához.

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

Hozza létre a felügyelt példányok feladatátvételi csoportját a Azure Portal vagy a PowerShell használatával.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Hozza létre a feladatátvételi csoportot az SQL felügyelt példányaihoz a Azure Portal használatával.

1. Válassza az **Azure SQL** lehetőséget a [Azure Portal](https://portal.azure.com)bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás** lehetőséget, majd írja be az Azure SQL kifejezést a keresőmezőbe. Választható Válassza ki az **Azure SQL** melletti csillagot a kedvencekhez, és adja hozzá elemként a bal oldali navigációs sávon.
1. Válassza ki azt az elsődleges felügyelt példányt, amelyet hozzá szeretne adni a feladatátvételi csoporthoz.  
1. A **Beállítások** területen navigáljon a **példány feladatátvételi csoportok** elemre, majd válassza a **Csoport hozzáadása** lehetőséget a **példány feladatátvételi csoport** lap megnyitásához.

   ![Feladatátvételi csoport hozzáadása](./media/auto-failover-group-configure/add-failover-group.png)

1. A **példány feladatátvételi csoport** lapján írja be a feladatátvételi csoport nevét, majd válassza ki a másodlagos felügyelt példányt a legördülő menüből. Válassza a **Létrehozás** lehetőséget a feladatátvételi csoport létrehozásához.

   ![Feladatátvételi csoport létrehozása](./media/auto-failover-group-configure/create-failover-group.png)

1. A feladatátvételi csoport központi telepítésének befejezése után vissza fog térni a **feladatátvételi csoport** lapra.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Hozzon létre egy feladatátvételi csoportot a felügyelt példányok számára a PowerShell használatával.

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

A feladatátvételi csoport feladatátvételi tesztje a Azure Portal vagy a PowerShell használatával.

# <a name="portal"></a>[Portál](#tab/azure-portal)

A feladatátvételi csoport feladatátvételi tesztje a Azure Portal használatával.

1. Navigáljon a _másodlagos_ felügyelt példányhoz a [Azure Portalon](https://portal.azure.com) belül, és válassza a **példányok feladatátvételi csoportok** lehetőséget a beállítások területen.
1. Tekintse át, hogy melyik felügyelt példány az elsődleges, és melyik felügyelt példány a másodlagos.
1. Válassza a **feladatátvétel** lehetőséget, majd válassza az **Igen** lehetőséget a TDS-munkamenetek leválasztására vonatkozó figyelmeztetésben.

   ![Feladatátvételi csoport feladatátvétele](./media/auto-failover-group-configure/failover-mi-failover-group.png)

1. Tekintse át az elsődleges és a másodlagos példányt. Ha a feladatátvétel sikeres volt, a két példánynak átváltott szerepkörökkel kell rendelkeznie.

   ![A felügyelt példányok a feladatátvételt követően váltanak át szerepköröket](./media/auto-failover-group-configure/mi-switched-after-failover.png)

1. Nyissa meg az új _másodlagos_ felügyelt példányt, és kattintson ismét a **feladatátvétel** lehetőségre, hogy az elsődleges példány vissza legyen hajtva az elsődleges szerepkörre.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A feladatátvételi csoport feladatátvételi tesztje a PowerShell használatával.

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

A privát hivatkozások lehetővé teszik, hogy egy logikai kiszolgálót egy adott magánhálózati IP-címhez rendeljen a virtuális hálózaton és az alhálózaton belül. 

Ha privát hivatkozást szeretne használni a feladatátvételi csoporttal, tegye a következőket:

1. Győződjön meg arról, hogy az elsődleges és a másodlagos kiszolgáló egy [párosított régióban](../../best-practices-availability-paired-regions.md)van. 
1. Hozza létre a virtuális hálózatot és az alhálózatot az egyes régiókban az elsődleges és a másodlagos kiszolgálókhoz tartozó privát végpontok üzemeltetéséhez, hogy azok ne legyenek átfedésben az IP-címekkel. Például a 10.0.0.0/16 elsődleges virtuális hálózati címtartomány és a 10.0.0.1/16 másodlagos virtuális hálózati címtartomány átfedésben van. A virtuális hálózati címtartományok részletes ismertetését az Azure-beli [virtuális hálózatok tervezése](https://devblogs.microsoft.com/premier-developer/understanding-cidr-notation-when-designing-azure-virtual-networks-and-subnets/)című blogban találja.
1. Hozzon létre egy [privát végpontot és egy Azure saját DNS zónát az elsődleges kiszolgálóhoz](../../private-link/create-private-endpoint-portal.md#create-a-private-endpoint). 
1. Hozzon létre egy privát végpontot a másodlagos kiszolgálóhoz is, de ezúttal úgy dönt, hogy újrahasznosítja ugyanazt a saját DNS zónát, amely az elsődleges kiszolgálóhoz lett létrehozva. 
1. A magánhálózati kapcsolat létrejötte után a feladatátvételi csoportot a cikkben korábban ismertetett lépéseket követve hozhatja létre. 


## <a name="locate-listener-endpoint"></a>Figyelő végpontjának keresése

Miután konfigurálta a feladatátvételi csoportot, frissítse az alkalmazáshoz tartozó kapcsolódási karakterláncot a figyelő végpontján. Így az alkalmazás az elsődleges adatbázis, a rugalmas készlet vagy a példány-adatbázis helyett a feladatátvételi csoport figyelőjét fogja csatlakoztatni. Így nem kell manuálisan frissítenie a kapcsolati karakterláncot minden alkalommal, amikor az adatbázis-entitás feladatátvételt végez, és a forgalmat a rendszer átirányítja egy adott entitásra, amely jelenleg elsődleges.

A figyelő végpont formátuma a (z) `fog-name.database.windows.net` , és a Azure Portal látható a feladatátvételi csoport megtekintésekor:

![Feladatátvételi csoport kapcsolatainak karakterlánca](./media/auto-failover-group-configure/find-failover-group-connection-string.png)

## <a name="remarks"></a>Megjegyzések

- Egyetlen vagy készletezett adatbázis feladatátvételi csoportjának eltávolítása nem állítja le a replikálást, és nem törli a replikált adatbázist. Manuálisan kell leállítania a Geo-replikálást, és törölnie kell az adatbázist a másodlagos kiszolgálóról, ha az eltávolítását követően egyetlen vagy készletezett adatbázist szeretne visszaadni egy feladatátvételi csoportnak. A művelet elvégzése sikertelen lehet, `The operation cannot be performed due to multiple errors` Ha az adatbázis feladatátvételi csoportba való hozzáadására tett kísérlet során hasonló hibaüzenetet eredményez.

## <a name="next-steps"></a>Következő lépések

A feladatátvételi csoportok konfigurálásának részletes lépéseiért tekintse meg a következő oktatóanyagokat:

- [Egyetlen adatbázis hozzáadása egy feladatátvételi csoporthoz](failover-group-add-single-database-tutorial.md)
- [Rugalmas készlet hozzáadása feladatátvételi csoporthoz](failover-group-add-elastic-pool-tutorial.md)
- [Felügyelt példány hozzáadása feladatátvételi csoporthoz](../managed-instance/failover-group-add-instance-tutorial.md)

Az Azure SQL Database magas rendelkezésre állási lehetőségeinek áttekintését lásd: [geo-replikáció](active-geo-replication-overview.md) és [automatikus feladatátvételi csoportok](auto-failover-group-overview.md).
---
title: 'Oktatóanyag: SQL felügyelt példány hozzáadása feladatátvételi csoporthoz'
titleSuffix: Azure SQL Managed Instance
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy feladatátvételi csoportot egy elsődleges és egy másodlagos Azure SQL felügyelt példány között.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurepowershell
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: sashan
ms.date: 08/27/2019
ms.openlocfilehash: 1609f188af8ffb58251edc806e19f7820a6b0869
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525721"
---
# <a name="tutorial-add-sql-managed-instance-to-a-failover-group"></a>Oktatóanyag: SQL felügyelt példány hozzáadása feladatátvételi csoporthoz
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Az Azure SQL felügyelt példány felügyelt példányainak hozzáadása egy feladatátvételi csoporthoz. Ebből a cikkből megtudhatja, hogyan végezheti el a következőket:

> [!div class="checklist"]
> - Hozzon létre egy elsődleges felügyelt példányt.
> - Hozzon létre egy másodlagos felügyelt példányt egy [feladatátvételi csoport](../database/auto-failover-group-overview.md)részeként. 
> - Feladatátvételi teszt.

  > [!NOTE]
  > - Ha ezt az oktatóanyagot hajtja végre, győződjön meg arról, hogy az erőforrásokat az [SQL felügyelt példányához tartozó feladatátvételi csoportok beállításának előfeltételei](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets)szerint konfigurálja. 
  > - A felügyelt példányok létrehozása jelentős időt vehet igénybe. Ennek eredményeképpen ez az oktatóanyag több órát is igénybe vehet. További információ a kiépítési időszakokról: [SQL felügyelt példányok kezelési műveletei](sql-managed-instance-paas-overview.md#management-operations). 
  > - A feladatátvételi csoportban részt vevő felügyelt példányokhoz [Azure ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md), globális VNet-társítás vagy két csatlakoztatott VPN-átjáró szükséges. Ez az oktatóanyag a VPN-átjárók létrehozásának és csatlakoztatásának lépéseit ismerteti. Hagyja ki ezeket a lépéseket, ha már konfigurálta a ExpressRoute. 


## <a name="prerequisites"></a>Előfeltételek

# <a name="portal"></a>[Portál](#tab/azure-portal)
Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következőkkel: 

- Azure-előfizetés. Ha még nem rendelkezik ilyennel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbi elemekkel:

- Azure-előfizetés. Ha még nem rendelkezik ilyennel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .
- [Azure PowerShell](/powershell/azure/)

---


## <a name="create-a-resource-group-and-primary-managed-instance"></a>Erőforráscsoport és elsődleges felügyelt példány létrehozása

Ebben a lépésben létrehozza az erőforráscsoportot és a feladatátvételi csoport elsődleges felügyelt példányát a Azure Portal vagy a PowerShell használatával. 

A felügyelt példányokat a [párosított régiók](../../best-practices-availability-paired-regions.md) számára is üzembe helyezheti teljesítménybeli okokból. A földrajzilag párosított régiókban található felügyelt példányok sokkal jobb teljesítményt biztosítanak a nem párosított régiókhoz képest. 


# <a name="portal"></a>[Portál](#tab/azure-portal) 

Hozza létre az erőforráscsoportot és az elsődleges felügyelt példányt a Azure Portal használatával. 

1. Válassza az **Azure SQL** lehetőséget a Azure Portal bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás** lehetőséget, majd írja be `Azure SQL` a keresőmezőbe a kifejezést. Választható Válassza ki az **Azure SQL** melletti csillagot a kedvencekhez, és adja hozzá elemként a bal oldali navigációs sávon. 
1. Válassza a **+ Hozzáadás** lehetőséget az **SQL-telepítés kiválasztása** lap megnyitásához. A különböző adatbázisokkal kapcsolatos további információkat az **adatbázisok** csempén a **Részletek megjelenítése** lehetőség kiválasztásával tekintheti meg.
1. Válassza a **Létrehozás** lehetőséget a **felügyelt SQL-példányok** csempén. 

    ![SQL felügyelt példány kiválasztása](./media/failover-group-add-instance-tutorial/select-managed-instance.png)

1. Az **Azure SQL felügyelt példány létrehozása** oldalon az **alapok** lapon:
    1. A **Project Details (projekt részletei**) területen válassza ki az **előfizetését** a legördülő menüből, majd válassza az **Új erőforráscsoport létrehozása** lehetőséget. Írja be az erőforráscsoport nevét, például: `myResourceGroup` . 
    1. Az **SQL felügyelt példányának adatai** alatt adja meg a felügyelt példány nevét, valamint azt a régiót, ahol a felügyelt példányt telepíteni szeretné. Az alapértelmezett értékekben hagyja a **számítás + tárolás** értéket. 
    1. A **rendszergazdai fiók** területen adjon meg egy rendszergazdai bejelentkezési azonosítót, például `azureuser` , és egy összetett rendszergazdai jelszót. 

    ![Elsődleges felügyelt példány létrehozása](./media/failover-group-add-instance-tutorial/primary-sql-mi-values.png)

1. Hagyja meg a többi beállítást az alapértelmezett értékeken, majd válassza a **felülvizsgálat + létrehozás** lehetőséget, hogy áttekintse az SQL felügyelt példányának beállításait. 
1. Válassza a **Létrehozás** lehetőséget az elsődleges felügyelt példány létrehozásához. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Hozza létre az erőforráscsoportot és az elsődleges felügyelt példányt a PowerShell használatával. 

   ```powershell-interactive
   # Connect-AzAccount
   # The SubscriptionId in which to create these objects
   $SubscriptionId = '<Subscription-ID>'
   # Create a random identifier to use as subscript for the different resource names
   $randomIdentifier = $(Get-Random)
   # Set the resource group name and location for SQL Managed Instance
   $resourceGroupName = "myResourceGroup-$randomIdentifier"
   $location = "eastus"
   $drLocation = "eastus2"
   
   # Set the networking values for your primary managed instance
   $primaryVNet = "primaryVNet-$randomIdentifier"
   $primaryAddressPrefix = "10.0.0.0/16"
   $primaryDefaultSubnet = "primaryDefaultSubnet-$randomIdentifier"
   $primaryDefaultSubnetAddress = "10.0.0.0/24"
   $primaryMiSubnetName = "primaryMISubnet-$randomIdentifier"
   $primaryMiSubnetAddress = "10.0.0.0/24"
   $primaryMiGwSubnetAddress = "10.0.255.0/27"
   $primaryGWName = "primaryGateway-$randomIdentifier"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   $primaryGWConnection = $primaryGWName + "-connection"
   
   
   # Set the networking values for your secondary managed instance
   $secondaryVNet = "secondaryVNet-$randomIdentifier"
   $secondaryAddressPrefix = "10.128.0.0/16"
   $secondaryDefaultSubnet = "secondaryDefaultSubnet-$randomIdentifier"
   $secondaryDefaultSubnetAddress = "10.128.0.0/24"
   $secondaryMiSubnetName = "secondaryMISubnet-$randomIdentifier"
   $secondaryMiSubnetAddress = "10.128.0.0/24"
   $secondaryMiGwSubnetAddress = "10.128.255.0/27"
   $secondaryGWName = "secondaryGateway-$randomIdentifier"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   $secondaryGWConnection = $secondaryGWName + "-connection"
   
   
   
   # Set the SQL Managed Instance name for the new managed instances
   $primaryInstance = "primary-mi-$randomIdentifier"
   $secondaryInstance = "secondary-mi-$randomIdentifier"
   
   # Set the admin login and password for SQL Managed Instance
   $secpasswd = "PWD27!"+(New-Guid).Guid | ConvertTo-SecureString -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential ("azureuser", $secpasswd)
   
   
   # Set the SQL Managed Instance service tier, compute level, and license mode
   $edition = "General Purpose"
   $vCores = 8
   $maxStorage = 256
   $computeGeneration = "Gen5"
   $license = "LicenseIncluded" #"BasePrice" or LicenseIncluded if you have don't have SQL Server license that can be used for AHB discount
   
   # Set failover group details
   $vpnSharedKey = "mi1mi2psk"
   $failoverGroupName = "failovergroup-$randomIdentifier"
   
   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName
   Write-host "Password is" $secpasswd
   Write-host "Primary Virtual Network name is" $primaryVNet
   Write-host "Primary default subnet name is" $primaryDefaultSubnet
   Write-host "Primary SQL Managed Instance subnet name is" $primaryMiSubnetName
   Write-host "Secondary Virtual Network name is" $secondaryVNet
   Write-host "Secondary default subnet name is" $secondaryDefaultSubnet
   Write-host "Secondary SQL Managed Instance subnet name is" $secondaryMiSubnetName
   Write-host "Primary SQL Managed Instance name is" $primaryInstance
   Write-host "Secondary SQL Managed Instance name is" $secondaryInstance
   Write-host "Failover group name is" $failoverGroupName
   
   # Suppress networking breaking changes warning (https://aka.ms/azps-changewarnings
   Set-Item Env:\SuppressAzurePowerShellBreakingChangeWarnings "true"
   
   # Set the subscription context
   Set-AzContext -SubscriptionId $subscriptionId 
   
   # Create the resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup
   
   # Configure the primary virtual network
   Write-host "Creating primary virtual network..."
   $primaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -Name $primaryVNet `
                         -AddressPrefix $primaryAddressPrefix
   
                     Add-AzVirtualNetworkSubnetConfig `
                         -Name $primaryMiSubnetName `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                     | Set-AzVirtualNetwork
   $primaryVirtualNetwork
   
   
   # Configure the primary managed instance subnet
   Write-host "Configuring primary MI subnet..."
   $primaryVirtualNetwork = Get-AzVirtualNetwork -Name $primaryVNet -ResourceGroupName $resourceGroupName
   
   
   $primaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $primaryMiSubnetName `
                           -VirtualNetwork $primaryVirtualNetwork
   $primaryMiSubnetConfig
   
   # Configure the network security group management service
   Write-host "Configuring primary MI subnet..."
   
   $primaryMiSubnetConfigId = $primaryMiSubnetConfig.Id
   
   $primaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'primaryNSGMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryNSGMiManagementService
   
   # Configure the route table management service
   Write-host "Configuring primary MI route table management service..."
   
   $primaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'primaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryRouteTableMiManagementService
   
   # Configure the primary network security group
   Write-host "Configuring primary network security group..."
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -Name $primaryMiSubnetName `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NetworkSecurityGroup $primaryNSGMiManagementService `
                         -RouteTable $primaryRouteTableMiManagementService | `
                       Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryNSGMiManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $PrimaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $PrimaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   Write-host "Primary network security group configured successfully."
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "primaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Primary network route table configured successfully."
   
   
   # Create the primary managed instance
   
   Write-host "Creating primary SQL Managed Instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $primaryInstance `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -SubnetId $primaryMiSubnetConfigId `
                         -AdministratorCredential $mycreds `
                         -StorageSizeInGB $maxStorage `
                         -VCore $vCores `
                         -Edition $edition `
                         -ComputeGeneration $computeGeneration `
                         -LicenseType $license
   Write-host "Primary SQL Managed Instance created successfully."
   ```

Az oktatóanyag ezen része a következő PowerShell-parancsmagokat használja:

| Parancs | Jegyzetek |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy Azure-erőforráscsoportot.  |
| [Új – AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Virtuális hálózatot hoz létre.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Alhálózati konfigurációt rendel egy virtuális hálózathoz. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Lekér egy virtuális hálózatot egy erőforráscsoportban. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Beolvas egy alhálózatot egy virtuális hálózatban. | 
| [Új – AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Létrehoz egy hálózati biztonsági csoportot. | 
| [Új – AzRouteTable](/powershell/module/az.network/new-azroutetable) | Létrehoz egy útválasztási táblázatot. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Egy virtuális hálózat alhálózati konfigurációjának frissítése.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Frissíti a virtuális hálózatot.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Hálózati biztonsági csoport beolvasása. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Hálózati biztonsági szabály konfigurációját adja hozzá egy hálózati biztonsági csoporthoz. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Egy hálózati biztonsági csoport frissítése.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Útvonalat helyez el egy útválasztási táblához. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Frissíti az útválasztási táblázatot.  |
| [Új – AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Létrehoz egy felügyelt példányt.  |

---

## <a name="create-secondary-virtual-network"></a>Másodlagos virtuális hálózat létrehozása

Ha a Azure Portal a felügyelt példány létrehozásához használja, külön kell létrehoznia a virtuális hálózatot, mivel követelmény, hogy az elsődleges és a másodlagos felügyelt példány alhálózata ne rendelkezzen átfedésben lévő tartományokkal. Ha a PowerShell segítségével konfigurálja a felügyelt példányt, ugorjon a 3. lépésre. 

# <a name="portal"></a>[Portál](#tab/azure-portal) 

Az elsődleges virtuális hálózat alhálózati tartományának ellenőrzéséhez kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com)navigáljon az erőforráscsoporthoz, és válassza ki az elsődleges példány virtuális hálózatát.  
2. Válassza ki az **alhálózatok** lehetőséget a **Beállítások** területen, és jegyezze fel a **címtartományt**. A másodlagos felügyelt példányhoz tartozó virtuális hálózat alhálózati címtartomány nem fedi át ezt a tartományt. 


   ![Elsődleges alhálózat](./media/failover-group-add-instance-tutorial/verify-primary-subnet-range.png)

Virtuális hálózat létrehozásához kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com)válassza az **erőforrás létrehozása** és a *virtuális hálózat* keresése lehetőséget. 
1. Válassza ki a Microsoft által közzétett **Virtual Network** lehetőséget, majd válassza a **Létrehozás** elemet a következő oldalon. 
1. Adja meg a szükséges mezőket a másodlagos felügyelt példány virtuális hálózatának konfigurálásához, majd válassza a **Létrehozás** lehetőséget. 

   A következő táblázat a másodlagos virtuális hálózathoz szükséges értékeket mutatja be:

    | **Mező** | Érték |
    | --- | --- |
    | **Név** |  A másodlagos felügyelt példány által használandó virtuális hálózat neve, például: `vnet-sql-mi-secondary` . |
    | **Címtér** | A virtuális hálózat címterület, például: `10.128.0.0/16` . | 
    | **Előfizetés** | Az az előfizetés, amelyben az elsődleges felügyelt példány és az erőforráscsoport található. |
    | **Régió** | Az a hely, ahová a másodlagos felügyelt példányt telepíteni fogja. |
    | **Alhálózat** | Az alhálózat neve. `default` Alapértelmezés szerint meg van biztosítva. |
    | **Címtartomány**| Az alhálózat címtartomány. Ennek eltérőnek kell lennie, mint az elsődleges felügyelt példány virtuális hálózata által használt alhálózat-címtartomány, például: `10.128.0.0/24` .  |
    | &nbsp; | &nbsp; |

    ![Másodlagos virtuális hálózati értékek](./media/failover-group-add-instance-tutorial/secondary-virtual-network.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ez a lépés csak akkor szükséges, ha a Azure Portal használatával telepíti az SQL felügyelt példányát. Ugorjon a 3. lépésre, ha a PowerShellt használja. 

---

## <a name="create-a-secondary-managed-instance"></a>Másodlagos felügyelt példány létrehozása
Ebben a lépésben egy másodlagos felügyelt példányt fog létrehozni a Azure Portalban, amely a két felügyelt példány közötti hálózatkezelést is konfigurálja. 

A második felügyelt példánynak a következőket kell tennie:
- Üresnek kell lennie. 
- Az elsődleges felügyelt példánytól eltérő alhálózattal és IP-tartománnyal rendelkezik. 

# <a name="portal"></a>[Portál](#tab/azure-portal) 

Hozza létre a másodlagos felügyelt példányt a Azure Portal használatával. 

1. Válassza az **Azure SQL** lehetőséget a Azure Portal bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás** lehetőséget, majd írja be `Azure SQL` a keresőmezőbe a kifejezést. Választható Válassza ki az **Azure SQL** melletti csillagot a kedvencekhez, és adja hozzá elemként a bal oldali navigációs sávon. 
1. Válassza a **+ Hozzáadás** lehetőséget az **SQL-telepítés kiválasztása** lap megnyitásához. A különböző adatbázisokkal kapcsolatos további információkat az **adatbázisok** csempén a **Részletek megjelenítése** lehetőség kiválasztásával tekintheti meg.
1. Válassza a **Létrehozás** lehetőséget a **felügyelt SQL-példányok** csempén. 

    ![SQL felügyelt példány kiválasztása](./media/failover-group-add-instance-tutorial/select-managed-instance.png)

1. Az **Azure SQL felügyelt példány létrehozása** lap **alapok** lapján töltse ki a szükséges mezőket a másodlagos felügyelt példány konfigurálásához. 

   A következő táblázat a másodlagos felügyelt példányhoz szükséges értékeket tartalmazza:
 
    | **Mező** | Érték |
    | --- | --- |
    | **Előfizetés** |  Az az előfizetés, amelyben az elsődleges felügyelt példánya. |
    | **Erőforráscsoport**| Az az erőforráscsoport, amelyben az elsődleges felügyelt példánya. |
    | **SQL felügyelt példányának neve** | Az új másodlagos felügyelt példány neve, például: `sql-mi-secondary` .  | 
    | **Régió**| A másodlagos felügyelt példány helye.  |
    | **SQL felügyelt példány rendszergazdai bejelentkezés** | Az új másodlagos felügyelt példányhoz használni kívánt bejelentkezési azonosító, például: `azureuser` . |
    | **Jelszó** | Összetett jelszó, amelyet az új másodlagos felügyelt példányhoz tartozó rendszergazdai bejelentkezés fog használni.  |
    | &nbsp; | &nbsp; |

1. A **hálózatkezelés** lap **Virtual Network** területén válassza ki a másodlagos felügyelt példányhoz létrehozott virtuális hálózatot a legördülő menüből.

   ![Másodlagos MI hálózatkezelés](./media/failover-group-add-instance-tutorial/networking-settings-for-secondary-mi.png)

1. A **További beállítások** lap **geo-replikáció** területén válassza az **Igen** lehetőséget a _feladatátvétel másodlagosként való használathoz_. Válassza ki az elsődleges felügyelt példányt a legördülő menüből. 
    
   Ügyeljen arra, hogy a rendezés és az időzóna megfeleljen az elsődleges felügyelt példánynak. Az oktatóanyagban létrehozott elsődleges felügyelt példány az alapértelmezett `SQL_Latin1_General_CP1_CI_AS` rendezést és az `(UTC) Coordinated Universal Time` időzónát használta. 

   ![Másodlagos felügyelt példányok hálózatkezelése](./media/failover-group-add-instance-tutorial/secondary-mi-failover.png)

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget a másodlagos felügyelt példány beállításainak áttekintéséhez. 
1. Válassza a **Létrehozás** lehetőséget a másodlagos felügyelt példány létrehozásához. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Hozza létre a másodlagos felügyelt példányt a PowerShell használatával. 

   ```powershell-interactive
   # Configure the secondary virtual network
   Write-host "Configuring secondary virtual network..."
   
   $SecondaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $drlocation `
                         -Name $secondaryVNet `
                         -AddressPrefix $secondaryAddressPrefix
   
   Add-AzVirtualNetworkSubnetConfig `
                         -Name $secondaryMiSubnetName `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -AddressPrefix $secondaryMiSubnetAddress `
                       | Set-AzVirtualNetwork
   $SecondaryVirtualNetwork
   
   # Configure the secondary managed instance subnet
   Write-host "Configuring secondary MI subnet..."
   
   $SecondaryVirtualNetwork = Get-AzVirtualNetwork -Name $secondaryVNet `
                                   -ResourceGroupName $resourceGroupName
   
   $secondaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $secondaryMiSubnetName `
                           -VirtualNetwork $SecondaryVirtualNetwork
   $secondaryMiSubnetConfig
   
   # Configure the secondary network security group management service
   Write-host "Configuring secondary network security group management service..."
   
   $secondaryMiSubnetConfigId = $secondaryMiSubnetConfig.Id
   
   $secondaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'secondaryToMIManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryNSGMiManagementService
   
   # Configure the secondary route table MI management service
   Write-host "Configuring secondary route table MI management service..."
   
   $secondaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'secondaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryRouteTableMiManagementService
   
   # Configure the secondary network security group
   Write-host "Configuring secondary network security group..."
   
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -Name $secondaryMiSubnetName `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NetworkSecurityGroup $secondaryNSGMiManagementService `
                         -RouteTable $secondaryRouteTableMiManagementService `
                       | Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryToMIManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $secondaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $secondaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "secondaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Secondary network security group configured successfully."
   
   # Create the secondary managed instance
   
   $primaryManagedInstanceId = Get-AzSqlInstance -Name $primaryInstance -ResourceGroupName $resourceGroupName | Select-Object Id
   
   
   Write-host "Creating secondary SQL Managed Instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $secondaryInstance `
                     -ResourceGroupName $resourceGroupName `
                     -Location $drLocation `
                     -SubnetId $secondaryMiSubnetConfigId `
                     -AdministratorCredential $mycreds `
                     -StorageSizeInGB $maxStorage `
                     -VCore $vCores `
                     -Edition $edition `
                     -ComputeGeneration $computeGeneration `
                     -LicenseType $license `
                     -DnsZonePartner $primaryManagedInstanceId.Id
   Write-host "Secondary SQL Managed Instance created successfully."
   ```

Az oktatóanyag ezen része a következő PowerShell-parancsmagokat használja:

| Parancs | Jegyzetek |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy Azure-erőforráscsoportot.  |
| [Új – AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Virtuális hálózatot hoz létre.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Alhálózati konfigurációt rendel egy virtuális hálózathoz. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Lekér egy virtuális hálózatot egy erőforráscsoportban. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Beolvas egy alhálózatot egy virtuális hálózatban. | 
| [Új – AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Létrehoz egy hálózati biztonsági csoportot. | 
| [Új – AzRouteTable](/powershell/module/az.network/new-azroutetable) | Létrehoz egy útválasztási táblázatot. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Egy virtuális hálózat alhálózati konfigurációjának frissítése.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Frissíti a virtuális hálózatot.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Hálózati biztonsági csoport beolvasása. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Hálózati biztonsági szabály konfigurációját adja hozzá egy hálózati biztonsági csoporthoz. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Egy hálózati biztonsági csoport frissítése.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Útvonalat helyez el egy útválasztási táblához. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Frissíti az útválasztási táblázatot.  |
| [Új – AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Létrehoz egy felügyelt példányt.  |

---

## <a name="create-a-primary-gateway"></a>Elsődleges átjáró létrehozása 

Két felügyelt példánynak a feladatátvételi csoportban való részvételhez ExpressRoute vagy átjárót kell konfigurálnia a két felügyelt példány virtuális hálózatai között, hogy engedélyezze a hálózati kommunikációt. Ha úgy dönt, hogy [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) konfigurálja a két VPN-átjáró csatlakoztatása helyett, ugorjon a [7. lépésre](#create-a-failover-group).  

Ez a cikk a két VPN-átjáró létrehozásának lépéseit és azok összekapcsolását ismerteti, de a feladatátvételi csoport létrehozásához a ExpressRoute konfigurálásakor ugorjon előre. 

> [!NOTE]
> Az átjáró SKU-jának hatással van az átviteli teljesítményre. Ez az oktatóanyag egy átjárót helyez üzembe a legalapvetőbb SKU ( `HwGw1` ) használatával. Helyezzen üzembe egy magasabb SKU-t (például: `VpnGw3` ) a nagyobb átviteli sebesség eléréséhez. Az összes elérhető lehetőség esetében lásd: [átjáró SKU](../../vpn-gateway/vpn-gateway-about-vpngateways.md#benchmark) -i

# <a name="portal"></a>[Portál](#tab/azure-portal)

Hozza létre az elsődleges felügyelt példány virtuális hálózatának átjáróját a Azure Portal használatával. 


1. A [Azure Portal](https://portal.azure.com)nyissa meg az erőforráscsoportot, és válassza ki a **virtuális hálózati** erőforrást az elsődleges felügyelt példányhoz. 
1. Válassza ki az **alhálózatok** lehetőséget a **Beállítások** területen, majd válassza a lehetőséget egy új **átjáró-alhálózat** hozzáadásához. Hagyja meg az alapértelmezett értékeket. 

   ![Átjáró hozzáadása az elsődleges felügyelt példányhoz](./media/failover-group-add-instance-tutorial/add-subnet-gateway-primary-vnet.png)

1. Az alhálózat-átjáró létrehozása után válassza az **erőforrás létrehozása** lehetőséget a bal oldali navigációs ablaktáblán, majd írja be `Virtual network gateway` a keresőmezőbe a kifejezést. Válassza ki a **Microsoft** által közzétett **Virtual Network Gateway** -erőforrást. 

   ![Új virtuális hálózati átjáró létrehozása](./media/failover-group-add-instance-tutorial/create-virtual-network-gateway.png)

1. Adja meg az elsődleges felügyelt példány átjárójának konfigurálásához szükséges mezőket. 

   Az alábbi táblázat az elsődleges felügyelt példány átjárója számára szükséges értékeket tartalmazza:
 
    | **Mező** | Érték |
    | --- | --- |
    | **Előfizetés** |  Az az előfizetés, amelyben az elsődleges felügyelt példánya. |
    | **Név** | A virtuális hálózati átjáró neve, például: `primary-mi-gateway` . | 
    | **Régió** | Az a régió, ahol az elsődleges felügyelt példánya van. |
    | **Átjáró típusa** | Válassza a **VPN** lehetőséget. |
    | **VPN-típus** | Válassza az **útvonal-alapú** lehetőséget. |
    | **Termékváltozat**| Hagyja meg az alapértelmezett értéket `VpnGw1` . |
    | **Virtuális hálózat**| Válassza ki a 2. szakaszban létrehozott virtuális hálózatot, például: `vnet-sql-mi-primary` . |
    | **Nyilvános IP-cím**| Válassza az **Új létrehozása** lehetőséget. |
    | **Nyilvános IP-cím neve**| Adja meg az IP-cím nevét, például: `primary-gateway-IP` . |
    | &nbsp; | &nbsp; |

1. Hagyja meg a többi értéket alapértelmezettként, majd válassza a **felülvizsgálat + létrehozás** lehetőséget a virtuális hálózati átjáró beállításainak áttekintéséhez.

   ![Elsődleges átjáró beállításai](./media/failover-group-add-instance-tutorial/settings-for-primary-gateway.png)

1. Válassza a **Létrehozás** lehetőséget az új virtuális hálózati átjáró létrehozásához. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Hozza létre az elsődleges felügyelt példány virtuális hálózatának átjáróját a PowerShell használatával. 

   ```powershell-interactive
   # Create the primary gateway
   Write-host "Adding GatewaySubnet to primary VNet..."
   Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $primaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $primaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName
   $primaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $primaryVirtualNetwork
   
   Write-host "Creating primary gateway..."
   Write-host "This will take some time."
   $primaryGWPublicIP = New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $location -AllocationMethod Dynamic
   $primaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -Subnet $primaryGatewaySubnet -PublicIpAddress $primaryGWPublicIP
   
   $primaryGateway = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $resourceGroupName `
       -Location $location -IpConfigurations $primaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $primaryGateway
   ```

Az oktatóanyag ezen része a következő PowerShell-parancsmagokat használja:

| Parancs | Jegyzetek |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Lekér egy virtuális hálózatot egy erőforráscsoportban. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Alhálózati konfigurációt rendel egy virtuális hálózathoz. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Frissíti a virtuális hálózatot.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Beolvas egy alhálózatot egy virtuális hálózatban. |
| [Új – AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Egy nyilvános IP-címet hoz létre.  | 
| [Új – AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Létrehoz egy IP-konfigurációt egy virtuális hálózati átjáróhoz. |
| [Új – AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Létrehoz egy virtuális hálózati átjárót. |


---


## <a name="create-secondary-gateway"></a>Másodlagos átjáró létrehozása 
Ebben a lépésben a Azure Portal használatával hozza létre a másodlagos felügyelt példány virtuális hálózatának átjáróját. 


# <a name="portal"></a>[Portál](#tab/azure-portal)

A Azure Portal használatával ismételje meg az előző szakaszban leírt lépéseket a virtuális hálózati alhálózat és átjáró létrehozásához a másodlagos felügyelt példányhoz. Adja meg a szükséges mezőket a másodlagos felügyelt példány átjárójának konfigurálásához. 

   A következő táblázat a másodlagos felügyelt példány átjárója számára szükséges értékeket tartalmazza:

   | **Mező** | Érték |
   | --- | --- |
   | **Előfizetés** |  Az előfizetés, amelyben a másodlagos felügyelt példánya van. |
   | **Név** | A virtuális hálózati átjáró neve, például: `secondary-mi-gateway` . | 
   | **Régió** | Az a régió, amelyben a másodlagos felügyelt példány található. |
   | **Átjáró típusa** | Válassza a **VPN** lehetőséget. |
   | **VPN-típus** | Válassza az **útvonal-alapú** lehetőséget. |
   | **Termékváltozat**| Hagyja meg az alapértelmezett értéket `VpnGw1` . |
   | **Virtuális hálózat**| Válassza ki a virtuális hálózatot a másodlagos felügyelt példányhoz, például: `vnet-sql-mi-secondary` . |
   | **Nyilvános IP-cím**| Válassza az **Új létrehozása** lehetőséget. |
   | **Nyilvános IP-cím neve**| Adja meg az IP-cím nevét, például: `secondary-gateway-IP` . |
   | &nbsp; | &nbsp; |

   ![Másodlagos átjáró beállításai](./media/failover-group-add-instance-tutorial/settings-for-secondary-gateway.png)


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Hozza létre a másodlagos felügyelt példány virtuális hálózatának átjáróját a PowerShell használatával. 

   ```powershell-interactive
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   
   Write-host "Adding GatewaySubnet to secondary VNet..."
   Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $secondaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $secondaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName
   $secondaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $secondaryVirtualNetwork
   $drLocation = $secondaryVirtualNetwork.Location
   
   Write-host "Creating secondary gateway..."
   Write-host "This will take some time."
   $secondaryGWPublicIP = New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $drLocation -AllocationMethod Dynamic
   $secondaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -Subnet $secondaryGatewaySubnet -PublicIpAddress $secondaryGWPublicIP
   
   $secondaryGateway = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $resourceGroupName `
       -Location $drLocation -IpConfigurations $secondaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   $secondaryGateway
   ```

Az oktatóanyag ezen része a következő PowerShell-parancsmagokat használja:

| Parancs | Jegyzetek |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Lekér egy virtuális hálózatot egy erőforráscsoportban. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Alhálózati konfigurációt rendel egy virtuális hálózathoz. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Frissíti a virtuális hálózatot.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Beolvas egy alhálózatot egy virtuális hálózatban. |
| [Új – AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Egy nyilvános IP-címet hoz létre.  | 
| [Új – AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Létrehoz egy IP-konfigurációt egy virtuális hálózati átjáróhoz. |
| [Új – AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Létrehoz egy virtuális hálózati átjárót. |

---


## <a name="connect-the-gateways"></a>Az átjárók összekötése
Ebben a lépésben hozzon létre kétirányú kapcsolatot a két virtuális hálózat két átjárója között. 


# <a name="portal"></a>[Portál](#tab/azure-portal)

Kapcsolja össze a két átjárót a Azure Portal használatával. 


1. Válassza az **erőforrás létrehozása** lehetőséget a [Azure Portal](https://portal.azure.com).
1. Írja be `connection` a keresőmezőbe, majd nyomja le az ENTER billentyűt a kereséshez, amely a Microsoft által közzétett **kapcsolódási** erőforrásra viszi.
1. Válassza a **Létrehozás** lehetőséget a kapcsolódás létrehozásához. 
1. Az **alapvető beállítások** lapon válassza ki a következő értékeket, majd kattintson az **OK gombra**. 
    1. Válassza `VNet-to-VNet` a **kapcsolattípus** lehetőséget. 
    1. Válassza ki előfizetését a legördülő listából. 
    1. Válassza ki az SQL felügyelt példányhoz tartozó erőforráscsoportot a legördülő menüből. 
    1. Válassza ki az elsődleges felügyelt példány helyét a legördülő menüből. 
1. A **Beállítások** lapon válassza ki vagy adja meg a következő értékeket, majd kattintson az **OK gombra**:
    1. Válassza ki az **első virtuális hálózati átjáró** elsődleges hálózati átjáróját, például: `primaryGateway` .  
    1. Válassza ki a **második virtuális hálózati átjáró** másodlagos hálózati átjáróját, például: `secondaryGateway` . 
    1. Jelölje be a **kétirányú kapcsolat létesítése** melletti jelölőnégyzetet. 
    1. Hagyja meg az alapértelmezett elsődleges kapcsolódási nevet, vagy nevezze át tetszőleges értékre. 
    1. Adjon meg egy **megosztott kulcsot (PSK)** a kapcsolatban, például: `mi1m2psk` . 
    1. A beállítások mentéséhez kattintson **az OK gombra** . 

    ![Átjáró-kapcsolatok létrehozása](./media/failover-group-add-instance-tutorial/create-gateway-connection.png)

    

1. A **felülvizsgálat + létrehozás** oldalon tekintse át a kétirányú kapcsolatok beállításait, majd kattintson **az OK** gombra a kapcsolódás létrehozásához. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A két átjáró összekapcsolásához használja a PowerShellt. 

   ```powershell-interactive
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway to secondary gateway..."
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $primaryGateway -VirtualNetworkGateway2 $secondaryGateway -Location $location `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway to primary gateway..."
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $secondaryGateway -VirtualNetworkGateway2 $primaryGateway -Location $drLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

Az oktatóanyag ezen része a következő PowerShell-parancsmagot használja:

| Parancs | Jegyzetek |
|---|---|
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Kapcsolatot hoz létre a két virtuális hálózati átjáró között.   |

---


## <a name="create-a-failover-group"></a>Feladatátvételi csoport létrehozása
Ebben a lépésben létrehozza a feladatátvételi csoportot, és hozzáadja a felügyelt példányokat is. 


# <a name="portal"></a>[Portál](#tab/azure-portal)
Hozza létre a feladatátvételi csoportot a Azure Portal használatával. 


1. Válassza az **Azure SQL** lehetőséget a [Azure Portal](https://portal.azure.com)bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás** lehetőséget, majd írja be `Azure SQL` a keresőmezőbe a kifejezést. Választható Válassza ki az **Azure SQL** melletti csillagot a kedvencekhez, és adja hozzá elemként a bal oldali navigációs sávon. 
1. Válassza ki az első szakaszban létrehozott elsődleges felügyelt példányt, például: `sql-mi-primary` . 
1. A **Beállítások** területen navigáljon a **példány feladatátvételi csoportok** elemre, majd válassza a **Csoport hozzáadása** lehetőséget a **példány feladatátvételi csoport** lap megnyitásához. 

   ![Feladatátvételi csoport hozzáadása](./media/failover-group-add-instance-tutorial/add-failover-group.png)

1. A **példány feladatátvételi csoport** lapján írja be a feladatátvételi csoport nevét, például: `failovergrouptutorial` . Ezután válassza ki a másodlagos felügyelt példányt, például: `sql-mi-secondary` , a legördülő menüből. Válassza a **Létrehozás** lehetőséget a feladatátvételi csoport létrehozásához. 

   ![Feladatátvételi csoport létrehozása](./media/failover-group-add-instance-tutorial/create-failover-group.png)

1. A feladatátvételi csoport központi telepítésének befejezése után vissza fog térni a **feladatátvételi csoport** lapra. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Hozza létre a feladatátvételi csoportot a PowerShell használatával. 

   ```powershell-interactive
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $location -ResourceGroupName $resourceGroupName -PrimaryManagedInstanceName $primaryInstance `
        -PartnerRegion $drLocation -PartnerManagedInstanceName $secondaryInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

Az oktatóanyag ezen része a következő PowerShell-parancsmagot használja:

| Parancs | Jegyzetek |
|---|---|
| [Új – AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Létrehoz egy új Azure SQL felügyelt példány feladatátvételi csoportot.  |


---


## <a name="test-failover"></a>Feladatátvétel tesztelése
Ebben a lépésben a feladatátvételi csoportot a másodlagos kiszolgálóra fogja felvenni, majd a Azure Portal használatával hajtja végre a feladatokat. 


# <a name="portal"></a>[Portál](#tab/azure-portal)
Feladatátvételi teszt a Azure Portal használatával. 


1. Navigáljon a _másodlagos_ felügyelt példányhoz a [Azure Portalon](https://portal.azure.com) belül, és válassza a **példányok feladatátvételi csoportok** lehetőséget a beállítások területen. 
1. Tekintse át, hogy melyik felügyelt példány az elsődleges, és melyik felügyelt példány a másodlagos. 
1. Válassza a **feladatátvétel** lehetőséget, majd válassza az **Igen** lehetőséget a TDS-munkamenetek leválasztására vonatkozó figyelmeztetésben. 

   ![Feladatátvételi csoport feladatátvétele](./media/failover-group-add-instance-tutorial/failover-mi-failover-group.png)

1. Tekintse át, hogy melyik felügyelt példány az elsődleges, és melyik felügyelt példány a másodlagos. Ha a feladatátvétel sikeres volt, a két példánynak átváltott szerepkörökkel kell rendelkeznie. 

   ![A felügyelt példányok a feladatátvételt követően váltanak át szerepköröket](./media/failover-group-add-instance-tutorial/mi-switched-after-failover.png)

1. Nyissa meg az új _másodlagos_ felügyelt példányt, és kattintson ismét a **feladatátvétel** lehetőségre, hogy az elsődleges példány vissza legyen hajtva az elsődleges szerepkörre. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Feladatátvételi teszt a PowerShell használatával. 

   ```powershell-interactive
    
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   
   # Fail over the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   ```


Állítsa vissza a feladatátvételi csoportot az elsődleges kiszolgálóra:

   ```powershell-interactive
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName
   
   # Fail the primary managed instance back to the primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   ```

Az oktatóanyag ezen része a következő PowerShell-parancsmagokat használja:

| Parancs | Jegyzetek |
|---|---|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Lekérdezi vagy listázza az SQL felügyelt példányának feladatátvételi csoportjait.| 
| [Kapcsoló – AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Végrehajtja az SQL felügyelt példányok feladatátvételi csoportjának feladatátvételét. | 

---



## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Az erőforrások törléséhez először törölje a felügyelt példányokat, majd a virtuális fürtöt, majd a többi erőforrást és végül az erőforráscsoportot. 

# <a name="portal"></a>[Portál](#tab/azure-portal)
1. Navigáljon az erőforráscsoporthoz a [Azure Portal](https://portal.azure.com). 
1. Válassza ki a felügyelt példány (oka) t, majd válassza a **Törlés** lehetőséget. Írja be `yes` a szövegmezőbe, és erősítse meg, hogy törölni kívánja az erőforrást, majd válassza a **Törlés** lehetőséget. Ez a folyamat hosszabb időt is igénybe vehet a háttérben, és amíg el nem végzi a műveletet, nem fogja tudni törölni a *virtuális fürtöt* vagy bármely más függő erőforrást. Figyelje meg a törlést a **tevékenység** lapon a felügyelt példány törlésének megerősítéséhez. 
1. A felügyelt példány törlése után törölje a *virtuális fürtöt* úgy, hogy kiválasztja az erőforráscsoportot, majd a **Törlés** lehetőséget választja. Írja be `yes` a szövegmezőbe, és erősítse meg, hogy törölni kívánja az erőforrást, majd válassza a **Törlés** lehetőséget. 
1. Törölje a többi erőforrást. Írja be `yes` a szövegmezőbe, és erősítse meg, hogy törölni kívánja az erőforrást, majd válassza a **Törlés** lehetőséget. 
1. Törölje az erőforráscsoportot az **erőforráscsoport törlése** elem kiválasztásával, írja be az erőforráscsoport nevét, majd válassza a `myResourceGroup` **Törlés** lehetőséget. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az erőforráscsoportot kétszer kell eltávolítania. Az erőforráscsoport eltávolításával a rendszer először eltávolítja a felügyelt példányokat és a virtuális fürtöket, de a hibaüzenettel meghiúsul `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'` . Futtassa a Remove-AzResourceGroup parancsot egy második alkalommal, hogy eltávolítsa a fennmaradó erőforrásokat és az erőforráscsoportot.

```powershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing SQL Managed Instance and virtual cluster..."
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing residual resources and resource group..."
```

Az oktatóanyag ezen része a következő PowerShell-parancsmagot használja:

| Parancs | Jegyzetek |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Eltávolít egy erőforráscsoportot. |

---

## <a name="full-script"></a>Teljes szkript

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add SQL Managed Instance to a failover group")]

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy Azure-erőforráscsoportot.  |
| [Új – AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Virtuális hálózatot hoz létre.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Alhálózati konfigurációt rendel egy virtuális hálózathoz. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Lekér egy virtuális hálózatot egy erőforráscsoportban. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Beolvas egy alhálózatot egy virtuális hálózatban. | 
| [Új – AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Létrehoz egy hálózati biztonsági csoportot. | 
| [Új – AzRouteTable](/powershell/module/az.network/new-azroutetable) | Létrehoz egy útválasztási táblázatot. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Egy virtuális hálózat alhálózati konfigurációjának frissítése.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Frissíti a virtuális hálózatot.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Hálózati biztonsági csoport beolvasása. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Hálózati biztonsági szabály konfigurációját adja hozzá egy hálózati biztonsági csoporthoz. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Egy hálózati biztonsági csoport frissítése.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Útvonalat helyez el egy útválasztási táblához. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Frissíti az útválasztási táblázatot.  |
| [Új – AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Létrehoz egy felügyelt példányt.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| A felügyelt Azure SQL-példányra vonatkozó adatokat adja vissza. |
| [Új – AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Egy nyilvános IP-címet hoz létre.  | 
| [Új – AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Létrehoz egy IP-konfigurációt egy virtuális hálózati átjáróhoz. |
| [Új – AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Létrehoz egy virtuális hálózati átjárót. |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Kapcsolatot hoz létre a két virtuális hálózati átjáró között.   |
| [Új – AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Létrehoz egy új SQL felügyelt példány feladatátvételi csoportot.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Lekérdezi vagy listázza az SQL felügyelt példányának feladatátvételi csoportjait.| 
| [Kapcsoló – AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Végrehajtja az SQL felügyelt példányok feladatátvételi csoportjának feladatátvételét. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Eltávolít egy erőforráscsoportot. | 

# <a name="portal"></a>[Portál](#tab/azure-portal) 

Nincsenek elérhető parancsfájlok a Azure Portal számára.

---

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy feladatátvételi csoportot konfigurált két felügyelt példány között. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> - Hozzon létre egy elsődleges felügyelt példányt.
> - Hozzon létre egy másodlagos felügyelt példányt egy [feladatátvételi csoport](../database/auto-failover-group-overview.md)részeként. 
> - Feladatátvételi teszt.

Folytassa a következő rövid útmutatóval, hogyan csatlakozhat az SQL felügyelt példányához, és hogyan állíthatja vissza az adatbázist az SQL felügyelt példányára: 

> [!div class="nextstepaction"]
> [Kapcsolódás az SQL felügyelt példányához](connect-vm-instance-configure.md) 
>  [Adatbázis visszaállítása SQL felügyelt példányra](restore-sample-database-quickstart.md)



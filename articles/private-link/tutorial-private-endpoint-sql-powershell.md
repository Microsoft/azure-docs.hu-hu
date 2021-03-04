---
title: Oktatóanyag – kapcsolódás Azure SQL Serverhez Azure Private Endpoint-PowerShell használatával
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy privát végponttal rendelkező Azure SQL Servert Azure PowerShell használatával
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 10/31/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: 4999d8b5d5ebe5d44aae782589b5c63324a4027b
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096513"
---
# <a name="tutorial---connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-powershell"></a>Oktatóanyag – kapcsolódás Azure SQL Serverhez Azure Private Endpoint-Azure PowerShell használatával

Az Azure privát végpontja az Azure-beli privát kapcsolat alapvető építőeleme. Lehetővé teszi az Azure-erőforrások, például a virtuális gépek (VM-EK) számára, hogy magánjellegű módon kommunikáljanak a privát kapcsolati erőforrásokkal.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy virtuális hálózatot és egy megerősített gazdagépet.
> * Virtuális gépet hoz létre.
> * Hozzon létre egy Azure SQL-kiszolgálót és egy magánhálózati végpontot.
> * Az SQL Server magánhálózati végpontjának kapcsolatának tesztelése.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ha a PowerShell helyi telepítése és használata mellett dönt, ehhez a cikkhez az Azure PowerShell-modul 5.4.1-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor azt is futtatnia kell, `Connect-AzAccount` hogy létrehozza az Azure-hoz való kapcsolódást.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```azurepowershell-interactive
New-AzResourceGroup -Name 'CreateSQLEndpointTutorial-rg' -Location 'eastus'
```

## <a name="create-a-virtual-network-and-bastion-host"></a>Virtuális hálózat és megerősített gazdagép létrehozása

Ebben a szakaszban létrehoz egy virtuális hálózatot, alhálózatot és egy megerősített gazdagépet. 

A megerősített gazdagép a magánhálózati végpont teszteléséhez a virtuális géphez való biztonságos kapcsolódást fogja használni.

Hozzon létre egy virtuális hálózatot és egy megerősített gazdagépet az alábbiakkal:

* [Új – AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)
* [Új – AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)
* [Új – AzBastion](/powershell/module/az.network/new-azbastion)

```azurepowershell-interactive
## Create backend subnet config. ##
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name myBackendSubnet -AddressPrefix 10.0.0.0/24

## Create Azure Bastion subnet. ##
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig -Name AzureBastionSubnet -AddressPrefix 10.0.1.0/24

## Create the virtual network. ##
$parameters1 = @{
    Name = 'MyVNet'
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Location = 'eastus'
    AddressPrefix = '10.0.0.0/16'
    Subnet = $subnetConfig, $bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @parameters1

## Create public IP address for bastion host. ##
$parameters2 = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @parameters2

## Create bastion host ##
$parameters3 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @parameters3
```

Az Azure Bastion-gazdagép üzembe helyezése néhány percet is igénybe vehet.

## <a name="create-test-virtual-machine"></a>Teszt virtuális gép létrehozása

Ebben a szakaszban létre fog hozni egy virtuális gépet, amely a privát végpont tesztelésére szolgál.

A virtuális gép létrehozása a rel:

  * [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)
  * [Új – AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) 
  * [New-AzVM](/powershell/module/az.compute/new-azvm)
  * [Új – AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
  * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
  * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
  * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


```azurepowershell-interactive
## Set credentials for server admin and password. ##
$cred = Get-Credential

## Command to get virtual network configuration. ##
$vnet = Get-AzVirtualNetwork -Name myVNet -ResourceGroupName CreateSQLEndpointTutorial-rg

## Command to create network interface for VM ##
$parameters1 = @{
    Name = 'myNicVM'
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
}
$nicVM = New-AzNetworkInterface @parameters1

## Create a virtual machine configuration.##
$parameters2 = @{
    VMName = 'myVM'
    VMSize = 'Standard_DS1_v2'
}
$parameters3 = @{
    ComputerName = 'myVM'
    Credential = $cred
}
$parameters4 = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'
}
$vmConfig = 
New-AzVMConfig @parameters2 | Set-AzVMOperatingSystem -Windows @parameters3 | Set-AzVMSourceImage @parameters4 | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName 'CreateSQLEndpointTutorial-rg' -Location 'eastus' -VM $vmConfig
```

## <a name="create-an-azure-sql-server"></a>Azure SQL-kiszolgáló létrehozása

Ebben a szakaszban egy SQL-kiszolgálót és-adatbázist fog létrehozni a következő használatával:

* [Új – AzSqlServer](/powershell/module/az.sql/new-azsqlserver)
* [Új – AzSQlDatabase](/powershell/module/az.sql/new-azsqldatabase)

Hozzon létre SQL Servert és adatbázist. Cserélje le az **\<sql-server-name>** nevet az egyedi kiszolgálónévre:

```azurepowershell-interactive
## Set and administrator and password for the SQL server. ##
$cred = Get-Credential

## Create SQL server ##
$parameters1 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    ServerName = '<sql-server-name>'
    SqlAdministratorCredentials = $cred
    Location = 'eastus'
}
New-AzSqlServer @parameters1

## Create database. ##
$parameters2 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    ServerName = '<sql-server-name>'
    DatabaseName = 'mysqldatabase'
    RequestedServiceObjectiveName = 'S0'
    SampleName = 'AdventureWorksLT'
}
New-AzSqlDatabase @parameters2
```

## <a name="create-private-endpoint"></a>Privát végpont létrehozása

Ebben a szakaszban a következő módon hozza létre a privát végpontot és a kapcsolatokat a használatával:

* [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection)
* [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint)

```azurepowershell-interactive
## Place SQL server into variable. Replace <sql-server-name> with your server name ##
$server = Get-AzSqlServer -ResourceGroupName CreateSQLEndpointTutorial-rg -ServerName <sql-server-name>

## Create private endpoint connection. ##
$parameters1 = @{
    Name = 'myConnection'
    PrivateLinkServiceId = $server.ResourceID
    GroupID = 'sqlserver'
}
$privateEndpointConnection = New-AzPrivateLinkServiceConnection @parameters1

## Place virtual network into variable. ##
$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreateSQLEndpointTutorial-rg' -Name 'myVNet'

## Disable private endpoint network policy ##
$vnet.Subnets[0].PrivateEndpointNetworkPolicies = "Disabled"
$vnet | Set-AzVirtualNetwork

## Create private endpoint
$parameters2 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Name = 'myPrivateEndpoint'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
    PrivateLinkServiceConnection = $privateEndpointConnection
}
New-AzPrivateEndpoint @parameters2
```
## <a name="configure-the-private-dns-zone"></a>A magánhálózati DNS-zóna konfigurálása

Ebben a szakaszban a következő módon hozza létre és konfigurálja a magánhálózati DNS-zónát:

* [Új – AzPrivateDnsZone](/powershell/module/az.privatedns/new-azprivatednszone)
* [Új – AzPrivateDnsVirtualNetworkLink](/powershell/module/az.privatedns/new-azprivatednsvirtualnetworklink)
* [Új – AzPrivateDnsZoneConfig](/powershell/module/az.network/new-azprivatednszoneconfig)
* [Új – AzPrivateDnsZoneGroup](/powershell/module/az.network/new-azprivatednszonegroup)

```azurepowershell-interactive
## Place virtual network into variable. ##
$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreateSQLEndpointTutorial-rg' -Name 'myVNet'

## Create private dns zone. ##
$parameters1 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Name = 'privatelink.database.windows.net'
}
$zone = New-AzPrivateDnsZone @parameters1

## Create dns network link. ##
$parameters2 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    ZoneName = 'privatelink.database.windows.net'
    Name = 'myLink'
    VirtualNetworkId = $vnet.Id
}
$link = New-AzPrivateDnsVirtualNetworkLink @parameters2

## Create DNS configuration ##
$parameters3 = @{
    Name = 'privatelink.database.windows.net'
    PrivateDnsZoneId = $zone.ResourceId
}
$config = New-AzPrivateDnsZoneConfig @parameters3

## Create DNS zone group. ##
$parameters4 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    PrivateEndpointName = 'myPrivateEndpoint'
    Name = 'myZoneGroup'
    PrivateDnsZoneConfig = $config
}
New-AzPrivateDnsZoneGroup @parameters4
```

## <a name="test-connectivity-to-private-endpoint"></a>A magánhálózati végponthoz való kapcsolódás tesztelése

Ebben a szakaszban az előző lépésben létrehozott virtuális gépet fogja használni az SQL Serverhez való kapcsolódáshoz a privát végponton keresztül.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) 
 
2. Válassza az **erőforráscsoportok** lehetőséget a bal oldali navigációs ablaktáblán.

3. Válassza a **CreateSQLEndpointTutorial-RG** elemet.

4. Válassza a **myVM** lehetőséget.

5. A **myVM** áttekintés lapján válassza a **kapcsolat** , majd a **megerősített** lehetőséget.

6. Válassza a kék **használat Bastion** gombot.

7. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

8. A kapcsolat után nyissa meg a Windows PowerShellt a kiszolgálón.

9. Írja be a következő szöveget: `nslookup <sqlserver-name>.database.windows.net`. A helyére írja **\<sqlserver-name>** be az előző lépésben létrehozott SQL Server nevét.  A következőhöz hasonló üzenet jelenik meg:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    A rendszer a **10.0.0.5** magánhálózati IP-címét adja vissza az SQL-kiszolgáló nevéhez.  Ez a címe a korábban létrehozott virtuális hálózat alhálózatában található.


10. Telepítse a [SQL Server Management Studiot](/sql/ssms/download-sql-server-management-studio-ssms?preserve-view=true&view=sql-server-2017) a **myVM**.

11. Nyissa meg **SQL Server Management Studio**.

12. A **Kapcsolódás a kiszolgálóhoz** lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Server type (Kiszolgáló típusa) | Válassza a **Database Engine** (Adatbázismotor) lehetőséget.|
    | Kiszolgálónév | Adja meg a **\<sql-server-name> . database.Windows.net** |
    | Hitelesítés | Válassza ki az **SQL Server-hitelesítés** lehetőséget. |
    | Felhasználónév | Adja meg a kiszolgáló létrehozásakor megadott felhasználónevet |
    | Jelszó | Adja meg a kiszolgáló létrehozásakor megadott jelszót |
    | Jelszó megjegyzése | Válassza az **Igen** lehetőséget. |

13. Válassza a **Kapcsolódás** lehetőséget.

14. Az adatbázisok tallózása a bal oldali menüben.

15. Opcionálisan Információk létrehozása vagy lekérdezése a **mysqldatabase**.

16. A **myVM** felé irányuló megerősített kapcsolódás lezárása. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 
Ha végzett a magánhálózati végpont, az SQL Server és a virtuális gép használatával, törölje az erőforráscsoportot és az összes benne lévő erőforrást: 

1. Írja be a **CreateSQLEndpointTutorial-RG** kifejezést a portál felső részén található **keresőmezőbe** , és válassza ki a **CreateSQLEndpointTutorial-RG** elemet a keresési eredmények közül. 

2. Válassza az **Erőforráscsoport törlése** elemet. 

3. Írja be az **CreateSQLEndpointTutorial-RG** értéket **az erőforráscsoport nevének** beírásához, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a következőket hozta létre:

* Virtuális hálózat és a megerősített gazdagép.
* Virtuális gép.
* Azure SQL Server magánhálózati végponttal.

A virtuális gépet arra használta, hogy biztonságosan tesztelje a kapcsolatot az SQL Serverhez a magánhálózati végponton keresztül.

A következő lépésként a webalkalmazás az Azure SQL Database architektúra-forgatókönyvhöz való [privát kapcsolattal](https://docs.microsoft.com/azure/architecture/example-scenario/private-web-app/private-web-app) is érdekli, amely a virtuális hálózaton kívül egy webalkalmazást kapcsol össze egy adatbázis privát végpontján.

Megtudhatja, hogyan hozhat létre privát hivatkozási szolgáltatást:
> [!div class="nextstepaction"]
> [Privát kapcsolati szolgáltatás létrehozása](create-private-link-service-portal.md)
---
title: Rövid útmutató – Azure Private-végpont létrehozása Azure PowerShell használatával
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy privát végpontot a Azure PowerShell használatával.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 11/02/2020
ms.author: allensu
ms.openlocfilehash: afd1f8b6c80cfd7aa8d5142841458d76511a9e96
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102562911"
---
# <a name="quickstart-create-an-azure-private-endpoint-using-azure-powershell"></a>Rövid útmutató: Azure Private-végpont létrehozása Azure PowerShell használatával

Ismerkedjen meg az Azure Private-hivatkozással egy privát végpont használatával, amellyel biztonságosan csatlakozhat egy Azure-webalkalmazáshoz.

Ebben a rövid útmutatóban létrehoz egy privát végpontot egy Azure-webalkalmazáshoz, és üzembe helyez egy virtuális gépet a magánhálózati kapcsolat teszteléséhez.  

A magánhálózati végpontok különböző típusú Azure-szolgáltatásokhoz, például az Azure SQL-hez és az Azure Storage-hoz is létrehozhatók.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Azure-webalkalmazás az Azure **-** előfizetésében üzembe helyezett PremiumV2 vagy magasabb szintű app Service-csomaggal.  
    * További információkat és példákat a rövid útmutató [: ASP.net Core Webalkalmazás létrehozása az Azure-ban](../app-service/quickstart-dotnetcore.md)című témakörben talál. 
    * A webalkalmazások és végpontok létrehozásával kapcsolatos részletes oktatóanyagért lásd [: oktatóanyag: Kapcsolódás egy webalkalmazáshoz egy Azure Private-végpont használatával](tutorial-private-endpoint-webapp-portal.md).

Ha a PowerShell helyi telepítése és használata mellett dönt, ehhez a cikkhez az Azure PowerShell-modul 5.4.1-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor azt is futtatnia kell, `Connect-AzAccount` hogy létrehozza az Azure-hoz való kapcsolódást.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```azurepowershell-interactive
New-AzResourceGroup -Name 'CreatePrivateEndpointQS-rg' -Location 'eastus'
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
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Location = 'eastus'
    AddressPrefix = '10.0.0.0/16'
    Subnet = $subnetConfig, $bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @parameters1

## Create public IP address for bastion host. ##
$parameters2 = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @parameters2

## Create bastion host ##
$parameters3 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
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
$vnet = Get-AzVirtualNetwork -Name myVNet -ResourceGroupName CreatePrivateEndpointQS-rg

## Command to create network interface for VM ##
$parameters1 = @{
    Name = 'myNicVM'
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
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
New-AzVM -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Location 'eastus' -VM $vmConfig
```

## <a name="create-private-endpoint"></a>Privát végpont létrehozása

Ebben a szakaszban a következő módon hozza létre a privát végpontot és a kapcsolatokat a használatával:

* [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection)
* [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint)

```azurepowershell-interactive
## Place web app into variable. Replace <webapp-resource-group-name> with the resource group of your webapp. ##
## Replace <your-webapp-name> with your webapp name ##
$webapp = Get-AzWebApp -ResourceGroupName <webapp-resource-group-name> -Name <your-webapp-name>

## Create private endpoint connection. ##
$parameters1 = @{
    Name = 'myConnection'
    PrivateLinkServiceId = $webapp.ID
    GroupID = 'sites'
}
$privateEndpointConnection = New-AzPrivateLinkServiceConnection @parameters1

## Place virtual network into variable. ##
$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Name 'myVNet'

## Disable private endpoint network policy ##
$vnet.Subnets[0].PrivateEndpointNetworkPolicies = "Disabled"
$vnet | Set-AzVirtualNetwork

## Create private endpoint
$parameters2 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
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
$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Name 'myVNet'

## Create private dns zone. ##
$parameters1 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Name = 'privatelink.azurewebsites.net'
}
$zone = New-AzPrivateDnsZone @parameters1

## Create dns network link. ##
$parameters2 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    ZoneName = 'privatelink.azurewebsites.net'
    Name = 'myLink'
    VirtualNetworkId = $vnet.Id
}
$link = New-AzPrivateDnsVirtualNetworkLink @parameters2

## Create DNS configuration ##
$parameters3 = @{
    Name = 'privatelink.azurewebsites.net'
    PrivateDnsZoneId = $zone.ResourceId
}
$config = New-AzPrivateDnsZoneConfig @parameters3

## Create DNS zone group. ##
$parameters4 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
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

3. Válassza a **CreatePrivateEndpointQS-RG** elemet.

4. Válassza a **myVM** lehetőséget.

5. A **myVM** áttekintés lapján válassza a **kapcsolat** , majd a **megerősített** lehetőséget.

6. Válassza a kék **használat Bastion** gombot.

7. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

8. A kapcsolat után nyissa meg a Windows PowerShellt a kiszolgálón.

9. Írja be a következő szöveget: `nslookup <your-webapp-name>.azurewebsites.net`. A helyére írja **\<your-webapp-name>** be az előző lépésekben létrehozott webalkalmazás nevét.  A következőhöz hasonló üzenet jelenik meg:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.0.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    A rendszer a webalkalmazás neveként a **10.0.0.5** magánhálózati IP-címét adja vissza.  Ez a címe a korábban létrehozott virtuális hálózat alhálózatában található.

10. Nyissa meg az Internet Explorert a **myVM**-ben lévő megerősített kapcsolódásban.

11. Adja meg a webalkalmazás ( **https:// \<your-webapp-name> . azurewebsites.net**) URL-címét.

12. Ha még nem telepítette az alkalmazást, az alapértelmezett webalkalmazás-oldal jelenik meg:

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="Alapértelmezett webalkalmazás lap." border="true":::

13. A **myVM** létesített kapcsolatok lezárása.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 
Ha végzett a privát végpont és a virtuális gép használatával, a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) használatával távolítsa el az erőforráscsoportot és az összes erőforrást:

```azurepowershell-interactive
Remove-AzResourceGroup -Name CreatePrivateEndpointQS-rg -Force
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozta a következőket:

* Virtuális hálózat és a megerősített gazdagép.
* Virtuális gép.
* Privát végpont egy Azure-webalkalmazáshoz.

A virtuális gépet arra használta, hogy biztonságosan tesztelje a kapcsolatot a webalkalmazáshoz a privát végponton keresztül.

A privát végpontot támogató szolgáltatásokkal kapcsolatos további információkért lásd:
> [!div class="nextstepaction"]
> [Privát kapcsolat elérhetősége](private-link-overview.md#availability)

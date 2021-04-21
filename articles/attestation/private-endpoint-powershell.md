---
title: Privát végpont létrehozása a Azure PowerShell
description: Privát végpont létrehozása Azure Attestation Azure PowerShell
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 03/26/2021
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ceefd4695583822536d8cc4c14614af7f6736f70
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830123"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-powershell"></a>Rövid útmutató: Privát végpont létrehozása a Azure PowerShell

Az Azure Private Link használatának első lépésekhez egy privát végponttal csatlakozhat biztonságosan a Azure Attestation.

Ebben a rövid útmutatóban létrehoz egy privát végpontot a Azure Attestation és üzembe helyez egy virtuális gépet a privát kapcsolat teszteléséhez.  

> [!NOTE]
> A jelenlegi implementáció csak az automatikus jóváhagyási lehetőséget tartalmazza. Az előfizetést hozzá kell adni egy engedélyező listához, hogy folytatni tudja a privát végpont létrehozását. Az alábbi lépések előtt forduljon a szolgáltatási csapathoz, vagy küldjön Azure-támogatás a Azure-támogatás [oldalon.](https://azure.microsoft.com/support/options/)

## <a name="prerequisites"></a>Előfeltételek

* További információ a [Azure Private Link](../private-link/private-link-overview.md)
* [A Azure Attestation beállítása Azure PowerShell](./quickstart-powershell.md)

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup segítségével:](/powershell/module/az.resources/new-azresourcegroup)

```azurepowershell-interactive
## Create to your Azure account subscription and create a resource group in a desired location. ##
Connect-AzAccount
Set-AzSubscription “mySubscription”
$rg = “CreateAttestationPrivateLinkTutorial-rg”
$loc= "eastus”
New-AzResourceGroup -Name $rg -Location $loc
```

## <a name="create-a-virtual-network-and-bastion-host"></a>Virtuális hálózat és megerősített gazdagép létrehozása

Ebben a szakaszban egy virtuális hálózatot, egy alhálózatot és egy megerősített gazdagépet fog létrehozni. 

A bástyagazdagép segítségével biztonságosan csatlakozhat a virtuális géphez a privát végpont tesztelése céljából.

Hozzon létre egy virtuális hálózatot és egy megerősített gazdagépet a következővel:

* [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)
* [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)
* [New-AzBastion](/powershell/module/az.network/new-azbastion)

```azurepowershell-interactive
## Create backend subnet config. ##
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name myBackendSubnet -AddressPrefix 10.0.0.0/24

## Create Azure Bastion subnet. ##
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig -Name AzureBastionSubnet -AddressPrefix 10.0.1.0/24

## Create the virtual network. ##
$vnet = New-AzVirtualNetwork -Name "myAttestationTutorialVNet" -ResourceGroupName $rg -Location $loc -AddressPrefix "10.0.0.0/16" -Subnet $subnetConfig, $bastsubnetConfig

## Create public IP address for bastion host. ##
$publicip = New-AzPublicIpAddress -Name "myBastionIP" -ResourceGroupName $rg -Location $loc -Sku "Standard" -AllocationMethod "Static"

## Create bastion host ##
New-AzBastion -ResourceGroupName $rg -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
```

A gazdagép üzembe helyezése eltarthat néhány Azure Bastion.

## <a name="create-test-virtual-machine"></a>Teszt virtuális gép létrehozása

Ebben a szakaszban egy virtuális gépet fog létrehozni, amely a privát végpont tesztelésére lesz használva.

Hozza létre a virtuális gépet a következővel:

  * [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)
  * [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) 
  * [New-AzVM](/powershell/module/az.compute/new-azvm)
  * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
  * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
  * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
  * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


```azurepowershell-interactive
## Set credentials for server admin and password. ##
$cred = Get-Credential

## Command to create network interface for VM ##
$nicVM = New-AzNetworkInterface -Name "myNicVM" -ResourceGroupName $rg -Location $loc -Subnet $vnet.Subnets[0] 

## Create a virtual machine configuration.##
$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize "Standard_DS1_v2" | Set-AzVMOperatingSystem -Windows -ComputerName "myVM" -Credential $cred | Set-AzVMSourceImage -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2019-Datacenter" -Version "latest" | Add-AzVMNetworkInterface -Id $nicVM.Id 

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig
```

## <a name="create-an-attestation-provider"></a>Igazolásszolgáltató létrehozása

```azurepowershell-interactive
## Create an attestation provider ##
$attestationProviderName = "myattestationprovider"
$attestationProvider = New-AzAttestation -Name $attestationProviderName -ResourceGroupName $rg -Location $loc
$attestationProviderId = $attestationProvider.Id

## Access the attestation provider from local machine ##
Enter nslookup <provider-name>.attest.azure.net. Replace <provider-name> with the name of the attestation provider instance you created in the previous steps. 

You'll receive a message similar to what is displayed below:

## PowerShell copy. ##
nslookup myattestationprovider.eus.attest.azure.net

Server:  cdns01.comcast.net
Address:  2001:558:feed::1

Non-authoritative answer:
Name:    eus.service.attest.azure.net
Address:  20.62.219.160
Aliases:  myattestationprovider.eus.attest.azure.net
    attesteusatm.trafficmanager.net
```

## <a name="create-private-endpoint"></a>Privát végpont létrehozása

Ebben a szakaszban a privát végpontot és a kapcsolatot a következővel fogja létrehozni:

* [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection)
* [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint)

```azurepowershell-interactive
## Create private endpoint connection. ##
$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnection" -PrivateLinkServiceId $attestationProviderId -GroupID "Standard"

## Disable private endpoint network policy ##
 $vnet.Subnets[0].PrivateEndpointNetworkPolicies = "Disabled" 
$vnet | Set-AzVirtualNetwork

## Create private endpoint
New-AzPrivateEndpoint  -ResourceGroupName $rg -Name "myPrivateEndpoint" -Location $loc -Subnet $vnet.Subnets[0] -PrivateLinkServiceConnection $privateEndpointConnection
```
## <a name="configure-the-private-dns-zone"></a>A privát DNS-zóna konfigurálása

Ebben a szakaszban a privát DNS-zónát a következővel fogja létrehozni és konfigurálni:

* [New-AzPrivateDnsZone](/powershell/module/az.privatedns/new-azprivatednszone)
* [New-AzPrivateDnsVirtualNetworkLink](/powershell/module/az.privatedns/new-azprivatednsvirtualnetworklink)
* [New-AzPrivateDnsZoneConfig](/powershell/module/az.network/new-azprivatednszoneconfig)
* [New-AzPrivateDnsZoneGroup](/powershell/module/az.network/new-azprivatednszonegroup)

```azurepowershell-interactive
## Create private dns zone. ##
$zone = New-AzPrivateDnsZone -ResourceGroupName $rg -Name "privatelink.attest.azure.net"

## Create dns network link. ##
$link = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $rg -ZoneName "privatelink.attest.azure.net" -Name "myLink" -VirtualNetworkId $vnet.Id

## Create DNS configuration ##
$config = New-AzPrivateDnsZoneConfig -Name "privatelink.attest.azure.net" -PrivateDnsZoneId $zone.ResourceId

## Create DNS zone group. ##
New-AzPrivateDnsZoneGroup -ResourceGroupName $rg -PrivateEndpointName "myPrivateEndpoint" -Name "myZoneGroup" -PrivateDnsZoneConfig $config
```

## <a name="test-connectivity-to-private-endpoint"></a>Privát végponttal való kapcsolat tesztelése

Ebben a szakaszban az előző lépésben létrehozott virtuális gépet fogja használni az SQL-kiszolgálóhoz való csatlakozáshoz a privát végponton keresztül.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) 
 
2. A **bal oldali navigációs** panelen válassza az Erőforráscsoportok lehetőséget.

3. Válassza **a CreateAttestationPrivateLinkTutorial-rg lehetőséget.**

4. Válassza **a myVM lehetőséget.**

5. A **myVM áttekintő oldalán** válassza a **Csatlakozás,** majd **a Bastion lehetőséget.**

6. Kattintson a kék **Bastion használata gombra.**

7. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

8. Csatlakozás Windows PowerShell nyissa meg a kiszolgálón a következőt: .

9. Írja be a következő szöveget: `nslookup <provider-name>.attest.azure.net`. Cserélje le a helyére az előző lépésekben létrehozott **\<provider-name>** igazolásszolgáltató-példány nevét. Az alábbihoz hasonló üzenet jelenik meg:

    ```powershell

    ## Access the attestation provider from local machine ##
    nslookup myattestationprovider.eus.attest.azure.net

    Server:  cdns01.comcast.net
    Address:  2001:558:feed::1

    cdns01.comcast.net can't find myattestationprovider.eus.attest.azure.net: Non-existent domain

    ## Access the attestation provider from the VM created in the same virtual network as the private endpoint.   ##
    nslookup myattestationprovider.eus.attest.azure.net

    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myattestationprovider.eastus.test.attest.azure.net
    ```

---
title: Azure Firewall üzembe helyezése és konfigurálása Azure PowerShell használatával
description: Ebből a cikkből megtudhatja, hogyan telepítheti és konfigurálhatja a Azure Firewallt a Azure PowerShell használatával.
services: firewall
author: vhorne
ms.service: firewall
ms.date: 12/03/2020
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: dc36d45e226cffafb51cf7aa09ea6f0d528ee016
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98051377"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-powershell"></a>Azure Firewall üzembe helyezése és konfigurálása Azure PowerShell használatával

A kimenő hálózati hozzáférés ellenőrzése az általános hálózati biztonsági terv fontos részét képezi. Előfordulhat például, hogy korlátozni szeretné a webhelyekhez való hozzáférést. Az is előfordulhat, hogy korlátozni szeretné az elérhető kimenő IP-címeket és portokat.

Az Azure-alhálózatok kimenő hálózati hozzáférése többek között az Azure Firewall használatával vezérelhető. Az Azure Firewall segítségével a következőket konfigurálhatja:

* Alkalmazásszabályokat, amelyek egy alhálózatról elérhető teljes tartományneveket (FQDN) határoznak meg.
* Hálózatszabályokat, amelyek forráscímet, protokollt, valamint célportot és célcímet határoznak meg.

A hálózati forgalmat a konfigurált tűzfalszabályok irányítják, ha alapértelmezett alhálózati átjáróként irányítja a tűzfalhoz a forgalmat.

Ebben a cikkben egy egyszerűsített egyszeri VNet hoz létre három alhálózattal az egyszerű üzembe helyezéshez. Éles környezetekben a [hub és a küllős modell](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) használata ajánlott, ahol a tűzfal a saját VNet van. A munkaterhelés-kiszolgálók egy vagy több alhálózattal azonos régióban lévő, egymással azonos régióba tartozó virtuális hálózatok találhatók.

* **AzureFirewallSubnet** – ezen az alhálózaton található a tűzfal.
* **Workload-SN** – ezen az alhálózaton található a számítási feladat kiszolgálója. Ennek az alhálózatnak a hálózati forgalma a tűzfalon halad át.
* **AzureBastionSubnet** – az Azure-hoz való kapcsolódáshoz használt alhálózat, amely a munkaterhelés-kiszolgálóhoz való kapcsolódásra szolgál. További információ az Azure Bastion-ről: [Mi az az Azure Bastion?](../bastion/bastion-overview.md)

![Az oktatóanyag hálózati infrastruktúrája](media/deploy-ps/tutorial-network.png)

Ebben a cikkben az alábbiakkal ismerkedhet meg:


* Tesztelési hálózati környezet beállítása
* Tűzfal üzembe helyezése
* Alapértelmezett útvonal létrehozása
* Alkalmazás-szabály konfigurálása a www.google.com való hozzáférés engedélyezéséhez
* Hálózatszabály konfigurálása külső DNS-kiszolgálókhoz való hozzáférés engedélyezéséhez
* A tűzfal tesztelése

Ha szeretné, ezt az eljárást a [Azure Portal](tutorial-firewall-deploy-portal.md)használatával végezheti el.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

Ehhez az eljáráshoz helyileg kell futtatni a PowerShellt. Telepítenie kell a Azure PowerShell-modult. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. A PowerShell-verzió ellenőrzése után futtassa az `Connect-AzAccount` parancsot az Azure-hoz való kapcsolódáshoz.

## <a name="set-up-the-network"></a>A hálózat beállítása

Először is hozzon létre egy erőforráscsoportot, amely a tűzfal üzembe helyezéséhez szükséges erőforrásokat tartalmazza. Ezután hozzon létre egy virtuális hálózatot, alhálózatokat és tesztkiszolgálókat.

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport az üzemelő példány összes erőforrását tartalmazza.

```azurepowershell
New-AzResourceGroup -Name Test-FW-RG -Location "East US"
```

### <a name="create-a-virtual-network-and-azure-bastion-host"></a>Virtuális hálózat és Azure Bastion-gazdagép létrehozása

A virtuális hálózat három alhálózattal rendelkezik:

> [!NOTE]
> A AzureFirewallSubnet-alhálózat mérete/26. További információ az alhálózat méretétől: [Azure Firewall GYIK](firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size).

```azurepowershell
$Bastionsub = New-AzVirtualNetworkSubnetConfig -Name AzureBastionSubnet -AddressPrefix 10.0.0.0/27
$FWsub = New-AzVirtualNetworkSubnetConfig -Name AzureFirewallSubnet -AddressPrefix 10.0.1.0/26
$Worksub = New-AzVirtualNetworkSubnetConfig -Name Workload-SN -AddressPrefix 10.0.2.0/24
```
Most hozza létre a virtuális hálózatot:

```azurepowershell
$testVnet = New-AzVirtualNetwork -Name Test-FW-VN -ResourceGroupName Test-FW-RG `
-Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $Bastionsub, $FWsub, $Worksub
```
### <a name="create-public-ip-address-for-azure-bastion-host"></a>Nyilvános IP-cím létrehozása az Azure Bastion-gazdagéphez

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName Test-FW-RG -Location "East US" `
   -Name Bastion-pip -AllocationMethod static -Sku standard
```

### <a name="create-azure-bastion-host"></a>Azure Bastion-gazdagép létrehozása

```azurepowershell
New-AzBastion -ResourceGroupName Test-FW-RG -Name Bastion-01 -PublicIpAddress $publicip -VirtualNetwork $testVnet
```
### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Most hozza létre a munkaterhelés virtuális gépet, és helyezze el a megfelelő alhálózatba.
Amikor a rendszer kéri, adjon meg egy felhasználónevet és jelszót a virtuális gép számára.


Hozzon létre egy munkaterhelés virtuális gépet.
Amikor a rendszer kéri, adjon meg egy felhasználónevet és jelszót a virtuális gép számára.

```azurepowershell
#Create the NIC
$wsn = Get-AzVirtualNetworkSubnetConfig -Name  Workload-SN -VirtualNetwork $testvnet
$NIC01 = New-AzNetworkInterface -Name Srv-Work -ResourceGroupName Test-FW-RG -Location "East us" -Subnet $wsn

#Define the virtual machine
$VirtualMachine = New-AzVMConfig -VMName Srv-Work -VMSize "Standard_DS2"
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Srv-Work -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC01.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2019-Datacenter' -Version latest

#Create the virtual machine
New-AzVM -ResourceGroupName Test-FW-RG -Location "East US" -VM $VirtualMachine -Verbose
```

## <a name="deploy-the-firewall"></a>A tűzfal üzembe helyezése

Most telepítse a tűzfalat a virtuális hálózatba.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzPublicIpAddress -Name "fw-pip" -ResourceGroupName Test-FW-RG `
  -Location "East US" -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzFirewall -Name Test-FW01 -ResourceGroupName Test-FW-RG -Location "East US" -VirtualNetwork $testVnet -PublicIpAddress $FWpip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP
```

Jegyezze fel a magánhálózati IP-címet. Később, az alapértelmezett útvonal létrehozásakor szükség lesz rá.

## <a name="create-a-default-route"></a>Alapértelmezett útvonal létrehozása

Tábla létrehozása a BGP-útvonal propagálásával letiltva

```azurepowershell
$routeTableDG = New-AzRouteTable `
  -Name Firewall-rt-table `
  -ResourceGroupName Test-FW-RG `
  -location "East US" `
  -DisableBgpRoutePropagation

#Create a route
 Add-AzRouteConfig `
  -Name "DG-Route" `
  -RouteTable $routeTableDG `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $testVnet `
  -Name Workload-SN `
  -AddressPrefix 10.0.2.0/24 `
  -RouteTable $routeTableDG | Set-AzVirtualNetwork
```

## <a name="configure-an-application-rule"></a>Alkalmazásszabály konfigurálása

Az alkalmazási szabály lehetővé teszi a kimenő hozzáférést a www.google.com.

```azurepowershell
$AppRule1 = New-AzFirewallApplicationRule -Name Allow-Google -SourceAddress 10.0.2.0/24 `
  -Protocol http, https -TargetFqdn www.google.com

$AppRuleCollection = New-AzFirewallApplicationRuleCollection -Name App-Coll01 `
  -Priority 200 -ActionType Allow -Rule $AppRule1

$Azfw.ApplicationRuleCollections.Add($AppRuleCollection)

Set-AzFirewall -AzureFirewall $Azfw
```

Az Azure Firewall tartalmaz egy beépített szabálygyűjteményt az infrastruktúra alapértelmezés szerint engedélyezett teljes tartományneveiről. Ezek a teljes tartománynevek csak az adott platformra vonatkoznak, egyéb célra nem használhatók. További információ: [Infrastruktúra FQDN-jei](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Hálózatszabály konfigurálása

A hálózati szabály lehetővé teszi a kimenő hozzáférést két IP-címhez a 53-as porton (DNS).

```azurepowershell
$NetRule1 = New-AzFirewallNetworkRule -Name "Allow-DNS" -Protocol UDP -SourceAddress 10.0.2.0/24 `
   -DestinationAddress 209.244.0.3,209.244.0.4 -DestinationPort 53

$NetRuleCollection = New-AzFirewallNetworkRuleCollection -Name RCNet01 -Priority 200 `
   -Rule $NetRule1 -ActionType "Allow"

$Azfw.NetworkRuleCollections.Add($NetRuleCollection)

Set-AzFirewall -AzureFirewall $Azfw
```

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Módosítsa az **Srv-Work** hálózati adapter elsődleges és másodlagos DNS-címét.

Ebben az eljárásban tesztelési célból konfigurálja a kiszolgáló elsődleges és másodlagos DNS-címét. Ez nem általános Azure Firewall követelmény.

```azurepowershell
$NIC01.DnsSettings.DnsServers.Add("209.244.0.3")
$NIC01.DnsSettings.DnsServers.Add("209.244.0.4")
$NIC01 | Set-AzNetworkInterface
```

## <a name="test-the-firewall"></a>A tűzfal tesztelése

Most tesztelje a tűzfalat, és ellenőrizze, hogy az a várt módon működik-e.

1. Kapcsolódjon az **SRV-Work** virtuális géphez a Bastion használatával, és jelentkezzen be. 

   :::image type="content" source="media/deploy-ps/bastion.png" alt-text="Kapcsolódjon a Bastion használatával.":::

3. Az **SRV-Work** lapon nyisson meg egy PowerShell-ablakot, és futtassa a következő parancsokat:

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   Mindkét parancsnak válaszokat kell visszaadnia, ami azt mutatja, hogy a DNS-lekérdezések bekerülnek a tűzfalon.

1. Futtassa az alábbi parancsot:

   ```
   Invoke-WebRequest -Uri https://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri https://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   A `www.google.com` kérések sikeresek lesznek, és a `www.microsoft.com` kérések sikertelenek lesznek. Ez azt mutatja, hogy a tűzfalszabályok a várt módon működnek.

Most ellenőrizte, hogy a tűzfalszabályok működnek-e:

* Fel tudja oldani a DNS-neveket a konfigurált külső DNS-kiszolgálóval.
* Az egyetlen engedélyezett FQDN-t el tudja érni, de másokat nem.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A következő oktatóanyagban megtarthatja a tűzfal erőforrásait, vagy ha már nincs rá szükség, törölje a **test-FW-RG** erőforráscsoportot az összes tűzfalhoz kapcsolódó erőforrás törléséhez:

```azurepowershell
Remove-AzResourceGroup -Name Test-FW-RG
```

## <a name="next-steps"></a>Következő lépések

* [Oktatóanyag: Az Azure Firewall naplóinak monitorozása](./firewall-diagnostics.md)

---
title: Az Azure DS tartományi szolgáltatások engedélyezése a PowerShell használatával | Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja és engedélyezheti Azure Active Directory Domain Services az Azure AD PowerShell és a Azure PowerShell használatával.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: sample
ms.date: 03/10/2021
ms.author: justinha
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8056e95b731b1818e10d7415cb813d6aba0ec7fa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103149067"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Azure Active Directory Domain Services engedélyezése a PowerShell használatával

Azure Active Directory Domain Services (Azure AD DS) olyan felügyelt tartományi szolgáltatásokat biztosít, mint például a tartományhoz való csatlakozás, a csoportházirend, az LDAP, a Kerberos/NTLM hitelesítés, amely teljes mértékben kompatibilis a Windows Server Active Directoryekkel. Ezeket a tartományi szolgáltatásokat a tartományvezérlők üzembe helyezése, kezelése és javítása nélkül használhatja fel. Az Azure AD DS integrálható a meglévő Azure AD-Bérlővel. Ez az integráció lehetővé teszi, hogy a felhasználók a vállalati hitelesítő adataikkal jelentkezzenek be, és meglévő csoportokat és felhasználói fiókokat is használhatnak az erőforrásokhoz való hozzáférés biztosításához.

Ez a cikk bemutatja, hogyan engedélyezheti az Azure AD DSt a PowerShell használatával.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

A cikk végrehajtásához a következő erőforrásokra van szükség:

* Az Azure PowerShell telepítése és konfigurálása.
    * Ha szükséges, kövesse az utasításokat a [Azure PowerShell modul telepítéséhez és az Azure-előfizetéshez való kapcsolódáshoz](/powershell/azure/install-az-ps).
    * Győződjön meg róla, hogy bejelentkezik az Azure-előfizetésbe a [AzAccount][Connect-AzAccount] parancsmag használatával.
* Az Azure AD PowerShell telepítése és konfigurálása.
    * Ha szükséges, kövesse az [Azure ad PowerShell-modul telepítésére és az Azure ad-hez való kapcsolódásra](/powershell/azure/active-directory/install-adv2)vonatkozó utasításokat.
    * Győződjön meg arról, hogy bejelentkezik az Azure AD-bérlőbe a [AzureAD][Connect-AzureAD] parancsmag használatával.
* Az Azure AD DS engedélyezéséhez *globális rendszergazdai* jogosultságok szükségesek az Azure ad-bérlőben.
* Az Azure-előfizetésben *közreműködő* jogosultságokkal kell rendelkeznie a szükséges Azure AD DS-erőforrások létrehozásához.

  > [!IMPORTANT]
  > Míg az az **. ADDomainServices** PowerShell-modul előzetes verzióban érhető el, a parancsmaggal külön kell telepítenie `Install-Module` .

  ```azurepowershell-interactive
  Install-Module -Name Az.ADDomainServices
  ```

## <a name="create-required-azure-ad-resources"></a>Szükséges Azure AD-erőforrások létrehozása

Az Azure AD DS a hitelesítéshez és a kommunikációhoz, valamint egy Azure AD-csoporthoz szükséges egyszerű szolgáltatást, amely meghatározza, hogy mely felhasználók rendelkeznek rendszergazdai jogosultságokkal a felügyelt tartományban.

Először hozzon létre egy Azure AD-szolgáltatásnevet egy, a *tartományvezérlői szolgáltatások* nevű adott alkalmazás-azonosító használatával. A nyilvános Azure-ban az azonosító értéke *2565bd9d-DA50-47d4-8B85-4c97f669dc36*. Más felhőkben az érték a *6ba9a5d4-8456-4118-b521-9c5ca10cdf84*. Ne módosítsa az alkalmazás AZONOSÍTÓját.

Hozzon létre egy Azure AD-szolgáltatásnevet a [New-azureadserviceprincipal parancsmagot][New-AzureADServicePrincipal] parancsmag használatával:

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Most hozzon létre egy *HRE DC-rendszergazdák* nevű Azure ad-csoportot. A csoportba felvett felhasználók engedélyeket kapnak az adminisztrációs feladatok végrehajtásához a felügyelt tartományon.

Először a [Get-AzureADGroup][Get-AzureADGroup] parancsmag használatával szerezze be a *HRE DC rendszergazdák* csoportjának azonosítóját. Ha a csoport nem létezik, hozza létre a *HRE DC-rendszergazdák* csoporttal a [New-AzureADGroup][New-AzureADGroup] parancsmag használatával:

```powershell
# First, retrieve the object ID of the 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# If the group doesn't exist, create it
if (!$GroupObjectId) {
  $GroupObjectId = New-AzureADGroup -DisplayName "AAD DC Administrators" `
    -Description "Delegated group to administer Azure AD Domain Services" `
    -SecurityEnabled $true `
    -MailEnabled $false `
    -MailNickName "AADDCAdministrators"
  }
else {
  Write-Output "Admin group already exists."
}
```

Ha létrehozta a *HRE DC-rendszergazdák* csoportot, szerezze be a kívánt felhasználó objektumazonosítót a [Get-AzureADUser][Get-AzureADUser] parancsmag használatával, majd adja hozzá a felhasználót a csoporthoz az [Add-AzureADGroupMember][Add-AzureADGroupMember] parancsmag használatával.

A következő példában a fiók felhasználói objektumának azonosítója egy egyszerű felhasználónévvel `admin@contoso.onmicrosoft.com` . Cserélje le ezt a felhasználói fiókot annak a felhasználónak a UPN-fiókjára, amelyet hozzá szeretne adni a *HRE DC-rendszergazdák* csoporthoz:

```powershell
# Retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása

Először regisztrálja a Azure AD Domain Services erőforrás-szolgáltatót a [Register-AzResourceProvider][Register-AzResourceProvider] parancsmag használatával:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Ezután hozzon létre egy erőforráscsoportot a [New-AzResourceGroup][New-AzResourceGroup] parancsmag használatával. A következő példában az erőforráscsoport neve *myResourceGroup* , és a *westus* régióban jön létre. Saját név és kívánt régió használata:

```azurepowershell-interactive
$ResourceGroupName = "myResourceGroup"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Hozza létre a virtuális hálózatot és az alhálózatokat a Azure AD Domain Serviceshoz. Két alhálózat jön létre – egy a *DomainServices*, egy pedig a számítási *feladatok* számára. Az Azure AD DS üzembe helyezése a dedikált *DomainServices* alhálózaton történik. Ne helyezzen üzembe más alkalmazásokat vagy munkaterheléseket ebbe az alhálózatba. A többi virtuális gép esetében használja a különálló *munkaterheléseket* vagy más alhálózatokat.

Hozza létre az alhálózatokat a [New-AzVirtualNetworkSubnetConfig][New-AzVirtualNetworkSubnetConfig] parancsmaggal, majd hozza létre a virtuális hálózatot a [New-AzVirtualNetwork][New-AzVirtualNetwork] parancsmag használatával.

```azurepowershell-interactive
$VnetName = "myVnet"

# Create the dedicated subnet for Azure AD Domain Services.
$SubnetName = "DomainServices"
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name $SubnetName `
  -AddressPrefix 10.0.0.0/24

# Create an additional subnet for your own VM workloads
$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet= New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```

### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Az Azure AD DS hálózati biztonsági csoportra van szüksége a felügyelt tartományhoz szükséges portok biztonságossá tételéhez és az összes többi bejövő forgalom blokkolásához. A [hálózati biztonsági csoport (NSG)][nsg-overview] olyan szabályokat tartalmaz, amelyek engedélyezik vagy megtagadják egy Azure-beli virtuális hálózat forgalmának hálózati forgalmát. Az Azure AD DS a hálózati biztonsági csoport egy további védelmi rétegként működik a felügyelt tartományhoz való hozzáférés zárolása érdekében. A szükséges portok megtekintéséhez tekintse meg a [hálózati biztonsági csoportok és a szükséges portok][network-ports]című témakört.

A következő PowerShell-parancsmagok a [New-AzNetworkSecurityRuleConfig][New-AzNetworkSecurityRuleConfig] használatával hozza létre a szabályokat, majd a [New-AzNetworkSecurityGroup][New-AzNetworkSecurityGroup] paranccsal hozza létre a hálózati biztonsági csoportot. Ezután a hálózati biztonsági csoport és a szabályok társítva lesznek a virtuális hálózat alhálózatához a [set-AzVirtualNetworkSubnetConfig][Set-AzVirtualNetworkSubnetConfig] parancsmag használatával.

```azurepowershell-interactive
$NSGName = "aaddsNSG"

# Create a rule to allow inbound TCP port 3389 traffic from Microsoft secure access workstations for troubleshooting
$nsg201 = New-AzNetworkSecurityRuleConfig -Name AllowRD `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 201 `
    -SourceAddressPrefix CorpNetSaw `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389

# Create a rule to allow TCP port 5986 traffic for PowerShell remote management
$nsg301 = New-AzNetworkSecurityRuleConfig -Name AllowPSRemoting `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 301 `
    -SourceAddressPrefix AzureActiveDirectoryDomainServices `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 5986

# Create the network security group and rules
$nsg = New-AzNetworkSecurityGroup -Name $NSGName `
    -ResourceGroupName $ResourceGroupName `
    -Location $AzureLocation `
    -SecurityRules $nsg201,$nsg301

# Get the existing virtual network resource objects and information
$vnet = Get-AzVirtualNetwork -Name $VnetName -ResourceGroupName $ResourceGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $SubnetName
$addressPrefix = $subnet.AddressPrefix

# Associate the network security group with the virtual network subnet
Set-AzVirtualNetworkSubnetConfig -Name $SubnetName `
    -VirtualNetwork $vnet `
    -AddressPrefix $addressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork
```

## <a name="create-a-managed-domain"></a>Felügyelt tartomány létrehozása

Most hozzon létre egy felügyelt tartományt. Állítsa be az Azure-előfizetés AZONOSÍTÓját, majd adja meg a felügyelt tartomány nevét (például *aaddscontoso.com*). Az előfizetés-azonosítót a [Get-AzSubscription][Get-AzSubscription] parancsmaggal kérheti le.

Ha olyan régiót választ, amely támogatja az Availability Zones-t, az Azure AD DS erőforrásai a további redundancia érdekében a zónák között oszlanak meg.

A rendelkezésreállási zónák fizikailag elkülönített helyek egy Azure-régión belül. Minden rendelkezésreállási zóna egy vagy több, független áramforrással, hűtéssel és hálózatkezelési megoldással ellátott adatközpontból áll. A rugalmasság biztosítása érdekében minden engedélyezett régióban legalább három elkülönített zóna található.

Nem kell konfigurálnia az Azure AD DS a zónák közötti elosztására. Az Azure platform automatikusan kezeli az erőforrások zónájának eloszlását. További információért és a régiók rendelkezésre állásának megtekintéséhez lásd: [Mi a Availability Zones az Azure-ban?][availability-zones].

```azurepowershell-interactive
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

# Enable Azure AD Domain Services for the directory.
$replicaSetParams = @{
  Location = $AzureLocation
  SubnetId = "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"
}
$replicaSet = New-AzADDomainServiceReplicaSetObject @replicaSetParams

$domainServiceParams = @{
  Name = $ManagedDomainName
  ResourceGroupName = $ResourceGroupName
  DomainName = $ManagedDomainName
  ReplicaSet = $replicaSet
}
New-AzADDomainService @domainServiceParams
```

Az erőforrás létrehozása és a vezérlés visszaküldése a PowerShell-parancssorba néhány percet vesz igénybe. A felügyelt tartomány továbbra is a háttérben lesz kiépítve, és akár egy óráig is eltarthat a telepítés befejezéséhez. A Azure Portal a felügyelt tartomány **Áttekintés** lapja a telepítési fázis aktuális állapotát jeleníti meg.

Ha a Azure Portal azt mutatja, hogy a felügyelt tartomány befejezte az üzembe helyezést, a következő feladatokat kell elvégezni:

* A virtuális hálózat DNS-beállításainak frissítése, hogy a virtuális gépek megtalálják a felügyelt tartományt a tartományhoz való csatlakozáshoz vagy a hitelesítéshez.
    * A DNS konfigurálásához válassza ki a felügyelt tartományt a portálon. Az **Áttekintés** ablakban a rendszer automatikusan konfigurálja ezeket a DNS-beállításokat.
* [Engedélyezze a jelszó-szinkronizálást az Azure AD DS](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) így a végfelhasználók a vállalati hitelesítő adataikkal jelentkezhetnek be a felügyelt tartományba.

## <a name="complete-powershell-script"></a>PowerShell-parancsfájl befejezése

A következő teljes PowerShell-parancsfájl ötvözi a cikkben látható összes feladatot. Másolja a szkriptet, és mentse egy `.ps1` kiterjesztésű fájlba. Futtassa a szkriptet egy helyi PowerShell-konzolon vagy a [Azure Cloud Shell][cloud-shell].

> [!NOTE]
> Az Azure AD DS engedélyezéséhez globális rendszergazdának kell lennie az Azure AD-bérlő számára. Emellett az Azure-előfizetésben legalább *közreműködő* jogosultsággal is rendelkeznie kell.

```azurepowershell-interactive
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso.onmicrosoft.com"
$ResourceGroupName = "myResourceGroup"
$VnetName = "myVnet"
$AzureLocation = "westus"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

# Connect to your Azure AD directory.
Connect-AzureAD

# Login to your Azure subscription.
Connect-AzAccount

# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"

# First, retrieve the object ID of the 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Create the delegated administration group for Azure AD Domain Services if it doesn't already exist.
if (!$GroupObjectId) {
  $GroupObjectId = New-AzureADGroup -DisplayName "AAD DC Administrators" `
    -Description "Delegated group to administer Azure AD Domain Services" `
    -SecurityEnabled $true `
    -MailEnabled $false `
    -MailNickName "AADDCAdministrators"
  }
else {
  Write-Output "Admin group already exists."
}

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq '$AaddsAdminUserUpn'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId

# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation

# Create the dedicated subnet for AAD Domain Services.
$SubnetName = "DomainServices"
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $AzureLocation `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet

$NSGName = "aaddsNSG"

# Create a rule to allow inbound TCP port 3389 traffic from Microsoft secure access workstations for troubleshooting
$nsg201 = New-AzNetworkSecurityRuleConfig -Name AllowRD `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 201 `
    -SourceAddressPrefix CorpNetSaw `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389

# Create a rule to allow TCP port 5986 traffic for PowerShell remote management
$nsg301 = New-AzNetworkSecurityRuleConfig -Name AllowPSRemoting `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 301 `
    -SourceAddressPrefix AzureActiveDirectoryDomainServices `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 5986

# Create the network security group and rules
$nsg = New-AzNetworkSecurityGroup -Name $NSGName `
    -ResourceGroupName $ResourceGroupName `
    -Location $AzureLocation `
    -SecurityRules $nsg201,$nsg301

# Get the existing virtual network resource objects and information
$vnet = Get-AzVirtualNetwork -Name $VnetName -ResourceGroupName $ResourceGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $SubnetName
$addressPrefix = $subnet.AddressPrefix

# Associate the network security group with the virtual network subnet
Set-AzVirtualNetworkSubnetConfig -Name $SubnetName `
    -VirtualNetwork $vnet `
    -AddressPrefix $addressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork

# Enable Azure AD Domain Services for the directory.
$replicaSetParams = @{
  Location = $AzureLocation
  SubnetId = "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"
}
$replicaSet = New-AzADDomainServiceReplicaSetObject @replicaSetParams

$domainServiceParams = @{
  Name = $ManagedDomainName
  ResourceGroupName = $ResourceGroupName
  DomainName = $ManagedDomainName
  ReplicaSet = $replicaSet
}
New-AzADDomainService @domainServiceParams
```

Az erőforrás létrehozása és a vezérlés visszaküldése a PowerShell-parancssorba néhány percet vesz igénybe. A felügyelt tartomány továbbra is a háttérben lesz kiépítve, és akár egy óráig is eltarthat a telepítés befejezéséhez. A Azure Portal a felügyelt tartomány **Áttekintés** lapja a telepítési fázis aktuális állapotát jeleníti meg.

Ha a Azure Portal azt mutatja, hogy a felügyelt tartomány befejezte az üzembe helyezést, a következő feladatokat kell elvégezni:

* A virtuális hálózat DNS-beállításainak frissítése, hogy a virtuális gépek megtalálják a felügyelt tartományt a tartományhoz való csatlakozáshoz vagy a hitelesítéshez.
    * A DNS konfigurálásához válassza ki a felügyelt tartományt a portálon. Az **Áttekintés** ablakban a rendszer automatikusan konfigurálja ezeket a DNS-beállításokat.
* [Engedélyezze a jelszó-szinkronizálást az Azure AD DS](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) így a végfelhasználók a vállalati hitelesítő adataikkal jelentkezhetnek be a felügyelt tartományba.

## <a name="next-steps"></a>Következő lépések

A felügyelt tartomány működés közbeni megtekintéséhez [tartományhoz csatlakoztathat egy Windows rendszerű virtuális gépet][windows-join], [konfigurálhatja a biztonságos LDAP][tutorial-ldaps]-t, és [konfigurálhatja a jelszó-kivonatok szinkronizálását][tutorial-phs].

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md
[nsg-overview]: ../virtual-network/network-security-groups-overview.md
[network-ports]: network-considerations.md#network-security-groups-and-required-ports

<!-- EXTERNAL LINKS -->
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[New-AzureADGroup]: /powershell/module/AzureAD/New-AzureADGroup
[Add-AzureADGroupMember]: /powershell/module/AzureAD/Add-AzureADGroupMember
[Get-AzureADGroup]: /powershell/module/AzureAD/Get-AzureADGroup
[Get-AzureADUser]: /powershell/module/AzureAD/Get-AzureADUser
[Register-AzResourceProvider]: /powershell/module/Az.Resources/Register-AzResourceProvider
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[New-AzVirtualNetworkSubnetConfig]: /powershell/module/Az.Network/New-AzVirtualNetworkSubnetConfig
[New-AzVirtualNetwork]: /powershell/module/Az.Network/New-AzVirtualNetwork
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: ../cloud-shell/cloud-shell-windows-users.md
[availability-zones]: ../availability-zones/az-overview.md
[New-AzNetworkSecurityRuleConfig]: /powershell/module/az.network/new-aznetworksecurityruleconfig
[New-AzNetworkSecurityGroup]: /powershell/module/az.network/new-aznetworksecuritygroup
[Set-AzVirtualNetworkSubnetConfig]: /powershell/module/az.network/set-azvirtualnetworksubnetconfig

---
title: Felhőalapú szolgáltatás (klasszikus) összekötése egyéni tartományvezérlővel | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztathatók a webes/feldolgozói szerepkörök egy egyéni AD-tartományhoz a PowerShell és az AD tartományi bővítmény használatával
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 8c2c8377944caa7ad28f6b379531e6d5bf44c9e7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98742505"
---
# <a name="connecting-azure-cloud-services-classic-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>Azure Cloud Services (klasszikus) szerepkörök csatlakoztatása az Azure-ban üzemeltetett egyéni AD-tartományvezérlőhöz

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

Először be kell állítania egy Virtual Network (VNet) az Azure-ban. Ezután hozzáadunk egy Active Directory-tartomány vezérlőt (amelyet egy Azure-beli virtuális gépen futtat) a VNet. Ezután hozzáadjuk a meglévő Cloud Service-szerepköröket az előre létrehozott VNet, majd összekapcsolhatjuk azokat a tartományvezérlőhöz.

A Kezdés előtt néhány dolgot figyelembe kell venni:

1. Ez az oktatóanyag a PowerShellt használja, ezért győződjön meg arról, hogy van Azure PowerShell telepítve, és készen áll a használatra. Ha segítséget szeretne kérni a Azure PowerShell beállításával kapcsolatban, olvassa el a [Azure PowerShell telepítése és konfigurálása](/powershell/azure/)című témakört.
2. Az AD-tartományvezérlő és a webes/feldolgozói szerepkör példányainak a VNet kell lenniük.

Kövesse ezt a lépésenkénti útmutatót, és ha bármilyen problémába ütközik, a cikk végén küldje el nekünk a megjegyzést. Valaki vissza fog térni Önnel (igen, az olvasási megjegyzéseket olvasjuk).

A felhőalapú szolgáltatás által hivatkozott hálózatnak **klasszikus virtuális hálózatnak** kell lennie.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
Az Azure Portal vagy a PowerShell használatával létrehozhat egy Virtual Network az Azure-ban. Ebben az oktatóanyagban a PowerShell használatos. Ha a Azure Portal használatával szeretne virtuális hálózatot létrehozni, tekintse meg [a virtuális hálózat létrehozása](../virtual-network/quick-create-portal.md)című témakört. A cikk a virtuális hálózatok (Resource Manager) létrehozásával foglalkozik, de létre kell hoznia egy virtuális hálózatot (klasszikus) a Cloud Serviceshez. Ehhez a portálon válassza az **erőforrás létrehozása** elemet, írja be a *virtuális hálózat* **kifejezést a keresőmezőbe** , majd nyomja le az **ENTER** billentyűt. A keresési eredmények területen a **minden** elemnél válassza a **virtuális hálózat** lehetőséget. **A telepítési modell kiválasztása** területen válassza a **klasszikus** lehetőséget, majd kattintson a **Létrehozás** elemre. Ezután követheti a cikkben ismertetett lépéseket.

```powershell
#Create Virtual Network

$vnetStr =
@"<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
    <VirtualNetworkConfiguration>
    <VirtualNetworkSites>
        <VirtualNetworkSite name="[your-vnet-name]" Location="West US">
        <AddressSpace>
            <AddressPrefix>[your-address-prefix]</AddressPrefix>
        </AddressSpace>
        <Subnets>
            <Subnet name="[your-subnet-name]">
            <AddressPrefix>[your-subnet-range]</AddressPrefix>
            </Subnet>
        </Subnets>
        </VirtualNetworkSite>
    </VirtualNetworkSites>
    </VirtualNetworkConfiguration>
</NetworkConfiguration>
"@;

$vnetConfigPath = "<path-to-vnet-config>"
Set-AzureVNetConfig -ConfigurationPath $vnetConfigPath
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása
Ha befejezte a Virtual Network beállítását, létre kell hoznia egy AD-tartományvezérlőt. Ebben az oktatóanyagban egy AD-tartományvezérlőt állítunk be egy Azure-beli virtuális gépen.

Ehhez hozzon létre egy virtuális gépet a PowerShell használatával a következő parancsokkal:

```powershell
# Initialize variables
# VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.

$vnetname = '<your-vnet-name>'
$subnetname = '<your-subnet-name>'
$vmsvc1 = '<your-hosted-service>'
$vm1 = '<your-vm-name>'
$username = '<your-username>'
$password = '<your-password>'
$affgrp = '<your- affgrp>'

# Create a VM and add it to the Virtual Network

New-AzureQuickVM -Windows -ServiceName $vmsvc1 -Name $vm1 -ImageName $imgname -AdminUsername $username -Password $password -AffinityGroup $affgrp -SubnetNames $subnetname -VNetName $vnetname
```

## <a name="promote-your-virtual-machine-to-a-domain-controller"></a>A virtuális gép előléptetése tartományvezérlővé
Ha a virtuális gépet AD-tartományvezérlőként szeretné konfigurálni, be kell jelentkeznie a virtuális GÉPRE, és konfigurálnia kell azt.

A virtuális gépre való bejelentkezéshez az RDP-fájlt a PowerShellen keresztül érheti el, a következő parancsokkal:

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

Miután bejelentkezett a virtuális gépre, állítsa be a virtuális gépet AD-tartományvezérlőként az [ügyfél ad-tartományvezérlő beállításának](https://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx)lépésenkénti útmutatója alapján.

## <a name="add-your-cloud-service-to-the-virtual-network"></a>Felhőalapú szolgáltatás hozzáadása a Virtual Network
Ezután hozzá kell adnia a Cloud Service-telepítést az új VNet. Ehhez módosítsa a Cloud Service cscfg úgy, hogy hozzáadja a megfelelő szakaszt a cscfg a Visual Studióval vagy az Ön által választott szerkesztővel.

```xml
<ServiceConfiguration serviceName="[hosted-service-name]" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="[os-family]" osVersion="*">
    <Role name="[role-name]">
    <Instances count="[number-of-instances]" />
    </Role>
    <NetworkConfiguration>

    <!--optional-->
    <Dns>
        <DnsServers><DnsServer name="[dns-server-name]" IPAddress="[ip-address]" /></DnsServers>
    </Dns>
    <!--optional-->

    <!--VNet settings
        VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.-->
    <VirtualNetworkSite name="[virtual-network-name]" />
    <AddressAssignments>
        <InstanceAddress roleName="[role-name]">
        <Subnets>
            <Subnet name="[subnet-name]" />
        </Subnets>
        </InstanceAddress>
    </AddressAssignments>
    <!--VNet settings-->

    </NetworkConfiguration>
</ServiceConfiguration>
```

Ezután hozza létre a Cloud Services-projektet, és telepítse az Azure-ba. Ha segítséget szeretne kérni a Cloud Services-csomag Azure-beli üzembe helyezésével kapcsolatban, tekintse meg [a felhőalapú szolgáltatás létrehozása és üzembe helyezése](cloud-services-how-to-create-deploy-portal.md) című témakört.

## <a name="connect-your-webworker-roles-to-the-domain"></a>A webes/feldolgozói szerepkörök összekötése a tartományhoz
Miután üzembe helyezte a Cloud Service-projektet az Azure-ban, az AD tartományi bővítmény használatával kapcsolja össze a szerepkör-példányokat az egyéni AD-tartományhoz. Ha az AD tartományi bővítményt hozzá szeretné adni a meglévő Cloud Services-környezethez, és csatlakozik az egyéni tartományhoz, hajtsa végre a következő parancsokat a PowerShellben:

```powershell
# Initialize domain variables

$domain = '<your-domain-name>'
$dmuser = '$domain\<your-username>'
$dmpswd = '<your-domain-password>'
$dmspwd = ConvertTo-SecureString $dmpswd -AsPlainText -Force
$dmcred = New-Object System.Management.Automation.PSCredential ($dmuser, $dmspwd)

# Add AD Domain Extension to the cloud service roles

Set-AzureServiceADDomainExtension -Service <your-cloud-service-hosted-service-name> -Role <your-role-name> -Slot <staging-or-production> -DomainName $domain -Credential $dmcred -JoinOption 35
```

Ennyi az egész.

A felhőalapú szolgáltatásokat csatlakoztatni kell az egyéni tartományvezérlőhöz. Ha többet szeretne megtudni az AD tartományi bővítmény konfigurálásához elérhető különböző lehetőségekről, használja a PowerShell súgóját. Néhány példa a következőkre:

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```




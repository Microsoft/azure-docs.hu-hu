---
title: Virtuális gépek magánhálózati IP-címeinek konfigurálása (klasszikus) – Azure PowerShell | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat magánhálózati IP-címeket a virtuális gépekhez (klasszikus) a PowerShell használatával.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
tags: azure-service-management
ms.assetid: 60c7b489-46ae-48af-a453-2b429a474afd
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2e798f81102017b4d34c4b1b219a9f23035b0359
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222836"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-powershell"></a>Virtuális gép magánhálózati IP-címeinek konfigurálása (klasszikus) a PowerShell használatával

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. [A statikus magánhálózati IP-címeket a Resource Manager-alapú üzemi modellben is kezelheti](virtual-networks-static-private-ip-arm-ps.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Az alábbi PowerShell-parancsok egy már létrehozott egyszerű környezetet várnak. Ha a jelen dokumentumban megjelenő parancsokat szeretné futtatni, először [hozza létre a VNet létrehozása](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-netcfg-ps)című témakörben leírt tesztkörnyezetben.

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Annak ellenőrzése, hogy elérhető-e egy adott IP-cím
Annak ellenőrzéséhez, hogy az IP- *192.168.1.101* elérhető-e egy *TestVNet* nevű VNet, futtassa a következő PowerShell-parancsot, és ellenőrizze a *IsAvailable* értékét:

```azurepowershell
Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 192.168.1.101 
```

Várt kimenet:

```output
IsAvailable          : True
AvailableAddresses   : {}
OperationDescription : Test-AzureStaticVNetIP
OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
OperationStatus      : Succeeded
```

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Statikus magánhálózati IP-cím megadása virtuális gép létrehozásakor
Az alábbi PowerShell-szkript létrehoz egy *TestService* nevű új felhőalapú szolgáltatást, majd beolvas egy rendszerképet az Azure-ból, létrehoz egy *DNS01* nevű virtuális gépet az új Cloud Service-ben a beolvasott rendszerkép használatával, beállítja a virtuális gépet egy *FrontEnd* nevű alhálózatban, és a *192.168.1.7* statikus magánhálózati IP-címként állítja be a virtuális géphez:

```azurepowershell
New-AzureService -ServiceName TestService -Location "Central US"
$image = Get-AzureVMImage | where {$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
New-AzureVMConfig -Name DNS01 -InstanceSize Small -ImageName $image.ImageName |
    Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! |
    Set-AzureSubnet –SubnetNames FrontEnd |
    Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
    New-AzureVM -ServiceName TestService –VNetName TestVNet
```

Várt kimenet:

```output
WARNING: No deployment found in service: 'TestService'.
OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
New-AzureService     fcf705f1-d902-011c-95c7-b690735e7412 Succeeded      
New-AzureVM          3b99a86d-84f8-04e5-888e-b6fc3c73c4b9 Succeeded  
```

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Virtuális gép statikus magánhálózati IP-címére vonatkozó információk lekérése
A fenti parancsfájllal létrehozott virtuális gép statikus magánhálózati IP-címére vonatkozó információk megtekintéséhez futtassa a következő PowerShell-parancsot, és figyelje meg az *IP*-címek értékét:

```azurepowershell
Get-AzureVM -Name DNS01 -ServiceName TestService
```

Várt kimenet:

```output
DeploymentName              : TestService
Name                        : DNS01
Label                       : 
VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
InstanceStatus              : Provisioning
IpAddress                   : 192.168.1.7
InstanceStateDetails        : Windows is preparing your computer for first use...
PowerState                  : Started
InstanceErrorCode           : 
InstanceFaultDomain         : 0
InstanceName                : DNS01
InstanceUpgradeDomain       : 0
InstanceSize                : Small
HostName                    : rsR2-797
AvailabilitySetName         : 
DNSName                     : http://testservice000.cloudapp.net/
Status                      : Provisioning
GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
PublicIPAddress             : 
PublicIPName                : 
NetworkInterfaces           : {}
ServiceName                 : TestService
OperationDescription        : Get-AzureVM
OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
OperationStatus             : OK
```

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Statikus magánhálózati IP-cím eltávolítása egy virtuális gépről
A fenti szkriptben a virtuális géphez hozzáadott statikus magánhálózati IP-cím eltávolításához futtassa a következő PowerShell-parancsot:

```azurepowershell
Get-AzureVM -ServiceName TestService -Name DNS01 |
    Remove-AzureStaticVNetIP |
    Update-AzureVM
```

Várt kimenet:

```output
OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       052fa6f6-1483-0ede-a7bf-14f91f805483 Succeeded
```

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Statikus magánhálózati IP-cím hozzáadása meglévő virtuális géphez
Ha a fenti szkript használatával létrehozott virtuális géphez statikus magánhálózati IP-címet szeretne hozzáadni, futtassa a következő parancsot:

```azurepowershell
Get-AzureVM -ServiceName TestService -Name DNS01 |
    Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
    Update-AzureVM
```

Várt kimenet:

```output
OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       77d8cae2-87e6-0ead-9738-7c7dae9810cb Succeeded 
```

## <a name="set-ip-addresses-within-the-operating-system"></a>IP-címek beállítása az operációs rendszeren belül

Azt javasoljuk, hogy a virtuális gép operációs rendszerén belül ne rendeljen statikusan az Azure-beli virtuális géphez rendelt magánhálózati IP-címet, ha szükséges. Ha a magánhálózati IP-címet manuálisan állítja be az operációs rendszeren belül, győződjön meg arról, hogy a cím megegyezik az Azure-beli virtuális géphez rendelt magánhálózati IP-címmel, vagy megszakadhat a kapcsolat a virtuális géppel. Soha ne rendeljen hozzá manuálisan egy Azure-beli virtuális géphez hozzárendelt nyilvános IP-címet a virtuális gép operációs rendszerén belül.

## <a name="next-steps"></a>Következő lépések
* További információ a [fenntartott nyilvános IP-](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) címekről.
* További információ a [példány szintű nyilvános IP-címekről (ILPIP)](/previous-versions/azure/virtual-network/virtual-networks-instance-level-public-ip) .
* Forduljon a [fenntartott IP REST API](/previous-versions/azure/reference/dn722420(v=azure.100))-khoz.
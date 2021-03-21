---
title: Virtuális hálózati DNS-kiszolgálók beállításának szinkronizálása az SQL felügyelt példányának virtuális fürtjén
description: Ismerje meg, hogyan szinkronizálhatja a virtuális hálózat DNS-kiszolgálóinak beállításait az SQL felügyelt példányának virtuális fürtjén.
services: sql-database
ms.service: sql-managed-instance
author: srdan-bozovic-msft
ms.author: srbozovi
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: b95afe513dba2f1da9556b27ec17bcccc9fe88e1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102173551"
---
# <a name="synchronize-virtual-network-dns-servers-setting-on-sql-managed-instance-virtual-cluster"></a>Virtuális hálózati DNS-kiszolgálók beállításának szinkronizálása az SQL felügyelt példányának virtuális fürtjén
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ez a cikk azt ismerteti, hogy mikor és hogyan szinkronizálhatók a virtuális hálózati DNS-kiszolgálók beállítása az SQL felügyelt példányának virtuális fürtjén.

## <a name="when-to-synchronize-the-dns-setting"></a>Mikor kell szinkronizálni a DNS-beállítást

Egyes esetekben (például db mail – más SQL Server-példányokhoz társított kiszolgálók a felhőben vagy hibrid környezetben), a felügyelt SQL-példányról kell feloldani a privát gazdagépneveket. Ebben az esetben konfigurálni kell egy egyéni DNS-t az Azure-ban. További információ: [Egyéni DNS konfigurálása Azure SQL Managed Instance-hez](custom-dns-configure.md).

Ha ezt a változást a felügyelt példányt futtató [virtuális fürt](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) létrehozása után hajtja végre, akkor a virtuális hálózat konfigurációjának megfelelően szinkronizálnia kell a DNS-kiszolgálók beállítását a virtuális fürtön.

> [!IMPORTANT]
> A DNS-kiszolgálók szinkronizálása beállítás hatással lesz a virtuális fürtben üzemeltetett összes felügyelt példányra.

## <a name="how-to-synchronize-the-dns-setting"></a>A DNS-beállítás szinkronizálása

### <a name="azure-rbac-permissions-required"></a>Azure RBAC-engedélyek szükségesek

A DNS-kiszolgáló konfigurációjának szinkronizálásához a felhasználónak a következő Azure-szerepkörök egyikével kell rendelkeznie:

- Előfizetés tulajdonosi szerepköre, vagy
- Felügyelt példány közreműködői szerepköre, vagy
- Egyéni szerepkör a következő engedélyekkel:
  - `Microsoft.Sql/virtualClusters/updateManagedInstanceDnsServers/action`

### <a name="use-azure-powershell"></a>Azure PowerShell használatával

Virtuális hálózat beolvasása, ahol a DNS-kiszolgálók beállítása frissült.

```PowerShell
$ResourceGroup = 'enter resource group of virtual network'
$VirtualNetworkName = 'enter virtual network name'
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroup $ResourceGroup -Name $VirtualNetworkName
```
Az alhálózaton található összes virtuális fürthöz tartozó DNS-kiszolgálók konfigurációjának szinkronizálásához használja az [AzResourceAction PowerShell-](/powershell/module/az.resources/invoke-azresourceaction) parancsot.

```PowerShell
Get-AzSqlVirtualCluster `
    | where SubnetId -match $virtualNetwork.Id `
    | select Id `
    | Invoke-AzResourceAction -Action updateManagedInstanceDnsServers -Force
```
### <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

Virtuális hálózat beolvasása, ahol a DNS-kiszolgálók beállítása frissült.

```Azure CLI
resourceGroup="auto-failover-group"
virtualNetworkName="vnet-fog-eastus"
virtualNetwork=$(az network vnet show -g $resourceGroup -n $virtualNetworkName --query "id" -otsv)
```

Használja az Azure CLI [-parancsot az erőforrás-Meghívási művelettel](/cli/azure/resource#az_resource_invoke_action) a DNS-kiszolgálók konfigurációjának szinkronizálásához az alhálózat összes virtuális fürtjénél.

```Azure CLI
az sql virtual-cluster list --query "[? contains(subnetId,'$virtualNetwork')].id" -o tsv \
    | az resource invoke-action --action updateManagedInstanceDnsServers --ids @-
```
## <a name="next-steps"></a>Következő lépések

- További információ az egyéni DNS konfigurálásáról [Egyéni DNS konfigurálása az Azure SQL felügyelt példányához](custom-dns-configure.md).
- Az áttekintést lásd: [Mi az az Azure SQL felügyelt példánya?](sql-managed-instance-paas-overview.md).

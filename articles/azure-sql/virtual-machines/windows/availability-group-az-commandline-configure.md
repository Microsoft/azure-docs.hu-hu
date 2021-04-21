---
title: Rendelkezésre állási csoport konfigurálása (PowerShell & Az CLI)
description: Használja a PowerShellt vagy az Azure CLI-t a Windows feladatátvevő fürt, a rendelkezésre állási csoport listenerje és a belső terheléselosztás létrehozásához egy Azure-beli SQL Server VM virtuális SQL Server VM létrehozásához.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/20/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019, devx-track-azurecli
ms.openlocfilehash: ffd4ec6eff94589abbc8af70ecf9c0f7dc168962
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766932"
---
# <a name="use-powershell-or-az-cli-to-configure-an-availability-group-for-sql-server-on-azure-vm"></a>Rendelkezésre állási csoport konfigurálása Azure-beli virtuális gépen SQL Server PowerShell vagy az Az CLI használatával 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez a cikk azt ismerteti, hogyan használhatja a [PowerShellt](/powershell/scripting/install/installing-powershell) vagy az [Azure CLI-t](/cli/azure/sql/vm) egy Windows feladatátvevő fürt üzembe helyezéséhez SQL Server, virtuális gépek fürthöz való hozzáadásához, valamint egy Always On rendelkezésre állási csoport belső terheléselelosztásának és figyelőjének létrehozásához. 

A rendelkezésre állási csoport üzembe helyezése továbbra is manuálisan történik a SQL Server Management Studio (SSMS) vagy a Transact-SQL (T-SQL) használatával. 

Bár ez a cikk a PowerShell és az Az CLI használatával konfigurálja a rendelkezésre állási csoport környezetét, [](availability-group-manually-configure-tutorial.md) ezt a [Azure Portal](availability-group-azure-portal-configure.md)használatával is meg lehet tenni, [Azure](availability-group-quickstart-template-configure.md)gyorsindítási sablonok használatával, vagy Manuálisan is. 

## <a name="prerequisites"></a>Előfeltételek

Always On rendelkezésre állási csoport konfigurálásának előfeltételei a következők: 

- Egy [Azure-előfizetés](https://azure.microsoft.com/free/).
- Egy erőforráscsoport tartományvezérlővel. 
- Az Azure-ban egy vagy több, [SQL Server 2016-os (vagy újabb)](./create-sql-vm-portal.md)  Enterprise kiadást  futtató, tartományhoz csatlakozott virtuális gép ugyanabban a rendelkezésre állási csoportban vagy különböző rendelkezésre állási zónákban, amelyek regisztrálva vannak az [SQL IaaS-ügynök bővítményben.](sql-agent-extension-manually-register-single-vm.md)  
- A [PowerShell](/powershell/scripting/install/installing-powershell) vagy az [Azure CLI legújabb verziója.](/cli/azure/install-azure-cli) 
- Két elérhető (entitás által nem használt) IP-cím. Az egyik a belső terheléselosztáshoz való. A másik a rendelkezésre állási csoport figyelőjére vonatkozik, amely ugyanabban az alhálózatban található, mint a rendelkezésre állási csoport. Ha meglévő terheléselosztást használ, csak egy elérhető IP-címre van szüksége a rendelkezésre állási csoport figyelőjére. 

## <a name="permissions"></a>Engedélyek

Az Always On rendelkezésre állási csoport Azure CLI használatával való konfiguráláshoz a következő fiókengedélyek szükségesek: 

- Meglévő tartományi felhasználói fiók, amely **számítógép-objektum létrehozása engedéllyel** rendelkezik a tartományban. Egy tartományi rendszergazdai fiók például általában elegendő engedéllyel rendelkezik (például: account@domain.com ). _Ennek a fióknak az egyes virtuális gépek helyi rendszergazdai csoportjának is részét kell, hogy legyen a fürt létrehozásához._
- A tartományt vezérlő SQL Server. 
 
## <a name="create-a-storage-account"></a>Tárfiók létrehozása 

A fürtnek szüksége van egy tárfiókra, amely a felhőbeli tanúsítóként működik. Használhat bármely meglévő tárfiókot, vagy létrehozhat egy újat. Ha meglévő tárfiókot szeretne használni, ugorjon a következő szakaszra. 

Az alábbi kódrészlet létrehozza a tárfiókot: 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> Ha az Azure CLI elavult verzióját használja, a `az sql: 'vm' is not in the 'az sql' command group` hibaüzenetet láthatja. Töltse le [az Azure CLI legújabb verzióját](/cli/azure/install-azure-cli-windows) a hiba kieső útjára.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Create the storage account
# example: New-AzStorageAccount -ResourceGroupName SQLVM-RG -Name cloudwitness `
#    -SkuName Standard_LRS -Location West US -Kind StorageV2 `
#    -AccessTier Hot -EnableHttpsTrafficOnly

New-AzStorageAccount -ResourceGroupName <resource group name> -Name <name> `
    -SkuName Standard_LRS -Location <region> -Kind StorageV2 `
    -AccessTier Hot -EnableHttpsTrafficOnly
```

---

## <a name="define-cluster-metadata"></a>Fürt metaadatainak meghatározása

Az Azure CLI [az sql vm group](/cli/azure/sql/vm/group) parancscsoport kezeli a rendelkezésre állási csoportot futtató Windows Server feladatátvevő fürt (WSFC) szolgáltatás metaadatait. A fürt metaadatai közé Active Directory tartomány, fürtfiókok, felhőbeli tanúsítóként használt tárfiókok és a SQL Server verziója. Az [az sql vm group create parancs](/cli/azure/sql/vm/group#az_sql_vm_group_create) használatával definiálhatja a WSFC metaadatait, így az első SQL Server VM hozzáadásakor a fürt a meghatározottak szerint jön létre. 

Az alábbi kódrészlet határozza meg a fürt metaadatait:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Define the cluster metadata
# example: az sql vm group create -n Cluster -l 'West US' -g SQLVM-RG `
#  --image-offer SQL2017-WS2016 --image-sku Enterprise --domain-fqdn domain.com `
#  --operator-acc vmadmin@domain.com --bootstrap-acc vmadmin@domain.com --service-acc sqlservice@domain.com `
#  --sa-key '4Z4/i1Dn8/bpbseyWX' `
#  --storage-account 'https://cloudwitness.blob.core.windows.net/'

az sql vm group create -n <cluster name> -l <region ex:eastus> -g <resource group name> `
  --image-offer <SQL2016-WS2016 or SQL2017-WS2016> --image-sku Enterprise --domain-fqdn <FQDN ex: domain.com> `
  --operator-acc <domain account ex: testop@domain.com> --bootstrap-acc <domain account ex:bootacc@domain.com> `
  --service-acc <service account ex: testservice@domain.com> `
  --sa-key '<PublicKey>' `
  --storage-account '<ex:https://cloudwitness.blob.core.windows.net/>'
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Define the cluster metadata
# example: $group = New-AzSqlVMGroup -Name Cluster -Location West US ' 
#  -ResourceGroupName SQLVM-RG -Offer SQL2017-WS2016
#  -Sku Enterprise -DomainFqdn domain.com -ClusterOperatorAccount vmadmin@domain.com
#  -ClusterBootstrapAccount vmadmin@domain.com  -SqlServiceAccount sqlservice@domain.com 
#  -StorageAccountUrl '<ex:https://cloudwitness.blob.core.windows.net/>' `
#  -StorageAccountPrimaryKey '4Z4/i1Dn8/bpbseyWX'

$group = New-AzSqlVMGroup -Name <name> -Location <regio> 
  -ResourceGroupName <resource group name> -Offer <SQL201?-WS201?> 
  -Sku Enterprise -DomainFqdn <FQDN> -ClusterOperatorAccount <domain account> 
  -ClusterBootstrapAccount <domain account>  -SqlServiceAccount <service account> 
  -StorageAccountUrl '<ex:StorageAccountUrl>' `
  -StorageAccountPrimaryKey '<PublicKey>'
```

---

## <a name="add-vms-to-the-cluster"></a>Virtuális gépek hozzáadása a fürthöz

Az első SQL Server VM a fürthöz való hozzáadása létrehozza a fürtöt. Az [az sql vm add-to-group](/cli/azure/sql/vm#az_sql-vm_add_to_group) parancs létrehozza a fürtöt a korábban megadott névvel, telepíti a fürtszerepket a SQL Server virtuális gépekre, és hozzáadja őket a fürthöz. A parancs későbbi használata további SQL Server a virtuális `az sql vm add-to-group` gépeket az újonnan létrehozott fürthöz. 

A következő kódrészlet létrehozza a fürtöt, és hozzáadja SQL Server VM első kódrészletet: 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Add SQL Server VMs to cluster
# example: az sql vm add-to-group -n SQLVM1 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!
# example: az sql vm add-to-group -n SQLVM2 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

az sql vm add-to-group -n <VM1 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
az sql vm add-to-group -n <VM2 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
```
Ezzel a paranccsal adhat hozzá SQL Server virtuális gépeket a fürthöz. Csak a `-n` név paraméterét SQL Server VM módosítsa. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Add SQL Server VMs to cluster
# example: $sqlvm1 = Get-AzSqlVM -Name SQLVM1 -ResourceGroupName SQLVM-RG
# $sqlvm2 = Get-AzSqlVM -Name SQLVM2  -ResourceGroupName SQLVM-RG

# $sqlvmconfig1 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm1 `
#  -SqlVMGroup $group -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
#  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
#  -ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

# $sqlvmconfig2 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm2 `
#  -SqlVMGroup $group -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
#  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
#  - ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

$sqlvm1 = Get-AzSqlVM -Name <VM1 Name> -ResourceGroupName <Resource Group Name>
$sqlvm2 = Get-AzSqlVM -Name <VM2 Name> -ResourceGroupName <Resource Group Name>

$sqlvmconfig1 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm1 `
   -SqlVMGroup $group -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

Update-AzSqlVM -ResourceId $sqlvm1.ResourceId -SqlVM $sqlvmconfig1

$sqlvmconfig2 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm2 `
   -SqlVMGroup $group -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

Update-AzSqlVM -ResourceId $sqlvm2.ResourceId -SqlVM $sqlvmconfig2
```

---


## <a name="validate-cluster"></a>Fürt ellenőrzése 

Ahhoz, hogy egy feladatátvevő fürtöt támogatjon a Microsoft, át kell mennie a fürtérvényesítésen. Csatlakozzon a virtuális géphez az előnyben részesített módszerrel, például a RDP protokoll (RDP) használatával, és ellenőrizze, hogy a fürt megfelel-e az ellenőrzésen, mielőtt továbbhaladna. Ennek elmulasztása esetén a fürt nem támogatott állapotban marad. 

A fürtöt az alábbi Feladatátvevőfürt-kezelő (FCM) vagy a következő PowerShell-paranccsal ellenőrizheti:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

## <a name="create-availability-group"></a>Rendelkezésre állási csoport létrehozása

Manuálisan hozza létre a rendelkezésre állási csoportot a szokásos módon a SQL Server Management Studio, [a PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)vagy [a](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio) [Transact-SQL használatával.](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql) 

>[!IMPORTANT]
> Jelenleg *ne* hozzon létre listenert, mert ezt az Azure CLI-nek a következő szakaszokban kell megtennie.  

## <a name="create-internal-load-balancer"></a>Belső terheléselosztás létrehozása

[!INCLUDE [sql-ag-use-dnn-listener](../../includes/sql-ag-use-dnn-listener.md)]

Az Always On rendelkezésre állási csoport figyelője a szolgáltatás belső példányát Azure Load Balancer. A belső terheléselosztás "lebegő" IP-címet biztosít a rendelkezésre állási csoport figyelőjének, amely gyorsabb feladatátvételt és újracsatlakozást tesz lehetővé. Ha SQL Server rendelkezésre állási csoport virtuális gépei ugyanannak a rendelkezésre állási csoportnak a részei, használhat alapszintű terheléselosztást. Ellenkező esetben standard terheléselosztást kell használnia.  

> [!NOTE]
> A belső terheléselosztásnak ugyanabban a virtuális hálózatban kell lennie, mint a SQL Server VM példányainak. 

Az alábbi kódrészlet létrehozza a belső terheléselosztást:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Create the internal load balancer
# example: New-AzLoadBalancer -name sqlILB -ResourceGroupName SQLVM-RG  `
#  -Sku Standard -Location West US

New-AzLoadBalancer -name sqlILB -ResourceGroupName <resource group name> `
   -Sku Standard -Location <region>
```

---

>[!IMPORTANT]
> Az egyes erőforrások nyilvános IP-SQL Server VM standard termékváltozatot kell, hogy legyen, amely kompatibilis a Standard terheléselosztással. A virtuális gép nyilvános IP-erőforrásának termékváltozatának meghatározásához keresse  meg az Erőforráscsoportot, válassza ki a kívánt ip-cím erőforrását a kívánt SQL Server VM, és keresse meg az értéket a **Termékváltozat** területen az Áttekintés **panelen.**  

## <a name="create-listener"></a>Listener létrehozása

A rendelkezésre állási csoport manuális létrehozása után az [az sql vm ag-listener](/cli/azure/sql/vm/group/ag-listener#az_sql_vm_group_ag_listener_create)használatával hozhatja létre a figyelőt. 

Az *alhálózati erőforrás-azonosító* a érték, amely a virtuális hálózati erőforrás erőforrás-azonosítójával `/subnets/<subnetname>` van hozzáfűzve. Az alhálózat erőforrás-azonosítójának azonosítása:
   1. Az erőforráscsoporthoz a következő [Azure Portal.](https://portal.azure.com) 
   1. Válassza ki a virtuális hálózati erőforrást. 
   1. A **Beállítások panelen** válassza a **Tulajdonságok** lehetőséget. 
   1. Az alhálózati erőforrás-azonosító létrehozásához azonosítsa a virtuális hálózat erőforrás-azonosítóját, és fűzse hozzá a következőt: `/subnets/<subnetname>` . Például:
      - A virtuális hálózat erőforrás-azonosítója: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - Az alhálózat neve: `default`
      - Ezért az alhálózat erőforrás-azonosítója a következő: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


A következő kódrészlet létrehozza a rendelkezésre állási csoport listenerét:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Create the availability group listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM's hosting AG replicas, ex: sqlvm1 sqlvm2>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive

# example: New-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
#    -AvailabilityGroupName SQLAG  -GroupName Cluster `
#    -IpAddress 10.0.0.27 -LoadBalancerResourceId sqlilb `
#    -ProbePort 59999 `
#    -SubnetId /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#    -SqlVirtualMachineId sqlvm1 sqlvm2


New-AzAvailabilityGroupListener -Name <listener name> -ResourceGroupName <resource group name> `
   -AvailabilityGroupName <availability group name> -GroupName <cluster name> `
   -IpAddress <ag listener IP address> -LoadBalancerResourceId <lbid> `
   -ProbePort <Load Balancer probe port, default 59999> `
   -SubnetId <subnet resource id> `
   -SqlVirtualMachineId <names of SQL VM's hosting AG replicas>
```

---

## <a name="modify-number-of-replicas"></a>Replikák számának módosítása 
Ha rendelkezésre állási csoportot helyez üzembe az Azure-ban üzemeltetett virtuális gépek SQL Server egy további összetettségi réteggel. Az erőforrásokat az erőforrás-szolgáltató és a virtuálisgép-csoport kezeli. Így amikor replikákat ad hozzá vagy távolít el a rendelkezésre állási csoportban, egy további lépéssel is frissítve lesz a figyelés metaadatai a virtuális gépek SQL Server adataival. Amikor módosítja a rendelkezésre állási csoportban található replikák számát, az [az sql vm group ag-listener update](/cli/azure/sql/vm/group/ag-listener#az_sql_vm_group_ag_listener_update) paranccsal is frissítenie kell a figyelőt az SQL Server virtuális gépek metaadataival. 


### <a name="add-a-replica"></a>Replika hozzáadása

Új replika hozzáadása a rendelkezésre állási csoporthoz:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Adja hozzá SQL Server VM a fürtcsoporthoz:
   ```azurecli-interactive

   # Add the SQL Server VM to the cluster group
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```

1. A SQL Server Management Studio adja hozzá a SQL Server példányt replikaként a rendelkezésre állási csoporton belül.
1. Adja hozzá SQL Server VM metaadatait a listenerhez:

   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Adja hozzá SQL Server VM a fürtcsoporthoz:

   ```powershell-interactive
   # Add the SQL Server VM to the cluster group
   # example: $sqlvm3 = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG 
   # $group = Get-AzSqlVMGroup -ResourceGroupName SQLVM-RG -Name Cluster
   # $sqlvmconfig3 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm3 -SqlVMGroup $group `
   #  -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
   #  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
   #  -ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

   $sqlvm3 = Get-AzSqlVM -Name <VM1 Name> -ResourceGroupName <Resource Group Name>
   $group = Get-AzSqlVMGroup  -ResourceGroupName <resource group name> -Name <name>
   $sqlvmconfig3 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm3 -SqlVMGroup $group `
   -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

   Update-AzSqlVM -ResourceId $sqlvm3.ResourceId -SqlVM $sqlvmconfig3
   ```

1. A SQL Server Management Studio adja hozzá a SQL Server példányt replikaként a rendelkezésre állási csoporton belül.
1. Adja hozzá SQL Server VM metaadatait a listenerhez:

   ```powershell-interactive
   # Update the listener metadata with the new VM
   # example: Update-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
   #   -SqlVMGroupName Cluster -SqlVirtualMachineId SQLVM3

   Update-AzAvailabilityGroupListener -Name <Listener> -ResourceGroupName <Resource Group Name> `
      -SqlVMGroupName <cluster name> -SqlVirtualMachineId <SQL VMs, along with new SQL VM> 

   ```

---

### <a name="remove-a-replica"></a>Replika eltávolítása

Replika eltávolítása a rendelkezésre állási csoportból:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Távolítsa el a replikát a rendelkezésre állási csoportból az SQL Server Management Studio. 
1. Távolítsa el SQL Server VM metaadatait a listenerből:
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. Távolítsa el SQL Server VM fürtből a következőt:
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Távolítsa el a replikát a rendelkezésre állási csoportból az SQL Server Management Studio. 
1. Távolítsa el SQL Server VM metaadatait a listenerből:

   ```powershell-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: Update-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
   #  -SqlVMGroupName Cluster -SqlVirtualMachineId SQLVM3

   Update-AzAvailabilityGroupListener -Name <Listener> -ResourceGroupName <Resource Group Name> `
      -SqlVMGroupName <cluster name> -SqlVirtualMachineId <SQL VMs that remain>

   ```
1. Távolítsa el SQL Server VM fürtből a következőt:

   ```powershell-interactive
   # Remove the SQL VM from the cluster
   # example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
   #  $sqlvm. SqlVirtualMachineGroup = ""
   #  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

   $sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
      $sqlvm. SqlVirtualMachineGroup = ""
    
    Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
   ```

---

## <a name="remove-listener"></a>Listener eltávolítása
Ha később el kell távolítania az Azure CLI-hez konfigurált rendelkezésre állásicsoport- figyelőt, át kell mennie az SQL IaaS-ügynök bővítményén. Mivel a figyelő az SQL IaaS-ügynök bővítményen keresztül van regisztrálva, a figyelő csak a virtuális SQL Server Management Studio nem elegendő. 

A legjobb módszer az, ha törli az SQL IaaS-ügynök bővítményen keresztül az alábbi kódrészlet használatával az Azure CLI-n. Ezzel eltávolítja a rendelkezésre állási csoport listener metaadatait az SQL IaaS-ügynök bővítményből. Emellett fizikailag törli a figyelőt a rendelkezésre állási csoportból. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Remove the availability group listener
# example: Remove-AzAvailabilityGroupListener -Name AGListener `
#   -ResourceGroupName SQLVM-RG -SqlVMGroupName Cluster

Remove-AzAvailabilityGroupListener -Name <Listener> `
   -ResourceGroupName <Resource Group Name> -SqlVMGroupName <cluster name>
```

---

## <a name="remove-cluster"></a>Fürt eltávolítása

Távolítsa el az összes csomópontot a fürtből azok megsemmisítéséhez, majd távolítsa el a fürt metaadatait az SQL IaaS-ügynök bővítményből. Ezt az Azure CLI vagy a PowerShell használatával is meg lehet tenni. 


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Először távolítsa el az összes virtuális SQL Server a fürtből: 

```azurecli-interactive
# Remove the VM from the cluster metadata
# example: az sql vm remove-from-group --name SQLVM2 --resource-group SQLVM-RG

az sql vm remove-from-group --name <VM1 name>  --resource-group <resource group name>
az sql vm remove-from-group --name <VM2 name>  --resource-group <resource group name>
```

Ha csak ezek a virtuális gépek vannak a fürtben, akkor a fürt megsemmisül. Ha a fürtön a SQL Server virtuális gépeken kívül más virtuális gépek is el vannak távolítva, a többi virtuális gép nem lesz eltávolítva, és a fürt nem lesz megsemmisítve. 

Ezután távolítsa el a fürt metaadatait az SQL IaaS-ügynök bővítményből: 

```azurecli-interactive
# Remove the cluster from the SQL VM RP metadata
# example: az sql vm group delete --name Cluster --resource-group SQLVM-RG

az sql vm group delete --name <cluster name> Cluster --resource-group <resource group name>
```



# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Először távolítsa el az összes virtuális SQL Server a fürtből. Ez fizikailag eltávolítja a csomópontokat a fürtből, és megsemmisíti a fürtöt: 

```powershell-interactive
# Remove the SQL VM from the cluster
# example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
#  $sqlvm. SqlVirtualMachineGroup = ""
#  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

$sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
   $sqlvm. SqlVirtualMachineGroup = ""
   
   Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
```

Ha csak ezek a virtuális gépek vannak a fürtben, akkor a fürt megsemmisül. Ha a fürtön a SQL Server virtuális gépeken kívül más virtuális gépek is el vannak távolítva, a többi virtuális gép nem lesz eltávolítva, és a fürt nem lesz megsemmisítve. 

Ezután távolítsa el a fürt metaadatait az SQL IaaS-ügynök bővítményből: 

```powershell-interactive
# Remove the cluster metadata
# example: Remove-AzSqlVMGroup -ResourceGroupName "SQLVM-RG" -Name "Cluster"

Remove-AzSqlVMGroup -ResourceGroupName "<resource group name>" -Name "<cluster name> "
```

---

## <a name="next-steps"></a>Következő lépések

További információért tekintse át a következő cikkeket: 

* [A virtuális SQL Server áttekintése](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Gyakori kérdések SQL Server virtuális gépekről](frequently-asked-questions-faq.md)
* [A virtuális gépek SQL Server kibocsátási megjegyzései](../../database/doc-changes-updates-release-notes.md)
* [Licencelési modellek váltása egy SQL Server VM](licensing-model-azure-hybrid-benefit-ahb-change.md)
* [Az Always On rendelkezésre állási csoportok áttekintésének &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Kiszolgálópéldány konfigurálása Always On rendelkezésre állási csoportokhoz &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Rendelkezésre állási csoport felügyeleti &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Rendelkezésre állási csoportok figyelése &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Az Always On rendelkezésre állási csoportokra vonatkozó Transact-SQL-utasítások áttekintése &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Az Always On rendelkezésre állási csoportok powerShell-parancsmagok áttekintése &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)
---
title: Rendelkezésre állási csoport konfigurálása (PowerShell-& az parancssori felület)
description: A PowerShell vagy az Azure CLI használatával hozza létre a Windows feladatátvevő fürtöt, a rendelkezésre állási csoport figyelőjét és a belső terheléselosztó SQL Server VM az Azure-ban.
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
ms.openlocfilehash: 865ee3a5aeb8a2dd06d8759ba04d02259d2b4bee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359965"
---
# <a name="use-powershell-or-az-cli-to-configure-an-availability-group-for-sql-server-on-azure-vm"></a>Az Azure-beli virtuális gépen SQL Server rendelkezésre állási csoport konfigurálása a PowerShell vagy az a CLI használatával 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez a cikk azt ismerteti, hogyan használható a [PowerShell](/powershell/scripting/install/installing-powershell) vagy az [Azure CLI](/cli/azure/sql/vm) egy Windows feladatátvevő fürt üzembe helyezéséhez, SQL Server virtuális gépek hozzáadásához a fürthöz, valamint az Always On rendelkezésre állási csoport létrehozásához a belső terheléselosztó és figyelő. 

A rendelkezésre állási csoport üzembe helyezése a SQL Server Management Studio (SSMS) vagy a Transact-SQL (T-SQL) használatával manuálisan történik. 

Habár ez a cikk a PowerShellt és az az az CLI-t használja a rendelkezésre állási csoport környezetének konfigurálásához, az [Azure Portal](availability-group-azure-portal-configure.md), az [Azure Gyorsindítás sablonok](availability-group-quickstart-template-configure.md)használatával vagy [manuálisan](availability-group-manually-configure-tutorial.md) is megteheti. 

## <a name="prerequisites"></a>Előfeltételek

Az Always On rendelkezésre állási csoport konfigurálásához a következő előfeltételek szükségesek: 

- Egy [Azure-előfizetés](https://azure.microsoft.com/free/).
- Egy tartományvezérlővel rendelkező erőforráscsoport. 
- Egy vagy több [, az Azure-ban SQL Server 2016 (vagy újabb) Enterprise Edition rendszert futtató,](./create-sql-vm-portal.md) tartományhoz csatlakoztatott virtuális gép *ugyanazon* rendelkezésre állási csoporton vagy *különböző* rendelkezésre állási zónákban, amelyek [regisztrálva vannak az SQL IaaS-ügynök bővítménnyel](sql-agent-extension-manually-register-single-vm.md).  
- A [PowerShell](/powershell/scripting/install/installing-powershell) vagy az [Azure CLI](/cli/azure/install-azure-cli)legújabb verziója. 
- Két elérhető (egyetlen entitás sem használja) IP-címek. Az egyik a belső terheléselosztó. A másik a rendelkezésre állási csoporttal megegyező alhálózaton belüli figyelő. Ha meglévő terheléselosztó használatával rendelkezik, csak egy elérhető IP-címet kell használnia a rendelkezésre állási csoport figyelője számára. 

## <a name="permissions"></a>Engedélyek

Az Always On rendelkezésre állási csoport az Azure CLI használatával történő konfigurálásához a következő fiókra van szükség: 

- Egy meglévő tartományi felhasználói fiók, amely **számítógép-objektum létrehozása** engedéllyel rendelkezik a tartományban. Például egy tartományi rendszergazdai fióknak jellemzően megfelelő engedélye van (például: account@domain.com ). _Ennek a fióknak a helyi rendszergazda csoportnak is szerepelnie kell az egyes virtuális gépeken a fürt létrehozásához._
- A SQL Server vezérlő tartományi felhasználói fiók. 
 
## <a name="create-a-storage-account"></a>Tárfiók létrehozása 

A fürtnek olyan Storage-fiókra van szüksége, amely tanúsítja a felhőt. Bármilyen meglévő Storage-fiókot használhat, vagy létrehozhat egy új Storage-fiókot is. Ha meglévő Storage-fiókot szeretne használni, ugorjon a következő szakaszra. 

A következő kódrészlet létrehozza a Storage-fiókot: 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> Előfordulhat, hogy a hiba akkor jelenik meg, `az sql: 'vm' is not in the 'az sql' command group` Ha az Azure CLI elavult verzióját használja. Töltse le az [Azure CLI legújabb verzióját](/cli/azure/install-azure-cli-windows) , hogy megszerezze a hibát.


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

Az Azure CLI az [SQL VM Group](/cli/azure/sql/vm/group) parancs a rendelkezésre állási csoportot üzemeltető Windows Server feladatátvételi fürt (WSFC) szolgáltatás metaadatait kezeli. A fürt metaadatai közé tartozik a Active Directory tartomány, a fürt fiókjai, a Felhőbeli tanúként használandó Storage-fiókok és a SQL Server verziója. Az az [SQL VM Group Create](/cli/azure/sql/vm/group#az-sql-vm-group-create) paranccsal definiálhatja a WSFC metaadatait, hogy az első SQL Server VM hozzáadásakor a fürt a megadott módon legyen létrehozva. 

A következő kódrészlet a fürt metaadatait határozza meg:

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

A fürt első SQL Server VM hozzáadásával létrehozza a fürtöt. Az az [SQL VM-bővítmény – csoport](/cli/azure/sql/vm#az-sql-vm-add-to-group) parancs létrehozza a fürtöt a korábban megadott névvel, telepíti a fürt szerepkört a SQL Server virtuális gépekre, és hozzáadja őket a fürthöz. A parancs későbbi használata `az sql vm add-to-group` további SQL Server virtuális gépeket ad hozzá az újonnan létrehozott fürthöz. 

A következő kódrészlet létrehozza a fürtöt, és hozzáadja az első SQL Server VMt: 

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
Ezzel a paranccsal adhat hozzá más SQL Server virtuális gépeket a fürthöz. Csak a `-n` paramétert módosítsa a SQL Server VM nevéhez. 

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

A Microsoft által támogatott feladatátvevő fürtökön át kell adni a fürt érvényesítését. Kapcsolódjon a virtuális géphez a kívánt módszerrel, például RDP protokoll (RDP), és ellenőrizze, hogy a fürt megfelel-e az érvényesítésnek a folytatás előtt. Ennek elmulasztása esetén a fürt nem támogatott állapotban hagyható. 

A fürtöt Feladatátvevőfürt-kezelő (FCM) vagy a következő PowerShell-parancs használatával ellenőrizheti:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

## <a name="create-availability-group"></a>Rendelkezésre állási csoport létrehozása

A szokásos módon hozza létre manuálisan a rendelkezésre állási csoportot a [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), a [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)vagy a [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql)használatával. 

>[!IMPORTANT]
> Jelenleg *ne hozzon* létre figyelőt, mert ez az alábbi részekben található Azure CLI-n keresztül történik.  

## <a name="create-internal-load-balancer"></a>Belső terheléselosztó létrehozása

[!INCLUDE [sql-ag-use-dnn-listener](../../includes/sql-ag-use-dnn-listener.md)]

Az Always On rendelkezésre állási csoport figyelője a Azure Load Balancer belső példányát igényli. A belső terheléselosztó egy "lebegő" IP-címet biztosít a rendelkezésre állási csoport figyelője számára, amely gyorsabb feladatátvételt és újracsatlakoztatást tesz lehetővé. Ha a rendelkezésre állási csoportban lévő SQL Server virtuális gépek ugyanazon rendelkezésre állási csoportba tartoznak, alapszintű Load balancert is használhat. Ellenkező esetben standard Load balancert kell használnia.  

> [!NOTE]
> A belső terheléselosztónek ugyanabban a virtuális hálózatban kell lennie, mint a SQL Server VM példányoknak. 

A következő kódrészlet létrehozza a belső terheléselosztó:

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
> Az egyes SQL Server VMokhoz tartozó nyilvános IP-erőforrásnak standard SKU-nak kell lennie, hogy kompatibilis legyen a standard Load balancerrel. A virtuális gép nyilvános IP-címéhez tartozó SKU meghatározásához lépjen az **erőforráscsoport** elemre, válassza ki a **nyilvános IP-cím** erőforrást a kívánt SQL Server VMhoz, és keresse meg az értéket az **áttekintő** ablaktábla **SKU** elemében.  

## <a name="create-listener"></a>Figyelő létrehozása

A rendelkezésre állási csoport manuális létrehozása után a figyelőt az [az SQL VM AG-Listener](/cli/azure/sql/vm/group/ag-listener#az-sql-vm-group-ag-listener-create)paranccsal hozhatja létre. 

Az *alhálózati erőforrás-azonosító* a `/subnets/<subnetname>` virtuális hálózati erőforráshoz tartozó erőforrás-azonosítóhoz hozzáfűzett érték. Az alhálózati erőforrás AZONOSÍTÓjának azonosítása:
   1. Nyissa meg az erőforráscsoportot a [Azure Portal](https://portal.azure.com). 
   1. Válassza ki a virtuális hálózati erőforrást. 
   1. A **Beállítások** ablaktáblán válassza a **Tulajdonságok** lehetőséget. 
   1. Azonosítsa a virtuális hálózat erőforrás-AZONOSÍTÓját, és fűzze hozzá a `/subnets/<subnetname>` végponthoz az alhálózati erőforrás-azonosító létrehozásához. Például:
      - A virtuális hálózati erőforrás-azonosító: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - Az alhálózat neve: `default`
      - Ezért az alhálózati erőforrás-azonosító a következő: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


A következő kódrészlet hozza létre a rendelkezésre állási csoport figyelőjét:

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
Az Azure-ban üzemeltetett virtuális gépek SQL Server a rendelkezésre állási csoport üzembe helyezése során további bonyolultsági szinttel bővült. Az erőforrás-szolgáltató és a virtuálisgép-csoport most már kezeli az erőforrásokat. Így amikor replikákat ad hozzá vagy távolít el a rendelkezésre állási csoportból, a figyelő metaadatait a SQL Server virtuális gépekkel kapcsolatos információkkal együtt kell frissítenie. A rendelkezésre állási csoportban lévő replikák számának módosításakor az az [SQL VM Group AG-Listener Update](/cli/azure/sql/vm/group/ag-listener#az-sql-vm-group-ag-listener-update) paranccsal is frissítheti a figyelőt a SQL Server virtuális gépek metaadataival. 


### <a name="add-a-replica"></a>Replika hozzáadása

Új replika hozzáadása a rendelkezésre állási csoporthoz:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Adja hozzá a SQL Server VMt a fürt csoportjához:
   ```azurecli-interactive

   # Add the SQL Server VM to the cluster group
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```

1. A SQL Server Management Studio használatával adja hozzá a SQL Server példányt replikaként a rendelkezésre állási csoporton belül.
1. Adja hozzá a SQL Server VM metaadatokat a figyelőhöz:

   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Adja hozzá a SQL Server VMt a fürt csoportjához:

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

1. A SQL Server Management Studio használatával adja hozzá a SQL Server példányt replikaként a rendelkezésre állási csoporton belül.
1. Adja hozzá a SQL Server VM metaadatokat a figyelőhöz:

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

1. Távolítsa el a replikát a rendelkezésre állási csoportból SQL Server Management Studio használatával. 
1. Távolítsa el a SQL Server VM metaadatokat a figyelőből:
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. Távolítsa el a SQL Server VM a fürtből:
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Távolítsa el a replikát a rendelkezésre állási csoportból SQL Server Management Studio használatával. 
1. Távolítsa el a SQL Server VM metaadatokat a figyelőből:

   ```powershell-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: Update-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
   #  -SqlVMGroupName Cluster -SqlVirtualMachineId SQLVM3

   Update-AzAvailabilityGroupListener -Name <Listener> -ResourceGroupName <Resource Group Name> `
      -SqlVMGroupName <cluster name> -SqlVirtualMachineId <SQL VMs that remain>

   ```
1. Távolítsa el a SQL Server VM a fürtből:

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

## <a name="remove-listener"></a>Figyelő eltávolítása
Ha később el kell távolítania az Azure CLI-vel konfigurált rendelkezésre állási csoport figyelőjét, el kell végeznie az SQL IaaS-ügynök bővítményt. Mivel a figyelő az SQL IaaS-ügynök bővítménnyel van regisztrálva, csak a SQL Server Management Studio-en keresztüli törlés nem elegendő. 

Az ajánlott módszer az SQL IaaS-ügynök bővítményen keresztüli törlése az Azure CLI következő kódrészletének használatával. Ezzel eltávolítja a rendelkezésre állási csoport figyelő metaadatait az SQL IaaS-ügynök bővítménnyel. Emellett fizikailag törli a figyelőt a rendelkezésre állási csoportból. 

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

Távolítsa el az összes csomópontot a fürtből annak megsemmisítéséhez, majd távolítsa el a fürt metaadatait az SQL IaaS-ügynök bővítménnyel. Ezt az Azure CLI vagy a PowerShell használatával teheti meg. 


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Először távolítsa el az összes SQL Server virtuális gépet a fürtből: 

```azurecli-interactive
# Remove the VM from the cluster metadata
# example: az sql vm remove-from-group --name SQLVM2 --resource-group SQLVM-RG

az sql vm remove-from-group --name <VM1 name>  --resource-group <resource group name>
az sql vm remove-from-group --name <VM2 name>  --resource-group <resource group name>
```

Ha ezek az egyetlen virtuális gép a fürtben, akkor a rendszer megsemmisíti a fürtöt. Ha a fürtben más virtuális gépek is találhatók, az eltávolított SQL Server virtuális gépektől függetlenül, a többi virtuális gép nem lesz eltávolítva, és a fürt nem lesz megsemmisítve. 

Ezután távolítsa el a fürt metaadatait az SQL IaaS-ügynök bővítményből: 

```azurecli-interactive
# Remove the cluster from the SQL VM RP metadata
# example: az sql vm group delete --name Cluster --resource-group SQLVM-RG

az sql vm group delete --name <cluster name> Cluster --resource-group <resource group name>
```



# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Először távolítsa el az összes SQL Server virtuális gépet a fürtből. Ezzel fizikailag eltávolítja a csomópontokat a fürtből, és megsemmisíti a fürtöt: 

```powershell-interactive
# Remove the SQL VM from the cluster
# example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
#  $sqlvm. SqlVirtualMachineGroup = ""
#  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

$sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
   $sqlvm. SqlVirtualMachineGroup = ""
   
   Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
```

Ha ezek az egyetlen virtuális gép a fürtben, akkor a rendszer megsemmisíti a fürtöt. Ha a fürtben más virtuális gépek is találhatók, az eltávolított SQL Server virtuális gépektől függetlenül, a többi virtuális gép nem lesz eltávolítva, és a fürt nem lesz megsemmisítve. 

Ezután távolítsa el a fürt metaadatait az SQL IaaS-ügynök bővítményből: 

```powershell-interactive
# Remove the cluster metadata
# example: Remove-AzSqlVMGroup -ResourceGroupName "SQLVM-RG" -Name "Cluster"

Remove-AzSqlVMGroup -ResourceGroupName "<resource group name>" -Name "<cluster name> "
```

---

## <a name="next-steps"></a>Következő lépések

További információért tekintse át a következő cikkeket: 

* [SQL Server virtuális gépek áttekintése](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [SQL Server virtuális gépekkel kapcsolatos gyakori kérdések](frequently-asked-questions-faq.md)
* [SQL Server virtuális gépek kibocsátási megjegyzései](../../database/doc-changes-updates-release-notes.md)
* [SQL Server VM licencelési modelljeinek váltása](licensing-model-azure-hybrid-benefit-ahb-change.md)
* [Az Always On rendelkezésre állási csoportok áttekintése &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Az Always On rendelkezésre állási csoportokhoz tartozó kiszolgálópéldány konfigurálása &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Rendelkezésre állási csoport felügyelete &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [A rendelkezésre állási csoportok figyelése &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Az Always On rendelkezésre állási csoportokra vonatkozó Transact-SQL-utasítások áttekintése &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Az Always On rendelkezésre állási csoportokhoz tartozó PowerShell-parancsmagok áttekintése &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)
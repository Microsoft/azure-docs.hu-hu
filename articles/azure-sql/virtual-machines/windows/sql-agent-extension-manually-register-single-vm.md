---
title: Regisztrálás SQL IaaS-ügynök bővítménnyel
description: Regisztrálja Azure SQL Server virtuális gépét az SQL IaaS-ügynök bővítményével, hogy lehetővé tegye az Azure Marketplace-en kívül üzembe helyezett SQL Server virtuális gépek funkcióinak, valamint a megfelelőség és a jobb kezelhetőség biztosítását.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurecli, devx-track-azurepowershell, contperf-fy21q2
ms.openlocfilehash: 3cea15114e125951a8fbec73f965b272a4f8053d
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284158"
---
# <a name="register-sql-server-vm-with-sql-iaas-agent-extension"></a>SQL Server VM regisztrálása az SQL IaaS-ügynök bővítménnyel
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Regisztrálja SQL Server VM az [SQL IaaS-ügynök bővítményével](sql-server-iaas-agent-extension-automate-management.md) , hogy feloldja az Azure-beli virtuális gépen futó SQL Server számos funkciójának előnyeit. 

Ebből a cikkből megtudhatja, hogyan regisztrálhat egyetlen SQL Server VM az SQL IaaS-ügynök bővítménnyel. Azt is megteheti, hogy az összes SQL Server virtuális gépet [automatikusan](sql-agent-extension-automatic-registration-all-vms.md) vagy [több, tömegesen megírt virtuális](sql-agent-extension-manually-register-vms-bulk.md)gépre regisztrálja.


## <a name="overview"></a>Áttekintés

A [SQL Server IaaS-ügynök bővítménnyel](sql-server-iaas-agent-extension-automate-management.md) való regisztrálása létrehozza az SQL-alapú **virtuálisgép** - _erőforrást_ az előfizetésen belül, amely a virtuális gép erőforrásának _külön_ erőforrása. Ha törli a SQL Server VM regisztrációját a bővítményből, a rendszer eltávolítja az SQL-alapú **virtuális gép** _erőforrását_ , de nem dobja el a tényleges virtuális gépet.

SQL Server VM Azure Marketplace-rendszerképnek a Azure Portal használatával történő üzembe helyezése automatikusan regisztrálja a SQL Server VM a kiterjesztéssel. Ha azonban úgy dönt, hogy egy Azure-beli virtuális gépen telepíti a SQL Servert, vagy egyéni virtuális MEREVLEMEZről szeretne üzembe helyezni egy Azure-beli virtuális gépet, akkor a teljes funkció előnyeinek és kezelhetőségének feloldásához regisztrálnia kell a SQL Server VMt az SQL IaaS-ügynök bővítménnyel. 

Az SQL IaaS-ügynök bővítményének használatához először [regisztrálnia kell az előfizetését a **Microsoft. SqlVirtualMachine** szolgáltatónál](#register-subscription-with-resource-provider), amely az SQL IaaS bővítmény lehetővé teszi az erőforrások létrehozását az adott előfizetésen belül.

> [!IMPORTANT]
> Az SQL IaaS-ügynök bővítmény olyan adatokat gyűjt az expressz célra, amelyek az ügyfelek számára opcionális előnyöket biztosítanak SQL Server Azure-beli Virtual Machines való használatakor. A Microsoft ezeket az adatszolgáltatásokat nem használja fel a licencelési naplózáshoz az ügyfél előzetes belefoglalása nélkül. További információért tekintse meg a [SQL Server adatvédelmi kiegészítését](/sql/sql-server/sql-server-privacy#non-personal-data) .

## <a name="prerequisites"></a>Előfeltételek

A SQL Server VM a bővítménnyel való regisztrálásához a következőkre lesz szüksége: 

- Egy [Azure-előfizetés](https://azure.microsoft.com/free/).
- Egy Azure-erőforrás modellje a [Windows Server 2008 (vagy újabb) rendszerű virtuális gép](../../../virtual-machines/windows/quick-create-portal.md) , amely [SQL Server 2008 (vagy újabb)](https://www.microsoft.com/sql-server/sql-server-downloads) rendszert helyez üzembe a nyilvános vagy Azure Government felhőben. 
- Az [Azure CLI](/cli/azure/install-azure-cli) legújabb verziója vagy [Azure PowerShell (minimum 5,0)](/powershell/azure/install-az-ps). 


## <a name="register-subscription-with-resource-provider"></a>Előfizetés regisztrálása erőforrás-szolgáltatóval

Ha regisztrálni szeretné a SQL Server VMt az SQL IaaS-ügynök bővítménnyel, először regisztrálnia kell az előfizetését a **Microsoft. SqlVirtualMachine** erőforrás-szolgáltatóval. Ez biztosítja, hogy az SQL IaaS-ügynök bővítmény képes legyen erőforrásokat létrehozni az előfizetésen belül.  Ezt a Azure Portal, az Azure CLI vagy a Azure PowerShell használatával teheti meg.

### <a name="azure-portal"></a>Azure Portal

1. Nyissa meg a Azure Portalt, és válassza a **minden szolgáltatás** lehetőséget. 
1. Lépjen az **előfizetések** elemre, és válassza ki a kamat előfizetését.  
1. Az **előfizetések** lapon válassza a **bővítmények** lehetőséget. 
1. Adja **meg az SQL-** t a szűrőben az SQL-hez kapcsolódó bővítmények létrehozásához. 
1. Válassza a **Microsoft. SqlVirtualMachine** -szolgáltató **regisztrálása**, **újbóli regisztrálása** vagy **regisztrációjának** törlése lehetőséget a kívánt művelettől függően. 

   ![A szolgáltató módosítása](./media/sql-agent-extension-manually-register-single-vm/select-resource-provider-sql.png)


### <a name="command-line"></a>Parancssor

Regisztrálja Azure-előfizetését a **Microsoft. SqlVirtualMachine** szolgáltatónál az Azure CLI vagy a Azure PowerShell használatával. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

```azurecli-interactive
# Register the SQL IaaS Agent extension to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL IaaS Agent extension to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-with-extension"></a>Regisztráció bővítménysel

A [SQL Server IaaS-ügynök bővítményének](sql-server-iaas-agent-extension-automate-management.md#management-modes)három kezelési módja van. 

A bővítmény teljes felügyeleti módban való regisztrálása újraindítja a SQL Server szolgáltatást, ezért azt javasoljuk, hogy először az egyszerűsített módban regisztrálja a bővítményt, majd a karbantartási időszak alatt [teljesre frissítsen](#upgrade-to-full) . 

### <a name="lightweight-management-mode"></a>Könnyűsúlyú felügyeleti mód

Az Azure CLI vagy a Azure PowerShell használatával a bővítményt egyszerűsített módban regisztrálhatja a SQL Server VM. Ez nem fogja újraindítani a SQL Server szolgáltatást. Ezt követően bármikor frissítheti a teljes üzemmódra, de ez a művelet újraindítja a SQL Server szolgáltatást, ezért ajánlott megvárni az ütemezett karbantartási időszakot. 

Az ingyenes DR replika-licenc aktiválásához adja meg az SQL Server licenc típusát, mint az utólagos elszámolású ( `PAYG` ) használati díjat, Azure Hybrid Benefit ( `AHUB` ) használja a saját licencét, vagy a vész-helyreállítást ( `DR` ). [](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)

A feladatátvevő fürtök példányai és a többpéldányos központi telepítések csak az SQL IaaS-ügynök bővítménnyel regisztrálhatók egyszerűsített módban. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

SQL Server VM regisztrálása könnyűsúlyú módban az Azure CLI-vel: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type <license_type> 
  ```


# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

SQL Server VM regisztrálása egyszerűsített módban Azure PowerShell:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType <license_type>  -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Teljes felügyeleti mód

Ha a SQL Server VM teljes módban regisztrálja, a rendszer újraindítja a SQL Server szolgáltatást. Körültekintően járjon el. 

Ha a SQL Server VMt közvetlenül teljes módban szeretné regisztrálni (és valószínűleg újraindítani a SQL Server szolgáltatást), használja a következő Azure PowerShell parancsot: 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL IaaS Agent extension in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>Ügynök-felügyeleti mód 

A Windows Server 2008 (_nem R2_) rendszerre telepített 2008-es és 2008 R2-es SQL Server a nem [ügynök módban](sql-server-iaas-agent-extension-automate-management.md#management-modes)lehet regisztrálni az SQL IaaS-ügynök bővítménnyel. Ez a beállítás biztosítja a megfelelőséget, és lehetővé teszi a SQL Server VM figyelését a Azure Portal korlátozott funkcionalitással.


A **licenc típusa** mezőben adja meg a következőt: `AHUB` , `PAYG` , vagy `DR` . A **rendszerkép-ajánlathoz** adjon meg `SQL2008-WS2008` vagy `SQL2008R2-WS2008`

A SQL Server 2008 ( `SQL2008-WS2008` ) vagy a 2008 R2 ( `SQL2008R2-WS2008` ) Windows Server 2008-példányon való regisztrálásához használja a következő Azure CLI-vagy Azure PowerShell-kódrészletet: 


# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Regisztrálja SQL Server virtuális gépét a nem ügynök módban az Azure CLI-vel: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type <license type>  --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer <image offer> 
 ```
 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)


Regisztrálja SQL Server virtuális gépét a Azure PowerShell: 

  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType <license type> -SqlManagementType NoAgent -Sku Standard -Offer <image offer>
  ```

---

## <a name="verify-mode"></a>Mód ellenőrzése

A SQL Server IaaS-ügynök aktuális módja a Azure PowerShell használatával tekinthető meg: 

```powershell-interactive
# Get the SqlVirtualMachine
$sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
$sqlvm.SqlManagementType
```

## <a name="upgrade-to-full"></a>Frissítés teljesre  

SQL Server *egyszerűsített* módban regisztrált virtuális gépek a Azure Portal, az Azure CLI vagy a Azure PowerShell használatával _teljes mértékben_ frissíthetők. SQL Server a nem _ügynök_ módban lévő virtuális gépek az operációs rendszer Windows 2008 R2 vagy újabb verzióra való frissítése után _teljes egészében_ frissíthetnek. Nem lehetséges a visszalépés – ehhez meg kell [szüntetnie a SQL Server VM regisztrációját](#unregister-from-extension) az SQL IaaS-ügynök bővítménnyel. Ezzel a művelettel eltávolítja az SQL-alapú **virtuális gép** _erőforrását_, de nem törli a tényleges virtuális gépet. 

> [!NOTE]
> Ha az SQL IaaS-bővítmény felügyeleti üzemmódját teljesre frissíti, a rendszer újraindítja a SQL Server szolgáltatást. Bizonyos esetekben az újraindítás miatt előfordulhat, hogy az SQL Server szolgáltatáshoz társított egyszerű szolgáltatásnév (SPN) nem megfelelő felhasználói fiókra vált. Ha a felügyeleti mód teljes verzióra történő frissítése után csatlakozási problémák léptek fel, akkor [szüntesse meg a regisztrációt, és regisztrálja újra az SPN](/sql/database-engine/configure-windows/register-a-service-principal-name-for-kerberos-connections)-ket.


### <a name="azure-portal"></a>Azure Portal

Ha a bővítményt teljes módra szeretné frissíteni a Azure Portal használatával, kövesse az alábbi lépéseket: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Nyissa meg az SQL-alapú [virtuális gépek](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) erőforrását. 
1. Válassza ki a SQL Server VM, és válassza az **Áttekintés** lehetőséget. 
1. A nem ügynökkel vagy Lightweight IaaS-móddal rendelkező SQL Server virtuális gépek esetében válassza az **SQL IaaS kiterjesztési üzenetében csak a licenc típusát és a kiadási frissítéseket** .

   ![A mód a portálról való módosításának kiválasztása](./media/sql-agent-extension-manually-register-single-vm/change-sql-iaas-mode-portal.png)

1. Jelölje be az **Elfogadom a SQL Server szolgáltatás újraindítása a virtuális gépen** jelölőnégyzetet, majd válassza a **megerősítés** lehetőséget a IaaS mód teljes állapotra való frissítéséhez. 

    ![A virtuális gépen SQL Server szolgáltatás újraindítását kérő jelölőnégyzet](./media/sql-agent-extension-manually-register-single-vm/enable-full-mode-iaas.png)

### <a name="command-line"></a>Parancssor

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

A bővítmény teljes módba való frissítéséhez futtassa az alábbi Azure CLI-kódrészletet:

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

A bővítmény teljes módba való frissítéséhez futtassa a következő Azure PowerShell kódrészletet:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL IaaS Agent extension in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Regisztrációs állapot ellenőrzése
A Azure Portal, az Azure CLI vagy a Azure PowerShell használatával ellenőrizheti, hogy a SQL Server VM már regisztrálva van-e az SQL IaaS-ügynök bővítménnyel. 

### <a name="azure-portal"></a>Azure Portal 

A regisztrációs állapot a Azure Portal használatával történő ellenőrzéséhez kövesse az alábbi lépéseket: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
1. Nyissa meg a [SQL Server virtuális gépeket](manage-sql-vm-portal.md).
1. Válassza ki a SQL Server VM a listából. Ha a SQL Server VM nem szerepel a listán, valószínűleg nincs regisztrálva az SQL IaaS-ügynök bővítményében. 
1. Tekintse meg az **állapot** alatt lévő értéket. Ha  az állapot **sikeres**, akkor a SQL Server VM sikeresen regisztrálva lett az SQL IaaS-ügynök bővítményében. 

   ![SQL RP-regisztrációval rendelkező állapot ellenőrzése](./media/sql-agent-extension-manually-register-single-vm/verify-registration-status.png)

### <a name="command-line"></a>Parancssor

Ellenőrizze az aktuális SQL Server VM regisztrációs állapotot az Azure CLI vagy a Azure PowerShell használatával. `ProvisioningState` azt mutatja, `Succeeded` hogy a regisztráció sikeres volt-e. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

A regisztrációs állapot az Azure CLI használatával történő ellenőrzéséhez futtassa a következő kódrészletet:  

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

A regisztrációs állapot a Azure PowerShell használatával történő ellenőrzéséhez futtassa a következő kódrészletet:

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

A hiba azt jelzi, hogy a SQL Server VM nincs regisztrálva a bővítményben. 


## <a name="unregister-from-extension"></a>Regisztráció törlése a bővítményből

Ha meg szeretné szüntetni a SQL Server VM regisztrációját az SQL IaaS-ügynök bővítménnyel, törölje az SQL-alapú virtuális gép *erőforrását* az Azure Portal vagy az Azure CLI használatával. Az SQL-alapú virtuális gép *erőforrásának* törlése nem törli a SQL Server VM. Azonban körültekintően járjon el, és gondosan kövesse a lépéseket, mert lehetséges, hogy véletlenül törli a virtuális gépet az *erőforrás* eltávolítására tett kísérlet során. 

A felügyeleti mód teljes állapotának visszavonásához le kell törölni az SQL-IaaS ügynök bővítménnyel való regisztrációját. 

### <a name="azure-portal"></a>Azure Portal

Ha törölni szeretné a SQL Server VM regisztrációját a bővítményből a Azure Portal használatával, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Navigáljon az SQL VM-erőforráshoz. 
  
   ![SQL-alapú virtuális gépek erőforrása](./media/sql-agent-extension-manually-register-single-vm/sql-vm-manage.png)

1. Válassza a **Törlés** elemet. 

   ![Válassza a Törlés lehetőséget a felső navigációs sávon](./media/sql-agent-extension-manually-register-single-vm/delete-sql-vm-resource.png)

1. Írja be az SQL-virtuális gép nevét, és **törölje a virtuális gép melletti jelölőnégyzet** jelölését.

   ![Szüntesse meg a virtuális gép törlését, hogy ne törölje a tényleges virtuális gépet, majd válassza a Törlés lehetőséget az SQL VM-erőforrás törlésének folytatásához.](./media/sql-agent-extension-manually-register-single-vm/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Nem sikerült törölni a virtuális gép neve melletti jelölőnégyzetet a virtuális gép teljes *törléséhez* . Törölje a jelet a SQL Server VM regisztrációjának törléséhez a bővítményből, de *ne törölje a tényleges virtuális gépet*. 

1. Válassza a **Törlés** lehetőséget az SQL-alapú virtuális gép *erőforrásának* törlésének megerősítéséhez, és ne a SQL Server VM. 

### <a name="command-line"></a>Parancssor

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha törölni szeretné a SQL Server VM regisztrációját a bővítményből az Azure CLI-vel, használja az az [SQL VM delete](/cli/azure/sql/vm#az-sql-vm-delete) parancsot. Ezzel eltávolítja a SQL Server VM *erőforrást* , de nem törli a virtuális gépet. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ha törölni szeretné a SQL Server VM regisztrációját a bővítményből a Azure PowerShell használatával, használja a [Remove-AzSqlVM](/powershell/module/az.sqlvirtualmachine/remove-azsqlvm)parancsot. Ezzel eltávolítja a SQL Server VM *erőforrást* , de nem törli a virtuális gépet. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---


## <a name="next-steps"></a>Következő lépések

További információért tekintse át a következő cikkeket: 

* [Windows rendszerű virtuális gépek SQL Server áttekintése](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows rendszerű virtuális gépen SQL Server gyakori kérdések](frequently-asked-questions-faq.md)  
* [A Windows rendszerű virtuális gépek SQL Server díjszabási útmutatója](pricing-guidance.md)
* [Windows rendszerű virtuális gépen SQL Server kibocsátási megjegyzései](../../database/doc-changes-updates-release-notes.md)

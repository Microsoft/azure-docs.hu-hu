---
title: Regisztrálás sql IaaS-ügynökbővítményben
description: Regisztrálja a Azure SQL Servert futtató virtuális gépet az SQL IaaS-ügynök bővítővel, hogy az SQL Server Azure Marketplace-n kívül telepített SQL Server virtuális gépek funkcióit, a megfelelőséget és a jobb kezelhetőséget is lehetővé tegye.
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
ms.openlocfilehash: e34876c76259b8274e0b0ef9059659802eb55cf1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765448"
---
# <a name="register-sql-server-vm-with-sql-iaas-agent-extension"></a>Regisztrálás SQL Server VM SQL IaaS-ügynökbővítményben
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ha regisztrálja SQL Server VM az [SQL IaaS-ügynök](sql-server-iaas-agent-extension-automate-management.md) bővítővel, számos funkcióval SQL Server Azure-beli virtuális gépen. 

Ez a cikk azt tanítja meg, hogyan regisztrálhat egyetlen SQL Server VM az SQL IaaS-ügynök bővítményben. Másik lehetőségként automatikusan regisztrálhat minden virtuális [](sql-agent-extension-automatic-registration-all-vms.md) SQL Server virtuális gépeket, vagy több, tömegesen [szkriptált virtuális gépeket](sql-agent-extension-manually-register-vms-bulk.md)is.


## <a name="overview"></a>Áttekintés

A SQL Server [IaaS-ügynök](sql-server-iaas-agent-extension-automate-management.md) bővítményével való regisztráció létrehozza az **SQL**  virtuálisgép-erőforrást az előfizetésen belül, amely a virtuálisgép-erőforrástól különálló erőforrás.  Ha törli a SQL Server VM a bővítményből, az eltávolítja az **SQL** virtuális gép erőforrását,  de a tényleges virtuális gépet nem.

A virtuálisgép SQL Server VM Azure Marketplace rendszerkép üzembe helyezése a Azure Portal automatikusan regisztrálja a SQL Server VM a bővítővel. Ha azonban úgy dönt, hogy az SQL Server-t önálló telepítéssel telepíti egy Azure-beli virtuális gépen, vagy azure-beli virtuális gépet hoz létre egyéni virtuális merevlemezről, akkor regisztrálnia kell az SQL Server VM-t az SQL IaaS-ügynök bővítményével, hogy ki tudja oldani a funkciók összes előnyét és kezelhetőségét. 

Az SQL IaaS Agent bővítmény kihasználásához először regisztrálnia kell az előfizetést a [ **Microsoft.SqlVirtualMachine**](#register-subscription-with-resource-provider)szolgáltatónál, amely lehetővé teszi az SQL IaaS-bővítmény számára, hogy erőforrásokat hozzon létre az adott előfizetésen belül.

> [!IMPORTANT]
> Az SQL IaaS-ügynök bővítmény olyan adatokat gyűjt, amelyek kifejezett célja, hogy választható előnyöket biztosítsunk az ügyfeleknek az Azure SQL Server-Virtual Machines. A Microsoft ezeket az adatokat nem használja fel a licencelési naplózáshoz az ügyfél előzetes beleegyezése nélkül. További információt [SQL Server adatvédelmi kiegészítésében.](/sql/sql-server/sql-server-privacy#non-personal-data)

## <a name="prerequisites"></a>Előfeltételek

A SQL Server VM a bővítményben való regisztráláshoz a következőre lesz szüksége: 

- Egy [Azure-előfizetés](https://azure.microsoft.com/free/).
- Azure-erőforrásmodell [Windows Server 2008 (vagy nagyobb)](../../../virtual-machines/windows/quick-create-portal.md) rendszerű virtuális gép, SQL Server [2008-as (vagy nagyobb)](https://www.microsoft.com/sql-server/sql-server-downloads) verzióval a nyilvános vagy a felhőben Azure Government üzembe. 
- Az Azure [CLI](/cli/azure/install-azure-cli) vagy a [Azure PowerShell legújabb verziója (legalább 5.0).](/powershell/azure/install-az-ps) 


## <a name="register-subscription-with-resource-provider"></a>Előfizetés regisztrálása erőforrás-szolgáltatónál

Ha regisztrálnia SQL Server VM SQL IaaS-ügynök bővítményével, először regisztrálnia kell az előfizetését a **Microsoft.SqlVirtualMachine** erőforrás-szolgáltatónál. Ez lehetővé teszi az SQL IaaS-ügynök bővítményének, hogy erőforrásokat hozzon létre az előfizetésen belül.  Ezt az azure-beli virtuális Azure Portal, az Azure CLI vagy a Azure PowerShell.

### <a name="azure-portal"></a>Azure Portal

1. Nyissa meg a Azure Portal, és nyissa meg a **Minden szolgáltatás gombra.** 
1. Az **Előfizetések alatt válassza** ki a kívánt előfizetést.  
1. Az **Előfizetések lapon** válassza a **bővítmények lehetőséget.** 
1. Írja **be az SQL-t** a szűrőbe az SQL-hez kapcsolódó bővítményekhez. 
1. Válassza **a Register (Regisztrálás)**, **Re-register**(Újbóli regisztrálás) vagy a **Unregister** (Regisztráció) lehetőséget a  **Microsoft.SqlVirtualMachine** szolgáltatónál a kívánt művelettől függően. 

   ![A szolgáltató módosítása](./media/sql-agent-extension-manually-register-single-vm/select-resource-provider-sql.png)


### <a name="command-line"></a>Parancssor

Regisztrálja Azure-előfizetését a **Microsoft.SqlVirtualMachine** szolgáltatónál az Azure CLI vagy a Azure PowerShell. 

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

## <a name="register-with-extension"></a>Regisztrálás bővítménysel

Az [IaaS-ügynök bővítmény három SQL Server módban használható.](sql-server-iaas-agent-extension-automate-management.md#management-modes) 

A bővítmény teljes felügyeleti módban való regisztrálása újraindítja a SQL Server szolgáltatást, ezért javasoljuk, [](#upgrade-to-full) hogy először regisztrálja a bővítményt egyszerűsített módban, majd frissítsen teljes verzióra egy karbantartási időszak alatt. 

### <a name="lightweight-management-mode"></a>Egyszerűsített felügyeleti mód

Az Azure CLI vagy Azure PowerShell segítségével regisztrálhatja a SQL Server VM a bővítményben egyszerűsített módban. Ez nem indítja újra a SQL Server szolgáltatást. Ezt követően bármikor frissíthet teljes módra, de ezzel újraindítja a SQL Server szolgáltatást, ezért javasoljuk, hogy várjon egy ütemezett karbantartási időszakig. 

Az SQL Server licenctípust használat alapján fizetéses fizetéshez adja meg használat alapján, Azure Hybrid Benefit ( ) a saját licencének használata esetén, vagy vészhelyreállítás () használatával az ingyenes DR replikalicenc `PAYG` `AHUB` `DR` [aktiválásához.](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)

A feladatátvevő fürtpéldányok és a többpéldányos üzembe helyezések csak egyszerűsített módban regisztrálhatóak az SQL IaaS-ügynök bővítménysel. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Regisztráljon egy SQL Server VM egyszerűsített módban az Azure CLI-val: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type <license_type> 
  ```


# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Regisztráljon egy SQL Server VM egyszerűsített módban a következő Azure PowerShell:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType <license_type>  -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Teljes körű felügyeleti mód

Ha teljes módban SQL Server VM regisztrálja az SQL Server szolgáltatást. Körültekintően járjon el. 

A SQL Server VM teljes módban való regisztrálásához (és esetleg a SQL Server szolgáltatás újraindításához) használja a következő Azure PowerShell parancsot: 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL IaaS Agent extension in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>NoAgent felügyeleti mód 

SQL Server Windows Server 2008 (és nem _R2)_ rendszeren telepített 2008 és 2008 R2 regisztrálható az SQL IaaS-ügynök bővítményben [NoAgent módban.](sql-server-iaas-agent-extension-automate-management.md#management-modes) Ez a beállítás biztosítja a megfelelőséget, és SQL Server VM lehetővé teszi a Azure Portal funkciókkal való figyelését.


A **licenctípushoz adja** meg a következőket: `AHUB` , vagy `PAYG` `DR` . A **rendszerkép-ajánlathoz adja** meg a következőket: `SQL2008-WS2008` vagy `SQL2008R2-WS2008`

Az SQL Server 2008 ( ) vagy `SQL2008-WS2008` 2008 R2 ( ) Windows Server 2008-példányon való regisztráláshoz használja a következő Azure CLI-t vagy `SQL2008R2-WS2008` Azure PowerShell kódrészletet: 


# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Regisztrálja SQL Server virtuális gépet NoAgent módban az Azure CLI-val: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type <license type>  --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer <image offer> 
 ```
 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)


Regisztrálja SQL Server virtuális gépet NoAgent módban az Azure PowerShell: 

  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType <license type> -SqlManagementType NoAgent -Sku Standard -Offer <image offer>
  ```

---

## <a name="verify-mode"></a>Ellenőrzés mód

Az IaaS-ügynök aktuális SQL Server a következő Azure PowerShell: 

```powershell-interactive
# Get the SqlVirtualMachine
$sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
$sqlvm.SqlManagementType
```

## <a name="upgrade-to-full"></a>Frissítés teljes verzióra  

SQL Server bővítményt egyszerűsített módban regisztráló  virtuális gépek teljes  verzióra frissíthet a Azure Portal, az Azure CLI vagy a Azure PowerShell. SQL Server _NoAgent_ módban az operációs rendszer  Windows 2008 R2 vagy magasabb verzióra való frissítése után a virtuális gépek teljes verzióra frissíthetőek. Nem lehet visszalépést tenni – erre az SQL [](#unregister-from-extension) IaaS-ügynökbővítményben SQL Server VM kell a regisztrációt. Ezzel eltávolítja az **SQL virtuális** gép _erőforrását,_ de nem törli a tényleges virtuális gépet. 

> [!NOTE]
> Amikor az SQL IaaS-bővítmény felügyeleti módját teljesre frissíti, az újraindítja a SQL Server szolgáltatást. Bizonyos esetekben az újraindítás azt okozhatja, hogy a SQL Server szolgáltatáshoz társított egyszerű szolgáltatásnevek (SPN-k) rossz felhasználói fiókra változnak. Ha a felügyeleti mód teljes verzióra való frissítése után kapcsolódási problémák lépnek fel, akkor az SPN-ek regisztrációjának és [regisztrációjának újra megszabadulása után.](/sql/database-engine/configure-windows/register-a-service-principal-name-for-kerberos-connections)


### <a name="azure-portal"></a>Azure Portal

A bővítmény teljes módra való frissítését a Azure Portal kövesse az alábbi lépéseket: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Ugrás az [SQL virtuális gépek erőforrásra.](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) 
1. Válassza ki a SQL Server VM, majd válassza az **Áttekintés lehetőséget.** 
1. Ha SQL Server NoAgent vagy egyszerűsített IaaS módú virtuális gépeket, válassza a Csak licenctípus és kiadásfrissítések érhetők el az **SQL IaaS-bővítmény üzenettel** lehetőséget.

   ![A mód portálról való módosításának kijelölései](./media/sql-agent-extension-manually-register-single-vm/change-sql-iaas-mode-portal.png)

1. Jelölje be **az Elfogadom, hogy újraindítom** a SQL Server szolgáltatást a  virtuális gépen jelölőnégyzetet, majd válassza a Megerősítés lehetőséget az IaaS-mód teljes verzióra való frissítéshez. 

    ![Jelölőnégyzet a virtuális gép SQL Server szolgáltatás újraindításához](./media/sql-agent-extension-manually-register-single-vm/enable-full-mode-iaas.png)

### <a name="command-line"></a>Parancssor

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

A bővítmény teljes módra való frissítéshez futtassa a következő Azure CLI-kódrészletet:

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

A bővítmény teljes módra való frissítéshez futtassa a következő Azure PowerShell kódrészletet:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL IaaS Agent extension in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Regisztráció állapotának ellenőrzése
A Azure Portal, az Azure CLI vagy a Azure PowerShell segítségével ellenőrizheti, hogy a SQL Server VM-adatbázisa már regisztrálva van-e az SQL IaaS-ügynök bővítményben. 

### <a name="azure-portal"></a>Azure Portal 

A regisztráció állapotának ellenőrzéséhez kövesse Azure Portal következő lépéseket: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
1. A virtuális [géphez SQL Server meg.](manage-sql-vm-portal.md)
1. Válassza ki SQL Server VM a listából. Ha a SQL Server VM nem szerepel itt, akkor valószínűleg nem lett regisztrálva az SQL IaaS-ügynök bővítményben. 
1. Tekintse meg a Status (Állapot) **alatt látható értéket.** Ha **az Állapot** **Sikeres,** akkor a SQL Server VM sikeresen regisztrálva lett az SQL IaaS-ügynök bővítményben. 

   ![Állapot ellenőrzése SQL RP-regisztrációval](./media/sql-agent-extension-manually-register-single-vm/verify-registration-status.png)

### <a name="command-line"></a>Parancssor

Ellenőrizze az SQL Server VM állapotát az Azure CLI vagy a Azure PowerShell. `ProvisioningState` A `Succeeded` mutatja, ha a regisztráció sikeres volt. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

A regisztrációs állapot Azure CLI használatával való ellenőrzéséhez futtassa a következő kódrészletet:  

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

A regisztrációs állapot ellenőrzéséhez futtassa Azure PowerShell kódrészletet:

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

A hiba azt jelzi, SQL Server VM nincs regisztrálva a bővítményben. 


## <a name="unregister-from-extension"></a>A bővítmény regisztrációjának megszabadítása

Az SQL IaaS-SQL Server VM az SQL IaaS-ügynök bővítmény  regisztrációjának törléséhez törölje az SQL virtuális gép erőforrását az Azure Portal vagy az Azure CLI használatával. Az SQL virtuális gép erőforrásának *törlése* nem törli a SQL Server VM. Azonban körültekintően járjon el, és kövesse a lépéseket, mert előfordulhat, hogy véletlenül is törölni lehet a virtuális gépet, amikor megpróbálja eltávolítani a *erőforrást.* 

Az SQL-et futtató virtuális gépNEK az SQL IaaS-ügynök bővítővel való regisztrációjának a teljes felügyeleti módra való visszalépéséhez szükség van. 

### <a name="azure-portal"></a>Azure Portal

Az alábbi lépésekkel SQL Server VM a bővítményből a Azure Portal a regisztrációt:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Lépjen az SQL virtuális gép erőforráshoz. 
  
   ![SQL virtuális gépek erőforrás](./media/sql-agent-extension-manually-register-single-vm/sql-vm-manage.png)

1. Válassza a **Törlés** elemet. 

   ![Válassza a törlés lehetőséget a felső navigációs sávon](./media/sql-agent-extension-manually-register-single-vm/delete-sql-vm-resource.png)

1. Írja be az SQL virtuális gép nevét, és törölje a jelölést **a virtuális gép melletti jelölőnégyzetből.**

   ![Törölje a virtuális gép jelölését a tényleges virtuális gép törlésének megakadályozásához, majd válassza a Törlés lehetőséget az SQL-hez szükséges virtuális gép erőforrásának törléséhez](./media/sql-agent-extension-manually-register-single-vm/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Ha nem törli a virtuális gép neve melletti jelölőnégyzetet, a rendszer *teljesen törli* a virtuális gépet. Törölje a jelölőnégyzet jelölését, ha törölni szeretné a SQL Server VM a bővítményből, de ne törölje *a tényleges virtuális gépet.* 

1. Válassza **a Törlés** lehetőséget az SQL virtuális gép erőforrásának törlésének megerősítéséhez, és ne a SQL Server VM. 

### <a name="command-line"></a>Parancssor

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha törölni szeretné a SQL Server VM a bővítményből az Azure CLI-val, használja [az az sql vm delete](/cli/azure/sql/vm#az_sql_vm_delete) parancsot. Ezzel eltávolítja a SQL Server VM *erőforrást,* de nem törli a virtuális gépet. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ha törölni szeretné a SQL Server VM a bővítményből a Azure PowerShell, használja a [Remove-AzSqlVM](/powershell/module/az.sqlvirtualmachine/remove-azsqlvm)parancsot. Ezzel eltávolítja a SQL Server VM *erőforrást,* de nem törli a virtuális gépet. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---


## <a name="next-steps"></a>Következő lépések

További információért tekintse át a következő cikkeket: 

* [A windowsos SQL Server gépeken történő telepítés áttekintése](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Gyakori kérdések SQL Server Windows rendszerű virtuális gépeken történő telepítésről](frequently-asked-questions-faq.md)  
* [Díjszabási útmutató SQL Server Windows rendszerű virtuális gépeken történő telepítéshez](pricing-guidance.md)
* [Windows rendszerű SQL Server gépeken történő telepítés kibocsátási megjegyzései](../../database/doc-changes-updates-release-notes.md)

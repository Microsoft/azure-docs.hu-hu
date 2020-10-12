---
title: Az Azure-beli SQL virtuális gép licencelési modelljének módosítása
description: Megtudhatja, hogyan válthat az Azure-beli SQL Server VM licencelése az előfizetéses elszámolással a saját licencek használatára a Azure Hybrid Benefit használatával.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b09d808201d58b571b2fe5ceb2e228d4e1c21d11
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316953"
---
# <a name="change-the-license-model-for-a-sql-virtual-machine-in-azure"></a>Azure-beli, SQL-t futtató virtuális gépek licencmodelljének módosítása
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


Ez a cikk azt ismerteti, hogyan változtatható meg egy SQL Server virtuális gép (VM) licencelési modellje az Azure-ban az új SQL Server VM erőforrás-szolgáltató, a **Microsoft. SqlVirtualMachine**használatával.

A virtuális gépek három licenccel rendelkeznek, amelyek SQL Server üzemeltetik: utólagos elszámolású, Azure Hybrid Benefit (AHB) és vész-helyreállítási (DR). A SQL Server VM licencelési modelljét a Azure Portal, az Azure CLI vagy a PowerShell használatával módosíthatja. 

- Az utólagos **elszámolású modell azt** jelenti, hogy az Azure-beli virtuális gép futtatásának másodpercenkénti díja magában foglalja a SQL Server licenc költségeit.
- [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) lehetővé teszi, hogy a saját SQL Server licencét egy SQL Server futtató virtuális géppel használhassa. 
- A vész- **helyreállítási** licenc típusát az Azure-beli [ingyenes Dr-replika](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) használja. 

Azure Hybrid Benefit lehetővé teszi a frissítési garanciával rendelkező SQL Server-licencek használatát az Azure-beli virtuális gépeken. A Azure Hybrid Benefit használata esetén az ügyfelek nem számítanak fel díjat a virtuális gépeken SQL Server licencek esetében. Azonban továbbra is fizetniük kell a mögöttes Felhőbeli számítás (azaz az alaparány), a tárterület és a biztonsági másolatok költségeiért. Emellett a szolgáltatások használatára vonatkozó I/O-díjat is fizetniük kell (ha vannak ilyenek).

A Microsoft-termékekkel kapcsolatos feltételek szerint: "a felhasználóknak jelezniük kell, hogy a Azure SQL Database (felügyelt példány, rugalmas készlet és önálló adatbázis), Azure Data Factory, SQL Server Integration Services vagy SQL Server Virtual Machines Azure Hybrid Benefit a munkaterhelések Azure-ban való konfigurálásakor SQL Server."

Ha az Azure-beli virtuális gépen SQL Server Azure Hybrid Benefit használatát szeretné jelezni, és a megfelelőnek kell lennie, három lehetősége van:

- Hozzon létre egy virtuális gépet egy saját licencű SQL Server rendszerkép használatával az Azure Marketplace-en. Ez a beállítás csak Nagyvállalati Szerződés rendelkező ügyfelek számára érhető el.
- Hozzon létre egy virtuális gépet az Azure Marketplace-en az utólagos elszámolású SQL Server rendszerképpel, és aktiválja a Azure Hybrid Benefit.
- Telepítse SQL Servert az Azure-beli virtuális gépen, manuálisan [regisztrálja az SQL VM erőforrás-szolgáltatót](sql-vm-resource-provider-register.md), és aktiválja a Azure Hybrid Benefit.

A SQL Server licencelése a virtuális gép kiépített állapotában, vagy később bármikor konfigurálható. A licencelési modellek közötti váltás nem eredményez leállást, nem indítja újra a virtuális gépet vagy a SQL Server szolgáltatást, nem vesz fel további költségeket, és azonnal érvénybe lép. Valójában a Azure Hybrid Benefit aktiválása *csökkenti* a költségeket.

## <a name="prerequisites"></a>Előfeltételek

A SQL Server VM licencelési modelljének módosítása az alábbi követelményekkel rendelkezik: 

- Egy [Azure-előfizetés](https://azure.microsoft.com/free/).
- Az [SQL VM erőforrás-szolgáltatónál](sql-vm-resource-provider-register.md)regisztrált [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) .
- A frissítési [garancia](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) a [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)használatára vonatkozó követelmény. 


## <a name="vms-already-registered-with-the-resource-provider"></a>Az erőforrás-szolgáltatónál már regisztrált virtuális gépek 

# <a name="the-azure-portal"></a>[Az Azure Portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

A licencelési modellt közvetlenül a portálról is módosíthatja: 

1. Nyissa meg a [Azure Portal](https://portal.azure.com) , és nyissa meg az [SQL Virtual Machines-erőforrást](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) a SQL Server VMhoz. 
1. Válassza a **Konfigurálás** lehetőséget a **Beállítások**területen. 
1. Válassza a **Azure Hybrid Benefit** lehetőséget, majd jelölje be a jelölőnégyzetet annak megerősítéséhez, hogy rendelkezik-e a frissítési garanciával rendelkező SQL Server licenccel. 
1. Válassza az **alkalmaz** lehetőséget a configure ( **Konfigurálás** ) lap alján. 

![Azure Hybrid Benefit a portálon](./media/licensing-model-azure-hybrid-benefit-ahb-change/ahb-in-portal.png)


# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI használatával módosíthatja a licenc modelljét.  


**Azure Hybrid Benefit**

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

**Fizetés menet közben**: 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

**Vészhelyreállítás (DR)**

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type DR

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type DR
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A PowerShell segítségével módosíthatja a licenc modelljét.

**Azure Hybrid Benefit**

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType AHUB
```

**Használatalapú fizetés**

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType PAYG
```

**Vészhelyreállítás** 

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType DR
```

---

## <a name="vms-not-registered-with-the-resource-provider"></a>Az erőforrás-szolgáltatónál nem regisztrált virtuális gépek

Ha kiépített egy SQL Server VM az utólagos elszámolású Azure Marketplace-rendszerképekből, a SQL Server-licenc típusa utólagos elszámolású lesz. Ha az Azure Marketplace-en keresztül létrehozott egy saját licenctel ellátott rendszerképet használó SQL Server VM-t, a licenc típusa AHUB lesz. Az alapértelmezett (utólagos elszámolású) vagy a saját licencű Azure Marketplace-lemezképek által kiépített SQL Server virtuális gépek automatikusan regisztrálva lesznek az SQL VM erőforrás-szolgáltatóban, így megváltoztathatják a [licenc típusát](#vms-already-registered-with-the-resource-provider).

Csak az Azure-beli virtuális gépeken Azure Hybrid Benefit-on keresztül telepíthető SQL Server. Ezeket a [virtuális gépeket az SQL VM erőforrás-szolgáltatóval kell regisztrálnia](sql-vm-resource-provider-register.md) , ha a SQL Server-licencet Azure Hybrid Benefitként állítja be, hogy jelezze a Azure Hybrid Benefit használatát a Microsoft termék feltételeinek megfelelően.

A SQL Server VM licencének típusa csak akkor módosítható, ha a SQL Server VM regisztrálva van az SQL VM erőforrás Azure Hybrid Benefit-szolgáltatóban.

## <a name="remarks"></a>Megjegyzések

- A Azure Cloud Solution Provider (CSP) ügyfelek a Azure Hybrid Benefit az utólagos elszámolású virtuális gépek üzembe helyezésével, majd a saját licenc használatára való átalakításával, ha aktív frissítési garanciával rendelkeznek.
- Ha eldobja a SQL Server VM-erőforrást, visszatérhet a rendszerkép rögzített licencelési beállítására. 
- A licencelési modell megváltoztatásának lehetősége az SQL VM erőforrás-szolgáltató szolgáltatása. Egy Azure Marketplace-rendszerkép üzembe helyezése a Azure Portal automatikusan regisztrálja az SQL Server VM az erőforrás-szolgáltatóval. A SQL Server önálló telepítését végző ügyfeleknek azonban manuálisan kell [regisztrálniuk SQL Server VM](sql-vm-resource-provider-register.md). 
- SQL Server VM hozzáadása egy rendelkezésre állási csoporthoz újra kell létrehozni a virtuális gépet. Így a rendelkezésre állási csoportba felvett virtuális gépek visszatérhetnek az alapértelmezett utólagos elszámolású licenc típusára. Azure Hybrid Benefit újra engedélyezni kell. 


## <a name="limitations"></a>Korlátozások

A licencelési modell módosítása:
   - Csak frissítési [garanciával](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview)rendelkező ügyfelek számára érhető el.
   - Csak a SQL Server standard és Enterprise kiadásai esetében támogatott. Az Express, a web és a Developer licencelési módosításai nem támogatottak. 
   - Csak a Azure Resource Manager modellen keresztül üzembe helyezett virtuális gépek esetében támogatott. A klasszikus modellen keresztül üzembe helyezett virtuális gépek nem támogatottak. 
   - Csak a nyilvános vagy Azure Government felhők esetében érhető el. 
   - Csak olyan virtuális gépek esetében támogatott, amelyek egyetlen hálózati adapterrel (NIC) rendelkeznek. 


## <a name="known-errors"></a>Ismert hibák

Tekintse át az általánosan ismert hibákat és azok megoldásait. 

**A (z) "" erőforráscsoport alatti "Microsoft. SqlVirtualMachine/SqlVirtualMachines/ \<resource-group> " erőforrás \<resource-group> nem található.**

Ez a hiba akkor fordul elő, ha olyan SQL Server VMon próbálja meg módosítani a licencet, amely még nincs regisztrálva az SQL VM erőforrás-szolgáltatónál:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

Regisztrálnia kell az előfizetését az erőforrás-szolgáltatónál, majd [regisztrálnia kell a SQL Server VM az erőforrás-szolgáltatóval](sql-vm-resource-provider-register.md). 


**A (z) "" virtuális gépnek \<vmname\> több hálózati adaptere van társítva**

Ez a hiba olyan virtuális gépeken fordul elő, amelyek egynél több hálózati adapterrel rendelkeznek. A licencelési modell módosítása előtt távolítsa el az egyik hálózati adaptert. Bár a licenc modell módosítása után a hálózati adaptert visszaállíthatja a virtuális géphez, a Azure Portal, például az automatikus biztonsági mentés és a javítások nem lesznek többé támogatottak. 


## <a name="next-steps"></a>Következő lépések

További információért tekintse át a következő cikkeket: 

* [Windows rendszerű virtuális gépek SQL Server áttekintése](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows rendszerű virtuális gépen SQL Server gyakori kérdések](frequently-asked-questions-faq.md)
* [A Windows rendszerű virtuális gépek SQL Server díjszabási útmutatója](pricing-guidance.md)
* [Windows rendszerű virtuális gépen SQL Server kibocsátási megjegyzései](../../database/doc-changes-updates-release-notes.md)



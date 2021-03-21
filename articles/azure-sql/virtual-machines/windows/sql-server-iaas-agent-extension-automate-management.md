---
title: Mi a SQL Server IaaS-ügynök bővítmény?
description: Ez a cikk azt ismerteti, hogy a SQL Server IaaS-ügynök bővítménye Hogyan automatizálható az Azure-beli virtuális gépeken futó SQL Server felügyeleti feladatainak automatizálására. Ilyenek például az automatikus biztonsági mentés, az automatikus javítás, az Azure Key Vault integráció, a licencelés kezelése, a tárolási konfiguráció és az összes SQL Server VM példány központi kezelése.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: fdff3f6144f7099f3f61cfe57186357e17136e9f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103225489"
---
# <a name="automate-management-with-the-sql-server-iaas-agent-extension"></a>A felügyelet automatizálása a SQL Server IaaS-ügynök bővítménnyel
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


Az SQL Server IaaS-ügynök bővítmény (SqlIaasExtension) az Azure Virtual Machines (VM) SQL Server fut a felügyeleti és adminisztrációs feladatok automatizálásához. 

Ez a cikk áttekintést nyújt a bővítményről. A SQL Server IaaS bővítmény Azure-beli virtuális gépeken való SQL Server való telepítéséhez tekintse meg az [automatikus telepítéssel](sql-agent-extension-automatic-registration-all-vms.md), az [önálló virtuális gépekkel](sql-agent-extension-manually-register-single-vm.md)vagy [a nagy méretű virtuális gépekkel](sql-agent-extension-manually-register-vms-bulk.md)kapcsolatos cikkeket. 

## <a name="overview"></a>Áttekintés

A SQL Server IaaS-ügynök bővítmény lehetővé teszi a Azure Portal való integrációt, és a felügyeleti módtól függően feloldja az Azure-beli virtuális gépeken SQL Server számos funkciójának előnyeit: 

- A **szolgáltatás előnyei**: a bővítmény számos Automation-szolgáltatás előnyeit feloldja, például a portál felügyeletét, a licencek rugalmasságát, az automatikus biztonsági mentést, az automatizált javításokat és egyebeket. A részletekért lásd a jelen cikkben a [szolgáltatásokkal kapcsolatos előnyöket](#feature-benefits) . 

- **Megfelelőség**: a bővítmény egyszerűsített módszert biztosít ahhoz, hogy a Microsoft értesíti a felhasználót arról, hogy a Azure Hybrid Benefit a termék feltételeiben megadott módon van engedélyezve. Ez a folyamat cáfolja az egyes erőforrások licencelési regisztrációs űrlapjainak kezelését.  

- **Ingyenes**: a bővítmény mindhárom kezelhetőségi módban teljesen ingyenes. A bővítményhez nem tartozik további díj, illetve nem módosítható felügyeleti mód. 

- **Egyszerűsített licencek kezelése**: a bővítmény leegyszerűsíti SQL Server a licencek kezelését, és lehetővé teszi SQL Server virtuális Azure Hybrid Benefit gépek gyors azonosítását a [Azure Portal](manage-sql-vm-portal.md), a POWERSHELL vagy az Azure CLI használatával: 

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   $ az sql vm list --query "[?sqlServerLicenseType=='AHUB']"
   ```
   ---


> [!IMPORTANT]
> Az SQL IaaS-ügynök bővítmény olyan adatokat gyűjt az expressz célra, amelyek az ügyfelek számára opcionális előnyöket biztosítanak SQL Server Azure-beli Virtual Machines való használatakor. A Microsoft ezeket az adatszolgáltatásokat nem használja fel a licencelési naplózáshoz az ügyfél előzetes belefoglalása nélkül. További információért tekintse meg a [SQL Server adatvédelmi kiegészítését](/sql/sql-server/sql-server-privacy#non-personal-data) .


## <a name="feature-benefits"></a>Funkciók előnyei 

A SQL Server IaaS-ügynök bővítmény feloldja számos funkció előnyeit a SQL Server VM kezeléséhez. 

A következő táblázat részletezi ezeket az előnyöket: 


| Szolgáltatás | Leírás |
| --- | --- |
| **A portál kezelése** | Feloldja [a felügyeletet a portálon](manage-sql-vm-portal.md), így egyetlen helyen tekintheti meg az összes SQL Server virtuális gépet, és így közvetlenül a portálról engedélyezheti és letilthatja az SQL-specifikus szolgáltatásokat. <br/> Felügyeleti mód: könnyű & teljes|  
| **Automatikus biztonsági mentés** |Automatizálja a biztonsági mentések ütemezését az összes adatbázishoz az alapértelmezett példányhoz, vagy a virtuális gépen a SQL Server [megfelelően telepített](frequently-asked-questions-faq.md#administration) példányát. További információ: [SQL Server automatikus biztonsági mentése az Azure Virtual Machines szolgáltatásban (Resource Manager)](automated-backup-sql-2014.md). <br/> Felügyeleti mód: teljes|
| **Automatikus javítás** |Beállít egy karbantartási időszakot, amely alatt a virtuális gép fontos Windows-és SQL Server biztonsági frissítéseit elvégezheti, így elkerülhető a frissítések végrehajtása a számítási feladatok csúcsidőben. További információ: [SQL Server automatikus javítása az Azure Virtual Machines szolgáltatásban (Resource Manager)](automated-patching.md). <br/> Felügyeleti mód: teljes|
| **Azure Key Vault integráció** |Lehetővé teszi Azure Key Vault automatikus telepítését és konfigurálását a SQL Server VM. További információ: [Azure Key Vault integrációjának konfigurálása az Azure-beli SQL Serverhoz Virtual Machines (Resource Manager)](azure-key-vault-integration-configure.md). <br/> Felügyeleti mód: teljes|
| **Lemez kihasználtságának megtekintése a portálon** | Lehetővé teszi, hogy megtekintse a Azure Portalban található SQL-adatfájlok lemezhasználat-kihasználtságának grafikus ábrázolását.  <br/> Felügyeleti mód: teljes | 
| **Rugalmas licencelés** | Az utólagos elszámolású licencelési modellre való zökkenőmentes áttéréssel, a saját licencek (más néven a Azure Hybrid Benefit) és a back-as-you-go licencelési modelljének [zavartalan átváltásával](licensing-model-azure-hybrid-benefit-ahb-change.md) megtakaríthatja a költségeket. <br/> Felügyeleti mód: könnyű & teljes| 
| **Rugalmas verzió/kiadás** | Ha úgy dönt, hogy módosítja a SQL Server [verzióját](change-sql-server-version.md) vagy [kiadását](change-sql-server-edition.md) , akkor a teljes SQL Server VM újbóli üzembe helyezése nélkül frissítheti a Azure Portalon belüli metaadatokat.  <br/> Felügyeleti mód: könnyű & teljes| 


## <a name="management-modes"></a>Felügyeleti módok

Az SQL IaaS-bővítményt három felügyeleti módban is regisztrálhatja: 

- A **könnyű** mód a bináris fájlokat a virtuális gépre másolja, de nem telepíti az ügynököt, és nem indítja újra a SQL Server szolgáltatást. A könnyű mód csak a SQL Server licencek típusának és kiadásának módosítását támogatja, és korlátozott portál-felügyeletet biztosít. Ezzel a beállítással SQL Server virtuális gépek több példánnyal, vagy a feladatátvevő fürt példányaiban (...) részt vesznek. A könnyű mód az alapértelmezett felügyeleti mód az [automatikus regisztrációs](sql-agent-extension-automatic-registration-all-vms.md) funkció használatakor, vagy ha nincs megadva felügyeleti típus a manuális regisztráció során. A könnyű mód használata esetén nincs hatással a memóriára vagy a CPU-ra, és nincs kapcsolódó díj. Javasoljuk, hogy először regisztrálja a SQL Server VMt, majd egy ütemezett karbantartási időszak alatt frissítsen a teljes módba. 

- A **teljes** mód telepíti az SQL IaaS-ügynököt a virtuális gépre az összes funkció biztosításához, de a SQL Server szolgáltatás és a rendszergazda engedélyeinek újraindítását igényli. A SQL Server VM egyetlen példánnyal való felügyeletére használható. A teljes mód két Windows-szolgáltatást telepít, amelyek minimális hatással vannak a memóriára és a CPU-ra – ezeket a Feladatkezelő segítségével figyelheti. A teljes kezelhetőségi mód használata nem jár együtt. 

- A nem **ügynök** mód a Windows Server 2008 rendszeren telepített SQL Server 2008 és SQL Server 2008 R2 rendszerre van kijelölve. A nem befolyásolja a memóriát vagy a CPU-t a nem ügynök mód használata esetén. A nem ügynöki kezelhetőségi mód használata esetén a SQL Server nem indul újra, és az ügynök nincs telepítve a virtuális gépre. 

A SQL Server IaaS-ügynök aktuális módja a Azure PowerShell használatával tekinthető meg: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```


## <a name="installation"></a>Telepítés

Regisztrálja a SQL Server VMt a SQL Server IaaS-ügynök bővítménnyel, hogy az előfizetésen belül hozza létre az SQL-alapú **virtuális gép** _erőforrását_ , amely a virtuális gép erőforrásának _külön_ erőforrása. Ha törli a SQL Server VM regisztrációját a bővítményből, a rendszer eltávolítja az SQL-alapú **virtuális gép** _erőforrását_ , de nem dobja el a tényleges virtuális gépet.

SQL Server VM Azure Marketplace-rendszerképnek a Azure Portal használatával történő üzembe helyezése automatikusan regisztrálja a SQL Server VM a kiterjesztéssel. Ha azonban úgy dönt, hogy egy Azure-beli virtuális gépen telepíti a SQL Servert, vagy egyéni virtuális MEREVLEMEZről szeretne üzembe helyezni egy Azure-beli virtuális gépet, akkor a szolgáltatás előnyeinek feloldásához regisztrálnia kell a SQL Server VMt az SQL IaaS bővítménnyel. 

A bővítmény egyszerűsített módban való regisztrálása a bináris fájlokat másolja, de nem telepíti az ügynököt a virtuális gépre. Az ügynököt a rendszer a virtuális gépre telepíti, ha a bővítmény teljes felügyeleti módra van frissítve. 

A bővítmény háromféleképpen regisztrálhat: 
- [Az előfizetésben lévő összes jelenlegi és jövőbeli virtuális gép esetében automatikusan](sql-agent-extension-automatic-registration-all-vms.md)
- [Manuálisan egyetlen virtuális géphez](sql-agent-extension-manually-register-single-vm.md)
- [Több nagy méretű virtuális gép esetén manuálisan](sql-agent-extension-manually-register-vms-bulk.md)

### <a name="named-instance-support"></a>Megnevezett példányok támogatása

A SQL Server IaaS-ügynök bővítmény a SQL Server megnevezett példányával működik, ha a virtuális gépen csak az egyetlen SQL Server példány érhető el. A bővítmény nem telepíthető olyan virtuális gépekre, amelyeken több névvel ellátott SQL Server példány található, ha a virtuális gépen nincs alapértelmezett példány. 

SQL Server elnevezett példányának használatához helyezzen üzembe egy Azure-beli virtuális gépet, telepítsen egy nevű SQL Server példányt, majd regisztrálja az [SQL IaaS bővítménnyel](sql-agent-extension-manually-register-single-vm.md).

Azt is megteheti, hogy egy megnevezett példányt használ egy Azure Marketplace SQL Server-lemezképpel, kövesse az alábbi lépéseket: 

   1. SQL Server VM üzembe helyezése az Azure piactéren. 
   1. Szüntesse meg a SQL Server VM [regisztrációját](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) az SQL IaaS-ügynök bővítménnyel. 
   1. Távolítsa el SQL Server teljesen a SQL Server VMon belül.
   1. Telepítse a SQL Servert egy megnevezett példánnyal a SQL Server VMon belül. 
   1. [Regisztrálja a virtuális gépet az SQL IaaS-ügynök bővítménnyel](sql-agent-extension-manually-register-single-vm.md#register-with-extension). 

## <a name="verify-status-of-extension"></a>A bővítmény állapotának ellenőrzése

A bővítmény állapotának vizsgálatához használja a Azure Portal vagy a Azure PowerShell. 

### <a name="azure-portal"></a>Azure Portal

Ellenőrizze, hogy a bővítmény telepítve van-e a Azure Portal. 

Válassza a **minden beállítás** lehetőséget a virtuális gép ablaktáblán, majd válassza a **bővítmények** lehetőséget. Ekkor meg kell jelennie a felsorolt **SqlIaasExtension** -bővítménynek.

![A SQL Server IaaS-ügynök bővítmény állapota a Azure Portal](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)


### <a name="azure-powershell"></a>Azure PowerShell

A **Get-AzVMSqlServerExtension** Azure PowerShell parancsmagot is használhatja:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

Az előző parancs megerősíti, hogy az ügynök telepítve van, és általános állapotinformációkat biztosít. Az automatikus biztonsági mentéssel és javítással kapcsolatos információkat az alábbi parancsokkal érheti el:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```


## <a name="limitations"></a>Korlátozások

Az SQL IaaS-ügynök bővítménye csak a következőket támogatja: 

- SQL Server a Azure Resource Manager üzembe helyezett virtuális gépeket. SQL Server a klasszikus modellen keresztül üzembe helyezett virtuális gépek nem támogatottak. 
- SQL Server a nyilvános vagy Azure Government felhőbe telepített virtuális gépeket. A többi magán-vagy kormányzati felhőbe való üzembe helyezés nem támogatott. 


## <a name="in-region-data-residency"></a>Régión belüli adattárolás
Az Azure SQL-alapú virtuális gép és az SQL IaaS-ügynök bővítménye nem helyezi át vagy tárolja az ügyféladatokat azon a régión kívül, amelyben telepítik.

## <a name="next-steps"></a>Következő lépések

A SQL Server IaaS bővítmény Azure-beli virtuális gépeken való SQL Server való telepítéséhez tekintse meg az [automatikus telepítéssel](sql-agent-extension-automatic-registration-all-vms.md), az [önálló virtuális gépekkel](sql-agent-extension-manually-register-single-vm.md)vagy [a nagy méretű virtuális gépekkel](sql-agent-extension-manually-register-vms-bulk.md)kapcsolatos cikkeket.

További információ az Azure Virtual Machines SQL Server futtatásáról: [mi SQL Server az azure Virtual Machines?](sql-server-on-azure-vm-iaas-what-is-overview.md)

További információ: [Gyakori kérdések](frequently-asked-questions-faq.md). 

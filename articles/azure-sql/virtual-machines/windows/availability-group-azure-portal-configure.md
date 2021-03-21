---
title: Rendelkezésre állási csoport konfigurálása (Azure Portal)
description: A Azure Portal segítségével hozza létre a Windows feladatátvevő fürtöt, a rendelkezésre állási csoport figyelőjét és a belső Load balancert az Azure egyik SQL Server VMján.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/20/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019, devx-track-azurecli
ms.openlocfilehash: 14760b4244d42e57aaed7f7d96f487a66147a554
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359506"
---
# <a name="use-azure-portal-to-configure-an-availability-group-preview-for-sql-server-on-azure-vm"></a>Azure Portal használata az Azure-beli virtuális gépen SQL Server rendelkezésre állási csoport (előzetes verzió) konfigurálásához 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez a cikk azt ismerteti, hogyan használható a [Azure Portal](https://portal.azure.com) az Azure-beli virtuális gépeken futó SQL Server rendelkezésre állási csoportjának konfigurálásához. 

A Azure Portal használatával hozzon létre egy új fürtöt, vagy hozzon létre egy meglévő fürtöt, majd hozza létre a rendelkezésre állási csoportot, a figyelőt és a belső Load balancert. 

Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. 

Habár ez a cikk a Azure Portal használja a rendelkezésre állási csoport környezetének konfigurálására, a [PowerShell vagy az Azure CLI, az](availability-group-az-commandline-configure.md) [Azure Gyorsindítás sablonok](availability-group-quickstart-template-configure.md)vagy [manuálisan](availability-group-manually-configure-tutorial.md) is használható. 


## <a name="prerequisites"></a>Előfeltételek

Az Always On rendelkezésre állási csoport a Azure Portal használatával történő konfigurálásához a következő előfeltételek szükségesek: 

- Egy [Azure-előfizetés](https://azure.microsoft.com/free/).
- Egy tartományvezérlővel rendelkező erőforráscsoport. 
- Egy vagy több [, az Azure-ban SQL Server 2016 (vagy újabb) Enterprise Edition rendszert futtató,](./create-sql-vm-portal.md) tartományhoz csatlakoztatott virtuális gép *ugyanazon* a rendelkezésre állási csoporton vagy *különböző* rendelkezésre állási zónákban, amelyek [teljes kezelhetőségi módban vannak regisztrálva az SQL IaaS-ügynök bővítménnyel](sql-agent-extension-manually-register-single-vm.md) , és ugyanazt a tartományi FIÓKOT használják a SQL Server szolgáltatáshoz az egyes virtuális gépeken.
- Két elérhető (egyetlen entitás sem használja) IP-címek. Az egyik a belső terheléselosztó. A másik a rendelkezésre állási csoporttal megegyező alhálózaton belüli figyelő. Ha meglévő terheléselosztó használatával rendelkezik, csak egy elérhető IP-címet kell használnia a rendelkezésre állási csoport figyelője számára. 

## <a name="permissions"></a>Engedélyek

A rendelkezésre állási csoport a Azure Portal használatával történő konfigurálásához a következő fiókra van szükség: 

- Egy meglévő tartományi felhasználói fiók, amely **számítógép-objektum létrehozása** engedéllyel rendelkezik a tartományban. Például egy tartományi rendszergazdai fióknak jellemzően megfelelő engedélye van (például: account@domain.com ). _Ennek a fióknak a helyi rendszergazda csoportnak is szerepelnie kell az egyes virtuális gépeken a fürt létrehozásához._
- A SQL Server vezérlő tartományi felhasználói fiók. Ennek a fióknak azonosnak kell lennie minden SQL Server VM, amelyet hozzá kíván adni a rendelkezésre állási csoporthoz. 

## <a name="configure-cluster"></a>Fürt konfigurálása

Konfigurálja a fürtöt a Azure Portal használatával. Létrehozhat egy új fürtöt, vagy ha már rendelkezik meglévő fürttel, a portál kezelhetősége érdekében bevezetheti azt az SQL IaaS-ügynök bővítménnyel.


### <a name="create-a-new-cluster"></a>Új fürt létrehozása

Ha már rendelkezik fürttel, ugorja át ezt a szakaszt, és térjen át a [meglévő fürtbe](#onboard-existing-cluster) . 

Ha még nem rendelkezik meglévő fürttel, hozza létre a Azure Portal használatával a következő lépésekkel:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
1. Navigáljon az [SQL Virtual Machines](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) -erőforráshoz. 
1. Válassza a **magas rendelkezésre állás** lehetőséget a **Beállítások** területen. 
1. Válassza az **+ új Windows Server feladatátvevő fürt** lehetőséget a **Windows feladatátvevő fürt konfigurálása** lap megnyitásához.  

   :::image type="content" source="media/availability-group-az-portal-configure/create-new-cluster.png" alt-text="Hozzon létre új fürtöt az + új fürt kiválasztásával a portálon":::

1. Nevezze el a fürtöt, és adjon meg egy Felhőbeli tanúsító használandó Storage-fiókot. Használjon meglévő Storage-fiókot, vagy válassza az **új létrehozása** lehetőséget egy új Storage-fiók létrehozásához. A Storage-fiók nevének 3 – 24 karakter hosszúnak kell lennie, és csak számokat és kisbetűket használjon.

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-cluster-1.png" alt-text="Adja meg a fürt nevét, a Storage-fiókot és a hitelesítő adatokat.":::

1. Bontsa ki a **Windows Server feladatátvételi fürt hitelesítő adatait** a SQL Server szolgáltatásfiók, valamint a fürt operátora és a rendszerindítási fiókok [hitelesítő adatainak](/rest/api/sqlvm/sqlvirtualmachinegroups/createorupdate#wsfcdomainprofile) megadásához, ha azok eltérnek a SQL Server szolgáltatáshoz használt fióktól. 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-cluster-2.png" alt-text="Adja meg az SQL-szolgáltatásfiók, a Fürtfelügyelő-fiók és a fürt rendszerindítási fiókjának hitelesítő adatait.":::

1. Válassza ki azokat a SQL Server virtuális gépeket, amelyeket hozzá szeretne adni a fürthöz. Vegye figyelembe, hogy újraindításra van szükség, és körültekintően járjon el. Csak az SQL IaaS-ügynök bővítményben regisztrált virtuális gépek teljes körű kezelhetőségi módban vannak, és ugyanazon a helyen, tartományban és ugyanazon a virtuális hálózaton találhatók, mint az elsődleges SQL Server VM láthatók. 
1. A fürt létrehozásához kattintson az **alkalmaz** gombra. A központi telepítés állapotát a **tevékenység naplójában** tekintheti meg, amely a felső navigációs sávon harang ikonján keresztül érhető el. 
1. A Microsoft által támogatott feladatátvevő fürtökön át kell adni a fürt érvényesítését. Kapcsolódjon a virtuális géphez a kívánt módszerrel (például RDP protokoll (RDP)), és ellenőrizze, hogy a fürt megfelel-e az érvényesítésnek a folytatás előtt. Ennek elmulasztása esetén a fürt nem támogatott állapotban hagyható. A fürtöt Feladatátvevőfürt-kezelő (FCM) vagy a következő PowerShell-parancs használatával ellenőrizheti:

    ```powershell
    Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
    ```
    


### <a name="onboard-existing-cluster"></a>Meglévő fürt előkészítése

Ha már van egy fürt konfigurálva a SQL Server VM-környezetben, akkor a Azure Portal.

Ehhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
1. Navigáljon az [SQL Virtual Machines](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) -erőforráshoz. 
1. Válassza a **magas rendelkezésre állás** lehetőséget a **Beállítások** területen. 
1. Válassza a **meglévő Windows Server feladatátvételi fürt** beléptetése lehetőséget a **Windows Server feladatátvételi fürt** bevezetési oldalának megnyitásához. 

   :::image type="content" source="media/availability-group-az-portal-configure/onboard-existing-cluster.png" alt-text="Meglévő fürt beléptetése az SQL Virtual Machines erőforrás magas rendelkezésre állási oldaláról":::

1. Tekintse át a fürt beállításait. 
1. Válassza az alkalmaz elemet a fürt **beléptetéséhez** , majd a folytatáshoz válassza az **Igen** lehetőséget.

## <a name="create-availability-group"></a>Rendelkezésre állási csoport létrehozása

A fürt létrehozása vagy előkészítése után hozza létre a rendelkezésre állási csoportot a Azure Portal használatával. Ehhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
1. Navigáljon az [SQL Virtual Machines](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) -erőforráshoz. 
1. Válassza a **magas rendelkezésre állás** lehetőséget a **Beállítások** területen. 
1. Válassza az **+ új always on rendelkezésre állási csoport** lehetőséget a **rendelkezésre állási csoport létrehozása** lap megnyitásához.

   :::image type="content" source="media/availability-group-az-portal-configure/create-new-availability-group.png" alt-text="Válassza az új always on rendelkezésre állási csoport lehetőséget a rendelkezésre állási csoport létrehozása lap megnyitásához.":::

1. Adja meg a rendelkezésre állási csoport nevét. 
1. Válassza a **figyelő beállítása** lehetőséget a **rendelkezésre állási csoport figyelője beállítása** lap megnyitásához. 

   :::image type="content" source="media/availability-group-az-portal-configure/create-availability-group.png" alt-text="Adja meg a rendelkezésre állási csoport nevét, és állítson be egy figyelőt.":::

1. Töltse ki az értékeket, vagy használjon egy meglévő terheléselosztó-t, vagy válassza az **új létrehozása** lehetőséget egy új terheléselosztó létrehozásához.  Kattintson az **alkalmaz** gombra a beállítások mentéséhez és a figyelő és a terheléselosztó létrehozásához. 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-listener.png" alt-text="Töltse ki az űrlap értékeit az új figyelő és a terheléselosztó létrehozásához":::

1. Válassza a **+ replika lehetőséget** a **rendelkezésre állási csoport replikáinak konfigurálása** lap megnyitásához.
1. Válassza ki a rendelkezésre állási csoportba felvenni kívánt virtuális gépeket, és válassza ki az üzleti igényeinek leginkább megfelelő rendelkezésre állási csoport beállításait. A beállítások mentéséhez kattintson az **alkalmaz** gombra. 

   :::image type="content" source="media/availability-group-az-portal-configure/add-replicas.png" alt-text="Válassza ki a rendelkezésre állási csoportba felvenni kívánt virtuális gépeket, és konfigurálja a vállalata számára megfelelő beállításokat":::

1. Ellenőrizze a rendelkezésre állási csoport beállításait, majd válassza az **alkalmaz** lehetőséget a rendelkezésre állási csoport létrehozásához. 

A központi telepítés állapotát a **tevékenység naplójában** tekintheti meg, amely a felső navigációs sávon harang ikonján keresztül érhető el. 

  > [!NOTE]
  > A Azure Portal **magas rendelkezésre állási** lapján a **szinkronizációs állapot** **nem kifogástalan** állapotba kerül, amíg nem ad hozzá adatbázisokat a rendelkezésre állási csoporthoz. 


## <a name="add-database-to-availability-group"></a>Adatbázis hozzáadása a rendelkezésre állási csoporthoz

Az üzembe helyezés befejezése után adja hozzá az adatbázisokat a rendelkezésre állási csoporthoz. Az alábbi lépések a SQL Server Management Studio (SSMS) használatát használják, de a [Transact-SQL vagy a PowerShell](/sql/database-engine/availability-groups/windows/availability-group-add-a-database) is használható. 

Ha SQL Server Management Studio használatával szeretne adatbázisokat felvenni a rendelkezésre állási csoportba, kövesse az alábbi lépéseket:

1. Kapcsolódjon az egyik SQL Server virtuális géphez az előnyben részesített módszer, például a Távoli asztali kapcsolat (RDP) használatával. 
1. Nyissa meg SQL Server Management Studio (SSMS).
1. Kapcsolódjon a SQL Server-példányhoz. 
1. Bontsa ki az **Always on magas rendelkezésre állást** **Object Explorer**.
1. Bontsa ki a **rendelkezésre állási csoportok** csomópontot, kattintson a jobb gombbal a rendelkezésre állási csoportra, és válassza az **adatbázis hozzáadása lehetőséget.**

   :::image type="content" source="media/availability-group-az-portal-configure/add-database.png" alt-text="Kattintson a jobb gombbal a rendelkezésre állási csoportra az Object Explorerben, és válassza az adatbázis hozzáadása elemet.":::

1. Az utasításokat követve válassza ki a rendelkezésre állási csoportba felvenni kívánt adatbázis (oka) t. 
1. Kattintson az **OK** gombra a beállítások mentéséhez és az adatbázis a rendelkezésre állási csoportba való felvételéhez. 
1. Az adatbázis hozzáadása után frissítse **Object Explorer** az adatbázis állapotának megerősítéséhez `synchronized` . 

Az adatbázisok hozzáadása után megtekintheti a rendelkezésre állási csoport állapotát a Azure Portalban: 

:::image type="content" source="media/availability-group-az-portal-configure/healthy-availability-group.png" alt-text="Tekintse meg a rendelkezésre állási csoport állapotát a magas rendelkezésre állás lapon a Azure Portal az adatbázisok szinkronizálása után.":::

## <a name="add-more-vms"></a>További virtuális gépek hozzáadása

Ha további SQL Server virtuális gépeket szeretne hozzáadni a fürthöz, kövesse az alábbi lépéseket: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
1. Navigáljon az [SQL Virtual Machines](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) -erőforráshoz. 
1. Válassza a **magas rendelkezésre állás** lehetőséget a **Beállítások** területen. 
1. Válassza a **Windows Server feladatátvevő fürt konfigurálása** elemet a **Windows Server feladatátvevő fürt konfigurálása** lap megnyitásához. 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-existing-cluster.png" alt-text="Válassza a Windows Server feladatátvevő fürt beállítása lehetőséget a virtuális gépek fürthöz való hozzáadásához.":::

1. Bontsa ki a **Windows Server feladatátvételi fürt hitelesítő adatait** , és adja meg a SQL Server szolgáltatáshoz, a fürthöz és a fürthöz tartozó rendszertöltő fiókokhoz használt fiókokat. 
1. Válassza ki azokat a SQL Server virtuális gépeket, amelyeket hozzá szeretne adni a fürthöz. 
1. Kattintson az **Alkalmaz** gombra. 

A központi telepítés állapotát a **tevékenység naplójában** tekintheti meg, amely a felső navigációs sávon harang ikonján keresztül érhető el. 


## <a name="modify-availability-group"></a>Rendelkezésre állási csoport módosítása 


**További replikákat adhat hozzá** a rendelkezésre állási csoporthoz, **konfigurálhatja a figyelőt**, vagy **törölheti a figyelőt** a Azure Portal **magas rendelkezésre állási** lapjáról a rendelkezésre állási csoport melletti három pontra (...) kattintva: 

:::image type="content" source="media/availability-group-az-portal-configure/configure-listener.png" alt-text="Jelölje ki a rendelkezésre állási csoport melletti három pontot, majd válassza a replika hozzáadása lehetőséget a rendelkezésre állási csoport további replikáinak hozzáadásához.":::

## <a name="remove-cluster"></a>Fürt eltávolítása

Távolítsa el az összes SQL Server virtuális gépet a fürtből annak megsemmisítéséhez, majd távolítsa el a fürt metaadatait az SQL IaaS-ügynök bővítménnyel. Ezt az [Azure CLI](/cli/azure/install-azure-cli) vagy a PowerShell legújabb verziójának használatával teheti meg. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Először távolítsa el az összes SQL Server virtuális gépet a fürtből. Ezzel fizikailag eltávolítja a csomópontokat a fürtből, és megsemmisíti a fürtöt:  

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

az sql vm group delete --name <cluster name> --resource-group <resource group name>
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

Remove-AzSqlVMGroup -ResourceGroupName "<resource group name>" -Name "<cluster name>"
```

---

## <a name="troubleshooting"></a>Hibaelhárítás

Ha problémákba ütközik, ellenőrizheti az üzembe helyezési előzményeket, és áttekintheti a gyakori hibákat és azok megoldásait is. 

### <a name="check-deployment-history"></a>Telepítési előzmények keresése

A fürt és a rendelkezésre állási csoport a portálon keresztüli módosításai az üzembe helyezéseken keresztül valósíthatók meg. Az üzembe helyezési előzmények nagyobb részletességet biztosíthatnak, ha problémák merülnek fel a fürt létrehozásával vagy előkészítésével, illetve a rendelkezésre állási csoport létrehozásával kapcsolatban.

Ha meg szeretné tekinteni a központi telepítés naplóit, és tekintse meg az üzembe helyezési előzményeket, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Navigáljon az erőforráscsoporthoz.
1. A **Beállítások** területen válassza a **központi telepítések** lehetőséget.
1. A központi telepítéssel kapcsolatos további információért válassza ki a kívánt központi telepítést. 


   :::image type="content" source="media/availability-group-az-portal-configure/failed-deployment.png" alt-text="Válassza ki azt a központi telepítést, amelyre kíváncsi." :::

### <a name="common-errors"></a>Gyakori hibák

Tekintse át a következő gyakori hibákat és azok megoldásait. 

#### <a name="the-account-which-is-used-to-start-up-sql-service-is-not-a-domain-account"></a>Az SQL-szolgáltatás indítására használt fiók nem tartományi fiók

Ez azt jelzi, hogy az erőforrás-szolgáltató nem fér hozzá a SQL Server szolgáltatáshoz a megadott hitelesítő adatokkal. Gyakori megoldások:
- Győződjön meg arról, hogy a tartományvezérlő fut.
- Ellenőrizze, hogy a portálon megadott hitelesítő adatok megfelelnek-e a SQL Server szolgáltatásnak. 


## <a name="next-steps"></a>Következő lépések


A rendelkezésre állási csoportokról további információt a következő témakörben talál:

- [A rendelkezésre állási csoportok áttekintése](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
* [Rendelkezésre állási csoport felügyelete](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [A rendelkezésre állási csoportok figyelése &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Rendelkezésre állási csoport Transact-SQL-utasítások ](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Rendelkezésre állási csoportok PowerShell-parancsai](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  

A SQL Server virtuális gépekkel kapcsolatos további információkért lásd: 

* [SQL Server virtuális gépek áttekintése](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [SQL Server virtuális gépek kibocsátási megjegyzései](../../database/doc-changes-updates-release-notes.md)
* [SQL Server virtuális gépekkel kapcsolatos gyakori kérdések](frequently-asked-questions-faq.md)

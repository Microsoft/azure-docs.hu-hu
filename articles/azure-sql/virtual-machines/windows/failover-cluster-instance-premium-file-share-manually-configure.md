---
title: Egy prémium szintű fájlmegosztás létrehozása
description: Hozzon létre egy prémium szintű fájlmegosztást (PFS) egy, az Azure Virtual Machines szolgáltatásban SQL Server-t használó feladatátvevő fürtbeli példány létrehozásához.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.custom: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/18/2020
ms.author: mathoma
ms.openlocfilehash: 86caf39e0d31a41ca454c65311ff2fab52b56f5b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101691161"
---
# <a name="create-an-fci-with-a-premium-file-share-sql-server-on-azure-vms"></a>Egy prémium szintű fájlmegosztás (SQL Server Azure-beli virtuális gépeken) létrehozása
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez a cikk azt ismerteti, hogyan lehet egy [prémium szintű fájlmegosztás](../../../storage/files/storage-how-to-create-file-share.md)használatával létrehozni egy SQL Server Azure Virtual Machines (VM) rendszerű feladatátvevő fürt példányát.

A prémium szintű fájlmegosztás Közvetlen tárolóhelyek (SSD)-alapú, következetesen alacsony késésű fájlmegosztás, amely teljes mértékben támogatott a Windows Server 2012-es vagy újabb verzióiban a feladatátvevő fürt példányain SQL Server 2012-es vagy későbbi verziókban. A prémium szintű fájlmegosztás nagyobb rugalmasságot biztosít, ami lehetővé teszi a fájlmegosztás átméretezését és méretezését leállás nélkül.

További információért lásd: az Azure-beli [virtuális gépekkel](failover-cluster-instance-overview.md) és a [fürttel kapcsolatos ajánlott eljárások](hadr-cluster-best-practices.md)a SQL Server-es és újabb változatának áttekintése. 

## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő utasítások elvégzése előtt a következőket kell tennie:

- Azure-előfizetés.
- Egy olyan fiók, amely rendelkezik objektumok létrehozásához szükséges engedélyekkel mind az Azure-beli virtuális gépeken, mind pedig a Active Directory.
- [Két vagy több előkészített Windows Azure-beli virtuális gép](failover-cluster-instance-prepare-vm.md) egy [rendelkezésre állási csoporton](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) belül vagy különböző [rendelkezésre állási zónákban](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address).
- A fürtözött meghajtóként használandó [prémium fájlmegosztás](../../../storage/files/storage-how-to-create-file-share.md) az adatfájlok adatbázisának tárolási kvótája alapján.
- A [PowerShell](/powershell/azure/install-az-ps)legújabb verziója. 

## <a name="mount-premium-file-share"></a>Prémium fájlmegosztás csatlakoztatása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). és nyissa meg a Storage-fiókját.
1. Nyissa meg a **file** shares elemet a **Fájlszolgáltatások** területen, majd válassza ki az SQL-tárolóhoz használni kívánt prémium fájlmegosztást.
1. Válassza a **Kapcsolódás** lehetőséget a fájlmegosztás kapcsolati karakterláncának megadásához.
1. A legördülő listából válassza ki a használni kívánt meghajtóbetűjelet, majd másolja mindkét kódrészletet a Jegyzettömbbe.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/premium-file-storage-commands.png" alt-text="Mindkét PowerShell-parancs másolása a fájlmegosztás csatlakozási portálján":::

1. Az RDP protokoll (RDP) használatával csatlakozzon a SQL Server VMhoz azzal a fiókkal, amelyet a SQL Server a szolgáltatásfiók használni fog.
1. Nyisson meg egy rendszergazdai PowerShell-parancssori konzolt.
1. Futtassa a korábban mentett parancsokat a portálon végzett munka során.
1. Nyissa meg a megosztást a fájlkezelő vagy a **Futtatás** párbeszédpanel használatával (válassza a Windows + R lehetőséget). Használja a hálózati elérési utat `\\storageaccountname.file.core.windows.net\filesharename` . Például: `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. Hozzon létre legalább egy mappát az újonnan csatlakoztatott fájlmegosztás számára az SQL-adatfájlok a alkalmazásba történő elhelyezéséhez.
1. Ismételje meg ezeket a lépéseket minden olyan SQL Server VM, amely részt vesz a fürtben.

  > [!IMPORTANT]
  > - Érdemes lehet külön fájlmegosztást használni a biztonságimásolat-fájlokhoz, hogy mentse a másodpercenkénti bemeneti/kimeneti műveleteket (IOPS) és a megosztás lemezterület-kapacitását az adatok és a naplófájlok számára. A biztonsági másolati fájlok prémium vagy standard fájlmegosztást is használhatnak.
  > - Ha Windows 2012 R2 vagy korábbi rendszert használ, kövesse ugyanezen lépéseket a tanúsító fájlmegosztásként használni kívánt fájlmegosztás csatlakoztatásához. 
  > 


## <a name="add-windows-cluster-feature"></a>Windows-fürt funkciójának hozzáadása

1. Kapcsolódjon az első virtuális géphez RDP használatával egy olyan tartományi fiókkal, amely tagja a helyi rendszergazdáknak, és amely jogosult objektumok létrehozására Active Directoryban. Ezt a fiókot használja a többi konfigurációhoz.

1. [Adja hozzá a feladatátvételi fürtszolgáltatást az egyes virtuális gépekhez](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms).

   Ha a felhasználói felületen szeretné telepíteni a feladatátvételi fürtszolgáltatást, tegye a következőket mindkét virtuális gépen:
   1. A **Kiszolgálókezelőben** válassza a **kezelés**, majd a **szerepkörök és szolgáltatások hozzáadása** lehetőséget.
   1. A **szerepkörök és szolgáltatások hozzáadása** varázslóban kattintson a **tovább** gombra, amíg ki nem **választja a funkciókat**.
   1. A **szolgáltatások kiválasztása** területen válassza a **feladatátvételi fürtszolgáltatás** lehetőséget. Adja meg az összes szükséges funkciót és a felügyeleti eszközöket. 
   1. Válassza a **szolgáltatások hozzáadása** lehetőséget.
   1. Válassza a **tovább**, majd a **Befejezés** lehetőséget a funkciók telepítéséhez.

   A feladatátvételi fürtszolgáltatás PowerShell használatával történő telepítéséhez futtassa a következő parancsfájlt egy rendszergazdai PowerShell-munkamenetből az egyik virtuális gépen:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

## <a name="validate-cluster"></a>Fürt ellenőrzése

Ellenőrizze a fürtöt a felhasználói felületen vagy a PowerShell használatával.

A fürt a felhasználói felületen való ellenőrzéséhez tegye a következőket az egyik virtuális gépen:

1. A **Kiszolgálókezelő** területen válassza az **eszközök**, majd a **Feladatátvevőfürt-kezelő** lehetőséget.
1. A **Feladatátvevőfürt-kezelő** alatt válassza a **művelet**, majd a **Konfiguráció ellenőrzése** lehetőséget.
1. Kattintson a **Tovább** gombra.
1. A **kiszolgálók vagy fürt kijelölése** területen adja meg mindkét virtuális gép nevét.
1. A **tesztelési beállítások** területen válassza a **csak a kiválasztott tesztek futtatása** lehetőséget. 
1. Kattintson a **Tovább** gombra.
1. A **teszt kiválasztása** területen válassza a minden teszt lehetőséget a **tárolás** és a **közvetlen tárolóhelyek** kivételével, ahogy az itt látható:

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/cluster-validation.png" alt-text="Fürt-ellenőrzési tesztek kiválasztása":::

1. Kattintson a **Tovább** gombra.
1. A **megerősítés** területen válassza a **tovább** lehetőséget.

A **Konfiguráció ellenőrzése** varázsló futtatja az ellenőrző teszteket.

A fürt a PowerShell használatával történő ellenőrzéséhez futtassa a következő parancsfájlt egy rendszergazdai PowerShell-munkamenetből az egyik virtuális gépen:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

A fürt érvényesítése után hozza létre a feladatátvevő fürtöt.


## <a name="create-failover-cluster"></a>Feladatátvevő fürt létrehozása

A feladatátvevő fürt létrehozásához a következők szükségesek:

- A fürtcsomópontok lesznek a virtuális gépek nevei.
- A feladatátvevő fürt neve.
- A feladatátvevő fürt IP-címe. Olyan IP-címet is használhat, amely nem szerepel ugyanazon az Azure-beli virtuális hálózaton és az alhálózaton, mint a fürtcsomópontok.


# <a name="windows-server-2012---2016"></a>[Windows Server 2012 – 2016](#tab/windows2012)

A következő PowerShell-parancsfájl egy feladatátvevő fürtöt hoz létre a Windows Server 2012 rendszerhez a Windows Server 2016 használatával. Frissítse a parancsfájlt a csomópontok nevével (a virtuális gépek neveivel) és egy elérhető IP-címmel az Azure virtuális hálózatból.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows2019)

A következő PowerShell-szkript létrehoz egy feladatátvevő fürtöt a Windows Server 2019-hez.  Frissítse a parancsfájlt a csomópontok nevével (a virtuális gépek neveivel) és egy elérhető IP-címmel az Azure virtuális hálózatból.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```

További információ: [feladatátvevő fürt: fürt hálózati objektuma](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97).

---


## <a name="configure-quorum"></a>Kvórum konfigurálása

Konfigurálja az üzleti igényeknek leginkább megfelelő kvórum megoldást. Beállíthat egy [tanúsító lemezt](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum), egy [Felhőbeli tanúsító](/windows-server/failover-clustering/deploy-cloud-witness)vagy egy [tanúsító fájlmegosztást](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum). További információ: [kvórum SQL Server virtuális gépekkel](hadr-cluster-best-practices.md#quorum). 


## <a name="test-cluster-failover"></a>Fürt feladatátvételének tesztelése

Tesztelje a fürt feladatátvételét. A **Feladatátvevőfürt-kezelőban** kattintson a jobb gombbal a fürtre, válassza a **További műveletek**  >  csomópontot az **alapszintű fürt erőforrásainak**  >  **kiválasztásához**, majd válassza ki a fürt másik csomópontját. Helyezze át az alapszintű fürt erőforrását a fürt minden csomópontjára, majd helyezze vissza az elsődleges csomópontra. Ha sikeresen át tudja helyezni a fürtöt az egyes csomópontokra, készen áll a SQL Server telepítésére.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Fürt feladatátvételének tesztelése az alapvető erőforrás más csomópontokra való áthelyezésével":::


## <a name="create-sql-server-fci"></a>Létrehozás SQL Server

Miután konfigurálta a feladatátvevő fürtöt, létrehozhatja a SQL Server-t.

1. Kapcsolódjon az első virtuális géphez RDP használatával.

1. **Feladatátvevőfürt-kezelő** ellenőrizze, hogy az összes alapvető fürterőforrás az első virtuális gépen van-e. Ha szükséges, helyezze át az összes erőforrást erre a virtuális gépre.

1. Keresse meg a telepítési adathordozót. Ha a virtuális gép az egyik Azure Marketplace-lemezképet használja, az adathordozó a következő helyen található: `C:\SQLServer_<version number>_Full` . 

1. Válassza a **beállítás** lehetőséget.

1. A **SQL Server telepítési központban** válassza a **telepítés** lehetőséget.

1. Válassza az **új SQL Server feladatátvevő fürt telepítése** lehetőséget, majd kövesse a varázsló utasításait a SQL Server-verzió telepítéséhez.

   Az adatkönyvtárak a prémium fájlmegosztás esetében szükségesek. Adja meg a megosztás teljes elérési útját a következő formátumban: `\\storageaccountname.file.core.windows.net\filesharename\foldername` . Megjelenik egy figyelmeztetés, amely arról tájékoztat, hogy a fájlkiszolgáló adatkönyvtárként van megadva. Ez a figyelmeztetés várható. Győződjön meg arról, hogy a fájlmegosztás megtartásakor a virtuális gép RDP-kapcsolaton keresztüli elérésére használt felhasználói fiók ugyanaz a fiók, amelyet a SQL Server szolgáltatás a lehetséges hibák elkerülése érdekében használ.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/use-file-share-as-data-directories.png" alt-text="Fájlmegosztás használata SQL-adatkönyvtárakként":::

1. A varázsló lépéseinek elvégzése után a telepítő telepíti az első csomóponton egy SQL Server-t.

1. Miután a telepítő telepíti a (z)-t az első csomóponton, csatlakozzon a második csomóponthoz RDP használatával.

1. Nyissa meg a **SQL Server telepítési központot**, majd válassza a **telepítés** lehetőséget.

1. Válassza **a csomópont hozzáadása SQL Server feladatátvevő fürthöz** lehetőséget. A varázsló utasításait követve telepítse a SQL Servert, és adja hozzá a kiszolgálót a modulhoz.

   >[!NOTE]
   >Ha SQL Server használatával Azure Marketplace-katalógust használt, SQL Server eszközöket tartalmazott a rendszerképben. Ha nem használja ezeket a képeket, telepítse külön a SQL Server-eszközöket. További információ: [Download SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).

1. Ismételje meg ezeket a lépéseket minden olyan csomóponton, amelyet hozzá szeretne adni a SQL Server feladatátvevő fürt példányához. 

## <a name="register-with-the-sql-vm-rp"></a>Regisztrálás az SQL VM RP-vel

A SQL Server VM a portálról való kezeléséhez regisztrálja az SQL IaaS-ügynök bővítménnyel (RP) az [egyszerűsített felügyeleti módban](sql-agent-extension-manually-register-single-vm.md#lightweight-management-mode), jelenleg az egyetlen olyan mód, amelyet az Azure-beli virtuális gépeken a (z) és a SQL Server is támogat. 

SQL Server VM regisztrálása könnyűsúlyú módban a PowerShell-lel (-LicenseType lehet `PAYG` vagy `AHUB` ):

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType ???? -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Kapcsolat konfigurálása 

Ha a forgalmat az aktuális elsődleges csomópontnak megfelelően szeretné irányítani, konfigurálja a környezetének megfelelő kapcsolódási lehetőséget. Létrehozhat egy [Azure Load balancert](failover-cluster-instance-vnn-azure-load-balancer-configure.md) , vagy ha a SQL Server 2019 CU2 (vagy újabb) és a Windows Server 2016 (vagy újabb) rendszert használja, Ehelyett használhatja a [Distributed Network name](failover-cluster-instance-distributed-network-name-dnn-configure.md) szolgáltatást. 

## <a name="limitations"></a>Korlátozások

- A Microsoft Distributed Transaction Coordinator (MSDTC) nem támogatott a Windows Server 2016-es és korábbi verzióiban. 
- A FileStream nem támogatott egy prémium szintű fájlmegosztást használó feladatátvevő fürt esetén. A FileStream használatához [közvetlen tárolóhelyek](failover-cluster-instance-storage-spaces-direct-manually-configure.md) vagy [Azure Shared Disks](failover-cluster-instance-azure-shared-disks-manually-configure.md) használatával helyezze üzembe a fürtöt.
- Csak az SQL IaaS-ügynök bővítménnyel való regisztráció [egyszerűsített felügyeleti módban](sql-server-iaas-agent-extension-automate-management.md#management-modes) támogatott. 

## <a name="next-steps"></a>Következő lépések

Ha még nem tette meg, állítsa be a kapcsolatot a [virtuális hálózat nevével és az Azure Load balancerrel](failover-cluster-instance-vnn-azure-load-balancer-configure.md) vagy az [elosztott hálózat nevével (DNN)](failover-cluster-instance-distributed-network-name-dnn-configure.md). 


Ha a prémium fájlmegosztás nem az Ön számára megfelelő, az Azure-beli [megosztott lemezekkel](failover-cluster-instance-azure-shared-disks-manually-configure.md) vagy [közvetlen tárolóhelyekekkel](failover-cluster-instance-storage-spaces-direct-manually-configure.md) hozza létre a-t. 

További tudnivalókat az Azure-beli [virtuális gépekkel](failover-cluster-instance-overview.md) és a [fürt konfigurálásával kapcsolatos ajánlott eljárásokkal](hadr-cluster-best-practices.md)foglalkozó, a SQL Server az Azure-ban című témakörben 

További információkért lásd: 
- [Windows-fürtök technológiái](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server feladatátvevő fürt példányai](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
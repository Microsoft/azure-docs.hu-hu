---
title: Hozzon létre egy és Közvetlen tárolóhelyek
description: A Közvetlen tárolóhelyek használatával hozzon létre egy feladatátvételi fürtszolgáltatást (és az Azure Virtual Machines szolgáltatásban SQL Server).
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
ms.openlocfilehash: aa19cf6b59b1efa4b14501fbf64e319da3e4c0b3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048641"
---
# <a name="create-an-fci-with-storage-spaces-direct-sql-server-on-azure-vms"></a>Közvetlen tárolóhelyek (SQL Server Azure-beli virtuális gépeken) létrehozása
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez a cikk azt ismerteti, hogyan hozható létre a feladatátvevő fürt példánya (a [közvetlen tárolóhelyek](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) az Azure Virtual Machines (vm) SQL Server használatával. A Közvetlen tárolóhelyek szoftveres virtuálisgép-hálózatként (VSAN) működik, amely a Windows-fürt csomópontjai (Azure-beli virtuális gépek) között szinkronizálja a tárolót (adatlemezeket). 

További információért lásd: az Azure-beli [virtuális gépekkel](failover-cluster-instance-overview.md) és a [fürttel kapcsolatos ajánlott eljárások](hadr-cluster-best-practices.md)a SQL Server-es és újabb változatának áttekintése. 


## <a name="overview"></a>Áttekintés 

[Közvetlen tárolóhelyek (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) két típusú architektúrát támogat: konvergált és hiperkonvergens. A hiperkonvergens-infrastruktúra a fürtözött alkalmazást futtató kiszolgálókon helyezi el a tárolót, így a tárterület minden SQL Server-os csomóponton fut. 

Az alábbi ábrán a teljes megoldás látható, amely az Azure-beli virtuális gépeken futó SQL Server hiperkonvergens-Közvetlen tárolóhelyek használja: 

![A teljes megoldás ábrája, a hiperkonvergens Közvetlen tárolóhelyek használatával](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/00-sql-fci-s2d-complete-solution.png)

Az előző ábrán a következő erőforrások láthatók ugyanabban az erőforráscsoportban:

- Két virtuális gép egy Windows Server rendszerű feladatátvevő fürtben. Ha egy virtuális gép feladatátvevő fürtben található, akkor azt is nevezik *fürtcsomópont* vagy *csomópontnak*.
- Mindegyik virtuális gép két vagy több adatlemezzel rendelkezik.
- Közvetlen tárolóhelyek szinkronizálja az adatokat az adatlemezeken, és a szinkronizált tárolót tárolóként jeleníti meg.
- A tárolási készlet egy Fürt megosztott kötete (CSV) a feladatátvevő fürthöz.
- Az SQL Server-os szerepköralapú fürt szerepkör a CSV-t használja az adatmeghajtókhoz.
- Egy Azure Load Balancer, amely a SQL Server-alapú IP-címet fogja tárolni.
- Az Azure-beli rendelkezésre állási csoport minden erőforrást tárol.

   > [!NOTE]
   > Ezt a teljes megoldást létrehozhatja az Azure-ban egy sablonból. Egy sablon például elérhető a GitHub [Azure Gyorsindítás sablonok](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad) lapján. Ez a példa nincs megtervezve vagy tesztelve az adott számítási feladatokhoz. A sablon futtatásával létrehozhat egy SQL Server a tartományhoz csatlakoztatott Közvetlen tárolóhelyek-tárolóval. Kiértékelheti a sablont, és módosíthatja azt a célra.


## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő utasítások elvégzése előtt a következőket kell tennie:

- Azure-előfizetés. Az első lépések [ingyenesek](https://azure.microsoft.com/free/). 
- [Két vagy több előkészített Windows Azure-beli virtuális gép](failover-cluster-instance-prepare-vm.md) egy [rendelkezésre állási csoporton](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set)belül.
- Egy olyan fiók, amely rendelkezik objektumok létrehozásához szükséges engedélyekkel mind az Azure-beli virtuális gépeken, mind pedig a Active Directory.
- A [PowerShell](/powershell/azure/install-az-ps)legújabb verziója. 


## <a name="add-the-windows-cluster-feature"></a>A Windows-fürt funkciójának hozzáadása

1. Kapcsolódjon az első virtuális géphez RDP protokoll (RDP) használatával olyan tartományi fiókkal, amely tagja a helyi rendszergazdáknak, és amely jogosult objektumok létrehozására a Active Directory. Ezt a fiókot használja a többi konfigurációhoz.

1. Adja hozzá a feladatátvételi fürtszolgáltatást az egyes virtuális gépekhez.

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

A következő lépésekkel kapcsolatos további információkért tekintse meg a Hiperkonvergens megoldás "3. lépés: Közvetlen tárolóhelyek konfigurálása" című szakaszának utasításait a [Windows Server 2016 közvetlen tárolóhelyek használatával](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-3-configure-storage-spaces-direct).


## <a name="validate-the-cluster"></a>A fürt ellenőrzése

Ellenőrizze a fürtöt a felhasználói felületen vagy a PowerShell használatával.

A fürt a felhasználói felületen való ellenőrzéséhez tegye a következőket az egyik virtuális gépen:

1. A **Kiszolgálókezelő** területen válassza az **eszközök**, majd a **Feladatátvevőfürt-kezelő** lehetőséget.
1. A **Feladatátvevőfürt-kezelő** alatt válassza a **művelet**, majd a **Konfiguráció ellenőrzése** lehetőséget.
1. Kattintson a **Tovább** gombra.
1. A **kiszolgálók vagy fürt kijelölése** területen adja meg mindkét virtuális gép nevét.
1. A **tesztelési beállítások** területen válassza a **csak a kiválasztott tesztek futtatása** lehetőséget. 
1. Kattintson a **Tovább** gombra.
1. A **teszt kiválasztása** területen válassza a minden teszt lehetőséget a **tárolás** kivételével, ahogy az itt látható:

   ![Fürt-ellenőrzési tesztek kiválasztása](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/10-validate-cluster-test.png)

1. Kattintson a **Tovább** gombra.
1. A **megerősítés** területen válassza a **tovább** lehetőséget.

    A **Konfiguráció ellenőrzése** varázsló futtatja az ellenőrző teszteket.

A fürt a PowerShell használatával történő ellenőrzéséhez futtassa a következő parancsfájlt egy rendszergazdai PowerShell-munkamenetből az egyik virtuális gépen:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
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

## <a name="add-storage"></a>Tároló hozzáadása

A Közvetlen tárolóhelyek lemezének üresnek kell lennie. Nem tartalmazhatnak partíciókat vagy egyéb adatforrásokat. A lemezek tisztításához kövesse az [közvetlen tárolóhelyek üzembe helyezése](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-31-clean-drives)című témakör utasításait.

1. [Közvetlen tárolóhelyek engedélyezése](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   A következő PowerShell-parancsfájl lehetővé teszi a Közvetlen tárolóhelyek:  

   ```powershell
   Enable-ClusterS2D
   ```

   **Feladatátvevőfürt-kezelő** a Storage-készletet láthatja.

1. [Hozzon létre egy kötetet](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-36-create-volumes).

   A Közvetlen tárolóhelyek automatikusan létrehoz egy tárolási készletet, amikor engedélyezi azt. Most már készen áll egy kötet létrehozására. A PowerShell-parancsmag `New-Volume` automatizálja a kötet létrehozásának folyamatát. Ez a folyamat formázást tartalmaz, hozzáadja a kötetet a fürthöz, és létrehoz egy CSV-t. Ez a példa egy 800 gigabájt (GB) CSV-fájlt hoz létre:

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Az előző parancs futtatása után egy 800 GB-os kötet van csatlakoztatva fürt erőforrásként. A kötet a következő helyen található: `C:\ClusterStorage\Volume1\` .

   Ez a képernyőfelvétel a Közvetlen tárolóhelyekt tartalmazó CSV-t jeleníti meg:

   ![Képernyőkép egy Fürt megosztott köteteról Közvetlen tárolóhelyek](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/15-cluster-shared-volume.png)



## <a name="test-cluster-failover"></a>Fürt feladatátvételének tesztelése

Tesztelje a fürt feladatátvételét. A **Feladatátvevőfürt-kezelőban** kattintson a jobb gombbal a fürtre, válassza a **További műveletek**  >  csomópontot az **alapszintű fürt erőforrásainak**  >  **kiválasztásához**, majd válassza ki a fürt másik csomópontját. Helyezze át az alapszintű fürt erőforrását a fürt minden csomópontjára, majd helyezze vissza az elsődleges csomópontra. Ha sikeresen át tudja helyezni a fürtöt az egyes csomópontokra, készen áll a SQL Server telepítésére.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Fürt feladatátvételének tesztelése az alapvető erőforrás más csomópontokra való áthelyezésével":::

## <a name="create-sql-server-fci"></a>Létrehozás SQL Server

Miután konfigurálta a feladatátvevő fürtöt és a fürt összes összetevőjét, beleértve a Storage-t is, létrehozhatja a SQL Server-t.

1. Kapcsolódjon az első virtuális géphez RDP használatával.

1. **Feladatátvevőfürt-kezelő** ellenőrizze, hogy az összes alapvető fürterőforrás az első virtuális gépen van-e. Ha szükséges, helyezze át az összes erőforrást az adott virtuális gépre.

1. Keresse meg a telepítési adathordozót. Ha a virtuális gép az egyik Azure Marketplace-lemezképet használja, az adathordozó a következő helyen található: `C:\SQLServer_<version number>_Full` . Válassza a **beállítás** lehetőséget.

1. **SQL Server telepítési központban** válassza a **telepítés** lehetőséget.

1. Válassza az **új SQL Server feladatátvevő fürt telepítése** lehetőséget. A varázsló utasításait követve telepítse a SQL Server-t.

   A (z)-es adatkönyvtárak fürtözött tárolón kell lenniük. A Közvetlen tárolóhelyek esetében ez nem egy megosztott lemez, hanem egy, az egyes kiszolgálókon lévő kötetre mutató csatlakoztatási pont. Közvetlen tárolóhelyek szinkronizálja a kötetet mindkét csomópont között. A kötetet a rendszer CSV-fájlként mutatja be a fürt számára. Használja a CSV csatlakoztatási pontot az adatkönyvtárakhoz.

   ![Adatkönyvtárak](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/20-data-dicrectories.png)

1. Miután elvégezte a varázsló utasításait, a telepítő telepíti az első csomóponton egy SQL Server-t.

1. Miután a telepítő telepíti a (z)-t az első csomóponton, csatlakozzon a második csomóponthoz RDP használatával.

1. Nyissa meg a **SQL Server telepítési központot**. Válassza a **telepítés** lehetőséget.

1. Válassza **a csomópont hozzáadása SQL Server feladatátvevő fürthöz** lehetőséget. A varázsló utasításait követve telepítse a SQL Servert, és adja hozzá a kiszolgálót a modulhoz.

   >[!NOTE]
   >Ha SQL Servert tartalmazó Azure Marketplace Gallery-rendszerképet használt, SQL Server eszközöket tartalmazott a rendszerképben. Ha nem használja ezeket a képeket, telepítse külön a SQL Server-eszközöket. További információ: [Download SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).
   >


## <a name="register-with-the-sql-vm-rp"></a>Regisztrálás az SQL VM RP-vel

A SQL Server VM a portálról való kezeléséhez regisztrálja az SQL IaaS-ügynök bővítménnyel (RP) az [egyszerűsített felügyeleti módban](sql-agent-extension-manually-register-single-vm.md#lightweight-management-mode), jelenleg az egyetlen olyan mód, amelyet az Azure-beli virtuális gépeken a (z) és a SQL Server is támogat. 


SQL Server VM regisztrálása könnyűsúlyú módban a PowerShell használatával:  

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType PAYG -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Kapcsolat konfigurálása 

Ha a forgalmat az aktuális elsődleges csomópontnak megfelelően szeretné irányítani, konfigurálja a környezetének megfelelő kapcsolódási lehetőséget. Létrehozhat egy [Azure Load balancert](failover-cluster-instance-vnn-azure-load-balancer-configure.md) , vagy ha a SQL Server 2019 CU2 (vagy újabb) és a Windows Server 2016 (vagy újabb) rendszert használja, Ehelyett használhatja a [Distributed Network name](failover-cluster-instance-distributed-network-name-dnn-configure.md) szolgáltatást. 

A fürt csatlakozási lehetőségeivel kapcsolatos további információkért lásd: [HADR-kapcsolatok továbbítása SQL Server Azure-beli virtuális gépeken](hadr-cluster-best-practices.md#connectivity). 

## <a name="limitations"></a>Korlátozások

- Az Azure Virtual Machines támogatja a Microsoft Distributed Transaction Coordinator (MSDTC) szolgáltatást a Windows Server 2019-on a CSV és a [standard Load Balancer](../../../load-balancer/load-balancer-overview.md)szolgáltatással.
- Az NTFS fájlrendszerrel formázott lemezként csatolt lemezek Közvetlen tárolóhelyek csak akkor használhatók, ha nincs bejelölve a lemezre vonatkozó jogosultsági lehetőség, vagy nincs törölve, amikor a tárolót hozzáadja a fürthöz. 
- Csak az SQL IaaS-ügynök bővítménnyel való regisztráció [egyszerűsített felügyeleti módban](sql-server-iaas-agent-extension-automate-management.md#management-modes) támogatott.

## <a name="next-steps"></a>Következő lépések

Ha még nem tette meg, állítsa be a kapcsolatot a [virtuális hálózat nevével és az Azure Load balancerrel](failover-cluster-instance-vnn-azure-load-balancer-configure.md) vagy az [elosztott hálózat nevével (DNN)](failover-cluster-instance-distributed-network-name-dnn-configure.md). 

Ha Közvetlen tárolóhelyek nem az Ön számára megfelelő, az Ön számára megfelelő, az Azure-beli [megosztott lemezekkel](failover-cluster-instance-azure-shared-disks-manually-configure.md) vagy [prémium fájlmegosztás](failover-cluster-instance-premium-file-share-manually-configure.md) használatával hozza létre a-t. 

További tudnivalókat az Azure-beli [virtuális gépekkel](failover-cluster-instance-overview.md) és a [fürt konfigurálásával kapcsolatos ajánlott eljárásokkal](hadr-cluster-best-practices.md)foglalkozó, a SQL Server az Azure-ban című témakörben 

További információkért lásd: 
- [Windows-fürtök technológiái](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server feladatátvevő fürt példányai](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
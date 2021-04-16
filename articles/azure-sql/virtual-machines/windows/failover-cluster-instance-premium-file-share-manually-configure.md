---
title: FCI létrehozása prémium fájlmegosztással
description: Prémium fájlmegosztás (PFS) használatával hozzon létre egy feladatátvevőfürt-példányt (FCI) SQL Server Azure-beli virtuális gépeken.
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
ms.openlocfilehash: ddd25c605ef159bddfb8a9c7cb4d02ac7094c511
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482194"
---
# <a name="create-an-fci-with-a-premium-file-share-sql-server-on-azure-vms"></a>FCI létrehozása prémium fájlmegosztással (SQL Server Azure-beli virtuális gépeken)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez a cikk azt ismerteti, hogyan hozható létre egy feladatátvevőfürt-példány (FCI) SQL Server azure Virtual Machines (virtuális gépek) szolgáltatásban prémium [fájlmegosztás használatával.](../../../storage/files/storage-how-to-create-file-share.md)

A prémium fájlmegosztások Közvetlen tárolóhelyek (SSD) alapú, konzisztensen alacsony késésű fájlmegosztások, amelyek teljes mértékben támogatottak a 2012-es vagy újabb windows server 2012 vagy újabb rendszerű feladatátvevőfürt SQL Server-példányokkal való használathoz. A prémium fájlmegosztások nagyobb rugalmasságot adnak, lehetővé téve a fájlmegosztások átméretezését és skálázát állásidő nélkül.

További tudnivalókért tekintse meg az [FCI](failover-cluster-instance-overview.md) áttekintését SQL Server Azure-beli virtuális gépekre és a fürtökre [vonatkozó ajánlott eljárásokról.](hadr-cluster-best-practices.md) 

## <a name="prerequisites"></a>Előfeltételek

A cikkben található utasítások befejezése előtt a következővel kell rendelkezik:

- Azure-előfizetés.
- Egy fiók, amely engedéllyel rendelkezik objektumok létrehozásához azure-beli virtuális gépeken és Active Directory.
- [Két vagy több előkészített Windows Azure-beli](failover-cluster-instance-prepare-vm.md) virtuális gép rendelkezésre [állási csoportban](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) vagy különböző [rendelkezésre állási zónákban.](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address)
- Egy [prémium fájlmegosztás,](../../../storage/files/storage-how-to-create-file-share.md) amely fürtözött meghajtóként használható az adatbázis adatfájlokra vonatkozó tárolási kvótája alapján.
- A PowerShell legújabb [verziója.](/powershell/azure/install-az-ps) 

## <a name="mount-premium-file-share"></a>Prémium fájlmegosztás csatlakoztatása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). majd a tárfiókhoz.
1. A **Fájlszolgáltatás alatt** **válassza** a Fájlmegosztások lehetőséget, majd válassza ki az SQL-tárolóhoz használni kívánt prémium fájlmegosztást.
1. Válassza **a Csatlakozás** lehetőséget a fájlmegosztás kapcsolati sztringjének megnyitásához.
1. A legördülő listában válassza ki a használni kívánt meghajtóbetűjelet, majd másolja mindkét kódblokkot a Jegyzettömbbe.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/premium-file-storage-commands.png" alt-text="Mindkét PowerShell-parancs másolása a fájlmegosztás-kapcsolódási portálról":::

1. A RDP protokoll (RDP) használatával csatlakozzon a SQL Server VM ahhoz a fiókhoz, SQL Server FCI használni fogja a szolgáltatásfiókhoz.
1. Nyisson meg egy felügyeleti PowerShell-parancskonzolt.
1. Futtassa a korábban, a portálon való munka közben mentett parancsokat.
1. A megosztás megnyitásához használja a Fájlkezelő vagy a **Futtatás** párbeszédpanelt (válassza a Windows + R lehetőséget). Használja a hálózati elérési `\\storageaccountname.file.core.windows.net\filesharename` utat. Például: `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. Hozzon létre legalább egy mappát az újonnan csatlakoztatott fájlmegosztáson, amelybe az SQL-adatfájlokat helyezze.
1. Ismételje meg ezeket a lépéseket minden olyan SQL Server VM, amely részt fog venni a fürtben.

  > [!IMPORTANT]
  > - Érdemes lehet egy külön fájlmegosztást használni a biztonsági mentési fájlokhoz a másodpercenkénti bemeneti/kimeneti műveletek (IOPS) és a megosztás lemezterület-kapacitásának az adat- és naplófájlok számára való mentéséhez. A biztonsági mentési fájlokhoz prémium vagy standard szintű fájlmegosztást is használhat.
  > - Ha Windows 2012 R2 vagy korábbi rendszert használ, ugyanezekkel a lépésekkel csatlakoztatja a tanúsító fájlmegosztásként használni kívánt fájlmegosztást. 
  > 


## <a name="add-windows-cluster-feature"></a>Windows-fürtszolgáltatás hozzáadása

1. Csatlakozzon az első virtuális géphez RDP-n keresztül egy olyan tartományi fiókkal, amely a helyi rendszergazdák tagja, és rendelkezik engedéllyel objektumok létrehozásához a Active Directory. A konfiguráció további részében ezt a fiókot használja.

1. [Feladatátvételi fürtszolgáltatás hozzáadása az egyes virtuális gépekhez.](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms)

   A feladatátvételi fürtszolgáltatás felhasználói felületről való telepítéséhez tegye a következőket mindkét virtuális gépen:
   1. A **Kiszolgálókezelőben** válassza a **Kezelés,** majd a **Szerepkörök és szolgáltatások hozzáadása lehetőséget.**
   1. A Szerepkörök **és szolgáltatások hozzáadása varázslóban** válassza a **Tovább** lehetőséget, amíg el nem jut a Szolgáltatások **kiválasztása gombra.**
   1. A **Szolgáltatások kiválasztása mezőben** válassza a **Feladatátvételi fürtszolgáltatás lehetőséget.** Foglalja bele az összes szükséges funkciót és a felügyeleti eszközöket. 
   1. Válassza **a Szolgáltatások hozzáadása lehetőséget.**
   1. Válassza **a Tovább,** majd a **Befejezés lehetőséget** a funkciók telepítéséhez.

   A feladatátvételi fürtszolgáltatás PowerShell használatával való telepítéséhez futtassa a következő szkriptet egy rendszergazdai PowerShell-munkamenetből az egyik virtuális gépen:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

## <a name="validate-cluster"></a>Fürt ellenőrzése

Ellenőrizze a fürtöt a felhasználói felületen vagy a PowerShell használatával.

A fürt felhasználói felülettel való érvényesítéséhez tegye a következőket az egyik virtuális gépen:

1. A **Kiszolgálókezelő alatt** válassza az **Eszközök** lehetőséget, majd a **Feladatátvevőfürt-kezelő.**
1. A Feladatátvevőfürt-kezelő válassza **a** **Művelet lehetőséget,** majd a Konfiguráció ellenőrzése **lehetőséget.**
1. Kattintson a **Tovább** gombra.
1. A **Kiszolgálók vagy fürt kiválasztása alatt** adja meg mindkét virtuális gép nevét.
1. A **Tesztelési beállítások alatt** válassza a Csak tesztek futtatása **lehetőséget, majd a gombra.** 
1. Kattintson a **Tovább** gombra.
1. A **Tesztelési kiválasztás alatt** válassza ki az összes tesztet a **Storage** és a **Közvetlen tárolóhelyek** kivételével az itt látható módon:

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/cluster-validation.png" alt-text="Fürtellenőrzési tesztek kiválasztása":::

1. Kattintson a **Tovább** gombra.
1. A **Jóváhagyás alatt** válassza a Tovább **lehetőséget.**

A **Konfiguráció ellenőrzése varázsló** futtatja az érvényesítési teszteket.

A fürt PowerShell használatával való érvényesítéséhez futtassa a következő szkriptet egy rendszergazdai PowerShell-munkamenetből az egyik virtuális gépen:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

A fürt ellenőrzése után hozza létre a feladatátvevő fürtöt.


## <a name="create-failover-cluster"></a>Feladatátvevő fürt létrehozása

A feladatátvevő fürt létrehozásához a következő szükséges:

- Azoknak a virtuális gépeknek a neve, amelyek a fürtcsomópontok lesznek.
- A feladatátvevő fürt neve.
- A feladatátvevő fürt IP-címe. Olyan IP-címet is használhat, amely nem ugyanazon az Azure-beli virtuális hálózaton és alhálózaton van használatban, mint a fürtcsomópontok.


# <a name="windows-server-2012---2016"></a>[Windows Server 2012 – 2016](#tab/windows2012)

Az alábbi PowerShell-szkript egy feladatátvevő fürtöt hoz létre a Windows Server 2012 és a Windows Server 2016 között. Frissítse a szkriptet a csomópontok nevére (a virtuális gépek nevére), valamint egy, az Azure virtuális hálózatból elérhető IP-címre.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows2019)

Az alábbi PowerShell-szkript egy feladatátvevő fürtöt hoz létre a Windows Server 2019-hez.  Frissítse a szkriptet a csomópontok nevére (a virtuális gépek nevére), valamint egy, az Azure virtuális hálózatból elérhető IP-címre.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```

További információ: Feladatátvevő [fürt: Fürt hálózati objektuma.](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97)

---


## <a name="configure-quorum"></a>Kvórum konfigurálása

Konfigurálja az üzleti igényeinek leginkább megfelelő kvórummegoldást. Konfigurálhat tanúsító [lemezt,](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum) [felhőbeli](/windows-server/failover-clustering/deploy-cloud-witness)tanúsítót vagy tanúsító [fájlmegosztást.](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum) További információ: [Kvórum SQL Server virtuális gépekkel.](hadr-cluster-best-practices.md#quorum) 


## <a name="test-cluster-failover"></a>Fürt feladatátvételének tesztelése

Tesztelje a fürt feladatátvételét. A **Feladatátvevőfürt-kezelő** kattintson a jobb gombbal a fürtre, válassza a További műveletek Alapvető fürterőforrás áthelyezése Csomópont kiválasztása lehetőséget, majd válassza ki a fürt másik  >    >  csomópontját. Helyezze át a magfürt-erőforrást a fürt minden csomópontjára, majd helyezze vissza az elsődleges csomópontra. Ha sikeresen áthelyezi a fürtöt az egyes csomópontok között, készen áll a fürt SQL Server.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="A fürt feladatátvételének tesztelése az alapvető erőforrás más csomópontokra való áthelyezésével":::


## <a name="create-sql-server-fci"></a>FCI SQL Server létrehozása

A feladatátvevő fürt konfigurálása után létrehozhatja a SQL Server FCI-t.

1. Csatlakozzon az első virtuális géphez RDP használatával.

1. A **Feladatátvevőfürt-kezelő** győződjön meg arról, hogy az összes alapvető fürterőforrás az első virtuális gépen található. Szükség esetén helyezze át az összes erőforrást erre a virtuális gépre.

1. Keresse meg a telepítési adathordozót. Ha a virtuális gép az egyik Azure Marketplace rendszerképet használja, az adathordozó a következő helyen található: `C:\SQLServer_<version number>_Full` . 

1. Válassza a **Beállítás lehetőséget.**

1. A SQL Server **központban válassza** a Telepítés **lehetőséget.**

1. Válassza **az SQL Server fürt telepítése** lehetőséget, majd a varázsló utasításait követve telepítse a SQL Server FCI-t.

   Az FCI-adattáraknak a prémium fájlmegosztáson kell lennie. Adja meg a megosztás teljes elérési útját a következő formátumban: `\\storageaccountname.file.core.windows.net\filesharename\foldername` . Megjelenik egy figyelmeztetés, amely tudatja, hogy adatkönyvtárként megadott egy fájlkiszolgálót. Ez a figyelmeztetés várható. Győződjön meg arról, hogy a virtuális gép RDP-n keresztüli eléréséhez használt felhasználói fiók a fájlmegosztás megőrzésével megegyezik az SQL Server szolgáltatás által a lehetséges hibák elkerülése érdekében használt fiókkal.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/use-file-share-as-data-directories.png" alt-text="Fájlmegosztás használata SQL-adattáraként":::

1. Miután befejezte a varázsló lépéseit, a telepítő telepíti a SQL Server FCI-t az első csomópontra.

1. Miután a telepítő telepíti az FCI-t az első csomópontra, csatlakozzon a második csomóponthoz RDP használatával.

1. Nyissa meg **SQL Server telepítési központot,** majd válassza a **Telepítés lehetőséget.**

1. Válassza **a Csomópont hozzáadása SQL Server fürthöz lehetőséget.** A varázsló utasításait követve telepítse a SQL Server és adja hozzá a kiszolgálót az FCI-hez.

   >[!NOTE]
   >Ha katalógusképet Azure Marketplace a SQL Server, SQL Server a rendszerkép tartalmazta a megfelelő eszközöket. Ha nem ezeket a rendszerképeket használja, telepítse külön a SQL Server eszközöket. További információ: [Download SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).

1. Ismételje meg ezeket a lépéseket a feladatátvevőfürt-példányhoz hozzáadni kívánt SQL Server csomópontokon. 

## <a name="register-with-the-sql-vm-rp"></a>Regisztrálás az SQL VM RP-ben

A SQL Server VM portálról való kezeléséhez regisztrálja azt az SQL IaaS-ügynök [](sql-agent-extension-manually-register-single-vm.md#lightweight-management-mode)bővítményével (RP) egyszerűsített felügyeleti módban, amely jelenleg az FCI és a SQL Server támogatott az Azure-beli virtuális gépeken. 

Regisztráljon egy SQL Server VM egyszerűsített módban a PowerShell-ben (a-LicenseType lehet `PAYG` vagy `AHUB` ):

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType ???? -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Kapcsolat konfigurálása 

A forgalomnak az aktuális elsődleges csomóponthoz való megfelelő útválasztása érdekében konfigurálja a környezetének megfelelő kapcsolati beállítást. Létrehozhat egy Azure Load [Balancert,](failover-cluster-instance-vnn-azure-load-balancer-configure.md) vagy ha SQL Server 2019 CU2 (vagy újabb) vagy Windows Server 2016 (vagy újabb) rendszert használ, használhatja helyette az elosztott hálózatnév funkciót. [](failover-cluster-instance-distributed-network-name-dnn-configure.md) 

A fürtök kapcsolati beállításaival kapcsolatos további információkért lásd: HADR-kapcsolatok SQL Server [Azure-beli virtuális gépeken.](hadr-cluster-best-practices.md#connectivity) 

## <a name="limitations"></a>Korlátozások

- Microsoft Distributed Transaction Coordinator (MSDTC) nem támogatott a Windows Server 2016 és korábbi verziókban. 
- A fájlstream prémium fájlmegosztású feladatátvevő fürtök esetén nem támogatott. A fájlstreamek használata esetén [](failover-cluster-instance-storage-spaces-direct-manually-configure.md) a fürtöt Közvetlen tárolóhelyek [azure-beli](failover-cluster-instance-azure-shared-disks-manually-configure.md) megosztott lemezek használatával telepítse.
- Csak az SQL IaaS-ügynök bővítményével való regisztráció támogatott egyszerűsített felügyeleti [módban.](sql-server-iaas-agent-extension-automate-management.md#management-modes) 
- Az adatbázis-pillanatképek jelenleg nem támogatottak a Azure Files fájlokra vonatkozó [ritka korlátozások miatt.](/rest/api/storageservices/features-not-supported-by-the-azure-file-service)  

## <a name="next-steps"></a>Következő lépések

Ha még nem tette meg, konfigurálja az FCI-hez való csatlakozást egy virtuális hálózat nevével és [egy Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) vagy elosztott hálózatnévvel [(DNN).](failover-cluster-instance-distributed-network-name-dnn-configure.md) 


Ha a prémium fájlmegosztások nem a megfelelő FCI-tárolási megoldás [](failover-cluster-instance-azure-shared-disks-manually-configure.md) az Ön számára, fontolja meg az FCI azure-beli megosztott lemezekkel vagy Közvetlen tárolóhelyek [létrehozását.](failover-cluster-instance-storage-spaces-direct-manually-configure.md) 

További tudnivalókért tekintse meg az FCI áttekintését az [Azure-beli](failover-cluster-instance-overview.md) virtuális SQL Server és a fürtkonfiguráció ajánlott [eljárásait ismertető témakörben.](hadr-cluster-best-practices.md) 

További információkért lásd: 
- [Windows-fürttechnológiák](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server fürtpéldányok létrehozása](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

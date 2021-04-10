---
title: SAP ASCS/SCS multi-SID, HA a WSFC és az Azure Shared Disk | Microsoft Docs
description: Többszörös SID magas rendelkezésre állása egy SAP ASCS/SCS-példányhoz a WSFC és az Azure Shared Disk szolgáltatással
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: cbf18abe-41cb-44f7-bdec-966f32c89325
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cef0630cfc7ec7d080073085e577153ae7a47ecf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102504456"
---
# <a name="sap-ascsscs-instance-multi-sid-high-availability-with-windows-server-failover-clustering-and-azure-shared-disk"></a>SAP ASCS/SCS instance multi-SID magas rendelkezésre állás a Windows Server feladatátvételi fürtszolgáltatással és az Azure megosztott lemezzel

> ![Windows operációs rendszer][Logo_Windows] Windows
>

Ebből a cikkből megtudhatja, hogyan helyezhet át egyetlen ASCS/SCS-telepítésről egy SAP multi-SID konfigurációra, ha további SAP ASCS/SCS fürtözött példányokat telepít egy meglévő Windows Server feladatátvételi fürtszolgáltatási (WSFC) fürtre az Azure Shared Disk használatával. A folyamat befejezésekor konfigurált egy SAP multi-SID-fürtöt.

## <a name="prerequisites-and-limitations"></a>Előfeltételek és korlátozások

Jelenleg az Azure prémium SSD-lemezeket Azure-beli megosztott lemezként használhatja az SAP ASCS/SCS-példányhoz. A következő korlátozások vannak érvényben:

-  Az [Azure Ultra Disk](../../disks-types.md#ultra-disk) nem támogatott Azure-beli megosztott lemezként az SAP-munkaterhelésekhez. Jelenleg nem helyezhetők üzembe Azure-beli virtuális gépek a rendelkezésre állási csoportba tartozó Azure Ultra Disk használatával
-  Prémium SSD lemezzel rendelkező Azure-beli [megosztott lemez](../../disks-shared.md) csak a rendelkezésre állási csoportba tartozó virtuális gépek esetében támogatott. Availability Zones üzemelő példányban nem támogatott. 
-  Az Azure Shared Disk Value [maxShares](../../disks-shared-enable.md?tabs=azure-cli#disk-sizes) meghatározza, hogy hány fürtcsomópont használhatja a megosztott lemezt. Az SAP ASCS/SCS-példány esetében általában két csomópontot fog konfigurálni a Windows feladatátvevő fürtben, ezért a értékét `maxShares` kettőre kell beállítani.
-  Az összes SAP-ASCS/SCS-fürt virtuális gépnek ugyanabban az Azure-beli [közelségi csoportban](../../windows/proximity-placement-groups.md)kell lennie.   
   Bár a Windows-fürt virtuális gépei a rendelkezésre állási csoportokban a PPG nélkül is üzembe helyezhetők az Azure-beli megosztott lemezzel, a PPG biztosítja az Azure-beli megosztott lemezek és a fürt virtuális gépei közel fizikai közelségét, így a virtuális gépek és a tárolási réteg közötti alacsonyabb késést.    

Az Azure Shared Disk korlátozásával kapcsolatos további részletekért tekintse át az Azure Shared Disk dokumentációjának [korlátozások](../../disks-shared.md#limitations) című szakaszát.  

> [!IMPORTANT]
> Ha az Azure megosztott lemezzel telepíti az SAP ASCS/SCS Windows feladatátvevő fürtöt, vegye figyelembe, hogy az üzemelő példány egyetlen megosztott lemezzel fog működni egyetlen tároló fürtben. Az SAP-ASCS/SCS-példány hatással lesz a Storage-fürttel kapcsolatos problémák esetén, ahol az Azure-beli megosztott lemez telepítve van.  

> [!IMPORTANT]
> A telepítésnek meg kell felelnie a következő feltételeknek:
> * Minden adatbázis-kezelő rendszer (adatbázisok) biztonsági azonosítójának saját dedikált WSFC-fürttel kell rendelkeznie.  
> * Az egyik SAP-rendszerbiztonsági azonosítóhoz tartozó SAP-alkalmazás-kiszolgálóknak saját dedikált virtuális gépekkel kell rendelkezniük.  
> * A sorba helyezni replikációs kiszolgáló 1 és a sorba helyezni replikációs kiszolgáló 2 együttese nem támogatott ugyanazon a fürtön.  


## <a name="supported-os-versions"></a>Támogatott operációsrendszer-verziók

A Windows Server 2016 és a Windows Server 2019 egyaránt támogatott (a legújabb adatközpont-lemezképek használata).

Javasoljuk, hogy a **Windows Server 2019 Datacenter** használatát a következőképpen ajánljuk:
- A Windows 2019 feladatátvételi fürtszolgáltatás Azure-beli szolgáltatás
- Az Azure-gazdagép karbantartásával és jobb megismerésével bővült az Azure-beli menetrend eseményeinek monitorozása.
- Az elosztott hálózat nevét is használhatja (ez az alapértelmezett beállítás). Ezért nem kell dedikált IP-címet adni a fürt hálózati neveként. Ezt az IP-címet nem kell konfigurálnia az Azure belső Load Balancerján. 

## <a name="architecture"></a>Architektúra

A sorba helyezni replikációs kiszolgáló 1 (ERS1) és a sorba helyezni Replication Server 2 (ERS2) is támogatott a többszörös SID-konfigurációban.  A ERS1 és a ERS2 kombinációja nem támogatott ugyanabban a fürtben. 

1. Az első példa két SAP-SID-t mutat be a ERS1 architektúrával együtt, ahol:

   - Az SAP-SID1 a megosztott lemezen, a ERS1-mel van üzembe helyezve. Az ERS-példány a helyi gazdagépre és a helyi meghajtóra van telepítve.
     Az SAP SID1 rendelkezik a saját (virtuális) IP-címmel (SID1 (A) SCS IP1), amely az Azure belső Load balanceren van konfigurálva.

   - Az SAP-SID2 a megosztott lemezen, a ERS1-mel van üzembe helyezve. Az ERS-példány a helyi gazdagépre és a helyi meghajtóra van telepítve.
     Az SAP-SID2 rendelkezik a saját (virtuális) IP-címmel (SID2 (A) SCS IP2), amely az Azure belső Load balanceren is konfigurálva van.

![Magas rendelkezésre állású SAP ASCS/SCS-példány – két példány ERS1-konfigurációval][sap-ha-guide-figure-6007]

2. A második példa két SAP-SID-t mutat be a ERS2 architektúrával együtt, ahol: 

   - A ERS2-mel rendelkező SAP-SID1 is fürtözött, és helyi meghajtón vannak telepítve.  
     Az SAP-SID1 rendelkezik saját (virtuális) IP-címmel (SID1 (A) SCS IP1), amely az Azure belső Load balanceren van konfigurálva.
     Az SAP SID1 rendszer által használt SAP-ERS2 saját (virtuális) IP-címmel (SID1 ERS2 IP2) rendelkezik, amely az Azure belső Load balanceren van konfigurálva.

   - A ERS2-mel rendelkező SAP-SID2 is fürtözött, és helyi meghajtón vannak telepítve.  
     Az SAP-SID2 rendelkezik saját (virtuális) IP-címmel (SID2 (A) SCS IP3), amely az Azure belső Load balanceren van konfigurálva.
     Az SAP SID2 rendszer által használt SAP-ERS2 saját (virtuális) IP-címmel (SID2 ERS2 IP4) rendelkezik, amely az Azure belső Load balanceren van konfigurálva.

   Itt összesen négy virtuális IP-címmel rendelkezünk:  
   - SID1 (A) SCS-IP1
   - SID2 ERS2 IP2
   - SID2 (A) SCS-IP3
   - SID2 ERS2 IP4

![Magas rendelkezésre állású SAP ASCS/SCS-példány – két példány a ERS1 és a ERS2-konfigurációval][sap-ha-guide-figure-6008]

## <a name="infrastructure-preparation"></a>Infrastruktúra-előkészítés

Új SAP SID- **PR2** fogunk telepíteni a **meglévő fürtözött** SAP **PR1** ASCS/SCS-példányon kívül.  

### <a name="host-names-and-ip-addresses"></a>Állomásnevek és IP-címek

| Állomásnév szerepkör | Állomásnév | Statikus IP-cím | Rendelkezésre állási csoport | Proximity elhelyezési csoport |
| --- | --- | --- |---| ---|
| első fürtcsomópont ASCS/SCS-fürt |PR1-ASCs-10 |10.0.0.4 |PR1-ASCs-avset |PR1PPG |
| második fürtcsomópont ASCS/SCS-fürt |PR1-ASCs-11 |10.0.0.5 |PR1-ASCs-avset |PR1PPG |
| Fürt hálózatnév | pr1clust |10.0.0.42 (**csak** a Win 2016-fürt esetében) | n.a. | n.a. |
| **SID1** ASCS-fürt hálózatnév | PR1 – ascscl |10.0.0.43 | n.a. | n.a. |
| **SID1** ERS-fürt hálózatának neve (**csak** ERS2 esetében) | PR1 – erscl |10.0.0.44 | n.a. | n.a. |
| **SID2** ASCS-fürt hálózatnév | PR2 – ascscl |10.0.0.45 | n.a. | n.a. |
| **SID2** ERS-fürt hálózatának neve (**csak** ERS2 esetében) | PR1 – erscl |10.0.0.46 | n.a. | n.a. |

### <a name="create-azure-internal-load-balancer"></a>Azure belső terheléselosztó létrehozása

Az SAP ASCS, az SAP SCS és az új SAP-ERS2 a virtuális állomásnév és a virtuális IP-címek használata. Az Azure-ban a virtuális IP-címek használatához [Load Balancer](../../../load-balancer/load-balancer-overview.md) szükséges. Javasoljuk, hogy használja a [standard Load balancert](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md). 

A második SAP SID ASCS/SCS/ERS példány **PR2** hozzá kell adnia egy konfigurációt a meglévő Load Balancerhez. Az első SAP SID- **PR1** konfigurációját már meg kell adni.  

**Egy SCS PR2 [példány száma 02]**
- Előtér-konfiguráció
    - Statikus ASCS/SCS IP- **10.0.0.45**
- Háttér-konfiguráció  
    Már létezik – a virtuális gépek már hozzá lettek adva a háttér-készlethez, az SAP SID- **PR1** konfigurálása közben.
- Mintavételi port
    - A 620 **-** es Port [**62002**] nem hagyja meg az alapértelmezett beállítást a protokoll (TCP), az intervallum (5), a nem Kifogástalan állapot küszöbértéke (2) számára.
- Terheléselosztási szabályok
    - Ha standard Load Balancer használ, válassza a hektár portok elemet.
    - Ha alapszintű Load Balancer használ, hozzon létre terheléselosztási szabályokat a következő portokhoz
        - 32 **Nr** TCP [**3202**]
        - 36 **Nr** TCP [**3602**]
        - 39 **Nr** TCP [**3902**]
        - 81 **Nr** TCP [**8102**]
        - 5 **Nr** 13 TCP [**50213**]
        - 5 **Nr** 14 TCP [**50214**]
        - 5 **Nr** 16 TCP [**50216**]
        - Társítsa a **PR2** ASCS frontend IP-címét, az állapot-mintavételt és a meglévő háttér-készletet.  

    - Győződjön meg arról, hogy az Üresjárati időkorlát (perc) a maximális 30 értékre van beállítva, és hogy a lebegőpontos IP-cím (a közvetlen kiszolgáló visszaadása) engedélyezve van.

**ERS2-PR2 [példány száma 12]** 

Ahogy a sorba helyezni Replication Server 2 (ERS2) is fürtözött, a ERS2 virtuális IP-címet is konfigurálni kell az Azure ILB az SAP ASCS/SCS IP felett. Ez a szakasz csak akkor érvényes, ha a sorba helyezni Replication Server 2 architektúrát használja a **PR2**.  
- Új előtér-konfiguráció
    - Statikus SAP-ERS2 IP- **10.0.0.46**

- Háttér-konfiguráció  
  A virtuális gépek már hozzá lettek adva a ILB backend-készlethez.  

- Új mintavételi port
    - A 621 **-**  es Port [**62112**] nem hagyja meg az alapértelmezett beállítást a protokoll (TCP), az intervallum (5), a nem Kifogástalan állapot küszöbértéke (2) számára.

- Új terheléselosztási szabályok
    - Ha standard Load Balancer használ, válassza a hektár portok elemet.
    - Ha alapszintű Load Balancer használ, hozzon létre terheléselosztási szabályokat a következő portokhoz
        - 32 **Nr** TCP [**3212**]
        - 33 **Nr** TCP [**3312**]
        - 5 **Nr** 13 TCP [**51212**]
        - 5 **Nr** 14 TCP [**51212**]
        - 5 **Nr** 16 TCP [**51212**]
        - Társítsa a **PR2** ERS2 frontend IP-címét, az állapot-mintavételt és a meglévő háttér-készletet.  

    - Győződjön meg arról, hogy az Üresjárati időkorlát (perc) a maximális értékre van beállítva (például 30), és hogy a lebegőpontos IP-cím (közvetlen kiszolgáló visszaadása) engedélyezve van.


### <a name="create-and-attach-second-azure-shared-disk"></a>Második Azure megosztott lemez létrehozása és csatolása

Futtassa ezt a parancsot a fürtcsomópontok egyikén. Módosítania kell az erőforráscsoport, az Azure-régió, a SAPSID és egyebek értékét.  

```powershell
    $ResourceGroupName = "MyResourceGroup"
    $location = "MyRegion"
    $SAPSID = "PR2"
    $DiskSizeInGB = 512
    $DiskName = "$($SAPSID)ASCSSharedDisk"
    $NumberOfWindowsClusterNodes = 2
    $diskConfig = New-AzDiskConfig -Location $location -SkuName Premium_LRS  -CreateOption Empty  -DiskSizeGB $DiskSizeInGB -MaxSharesCount $NumberOfWindowsClusterNodes
    
    $dataDisk = New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $diskConfig
    ##################################
    ## Attach the disk to cluster VMs
    ##################################
    # ASCS Cluster VM1
    $ASCSClusterVM1 = "pr1-ascs-10"
    # ASCS Cluster VM2
    $ASCSClusterVM2 = "pr1-ascs-11"
    # next free LUN number
    $LUNNumber = 1
    # Add the Azure Shared Disk to Cluster Node 1
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM1 
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun $LUNNumber
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
    # Add the Azure Shared Disk to Cluster Node 2
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM2
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun $LUNNumber
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
   ```
### <a name="format-the-shared-disk-with-powershell"></a>A megosztott lemez formázása a PowerShell-lel
1. A lemez számának beolvasása. Futtassa a PowerShell-parancsokat az egyik fürtcsomóponton:

   ```powershell
    Get-Disk | Where-Object PartitionStyle -Eq "RAW"  | Format-Table -AutoSize 
    # Example output
    # Number Friendly Name     Serial Number HealthStatus OperationalStatus Total Size Partition Style
    # ------ -------------     ------------- ------------ ----------------- ---------- ---------------
    # 3      Msft Virtual Disk               Healthy      Online                512 GB RAW            

   ```
2. Formázza a lemezt. Ebben a példában ez a 3. lemez. 

   ```powershell
    # Format SAP ASCS Disk number '3', with drive letter 'S'
    $SAPSID = "PR2"
    $DiskNumber = 3
    $DriveLetter = "S"
    $DiskLabel = "$SAPSID" + "SAP"
    
    Get-Disk -Number $DiskNumber | Where-Object PartitionStyle -Eq "RAW" | Initialize-Disk -PartitionStyle GPT -PassThru |  New-Partition -DriveLetter $DriveLetter -UseMaximumSize | Format-Volume  -FileSystem ReFS -NewFileSystemLabel $DiskLabel -Force -Verbose
    # Example outout
    # DriveLetter FileSystemLabel FileSystem DriveType HealthStatus OperationalStatus SizeRemaining      Size
    # ----------- --------------- ---------- --------- ------------ ----------------- -------------      ----
    # S           PR2SAP          ReFS       Fixed     Healthy      OK                    504.98 GB 511.81 GB
   ```

3. Ellenőrizze, hogy a lemez most már fürtözött lemezként jelenik-e meg.  
   ```powershell
    # List all disks
    Get-ClusterAvailableDisk -All
    # Example output
    # Cluster    : pr1clust
    # Id         : c469b5ad-d089-4d8f-ae4c-d834cbbde1a2
    # Name       : Cluster Disk 2
    # Number     : 3
    # Size       : 549755813888
    # Partitions : {\\?\GLOBALROOT\Device\Harddisk3\Partition2\}
   ```

4. Regisztrálja a lemezt a fürtben.  
   ```powershell
     # Add the disk to cluster 
    Get-ClusterAvailableDisk -All | Add-ClusterDisk
    # Example output 
    # Name           State  OwnerGroup        ResourceType 
    # ----           -----  ----------        ------------ 
    # Cluster Disk 2 Online Available Storage Physical Disk
   ```

## <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a>Virtuális állomásnév létrehozása a fürtözött SAP ASCS/SCS-példányhoz

1. Hozzon létre egy DNS-bejegyzést a virtuális gazdagép neveként a Windows DNS-kezelőben az SAP ASCS/SCS példány új verziójának létrehozásához.  
   A virtuális gazdagép neveként a DNS-ben hozzárendelt IP-címnek meg kell egyeznie a Azure Load Balancer hozzárendelt IP-címmel.  

   ![_Define az SAP ASCS/SCS-fürt virtuális neve és IP-címe DNS-bejegyzését][sap-ha-guide-figure-6009]
 
   _Adja meg a DNS-bejegyzést az SAP ASCS/SCS-fürt virtuális neve és IP-címe számára_

2. Ha az SAP sorba helyezni Replication Server 2-et használja, amely fürtözött példány is, akkor le kell foglalni a DNS virtuális ERS2 is. 
   A DNS-ben a ERS2 virtuális állomásnévhez hozzárendelt IP-címnek meg kell egyeznie a Azure Load Balancer hozzárendelt IP-címmel.  

   ![Az SAP ERS2-fürt virtuális neve és IP-címéhez tartozó DNS-bejegyzés _Define][sap-ha-guide-figure-6010]
 
   _Adja meg az SAP ERS2-fürt virtuális neve és IP-címe DNS-bejegyzését_

3. A virtuális gazdagép neveként hozzárendelt IP-cím megadásához válassza a **DNS-kezelő**  >  **tartomány** lehetőséget.

   ![Új virtuális név és IP-cím az SAP ASCS/SCS és a ERS2-fürt konfigurációjához][sap-ha-guide-figure-6011]

   _Új virtuális név és TCP/IP-cím az SAP ASCS/SCS és a ERS2-fürt konfigurációjához_

## <a name="sap-installation"></a>SAP-telepítés 

### <a name="install-the-sap-first-cluster-node"></a>Az SAP első fürtcsomópont telepítése

Kövesse az SAP által ismertetett telepítési eljárást. Győződjön meg arról, hogy a telepítés indítása "első fürt csomópontja", majd a "fürt megosztott lemeze" lehetőséget választja a konfigurációs beállításként.  
Válassza ki az újonnan létrehozott megosztott lemezt.  

### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a>Az ASCS/SCS-példány SAP-profiljának módosítása

Ha a sorba helyezni Replication Server 1 rendszert futtatja, `enque/encni/set_so_keepalive` az alább leírtak szerint adja hozzá az SAP-profil paramétert. A profil paraméter megakadályozza a kapcsolódást az SAP-munkafolyamatok és a sorba helyezni-kiszolgáló között, ha túl sokáig tétlenek. Az SAP-paraméter nem szükséges a ERS2. 

1. Adja hozzá ezt a profil paramétert az SAP ASCS/SCS instance profilhoz, ha az ERS1-t használja.

   ```
   enque/encni/set_so_keepalive = true
   ```
   
   A ERS1 és a ERS2 esetében ügyeljen arra, hogy az `keepalive` operációs rendszer paramétereinek beállítása a [1410736](https://launchpad.support.sap.com/#/notes/1410736)-es SAP-megjegyzésben leírtak szerint történjen.   

2. Az SAP-profil paramétereinek alkalmazásához indítsa újra az SAP ASCS/SCS-példányt.

### <a name="configure-probe-port-on-the-cluster-resource"></a>Mintavételi port konfigurálása a fürterőforrás számára

A belső terheléselosztó mintavételi funkciójának használatával biztosíthatja, hogy a teljes fürtkonfiguráció működjön a Azure Load Balancer. Az Azure belső terheléselosztó általában a bejövő munkaterheléseket a részt vevő virtuális gépek között egyenlően osztja el.

Ez azonban nem fog működni bizonyos fürtkonfiguráció esetén, mert csak egy példány aktív. A másik példány passzív, és nem fogadja el a számítási feladatok egyikét sem. A mintavételi funkciók segítségével az Azure belső terheléselosztó felismeri, hogy melyik példány aktív, és csak az aktív példányt célozza meg.  

> [!IMPORTANT]
> Ebben a példában a **ProbePort** a 620 **Nr** értékre van állítva. A **02** -es számú SAP ASCS-példány esetében 620 **02**.
> A konfigurációt úgy kell beállítani, hogy az megfeleljen az SAP-példányok számának és az SAP SID-nek.

Mintavételi port hozzáadásához futtassa ezt a PowerShell-modult az egyik fürtözött virtuális gépen:

- SAP ASC/SCS-példány esetén a **02** -es számú példánnyal 
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID PR2 -ProbePort 62002
   ```

- A ERS2 használata esetén a **12-es** példányszámmal, amely fürtözött. Nem kell konfigurálni a mintavételi portot a ERS1, mert nem fürtözött.  
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID PR2 -ProbePort 62012 -IsSAPERSClusteredInstance $True
   ```

 A függvény kódja a következőhöz `Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource` hasonlóan fog kinézni:
   ```powershell
    function Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource {
    <#
    .SYNOPSIS 
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    
    .DESCRIPTION
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    It will also restart SAP Cluster group (default behavior), to activate the changes. 
    
    You need to run it on one of the SAP ASCS/SCS Windows cluster nodes.
    
    Expectation is that SAP group is installed with official SWPM installation tool, which will set default expected naming convention for:
    - SAP Cluster Group:               'SAP $SAPSID'
    - SAP Cluster IP Address Resource: 'SAP $SAPSID IP' 
    
    .PARAMETER SAPSID 
    SAP SID - 3 characters staring with letter.
    
    .PARAMETER ProbePort 
    Azure Load Balancer Health Check Probe Port.
    
    .PARAMETER RestartSAPClusterGroup 
    Optional parameter. Default value is '$True', so SAP cluster group will be restarted to activate the changes.
    
    .PARAMETER IsSAPERSClusteredInstance 
    Optional parameter.Default value is '$False'.
    If set to $True , then handle clsutered new SAP ERS2 instance.
    
    
    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP', and restart the SAP cluster group 'SAP AB1', to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 
    
    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP'. SAP cluster group 'SAP AB1' IS NOT restarted, therefore changes are NOT active.
    # To activate the changes you need to manually restart 'SAP AB1' cluster group.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -RestartSAPClusterGroup $False
    
    .EXAMPLE 
    # Set probe port to 62001, on SAP cluster resource 'SAP AB1 ERS IP'. SAP cluster group 'SAP AB1 ERS' IS restarted, to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -IsSAPERSClusteredInstance $True
        
    #> 
    
        [CmdletBinding()]
        param(
            
            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]  
            [ValidateLength(3,3)]      
            [string]$SAPSID,

            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]        
            [int] $ProbePort,
    
            [Parameter(Mandatory=$False)] 
            [bool] $RestartSAPClusterGroup = $True,
    
            [Parameter(Mandatory=$False)] 
            [bool] $IsSAPERSClusteredInstance = $False
        
        )
    
        BEGIN{}
        
        PROCESS{
            try{                                      
                
                if($IsSAPERSClusteredInstance){
                    #Handle clustered SAP ERS Instance
                    $SAPClusterRoleName = "SAP $SAPSID ERS"
                    $SAPIPresourceName = "SAP $SAPSID ERS IP"            
                }else{
                    #Handle clustered SAP ASCS/SCS Instance
                    $SAPClusterRoleName = "SAP $SAPSID"
                    $SAPIPresourceName = "SAP $SAPSID IP"
                }

                $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
                $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
                $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
                $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
                $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
                $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
                $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value
    
                $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }
    
                #Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
                Write-Output "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" 
    
                Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
    
                Write-Output " "
                Write-Output "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." 
                Write-Output " "
                Write-Output "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." 
                Write-Output " "
    
                $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}
    
                Write-Output " "
    
                #$ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"
    
                if($RestartSAPClusterGroup){
                    Write-Output ""
                    Write-Output "Activating changes..." 
    
                    Write-Output " "
                    Write-Output "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..."
                    Stop-ClusterResource -Name $SAPIPresourceName
                    sleep 5
    
                    Write-Output "Starting SAP cluster role '$SAPClusterRoleName' ..."
                    Start-ClusterGroup -Name $SAPClusterRoleName
    
                    Write-Output "New ProbePort parameter is active." 
                    Write-Output " "
    
                    Write-Output "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" 
                    Write-Output " " 
                    Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
                }else
                {
                    Write-Output "SAP cluster role '$SAPClusterRoleName' is not restarted, therefore changes are not activated."
                }
            }
            catch{
               Write-Error  $_.Exception.Message
           }
    
        }
    
        END {}
    }
   ```

### <a name="continue-with-the-sap-installation"></a>Az SAP telepítésének folytatása

1. Telepítse az adatbázis-példányt az SAP telepítési útmutatójában ismertetett eljárást követve.  
2. Telepítse az SAP-t a második fürtcsomóponton az SAP telepítési útmutatójában ismertetett lépéseket követve.  
3. Telepítse az SAP Primary Application Server-(PAS-) példányt arra a virtuális gépre, amelyet a PAS üzemeltetésére jelölt ki.   
   Kövesse az SAP telepítési útmutatójában leírt eljárást. Nincsenek függőségek az Azure-ban.
4. Telepítsen további SAP-alkalmazás-kiszolgálókat a virtuális gépekre, amelyek az SAP Application Server-példányok üzemeltetésére vannak kijelölve.  
   Kövesse az SAP telepítési útmutatójában leírt eljárást. Nincsenek függőségek az Azure-ban. 

## <a name="test-the-sap-ascsscs-instance-failover"></a>Az SAP-ASCS/SCS-példány feladatátvételének tesztelése
A vázolt feladatátvételi tesztek esetében feltételezzük, hogy az SAP ASCS aktív az A csomóponton.  

1. Győződjön meg arról, hogy az SAP-rendszer sikeresen feladatátvételt végez az A csomópontról a B csomópontra. Ebben a példában a tesztet a SAPSID **PR2** hajtja végre.  
   Győződjön meg arról, hogy minden SAPSID sikeresen át tud lépni a másik fürtcsomóponton.   
   Válasszon egyet az alábbi lehetőségek közül, ha feladatátvételt szeretne kezdeményezni az SAP-fürtről a (z) " \<SID\> a" fürtről a B csomópontra:
    - Feladatátvevőfürt-kezelő  
    - Feladatátvevő fürt PowerShell

    ```powershell
    $SAPSID = "PR2"     # SAP <SID>
 
    $SAPClusterGroup = "SAP $SAPSID"
    Move-ClusterGroup -Name $SAPClusterGroup

    ```
2. Indítsa újra az A fürtcsomópont-csomópontot a Windows vendég operációs rendszeren. Ez automatikusan feladatátvételt kezdeményez az SAP \<SID\> -fürtből az a csomópontról a B csomópontra.  
3. Indítsa újra az A fürtcsomópont-csomópontot a Azure Portal. Ez automatikusan feladatátvételt kezdeményez az SAP \<SID\> -fürtből az a csomópontról a B csomópontra.  
4. Indítsa újra az A fürtcsomópont Azure PowerShell használatával. Ez automatikusan feladatátvételt kezdeményez az SAP \<SID\> -fürtből az a csomópontról a B csomópontra.

## <a name="next-steps"></a>Következő lépések

* [Készítse elő az Azure-infrastruktúrát az SAP-hez a Windows feladatátvevő fürt és az SAP ASCS/SCS-példány megosztott lemezének használatával][sap-high-availability-infrastructure-wsfc-shared-disk]
* [SAP NetWeaver HA telepítése Windows feladatátvevő fürtön és megosztott lemezen egy SAP ASCS/SCS-példányhoz][sap-high-availability-installation-wsfc-shared-disk]


[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1869038]:https://launchpad.support.sap.com/#/notes/1869038
[2287140]:https://launchpad.support.sap.com/#/notes/2287140
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[networking-limits-azure-resource-manager]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md


[sap-net-weaver-ports]:https://help.sap.com/viewer/ports
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-net-weaver-ports-ascs-scs-ports]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md

[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk-install-ascs]:sap-high-availability-installation-wsfc-shared-disk.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-high-availability-installation-wsfc-shared-disk-modify-ascs-profile]:sap-high-availability-installation-wsfc-shared-disk.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556
[sap-high-availability-installation-wsfc-shared-disk-add-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761
[sap-high-availability-installation-wsfc-shared-disk-win-firewall-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#4498c707-86c0-4cde-9c69-058a7ab8c3ac
[sap-high-availability-installation-wsfc-shared-disk-change-ers-service-startup-type]:sap-high-availability-installation-wsfc-shared-disk.md#094bc895-31d4-4471-91cc-1513b64e406a
[sap-high-availability-installation-wsfc-shared-disk-test-ascs-failover-and-sios-repl]:sap-high-availability-installation-wsfc-shared-disk.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9


[sap-high-availability-installation-wsfc-file-share]:sap-high-availability-installation-wsfc-file-share.md
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6001]:media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png

[sap-ha-guide-figure-6007]:media/virtual-machines-shared-sap-high-availability-guide/6007-sap-multi-sid-ascs-azure-shared-disk-sid1.png
[sap-ha-guide-figure-6008]:media/virtual-machines-shared-sap-high-availability-guide/6008-sap-multi-sid-ascs-azure-shared-disk-sid2.png
[sap-ha-guide-figure-6009]:media/virtual-machines-shared-sap-high-availability-guide/6009-sap-multi-sid-ascs-azure-shared-disk-dns1.png
[sap-ha-guide-figure-6010]:media/virtual-machines-shared-sap-high-availability-guide/6010-sap-multi-sid-ascs-azure-shared-disk-dns2.png
[sap-ha-guide-figure-6011]:media/virtual-machines-shared-sap-high-availability-guide/6011-sap-multi-sid-ascs-azure-shared-disk-dns3.png

[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../availability.md
---
title: SAP ASCS/SCS multi-SID HA a WSFC&megosztott lemezzel rendelkezik az Azure-ban | Microsoft Docs
description: Többszörös SID magas rendelkezésre állása egy SAP ASCS/SCS-példányhoz a Windows Server feladatátvételi fürtszolgáltatással és az Azure-beli megosztott lemezzel
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
ms.date: 10/16/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 92d881a85f5719e15db6fe24b80deed3b3d3fe35
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102504439"
---
# <a name="sap-ascsscs-instance-multi-sid-high-availability-with-windows-server-failover-clustering-and-shared-disk-on-azure"></a>SAP ASCS/SCS instance multi-SID magas rendelkezésre állás a Windows Server feladatátvételi fürtszolgáltatással és a megosztott lemezzel az Azure-ban

> ![Windows operációs rendszer][Logo_Windows] Windows
>

Ha SAP-telepítést használ, belső terheléselosztó használatával létre kell hoznia egy Windows-fürtkonfiguráció az SAP Central Services (ASCS/SCS) példányaihoz.

Ebből a cikkből megtudhatja, hogyan helyezhet át egyetlen ASCS/SCS-telepítésről egy SAP multi-SID konfigurációra úgy, hogy további SAP ASCS/SCS fürtözött példányokat telepít egy meglévő Windows Server feladatátvételi fürtszolgáltatásba (WSFC) a megosztott lemezzel a SIOS használatával a megosztott lemez szimulálása érdekében. A folyamat befejezésekor konfigurált egy SAP multi-SID-fürtöt.

> [!NOTE]
> Ez a funkció csak a Azure Resource Manager telepítési modellben érhető el.
>
>Az egyes Azure-beli belső terheléselosztó esetében korlátozva van a magánhálózati előtér-IP-címek száma.
>
>Az egyes WSFC-fürtökben lévő SAP ASCS/SCS-példányok maximális száma megegyezik az egyes Azure-beli belső terheléselosztó számára elérhető saját előtér-IP-címek maximális számával.
>

A terheléselosztó korlátaival kapcsolatos további információkért tekintse meg a [hálózatkezelési korlátok: Azure Resource Manager][networking-limits-azure-resource-manager]a "privát ELŐTÉR-IP/terheléselosztó" című szakaszt.

> [!IMPORTANT]
> A lebegő IP-címek nem támogatottak a terheléselosztási helyzetekben a hálózati adapter másodlagos IP-konfigurációjában. További részletek: az [Azure Load Balancer korlátozásai](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Ha a virtuális gép további IP-címére van szüksége, helyezzen üzembe egy második hálózati adaptert.  

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Már konfigurált egy WSFC-fürtöt egy SAP ASCS/SCS-példányhoz a **fájlmegosztás** használatával, ahogy az ebben a diagramban látható.

![Magas rendelkezésre állású SAP ASCS/SCS-példány][sap-ha-guide-figure-6001]

> [!IMPORTANT]
> A telepítésnek meg kell felelnie a következő feltételeknek:
> * Az SAP-ASCS/SCS-példányoknak ugyanazt a WSFC-fürtöt kell megosztaniuk.  
> * Minden adatbázis-kezelő rendszer (adatbázisok) biztonsági azonosítójának saját dedikált WSFC-fürttel kell rendelkeznie.  
> * Az egyik SAP-rendszerbiztonsági azonosítóhoz tartozó SAP-alkalmazás-kiszolgálóknak saját dedikált virtuális gépekkel kell rendelkezniük.  
> * A sorba helyezni replikációs kiszolgáló 1 és a sorba helyezni replikációs kiszolgáló 2 együttese nem támogatott ugyanazon a fürtön.  

## <a name="sap-ascsscs-multi-sid-architecture-with-shared-disk"></a>SAP ASCS/SCS multi-SID architektúra megosztott lemezzel

A cél az, hogy több SAP ABAP ASCS vagy SAP Java SCS fürtözött példányt telepítsen ugyanabba a WSFC-fürtbe, ahogy az itt látható:

![Több SAP ASCS/SCS fürtözött példány az Azure-ban][sap-ha-guide-figure-6002]

A terheléselosztó korlátaival kapcsolatos további információkért tekintse meg a [hálózatkezelési korlátok: Azure Resource Manager][networking-limits-azure-resource-manager]a "privát ELŐTÉR-IP/terheléselosztó" című szakaszt.

A két magas rendelkezésre állású SAP-rendszerrel rendelkező teljes környezet a következőképpen fog kinézni:

![SAP magas rendelkezésre állású többszörös SID beállítása két SAP-rendszerbiztonsági azonosítóval][sap-ha-guide-figure-6003]

## <a name="prepare-the-infrastructure-for-an-sap-multi-sid-scenario"></a><a name="25e358f8-92e5-4e8d-a1e5-df7580a39cb0"></a> Az infrastruktúra előkészítése egy több SID-es SAP-forgatókönyvhöz

Az infrastruktúra előkészítéséhez telepítsen egy további SAP ASCS/SCS-példányt a következő paraméterekkel:

| Paraméter neve | Érték |
| --- | --- |
| SAP-ASCS/SCS SID |PR1-LB – ASCs |
| SAP adatbázis-kezelő belső terheléselosztó | PR5 |
| SAP virtuális gazdagép neve | PR5 – SAP-CL |
| SAP ASCS/SCS virtuális gazdagép IP-címe (további Azure Load Balancer IP-cím) | 10.0.0.50 |
| SAP-ASCS/SCS-példány száma | 50 |
| ILB mintavételi port további SAP ASCS/SCS-példányhoz | 62350 |

> [!NOTE]
> Az SAP ASCS/SCS cluster instances esetében minden IP-címnek egyedi mintavételi portot kell megadnia. Ha például egy Azure-beli belső terheléselosztó egy IP-címe a 62300-es mintavételi portot használja, a terheléselosztó más IP-címe nem használhatja az 62300-es mintavételi portot.
>
>Azért, mert a 62300-es mintavételi port már foglalt, az 62350-es mintavételi portot használjuk.

További SAP ASCS/SCS-példányokat is telepíthet a meglévő WSFC-fürtbe két csomóponttal:

| Virtuális gépi szerepkör | Virtuális gép gazdagépének neve | Statikus IP-cím |
| --- | --- | --- |
| Első fürtcsomópont a ASCS/SCS-példányhoz |PR1-ASCs-0 |10.0.0.10 |
| Második fürtcsomópont a ASCS/SCS-példányhoz |PR1-ASCs-1 |10.0.0.9 címek |

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance-on-the-dns-server"></a>Virtuális állomásnév létrehozása a fürtözött SAP ASCS/SCS-példányhoz a DNS-kiszolgálón

A következő paraméterek használatával létrehozhat egy DNS-bejegyzést a ASCS/SCS-példány virtuális állomásnevét:

| Új SAP ASCS/SCS virtuális gazdagép neve | Társított IP-cím |
| --- | --- |
|PR5 – SAP-CL |10.0.0.50 |

Az új állomásnév és IP-cím a DNS-kezelőben jelenik meg, az alábbi képernyőképen látható módon:

![A DNS-kezelő listája kiemelve a megadott DNS-bejegyzést az új SAP ASCS/SCS-fürt virtuális neve és TCP/IP-címe számára][sap-ha-guide-figure-6004]

> [!NOTE]
> A további ASCS/SCS-példány virtuális állomásnevét hozzárendelt új IP-címnek meg kell egyeznie az SAP Azure Load Balancerhez rendelt új IP-címmel.
>
>Ebben az esetben az IP-cím 10.0.0.50.

### <a name="add-an-ip-address-to-an-existing-azure-internal-load-balancer-by-using-powershell"></a>IP-cím hozzáadása meglévő Azure belső terheléselosztóhoz a PowerShell használatával

Ha több SAP ASCS/SCS-példányt szeretne létrehozni ugyanabban a WSFC-fürtben, a PowerShell használatával vegyen fel egy IP-címet egy meglévő Azure belső terheléselosztó számára. Minden IP-címnek saját terheléselosztási szabályokra, mintavételi portra, előtér-IP-készletre és háttér-készletre van szüksége.

A következő parancsfájl egy új IP-címet helyez el egy meglévő terheléselosztó számára. Frissítse a környezet PowerShell-változóit. A parancsfájl létrehozza az összes szükséges terheléselosztási szabályt az összes SAP ASCS/SCS-porthoz.

```powershell

# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>
Clear-Host
$ResourceGroupName = "SAP-MULTI-SID-Landscape"      # Existing resource group name
$VNetName = "pr2-vnet"                        # Existing virtual network name
$SubnetName = "Subnet"                        # Existing subnet name
$ILBName = "pr2-lb-ascs"                      # Existing ILB name                      
$ILBIP = "10.0.0.50"                          # New IP address
$VMNames = "pr2-ascs-0","pr2-ascs-1"          # Existing cluster virtual machine names
$SAPInstanceNumber = 50                       # SAP ASCS/SCS instance number: must be a unique value for each cluster
[int]$ProbePort = "623$SAPInstanceNumber"     # Probe port: must be a unique value for each IP and load balancer

$ILB = Get-AzLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName

$count = $ILB.FrontendIpConfigurations.Count + 1
$FrontEndConfigurationName ="lbFrontendASCS$count"
$LBProbeName = "lbProbeASCS$count"

# Get the Azure virtual network and subnet
$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

# Add a second front-end and probe configuration
Write-Host "Adding new front end IP Pool '$FrontEndConfigurationName' ..." -ForegroundColor Green
$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id
$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 10  | Set-AzLoadBalancer

# Get a new updated configuration
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Get an updated LP FrontendIpConfig
$FEConfig = Get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

# Add a back-end configuration into an existing ILB
$BackEndConfigurationName  = "backendPoolASCS$count"
Write-Host "Adding new backend Pool '$BackEndConfigurationName' ..." -ForegroundColor Green
$BEConfig = Add-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB | Set-AzLoadBalancer

# Get an updated config
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Assign VM NICs to the back-end pool
$BEPool = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
foreach($VMName in $VMNames){
        $VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)        
        $NIC = Get-AzNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName                
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools += $BEPool
        Write-Host "Assigning network card '$NICName' of the '$VMName' VM to the backend pool '$BackEndConfigurationName' ..." -ForegroundColor Green
        Set-AzNetworkInterface -NetworkInterface $NIC
        #start-AzVM -ResourceGroupName $ResourceGroupName -Name $VM.Name
}


# Create load-balancing rules
$Ports = "445","32$SAPInstanceNumber","33$SAPInstanceNumber","36$SAPInstanceNumber","39$SAPInstanceNumber","5985","81$SAPInstanceNumber","5$SAPInstanceNumber`13","5$SAPInstanceNumber`14","5$SAPInstanceNumber`16"
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
$FEConfig = get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$BEConfig = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

Write-Host "Creating load balancing rules for the ports: '$Ports' ... " -ForegroundColor Green

foreach ($Port in $Ports) {

        $LBConfigrulename = "lbrule$Port" + "_$count"
        Write-Host "Creating load balancing rule '$LBConfigrulename' for the port '$Port' ..." -ForegroundColor Green

        $ILB | Add-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $HealthProbe -Protocol tcp -FrontendPort  $Port -BackendPort $Port -IdleTimeoutInMinutes 30 -LoadDistribution Default -EnableFloatingIP   
}

$ILB | Set-AzLoadBalancer

Write-Host "Successfully added new IP '$ILBIP' to the internal load balancer '$ILBName'!" -ForegroundColor Green

```
A parancsfájl futtatása után az eredmények megjelennek a Azure Portalban, ahogy az alábbi képernyőképen látható:

![Új előtér-IP-címkészlet a Azure Portal][sap-ha-guide-figure-6005]

### <a name="add-disks-to-cluster-machines-and-configure-the-sios-cluster-share-disk"></a>Lemezek hozzáadása a fürtökhöz és a SIOS-fürt beállítása – lemez megosztása

Minden további SAP ASCS/SCS-példányhoz hozzá kell adnia egy új fürtre osztott lemezt. A Windows Server 2012 R2 esetében a jelenleg használt WSFC-fürt megosztott lemeze a SIOS DataKeeper szoftveres megoldás.

Tegye a következőket:
1. Adjon hozzá egy azonos méretű (szalagos) lemezt vagy lemezt az egyes fürtcsomópontok számára, és formázza őket.
2. Konfigurálja a tárolási replikációt a SIOS DataKeeper.

Ez az eljárás feltételezi, hogy már telepítette a SIOS DataKeeper a WSFC-fürtön. Ha telepítette, most konfigurálnia kell a számítógépek közötti replikálást. A folyamat részletes leírása a [SIOS DataKeeper-fürt kiadásának telepítése az SAP ASCS/SCS-fürt megosztott lemezére][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]című cikkben olvasható.  

![DataKeeper szinkron tükrözés az új SAP ASCS/SCS Share lemezhez][sap-ha-guide-figure-6006]

### <a name="deploy-vms-for-sap-application-servers-and-the-dbms-cluster"></a>Virtuális gépek üzembe helyezése SAP-alkalmazási kiszolgálókhoz és az adatbázis-kezelő fürthöz

A második SAP-rendszer infrastruktúra-előkészítésének befejezéséhez tegye a következőket:

1. Telepítsen dedikált virtuális gépeket az SAP-alkalmazáskiszolgáló számára, és tegye a saját dedikált rendelkezésre állási csoportját.
2. Telepítsen dedikált virtuális gépeket az adatbázis-kezelő fürthöz, és tegye mindegyiket saját dedikált rendelkezésre állási csoportjához.

## <a name="install-an-sap-netweaver-multi-sid-system"></a>SAP NetWeaver multi-SID rendszer telepítése

A második SAP-SID2 telepítésének teljes folyamatát a következő témakörben tekintheti [meg: SAP NETWEAVER Ha telepítés Windows feladatátvevő fürtön és megosztott lemez az SAP ASCS/SCS-példányokhoz][sap-high-availability-installation-wsfc-shared-disk].

A magas szintű eljárás a következő:

1. [Telepítse az SAP-t egy magas rendelkezésre állású ASCS/SCS-példánnyal][sap-high-availability-installation-wsfc-shared-disk-install-ascs].  
 Ebben a lépésben telepíti az SAP-t egy magas rendelkezésre állású ASCS/SCS-példánnyal a meglévő WSFC-fürt 1. csomópontján.

2. [Módosítsa az ASCS/SCS-példány SAP-profilját][sap-high-availability-installation-wsfc-shared-disk-modify-ascs-profile].

3. [Állítson be egy mintavételi portot][sap-high-availability-installation-wsfc-shared-disk-add-probe-port].  
 Ebben a lépésben egy SAP-fürterőforrás SAP-SID2-IP mintavételi portját konfigurálja a PowerShell használatával. Hajtsa végre ezt a konfigurációt az egyik SAP ASCS-/SCS-fürt csomópontjain.

4. Telepítse az adatbázis-példányt.  
 A második fürt telepítéséhez kövesse az SAP telepítési útmutatójának lépéseit.

5. Telepítse a második fürtcsomópont-csomópontot.  
 Ebben a lépésben telepíti az SAP-t egy magas rendelkezésre állású ASCS/SCS-példánnyal a meglévő WSFC-fürt 2. csomópontján. A második fürt telepítéséhez kövesse az SAP telepítési útmutatójának lépéseit.

6. Nyissa meg a Windows tűzfal portjait az SAP ASCS/SCS instance és a mintavételi port számára.  
    Mindkét, az SAP ASCS/SCS-példányokhoz használt fürtcsomóponton megnyitja az SAP ASCS/SCS által használt összes Windows tűzfal-portot. Ezek az SAP ASCS/SCS-példányok portjai az [SAP ASCS/SCS-portok][sap-net-weaver-ports-ascs-scs-ports]című fejezetben vannak felsorolva.

    Az összes többi SAP-port listáját itt tekintheti meg: az [összes SAP-termék TCP/IP-portjai][sap-net-weaver-ports].  

    Nyissa meg az Azure belső terheléselosztó mintavételi portját is, amely a forgatókönyvben 62350. Ez a [cikk][sap-high-availability-installation-wsfc-shared-disk-win-firewall-probe-port]ismerteti.

8. Telepítse az SAP Primary Application Servert az új dedikált virtuális gépre az SAP telepítési útmutatójában leírtak szerint.  

9. Telepítse az SAP további alkalmazáskiszolgáló szolgáltatást az új dedikált virtuális gépre az SAP telepítési útmutatójában leírtak szerint.

10. [Tesztelje az SAP ASCS/SCS instance feladatátvételi és SIOS replikációját][sap-high-availability-installation-wsfc-shared-disk-test-ascs-failover-and-sios-repl].

## <a name="next-steps"></a>Következő lépések

- [Hálózati korlátok: Azure Resource Manager][networking-limits-azure-resource-manager]
- [Több VIP Azure Load Balancer][load-balancer-multivip-overview]

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
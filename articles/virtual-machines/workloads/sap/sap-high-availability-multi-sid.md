---
title: Magas rendelkezésre állású SAP multi-SID konfiguráció létrehozása az Azure-ban
description: Ismerje meg, hogyan hozhat létre magas rendelkezésre állású SAP NetWeaver multi-SID konfigurációt a Windows rendszerű virtuális gépeken.
services: virtual-machines-windows, virtual-network, storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 0b89b4f8-6d6c-45d7-8d20-fe93430217ca
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 079ac273f7b2aa91b61ad2cdbcfe62cd989bb2f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101675276"
---
# <a name="create-a-high-availability-sap-netweaver-multi-sid-configuration"></a>Magas rendelkezésre állású SAP NetWeaver multi-SID konfiguráció létrehozása

[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md
[sap-ha-guide]:sap-high-availability-guide.md 
[sap-ha-guide-figure-6001]:./media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:./media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:./media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:./media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:./media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png
[networking-limits-azure-resource-manager]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[sap-ha-guide-9.1.1]:sap-high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097 
[sap-ha-guide-8.8]:sap-high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c
[sap-ha-guide-8.12.3.3]:sap-high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006 
[sap-ha-guide-9]:sap-high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:sap-high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170 
[sap-ha-guide-9.1.2]:sap-high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0 
[sap-ha-guide-9.1.3]:sap-high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556 
[sap-ha-guide-9.1.4]:sap-high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761 
[sap-ha-guide-9.4]:sap-high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a 
[sap-ha-guide-9.5]:sap-high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5 
[sap-ha-guide-9.6]:sap-high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772 
[sap-ha-guide-10]:sap-high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9

A Microsoft szeptember 2016-én kiadott egy szolgáltatást, amelyben több virtuális IP-címet is kezelhet egy belső Azure Load Balancer használatával. Ez a funkció már létezik az Azure külső Load balancerben.

Ha rendelkezik SAP-telepítéssel, a belső terheléselosztó használatával létrehozhat egy Windows-fürtöt az SAP ASCS/SCS számára, a [Windows rendszerű virtuális gépeken elérhető magas rendelkezésre állású SAP NetWeaver útmutatójában][sap-ha-guide]leírtaknak megfelelően.

Ebből a cikkből megtudhatja, hogyan helyezhet át egyetlen ASCS/SCS-telepítésről egy SAP multi-SID konfigurációra, ha további SAP ASCS/SCS fürtözött példányokat telepít egy meglévő Windows Server feladatátvételi fürtszolgáltatási (WSFC) fürtbe. A folyamat befejezése után egy SAP multi-SID fürtöt konfiguráltunk.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek
Már konfigurált egy WSFC-fürtöt, amely egy SAP ASCS/SCS-példányhoz van konfigurálva, ahogy az [útmutató a magas rendelkezésre állású SAP NetWeaver Windows rendszerű virtuális gépeken][sap-ha-guide] , valamint a jelen ábrán látható módon.

![Magas rendelkezésre állású SAP ASCS/SCS-példány][sap-ha-guide-figure-6001]

## <a name="target-architecture"></a>Cél architektúrája

A cél az, hogy több SAP ABAP ASCS vagy SAP Java SCS fürtözött példányt telepítsen ugyanabba a WSFC-fürtbe, ahogy az itt látható:

![Több SAP ASCS/SCS fürtözött példány az Azure-ban][sap-ha-guide-figure-6002]

> [!NOTE]
>Az egyes Azure-beli belső terheléselosztó esetében korlátozva van a magánhálózati előtér IP-címeinek száma.
>
>Az egyes WSFC-fürtökben lévő SAP ASCS/SCS-példányok maximális száma megegyezik az egyes Azure-beli belső terheléselosztó számára elérhető saját előtér-IP-címek maximális számával.
>

A terheléselosztó korlátaival kapcsolatos további információkért lásd: "privát előtéri IP-cím/terheléselosztó" a [hálózati korlátok között: Azure Resource Manager][networking-limits-azure-resource-manager].

A két magas rendelkezésre állású SAP-rendszerrel rendelkező teljes környezet a következőképpen fog kinézni:

![SAP magas rendelkezésre állású többszörös SID beállítása két SAP-rendszerbiztonsági azonosítóval][sap-ha-guide-figure-6003]

> [!IMPORTANT]
> A telepítésnek meg kell felelnie a következő feltételeknek:
> - Az SAP-ASCS/SCS-példányoknak ugyanazt a WSFC-fürtöt kell megosztaniuk.
> - Minden adatbázis-kezelői SID-nek saját dedikált WSFC-fürttel kell rendelkeznie.
> - Az egyik SAP-rendszerbiztonsági azonosítóhoz tartozó SAP-alkalmazás-kiszolgálóknak saját dedikált virtuális gépekkel kell rendelkezniük.


## <a name="prepare-the-infrastructure"></a>Az infrastruktúra előkészítése
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
| 1. ASCS/SCS-példányhoz tartozó fürtcsomópont |PR1-ASCs-0 |10.0.0.10 |
| 2. ASCS/SCS-példányhoz tartozó fürtcsomópont |PR1-ASCs-1 |10.0.0.9 címek |

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance-on-the-dns-server"></a>Virtuális állomásnév létrehozása a fürtözött SAP ASCS/SCS-példányhoz a DNS-kiszolgálón

A következő paraméterek használatával létrehozhat egy DNS-bejegyzést a ASCS/SCS-példány virtuális állomásnevét:

| Új SAP ASCS/SCS virtuális gazdagép neve | Társított IP-cím |
| --- | --- |
|PR5 – SAP-CL |10.0.0.50 |

Az új állomásnév és IP-cím a DNS-kezelőben jelenik meg, az alábbi képernyőképen látható módon:

![A DNS-kezelő listája kiemelve a megadott DNS-bejegyzést az új SAP ASCS/SCS-fürt virtuális neve és TCP/IP-címe számára][sap-ha-guide-figure-6004]

A DNS-bejegyzések létrehozásához szükséges eljárást a [Windows rendszerű virtuális gépeken a magas rendelkezésre állású SAP NetWeaver fő útmutatójában][sap-ha-guide-9.1.1]is részletesen ismertetjük.

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

# Get the Azure VNet and subnet
$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

# Add second front-end and probe configuration
Write-Host "Adding new front end IP Pool '$FrontEndConfigurationName' ..." -ForegroundColor Green
$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id
$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 10  | Set-AzLoadBalancer

# Get new updated configuration
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
# Get new updated LP FrontendIP COnfig
$FEConfig = Get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

# Add new back-end configuration into existing ILB
$BackEndConfigurationName  = "backendPoolASCS$count"
Write-Host "Adding new backend Pool '$BackEndConfigurationName' ..." -ForegroundColor Green
$BEConfig = Add-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB | Set-AzLoadBalancer

# Get new updated config
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Assign VM NICs to backend pool
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

### <a name="add-disks-to-cluster-machines-and-configure-the-sios-cluster-share-disk"></a>Lemezek hozzáadása a fürtökhöz, valamint a SIOS-fürt megosztott lemezének konfigurálása

Minden további SAP ASCS/SCS-példányhoz hozzá kell adnia egy új fürthöz tartozó megosztási lemezt. A Windows Server 2012 R2 esetében a jelenleg használt WSFC-fürt megosztott lemeze a SIOS DataKeeper szoftveres megoldás.

Tegye a következőket:
1. Adjon hozzá egy azonos méretű (szalagos) lemezt vagy lemezt az egyes fürtcsomópontok számára, és formázza őket.
2. Konfigurálja a tárolási replikációt a SIOS DataKeeper.

Ez az eljárás feltételezi, hogy már telepítette a SIOS DataKeeper a WSFC-fürtön. Ha telepítette, most konfigurálnia kell a számítógépek közötti replikálást. A folyamat részletes leírását a fő útmutató a [magas rendelkezésre állású SAP NetWeaver Windows rendszerű virtuális gépeken][sap-ha-guide-8.12.3.3]című témakör ismerteti.  

![DataKeeper szinkron tükrözés az új SAP ASCS/SCS Share lemezhez][sap-ha-guide-figure-6006]

### <a name="deploy-vms-for-sap-application-servers-and-dbms-cluster"></a>Virtuális gépek üzembe helyezése SAP-alkalmazáskiszolgáló és adatbázis-kezelő fürt számára

A második SAP-rendszer infrastruktúra-előkészítésének befejezéséhez tegye a következőket:

1. Telepítsen dedikált virtuális gépeket az SAP-alkalmazások kiszolgálóihoz, és helyezze őket a saját, dedikált rendelkezésre állási csoportba.
2. Telepítsen dedikált virtuális gépeket az adatbázis-kezelő fürt számára, és helyezze őket a saját, dedikált rendelkezésre állási csoportba.


## <a name="install-the-second-sap-sid2-netweaver-system"></a>A második SAP SID2 NetWeaver-szolgáltatás telepítése

A második SAP SID2 rendszer telepítésének teljes folyamatát a Windows rendszerű [virtuális gépek magas rendelkezésre állású SAP NetWeaver fő útmutatója][sap-ha-guide-9]ismerteti.

A magas szintű eljárás a következő:

1. [Telepítse az SAP első fürtcsomópont-csomópontját][sap-ha-guide-9.1.2].  
 Ebben a lépésben telepíti az SAP-t egy magas rendelkezésre állású ASCS/SCS-példánnyal a **meglévő WSFC-fürt 1. csomópontján**.

2. [Módosítsa az ASCS/SCS-példány SAP-profilját][sap-ha-guide-9.1.3].

3. [Állítson be egy mintavételi portot][sap-ha-guide-9.1.4].  
 Ebben a lépésben egy SAP-fürterőforrás SAP-SID2-IP mintavételi portját konfigurálja a PowerShell használatával. Hajtsa végre ezt a konfigurációt az egyik SAP ASCS-/SCS-fürt csomópontjain.

4. [Az adatbázis példányának telepítése] [SAP-ha-Guide-9,2].  
 Ebben a lépésben telepíti az adatbázis-kezelőt egy dedikált WSFC-fürtre.

5. [A második fürtcsomópont telepítése] [SAP-ha-Guide-9,3].  
 Ebben a lépésben telepíti az SAP-t egy magas rendelkezésre állású ASCS/SCS-példánnyal a meglévő WSFC-fürt 2. csomópontján.

6. Nyissa meg a Windows tűzfal portjait az SAP ASCS/SCS-példányhoz és a ProbePort.  
 Mindkét, az SAP ASCS/SCS-példányokhoz használt fürtcsomóponton megnyitja az SAP ASCS/SCS által használt összes Windows tűzfal-portot. Ezek a portok a [magas rendelkezésre állású SAP NetWeaver Windows rendszerű virtuális gépeken című útmutatójában][sap-ha-guide-8.8]találhatók.  
 Nyissa meg az Azure belső terheléselosztó mintavételi portját is, amely a forgatókönyvben 62350.

7. [Módosítsa az SAP ERS Windows-szolgáltatás példányának indítási típusát][sap-ha-guide-9.4].

8. [Telepítse az SAP Primary Application Servert][sap-ha-guide-9.5] az új dedikált virtuális gépre.

9. [Telepítse az SAP további alkalmazáskiszolgáló szolgáltatást][sap-ha-guide-9.6] az új dedikált virtuális gépre.

10. [Tesztelje az SAP ASCS/SCS instance feladatátvételi és SIOS replikációját][sap-ha-guide-10].

## <a name="next-steps"></a>Következő lépések

- [Hálózati korlátok: Azure Resource Manager][networking-limits-azure-resource-manager]
- [Több VIP Azure Load Balancer][load-balancer-multivip-overview]
- [Útmutató a magas rendelkezésre állású SAP NetWeaver Windows rendszerű virtuális gépeken való üzemeltetéséhez][sap-ha-guide]

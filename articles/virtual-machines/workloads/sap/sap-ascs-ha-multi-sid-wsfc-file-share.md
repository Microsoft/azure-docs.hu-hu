---
title: SAP ASCS/SCS instance multi-SID magas rendelkezésre állás a Windows Server feladatátvételi fürtszolgáltatással és fájlmegosztás az Azure-ban | Microsoft Docs
description: Több SID magas rendelkezésre állása SAP ASCS/SCS-példányokhoz Windows Server feladatátvételi fürtszolgáltatással és fájlmegosztás az Azure-ban
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
ms.date: 08/24/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 30f2e0e96f11203633b756a320d3da503686ed9f
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102504490"
---
# <a name="sap-ascsscs-instance-multi-sid-high-availability-with-windows-server-failover-clustering-and-file-share-on-azure"></a>SAP ASCS/SCS instance multi-SID magas rendelkezésre állás a Windows Server feladatátvételi fürtszolgáltatással és fájlmegosztás az Azure-ban

> ![Windows operációs rendszer][Logo_Windows] Windows
>

Több virtuális IP-címet is kezelhet egy [belső Azure Load Balancer][load-balancer-multivip-overview]használatával. 

Ha rendelkezik SAP-telepítéssel, a belső terheléselosztó használatával létrehozhat egy Windows-fürtöt az SAP Central Services (ASCS/SCS) példányaihoz.

Ebből a cikkből megtudhatja, hogyan helyezhet át egyetlen ASCS/SCS-telepítésről egy SAP multi-SID konfigurációra úgy, hogy további SAP ASCS/SCS fürtözött példányokat telepít egy meglévő Windows Server feladatátvételi fürtszolgáltatási (WSFC) fürtre a **fájlmegosztás** használatával. A folyamat befejezésekor konfigurált egy SAP multi-SID-fürtöt.

> [!NOTE]
>
> Ez a funkció csak a Azure Resource Manager telepítési modellben érhető el.
>
>Az egyes Azure-beli belső terheléselosztó esetében korlátozva van a magánhálózati előtér-IP-címek száma.
>
>Az egyes WSFC-fürtökben lévő SAP ASCS/SCS-példányok maximális száma megegyezik az egyes Azure-beli belső terheléselosztó számára elérhető saját előtér-IP-címek maximális számával.
>
> Az ebben a dokumentációban bemutatott konfiguráció még nem támogatott [Azure Availability Zones](../../../availability-zones/az-overview.md)
> 

A terheléselosztó korlátaival kapcsolatos további információkért tekintse meg a [hálózatkezelési korlátok: Azure Resource Manager][networking-limits-azure-resource-manager]a "privát ELŐTÉR-IP/terheléselosztó" című szakaszt. Vegye fontolóra az Azure [standard Load BALANCER SKU](../../../load-balancer/load-balancer-standard-availability-zones.md) használatát is az Azure Load Balancer alapszintű SKU-jának használata helyett.

## <a name="prerequisites"></a>Előfeltételek

Már konfigurált egy WSFC-fürtöt egy SAP ASCS/SCS-példányhoz a **fájlmegosztás** használatával, ahogy az ebben a diagramban látható.

![1. ábra: SAP-ASCS/SCS-példány és SOFS két fürtben üzembe helyezve][sap-ha-guide-figure-8007]

_**1. ábra:** Egy SAP ASCS/SCS-példány és egy két fürtön üzembe helyezett SOFS_

> [!IMPORTANT]
> A telepítésnek meg kell felelnie a következő feltételeknek:
> * Az SAP-ASCS/SCS-példányoknak ugyanazt a WSFC-fürtöt kell megosztaniuk.
> * A különböző SAP-SID-fájlokhoz tartozó más SAP-gazdagépekhez tartozó fájlmegosztás-megosztásoknak azonos SOFS-fürtön kell lenniük.
> * Minden adatbázis-kezelő rendszer (adatbázisok) biztonsági azonosítójának saját dedikált WSFC-fürttel kell rendelkeznie.
> * Az egyik SAP-rendszerbiztonsági azonosítóhoz tartozó SAP-alkalmazás-kiszolgálóknak saját dedikált virtuális gépekkel kell rendelkezniük.
> * A sorba helyezni replikációs kiszolgáló 1 és a sorba helyezni replikációs kiszolgáló 2 együttese nem támogatott ugyanazon a fürtön.  

## <a name="sap-ascsscs-multi-sid-architecture-with-file-share"></a>SAP ASCS/SCS multi-SID architektúra fájlmegosztás esetén

A cél az, hogy több SAP Advanced Business Application Programming (ASCS) vagy SAP Java (SCS) fürtözött példányt telepítsen ugyanabban a WSFC-fürtben, ahogy az itt látható: 

![2. ábra: SAP multi-SID konfiguráció két fürtben][sap-ha-guide-figure-8008]

_**2. ábra:** SAP multi-SID-konfiguráció két fürtben_

Egy további **SAP \<SID2>** -rendszer telepítése azonos egy rendszer telepítésével \<SID> . Két további előkészítési lépésre van szükség a ASCS/SCS-fürtön, valamint a fájlmegosztás SOFS-fürtön.

## <a name="prepare-the-infrastructure-for-an-sap-multi-sid-scenario"></a>Az infrastruktúra előkészítése egy több SID-es SAP-forgatókönyvhöz

### <a name="prepare-the-infrastructure-on-the-domain-controller"></a>Az infrastruktúra előkészítése a tartományvezérlőn

Hozza létre a következő tartományi csoportot: **\<Domain> \ SAP_ \<SID2> _GlobalAdmin**, például a \<SID2> = PR2. A tartományi csoport neve \<Domain> \ SAP_PR2_GlobalAdmin.

### <a name="prepare-the-infrastructure-on-the-ascsscs-cluster"></a>Az infrastruktúra előkészítése a ASCS/SCS-fürtön

Elő kell készítenie az infrastruktúrát a meglévő ASCS/SCS-fürtön egy második SAP esetében \<SID> :

* Hozzon létre egy virtuális állomásnevet a fürtözött SAP ASCS/SCS-példányhoz a DNS-kiszolgálón.
* Adjon hozzá egy IP-címet egy meglévő Azure belső terheléselosztó számára a PowerShell használatával.

Ezek a lépések az [infrastruktúra-előkészítés egy SAP multi-SID forgatókönyv][sap-ascs-ha-multi-sid-wsfc-shared-disk-infrast-prepare]esetén című témakörben találhatók.


### <a name="prepare-the-infrastructure-on-an-sofs-cluster-by-using-the-existing-sap-global-host"></a>Az infrastruktúra előkészítése SOFS-fürtön a meglévő SAP globális gazdagép használatával

Újra felhasználhatja az \<SAPGlobalHost> első SAP-rendszeren meglévő és Volume1 is \<SID1> .

![3. ábra: a többszörös SID SOFS megegyezik az SAP globális gazdagép nevével][sap-ha-guide-figure-8014]

_**3. ábra:** A többszörös SID-SOFS megegyezik az SAP globális gazdagép nevével_

> [!IMPORTANT]
>A második **SAP \<SID2>** -rendszer esetében ugyanaz a Volume1 és ugyanaz a **\<SAPGlobalHost>** hálózatnév használatos.
>Mivel már beállította a **SAPMNT** -t a különböző SAP-rendszerek megosztási neveként, **\<SAPGlobalHost>** a hálózat nevének újrafelhasználásához ugyanazt a **Volume1** kell használnia.
>
>A globális gazdagép elérési útja a \<SID2> C:\ClusterStorage \\ **Volume1**\usr\sap \<SID2> \SYS\.
>

A \<SID2> rendszerhez elő kell készítenie az SAP globális gazdagépet. \SYS \. . mappát a SOFS-fürtön.

A-példányhoz tartozó SAP globális gazdagép előkészítéséhez \<SID2> futtassa a következő PowerShell-parancsfájlt:


```powershell
##################
# SAP multi-SID
##################

$SAPSID2 = "PR2"
$DomainName2 = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName2 = "$DomainName2\SAP_" + $SAPSID2 + "_GlobalAdmin"

# SAP ASCS/SCS cluster nodes
$ASCSCluster2Node1 = "ja1-ascs-0"
$ASCSCluster2Node2 = "ja1-ascs-1"

# Define the SAP ASCS/SCS cluster node computer objects
$ASCSCluster2ObjectNode1 = "$DomainName2\$ASCSCluster2Node1$"
$ASCSCluster2ObjectNode2 = "$DomainName2\$ASCSCluster2Node2$"

# Create usr\sap\.. folders on CSV
$SAPGlobalFolder2 = "C:\ClusterStorage\Volume1\usr\sap\$SAPSID2\SYS"
New-Item -Path $SAPGlobalFolder2 -ItemType Directory

# Add permissions for the SAP SID2 system
Grant-SmbShareAccess -Name sapmnt -AccountName $SAPSIDGlobalAdminGroupName2, $ASCSCluster2ObjectNode1, $ASCSCluster2ObjectNode2 -AccessRight Full -Force


$UsrSAPFolder = "C:\ClusterStorage\Volume1\usr\sap\"

# Set file and folder security
$Acl = Get-Acl $UsrSAPFolder

# Add the security object of the SAP_<sid>_GlobalAdmin group
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($SAPSIDGlobalAdminGroupName2,"FullControl", 'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add the security object of the clusternode1$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSCluster2ObjectNode1,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add the security object of the clusternode2$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSCluster2ObjectNode2,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Set security
Set-Acl $UsrSAPFolder $Acl -Verbose
```

### <a name="prepare-the-infrastructure-on-the-sofs-cluster-by-using-a-different-sap-global-host"></a>Az infrastruktúra előkészítése a SOFS-fürtön egy másik SAP globális gazdagép használatával

Megadhatja a második SOFS (például a második SOFS-fürt szerepkört **\<SAPGlobalHost2>** és egy másik **indítása kötet2** a másodikhoz **\<SID2>** ).

![4. ábra: a többszörös SID SOFS ugyanaz, mint az SAP globális állomásneve 2][sap-ha-guide-figure-8015]

_**4. ábra:** A többszörös SID SOFS ugyanaz, mint az SAP globális állomásneve 2_

A második SOFS-szerepkör a használatával történő létrehozásához \<SAPGlobalHost2> futtassa a következő PowerShell-parancsfájlt:

```powershell
# Create SOFS with SAP Global Host Name 2
$SAPGlobalHostName = "sapglobal2"
Add-ClusterScaleOutFileServerRole -Name $SAPGlobalHostName
```

Hozza létre a második **indítása kötet2**. Futtassa ezt a PowerShell-parancsfájlt:

```powershell
New-Volume -StoragePoolFriendlyName S2D* -FriendlyName SAPPR2 -FileSystem CSVFS_ReFS -Size 5GB -ResiliencySettingName Mirror
```

![5. ábra: a Feladatátvevőfürt-kezelő második indítása kötet2][sap-ha-guide-figure-8016]

_**5. ábra:** Második indítása kötet2 a Feladatátvevőfürt-kezelő_

Hozzon létre egy SAP globális mappát a másodikhoz \<SID2> , és állítsa be a fájl biztonságát.

Futtassa ezt a PowerShell-parancsfájlt:

```powershell
# Create a folder for <SID2> on a second Volume2 and set file security
$SAPSID = "PR2"
$DomainName = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName = "$DomainName\SAP_" + $SAPSID + "_GlobalAdmin"

# SAP ASCS/SCS cluster nodes
$ASCSClusterNode1 = "ascs-1"
$ASCSClusterNode2 = "ascs-2"

# Define SAP ASCS/SCS cluster node computer objects
$ASCSClusterObjectNode1 = "$DomainName\$ASCSClusterNode1$"
$ASCSClusterObjectNode2 = "$DomainName\$ASCSClusterNode2$"

# Create usr\sap\.. folders on CSV
$SAPGlobalFolder = "C:\ClusterStorage\Volume2\usr\sap\$SAPSID\SYS"
New-Item -Path $SAPGlobalFOlder -ItemType Directory

$UsrSAPFolder = "C:\ClusterStorage\Volume2\usr\sap\"

# Set file and folder security
$Acl = Get-Acl $UsrSAPFolder

# Add the file security object of the SAP_<sid>_GlobalAdmin group
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($SAPSIDGlobalAdminGroupName,"FullControl", 'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add the security object of the clusternode1$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSClusterObjectNode1,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add the security object of the clusternode2$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSClusterObjectNode2,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Set security
Set-Acl $UsrSAPFolder $Acl -Verbose
```

Ha SAPMNT-fájlmegosztást szeretne létrehozni a indítása kötet2 *\<SAPGlobalHost2>* -ben a második SAP állomásneveként \<SID2> , indítsa el a **fájlmegosztás hozzáadása** varázslót Feladatátvevőfürt-kezelő.

Kattintson a jobb gombbal a **saoglobal2** SOFS, majd válassza a **fájlmegosztás hozzáadása** elemet.

![6. ábra: a "fájlmegosztás hozzáadása" varázsló elindítása][sap-ha-guide-figure-8017]

_**6. ábra:** A "fájlmegosztás hozzáadása" varázsló elindítása_

<br>

![7. ábra: "SMB-megosztás kiválasztása – gyors"][sap-ha-guide-figure-8018]

_**7. ábra:** "SMB-megosztás – gyors" kiválasztása_

<br>

![8. ábra: válassza a "sapglobalhost2" lehetőséget, és adja meg a indítása kötet2 elérési útját][sap-ha-guide-figure-8019]

_**8. ábra:** Válassza a "sapglobalhost2" lehetőséget, és adja meg a indítása kötet2 elérési útját_

<br>

![9. ábra: fájlmegosztási név beállítása "sapmnt" értékre][sap-ha-guide-figure-8020]

_**9. ábra:** Fájlmegosztási név beállítása "sapmnt" értékre_

<br>

![10. ábra: az összes beállítás letiltása][sap-ha-guide-figure-8021]

_**10. ábra:** Az összes beállítás letiltása_

<br>

*Teljes hozzáférés* engedélyek kiosztása fájlokhoz és sapmnt-megosztáshoz a következőhöz:
* A **SAP_ \<SID> _GlobalAdmin** tartományi felhasználói csoport
* ASCS/SCS-fürtcsomópontok **ASCS-$1** és **ASCS-$2 számítógép-** objektuma

![11. ábra: teljes hozzáférés engedélyek kiosztása a felhasználói csoport és számítógépfiókok számára][sap-ha-guide-figure-8022]

_**11. ábra:** A "teljes hozzáférés" társítása a felhasználói csoport és számítógépfiókok számára_

<br>

![12. ábra: a "létrehozás" kiválasztása][sap-ha-guide-figure-8023]

_**12. ábra:** Válassza a létrehozás lehetőséget_

<br>

![13. ábra: a sapglobal2-gazdagéphez és a indítása kötet2-hoz kötött második sapmnt létrejött.][sap-ha-guide-figure-8024]

_**13. ábra:** A sapglobal2-gazdagéphez és a indítása kötet2-hoz kötött második sapmnt létrejön._

<br>

## <a name="install-sap-netweaver-multi-sid"></a>Az SAP NetWeaver multi-SID telepítése

### <a name="install-sap-sid2-ascsscs-and-ers-instances"></a>SAP \<SID2> ASCS/SCS és ERS példányok telepítése

Kövesse ugyanazt a telepítési és konfigurációs lépéseket, ahogy az egy SAP-hoz korábban leírtak szerint \<SID> .

### <a name="install-dbms-and-sap-application-servers"></a>Az adatbázis-kezelő és az SAP-alkalmazáskiszolgáló telepítése
Telepítse az adatbázis-kezelőt és az SAP-alkalmazásokat a korábban leírtak szerint.

## <a name="next-steps"></a>Következő lépések

* [ASCS-/SCS-példány telepítése megosztott lemezek nélküli feladatátvevő fürtön][sap-official-ha-file-share-document]: Ha egy adott fájlmegosztás esetében hivatalos SAP irányelvek

* [Közvetlen tárolóhelyek a Windows Server 2016-ben][s2d-in-win-2016]

* [Kibővíthető fájlkiszolgáló az alkalmazásadatok áttekintéséhez][sofs-overview]

* [Újdonságok a Storage szolgáltatásban a Windows Server 2016-ben][new-in-win-2016-storage]


[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1869038]:https://launchpad.support.sap.com/#/notes/1869038
[2287140]:https://launchpad.support.sap.com/#/notes/2287140
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[sap-official-ha-file-share-document]:https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html
[s2d-in-win-2016]:https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview
[sofs-overview]:https://technet.microsoft.com/library/hh831349(v=ws.11).aspx
[new-in-win-2016-storage]:https://docs.microsoft.com/windows-server/storage/whats-new-in-storage

[sap-installation-guides]:http://service.sap.com/instguides
[sap-installation-guides-file-share]:https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html
[networking-limits-azure-resource-manager]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md
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

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ascs-ha-multi-sid-wsfc-shared-disk]:sap-ascs-ha-multi-sid-wsfc-shared-disk.md
[sap-ascs-ha-multi-sid-wsfc-shared-disk-infrast-prepare]:sap-ascs-ha-multi-sid-wsfc-shared-disk.md#25e358f8-92e5-4e8d-a1e5-df7580a39cb0
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

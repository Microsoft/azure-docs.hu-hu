---
title: Fürt SAP ASCS/SCS-példánya a WSFC megosztott lemez használatával az Azure-ban | Microsoft Docs
description: Ismerje meg, hogyan lehet egy Windows feladatátvevő fürtön egy SAP ASCS/SCS-példányt fürtözött megosztott lemez használatával fürtbe felvenni.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: f6fb85f8-c77a-4af1-bde8-1de7e4425d2e
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c903cf06981e1336ae30942775de11d09bb1299b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101675363"
---
# <a name="cluster-an-sap-ascsscs-instance-on-a-windows-failover-cluster-by-using-a-cluster-shared-disk-in-azure"></a>Az SAP ASCS/SCS-példányok fürtözése Windows feladatátvevő fürtön az Azure-ban megosztott fürtözött lemez használatával

> ![Windows operációs rendszer][Logo_Windows] Windows
>

A Windows Server feladatátvételi fürtszolgáltatás a magas rendelkezésre állású SAP ASCS/SCS-telepítés és az adatbázis-kezelő rendszer alapja a Windows rendszerben.

A feladatátvevő fürt 1 + n-független kiszolgálók (csomópontok) csoportja, amelyek együttműködve fokozzák az alkalmazások és szolgáltatások rendelkezésre állását. Csomópont meghibásodása esetén a Windows Server feladatátvételi fürtszolgáltatás kiszámítja a hibák számát, és továbbra is kifogástalan állapotú fürtöt biztosít az alkalmazások és szolgáltatások biztosításához. A feladatátvételi fürtszolgáltatás eléréséhez különböző kvórum módok közül választhat.

## <a name="prerequisites"></a>Előfeltételek
A cikkben szereplő feladatok elkezdése előtt tekintse át a következő cikket:

* [Azure Virtual Machines magas rendelkezésre állású architektúra és forgatókönyvek az SAP NetWeaver-hoz][sap-high-availability-architecture-scenarios]


## <a name="windows-server-failover-clustering-in-azure"></a>Windows Server feladatátvételi fürtszolgáltatás az Azure-ban

A Windows Server feladatátvételi fürtszolgáltatás az Azure Virtual Machines további konfigurációs lépéseket igényel. Fürt létrehozásakor több IP-címet és virtuális állomásnevet kell beállítania az SAP ASCS/SCS-példányhoz.

### <a name="name-resolution-in-azure-and-the-cluster-virtual-host-name"></a>Névfeloldás az Azure-ban és a fürt virtuális gazdagépének neve

Az Azure Cloud platform nem nyújt lehetőséget a virtuális IP-címek, például a lebegő IP-címek konfigurálására. Egy alternatív megoldásra van szükség egy virtuális IP-cím beállításához, hogy elérje a fürt erőforrását a felhőben. 

A Azure Load Balancer szolgáltatás *belső Load balancert* biztosít az Azure-hoz. A belső terheléselosztó révén az ügyfelek a fürt virtuális IP-címén keresztül érik el a fürtöt. 

Helyezze üzembe a belső terheléselosztó-t a fürtcsomópontok tartalmazó erőforráscsoporthoz. Ezután konfigurálja az összes szükséges port továbbítási szabályt a belső Load Balancer mintavételi portjaival. Az ügyfelek a virtuális állomásnév használatával kapcsolódhatnak. A DNS-kiszolgáló feloldja a fürt IP-címét, és a belső terheléselosztó kezeli a port továbbítását a fürt aktív csomópontjára.

> [!IMPORTANT]
> A lebegő IP-címek nem támogatottak a terheléselosztási helyzetekben a hálózati adapter másodlagos IP-konfigurációjában. További részletek: az [Azure Load Balancer korlátozásai](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Ha a virtuális gép további IP-címére van szüksége, helyezzen üzembe egy második hálózati adaptert.  

![1. ábra: Windows feladatátvételi fürtszolgáltatás konfigurálása az Azure-ban megosztott lemez nélkül][sap-ha-guide-figure-1001]

_Windows Server feladatátvételi fürtszolgáltatás konfigurálása az Azure-ban megosztott lemez nélkül_

### <a name="sap-ascsscs-ha-with-cluster-shared-disks"></a>SAP ASCS/SCS HA fürt megosztott lemezekkel
A Windows rendszerben az SAP ASCS/SCS példány az SAP Central Services, az SAP Message Server, a sorba helyezni Server folyamatai és az SAP globális gazdagép fájljait tartalmazza. Az SAP globális gazdagép fájljai a teljes SAP-rendszer központi fájljait tárolják.

Az SAP-ASCS/SCS-példányok a következő összetevőket tartalmazják:

* SAP központi szolgáltatások:
    * Két folyamat, egy üzenet-és sorba helyezni-kiszolgáló, valamint egy \<ASCS/SCS virtual host name> , amely a két folyamat elérésére szolgál.
    * Fájl szerkezete: S:\usr\sap \\ &lt; SID &gt; \ ASCS/SCS\<instance number\>


* SAP globális gazdagép fájljai:
  * Fájl szerkezete: S:\usr\sap \\ &lt; SID &gt; \SYS \. ..
  * A sapmnt fájlmegosztást, amely \\ &lt; &gt; a következő UNC elérési út használatával engedélyezi a hozzáférést a globális S:\usr\sap SID \SYS \. .. fájlokhoz:

    \\\\<ASCS/SCS virtuális gazdagép neve \> \Sapmnt \\ &lt; SID &gt; \SYS \. ..


![2. ábra: a folyamatok, a fájlok szerkezete és a globális gazdagép sapmnt egy SAP ASCS/SCS-példányon][sap-ha-guide-figure-8001]

_Egy SAP ASCS/SCS-példány folyamatai, a fájl szerkezete és a globális gazdagép sapmnt_

Magas rendelkezésre állási beállítás esetén az SAP ASCS/SCS-példányok fürtje. A *fürtözött megosztott lemezeket* (a példában a meghajtót) a SAP ASCS/SCS és az SAP globális gazdagép fájljainak elhelyezésére használjuk.

![3. ábra: SAP ASCS/SCS HA-architektúra megosztott lemezzel][sap-ha-guide-figure-8002]

_SAP ASCS/SCS HA architektúra megosztott lemezzel_


A sorba helyezni Server Replication 1 architektúrával:
* Ugyanezt \<ASCS/SCS virtual host name> használja az SAP-üzenetek és a sorba helyezni-kiszolgáló folyamatainak, valamint az SAP globális gazdagép-fájlok elérésére a sapmnt-fájlmegosztás segítségével.
* Ugyanaz a fürt megosztott lemezmeghajtója van megosztva egymás között.  

A sorba helyezni Server Replication 2 architektúrával: 
* Ugyanezt \<ASCS/SCS virtual host name> használja az SAP Message Server folyamat és az SAP globális gazdagép fájljainak elérésére a sapmnt-fájlmegosztás segítségével.
* Ugyanaz a fürt megosztott lemezmeghajtója van megosztva egymás között.
* \<ERS virtual host name>A sorba helyezni-kiszolgáló folyamata külön érhető el  

![4. ábra: SAP ASCS/SCS HA architektúra megosztott lemezzel][sap-ha-guide-figure-8003]

_SAP ASCS/SCS HA architektúra megosztott lemezzel_

#### <a name="shared-disk-and-enqueue-replication-server"></a>Megosztott lemez-és sorba helyezni replikációs kiszolgáló 

1. A megosztott lemez a sorba helyezni Server Replication 1 architektúrával támogatott, ahol a sorba helyezni-replikációs kiszolgáló (ERS) példánya:   

   - nem fürtözött
   - felhasználás `localhost` neve
   - helyi lemezeken van telepítve az egyes fürtcsomópontok esetében

2. A megosztott lemez a sorba helyezni Server Replication 2 architektúrával is támogatott, ahol a sorba helyezni Replication Server 2 (ERS2) példánya:  

   - fürtözött
   - dedikált virtuális/hálózati gazdagép nevét használja
   - szüksége van az ERS virtuális állomásnév IP-címére, amelyet az Azure belső Load Balancer kell konfigurálni, az (A) SCS IP-címén kívül
   - minden fürtözött csomóponton **helyi lemezekre** van telepítve, ezért nincs szükség megosztott lemezre

   > [!TIP]
   > További információt az 1. és 2. sorba helyezni replikációs kiszolgálóról (ERS1 és ERS2) itt talál:  
   > [Sorba helyezni replikációs kiszolgáló egy Microsoft feladatátvevő fürtben](https://help.sap.com/viewer/3741bfe0345f4892ae190ee7cfc53d4c/CURRENT_VERSION_SWPM20/en-US/8abd4b52902d4b17a105c2fabdf5c0cf.html)  
   > [Új sorba helyezni replikátor a feladatátvevő fürt környezetében](https://blogs.sap.com/2019/03/19/new-enqueue-replicator-in-failover-cluster-environments/)  

#### <a name="options-for-shared-disk-in-azure-for-sap-workloads"></a>Az Azure-beli megosztott lemez lehetőségei az SAP számítási feladatokhoz

Az Azure-beli Windows feladatátvevő fürtben két lehetőség van megosztott lemezre:

- [Azure Shared](../../disks-shared.md) Disks – funkció, amely lehetővé teszi az Azure felügyelt lemez egyidejű csatlakoztatását több virtuális géphez. 
- A harmadik féltől származó szoftverek [SIOS DataKeeper cluster Edition](https://us.sios.com/products/datakeeper-cluster) használatával hozzon létre egy tükrözött tárolót, amely szimulálja a fürt megosztott tárolóját. 

A megosztott lemez technológiájának kiválasztásakor vegye figyelembe a következő szempontokat:

**Azure megosztott lemez SAP számítási feladatokhoz**
- Lehetővé teszi, hogy az Azure felügyelt lemezeket egyszerre több virtuális géphez csatolja anélkül, hogy további szoftvereket kellene fenntartania és működnie 
- Egyetlen tároló fürtön egyetlen Azure-beli megosztott lemezzel fog működni. Ez hatással van az SAP-megoldás megbízhatóságára.
- Jelenleg az egyetlen támogatott üzemelő példány a rendelkezésre állási csoporton belül az Azure Shared Premium Disk. Az Azure-beli megosztott lemez nem támogatott a zónabeli telepítésben.     
- Győződjön meg arról, hogy az Azure Premium-lemezt a [prémium SSD tartományokban](../../disks-shared.md#disk-sizes) megadott minimális méretű lemezzel szeretné kiépíteni, hogy képes legyen a szükséges számú virtuális gép egyidejű csatolására (általában 2 az SAP ASCS Windows feladatátvevő fürt esetén). 
- Az Azure Shared Ultra Disk nem támogatott az SAP-munkaterhelések esetében, mivel nem támogatja az üzembe helyezést a rendelkezésre állási csoportokban vagy a zónabeli üzembe helyezésben.  
 
**SIOS**
- A SIOS megoldás valós idejű szinkron adatreplikálást biztosít két lemez között
- Ha a SIOS-megoldás két felügyelt lemezzel működik, és ha rendelkezésre állási csoportokat vagy rendelkezésre állási zónákat használ, a felügyelt lemezek különböző tárolási fürtökön fognak megjelenni. 
- A rendelkezésre állási zónákban való üzembe helyezés támogatott
- Külső gyártótól származó szoftver telepítésére és üzemeltetésére van szükség, amelyet Emellett meg kell vásárolnia

### <a name="shared-disk-using-azure-shared-disk"></a>Megosztott lemez az Azure Shared Disk használatával

A Microsoft Azure-beli [megosztott lemezeket](../../disks-shared.md)kínál, amelyek segítségével megosztott lemezes lehetőséggel implementálhatja az SAP ASCS/SCS magas rendelkezésre állását.

#### <a name="prerequisites-and-limitations"></a>Előfeltételek és korlátozások

Jelenleg az Azure prémium SSD-lemezeket Azure-beli megosztott lemezként használhatja az SAP ASCS/SCS-példányhoz. Jelenleg a következő korlátozások vannak érvényben:

-  Az [Azure Ultra Disk](../../disks-types.md#ultra-disk) nem támogatott Azure-beli megosztott lemezként az SAP-munkaterhelésekhez. Jelenleg nem helyezhetők üzembe Azure-beli virtuális gépek a rendelkezésre állási csoportba tartozó Azure Ultra Disk használatával
-  Prémium SSD lemezzel rendelkező Azure-beli [megosztott lemez](../../disks-shared.md) csak a rendelkezésre állási csoportba tartozó virtuális gépek esetében támogatott. Availability Zones üzemelő példányban nem támogatott. 
-  Az Azure Shared Disk Value [maxShares](../../disks-shared-enable.md?tabs=azure-cli#disk-sizes) meghatározza, hogy hány fürtcsomópont használhatja a megosztott lemezt. Az SAP ASCS/SCS-példány esetében általában két csomópontot fog konfigurálni a Windows feladatátvevő fürtben, ezért a értékét `maxShares` kettőre kell beállítani.
-  Az összes SAP-ASCS/SCS-fürt virtuális gépnek ugyanabban az Azure-beli [közelségi csoportban](../../windows/proximity-placement-groups.md)kell lennie.   
   Bár a Windows-fürt virtuális gépeket a rendelkezésre állási csoportba helyezheti a PPG nélkül, az Azure-ban megosztott lemezzel, a PPG biztosítja az Azure-beli megosztott lemezek és a fürt virtuális gépei közel fizikai közelségét, így a virtuális gépek és a tárolási réteg közötti alacsonyabb késést is biztosít.    

Az Azure Shared Disk korlátozásával kapcsolatos további részletekért tekintse át az Azure Shared Disk dokumentációjának [korlátozások](../../disks-shared.md#limitations) című szakaszát.

> [!IMPORTANT]
> Ha az Azure megosztott lemezzel telepíti az SAP ASCS/SCS Windows feladatátvevő fürtöt, vegye figyelembe, hogy az üzemelő példány egyetlen megosztott lemezzel fog működni egyetlen tároló fürtben. Az SAP-ASCS/SCS-példány hatással lenne a Storage-fürttel kapcsolatos problémák esetén, ahol az Azure-beli megosztott lemez telepítve van.    

> [!TIP]
> Tekintse át az SAP [NetWeaver az Azure tervezési útmutatójában](./planning-guide.md) és az [Azure Storage útmutatót az SAP](./planning-guide-storage.md) számítási feladataihoz az SAP üzembe helyezésének megtervezése során.

### <a name="supported-os-versions"></a>Támogatott operációsrendszer-verziók

A Windows Server 2016 és a 2019 egyaránt támogatott (a legújabb adatközpont-lemezképek használata).

Javasoljuk, hogy a **Windows Server 2019 Datacenter** használatát a következőképpen ajánljuk:
- A Windows 2019 feladatátvételi fürtszolgáltatás Azure-beli szolgáltatás
- Az Azure-gazdagép karbantartásával és jobb megismerésével bővült az Azure-beli menetrend eseményeinek monitorozása.
- Az elosztott hálózat nevét is használhatja (ez az alapértelmezett beállítás). Ezért nem kell dedikált IP-címet adni a fürt hálózati neveként. Ezt az IP-címet nem kell konfigurálnia az Azure belső Load Balancerján. 

### <a name="shared-disks-in-azure-with-sios-datakeeper"></a>Megosztott lemezek az Azure-ban a SIOS DataKeeper

A megosztott lemez egy másik lehetősége, hogy külső gyártótól származó szoftverek SIOS DataKeeper cluster Edition használatával hozzon létre egy tükrözött tárolót, amely szimulálja a fürt megosztott tárolóját. A SIOS megoldás valós idejű szinkron adatreplikálást biztosít.

Megosztott lemez erőforrásának létrehozása fürthöz:

1. Csatoljon egy további lemezt a Windows-fürt konfigurációjában lévő összes virtuális géphez.
2. Futtassa a SIOS DataKeeper cluster Editiont mindkét virtuálisgép-csomóponton.
3. Konfigurálja a SIOS DataKeeper-fürt kiadását úgy, hogy az a forrás virtuális gépről a további lemezhez csatolt kötet tartalmát a cél virtuális gép további lemezhez csatolt kötetére használja. A SIOS DataKeeper elküldi a forrás-és a célként megadott helyi köteteket, majd a Windows Server feladatátvételi fürtszolgáltatást egy megosztott lemezként jeleníti meg.

További információ a [SIOS DataKeeper](https://us.sios.com/products/datakeeper-cluster/).

![5. ábra: Windows Server feladatátvételi fürtszolgáltatás konfigurálása az Azure-ban a SIOS DataKeeper][sap-ha-guide-figure-1002]

_Windows feladatátvételi fürtszolgáltatás konfigurálása az Azure-ban a SIOS DataKeeper_

> [!NOTE]
> A magas rendelkezésre álláshoz nincs szükség megosztott lemezekre, néhány adatbázis-kezelő termékkel, például a SQL Serversal. SQL Server a AlwaysOn replikálja az adatbázis-kezelői adatait és a naplófájlokat az egyik fürtcsomópont helyi lemezéről egy másik fürtcsomópont helyi lemezére. Ebben az esetben a Windows-fürt konfigurációjának nincs szüksége megosztott lemezre.
>

## <a name="next-steps"></a>Következő lépések

* [Készítse elő az Azure-infrastruktúrát az SAP-hez a Windows feladatátvevő fürt és az SAP ASCS/SCS-példány megosztott lemezének használatával][sap-high-availability-infrastructure-wsfc-shared-disk]

* [SAP NetWeaver HA telepítése Windows feladatátvevő fürtön és megosztott lemezen egy SAP ASCS/SCS-példányhoz][sap-high-availability-installation-wsfc-shared-disk]


[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md

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

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP multi-SID magas rendelkezésre állási konfiguráció)

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

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png


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

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md
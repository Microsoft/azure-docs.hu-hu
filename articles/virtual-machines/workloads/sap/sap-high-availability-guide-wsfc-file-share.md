---
title: Fürt SAP ASCS/SCS on WSFC az Azure fájlmegosztás használatával | Microsoft Docs
description: Ismerje meg, hogyan fürtözött SAP ASCS/SCS-példány egy Windows feladatátvevő fürtön egy fájlmegosztás használatával az Azure-ban.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/15/2021
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a51f874d09aebfcb2c0b73e0b484f68042d1bb6d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103496201"
---
# <a name="cluster-an-sap-ascsscs-instance-on-a-windows-failover-cluster-by-using-a-file-share-in-azure"></a>SAP ASCS-/SCS-példány fürthöz való fürtözése Windows feladatátvevő fürtön egy Azure-beli fájlmegosztás használatával

> ![Windows-embléma.][Logo_Windows] Windows
>

A Windows Server feladatátvételi fürtszolgáltatás a magas rendelkezésre állású SAP ASCS/SCS-telepítés és az adatbázis-kezelő rendszer alapja a Windows rendszerben.

A feladatátvevő fürt 1 + n független kiszolgálók (csomópontok) csoportja, amelyek együttműködve fokozzák az alkalmazások és szolgáltatások rendelkezésre állását. Csomópont meghibásodása esetén a Windows Server feladatátvételi fürtszolgáltatás kiszámítja a hibák számát, és továbbra is kifogástalan állapotú fürtöt biztosít az alkalmazások és szolgáltatások biztosításához. A feladatátvételi fürtszolgáltatás eléréséhez különböző kvórum módok közül választhat.

## <a name="prerequisites"></a>Előfeltételek
A cikkben ismertetett feladatok megkezdése előtt tekintse át ezt a cikket:

* [Azure Virtual Machines magas rendelkezésre állású architektúra és forgatókönyvek az SAP NetWeaver-hoz][sap-high-availability-architecture-scenarios]

> [!IMPORTANT]
> Az SAP ASCS/SCS-példányok egy fájlmegosztás használatával történő fürtözését az SAP NetWeaver 7,40 (és újabb) SAP kernel 7,49 (és újabb verziók) támogatják.
>


## <a name="windows-server-failover-clustering-in-azure"></a>Windows Server feladatátvételi fürtszolgáltatás az Azure-ban

Az operációs rendszer nélküli vagy a saját felhőalapú üzemelő példányokhoz képest az Azure Virtual Machines további lépéseket igényel a Windows Server feladatátvételi fürtszolgáltatás konfigurálásához. Fürt létrehozásakor több IP-címet és virtuális állomásnevet kell beállítania az SAP ASCS/SCS-példányhoz.

### <a name="name-resolution-in-azure-and-the-cluster-virtual-host-name"></a>Névfeloldás az Azure-ban és a fürt virtuális gazdagépének neve

Az Azure Cloud platform nem nyújt lehetőséget a virtuális IP-címek, például a lebegő IP-címek konfigurálására. Egy alternatív megoldásra van szükség egy virtuális IP-cím beállításához, hogy elérje a fürt erőforrását a felhőben. 

A Azure Load Balancer szolgáltatás *belső Load balancert* biztosít az Azure-hoz. A belső terheléselosztó révén az ügyfelek a fürt virtuális IP-címén keresztül érik el a fürtöt. 

Helyezze üzembe a belső terheléselosztó-t a fürtcsomópontok tartalmazó erőforráscsoporthoz. Ezután konfigurálja az összes szükséges port továbbítási szabályt a belső Load Balancer mintavételi portjaival. Az ügyfelek csatlakozhatnak a virtuális gazdagép nevével. A DNS-kiszolgáló feloldja a fürt IP-címét. A belső terheléselosztó a fürt aktív csomópontjára irányítja a port továbbítását.

![1. ábra: Windows Server feladatátvételi fürtszolgáltatás konfigurációja az Azure-ban megosztott lemez nélkül][sap-ha-guide-figure-1001]

_**1. ábra:** Windows Server feladatátvételi fürtszolgáltatás konfigurálása az Azure-ban megosztott lemez nélkül_

## <a name="sap-ascsscs-ha-with-file-share"></a>SAP ASCS/SCS HA fájlmegosztás

Az SAP új megközelítést fejlesztett ki, és egy alternatívát a fürt megosztott lemezei számára egy SAP ASCS/SCS-példány fürtözésére egy Windows feladatátvevő fürtön. A fürt megosztott lemezeinek használata helyett SMB-fájlmegosztást használhat az SAP globális gazdagép-fájljainak telepítéséhez.

> [!NOTE]
> Az SMB-fájlmegosztás egy alternatív megoldás, amellyel fürtözött megosztott lemezek is használhatók az SAP ASCS/SCS-példányok fürtözéséhez.  
>

Ez az architektúra a következő módokon jellemző:

* Az SAP központi szolgáltatásai (a saját fájl-struktúra, az üzenet-és a sorba helyezni-folyamatok esetében) eltérnek az SAP globális gazdagép fájljaitól.
* Az SAP központi szolgáltatásai egy SAP ASCS/SCS-példány alatt futnak.
* Az SAP ASCS/SCS-példány fürtözött, és a \<ASCS/SCS virtual host name\> virtuális gazdagép neve alapján érhető el.
* Az SAP globális fájljai az SMB-fájlmegosztás számára vannak elhelyezve, és a következő állomásnév használatával érhetők el \<SAP global host\> : \\ \\ &lt; SAP Global Host &gt; \sapmnt \\ &lt; SID &gt; \SYS \. ..
* Az SAP ASCS/SCS-példány egy helyi lemezre van telepítve a fürtcsomópontok között.
* A \<ASCS/SCS virtual host name\> hálózat neve eltér az &lt; SAP globális gazdagéptől &gt; .

![2. ábra: SAP ASCS/SCS HA architektúra SMB-fájlmegosztás esetén][sap-ha-guide-figure-8004]

_**2. ábra:** Új SAP ASCS/SCS HA architektúra SMB-fájlmegosztás esetén_

SMB-fájlmegosztás előfeltételei:

* SMB 3,0 (vagy újabb) protokoll.
* Lehetőség Active Directory hozzáférés-vezérlési listák (ACL-ek) beállítására Active Directory felhasználói csoportok és a `computer$` számítógép-objektum számára.
* A fájlmegosztás csak akkor engedélyezhető, HA engedélyezve van:
    * A fájlok tárolására használt lemezek nem lehetnek egyetlen meghibásodási pontnak sem.
    * A kiszolgáló vagy a virtuális gép leállása nem okoz állásidőt a fájlmegosztás esetében.

Az SAP- \<SID\> fürt szerepkör nem tartalmazza a fürt megosztott lemezeit vagy egy általános fájlmegosztási fürterőforrás-erőforrást.


![3. ábra: \< az SAP SID- \> fürt szerepkörének erőforrásai a fájlmegosztás használatához][sap-ha-guide-figure-8005]

_**3. ábra:** SAP &lt; SID- &gt; fürt szerepkör erőforrásai a fájlmegosztás használatához_


## <a name="scale-out-file-shares-with-storage-spaces-direct-in-azure-as-an-sapmnt-file-share"></a>Kibővített fájlmegosztás Közvetlen tárolóhelyek az Azure-ban SAPMNT-fájlmegosztásként

Kibővített fájlmegosztást használhat a globális SAP-gazdagépek fájljainak üzemeltetéséhez és a védelemhez. A kibővített fájlmegosztás is kínál egy magasan elérhető SAPMNT-fájlmegosztás szolgáltatást.

![4. ábra: az SAP globális gazdagép fájljainak védelemmel ellátott kibővített fájlmegosztás][sap-ha-guide-figure-8006]

_**4. ábra:** Egy kibővített fájlmegosztás, amely az SAP globális gazdagép fájljainak a megóvására szolgál_

> [!IMPORTANT]
> A kibővíthető fájlmegosztás teljes mértékben támogatott a Microsoft Azure felhőben és a helyszíni környezetekben.
>

A kibővített fájlmegosztás egy magasan elérhető és horizontálisan méretezhető SAPMNT-fájlmegosztást biztosít.

A Közvetlen tárolóhelyek megosztott lemezként használható a kibővített fájlmegosztás számára. A Közvetlen tárolóhelyek használatával a helyi tárterülettel rendelkező kiszolgálók segítségével kiválóan elérhető és méretezhető tárhelyet építhet ki. A kibővíthető fájlmegosztás, például az SAP globális gazdagépek fájljai esetében használt megosztott tároló nem egy meghibásodási pont.

Közvetlen tárolóhelyek kiválasztásakor vegye figyelembe ezeket a használati eseteket:

- Az Közvetlen tárolóhelyek-fürt létrehozásához használt virtuális gépeket egy Azure-beli rendelkezésre állási csoportba kell telepíteni.
- Közvetlen tárolóhelyek-fürt vész-helyreállításához használhatja a [Azure site Recovery szolgáltatásokat](../../../site-recovery/azure-to-azure-support-matrix.md#replicated-machines---storage).
- A közvetlen tárolóhelyek fürtjét nem lehet a különböző Azure Availability Zones között kiterjeszteni.

### <a name="sap-prerequisites-for-scale-out-file-shares-in-azure"></a>A kibővíthető fájlmegosztás SAP-előfeltételei az Azure-ban

A kibővített fájlmegosztás használatához a rendszernek meg kell felelnie a következő követelményeknek:

* Legalább két fürtcsomópont a kibővíthető fájlmegosztás számára.
* Minden csomópontnak legalább két helyi lemezzel kell rendelkeznie.
* A teljesítmény miatt a *tükrözési rugalmasságot* kell használnia:
    * Kétirányú tükrözés egy kibővíthető fájlmegosztás két fürtcsomóponton.
    * Három irányú tükrözés egy kibővített fájlmegosztás számára három (vagy több) fürtcsomóponton.
* Három (vagy több) fürtcsomópont használatát javasoljuk egy kibővített fájlmegosztás számára, háromutas tükrözéssel.
    Ez a telepítő nagyobb méretezhetőséget és több tárolási rugalmasságot kínál, mint a kibővíthető fájlmegosztás beállítása két fürtcsomóponton és kétirányú tükrözéssel.
* Az Azure Premium-lemezeket kell használnia.
* Javasoljuk, hogy használja az Azure Managed Disks-t.
* Javasoljuk, hogy a köteteket rugalmas fájlrendszer (ReFS) használatával formázza.
    * További információ: [SAP-megjegyzés 1869038 – SAP-támogatás a ReFs fájlrendszerhez][1869038] , valamint a kötetek megtervezése című cikk [fájlrendszerének kiválasztása][planning-volumes-s2d-choosing-filesystem] közvetlen tárolóhelyek.
    * Ügyeljen arra, hogy a [Microsoft KB4025334 összegző frissítését][kb4025334]telepítse.
* DS-Series vagy DSv2-Series Azure-beli virtuálisgép-méreteket is használhat.
* A virtuális gépek közötti megfelelő hálózati teljesítmény érdekében, amely Közvetlen tárolóhelyek lemezes szinkronizáláshoz szükséges, használjon olyan virtuálisgép-típust, amely legalább egy "magas" hálózati sávszélességgel rendelkezik.
    További információ: a [DSv2 sorozat][dv2-series] és a [DS-sorozat][ds-series] specifikációja.
* Javasoljuk, hogy foglaljon le néhány le nem foglalt kapacitást a tárolóban. Ha a tárolóhelyen nem foglalt kapacitást ad meg, a kötetek lemezterületet biztosítanak a "helyben" javításhoz, ha a meghajtó meghibásodik. Ez javítja az adatbiztonságot és a teljesítményt.  További információ: a [kötet méretének kiválasztása][choosing-the-size-of-volumes-s2d].
* Nem kell konfigurálnia az Azure belső Load balancert a kibővített fájlmegosztás hálózati neveként, például: \<SAP global host\> . Ez az \<ASCS/SCS virtual host name\> SAP ASCS/SCS példány vagy az adatbázis-kezelő rendszer esetében történik. A kibővített fájlmegosztás az összes fürtcsomóponton kibővíti a terhelést. \<SAP global host\> a a fürt összes csomópontjának helyi IP-címét használja.


> [!IMPORTANT]
> A SAPMNT-fájlmegosztás nem nevezhető át, amely a következőre mutat: \<SAP global host\> . Az SAP csak a "sapmnt" megosztási nevet támogatja.
>
> További információ: [SAP Note 2492395 – a megosztás neve sapmnt módosítható?][2492395]

### <a name="configure-sap-ascsscs-instances-and-a-scale-out-file-share-in-two-clusters"></a>Az SAP ASCS/SCS-példányok és a kibővíthető fájlmegosztás konfigurálása két fürtben

Az SAP ASCS/SCS-példányokat külön fürtben kell telepítenie, a saját SAP- \<SID\> fürt szerepkörével. Ebben az esetben a kibővíthető fájlmegosztást egy másik fürtön konfigurálja, egy másik fürt szerepkörrel.


> [!IMPORTANT]
> A telepítőnek meg kell felelnie a következő követelményeknek: az SAP ASCS/SCS-példányokat és a SOFS-megosztást külön fürtökben kell telepíteni.    
>
> [!IMPORTANT] 
> Ebben az esetben az SAP ASCS/SCS-példány úgy van konfigurálva, hogy az SAP globális gazdagépet az UNC elérési út \\ \\ &lt; SAP Global Host &gt; \sapmnt \\ &lt; SID &gt; \SYS használatával elérje.\.
>

![5. ábra: SAP ASCS/SCS-példány és két fürtön üzembe helyezett kibővített fájlmegosztás][sap-ha-guide-figure-8007]

_**5. ábra:** Egy SAP ASCS/SCS-példány és egy kibővíthető fájlmegosztás két fürtben_

> [!IMPORTANT]
> Az Azure-felhőben az SAP-hez és a kibővített fájlmegosztáshoz használt összes fürtöt saját Azure-beli rendelkezésre állási csoportba vagy Azure Availability Zones kell telepíteni. Ez biztosítja a fürt virtuális gépek elosztott elhelyezését az alapul szolgáló Azure-infrastruktúrában. A rendelkezésre állási zónák üzemelő példányai ebben a technológiában támogatottak.
>

## <a name="generic-file-share-with-sios-datakeeper-as-cluster-shared-disks"></a>Általános fájlmegosztás SIOS-DataKeeper, fürt megosztott lemezként


Az általános fájlmegosztás egy másik lehetőség a kiválóan elérhető fájlmegosztás megvalósítására.

Ebben az esetben egy külső gyártótól származó SIOS-megoldást is használhat fürt megosztott lemezként.

## <a name="next-steps"></a>Következő lépések

* [Készítse elő az Azure-infrastruktúrát az SAP-hez HA egy Windows feladatátvevő fürt és fájlmegosztás használatával egy SAP ASCS/SCS-példányhoz][sap-high-availability-infrastructure-wsfc-file-share]
* [SAP NetWeaver HA telepítése Windows feladatátvevő fürtön és fájlmegosztás esetén SAP ASCS/SCS-példány esetén][sap-high-availability-installation-wsfc-shared-disk]
* [Két csomópontos Közvetlen tárolóhelyek kibővíthető fájlkiszolgáló üzembe helyezése az Azure-beli UPD-tároláshoz][deploy-sofs-s2d-in-azure]
* [A Windows Server 2016 Közvetlen tárolóhelyek szolgáltatása][s2d-in-win-2016]
* [Deep Dive: kötetek a Közvetlen tárolóhelyekban][deep-dive-volumes-in-s2d]

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[kb4025334]:https://support.microsoft.com/help/4025334/windows-10-update-kb4025334

[dv2-series]:../../dv2-dsv2-series.md
[ds-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-volumes-s2d-choosing-filesystem]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-filesystem
[choosing-the-size-of-volumes-s2d]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-size-of-volumes
[deploy-sofs-s2d-in-azure]:https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment
[s2d-in-win-2016]:https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview
[deep-dive-volumes-in-s2d]:https://blogs.technet.microsoft.com/filecab/2016/08/29/deep-dive-volumes-in-spaces-direct/

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

[1869038]:https://launchpad.support.sap.com/#/notes/1869038 

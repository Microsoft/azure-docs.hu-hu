---
title: Azure-beli virtuális gépek magas rendelkezésre állása az SAP NW számára a RHEL multi-SID útmutatóban | Microsoft Docs
description: Magas rendelkezésre állás biztosítása az SAP NW számára az Azure Virtual Machines (VM) RHEL több SID-hez.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/11/2021
ms.author: radeltch
ms.openlocfilehash: ec2121754a24a44288c158e630a4e84219c744e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676912"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-red-hat-enterprise-linux-for-sap-applications-multi-sid-guide"></a>Magas rendelkezésre állás az SAP NetWeaver Azure-beli virtuális gépeken Red Hat Enterprise Linux for SAP Applications multi-SID Guide

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

Ez a cikk azt ismerteti, hogyan helyezhet üzembe több SAP NetWeaver-t (azaz több SID-t) egy két csomópontos fürtön az Azure-beli virtuális gépeken, Red Hat Enterprise Linux SAP-alkalmazásokhoz.  

A példában a konfigurációk, telepítési parancsok stb. három SAP NetWeaver 7,50 rendszer van üzembe helyezve egyetlen, két csomópont magas rendelkezésre állású fürtben. Az SAP-rendszerek biztonsági azonosítói a következők:
* **NW1**: a ASCS-példány száma **00** és a virtuális gazdagép neve **msnw1ascs**; Az ERS-példányok száma **02** és a virtuális gazdagép neve **msnw1ers**.  
* **NW2**: ASCS-példány száma **10** és virtuális állomásnév **msnw2ascs**; Az ERS-példányok száma **12** és a virtuális gazdagép neve **msnw2ers**.  
* **NW3**: ASCS-példány száma **20** és virtuális állomásnév **msnw3ascs**; A **22-es** számú példány és a virtuális állomásnév **msnw3ers**.  

A cikk nem fedi le az adatbázis rétegét és az SAP NFS-megosztások központi telepítését. A cikkben szereplő példákban [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)  mennyiségi **sapMSID** használunk az NFS-megosztásokhoz, feltéve, hogy a kötet már telepítve van. Azt is feltételezzük, hogy a Azure NetApp Files kötet NFSv3 protokollal van telepítve, és a következő fájlelérési utak léteznek az SAP Systems NW1, NW2 és NW3 ASCS és ERS példányai esetében:  

* mennyiségi sapMSID (nfs://10.42.0.4/sapmnt<b>NW1</b>)
* mennyiségi sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>ASCs)
* mennyiségi sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>sys)
* mennyiségi sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>-esek)
* mennyiségi sapMSID (nfs://10.42.0.4/sapmnt<b>NW2</b>)
* mennyiségi sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>ASCs)
* mennyiségi sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>sys)
* mennyiségi sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>-esek)
* mennyiségi sapMSID (nfs://10.42.0.4/sapmnt<b>NW3</b>)
* mennyiségi sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>ASCs)
* mennyiségi sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>sys)
* mennyiségi sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>-esek)

Mielőtt elkezdené, tekintse meg a következő SAP-megjegyzéseket és dokumentumokat először:

* SAP-Megjegyzés [1928533], amely a következőket tartalmazta:
  * Az SAP-szoftverek üzembe helyezéséhez támogatott Azure-beli virtuálisgép-méretek listája
  * Fontos kapacitási információk Azure-beli virtuális gépek méreteihez
  * Támogatott SAP-szoftverek és operációs rendszerek (OS) és adatbázis-kombinációk
  * A Windows és a Linux rendszerhez szükséges SAP kernel verziója Microsoft Azure
* [Azure NetApp Files dokumentáció][anf-azure-doc]
* Az SAP Note [2015553] az SAP által támogatott SAP-szoftverek Azure-beli üzembe helyezésének előfeltételeit sorolja fel.
* Az SAP Megjegyzés [2002167] ajánlott operációsrendszer-beállításokkal Red Hat Enterprise Linux
* A [2009879] -es SAP-Megjegyzés SAP HANA irányelvek a Red Hat Enterprise Linux
* Az [2178632] -es SAP-Megjegyzés részletes információkat tartalmaz az Azure-beli SAP-ban jelentett összes figyelési mérőszámról.
* A [2191498] -es SAP-Megjegyzés a szükséges SAP-gazdagép ügynökének verziója az Azure-ban linuxos.
* Az [2243692] -es SAP-Megjegyzés az Azure-beli Linuxon futó SAP-licenceléssel kapcsolatos információkat tartalmaz.
* Az SAP Megjegyzés [1999351] további hibaelhárítási információkat tartalmaz az SAP-hez készült Azure Enhanced monitoring bővítménnyel kapcsolatban.
* Az [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) rendelkezik minden szükséges SAP-megjegyzéssel a Linux rendszerhez.
* [Azure Virtual Machines tervezése és implementálása Linux rendszeren az SAP-ban][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP-hez Linux rendszeren][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hez Linux rendszeren][dbms-guide]
* [SAP NetWeaver a pacemaker-fürtben](https://access.redhat.com/articles/3150081)
* Általános RHEL dokumentáció
  * [Magas rendelkezésre állású Add-On áttekintése](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Magas rendelkezésre állású Add-On felügyelet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Magas rendelkezésre állású Add-On referenciája](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Az SAP NetWeaver ASCS/ERS konfigurálása önálló erőforrásokkal a RHEL 7,5-ben](https://access.redhat.com/articles/3569681)
  * [Az SAP S/4HANA ASCS/ERS konfigurálása önálló sorba helyezni Server 2 (ENSA2) segítségével a RHEL-beli Pacemakerben](https://access.redhat.com/articles/3974941)
* Az Azure-specifikus RHEL dokumentációja:
  * [A RHEL magas rendelkezésre állású fürtökre vonatkozó támogatási szabályzatok – Microsoft Azure Virtual Machines a fürt tagjai](https://access.redhat.com/articles/3131341)
  * [Red Hat Enterprise Linux 7,4 (és újabb) High-Availability fürt telepítése és konfigurálása Microsoft Azure](https://access.redhat.com/articles/3252491)
* [NetApp SAP-alkalmazások Microsoft Azure a Azure NetApp Files használatával][anf-sap-applications-azure]

## <a name="overview"></a>Áttekintés

A fürtben részt vevő virtuális gépeket úgy kell méretezni, hogy az összes erőforrást futtatni lehessen, ha feladatátvétel történik. Az egyes SAP-SID-feladatok egymástól függetlenek lehetnek a többszörös SID magas rendelkezésre állási fürtben.  

A magas rendelkezésre állás elérése érdekében az SAP NetWeaver magas rendelkezésre állású megosztásokat igényel. Ebben a dokumentációban az [Azure NETAPP Files NFS-köteteken](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)üzembe helyezett SAP-megosztásokkal kapcsolatos példákat mutatjuk be. Az is lehetséges, hogy a megosztások a GlusterFS- [fürtön](./high-availability-guide-rhel-glusterfs.md)is futtathatók, amelyet több SAP-rendszer is használhat.  

![SAP NetWeaver – magas rendelkezésre állás – áttekintés](./media/high-availability-guide-rhel/ha-rhel-multi-sid.png)

> [!IMPORTANT]
> Az Azure-beli virtuális gépeken futó, az SAP ASCS/ERS és a Red Hat Linux operációs rendszert futtató többszörös SID fürtözés támogatása ugyanazon a fürtön **öt** SAP-SID-re korlátozódik. Minden új SID növeli a bonyolultságot. Az SAP sorba helyezni Replication Server 1 és a sorba helyezni Replication Server 2 együttes használata **nem támogatott** ugyanazon a fürtön. A többszörös SID-fürtszolgáltatás több SAP ASCS/ERS példány telepítését ismerteti különböző SID-kiszolgálókkal egy pacemaker-fürtben. Jelenleg a többszörös SID-fürtszolgáltatás csak ASCS/ERS esetén támogatott.  

> [!TIP]
> Az SAP ASCS/ERS több SID-fürtszolgáltatása nagyobb komplexitású megoldás. A megvalósítás összetettebb. Emellett a karbantartási tevékenységek (például az operációs rendszer javításai) végrehajtásakor is magasabb adminisztrációs erőfeszítéssel jár. A tényleges megvalósítás megkezdése előtt Szánjon időt arra, hogy gondosan tervezze meg az üzembe helyezést és az összes érintett összetevőt, például a virtuális gépeket, az NFS-csatlakoztatásokat, a VIP-ket és a terheléselosztó konfigurációját  

Az SAP NetWeaver ASCS, az SAP NetWeaver SCS és az SAP NetWeaver ERS virtuális állomásnév és virtuális IP-címeket használnak. Az Azure-ban a virtuális IP-címek használatához terheléselosztó szükséges. A [standard Load Balancer](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md)használatát javasoljuk.  

A következő lista az (A) SCS és ERS Load Balancer konfigurációját mutatja be ehhez a többszörös SID-fürthöz, például három SAP-rendszerrel. A biztonsági azonosítók mindegyikéhez külön előtérbeli IP-címet, állapot-mintavételi és terheléselosztási szabályokat kell megadnia minden egyes ASCS és ERS-példányhoz. Rendelje hozzá az összes virtuális gépet, amelyek a ASCS/ASCS-fürt részét képezik egyetlen ILB egyetlen háttér-készletének.  

### <a name="ascs"></a>Egy SCS

* Előtér-konfiguráció
  * A NW1 IP-címe: 10.3.1.50
  * A NW2 IP-címe: 10.3.1.52
  * A NW3 IP-címe: 10.3.1.54

* Mintavételi portok
  * 620  -es port, ezért a NW1, a NW2 és a NW3 mintavételi portok esetében 620 00, 620 10 és 620 20<strong> &lt; &gt; </strong>
* Terheléselosztási szabályok – hozzon létre egyet minden példányhoz, azaz a következőhöz: NW1/ASCS, NW2/ASCS és NW3/ASCS.
  * Ha standard Load Balancer használ, válassza a **hektár portok** elemet.
  * Ha alapszintű Load Balancer használ, hozzon létre terheléselosztási szabályokat a következő portokhoz
    * 32<strong> &lt; nr &gt; </strong> TCP
    * 36<strong> &lt; nr &gt; </strong> TCP
    * 39<strong> &lt; nr &gt; </strong> TCP
    * 81<strong> &lt; nr &gt; </strong> TCP
    * 5<strong> &lt; nr &gt; </strong>13 TCP
    * 5<strong> &lt; nr &gt; </strong>14 TCP
    * 5<strong> &lt; nr &gt; </strong>16 TCP

### <a name="ers"></a>ERS

* Előtér-konfiguráció
  * A NW1 10.3.1.51 IP-címe
  * A NW2 10.3.1.53 IP-címe
  * A NW3 10.3.1.55 IP-címe

* Mintavételi port
  * A 621 <strong> &lt; nr &gt;</strong>, ezért a NW1, a NW2 és az N3 mintavételi portok esetében 621 **02**, 621 **12** és 621 **22**
* Terheléselosztási szabályok – hozzon létre egyet az egyes példányok, azaz a NW1/ERS, a NW2/ERS és a NW3/ERS esetében.
  * Ha standard Load Balancer használ, válassza a **hektár portok** elemet.
  * Ha alapszintű Load Balancer használ, hozzon létre terheléselosztási szabályokat a következő portokhoz
    * 32<strong> &lt; nr &gt; </strong> TCP
    * 33<strong> &lt; nr &gt; </strong> TCP
    * 5<strong> &lt; nr &gt; </strong>13 TCP
    * 5<strong> &lt; nr &gt; </strong>14 TCP
    * 5<strong> &lt; nr &gt; </strong>16 TCP

* Háttér-konfiguráció
  * Az (A) SCS/ERS-fürt részét képező összes virtuális gép elsődleges hálózati adapteréhez csatlakozik

> [!IMPORTANT]
> A lebegő IP-címek nem támogatottak a terheléselosztási helyzetekben a hálózati adapter másodlagos IP-konfigurációjában. További részletek: az [Azure Load Balancer korlátozásai](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Ha a virtuális gép további IP-címére van szüksége, helyezzen üzembe egy második hálózati adaptert.  

> [!Note]
> Ha a nyilvános IP-címek nélküli virtuális gépek a belső (nincs nyilvános IP-cím) standard Azure Load Balancer háttér-készletbe kerülnek, nem lesz kimenő internetkapcsolat, kivéve, ha további konfigurálást végeznek a nyilvános végpontok útválasztásának engedélyezéséhez. A kimenő kapcsolatok elérésével kapcsolatos részletekért lásd: [nyilvános végpontú kapcsolat Virtual Machines az Azure standard Load Balancer használata az SAP magas rendelkezésre állási helyzetekben](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

> [!IMPORTANT]
> Ne engedélyezze a TCP-időbélyegeket a Azure Load Balancer mögött elhelyezett Azure-beli virtuális gépeken. A TCP-időbélyegek engedélyezése az állapot-mintavételek meghibásodását eredményezi. Állítsa a paramétert a **0** értékre **net.IPv4.tcp_timestamps** . Részletekért lásd: [Load Balancer Health](../../../load-balancer/load-balancer-custom-probe-overview.md)-tesztek.

## <a name="sap-shares"></a>SAP-megosztások

Az SAP NetWeaver megosztott tárterületet igényel az átvitelhez, a profil könyvtárához stb. A nagy rendelkezésre állású SAP-rendszerek esetében fontos a nagy rendelkezésre állású megosztások használata. Az SAP-megosztások architektúráját is el kell döntenie. Az egyik lehetőség a megosztások telepítése [Azure NETAPP Files NFS-köteteken](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).  A Azure NetApp Files az SAP NFS-megosztások beépített magas rendelkezésre állását fogja kapni.

Egy másik lehetőség, hogy az GlusterFS-t az Azure-beli [virtuális gépeken hozza létre a Red Hat Enterprise Linux for SAP NetWeaver számára](./high-availability-guide-rhel-glusterfs.md), amely több SAP-rendszer között is megosztható. 

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>Az első SAP-rendszer üzembe helyezése a fürtben

Most, hogy eldöntötte az SAP-megosztások architektúráját, a megfelelő dokumentációt követve telepítse az első SAP-rendszerét a fürtben.

* Ha Azure NetApp Files NFS-köteteket használ, kövesse az Azure-beli [virtuális gépek magas rendelkezésre állását az SAP NetWeaver számára a Red Hat Enterprise Linux SAP-alkalmazásokhoz Azure NetApp Files](./high-availability-guide-rhel-netapp-files.md)  
* Ha GlusterFS-fürtöt használ, kövesse az [GlusterFS Azure-beli virtuális gépeken az SAP NetWeaver Red Hat Enterprise Linux](./high-availability-guide-rhel-glusterfs.md).  

A fent felsorolt dokumentumok végigvezetik a szükséges infrastruktúra előkészítésének lépésein, a fürt létrehozásán és az operációs rendszer az SAP-alkalmazás futtatásához való előkészítésének lépésein.  

> [!TIP]
> Mindig tesztelje a fürt feladatátvételi funkcióját az első rendszer telepítése után, mielőtt hozzáadja a további SAP-SID-ket a fürthöz. Így tudni fogja, hogy a fürt működése működik-e, mielőtt hozzáadja a további SAP-rendszerek összetettségét a fürthöz.   

## <a name="deploy-additional-sap-systems-in-the-cluster"></a>További SAP-rendszerek üzembe helyezése a fürtben

Ebben a példában feltételezzük, hogy a rendszer **NW1** már telepítve van a fürtben. Bemutatjuk, hogyan helyezhető üzembe a fürt SAP Systems **NW2** és **NW3**. 

A következő elemek a **[a]** előtaggal vannak ellátva, amelyek az összes csomópontra érvényesek, **[1]** – csak az 1. vagy **[2]** csomópontra érvényesek, csak a 2. csomópontra.

### <a name="prerequisites"></a>Előfeltételek 

> [!IMPORTANT]
> A fürtben további SAP-rendszerek üzembe helyezéséhez szükséges utasítások követése előtt kövesse az első SAP-rendszer telepítése a fürtben című témakör utasításait, mivel azok a lépések, amelyek csak az első rendszer központi telepítése során szükségesek.  

A dokumentáció a következőket feltételezi:
* A pacemaker-fürt már konfigurálva van és fut.  
* Legalább egy SAP-rendszer (ASCS/ERS-példány) már telepítve van, és a fürtben fut.  
* A fürt feladatátvételi funkcióját tesztelték.  
* Az összes SAP-rendszer NFS-megosztása telepítve van.  

### <a name="prepare-for-sap-netweaver-installation"></a>Felkészülés az SAP NetWeaver telepítésére

1. Adja hozzá a konfigurációt az újonnan telepített rendszerhez (azaz **NW2**, **NW3**) a meglévő Azure Load Balancerhoz, és az utasításokat követve [telepítse manuálisan a Azure Load Balancert Azure Portal használatával](./high-availability-guide-rhel-netapp-files.md#deploy-linux-manually-via-azure-portal). Módosítsa az IP-címeket, az állapot-mintavételi portokat, a konfiguráció terheléselosztási szabályait.  

2. **[A]** a további SAP-rendszerek beállításának névfeloldása. Használhatja a DNS-kiszolgálót, vagy módosíthatja `/etc/hosts` az összes csomópontot. Ez a példa a fájl használatát mutatja be `/etc/hosts` .  Igazítsa az IP-címeket és az állomásneveket a környezethez. 

    ```
    sudo vi /etc/hosts
    # IP address of the load balancer frontend configuration for NW2 ASCS
    10.3.1.52 msnw2ascs
    # IP address of the load balancer frontend configuration for NW3 ASCS
    10.3.1.54 msnw3ascs
    # IP address of the load balancer frontend configuration for NW2 ERS
    10.3.1.53 msnw2ers
    # IP address of the load balancer frontend configuration for NW3 ERS
    10.3.1.55 msnw3ers
   ```

3. **[A]** hozza létre a megosztott könyvtárakat azon további **NW2** és **NW3** SAP-rendszerek számára, amelyeket üzembe helyez a fürtön. 

    ```
    sudo mkdir -p /sapmnt/NW2
    sudo mkdir -p /usr/sap/NW2/SYS
    sudo mkdir -p /usr/sap/NW2/ASCS10
    sudo mkdir -p /usr/sap/NW2/ERS12
    sudo mkdir -p /sapmnt/NW3
    sudo mkdir -p /usr/sap/NW3/SYS
    sudo mkdir -p /usr/sap/NW3/ASCS20
    sudo mkdir -p /usr/sap/NW3/ERS22

    
    sudo chattr +i /sapmnt/NW2
    sudo chattr +i /usr/sap/NW2/SYS
    sudo chattr +i /usr/sap/NW2/ASCS10
    sudo chattr +i /usr/sap/NW2/ERS12
    sudo chattr +i /sapmnt/NW3
    sudo chattr +i /usr/sap/NW3/SYS
    sudo chattr +i /usr/sap/NW3/ASCS20
    sudo chattr +i /usr/sap/NW3/ERS22
   ```

4. **[A]** adja hozzá a/sapmnt/SID-és/usr/sap/SID/sys-fájlrendszerek csatlakoztatási bejegyzéseit a fürtre TELEPÍTENDŐ további SAP-rendszerekhez. Ebben a példában a **NW2** és a **NW3**.  

   Frissítse a fájlrendszert a `/etc/fstab` fürtön üzembe helyezett további SAP-rendszerekhez.  

   * Ha Azure NetApp Files használ, kövesse az [itt](./high-availability-guide-rhel-netapp-files.md#prepare-for-sap-netweaver-installation) található utasításokat.  
   * Ha GlusterFS-fürtöt használ, kövesse az [itt](./high-availability-guide-rhel.md#prepare-for-sap-netweaver-installation) található utasításokat.  

### <a name="install-ascs--ers"></a>ASCS/ERS telepítése

1. Hozza létre a virtuális IP-és állapot-mintavételi fürt erőforrásait a fürtre telepítendő további SAP-rendszerek ASCS példányaihoz. Az itt látható példa a **NW2** és a **NW3** ASCS, az NFS használatával Azure NetApp Files köteteken, NFSv3 protokollal.  

    ```
    sudo pcs resource create fs_NW2_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ascs' \
    directory='/usr/sap/NW2/ASCS10' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_ASCS

    sudo pcs resource create vip_NW2_ASCS IPaddr2 \
    ip=10.3.1.52 cidr_netmask=24 \
     --group g-NW2_ASCS
    
    sudo pcs resource create nc_NW2_ASCS azure-lb port=62010 \
     --group g-NW2_ASCS

    sudo pcs resource create fs_NW3_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ascs' \
    directory='/usr/sap/NW3/ASCS20' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-NW3_ASCS

    sudo pcs resource create vip_NW3_ASCS IPaddr2 \
    ip=10.3.1.54 cidr_netmask=24 \
    --group g-NW3_ASCS

    sudo pcs resource create nc_NW3_ASCS azure-lb port=62020 \
    --group g-NW3_ASCS
    ```

   Győződjön meg arról, hogy a fürt állapota ok, és hogy minden erőforrás elindult. Nem fontos, hogy az erőforrások melyik csomóponton futnak.  

2. **[1]** az SAP NetWeaver ASCS telepítése  

   Telepítse az SAP NetWeaver-ASCS root-ként, egy virtuális állomásnév használatával, amely a ASCS tartozó terheléselosztó előtér-konfigurációjának IP-címét képezi le. A rendszer **NW2** például a virtuális állomásnév a <b>msnw2ascs</b>, a <b>10.3.1.52</b> és a terheléselosztó mintavételéhez használt példány száma, például <b>10</b>. A rendszer **NW3** a virtuális állomásnév a <b>msnw3ascs</b>, a <b>10.3.1.54</b> és a terheléselosztó mintavételéhez használt példány száma, például <b>20</b>. Jegyezze fel, hogy melyik fürtcsomóponton telepítette az egyes SAP SID-ASCS.  

   A sapinst paraméterrel SAPINST_REMOTE_ACCESS_USER engedélyezheti, hogy a nem root felhasználó csatlakozhasson a sapinst. A (z) SAPINST_USE_HOSTNAME paraméter használatával telepítheti az SAP-t a virtuális gazdagép nevével.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   Ha a telepítés során nem sikerül almappát létrehozni a/usr/SAP/**SID**/ASCS-**példányában**, próbálja meg a tulajdonost **SID** adm-re állítani, és a ASCS-**példány** sapsys, majd próbálkozzon újra.

3. **[1]** hozzon létre egy virtuális IP-és állapot-mintavételi fürt erőforrásait a fürtre TELEPÍTENDŐ további SAP-rendszer ERS-példányához. Az itt látható példa a **NW2** és a **NW3** , az NFS használatával Azure NetApp Files köteteken, NFSv3 protokollal.  

    ```
    sudo pcs resource create fs_NW2_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ers' \
    directory='/usr/sap/NW2/ERS12' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_AERS

    sudo pcs resource create vip_NW2_AERS IPaddr2 \
    ip=10.3.1.53 cidr_netmask=24 \
     --group g-NW2_AERS

    sudo pcs resource create nc_NW2_AERS azure-lb port=62112 \
     --group g-NW2_AERS

    sudo pcs resource create fs_NW3_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ers' \
    directory='/usr/sap/NW3/ERS22' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW3_AERS

    sudo pcs resource create vip_NW3_AERS IPaddr2 \
    ip=10.3.1.55 cidr_netmask=24 \
     --group g-NW3_AERS

    sudo pcs resource create nc_NW3_AERS azure-lb port=62122 \
     --group g-NW3_AERS
   ```

   Győződjön meg arról, hogy a fürt állapota ok, és hogy minden erőforrás elindult.  

   Ezután győződjön meg arról, hogy az újonnan létrehozott ERS-csoport erőforrásai futnak a fürtcsomóponton, szemben azzal a fürtcsomópont-csomóponttal, ahol a ASCS-példány ugyanarra az SAP-rendszerre lett telepítve.  Ha például a NW2 ASCS telepítve `rhelmsscl1` van, akkor győződjön meg arról, hogy a NW2 ERS csoport fut `rhelmsscl2` .  A NW2 ERS Group a `rhelmsscl2` következő parancs futtatásával telepíthető át a csoport egyik erőforrásához: 

    ```
      pcs resource move fs_NW2_AERS rhelmsscl2
    ```

4. **[2]** SAP NETWEAVER-ERS telepítése

   Telepítse az SAP NetWeaver-ket root-ként a másik csomóponton, egy virtuális állomásnév használatával, amely a terheléselosztó előtér-konfigurációjának IP-címét képezi le az ERS-hoz. Például a rendszerszintű **NW2** a virtuális állomásnév a <b>msnw2ers</b>, a <b>10.3.1.53</b> és a terheléselosztó mintavételéhez használt példány száma, például <b>12</b>. A rendszer **NW3** a virtuális állomásnév <b>msnw3ers</b>, a <b>10.3.1.55</b> és a terheléselosztó mintavételéhez használt példány számát, például <b>22</b>. 

   A sapinst paraméterrel SAPINST_REMOTE_ACCESS_USER engedélyezheti, hogy a nem root felhasználó csatlakozhasson a sapinst. A (z) SAPINST_USE_HOSTNAME paraméter használatával telepítheti az SAP-t a virtuális gazdagép nevével.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > Használja az SWPM SP 20 PL 05-es vagy újabb verzióját. Az alacsonyabb verziók nem tudják megfelelően beállítani az engedélyeket, és a telepítés sikertelen lesz.

   Ha a telepítés nem tud almappát létrehozni a/usr/SAP/**NW2**/ERS-**példányban**, próbálja meg beállítani a tulajdonost a **SID** adm-re, és a csoportot az ERS-**példány #** mappa sapsys, és próbálkozzon újra.

   Ha szükséges, hogy áttelepítse az újonnan telepített SAP-rendszer ERS-csoportját egy másik fürtcsomóponton, ne felejtse el eltávolítani az ERS csoporthoz tartozó hely megkötését. A korlátozást a következő parancs futtatásával távolíthatja el (ez a példa az SAP Systems **NW2** és a **NW3** esetében van megadva). Győződjön meg arról, hogy a parancsban használt erőforrás ideiglenes korlátozásait eltávolítja az ERS-fürt áthelyezéséhez.

    ```
      pcs resource clear fs_NW2_AERS
      pcs resource clear fs_NW3_AERS
    ```

5. **[1]** a ASCS/SCS és a ERS instance profilokat az újonnan telepített SAP-rendszer (ek) hez igazíthatja. Az alább látható példa a NW2. A fürthöz hozzáadott összes SAP-példányhoz alkalmazkodnia kell a ASCS/SCS és a ERS profilhoz.  
 
   * ASCS/SCS-profil

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
      # Change the restart command to a start command
      #Restart_Program_01 = local $(_EN) pf=$(_PF)
      Start_Program_01 = local $(_EN) pf=$(_PF)

      # Add the keep alive parameter, if using ENSA1
      enque/encni/set_so_keepalive = true
      ```

     A ENSA1 és a ENSA2 esetében ügyeljen arra, hogy az `keepalive` operációs rendszer paramétereinek beállítása a [1410736](https://launchpad.support.sap.com/#/notes/1410736)-es SAP-megjegyzésben leírtak szerint történjen.    

   * ERS-profil

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ERS12_msnw2ers
   
      # Change the restart command to a start command
      #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
      Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
      # remove Autostart from ERS profile
      # Autostart = 1
      ```

6. **[A]** a/usr/SAP/sapservices fájl frissítése

   Ha meg szeretné akadályozni, hogy a sapinit indítási parancsfájlja elindítsa a példányokat, a pacemaker által kezelt összes példányt ki kell kommentálni a `/usr/sap/sapservices` fájlból.  Az alább látható példa az SAP Systems **NW2** és a **NW3**.  

   ```
    # On the node where ASCS was installed, comment out the line for the ASCS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ASCS10/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ASCS10/exe/sapstartsrv pf=/usr/sap/NW2/SYS/profile/NW2_ASCS10_msnw2ascs -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ASCS20/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ASCS20/exe/sapstartsrv pf=/usr/sap/NW3/SYS/profile/NW3_ASCS20_msnw3ascs -D -u nw3adm

    # On the node where ERS was installed, comment out the line for the ERS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ERS12/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ERS12/exe/sapstartsrv pf=/usr/sap/NW2/ERS12/profile/NW2_ERS12_msnw2ers -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ERS22/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ERS22/exe/sapstartsrv pf=/usr/sap/NW3/ERS22/profile/NW3_ERS22_msnw3ers -D -u nw3adm
   ```

7. **[1]** hozza létre az SAP-fürt erőforrásait az újonnan telepített SAP-rendszerhez.  

   Ha a sorba helyezni Server 1 architektúráját (ENSA1) használja, az alábbi módon határozza meg az SAP Systems **NW2** és **NW3** erőforrásait:

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint location rsc_sap_NW2_ASCS10 rule score=2000 runs_ers_NW2 eq 1
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint location rsc_sap_NW3_ASCS20 rule score=2000 runs_ers_NW3 eq 1
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   Az SAP bevezette a 2. sorba helyezni-kiszolgáló, beleértve a replikálást, az SAP NW 7,52-támogatását. A ABAP platform 1809-től kezdődően a sorba helyezni Server 2 alapértelmezés szerint telepítve van. Lásd: SAP-Megjegyzés [2630416](https://launchpad.support.sap.com/#/notes/2630416) a sorba helyezni Server 2 támogatásához.
   Ha a sorba helyezni Server 2 architektúráját ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)) használja, az alábbi módon határozza meg az SAP Systems **NW2** és **NW3** erőforrásait:

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW2_ASCS then stop g-NW2_AERS symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW3_ASCS then stop g-NW3_AERS symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   Ha egy régebbi verzióról frissít, és átvált a 2. sorba helyezni-kiszolgálóra, tekintse meg a következőt: SAP Note [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   > [!NOTE]
   > A fenti konfiguráció időtúllépései csak példák, és előfordulhat, hogy az adott SAP-beállításhoz kell igazítani őket. 

   Győződjön meg arról, hogy a fürt állapota ok, és hogy az összes erőforrás el van indítva. Nem fontos, hogy az erőforrások melyik csomóponton futnak.
   Az alábbi példa a fürterőforrás állapotát mutatja be, miután az SAP Systems **NW2** és **NW3** a fürthöz lettek adva. 

    ```
     sudo pcs status

    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    ```

8. **[A]** tűzfalszabályok hozzáadása a ASCS és a ERS-hoz mindkét csomóponton.  Az alábbi példában az SAP Systems **NW2** és a **NW3** esetében egyaránt láthatók a tűzfalszabályok.  

   ```
    # NW2 - ASCS
    sudo firewall-cmd --zone=public --add-port=62010/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62010/tcp
    sudo firewall-cmd --zone=public --add-port=3210/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3210/tcp
    sudo firewall-cmd --zone=public --add-port=3610/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3610/tcp
    sudo firewall-cmd --zone=public --add-port=3910/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3910/tcp
    sudo firewall-cmd --zone=public --add-port=8110/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8110/tcp
    sudo firewall-cmd --zone=public --add-port=51013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51013/tcp
    sudo firewall-cmd --zone=public --add-port=51014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51014/tcp
    sudo firewall-cmd --zone=public --add-port=51016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51016/tcp
    # NW2 - ERS
    sudo firewall-cmd --zone=public --add-port=62112/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62112/tcp
    sudo firewall-cmd --zone=public --add-port=3212/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3212/tcp
    sudo firewall-cmd --zone=public --add-port=3312/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3312/tcp
    sudo firewall-cmd --zone=public --add-port=51213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51213/tcp
    sudo firewall-cmd --zone=public --add-port=51214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51214/tcp
    sudo firewall-cmd --zone=public --add-port=51216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51216/tcp
    # NW3 - ASCS
    sudo firewall-cmd --zone=public --add-port=62020/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62020/tcp
    sudo firewall-cmd --zone=public --add-port=3220/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3220/tcp
    sudo firewall-cmd --zone=public --add-port=3620/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3620/tcp
    sudo firewall-cmd --zone=public --add-port=3920/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3920/tcp
    sudo firewall-cmd --zone=public --add-port=8120/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8120/tcp
    sudo firewall-cmd --zone=public --add-port=52013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52013/tcp
    sudo firewall-cmd --zone=public --add-port=52014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52014/tcp
    sudo firewall-cmd --zone=public --add-port=52016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52016/tcp
    # NW3 - ERS
    sudo firewall-cmd --zone=public --add-port=62122/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62122/tcp
    sudo firewall-cmd --zone=public --add-port=3222/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3222/tcp
    sudo firewall-cmd --zone=public --add-port=3322/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3322/tcp
    sudo firewall-cmd --zone=public --add-port=52213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52213/tcp
    sudo firewall-cmd --zone=public --add-port=52214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52214/tcp
    sudo firewall-cmd --zone=public --add-port=52216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52216/tcp
   ```

### <a name="proceed-with-the-sap-installation"></a>Az SAP telepítésének folytatása 

Fejezze be az SAP telepítését:

* [Az SAP NetWeaver alkalmazás-kiszolgálók előkészítése](./high-availability-guide-rhel-netapp-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [Adatbázis-kezelő példány telepítése](./high-availability-guide-rhel-netapp-files.md#install-database)
* [Elsődleges SAP-alkalmazáskiszolgáló telepítése](./high-availability-guide-rhel-netapp-files.md#sap-netweaver-application-server-installation)
* Egy vagy több további SAP-alkalmazás példányának telepítése

## <a name="test-the-multi-sid-cluster-setup"></a>A többszörös SID-fürt beállításának tesztelése

Az alábbi tesztek a Red Hat ajánlott eljárási útmutatójában szereplő tesztelési esetek egy részét képezik. Az Ön kényelme érdekében a szolgáltatás részét képezi. A fürt tesztek teljes listáját a következő dokumentációban találja:

* Ha Azure NetApp Files NFS-köteteket használ, kövesse az Azure-beli [virtuális gépek magas rendelkezésre állását az SAP NETWEAVER RHEL és az SAP-alkalmazások Azure NetApp Files](./high-availability-guide-rhel-netapp-files.md)
* Ha magas rendelkezésre `GlusterFS` állást használ, kövesse az Azure-beli [virtuális gépek magas rendelkezésre állását az SAP NetWeaver RHEL SAP-alkalmazásokhoz](./high-availability-guide-rhel.md)című témakörben.  

Mindig olvassa el a Red Hat ajánlott eljárásokat ismertető útmutatót, és végezze el az esetlegesen hozzáadott további tesztek elvégzését.  
A bemutatott tesztek egy két csomóponton, több SID-fürtön, három SAP-rendszerrel vannak telepítve.  

1. Telepítse manuálisan a ASCS-példányt. A példa bemutatja az ASCS-példány áttelepítését az SAP rendszer NW3.

   Erőforrás állapota a teszt elindítása előtt:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   Futtassa a következő parancsokat root-ként a NW3 ASCS-példány áttelepíteni.

   ```
    pcs resource move rsc_sap_NW3_ASCS200
    # Clear temporary migration constraints
    pcs resource clear rsc_sap_NW3_ASCS20

    # Remove failed actions for the ERS that occurred as part of the migration
    pcs resource cleanup rsc_sap_NW3_ERS22
   ```

   Erőforrás állapota a teszt után:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

1. Csomópont összeomlásának szimulálása

   Erőforrás állapota a teszt elindítása előtt:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

   Futtassa a következő parancsot root-ként egy csomóponton, ahol legalább egy ASCS-példány fut. Ebben a példában a parancsot futtatjuk `rhelmsscl1` , ahol a NW1, a NW2 és a NW3 ASCS példányai futnak.  

   ```
   echo c > /proc/sysrq-trigger
   ```

   A teszt után az állapotot, a csomópontot pedig azután, hogy összeomlott, a következőhöz hasonlóan kell kinéznie.

   ```
    Full list of resources:

    rsc_st_azure    (stonith:fence_azure_arm):      Started rhelmsscl2
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   Ha a sikertelen erőforrások üzenetei vannak, törölje a sikertelen erőforrások állapotát. Például:

   ```
   pcs resource cleanup rsc_sap_NW1_ERS02
   ```

## <a name="next-steps"></a>Következő lépések

* [Azure Virtual Machines az SAP tervezéséhez és megvalósításához][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP-ban][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hoz][dbms-guide]
* A magas rendelkezésre állás és a SAP HANA Azure-beli virtuális gépeken történő vész-helyreállítási tervének megismeréséhez lásd: [Az Azure-beli SAP HANA magas rendelkezésre állása Virtual Machines (VM)][sap-hana-ha]
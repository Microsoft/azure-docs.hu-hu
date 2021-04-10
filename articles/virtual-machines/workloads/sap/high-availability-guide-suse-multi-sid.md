---
title: Azure-beli virtuális gépek magas rendelkezésre állása az SAP NetWeaver számára a SLES multi-SID útmutatóban | Microsoft Docs
description: Többszörös SID – magas rendelkezésre állási útmutató SAP NetWeaver-hez SUSE Linux Enterprise Server SAP-alkalmazásokhoz
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
ms.date: 10/16/2020
ms.author: radeltch
ms.openlocfilehash: 74606909a0bc87caa6acfb0eaf35c05cf35b1858
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676939"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-for-sap-applications-multi-sid-guide"></a>Magas rendelkezésre állás az SAP NetWeaver Azure-beli virtuális gépeken SUSE Linux Enterprise Server for SAP Applications multi-SID Guide

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

Ez a cikk azt ismerteti, hogyan helyezhetők üzembe több SAP NetWeaver-vagy S4HANA-kompatibilis rendszer (azaz több SID) az Azure-beli virtuális gépeken futó két csomópontos fürtön SUSE Linux Enterprise Server SAP-alkalmazásokhoz.  

A példában a konfigurációk, telepítési parancsok stb. három SAP NetWeaver 7,50 rendszer van üzembe helyezve egyetlen, két csomópont magas rendelkezésre állású fürtben. Az SAP-rendszerek biztonsági azonosítói a következők:
* **NW1**: a ASCS-példány száma **00** és a virtuális gazdagép neve **msnw1ascs**; Az ERS-példányok száma **02** és a virtuális gazdagép neve **msnw1ers**.  
* **NW2**: ASCS-példány száma **10** és virtuális állomásnév **msnw2ascs**; Az ERS-példányok száma **12** és a virtuális gazdagép neve **msnw2ers**.  
* **NW3**: ASCS-példány száma **20** és virtuális állomásnév **msnw3ascs**; A **22-es** számú példány és a virtuális állomásnév **msnw3ers**.  

A cikk nem fedi le az adatbázis rétegét és az SAP NFS-megosztások központi telepítését. A cikkben szereplő példákban a NW2-NFS virtuális neveket használjuk a NW2 NFS-megosztásokhoz és a NW3-NFS-hez a NW3 NFS-megosztásokhoz, feltéve, hogy az NFS-fürt üzembe lett helyezve.  

Mielőtt elkezdené, tekintse meg a következő SAP-megjegyzéseket és dokumentumokat először:

* SAP-Megjegyzés [1928533][1928533], amely a következőket tartalmazta:
  * Az SAP-szoftverek üzembe helyezéséhez támogatott Azure-beli virtuálisgép-méretek listája
  * Fontos kapacitási információk Azure-beli virtuális gépek méreteihez
  * Támogatott SAP-szoftverek és operációs rendszerek (OS) és adatbázis-kombinációk
  * A Windows és a Linux rendszerhez szükséges SAP kernel verziója Microsoft Azure

* Az SAP Note [2015553][2015553] az SAP által támogatott SAP-szoftverek Azure-beli üzembe helyezésének előfeltételeit sorolja fel.
* Az SAP Megjegyzés [2205917][2205917] ajánlott operációsrendszer-beállításokkal SUSE Linux Enterprise Server SAP-alkalmazásokhoz
* A [1944799][1944799] -es SAP-Megjegyzés SAP HANA irányelvek az SAP-alkalmazásokhoz SUSE Linux Enterprise Server
* Az [2178632][2178632] -es SAP-Megjegyzés részletes információkat tartalmaz az Azure-beli SAP-ban jelentett összes figyelési mérőszámról.
* A [2191498][2191498] -es SAP-Megjegyzés a szükséges SAP-gazdagép ügynökének verziója az Azure-ban linuxos.
* Az [2243692][2243692] -es SAP-Megjegyzés az Azure-beli Linuxon futó SAP-licenceléssel kapcsolatos információkat tartalmaz.
* Az [1984787][1984787] -es SAP-Megjegyzés általános információkat tartalmaz a SUSE Linux Enterprise Server 12.
* Az SAP Megjegyzés [1999351][1999351] további hibaelhárítási információkat tartalmaz az SAP-hez készült Azure Enhanced monitoring bővítménnyel kapcsolatban.
* Az [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) rendelkezik minden szükséges SAP-megjegyzéssel a Linux rendszerhez.
* [Azure Virtual Machines tervezése és implementálása Linux rendszeren az SAP-ban][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP-hez Linux rendszeren][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hez Linux rendszeren][dbms-guide]
* [SUSE SAP ha ajánlott eljárási útmutatók][suse-ha-guide] Az útmutatók az összes szükséges információt tartalmazzák, amelyekkel beállítható a NetWeaver HA és SAP HANA rendszer-replikáció a helyszínen. Ezeket az útmutatókat általános alaptervként használhatja. Sokkal részletesebb információkat biztosítanak.
* [SUSE magas rendelkezésre állású bővítmény – 12 SP3 kibocsátási megjegyzések][suse-ha-12sp3-relnotes]
* [SUSE multi-SID cluster útmutató a SLES 12 és SLES 15 rendszerhez](https://documentation.suse.com/sbp/all/html/SBP-SAP-MULTI-SID/index.html)
* [NetApp SAP-alkalmazások Microsoft Azure a Azure NetApp Files használatával][anf-sap-applications-azure]
## <a name="overview"></a>Áttekintés

A fürtben részt vevő virtuális gépeket úgy kell méretezni, hogy az összes erőforrást futtatni lehessen, ha feladatátvétel történik. Az egyes SAP-SID-feladatok egymástól függetlenek lehetnek a többszörös SID magas rendelkezésre állási fürtben.  SBD-kerítés használata esetén a SBD-eszközök több fürt között is megoszthatók.  

A magas rendelkezésre állás eléréséhez az SAP NetWeaver magas rendelkezésre állású NFS-megosztásokat igényel. Ebben a példában feltételezzük, hogy az SAP NFS-megosztások vagy egy olyan, magasan elérhető [NFS-fájlkiszolgálón](./high-availability-guide-suse-nfs.md)futnak, amelyet több SAP-rendszer is használhat. Vagy a megosztások [Azure NETAPP Files NFS-köteteken](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)vannak telepítve.  

![A pacemaker-fürt részletes információkat jelenít meg két több SID-fürtről, a msidcl1 és a msidcl2.](./media/high-availability-guide-suse/ha-suse-multi-sid.png)

> [!IMPORTANT]
> Az Azure-beli virtuális gépeken futó SUSE Linux rendszerű, az SAP ASCS és a több SID-fürtözés támogatásának támogatása ugyanazon a fürtön **öt** SAP-SID-re korlátozódik. Minden új SID növeli a bonyolultságot. Az SAP sorba helyezni Replication Server 1 és a sorba helyezni Replication Server 2 együttes használata **nem támogatott** ugyanazon a fürtön. A többszörös SID-fürtszolgáltatás több SAP ASCS/ERS példány telepítését ismerteti különböző SID-kiszolgálókkal egy pacemaker-fürtben. Jelenleg a többszörös SID-fürtszolgáltatás csak ASCS/ERS esetén támogatott.  

> [!TIP]
> Az SAP ASCS/ERS több SID-fürtszolgáltatása nagyobb komplexitású megoldás. A megvalósítás összetettebb. Emellett a karbantartási tevékenységek (például az operációs rendszer javításai) végrehajtásakor is magasabb adminisztrációs erőfeszítéssel jár. A tényleges megvalósítás megkezdése előtt Szánjon időt arra, hogy gondosan tervezze meg az üzembe helyezést és az összes érintett összetevőt, például a virtuális gépeket, az NFS-csatlakoztatásokat, a VIP-ket és a terheléselosztó konfigurációját  

Az NFS-kiszolgáló, az SAP NetWeaver ASCS, az SAP NetWeaver SCS, az SAP NetWeaver ERS és a SAP HANA adatbázis virtuális gazdagépeket és virtuális IP-címeket használ. Az Azure-ban a virtuális IP-címek használatához terheléselosztó szükséges. A [standard Load Balancer](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md)használatát javasoljuk.  

A következő lista az (A) SCS és ERS Load Balancer konfigurációját mutatja be ehhez a többszörös SID-fürthöz, például három SAP-rendszerrel. A biztonsági azonosítók mindegyikéhez külön előtérbeli IP-címet, állapot-mintavételi és terheléselosztási szabályokat kell megadnia minden egyes ASCS és ERS-példányhoz. Rendelje hozzá az összes virtuális gépet, amely a ASCS/ASCS-fürt részét képezi egy háttér-készletnek.  

### <a name="ascs"></a>Egy SCS

* Előtér-konfiguráció
  * A NW1 IP-címe: 10.3.1.14
  * A NW2 IP-címe: 10.3.1.16
  * A NW3 IP-címe: 10.3.1.13
* Mintavételi portok
  * 620  -es port, ezért a NW1, a NW2 és a NW3 mintavételi portok esetében 620 00, 620 10 és 620 20<strong> &lt; &gt; </strong>
* Terheléselosztási szabályok – 
* hozzon létre egyet minden példányhoz, azaz: NW1/ASCS, NW2/ASCS és NW3/ASCS.
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
  * A NW1 10.3.1.15 IP-címe
  * A NW2 10.3.1.17 IP-címe
  * A NW3 10.3.1.19 IP-címe
* Mintavételi port
  * 621  -es port, ezért a NW1, a NW2 és a N # mintavételi portok esetében 621 02, 621 12 és 621 22<strong> &lt; &gt; </strong>
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

## <a name="sap-nfs-shares"></a>SAP NFS-megosztások

Az SAP NetWeaver megosztott tárterületet igényel az átvitelhez, a profil könyvtárához stb. A nagy rendelkezésre állású SAP-rendszerek esetében fontos a nagy rendelkezésre állású NFS-megosztások használata. Az SAP NFS-megosztások architektúráját is el kell döntenie. Az egyik lehetőség, hogy a SUSE Linux Enterprise Server-on található Azure-beli virtuális gépeken, több SAP-rendszer között megosztható, [jól elérhető NFS-fürtöt][nfs-ha]építhet ki. 

Egy másik lehetőség a megosztások telepítése [Azure NETAPP Files NFS-köteteken](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).  A Azure NetApp Files az SAP NFS-megosztások beépített magas rendelkezésre állását fogja kapni.

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>Az első SAP-rendszer üzembe helyezése a fürtben

Most, hogy eldöntötte az SAP NFS-megosztások architektúráját, a megfelelő dokumentációt követve telepítse az első SAP-rendszerét a fürtben.

* Ha magas rendelkezésre állású NFS-kiszolgálót használ, kövesse [Az Azure-beli virtuális gépeken az SAP NetWeaver magas rendelkezésre állását SUSE Linux Enterprise Server SAP-alkalmazásokhoz](./high-availability-guide-suse.md).  
* Ha Azure NetApp Files NFS-köteteket használ, kövesse [Az Azure-beli virtuális gépeken az SAP NetWeaver magas rendelkezésre állását a Azure NetApp Files for SAP Applications](./high-availability-guide-suse-netapp-files.md) szolgáltatásban SUSE Linux Enterprise Server

A fent felsorolt dokumentumok végigvezetik a szükséges infrastruktúrák előkészítésének lépésein, a fürt összeállításán, az operációs rendszernek az SAP-alkalmazás futtatásához való előkészítésének lépésein.  

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

1. Adja hozzá a konfigurációt az újonnan telepített rendszerhez (azaz **NW2**, **NW3**) a meglévő Azure Load Balancerhoz, és az utasításokat követve [telepítse manuálisan a Azure Load Balancert Azure Portal használatával](./high-availability-guide-suse-netapp-files.md#deploy-azure-load-balancer-manually-via-azure-portal). Módosítsa az IP-címeket, az állapot-mintavételi portokat, a konfiguráció terheléselosztási szabályait.  

2. **[A]** a további SAP-rendszerek névfeloldásának beállítása. Használhatja a DNS-kiszolgálót, vagy módosíthatja `/etc/hosts` az összes csomópontot. Ez a példa a fájl használatát mutatja be `/etc/hosts` .  Igazítsa az IP-címeket és az állomásneveket a környezethez. 

    ```
    sudo vi /etc/hosts
    # IP address of the load balancer frontend configuration for NW2 ASCS
    10.3.1.16 msnw2ascs
    # IP address of the load balancer frontend configuration for NW3 ASCS
    10.3.1.13 msnw3ascs
    # IP address of the load balancer frontend configuration for NW2 ERS
    10.3.1.17 msnw2ers
    # IP address of the load balancer frontend configuration for NW3 ERS
    10.3.1.19 msnw3ers
    # IP address for virtual host name for the NFS server for NW2
    10.3.1.31 nw2-nfs
    # IP address for virtual host name for the NFS server for NW3
    10.3.1.32 nw3-nfs
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

4. **[A]** konfigurálja a `autofs` /Sapmnt/SID-és/usr/sap/SID/sys-fájlrendszerek csatlakoztatását a fürtön üzembe helyezett további SAP-rendszerekhez. Ebben a példában a **NW2** és a **NW3**.  

   Frissítse a fájlrendszert a `/etc/auto.direct` fürtön üzembe helyezett további SAP-rendszerekhez.  

   * Ha NFS-kiszolgálószolgáltatást használ, kövesse az [itt](./high-availability-guide-suse.md#prepare-for-sap-netweaver-installation) található utasításokat.
   * Ha Azure NetApp Files használ, kövesse az [itt](./high-availability-guide-suse-netapp-files.md#prepare-for-sap-netweaver-installation) található utasításokat. 

   Az `autofs` újonnan hozzáadott megosztások csatlakoztatásához újra kell indítania a szolgáltatást.  

### <a name="install-ascs--ers"></a>ASCS/ERS telepítése

1. Hozza létre a virtuális IP-és állapot-mintavételi fürt erőforrásait a fürtre telepítendő további SAP-rendszer ASCS-példányához. Az itt látható példa a **NW2** és a **NW3** ASCS, amely a jól elérhető NFS-kiszolgáló használatával történik.  

   > [!IMPORTANT]
   > A közelmúltbeli tesztelés feltárta a helyzeteket, ahol a netcat nem válaszol a várakozó kérelmekre, és csak egyetlen kapcsolat kezelésére vonatkozó korlátozásokat okoz. A netcat erőforrás nem figyeli az Azure Load Balancer kéréseit, és a lebegőpontos IP-cím elérhetetlenné válik.  
   > A meglévő pacemaker-fürtök esetében a netcat és a socat helyett a múltban javasolt. Jelenleg a csomag erőforrás-ügynökök részét képező Azure-LB erőforrás-ügynök használatát javasoljuk a következő csomag-verzióra vonatkozó követelményekkel:
   > - A SLES 12 SP4/SP5 esetében a verziónak legalább Resource-Agent-4.3.018. a7fb5035-3.30.1 kell lennie.  
   > - A SLES 15/15 SP1 esetében a verziónak legalább Resource-Agent-4.3.0184.6 ee15eb2-4.13.1 kell lennie.  
   >
   > Vegye figyelembe, hogy a módosítás rövid állásidőt igényel.  
   > Meglévő pacemaker-fürtök esetén, ha a konfigurációt már úgy módosították, hogy a socat használja az [azure Load-Balancer észlelésének megerősítése](https://www.suse.com/support/kb/doc/?id=7024128)című cikkben leírtak szerint, nem kell azonnal váltania az Azure-LB erőforrás-ügynökre.

    ```
      sudo crm configure primitive fs_NW2_ASCS Filesystem device='nw2-nfs:/NW2/ASCS' directory='/usr/sap/NW2/ASCS10' fstype='nfs4' \
       op start timeout=60s interval=0 \
       op stop timeout=60s interval=0 \
       op monitor interval=20s timeout=40s
   
      sudo crm configure primitive vip_NW2_ASCS IPaddr2 \
        params ip=10.3.1.16 cidr_netmask=24 \
        op monitor interval=10 timeout=20
   
      sudo crm configure primitive nc_NW2_ASCS azure-lb port=62010
   
      sudo crm configure group g-NW2_ASCS fs_NW2_ASCS nc_NW2_ASCS vip_NW2_ASCS \
         meta resource-stickiness=3000

      sudo crm configure primitive fs_NW3_ASCS Filesystem device='nw3-nfs:/NW3/ASCS' directory='/usr/sap/NW3/ASCS20' fstype='nfs4' \
        op start timeout=60s interval=0 \
        op stop timeout=60s interval=0 \
        op monitor interval=20s timeout=40s
   
      sudo crm configure primitive vip_NW3_ASCS IPaddr2 \
       params ip=10.3.1.13 cidr_netmask=24 \
       op monitor interval=10 timeout=20
   
      sudo crm configure primitive nc_NW3_ASCS azure-lb port=62020
   
      sudo crm configure group g-NW3_ASCS fs_NW3_ASCS nc_NW3_ASCS vip_NW3_ASCS \
        meta resource-stickiness=3000
    ```

   Az erőforrások létrehozásakor előfordulhat, hogy a különböző fürterőforrás-erőforrásokhoz vannak rendelve. A csoportosítás során a rendszer áttelepíti azokat a fürtcsomópontok egyik csomópontjára. Győződjön meg arról, hogy a fürt állapota ok, és hogy minden erőforrás elindult. Nem fontos, hogy az erőforrások melyik csomóponton futnak.

2. **[1]** az SAP NetWeaver ASCS telepítése  

   Telepítse az SAP NetWeaver-ASCS root-ként, egy virtuális állomásnév használatával, amely a ASCS tartozó terheléselosztó előtér-konfigurációjának IP-címét képezi le. A rendszer **NW2** például a virtuális állomásnév a <b>msnw2ascs</b>, a <b>10.3.1.16</b> és a terheléselosztó mintavételéhez használt példány száma, például <b>10</b>. a rendszer **NW3** a virtuális állomásnév a <b>msnw3ascs</b>, a <b>10.3.1.13</b> és a terheléselosztó mintavételéhez használt példány száma, például <b>20</b>.

   A sapinst paraméterrel SAPINST_REMOTE_ACCESS_USER engedélyezheti, hogy a nem root felhasználó csatlakozhasson a sapinst. A (z) SAPINST_USE_HOSTNAME paraméter használatával telepítheti az SAP-t a virtuális gazdagép nevével.  

     ```
      sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
     ```

   Ha a telepítés során nem sikerül almappát létrehozni a/usr/SAP/**SID**/ASCS-**példányában**, próbálja meg a tulajdonost **SID** adm-re állítani, és a ASCS-**példány** sapsys, majd próbálkozzon újra.

3. **[1]** hozzon létre egy virtuális IP-és állapot-mintavételi fürt erőforrásait a fürtre TELEPÍTENDŐ további SAP-rendszer ERS-példányához. Az itt látható példa a **NW2** és a **NW3** -esekre, a magasan rendelkezésre álló NFS-kiszolgáló használatával. 

   ```
    sudo crm configure primitive fs_NW2_ERS Filesystem device='nw2-nfs:/NW2/ASCSERS' directory='/usr/sap/NW2/ERS12' fstype='nfs4' \
      op start timeout=60s interval=0 \
      op stop timeout=60s interval=0 \
      op monitor interval=20s timeout=40s
   
    sudo crm configure primitive vip_NW2_ERS IPaddr2 \
      params ip=10.3.1.17 cidr_netmask=24 \
      op monitor interval=10 timeout=20
   
    sudo crm configure primitive nc_NW2_ERS azure-lb port=62112
   
    sudo crm configure group g-NW2_ERS fs_NW2_ERS nc_NW2_ERS vip_NW2_ERS

    sudo crm configure primitive fs_NW3_ERS Filesystem device='nw3-nfs:/NW3/ASCSERS' directory='/usr/sap/NW3/ERS22' fstype='nfs4' \
      op start timeout=60s interval=0 \
      op stop timeout=60s interval=0 \
      op monitor interval=20s timeout=40s
   
    sudo crm configure primitive vip_NW3_ERS IPaddr2 \
      params ip=10.3.1.19 cidr_netmask=24 \
      op monitor interval=10 timeout=20
   
    sudo crm configure primitive nc_NW3_ERS azure-lb port=62122
   
    sudo crm configure group g-NW3_ERS fs_NW3_ERS nc_NW3_ERS vip_NW3_ERS
   ```

   Az erőforrások létrehozásakor előfordulhat, hogy a fürt különböző csomópontjaihoz van rendelve. A csoportosítás során a rendszer áttelepíti azokat a fürtcsomópontok egyik csomópontjára. Győződjön meg arról, hogy a fürt állapota ok, és hogy minden erőforrás elindult.  

   Ezután győződjön meg arról, hogy az újonnan létrehozott ERS-csoport erőforrásai futnak a fürtcsomóponton, szemben azzal a fürtcsomópont-csomóponttal, ahol a ASCS-példány ugyanarra az SAP-rendszerre lett telepítve.  Ha például a NW2 ASCS telepítve `slesmsscl1` van, akkor győződjön meg arról, hogy a NW2 ERS csoport fut `slesmsscl2` .  A NW2 ERS Group a `slesmsscl2` következő parancs futtatásával telepíthető át: 

    ```
      crm resource migrate g-NW2_ERS slesmsscl2 force
    ```

4. **[2]** SAP NETWEAVER-ERS telepítése

   Telepítse az SAP NetWeaver-ket root-ként a másik csomóponton, egy virtuális állomásnév használatával, amely a terheléselosztó előtér-konfigurációjának IP-címét képezi le az ERS-hoz. Például a rendszerszintű **NW2** a virtuális állomásnév a <b>msnw2ers</b>, a <b>10.3.1.17</b> és a terheléselosztó mintavételéhez használt példány száma, például <b>12</b>. A rendszer **NW3** a virtuális állomásnév <b>msnw3ers</b>, a <b>10.3.1.19</b> és a terheléselosztó mintavételéhez használt példány számát, például <b>22</b>. 

   A sapinst paraméterrel SAPINST_REMOTE_ACCESS_USER engedélyezheti, hogy a nem root felhasználó csatlakozhasson a sapinst. A (z) SAPINST_USE_HOSTNAME paraméter használatával telepítheti az SAP-t a virtuális gazdagép nevével.  

    ```
     sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > Használja az SWPM SP 20 PL 05-es vagy újabb verzióját. Az alacsonyabb verziók nem tudják megfelelően beállítani az engedélyeket, és a telepítés sikertelen lesz.

   Ha a telepítés nem tud almappát létrehozni a/usr/SAP/**NW2**/ERS-**példányban**, próbálja meg beállítani a tulajdonost a **SID** adm-re, és a csoportot az ERS-**példány #** mappa sapsys, és próbálkozzon újra.

   Ha szükséges, hogy áttelepítse az újonnan telepített SAP-rendszer ERS-csoportját egy másik fürtcsomóponton, ne felejtse el eltávolítani az ERS csoporthoz tartozó hely megkötését. A korlátozást a következő parancs futtatásával távolíthatja el (ez a példa az SAP Systems **NW2** és a **NW3** esetében van megadva).  

    ```
      crm resource unmigrate g-NW2_ERS
      crm resource unmigrate g-NW3_ERS
    ```

5. **[1]** a ASCS/SCS és a ERS instance profilokat az újonnan telepített SAP-rendszer (ek) hez igazíthatja. Az alább látható példa a NW2. A fürthöz hozzáadott összes SAP-példányhoz alkalmazkodnia kell a ASCS/SCS és a ERS profilhoz.  
 
 * ASCS/SCS-profil

   ```
   sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
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
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```

6. **[A]** KONFIGURÁLJA az SAP-felhasználókat az újonnan telepített SAP-rendszerhez, ebben a példában a **NW2** és a **NW3**. 

   ```
   # Add sidadm to the haclient group
   sudo usermod -aG haclient nw2adm
   sudo usermod -aG haclient nw3adm
   ```

7. Adja hozzá az újonnan telepített SAP-rendszerhez tartozó ASCS és ERS SAP-szolgáltatásokat a `sapservice` fájlhoz. Az alább látható példa az SAP Systems **NW2** és a **NW3**.  

   Adja hozzá a ASCS-szolgáltatási bejegyzést a második csomóponthoz, és másolja az ERS szolgáltatási bejegyzést az első csomópontra. Hajtsa végre a parancsokat minden egyes SAP-rendszeren a csomóponton, ahol az SAP-rendszer ASCS-példánya telepítve lett.  

    ```
     # Execute the following commands on slesmsscl1,assuming the NW2 ASCS instance was installed on slesmsscl1
     cat /usr/sap/sapservices | grep ASCS10 | sudo ssh slesmsscl2 "cat >>/usr/sap/sapservices"
     sudo ssh slesmsscl2 "cat /usr/sap/sapservices" | grep ERS12 | sudo tee -a /usr/sap/sapservices
     # Execute the following commands on slesmsscl2, assuming the NW3 ASCS instance was installed on slesmsscl2
     cat /usr/sap/sapservices | grep ASCS20 | sudo ssh slesmsscl1 "cat >>/usr/sap/sapservices"
     sudo ssh slesmsscl1 "cat /usr/sap/sapservices" | grep ERS22 | sudo tee -a /usr/sap/sapservices
    ```

8. **[1]** hozza létre az SAP-fürt erőforrásait az újonnan telepített SAP-rendszerhez. 

   Ha a sorba helyezni Server 1 architektúráját (ENSA1) használja, az alábbi módon határozza meg az SAP Systems **NW2** és **NW3** erőforrásait:

    ```
     sudo crm configure property maintenance-mode="true"
    
     sudo crm configure primitive rsc_sap_NW2_ASCS10 SAPInstance \
      operations \$id=rsc_sap_NW2_ASCS10-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
    
     sudo crm configure primitive rsc_sap_NW2_ERS12 SAPInstance \
      operations \$id=rsc_sap_NW2_ERS12-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true \
      meta priority=1000
    
     sudo crm configure modgroup g-NW2_ASCS add rsc_sap_NW2_ASCS10
     sudo crm configure modgroup g-NW2_ERS add rsc_sap_NW2_ERS12
    
     sudo crm configure colocation col_sap_NW2_no_both -5000: g-NW2_ERS g-NW2_ASCS
     sudo crm configure location loc_sap_NW2_failover_to_ers rsc_sap_NW2_ASCS10 rule 2000: runs_ers_NW2 eq 1
     sudo crm configure order ord_sap_NW2_first_start_ascs Optional: rsc_sap_NW2_ASCS10:start rsc_sap_NW2_ERS12:stop symmetrical=false
   
     sudo crm configure primitive rsc_sap_NW3_ASCS20 SAPInstance \
      operations \$id=rsc_sap_NW3_ASCS20-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ASCS10_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
    
     sudo crm configure primitive rsc_sap_NW3_ERS22 SAPInstance \
      operations \$id=rsc_sap_NW3_ERS22-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW3_ERS22_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true \
      meta priority=1000
    
     sudo crm configure modgroup g-NW3_ASCS add rsc_sap_NW3_ASCS20
     sudo crm configure modgroup g-NW3_ERS add rsc_sap_NW3_ERS22
    
     sudo crm configure colocation col_sap_NW3_no_both -5000: g-NW3_ERS g-NW3_ASCS
     sudo crm configure location loc_sap_NW3_failover_to_ers rsc_sap_NW3_ASCS10 rule 2000: runs_ers_NW3 eq 1
     sudo crm configure order ord_sap_NW3_first_start_ascs Optional: rsc_sap_NW3_ASCS20:start rsc_sap_NW3_ERS22:stop symmetrical=false
     sudo crm configure property maintenance-mode="false"
    ```

   Az SAP bevezette a 2. sorba helyezni-kiszolgáló, beleértve a replikálást, az SAP NW 7,52-támogatását. A ABAP platform 1809-től kezdődően a sorba helyezni Server 2 alapértelmezés szerint telepítve van. Lásd: SAP-Megjegyzés [2630416](https://launchpad.support.sap.com/#/notes/2630416) a sorba helyezni Server 2 támogatásához.
   Ha a sorba helyezni Server 2 architektúráját ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)) használja, az alábbi módon határozza meg az SAP Systems **NW2** és **NW3** erőforrásait:

    ```
     sudo crm configure property maintenance-mode="true"
    
     sudo crm configure primitive rsc_sap_NW2_ASCS10 SAPInstance \
      operations \$id=rsc_sap_NW2_ASCS10-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 
    
     sudo crm configure primitive rsc_sap_NW2_ERS12 SAPInstance \
      operations \$id=rsc_sap_NW2_ERS12-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true 
    
     sudo crm configure modgroup g-NW2_ASCS add rsc_sap_NW2_ASCS10
     sudo crm configure modgroup g-NW2_ERS add rsc_sap_NW2_ERS12
    
     sudo crm configure colocation col_sap_NW2_no_both -5000: g-NW2_ERS g-NW2_ASCS
     sudo crm configure order ord_sap_NW2_first_start_ascs Optional: rsc_sap_NW2_ASCS10:start rsc_sap_NW2_ERS12:stop symmetrical=false
   
     sudo crm configure primitive rsc_sap_NW3_ASCS20 SAPInstance \
      operations \$id=rsc_sap_NW3_ASCS20-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ASCS10_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000
    
     sudo crm configure primitive rsc_sap_NW3_ERS22 SAPInstance \
      operations \$id=rsc_sap_NW3_ERS22-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW3_ERS22_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true
    
     sudo crm configure modgroup g-NW3_ASCS add rsc_sap_NW3_ASCS20
     sudo crm configure modgroup g-NW3_ERS add rsc_sap_NW3_ERS22
    
     sudo crm configure colocation col_sap_NW3_no_both -5000: g-NW3_ERS g-NW3_ASCS
     sudo crm configure order ord_sap_NW3_first_start_ascs Optional: rsc_sap_NW3_ASCS20:start rsc_sap_NW3_ERS22:stop symmetrical=false
     sudo crm configure property maintenance-mode="false"
    ```

   Ha egy régebbi verzióról frissít, és átvált a 2. sorba helyezni-kiszolgálóra, tekintse meg a következőt: SAP Note [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   Győződjön meg arról, hogy a fürt állapota ok, és hogy az összes erőforrás el van indítva. Nem fontos, hogy az erőforrások melyik csomóponton futnak.
   Az alábbi példa a fürterőforrás állapotát mutatja be, miután az SAP Systems **NW2** és **NW3** a fürthöz lettek adva. 

    ```
     sudo crm_mon -r
    
    # Online: [ slesmsscl1 slesmsscl2 ]
    
    #Full list of resources:
    
    #stonith-sbd     (stonith:external/sbd): Started slesmsscl1
    # Resource Group: g-NW1_ASCS
    #     fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
    #     vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    # Resource Group: g-NW1_ERS
    #     fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
    #     vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW2_ASCS
    #     fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
    #     vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW2_ERS
    #     fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
    #     vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    # Resource Group: g-NW3_ASCS
    #     fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
    #     vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW3_ERS
    #     fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
    #     vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    ```

   Az alábbi képen látható, hogyan jelennek meg az erőforrások a HA web-konzolon (Hawk), az SAP System **NW2** erőforrásainak kibontásával.  

   [![SAP NetWeaver – magas rendelkezésre állás – áttekintés](./media/high-availability-guide-suse/ha-suse-multi-sid-hawk.png)](./media/high-availability-guide-suse/ha-suse-multi-sid-hawk-detail.png#lightbox)

### <a name="proceed-with-the-sap-installation"></a>Az SAP telepítésének folytatása 

Fejezze be az SAP telepítését:

* [Az SAP NetWeaver alkalmazás-kiszolgálók előkészítése](./high-availability-guide-suse.md#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [Adatbázis-kezelő példány telepítése](./high-availability-guide-suse.md#install-database)
* [Elsődleges SAP-alkalmazáskiszolgáló telepítése](./high-availability-guide-suse.md#sap-netweaver-application-server-installation)
* Egy vagy több további SAP-alkalmazás példányának telepítése

## <a name="test-the-multi-sid-cluster-setup"></a>A többszörös SID-fürt beállításának tesztelése

Az alábbi tesztek a SUSE ajánlott eljárási útmutatójában szereplő tesztelési esetek egy részét képezik. Az Ön kényelme érdekében a szolgáltatás részét képezi. A fürt tesztek teljes listáját a következő dokumentációban találja:

* Ha magas rendelkezésre állású NFS-kiszolgálót használ, kövesse [Az Azure-beli virtuális gépeken az SAP NetWeaver magas rendelkezésre állását SUSE Linux Enterprise Server SAP-alkalmazásokhoz](./high-availability-guide-suse.md).  
* Ha Azure NetApp Files NFS-köteteket használ, kövesse [Az Azure-beli virtuális gépeken az SAP NetWeaver magas rendelkezésre állását a Azure NetApp Files for SAP Applications](./high-availability-guide-suse-netapp-files.md) szolgáltatásban SUSE Linux Enterprise Server

Mindig olvassa el a SUSE ajánlott eljárásokat ismertető útmutatóit, és végezze el az esetlegesen hozzáadott további tesztek elvégzését.  
A bemutatott tesztek egy két csomóponton, több SID-fürtön, három SAP-rendszerrel vannak telepítve.  

1. HAGetFailoverConfig és HACheckFailoverConfig tesztelése

   Futtassa az alábbi parancsokat adm-ként <sapsid> azon a csomóponton, amelyen a ASCS-példány jelenleg fut. Ha a parancsok meghiúsulnak: nincs elég memória, a gazdagép kötőjelei okozhatja. Ez egy ismert probléma, amelyet a SUSE az SAP-SUSE-cluster-Connector csomagban fog megállapítani.

   ```
    slesmsscl1:nw1adm 57> sapcontrol -nr 00 -function HAGetFailoverConfig

   # 10.12.2019 21:33:08
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl1
   # HANodes: slesmsscl1, slesmsscl2

    slesmsscl1:nw1adm 53> sapcontrol -nr 00 -function HACheckFailoverConfig

    # 19.12.2019 21:19:58
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch

    slesmsscl2:nw2adm 35> sapcontrol -nr 10 -function HAGetFailoverConfig

   # 10.12.2019 21:37:09
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl2
   # HANodes: slesmsscl2, slesmsscl1

    slesmsscl2:nw2adm 52> sapcontrol -nr 10 -function HACheckFailoverConfig

    # 19.12.2019 21:17:39
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch

    slesmsscl1:nw3adm 49> sapcontrol -nr 20 -function HAGetFailoverConfig

   # 10.12.2019 23:35:36
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl1
   # HANodes: slesmsscl1, slesmsscl2

    slesmsscl1:nw3adm 52> sapcontrol -nr 20 -function HACheckFailoverConfig

    # 19.12.2019 21:10:42
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   ```

2. Telepítse manuálisan a ASCS-példányt. A példa bemutatja az ASCS-példány áttelepítését az SAP rendszer NW2.  
   Erőforrás állapota a teszt elindítása előtt:
   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   Futtassa a következő parancsokat root-ként a NW2 ASCS-példány áttelepíteni.

   ```
    crm resource migrate rsc_sap_NW2_ASCS10 force
    # INFO: Move constraint created for rsc_sap_NW2_ASCS10
    
    crm resource unmigrate rsc_sap_NW2_ASCS10
   # INFO: Removed migration constraints for rsc_sap_NW2_ASCS10
   
   # Remove failed actions for the ERS that occurred as part of the migration
    crm resource cleanup rsc_sap_NW2_ERS12
   ```

   Erőforrás állapota a teszt után:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

1. HAFailoverToNode tesztelése. Az itt bemutatott teszt a ASCS-példány áttelepítését mutatja be az SAP rendszer NW2.  

   Erőforrás állapota a teszt elindítása előtt:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   Futtassa a következő parancsokat a **NW2** adm használatával a NW2 ASCS-példány áttelepíteni.

   ```
    slesmsscl2:nw2adm 53> sapcontrol -nr 10 -host msnw2ascs -user nw2adm password -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   crm resource cleanup rsc_sap_NW2_ERS12
   # Remove migration constraints
   crm resource clear rsc_sap_NW2_ASCS10
   #INFO: Removed migration constraints for rsc_sap_NW2_ASCS10
   ```

   Erőforrás állapota a teszt után:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

1. Csomópont összeomlásának szimulálása

   Erőforrás állapota a teszt elindítása előtt:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   Futtassa a következő parancsot gyökérként azon a csomóponton, amelyen legalább egy ASCS-példány fut. Ebben a példában a parancsot futtatjuk `slesmsscl2` , ahol a NW1 és a NW3 ASCS példányai futnak.  

   ```
    slesmsscl2:~ # echo b > /proc/sysrq-trigger
   ```

   Ha SBD használ, a pacemaker nem kezdődhet automatikusan a megölt csomóponton. A csomópont újraindítása utáni állapotnak így kell kinéznie.

   ```
    Online: [ slesmsscl1 ]
    OFFLINE: [ slesmsscl2 ]
    Full list of resources:

    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    
    Failed Resource Actions:
    * rsc_sap_NW1_ERS02_monitor_11000 on slesmsscl1 'not running' (7): call=125, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
    * rsc_sap_NW2_ERS12_monitor_11000 on slesmsscl1 'not running' (7): call=126, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
    * rsc_sap_NW3_ERS22_monitor_11000 on slesmsscl1 'not running' (7): call=127, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
   ```

   Az alábbi parancsokkal elindíthatja a pacemakert a megölt csomóponton, megtisztíthatja a SBD-üzeneteket, és megtisztíthatja a sikertelen erőforrásokat.

   ```bash
   # run as root
   # list the SBD device(s)
   cat /etc/sysconfig/sbd | grep SBD_DEVICE=

   # output is like:
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message slesmsscl2 clear
   
   systemctl start pacemaker
   crm resource cleanup rsc_sap_NW1_ERS02
   crm resource cleanup rsc_sap_NW2_ERS12
   crm resource cleanup rsc_sap_NW3_ERS22
   ```

   Erőforrás állapota a teszt után:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
   ```

## <a name="next-steps"></a>Következő lépések

* [Azure Virtual Machines az SAP tervezéséhez és megvalósításához][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP-ban][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hoz][dbms-guide]
* A magas rendelkezésre állás és a SAP HANA Azure-beli virtuális gépeken történő vész-helyreállítási tervének megismeréséhez lásd: [Az Azure-beli SAP HANA magas rendelkezésre állása Virtual Machines (VM)][sap-hana-ha]
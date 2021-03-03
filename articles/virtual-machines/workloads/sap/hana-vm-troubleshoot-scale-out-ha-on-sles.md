---
title: Az Azure-beli virtuális gépekkel kapcsolatos SLES SAP HANA kibővíthető HSR-Pacemaker Microsoft Docs
description: Útmutató az Azure Virtual Machines szolgáltatásban futó SLES 12 SP3 rendszerű, összetett SAP HANA kibővíthető magas rendelkezésre állási konfigurációjának vizsgálatához és hibakereséséhez SAP HANA rendszerreplikáció (HSR) és a pacemaker alapján
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/24/2018
ms.author: hermannd
ms.openlocfilehash: f4c1de484ce2659a7e84a1546a7c49c1d77a7d56
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101674486"
---
# <a name="verify-and-troubleshoot-sap-hana-scale-out-high-availability-setup-on-sles-12-sp3"></a>A kibővíthető magas rendelkezésre állású telepítés ellenőrzése és SAP HANA megoldása a SLES 12 SP3 rendszeren 

[sles-pacemaker-ha-guide]:high-availability-guide-suse-pacemaker.md
[sles-hana-scale-out-ha-paper]:https://www.suse.com/documentation/suse-best-practices/singlehtml/SLES4SAP-hana-scaleOut-PerfOpt-12/SLES4SAP-hana-scaleOut-PerfOpt-12.html
[sap-hana-iaas-list]:https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html
[suse-pacemaker-support-log-files]:https://www.suse.com/support/kb/doc/?id=7022702
[azure-linux-multiple-nics]:https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics
[suse-cloud-netconfig]:https://www.suse.com/c/multi-nic-cloud-netconfig-ec2-azure/
[sap-list-port-numbers]:https://help.sap.com/viewer/ports
[sles-12-ha-paper]:https://www.suse.com/documentation/sle-ha-12/pdfdoc/book_sleha/book_sleha.pdf
[sles-zero-downtime-paper]:https://www.suse.com/media/presentation/TUT90846_towards_zero_downtime%20_how_to_maintain_sap_hana_system_replication_clusters.pdf
[sap-nw-ha-guide-sles]:high-availability-guide-suse.md
[sles-12-for-sap]:https://www.suse.com/media/white-paper/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf


Ebből a cikkből megtudhatja, hogy az Azure Virtual Machines (VM) szolgáltatásban futó SAP HANA kibővíthető-e a pacemaker-fürt konfigurációja. A fürt beállítása SAP HANA rendszerreplikációval (HSR) és a SUSE RPM-csomaggal (SAPHanaSR-horizontális felskálázás) együtt valósult meg. Az összes teszt csak a SUSE SLES 12 SP3 szervizcsomaggal lett végrehajtva. A cikk fejezetei különböző területekre terjednek ki, és a konfigurációs fájlokból származó minta parancsokat és kivonatokat tartalmaznak. Ezeket a mintákat ajánlott módszerként beállítani a teljes fürtkonfiguráció ellenőrzéséhez és ellenőrzéséhez.



## <a name="important-notes"></a>Fontos megjegyzések

A SAP HANA SAP HANA rendszer-replikációval és a pacemakerrel együtt végzett összes tesztelés csak SAP HANA 2,0-es verzióval történt. Az operációs rendszer verziószáma SUSE Linux Enterprise Server 12 SP3 az SAP-alkalmazásokhoz. A pacemaker-fürt beállításához a legújabb RPM-csomagot, SAPHanaSR-ScaleOut a SUSE-től kezdve használták.
A SUSE közzétette a [teljesítményre optimalizált telepítő részletes leírását][sles-hana-scale-out-ha-paper].

SAP HANA kibővíthető virtuálisgép-típusok esetén tekintse meg a [SAP HANA Certified IaaS könyvtárat][sap-hana-iaas-list].

> [!NOTE]
> Ez a cikk a " *Master* " és a " *Slave*" kifejezésekre mutató hivatkozásokat tartalmaz, amelyeket a Microsoft már nem használ. Ha eltávolítja ezeket a feltételeket a szoftverből, a rendszer eltávolítja őket ebből a cikkből.

Technikai probléma merült fel SAP HANA több alhálózattal és Vnic, valamint a HSR beállításával együttesen felskálázással. A probléma javításához a legújabb SAP HANA 2,0-es javításokat kell használni. A következő SAP HANA verziók támogatottak: 

* rev 2.00.024.04 vagy újabb 
* rev 2.00.032 vagy újabb

Ha a SUSE támogatásra van szüksége, kövesse ezt az [útmutatót][suse-pacemaker-support-log-files]. Gyűjtsön információkat a SAP HANA magas rendelkezésre állású (HA) fürtről a cikkben leírtak szerint. A SUSE-támogatásnak további elemzés céljából szüksége van ezekre az információkra.

A belső tesztelés során a fürt beállítása megzavarodott egy normál, kecses virtuális gép leállításával a Azure Portalon keresztül. Ezért azt javasoljuk, hogy más módszerekkel tesztelje a fürt feladatátvételét. Használjon olyan metódusokat, mint például a kernel-pánik kényszerítése vagy a hálózatok leállítása vagy az **MSL** -erőforrás áttelepíthetők. Tekintse meg a részleteket az alábbi részekben. Feltételezi, hogy a szokásos leállás a szándékkal történik. A szándékos leállítás legjobb példája a karbantartás. Tekintse meg a [tervezett karbantartás](#planned-maintenance)részleteit.

Emellett a belső tesztelés során a fürt beállítása megzavarodott volt a manuális SAP HANA átvétele után, miközben a fürt karbantartási módban volt. Javasoljuk, hogy a fürt karbantartási módjának befejezése előtt manuálisan váltson vissza. Egy másik lehetőség a feladatátvétel elindítása, mielőtt a fürtöt karbantartási módba helyezné. További információ: [tervezett karbantartás](#planned-maintenance). A SUSE dokumentációja azt ismerteti, hogyan állíthatja alaphelyzetbe a fürtöt a **CRM** parancs használatával. De a korábban említett megközelítés robusztus volt a belső tesztelés során, és soha nem mutatott semmilyen váratlan mellékhatást.

A **CRM áttelepítési** parancs használatakor ügyeljen arra, hogy törölje a fürt konfigurációját. Felveszi a helyhez tartozó korlátozásokat, amelyeket esetleg nem ismer. Ezek a korlátozások befolyásolják a fürt viselkedését. További információ a [tervezett karbantartásról](#planned-maintenance).



## <a name="test-system-description"></a>Test System Description

 SAP HANA kibővíthető if ellenőrzés és tanúsítás esetén a telepítőt használták. Két rendszerből áll, amelyek mindegyike három SAP HANA csomóponttal rendelkezik: egy főkiszolgálóval és két feldolgozóval. A következő táblázat a virtuális gépek nevét és belső IP-címeit sorolja fel. Az alábbi összes ellenőrző minta ezen a virtuális gépeken lett végrehajtva. Ha ezeket a virtuális gépek nevét és IP-címeit használja a parancs mintájában, jobban megismerheti a parancsokat és azok kimeneteit:


| Csomópont típusa | a virtuális gép neve | IP-cím |
| --- | --- | --- |
| Fő csomópont az 1. helyen | hso-Hana-VM-S1-0 | 10.0.0.30 |
| 1. munkavégző csomópont az 1. helyen | hso-Hana-VM-S1-1 | 10.0.0.31 |
| 2. feldolgozói csomópont az 1. helyen | hso-Hana-VM-s1-2 | 10.0.0.32 |
| | | |
| Fő csomópont a 2. helyen | hso-Hana-VM-S2-0 | 10.0.0.40 |
| 1. munkavégző csomópont a 2. helyen | hso-Hana-VM-S2-1 | 10.0.0.41 |
| 2. munkavégző csomópont a 2. helyen | hso-Hana-VM-S2-2  | 10.0.0.42 |
| | | |
| Többségi gyártó csomópontja | hso – Hana-DM | 10.0.0.13 |
| SBD-eszköz kiszolgálója | hso – Hana-SBD | 10.0.0.19 |
| | | |
| NFS-kiszolgáló 1 | hso – NFS – virtuális gép – 0 | 10.0.0.15 |
| NFS-kiszolgáló 2 | hso-NFS-VM-1 | 10.0.0.14 |



## <a name="multiple-subnets-and-vnics"></a>Több alhálózat és Vnic

A következő SAP HANA hálózati javaslatok közül három alhálózat jött létre egy Azure-beli virtuális hálózaton belül. SAP HANA az Azure-ban való méretezést nem megosztott módban kell telepíteni. Ez azt jelenti, hogy minden csomópont helyi lemezes köteteket használ a **/Hana/Data** és a **/Hana/log**. Mivel a csomópontok csak a helyi lemezes köteteket használják, nem szükséges külön alhálózatot definiálni a tárolóhoz:

- 10.0.2.0/24 SAP HANA csomópontok közötti kommunikációhoz
- 10.0.1.0/24 SAP HANA rendszer-replikáláshoz (HSR)
- 10.0.0.0/24 minden más számára

További információ a több hálózat használatával kapcsolatos SAP HANA konfigurációról: [SAP HANA global.ini](#sap-hana-globalini).

A fürtben lévő minden virtuális gépnek három Vnic van, amelyek megfelelnek az alhálózatok számának. Linux rendszerű [virtuális gép létrehozása az Azure-ban több hálózati adapterrel][azure-linux-multiple-nics] az Azure-beli lehetséges útválasztási probléma a Linux rendszerű virtuális gépek telepítésekor. Ez a konkrét útválasztási cikk csak a több Vnic használatára vonatkozik. A problémát a SUSE alapértelmezés szerint oldja meg a SLES 12 SP3 verzióban. További információ: [multi-NIC a Cloud-netconfig a EC2 és az Azure-ban][suse-cloud-netconfig].


A következő parancsok futtatásával ellenőrizheti, hogy a SAP HANA megfelelően van-e konfigurálva a több hálózat használatára. Először győződjön meg arról, hogy az operációs rendszer szintjén mindhárom belső IP-cím aktív. Ha az alhálózatokat eltérő IP-címtartományok alapján adta meg, akkor a következő parancsokat kell módosítania:

<pre><code>
ifconfig | grep "inet addr:10\."
</code></pre>

A következő minta kimenet a 2. hely második munkavégző csomópontján található. Három különböző belső IP-címet láthat a ETH0, a eth1 és a eth2 használatával:

<pre><code>
inet addr:10.0.0.42  Bcast:10.0.0.255  Mask:255.255.255.0
inet addr:10.0.1.42  Bcast:10.0.1.255  Mask:255.255.255.0
inet addr:10.0.2.42  Bcast:10.0.2.255  Mask:255.255.255.0
</code></pre>


Ezután ellenőrizze a SAP HANA portokat a névkiszolgáló és a HSR számára. SAP HANA figyelnie kell a megfelelő alhálózatokat. A SAP HANA példány számától függően módosítania kell a parancsokat. A tesztelési rendszer esetében a példány száma **00** volt. Többféle módon is megtudhatja, hogy mely portok vannak használatban. 

A következő SQL-utasítás a példány AZONOSÍTÓját, a példány számát és az egyéb információkat adja vissza:

<pre><code>
select * from "SYS"."M_SYSTEM_OVERVIEW"
</code></pre>

A megfelelő portszámok megkereséséhez tekintse meg például a HANA Studióban a **Configuration** vagy egy SQL-utasítás segítségével:

<pre><code>
select * from M_INIFILE_CONTENTS WHERE KEY LIKE 'listen%'
</code></pre>

Az SAP szoftveres veremben használt összes port megkereséséhez, beleértve a SAP HANAt, az [összes SAP-termék TCP/IP-portjait][sap-list-port-numbers]keresse meg.

A SAP HANA 2,0 tesztelési rendszeren a **00** -as példányszám miatt a névkiszolgálók portszáma **30001**. A HSR metaadat-kommunikáció portszáma **40002**. Az egyik lehetőség a munkavégző csomópontba való bejelentkezés, majd a fő csomópont-szolgáltatások beadása. Ehhez a cikkhez a 2. helyen a 2. feldolgozói csomópontot próbáltuk csatlakozni a 2. helyen található fő csomóponthoz.

Keresse meg a Name Server portot:

<pre><code>
nc -vz 10.0.0.40 30001
nc -vz 10.0.1.40 30001
nc -vz 10.0.2.40 30001
</code></pre>

Annak bizonyításához, hogy a csomópontok közötti kommunikáció a **10.0.2.0/24** alhálózatot használja, az eredménynek a következő minta kimenethez hasonlóan kell kinéznie.
Csak a **10.0.2.0/24** alhálózaton keresztüli kapcsolatok sikeresek legyenek:

<pre><code>
nc: connect to 10.0.0.40 port 30001 (tcp) failed: Connection refused
nc: connect to 10.0.1.40 port 30001 (tcp) failed: Connection refused
Connection to 10.0.2.40 30001 port [tcp/pago-services1] succeeded!
</code></pre>

Most keresse meg a **40002**-es HSR-portot:

<pre><code>
nc -vz 10.0.0.40 40002
nc -vz 10.0.1.40 40002
nc -vz 10.0.2.40 40002
</code></pre>

Annak bizonyításához, hogy a HSR-kommunikáció a **10.0.1.0/24** alhálózatot használja, az eredménynek a következő minta kimenetéhez hasonlóan kell kinéznie.
Csak a **10.0.1.0/24** alhálózaton keresztüli kapcsolatok sikeresek legyenek:

<pre><code>
nc: connect to 10.0.0.40 port 40002 (tcp) failed: Connection refused
Connection to 10.0.1.40 40002 port [tcp/*] succeeded!
nc: connect to 10.0.2.40 port 40002 (tcp) failed: Connection refused
</code></pre>



## <a name="corosync"></a>Corosync


A **Corosync** konfigurációs fájljának helyesnek kell lennie a fürt minden csomópontján, beleértve a többségi készítő csomópontot is. Ha egy csomópont fürthöz való csatlakoztatása nem a várt módon működik, manuálisan hozza létre vagy másolja a **/etc/Corosync/Corosync.conf** az összes csomópontra, majd indítsa újra a szolgáltatást. 

Egy példa a **Corosync. conf** fájl tartalmára a tesztelési rendszerből.

Az első szakasz a **Totem**, a [fürt telepítése](./high-availability-guide-suse-pacemaker.md#cluster-installation)című részben leírtak szerint, 11. lépés. Figyelmen kívül hagyhatja a **mcastaddr** értékét. Csak tartsa meg a meglévő bejegyzést. A **jogkivonatra** és a **konszenzusra** vonatkozó bejegyzéseket a [Microsoft Azure SAP HANA dokumentációjának][sles-pacemaker-ha-guide]megfelelően kell beállítani.

<pre><code>
totem {
    version:    2
    secauth:    on
    crypto_hash:    sha1
    crypto_cipher:  aes256
    cluster_name:   hacluster
    clear_node_high_bit: yes

    token:      30000
    token_retransmits_before_loss_const: 10
    join:       60
    consensus:  36000
    max_messages:   20

    interface {
        ringnumber:     0
        bindnetaddr: 10.0.0.0
        mcastaddr:  239.170.19.232
        mcastport:  5405

        ttl:        1
    }
    transport:      udpu

}
</code></pre>

A második szakasz, a **naplózás**, nem módosult a megadott alapbeállításokban:

<pre><code>
logging {
    fileline:   off
    to_stderr:  no
    to_logfile:     no
    logfile:    /var/log/cluster/corosync.log
    to_syslog:  yes
    debug:      off
    timestamp:  on
    logger_subsys {
        subsys:     QUORUM
        debug:  off
    }
}
</code></pre>

A harmadik szakasz a **nodeList** mutatja. A fürt összes csomópontjának meg kell jelenítenie a **nodeId**:

<pre><code>
nodelist {
  node {
   ring0_addr:hso-hana-vm-s1-0
   nodeid: 1
   }
  node {
   ring0_addr:hso-hana-vm-s1-1
   nodeid: 2
   }
  node {
   ring0_addr:hso-hana-vm-s1-2
   nodeid: 3
   }
  node {
   ring0_addr:hso-hana-vm-s2-0
   nodeid: 4
   }
  node {
   ring0_addr:hso-hana-vm-s2-1
   nodeid: 5
   }
  node {
   ring0_addr:hso-hana-vm-s2-2
   nodeid: 6
   }
  node {
   ring0_addr:hso-hana-dm
   nodeid: 7
   }
}
</code></pre>

Az utolsó szakaszban a **kvórumot** fontos a **expected_votes** helyes értékének beállítása. A csomópontok számának kell lennie, beleértve a többségi készítő csomópontot. **Two_node** értékének pedig **0** értékűnek kell lennie. Ne távolítsa el teljesen a bejegyzést. Állítsa az értéket **0-ra**.

<pre><code>
quorum {
    # Enable and configure quorum subsystem (default: off)
    # see also corosync.conf.5 and votequorum.5
    provider: corosync_votequorum
    expected_votes: 7
    two_node: 0
}
</code></pre>


Indítsa újra a szolgáltatást a **systemctl**-on keresztül:

<pre><code>
systemctl restart corosync
</code></pre>




## <a name="sbd-device"></a>SBD-eszköz

A SBD-eszköz Azure-beli virtuális gépen történő beállítását a [SBD-kerítés](./high-availability-guide-suse-pacemaker.md#sbd-fencing)ismerteti.

Először is ellenőrizze a SBD-kiszolgáló virtuális gépen, hogy vannak-e ACL-bejegyzések a fürt minden csomópontja számára. Futtassa a következő parancsot a SBD-kiszolgáló virtuális gépen:


<pre><code>
targetcli ls
</code></pre>


A tesztelési rendszeren a parancs kimenete az alábbi példához hasonlóan néz ki. Az ACL-nevek, például a **IQN. 2006-04. hso-db-0. local: hso-db-0 értéket** kell megadni a virtuális gépek megfelelő kezdeményező neveként. Minden virtuális gépnek egy másikra van szüksége.

<pre><code>
 | | o- sbddbhso ................................................................... [/sbd/sbddbhso (50.0MiB) write-thru activated]
  | |   o- alua ................................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ....................................................................... [ALUA state: Active/optimized]
  | o- pscsi .................................................................................................. [Storage Objects: 0]
  | o- ramdisk ................................................................................................ [Storage Objects: 0]
  o- iscsi ............................................................................................................ [Targets: 1]
  | o- iqn.2006-04.dbhso.local:dbhso ..................................................................................... [TPGs: 1]
  |   o- tpg1 ............................................................................................... [no-gen-acls, no-auth]
  |     o- acls .......................................................................................................... [ACLs: 7]
  |     | o- iqn.2006-04.hso-db-0.local:hso-db-0 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-1.local:hso-db-1 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-2.local:hso-db-2 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-3.local:hso-db-3 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-4.local:hso-db-4 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-5.local:hso-db-5 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-6.local:hso-db-6 .................................................................. [Mapped LUNs: 1]
</code></pre>

Ezután győződjön meg arról, hogy a kezdeményezők nevei az összes virtuális gépen eltérnek, és megfelelnek a korábban megjelenített bejegyzéseknek. Ez a példa az 1. hely munkavégző csomópontjának 1:

<pre><code>
cat /etc/iscsi/initiatorname.iscsi
</code></pre>

A kimenet a következő példához hasonlóan néz ki:

<pre><code>
##
## /etc/iscsi/iscsi.initiatorname
##
## Default iSCSI Initiatorname.
##
## DO NOT EDIT OR REMOVE THIS FILE!
## If you remove this file, the iSCSI daemon will not start.
## If you change the InitiatorName, existing access control lists
## may reject this initiator.  The InitiatorName must be unique
## for each iSCSI initiator.  Do NOT duplicate iSCSI InitiatorNames.
InitiatorName=iqn.2006-04.hso-db-1.local:hso-db-1
</code></pre>

Ezután ellenőrizze, hogy a **felderítés** megfelelően működik-e. Futtassa a következő parancsot minden fürtcsomóponton a SBD-kiszolgáló virtuális gép IP-címének használatával:

<pre><code>
iscsiadm -m discovery --type=st --portal=10.0.0.19:3260
</code></pre>

A kimenetnek az alábbi példához hasonlóan kell kinéznie:

<pre><code>
10.0.0.19:3260,1 iqn.2006-04.dbhso.local:dbhso
</code></pre>

A következő bizonyítási pont annak ellenőrzése, hogy a csomópont látja-e az SDB-eszközt. Győződjön meg róla, hogy minden csomóponton, beleértve a többségi készítő csomópontot:

<pre><code>
lsscsi | grep dbhso
</code></pre>

A kimenetnek az alábbi példához hasonlóan kell kinéznie. A nevek azonban eltérőek lehetnek. A virtuális gép újraindítása után az eszköz neve is változhat:

<pre><code>
[6:0:0:0]    disk    LIO-ORG  sbddbhso         4.0   /dev/sdm
</code></pre>

A rendszer állapotától függően időnként az iSCSI-szolgáltatások újraindítását is lehetővé teszi a problémák megoldásához. Ezután futtassa le a következő parancsokat:

<pre><code>
systemctl restart iscsi
systemctl restart iscsid
</code></pre>


Bármely csomópontból megtekintheti, hogy az összes csomópont **törölve** van-e. Győződjön meg arról, hogy az eszköz helyes nevét használja egy adott csomóponton:

<pre><code>
sbd -d /dev/sdm list
</code></pre>

A kimenetnek **egyértelműnek** kell lennie a fürt minden csomópontján:

<pre><code>
0       hso-hana-vm-s1-0        clear
1       hso-hana-vm-s2-2        clear
2       hso-hana-vm-s2-1        clear
3       hso-hana-dm     clear
4       hso-hana-vm-s1-1        clear
5       hso-hana-vm-s2-0        clear
6       hso-hana-vm-s1-2        clear
</code></pre>


Egy másik SBD-ellenőrzési lehetőség a **SBD** parancs **dump** kapcsolója. Ebben a minta-és kimenetben a többségi készítő csomópontból az eszköz neve **SDD** volt, nem **SDM**:

<pre><code>
sbd -d /dev/sdd dump
</code></pre>

Az eszköz neve melletti kimenetnek az összes csomóponton azonosnak kell lennie:

<pre><code>
==Dumping header on disk /dev/sdd
Header version     : 2.1
UUID               : 9fa6cc49-c294-4f1e-9527-c973f4d5a5b0
Number of slots    : 255
Sector size        : 512
Timeout (watchdog) : 60
Timeout (allocate) : 2
Timeout (loop)     : 1
Timeout (msgwait)  : 120
==Header on disk /dev/sdd is dumped
</code></pre>

A SBD egy további keresése arra a lehetőségre van, hogy üzenetet küldjön egy másik csomópontnak. Ha üzenetet szeretne küldeni a 2. munkavégző csomópontnak 2. helyen, futtassa a következő parancsot a (z) 2. helyen található 1. munkavégző csomóponton:

<pre><code>
sbd -d /dev/sdm message hso-hana-vm-s2-2 test
</code></pre>

A cél virtuális gép oldalán **hso-Hana-VM-S2-2** ebben a példában a következő bejegyzést találja a **/var/log/messages**:

<pre><code>
/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68:   notice: servant: Received command test from hso-hana-vm-s2-1 on disk /dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68
</code></pre>

Győződjön meg arról, hogy a **/etc/sysconfig/SBD** bejegyzései megfelelnek a [pacemaker beállítása SUSE Linux Enterprise Server az Azure-ban című rész](./high-availability-guide-suse-pacemaker.md#sbd-fencing)leírásában. Ellenőrizze, hogy a **/etc/iSCSI/iscsid.conf** indítási beállítása automatikus értékre van-e állítva.

A **/etc/sysconfig/SBD**-ben a következő bejegyzések fontosak. Szükség esetén módosítsa az **azonosító** értékét:

<pre><code>
SBD_DEVICE="/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68;"
SBD_PACEMAKER=yes
SBD_STARTMODE=always
SBD_WATCHDOG=yes
</code></pre>


Keresse meg az indítási beállítást a **/etc/iSCSI/iscsid.conf**. A kötelező beállításnak a dokumentációban leírt következő **iscsiadm** -paranccsal kell történnie. Ellenőrizze és módosítsa manuálisan a **VI** -vel, ha ez eltér.

Ez a parancs az indítási viselkedést állítja be:

<pre><code>
iscsiadm -m node --op=update --name=node.startup --value=automatic
</code></pre>

Tegye ezt a bejegyzést a **/etc/iSCSI/iscsid.conf**:

<pre><code>
node.startup = automatic
</code></pre>

A tesztelés és az ellenőrzés során egy virtuális gép újraindítása után a SBD-eszköz bizonyos esetekben már nem látható. Eltérés történt az indítási beállítás és a YaST2 között. A beállítások megadásához hajtsa végre a következő lépéseket:

1. Indítsa el a YaST2.
2. Válassza ki a **Network Services** a bal oldalon.
3. Görgessen le a jobb oldalon az **iSCSI-kezdeményezőre** , és jelölje ki.
4. A **szolgáltatás** lap következő képernyőjén megjelenik a csomópont egyedi kezdeményező neve.
5. A kezdeményező neve fölött ellenőrizze, hogy a **szolgáltatás indítási** értéke a **rendszerindítás során** van-e beállítva.
6. Ha nem, akkor állítsa be, hogy a **manuális** **Indítás** helyett
7. Ezután váltson a felső lapra a **csatlakoztatott célokhoz**.
8. A **csatlakoztatott célok** képernyőn megjelenik egy bejegyzés a SBD-eszközhöz, például a következő mintának: **10.0.0.19:3260 IQN. 2006-04. dbhso. local: dbhso**.
9. Ellenőrizze, hogy az **indítási** érték be van-e állítva **a rendszerindításra**.
10. Ha nem, válassza a **Szerkesztés** lehetőséget, és módosítsa azt.
11. Mentse a módosításokat, és lépjen ki a YaST2.



## <a name="pacemaker"></a>Pacemaker

Miután minden megfelelően beállította a beállításokat, a következő parancs futtatásával tekintheti meg az összes csomópontot a pacemaker szolgáltatás állapotának ellenőrzéséhez:

<pre><code>
systemctl status pacemaker
</code></pre>

A kimenet tetején a következő mintához hasonlóan kell kinéznie. Fontos, hogy az **aktív** állapot után **betöltött** és **aktív (futó)** állapot jelenjen meg. A **Betöltés** utáni állapotot **engedélyezve** értékként kell megjeleníteni.

<pre><code>
  pacemaker.service - Pacemaker High Availability Cluster Manager
   Loaded: loaded (/usr/lib/systemd/system/pacemaker.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2018-09-07 05:56:27 UTC; 4 days ago
     Docs: man:pacemakerd
           http://clusterlabs.org/doc/en-US/Pacemaker/1.1-pcs/html/Pacemaker_Explained/index.html
 Main PID: 4496 (pacemakerd)
    Tasks: 7 (limit: 4915)
   CGroup: /system.slice/pacemaker.service
           ├─4496 /usr/sbin/pacemakerd -f
           ├─4499 /usr/lib/pacemaker/cib
           ├─4500 /usr/lib/pacemaker/stonithd
           ├─4501 /usr/lib/pacemaker/lrmd
           ├─4502 /usr/lib/pacemaker/attrd
           ├─4503 /usr/lib/pacemaker/pengine
           └─4504 /usr/lib/pacemaker/crmd
</code></pre>

Ha a beállítás továbbra is **le van tiltva**, futtassa a következő parancsot:

<pre><code>
systemctl enable pacemaker
</code></pre>

A pacemaker összes konfigurált erőforrásának megtekintéséhez futtassa a következő parancsot:

<pre><code>
crm status
</code></pre>

A kimenetnek az alábbi példához hasonlóan kell kinéznie. Nagyon fontos, hogy a **CLN** és az **MSL** -erőforrások a többségi gyártó virtuális gépen, a **hso-Hana-DM**-ben leállítottként jelenjenek meg. Nincs SAP HANA telepítés a többségi gyártó csomópontján. Így a **CLN** és az **MSL** -erőforrások leálltként jelennek meg. Fontos, hogy a virtuális gépek megfelelő teljes számát jelenítse meg, **7**. A fürt részét képező összes virtuális gépnek az **online** állapottal kell szerepelnie. Az aktuális elsődleges főcsomópontot helyesen kell azonosítani. Ebben a példában ez a **hso-Hana-VM-S1-0**:

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Tue Sep 11 15:56:40 2018
Last change: Tue Sep 11 15:56:23 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0
</code></pre>

A pacemaker egyik fontos funkciója a karbantartási mód. Ebben a módban módosításokat hajthat végre a fürt azonnali futtatása nélkül. Ilyen például egy virtuális gép újraindítása. Tipikus használati eset a tervezett operációs rendszer vagy az Azure-infrastruktúra karbantartása. Lásd: [tervezett karbantartás](#planned-maintenance). A következő parancs használatával állítsa be a pacemakert karbantartási módba:

<pre><code>
crm configure property maintenance-mode=true
</code></pre>

A **CRM állapotának** ellenőrzésekor a kimenetben láthatja, hogy az összes erőforrás nem **kezeltként** van megjelölve. Ebben az állapotban a fürt nem reagál olyan változásokra, mint a SAP HANA elindítása vagy leállítása.
A következő minta a **CRM status** parancs kimenetét mutatja be, miközben a fürt karbantartási módban van:

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Wed Sep 12 07:48:10 2018
Last change: Wed Sep 12 07:46:54 2018 by root via cibadmin on hso-hana-vm-s2-1

7 nodes configured
17 resources configured

              *** Resource management is DISABLED ***
  The cluster will not attempt to start, stop or recover services

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm (unmanaged)
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00] (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-0 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00] (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Master hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm hso-hana-vm-s1-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s2-0 (unmanaged)
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s2-0 (unmanaged)
</code></pre>


Ez a parancs a fürt karbantartási módjának befejezését mutatja be:

<pre><code>
crm configure property maintenance-mode=false
</code></pre>


Egy másik **CRM** -parancs beolvassa a fürt teljes konfigurációját egy Szerkesztőbe, így szerkesztheti azt. A módosítások mentése után a fürt elindítja a megfelelő műveleteket:

<pre><code>
crm configure edit
</code></pre>

A fürt teljes konfigurációjának megkereséséhez használja a **CRM show** lehetőséget:

<pre><code>
crm configure show
</code></pre>



A fürt erőforrásainak meghibásodása után a **CRM status** parancs a **sikertelen műveletek** listáját jeleníti meg. Tekintse meg a kimenet következő mintáját:


<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Thu Sep 13 07:30:44 2018
Last change: Thu Sep 13 07:30:20 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm hso-hana-vm-s2-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0

Failed Actions:
* rsc_SAPHanaCon_HSO_HDB00_monitor_60000 on hso-hana-vm-s2-0 'unknown error' (1): call=86, status=complete, exitreason='none',
    last-rc-change='Wed Sep 12 17:01:28 2018', queued=0ms, exec=277663ms
</code></pre>

A meghibásodások után a fürt tisztítása szükséges. Használja újra a **CRM** -parancsot, és használja a parancs **törlését** , hogy megszabaduljunk a sikertelen műveleti bejegyzéseket. A megfelelő fürterőforrás nevét a következőképpen adja meg:

<pre><code>
crm resource cleanup rsc_SAPHanaCon_HSO_HDB00
</code></pre>

A parancsnak a következő mintához hasonló kimenetet kell visszaadnia:

<pre><code>
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-dm
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-2
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-2
Waiting for 7 replies from the CRMd....... OK
</code></pre>



## <a name="failover-or-takeover"></a>Feladatátvétel vagy átvétel

A [Fontos megjegyzések](#important-notes)szerint a fürt feladatátvételének teszteléséhez vagy SAP HANA HSR átvételéhez nem ajánlott szabványos kecses leállítást alkalmazni. Ehelyett azt javasoljuk, hogy indítson el egy kernel-pánikot, kényszerítse az erőforrások áttelepítését, vagy esetleg állítsa le az összes hálózatot a virtuális gép operációsrendszer-szintjén. Egy másik módszer a **CRM \<node\> készenléti** parancsa. Lásd a [SUSE-dokumentumot][sles-12-ha-paper]. 

A következő három minta parancs kényszerítheti a fürt feladatátvételét:

<pre><code>
echo c &gt /proc/sysrq-trigger

crm resource migrate msl_SAPHanaCon_HSO_HDB00 hso-hana-vm-s2-0 force

wicked ifdown eth0
wicked ifdown eth1
wicked ifdown eth2
......
wicked ifdown eth&ltn&gt
</code></pre>

A [tervezett karbantartás](#planned-maintenance)során a fürt tevékenységeinek figyelésére jó módszer a **SAPHanaSR-showAttr** futtatása a **Watch** paranccsal:

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

Emellett segít megtekinteni az SAP Python-szkriptből érkező SAP HANA tájképi állapotot. A fürt beállítása az állapot értékét keresi. Egyértelművé válik, ha a feldolgozó csomópont hibáját gondolja. Ha egy feldolgozó csomópont leáll, SAP HANA nem ad vissza azonnal hibát a teljes kibővíthető rendszer állapotára vonatkozóan. 

A szükségtelen feladatátvételek elkerülése érdekében újrapróbálkozik. A fürt csak akkor működik, ha az állapot az **OK**, a **4**. visszatérési érték, a **hiba**, az **1**. visszaadott érték változik. Ezért helyes, ha a **SAPHanaSR-showAttr** kimenete **Offline** állapotú virtuális gépet mutat be. Azonban még nincs tevékenység az elsődleges és a másodlagos váltáshoz. Nem indul el a fürt tevékenysége, amíg SAP HANA nem ad vissza hibát.

Az SAP Python-szkriptet az alábbi módon figyelheti a SAP HANA fekvő állapotának felhasználói **\<HANA SID\> adm** -ként való meghívásával. Lehetséges, hogy az elérési utat módosítania kell:

<pre><code>
watch python /hana/shared/HSO/exe/linuxx86_64/HDB_2.00.032.00.1533114046_eeaf4723ec52ed3935ae0dc9769c9411ed73fec5/python_support/landscapeHostConfiguration.py
</code></pre>

A parancs kimenetének a következő mintához hasonlóan kell kinéznie. A **gazdagép állapota** oszlop és a **gazdagép teljes állapota** egyaránt fontos. A tényleges kimenet szélesebb, további oszlopokkal.
Ahhoz, hogy a kimeneti tábla olvashatóbb legyen a dokumentumban, a jobb oldalon található legtöbb oszlop el lett megfosztotva:

<pre><code>
| Host             | Host   | Host   | Failover | Remove | 
|                  | Active | Status | Status   | Status | 
|                  |        |        |          |        | 
| ---------------- | ------ | ------ | -------- | ------ |    .......
| hso-hana-vm-s2-0 | yes    | ok     |          |        |        
| hso-hana-vm-s2-1 | yes    | ok     |          |        |         
| hso-hana-vm-s2-2 | yes    | ok     |          |        |        

overall host status: ok
</code></pre>


Van egy másik parancs a fürt aktuális tevékenységeinek vizsgálatához. Az elsődleges hely fő csomópontjának leölése után tekintse meg a következő parancsot és a kimenetet. Az új elsődleges főkiszolgálóként megtekintheti az áttérési műveletek listáját, például a korábbi másodlagos főcsomópont, a **hso-Hana-VM-S2-0** **előléptetését** . Ha minden tevékenység rendben van, és minden tevékenység elkészült, akkor az **átváltási összesítő** lista üresnek kell lennie.

<pre><code>
 crm_simulate -Ls

...........

Transition Summary:
 * Fence hso-hana-vm-s1-0
 * Stop    rsc_SAPHanaTop_HSO_HDB00:1   (hso-hana-vm-s1-0)
 * Demote  rsc_SAPHanaCon_HSO_HDB00:1   (Master -> Stopped hso-hana-vm-s1-0)
 * Promote rsc_SAPHanaCon_HSO_HDB00:5   (Slave -> Master hso-hana-vm-s2-0)
 * Move    rsc_ip_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
 * Move    rsc_nc_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
</code></pre>



## <a name="planned-maintenance"></a>Tervezett karbantartás 

A tervezett karbantartáshoz különböző használati esetek tartoznak. Az egyik kérdés, hogy csak az infrastruktúra-karbantartás, például az operációs rendszer szintjén történt változások, a lemez konfigurációja vagy a HANA-frissítés.
További információkat a SUSE-ben található dokumentumokban találhat, például a [nulla leállás][sles-zero-downtime-paper] vagy a [SAP HANA SR teljesítményének optimalizált forgatókönyve][sles-12-for-sap]irányába. Ezek a dokumentumok olyan mintákat is tartalmaznak, amelyek bemutatják, hogyan telepíthet át manuálisan egy elsődlegeset.

Intenzív belső tesztelés történt az infrastruktúra-karbantartási használati eset ellenőrzéséhez. Ha el szeretné kerülni az elsődleges áttelepítéssel kapcsolatos problémákat, úgy döntöttünk, hogy mindig áttelepíti az elsődlegest, mielőtt a fürtöt karbantartási módba helyezné. Így nem szükséges felejtsd a fürtöt arra, hogy a korábbi helyzetet felejtsen: az elsődleges és másodlagos volt.

Ebben a tekintetben két különböző helyzet áll fenn:

- **Tervezett karbantartás az aktuális másodlagos oldalon**. Ebben az esetben csak karbantartási módba helyezheti a fürtöt, és a másodlagos munkát anélkül végezheti el, hogy befolyásolná a fürtöt.

- **Tervezett karbantartás az aktuális elsődlegesen**. Annak érdekében, hogy a felhasználók továbbra is működőképesek legyenek a karbantartás során, kényszeríteni kell a feladatátvételt. Ezzel a módszerrel a fürt feladatátvételét a pacemakerrel kell elindítani, nem csak a SAP HANA HSR szinten. A pacemaker telepítője automatikusan elindítja a SAP HANA átvételt. A feladatátvételt még azelőtt kell végrehajtania, hogy a fürtöt karbantartási módba helyezné.

Az aktuális másodlagos hely karbantartásának eljárása a következő:

1. Helyezze a fürtöt karbantartási módba.
2. A munka elvégzése a másodlagos helyen. 
3. Fejezze be a fürt karbantartási módját.

Az aktuális elsődleges helyen lévő karbantartási eljárás összetettebb:

1. Feladatátvétel vagy SAP HANA átvételének manuális elindítása pacemaker erőforrás-áttelepítés használatával. Tekintse meg az alábbi részleteket.
2. A korábbi elsődleges helyen lévő SAP HANA a fürt beállítása leállítja.
3. Helyezze a fürtöt karbantartási módba.
4. A karbantartási munkálatok befejezése után regisztrálja a korábbi elsődlegest az új másodlagos helyként.
5. Ürítse ki a fürtkonfiguráció konfigurációját. Tekintse meg az alábbi részleteket.
6. Fejezze be a fürt karbantartási módját.


Egy erőforrás áttelepítése egy bejegyzést hoz létre a fürt konfigurációjához. Ilyen például a feladatátvétel kényszerítése. Ezeket a bejegyzéseket a karbantartási mód befejezése előtt kell megtisztítania. Tekintse meg a következő mintát.

Először kényszerítse ki a fürt feladatátvételét úgy, hogy áttelepíti az **MSL** -erőforrást az aktuális másodlagos főcsomópontra. Ez a parancs figyelmeztetést ad arra vonatkozóan, hogy az **áthelyezési megkötés** létrejött:

<pre><code>
crm resource migrate msl_SAPHanaCon_HSO_HDB00 force

INFO: Move constraint created for msl_SAPHanaCon_HSO_HDB00
</code></pre>


A feladatátvételi folyamatot a **SAPHanaSR-showAttr** parancs használatával vizsgálja meg. A fürt állapotának figyeléséhez nyisson meg egy dedikált rendszerhéj-ablakot, és indítsa el a parancsot az **óra** használatával:

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

A kimenetnek a manuális feladatátvételt kell mutatnia. A korábbi másodlagos főcsomópont **előléptetve** lett, ebben a példában a **hso-Hana-VM-S2-0**. A korábbi elsődleges hely le lett állítva, **1** . **licencszolgáltatóval** érték a korábbi elsődleges főcsomóponthoz **hso-Hana-VM-S1-0**: 

<pre><code>
Global cib-time                 prim  sec srHook sync_state
------------------------------------------------------------
global Wed Sep 12 07:40:02 2018 HSOS2 -   SFAIL  SFAIL


Sites lpt        lss mns              srr
------------------------------------------
HSOS1 10         1   hso-hana-vm-s1-0 P
HSOS2 1536738002 4   hso-hana-vm-s2-0 P


Hosts            clone_state node_state roles                        score  site
----------------------------------------------------------------------------------
hso-hana-dm                  online
hso-hana-vm-s1-0 UNDEFINED   online     master1::worker:             150    HSOS1
hso-hana-vm-s1-1 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s1-2 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s2-0 PROMOTED    online     master1:master:worker:master 150    HSOS2
hso-hana-vm-s2-1 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
hso-hana-vm-s2-2 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
</code></pre>

A fürt feladatátvétele és SAP HANA átvétele után helyezze a fürtöt karbantartási módba a [pacemakerben](#pacemaker)leírtak szerint.

A **SAPHanaSR-showAttr** és a **CRM** -alapú parancsok nem utalnak az erőforrás-áttelepítéssel létrehozott korlátozásokra. Az egyik lehetőség, hogy láthatóvá tegye ezeket a korlátozásokat, a fürt teljes erőforrás-konfigurációjának megjelenítése a következő paranccsal:

<pre><code>
crm configure show
</code></pre>

A fürt konfigurációjában a korábbi manuális erőforrás-áttelepítés által okozott új hely megkötése található. Ez a példában szereplő bejegyzés a **CLI-** vel kezdődik:

<pre><code>
location cli-ban-msl_SAPHanaCon_HSO_HDB00-on-hso-hana-vm-s1-0 msl_SAPHanaCon_HSO_HDB00 role=Started -inf: hso-hana-vm-s1-0
</code></pre>

Sajnos az ilyen megkötések befolyásolhatják a fürt általános viselkedését. Ezért a teljes rendszeren történő biztonsági mentés előtt el kell távolítani őket. A **remigrálás** paranccsal kiürítheti a korábban létrehozott hely megkötéseit. Előfordulhat, hogy az elnevezés kissé zavaros. Nem próbálja meg újra áttelepíteni az erőforrást arra az eredeti virtuális gépre, amelyről az áttelepítette. Csak eltávolítja a hely megkötéseit, és a parancs futtatásakor a megfelelő információkat is visszaadja:


<pre><code>
crm resource unmigrate msl_SAPHanaCon_HSO_HDB00

INFO: Removed migration constraints for msl_SAPHanaCon_HSO_HDB00
</code></pre>

A karbantartási munkálatok végén le kell állítania a fürt karbantartási módját a [pacemakerben](#pacemaker)látható módon.



## <a name="hb_report-to-collect-log-files"></a>hb_report a naplófájlok gyűjtéséhez

A pacemaker-fürtökkel kapcsolatos problémák elemzéséhez hasznos és a SUSE-támogatás azt is kéri, hogy futtassa a **hb_report** segédprogramot. A rendszer összegyűjti az összes fontos naplófájlt, melyeket elemezni kell, hogy mi történt. Ez a példa egy kezdési és befejezési időpontot használ, ahol egy adott incidens történt. A [Fontos megjegyzések](#important-notes)is megjelennek:

<pre><code>
hb_report -f "2018/09/13 07:36" -t "2018/09/13 08:00" /tmp/hb_report_log
</code></pre>

A parancs megadja, hová helyezi a tömörített naplófájlokat:

<pre><code>
The report is saved in /tmp/hb_report_log.tar.bz2
Report timespan: 09/13/18 07:36:00 - 09/13/18 08:00:00
</code></pre>

Ezután kibonthatja az egyes fájlokat a szabványos **tar** parancs használatával:

<pre><code>
tar -xvf hb_report_log.tar.bz2
</code></pre>

Ha megtekinti a kibontott fájlokat, megkeresi az összes naplófájlt. A legtöbb esetben a fürt összes csomópontja külön címtárakba került:

<pre><code>
-rw-r--r-- 1 root root  13655 Sep 13 09:01 analysis.txt
-rw-r--r-- 1 root root  14422 Sep 13 09:01 description.txt
-rw-r--r-- 1 root root      0 Sep 13 09:01 events.txt
-rw-r--r-- 1 root root 275560 Sep 13 09:00 ha-log.txt
-rw-r--r-- 1 root root     26 Sep 13 09:00 ha-log.txt.info
drwxr-xr-x 4 root root   4096 Sep 13 09:01 hso-hana-dm
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-2
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-2
-rw-r--r-- 1 root root 264726 Sep 13 09:00 journal.log
</code></pre>


A megadott időtartományon belül az aktuális fő csomópont **hso-Hana-VM-S1-0** lett leállítva. Az eseményhez kapcsolódó bejegyzéseket a **Journal. log naplófájlban** találja:

<pre><code>
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: (to hsoadm) root on none
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session opened for user hsoadm by (uid=0)
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 systemd[1]: Started Session c44290 of user hsoadm.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] A new membership (10.0.0.13:120996) was formed. Members left: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] Failed to receive the leave message. failed: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Removing all hso-hana-vm-s1-0 attributes for peer loss
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [QUORUM] Members[6]: 7 2 3 4 5 6
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [MAIN  ] Completed service synchronization, ready to provide service.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 crmd[28315]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 pacemakerd[28308]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:03+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session closed for user hsoadm
</code></pre>

Egy másik példa a pacemaker naplófájl a másodlagos főkiszolgálón, amely az új elsődleges főkiszolgáló lett. Ez a részlet azt mutatja, hogy a megölt elsődleges főcsomópont állapota **Offline** értékre lett állítva:

<pre><code>
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 3 still member of group stonith-ng (peer=hso-hana-vm-s1-2, counter=5.1)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 4 still member of group stonith-ng (peer=hso-hana-vm-s2-0, counter=5.2)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 5 still member of group stonith-ng (peer=hso-hana-vm-s2-1, counter=5.3)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 6 still member of group stonith-ng (peer=hso-hana-vm-s2-2, counter=5.4)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 7 still member of group stonith-ng (peer=hso-hana-dm, counter=5.5)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 1 left group crmd (peer=hso-hana-vm-s1-0, counter=5.0)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: crm_update_peer_proc:     pcmk_cpg_membership: Node hso-hana-vm-s1-0[1] - corosync-cpg is now offline
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: peer_update_callback:     Client hso-hana-vm-s1-0/peer now has status [offline] (DC=hso-hana-dm, changed=4000000)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 2 still member of group crmd (peer=hso-hana-vm-s1-1, counter=5.0)
</code></pre>





## <a name="sap-hana-globalini"></a>SAP HANA global.ini


A következő részletek a SAP HANA **global.ini** fájlból származnak a (z) 2. fürtben. Ez a példa az állomásnév-feloldási bejegyzéseket mutatja be különböző hálózatok használatára SAP HANA csomópontok közötti kommunikációhoz és HSR:

<pre><code>
[communication]
tcp_keepalive_interval = 20
internal_network = 10.0.2/24
listeninterface = .internal
</code></pre>


<pre><code>
[internal_hostname_resolution]
10.0.2.40 = hso-hana-vm-s2-0
10.0.2.42 = hso-hana-vm-s2-2
10.0.2.41 = hso-hana-vm-s2-1
</code></pre>

<pre><code>
[ha_dr_provider_SAPHanaSR]
provider = SAPHanaSR
path = /hana/shared/myHooks
execution_order = 1
</code></pre>

<pre><code>
[system_replication_communication]
listeninterface = .internal

[system_replication_hostname_resolution]
10.0.1.30 = hso-hana-vm-s1-0
10.0.1.31 = hso-hana-vm-s1-1
10.0.1.32 = hso-hana-vm-s1-2
10.0.1.40 = hso-hana-vm-s2-0
10.0.1.41 = hso-hana-vm-s2-1
10.0.1.42 = hso-hana-vm-s2-2
</code></pre>



## <a name="hawk"></a>Hawk

A cluster megoldás egy böngésző felületét biztosítja, amely egy grafikus felhasználói felületet biztosít azon felhasználók számára, akik a menüket és a grafikát kedvelik a rendszerhéj szintjén lévő összes parancshoz.
A böngésző felületének használatához cserélje le a-t **\<node\>** egy tényleges SAP HANA csomópontra a következő URL-címben. Ezután adja meg a fürt hitelesítő adatait (felhasználói **fürt**):

<pre><code>
https://&ltnode&gt:7630
</code></pre>

Ez a képernyőkép a fürt irányítópultját mutatja:


![Hawk-fürt irányítópultja](media/hana-vm-scale-out-HA-troubleshooting/hawk-1.png)


Ez a példa a fürterőforrás-áttelepítés által a [tervezett karbantartás](#planned-maintenance)során okozott korlátozásokat szemlélteti:


![A Hawk List korlátozásai](media/hana-vm-scale-out-HA-troubleshooting/hawk-2.png)


A Hawk ( **hb_report** ) kimenetét is feltöltheti az **Előzmények** alatt, a következőképpen jelenik meg. A naplófájlok gyűjtéséhez hb_report tekintse meg a következőt: 

![Hawk feltöltési hb_report kimenete](media/hana-vm-scale-out-HA-troubleshooting/hawk-3.png)

Az **Előzmények Explorerrel** a **hb_report** kimenetben található összes fürt átváltását áttekintheti:

![A Hawk-átmenetek a hb_report kimenetében](media/hana-vm-scale-out-HA-troubleshooting/hawk-4.png)

Ez a végső képernyőkép egy átmeneti átmenet **részleteit** mutatja be. A fürt elsődleges főcsomópont-összeomlásra reagált, node **hso-Hana-VM-S1-0**. Mostantól a másodlagos csomópontot új főkiszolgálóként, **hso-Hana-VM-S2-0-** ként népszerűsíti:

![Hawk Single Transition](media/hana-vm-scale-out-HA-troubleshooting/hawk-5.png)


## <a name="next-steps"></a>Következő lépések

Ez a hibaelhárítási útmutató a SAP HANA magas rendelkezésre állását ismerteti a kibővíthető konfigurációban. Az adatbázison kívül az SAP-környezet egy másik fontos összetevője az SAP NetWeaver stack. Ismerje meg az [SAP NetWeaver magas rendelkezésre állását a SUSE Enterprise Linux Servert használó Azure-beli virtuális gépeken][sap-nw-ha-guide-sles].

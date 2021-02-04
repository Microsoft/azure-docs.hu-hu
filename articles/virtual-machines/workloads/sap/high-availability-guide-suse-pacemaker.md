---
title: A pacemaker beállítása a SLES az Azure-ban | Microsoft Docs
description: A pacemaker beállítása SUSE Linux Enterprise Server az Azure-ban
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: radeltch
ms.openlocfilehash: ad0f0e9bdc4398af150874d398968d1116578350
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550691"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>A pacemaker beállítása SUSE Linux Enterprise Server az Azure-ban

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[virtual-machines-windows-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[sles-nfs-guide]:high-availability-guide-suse-nfs.md
[sles-guide]:high-availability-guide-suse.md

Az Azure-ban két lehetőség van egy pacemaker-fürt beállítására. Használhat egy kerítési ügynököt is, amely gondoskodik a meghibásodott csomópontok Azure API-kon keresztüli újraindításáról, vagy használhat egy SBD-eszközt.

A SBD-eszközhöz legalább egy további virtuális gép szükséges, amely iSCSI-célkiszolgálóként működik, és SBD-eszközt biztosít. Ezek az iSCSI-célkiszolgáló azonban más pacemaker-fürtökkel is megoszthatók. Ha már használja a helyszíni SBD-eszközöket, a SBD-eszköz használatának előnye, hogy a pacemaker-fürt működését nem kell módosítania. Akár három SBD eszközt is használhat a pacemaker-fürthöz, hogy egy SBD-eszköz elérhetetlenné váljon, például az iSCSI-célkiszolgáló operációs rendszerének javítása során. Ha a SBD egynél több eszközt szeretne használni, győződjön meg arról, hogy több iSCSI-célkiszolgáló üzembe helyezése és egy SBD csatlakoztatása minden iSCSI-célkiszolgálón. Javasoljuk, hogy használjon egy SBD-eszközt vagy háromat. A pacemaker nem fogja tudni automatikusan elkeríteni a fürtcsomópont-csomópontot, ha csak két SBD-eszközt konfigurál, és ezek egyike nem érhető el. Ha azt szeretné, hogy az egyik iSCSI-célkiszolgáló leálljon, három SBD-eszközt kell használnia, ezért három iSCSI-célkiszolgáló, amely a legrugalmasabb konfiguráció a SBDs használatakor.

Az Azure kerítés ügynökének nem szükséges további virtuális gép (ek) telepítése.   

![Pacemaker on SLES – áttekintés](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

>[!IMPORTANT]
> A Linux pacemaker fürtözött csomópontok és SBD tervezése és üzembe helyezése során elengedhetetlen a teljes fürtkonfiguráció teljes megbízhatósága, amely az érintett virtuális gépek és a SBD-eszközt üzemeltető virtuális gép (ek) közötti útválasztás nem halad át semmilyen más eszközön, például a [NVA](https://azure.microsoft.com/solutions/network-appliances/)-en keresztül. Ellenkező esetben a NVA kapcsolatos problémák és karbantartási események negatív hatással lehetnek a fürt általános konfigurációjának stabilitására és megbízhatóságára. Az ilyen akadályok elkerülése érdekében ne határozzon meg olyan NVA vagy [felhasználó által definiált útválasztási szabályok](../../../virtual-network/virtual-networks-udr-overview.md) útválasztási szabályait, amelyek a fürtözött csomópontok és a SBD eszközök közötti forgalmat irányítják a NVA és hasonló eszközökön a Linux pacemaker fürtözött csomópontok és SBD-eszközök tervezése és telepítése során. 
>

## <a name="sbd-fencing"></a>SBD-kerítés

Kövesse az alábbi lépéseket, ha SBD-eszközt kíván használni a kerítéshez.

### <a name="set-up-iscsi-target-servers"></a>ISCSI-tároló kiszolgálók beállítása

Először létre kell hoznia az iSCSI cél virtuális gépeket. az iSCSI-célkiszolgáló több pacemaker-fürttel is megoszthatók.

1. Helyezzen üzembe új SLES 12 SP1 vagy újabb virtuális gépet, és csatlakozzon hozzájuk SSH-n keresztül. A gépeknek nem kell nagy méretűeknek lenniük. A virtuális gép mérete (például Standard_E2s_v3 vagy Standard_D2s_v3) elegendő. Ügyeljen arra, hogy a Premium Storage-ot használja az operációsrendszer-lemezen.

Futtassa az alábbi parancsokat az összes **iSCSI cél virtuális gépen**.

1. SLES frissítése

   <pre><code>sudo zypper update
   </code></pre>

   > [!NOTE]
   > Előfordulhat, hogy az operációs rendszer frissítése vagy frissítése után újra kell indítania az operációs rendszert. 

1. Csomagok eltávolítása

   Ha el szeretné kerülni a targetcli és a SLES 12 SP3 ismert problémáját, távolítsa el a következő csomagokat. Figyelmen kívül hagyhatja a nem található csomagokkal kapcsolatos hibákat.

   <pre><code>sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>

1. ISCSI-tárolók telepítése

   <pre><code>sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. Az iSCSI-tároló szolgáltatás engedélyezése

   <pre><code>sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>ISCSI-eszköz létrehozása az iSCSI-célkiszolgálón

Futtassa az alábbi parancsokat az összes **iSCSI-tároló virtuális gépen** az SAP-rendszerek által használt fürtök iSCSI-lemezének létrehozásához. A következő példában a több fürthöz tartozó SBD-eszközök jönnek létre. Ez azt mutatja be, hogyan érdemes egyetlen iSCSI-célkiszolgáló használatát több fürthöz használni. A SBD-eszközök az operációsrendszer-lemezen vannak elhelyezve. Győződjön meg arról, hogy elegendő lemezterülettel rendelkezik.

**`nfs`** az NFS-fürt azonosítására szolgál, **a ascsnw1** a **NW1** ASCS-fürt azonosítására szolgál. a **dbnw1** a **NW1**, az **NFS-0** és az **NFS-1 adatbázis-** fürtjének azonosítására szolgál. az NFS-fürtcsomópontok állomásneve, a **NW1-xscs-0** és a **NW1-xscs-1** a **NW1** ASCS-fürtcsomópontok állomásneve, a NW1- **db-0** és a NW1- **db-** 1 pedig az adatbázis-fürt csomópontjainak állomásneve. Cserélje le őket a fürtcsomópontok állomásneve és az SAP-System SID azonosítóra.

<pre><code># Create the root folder for all SBD devices
sudo mkdir /sbd

# Create the SBD device for the NFS server
sudo targetcli backstores/fileio create sbdnfs /sbd/sbdnfs 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.nfs.local:nfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/luns/ create /backstores/fileio/sbdnfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-0.local:nfs-0</b>
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-1.local:nfs-1</b>

# Create the SBD device for the ASCS server of SAP System NW1
sudo targetcli backstores/fileio create sbdascs<b>nw1</b> /sbd/sbdascs<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbdascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-0.local:nw1-xscs-0</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-1.local:nw1-xscs-1</b>

# Create the SBD device for the database cluster of SAP System NW1
sudo targetcli backstores/fileio create sbddb<b>nw1</b> /sbd/sbddb<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbddb<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-0.local:nw1-db-0</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-1.local:nw1-db-1</b>

# save the targetcli changes
sudo targetcli saveconfig
</code></pre>

Megtekintheti, hogy minden beállítás megfelelően van-e beállítva

<pre><code>sudo targetcli ls

o- / .......................................................................................................... [...]
  o- backstores ............................................................................................... [...]
  | o- block ................................................................................... [Storage Objects: 0]
  | o- fileio .................................................................................. [Storage Objects: 3]
  | | o- <b>sbdascsnw1</b> ................................................ [/sbd/sbdascsnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbddbnw1</b> .................................................... [/sbd/sbddbnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbdnfs</b> ........................................................ [/sbd/sbdnfs (50.0MiB) write-thru activated]
  | |   o- alua .................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | o- pscsi ................................................................................... [Storage Objects: 0]
  | o- ramdisk ................................................................................. [Storage Objects: 0]
  o- iscsi ............................................................................................. [Targets: 3]
  | o- <b>iqn.2006-04.ascsnw1.local:ascsnw1</b> .................................................................. [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-xscs-0.local:nw1-xscs-0</b> ............................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-xscs-1.local:nw1-xscs-1</b> ............................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .......................................... [fileio/sbdascsnw1 (/sbd/sbdascsnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.dbnw1.local:dbnw1</b> ...................................................................... [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-db-0.local:nw1-db-0</b> ................................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-db-1.local:nw1-db-1</b> ................................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .............................................. [fileio/sbddbnw1 (/sbd/sbddbnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.nfs.local:nfs</b> .......................................................................... [TPGs: 1]
  |   o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  |     o- acls ........................................................................................... [ACLs: 2]
  |     | o- <b>iqn.2006-04.nfs-0.local:nfs-0</b> ......................................................... [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     | o- <b>iqn.2006-04.nfs-1.local:nfs-1</b> ......................................................... [Mapped LUNs: 1]
  |     |   o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     o- luns ........................................................................................... [LUNs: 1]
  |     | o- lun0 .................................................. [fileio/sbdnfs (/sbd/sbdnfs) (default_tg_pt_gp)]
  |     o- portals ..................................................................................... [Portals: 1]
  |       o- 0.0.0.0:3260 ...................................................................................... [OK]
  o- loopback .......................................................................................... [Targets: 0]
  o- vhost ............................................................................................. [Targets: 0]
  o- xen-pvscsi ........................................................................................ [Targets: 0]
</code></pre>

### <a name="set-up-sbd-device"></a>SBD-eszköz beállítása

Kapcsolódjon a fürt utolsó lépéseként létrehozott iSCSI-eszközhöz.
Futtassa a következő parancsokat a létrehozni kívánt új fürt csomópontjain.
A következő elemek a **[a]** előtaggal vannak ellátva, amelyek az összes csomópontra érvényesek, **[1]** – csak az 1. vagy **[2]** csomópontra érvényesek, csak a 2. csomópontra.

1. **[A]** kapcsolódás az iSCSI-eszközökhöz

   Először engedélyezze az iSCSI-és SBD-szolgáltatásokat.

   <pre><code>sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]** a kezdeményező nevének módosítása az első csomóponton

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Módosítsa a fájl tartalmát úgy, hogy azok megfeleljenek az iSCSI-eszköz iSCSI-célkiszolgálón, például az NFS-kiszolgálón való létrehozásakor használt ACL-ekkel.

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-0.local:nfs-0</b>
   </code></pre>

1. **[2]** a kezdeményező nevének módosítása a második csomóponton

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Módosítsa a fájl tartalmát úgy, hogy azok megfeleljenek az iSCSI-eszköz iSCSI-célkiszolgálón való létrehozásakor használt ACL-ekkel

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-1.local:nfs-1</b>
   </code></pre>

1. **[A]** az iSCSI szolgáltatás újraindítása

   Most indítsa újra az iSCSI szolgáltatást a módosítás alkalmazásához

   <pre><code>sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   Az iSCSI-eszközök csatlakoztatása. Az alábbi példában a 10.0.0.17 az iSCSI-célkiszolgáló IP-címe, a 3260 pedig az alapértelmezett port. <b>IQN. 2006-04. NFS. local: az NFS</b> az egyik cél neve, amely az alábbi első parancs futtatásakor jelenik meg (iscsiadm-m felderítés).

   <pre><code>sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> -T <b>iqn.2006-04.nfs.local:nfs</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the second iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.18:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.18:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.18:3260</b> -T <b>iqn.2006-04.nfs.local:nfs</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the third iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.19:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.19:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.19:3260</b> -T <b>iqn.2006-04.nfs.local:nfs</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   Győződjön meg arról, hogy az iSCSI-eszközök elérhetők, és jegyezze fel az eszköz nevét (a következő példában/dev/Sde)

   <pre><code>lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdd</b>
   # <b>[7:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sde</b>
   # <b>[8:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdf</b>
   </code></pre>

   Most kérje le az iSCSI-eszközök azonosítóit.

   <pre><code>ls -l /dev/disk/by-id/scsi-* | grep <b>sdd</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03 -> ../../sdd</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sdf</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf -> ../../sdf</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   </code></pre>

   A parancs három eszköz azonosítóját sorolja fel minden SBD-eszközhöz. Javasoljuk, hogy használja a SCSI-3 kezdetű azonosítót, a fenti példában látható módon:

   * **/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03**
   * **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**
   * **/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf**

1. **[1]** a SBD-eszköz létrehozása

   Az iSCSI-eszközök AZONOSÍTÓjának használatával hozza létre az új SBD-eszközöket az első fürtcsomóponton.

   <pre><code>sudo sbd -d <b>/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03</b> -1 60 -4 120 create

   # Also create the second and third SBD devices if you want to use more than one.
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 60 -4 120 create
   sudo sbd -d <b>/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf</b> -1 60 -4 120 create
   </code></pre>

1. **[A]** a SBD konfigurációjának átalakítása

   A SBD konfigurációs fájl megnyitása

   <pre><code>sudo vi /etc/sysconfig/sbd
   </code></pre>

   Módosítsa a SBD eszköz tulajdonságát, engedélyezze a pacemaker-integrációt, és módosítsa a SBD indítási módját.

   <pre><code>[...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   [...]
   </code></pre>

   A `softdog` konfigurációs fájl létrehozása

   <pre><code>echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   Most töltse be a modult

   <pre><code>sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Fürt telepítése

A következő elemek a **[a]** előtaggal vannak ellátva, amelyek az összes csomópontra érvényesek, **[1]** – csak az 1. vagy **[2]** csomópontra érvényesek, csak a 2. csomópontra.

1. **[A]** SLES frissítése

   <pre><code>sudo zypper update
   </code></pre>

1. **[A]** a fürt erőforrásaihoz szükséges telepítési összetevő

   <pre><code>sudo zypper in socat
   </code></pre>

1. **[A]** a fürt erőforrásaihoz szükséges Azure-LB összetevő telepítése

   <pre><code>sudo zypper in resource-agents
   </code></pre>

   > [!NOTE]
   > Ellenőrizze a csomag erőforrás-ügynökének verzióját, és győződjön meg arról, hogy teljesülnek a minimális verzióra vonatkozó követelmények:  
   > - A SLES 12 SP4/SP5 esetében a verziónak legalább Resource-Agent-4.3.018. a7fb5035-3.30.1 kell lennie.  
   > - A SLES 15/15 SP1 esetében a verziónak legalább Resource-Agent-4.3.0184.6 ee15eb2-4.13.1 kell lennie.  

1. **[A]** az operációs rendszer konfigurálása

   Bizonyos esetekben a pacemaker számos folyamatot hoz létre, így kimeríti az engedélyezett számú folyamatot. Ilyen esetben előfordulhat, hogy a fürtcsomópontok közötti szívverés meghiúsul, és az erőforrások feladatátvételét eredményezi. Javasoljuk, hogy a következő paraméter beállításával növelje a maximálisan engedélyezett folyamatokat.

   <pre><code># Edit the configuration file
   sudo vi /etc/systemd/system.conf
   
   # Change the DefaultTasksMax
   #DefaultTasksMax=512
   DefaultTasksMax=4096
   
   #and to activate this setting
   sudo systemctl daemon-reload
   
   # test if the change was successful
   sudo systemctl --no-pager show | grep DefaultTasksMax
   </code></pre>

   Csökkentse a piszkos gyorsítótár méretét. További információ: [alacsony írási teljesítmény a SLES 11/12-kiszolgálókon nagyméretű RAM-mal](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]** a Cloud-netconfig konfigurálása – Azure a ha-fürthöz

   >[!NOTE]
   > A Cloud **-netconfig-Azure** csomag telepített verziójának ellenőrzéséhez futtassa a **Zypper info Cloud-netconfig-Azure** alkalmazást. Ha a környezete 1,3-es vagy újabb verziójú, a hálózati adapterek a Cloud Network beépülő modullal való felügyeletét már nem szükséges letiltani. Ha a verzió 1,3-nál kisebb, javasoljuk, hogy frissítse a **Cloud-netconfig-Azure** csomagot a legújabb elérhető verzióra.  

   Módosítsa a hálózati adapter konfigurációs fájlját az alább látható módon, hogy megakadályozza, hogy a Cloud Network beépülő modul eltávolítsa a virtuális IP-címet (a pacemakernek meg kell határoznia a VIP-hozzárendelést). További információ: [SUSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633). 

   <pre><code># Edit the configuration file
   sudo vi /etc/sysconfig/network/ifcfg-eth0 
   
   # Change CLOUD_NETCONFIG_MANAGE
   # CLOUD_NETCONFIG_MANAGE="yes"
   CLOUD_NETCONFIG_MANAGE="no"
   </code></pre>

1. **[1]** SSH-hozzáférés engedélyezése

   <pre><code>sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[2]** SSH-hozzáférés engedélyezése

   <pre><code>
   sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys   
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[1]** SSH-hozzáférés engedélyezése

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]** a kerítés ügynökök csomagjának telepítése, ha STONITH-eszközt használ az Azure kerítés ügynöke alapján.  
   
   <pre><code>sudo zypper install fence-agents
   </code></pre>

   >[!IMPORTANT]
   > A csomagok **kerítésének** telepített verziójának legalább **4.4.0**  kell lennie ahhoz, hogy kihasználhassa az Azure kerítés ügynökének gyorsabb feladatátvételi idejét, ha a fürtcsomópontok bekerítése szükséges. Javasoljuk, hogy frissítse a csomagot, ha alacsonyabb verziójú verziót futtat.  


1. **[A]** az Azure Python SDK telepítése 
   - SLES 12 SP4 vagy SLES 12 SP5
   <pre><code>
    # You may need to activate the Public cloud extention first
    SUSEConnect -p sle-module-public-cloud/12/x86_64
    sudo zypper install python-azure-mgmt-compute
   </code></pre> 

   - 15-SLES és újabb verziók 
   <pre><code>
    # You may need to activate the Public cloud extention first. In this example the SUSEConnect command is for SLES 15 SP1
    SUSEConnect -p sle-module-public-cloud/15.1/x86_64
    sudo zypper install python3-azure-mgmt-compute
   </code></pre> 
 
   >[!IMPORTANT]
   >A verziótól és a képtípustól függően előfordulhat, hogy az Azure Python SDK telepítése előtt aktiválnia kell az operációsrendszer-kiadás nyilvános Felhőbeli bővítményét.
   >A bővítményt a SUSEConnect---listájának bővítményeinek futtatásával tekintheti meg.  
   >A gyorsabb feladatátvételi idő elérése az Azure kerítés ügynökével:
   > - a SLES 12 SP4 vagy a SLES 12 SP5 telepítse a Python-Azure-mgmt-számítás **4.6.2** vagy újabb verzióját  
   > - a SLES 15-én telepítse a Python **3** csomag **4.6.2** vagy újabb verzióját – Azure-mgmt-számítás 

1. **[A]** telepítési állomásnév feloldása

   Használhat DNS-kiszolgálót, vagy módosíthatja a/etc/hosts az összes csomóponton. Ez a példa a/etc/hosts fájl használatát mutatja be.
   Cserélje le az IP-címet és a gazdagépet a következő parancsokra.

   >[!IMPORTANT]
   > Ha a fürt konfigurációjában állomásnévket használ, elengedhetetlen, hogy megbízható legyen az állomásnév feloldása. A fürt kommunikációja sikertelen lesz, ha a nevek nem érhetők el, és ez a fürt feladatátvételi késéséhez vezethet.
   > A/etc/hosts használatának előnye, hogy a fürt független lesz a DNS-től, ami egyetlen meghibásodási pont lehet.  
     
   <pre><code>sudo vi /etc/hosts

   </code></pre>

   Szúrja be a következő sorokat a/etc/hosts. Az IP-cím és az állomásnév módosítása a környezetnek megfelelően   

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]** fürt telepítése
- Ha SBD-eszközöket használ a kerítéshez
   <pre><code>sudo ha-cluster-init -u
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Address for ring0 [10.0.0.6] <b>Press ENTER</b>
   # Port for ring0 [5405] <b>Press ENTER</b>
   # SBD is already configured to use /dev/disk/by-id/scsi-36001405639245768818458b930abdf69;/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf - overwrite (y/n)? <b>n</b>
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

- Ha *nem használ* SBD-eszközöket a kerítéshez
   <pre><code>sudo ha-cluster-init -u
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Address for ring0 [10.0.0.6] <b>Press ENTER</b>
   # Port for ring0 [5405] <b>Press ENTER</b>
   # Do you wish to use SBD (y/n)? <b>n</b>
   #WARNING: Not configuring SBD - STONITH will be disabled.
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

1. **[2]** csomópont hozzáadása a fürthöz

   <pre><code>sudo ha-cluster-join
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # IP address or hostname of existing node (e.g.: 192.168.1.1) []<b>10.0.0.6</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   </code></pre>

1. **[A]** hacluster jelszavának módosítása ugyanarra a jelszóra

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A] módosítsa A** Corosync beállításait.  

   <pre><code>sudo vi /etc/corosync/corosync.conf
   </code></pre>

   Adja hozzá a következő félkövér tartalmat a fájlhoz, ha az értékek nem léteznek vagy eltérőek. Győződjön meg arról, hogy a tokent 30000 értékre módosítja, hogy a memóriát megőrizve karbantartást lehessen végezni. További információt a Linux vagy a [Windows rendszerhez][virtual-machines-windows-maintenance]készült [cikkben][virtual-machines-linux-maintenance] talál.

   <pre><code>[...]
     <b>token:          30000
     token_retransmits_before_loss_const: 10
     join:           60
     consensus:      36000
     max_messages:   20</b>
     
     interface { 
        [...] 
     }
     transport:      udpu
   } 
   nodelist {
     node {
      ring0_addr:10.0.0.6
     }
     node {
      ring0_addr:10.0.0.7
     } 
   }
   logging {
     [...]
   }
   quorum {
        # Enable and configure quorum subsystem (default: off)
        # see also corosync.conf.5 and votequorum.5
        provider: corosync_votequorum
        <b>expected_votes: 2</b>
        <b>two_node: 1</b>
   }
   </code></pre>

   Ezután indítsa újra a Corosync szolgáltatást

   <pre><code>sudo service corosync restart
   </code></pre>

## <a name="default-pacemaker-configuration-for-sbd"></a>Alapértelmezett pacemaker-konfiguráció a SBD

Az ebben a szakaszban található konfiguráció csak a SBD STONITH használata esetén alkalmazható.  

1. **[1]** STONITH-eszköz használatának engedélyezése és a kerítés késleltetésének beállítása

<pre><code>sudo crm configure property stonith-timeout=144
sudo crm configure property stonith-enabled=true

# List the resources to find the name of the SBD device
sudo crm resource list
sudo crm resource stop stonith-sbd
sudo crm configure delete <b>stonith-sbd</b>
sudo crm configure primitive <b>stonith-sbd</b> stonith:external/sbd \
   params pcmk_delay_max="15" \
   op monitor interval="15" timeout="15"
</code></pre>

## <a name="create-azure-fence-agent-stonith-device"></a>Azure kerítés-ügynök STONITH-eszközének létrehozása

A dokumentáció ezen szakasza csak akkor alkalmazható, ha az Azure kerítés-ügynökön alapuló STONITH használ.
A STONITH-eszköz egy egyszerű szolgáltatásnév használatával engedélyezi a Microsoft Azure. Egy egyszerű szolgáltatásnév létrehozásához kövesse az alábbi lépéseket.

1. Nyissa meg a következőt: <https://portal.azure.com>
1. A Azure Active Directory panel megnyitása  
   Válassza a tulajdonságok lehetőséget, és jegyezze fel a címtár-azonosítót. Ez a **bérlő azonosítója**.
1. Kattintson Alkalmazásregisztrációk
1. Kattintson az új regisztráció elemre.
1. Adjon meg egy nevet, válassza a "fiókok ebben a szervezeti címtárban" lehetőséget. 
2. Válassza az alkalmazás típusa "web" lehetőséget, adjon meg egy bejelentkezési URL-címet (például http: \/ /localhost), és kattintson a Hozzáadás gombra.  
   A bejelentkezési URL-cím nincs használatban, és bármely érvényes URL-cím lehet
1. Válassza a tanúsítványok és titkos kulcsok lehetőséget, majd kattintson az új ügyfél titka elemre.
1. Adja meg az új kulcs leírását, válassza a "soha nem jár le" lehetőséget, majd kattintson a Hozzáadás gombra.
1. Jegyezze fel az értéket. Az egyszerű szolgáltatás **jelszavaként** van használatban
1. Válassza az Áttekintés lehetőséget. Jegyezze fel az alkalmazás AZONOSÍTÓját. A szolgáltatás felhasználóneveként (**Bejelentkezési azonosítóként** az alábbi lépésekben) használatos az egyszerű szolgáltatásnév számára

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** egyéni szerepkör létrehozása a kerítés ügynökéhez

Az egyszerű szolgáltatás alapértelmezés szerint nem rendelkezik engedéllyel az Azure-erőforrások eléréséhez. A fürt összes virtuális gépe elindításához és leállításához (felszabadításához) engedélyeket kell adni a szolgáltatásnak. Ha még nem tette meg az egyéni szerepkört, akkor a [PowerShell](../../../role-based-access-control/custom-roles-powershell.md#create-a-custom-role) vagy az [Azure CLI](../../../role-based-access-control/custom-roles-cli.md) használatával hozhatja létre

Használja az alábbi tartalmat a bemeneti fájlhoz. A tartalmat az előfizetéséhez kell igazítania, a c276fc76-9cd4-44c9-99a7-4fd71546436e és a e91d47c4-76f3-4271-a796-21b4ecfe3624 helyére az előfizetés azonosítóit kell cserélnie. Ha csak egy előfizetéssel rendelkezik, távolítsa el a második bejegyzést a AssignableScopes-ben.

```json
{
    "properties": {
        "roleName": "Linux Fence Agent Role",
        "description": "Allows to power-off and start virtual machines",
        "assignableScopes": [
            "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
            "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.Compute/*/read",
                    "Microsoft.Compute/virtualMachines/powerOff/action",
                    "Microsoft.Compute/virtualMachines/start/action"
                ],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ]
    }
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** az egyéni szerepkör társítása az egyszerű szolgáltatáshoz

Rendelje hozzá az előző fejezetben az egyszerű szolgáltatásnév számára létrehozott "Linux kerítési ügynök szerepkör" egyéni szerepkört. Ne használja többé a tulajdonosi szerepkört!

1. Ugrás ide: [https://portal.azure.com](https://portal.azure.com)
1. Nyissa meg az összes erőforrás panelt
1. Válassza ki az első fürtcsomópont virtuális gépét.
1. Kattintson a hozzáférés-vezérlés (IAM) elemre.
1. Kattintson a szerepkör-hozzárendelés hozzáadása elemre.
1. Válassza ki a "Linux kerítési ügynök szerepkör" szerepkört
1. Adja meg a fent létrehozott alkalmazás nevét
1. Kattintson a Save (Mentés) gombra.

Ismételje meg a fenti lépéseket a második fürtcsomóponton.

### <a name="1-create-the-stonith-devices"></a>**[1]** a STONITH-eszközök létrehozása

A virtuális gépek engedélyeinek szerkesztése után a fürtben konfigurálhatja a STONITH-eszközöket.

> [!NOTE]
> A (z) "pcmk_host_map" kapcsoló csak akkor szükséges a parancsban, ha az állomásnév és az Azure-beli virtuális gép neve nem azonos. Adja meg a leképezést a **hostname: VM-Name** formátumban.
> Tekintse meg az parancs félkövér szakaszát.

<pre><code>sudo crm configure property stonith-enabled=true
crm configure property concurrent-fencing=true
# replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
  params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>" \
  pcmk_monitor_retries=4 pcmk_action_limit=3 power_timeout=240 pcmk_reboot_timeout=900 <b>pcmk_host_map="prod-cl1-0:prod-cl1-0-vm-name;prod-cl1-1:prod-cl1-1-vm-name"</b> \
  op monitor interval=3600 timeout=120

sudo crm configure property stonith-timeout=900

</code></pre>

> [!IMPORTANT]
> A figyelési és a kerítési műveletek deszerializáltak. Ennek eredményeképpen, ha már futó figyelési művelet és egyidejű kerítési esemény van, akkor a fürt feladatátvétele a már futó figyelési művelet miatt nem jár késéssel.

> [!TIP]
>Az Azure kerítés ügynöke a nyilvános végponti pontokhoz kapcsolódóan dokumentált kimenő kapcsolatot igényel, valamint a lehetséges megoldásokkal együtt a [standard szintű ILB használó virtuális gépek nyilvános végponti kapcsolatát](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

## <a name="pacemaker-configuration-for-azure-scheduled-events"></a>Pacemaker-konfiguráció az Azure ütemezett eseményeihez

Az Azure [ütemezett eseményeket](../../linux/scheduled-events.md)kínál. Az ütemezett események a meta-adatszolgáltatáson keresztül érhetők el, és lehetővé teszik, hogy az alkalmazás előkészítse az eseményeket, például a virtuális gépek leállítását, a virtuális gépek újratelepítését stb. Erőforrás **[-ügynök Azure – események](https://github.com/ClusterLabs/resource-agents/pull/1161)** figyelők az ütemezett Azure-eseményekhez. Ha a rendszer eseményeket észlel, az ügynök megkísérli leállítani az érintett virtuális gép összes erőforrását, és áthelyezi őket a fürt egy másik csomópontjára. A további pacemaker-erőforrások eléréséhez konfigurálni kell. 

1. **[A]** ellenőrizze, hogy az **Azure-Events** Agent csomag már telepítve van-e és naprakész-e. 

<pre><code>sudo zypper info resource-agents
</code></pre>

2. **[1]** adja meg az erőforrásokat a pacemakerben. 

<pre><code>
#Place the cluster in maintenance mode
sudo crm configure property maintenance-mode=true

#Create Pacemaker resources for the Azure agent
sudo crm configure primitive rsc_azure-events ocf:heartbeat:azure-events op monitor interval=10s
sudo crm configure clone cln_azure-events rsc_azure-events

#Take the cluster out of maintenance mode
sudo crm configure property maintenance-mode=false
</code></pre>

   > [!NOTE]
   > Miután konfigurálta a pacemaker erőforrásait az Azure-Events Agent szolgáltatáshoz, amikor a fürtöt karbantartási módba vagy kifelé helyezi, a következőhöz hasonló figyelmeztető üzenetek jelenhetnek meg:  
     Figyelmeztetés: CIB-bootstrap-Options: ismeretlen "hostName_ <strong> állomásnév</strong>" attribútum  
     Figyelmeztetés: CIB-bootstrap-Options: ismeretlen "Azure-events_globalPullState" attribútum  
     Figyelmeztetés: CIB-bootstrap-Options: ismeretlen "hostName_ <strong>állomásnév</strong>" attribútum  
   > Ezek a figyelmeztető üzenetek figyelmen kívül hagyhatók.

## <a name="next-steps"></a>Következő lépések

* [Azure Virtual Machines az SAP tervezéséhez és megvalósításához][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP-ban][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hoz][dbms-guide]
* [Magas rendelkezésre állás az NFS-en SUSE Linux Enterprise Server Azure-beli virtuális gépeken][sles-nfs-guide]
* [Magas rendelkezésre állás az Azure-beli virtuális gépeken futó SAP NetWeaver számára SUSE Linux Enterprise Server SAP-alkalmazásokhoz][sles-guide]
* A magas rendelkezésre állás és a SAP HANA Azure-beli virtuális gépeken történő vész-helyreállítási tervének megismeréséhez lásd: [Az Azure-beli SAP HANA magas rendelkezésre állása Virtual Machines (VM)][sap-hana-ha]

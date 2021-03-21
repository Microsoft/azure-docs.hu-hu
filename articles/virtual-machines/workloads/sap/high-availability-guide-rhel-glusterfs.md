---
title: GlusterFS az Azure-beli virtuális gépeken a RHEL for SAP NetWeaver szolgáltatáshoz | Microsoft Docs
description: Red Hat Enterprise Linuxon futó Azure-beli virtuális gépeken üzemelő GlusterFS SAP NetWeaverhez
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
ms.date: 08/16/2018
ms.author: radeltch
ms.openlocfilehash: 3ebc125fe6802ffbe4192c0250ec9adc2ceceb0b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101668724"
---
# <a name="glusterfs-on-azure-vms-on-red-hat-enterprise-linux-for-sap-netweaver"></a>Red Hat Enterprise Linuxon futó Azure-beli virtuális gépeken üzemelő GlusterFS SAP NetWeaverhez

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md

Ez a cikk leírja, hogyan telepítheti a virtuális gépeket, konfigurálhatja a virtuális gépeket, valamint telepítheti a GlusterFS-fürtöt, amely egy magasan elérhető SAP-rendszer megosztott adattárolására használható.
Ez az útmutató a két SAP-rendszer, a NW1 és a NW2 által használt GlusterFS beállítását ismerteti. A példában szereplő erőforrások nevei (például a virtuális gépek, a virtuális hálózatok) feltételezik, hogy az [SAP fájlkiszolgáló sablont][template-file-server] használta a **glust** erőforrás-előtaggal.

Először olvassa el a következő SAP-megjegyzéseket és dokumentumokat

* SAP-Megjegyzés [1928533], amely a következőket tartalmazta:
  * Az SAP-szoftverek üzembe helyezéséhez támogatott Azure-beli virtuálisgép-méretek listája
  * Fontos kapacitási információk Azure-beli virtuális gépek méreteihez
  * Támogatott SAP-szoftverek és operációs rendszerek (OS) és adatbázis-kombinációk
  * A Windows és a Linux rendszerhez szükséges SAP kernel verziója Microsoft Azure

* Az SAP Note [2015553] az SAP által támogatott SAP-szoftverek Azure-beli üzembe helyezésének előfeltételeit sorolja fel.
* Az SAP Megjegyzés [2002167] ajánlott operációsrendszer-beállításokkal Red Hat Enterprise Linux
* A [2009879] -es SAP-Megjegyzés SAP HANA irányelvek a Red Hat Enterprise Linux
* Az [2178632] -es SAP-Megjegyzés részletes információkat tartalmaz az Azure-beli SAP-ban jelentett összes figyelési mérőszámról.
* A [2191498] -es SAP-Megjegyzés a szükséges SAP-gazdagép ügynökének verziója az Azure-ban linuxos.
* Az [2243692] -es SAP-Megjegyzés az Azure-beli Linuxon futó SAP-licenceléssel kapcsolatos információkat tartalmaz.
* Az SAP Megjegyzés [1999351] további hibaelhárítási információkat tartalmaz az SAP-hez készült Azure Enhanced monitoring bővítménnyel kapcsolatban.
* Az [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) rendelkezik minden szükséges SAP-megjegyzéssel a Linux rendszerhez.
* [Azure Virtual Machines tervezése és implementálása Linux rendszeren az SAP-ban][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP-hez Linux rendszeren (ez a cikk)][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hez Linux rendszeren][dbms-guide]
* [A Red Hat Gluster Storage termék dokumentációja](https://access.redhat.com/documentation/red_hat_gluster_storage/)
* Általános RHEL dokumentáció
  * [Magas rendelkezésre állású Add-On áttekintése](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Magas rendelkezésre állású Add-On felügyelet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Magas rendelkezésre állású Add-On referenciája](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Az Azure-specifikus RHEL dokumentációja:
  * [A RHEL magas rendelkezésre állású fürtökre vonatkozó támogatási szabályzatok – Microsoft Azure Virtual Machines a fürt tagjai](https://access.redhat.com/articles/3131341)
  * [Red Hat Enterprise Linux 7,4 (és újabb) High-Availability fürt telepítése és konfigurálása Microsoft Azure](https://access.redhat.com/articles/3252491)

## <a name="overview"></a>Áttekintés

A magas rendelkezésre állás elérése érdekében az SAP NetWeaver megosztott tárterületet igényel. A GlusterFS külön fürtben van konfigurálva, és több SAP-rendszer is használható.

![SAP NetWeaver – magas rendelkezésre állás – áttekintés](./media/high-availability-guide-rhel-glusterfs/rhel-glusterfs.png)

## <a name="set-up-glusterfs"></a>GlusterFS beállítása

A githubról származó Azure-sablonok használatával üzembe helyezheti az összes szükséges Azure-erőforrást, beleértve a virtuális gépeket, a rendelkezésre állási készletet és a hálózati adaptereket, vagy manuálisan is üzembe helyezheti az erőforrásokat.

### <a name="deploy-linux-via-azure-template"></a>Linux telepítése Azure-sablon használatával

Az Azure Marketplace egy Red Hat Enterprise Linux rendszerképet tartalmaz, amely az új virtuális gépek üzembe helyezéséhez használható.
Az összes szükséges erőforrás üzembe helyezéséhez használhatja a githubon a rövid útmutató sablonjait. A sablon üzembe helyezi a virtuális gépeket, a rendelkezésre állási készletet stb. A sablon üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az [SAP fájlkiszolgáló sablonját][template-file-server] a Azure Portal
1. Adja meg a következő paramétereket
   1. Erőforrás-előtag  
      Adja meg a használni kívánt előtagot. Az értéket a rendszer az üzembe helyezett erőforrások előtagjaként használja.
   2. Az SAP-rendszer száma mezőben adja meg a fájlkiszolgáló által használt SAP-rendszerek számát. Ezzel a szükséges számú lemezt telepíti stb.
   3. Operációs rendszer típusa  
      Válassza ki a Linux-disztribúciók egyikét. Ebben a példában válassza a RHEL 7 elemet.
   4. Rendszergazdai Felhasználónév, rendszergazdai jelszó vagy SSH-kulcs  
      Létrejön egy új felhasználó, amely használható a gépre való bejelentkezéshez.
   5. Alhálózat azonosítója  
      Ha a virtuális gépet egy olyan meglévő VNet szeretné telepíteni, amelyben a virtuális gépet definiáló alhálózat van, akkor nevezze el az adott alhálózat AZONOSÍTÓját. Az azonosító általában úgy néz ki, mint az/Subscriptions/**&lt; előfizetés &gt; -azonosítója**/ResourceGroups/**&lt; &gt;****&lt; &gt;** nevű erőforráscsoport neve/Providers/Microsoft.Network/virtualNetworks/virtuális hálózat neve/Subnets/**&lt; alhálózat neve &gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>A Linux telepítése manuálisan Azure Portal használatával

Először létre kell hoznia a fürthöz tartozó virtuális gépeket. Ezt követően hozzon létre egy terheléselosztó-t, és használja a virtuális gépeket a háttér-készletekben. A [standard Load Balancer](../../../load-balancer/load-balancer-overview.md)használatát javasoljuk.  

1. Erőforráscsoport létrehozása
1. Virtuális hálózat létrehozása
1. Rendelkezésre állási csoport létrehozása  
   Maximális frissítési tartomány beállítása
1. 1. virtuális gép létrehozása  
   Használjon legalább RHEL 7, ebben a példában a Red Hat Enterprise Linux 7,4 rendszerképet <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Válassza ki a korábban létrehozott rendelkezésre állási készletet  
1. 2. virtuális gép létrehozása  
   Használjon legalább RHEL 7, ebben a példában a Red Hat Enterprise Linux 7,4 rendszerképet <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Válassza ki a korábban létrehozott rendelkezésre állási készletet  
1. Mindegyik SAP-rendszerhez adjon hozzá egy adatlemezt mindkét virtuális géphez.

### <a name="configure-glusterfs"></a>GlusterFS konfigurálása

A következő elemek előtaggal vannak ellátva a **[a]** típussal – az összes csomópontra érvényes, **[1]** – csak az 1 **.,** **[2]** csomópontra vonatkozik – csak a 3. csomópontra alkalmazható.

1. **[A]** telepítési állomásnév feloldása

   Használhat DNS-kiszolgálót, vagy módosíthatja a/etc/hosts az összes csomóponton. Ez a példa a/etc/hosts fájl használatát mutatja be.
   Cserélje le az IP-címet és a gazdagépet a következő parancsokra

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Szúrja be a következő sorokat a/etc/hosts. Az IP-cím és az állomásnév módosítása a környezetnek megfelelően

   <pre><code># IP addresses of the Gluster nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   </code></pre>

1. **[A]** regisztráció

   Regisztrálja a virtuális gépeket, és csatolja azt egy olyan készlethez, amely a RHEL 7 és a GlusterFS tárházait tartalmazza

   <pre><code>sudo subscription-manager register
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

1. **[A]** GlusterFS-repók engedélyezése

   A szükséges csomagok telepítéséhez engedélyezze a következő adattárakat.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rh-gluster-3-for-rhel-7-server-rpms
   </code></pre>
  
1. **[A]** GlusterFS-csomagok telepítése

   A csomagok telepítése az összes GlusterFS-csomóponton

   <pre><code>sudo yum -y install redhat-storage-server
   </code></pre>

   A telepítés után indítsa újra a csomópontokat.

1. **[A]** tűzfal módosítása

   Tűzfalszabályok hozzáadásával engedélyezheti az ügyfelek forgalmát a GlusterFS-csomópontok számára.

   <pre><code># list the available zones
   firewall-cmd --get-active-zones
   
   sudo firewall-cmd --zone=public --add-service=glusterfs --permanent
   sudo firewall-cmd --zone=public --add-service=glusterfs
   </code></pre>

1. **[A] a** GlusterFS szolgáltatás engedélyezése és elindítása

   Indítsa el a GlusterFS szolgáltatást az összes csomóponton.

   <pre><code>sudo systemctl start glusterd
   sudo systemctl enable glusterd
   </code></pre>

1. **[1]** GluserFS létrehozása

   Futtassa a következő parancsokat a GlusterFS-fürt létrehozásához.

   <pre><code>sudo gluster peer probe glust-1
   sudo gluster peer probe glust-2
   
   # Check gluster peer status
   sudo gluster peer status
   
   # Number of Peers: 2
   # 
   # Hostname: glust-1
   # Uuid: 10d43840-fee4-4120-bf5a-de9c393964cd
   # State: Accepted peer request (Connected)
   # 
   # Hostname: glust-2
   # Uuid: 9e340385-12fe-495e-ab0f-4f851b588cba
   # State: Accepted peer request (Connected)
   </code></pre>

1. **[2]** a társak állapotának tesztelése

   A társ állapotának tesztelése a második csomóponton

   <pre><code>sudo gluster peer status
   # Number of Peers: 2
   #
   # Hostname: glust-0
   # Uuid: 6bc6927b-7ee2-461b-ad04-da123124d6bd
   # State: Peer in Cluster (Connected)
   #
   # Hostname: glust-2
   # Uuid: 9e340385-12fe-495e-ab0f-4f851b588cba
   # State: Peer in Cluster (Connected)
   </code></pre>

1. **[3]** a társ állapotának tesztelése

   A társ állapotának tesztelése a harmadik csomóponton

   <pre><code>sudo gluster peer status
   # Number of Peers: 2
   #
   # Hostname: glust-0
   # Uuid: 6bc6927b-7ee2-461b-ad04-da123124d6bd
   # State: Peer in Cluster (Connected)
   #
   # Hostname: glust-1
   # Uuid: 10d43840-fee4-4120-bf5a-de9c393964cd
   # State: Peer in Cluster (Connected)
   </code></pre>

1. **[A]** LVM létrehozása

   Ebben a példában a GlusterFS két SAP-rendszer, a NW1 és a NW2 esetében használatos. Az alábbi parancsokkal LVM-konfigurációkat hozhat létre ezekhez az SAP-rendszerekhez.

   NW1-parancsok használata

   <pre><code>sudo pvcreate --dataalignment 1024K /dev/disk/azure/scsi1/lun0
   sudo pvscan
   sudo vgcreate --physicalextentsize 256K rhgs-<b>NW1</b> /dev/disk/azure/scsi1/lun0
   sudo vgscan
   sudo lvcreate -l 50%FREE -n rhgs-<b>NW1</b>/sapmnt
   sudo lvcreate -l 20%FREE -n rhgs-<b>NW1</b>/trans
   sudo lvcreate -l 10%FREE -n rhgs-<b>NW1</b>/sys
   sudo lvcreate -l 50%FREE -n rhgs-<b>NW1</b>/ascs
   sudo lvcreate -l 100%FREE -n rhgs-<b>NW1</b>/aers
   sudo lvscan
   
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW1</b>/sapmnt
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW1</b>/trans
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW1</b>/sys
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW1</b>/ascs
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW1</b>/aers
   
   sudo mkdir -p /rhs/<b>NW1</b>/sapmnt
   sudo mkdir -p /rhs/<b>NW1</b>/trans
   sudo mkdir -p /rhs/<b>NW1</b>/sys
   sudo mkdir -p /rhs/<b>NW1</b>/ascs
   sudo mkdir -p /rhs/<b>NW1</b>/aers
   
   sudo chattr +i /rhs/<b>NW1</b>/sapmnt
   sudo chattr +i /rhs/<b>NW1</b>/trans
   sudo chattr +i /rhs/<b>NW1</b>/sys
   sudo chattr +i /rhs/<b>NW1</b>/ascs
   sudo chattr +i /rhs/<b>NW1</b>/aers

   echo -e "/dev/rhgs-<b>NW1</b>/sapmnt\t/rhs/<b>NW1</b>/sapmnt\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW1</b>/trans\t/rhs/<b>NW1</b>/trans\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW1</b>/sys\t/rhs/<b>NW1</b>/sys\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW1</b>/ascs\t/rhs/<b>NW1</b>/ascs\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW1</b>/aers\t/rhs/<b>NW1</b>/aers\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   sudo mount -a
   </code></pre>

   NW2-parancsok használata

   <pre><code>sudo pvcreate --dataalignment 1024K /dev/disk/azure/scsi1/lun1
   sudo pvscan
   sudo vgcreate --physicalextentsize 256K rhgs-<b>NW2</b> /dev/disk/azure/scsi1/lun1
   sudo vgscan
   sudo lvcreate -l 50%FREE -n rhgs-<b>NW2</b>/sapmnt
   sudo lvcreate -l 20%FREE -n rhgs-<b>NW2</b>/trans
   sudo lvcreate -l 10%FREE -n rhgs-<b>NW2</b>/sys
   sudo lvcreate -l 50%FREE -n rhgs-<b>NW2</b>/ascs
   sudo lvcreate -l 100%FREE -n rhgs-<b>NW2</b>/aers
   
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW2</b>/sapmnt
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW2</b>/trans
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW2</b>/sys
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW2</b>/ascs
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW2</b>/aers
   
   sudo mkdir -p /rhs/<b>NW2</b>/sapmnt
   sudo mkdir -p /rhs/<b>NW2</b>/trans
   sudo mkdir -p /rhs/<b>NW2</b>/sys
   sudo mkdir -p /rhs/<b>NW2</b>/ascs
   sudo mkdir -p /rhs/<b>NW2</b>/aers
   
   sudo chattr +i /rhs/<b>NW2</b>/sapmnt
   sudo chattr +i /rhs/<b>NW2</b>/trans
   sudo chattr +i /rhs/<b>NW2</b>/sys
   sudo chattr +i /rhs/<b>NW2</b>/ascs
   sudo chattr +i /rhs/<b>NW2</b>/aers
   sudo lvscan
   
   echo -e "/dev/rhgs-<b>NW2</b>/sapmnt\t/rhs/<b>NW2</b>/sapmnt\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW2</b>/trans\t/rhs/<b>NW2</b>/trans\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW2</b>/sys\t/rhs/<b>NW2</b>/sys\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW2</b>/ascs\t/rhs/<b>NW2</b>/ascs\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW2</b>/aers\t/rhs/<b>NW2</b>/aers\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   sudo mount -a
   </code></pre>

1. **[1]** az elosztott kötet létrehozása

   A következő parancsokkal hozza létre a GlusterFS-kötetet a NW1, és indítsa el.

   <pre><code>sudo gluster vol create <b>NW1</b>-sapmnt replica 3 glust-0:/rhs/<b>NW1</b>/sapmnt glust-1:/rhs/<b>NW1</b>/sapmnt glust-2:/rhs/<b>NW1</b>/sapmnt force
   sudo gluster vol create <b>NW1</b>-trans replica 3 glust-0:/rhs/<b>NW1</b>/trans glust-1:/rhs/<b>NW1</b>/trans glust-2:/rhs/<b>NW1</b>/trans force
   sudo gluster vol create <b>NW1</b>-sys replica 3 glust-0:/rhs/<b>NW1</b>/sys glust-1:/rhs/<b>NW1</b>/sys glust-2:/rhs/<b>NW1</b>/sys force
   sudo gluster vol create <b>NW1</b>-ascs replica 3 glust-0:/rhs/<b>NW1</b>/ascs glust-1:/rhs/<b>NW1</b>/ascs glust-2:/rhs/<b>NW1</b>/ascs force
   sudo gluster vol create <b>NW1</b>-aers replica 3 glust-0:/rhs/<b>NW1</b>/aers glust-1:/rhs/<b>NW1</b>/aers glust-2:/rhs/<b>NW1</b>/aers force
   
   sudo gluster volume start <b>NW1</b>-sapmnt
   sudo gluster volume start <b>NW1</b>-trans
   sudo gluster volume start <b>NW1</b>-sys
   sudo gluster volume start <b>NW1</b>-ascs
   sudo gluster volume start <b>NW1</b>-aers
   </code></pre>

   A következő parancsokkal hozza létre a GlusterFS-kötetet a NW2, és indítsa el.

   <pre><code>sudo gluster vol create <b>NW2</b>-sapmnt replica 3 glust-0:/rhs/<b>NW2</b>/sapmnt glust-1:/rhs/<b>NW2</b>/sapmnt glust-2:/rhs/<b>NW2</b>/sapmnt force
   sudo gluster vol create <b>NW2</b>-trans replica 3 glust-0:/rhs/<b>NW2</b>/trans glust-1:/rhs/<b>NW2</b>/trans glust-2:/rhs/<b>NW2</b>/trans force
   sudo gluster vol create <b>NW2</b>-sys replica 3 glust-0:/rhs/<b>NW2</b>/sys glust-1:/rhs/<b>NW2</b>/sys glust-2:/rhs/<b>NW2</b>/sys force
   sudo gluster vol create <b>NW2</b>-ascs replica 3 glust-0:/rhs/<b>NW2</b>/ascs glust-1:/rhs/<b>NW2</b>/ascs glust-2:/rhs/<b>NW2</b>/ascs force
   sudo gluster vol create <b>NW2</b>-aers replica 3 glust-0:/rhs/<b>NW2</b>/aers glust-1:/rhs/<b>NW2</b>/aers glust-2:/rhs/<b>NW2</b>/aers force
   
   sudo gluster volume start <b>NW2</b>-sapmnt
   sudo gluster volume start <b>NW2</b>-trans
   sudo gluster volume start <b>NW2</b>-sys
   sudo gluster volume start <b>NW2</b>-ascs
   sudo gluster volume start <b>NW2</b>-aers
   </code></pre>

## <a name="next-steps"></a>Következő lépések

* [Az SAP-ASCS és-adatbázis telepítése](high-availability-guide-rhel.md)
* [Azure Virtual Machines az SAP tervezéséhez és megvalósításához][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP-ban][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hoz][dbms-guide]
* Ha meg szeretné tudni, hogyan hozhat létre magas rendelkezésre állást, és hogyan tervezheti meg az Azure-beli SAP HANA vész-helyreállítását (nagyméretű példányok), tekintse meg [a SAP HANA (nagyméretű példányok) magas rendelkezésre állását és a](hana-overview-high-availability-disaster-recovery.md)
* A magas rendelkezésre állás és a SAP HANA Azure-beli virtuális gépeken történő vész-helyreállítási tervének megismeréséhez lásd: [Az Azure-beli SAP HANA magas rendelkezésre állása Virtual Machines (VM)][sap-hana-ha]

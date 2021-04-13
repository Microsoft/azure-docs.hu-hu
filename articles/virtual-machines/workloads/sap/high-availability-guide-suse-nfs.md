---
title: Magas rendelkezésre állás NFS-hez Azure-beli virtuális gépeken a SLES-on | Microsoft Docs
description: Magas rendelkezésre állás az NFS-en SUSE Linux Enterprise Server Azure-beli virtuális gépeken
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
ms.date: 04/12/2021
ms.author: radeltch
ms.openlocfilehash: a08d82103986435593cd73a0728e7426f205a90d
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307537"
---
# <a name="high-availability-for-nfs-on-azure-vms-on-suse-linux-enterprise-server"></a>Magas rendelkezésre állás az NFS-en SUSE Linux Enterprise Server Azure-beli virtuális gépeken

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

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

[sles-hae-guides]:https://www.suse.com/documentation/sle-ha-12/
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md

Ez a cikk leírja, hogyan telepítheti a virtuális gépeket, konfigurálhatja a virtuális gépeket, telepítheti a fürtöt, és telepíthet egy olyan, magasan rendelkezésre álló NFS-kiszolgálót, amely egy magasan elérhető SAP-rendszer megosztott adattárolására használható.
Ez az útmutató bemutatja, hogyan állíthat be egy olyan, magasan elérhető NFS-kiszolgálót, amelyet két SAP-rendszer, a NW1 és a NW2 használ. A példában szereplő erőforrások nevei (például a virtuális gépek, a virtuális hálózatok) feltételezik, hogy az [SAP fájlkiszolgáló sablonját][template-file-server] használta a következő előtaggal: **Prod**.


> [!NOTE]
> Ez a cikk a *Slave* és a *Master* kifejezésre mutató hivatkozásokat tartalmaz, amelyeket a Microsoft már nem használ. Ha eltávolítja a feltételeket a szoftverből, a rendszer eltávolítja őket ebből a cikkből.

Először olvassa el a következő SAP-megjegyzéseket és dokumentumokat

* SAP-Megjegyzés [1928533], amely a következőket tartalmazta:
  * Az SAP-szoftverek üzembe helyezéséhez támogatott Azure-beli virtuálisgép-méretek listája
  * Fontos kapacitási információk Azure-beli virtuális gépek méreteihez
  * Támogatott SAP-szoftverek és operációs rendszerek (OS) és adatbázis-kombinációk
  * A Windows és a Linux rendszerhez szükséges SAP kernel verziója Microsoft Azure

* Az SAP Note [2015553] az SAP által támogatott SAP-szoftverek Azure-beli üzembe helyezésének előfeltételeit sorolja fel.
* Az SAP Megjegyzés [2205917] ajánlott operációsrendszer-beállításokkal SUSE Linux Enterprise Server SAP-alkalmazásokhoz
* A [1944799] -es SAP-Megjegyzés SAP HANA irányelvek az SAP-alkalmazásokhoz SUSE Linux Enterprise Server
* Az [2178632] -es SAP-Megjegyzés részletes információkat tartalmaz az Azure-beli SAP-ban jelentett összes figyelési mérőszámról.
* A [2191498] -es SAP-Megjegyzés a szükséges SAP-gazdagép ügynökének verziója az Azure-ban linuxos.
* Az [2243692] -es SAP-Megjegyzés az Azure-beli Linuxon futó SAP-licenceléssel kapcsolatos információkat tartalmaz.
* Az [1984787] -es SAP-Megjegyzés általános információkat tartalmaz a SUSE Linux Enterprise Server 12.
* Az SAP Megjegyzés [1999351] további hibaelhárítási információkat tartalmaz az SAP-hez készült Azure Enhanced monitoring bővítménnyel kapcsolatban.
* Az [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) rendelkezik minden szükséges SAP-megjegyzéssel a Linux rendszerhez.
* [Azure Virtual Machines tervezése és implementálása Linux rendszeren az SAP-ban][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP-hez Linux rendszeren (ez a cikk)][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hez Linux rendszeren][dbms-guide]
* [SUSE Linux Enterprise magas rendelkezésre állású bővítmény 12 SP3 ajánlott eljárások útmutatók][sles-hae-guides]
  * A DRBD és a pacemaker szolgáltatással rendelkező, magasan elérhető NFS-tárolók
* [SUSE Linux Enterprise Server for SAP Applications 12 SP3 – ajánlott eljárások útmutatók][sles-for-sap-bp]
* [SUSE magas rendelkezésre állású bővítmény – 12 SP3 kibocsátási megjegyzések][suse-ha-12sp3-relnotes]

## <a name="overview"></a>Áttekintés

A magas rendelkezésre állás eléréséhez az SAP NetWeaver használatához NFS-kiszolgáló szükséges. Az NFS-kiszolgáló külön fürtben van konfigurálva, és több SAP-rendszer is használható.

![SAP NetWeaver – magas rendelkezésre állás – áttekintés](./media/high-availability-guide-nfs/ha-suse-nfs.png)

Az NFS-kiszolgáló egy dedikált virtuális gazdagépet és virtuális IP-címet használ minden olyan SAP-rendszerhez, amely ezt az NFS-kiszolgálót használja. Az Azure-ban a virtuális IP-címek használatához terheléselosztó szükséges. A következő lista a terheléselosztó konfigurációját mutatja be.        

* Előtér-konfiguráció
  * NW1 IP-10.0.0.4
  * NW2 IP-10.0.0.5
* Háttér-konfiguráció
  * Az NFS-fürt részét képező összes virtuális gép elsődleges hálózati adapteréhez csatlakozik
* Mintavételi port
  * A NW1 61000-es portja
  * A NW2 61001-es portja
* Terheléselosztási szabályok (ha alapszintű Load balancert használ)
  * 2049 TCP a NW1
  * 2049 UDP a NW1
  * 2049 TCP a NW2
  * 2049 UDP a NW2

## <a name="set-up-a-highly-available-nfs-server"></a>Egy magasan elérhető NFS-kiszolgáló beállítása

A GitHubon található Azure-sablonok használatával üzembe helyezheti az összes szükséges Azure-erőforrást, beleértve a virtuális gépeket, a rendelkezésre állási készletet és a terheléselosztó-t, vagy manuálisan is üzembe helyezheti az erőforrásokat.

### <a name="deploy-linux-via-azure-template"></a>Linux telepítése Azure-sablon használatával

Az Azure Marketplace lemezképet tartalmaz a SUSE Linux Enterprise Server for SAP Applications for 12 szolgáltatáshoz, amely az új virtuális gépek üzembe helyezésére használható.
Az összes szükséges erőforrás üzembe helyezéséhez használhatja a GitHubon a rövid útmutató sablonjait. A sablon üzembe helyezi a virtuális gépeket, a terheléselosztó-t, a rendelkezésre állási készletet stb. A sablon üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az [SAP fájlkiszolgáló sablonját][template-file-server] a Azure Portal   
1. Adja meg a következő paramétereket
   1. Erőforrás-előtag  
      Adja meg a használni kívánt előtagot. Az értéket a rendszer az üzembe helyezett erőforrások előtagjaként használja.
   2. SAP-rendszerek száma  
      Adja meg, hogy hány SAP-rendszer fogja használni ezt a kiszolgálót. Ezzel a beállítással a szükséges méretű előtér-konfigurációk, terheléselosztási szabályok, mintavételi portok, lemezek stb. telepíthetők.
   3. Operációs rendszer típusa  
      Válassza ki a Linux-disztribúciók egyikét. Ebben a példában válassza a 12. SLES
   4. Rendszergazdai Felhasználónév és rendszergazdai jelszó  
      Létrejön egy új felhasználó, amely használható a gépre való bejelentkezéshez.
   5. Alhálózat azonosítója  
      Ha a virtuális gépet egy olyan meglévő VNet szeretné telepíteni, amelyben a virtuális gépet definiáló alhálózat van, akkor nevezze el az adott alhálózat AZONOSÍTÓját. Az azonosító általában úgy néz ki, mint az/Subscriptions/**&lt; előfizetés &gt; -azonosítója**/ResourceGroups/**&lt; &gt;****&lt; &gt;** nevű erőforráscsoport neve/Providers/Microsoft.Network/virtualNetworks/virtuális hálózat neve/Subnets/**&lt; alhálózat neve &gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>A Linux telepítése manuálisan Azure Portal használatával

Először létre kell hoznia a virtuális gépeket ehhez az NFS-fürthöz. Ezt követően hozzon létre egy terheléselosztó-t, és használja a virtuális gépeket a háttér-készletekben.

1. Erőforráscsoport létrehozása
1. Virtuális hálózat létrehozása
1. Rendelkezésre állási csoport létrehozása  
   Maximális frissítési tartomány beállítása
1. 1. virtuális gép létrehozása legalább SLES4SAP 12 SP3, ebben a példában a SLES4SAP 12 SP3 BYOS rendszerkép SLES SAP-alkalmazásokhoz 12 SP3 (BYOS) használata  
   Válassza ki a korábban létrehozott rendelkezésre állási készletet  
1. A 2. virtuális gép létrehozása legalább SLES4SAP 12 SP3, ebben a példában a SLES4SAP 12 SP3 BYOS rendszerkép  
   SLES for SAP Applications for 12 SP3 (BYOS) használata  
   Válassza ki a korábban létrehozott rendelkezésre állási készletet  
1. Mindegyik SAP-rendszerhez adjon hozzá egy adatlemezt mindkét virtuális géphez.
1. Hozzon létre egy Load Balancer (belső). A [standard Load Balancer](../../../load-balancer/load-balancer-overview.md)használatát javasoljuk.  
   1. A standard Load Balancer létrehozásához kövesse az alábbi utasításokat:
      1. Az előtérbeli IP-címek létrehozása
         1. NW1 IP-10.0.0.4
            1. Nyissa meg a terheléselosztó-t, válassza a előtéri IP-készlet lehetőséget, majd kattintson a Hozzáadás gombra.
            1. Adja meg az új előtér-IP-készlet nevét (például **NW1-frontend**)
            1. Állítsa a hozzárendelést statikus értékre, és adja meg az IP-címet (például **10.0.0.4**).
            1. Kattintson az OK gombra
         1. NW2 IP-10.0.0.5
            * Ismételje meg a fenti lépéseket a NW2
      1. A háttér-készletek létrehozása
         1. Az NFS-fürt részét képező összes virtuális gép elsődleges hálózati adapteréhez csatlakozik
            1. Nyissa meg a Load balancert, válassza a háttérbeli készletek elemet, majd kattintson a Hozzáadás gombra.
            1. Adja meg az új háttérbeli készlet nevét (például **NW-backend**)
            1. Virtual Network kiválasztása
            1. Kattintson a virtuális gép hozzáadása elemre.
            1. Válassza ki az NFS-fürthöz tartozó virtuális gépeket és azok IP-címeit.
            1. Kattintson az Add (Hozzáadás) parancsra.
      1. Az állapot-mintavételek létrehozása
         1. A NW1 61000-es portja
            1. Nyissa meg a terheléselosztó-t, válassza az állapot-tesztek elemet, majd kattintson a Hozzáadás gombra.
            1. Adja meg az új állapot-mintavétel nevét (például **NW1-HP**)
            1. Válassza a TCP protokollt, a 610 **00** portot, az 5. időközt és a nem megfelelő állapotú küszöbértéket 2
            1. Kattintson az OK gombra
         1. A NW2 61001-es portja
            * A fenti lépések megismétlésével hozzon létre egy állapot-mintavételt a NW2
      1. Terheléselosztási szabályok
         1. Nyissa meg a Load balancert, válassza a terheléselosztási szabályok elemet, majd kattintson a Hozzáadás gombra.
         1. Adja meg az új Load Balancer-szabály nevét (például **NW1-LB**)
         1. Válassza ki a korábban létrehozott előtérbeli IP-címet, a háttér-készletet és az állapot-mintavételt (például **NW1-frontend**). **NW-háttér** és **NW1-HP**)
         1. Válassza a **hektár portok** lehetőséget.
         1. **Ügyeljen arra, hogy a lebegő IP-címet engedélyezze**
         1. Kattintson az OK gombra
         * A fenti lépések megismétlésével hozzon létre terheléselosztási szabályt a NW2
   1. Ha a forgatókönyv alapszintű Load balancert igényel, kövesse az alábbi utasításokat:
      1. Az előtérbeli IP-címek létrehozása
         1. NW1 IP-10.0.0.4
            1. Nyissa meg a terheléselosztó-t, válassza a előtéri IP-készlet lehetőséget, majd kattintson a Hozzáadás gombra.
            1. Adja meg az új előtér-IP-készlet nevét (például **NW1-frontend**)
            1. Állítsa a hozzárendelést statikus értékre, és adja meg az IP-címet (például **10.0.0.4**).
            1. Kattintson az OK gombra
         1. NW2 IP-10.0.0.5
            * Ismételje meg a fenti lépéseket a NW2
      1. A háttér-készletek létrehozása
         1. Az NFS-fürt részét képező összes virtuális gép elsődleges hálózati adapteréhez csatlakozik
            1. Nyissa meg a Load balancert, válassza a háttérbeli készletek elemet, majd kattintson a Hozzáadás gombra.
            1. Adja meg az új háttérbeli készlet nevét (például **NW-backend**)
            1. Kattintson a virtuális gép hozzáadása elemre.
            1. Válassza ki a korábban létrehozott rendelkezésre állási készletet
            1. Az NFS-fürt virtuális gépei kiválasztása
            1. Kattintson az OK gombra
      1. Az állapot-mintavételek létrehozása
         1. A NW1 61000-es portja
            1. Nyissa meg a terheléselosztó-t, válassza az állapot-tesztek elemet, majd kattintson a Hozzáadás gombra.
            1. Adja meg az új állapot-mintavétel nevét (például **NW1-HP**)
            1. Válassza a TCP protokollt, a 610 **00** portot, az 5. időközt és a nem megfelelő állapotú küszöbértéket 2
            1. Kattintson az OK gombra
         1. A NW2 61001-es portja
            * A fenti lépések megismétlésével hozzon létre egy állapot-mintavételt a NW2
      1. Terheléselosztási szabályok
         1. 2049 TCP a NW1
            1. Nyissa meg a terheléselosztó-t, válassza a terheléselosztási szabályok lehetőséget, majd kattintson a Hozzáadás gombra.
            1. Adja meg az új terheléselosztó-szabály nevét (például **NW1-LB-2049**)
            1. Válassza ki a korábban létrehozott előtérbeli IP-címet, a háttér-készletet és az állapot-mintavételt (például **NW1-frontend**)
            1. Tartsa meg a protokoll **TCP**-t, írja be a **2049** portot
            1. Üresjárati időkorlát 30 percre növelve
            1. **Ügyeljen arra, hogy a lebegő IP-címet engedélyezze**
            1. Kattintson az OK gombra
         1. 2049 UDP a NW1
            * Ismételje meg a fenti lépéseket a 2049-es és a NW1-es porton.
         1. 2049 TCP a NW2
            * Ismételje meg a fenti lépéseket a 2049-es és a TCP-es porton a NW2
         1. 2049 UDP a NW2
            * Ismételje meg a fenti lépéseket a 2049-es és a NW2-es porton.

> [!IMPORTANT]
> A lebegő IP-címek nem támogatottak a terheléselosztási helyzetekben a hálózati adapter másodlagos IP-konfigurációjában. További részletek: az [Azure Load Balancer korlátozásai](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Ha a virtuális gép további IP-címére van szüksége, helyezzen üzembe egy második hálózati adaptert.  

> [!Note]
> Ha a nyilvános IP-címek nélküli virtuális gépek a belső (nincs nyilvános IP-cím) standard Azure Load Balancer háttér-készletbe kerülnek, nem lesz kimenő internetkapcsolat, kivéve, ha további konfigurálást végeznek a nyilvános végpontok útválasztásának engedélyezéséhez. A kimenő kapcsolatok elérésével kapcsolatos részletekért lásd: [nyilvános végpontú kapcsolat Virtual Machines az Azure standard Load Balancer használata az SAP magas rendelkezésre állási helyzetekben](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

> [!IMPORTANT]
> Ne engedélyezze a TCP-időbélyegeket a Azure Load Balancer mögött elhelyezett Azure-beli virtuális gépeken. A TCP-időbélyegek engedélyezése az állapot-mintavételek meghibásodását eredményezi. Állítsa a paramétert a **0** értékre **net.IPv4.tcp_timestamps** . Részletekért lásd: [Load Balancer Health](../../../load-balancer/load-balancer-custom-probe-overview.md)-tesztek.

### <a name="create-pacemaker-cluster"></a>Pacemaker-fürt létrehozása

Kövesse az Azure-beli [SUSE Linux Enterprise Server a pacemaker beállítása az Azure-ban](high-availability-guide-suse-pacemaker.md) című témakör lépéseit az NFS-kiszolgáló alapszintű pacemaker-fürtjének létrehozásához.

### <a name="configure-nfs-server"></a>Az NFS-kiszolgáló konfigurálása

A következő elemek a **[a]** előtaggal vannak ellátva, amelyek az összes csomópontra érvényesek, **[1]** – csak az 1. vagy **[2]** csomópontra érvényesek, csak a 2. csomópontra.

1. **[A]** telepítési állomásnév feloldása

   Használhat DNS-kiszolgálót, vagy módosíthatja a/etc/hosts az összes csomóponton. Ez a példa a/etc/hosts fájl használatát mutatja be.
   Cserélje le az IP-címet és a gazdagépet a következő parancsokra

   <pre><code>sudo vi /etc/hosts
   </code></pre>
   
   Szúrja be a következő sorokat a/etc/hosts. Az IP-cím és az állomásnév módosítása a környezetnek megfelelően
   
   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   <b>10.0.0.5 nw2-nfs</b>
   </code></pre>

1. **[A]** NFS-kiszolgáló engedélyezése

   A root NFS-exportálási bejegyzés létrehozása

   <pre><code>sudo sh -c 'echo /srv/nfs/ *\(rw,no_root_squash,fsid=0\)>/etc/exports'
   
   sudo mkdir /srv/nfs/
   </code></pre>

1. **[A]** DRBD-összetevők telepítése

   <pre><code>sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]** partíció létrehozása a DRBD-eszközökhöz

   Az összes elérhető adatlemez listázása

   <pre><code>sudo ls /dev/disk/azure/scsi1/
   </code></pre>

   Példa kimenetre
   
   ```
   lun0  lun1
   ```

   Partíciók létrehozása minden adatlemezhez

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun1'
   </code></pre>

1. **[A]** LVM-konfigurációk létrehozása

   Az összes rendelkezésre álló partíció listázása

   <pre><code>ls /dev/disk/azure/scsi1/lun*-part*
   </code></pre>

   Példa kimenetre
   
   ```
   /dev/disk/azure/scsi1/lun0-part1  /dev/disk/azure/scsi1/lun1-part1
   ```

   LVM-kötetek létrehozása minden partícióhoz

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0-part1  
   sudo vgcreate vg-<b>NW1</b>-NFS /dev/disk/azure/scsi1/lun0-part1
   sudo lvcreate -l 100%FREE -n <b>NW1</b> vg-<b>NW1</b>-NFS

   sudo pvcreate /dev/disk/azure/scsi1/lun1-part1
   sudo vgcreate vg-<b>NW2</b>-NFS /dev/disk/azure/scsi1/lun1-part1
   sudo lvcreate -l 100%FREE -n <b>NW2</b> vg-<b>NW2</b>-NFS
   </code></pre>

1. **[A]** DRBD konfigurálása

   <pre><code>sudo vi /etc/drbd.conf
   </code></pre>

   Győződjön meg arról, hogy a DRBD. conf fájl a következő két sort tartalmazza

   <pre><code>include "drbd.d/global_common.conf";
   include "drbd.d/*.res";
   </code></pre>

   A globális DRBD-konfiguráció módosítása

   <pre><code>sudo vi /etc/drbd.d/global_common.conf
   </code></pre>

   Adja hozzá a következő bejegyzéseket a kezelő és a net szakaszhoz.

   <pre><code>global {
        usage-count no;
   }
   common {
        handlers {
             fence-peer "/usr/lib/drbd/crm-fence-peer.sh";
             after-resync-target "/usr/lib/drbd/crm-unfence-peer.sh";
             split-brain "/usr/lib/drbd/notify-split-brain.sh root";
             pri-lost-after-sb "/usr/lib/drbd/notify-pri-lost-after-sb.sh; /usr/lib/drbd/notify-emergency-reboot.sh; echo b > /proc/sysrq-trigger ; reboot -f";
        }
        startup {
             wfc-timeout 0;
        }
        options {
        }
        disk {
             md-flushes yes;
             disk-flushes yes;
             c-plan-ahead 1;
             c-min-rate 100M;
             c-fill-target 20M;
             c-max-rate 4G;
        }
        net {
             after-sb-0pri discard-younger-primary;
             after-sb-1pri discard-secondary;
             after-sb-2pri call-pri-lost-after-sb;
             protocol     C;
             tcp-cork yes;
             max-buffers 20000;
             max-epoch-size 20000;
             sndbuf-size 0;
             rcvbuf-size 0;
        }
   }
   </code></pre>

1. **[A]** NFS DRBD-eszközök létrehozása

   <pre><code>sudo vi /etc/drbd.d/<b>NW1</b>-nfs.res
   </code></pre>

   Helyezze be az új DRBD-eszköz konfigurációját, és lépjen ki

   <pre><code>resource <b>NW1</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   <pre><code>sudo vi /etc/drbd.d/<b>NW2</b>-nfs.res
   </code></pre>

   Helyezze be az új DRBD-eszköz konfigurációját, és lépjen ki

   <pre><code>resource <b>NW2</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   Hozza létre a DRBD eszközt, és indítsa el

   <pre><code>sudo drbdadm create-md <b>NW1</b>-nfs
   sudo drbdadm create-md <b>NW2</b>-nfs
   sudo drbdadm up <b>NW1</b>-nfs
   sudo drbdadm up <b>NW2</b>-nfs
   </code></pre>

1. **[1]** a kezdeti szinkronizálás kihagyása

   <pre><code>sudo drbdadm new-current-uuid --clear-bitmap <b>NW1</b>-nfs
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW2</b>-nfs
   </code></pre>

1. **[1]** az elsődleges csomópont beállítása

   <pre><code>sudo drbdadm primary --force <b>NW1</b>-nfs
   sudo drbdadm primary --force <b>NW2</b>-nfs
   </code></pre>

1. **[1]** várjon, amíg az új DRBD-eszközök nincsenek szinkronizálva

   <pre><code>sudo drbdsetup wait-sync-resource NW1-nfs
   sudo drbdsetup wait-sync-resource NW2-nfs
   </code></pre>

1. **[1]** fájlrendszerek létrehozása a DRBD-eszközökön

   <pre><code>sudo mkfs.xfs /dev/drbd0
   sudo mkdir /srv/nfs/NW1
   sudo chattr +i /srv/nfs/NW1
   sudo mount -t xfs /dev/drbd0 /srv/nfs/NW1
   sudo mkdir /srv/nfs/NW1/sidsys
   sudo mkdir /srv/nfs/NW1/sapmntsid
   sudo mkdir /srv/nfs/NW1/trans
   sudo mkdir /srv/nfs/NW1/ASCS
   sudo mkdir /srv/nfs/NW1/ASCSERS
   sudo mkdir /srv/nfs/NW1/SCS
   sudo mkdir /srv/nfs/NW1/SCSERS
   sudo umount /srv/nfs/NW1

   sudo mkfs.xfs /dev/drbd1
   sudo mkdir /srv/nfs/NW2
   sudo chattr +i /srv/nfs/NW2
   sudo mount -t xfs /dev/drbd1 /srv/nfs/NW2
   sudo mkdir /srv/nfs/NW2/sidsys
   sudo mkdir /srv/nfs/NW2/sapmntsid
   sudo mkdir /srv/nfs/NW2/trans
   sudo mkdir /srv/nfs/NW2/ASCS
   sudo mkdir /srv/nfs/NW2/ASCSERS
   sudo mkdir /srv/nfs/NW2/SCS
   sudo mkdir /srv/nfs/NW2/SCSERS
   sudo umount /srv/nfs/NW2
   </code></pre>

1. **[A]** beállítás DRBD – az agy észlelése

   Ha a DRBD használatával szinkronizálja az adatokat az egyik gazdagépről a másikra, az úgynevezett felosztott agy is megjelenhet. A felosztott agy olyan forgatókönyv, amelyben mindkét fürtcsomópont előléptette a DRBD-eszközt elsődlegesnek, és nem volt szinkronban. Lehet, hogy ritka helyzetben van, de továbbra is a lehető leggyorsabban szeretné kezelni és megoldani a felosztott agyat. Ezért fontos, hogy értesítést kapjon, ha egy megosztott agy történt.

   A Split Brain-értesítések beállításával kapcsolatban olvassa el [a hivatalos DRBD dokumentációját](https://www.linbit.com/drbd-user-guide/users-guide-drbd-8-4/#s-split-brain-notification) .

   Az is lehetséges, hogy automatikusan helyreállítható egy megosztott agyi forgatókönyvből. További információért olvassa el az [automatikus felosztott agyi helyreállítási szabályzatok](https://www.linbit.com/drbd-user-guide/users-guide-drbd-8-4/#s-automatic-split-brain-recovery-configuration) című témakört.
   
### <a name="configure-cluster-framework"></a>A fürt keretrendszerének konfigurálása

1. **[1]** adja hozzá az NFS-DRBD eszközöket az SAP rendszer NW1 a fürt konfigurációjához

   > [!IMPORTANT]
   > A közelmúltbeli tesztelés feltárta a helyzeteket, ahol a netcat nem válaszol a várakozó kérelmekre, és csak egyetlen kapcsolat kezelésére vonatkozó korlátozásokat okoz. A netcat erőforrás nem figyeli az Azure Load Balancer kéréseit, és a lebegőpontos IP-cím elérhetetlenné válik.  
   > A meglévő pacemaker-fürtök esetében a netcat és a socat helyett a múltban javasolt. Jelenleg a csomag erőforrás-ügynökök részét képező Azure-LB erőforrás-ügynök használatát javasoljuk a következő csomag-verzióra vonatkozó követelményekkel:
   > - A SLES 12 SP4/SP5 esetében a verziónak legalább Resource-Agent-4.3.018. a7fb5035-3.30.1 kell lennie.  
   > - A SLES 15/15 SP1 esetében a verziónak legalább Resource-Agent-4.3.0184.6 ee15eb2-4.13.1 kell lennie.  
   >
   > Vegye figyelembe, hogy a módosítás rövid állásidőt igényel.  
   > Meglévő pacemaker-fürtök esetén, ha a konfigurációt már úgy módosították, hogy a socat használja az [azure Load-Balancer észlelésének megerősítése](https://www.suse.com/support/kb/doc/?id=7024128)című cikkben leírtak szerint, nem kell azonnal váltania az Azure-LB erőforrás-ügynökre.

   <pre><code>sudo crm configure rsc_defaults resource-stickiness="200"

   # Enable maintenance mode
   sudo crm configure property maintenance-mode=true
   
   sudo crm configure primitive drbd_<b>NW1</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW1</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW1</b>_nfs drbd_<b>NW1</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW1</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd0 \
     directory=/srv/nfs/<b>NW1</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive nfsserver systemd:nfs-server \
     op monitor interval="30s"
   sudo crm configure clone cl-nfsserver nfsserver

   sudo crm configure primitive exportfs_<b>NW1</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>" \
     options="rw,no_root_squash,crossmnt" clientspec="*" fsid=1 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW1</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.4</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_nfs azure-lb port=<b>61000</b>
   
   sudo crm configure group g-<b>NW1</b>_nfs \
     fs_<b>NW1</b>_sapmnt exportfs_<b>NW1</b> nc_<b>NW1</b>_nfs vip_<b>NW1</b>_nfs
   
   sudo crm configure order o-<b>NW1</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW1</b>_nfs:promote g-<b>NW1</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW1</b>_nfs_on_drbd inf: \
     g-<b>NW1</b>_nfs ms-drbd_<b>NW1</b>_nfs:Master
   </code></pre>

1. **[1]** adja hozzá az NFS-DRBD eszközöket az SAP rendszer NW2 a fürt konfigurációjához

   <pre><code># Enable maintenance mode
   sudo crm configure property maintenance-mode=true
   
   sudo crm configure primitive drbd_<b>NW2</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW2</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW2</b>_nfs drbd_<b>NW2</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW2</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd1 \
     directory=/srv/nfs/<b>NW2</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive exportfs_<b>NW2</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>" \
     options="rw,no_root_squash,crossmnt" clientspec="*" fsid=2 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW2</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.5</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW2</b>_nfs azure-lb port=<b>61001</b>
   
   sudo crm configure group g-<b>NW2</b>_nfs \
     fs_<b>NW2</b>_sapmnt exportfs_<b>NW2</b> nc_<b>NW2</b>_nfs vip_<b>NW2</b>_nfs
   
   sudo crm configure order o-<b>NW2</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW2</b>_nfs:promote g-<b>NW2</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW2</b>_nfs_on_drbd inf: \
     g-<b>NW2</b>_nfs ms-drbd_<b>NW2</b>_nfs:Master
   </code></pre>

   A `crossmnt` `exportfs` fürt erőforrásaiban található lehetőség a régebbi SLES-verziókkal való visszamenőleges kompatibilitás érdekében szerepel a dokumentációban.  

1. **[1]** a karbantartási mód letiltása
   
   <pre><code>sudo crm configure property maintenance-mode=false
   </code></pre>

## <a name="next-steps"></a>Következő lépések

* [Az SAP-ASCS és-adatbázis telepítése](high-availability-guide-suse.md)
* [Azure Virtual Machines az SAP tervezéséhez és megvalósításához][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP-ban][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hoz][dbms-guide]
* A magas rendelkezésre állás és a SAP HANA Azure-beli virtuális gépeken történő vész-helyreállítási tervének megismeréséhez lásd: [Az Azure-beli SAP HANA magas rendelkezésre állása Virtual Machines (VM)][sap-hana-ha]

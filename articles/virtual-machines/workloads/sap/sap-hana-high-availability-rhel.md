---
title: Magas rendelkezésre állású SAP HANA RHEL-alapú Azure-beli virtuális gépeken | Microsoft Docs
description: Magas rendelkezésre állású virtuális SAP HANA Azure-beli virtuális gépeken.
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/12/2021
ms.author: radeltch
ms.openlocfilehash: 3d1b05560c02f3bf4de199a3d5cad48907ee16fb
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365807"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-red-hat-enterprise-linux"></a>Magas rendelkezésre állású SAP HANA azure-beli virtuális gépeken a Red Hat Enterprise Linux

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
[2388694]:https://launchpad.support.sap.com/#/notes/2388694
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json

Helyszíni fejlesztéshez használhatja a HANA rendszerreplikációt, vagy megosztott tárolót a magas rendelkezésre állás létrehozására a SAP HANA.
Az Azure-beli virtuális gépeken (VM)a HANA Azure-beli rendszerreplikációja jelenleg az egyetlen támogatott magas rendelkezésre állású funkció.
SAP HANA Replikáció egy elsődleges csomópontból és legalább egy másodlagos csomópontból áll. Az elsődleges csomóponton végzett adatváltozások szinkron vagy aszinkron módon replikálódnak a másodlagos csomópontra.

Ez a cikk bemutatja, hogyan telepítheti és konfigurálhatja a virtuális gépeket, hogyan telepítheti a fürt keretrendszerét, és hogyan telepítheti és konfigurálhatja SAP HANA rendszerreplikációt.
A példakonfigurációkban telepítési parancsokat, **03-as** példányszámot és HANA **HN1** rendszerazonosítót használ a rendszer.

Először olvassa el a következő SAP-megjegyzéseket és -dokumentumokat:

* SAP Note [1928533], amely a következővel rendelkezik:
  * Az SAP-szoftverek üzembe helyezéséhez támogatott Azure-beli virtuálisgép-méretek listája.
  * Az Azure-beli virtuális gépek méretének fontos kapacitásinformációi.
  * A támogatott SAP-szoftverek, valamint az operációs rendszer (OS) és adatbázis-kombinációk.
  * A windowsos és linuxos rendszerekhez szükséges SAP kernelverzió Microsoft Azure.
* Az SAP Note [2015553 az] SAP által támogatott SAP-szoftverek Azure-beli üzembe helyezésének előfeltételeit sorolja fel.
* Az SAP Note [2002167 ajánlott] operációsrendszer-beállításokat Red Hat Enterprise Linux
* Az SAP Note [2009879] SAP HANA irányelvek a Red Hat Enterprise Linux
* Az SAP [Note 2178632] részletes információkat tartalmaz az Azure-beli SAP-hez jelentett összes monitorozási metrikákról.
* Az SAP Note [2191498 rendelkezik] a Linuxhoz szükséges SAP Host Agent-verzióval az Azure-ban.
* Az SAP [Note 2243692 az] Azure-beli Linuxon való SAP-licencelésről tartalmaz információkat.
* Az SAP Note [1999351] további hibaelhárítási információkat tartalmaz az SAP Azure Továbbfejlesztett monitorozási bővítményével kapcsolatban.
* [Az SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) a Linuxhoz szükséges SAP-megjegyzésekkel rendelkezik.
* [Azure Virtual Machines SAP tervezése és implementációja Linux rendszeren][planning-guide]
* [Azure Virtual Machines sap on Linux rendszeren való üzembe helyezése (ez a cikk)][deployment-guide]
* [Azure Virtual Machines DBMS üzembe helyezése Linuxon az SAP-hez][dbms-guide]
* [SAP HANA rendszerreplikáció a pacemaker-fürtben](https://access.redhat.com/articles/3004101)
* Általános RHEL-dokumentáció
  * [Magas rendelkezésre állású Add-On áttekintése](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Magas rendelkezésre állású Add-On felügyelet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Magas rendelkezésre állású Add-On referencia](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Azure-specifikus RHEL-dokumentáció:
  * [Az RHEL magas rendelkezésre állású fürtökre vonatkozó támogatási szabályzatok – Microsoft Azure Virtual Machines fürttagok](https://access.redhat.com/articles/3131341)
  * [7.4-es (Red Hat Enterprise Linux és újabb) High-Availability fürt telepítése és konfigurálása a Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [A SAP HANA telepítése a Red Hat Enterprise Linux való használatra a Microsoft Azure](https://access.redhat.com/solutions/3193782)

## <a name="overview"></a>Áttekintés

A magas rendelkezésre állás érdekében a SAP HANA két virtuális gépen van telepítve. Az adatok replikálása a HANA rendszerreplikációval történt.

![SAP HANA magas rendelkezésre állás áttekintése](./media/sap-hana-high-availability-rhel/ha-hana.png)

SAP HANA rendszerreplikáció beállítása dedikált virtuális állomásnevet és virtuális IP-címeket használ. Az Azure-ban terheléselosztás szükséges egy virtuális IP-cím használatához. Az alábbi lista a terheléselosztás konfigurációját mutatja be:

* Előoldali konfiguráció: hn1-db IP-címe: 10.0.0.13
* Háttér-konfiguráció: A HANA rendszerreplikáció részét képezi összes virtuális gép elsődleges hálózati adapteréhez csatlakoztatva
* Mintavételi port: 62503-as port
* Terheléselosztási szabályok: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP

## <a name="deploy-for-linux"></a>Üzembe helyezés Linux rendszeren

A Azure Marketplace tartalmazza az Red Hat Enterprise Linux 7.4-es SAP HANA rendszerképét, amely új virtuális gépek üzembe helyezésére használható.

### <a name="deploy-with-a-template"></a>Üzembe helyezés sablon használatával

A GitHubon található gyorsindítási sablonok egyikének használatával üzembe helyezheti az összes szükséges erőforrást. A sablon telepíti a virtuális gépeket, a terheléselosztást, a rendelkezésre állási készletet stb.
A sablon üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg [az adatbázissablont][template-multisid-db] a Azure Portal.
1. Adja meg a következő paramétereket:
    * **Sap-rendszerazonosító:** Adja meg a telepíteni kívánt SAP-rendszer SAP-rendszerazonosítóját. Az azonosító az üzembe helyezett erőforrások előtagja.
    * **Operációs rendszer típusa:** Válassza ki az egyik Linux-disztribúciót. Ebben a példában válassza az **RHEL 7 lehetőséget.**
    * **Adatbázis típusa:** Válassza a **HANA lehetőséget.**
    * **Sap-rendszer mérete:** Adja meg, hogy az új rendszer hány SAPS-t fog megadni. Ha nem biztos abban, hogy a rendszernek hány SAPS-re van szüksége, kérdezze meg az SAP-technológiai partnert vagy a rendszerintegrátort.
    * **Rendszer rendelkezésre állása:** Válassza a **Magas rendelkezésre állás lehetőséget.**
    * **Rendszergazdai felhasználónév, rendszergazdai jelszó** vagy SSH-kulcs: Új felhasználó jön létre, amely a gépre való bejelentkezéshez használható.
    * **Alhálózat azonosítója:** Ha a virtuális gépet egy olyan meglévő virtuális hálózatban szeretné üzembe helyezni, ahol meghatározta az alhálózatot, amelyhez a virtuális gépet hozzá kell rendelni, nevezze el az adott alhálózat azonosítóját. Az azonosító általában a következő: **/subscriptions/ \<subscription ID> /resourceGroups/ \<resource group name> /providers/Microsoft.Network/virtualNetworks/ \<virtual network name> /subnets/ \<subnet name>**. Hagyja üresen, ha új virtuális hálózatot szeretne létrehozni

### <a name="manual-deployment"></a>Manuális üzembe helyezés

1. Hozzon létre egy erőforráscsoportot.
1. Hozzon létre egy virtuális hálózatot.
1. Rendelkezésre állási csoport létrehozása.  
   Állítsa be a maximális frissítési tartományt.
1. Terheléselosztás létrehozása (belső). A standard [terheléselosztást javasoljuk.](../../../load-balancer/load-balancer-overview.md)
   * Válassza ki a 2. lépésben létrehozott virtuális hálózatot.
1. Hozza létre az 1. virtuális gépet.  
   Használja a Red Hat Enterprise Linux 7.4-es SAP HANA. Ez a példa a Red Hat Enterprise Linux 7.4-es SAP HANA a 3. lépésben létrehozott rendelkezésre állási készlet <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> kiválasztása.
1. Hozza létre a 2. virtuális gépet.  
   Használja a Red Hat Enterprise Linux 7.4-es vagy SAP HANA. Ez a példa a Red Hat Enterprise Linux 7.4-es SAP HANA rendszerképhez Használja A 3. lépésben létrehozott rendelkezésre állási készlet <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> kiválasztása lehetőséget.
1. Adjon hozzá adatlemezeket.

> [!IMPORTANT]
> A nem rögzített IP-cím terheléselosztási forgatókönyvekben nem támogatott a hálózati adapterek másodlagos IP-konfigurációiban. Részletekért lásd: [Az Azure Load Balancer korlátozásai.](../../../load-balancer/load-balancer-multivip-overview.md#limitations) Ha további IP-címre van szüksége a virtuális géphez, telepítsen egy második hálózati adaptert.    

> [!Note]
> Ha a nyilvános IP-címmel nem rendelkezik virtuális gépek a standard Azure Load Balancer belső (nyilvános IP-cím nélküli) háttérkészletében vannak elhelyezve, nem lesz kimenő internetkapcsolat, kivéve, ha további konfigurációt végez a nyilvános végpontok felé irányuló útválasztás érdekében. A kimenő kapcsolatok elérésével kapcsolatos részletekért lásd: Nyilvános végponti kapcsolat Virtual Machines Azure standard Load Balancer sap magas rendelkezésre állású [forgatókönyvekben.](./high-availability-guide-standard-load-balancer-outbound-connections.md)  

1. Ha standard terheléselosztást használ, kövesse az alábbi konfigurációs lépéseket:
   1. Először hozzon létre egy előoldali IP-címkészletet:

      1. Nyissa meg a terheléselosztást, válassza ki **az előtere IP-címkészletet,** majd válassza a **Hozzáadás lehetőséget.**
      1. Adja meg az új előtere IP-címkészlet nevét (például **hana-frontend).**
      1. Állítsa a **Hozzárendelést** **Statikusra,** és adja meg az IP-címet (például **10.0.0.13).**
      1. Válassza az **OK** lehetőséget.
      1. Az új előoldali IP-címkészlet létrehozása után jegyezze fel a készlet IP-címét.

   1. Ezután hozzon létre egy háttérkészletet:

      1. Nyissa meg a terheléselosztást, válassza ki **a háttérkészleteket,** majd válassza a **Hozzáadás lehetőséget.**
      1. Adja meg az új háttérkészlet nevét (például **hana-backend).**
      1. Válassza **a Virtuális gép hozzáadása lehetőséget.**
      1. Válassza a ** Virtuális gép** lehetőséget.
      1. Válassza ki az SAP HANA virtuális gépeit és AZOK IP-címét.
      1. Válassza a **Hozzáadás** lehetőséget.

   1. Ezután hozzon létre egy állapot-mintavételt:

      1. Nyissa meg a terheléselosztást, válassza **ki az állapot-mintavételeket,** majd válassza a **Hozzáadás lehetőséget.**
      1. Adja meg az új állapot-mintavétel nevét (például **hana-hp).**
      1. Válassza **a TCP** protokollt és a 625 **03-as portot.** Az **Intervallum értékét** állítsa 5-re, a Nem megfelelő értékek küszöbértékét pedig 2-re. 
      1. Válassza az **OK** lehetőséget.

   1. Ezután hozza létre a terheléselosztási szabályokat:
   
      1. Nyissa meg a terheléselosztást, válassza ki **a terheléselosztási szabályokat,** majd válassza a **Hozzáadás lehetőséget.**
      1. Adja meg az új terheléselosztási szabály nevét (például **hana-lb).**
      1. Válassza ki az előtere IP-címét, a háttérkészletet és a korábban létrehozott állapot-mintavételt (például **hana-frontend,** **hana-backend** és **hana-hp).**
      1. Válassza **a HA portok lehetőséget.**
      1. Növelje az **üresjárati időkorlátot** 30 percre.
      1. Ügyeljen arra, hogy **engedélyezze a nem rögzített IP-címet.**
      1. Válassza az **OK** lehetőséget.


1. Ha a forgatókönyv előírja az alapszintű terheléselosztást, kövesse az alábbi konfigurációs lépéseket:
   1. Konfigurálja a terheléselosztást. Először hozzon létre egy előoldali IP-címkészletet:

      1. Nyissa meg a terheléselosztást, válassza ki **az előtere IP-készletet,** majd válassza a **Hozzáadás lehetőséget.**
      1. Adja meg az új előtere IP-címkészlet nevét (például **hana-frontend).**
      1. Állítsa a **Hozzárendelést** **Statikusra,** és adja meg az IP-címet (például **10.0.0.13).**
      1. Válassza az **OK** lehetőséget.
      1. Az új előoldali IP-címkészlet létrehozása után jegyezze fel a készlet IP-címét.

   1. Ezután hozzon létre egy háttérkészletet:

      1. Nyissa meg a terheléselosztást, válassza ki a **háttérkészleteket,** majd válassza a **Hozzáadás lehetőséget.**
      1. Adja meg az új háttérkészlet nevét (például **hana-backend).**
      1. Válassza **a Virtuális gép hozzáadása lehetőséget.**
      1. Válassza ki a 3. lépésben létrehozott rendelkezésre állási készletet.
      1. Válassza ki az SAP HANA virtuális gépeit.
      1. Válassza az **OK** lehetőséget.

   1. Ezután hozzon létre egy állapot-mintavételt:

      1. Nyissa meg a terheléselosztást, válassza **az Állapot-mintavételek,** majd a Hozzáadás **lehetőséget.**
      1. Adja meg az új állapot-mintavétel nevét (például **hana-hp).**
      1. Válassza **a TCP** protokollt és a 625 **03-as portot.** Az **Intervallum értéke** maradjon 5, a Nem megfelelő érték küszöbértéke pedig 2. 
      1. Válassza az **OK** lehetőséget.

   1. Az SAP HANA 1.0 esetén hozza létre a terheléselosztási szabályokat:

      1. Nyissa meg a terheléselosztást, válassza ki **a terheléselosztási szabályokat,** majd válassza a **Hozzáadás lehetőséget.**
      1. Adja meg az új terheléselosztási szabály nevét (például hana-lb-3 **03** 15).
      1. Válassza ki az előtere IP-címét, a háttérkészletet és a korábban létrehozott állapot-mintavételt (például **hana-frontend).**
      1. A Protokoll **beállításnál** tartsa meg a **TCP-t,** és adja meg a 3 **03** 15-ös portot.
      1. Növelje az **üresjárati időkorlátot** 30 percre.
      1. Ügyeljen arra, hogy **engedélyezze a nem rögzített IP-címet.**
      1. Válassza az **OK** lehetőséget.
      1. Ismételje meg ezeket a lépéseket a **303** 17-es porton.

   1. A SAP HANA 2.0 esetén hozza létre a terheléselosztási szabályokat a rendszeradatbázishoz:

      1. Nyissa meg a terheléselosztást, válassza ki **a terheléselosztási szabályokat,** majd válassza a **Hozzáadás lehetőséget.**
      1. Adja meg az új terheléselosztási szabály nevét (például hana-lb-3 **03** 13).
      1. Válassza ki az előtere IP-címét, a háttérkészletet és a korábban létrehozott állapot-mintavételt (például **hana-frontend).**
      1. A Protokoll **beállításnál** tartsa meg a **TCP-t,** és adja meg a **3 03** 13-as portot.
      1. Növelje az **üresjárati időkorlátot** 30 percre.
      1. Ügyeljen arra, hogy **engedélyezze a nem rögzített IP-címet.**
      1. Válassza az **OK** lehetőséget.
      1. Ismételje meg ezeket a lépéseket a **303** 14-es porton.

   1. A SAP HANA 2.0 esetén először hozza létre a terheléselosztási szabályokat a bérlői adatbázishoz:

      1. Nyissa meg a terheléselosztást, válassza a **terheléselosztási szabályok lehetőséget,** majd kattintson a **Hozzáadás gombra.**
      1. Adja meg az új terheléselosztási szabály nevét (például hana-lb-3 **03** 40).
      1. Válassza ki a korábban létrehozott előtere IP-címet, háttérkészletet és állapot-mintavételt (például **hana-frontend).**
      1. A Protokoll **beállításnál tartsa** meg a **TCP-t,** és adja meg a 3 **03** 40-es portot.
      1. Növelje az **üresjárati időkorlátot** 30 percre.
      1. Ügyeljen arra, hogy **engedélyezze a nem rögzített IP-címet.**
      1. Válassza az **OK** lehetőséget.
      1. Ismételje meg ezeket a lépéseket a 3 **03** 41-es és a **303 42-es** portokon is.

Az adatbázishoz szükséges portokkal kapcsolatos további SAP HANA [](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) olvassa el a Kapcsolatok a bérlői adatbázisokhoz fejezetet a [SAP HANA Tenant Databases](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) útmutatójában vagy az [SAP 2388694][2388694]megjegyzésben.

> [!IMPORTANT]
> Ne engedélyezze a TCP-időbélyegeket az azure-beli virtuális gépeken, amelyek a Azure Load Balancer. A TCP-időbélyegek engedélyezése az állapot-mintavételek sikertelen lesz. Állítsa a **paraméter net.ipv4.tcp_timestamps** **értékét 0-ra.** Részletekért [lásd: Load Balancer állapot-mintavételek.](../../../load-balancer/load-balancer-custom-probe-overview.md)
> Lásd még az SAP [2382421 megjegyzését.](https://launchpad.support.sap.com/#/notes/2382421) 

## <a name="install-sap-hana"></a>Az SAP HANA telepítése

Az ebben a szakaszban található lépések a következő előtagokat használják:

* **[A]**: A lépés az összes csomópontra vonatkozik.
* **[1]**: A lépés csak az 1. csomópontra vonatkozik.
* **[2]**: A lépés csak a Pacemaker-fürt 2. csomópontjára vonatkozik.

1. **[A]** A lemezelrendezés beállítása: **Logikaikötet-kezelő (LVM).**

   Javasoljuk, hogy LVM-et használjon az adatokat és naplófájlokat tároló kötetek számára. Az alábbi példa feltételezi, hogy a virtuális gépekhez négy csatlakoztatott adatlemez tartozik, amelyek két kötet létrehozására használhatók.

   List all the available disks :List all the available disks:

   <pre><code>ls /dev/disk/azure/scsi1/lun*
   </code></pre>

   Példa a kimenetre:

   <pre><code>
   /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
   </code></pre>

   Hozzon létre fizikai köteteket az összes használni kívánt lemezhez:

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0
   sudo pvcreate /dev/disk/azure/scsi1/lun1
   sudo pvcreate /dev/disk/azure/scsi1/lun2
   sudo pvcreate /dev/disk/azure/scsi1/lun3
   </code></pre>

   Hozzon létre egy kötetcsoportot az adatfájlokhoz. Használjon egy kötetcsoportot a naplófájlok és egy kötetcsoportot a SAP HANA:

   <pre><code>sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
   sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
   sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
   </code></pre>

   Hozza létre a logikai köteteket. A lineáris kötet akkor jön létre, ha a kapcsoló `lvcreate` nélkül `-i` használja. Javasoljuk, hogy hozzon létre egy csíkozott kötetet a jobb I/O-teljesítmény érdekében, és igazítsa a csíkméreteket a virtuálisgép SAP HANA konfigurációkban dokumentált [értékekhez.](./hana-vm-operations-storage.md) Az argumentumnak a mögöttes fizikai kötetek számának, az argumentumnak pedig a `-i` `-I` csíkméretnek kell lennie. Ebben a dokumentumban két fizikai kötetet használ az adatkötethez, ezért a switch argumentum `-i` **beállítása 2.** Az adatkötet csíkmérete **256KiB.** A rendszer egy fizikai kötetet használ a naplókötethez, ezért a naplókötet parancsaihoz explicit módon nem vagy `-i` `-I` kapcsolókat használ a rendszer.  

   > [!IMPORTANT]
   > Használja a kapcsolót, és állítsa a mögöttes fizikai kötet számára, ha több fizikai kötetet használ minden adathoz, naplóhoz vagy `-i` megosztott kötethez. Csíkozott kötet létrehozásakor a kapcsolóval adhatja meg `-I` a csíkméretet.  
   > Az [SAP HANA konfigurációkért,](./hana-vm-operations-storage.md) beleértve a csíkméreteket és a lemezek számát, tekintse meg a virtuális gépek tárolókonfigurációit.  

   <pre><code>sudo lvcreate <b>-i 2</b> <b>-I 256</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
   sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
   sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   </code></pre>

   Hozza létre a csatlakoztatási könyvtárakat, és másolja az összes logikai kötet UUID-ját:

   <pre><code>sudo mkdir -p /hana/data/<b>HN1</b>
   sudo mkdir -p /hana/log/<b>HN1</b>
   sudo mkdir -p /hana/shared/<b>HN1</b>
   # Write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log, and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   sudo blkid
   </code></pre>

   Hozzon `fstab` létre bejegyzéseket a három logikai kötethez:

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   Szúrja be a következő sort a `/etc/fstab` fájlba:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   Csatlakoztassa az új köteteket:

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** A lemezelrendezés beállítása: **Egyszerű lemezek.**

   Bemutató rendszerek esetén a HANA-adatokat és a naplófájlokat egyetlen lemezre is helyezze. Hozzon létre egy partíciót a /dev/disk/azure/scsi1/lun0 oldalon, és formázza xfs formátumban:

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   Szúrja be ezt a sort az /etc/fstab fájlba:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   Hozza létre a célkönyvtárat, és csatlakoztassa a lemezt:

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[A]** Állítsa be az összes gazdagép gazdagépnév-feloldási beállítását.

   Használhat DNS-kiszolgálót, vagy módosíthatja az /etc/hosts fájlt az összes csomóponton. Ez a példa bemutatja, hogyan használhatja az /etc/hosts fájlt.
   Cserélje le az IP-címet és az állomásnevet a következő parancsokban:

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Szúrja be a következő sorokat az /etc/hosts fájlba. Módosítsa az IP-címet és az állomásnevet úgy, hogy megfeleljen a környezetének:

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** RHEL a HANA-konfigurációhoz

   Konfigurálja az RHEL-t a következő <https://access.redhat.com/solutions/2447641> SAP-megjegyzésekben és a (2) által leírt módon:  
   - [2292690 – SAP HANA DB: Az RHEL 7 ajánlott operációsrendszer-beállításai](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782 – SAP HANA DB: Az RHEL 8 ajánlott operációsrendszer-beállításai](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582 – Linux: GCC 6.x-sel lefordított SAP-alkalmazások futtatása](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824 – Linux: GCC 7.x-sel lefordított SAP-alkalmazások futtatása](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607 – Linux: GCC 9.x-sel lefordított SAP-alkalmazások futtatása](https://launchpad.support.sap.com/#/notes/2886607)

1. **[A]** A SAP HANA

   A rendszerreplikáció SAP HANA telepítéséhez kövesse a <https://access.redhat.com/articles/3004101> következőt: .

   * Futtassa **a hdblcm** programot a HANA DVD-ről. Írja be a következő értékeket a parancssorba:
   * Telepítés kiválasztása: Adja meg **az 1-et.**
   * Válassza ki a telepítéshez szükséges további összetevőket: Adja meg **az 1-et.**
   * Adja meg a telepítési útvonalat [/hana/shared]: Válassza az Enter billentyűt.
   * Adja meg a Helyi állomásnév [..]: Válassza az Enter billentyűt.
   * Szeretne további gazdagépeket hozzáadni a rendszerhez? (y/n) [n]: Válassza az Enter billentyűt.
   * Adja SAP HANA rendszerazonosítót: Adja meg a HANA biztonsági azonosítóját, például: **HN1**.
   * Adja meg a Példányszám [00]: Adja meg a HANA-példány számát. Ha **az Azure-sablont** használta, vagy ha követte a cikk manuális üzembe helyezési szakaszát, adja meg a 03-ast.
   * Válassza az Adatbázis mód / Adja meg az Index [1] lehetőséget: Válassza az Enter billentyűt.
   * Válassza a Rendszerhasználat / Adja meg az Index [4] lehetőséget: Válassza ki a rendszerhasználati értéket.
   * Adja meg az adatkötetek helyét [/hana/data/HN1]: Válassza az Enter billentyűt.
   * Adja meg a naplókötetek helyét [/hana/log/HN1]: Válassza az Enter billentyűt.
   * Korlátozza a maximális memóriafoglalást? [n]: Válassza az Enter billentyűt.
   * Adja meg a "..." gazdagép tanúsítványgazdanevét :: Válassza az Enter billentyűt.
   * Adja meg az SAP-gazdaügynök felhasználója (sapadm) jelszavát: Adja meg a gazdaügynök felhasználói jelszavát.
   * Erősítse meg az SAP-gazdaügynök felhasználója (sapadm) jelszavát: A megerősítéshez adja meg ismét a gazdaügynök felhasználói jelszavát.
   * Adja meg a rendszergazdai (hdbadm) jelszót: Adja meg a rendszergazda jelszavát.
   * Erősítse meg a rendszergazda (hdbadm) jelszavát: A megerősítéshez adja meg ismét a rendszergazda jelszavát.
   * Adja meg a rendszergazda kezdőkönyvtárát [/usr/sap/HN1/home]: Válassza az Enter billentyűt.
   * Adja meg a rendszergazdai bejelentkezési rendszerhéjat [/bin/sh]: Válassza az Enter billentyűt.
   * Adja meg a rendszergazdai felhasználói azonosítót [1001]: Válassza az Enter billentyűt.
   * Adja meg a felhasználói csoport azonosítóját (sapsys) [79]: Válassza az Enter billentyűt.
   * Adja meg az adatbázis-felhasználó (SYSTEM) jelszavát: Adja meg az adatbázis-felhasználó jelszavát.
   * Erősítse meg az adatbázis-felhasználó (SYSTEM) jelszavát: A megerősítéshez adja meg ismét az adatbázis-felhasználó jelszavát.
   * Újraindítja a rendszert a gép újraindítása után? [n]: Válassza az Enter billentyűt.
   * Folytatja? (y/n): Ellenőrizze az összefoglalást. A folytatáshoz adja meg **az y** billentyűt.

1. **[A] Frissítse** az SAP-gazdagépügynöket.

   Töltse le a legújabb SAP-gazdagépügynök archívumát az [SAP Szoftverközpontból,][sap-swcenter] és futtassa a következő parancsot az ügynök frissítéséhez. Cserélje le az archívum elérési útját, hogy a letöltött fájlra mutasson:

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

1. **[A]** Tűzfal konfigurálása

   Hozza létre az Azure Load Balancer mintavételi portjának tűzfalszabályát.

   <pre><code>sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp
   sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp --permanent
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>A SAP HANA 2.0-s rendszerreplikáció konfigurálása

Az ebben a szakaszban található lépések a következő előtagokat használják:

* **[A]**: A lépés az összes csomópontra vonatkozik.
* **[1]**: A lépés csak az 1. csomópontra vonatkozik.
* **[2]**: A lépés csak a Pacemaker-fürt 2. csomópontjára vonatkozik.

1. **[A]** Tűzfal konfigurálása

   Hozzon létre tűzfalszabályokat, amelyek engedélyezik a HANA rendszerreplikációját és az ügyfélforgalmat. A szükséges portok az összes [SAP-termék TCP/IP-portjai között vannak felsorolva.](https://help.sap.com/viewer/ports) Az alábbi parancsok csak példaként engedik a HANA 2.0 rendszerreplikációt és ügyfélforgalmat a SYSTEMDB, HN1 és NW1 adatbázisra.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40301/tcp
   sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40307/tcp
   sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40303/tcp
   sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40340/tcp
   sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30340/tcp
   sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30341/tcp
   sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30342/tcp
   </code></pre>

1. **[1]** Hozza létre a bérlői adatbázist.

   Ha 2.0-s SAP HANA MDC-t használ, hozzon létre egy bérlői adatbázist az SAP NetWeaver rendszer számára. Cserélje **le az NW1** helyére az SAP-rendszer biztonsági azonosítóját.

   Hajtsa végre <hanasid \> adm parancsként a következő parancsot:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1] A** rendszerreplikáció konfigurálása az első csomóponton:

   Az adatbázisok biztonsági mentése <hanasid \> adm:

   <pre><code>hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Másolja a rendszer PKI-fájljait a másodlagos helyre:

   <pre><code>scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT   <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY  <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   Hozza létre az elsődleges helyet:

   <pre><code>hdbnsutil -sr_enable --name=<b>SITE1</b>
   </code></pre>

1. **[2] A** rendszerreplikáció konfigurálása a második csomóponton:
    
   Regisztrálja a második csomópontot a rendszerreplikáció elkezdése érdekében. Futtassa a következő parancsot <hanasid \> adm parancsként:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   </code></pre>

1. **[1] A** replikáció állapotának ellenőrzése

   Ellenőrizze a replikáció állapotát, és várjon, amíg az összes adatbázis szinkronizálva lesz. Ha az állapot ISMERETLEN marad, ellenőrizze a tűzfal beállításait.

   <pre><code>sudo su - <b>hn1</b>adm -c "python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/systemReplicationStatus.py"
   # | Database | Host     | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
   # |          |          |       |              |           |         |           | Host      | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
   # | -------- | -------- | ----- | ------------ | --------- | ------- | --------- | --------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
   # | SYSTEMDB | <b>hn1-db-0</b> | 30301 | nameserver   |         1 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30301 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30307 | xsengine     |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30307 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>NW1</b>      | <b>hn1-db-0</b> | 30340 | indexserver  |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30340 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30303 | indexserver  |         3 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30303 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   #
   # status system replication site "2": ACTIVE
   # overall system replication status: ACTIVE
   #
   # Local System Replication State
   # ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   #
   # mode: PRIMARY
   # site id: 1
   # site name: <b>SITE1</b>
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Az SAP HANA 1.0-s rendszerreplikáció konfigurálása

A szakasz lépései a következő előtagokat használják:

* **[A]**: A lépés az összes csomópontra vonatkozik.
* **[1]**: A lépés csak az 1. csomópontra vonatkozik.
* **[2]**: A lépés csak a Pacemaker-fürt 2. csomópontjára vonatkozik.

1. **[A]** Tűzfal konfigurálása

   Hozzon létre tűzfalszabályokat a HANA-rendszerreplikáció és az ügyfélforgalom engedélyezése érdekében. A szükséges portok az összes [SAP-termék TCP/IP-portjain vannak felsorolva.](https://help.sap.com/viewer/ports) Az alábbi parancsok csak példaként engedik a HANA 2.0 rendszerreplikációt. Adaptálja a SAP HANA 1.0-s telepítéséhez.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   </code></pre>

1. **[1]** Hozza létre a szükséges felhasználókat.

   Futtassa a következő parancsot gyökérként. Mindenképpen cserélje le a félkövér sztringeket (HANA System ID **HN1** és instance number **03**) a telepített SAP HANA értékeire:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]** Hozza létre a kulcstárbejegyzést.

   Futtassa a következő parancsot gyökérként egy új kulcstárbejegyzés létrehozásához:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1] Biztonsági** mentése az adatbázisról.

   Az adatbázisok biztonsági mentése gyökérként:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Ha több-bérlős telepítést használ, a bérlői adatbázisról is biztonsági mentéset kell használnia:

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1] Rendszerreplikáció** konfigurálása az első csomóponton.

   Hozza létre az elsődleges helyet <hanasid \> adm:

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2] A** rendszerreplikáció konfigurálása a másodlagos csomóponton.

   Regisztrálja a másodlagos helyet <hanasid \> adm- ként:

   <pre><code>HDB stop
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   HDB start
   </code></pre>

## <a name="create-a-pacemaker-cluster"></a>Pacemaker-fürt létrehozása

A Pacemaker beállítása az [Azure-ban Red Hat Enterprise Linux](high-availability-guide-rhel-pacemaker.md) lépéseit követve hozzon létre egy alapszintű Pacemaker-fürtöt ehhez a HANA-kiszolgálóhoz.

## <a name="implement-the-python-system-replication-hook-saphanasr"></a>A Python rendszerreplikációs hook SAPHanaSR implementációja

Ez fontos lépés a fürtök integrációjának optimalizálásához és az észlelés javításához, amikor feladatátvételre van szükség. Erősen ajánlott az SAPHanaSR Python-hook konfigurálása.    

1. **[A]** Telepítse a HANA "rendszerreplikációs hookot". A hookot mindkét HANA DB-csomóponton telepíteni kell.           

   > [!TIP]
   > A python hook csak a HANA 2.0-hoz valósítható meg.        

   1. Készítse elő a hookot a `root` következőként: .  

    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR/srHook/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. Állítsa le a HANA-t mindkét csomóponton. Hajtsa végre <sid \> adm azonosítóként:  
   
    ```bash
    sapcontrol -nr 03 -function StopSystem
    ```

   3. Állítsa `global.ini` be az egyes fürtcsomópontokat.  
 
    ```bash
    # add to global.ini
    [ha_dr_provider_SAPHanaSR]
    provider = SAPHanaSR
    path = /hana/shared/myHooks
    execution_order = 1
    
    [trace]
    ha_dr_saphanasr = info
    ```

2. **[A]** A fürt minden fürtcsomóponton sudoers konfigurációt igényel <sid \> adm azonosítóhoz. Ebben a példában ez egy új fájl létrehozásával érhető el. Hajtsa végre a parancsokat a következőként: `root` .    
    ```bash
    cat << EOF > /etc/sudoers.d/20-saphana
    # Needed for SAPHanaSR python hook
    hn1adm ALL=(ALL) NOPASSWD: /usr/sbin/crm_attribute -n hana_hn1_site_srHook_*
    EOF
    ```

3. **[A]** Indítsa SAP HANA mindkét csomóponton. Hajtsa végre <sid \> adm azonosítóval.  

    ```bash
    sapcontrol -nr 03 -function StartSystem 
    ```

4. **[1] Ellenőrizze** a hook telepítését. Hajtsa végre <sid \> adm azonosítóként az aktív HANA rendszerreplikációs helyen.   

    ```bash
     cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*
     # Example output
     # 2021-04-12 21:36:16.911343 ha_dr_SAPHanaSR SFAIL
     # 2021-04-12 21:36:29.147808 ha_dr_SAPHanaSR SFAIL
     # 2021-04-12 21:37:04.898680 ha_dr_SAPHanaSR SOK

    ```

A hálózati rendszerreplikációs hook implementációjával kapcsolatos további SAP HANA lásd: [Az SAP HA/DR szolgáltatói hook engedélyezése.](https://access.redhat.com/articles/3004101#enable-srhook)  
 
## <a name="create-sap-hana-cluster-resources"></a>Fürterőforrások SAP HANA létrehozása

Telepítse a SAP HANA-ügynököket az **összes csomópontra.** Engedélyezzen egy, a csomagot tartalmazó adattárat. RHEL 8.x HA-kompatibilis rendszerkép használata esetén nincs szükség további adattárak engedélyezésére.  

<pre><code># Enable repository that contains SAP HANA resource agents
sudo subscription-manager repos --enable="rhel-sap-hana-for-rhel-7-server-rpms"
   
sudo yum install -y resource-agents-sap-hana
</code></pre>

Ezután hozza létre a HANA-topológiát. Futtassa a következő parancsokat az egyik Pacemaker-fürtcsomóponton:

<pre><code>sudo pcs property set maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID
sudo pcs resource create SAPHanaTopology_<b>HN1</b>_<b>03</b> SAPHanaTopology SID=<b>HN1</b> InstanceNumber=<b>03</b> \
op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600 \
clone clone-max=2 clone-node-max=1 interleave=true
</code></pre>

Ezután hozza létre a HANA-erőforrásokat.

> [!NOTE]
> Ez a cikk a *slave* kifejezésre mutató hivatkozásokat tartalmaz, azt a kifejezést, amit a Microsoft már nem használ. Amikor eltávolítjuk a kifejezést a szoftverből, eltávolítjuk a cikkből.

Ha **RHEL 7.x-re épít** fürtöt, használja a következő parancsokat:  

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.
#
sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
op start timeout=3600 op stop timeout=3600 \
op monitor interval=61 role="Slave" timeout=700 \
op monitor interval=59 role="Master" timeout=700 \
op promote timeout=3600 op demote timeout=3600 \
master notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"
sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>
sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-master symmetrical=false
sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-master 4000

sudo pcs property set maintenance-mode=false
</code></pre>

Ha **RHEL 8.x-re épít** fürtöt, használja a következő parancsokat:  

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.
#
sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
op start timeout=3600 op stop timeout=3600 \
op monitor interval=61 role="Slave" timeout=700 \
op monitor interval=59 role="Master" timeout=700 \
op promote timeout=3600 op demote timeout=3600 \
promotable meta notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"
sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>
sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-clone symmetrical=false
sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-clone 4000

sudo pcs property set maintenance-mode=false
</code></pre>

Győződjön meg arról, hogy a fürt állapota ok, és hogy az összes erőforrás elindult. Nem fontos, hogy az erőforrások melyik csomóponton futnak.

> [!NOTE]
> A fenti konfigurációban az időtúllépések csak példák, és előfordulhat, hogy az adott HANA-beállításhoz kell igazítani. Előfordulhat például, hogy növelnie kell az indítási időkorlátot, ha az adatbázis SAP HANA tovább tart.  

<pre><code>sudo pcs status

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# azure_fence     (stonith:fence_azure_arm):      Started hn1-db-0
#  Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
#      Started: [ hn1-db-0 hn1-db-1 ]
#  Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
#      Masters: [ hn1-db-0 ]
#      Slaves: [ hn1-db-1 ]
#  Resource Group: g_ip_HN1_03
#      nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
#      vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>


## <a name="configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster"></a>A HANA aktív/olvasható rendszerreplikációjának konfigurálása a Pacemaker-fürtben

Az SAP HANA 2.0 SPS 01-től kezdődően az SAP engedélyezi az aktív/olvasható beállításokat az SAP HANA-rendszerreplikációhoz, ahol az SAP HANA rendszerreplikáció másodlagos rendszerei aktívan használhatók az intenzív olvasási terhelésű számítási feladatokhoz. Az ilyen beállítás fürtökben való támogatásához egy második virtuális IP-címre van szükség, amely lehetővé teszi az ügyfelek számára, hogy hozzáférjenek a másodlagos írási SAP HANA adatbázishoz. Annak érdekében, hogy a másodlagos replikációs hely a feladatátvétel után is elérhető legyen, a fürtnek át kell mozgatnia a virtuális IP-címet az SAPHana-erőforrás másodlagos helyével.

Ez a szakasz azokat a további lépéseket ismerteti, amelyek a HANA aktív/olvasható rendszerreplikációjának kezeléséhez szükségesek egy második virtuális IP-címmel rendelkező Red Hat magas rendelkezésre állású fürtben.    

Mielőtt továbblépne, győződjön meg arról, hogy teljes mértékben konfigurálta a Red Hat magas rendelkezésre állású fürtöt SAP HANA adatbázis kezeléséhez a dokumentáció fenti szegmensében leírtak szerint.  

![SAP HANA magas rendelkezésre állás az olvasásra képes másodlagos](./media/sap-hana-high-availability/ha-hana-read-enabled-secondary.png)

### <a name="additional-setup-in-azure-load-balancer-for-activeread-enabled-setup"></a>További beállítások az Azure Load Balancerben az aktív/olvasható beállításhoz

A második virtuális IP-cím üzembe helyezésének további lépéseiként győződjön meg arról, hogy konfigurálta a Azure Load Balancer manuális üzembe helyezés [szakaszban leírtak](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#manual-deployment) szerint.

1. Standard **terheléselosztás** esetén kövesse az alábbi további lépéseket a korábbi szakaszban létrehozott terheléselosztáson.

   a. Hozzon létre egy második előoldali IP-címkészletet: 

   - Nyissa meg a terheléselosztást, válassza ki **az előtere IP-készletet,** majd válassza a **Hozzáadás lehetőséget.**
   - Adja meg a második előoldali IP-címkészlet nevét (például **hana-secondaryIP).**
   - Állítsa a **Hozzárendelést** **Statikusra,** és adja meg az IP-címet (például **10.0.0.14).**
   - Válassza az **OK** lehetőséget.
   - Az új előoldali IP-címkészlet létrehozása után jegyezze fel a készlet IP-címét.

   b. Ezután hozzon létre egy állapot-mintavételt:

   - Nyissa meg a terheléselosztást, válassza **az Állapot-mintavételek,** majd a Hozzáadás **lehetőséget.**
   - Adja meg az új állapot-mintavétel nevét (például **hana-secondaryhp).**
   - Válassza a **TCP** protokollt és a **62603-as portot.** Az **Intervallum értéke** maradjon 5, a Nem megfelelő érték küszöbértéke pedig 2. 
   - Válassza az **OK** lehetőséget.

   c. Ezután hozza létre a terheléselosztási szabályokat:

   - Nyissa meg a terheléselosztást, válassza ki **a terheléselosztási szabályokat,** majd válassza a **Hozzáadás lehetőséget.**
   - Adja meg az új terheléselosztási szabály nevét (például **hana-secondarylb).**
   - Válassza ki az előtere IP-címét, a háttérkészletet és a korábban létrehozott állapot-mintavételt (például **hana-secondaryIP,** **hana-backend** és **hana-secondaryhp).**
   - Válassza **a HA portok lehetőséget.**
   - Ügyeljen arra, hogy **engedélyezze a nem rögzített IP-címet.**
   - Válassza az **OK** lehetőséget.

### <a name="configure-hana-activeread-enabled-system-replication"></a>A HANA aktív/olvasható rendszerreplikációjának konfigurálása

A HANA rendszerreplikáció konfigurálási lépéseit a Configure [SAP HANA 2.0 System Replication (A 2.0-s rendszerreplikáció](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#configure-sap-hana-20-system-replication) konfigurálása) című szakaszban ismertetjük. Ha írásra alkalmas másodlagos forgatókönyvet helyez üzembe, amikor a második csomóponton konfigurálja a rendszerreplikációt, hajtsa végre a következő parancsot **hanasid** adm-ként:

```
sapcontrol -nr 03 -function StopWait 600 10 

hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2 --operationMode=logreplay_readaccess 
```

### <a name="adding-a-secondary-virtual-ip-address-resource-for-an-activeread-enabled-setup"></a>Másodlagos virtuális IP-cím erőforrás hozzáadása aktív/olvasható beállításhoz

A második virtuális IP-cím és a megfelelő közös helymegkötés az alábbi parancsokkal konfigurálható:

```
pcs property set maintenance-mode=true

pcs resource create secvip_HN1_03 ocf:heartbeat:IPaddr2 ip="10.40.0.16"

pcs resource create secnc_HN1_03 ocf:heartbeat:azure-lb port=62603

pcs resource group add g_secip_HN1_03 secnc_HN1_03 secvip_HN1_03

RHEL 8.x: 
pcs constraint colocation add g_secip_HN1_03 with slave SAPHana_HN1_03-clone 4000
RHEL 7.x:
pcs constraint colocation add g_secip_HN1_03 with slave SAPHana_HN1_03-master 4000

pcs property set maintenance-mode=false
```
Győződjön meg arról, hogy a fürt állapota ok, és hogy az összes erőforrás elindult. A második virtuális IP-cím a másodlagos helyen fog futni az SAPHana másodlagos erőforrással együtt.

```
sudo pcs status

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full List of Resources:
#   rsc_hdb_azr_agt     (stonith:fence_azure_arm):      Started hn1-db-0
#   Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]:
#     Started: [ hn1-db-0 hn1-db-1 ]
#   Clone Set: SAPHana_HN1_03-clone [SAPHana_HN1_03] (promotable):
#     Masters: [ hn1-db-0 ]
#     Slaves: [ hn1-db-1 ]
#   Resource Group: g_ip_HN1_03:
#     nc_HN1_03         (ocf::heartbeat:azure-lb):      Started hn1-db-0
#     vip_HN1_03        (ocf::heartbeat:IPaddr2):       Started hn1-db-0
#   Resource Group: g_secip_HN1_03:
#     secnc_HN1_03      (ocf::heartbeat:azure-lb):      Started hn1-db-1
#     secvip_HN1_03     (ocf::heartbeat:IPaddr2):       Started hn1-db-1
```

A következő szakaszban megtalálja a végrehajtható feladatátvételi tesztek jellemző készletét.

Vegye figyelembe a második virtuális IP-viselkedést, amikor írásra képes másodlagos adatbázissal konfigurált HANA-fürtöt tesztel:

1. Amikor **fürterőforrást SAPHana_HN1_HDB03** át a **hn1-db-1-re,** a második virtuális IP-cím a másik **hn1-db-0 kiszolgálóra lesz áthelyezve.** Ha a AUTOMATED_REGISTER="false" értéket konfigurálta, és a HANA rendszerreplikációja nincs automatikusan regisztrálva, akkor a második virtuális **IP-cím a hn1-db-0-n** fog futni, mivel a kiszolgáló elérhető és a fürtszolgáltatások online állapotban vannak.  

2. A kiszolgáló összeomlásának tesztelése során a második virtuális IP-erőforrás **(rsc_secip_HN1_HDB03**) és az Azure Load Balancer porterőforrása **(rsc_secnc_HN1_HDB03**) az elsődleges kiszolgálón fog futni az elsődleges virtuális IP-erőforrások mellett.  Amíg a másodlagos kiszolgáló nem áll le, az olvasásra képes HANA-adatbázishoz csatlakozó alkalmazások az elsődleges HANA-adatbázishoz fognak csatlakozni. Ez a viselkedés várható, mivel nem szeretné, ha az olvasási módú HANA-adatbázishoz csatlakozó alkalmazások elérhetetlenné válnának, amíg a másodlagos kiszolgáló nem érhető el.

3. Ha a másodlagos kiszolgáló elérhető, és a fürtszolgáltatások online állapotban vannak, a második virtuális IP- és port-erőforrások automatikusan át lesznek áthelyezve a másodlagos kiszolgálóra, még akkor is, ha a HANA rendszerreplikációja nem másodlagosként van regisztrálva. Mielőtt elindítja a fürtszolgáltatásokat a kiszolgálón, győződjön meg arról, hogy a másodlagos HANA-adatbázist olvasottként regisztrálta. A HANA-példányfürt erőforrását úgy konfigurálhatja, hogy automatikusan regisztrálja a másodlagos példányt a AUTOMATED_REGISTER=true paraméter beállításával.
   
4. A feladatátvétel és a tartalék telepítés során megszakadhat az alkalmazások meglévő kapcsolatai, amelyek a MÁSODIK virtuális IP-címet használják a HANA-adatbázishoz való csatlakozáshoz.  

## <a name="test-the-cluster-setup"></a>A fürt beállításának tesztelése

Ez a szakasz a beállítás tesztelését ismerteti. A teszt elkezdése előtt győződjön meg arról, hogy a Pacemaker nem rendelkezik sikertelen művelettel (pcs állapoton keresztül), nincsenek váratlan helykorlátozások (például egy migrálási teszt maradékai), és hogy a HANA szinkronizálási állapot, például systemReplicationStatus esetén:

<pre><code>[root@hn1-db-0 ~]# sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
</code></pre>

### <a name="test-the-migration"></a>A migrálás tesztelése

Erőforrás állapota a teszt megkezdése előtt:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

A főcsomópont SAP HANA a következő parancs végrehajtásával mirateálhatja:

<pre><code># On RHEL <b>7.x</b> 
[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-master
# On RHEL <b>8.x</b>
[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-clone --master
</code></pre>

Ha be van állítva, a parancsnak a fő csomópontot SAP HANA át a virtuális IP-címet tartalmazó csoportot a `AUTOMATED_REGISTER="false"` hn1-db-1-re.

A migrálás után a "sudo pcs status" kimenete a következő lesz:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Stopped: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

A SAP HANA hn1-db-0-s erőforrás le van állítva. Ebben az esetben konfigurálja másodlagosként a HANA-példányt a következő parancs végrehajtásával:

<pre><code>[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMod
e=sync --name=SITE1
</code></pre>

A migrálás helykorlátozásokat hoz létre, amelyek újra törölhetők:

<pre><code># Switch back to root
exit
[root@hn1-db-0 ~]# pcs resource clear SAPHana_HN1_03-master
</code></pre>

A HANA-erőforrás állapotának figyelése a "pcs status" használatával. Miután elindította a HANA-t a hn1-db-0-ban, a kimenetnek így kell kinéznie:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent"></a>Az Azure-beli elkerítési ügynök tesztelése

> [!NOTE]
> Ez a cikk a *slave* kifejezésre mutató hivatkozásokat tartalmaz, amely a Microsoft által már nem használt kifejezés. Amikor eltávolítjuk a kifejezést a szoftverből, eltávolítjuk a cikkből.  

Erőforrás állapota a teszt megkezdése előtt:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

Az Azure elkerítési ügynök beállításának tesztelése érdekében tiltsa le a hálózati adaptert a csomóponton, SAP HANA főkiszolgálóként fut.
A hálózati hibák szimulálására vonatkozó leírásért tekintse meg a Red Hat Tudásbázis [79523-as](https://access.redhat.com/solutions/79523) cikkét. Ebben a példában a net_breaker szkriptet használjuk a hálózathoz való hozzáférés blokkolásához.

<pre><code>[root@hn1-db-1 ~]# sh ./net_breaker.sh BreakCommCmd 10.0.0.6
</code></pre>

A virtuális gépnek most újra kell indulni vagy le kell állnia a fürt konfigurációjának megfelelően.
Ha a beállítást kikapcsolja, a virtuális gép leáll, és az erőforrásokat áttelepíti a `stonith-action` futó virtuális gépre.

Miután újra elindítja a virtuális gépet, a SAP HANA erőforrás nem indul el másodlagosként, ha be van `AUTOMATED_REGISTER="false"` állítva. Ebben az esetben konfigurálja másodlagosként a HANA-példányt a következő parancs végrehajtásával:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>

# Switch back to root and clean up the failed state
exit
# On RHEL <b>7.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
# On RHEL <b>8.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03 node=&lt;hostname on which the resource needs to be cleaned&gt;
</code></pre>

Erőforrás állapota a teszt után:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

### <a name="test-a-manual-failover"></a>Manuális feladatátvétel tesztelése

Erőforrás állapota a teszt megkezdése előtt:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

A manuális feladatátvétel teszteléséhez leállíthatja a fürtöt a hn1-db-0 csomóponton:

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>

A feladatátvétel után újra elindíthatja a fürtöt. Ha be van `AUTOMATED_REGISTER="false"` állítva, SAP HANA hn1-db-0 csomóponton található erőforrás nem másodlagosként indul el. Ebben az esetben konfigurálja másodlagosként a HANA-példányt a következő parancs végrehajtásával:

<pre><code>[root@hn1-db-0 ~]# pcs cluster start
[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> exit
# On RHEL <b>7.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
# On RHEL <b>8.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03 node=&lt;hostname on which the resource needs to be cleaned&gt;
</code></pre>

Erőforrás állapota a teszt után:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-a-manual-failover"></a>Manuális feladatátvétel tesztelése

Erőforrás állapota a teszt megkezdése előtt:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

A manuális feladatátvétel teszteléséhez leállíthatja a fürtöt a hn1-db-0 csomóponton:

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>


## <a name="next-steps"></a>Következő lépések

* [Az Azure Virtual Machines tervezése és implementációja az SAP-hez][planning-guide]
* [Azure Virtual Machines üzembe helyezése az SAP-hez][deployment-guide]
* [Azure Virtual Machines DBMS üzembe helyezése SAP-hez][dbms-guide]
* [SAP HANA virtuálisgép-tárolókonfigurációk konfigurálása](./hana-vm-operations-storage.md)
---
title: Magas rendelkezésre állású SAP HANA SLES-alapú Azure-beli virtuális gépeken | Microsoft Docs
description: Magas rendelkezésre állású SAP HANA azure-beli virtuális gépeken a SUSE Linux Enterprise Server
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
ms.openlocfilehash: ea1296fd4e31c2deaed79e980ab764c523a2bfd7
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364362"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-suse-linux-enterprise-server"></a>Magas rendelkezésre állású SAP HANA azure-beli virtuális gépeken a SUSE Linux Enterprise Server

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
[401162]:https://launchpad.support.sap.com/#/notes/401162

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json

Helyszíni fejlesztéshez használhatja a HANA rendszerreplikációt, vagy megosztott tárolót a magas rendelkezésre állás létrehozására a SAP HANA.
Az Azure-beli virtuális gépeken (VM)a HANA Azure-beli rendszerreplikációja jelenleg az egyetlen támogatott magas rendelkezésre állású funkció. SAP HANA Replikáció egy elsődleges csomópontból és legalább egy másodlagos csomópontból áll. Az elsődleges csomóponton végzett adatváltozások szinkron vagy aszinkron módon replikálódnak a másodlagos csomópontra.

Ez a cikk bemutatja, hogyan telepítheti és konfigurálhatja a virtuális gépeket, hogyan telepítheti a fürt keretrendszerét, és hogyan telepítheti és konfigurálhatja SAP HANA rendszerreplikációt.
A példakonfigurációkban telepítési parancsokat, **03-as** példányszámot és HANA **HN1** rendszerazonosítót használ a rendszer.

Először olvassa el a következő SAP-megjegyzéseket és -dokumentumokat:

* SAP Note [1928533], amely a következővel rendelkezik:
  * Az SAP-szoftverek üzembe helyezéséhez támogatott Azure-beli virtuálisgép-méretek listája.
  * Az Azure-beli virtuális gépek méretének fontos kapacitásinformációi.
  * A támogatott SAP-szoftverek, valamint az operációs rendszer (OS) és adatbázis-kombinációk.
  * A windowsos és linuxos rendszerekhez szükséges SAP kernelverzió Microsoft Azure.
* Az SAP Note [2015553] az SAP által támogatott SAP-szoftverek Azure-beli üzembe helyezésének előfeltételeit sorolja fel.
* Az SAP Note [2205917] ajánlott operációsrendszer-beállításokat SUSE Linux Enterprise Server SAP-alkalmazásokhoz.
* Az SAP [Note 1944799] SAP HANA útmutatót SUSE Linux Enterprise Server AZ SAP-alkalmazásokhoz.
* Az SAP [Note 2178632] részletes információkat tartalmaz az Azure-beli SAP-hez jelentett összes monitorozási metrikákról.
* Az SAP Note [2191498 rendelkezik] a Linuxhoz szükséges SAP Host Agent-verzióval az Azure-ban.
* Az SAP [Note 2243692 az] Azure-beli Linuxon való SAP-licencelésről tartalmaz információkat.
* Az SAP [Note 1984787 általános] információkat tartalmaz a 12 SUSE Linux Enterprise Server ről.
* Az SAP Note [1999351] további hibaelhárítási információkat tartalmaz az SAP Azure Továbbfejlesztett monitorozási bővítményével kapcsolatban.
* Az SAP Note [401162] arról nyújt tájékoztatást, hogyan kerülheti el a "már használatban van címet" a HANA rendszerreplikáció beállításakor.
* [Az SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) rendelkezik az összes szükséges, Linuxhoz szükséges SAP-megjegyzésekkel.
* [SAP HANA IaaS-platformok](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
* [Útmutató Virtual Machines Azure-erőforrások tervezéshez és implementációhoz a Linuxon való SAP-hez.][planning-guide]
* [Azure Virtual Machines sap on Linux][deployment-guide] rendszeren való üzembe helyezése (ez a cikk).
* [Azure Virtual Machines DBMS üzembe helyezése Linuxon az SAP-hez][dbms-guide] – útmutató.
* [SUSE Linux Enterprise Server SAP Applications 12 SP3 ajánlott eljárásokkal kapcsolatos útmutatói][sles-for-sap-bp]
  * Egy SR SAP HANA optimalált infrastruktúra (SLES for SAP Applications 12 SP1) beállítása. Az útmutató tartalmazza az összes szükséges információt a SAP HANA rendszerreplikáció helyszíni fejlesztéshez való beállításához. Használja ezt az útmutatót alapkonfigurációként.
  * Egy SR SAP HANA optimalált infrastruktúra (SLES for SAP Applications 12 SP1) beállítása

## <a name="overview"></a>Áttekintés

A magas rendelkezésre állás érdekében a SAP HANA két virtuális gépre van telepítve. Az adatok replikálása a HANA rendszerreplikációval történt.

![SAP HANA magas rendelkezésre állás áttekintése](./media/sap-hana-high-availability/ha-suse-hana.png)

SAP HANA rendszerreplikáció beállítása dedikált virtuális gazdagépnevet és virtuális IP-címeket használ. Az Azure-ban terheléselosztás szükséges egy virtuális IP-cím használatához. Az alábbi lista a terheléselosztás konfigurációját mutatja be:

* Előoldali konfiguráció: hn1-db IP-címe: 10.0.0.13
* Háttér-konfiguráció: A HANA rendszerreplikáció részét képezi összes virtuális gép elsődleges hálózati adapteréhez csatlakoztatva
* Mintavételi port: 62503-as port
* Terheléselosztási szabályok: 30313 TCP, 30315 TCP, 30317 TCP

## <a name="deploy-for-linux"></a>Üzembe helyezés Linux rendszeren

Az SAP-alkalmazásokhoz SAP HANA erőforrásügynök SUSE Linux Enterprise Server tartalmazza.
A Azure Marketplace az SAP-SUSE Linux Enterprise Server 12-hez használható rendszerképet tartalmaz, amely új virtuális gépek üzembe helyezésére használható.

### <a name="deploy-with-a-template"></a>Üzembe helyezés sablon használatával

A GitHubon található gyorsindítási sablonok egyikének használatával üzembe helyezheti az összes szükséges erőforrást. A sablon telepíti a virtuális gépeket, a terheléselosztást, a rendelkezésre állási készletet stb.
A sablon üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg [az adatbázissablont][template-multisid-db] vagy az átkonvergens sablont a Azure Portal. [][template-converged] 
    Az adatbázissablon csak egy adatbázis terheléselosztási szabályait hozza létre. Az átkonvergens sablon egy ASCS-/SCS- és ERS- (csak Linux-) példány terheléselosztási szabályait is létrehozza. Ha SAP NetWeaver-alapú rendszert szeretne telepíteni, és az ASCS/SCS-példányt ugyanazokra a gépekre szeretné telepíteni, használja az [átkonvergens sablont.][template-converged]

1. Adja meg a következő paramétereket:
    - **Sap-rendszerazonosító:** Adja meg a telepíteni kívánt SAP-rendszer SAP-rendszerazonosítóját. Az azonosító az üzembe helyezett erőforrások előtagja.
    - **Verem típusa:**(Ez a paraméter csak akkor alkalmazható, ha az átkonvergens sablont használja.) Válassza ki az SAP NetWeaver veremtípust.
    - **Operációs rendszer típusa:** Válassza ki az egyik Linux-disztribúciót. Ebben a példában válassza az **SLES 12 lehetőséget.**
    - **Adatbázis típusa:** Válassza a **HANA lehetőséget.**
    - **Sap System Size (Sap-rendszer** mérete): Adja meg, hogy az új rendszer hány SAPS-t fog megadni. Ha nem biztos abban, hogy a rendszernek hány SAPS-re van szüksége, kérdezze meg az SAP-technológiai partnert vagy a rendszerintegrátort.
    - **Rendszer rendelkezésre állása:** Válassza a **Magas rendelkezésre állás lehetőséget.**
    - **Rendszergazdai felhasználónév és rendszergazdai** jelszó: A rendszer létrehoz egy új felhasználót, amely a gépre való bejelentkezéshez használható.
    - **Új vagy meglévő alhálózat:** Meghatározza, hogy új virtuális hálózatot és alhálózatot kell-e létrehozni, vagy meglévő alhálózatot kell használni. Ha már rendelkezik a helyszíni hálózathoz csatlakoztatott virtuális hálózattal, válassza a Meglévő **lehetőséget.**
    - **Alhálózat azonosítója:** Ha a virtuális gépet egy olyan meglévő virtuális hálózatban szeretné üzembe helyezni, ahol meghatározta az alhálózatot, amelyhez a virtuális gépet hozzá kell rendelni, nevezze el az adott alhálózat azonosítóját. Az azonosító általában a következő: **/subscriptions/ \<subscription ID> /resourceGroups/ \<resource group name> /providers/Microsoft.Network/virtualNetworks/ \<virtual network name> /subnets/ \<subnet name>**.

### <a name="manual-deployment"></a>Manuális üzembe helyezés

> [!IMPORTANT]
> Győződjön meg arról, hogy a kiválasztott operációs rendszer SAP-SAP HANA a használt virtuálisgép-típusokon. A minősített SAP HANA virtuálisgép-típusok és operációsrendszer-kiadások listája a minősített [IaaS-platformokon SAP HANA található.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Az adott virtuálisgép-típus támogatott operációsrendszer-SAP HANA listájának teljes listájáért kattintson a felsorolt virtuálisgép-típus részleteire.
>  

1. Hozzon létre egy erőforráscsoportot.
1. Hozzon létre egy virtuális hálózatot.
1. Rendelkezésre állási csoport létrehozása.
   - Állítsa be a maximális frissítési tartományt.
1. Terheléselosztás létrehozása (belső). A standard [terheléselosztást javasoljuk.](../../../load-balancer/load-balancer-overview.md)
   - Válassza ki a 2. lépésben létrehozott virtuális hálózatot.
1. Hozza létre az 1. virtuális gépet.
   - Olyan SLES4SAP-rendszerképet használjon az Azure-katalógusban, amely SAP HANA virtuálisgép-típuson támogatott.
   - Válassza ki a 3. lépésben létrehozott rendelkezésre állási készletet.
1. Hozza létre a 2. virtuális gépet.
   - Olyan SLES4SAP-rendszerképet használjon az Azure-katalógusban, amely SAP HANA virtuálisgép-típuson támogatott.
   - Válassza ki a 3. lépésben létrehozott rendelkezésre állási készletet. 
1. Adatlemezek hozzáadása.

> [!IMPORTANT]
> A nem rögzített IP-cím terheléselosztási forgatókönyvekben nem támogatott a hálózati adapterek másodlagos IP-konfigurációiban. Részletekért lásd: [Az Azure Load Balancer korlátozásai.](../../../load-balancer/load-balancer-multivip-overview.md#limitations) Ha további IP-címre van szüksége a virtuális géphez, telepítsen egy második hálózati adaptert.   

> [!Note]
> Ha a nyilvános IP-címekkel nem rendelkezik virtuális gépeket a belső (nyilvános IP-cím nélküli) Standard Azure-terheléselosztás háttérkészletében helyezi el, nem lesz kimenő internetkapcsolat, kivéve, ha további konfigurációt végez a nyilvános végpontok felé irányuló útválasztás engedélyezése érdekében. A kimenő kapcsolatok elérésével kapcsolatos részletekért lásd: Nyilvános végponti kapcsolat Virtual Machines Azure standard Load Balancer SAP magas rendelkezésre [állású forgatókönyvekben.](./high-availability-guide-standard-load-balancer-outbound-connections.md)  

1. Ha standard terheléselosztást használ, kövesse az alábbi konfigurációs lépéseket:
   1. Először hozzon létre egy előoldali IP-címkészletet:
   
      1. Nyissa meg a terheléselosztást, válassza ki **az előtere IP-készletet,** majd válassza a **Hozzáadás lehetőséget.**
      1. Adja meg az új előtere IP-címkészlet nevét (például **hana-frontend).**
      1. Állítsa a **Hozzárendelést** **Statikusra,** és adja meg az IP-címet (például **10.0.0.13).**
      1. Válassza az **OK** lehetőséget.
      1. Az új előoldali IP-címkészlet létrehozása után jegyezze fel a készlet IP-címét.
   
   1. Ezután hozzon létre egy háttérkészletet:
   
      1. Nyissa meg a terheléselosztást, válassza ki a **háttérkészleteket,** majd válassza a **Hozzáadás lehetőséget.**
      1. Adja meg az új háttérkészlet nevét (például **hana-backend).**
      1. Válassza a **Virtual Network** lehetőséget.
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
   
      1. Nyissa meg a terheléselosztást, válassza a **terheléselosztási szabályok lehetőséget,** majd kattintson a **Hozzáadás gombra.**
      1. Adja meg az új terheléselosztási szabály nevét (például **hana-lb).**
      1. Válassza ki az előtere IP-címét, a háttérkészletet és a korábban létrehozott állapot-mintavételt (például **hana-frontend,** **hana-backend** és **hana-hp).**
      1. Válassza a **HA portok lehetőséget.**
      1. Ügyeljen arra, hogy **engedélyezze a nem rögzített IP-címet.**
      1. Válassza az **OK** lehetőséget.

1. Ha az Ön forgatókönyve alapszintű terheléselosztást is előír, kövesse az alábbi konfigurációs lépéseket:
   1. Először hozzon létre egy előoldali IP-címkészletet:
   
      1. Nyissa meg a terheléselosztást, válassza ki **az előtere IP-címkészletet,** majd válassza a **Hozzáadás lehetőséget.**
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
      1. Ismételje meg ezeket a lépéseket a 3 **03** 17-es porton.
   
   1. A SAP HANA 2.0 esetén hozza létre a terheléselosztási szabályokat a rendszeradatbázishoz:
   
      1. Nyissa meg a terheléselosztást, válassza a **terheléselosztási szabályok lehetőséget,** majd kattintson a **Hozzáadás gombra.**
      1. Adja meg az új terheléselosztási szabály nevét (például hana-lb-3 **03** 13).
      1. Válassza ki az előtere IP-címét, a háttérkészletet és a korábban létrehozott állapot-mintavételt (például **hana-frontend).**
      1. A Protokoll **beállításnál tartsa** meg a **TCP-t,** és adja meg a 3 **03** 13-as portot.
      1. Növelje az **üresjárati időkorlátot** 30 percre.
      1. Ügyeljen arra, hogy **engedélyezze a nem rögzített IP-címet.**
      1. Válassza az **OK** lehetőséget.
      1. Ismételje meg ezeket a lépéseket a 3 **03** 14-es porton.
   
   1. A SAP HANA 2.0 esetén először hozza létre a terheléselosztási szabályokat a bérlői adatbázishoz:
   
      1. Nyissa meg a terheléselosztást, válassza a **terheléselosztási szabályok lehetőséget,** majd kattintson a **Hozzáadás gombra.**
      1. Adja meg az új terheléselosztási szabály nevét (például hana-lb-3 **03** 40).
      1. Válassza ki a korábban létrehozott előtere IP-címet, háttérkészletet és állapot-mintavételt (például **hana-frontend).**
      1. A Protokoll **beállításnál tartsa** meg a **TCP-t,** és adja meg a 3 **03** 40-es portot.
      1. Növelje az **üresjárati időkorlátot** 30 percre.
      1. Ügyeljen arra, hogy **engedélyezze a nem rögzített IP-címet.**
      1. Válassza az **OK** lehetőséget.
      1. Ismételje meg ezeket a lépéseket a 3 **03** 41-es és a **303 42-es** porton is.

   Az adatbázishoz szükséges portokkal kapcsolatos további SAP HANA [](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) olvassa el a Kapcsolatok a bérlői adatbázisokhoz fejezetet a [SAP HANA Tenant Databases](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) útmutatójában vagy az [SAP 2388694][2388694]megjegyzésben.

> [!IMPORTANT]
> Ne engedélyezze a TCP-időbélyegeket az azure-beli virtuális gépeken a Azure Load Balancer. A TCP-időbélyegek engedélyezése az állapot-mintavételek sikertelen lesz. Állítsa a **paraméter net.ipv4.tcp_timestamps** **értékét 0-ra.** Részletekért [lásd: Load Balancer állapot-mintavételek.](../../../load-balancer/load-balancer-custom-probe-overview.md)
> Lásd még az SAP [2382421 megjegyzését.](https://launchpad.support.sap.com/#/notes/2382421) 

## <a name="create-a-pacemaker-cluster"></a>Pacemaker-fürt létrehozása

A Pacemaker beállítása az [Azure-ban SUSE Linux Enterprise Server](high-availability-guide-suse-pacemaker.md) lépéseit követve hozzon létre egy alapszintű Pacemaker-fürtöt ehhez a HANA-kiszolgálóhoz. Ugyanazt a Pacemaker-fürtöt használhatja a SAP HANA és az SAP NetWeaver (A)SCS rendszerekhez.

## <a name="install-sap-hana"></a>Az SAP HANA telepítése

A szakasz lépései a következő előtagokat használják:
- **[A]**: A lépés az összes csomópontra vonatkozik.
- **[1]**: A lépés csak az 1. csomópontra vonatkozik.
- **[2]**: A lépés csak a Pacemaker-fürt 2. csomópontjára vonatkozik.

1. **[A]** A lemezelrendezés beállítása: **Logikaikötet-kezelő (LVM).**

   Javasoljuk, hogy az LVM-et használja az adatokat és naplófájlokat tároló kötetek számára. Az alábbi példa feltételezi, hogy a virtuális gépekhez négy csatlakoztatott adatlemez tartozik, amelyek két kötet létrehozására használhatók.

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

   Hozza létre a logikai köteteket. A lineáris kötet akkor jön létre, ha a kapcsoló `lvcreate` nélkül `-i` használja. Javasoljuk, hogy hozzon létre egy csíkozott kötetet a jobb I/O-teljesítmény érdekében, és igazítsa a csíkméreteket a virtuálisgép SAP HANA konfigurációkban dokumentált [értékekhez.](./hana-vm-operations-storage.md) Az argumentumnak a mögöttes fizikai kötetek számának, az argumentumnak pedig a `-i` `-I` csíkméretnek kell lennie. Ebben a dokumentumban két fizikai kötetet használunk az adatkötethez, ezért a switch argumentum `-i` **beállítása 2.** Az adatkötet csíkmérete **256KiB.** A rendszer egy fizikai kötetet használ a naplókötethez, ezért a naplókötet parancsaihoz a rendszer nem vagy `-i` `-I` kapcsolókat használ.  

   > [!IMPORTANT]
   > Használja a kapcsolót, és állítsa a mögöttes fizikai kötet számára, ha több fizikai kötetet használ minden adathoz, naplóhoz vagy `-i` megosztott kötethez. Csíkozott kötet létrehozásakor a kapcsolóval adhatja meg `-I` a csíkméretet.  
   > Tekintse [SAP HANA virtuálisgép-tárolókonfigurációk](./hana-vm-operations-storage.md) ajánlott tárolási konfigurációit, beleértve a csíkméreteket és a lemezek számát.  

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

   Bemutató rendszerek esetén a HANA-adatokat és a naplófájlokat egyetlen lemezre helyezze. Hozzon létre egy partíciót a /dev/disk/azure/scsi1/lun0 oldalon, és formázza xfs formátumban:

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

   Szúrja be a következő sorokat az /etc/hosts fájlba. Módosítsa az IP-címet és az állomásnevet a környezetének megfelelőre:

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** Telepítse a SAP HANA magas rendelkezésre állású csomagokat:

   <pre><code>sudo zypper install SAPHanaSR
   </code></pre>

A SAP HANA telepítéséhez kövesse az SR teljesítményoptimal SAP HANA forgatókönyv útmutatójának 4. [fejezetét.](https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/)

1. **[A] Futtassa** a **hdblcm** programot a HANA DVD-ről. Írja be a következő értékeket a parancssorba:
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

## <a name="configure-sap-hana-20-system-replication"></a>A SAP HANA 2.0-s rendszerreplikáció konfigurálása

A szakasz lépései a következő előtagokat használják:

* **[A]**: A lépés az összes csomópontra vonatkozik.
* **[1]**: A lépés csak az 1. csomópontra vonatkozik.
* **[2]**: A lépés csak a Pacemaker-fürt 2. csomópontjára vonatkozik.

1. **[1]** Hozza létre a bérlői adatbázist.

   Ha 2.0-s SAP HANA MDC-t használ, hozzon létre egy bérlői adatbázist az SAP NetWeaver rendszer számára. Cserélje **le az NW1** helyére az SAP-rendszer biztonsági azonosítóját.

   Hajtsa végre a következő parancsot <hanasid \> adm paranccsal:

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

## <a name="configure-sap-hana-10-system-replication"></a>Az SAP HANA 1.0-s rendszerreplikáció konfigurálása

A szakasz lépései a következő előtagokat használják:

* **[A]**: A lépés az összes csomópontra vonatkozik.
* **[1]**: A lépés csak az 1. csomópontra vonatkozik.
* **[2]**: A lépés csak a Pacemaker-fürt 2. csomópontjára vonatkozik.

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

   Regisztrálja a másodlagos helyet <hanasid \> adm:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="implement-the-python-system-replication-hook-saphanasr"></a>A Python rendszerreplikációs hook SAPHanaSR implementációja

Ez fontos lépés a fürt integrációjának optimalizálásához és az észlelés javításához, amikor feladatátvételre van szükség. Erősen ajánlott az SAPHanaSR Python-hook konfigurálása.    

1. **[A] Telepítse** a HANA "rendszerreplikációs hookot". A hookot mindkét HANA DB-csomóponton telepíteni kell.           

   > [!TIP]
   > Ellenőrizze, hogy az SAPHanaSR csomag legalább 0.153-as verziójú-e, hogy használni tudja az SAPHanaSR Python-hook funkciót.       
   > A Python-hook csak a HANA 2.0-hoz valósítható meg.        

   1. Készítse elő a hookot a `root` következőként: .  

    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR/SAPHanaSR.py /hana/shared/myHooks
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
Az új rendszerreplikációs hook implementációja SAP HANA [HANA HA/DR-szolgáltatók](https://documentation.suse.com/sbp/all/html/SLES4SAP-hana-sr-guide-PerfOpt-12/index.html#_set_up_sap_hana_hadr_providers)beállítása című oldalon talál további információt.  

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
     # 2021-04-08 22:18:15.877583 ha_dr_SAPHanaSR SFAIL
     # 2021-04-08 22:18:46.531564 ha_dr_SAPHanaSR SFAIL
     # 2021-04-08 22:21:26.816573 ha_dr_SAPHanaSR SOK

    ```

## <a name="create-sap-hana-cluster-resources"></a>Fürterőforrások SAP HANA létrehozása

Először hozza létre a HANA-topológiát. Futtassa a következő parancsokat az egyik Pacemaker-fürtcsomóponton:

<pre><code>sudo crm configure property maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID

sudo crm configure primitive rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
  operations \$id="rsc_sap2_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10" timeout="600" \
  op start interval="0" timeout="600" \
  op stop interval="0" timeout="300" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>"

sudo crm configure clone cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  meta clone-node-max="1" target-role="Started" interleave="true"
</code></pre>

Ezután hozza létre a HANA-erőforrásokat:

> [!IMPORTANT]
> A legutóbbi tesztelés olyan helyzeteket tárt fel, amikor a Netcat a hátralékok és a csak egy kapcsolat kezelésére vonatkozó korlátozása miatt nem válaszol a kérelmekre. A netcat erőforrás nem figyel az Azure Load Balancer-kérelmekre, és a nem rögzített IP-cím elérhetetlenné válik.  
> Meglévő Pacemaker-fürtök esetén korábban javasolt a netcat helyett a socat használata. Jelenleg az azure-lb erőforrásügynök használatát javasoljuk, amely a csomagerőforrás-ügynökök része, és a következő csomagverzió-követelményekkel:
> - Az SLES 12 SP4/SP5 verziónak legalább resource-agents-4.3.018.a7fb5035-3.30.1 verziónak kell lennie.  
> - Az SLES 15/15 SP1 verziónak legalább resource-agents-4.3.0184.6ee15eb2-4.13.1 verziónak kell lennie.  
>
> Vegye figyelembe, hogy a módosítás rövid állásidőt igényel.  
> Meglévő Pacemaker-fürtök esetén, ha a konfigurációt már módosították a socat használatára az [Azure Load-Balancer Detection Hardening (Az Azure Load-Balancer Detection Hardening](https://www.suse.com/support/kb/doc/?id=7024128)) leírásában leírtak szerint, nincs szükség arra, hogy azonnal átváltson az azure-lb erőforrásügynökre.


> [!NOTE]
> Ez a cikk a *master* és *a slave* kifejezésre mutató hivatkozásokat tartalmaz, amelyekre a Microsoft már nem használ. Ha eltávolítjuk ezeket a kifejezéseket a szoftverből, eltávolítjuk őket ebből a cikkből.

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer. 

sudo crm configure primitive rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHana \
  operations \$id="rsc_sap_<b>HN1</b>_HDB<b>03</b>-operations" \
  op start interval="0" timeout="3600" \
  op stop interval="0" timeout="3600" \
  op promote interval="0" timeout="3600" \
  op monitor interval="60" role="Master" timeout="700" \
  op monitor interval="61" role="Slave" timeout="700" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
  DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

sudo crm configure ms msl_SAPHana_<b>HN1</b>_HDB<b>03</b> rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> \
  meta notify="true" clone-max="2" clone-node-max="1" \
  target-role="Started" interleave="true"

sudo crm configure primitive rsc_ip_<b>HN1</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \
  meta target-role="Started" \
  operations \$id="rsc_ip_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10s" timeout="20s" \
  params ip="<b>10.0.0.13</b>"

sudo crm configure primitive rsc_nc_<b>HN1</b>_HDB<b>03</b> azure-lb port=625<b>03</b> \
  meta resource-stickiness=0

sudo crm configure group g_ip_<b>HN1</b>_HDB<b>03</b> rsc_ip_<b>HN1</b>_HDB<b>03</b> rsc_nc_<b>HN1</b>_HDB<b>03</b>

sudo crm configure colocation col_saphana_ip_<b>HN1</b>_HDB<b>03</b> 4000: g_ip_<b>HN1</b>_HDB<b>03</b>:Started \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>:Master  

sudo crm configure order ord_SAPHana_<b>HN1</b>_HDB<b>03</b> Optional: cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>

# Clean up the HANA resources. The HANA resources might have failed because of a known issue.
sudo crm resource cleanup rsc_SAPHana_<b>HN1</b>_HDB<b>03</b>

sudo crm configure property maintenance-mode=false
sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

Győződjön meg arról, hogy a fürt állapota ok, és hogy az összes erőforrás elindult. Nem fontos, hogy az erőforrások melyik csomóponton futnak.

<pre><code>sudo crm_mon -r

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# stonith-sbd     (stonith:external/sbd): Started hn1-db-0
# Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
#     Started: [ hn1-db-0 hn1-db-1 ]
# Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
#     Masters: [ hn1-db-0 ]
#     Slaves: [ hn1-db-1 ]
# Resource Group: g_ip_HN1_HDB03
#     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
#     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
</code></pre>

## <a name="configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster"></a>Aktív/olvasható HANA-alapú rendszerreplikáció konfigurálása a Pacemaker-fürtben

Az SAP HANA 2.0 SPS 01-től kezdődően az SAP engedélyezi az aktív/olvasható beállítást az SAP HANA rendszerreplikációhoz, ahol az SAP HANA rendszerreplikáció másodlagos rendszerei aktívan használhatók az intenzív olvasási terhelésű számítási feladatokhoz. Az ilyen beállítás fürtökben való támogatásához egy második virtuális IP-címre van szükség, amely lehetővé teszi az ügyfelek számára, hogy hozzáférjenek a másodlagos, SAP HANA adatbázishoz. Annak érdekében, hogy a másodlagos replikációs hely a feladatátvétel után is elérhető legyen, a fürtnek át kell mozgatnia a virtuális IP-címet az SAPHana-erőforrás másodlagos helyével.

Ez a szakasz azokat a további lépéseket ismerteti, amelyek a HANA aktív/olvasható rendszerreplikációjának kezeléséhez szükségesek egy második virtuális IP-címmel rendelkező SUSE magas rendelkezésre állású fürtben.    
Mielőtt továbblépne, győződjön meg arról, hogy teljes körűen konfigurálta az SUSE magas rendelkezésre állású fürtöt SAP HANA adatbázis kezeléséhez a dokumentáció fenti részében leírtak szerint.  

![SAP HANA magas rendelkezésre állás az olvasásra képes másodlagos adatbázissal](./media/sap-hana-high-availability/ha-hana-read-enabled-secondary.png)

### <a name="additional-setup-in-azure-load-balancer-for-activeread-enabled-setup"></a>További beállítások az Azure Load Balancerben az aktív/olvasható beállításhoz

A második virtuális IP-cím üzembe helyezésének további lépéseiként győződjön meg arról, hogy konfigurálta a Azure Load Balancer manuális üzembe [helyezés szakaszban leírtak](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability#manual-deployment) szerint.

1. Standard **terheléselosztás** esetén kövesse az alábbi lépéseket a korábbi szakaszban létrehozott terheléselosztáson.

   a. Hozzon létre egy második előoldali IP-címkészletet: 

   - Nyissa meg a terheléselosztást, válassza ki **az előtere IP-készletet,** majd válassza a **Hozzáadás lehetőséget.**
   - Adja meg a második előoldali IP-címkészlet nevét (például **hana-secondaryIP).**
   - Állítsa a **Hozzárendelést** **Statikusra,** és adja meg az IP-címet (például **10.0.0.14).**
   - Válassza az **OK** lehetőséget.
   - Az új előtere IP-címkészlet létrehozása után jegyezze fel az előtere IP-címét.

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
   - Növelje az **üresjárati időkorlátot** 30 percre.
   - Ügyeljen arra, hogy **engedélyezze a nem rögzített IP-címet.**
   - Válassza az **OK** lehetőséget.

### <a name="configure-hana-activeread-enabled-system-replication"></a>A HANA aktív/olvasási engedélyezett rendszerreplikációjának konfigurálása

A HANA rendszerreplikáció konfigurálási lépéseit a Configure [SAP HANA 2.0 System Replication (2.0-s](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability#configure-sap-hana-20-system-replication) rendszerreplikáció konfigurálása) című szakaszban ismertetjük. Ha írásra alkalmas másodlagos forgatókönyvet helyez üzembe, miközben a második csomóponton konfigurálja a rendszerreplikációt, hajtsa végre a következő parancsot **hanasid** adm-ként:

```
sapcontrol -nr 03 -function StopWait 600 10 

hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2 --operationMode=logreplay_readaccess 
```

### <a name="adding-a-secondary-virtual-ip-address-resource-for-an-activeread-enabled-setup"></a>Másodlagos virtuális IP-cím erőforrás hozzáadása aktív/olvasható beállításhoz

A második virtuális IP-cím és a megfelelő közös helymegkötés az alábbi parancsokkal konfigurálható:

```
crm configure property maintenance-mode=true

crm configure primitive rsc_secip_HN1_HDB03 ocf:heartbeat:IPaddr2 \
 meta target-role="Started" \
 operations \$id="rsc_secip_HN1_HDB03-operations" \
 op monitor interval="10s" timeout="20s" \
 params ip="10.0.0.14"

crm configure primitive rsc_secnc_HN1_HDB03 azure-lb port=62603 \
 meta resource-stickiness=0

crm configure group g_secip_HN1_HDB03 rsc_secip_HN1_HDB03 rsc_secnc_HN1_HDB03

crm configure colocation col_saphana_secip_HN1_HDB03 4000: g_secip_HN1_HDB03:Started \
 msl_SAPHana_HN1_HDB03:Slave 

crm configure property maintenance-mode=false
```
Győződjön meg arról, hogy a fürt állapota ok, és hogy az összes erőforrás elindult. A második virtuális IP-cím a másodlagos helyen fog futni az SAPHana másodlagos erőforrással együtt.

```
sudo crm_mon -r

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# stonith-sbd     (stonith:external/sbd): Started hn1-db-0
# Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
#     Started: [ hn1-db-0 hn1-db-1 ]
# Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
#     Masters: [ hn1-db-0 ]
#     Slaves: [ hn1-db-1 ]
# Resource Group: g_ip_HN1_HDB03
#     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
#     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
# Resource Group: g_secip_HN1_HDB03:
#     rsc_secip_HN1_HDB03       (ocf::heartbeat:IPaddr2):        Started hn1-db-1
#     rsc_secnc_HN1_HDB03       (ocf::heartbeat:azure-lb):       Started hn1-db-1

```

A következő szakaszban megtalálja a végrehajtható feladatátvételi tesztek jellemző készletét.

Vegye figyelembe a második virtuális IP-viselkedést, amikor az írásra képes másodlagos kiszolgálóval konfigurált HANA-fürtöt tesztel:

1. Amikor egy **fürterőforrást SAPHana_HN1_HDB03** **hn1-db-1-re,** a második virtuális IP-cím a másik **hn1-db-0 kiszolgálóra lesz áthelyezve.** Ha a AUTOMATED_REGISTER="false" értéket konfigurálta, és a HANA rendszerreplikációja nincs automatikusan regisztrálva, akkor a második virtuális **IP-cím a hn1-db-0-n** fog futni, mivel a kiszolgáló elérhető és a fürtszolgáltatások online állapotban vannak.  

2. A kiszolgáló összeomlásának tesztelése során a második virtuális IP-erőforrás **(rsc_secip_HN1_HDB03**) és az Azure Load Balancer porterőforrása **(rsc_secnc_HN1_HDB03**) az elsődleges virtuális IP-erőforrások mellett az elsődleges kiszolgálón fog futni. Amíg a másodlagos kiszolgáló nem áll le, az olvasásra képes HANA-adatbázishoz csatlakozó alkalmazások az elsődleges HANA-adatbázishoz fognak csatlakozni. Ez a viselkedés várható, mivel nem szeretné, ha az olvasási módú HANA-adatbázishoz csatlakozó alkalmazások elérhetetlenné válnának, amíg a másodlagos kiszolgáló nem érhető el.
  
3. Ha a másodlagos kiszolgáló elérhető és a fürtszolgáltatások online állapotban vannak, a második virtuális IP- és port-erőforrások automatikusan át lesznek áthelyezve a másodlagos kiszolgálóra, még akkor is, ha a HANA rendszerreplikációja nem másodlagosként van regisztrálva. Mielőtt elindítja a fürtszolgáltatásokat a kiszolgálón, győződjön meg arról, hogy a másodlagos HANA-adatbázist olvasottként regisztrálta. A HANA-példányfürt erőforrását úgy konfigurálhatja, hogy automatikusan regisztrálja a másodlagos példányt a AUTOMATED_REGISTER=true paraméter beállításával.       

4. A feladatátvétel és a tartalék telepítés során megszakadhat az alkalmazások meglévő kapcsolatai, amelyek a MÁSODIK virtuális IP-címet használják a HANA-adatbázishoz való csatlakozáshoz.  

## <a name="test-the-cluster-setup"></a>A fürt beállításának tesztelése

Ez a szakasz a beállítás tesztelését ismerteti. Minden teszt feltételezi, hogy Ön a gyökér, és SAP HANA főkiszolgáló a **hn1-db-0 virtuális gépen** fut.

### <a name="test-the-migration"></a>A migrálás tesztelése

A teszt elkezdése előtt győződjön meg arról, hogy a Pacemaker nem rendelkezik sikertelen művelettel (a crm_mon -r segítségével), nincsenek váratlan helykorlátozások (például egy migrálási teszt maradékai), és hogy a HANA szinkronizálási állapotban van,például az SAPHanaSR-showAttr segítségével:

<pre><code>hn1-db-0:~ # SAPHanaSR-showAttr
Sites    srHook
----------------
SITE2    SOK

Global cib-time
--------------------------------
global Mon Aug 13 11:26:04 2018

Hosts    clone_state lpa_hn1_lpt node_state op_mode   remoteHost    roles                            score site  srmode sync_state version                vhost
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
hn1-db-0 PROMOTED    1534159564  online     logreplay nws-hana-vm-1 4:P:master1:master:worker:master 150   SITE1 sync   PRIM       2.00.030.00.1522209842 nws-hana-vm-0
hn1-db-1 DEMOTED     30          online     logreplay nws-hana-vm-0 4:S:master1:master:worker:master 100   SITE2 sync   SOK        2.00.030.00.1522209842 nws-hana-vm-1
</code></pre>

A főcsomópont SAP HANA a következő parancs végrehajtásával mirateálhatja:

<pre><code>crm resource move msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b> force
</code></pre>

Ha be van állítva, ennek a parancssorozatnak a főcsomópontot és a virtuális IP-címet tartalmazó csoportot SAP HANA át a `AUTOMATED_REGISTER="false"` hn1-db-1-re.

A migrálás után a crm_mon -r kimenete a következő lesz

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Stopped: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1

Failed Actions:
* rsc_SAPHana_HN1_HDB03_start_0 on hn1-db-0 'not running' (7): call=84, status=complete, exitreason='none',
    last-rc-change='Mon Aug 13 11:31:37 2018', queued=0ms, exec=2095ms
</code></pre>

A SAP HANA hn1-db-0-s erőforrás nem indul el másodlagosként. Ebben az esetben konfigurálja másodlagosként a HANA-példányt a következő parancs végrehajtásával:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>
</code></pre>

A migrálás helykorlátozásokat hoz létre, amelyek újra törölhetők:

<pre><code># Switch back to root and clean up the failed state
exit
hn1-db-0:~ # crm resource clear msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
</code></pre>

A másodlagos csomópont-erőforrás állapotát is meg kell tisztítania:

<pre><code>hn1-db-0:~ # crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

A HANA-erőforrás állapotának figyelése az -r crm_mon használatával. Miután elindította a HANA-t a hn1-db-0-ban, a kimenetnek így kell kinéznie:

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent-not-sbd"></a>Az Azure-beli elkerítési ügynök tesztelése (nem SBD)

Az Azure elkerítési ügynök beállításának tesztelése érdekében tiltsa le a hálózati adaptert a hn1-db-0 csomóponton:

<pre><code>sudo ifdown eth0
</code></pre>

A virtuális gépnek most újra kell indulni vagy le kell állnia a fürt konfigurációjának megfelelően.
Ha a beállítást kikapcsolja, a virtuális gép leáll, és az erőforrásokat áttelepíti a `stonith-action` futó virtuális gépre.

Miután újra elindítja a virtuális gépet, a SAP HANA erőforrás nem indul el másodlagosként, ha be van `AUTOMATED_REGISTER="false"` állítva. Ebben az esetben konfigurálja másodlagosként a HANA-példányt a következő parancs végrehajtásával:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="test-sbd-fencing"></a>SBD-elkerítés tesztelése

Az SBD beállítását az inquisitor folyamatának leállítva tesztelheti.

<pre><code>hn1-db-0:~ # ps aux | grep sbd
root       1912  0.0  0.0  85420 11740 ?        SL   12:25   0:00 sbd: inquisitor
root       1929  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014056f268462316e4681b704a9f73 - slot: 0 - uuid: 7b862dba-e7f7-4800-92ed-f76a4e3978c8
root       1930  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014059bc9ea4e4bac4b18808299aaf - slot: 0 - uuid: 5813ee04-b75c-482e-805e-3b1e22ba16cd
root       1931  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-36001405b8dddd44eb3647908def6621c - slot: 0 - uuid: 986ed8f8-947d-4396-8aec-b933b75e904c
root       1932  0.0  0.0  90524 16656 ?        SL   12:25   0:00 sbd: watcher: Pacemaker
root       1933  0.0  0.0 102708 28260 ?        SL   12:25   0:00 sbd: watcher: Cluster
root      13877  0.0  0.0   9292  1572 pts/0    S+   12:27   0:00 grep sbd

hn1-db-0:~ # kill -9 1912
</code></pre>

A hn1-db-0 fürtcsomópontot újra kell indítani. Előfordulhat, hogy a Pacemaker szolgáltatás ezt követően nem kezdődik el. Mindenképpen indítsa el újra.

### <a name="test-a-manual-failover"></a>Manuális feladatátvétel tesztelése

A manuális feladatátvétel tesztelésére leállíthatja a `pacemaker` szolgáltatást a hn1-db-0 csomóponton:

<pre><code>service pacemaker stop
</code></pre>

A feladatátvétel után újra elindíthatja a szolgáltatást. Ha be van `AUTOMATED_REGISTER="false"` állítva, SAP HANA hn1-db-0 csomóponton található erőforrás nem másodlagosként indul el. Ebben az esetben konfigurálja másodlagosként a HANA-példányt a következő parancs végrehajtásával:

<pre><code>service pacemaker start
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="suse-tests"></a>SUSE-tesztek

> [!IMPORTANT]
> Győződjön meg arról, hogy a kiválasztott operációs rendszer SAP-SAP HANA a használt virtuálisgép-típusokon. A minősített SAP HANA virtuálisgép-típusok és operációsrendszer-kiadások listája a minősített [IaaS-platformokon SAP HANA található.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Az adott virtuálisgép-típus támogatott operációsrendszer-SAP HANA listájának teljes listájáért kattintson a felsorolt virtuálisgép-típus részleteire.

Futtassa az összes tesztelési esetet, amely szerepel az SAP HANA SR teljesítményoptimalált forgatókönyvében vagy SAP HANA SR Cost Optimized Scenario (SR-költségoptimalált forgatókönyv) útmutatóban az adott forgatókönyvtől függően. Az útmutatókat az [SLES for SAP ajánlott eljárások oldalán találja.][sles-for-sap-bp]

Az alábbi tesztek az SAP Applications 12 SP1 SAP HANA SR Performance Optimized Scenario SUSE Linux Enterprise Server forgatókönyv tesztelési leírásának másolatai. Naprakész verziókhoz mindig olvassa el magát az útmutatót is. A teszt megkezdése előtt mindig győződjön meg arról, hogy a HANA szinkronban van, és győződjön meg arról, hogy a Pacemaker konfigurációja helyes.

Az alábbi tesztleírások azt feltételezik, hogy PREFER_SITE_TAKEOVER="true" és AUTOMATED_REGISTER="false".
MEGJEGYZÉS: A következő tesztek egymás után futtathatók, és az előző tesztek kilépési állapotától függnek.

1. 1. TESZT: ELSŐDLEGES ADATBÁZIS LEÁLLÍTÁSA AZ 1. CSOMÓPONTON

   Erőforrás állapota a teszt megkezdése előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Futtassa a következő parancsokat <hanasid \> adm parancsként a hn1-db-0 csomóponton:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   A Pacemakernek észlelnie kell a leállított HANA-példányt és a másik csomópontra való feladatátvételt. A feladatátvétel után a HANA-példány leáll a hn1-db-0 csomóponton, mert a Pacemaker nem regisztrálja automatikusan a csomópontot HANA-másodlagosként.

   Az alábbi parancsok futtatásával regisztrálja a hn1-db-0 csomópontot másodlagosként, és tisztítsa meg a hibás erőforrást.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. 2. TESZT: ELSŐDLEGES ADATBÁZIS LEÁLLÍTÁSA A 2. CSOMÓPONTON

   Erőforrás állapota a teszt megkezdése előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Futtassa a következő parancsokat <hanasid \> adm parancsként a hn1-db-1 csomóponton:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   A Pacemakernek észlelnie kell a leállított HANA-példányt és a másik csomópontra való feladatátvételt. A feladatátvétel után a HANA-példány leáll a hn1-db-1 csomóponton, mert a Pacemaker nem regisztrálja automatikusan a csomópontot HANA-másodlagosként.

   Az alábbi parancsok futtatásával regisztrálja a hn1-db-1 csomópontot másodlagosként, és tisztítsa meg a hibás erőforrást.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. 3. TESZT: ELSŐDLEGES ADATBÁZIS ÖSSZEOMLÁSA A CSOMÓPONTON

   Erőforrás állapota a teszt megkezdése előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Futtassa a következő parancsokat <hanasid \> adm parancsként a hn1-db-0 csomóponton:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>
   
   A Pacemakernek észlelnie kell a lekért HANA-példányt, és feladatátvételt kell tartania a másik csomópontra. A feladatátvétel után a HANA-példány leáll a hn1-db-0 csomóponton, mert a Pacemaker nem regisztrálja automatikusan a csomópontot HANA-másodlagosként.

   Futtassa a következő parancsokat a hn1-db-0 csomópont másodlagosként való regisztrálására és a hibás erőforrás tisztítására.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. 4. TESZT: ELSŐDLEGES ADATBÁZIS ÖSSZEOMLÁSA A 2. CSOMÓPONTON

   Erőforrás állapota a teszt megkezdése előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Futtassa a következő parancsokat <hanasid \> adm parancsként a hn1-db-1 csomóponton:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   A Pacemakernek észlelnie kell a lekért HANA-példányt, és feladatátvételt kell tartania a másik csomópontra. A feladatátvétel után a HANA-példány leáll a hn1-db-1 csomóponton, mert a Pacemaker nem regisztrálja automatikusan a csomópontot HANA-másodlagosként.

   Futtassa a következő parancsokat a hn1-db-1 csomópont másodlagosként való regisztrálására és a hibás erőforrás tisztítására.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. 5. TESZT: ELSŐDLEGES HELY CSOMÓPONTJÁNAK ÖSSZEOMLÁSA (1. CSOMÓPONT)

   Erőforrás állapota a teszt megkezdése előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Futtassa a következő parancsokat gyökérként a hn1-db-0 csomóponton:

   <pre><code>hn1-db-0:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   A Pacemakernek észlelnie kell a lekért fürtcsomópontot, és el kellkerítéssel elkerítést kell tartania a csomóponton. A csomópont körülkerítését után a Pacemaker elindítja a HANA-példány feladatátvételét. A körülkerített csomópont újraindításakor a Pacemaker nem indul el automatikusan.

   Futtassa a következő parancsokat a Pacemaker futtatásához, a hn1-db-0 csomópont SBD-üzeneteit, a hn1-db-0 csomópont másodlagosként való regisztrálását és a hibás erőforrás tisztítását.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-0:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-0:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-0 clear
   
   hn1-db-0:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. 6. TESZT: MÁSODLAGOS HELY CSOMÓPONTJÁNAK ÖSSZEOMLÁSA (2. CSOMÓPONT)

   Erőforrás állapota a teszt megkezdése előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Futtassa az alábbi parancsokat gyökérként a hn1-db-1 csomóponton:

   <pre><code>hn1-db-1:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   A Pacemakernek észlelni kell a lekért fürtcsomópontot, és el kellkeredni a csomópontot. A csomópont körülkerítését után a Pacemaker elindítja a HANA-példány feladatátvételét. A körülkerített csomópont újraindításakor a Pacemaker nem indul el automatikusan.

   Az alábbi parancsok futtatásával indítsa el a Pacemakert, megtisztítsa a hn1-db-1 csomópont SBD-üzeneteit, regisztrálja a hn1-db-1 csomópontot másodlagosként, és a hibás erőforrást.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. 7. TESZT: A MÁSODLAGOS ADATBÁZIS LEÁLLÍTÁSA A 2. CSOMÓPONTON

   Erőforrás állapota a teszt megkezdése előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Futtassa a következő parancsokat <hanasid \> adm parancsként a hn1-db-1 csomóponton:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   A Pacemaker észleli a leállított HANA-példányt, és sikertelenként fogja megjelölni az erőforrást a hn1-db-1 csomóponton. A Pacemakernek automatikusan újra kell indítania a HANA-példányt. Futtassa a következő parancsot a sikertelen állapot tisztításhoz.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. 8. TESZT: A MÁSODLAGOS ADATBÁZIS ÖSSZEOMLÁSA A 2. CSOMÓPONTON

   Erőforrás állapota a teszt megkezdése előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Futtassa a következő parancsokat <hanasid \> adm parancsként a hn1-db-1 csomóponton:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   A Pacemaker észleli a lekért HANA-példányt, és sikertelenként fogja megjelölni az erőforrást a hn1-db-1 csomóponton. Futtassa a következő parancsot a sikertelen állapot tisztításhoz. A Pacemakernek ezután automatikusan újra kell indítania a HANA-példányt.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. 9. TESZT: MÁSODLAGOS HANA-ADATBÁZIST FUTTATÓ MÁSODLAGOS HELYCSOMÓPONT (NODE 2) ÖSSZEOMLÁSA

   Erőforrás állapota a teszt megkezdése előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Futtassa a következő parancsokat gyökérként a hn1-db-1 csomóponton:

   <pre><code>hn1-db-1:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   A Pacemakernek észlelnie kell a lekért fürtcsomópontot, és el kellkerítéssel elkerítést kell tartania a csomóponton. A körülkerített csomópont újraindításakor a Pacemaker nem indul el automatikusan.

   Futtassa az alábbi parancsokat a Pacemaker futtatásához, a hn1-db-1 csomópont SBD-üzeneteit és a hibás erőforrás tisztítását.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker  
   
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

## <a name="next-steps"></a>Következő lépések

* [Az Azure Virtual Machines tervezése és implementációja az SAP-hez][planning-guide]
* [Azure Virtual Machines üzembe helyezése SAP-hez][deployment-guide]
* [Azure Virtual Machines DBMS üzembe helyezése SAP-hez][dbms-guide]

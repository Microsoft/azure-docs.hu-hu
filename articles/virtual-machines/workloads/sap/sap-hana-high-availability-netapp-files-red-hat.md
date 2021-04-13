---
title: A SAP HANA rendelkezésre állása AZ ANF-felskálával RHEL-| Microsoft Docs
description: Az AZURE-beli virtuális gépeken SAP HANA ANF használatával magas rendelkezésre állású virtuális gépek létrehozása.
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
ms.openlocfilehash: 774344c4215088482b110de91f8951bae4a41d25
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365824"
---
# <a name="high-availability-of-sap-hana-scale-up-with-azure-netapp-files-on-red-hat-enterprise-linux"></a>A SAP HANA rendelkezésre állása a Azure NetApp Files-on Red Hat Enterprise Linux

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
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
[1900823]:https://launchpad.support.sap.com/#/notes/1900823
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2593824]:https://launchpad.support.sap.com/#/notes/2593824
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

Ez a cikk azt ismerteti, hogyan konfigurálható a SAP HANA rendszerreplikáció a felskálás telepítésben, ha a HANA-fájlrendszerek NFS-en keresztül vannak csatlakoztatva az Azure NetApp Files (ANF) használatával. A példakonfigurációkban és telepítési parancsokban a **03-as** példányszám és a HANA **HN1** rendszerazonosító van használva. SAP HANA Replikáció egy elsődleges csomópontból és legalább egy másodlagos csomópontból áll.

Ha a dokumentum lépései a következő előtagokkal vannak megjelölve, a jelentés a következő:

- **[A]**: A lépés az összes csomópontra vonatkozik
- **[1]**: A lépés csak a node1 csomópontra vonatkozik
- **[2]**: A lépés csak a node2 csomópontra vonatkozik
 
Először olvassa el a következő SAP-megjegyzéseket és -dokumentumokat:

- SAP Note [1928533 ,](https://launchpad.support.sap.com/#/notes/1928533)amely a következővel rendelkezik:
    - Az SAP-szoftverek üzembe helyezéséhez támogatott Azure-beli virtuálisgép-méretek listája.
    - Fontos kapacitásinformációk az Azure-beli virtuális gépek méretével kapcsolatban.
    - A támogatott SAP-szoftverek, valamint az operációs rendszer (OS) és adatbázis-kombinációk.
    - A windowsos és linuxos gépekhez szükséges SAP kernelverzió Microsoft Azure.
- Az SAP Note [2015553 az](https://launchpad.support.sap.com/#/notes/2015553) Azure-beli SAP által támogatott SAP-szoftvertelepítések előfeltételeit sorolja fel.
- Az SAP Note [405827 a](https://launchpad.support.sap.com/#/notes/405827) HANA-környezethez ajánlott fájlrendszert sorolja fel.
- Az SAP Note [2002167 ajánlott](https://launchpad.support.sap.com/#/notes/2002167) operációsrendszer-beállításokat Red Hat Enterprise Linux.
- Az SAP Note [2009879](https://launchpad.support.sap.com/#/notes/2009879) SAP HANA útmutatót tartalmaz a Red Hat Enterprise Linux.
- Az SAP [Note 2178632](https://launchpad.support.sap.com/#/notes/2178632) részletes információkat tartalmaz az Azure-beli SAP-hez jelentett összes monitorozási metrikákról.
- Az SAP Note [2191498 rendelkezik](https://launchpad.support.sap.com/#/notes/2191498) a Linuxhoz szükséges SAP Host Agent-verzióval az Azure-ban.
- Az SAP [Note 2243692 az](https://launchpad.support.sap.com/#/notes/2243692) Azure-beli Linuxon való SAP-licencelésről tartalmaz információkat.
- Az SAP Note [1999351](https://launchpad.support.sap.com/#/notes/1999351) további hibaelhárítási információkat tartalmaz az SAP Azure Továbbfejlesztett monitorozási bővítményével kapcsolatban.
- [Az SAP Community Wiki a](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) Linuxhoz szükséges SAP-megjegyzésekkel rendelkezik.
- [Azure Virtual Machines SAP tervezése és implementációja Linuxon][planning-guide]
- [Azure Virtual Machines üzembe helyezése Linuxon, SAP-hez][deployment-guide]
- [Azure Virtual Machines DBMS üzembe helyezése Linuxon és SAP-ben][dbms-guide]
- [SAP HANA rendszerreplikációt a Pacemaker-fürtben.](https://access.redhat.com/articles/3004101)
- Általános RHEL-dokumentáció
    - [Magas rendelkezésre állású Add-On áttekintése](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
    - [Magas rendelkezésre állású Add-On felügyelet.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
    - [Magas rendelkezésre állású Add-On referencia.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
    - [A SAP HANA konfigurálása egy PacemakerScale-Up fürtben, ha a HANA-fájlrendszerek NFS-megosztáson vannak](https://access.redhat.com/solutions/5156571)
- Azure-specifikus RHEL-dokumentáció:
    - [Támogatási szabályzatok az RHEL magas rendelkezésre állású fürtökhöz – Microsoft Azure Virtual Machines fürttagok.](https://access.redhat.com/articles/3131341)
    - [Fürt telepítése és konfigurálása Red Hat Enterprise Linux 7.4-es (és újabb) High-Availability a Microsoft Azure.](https://access.redhat.com/articles/3252491)
    - [Telepítse SAP HANA a Red Hat Enterprise Linux a Microsoft Azure.](https://access.redhat.com/solutions/3193782)
    - [A SAP HANA felskálásos rendszerreplikáció konfigurálása Pacemaker-fürtre, ha a HANA-fájlrendszerek NFS-megosztáson vannak](https://access.redhat.com/solutions/5156571)
- [NetApp SAP Applications on Microsoft Azure using Azure NetApp Files](https://www.netapp.com/us/media/tr-4746.pdf)
- [NFS 4.1-es verziójú kötetek az SAP HANA-hoz készült Azure NetApp Filesban](./hana-vm-operations-netapp.md)

## <a name="overview"></a>Áttekintés

A felskálásos környezetben hagyományosan az összes fájlrendszer SAP HANA helyi tárolóból vannak csatlakoztatva. A SAP HANA magas rendelkezésre állásának beállítását Red Hat Enterprise Linux A rendszerreplikáció beállítása az [RHEL-SAP HANA útmutatóban](./sap-hana-high-availability-rhel.md)

Az [Azure NetApp Files](../../../azure-netapp-files/index.yml) NFS-megosztások felskálásrendszerének magas rendelkezésre állásának eléréséhez további erőforrás-konfigurációra van szükség SAP HANA fürtben a HANA-erőforrások helyreállításához, amikor egy csomópont elveszíti az NFS-megosztások hozzáférését az ANF-ben.  A fürt kezeli az NFS-csatlakoztatásokat, így lehetővé teszi az erőforrások állapotának figyelése. A fájlrendszer-csatlakoztatások és a SAP HANA függőségek kényszerítve vannak.  

![SAP HANA HA felskálás az ANF-on](./media/sap-hana-high-availability-rhel/sap-hana-scale-up-netapp-files-red-hat.png)

SAP HANA fájlrendszerek NFS-megosztáshoz vannak csatlakoztatva az egyes csomópontok Azure NetApp Files használatával. A /hana/data, a /hana/log és a /hana/shared fájlrendszerek minden csomóponton egyediek. 

A node1 csomóponthoz csatlakoztatva (**hanadb1**)

- 10.32.2.4:/**hanadb1**-data-mnt00001 on /hana/data
- 10.32.2.4:/**hanadb1**-log-mnt00001 a /hana/log fájlban
- 10.32.2.4:/**hanadb1**-shared-mnt00001 on /hana/shared

A node2 csomóponthoz csatlakoztatva (**hanadb2**)

- 10.32.2.4:/**hanadb2**-data-mnt00001 on /hana/data
- 10.32.2.4:/**hanadb2**-log-mnt00001 a /hana/log fájlban
- 10.32.2.4:/**hanadb2**-shared-mnt00001 on /hana/shared

> [!NOTE]
> A /hana/shared, /hana/data és /hana/log fájlrendszerek nincsenek megosztva a két csomópont között. Minden fürtcsomópont saját, különálló fájlrendszerekkel rendelkezik.   

A SAP HANA rendszerreplikációs konfiguráció dedikált virtuális állomásnevet és virtuális IP-címeket használ. Az Azure-ban terheléselosztás szükséges a virtuális IP-címek használatára. Az alábbi lista a terheléselosztás konfigurációját mutatja be:

- Előoldali konfiguráció: hn1-db IP-címe: 10.32.0.10
- Háttér-konfiguráció: A HANA rendszerreplikáció részét képezi összes virtuális gép elsődleges hálózati adapteréhez csatlakoztatva
- Mintavételi port: 62503-as port
- Terheléselosztási szabályok: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP (alapszintű Azure Load Balancer használata esetén)  

## <a name="set-up-the-azure-netapp-file-infrastructure"></a>Az Azure NetApp File-infrastruktúra beállítása

Mielőtt folytatja az infrastruktúra Azure NetApp Files, ismerkedjen meg az Azure [NetApp Files dokumentációval.](../../../azure-netapp-files/index.yml)

Azure NetApp Files azure-régióban érhető [el.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp) Ellenőrizze, hogy a kiválasztott Azure-régió kínál-e Azure NetApp Files.

További információ az Azure-régiók Azure NetApp Files elérhetőségről: Azure NetApp Files [azure-régiónként.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)

Mielőtt üzembe helyezné Azure NetApp Files, kérje az Azure NetApp Files regisztrációját a [Regisztrálás Azure NetApp Files útmutatás alapján.](../../../azure-netapp-files/azure-netapp-files-register.md)

### <a name="deploy-azure-netapp-files-resources"></a>Erőforrások Azure NetApp Files üzembe helyezése

Az alábbi utasítások feltételezik, hogy már üzembe helyezett [azure-beli virtuális hálózatot.](../../../virtual-network/virtual-networks-overview.md) A Azure NetApp Files erőforrásokat és virtuális gépeket, amelyekhez a Azure NetApp Files-erőforrások csatlakoztatva lesznek, ugyanazon az Azure-beli virtuális hálózaton vagy társviszonyban álló Azure-beli virtuális hálózatokon kell üzembe helyezni.

1. Ha még nem telepítette az erőforrásokat, kérje az [Azure NetApp Files.](../../../azure-netapp-files/azure-netapp-files-register.md)

2. Hozzon létre egy NetApp-fiókot a kiválasztott Azure-régióban a NetApp-fiók létrehozása [útmutatását követve.](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)

3.  Állítson be egy Azure NetApp Files-kapacitáskészletet az Új [kapacitáskészlet beállítása Azure NetApp Files útmutatását követve.](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)

    A cikkben bemutatott HANA-architektúra egyetlen Azure NetApp Files az *Ultra* Service szintjén. HaNA számítási feladatokhoz az Azure-ban javasoljuk  a Azure NetApp Files *vagy* Prémium [szolgáltatási szint használatát.](../../../azure-netapp-files/azure-netapp-files-service-levels.md)

4.  Delegálhat egy alhálózatot a Azure NetApp Files számára [](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)az Alhálózat delegálásának a következőre: Azure NetApp Files.

5.  Telepítse Azure NetApp Files köteteket az [NFS-kötet](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)létrehozása a Azure NetApp Files.

    A kötetek üzembe helyezése után válassza az NFSv4.1 verziót. Telepítse a kijelölt alhálózaton Azure NetApp Files köteteket. Az Azure NetApp-kötetek IP-címei automatikusan ki vannak osztva.

    Ne feledje, hogy a Azure NetApp Files erőforrásoknak és az Azure-beli virtuális gépeknek ugyanabban az Azure-beli virtuális hálózatban vagy társviszonyban álló Azure-beli virtuális hálózatokban kell lennie. A hanadb1-data-mnt00001, hanadb1-log-mnt00001 stb. például a kötetek neve és a nfs://10.32.2.4/hanadb1-data-mnt00001, nfs://10.32.2.4/hanadb1-log-mnt00001 stb. a Azure NetApp Files-kötetek elérési útjai.
    
    On **hanadb1**

    - Volume hanadb1-data-mnt00001 (nfs://10.32.2.4:/hanadb1-data-mnt00001) 
    - Volume hanadb1-log-mnt00001 (nfs://10.32.2.4:/hanadb1-log-mnt00001)
    - Volume hanadb1-shared-mnt00001 (nfs://10.32.2.4:/hanadb1-shared-mnt00001)
    
    **Hanadb2-n**

    - Volume hanadb2-data-mnt00001 (nfs://10.32.2.4:/hanadb2-data-mnt00001)
    - Volume hanadb2-log-mnt00001 (nfs://10.32.2.4:/hanadb2-log-mnt00001)
    - Volume hanadb2-shared-mnt00001 (nfs://10.32.2.4:/hanadb2-shared-mnt00001)

### <a name="important-considerations"></a>Fontos szempontok

Az új Azure NetApp Files létrehozásakor SAP HANA a következő szempontot:

- A minimális kapacitáskészlet 4 tebibájt (TiB).
- A kötet minimális mérete 100 gibibájt (GiB).
- Azure NetApp Files virtuális gépeknek és az összes olyan virtuális gépnek, Azure NetApp Files köteteket csatlakoztatni [](../../../virtual-network/virtual-network-peering-overview.md) kell ugyanannak az Azure-beli virtuális hálózatnak vagy társviszonyban lévő virtuális hálózatoknak ugyanabban a régióban.
- A kiválasztott virtuális hálózatnak olyan alhálózattal kell lennie, amely delegálva van a Azure NetApp Files.
- A kötetkötet átviteli Azure NetApp Files a kötetkvóta és a szolgáltatási szint függvénye, amint az a szolgáltatásszintről [Azure NetApp Files.](../../../azure-netapp-files/azure-netapp-files-service-levels.md) A HANA Azure NetApp-kötetek méretezésekor győződjön meg arról, hogy az eredményül kapott átviteli sebesség megfelel a HANA rendszerkövetelményeinek.
- A Azure NetApp Files [házirendben](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)szabályozhatja az engedélyezett ügyfeleket, a hozzáférés típusát (írási-olvasási, írási és egyéb) stb.
- Az Azure NetApp Files funkció még nem zónatűnnek számít. A szolgáltatás jelenleg nem minden rendelkezésre állási zónában van telepítve egy Azure-régióban. Néhány Azure-régióban vegye figyelembe a lehetséges késési következményeket.

> [!IMPORTANT]
> A SAP HANA esetében az alacsony késés kritikus fontosságú. A Microsoft képviselőjével együtt ellenőrizze, hogy a virtuális gépek és Azure NetApp Files kötetek közelségben vannak-e telepítve.

### <a name="sizing-of-hana-database-on-azure-netapp-files"></a>A HANA-adatbázis méretezése a Azure NetApp Files

A kötetkötet átviteli Azure NetApp Files a kötet méretének és szolgáltatási szintjének függvénye, ahogyan az a kötetek szolgáltatási szintje [Azure NetApp Files.](../../../azure-netapp-files/azure-netapp-files-service-levels.md)

Az SAP azure-beli infrastruktúrájának tervezése során vegye figyelembe az SAP néhány minimális tárolási követelményét, amelyek a minimális átviteli sebesség jellemzőire vonatkoznak:

- Írás/hana/250 megabájt/másodperces (MB/s) naplóírás 1 MB I/O-mérettel.
- Legalább 400 MB/s olvasási tevékenység /hana/data esetén 16 MB és 64 MB I/O-méret esetén.
- Írási tevékenység legalább 250 MB/s /hana/data esetén 16 MB és 64 MB I/O mérettel.

A [Azure NetApp Files kvóta](../../../azure-netapp-files/azure-netapp-files-service-levels.md) 1 TiB-os korlátja a következő:

- Premium Storage réteg – 64 MiB/s.
- Ultra szintű tárolás – 128 MiB/s.

A /hana/data és a /hana/log SAP minimális átviteli sebességre vonatkozó követelményeinek, valamint a /hana/shared irányelveinek való megfelelés érdekében a javasolt méretek a következőek:

|    Kötet    | A Premium Storage mérete | Ultra szintű tárolási szint mérete | Támogatott NFS-protokoll |
| :----------: | :--------------------------: | :------------------------: | :--------------------: |
|  /hana/log   |            4 TiB             |           2 TiB            |          v4.1          |
|  /hana/data  |           6,3 TiB            |          3,2 TiB           |          v4.1          |
| /hana/shared |           1 db RAM            |          1 db RAM           |          v3 vagy v4.1    |


> [!NOTE]
> Az Azure NetApp Files javasolt méretezési javaslatok célja az SAP által az infrastruktúra-szolgáltatók számára javasolt minimális követelményeknek való megfelelés. Valós ügyféltelepítések és számítási feladatok esetén előfordulhat, hogy ezek a méretek nem elegendőek. Ezeket a javaslatokat kiindulópontként használhatja, és az adott számítási feladat követelményeinek megfelelően alkalmazkodik.

> [!TIP]
> A köteteket dinamikusan Azure NetApp Files átméretezheti  anélkül, hogy le kell leválasztani a köteteket, leállíthatja a virtuális gépeket, vagy leállíthatja SAP HANA. Ez a megközelítés rugalmasságot tesz lehetővé az alkalmazás várható és előre nem látható átviteli sebességi követelményeinek való megfelelőség érdekében.

> [!NOTE]
> A cikkben a /hana/shared csatlakoztatásra vonatkozó összes parancs az NFSv4.1 /hana/megosztott kötetek számára van bemutatva.
> Ha a /hana/megosztott köteteket NFSv3-kötetként telepítette, ne felejtse el módosítani az NFSv3 /hana/shared csatlakoztatási parancsát.


## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Linux rendszerű virtuális gép üzembe helyezése Azure Portal 

Először létre kell hoznia a Azure NetApp Files köteteket. Ezután tegye a következőket:

1.  Hozzon létre egy erőforráscsoportot.
2.  Hozzon létre egy virtuális hálózatot.
3.  Rendelkezésre állási csoport létrehozása. Állítsa be a maximális frissítési tartományt.
4.  Hozzon létre egy terheléselosztást (belső). A standard terheléselosztást javasoljuk.
    Válassza ki a 2. lépésben létrehozott virtuális hálózatot.
5.  Hozza létre az 1. virtuális gépet (**hanadb1**). 
6.  Hozza létre a 2. virtuális gépet (**hanadb2**).  
7.  A virtuális gép létrehozása során nem adunk hozzá lemezt, mivel az összes csatlakoztatási pont a virtuális gépek NFS-megosztásán Azure NetApp Files. 

> [!IMPORTANT]
> A nem rögzített IP-cím terheléselosztási forgatókönyvekben nem támogatott a hálózati adapterek másodlagos IP-konfigurációiban. Részletekért lásd: [Az Azure Load Balancer korlátozásai.](../../../load-balancer/load-balancer-multivip-overview.md#limitations) Ha további IP-címre van szüksége a virtuális géphez, telepítsen egy második hálózati adaptert.    

> [!NOTE] 
> Ha a nyilvános IP-címekkel nem rendelkezik virtuális gépeket a belső (nyilvános IP-cím nélküli) Standard Azure-terheléselosztás háttérkészletében helyezi el, nem lesz kimenő internetkapcsolat, kivéve, ha további konfigurációt végez a nyilvános végpontok felé irányuló útválasztás engedélyezése érdekében. A kimenő kapcsolatok elérésével kapcsolatos részletekért lásd: Nyilvános végponti kapcsolat Virtual Machines Azure standard Load Balancer SAP magas rendelkezésre [állású forgatókönyvekben.](./high-availability-guide-standard-load-balancer-outbound-connections.md)

8.  Ha standard terheléselosztást használ, kövesse az alábbi konfigurációs lépéseket:
    1.  Először hozzon létre egy előoldali IP-címkészletet:
        1.  Nyissa meg a terheléselosztást, válassza ki **az előtere IP-készletet,** majd válassza a **Hozzáadás lehetőséget.**
        1.  Adja meg az új előtere IP-címkészlet nevét (például **hana-frontend).**
        1.  Állítsa a **Hozzárendelést** **Statikusra,** és adja meg az IP-címet (például **10.32.0.10).**
        1.  Válassza az **OK** lehetőséget.
        1.  Az új előoldali IP-címkészlet létrehozása után jegyezze fel a készlet IP-címét.
    1.  Ezután hozzon létre egy háttérkészletet:
        1.  Nyissa meg a terheléselosztást, válassza ki **a háttérkészleteket,** majd válassza a **Hozzáadás lehetőséget.**
        1.  Adja meg az új háttérkészlet nevét (például **hana-backend).**
        1.  Válassza **a Virtuális gép hozzáadása lehetőséget.**
        1.  Válassza a ** Virtuális gép** lehetőséget.
        1.  Válassza ki a virtuális gépeket a SAP HANA és azok IP-címeit.
        1.  Válassza a **Hozzáadás** lehetőséget.
    1.  Ezután hozzon létre egy állapot-mintavételt:
        1.  Nyissa meg a terheléselosztást, válassza **az állapot-mintavételek** lehetőséget, majd válassza a **Hozzáadás lehetőséget.**
        1.  Adja meg az új állapot-mintavétel nevét (például **hana-hp).**
        1.  Válassza a TCP protokollt és a 625 **03-as portot.** Az **Intervallum értékét** állítsa 5-re, a Nem megfelelő értékek küszöbértékét pedig 2-re. 
        1.  Válassza az **OK** lehetőséget.
    1.  Ezután hozza létre a terheléselosztási szabályokat:
        1.  Nyissa meg a terheléselosztást, válassza a **terheléselosztási szabályok lehetőséget,** majd kattintson a **Hozzáadás gombra.**
        1.  Adja meg az új terheléselosztási szabály nevét (például **hana-lb).**
        1.  Válassza ki az előtere IP-címét, a háttérkészletet és a korábban létrehozott állapot-mintavételt (például **hana-frontend,** **hana-backend** és **hana-hp).**
        1.  Válassza a **HA portok lehetőséget.**
        1.  Ügyeljen arra, hogy **engedélyezze a nem rögzített IP-címet.**
        1.  Válassza az **OK** lehetőséget.


9. Ha az Ön forgatókönyve alapszintű terheléselosztást is előír, kövesse az alábbi konfigurációs lépéseket:
    1.  Konfigurálja a terheléselosztást. Először hozzon létre egy előoldali IP-címkészletet:
        1.  Nyissa meg a terheléselosztást, válassza ki **az előtere IP-készletet,** majd válassza a **Hozzáadás lehetőséget.**
        1.  Adja meg az új előtere IP-címkészlet nevét (például **hana-frontend).**
        1.  Állítsa a **Hozzárendelést** **Statikusra,** és adja meg az IP-címet (például **10.32.0.10).**
        1.  Válassza az **OK** lehetőséget.
        1.  Az új előoldali IP-címkészlet létrehozása után jegyezze fel a készlet IP-címét.
    1.  Ezután hozzon létre egy háttérkészletet:
        1.  Nyissa meg a terheléselosztást, válassza ki a **háttérkészleteket,** majd válassza a **Hozzáadás lehetőséget.**
        1.  Adja meg az új háttérkészlet nevét (például **hana-backend).**
        1.  Válassza **a Virtuális gép hozzáadása lehetőséget.**
        1.  Válassza ki a 3. lépésben létrehozott rendelkezésre állási készletet.
        1.  Válassza ki az SAP HANA virtuális gépeit.
        1.  Válassza az **OK** lehetőséget.
    1.  Ezután hozzon létre egy állapot-mintavételt:
        1.  Nyissa meg a terheléselosztást, válassza **az Állapot-mintavételek,** majd a Hozzáadás **lehetőséget.**
        1.  Adja meg az új állapot-mintavétel nevét (például **hana-hp).**
        1.  Válassza **a TCP** protokollt és a 625 **03-as portot.** Az **Intervallum értéke** maradjon 5, a Nem megfelelő érték küszöbértéke pedig 2. 
        1.  Válassza az **OK** lehetőséget.
    1.  Az SAP HANA 1.0 esetén hozza létre a terheléselosztási szabályokat:
        1.  Nyissa meg a terheléselosztást, válassza ki **a terheléselosztási szabályokat,** majd válassza a **Hozzáadás lehetőséget.**
        1.  Adja meg az új terheléselosztási szabály nevét (például hana-lb-3 **03** 15).
        1.  Válassza ki az előtere IP-címét, a háttérkészletet és a korábban létrehozott állapot-mintavételt (például **hana-frontend).**
        1.  A Protokoll **beállításnál** tartsa meg a **TCP-t,** és adja meg a 3 **03** 15-ös portot.
        1.  Növelje az **üresjárati időkorlátot** 30 percre.
        1.  Ügyeljen arra, hogy **engedélyezze a nem rögzített IP-címet.**
        1.  Válassza az **OK** lehetőséget.
        1.  Ismételje meg ezeket a lépéseket a 3 **03** 17-es porton.
    1.  A SAP HANA 2.0 esetén hozza létre a terheléselosztási szabályokat a rendszeradatbázishoz:
        1.  Nyissa meg a terheléselosztást, válassza a **terheléselosztási szabályok lehetőséget,** majd válassza a **Hozzáadás lehetőséget.**
        1.  Adja meg az új terheléselosztási szabály nevét (például hana-lb-3 **03** 13).
        1.  Válassza ki az előtere IP-címét, a háttérkészletet és a korábban létrehozott állapot-mintavételt (például **hana-frontend).**
        1.  A Protokoll **beállításnál tartsa** meg a **TCP-t,** és adja meg a 3 **03** 13-as portot.
        1.  Növelje az **üresjárati időkorlátot** 30 percre.
        1.  Ügyeljen arra, hogy **engedélyezze a nem rögzített IP-címet.**
        1.  Válassza az **OK** lehetőséget.
        1.  Ismételje meg ezeket a lépéseket a 3 **03** 14-es porton.
    1.  A SAP HANA 2.0 esetén először hozza létre a terheléselosztási szabályokat a bérlői adatbázishoz:
        1.  Nyissa meg a terheléselosztást, válassza a **terheléselosztási szabályok lehetőséget,** majd válassza a **Hozzáadás lehetőséget.**
        1.  Adja meg az új terheléselosztási szabály nevét (például hana-lb-3 **03** 40).
        1.  Válassza ki a korábban létrehozott előtere IP-címet, háttérkészletet és állapot-mintavételt (például **hana-frontend).**
        1.  A Protokoll **beállításnál tartsa** meg a **TCP-t,** és adja meg a 3 **03** 40-es portot.
        1.  Növelje az **üresjárati időkorlátot** 30 percre.
        1.  Ügyeljen arra, hogy **engedélyezze a nem rögzített IP-címet.**
        1.  Válassza az **OK** lehetőséget.
        1.  Ismételje meg ezeket a lépéseket a 3 **03** 41-es és a **303 42-es** porton is.

Az alkalmazáshoz szükséges portokkal kapcsolatos további SAP HANA az [SAP HANA Tenant Databases](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) (Bérlői adatbázisok) útmutató Connections to Tenant [Databases](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) (Bérlői adatbázisokhoz való csatlakozás) fejezetében vagy az SAP [2388694-es](https://launchpad.support.sap.com/#/notes/2388694)megjegyzésében olvashat.

> [!IMPORTANT]
> Ne engedélyezze a TCP-időbélyegeket az azure-beli virtuális gépeken a Azure Load Balancer. A TCP-időbélyegek engedélyezése az állapot-mintavételek sikertelen lesz. Állítsa a **paraméter net.ipv4.tcp_timestamps** **értékét 0-ra.** Részletekért [lásd: Load Balancer állapot-mintavételek.](../../../load-balancer/load-balancer-custom-probe-overview.md) Lásd még az SAP [2382421 megjegyzését.](https://launchpad.support.sap.com/#/notes/2382421)

## <a name="mount-the-azure-netapp-files-volume"></a>A Azure NetApp Files csatlakoztatása

1. **[A]** Csatlakozási pontokat hoz létre a HANA-adatbáziskötethez. 

    ```
    mkdir -p /hana/data
    mkdir -p /hana/log
    mkdir -p /hana/shared
    ```

2. **[A] Ellenőrizze** az NFS-tartománybeállítást. Győződjön meg arról, hogy a tartomány alapértelmezett tartományként van  Azure NetApp Files, például a defaultv4iddomain.com, a leképezés pedig senkire nincs **beállítva.**

    ```
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

    > [!IMPORTANT]    
    > Ügyeljen arra, hogy a virtuális gépen az /etc/idmapd.conf fájlban található NFS-tartomány megfeleljen az alapértelmezett tartománykonfigurációnak az Azure NetApp Files: **defaultv4iddomain.com.** Ha eltérés van az NFS-ügyfélen (például a virtuális gépen) és az NFS-kiszolgálón, például az Azure NetApp-konfigurációban található tartománykonfigurációk között, akkor a virtuális gépekhez csatlakoztatott Azure NetApp-kötetek fájljainak engedélyei senkiként jelennek meg.
    

3. **[1]** Csomópont-specifikus kötetek csatlakoztatása a node1 csomópontra (**hanadb1**) 

    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-data-mnt00001 /hana/data
    ```
    
4.  **[2]** Csomópont-specifikus kötetek csatlakoztatása a node2-n (**hanadb2**)
    
    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-data-mnt00001 /hana/data
    ```

5. **[A]** Ellenőrizze, hogy az összes HANA-kötet csatlakoztatva van-e az NFS protokoll NFSv4-verziójával.

    ```
    sudo nfsstat -m
    
    # Verify that flag vers is set to 4.1 
    # Example from hanadb1
    
    /hana/log from 10.32.2.4:/hanadb1-log-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    /hana/data from 10.32.2.4:/hanadb1-data-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    /hana/shared from 10.32.2.4:/hanadb1-shared-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    ```

6. **[A]** Ellenőrizze a **nfs4_disable_idmapping.** A beállításnak **Y-nak kell lennie.** A könyvtárstruktúra létrehozásához, **nfs4_disable_idmapping** a csatlakoztatási parancsot. A /sys/modules mappában nem hozhatja létre manuálisan a könyvtárat, mert a hozzáférés a kernel/illesztőprogramok számára van fenntartva.

    ```
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # If you need to set nfs4_disable_idmapping to Y
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

   További részletek a paraméter **nfs_disable_idmapping:** [https://access.redhat.com/solutions/1749883](https://access.redhat.com/solutions/1749883) . 


## <a name="sap-hana-installation"></a>SAP HANA telepítése

1. **[A]** Állítsa be a gazdagépnévfeloldás beállítását az összes gazdagéphez.

   Használhat DNS-kiszolgálót, vagy módosíthatja az /etc/hosts fájlt az összes csomóponton. Ez a példa bemutatja, hogyan használhatja az /etc/hosts fájlt. Cserélje le az IP-címet és az állomásnevet a következő parancsokban:

   ```
   sudo vi /etc/hosts
   # Insert the following lines in the /etc/hosts file. Change the IP address and hostname to match your environment  
   10.32.0.4   hanadb1
   10.32.0.5   hanadb2
   ```

2. **[A]** RHEL a HANA-konfigurációhoz

   Konfigurálja az RHEL-t az alábbi SAP-megjegyzésben leírt módon az RHEL-verzió alapján

   - [2292690 – SAP HANA DB: Az RHEL 7 ajánlott operációsrendszer-beállításai](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782 – SAP HANA DB: Az RHEL 8 ajánlott operációsrendszer-beállításai](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582 – Linux: GCC 6.x-sel lefordított SAP-alkalmazások futtatása](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824 – Linux: GCC 7.x-sel lefordított SAP-alkalmazások futtatása](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607 – Linux: GCC 9.x-sel lefordított SAP-alkalmazások futtatása](https://launchpad.support.sap.com/#/notes/2886607)

3. **[A]** A SAP HANA

   A HANA 2.0 SPS 01-ben indított MDC az alapértelmezett beállítás. HANA-rendszer telepítésekor a SYSTEMDB és egy bérlő azonos SID azonosítóval együtt jön létre. Bizonyos esetekben nem szeretné az alapértelmezett bérlőt használni. Ha a telepítéssel együtt nem szeretné létrehozni a kezdeti bérlőt, kövesse az SAP [2629711 megjegyzését](https://launchpad.support.sap.com/#/notes/2629711)

    Futtassa **a hdblcm** programot a HANA DVD-ről. Írja be a következő értékeket a parancssorba:  
    Telepítés kiválasztása: Adja meg **az 1-et** (telepítéshez)  
    Válassza ki a telepítéshez szükséges további összetevőket: Adja meg **az 1-et.**  
    Adja meg a telepítési útvonalat [/hana/shared]: nyomja le az Enter billentyűt az alapértelmezett érték elfogadásához  
    Adja meg a Helyi állomásnév [..]: Az alapértelmezett érték elfogadásához nyomja le az Enter billentyűt  
    Szeretne további gazdagépeket hozzáadni a rendszerhez? (y/n) [n]: **n**  
    Adja SAP HANA rendszerazonosítót: Adja meg a **HN1-et.**  
    Adja meg a példányszámot [00]: Adja meg a **03-ast**  
    Válassza az Adatbázis mód / Index beírása [1] lehetőséget: nyomja le az Enter billentyűt az alapértelmezett érték elfogadásához  
    Rendszerhasználat kiválasztása / Adja meg az indexet [4]: adja meg **a 4-et** (egyénileg)  
    Adja meg az adatkötetek helyét [/hana/data]: nyomja le az Enter billentyűt az alapértelmezett érték elfogadásához  
    Adja meg a naplókötetek helyét [/hana/log]: nyomja le az Enter billentyűt az alapértelmezett érték elfogadásához  
    Korlátozza a maximális memóriafoglalást? [n]: nyomja le az Enter billentyűt az alapértelmezett érték elfogadásához  
    Adja meg a "..." gazdagép tanúsítványgazdanevét :: nyomja le az Enter billentyűt az alapértelmezett érték elfogadásához  
    Adja meg az SAP-gazdaügynök felhasználója (sapadm) jelszavát: Adja meg a gazdaügynök felhasználói jelszavát  
    Az SAP-gazdaügynök felhasználója (sapadm) jelszavának megerősítése: Adja meg ismét a gazdaügynök felhasználói jelszavát a megerősítéshez  
    Adja meg a Rendszergazda (hn1adm) jelszavát: Adja meg a rendszergazda jelszavát  
    Rendszergazdai jelszó megerősítése (hn1adm): Adja meg ismét a rendszergazda jelszavát a megerősítéshez  
    Adja meg a rendszergazda kezdőkönyvtárát [/usr/sap/HN1/home]: nyomja le az Enter billentyűt az alapértelmezett érték elfogadásához  
    Adja meg a rendszergazdai bejelentkezési rendszerhéjat [/bin/sh]: nyomja le az Enter billentyűt az alapértelmezett beállítás elfogadásához  
    Adja meg a rendszergazdai felhasználói azonosítót [1001]: nyomja le az Enter billentyűt az alapértelmezett érték elfogadásához  
    Adja meg a felhasználói csoport azonosítóját (sapsys) [79]: nyomja le az Enter billentyűt az alapértelmezett érték elfogadásához  
    Adja meg az adatbázis-felhasználó (SYSTEM) jelszavát: Adja meg az adatbázis-felhasználó jelszavát  
    Adatbázis-felhasználó (SYSTEM) jelszavának megerősítése: Adja meg ismét az adatbázis-felhasználó jelszavát a megerősítéshez  
    Újraindítja a rendszert a gép újraindítása után? [n]: nyomja le az Enter billentyűt az alapértelmezett érték elfogadásához  
    Folytatja? (y/n): Ellenőrizze az összefoglalást. A **folytatáshoz írja be az y** billentyűt  

4. **[A]** Az SAP-gazdagépügynök frissítése

   Töltse le a legújabb SAP-gazdagépügynök archívumát az [SAP Szoftverközpontból,](https://launchpad.support.sap.com/#/softwarecenter) és futtassa a következő parancsot az ügynök frissítéséhez. Cserélje le az archívum elérési útját, hogy a letöltött fájlra mutasson:

   ```
   sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
   ```

5. **[A]** Tűzfal konfigurálása

   Hozza létre az Azure Load Balancer mintavételi portjának tűzfalszabályát.

   ```
   sudo firewall-cmd --zone=public --add-port=62503/tcp
   sudo firewall-cmd --zone=public --add-port=62503/tcp –permanent
   ```

## <a name="configure-sap-hana-system-replication"></a>A SAP HANA konfigurálása

A Rendszerreplikáció beállítása SAP HANA [lépéseit követve](./sap-hana-high-availability-rhel.md#configure-sap-hana-20-system-replication) konfigurálhatja SAP HANA rendszerreplikációt. 

## <a name="cluster-configuration"></a>Fürtkonfiguráció

Ez a szakasz a fürt zökkenőmentes működéséhez szükséges lépéseket ismerteti, SAP HANA NFS-megosztásra a fürtszolgáltatásokkal való Azure NetApp Files. 

### <a name="create-a-pacemaker-cluster"></a>Pacemaker-fürt létrehozása

A Pacemaker beállítása az [Azure-ban Red Hat Enterprise Linux](./high-availability-guide-rhel-pacemaker.md) lépéseit követve hozzon létre egy alapszintű Pacemaker-fürtöt ehhez a HANA-kiszolgálóhoz.

### <a name="implement-the-python-system-replication-hook-saphanasr"></a>A Python rendszerreplikációs hook SAPHanaSR implementációja

Ez fontos lépés a fürt integrációjának optimalizálásához és az észlelés javításához, amikor feladatátvételre van szükség. Erősen ajánlott az SAPHanaSR Python-hook konfigurálása.    

1. **[A] Telepítse** a HANA "rendszerreplikációs hookot". A hookot mindkét HANA DB-csomóponton telepíteni kell.           

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

### <a name="configure-filesystem-resources"></a>Fájlrendszererőforrások konfigurálása

Ebben a példában minden fürtcsomópont saját HANA NFS-fájlrendszerekkel rendelkezik :hana/shared, /hana/data és /hana/log.   

1. **[1]** Helyezze a fürtöt karbantartási módba.

   ```
   pcs property set maintenance-mode=true
   ```

2. **[1]** Hozza létre a fájlrendszer erőforrásait a **hanadb1 csatlakoztatáshoz.**

    ```
    pcs resource create hana_data1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_log1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_shared1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    ```

3. **[2]** Hozza létre a fájlrendszer erőforrásait a **hanadb2-csatlakoztatáshoz.**

    ```
    pcs resource create hana_data2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_log2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_shared2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    ```

    `OCF_CHECK_LEVEL=20` A rendszer hozzáadja az attribútumot a figyelőművelethez, így minden figyelő olvasási/írási tesztet hajt végre a fájlrendszeren. Ezen attribútum nélkül a figyelő művelet csak azt ellenőrzi, hogy a fájlrendszer csatlakoztatva van-e. Ez problémát okozhat, mert ha megszakad a kapcsolat, a fájlrendszer csatlakoztatva maradhat annak ellenére, hogy nem érhető el.

    `on-fail=fence` A attribútumot a figyelő művelethez is hozzáadja a rendszer. Ezzel a beállítással, ha a figyelőművelet meghiúsul egy csomóponton, a csomópontot a rendszer azonnal körülkerítéssel körülveszi. E beállítás nélkül az alapértelmezett viselkedés az, hogy leállítja a meghibásodott erőforrástól függő összes erőforrást, majd újraindítja a meghibásodott erőforrást, majd elindítja a meghibásodott erőforrástól függő összes erőforrást. Ez a viselkedés nem csak akkor tart sokáig, ha egy SAPHana-erőforrás a meghibásodott erőforrástól függ, de teljesen meghiúsulhat is. Az SAPHana erőforrás nem állítható le sikeresen, ha a HANA végrehajtható fájlokat futtató NFS-kiszolgáló nem érhető el.

4. **[1]** Helykorlátozások konfigurálása

   Helykorlátozások konfigurálása annak biztosításához, hogy a hanadb1 egyedi csatlakoztatásokat kezelő erőforrások soha ne fusson a hanadb2-n, és fordítva.

    ```
    pcs constraint location hanadb1_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb2
    pcs constraint location hanadb2_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb1
    ```

    A beállítás azért van beállítva, mert az egyes csomópontok egyedi csatlakoztatásai `resource-discovery=never` ugyanazon a csatlakoztatási ponton osztoznak. A például `hana_data1` a csatlakoztatási pontot, a pedig a `/hana/data` `hana_data2` csatlakoztatási pontot `/hana/data` használja. Ez téves pozitív eredményt okozhat egy mintavételi műveletnél, ha a fürt indításakor ellenőrzi az erőforrás állapotát, és ez szükségtelen helyreállítási viselkedést okozhat. Ez elkerülhető a következő beállítással: `resource-discovery=never`

5. **[1]** Attribútum-erőforrások konfigurálása

   Attribútum-erőforrások konfigurálása. Ezek az attribútumok igazra lesznek állítva, ha egy csomópont összes NFS-csatlakoztatása (/hana/data, /hana/log és /hana/data) csatlakoztatva van, egyébként pedig false (hamis) lesz.

   ```
   pcs resource create hana_nfs1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs1_active
   pcs resource create hana_nfs2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs2_active
   ```

6. **[1]** Helykorlátozások konfigurálása

   Helykorlátozások konfigurálása annak biztosításához, hogy a hanadb1 attribútum-erőforrása soha ne futjon a hanadb2-n, és fordítva.

   ```
   pcs constraint location hana_nfs1_active avoids hanadb2
   pcs constraint location hana_nfs2_active avoids hanadb1
   ```

7. **[1]** Rendelési korlátozások létrehozása

   Konfigurálja a rendelési korlátozásokat úgy, hogy a csomópont attribútum-erőforrásai csak a csomópont összes NFS-csatlakoztatásának csatlakoztatása után indulnak el.
   
    ```
    pcs constraint order hanadb1_nfs then hana_nfs1_active
    pcs constraint order hanadb2_nfs then hana_nfs2_active
    ```

   > [!TIP]
   > Ha a konfiguráció a csoporton vagy a rendszeren kívüli fájlrendszereket is tartalmaz, akkor használja a kapcsolót, hogy a fájlrendszerek között ne szerepeljenek rendezésre `hanadb1_nfs` `hanadb2_nfs` vonatkozó `sequential=false` függőségek. Minden fájlrendszernek a előtt kell kezdődnie, de nem kell egymáshoz képest semmilyen sorrendben `hana_nfs1_active` elindulni. További részletekért lásd: Hogyan a SAP HANA-replikáció konfigurálása egy PacemakerScale-Up fürtben, ha [a HANA-fájlrendszerek NFS-megosztáson vannak](https://access.redhat.com/solutions/5156571)

### <a name="configure-sap-hana-cluster-resources"></a>Fürterőforrások SAP HANA konfigurálása

1. Kövesse a [Fürterőforrások létrehozása SAP HANA lépéseit](./sap-hana-high-availability-rhel.md#create-sap-hana-cluster-resources) a fürtben SAP HANA erőforrások létrehozásához. Az SAP HANA létrehozása után létre kell hoznunk egy helyszabály-korlátozást a SAP HANA és fájlrendszerek (NFS-csatlakoztatások) között.

2. **[1]** A hálózati erőforrások és az NFS SAP HANA közötti korlátozások konfigurálása

   A helyszabály megkötései úgy lesznek beállítva, hogy a SAP HANA-erőforrások csak akkor futtassanak egy csomóponton, ha a csomópont összes NFS-csatlakoztatása csatlakoztatva van.

    ```
    pcs constraint location SAPHanaTopology_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # On RHEL 7.x
    pcs constraint location SAPHana_HN1_03-master rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # On RHEL 8.x
    pcs constraint location SAPHana_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # Take the cluster out of maintenance mode
    sudo pcs property set maintenance-mode=false
    ```

   A fürt és az összes erőforrás állapotának ellenőrzése
   > [!NOTE]
   > Ez a cikk a *slave* kifejezésre mutató hivatkozásokat tartalmaz, amely a Microsoft által már nem használt kifejezés. Amikor eltávolítjuk a kifejezést a szoftverből, eltávolítjuk a cikkből.
   
    ```
    sudo pcs status
    
    Online: [ hanadb1 hanadb2 ]
    
    Full list of resources:
    
    rsc_hdb_azr_agt(stonith:fence_azure_arm):  Started hanadb1
    
    Resource Group: hanadb1_nfs
    hana_data1 (ocf::heartbeat:Filesystem):Started hanadb1
    hana_log1  (ocf::heartbeat:Filesystem):Started hanadb1
    hana_shared1   (ocf::heartbeat:Filesystem):Started hanadb1
    
    Resource Group: hanadb2_nfs
    hana_data2 (ocf::heartbeat:Filesystem):Started hanadb2
    hana_log2  (ocf::heartbeat:Filesystem):Started hanadb2
    hana_shared2   (ocf::heartbeat:Filesystem):Started hanadb2
    
    hana_nfs1_active   (ocf::pacemaker:attribute): Started hanadb1
    hana_nfs2_active   (ocf::pacemaker:attribute): Started hanadb2
    
    Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hanadb1 hanadb2 ]
    
    Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hanadb1 ]
    Slaves: [ hanadb2 ]
    
    Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):  Started hanadb1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):   Started hanadb1
    ```

## <a name="configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster"></a>A HANA aktív/olvasható rendszerreplikációjának konfigurálása a Pacemaker-fürtben

Az SAP HANA 2.0 SPS 01-től kezdődően az SAP lehetővé teszi az aktív/olvasható beállítási beállításokat az SAP HANA rendszerreplikációhoz, ahol az SAP HANA rendszerreplikáció másodlagos rendszerei aktívan használhatók intenzív olvasási számítási feladatokhoz. Az ilyen beállítás fürtökben való támogatásához egy második virtuális IP-címre van szükség, amely lehetővé teszi az ügyfelek számára a másodlagos, olvasásra képes SAP HANA adatbázishoz való hozzáférést. Annak érdekében, hogy a másodlagos replikációs hely továbbra is elérhető legyen a feladatátvétel után, a fürtnek át kell vinnie a virtuális IP-címet az SAPHana erőforrás másodlagos tartományvezérlőjére.

A HANA aktív/olvasható rendszerreplikáció második virtuális IP-címmel rendelkező Red Hat magas rendelkezésre állású fürtökön való kezeléséhez szükséges további konfigurációt a [HANA aktív/olvasható](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster)rendszerreplikáció konfigurálása a Pacemaker-fürtben című cikk ismerteti.  

Mielőtt továbblépne, győződjön meg arról, hogy teljesen konfigurálta a Red Hat magas rendelkezésre állású fürtöt SAP HANA adatbázis kezeléséhez a dokumentáció fenti szegmenseiben leírtak szerint.    


## <a name="test-the-cluster-setup"></a>A fürt beállításának tesztelése

Ez a szakasz a beállítás tesztelését ismerteti. 

1. A teszt elkezdése előtt győződjön meg arról, hogy a Pacemaker nem rendelkezik sikertelen művelettel (a pcs állapotával), nincsenek váratlan helykorlátozások (például egy migrálási teszt maradékai), és hogy a HANA rendszerreplikációja szinkronizálási állapot, például systemReplicationStatus esetén:

    ```
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    ```

2. Ellenőrizze a fürtkonfigurációt olyan hiba esetén, amikor egy csomópont nem fér hozzá az NFS-megosztáshoz (/hana/shared)

   A SAP HANA erőforrások ügynökei bináris fájloktól függenek, amelyek a tárolóban vannak tárolva a feladatátvétel `/hana/shared` során a műveletek végrehajtásához. A bemutatott  `/hana/shared` forgatókönyvben a fájlrendszer NFS-n keresztül van csatlakoztatva.  
   Nehéz szimulálni egy hibát, amikor az egyik kiszolgáló elveszíti a hozzáférést az NFS-megosztáshoz. Egy teszt, amely elvégezhető, az, ha a fájlrendszert csak olvashatóként csatlakoztatja újra.
   Ez a megközelítés ellenőrzi, hogy a fürt képes-e feladatátvételre, ha a hozzáférése `/hana/shared` elveszett az aktív csomóponton.     


   **Várt eredmény:** Ha írási/olvasási fájlrendszerként végzi a műveletet, az írási/olvasási műveletet végző erőforrás attribútuma sikertelen lesz, mivel nem tud semmit írni a fájlrendszerre, és `/hana/shared` HANA-erőforrások feladatátvételét végzi `OCF_CHECK_LEVEL` `hana_shared1` el.  Ugyanez az eredmény akkor várható, amikor a HANA-csomópont elveszíti az NFS-megosztások hozzáférését. 

   Erőforrás állapota a teszt megkezdése előtt:

    ```
    sudo pcs status
    # Example output
    Full list of resources:
     rsc_hdb_azr_agt        (stonith:fence_azure_arm):      Started hanadb1

     Resource Group: hanadb1_nfs
         hana_data1 (ocf::heartbeat:Filesystem):    Started hanadb1
         hana_log1  (ocf::heartbeat:Filesystem):    Started hanadb1
         hana_shared1       (ocf::heartbeat:Filesystem):    Started hanadb1

    Resource Group: hanadb2_nfs
         hana_data2 (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_log2  (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_shared2       (ocf::heartbeat:Filesystem):    Started hanadb2

     hana_nfs1_active       (ocf::pacemaker:attribute):     Started hanadb1
     hana_nfs2_active       (ocf::pacemaker:attribute):     Started hanadb2

     Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
         Started: [ hanadb1 hanadb2 ]

     Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
         Masters: [ hanadb1 ]
         Slaves: [ hanadb2 ]

     Resource Group: g_ip_HN1_03
         nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hanadb1
         vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hanadb1
    ```

   A /hana/shared csak olvasható módban is használható az aktív fürtcsomóponton az alábbi paranccsal:

    ```
    sudo mount -o ro 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    ```

   A hanadb1 a stonith ( ) műveletkészlet alapján újraindul vagy `pcs property show stonith-action` kikapcsolód.  Ha a kiszolgáló (hanadb1) leállt, a HANA-erőforrás áthelyeződik a hanadb2-re. A fürt állapotát a hanadb2-ben ellenőrizheti.

    ```
    pcs status

    Full list of resources:

     rsc_hdb_azr_agt        (stonith:fence_azure_arm):      Started hanadb2

     Resource Group: hanadb1_nfs
         hana_data1 (ocf::heartbeat:Filesystem):    Stopped
         hana_log1  (ocf::heartbeat:Filesystem):    Stopped
         hana_shared1       (ocf::heartbeat:Filesystem):    Stopped

     Resource Group: hanadb2_nfs
         hana_data2 (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_log2  (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_shared2       (ocf::heartbeat:Filesystem):    Started hanadb2

     hana_nfs1_active       (ocf::pacemaker:attribute):     Stopped
     hana_nfs2_active       (ocf::pacemaker:attribute):     Started hanadb2

     Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
         Started: [ hanadb2 ]
         Stopped: [ hanadb1 ]

     Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
         Masters: [ hanadb2 ]
         Stopped: [ hanadb1 ]

     Resource Group: g_ip_HN1_03
         nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hanadb2
         vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hanadb2
    ```

   Javasoljuk, hogy alaposan tesztelje SAP HANA fürt konfigurációját a Setup [SAP HANA System Replication on RHEL (RHEL](./sap-hana-high-availability-rhel.md#test-the-cluster-setup)rendszeren történő rendszerreplikáció beállítása) című SAP HANA is.

## <a name="next-steps"></a>Következő lépések

* [Az Azure Virtual Machines tervezése és implementációja az SAP-hez][planning-guide]
* [Azure Virtual Machines üzembe helyezése az SAP-hez][deployment-guide]
* [Azure Virtual Machines DBMS üzembe helyezése SAP-hez][dbms-guide]
* [NFS 4.1-es verziójú kötetek az SAP HANA-hoz készült Azure NetApp Filesban](./hana-vm-operations-netapp.md)
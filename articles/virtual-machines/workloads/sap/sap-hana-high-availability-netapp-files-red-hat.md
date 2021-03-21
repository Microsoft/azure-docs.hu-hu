---
title: Az SAP HANA ANF magas rendelkezésre állása a RHEL-ben | Microsoft Docs
description: SAP HANA magas rendelkezésre állásának biztosítása az Azure Virtual Machines (VM) ANF.
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/17/2021
ms.author: radeltch
ms.openlocfilehash: c5f94329920f8c850c0a47dd607ade8e83658b29
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599918"
---
# <a name="high-availability-of-sap-hana-scale-up-with-azure-netapp-files-on-red-hat-enterprise-linux"></a>A SAP HANA skálázás magas rendelkezésre állása Azure NetApp Filesekkel Red Hat Enterprise Linux

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

Ez a cikk azt ismerteti, hogyan konfigurálható SAP HANA rendszerreplikáció a felskálázásos központi telepítésben, ha a HANA-fájlrendszerek NFS-en keresztül vannak csatlakoztatva, Azure NetApp Files (ANF) használatával. A példában a konfigurációk és a telepítési parancsok, a példány száma **03** és a HANA rendszerazonosító **HN1** vannak használatban. SAP HANA replikáció egy elsődleges csomópontból és legalább egy másodlagos csomópontból áll.

Ha a dokumentumban szereplő lépések a következő előtagokkal vannak megjelölve, a jelentés a következő:

- **[A]**: a lépés az összes csomópontra vonatkozik
- **[1]**: a lépés csak a csomópont1 vonatkozik
- **[2]**: a lépés csak a Csomópont2 vonatkozik
 
Először olvassa el a következő SAP-megjegyzéseket és dokumentumokat:

- SAP-Megjegyzés [1928533](https://launchpad.support.sap.com/#/notes/1928533), amely a következőket tartalmazta:
    - Az SAP-szoftverek üzembe helyezéséhez támogatott Azure-beli virtuálisgép-méretek listája.
    - Az Azure-beli virtuális gépek méretével kapcsolatos fontos kapacitási információk.
    - A támogatott SAP-szoftverek és operációs rendszerek (OS) és adatbázis-kombinációk.
    - A Windows és a Linux szükséges SAP kernel-verziója Microsoft Azureon.
- Az SAP Note [2015553](https://launchpad.support.sap.com/#/notes/2015553) az SAP által támogatott SAP-szoftverek Azure-beli üzembe helyezésének előfeltételeit sorolja fel.
- A [405827](https://launchpad.support.sap.com/#/notes/405827) -es SAP-Megjegyzés a HANA-környezet ajánlott fájlrendszerét sorolja fel.
- A [2002167](https://launchpad.support.sap.com/#/notes/2002167) -es SAP-megjegyzés a Red Hat Enterprise Linux operációsrendszer-beállításait javasolta.
- Az [2009879](https://launchpad.support.sap.com/#/notes/2009879) -es SAP-Megjegyzés SAP HANA irányelvekkel rendelkezik Red Hat Enterprise Linuxhoz.
- Az [2178632](https://launchpad.support.sap.com/#/notes/2178632) -es SAP-Megjegyzés részletes információkat tartalmaz az Azure-beli SAP-ban jelentett összes figyelési mérőszámról.
- A [2191498](https://launchpad.support.sap.com/#/notes/2191498) -es SAP-Megjegyzés a szükséges SAP-gazdagép ügynökének verziója az Azure-ban linuxos.
- Az [2243692](https://launchpad.support.sap.com/#/notes/2243692) -es SAP-Megjegyzés az Azure-beli Linuxon futó SAP-licenceléssel kapcsolatos információkat tartalmaz.
- Az SAP Megjegyzés [1999351](https://launchpad.support.sap.com/#/notes/1999351) további hibaelhárítási információkat tartalmaz az SAP-hez készült Azure Enhanced monitoring bővítménnyel kapcsolatban.
- Az [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) rendelkezik minden szükséges SAP-megjegyzéssel a Linux rendszerhez.
- [Azure Virtual Machines tervezése és implementálása Linux rendszeren az SAP-ban][planning-guide]
- [Azure Virtual Machines üzembe helyezés az SAP-hez Linux rendszeren][deployment-guide]
- [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hez Linux rendszeren][dbms-guide]
- [SAP HANA rendszerreplikáció a pacemaker-fürtben.](https://access.redhat.com/articles/3004101)
- Általános RHEL dokumentáció
    - [Magas rendelkezésre állású Add-On áttekintése](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
    - [Magas rendelkezésre állású Add-On felügyelet.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
    - [Magas rendelkezésre állású Add-On referenciája.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
    - [SAP HANA rendszer-replikálás konfigurálása a Scale-Up egy pacemaker-fürtben, ha a HANA-fájlrendszerek NFS-megosztásokon találhatók](https://access.redhat.com/solutions/5156571)
- Az Azure-specifikus RHEL dokumentációja:
    - [A magas rendelkezésre állású fürtök RHEL vonatkozó támogatási szabályzatok – Microsoft Azure Virtual Machines.](https://access.redhat.com/articles/3131341)
    - [Red Hat Enterprise Linux 7,4 (és újabb) High-Availability fürt telepítése és konfigurálása Microsoft Azureon.](https://access.redhat.com/articles/3252491)
    - [A Microsoft Azure-ban való használathoz telepítse a SAP HANA Red Hat Enterprise Linux.](https://access.redhat.com/solutions/3193782)
    - [SAP HANA skálázási rendszer replikálása a pacemaker-fürtön, ha a HANA-fájlrendszerek NFS-megosztásokon találhatók](https://access.redhat.com/solutions/5156571)
- [NetApp SAP-alkalmazások Microsoft Azure a Azure NetApp Files használatával](https://www.netapp.com/us/media/tr-4746.pdf)
- [NFS 4.1-es verziójú kötetek az SAP HANA-hoz készült Azure NetApp Filesban](./hana-vm-operations-netapp.md)

## <a name="overview"></a>Áttekintés

A vertikális Felskálázási környezetben a SAP HANA összes fájlrendszere helyi tárterületre van csatlakoztatva. A SAP HANA rendszerreplikáció magas rendelkezésre állásának beállítása Red Hat Enterprise Linuxon a következő témakörben: [SAP HANA rendszer-replikáció beállítása a RHEL](./sap-hana-high-availability-rhel.md) -ben

Ha az NFS-megosztások SAP HANA magas rendelkezésre állását szeretné elérni, a fürtben további erőforrás-konfigurációra van szükség, hogy a HANA [Azure NetApp Files](../../../azure-netapp-files/index.yml) -erőforrások helyreállítása megtörténjen, amikor az egyik csomópont elveszti a hozzáférést a ANF lévő NFS-megosztásokhoz.  A fürt kezeli az NFS-csatlakoztatásokat, ami lehetővé teszi az erőforrások állapotának figyelését. A fájlrendszer-csatlakoztatások és a SAP HANA erőforrások közötti függőségek kényszerítve vannak.  

![HA a ANF felskálázása SAP HANA](./media/sap-hana-high-availability-rhel/sap-hana-scale-up-netapp-files-red-hat.png)

SAP HANA fájlrendszerek az NFS-megosztásokhoz vannak csatlakoztatva, az egyes csomópontokon a Azure NetApp Files használatával. A fájlrendszerek/Hana/Data,/Hana/log és/Hana/Shared egyediek az egyes csomópontokon. 

Csatlakoztatva a csomópont1 (**hanadb1**)

- 10.32.2.4:/**hanadb1**-mnt00001 a/Hana/Data
- 10.32.2.4:/**hanadb1**-log-mnt00001 a/Hana/log
- 10.32.2.4:/**hanadb1**-Shared-mnt00001 a/Hana/Shared

Csatlakoztatva a Csomópont2 (**hanadb2**)

- 10.32.2.4:/**hanadb2**-mnt00001 a/Hana/Data
- 10.32.2.4:/**hanadb2**-log-mnt00001 a/Hana/log
- 10.32.2.4:/**hanadb2**-Shared-mnt00001 a/Hana/Shared

> [!NOTE]
> A fájlrendszerek/Hana/Shared,/Hana/Data és/Hana/log nincsenek megosztva a két csomópont között. Mindegyik fürtcsomópont saját, különálló fájlrendszerrel rendelkezik.   

A SAP HANA rendszer-replikációs konfiguráció dedikált virtuális állomásnevet és virtuális IP-címeket használ. Az Azure-ban a virtuális IP-címek használatához terheléselosztó szükséges. A terheléselosztó konfigurációját a következő lista tartalmazza:

- Előtér-konfiguráció: IP-10.32.0.10 a hn1-db-hez
- Háttérbeli konfiguráció: a HANA rendszer-replikáció részét képező összes virtuális gép elsődleges hálózati adapteréhez csatlakozik
- Mintavételi port: 62503-es port
- Terheléselosztási szabályok: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP (ha alapszintű Azure Load balancert használ)  

## <a name="set-up-the-azure-netapp-file-infrastructure"></a>Az Azure NetApp-fájl infrastruktúrájának beállítása

Mielőtt folytatná a Azure NetApp Files-infrastruktúra beállítását, ismerkedjen meg az Azure [NetApp Files dokumentációval](../../../azure-netapp-files/index.yml).

Azure NetApp Files több [Azure-régióban](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)is elérhető. Ellenőrizze, hogy a kiválasztott Azure-régió kínál-e Azure NetApp Files.

Az Azure-régiók Azure NetApp Files rendelkezésre állásáról az [Azure-régió Azure NetApp Files rendelkezésre állása](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)című témakörben olvashat bővebben.

A Azure NetApp Files üzembe helyezése előtt kérje a bevezetést a Azure NetApp Filesra, ha [Azure NetApp Files útmutatást szeretne regisztrálni](../../../azure-netapp-files/azure-netapp-files-register.md).

### <a name="deploy-azure-netapp-files-resources"></a>Azure NetApp Files erőforrások üzembe helyezése

Az alábbi utasítások azt feltételezik, hogy már üzembe helyezte az Azure-beli [virtuális hálózatot](../../../virtual-network/virtual-networks-overview.md). A Azure NetApp Files erőforrásokat és virtuális gépeket, amelyeken a Azure NetApp Files erőforrásokat csatlakoztatni kell, ugyanabban az Azure-beli virtuális hálózatban vagy az Azure-beli virtuális hálózatokban kell telepíteni.

1. Ha még nem telepítette az erőforrásokat, kérje a [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md)bevezetését.

2. Hozzon létre egy NetApp-fiókot a kiválasztott Azure-régióban a [NetApp-fiók létrehozása](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)című részben található utasításokat követve.

3.  Hozzon létre egy Azure NetApp Files kapacitási készletet a [Azure NetApp Files kapacitásának beállítása](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)című részben leírtak szerint.

    A cikkben bemutatott HANA-architektúra az *Ultra* Service szintjén egyetlen Azure NetApp Files kapacitású készletet használ. Az Azure-beli HANA-alapú számítási feladatokhoz ajánlott Azure NetApp Files *Ultra* vagy *prémium* [szintű szolgáltatási szintet](../../../azure-netapp-files/azure-netapp-files-service-levels.md)használni.

4.  Alhálózat delegálása Azure NetApp Filesre, az [alhálózat delegálása Azure NetApp Filesra](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)című részben leírtak szerint.

5.  Azure NetApp Files kötetek üzembe helyezéséhez kövesse az [NFS-kötet létrehozása a Azure NetApp Files számára](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)című témakör utasításait.

    A kötetek központi telepítésekor ügyeljen arra, hogy a NFSv 4.1 verziót válassza. Telepítse a köteteket a kijelölt Azure NetApp Files alhálózatban. Az Azure NetApp-kötetek IP-címeit a rendszer automatikusan hozzárendeli.

    Ne feledje, hogy a Azure NetApp Files erőforrásoknak és az Azure-beli virtuális gépeknek ugyanabban az Azure-beli virtuális hálózaton vagy az Azure-beli virtuális hálózatokban kell lenniük. Például a hanadb1-mnt00001, a hanadb1-log-mnt00001 és így tovább, a kötetek nevei és nfs://10.32.2.4/hanadb1-data-mnt00001, nfs://10.32.2.4/hanadb1-log-mnt00001 és így tovább, a fájlok elérési útja a Azure NetApp Files kötetek számára.
    
    **Hanadb1**

    - Volume hanadb1-mnt00001 (nfs://10.32.2.4:/hanadb1-data-mnt00001) 
    - Kötet hanadb1-log-mnt00001 (nfs://10.32.2.4:/hanadb1-log-mnt00001)
    - Kötet hanadb1-Shared-mnt00001 (nfs://10.32.2.4:/hanadb1-shared-mnt00001)
    
    **Hanadb2**

    - Volume hanadb2-mnt00001 (nfs://10.32.2.4:/hanadb2-data-mnt00001)
    - Kötet hanadb2-log-mnt00001 (nfs://10.32.2.4:/hanadb2-log-mnt00001)
    - Kötet hanadb2-Shared-mnt00001 (nfs://10.32.2.4:/hanadb2-shared-mnt00001)

### <a name="important-considerations"></a>Fontos szempontok

A Azure NetApp Files SAP HANA Felskálázási rendszerekhez való létrehozásakor vegye figyelembe az alábbi szempontokat:

- A minimális kapacitási készlet 4 tebibájt (TiB).
- A minimális kötet mérete 100 gibibájtban értendők (GiB).
- Azure NetApp Files és az összes virtuális gépet, amelybe a Azure NetApp Files köteteket csatlakoztatni kell, ugyanabban az Azure-beli virtuális hálózatban vagy azonos régióban lévő, egymással azonos [virtuális hálózatokban](../../../virtual-network/virtual-network-peering-overview.md) kell lennie.
- A kiválasztott virtuális hálózatnak rendelkeznie kell egy Azure NetApp Files delegált alhálózattal.
- Egy Azure NetApp Files kötet átviteli sebessége a mennyiségi kvóta és a szolgáltatási szint függvénye a [Azure NetApp Files szolgáltatási szintjén](../../../azure-netapp-files/azure-netapp-files-service-levels.md)dokumentálva. A HANA Azure NetApp-kötetek méretezése esetén győződjön meg arról, hogy az eredményül kapott átviteli sebesség megfelel a HANA rendszerkövetelményeinek.
- Az Azure NetApp Files [exportálási házirend](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)segítségével szabályozhatja az engedélyezett ügyfeleket, a hozzáférési típust (írható és olvasható, csak olvasható stb.).
- A Azure NetApp Files funkció még nem tud zónát kiszolgálni. Jelenleg a szolgáltatás nincs telepítve az Azure-régió összes rendelkezésre állási zónájában. Vegye figyelembe, hogy egyes Azure-régiókban lehetséges a késés következményei.

> [!IMPORTANT]
> SAP HANA munkaterhelések esetében a kis késleltetés kritikus fontosságú. A Microsoft képviselőjével együttműködve biztosíthatja, hogy a virtuális gépek és a Azure NetApp Filesi kötetek központi telepítése megtörténjen.

### <a name="sizing-of-hana-database-on-azure-netapp-files"></a>HANA-adatbázis méretezése Azure NetApp Files

Egy Azure NetApp Files kötet átviteli sebessége a kötet méretének és a szolgáltatási szintnek a függvénye, amely a [Azure NetApp Files szolgáltatási szintjén](../../../azure-netapp-files/azure-netapp-files-service-levels.md)van dokumentálva.

Az Azure-beli SAP-infrastruktúra tervezésekor vegye figyelembe az SAP által igényelt minimális tárolási követelményeket, amelyek az átviteli sebesség minimális jellemzőire fordíthatók:

- Írási/olvasási sebesség 250 megabájt/másodperc (MB/s), 1 MB méretű I/O-méretekkel/Hana/log.
- Olvasási tevékenység legalább 400 MB/s a/Hana/Data 16 MB-os és 64-MB I/O-méretekhez.
- Legalább 250 MB/s írási tevékenység a 16 MB-os és a 64-MB I/O-mérettel rendelkező/Hana/Data.

A [Azure NetApp Files átviteli sebességre vonatkozó határértékek](../../../azure-netapp-files/azure-netapp-files-service-levels.md) 1 TiB-ra vetítve:

- Premium Storage szintű 64 MiB/s.
- Ultra Storage-réteg – 128 MiB/s.

A/Hana/Data és a/Hana/log SAP minimális átviteli sebességére vonatkozó követelmények teljesítéséhez, valamint a/Hana/Shared iránymutatásaihoz az ajánlott méretek a következők:

|    Kötet    | Premium Storage rétegek mérete | Az ultra Storage-rétegek mérete | Támogatott NFS-protokoll |
| :----------: | :--------------------------: | :------------------------: | :--------------------: |
|  /hana/log   |            4 TiB             |           2 TiB            |          v 4.1          |
|  /hana/data  |           6,3 TiB            |          3,2 TiB           |          v 4.1          |
| /hana/shared |           1 x RAM            |          1 x RAM           |          v3 vagy v 4.1    |


> [!NOTE]
> Az itt ismertetett Azure NetApp Files méretezési javaslatok célja, hogy megfeleljenek az SAP által az infrastruktúra-szolgáltatók számára javasolt minimális követelményeknek. A valós ügyfelek központi telepítései és számítási feladatainak esetében ezek a méretek nem elégségesek. Ezeket a javaslatokat kiindulási pontként és alkalmazkodva használhatja az adott számítási feladatra vonatkozó követelmények alapján.

> [!TIP]
> Azure NetApp Files köteteket dinamikusan átméretezheti, anélkül, hogy le kellene *választania* a köteteket, le kell állítania a virtuális gépeket, vagy le kell állítania a SAP HANA. Ez a megközelítés lehetővé teszi a rugalmasságot, hogy megfeleljen az alkalmazás várható és előre nem látható átviteli követelményeinek.

> [!NOTE]
> Ebben a cikkben a/Hana/Shared csatlakoztatni kívánt összes parancs a NFSv 4.1/Hana/Shared-kötetekre mutat.
> Ha NFSv3 kötetekként telepítette a/Hana/Shared köteteket, ne felejtse el beállítani a/Hana/Shared csatlakoztatási parancsait a NFSv3-hez.


## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Linux rendszerű virtuális gép üzembe helyezése Azure Portal használatával 

Először létre kell hoznia a Azure NetApp Files köteteket. Ezután hajtsa végre a következő lépéseket:

1.  Hozzon létre egy erőforráscsoportot.
2.  Hozzon létre egy virtuális hálózatot.
3.  Hozzon létre egy rendelkezésre állási készletet. Állítsa be a maximális frissítési tartományt.
4.  Hozzon létre egy Load balancert (belső). A standard Load Balancer használatát javasoljuk.
    Válassza ki a 2. lépésben létrehozott virtuális hálózatot.
5.  Hozzon létre egy 1. virtuális gépet (**hanadb1**). 
6.  A 2. virtuális gép (**hanadb2**) létrehozása.  
7.  A virtuális gép létrehozása során nem adunk hozzá lemezeket, mert az összes csatlakoztatási pont a Azure NetApp Files NFS-megosztásokon lesz. 

> [!IMPORTANT]
> A lebegő IP-címek nem támogatottak a terheléselosztási helyzetekben a hálózati adapter másodlagos IP-konfigurációjában. További részletek: az [Azure Load Balancer korlátozásai](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Ha a virtuális gép további IP-címére van szüksége, helyezzen üzembe egy második hálózati adaptert.    

> [!NOTE] 
> Ha a nyilvános IP-címek nélküli virtuális gépek a belső (nincs nyilvános IP-cím) standard Azure Load Balancer háttér-készletbe kerülnek, nem lesz kimenő internetkapcsolat, kivéve, ha további konfigurálást végeznek a nyilvános végpontok útválasztásának engedélyezéséhez. A kimenő kapcsolatok elérésével kapcsolatos részletekért lásd: [nyilvános végpontú kapcsolat Virtual Machines az Azure standard Load Balancer használata az SAP magas rendelkezésre állási helyzetekben](./high-availability-guide-standard-load-balancer-outbound-connections.md).

8.  Ha standard Load balancert használ, kövesse az alábbi konfigurációs lépéseket:
    1.  Először hozzon létre egy előtér-IP-címkészletet:
        1.  Nyissa meg a terheléselosztó felületet, válassza a előtér **IP-készlet** lehetőséget, majd kattintson a **Hozzáadás** gombra.
        1.  Adja meg az új előtér-IP-készlet nevét (például **Hana-frontend**).
        1.  Állítsa a **hozzárendelést** **statikus** értékre, és adja meg az IP-címet (például **10.32.0.10**).
        1.  Válassza az **OK** lehetőséget.
        1.  Az új előtér-IP-készlet létrehozása után jegyezze fel a készlet IP-címét.
    1.  Következő lépésként hozzon létre egy háttér-készletet:
        1.  Nyissa meg a Load balancert, válassza a **háttérbeli készletek** lehetőséget, majd válassza a **Hozzáadás** lehetőséget.
        1.  Adja meg az új háttér-készlet nevét (például **Hana-backend**).
        1.  Válassza **a virtuális gép hozzáadása** lehetőséget.
        1.  Válassza a * * virtuális gép * * elemet.
        1.  Válassza ki a SAP HANA-fürthöz tartozó virtuális gépeket és azok IP-címeit.
        1.  Válassza a **Hozzáadás** lehetőséget.
    1.  Következő lépésként hozzon létre egy állapot-mintavételt:
        1.  Nyissa meg a terheléselosztó-t, válassza az **állapot**-tesztek elemet, majd kattintson a **Hozzáadás** gombra.
        1.  Adja meg az új állapot-mintavétel nevét (például **Hana-HP**).
        1.  Válassza a TCP lehetőséget a protokoll és a **625-** es port. Tartsa meg az **intervallum** értékét 5-re, a nem kifogástalan **állapot küszöbértékének** értéke pedig 2.
        1.  Válassza az **OK** lehetőséget.
    1.  Ezután hozza létre a terheléselosztási szabályokat:
        1.  Nyissa meg a terheléselosztó-t, válassza a terheléselosztási **szabályok** lehetőséget, majd válassza a **Hozzáadás** lehetőséget.
        1.  Adja meg az új terheléselosztó-szabály nevét (például **Hana-LB**).
        1.  Válassza ki az előtér-IP-címet, a háttér-készletet és a korábban létrehozott állapot-mintavételt (például **Hana-frontend**, **Hana-backend** és **Hana-HP**).
        1.  Válassza a **hektár portok** lehetőséget.
        1.  Növelje az **üresjárati időkorlátot** 30 percre.
        1.  Ügyeljen arra, hogy a **lebegő IP-címet engedélyezze**.
        1.  Válassza az **OK** lehetőséget.


9. Ha a forgatókönyv az alapszintű Load Balancer használatát is megköveteli, kövesse az alábbi konfigurációs lépéseket:
    1.  Konfigurálja a Load balancert. Először hozzon létre egy előtér-IP-címkészletet:
        1.  Nyissa meg a terheléselosztó felületet, válassza a előtér **IP-készlet** lehetőséget, majd kattintson a **Hozzáadás** gombra.
        1.  Adja meg az új előtér-IP-készlet nevét (például **Hana-frontend**).
        1.  Állítsa a **hozzárendelést** **statikus** értékre, és adja meg az IP-címet (például **10.32.0.10**).
        1.  Válassza az **OK** lehetőséget.
        1.  Az új előtér-IP-készlet létrehozása után jegyezze fel a készlet IP-címét.
    1.  Következő lépésként hozzon létre egy háttér-készletet:
        1.  Nyissa meg a Load balancert, válassza a **háttérbeli készletek** lehetőséget, majd válassza a **Hozzáadás** lehetőséget.
        1.  Adja meg az új háttér-készlet nevét (például **Hana-backend**).
        1.  Válassza **a virtuális gép hozzáadása** lehetőséget.
        1.  Válassza ki a 3. lépésben létrehozott rendelkezésre állási készletet.
        1.  Válassza ki a SAP HANA-fürthöz tartozó virtuális gépeket.
        1.  Válassza az **OK** lehetőséget.
    1.  Következő lépésként hozzon létre egy állapot-mintavételt:
        1.  Nyissa meg a terheléselosztó-t, válassza az **állapot**-tesztek elemet, majd kattintson a **Hozzáadás** gombra.
        1.  Adja meg az új állapot-mintavétel nevét (például **Hana-HP**).
        1.  Válassza a **TCP** lehetőséget a protokoll és a **625-** es port. Tartsa meg az **intervallum** értékét 5-re, a nem kifogástalan **állapot küszöbértékének** értéke pedig 2.
        1.  Válassza az **OK** lehetőséget.
    1.  SAP HANA 1,0 esetében hozza létre a terheléselosztási szabályokat:
        1.  Nyissa meg a terheléselosztó-t, válassza a terheléselosztási **szabályok** lehetőséget, majd válassza a **Hozzáadás** lehetőséget.
        1.  Adja meg az új terheléselosztó-szabály nevét (például Hana-LB-3 **03** 15).
        1.  Válassza ki az előtér-IP-címet, a háttér-készletet és a korábban létrehozott állapot-mintavételt (például **Hana-frontend**).
        1.  Tartsa a **protokollt** **TCP**-értékre, és írja be a 3 **03** 15 portot.
        1.  Növelje az **üresjárati időkorlátot** 30 percre.
        1.  Ügyeljen arra, hogy a **lebegő IP-címet engedélyezze**.
        1.  Válassza az **OK** lehetőséget.
        1.  Ismételje meg ezeket a lépéseket a 3 **03** 17-ös porton.
    1.  SAP HANA 2,0 esetében hozza létre a rendszeradatbázis terheléselosztási szabályait:
        1.  Nyissa meg a terheléselosztó-t, válassza a terheléselosztási **szabályok** lehetőséget, majd válassza a **Hozzáadás** lehetőséget.
        1.  Adja meg az új terheléselosztó-szabály nevét (például Hana-LB-3 **03** 13).
        1.  Válassza ki az előtér-IP-címet, a háttér-készletet és a korábban létrehozott állapot-mintavételt (például **Hana-frontend**).
        1.  Tartsa a **protokollt** **TCP**-értékre, és írja be a 3 **03** 13 portot.
        1.  Növelje az **üresjárati időkorlátot** 30 percre.
        1.  Ügyeljen arra, hogy a **lebegő IP-címet engedélyezze**.
        1.  Válassza az **OK** lehetőséget.
        1.  Ismételje meg ezeket a lépéseket a 3.**03**. porton.
    1.  SAP HANA 2,0 esetében először hozza létre a bérlői adatbázishoz tartozó terheléselosztási szabályokat:
        1.  Nyissa meg a terheléselosztó-t, válassza a terheléselosztási **szabályok** lehetőséget, majd válassza a **Hozzáadás** lehetőséget.
        1.  Adja meg az új terheléselosztó-szabály nevét (például Hana-LB-3 **03** 40).
        1.  Válassza ki a korábban létrehozott előtérbeli IP-címet, a háttér-készletet és az állapot-mintavételt (például **Hana-frontend**).
        1.  Tartsa a **protokollt** **TCP**-re, és írja be a 3 **03** 40 portot.
        1.  Növelje az **üresjárati időkorlátot** 30 percre.
        1.  Ügyeljen arra, hogy a **lebegő IP-címet engedélyezze**.
        1.  Válassza az **OK** lehetőséget.
        1.  Ismételje meg ezeket a lépéseket a 3 **03** 41 és 3 **03** 42-es porton.

A SAP HANA szükséges portokkal kapcsolatos további információkért olvassa el a [bérlői adatbázisok kapcsolatai](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) című részt a [SAP HANA bérlői adatbázisok](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) útmutatójában vagy az [2388694](https://launchpad.support.sap.com/#/notes/2388694)-es SAP-megjegyzésben.

> [!IMPORTANT]
> Ne engedélyezze a TCP-időbélyegeket a Azure Load Balancer mögött elhelyezett Azure-beli virtuális gépeken. A TCP-időbélyegek engedélyezése az állapot-mintavételek meghibásodását eredményezi. Állítsa a paramétert a **0** értékre **net.IPv4.tcp_timestamps** . Részletekért lásd: [Load Balancer Health](../../../load-balancer/load-balancer-custom-probe-overview.md)-tesztek. Lásd még: SAP Note [2382421](https://launchpad.support.sap.com/#/notes/2382421).

## <a name="mount-the-azure-netapp-files-volume"></a>A Azure NetApp Files kötet csatlakoztatása

1. **[A]** csatlakoztatási pontok létrehozása a HANA-adatbázis köteteihez. 

    ```
    mkdir -p /hana/data
    mkdir -p /hana/log
    mkdir -p /hana/shared
    ```

2. **[A]** ellenőrizze az NFS-tartomány beállítását. Győződjön meg arról, hogy a tartomány alapértelmezett Azure NetApp Files-tartományként van konfigurálva, azaz a **defaultv4iddomain.com** és a leképezés értéke **senki**.

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
    > Ügyeljen arra, hogy az NFS-tartományt a/etc/idmapd.conf-ben állítsa be a virtuális gépen, hogy az megfeleljen az alapértelmezett tartományi konfigurációnak Azure NetApp Files: **defaultv4iddomain.com**. Ha az NFS-ügyfél (azaz a virtuális gép) és az NFS-kiszolgáló (például az Azure NetApp-konfiguráció) közötti eltérés nem egyezik, akkor a virtuális gépekre csatlakoztatott Azure NetApp-köteteken található fájlok engedélyei nem lesznek láthatók.
    

3. **[1]** a csomópont-specifikus kötetek csatlakoztatása a csomópont1 (**hanadb1**) 

    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-data-mnt00001 /hana/data
    ```
    
4.  **[2]** a csomópont-specifikus kötetek csatlakoztatása a Csomópont2 (**hanadb2**)
    
    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-data-mnt00001 /hana/data
    ```

5. **[A]** ellenőrizze, hogy az összes HANA-kötet csatlakoztatva van-e az NFS protokoll nfsv4 névleképezője-verziójához.

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

6. **[A]** ellenőrizze **nfs4_disable_idmapping**. Értékeként az **Y** értéknek kell lennie. A **nfs4_disable_idmapping** található címtár-struktúra létrehozásához hajtsa végre a csatlakoztatási parancsot. Nem lehet manuálisan létrehozni a könyvtárat a/sys/modules alatt, mivel a hozzáférés a kernel/illesztőprogramok számára van fenntartva.

    ```
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # If you need to set nfs4_disable_idmapping to Y
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

   A **nfs_disable_idmapping** paraméter módosításáról további információt a következő témakörben talál: [https://access.redhat.com/solutions/1749883](https://access.redhat.com/solutions/1749883) . 


## <a name="sap-hana-installation"></a>SAP HANA telepítés

1. **[A]** állomásnév-feloldás beállítása az összes gazdagépen.

   Használhat DNS-kiszolgálót, vagy módosíthatja az/etc/hosts fájlt az összes csomóponton. Ez a példa a/etc/hosts fájl használatát mutatja be. Cserélje le az IP-címet és a gazdagépet a következő parancsokra:

   ```
   sudo vi /etc/hosts
   # Insert the following lines in the /etc/hosts file. Change the IP address and hostname to match your environment  
   10.32.0.4   hanadb1
   10.32.0.5   hanadb2
   ```

2. **[A]** RHEL a HANA-konfigurációhoz

   Konfigurálja a RHEL az SAP-Megjegyzés alább leírtak szerint a RHEL verziója alapján

   - [2292690 – SAP HANA DB: ajánlott operációsrendszer-beállítások a RHEL 7 rendszerhez](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782 – SAP HANA DB: ajánlott operációsrendszer-beállítások a RHEL 8 rendszerhez](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582 – Linux: a GCC 6. x-sel lefordított SAP-alkalmazások futtatása](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824 – Linux: a GCC 7. x verzióval lefordított SAP-alkalmazások futtatása](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607 – Linux: a GCC 9. x-szel lefordított SAP-alkalmazások futtatása](https://launchpad.support.sap.com/#/notes/2886607)

3. **[A]** telepítse a SAP HANA

   A HANA 2,0 SPS 01 MDC az alapértelmezett beállítás. A HANA rendszer telepítésekor a SYSTEMDB és az azonos biztonsági azonosítóval rendelkező bérlő együtt lesz létrehozva. Bizonyos esetekben nem szeretné, hogy az alapértelmezett bérlő legyen. Ha nem szeretné létrehozni a kezdeti bérlőt a telepítéssel együtt, kövesse az SAP Note [2629711](https://launchpad.support.sap.com/#/notes/2629711)

    Futtassa a **hdblcm** programot a HANA DVD-ről. Adja meg a következő értékeket a parancssorban:  
    Telepítés kiválasztása: adja meg az **1** értéket (telepítéshez)  
    További összetevők kiválasztása a telepítéshez: **1**. Adjon meg egy értéket.  
    Adja meg a telepítési útvonalat [/Hana/Shared]: nyomja le az ENTER billentyűt az alapértelmezett érték elfogadásához  
    Adja meg a helyi állomásnév [..]: nyomja meg az ENTER billentyűt az alapértelmezett érték elfogadásához  
    További gazdagépeket szeretne hozzáadni a rendszeren? (i/n) [n]: **n**  
    Adja meg SAP HANA rendszerazonosítót: adja meg a **HN1**.  
    Adja meg a példány számát [00]: ENTER **03**  
    Válassza az adatbázis mód/index megadása [1] lehetőséget: az ENTER billentyű lenyomásával fogadja el az alapértelmezett értéket.  
    Válasszon rendszerhasználatot/adja meg a (z) [4] indexet: **4** . adja meg (egyéni)  
    Adja meg az adatkötetek helyét [/Hana/Data]: nyomja le az ENTER billentyűt az alapértelmezett érték elfogadásához.  
    Adja meg a naplózási kötetek helyét [/Hana/log]: nyomja le az ENTER billentyűt az alapértelmezett érték elfogadásához.  
    Korlátozza a memória maximális kiosztását? [n]: az ENTER billentyű lenyomásával fogadja el az alapértelmezett értéket  
    Adja meg a (z) "..." gazdagép nevét. [...]: az ENTER billentyű lenyomásával fogadja el az alapértelmezett értéket  
    Adja meg az SAP Host Agent User (sapadm) jelszót: adja meg a gazdagép-ügynök felhasználói jelszavát.  
    SAP Host Agent-felhasználó (sapadm) jelszavának megerősítése: adja meg ismét a gazdagép-ügynök felhasználói jelszavát a megerősítéshez.  
    Adja meg a rendszergazda (hn1adm) jelszavát: írja be a rendszergazda jelszavát.  
    Rendszergazda (hn1adm) jelszavának megerősítése: a megerősítéshez írja be újra a rendszergazda jelszavát.  
    Adja meg a rendszergazda kezdőkönyvtár [/usr/sap/HN1/home]: az ENTER billentyű lenyomásával fogadja el az alapértelmezett értéket.  
    Adja meg a rendszergazda bejelentkezési rendszerhéját [/bin/sh]: nyomja le az ENTER billentyűt az alapértelmezett érték elfogadásához.  
    Adja meg a rendszergazda felhasználói AZONOSÍTÓját [1001]: nyomja le az ENTER billentyűt az alapértelmezett érték elfogadásához.  
    Adja meg a felhasználói csoport AZONOSÍTÓját (sapsys) [79]: nyomja le az ENTER billentyűt az alapértelmezett érték elfogadásához.  
    Adja meg az adatbázis-felhasználó (rendszer) jelszavát: adja meg az adatbázis felhasználói jelszavát.  
    Adatbázis-felhasználó (rendszer) jelszavának megerősítése: írja be újra az adatbázis felhasználói jelszavát a megerősítéshez.  
    Újraindítja a rendszert a gép újraindítása után? [n]: az ENTER billentyű lenyomásával fogadja el az alapértelmezett értéket  
    Folytatja? (i/n): az összefoglalás ellenőrzése. A folytatáshoz adja meg az **y** értéket.  

4. **[A]** az SAP Host Agent frissítése

   Töltse le a legújabb SAP Host Agent-archívumot az [SAP Software Center](https://launchpad.support.sap.com/#/softwarecenter) webhelyről, és futtassa a következő parancsot az ügynök frissítéséhez. Cserélje le az Archívum elérési útját úgy, hogy az a letöltött fájlra mutasson:

   ```
   sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
   ```

5. **[A]** tűzfal konfigurálása

   Hozza létre az Azure Load Balancer mintavételi portjának tűzfalszabály-szabályát.

   ```
   sudo firewall-cmd --zone=public --add-port=62503/tcp
   sudo firewall-cmd --zone=public --add-port=62503/tcp –permanent
   ```

## <a name="configure-sap-hana-system-replication"></a>SAP HANA rendszerreplikáció konfigurálása

SAP HANA rendszerreplikáció konfigurálásához kövesse a [SAP HANA Rendszerreplikáció](./sap-hana-high-availability-rhel.md#configure-sap-hana-20-system-replication) beállítása című témakörben leírt lépéseket. 

## <a name="cluster-configuration"></a>Fürtkonfiguráció

Ez a szakasz azokat a szükséges lépéseket ismerteti, amelyek szükségesek ahhoz, hogy a fürt zökkenőmentesen működjön, ha SAP HANA az NFS-megosztásokon Azure NetApp Files használatával van telepítve. 

### <a name="create-a-pacemaker-cluster"></a>Pacemaker-fürt létrehozása

Ha alapszintű pacemaker-fürtöt szeretne létrehozni ehhez a HANA-kiszolgálóhoz, kövesse az Azure-beli [Red Hat Enterprise Linux pacemaker beállítása](./high-availability-guide-rhel-pacemaker.md) című témakör lépéseit.

### <a name="configure-filesystem-resources"></a>Fájlrendszer-erőforrások konfigurálása

Ebben a példában mindegyik fürtcsomópont saját HANA NFS-fájlrendszerrel rendelkezik, a/Hana/Shared, a/Hana/Data és a/Hana/log.   

1. **[1]** a fürtöt karbantartási módba helyezi.

   ```
   pcs property set maintenance-mode=true
   ```

2. **[1]** hozza létre a **hanadb1** -csatlakoztatások fájlrendszer-erőforrásait.

    ```
    pcs resource create hana_data1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_log1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_shared1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    ```

3. **[2]** hozza létre a **hanadb2** -csatlakoztatások fájlrendszer-erőforrásait.

    ```
    pcs resource create hana_data2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_log2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_shared2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    ```

    `OCF_CHECK_LEVEL=20` a rendszer hozzáadja az attribútumot a figyelő művelethez, hogy mindegyik figyelő olvasási/írási tesztet hajtson végre a fájlrendszeren. Ezen attribútum nélkül a figyelő művelet csak azt ellenőrzi, hogy a fájlrendszer csatlakoztatva van-e. Ez akkor lehet probléma, ha a kapcsolat megszakad, a fájlrendszer csatlakoztatva maradhat annak ellenére, hogy nem érhető el.

    `on-fail=fence` a rendszer az attribútumot is hozzáadja a figyelő művelethez. Ha ezt a lehetőséget választja, ha a figyelő művelet egy csomóponton meghiúsul, a csomópont azonnal be lesz kerítve. A beállítás nélkül az alapértelmezett viselkedés az összes olyan erőforrás leállítása, amely a sikertelen erőforrástól függ, majd indítsa újra a sikertelen erőforrást, majd indítsa el az összes olyan erőforrást, amely a sikertelen erőforrástól függ. Ez a viselkedés nem csak hosszú időt vehet igénybe, ha egy SAPHana-erőforrás a sikertelen erőforrástól függ, de az is lehet, hogy teljesen meghiúsul. A SAPHana-erőforrás nem állítható le sikeresen, ha a HANA-végrehajtható fájlokat tároló NFS-kiszolgáló nem érhető el.

4. **[1]** a hely megkötésének konfigurálása

   Konfigurálja a hely megkötéseit annak biztosítására, hogy a hanadb1 egyedi csatlakoztatásokat kezelő erőforrások soha ne fussanak a hanadb2, és fordítva.

    ```
    pcs constraint location hanadb1_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb2
    pcs constraint location hanadb2_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb1
    ```

    A `resource-discovery=never` beállítás be van állítva, mert az egyes csomópontok egyedi csatlakoztatásai ugyanazt a csatlakoztatási pontot használják. Például a `hana_data1` csatlakoztatási pontot használja `/hana/data` , és a `hana_data2` csatlakoztatási pontot is használja `/hana/data` . Ez hamis pozitív értéket okozhat a mintavételi műveletekhez, amikor az erőforrás állapota a fürt indításakor be van jelölve, és ez szükségtelen helyreállítási viselkedést eredményezhet. Ezt a beállítással lehet elkerülni `resource-discovery=never`

5. **[1]** attribútum-erőforrások konfigurálása

   Adja meg az attribútumok erőforrásait. Ezek az attribútumok True értékre lesznek állítva, ha egy csomópont NFS-csatlakoztatása (/Hana/Data,/Hana/log és/Hana/Data) csatlakoztatva van, és hamis értéket ad meg.

   ```
   pcs resource create hana_nfs1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs1_active
   pcs resource create hana_nfs2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs2_active
   ```

6. **[1]** a hely megkötésének konfigurálása

   Konfigurálja a hely megkötéseit annak biztosítására, hogy a hanadb1's-attribútum erőforrás soha ne fusson a hanadb2, és fordítva.

   ```
   pcs constraint location hana_nfs1_active avoids hanadb2
   pcs constraint location hana_nfs2_active avoids hanadb1
   ```

7. **[1]** megrendelés megkötésének létrehozása

   Megrendelési korlátozásokat konfigurálhat úgy, hogy a csomópont attribútum-erőforrásai csak a csomópont NFS-csatlakoztatásai után legyenek elindítva.
   
    ```
    pcs constraint order hanadb1_nfs then hana_nfs1_active
    pcs constraint order hanadb2_nfs then hana_nfs2_active
    ```

   > [!TIP]
   > Ha a konfiguráció fájlrendszereket is tartalmaz, a csoporton kívül vagy a-ben, `hanadb1_nfs` `hanadb2_nfs` akkor adja meg a `sequential=false` beállítást, hogy a fájlrendszerek között ne legyenek rendezési függőségek. Az összes fájlrendszert előbb el kell indítani `hana_nfs1_active` , de nem kell egymáshoz viszonyított sorrendben elkezdeniük. További részletek: [Hogyan SAP HANA rendszerreplikáció konfigurálása a pacemaker-fürt Scale-Upjában, ha a HANA-fájlrendszer NFS-megosztásokon van](https://access.redhat.com/solutions/5156571)

### <a name="configure-sap-hana-cluster-resources"></a>SAP HANA fürterőforrás konfigurálása

1. A fürt SAP HANA erőforrásainak létrehozásához kövesse a [SAP HANA-fürterőforrások létrehozása](./sap-hana-high-availability-rhel.md#create-sap-hana-cluster-resources) című témakör lépéseit. SAP HANA erőforrások létrehozása után létre kell hoznia egy hely szabály-korlátozást SAP HANA erőforrások és fájlrendszerek (NFS-csatlakoztatások) között.

2. **[1]** megkötések konfigurálása a SAP HANA erőforrásai és az NFS-csatlakoztatások között

   A hely szabályának korlátozásai úgy lesznek beállítva, hogy a SAP HANA erőforrások csak akkor futhatnak a csomópontokon, ha az összes NFS-csatlakoztatás csatlakoztatva van.

    ```
    pcs constraint location SAPHanaTopology_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # On RHEL 7.x
    pcs constraint location SAPHana_HN1_03-master rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # On RHEL 8.x
    pcs constraint location SAPHana_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # Take the cluster out of maintenance mode
    sudo pcs property set maintenance-mode=false
    ```

   A fürt és az összes erőforrás állapotának megtekintése
   > [!NOTE]
   > Ez a cikk a *Slave* kifejezésre mutató hivatkozásokat tartalmaz, amelyek egy kifejezés, amelyet a Microsoft már nem használ. Ha a rendszer eltávolítja a kifejezést a szoftverből, azt a cikkből távolítjuk el.
   
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

## <a name="configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster"></a>A HANA aktív/olvasási engedélyezett rendszerreplikációjának konfigurálása a pacemaker-fürtben

A SAP HANA 2,0 SPS 01 SAP lehetővé teszi az aktív/olvasható beállítások használatát SAP HANA rendszer-replikáláshoz, ahol az SAP HANA rendszer-replikálás másodlagos rendszerei aktívan használhatók az olvasási és intenzív munkaterhelésekhez. Ha egy fürtön szeretné támogatni a telepítést, egy második virtuális IP-cím szükséges, amely lehetővé teszi, hogy az ügyfelek hozzáférhessenek a másodlagos, írásvédett SAP HANA adatbázishoz. Annak biztosítása érdekében, hogy a másodlagos replikálási hely továbbra is elérhető legyen egy átvétel után, a fürtnek át kell helyeznie a virtuális IP-címet a SAPHana-erőforrás másodlagos helyére.

A második virtuális IP-címmel rendelkező Red hat magas rendelkezésre állású fürtben a Hana Active/Read enabled rendszer replikálásának kezeléséhez szükséges további konfigurációt lásd: [Hana Active/Read enabled rendszer-replikálás beállítása a pacemaker-fürtben](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster).  

A folytatás előtt győződjön meg arról, hogy teljes mértékben konfigurálta a Red hat magas rendelkezésre állású fürtöt, amely a dokumentáció fenti szakaszaiban ismertetett módon felügyeli SAP HANA adatbázisát.    


## <a name="test-the-cluster-setup"></a>A fürt beállításának tesztelése

Ez a szakasz azt ismerteti, hogyan lehet tesztelni a telepítőt. 

1. Mielőtt elkezdené a tesztet, győződjön meg arról, hogy a pacemaker nem rendelkezik sikertelen művelettel (PC-ken keresztül), nincsenek váratlan helyekre vonatkozó korlátozások (például egy áttelepítési teszt maradékai), és hogy a HANA rendszer replikálása szinkronizálási állapotú, például a systemReplicationStatus:

    ```
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    ```

2. A fürt konfigurációjának ellenőrzése olyan meghibásodási forgatókönyv esetén, amikor egy csomópont elveszti az NFS-megosztáshoz való hozzáférést (/Hana/Shared)

   A SAP HANA erőforrás-ügynökök a `/hana/shared` feladatátvétel során a művelet végrehajtásához tárolt bináris fájloktól függenek. A fájlrendszer az  `/hana/shared` NFS-en keresztül van csatlakoztatva a bemutatott forgatókönyvben.  
   Nehéz szimulálni egy hibát, amelyben az egyik kiszolgáló elveszti az NFS-megosztás elérését. A végrehajtható teszt az, hogy a fájlrendszert csak olvashatóként csatlakoztassa újra.
   Ez a megközelítés ellenőrzi, hogy a fürt képes-e a feladatátvételre, ha `/hana/shared` az aktív csomóponton megszakad a hozzáférés.     


   **Várt eredmény:** Ha `/hana/shared` csak olvasható fájlrendszert használ, a fájlrendszer írási `OCF_CHECK_LEVEL` `hana_shared1` /olvasási műveletét végrehajtó erőforrás attribútuma meghiúsul, mivel nem tud semmit írni a fájlrendszerbe, és a HANA erőforrás-feladatátvételt hajtja végre.  Ugyanez az eredmény várható, ha a HANA-csomópont elveszti az NFS-megosztásokhoz való hozzáférést. 

   Erőforrás állapota a teszt elindítása előtt:

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

   A/Hana/Shared csak olvasható módban helyezhető el az aktív fürtcsomóponton az alábbi parancs használatával:

    ```
    sudo mount -o ro 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    ```

   a hanadb1 a stonith () függvényen beállított művelet alapján újraindítást vagy kikapcsolás-t futtat `pcs property show stonith-action` .  Ha a kiszolgáló (hanadb1) nem áll le, a HANA-erőforrás átkerül a hanadb2. A fürt állapotát a hanadb2 lehet megtekinteni.

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

   Javasoljuk, hogy alaposan tesztelje a SAP HANA-fürt konfigurációját, és végezze el a [telepítő SAP HANA rendszer-replikáció a RHEL](./sap-hana-high-availability-rhel.md#test-the-cluster-setup)-ben című témakörben ismertetett teszteket is.

## <a name="next-steps"></a>Következő lépések

* [Azure Virtual Machines az SAP tervezéséhez és megvalósításához][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP-ban][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hoz][dbms-guide]
* [NFS 4.1-es verziójú kötetek az SAP HANA-hoz készült Azure NetApp Filesban](./hana-vm-operations-netapp.md)
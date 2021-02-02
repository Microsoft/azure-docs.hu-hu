---
title: SAP HANA a SLES-vel való Azure NetApp Files a készenléti állapottal | Microsoft Docs
description: Megtudhatja, hogyan helyezhet üzembe egy SAP HANA kibővíthető rendszerét készenléti csomóponttal az Azure-beli virtuális gépeken a SUSE Linux Enterprise Server Azure NetApp Files használatával.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: radeltch
ms.openlocfilehash: 6da860cf9f116d859657be8b81682088b47474eb
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259151"
---
# <a name="deploy-a-sap-hana-scale-out-system-with-standby-node-on-azure-vms-by-using-azure-netapp-files-on-suse-linux-enterprise-server"></a>SAP HANA kibővíthető rendszer üzembe helyezése készenléti csomóponttal Azure-beli virtuális gépeken Azure NetApp Files használatával SUSE Linux Enterprise Server 

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

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


Ez a cikk azt ismerteti, hogyan helyezhető üzembe egy, az Azure Virtual Machines szolgáltatásban készenléti állapotban lévő, nagymértékben elérhető SAP HANA rendszer egy kibővíthető konfigurációban, a megosztott tároló köteteinek [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) használatával.  

A példában a konfigurációk, a telepítési parancsok és így tovább, a HANA-példány **03** , a HANA rendszer azonosítója pedig **HN1**. A példák a HANA 2,0 SP4-es és az SAP 12 SP4-es SUSE Linux Enterprise Serveron alapulnak. 

Mielőtt elkezdené, tekintse meg a következő SAP-megjegyzéseket és dokumentumokat:

* [Azure NetApp Files dokumentáció][anf-azure-doc] 
* A [1928533] -es SAP-Megjegyzés tartalma:  
  * Az SAP-szoftverek üzembe helyezéséhez támogatott Azure-beli virtuálisgép-méretek listája
  * Fontos kapacitási információk Azure-beli virtuális gépek méreteihez
  * Támogatott SAP-szoftverek és operációs rendszerek (OS) és adatbázis-kombinációk
  * A Windows és a Linux szükséges SAP kernel-verziója a Microsoft Azure
* [2015553]-es SAP-Megjegyzés: az Azure-ban az SAP által támogatott SAP-szoftverek üzembe helyezésének előfeltételeit sorolja fel
* [2205917]-es SAP-Megjegyzés: az SAP-alkalmazások SUSE Linux Enterprise Server ajánlott operációsrendszer-beállításait tartalmazza
* SAP-Megjegyzés [1944799]: az SAP-alkalmazások SUSE Linux Enterprise Server SAP-irányelveit tartalmazza
* [2178632]-es SAP-Megjegyzés: részletes információkat tartalmaz az Azure-beli SAP-ban jelentett összes figyelési metrikáról
* [2191498]-es SAP-Megjegyzés: a szükséges SAP Host Agent-verziót tartalmazza az Azure-beli Linux rendszerhez
* [2243692]-es SAP-Megjegyzés: az Azure-beli Linuxon futó SAP-licenceléssel kapcsolatos információkat tartalmaz
* SAP-Megjegyzés [1984787]: a (z) SUSE Linux Enterprise Server 12 általános információit tartalmazza
* [1999351]-es SAP-Megjegyzés: az SAP-hez készült Azure Enhanced monitoring bővítmény további hibaelhárítási információit tartalmazza.
* [1900823]-es SAP-Megjegyzés: a tárolási követelményekre vonatkozó információkat tartalmaz SAP HANA
* [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): tartalmazza az összes szükséges SAP-megjegyzést Linux rendszerhez
* [Azure Virtual Machines tervezése és implementálása Linux rendszeren az SAP-ban][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP-hez Linux rendszeren][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hez Linux rendszeren][dbms-guide]
* [SUSE SAP ha ajánlott eljárási útmutató][suse-ha-guide]: az összes szükséges információt tartalmazza a NetWeaver magas rendelkezésre állásának beállításához és SAP HANA a helyszíni rendszer-replikáláshoz (általános alapkonfigurációként való használatra), sokkal részletesebb információkat biztosítanak.
* [SUSE magas rendelkezésre állású bővítmény – 12 SP3 kibocsátási megjegyzések][suse-ha-12sp3-relnotes]
* [NetApp SAP-alkalmazások Microsoft Azure a Azure NetApp Files használatával][anf-sap-applications-azure]
* [NFS 4.1-es verziójú kötetek az SAP HANA-hoz készült Azure NetApp Filesban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-netapp)

## <a name="overview"></a>Áttekintés

A HANA magas rendelkezésre állásának eléréséhez az egyik módszer a gazdagép automatikus feladatátvételének konfigurálása. A gazdagép automatikus feladatátvételének konfigurálásához vegyen fel egy vagy több virtuális gépet a HANA rendszerbe, és konfigurálja készenléti csomópontként. Ha az aktív csomópont meghibásodik, a rendszer automatikusan átveszi a készenléti csomópontot. Az Azure Virtual Machines szolgáltatásban bemutatott konfigurációban az Azure NetApp Files-on található [NFS](../../../azure-netapp-files/azure-netapp-files-introduction.md)-sel automatikusan feladatátvételt érhet el.  

> [!NOTE]
> A készenléti csomópontnak hozzá kell férnie az összes adatbázis-kötethez. A HANA-köteteket Nfsv4 névleképezője-kötetként kell csatlakoztatni. A Nfsv4 névleképezője protokoll továbbfejlesztett file Lease-alapú zárolási mechanizmusa a `I/O` kerítéshez használatos. 

> [!IMPORTANT]
> A támogatott konfiguráció létrehozásához a HANA-és a NFSv 4.1-es kötetként kell üzembe helyeznie, és csatlakoztatni kell őket a NFSv 4.1 protokoll használatával. A HANA-gazdagép automatikus feladatátvételi konfigurációja készenléti csomóponttal nem támogatott a NFSv3.

![SAP NetWeaver – magas rendelkezésre állás – áttekintés](./media/high-availability-guide-suse-anf/sap-hana-scale-out-standby-netapp-files-suse.png)

Az előző ábrán, amely SAP HANA hálózati javaslatokat követ, három alhálózatot képvisel egy Azure virtuális hálózaton belül: 
* Ügyfél-kommunikációhoz
* A tárolási rendszerrel folytatott kommunikációhoz
* A HANA csomópontok közötti kommunikációhoz

Az Azure NetApp-kötetek külön alhálózatban találhatók, [Azure NetApp Files delegálva](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).  

Ebben a példában az alhálózatok a következők:  

  - `client` 10.23.0.0/24  
  - `storage` 10.23.2.0/24  
  - `hana` 10.23.3.0/24  
  - `anf` 10.23.1.0/26  

## <a name="set-up-the-azure-netapp-files-infrastructure"></a>A Azure NetApp Files-infrastruktúra beállítása 

Mielőtt folytatná a Azure NetApp Files-infrastruktúra beállítását, ismerkedjen meg a [Azure NetApp Files dokumentációval][anf-azure-doc]. 

Azure NetApp Files több [Azure-régióban](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)is elérhető. Ellenőrizze, hogy a kiválasztott Azure-régió kínál-e Azure NetApp Files.  

Az Azure-régiók Azure NetApp Files rendelkezésre állásáról az [Azure-régió Azure NetApp Files rendelkezésre állása][anf-avail-matrix]című témakörben olvashat bővebben.  

A Azure NetApp Files üzembe helyezése előtt kérje a bevezetést a Azure NetApp Filesra, ha [Azure NetApp Files útmutatást szeretne regisztrálni][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Azure NetApp Files erőforrások üzembe helyezése  

Az alábbi utasítások azt feltételezik, hogy már üzembe helyezte az Azure-beli [virtuális hálózatot](../../../virtual-network/virtual-networks-overview.md). A Azure NetApp Files erőforrásokat és virtuális gépeket, amelyeken a Azure NetApp Files erőforrásokat csatlakoztatni kell, ugyanabban az Azure-beli virtuális hálózatban vagy az Azure-beli virtuális hálózatokban kell telepíteni.  

1. Ha még nem telepítette az erőforrásokat, kérje a [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md)bevezetését.  

2. Hozzon létre egy NetApp-fiókot a kiválasztott Azure-régióban a [NetApp-fiók létrehozása](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)című részben található utasításokat követve.  

3. Hozzon létre egy Azure NetApp Files kapacitási készletet a [Azure NetApp Files kapacitásának beállítása](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)című részben leírtak szerint.  

   A cikkben bemutatott HANA-architektúra az *Ultra Service* szintjén egyetlen Azure NetApp Files kapacitású készletet használ. Az Azure-beli HANA-alapú számítási feladatokhoz ajánlott Azure NetApp Files *Ultra* vagy *prémium* [szintű szolgáltatási szintet](../../../azure-netapp-files/azure-netapp-files-service-levels.md)használni.  

4. Alhálózat delegálása Azure NetApp Filesre, az [alhálózat delegálása Azure NetApp Filesra](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)című részben leírtak szerint.  

5. Azure NetApp Files kötetek üzembe helyezéséhez kövesse az [NFS-kötet létrehozása a Azure NetApp Files számára](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)című témakör utasításait.  

   A kötetek központi telepítésekor ügyeljen arra, hogy a **nfsv 4.1** verziót válassza. Jelenleg a NFSv 4.1-hez való hozzáférés szükséges egy engedélyezési listához való hozzáadáshoz. Telepítse a köteteket a kijelölt Azure NetApp Files [alhálózatban](/rest/api/virtualnetwork/subnets). Az Azure NetApp-kötetek IP-címeit a rendszer automatikusan hozzárendeli. 
   
   Ne feledje, hogy a Azure NetApp Files erőforrásoknak és az Azure-beli virtuális gépeknek ugyanabban az Azure-beli virtuális hálózaton vagy az Azure-beli virtuális hálózatokban kell lenniük. Például a **HN1**-mnt00001, a **HN1**-log-mnt00001, és így tovább, a kötetek nevei és NFS://10.23.1.5/**HN1**-mnt00001, NFS://10.23.1.4/**HN1**-log-mnt00001 és így tovább, a fájlok elérési útja a Azure NetApp Files kötetek számára.  

   * Volume **HN1**-mnt00001 (NFS://10.23.1.5/**HN1**-adatforgalom-mnt00001)
   * Volume **HN1**-mnt00002 (NFS://10.23.1.6/**HN1**-adatforgalom-mnt00002)
   * kötet **HN1**-log-mnt00001 (NFS://10.23.1.4/**HN1**-log-mnt00001)
   * kötet **HN1**-log-mnt00002 (NFS://10.23.1.6/**HN1**-log-mnt00002)
   * mennyiségi **HN1**– megosztott (NFS://10.23.1.4/**HN1**-Shared)
   
   Ebben a példában egy külön Azure NetApp Files kötetet használtunk minden HANA-adathalmazhoz és a naplózási kötethez. A kisebb vagy nem produktív rendszerek költséghatékony konfigurációjának érdekében minden adatcsatlakoztatást és naplót egyetlen kötetre helyezhet el.  

### <a name="important-considerations"></a>Fontos szempontok

Az SAP NetWeaver SUSE magas rendelkezésre állású architektúrán való Azure NetApp Files létrehozásakor vegye figyelembe a következő fontos szempontokat:

- A minimális kapacitási készlet 4 tebibájt (TiB).  
- A minimális kötet mérete 100 gibibájtban értendők (GiB).
- Azure NetApp Files és az összes virtuális gépet, amelybe a Azure NetApp Files köteteket csatlakoztatni kell, ugyanabban az Azure-beli virtuális hálózatban vagy azonos régióban lévő, egymással azonos [virtuális hálózatokban](../../../virtual-network/virtual-network-peering-overview.md) kell lennie.  
- A kiválasztott virtuális hálózatnak rendelkeznie kell egy Azure NetApp Files delegált alhálózattal.
- Egy Azure NetApp Files kötet átviteli sebessége a mennyiségi kvóta és a szolgáltatási szint függvénye a [Azure NetApp Files szolgáltatási szintjén](../../../azure-netapp-files/azure-netapp-files-service-levels.md)dokumentálva. A HANA Azure NetApp-kötetek méretezése során győződjön meg arról, hogy az eredményül kapott átviteli sebesség megfelel a HANA rendszerkövetelményeinek.  
- Az Azure NetApp Files [exportálási házirend](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)segítségével szabályozhatja az engedélyezett ügyfeleket, a hozzáférési típust (írható és olvasható, csak olvasható stb.). 
- A Azure NetApp Files funkció még nem tud zónával foglalkozni. Jelenleg a funkció nincs telepítve az Azure-régió összes rendelkezésre állási zónájában. Vegye figyelembe, hogy egyes Azure-régiókban lehetséges a késés következményei.  
-  

> [!IMPORTANT]
> SAP HANA munkaterhelések esetében a kis késleltetés kritikus fontosságú. A Microsoft képviselőjével együttműködve biztosíthatja, hogy a virtuális gépek és a Azure NetApp Files kötetek központi telepítése közel legyen.  

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>HANA-adatbázis méretezése Azure NetApp Files

Egy Azure NetApp Files kötet átviteli sebessége a kötet méretének és a szolgáltatási szintnek a függvénye, amely a [Azure NetApp Files szolgáltatási szintjén](../../../azure-netapp-files/azure-netapp-files-service-levels.md)van dokumentálva. 

Az Azure-beli SAP-infrastruktúra tervezésekor vegye figyelembe az SAP által igényelt minimális tárolási követelményeket, amelyek az átviteli sebesség minimális jellemzőire fordíthatók:

- Engedélyezze az írható-olvasható 250 megabájt/másodperc (MB/s)/Hana/log, 1 MB méretű I/O-mérettel.  
- Engedélyezheti legalább 400 MB/s olvasási tevékenységet a/Hana/Data 16 MB-os és 64-MB I/O-méretekhez.  
- Engedélyezze az írási tevékenységet legalább 250 MB/s értéknél a 16 MB-os és a 64-MB I/O-méretekkel rendelkező/Hana/Data. 

A [Azure NetApp Files átviteli sebességre vonatkozó határértékek](../../../azure-netapp-files/azure-netapp-files-service-levels.md) 1 TiB-ra vetítve:
- Premium Storage szintű 64 MiB/s  
- Ultra Storage-réteg – 128 MiB/s  

Az adatokhoz és a naplóhoz szükséges minimális teljesítménybeli követelmények teljesítéséhez, valamint a/Hana/Shared vonatkozó irányelvekhez az ajánlott méretek a következők:

| Kötet | Méret<br>Premium Storagei szintű | Méret<br>Ultra Storage-rétegek | Támogatott NFS-protokoll |
| --- | --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v 4.1 |
| /hana/data | 6,3 TiB | 3,2 TiB | v 4.1 |
| /hana/shared | Max (512 GB, 1xRAM)/4 feldolgozó csomópont | Max (512 GB, 1xRAM)/4 feldolgozó csomópont | v3 vagy v 4.1 |

A cikkben bemutatott elrendezés SAP HANA konfigurációja a Azure NetApp Files Ultra Storage-rétegek használatával a következő lenne:

| Kötet | Méret<br>Ultra Storage-rétegek | Támogatott NFS-protokoll |
| --- | --- | --- |
| /hana/log/mnt00001 | 2 TiB | v 4.1 |
| /hana/log/mnt00002 | 2 TiB | v 4.1 |
| /hana/data/mnt00001 | 3,2 TiB | v 4.1 |
| /hana/data/mnt00002 | 3,2 TiB | v 4.1 |
| /hana/shared | 2 TiB | v3 vagy v 4.1 |

> [!NOTE]
> Az itt ismertetett Azure NetApp Files méretezési javaslatok célja, hogy megfeleljenek az SAP által az infrastruktúra-szolgáltatók számára javasolt minimális követelményeknek. A valós ügyfelek központi telepítései és számítási feladatainak esetében ezek a méretek nem elégségesek. Ezeket a javaslatokat kiindulási pontként és alkalmazkodva használhatja az adott számítási feladatra vonatkozó követelmények alapján.  

> [!TIP]
> Azure NetApp Files köteteket dinamikusan átméretezheti, anélkül, hogy le kellene *választania* a köteteket, le kell állítania a virtuális gépeket, vagy le kell állítania a SAP HANA. Ez a megközelítés lehetővé teszi a rugalmasságot, hogy megfeleljen az alkalmazás várható és előre nem látható átviteli követelményeinek.

## <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Linux rendszerű virtuális gépek üzembe helyezése a Azure Portal használatával

Először létre kell hoznia a Azure NetApp Files köteteket. Ezután hajtsa végre a következő lépéseket:
1. Hozza létre az Azure-beli [virtuális hálózati](../../../virtual-network/virtual-network-manage-subnet.md) alhálózatokat az [Azure Virtual Networkben](../../../virtual-network/virtual-networks-overview.md). 
1. Telepítse a virtuális gépeket. 
1. Hozza létre a további hálózati adaptereket, és csatolja a hálózati adaptereket a megfelelő virtuális gépekhez.  

   Mindegyik virtuális gépnek három hálózati adaptere van, amelyek megfelelnek a három Azure-beli virtuális hálózati alhálózatnak ( `client` `storage` és `hana` ). 

   További információkért lásd: [Linux rendszerű virtuális gép létrehozása az Azure-ban több hálózati kártyával](../../linux/multiple-nics.md).  

> [!IMPORTANT]
> SAP HANA munkaterhelések esetében a kis késleltetés kritikus fontosságú. Az alacsony késés érdekében működjön együtt a Microsoft-képviselőjével, hogy a virtuális gépek és a Azure NetApp Files kötetek központi telepítése közel legyen. Ha [új SAP HANA](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) , SAP HANA Azure NetApp filest használó rendszer bevezetését végzi, küldje el a szükséges információkat. 
 
A következő utasítások feltételezik, hogy már létrehozta az erőforráscsoportot, az Azure-beli virtuális hálózatot és a három Azure-beli virtuális hálózati alhálózatot: `client` `storage` és `hana` . Ha telepíti a virtuális gépeket, válassza ki az ügyfél alhálózatát, hogy az ügyfél hálózati adaptere legyen az elsődleges felület a virtuális gépeken. Egy explicit útvonalat is konfigurálnia kell a Azure NetApp Files delegált alhálózatra a Storage alhálózati átjárón keresztül. 

> [!IMPORTANT]
> Győződjön meg arról, hogy a kiválasztott operációs rendszer SAP-tanúsítvánnyal rendelkezik az adott virtuálisgép-típusok SAP HANAához. A SAP HANA Certified VM-típusok és operációsrendszer-kiadások listáját az adott típusokhoz a [SAP HANA Certified IaaS Platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) webhelyen teheti meg. Kattintson a felsorolt virtuálisgép-típus részleteire az adott típushoz tartozó SAP HANA által támogatott operációsrendszer-kiadások teljes listájának lekéréséhez.  

1. Hozzon létre egy rendelkezésre állási készletet a SAP HANAhoz. Ügyeljen arra, hogy a maximális frissítési tartományt állítsa be.  

2. Hozzon létre három virtuális gépet (**hanadb1**, **hanadb2**, **hanadb3**) az alábbi lépések végrehajtásával:  

   a. A SAP HANA által támogatott Azure-katalógusban SLES4SAP-rendszerképet használhat. Ebben a példában egy SLES4SAP 12 SP4-es rendszerképet használunk.  

   b. Válassza ki azt a rendelkezésre állási készletet, amelyet korábban a SAP HANAhoz hozott létre.  

   c. Válassza ki az ügyfél Azure virtuális hálózati alhálózatát. Válassza a [gyorsított hálózat](../../../virtual-network/create-vm-accelerated-networking-cli.md)lehetőséget.  

   A virtuális gépek központi telepítésekor a rendszer automatikusan létrehozza a hálózati adapter nevét. Az egyszerűség kedvéért ebben az útmutatóban az automatikusan generált hálózati adapterekre fogunk hivatkozni, amelyek az ügyfél Azure virtuális hálózati alhálózatához vannak csatolva, mint a **hanadb1**, a **hanadb2-Client** és a **hanadb3-Client**. 

3. Hozzon létre három hálózati adaptert, egyet az egyes virtuális gépekhez a virtuális hálózat alhálózatához `storage` (ebben a példában a **hanadb1**, a **hanadb2** és a **hanadb3-** tárolót).  

4. Hozzon létre három hálózati adaptert, egyet az egyes virtuális gépekhez a virtuális hálózat alhálózatához `hana`  (ebben a példában a **hanadb1-Hana**, a **hanadb2-Hana** és a **hanadb3-Hana**).  

5. Csatlakoztassa az újonnan létrehozott virtuális hálózati adaptereket a megfelelő virtuális gépekhez az alábbi lépések végrehajtásával:  

    a. Nyissa meg a [Azure Portal](https://portal.azure.com/#home)a virtuális gépet.  

    b. A bal oldali ablaktáblán válassza a **Virtual Machines** lehetőséget. Szűrje a virtuális gép nevét (például **hanadb1**), majd válassza ki a virtuális gépet.  

    c. Az **Áttekintés** ablaktáblán válassza a **Leállítás** elemet a virtuális gép felszabadításához.  

    d. Válassza a **hálózatkezelés** lehetőséget, majd csatlakoztassa a hálózati adaptert. A **hálózati adapter csatolása** legördülő listában válassza ki a már létrehozott hálózati adaptereket és az `storage` `hana` alhálózatokat.  
    
    e. Kattintson a **Mentés** gombra. 
 
    f. Ismételje meg a b – e lépéseket a fennmaradó virtuális gépek esetében (példánkban a  **hanadb2** és a **hanadb3**).
 
    : A virtuális gépeket most leállított állapotban hagyja. Ezután engedélyezzük a [gyorsított hálózatkezelést](../../../virtual-network/create-vm-accelerated-networking-cli.md) az újonnan csatlakoztatott hálózati adapterek esetében.  

6. A következő lépések végrehajtásával engedélyezheti a gyorsított hálózatkezelést az `storage` és az alhálózatok további hálózati adapterei számára `hana` :  

    a. Nyissa meg [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) a [Azure Portalban](https://portal.azure.com/#home).  

    b. Hajtsa végre a következő parancsokat a gyorsított hálózatkezelés engedélyezéséhez a további hálózati adapterekhez, amelyek a `storage` és az `hana` alhálózatokhoz vannak csatolva.  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-storage</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true

    </code></pre>

7. Indítsa el a virtuális gépeket a következő lépések végrehajtásával:  

    a. A bal oldali ablaktáblán válassza a **Virtual Machines** lehetőséget. Szűrje a virtuális gép nevét (például **hanadb1**), majd jelölje ki.  

    b. Az **Áttekintés** ablaktáblán válassza az **Indítás** lehetőséget.  

## <a name="operating-system-configuration-and-preparation"></a>Operációs rendszer konfigurációja és előkészítése

A következő részekben található utasítások a következők egyikével vannak ellátva:
* **[A]**: az összes csomópontra érvényes
* **[1]**: csak az 1. csomópontra érvényes
* **[2]**: csak a 2. csomópontra vonatkozik
* **[3]**: csak a 3. csomópontra vonatkozik

Konfigurálja és készítse elő az operációs rendszert a következő lépések végrehajtásával:

1. **[A]** a gazdagép fájljainak karbantartása a virtuális gépeken. Bejegyzések belefoglalása az összes alhálózathoz. Ehhez a példához a következő bejegyzések lettek hozzáadva `/etc/hosts` .  

    <pre><code>
    # Storage
    10.23.2.4   hanadb1-storage
    10.23.2.5   hanadb2-storage
    10.23.2.6   hanadb3-storage
    # Client
    10.23.0.5   hanadb1
    10.23.0.6   hanadb2
    10.23.0.7   hanadb3
    # Hana
    10.23.3.4   hanadb1-hana
    10.23.3.5   hanadb2-hana
    10.23.3.6   hanadb3-hana
    </code></pre>

2. **[A]** módosítsa a DHCP és a felhő konfigurációs beállításait a tárolási hálózati adapterhez, hogy elkerülje a nem kívánt állomásnév-módosításokat.  

    Az alábbi utasítások azt feltételezik, hogy a tárolási hálózati adapter `eth1` . 

    <pre><code>
    vi /etc/sysconfig/network/dhcp
    # Change the following DHCP setting to "no"
    DHCLIENT_SET_HOSTNAME="no"
    vi /etc/sysconfig/network/ifcfg-<b>eth1</b>
    # Edit ifcfg-eth1 
    #Change CLOUD_NETCONFIG_MANAGE='yes' to "no"
    CLOUD_NETCONFIG_MANAGE='no'
    </code></pre>

2. **[A]** adjon hozzá egy hálózati útvonalat, hogy a Azure NetApp Files felé irányuló kommunikáció a Storage hálózati adapteren keresztül történjen.  

    Az alábbi utasítások azt feltételezik, hogy a tárolási hálózati adapter `eth1` .  

    <pre><code>
    vi /etc/sysconfig/network/ifroute-<b>eth1</b>
    # Add the following routes 
    # RouterIPforStorageNetwork - - -
    # ANFNetwork/cidr RouterIPforStorageNetwork - -
    <b>10.23.2.1</b> - - -
    <b>10.23.1.0/26</b> <b>10.23.2.1</b> - -
    </code></pre>

    A módosítások aktiválásához indítsa újra a virtuális gépet.  

3. **[A]** készítse elő az operációs rendszert a NetApp rendszereken futó SAP HANA futtatásához az NFS használatával, a [NetApp SAP applications on Microsoft Azure using Azure NetApp Files használatával][anf-sap-applications-azure]. Hozza létre a */etc/sysctl.d/NetApp-Hana.conf* konfigurációs fájlját a NetApp konfigurációs beállításaihoz.  

    <pre><code>
    vi /etc/sysctl.d/netapp-hana.conf
    # Add the following entries in the configuration file
    net.core.rmem_max = 16777216
    net.core.wmem_max = 16777216
    net.core.rmem_default = 16777216
    net.core.wmem_default = 16777216
    net.core.optmem_max = 16777216
    net.ipv4.tcp_rmem = 65536 16777216 16777216
    net.ipv4.tcp_wmem = 65536 16777216 16777216
    net.core.netdev_max_backlog = 300000
    net.ipv4.tcp_slow_start_after_idle=0
    net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **[A]** konfigurációs fájl */etc/sysctl.d/MS-az.conf* létrehozása a Microsoft Azure-beli konfigurációs beállításaival.  

    <pre><code>
    vi /etc/sysctl.d/ms-az.conf
    # Add the following entries in the configuration file
    ipv6.conf.all.disable_ipv6 = 1
    net.ipv4.tcp_max_syn_backlog = 16348
    net.ipv4.conf.all.rp_filter = 0
    sunrpc.tcp_slot_table_entries = 128
    vm.swappiness=10
    </code></pre>

> [!TIP]
> Ne állítson be net.ipv4.ip_local_port_range és net.ipv4.ip_local_reserved_ports explicit módon a sysctl konfigurációs fájljaiban, hogy az SAP-gazdagép ügynöke kezelhesse a porttartomány-tartományokat. További részletek: SAP-Megjegyzés [2382421](https://launchpad.support.sap.com/#/notes/2382421).  

4. **[A]** a sunrpc beállításait a [NetApp SAP-alkalmazásokban javasolt Microsoft Azure a Azure NetApp Files használatával][anf-sap-applications-azure].  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

## <a name="mount-the-azure-netapp-files-volumes"></a>A Azure NetApp Files kötetek csatlakoztatása

1. **[A]** csatlakoztatási pontok létrehozása a HANA-adatbázis köteteihez.  

    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** a **HN1** megosztott/usr/SAP tartozó csomópont-specifikus könyvtárak létrehozása.  

    <pre><code>
    # Create a temporary directory to mount <b>HN1</b>-shared
    mkdir /mnt/tmp
    # if using NFSv3 for this volume, mount with the following command
    mount <b>10.23.1.4</b>:/<b>HN1</b>-shared /mnt/tmp
    # if using NFSv4.1 for this volume, mount with the following command
    mount -t nfs -o sec=sys,vers=4.1 <b>10.23.1.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** ellenőrizze az NFS-tartomány beállítását. Győződjön meg arról, hogy a tartomány alapértelmezett Azure NetApp Files-tartományként van konfigurálva, azaz **`defaultv4iddomain.com`** a leképezés értéke **senki**.  

    > [!IMPORTANT]
    > Ügyeljen arra, hogy az NFS-tartományt a virtuális gépen állítsa be, `/etc/idmapd.conf` hogy az megfeleljen az alapértelmezett tartományi konfigurációnak Azure NetApp Files: **`defaultv4iddomain.com`** . Ha az NFS-ügyfél (azaz a virtuális gép) és az NFS-kiszolgáló (például az Azure NetApp-konfiguráció) közötti eltérés nem egyezik, akkor a virtuális gépekre csatlakoztatott Azure NetApp-köteteken található fájlok engedélyei a következőképpen jelennek meg `nobody` .  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Verbosity = 0
    Pipefs-Directory = /var/lib/nfs/rpc_pipefs
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** ellenőrzés `nfs4_disable_idmapping` . Értékeként az **Y** értéknek kell lennie. A-t tartalmazó könyvtár-struktúra létrehozásához `nfs4_disable_idmapping` hajtsa végre a csatlakoztatási parancsot. Nem lehet manuálisan létrehozni a könyvtárat a/sys/modules alatt, mivel a hozzáférés a kernel/illesztőprogramok számára van fenntartva.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.23.1.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

5. **[A]** manuálisan hozza létre a SAP HANA csoportot és a felhasználót. A Group sapsys és a User **hn1** adm azonosítóit ugyanahhoz az azonosítóhoz kell beállítani, amelyek a bevezetéskor vannak megadva. (Ebben a példában az azonosítók értéke **1001**.) Ha az azonosítók nincsenek megfelelően beállítva, akkor nem fog tudni hozzáférni a kötetekhez. A Group sapsys és a **hn1** adm és sapadm felhasználói fiókjainak azonosítóinak azonosnak kell lenniük az összes virtuális gépen.  

    <pre><code>
    # Create user group 
    sudo groupadd -g 1001 sapsys
    # Create  users 
    sudo useradd <b>hn1</b>adm -u 1001 -g 1001 -d /usr/sap/<b>HN1</b>/home -c "SAP HANA Database System" -s /bin/sh
    sudo useradd sapadm -u 1002 -g 1001 -d /home/sapadm -c "SAP Local Administrator" -s /bin/sh
    # Set the password  for both user ids
    sudo passwd hn1adm
    sudo passwd sapadm
    </code></pre>

6. **[A]** csatlakoztassa a megosztott Azure NetApp Files köteteket.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.5:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **[1]** a csomópont-specifikus kötetek csatlakoztatása a **hanadb1**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** a csomópont-specifikus kötetek csatlakoztatása a **hanadb2**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** a csomópont-specifikus kötetek csatlakoztatása a **hanadb3**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** ellenőrizze, hogy az összes HANA-kötet csatlakoztatva van-e az NFS protokoll **nfsv4 névleképezője**-verziójához.  

    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.23.1.5:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.5
    /hana/log/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/data/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/log/<b>HN1</b>/mnt00001 from 10.23.1.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /usr/sap/<b>HN1</b> from 10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /hana/shared from 10.23.1.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    </code></pre>

## <a name="installation"></a>Telepítés  

Ebben a példában a SAP HANA üzembe helyezéséhez az Azure-ban készenléti csomóponttal rendelkező kibővített konfigurációban a HANA 2,0 SP4-et használtuk.  

### <a name="prepare-for-hana-installation"></a>A HANA telepítésének előkészítése

1. **[A]** a HANA telepítésének megkezdése előtt állítsa be a legfelső szintű jelszót. A legfelső szintű jelszót a telepítés befejezése után tilthatja le. Végrehajtás `root` parancsként `passwd` .  

2. **[1]** győződjön meg róla, hogy SSH-n keresztül tud bejelentkezni a **hanadb2** és a **hanadb3**-be anélkül, hogy jelszót kellene megadnia.  

    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** telepítsen további csomagokat, amelyek szükségesek a HANA 2,0 SP4-hez. További információ: SAP Note [2593824](https://launchpad.support.sap.com/#/notes/2593824). 

    <pre><code>
    sudo zypper install libgcc_s1 libstdc++6 libatomic1 
    </code></pre>

4. **[2], [3]** SAP HANA és címtárak tulajdonosának módosítása `data` `log` az **hn1** adm-re.   

    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

### <a name="hana-installation"></a>HANA-telepítés

1. **[1]** telepítse a SAP HANA az [SAP HANA 2,0 telepítési és frissítési útmutató](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html)utasításait követve. Ebben a példában a főkiszolgálóval, egy feldolgozóval és egy készenléti csomóponttal rendelkező SAP HANA bővítjük.  

   a. Indítsa el a **hdblcm** programot a HANA telepítési szoftver könyvtárából. Használja a `internal_network` paramétert, és adja át a címtartomány az alhálózat számára, amely a belső HANA-csomópontok közötti kommunikációhoz használatos.  

    <pre><code>
    ./hdblcm --internal_network=10.23.3.0/24
    </code></pre>

   b. A parancssorba írja be a következő értékeket:

     * **Válasszon műveletet**: **1** . lépés (telepítéshez)
     * **További összetevők a telepítéshez**: írja be a **2, 3** értéket
     * Telepítési útvonal: nyomja le az ENTER billentyűt (az alapértelmezett érték a/Hana/Shared)
     * **Helyi állomásnév** esetén: nyomja le az ENTER billentyűt az alapértelmezett érték elfogadásához.
     * A alá kívánja **adni az állomásokat a rendszeren?**: ENTER **y**
     * **Vesszővel tagolt állomásnevek** esetén adja meg a következőt: **hanadb2, hanadb3**
     * A **gyökér felhasználónévnél** [root]: nyomja le az ENTER billentyűt az alapértelmezett érték elfogadásához
     * A **gyökér felhasználói jelszava**: írja be a root felhasználó jelszavát
     * A gazdagépek hanadb2 tartozó szerepkörök esetében: **1**  megadása (feldolgozói)
     * A gazdagép hanadb2 tartozó **feladatátvételi csoport** esetében [alapértelmezett]: az ENTER billentyű lenyomásával fogadja el az alapértelmezett értéket.
     * A gazdagép hanadb2 [<>] **tárolási partíciójának száma** <assign automatically> : az ENTER billentyű lenyomásával fogadja el az alapértelmezett értéket.
     * A gazdagép hanadb2 tartozó **munkavégző csoport** esetében [alapértelmezett]: az ENTER billentyű lenyomásával fogadja el az alapértelmezett értéket.
     * A gazdagép hanadb3 tartozó **szerepkörök kiválasztása** : írja be a **2** értéket (készenléti állapotba)
     * A gazdagép hanadb3 tartozó **feladatátvételi csoport** esetében [alapértelmezett]: az ENTER billentyű lenyomásával fogadja el az alapértelmezett értéket.
     * A gazdagép hanadb3 tartozó **munkavégző csoport** esetében [alapértelmezett]: az ENTER billentyű lenyomásával fogadja el az alapértelmezett értéket.
     * **SAP HANA rendszer-azonosító**: írja be a **HN1**
     * **Példány száma** [00]: **03**
     * **Helyi gazdagép munkavégző csoportjának** [alapértelmezett]: az ENTER billentyű lenyomásával fogadja el az alapértelmezett értéket.
     * **Válassza a rendszerhasználat/ENTER index [4] lehetőséget**: írja be a **4** értéket (egyéni)
     * Az **adatkötetek helyének** eléréséhez [/Hana/Data/HN1]: nyomja le az ENTER billentyűt az alapértelmezett érték elfogadásához.
     * A **log kötetek helye** [/Hana/log/HN1]: az ENTER billentyű lenyomásával fogadja el az alapértelmezett értéket.
     * A **maximális memória kiosztásának korlátozása?** [n]: ENTER **n**
     * A (z) [hanadb1] **gazdagép hanadb1** : az ENTER billentyű lenyomásával fogadja el az alapértelmezett értéket.
     * A (z) [hanadb2] **gazdagép hanadb2** : az ENTER billentyű lenyomásával fogadja el az alapértelmezett értéket.
     * A (z) [hanadb3] **gazdagép hanadb3** : az ENTER billentyű lenyomásával fogadja el az alapértelmezett értéket.
     * A rendszergazda **(hn1adm) jelszava**: írja be a jelszót.
     * **Rendszeradatbázis-felhasználó (rendszer) jelszava**: adja meg a rendszer jelszavát.
     * **Rendszeradatbázis-felhasználó (rendszer) jelszavának megerősítése**: írja be a rendszer jelszavát.
     * **Újraindítási rendszer a gép újraindítása után?** [n]: ENTER **n** 
     * A **folytatja a folytatást (i/n)**: érvényesítse az összegzést, és ha minden jól látható, írja be az **y** értéket.


2. **[1]** global.ini ellenőrzése  

   Jelenítse meg global.ini, és győződjön meg arról, hogy a belső SAP HANA csomópontok közötti kommunikáció konfigurációja van érvényben. Ellenőrizze a **kommunikáció** szakaszát. Meg kell adni a címtartományt az `hana` alhálózathoz, és a következőre kell `listeninterface` állítani: `.internal` . Ellenőrizze a **internal_hostname_resolution** szakaszt. Az alhálózathoz tartozó HANA virtuális gépek IP-címeinek kell lenniük `hana` .  

   <pre><code>
    sudo cat /usr/sap/<b>HN1</b>/SYS/global/hdb/custom/config/global.ini
    # Example 
    #global.ini last modified 2019-09-10 00:12:45.192808 by hdbnameserve
    [communication]
    internal_network = <b>10.23.3/24</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.23.3.4</b> = <b>hanadb1</b>
    <b>10.23.3.5</b> = <b>hanadb2</b>
    <b>10.23.3.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** adja hozzá az állomás-hozzárendelést annak biztosításához, hogy az ügyfél IP-címei az ügyfél-kommunikációhoz legyenek használva. Vegyen fel szakaszt `public_host_resolution` , és adja hozzá a megfelelő IP-címeket az ügyfél alhálózatáról.  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.23.0.5</b>
    map_<b>hanadb2</b> = <b>10.23.0.6</b>
    map_<b>hanadb3</b> = <b>10.23.0.7</b>
   </code></pre>

4. **[1]** indítsa újra SAP HANA a módosítások aktiválásához.  

   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** ellenőrizze, hogy az ügyfél felülete az alhálózat IP-címeit fogja-e használni a `client` kommunikációhoz.  

   <pre><code>
    sudo -u hn1adm /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.23.0.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.23.0.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.23.0.5</b>"
   </code></pre>

   További információ a konfiguráció ellenőrzéséről: SAP Note [2183363 – SAP HANA belső hálózat konfigurálása](https://launchpad.support.sap.com/#/notes/2183363).  

6. Az alapul szolgáló Azure NetApp Files-tároló SAP HANA optimalizálásához állítsa be a következő SAP HANA paramétereket:

   - `max_parallel_io_requests`**128**
   - `async_read_submit`**bekapcsolva**
   - `async_write_submit_active`**bekapcsolva**
   - `async_write_submit_blocks`**az összes**

   További információ: [NETAPP SAP-alkalmazások Microsoft Azure a Azure NetApp Files használatával][anf-sap-applications-azure]. 

   A SAP HANA 2,0 rendszertől kezdve a paramétereket a ben állíthatja be `global.ini` . További információ: SAP Note [1999930](https://launchpad.support.sap.com/#/notes/1999930).  
   
   A SAP HANA 1,0 rendszerű SPS12 és korábbi verziók esetében ezek a paraméterek a telepítés során állíthatók be a [2267798](https://launchpad.support.sap.com/#/notes/2267798)-es SAP-megjegyzésben leírtak szerint.  

7. Az Azure NetApp Files által használt tárterületnek 16 terabájt (TB) fájlméretre vonatkozó korlátozása van. A SAP HANA nem ismeri implicit módon a tárolási korlátozást, és nem hoz létre automatikusan új adatfájlt, ha eléri a 16 TB-os fájlméretet. Mivel a SAP HANA 16 TB-nál nagyobb mennyiségű fájlt próbálnak növelni, ez a kísérlet hibákat eredményez, és végül egy index-kiszolgáló összeomlik. 

   > [!IMPORTANT]
   > Ha meg szeretné akadályozni, hogy SAP HANA ne növelje az adatfájlokat a tárolási alrendszer [16 TB-os korlátján](../../../azure-netapp-files/azure-netapp-files-resource-limits.md) túl, állítsa be a következő paramétereket a alkalmazásban `global.ini` .  
   > - datavolume_striping = igaz
   > - datavolume_striping_size_gb = 15000 további információ: SAP Note [2400005](https://launchpad.support.sap.com/#/notes/2400005).
   > Vegye figyelembe a [2631285](https://launchpad.support.sap.com/#/notes/2631285)-es SAP-megjegyzést. 

## <a name="test-sap-hana-failover"></a>Feladatátvételi SAP HANA tesztelése 

> [!NOTE]
> Ez a cikk a " *Master* " és a " *Slave*" kifejezésekre mutató hivatkozásokat tartalmaz, amelyeket a Microsoft már nem használ. Ha eltávolítja ezeket a feltételeket a szoftverből, a rendszer eltávolítja őket ebből a cikkből.

1. Csomópont-összeomlás szimulálása egy SAP HANA munkavégző csomóponton. Tegye a következőket: 

   a. A csomópont összeomlásának szimulálása előtt futtassa a következő parancsokat a **hn1** adm használatával a környezet állapotának rögzítéséhez:  

   <pre><code>
    # Check the landscape status
    python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
   </code></pre>

   b. A csomópontok összeomlásának szimulálásához futtassa a következő parancsot gyökérként a munkavégző csomóponton, amely ebben az esetben **hanadb2** :  
   
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   c. A rendszer figyelése a feladatátvétel befejezéséhez. A feladatátvétel befejezése után rögzítse az állapotot, amelynek az alábbihoz hasonlóan kell kinéznie:  

    <pre><code>
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    # Check the landscape status
    /usr/sap/HN1/HDB03/exe/python_support> python landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | no     | info   |          |        |         2 |         0 | default  | default  | master 2   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb3 | yes    | info   |          |        |         0 |         2 | default  | default  | master 3   | slave      | standby     | slave       | standby | worker  | default | default |
   </code></pre>

   > [!IMPORTANT]
   > Ha egy csomópont kernel pánikot tapasztal, a SAP HANA feladatátvételsel való késés elkerülése érdekében állítsa `kernel.panic` 20 másodpercre az *összes* HANA virtuális gépen. A konfiguráció a alkalmazásban történik `/etc/sysctl` . A módosítás aktiválásához indítsa újra a virtuális gépeket. Ha ezt a változást nem hajtja végre, a feladatátvétel 10 vagy több percet is igénybe vehet, amikor egy csomópont megtapasztalja a kernel pánikot.  

2. A következő lépésekkel megöli a kiszolgálónevet:

   a. A teszt előtt ellenőrizze a környezet állapotát a következő parancsok **hn1** adm-ként való futtatásával:  

   <pre><code>
    #Landscape status 
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
   </code></pre>

   b. Futtassa az alábbi parancsokat **hn1** adm-ként az aktív főcsomóponton, amely ebben az esetben **hanadb1** :  

    <pre><code>
        hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
    </code></pre>
    
    A készenléti csomópont **hanadb3** átveszi a fő csomópontot. A feladatátvételi teszt befejezése után itt látható az erőforrás állapota:  

    <pre><code>
        # Check the instance status
        sapcontrol -nr 03 -function GetSystemInstanceList
        GetSystemInstanceList
        OK
        hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
        hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
        hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
        hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
        # Check the landscape status
        python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
        | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
        |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
        |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
        | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
        | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
        | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
        | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
    </code></pre>

   c. Indítsa újra a HANA-példányt a **hanadb1** (azaz ugyanazon a virtuális gépen, ahol a névkiszolgáló le lett megölve). A **hanadb1** csomópont újra csatlakozik a környezethez, és megtartja a készenléti szerepkörét.  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   A **hanadb1** SAP HANA elindítása után a következő állapotot kell elvárnia:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03 -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   d. Ismételje meg a kiszolgálónevet a jelenleg aktív főcsomóponton (azaz a csomópont **hanadb3**).  
   
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   A csomópont **hanadb1** folytatja a fő csomópont szerepkörét. A feladatátvételi teszt befejezése után az állapot a következőképpen fog kinézni:

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

   e. Indítsa el SAP HANA a **hanadb3**, amely készen áll arra, hogy készenléti csomópontként szolgáljon.  

   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Miután SAP HANA megkezdődött a **hanadb3**, az állapot a következőhöz hasonlít:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

## <a name="next-steps"></a>Következő lépések

* [Azure Virtual Machines az SAP tervezéséhez és megvalósításához][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP-ban][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hoz][dbms-guide]
* [NFS 4.1-es verziójú kötetek az SAP HANA-hoz készült Azure NetApp Filesban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-netapp)
* A magas rendelkezésre állás és a SAP HANA Azure-beli virtuális gépeken történő vész-helyreállítási tervének megismeréséhez tekintse meg a [SAP HANA magas rendelkezésre állását az azure Virtual Machinesban (VM)][sap-hana-ha].

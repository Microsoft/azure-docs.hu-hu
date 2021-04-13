---
title: SAP HANA a HSR és a pacemakert a SLES-ben | Microsoft Docs
description: SAP HANA a HSR és a pacemakert a SLES-ben.
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
ms.date: 04/12/2021
ms.author: radeltch
ms.openlocfilehash: 637616c3698cc9ec0cd13a4584bad24b6ed02c34
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315431"
---
# <a name="high-availability-for-sap-hana-scale-out-system-with-hsr-on-suse-linux-enterprise-server"></a>Magas rendelkezésre állás SAP HANA kibővíthető rendszer számára a HSR-on SUSE Linux Enterprise Server 

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


Ez a cikk azt ismerteti, hogyan helyezhető üzembe egy magasan elérhető SAP HANA rendszer a HANA System Replication (HSR) és a pacemaker használatával az Azure SUSE Linux Enterprise Server Virtual Machines (VM) szolgáltatásban. A bemutatott architektúrában található megosztott fájlrendszereket [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) biztosítjuk, és az NFS-en keresztül vannak csatlakoztatva.  

A példában a konfigurációk, a telepítési parancsok és így tovább, a HANA-példány **03** , a HANA rendszer azonosítója pedig **HN1**. A példák a HANA 2,0 SP4 és a SUSE Linux Enterprise Server 12 SP5 alapulnak. 

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
* [SUSE magas rendelkezésre állású bővítmény 12 SP5 kibocsátási megjegyzései](https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP5/)
* [A sikertelen NFS-megosztások kezelési művelete SUSE HA-fürtben a HANA rendszer-replikáláshoz](https://www.suse.com/support/kb/doc/?id=000019904)
* [NetApp SAP-alkalmazások Microsoft Azure a Azure NetApp Files használatával][anf-sap-applications-azure]
* [NFS 4.1-es verziójú kötetek az SAP HANA-hoz készült Azure NetApp Filesban](./hana-vm-operations-netapp.md)

## <a name="overview"></a>Áttekintés

Az egyik módszer a Hana-kibővíthető telepítések HANA magas rendelkezésre állásának elérésére, a HANA rendszerreplikáció konfigurálása és a megoldás és a pacemaker-fürt elleni védelem az automatikus feladatátvétel engedélyezéséhez. Ha egy aktív csomópont meghibásodik, a fürt átadja a HANA-erőforrásokat a másik helyre.  
A bemutatott konfiguráció három HANA-csomópontot jelenít meg az egyes helyeken, valamint a többségi készítő csomópontot, hogy megakadályozza a megosztott agyi forgatókönyvet. Az utasítások úgy módosíthatók, hogy több virtuális gépet is felvegyen HANA DB-csomópontként.  

A `/hana/shared` bemutatott architektúrában a HANA megosztott fájlrendszert [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md)biztosítjuk. A NFSv 4.1-es porton keresztül csatlakozik a Hana-rendszer replikációs helyének minden HANA-csomópontján. A fájlrendszerek `/hana/data` és a `/hana/log` helyi fájlrendszerek, és nincsenek megosztva a HANA db-csomópontok között. A SAP HANA nem megosztott módban lesznek telepítve. 

> [!TIP]
> Az ajánlott SAP HANA tárolási konfigurációkhoz lásd: [SAP HANA Azure virtuális gépek tárolási konfigurációi](./hana-vm-operations-storage.md).   

[![SAP HANA a HSR és a pacemaker-fürttel a SLES](./media/sap-hana-high-availability/sap-hana-high-availability-scale-out-hsr-suse.png)](./media/sap-hana-high-availability/sap-hana-high-availability-scale-out-hsr-suse-detail.png#lightbox)

Az előző ábrán három alhálózat található egy Azure-beli virtuális hálózaton belül, a SAP HANA hálózati javaslatok követésével: 
* ügyfél-kommunikációhoz – `client` 10.23.0.0/24  
* a belső HANA-csomópontok közötti kommunikációhoz – `inter` 10.23.1.128/26  
* HANA rendszerbeli replikáláshoz – `hsr` 10.23.1.192/26  

A `/hana/data` és a `/hana/log` helyi lemezeken való üzembe helyezése esetén nem szükséges külön alhálózatot és különálló virtuális hálózati kártyákat telepíteni a tárolóhoz való kommunikációhoz.  

Az Azure NetApp-kötetek külön alhálózatban vannak telepítve, [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet): `anf` 10.23.1.0/26 delegált.   

> [!IMPORTANT]
> A rendszer nem támogatja a rendszerreplikációt harmadik helyre. További részletekért lásd a "fontos előfeltételek" című szakaszt a [SLES-SAP HANA rendszer-replikációs felskálázás teljesítményre optimalizált forgatókönyvben](https://documentation.suse.com/sbp/all/html/SLES4SAP-hana-scaleOut-PerfOpt-12/index.html#_important_prerequisites).     

## <a name="set-up-the-infrastructure"></a>Az infrastruktúra beállítása

Az alábbi utasításokban feltételezzük, hogy már létrehozta az erőforráscsoportot, az Azure-beli virtuális hálózatot három Azure-beli hálózati alhálózattal: `client` `inter` és `hsr` .

### <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Linux rendszerű virtuális gépek üzembe helyezése a Azure Portal használatával
1. Telepítse az Azure-beli virtuális gépeket.  
A jelen dokumentumban bemutatott konfiguráció esetében hét virtuális gépet helyezzen üzembe: 
   - három virtuális gép, amely HANA DB-csomópontként szolgál a HANA-alapú replikálási helyhez 1: **HANA-S1-db1**, **HANA-S1-DB2** és **HANA-S1-db3**  
   - három virtuális gép, amely HANA DB-csomópontként szolgál a HANA-alapú replikálási helyhez 2: **Hana-S2-db1**, **HANA-S2-DB2** és **HANA-S2-db3**  
   - egy kisméretű virtuális gép, amely *többségi gyártóként* szolgál: **Hana-s-mm**

   Az SAP DB HANA-csomópontként üzembe helyezett virtuális gépeket az SAP for HANA minősítéssel kell ellátni a [SAP HANA hardveres könyvtárban](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)közzétettek szerint. A HANA DB-csomópontok telepítésekor ellenőrizze, hogy a [gyorsított hálózat](../../../virtual-network/create-vm-accelerated-networking-cli.md) van-e kiválasztva.  
  
   A többségi gyártó csomópontja esetében telepíthet egy kisméretű virtuális gépet, mivel ez a virtuális gép nem futtatja a SAP HANA erőforrásait. A többségi gyártó virtuális gépet a fürt konfigurációjában használják páratlan számú fürtcsomópont eléréséhez egy megosztott agyi forgatókönyvben. A többségi gyártó virtuális gépnek ebben a példában csak egy virtuális hálózati adapterre van szüksége az `client` alhálózatban.        

   Helyi felügyelt lemezek telepítése a és rendszerhez `/hana/data` `/hana/log` . A és a minimálisan ajánlott tárolási konfigurációját `/hana/data` `/hana/log` [SAP HANA Azure-beli virtuális gépek tárolási konfigurációi](./hana-vm-operations-storage.md)írják le.

   Telepítse az elsődleges hálózati adaptert a `client` virtuális hálózati alhálózatban található minden virtuális géphez.  
   Ha a virtuális gépet Azure Portalon keresztül telepítik, a hálózati adapter neve automatikusan létrejön. Az egyszerűség kedvéért megtekintjük az automatikusan generált, elsődleges hálózati adaptereket, amelyek az `client` Azure virtuális hálózat alhálózatához vannak csatolva **Hana-S1-db1-Client**, **Hana-S1-DB2-Client**, **Hana-S1-db3-Client**, és így tovább.  


   > [!IMPORTANT]
   > Győződjön meg arról, hogy a kiválasztott operációs rendszer SAP-tanúsítvánnyal rendelkezik az adott virtuálisgép-típusok SAP HANAához. A SAP HANA Certified VM-típusok és operációsrendszer-kiadások listáját az adott típusokhoz a [SAP HANA Certified IaaS Platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) webhelyen teheti meg. Kattintson a felsorolt virtuálisgép-típus részleteire az adott típushoz tartozó SAP HANA által támogatott operációsrendszer-kiadások teljes listájának lekéréséhez.  
  

2. Hozzon létre hat hálózati adaptert, egyet mindegyik HANA DB virtuális géphez, a `inter` virtuális hálózati alhálózatban (ebben a példában a **HANA-S1-db1-Inter**, **Hana-S1-DB2-Inter**, Hana- **S1-db3-Inter**, **Hana-S2-db1-Inter**, **Hana-S2-DB2-Inter** és **Hana-S2-db3-Inter**).  

3. Hozzon létre hat hálózati adaptert, egyet mindegyik HANA DB virtuális géphez, a `hsr` virtuális hálózati alhálózatban (ebben a példában a **HANA-S1-db1-HSR**, **Hana-S1-DB2-HSR**, **Hana-S1-db3-HSR**, **Hana-S2-** db1-HSR, **Hana-S2-DB2-HSR** és **Hana-S2-db3-HSR**).  

4. Csatlakoztassa az újonnan létrehozott virtuális hálózati adaptereket a megfelelő virtuális gépekhez:  

    a. Nyissa meg a [Azure Portal](https://portal.azure.com/#home)a virtuális gépet.  

    b. A bal oldali ablaktáblán válassza a **Virtual Machines** lehetőséget. Szűrje a virtuális gép nevét (például **Hana-S1-db1**), majd válassza ki a virtuális gépet.  

    c. Az **Áttekintés** ablaktáblán válassza a **Leállítás** elemet a virtuális gép felszabadításához.  

    d. Válassza a **hálózatkezelés** lehetőséget, majd csatlakoztassa a hálózati adaptert. A **hálózati adapter csatolása** legördülő listában válassza ki a már létrehozott hálózati adaptereket és az `inter` `hsr` alhálózatokat.  
    
    e. Kattintson a **Mentés** gombra. 
 
    f. Ismételje meg a b – e lépéseket a fennmaradó virtuális gépek esetében (példánkban a  **Hana-S1-DB2**, **Hana-S1-db3**, **Hana-S2-db1**, **Hana-S2-DB2** és **Hana-S2-db3**).
 
    : A virtuális gépeket most leállított állapotban hagyja. Ezután engedélyezzük a [gyorsított hálózatkezelést](../../../virtual-network/create-vm-accelerated-networking-cli.md) az újonnan csatlakoztatott hálózati adapterek esetében.  

5. A következő lépések végrehajtásával engedélyezheti a gyorsított hálózatkezelést az `inter` és az alhálózatok további hálózati adapterei számára `hsr` :  

    a. Nyissa meg [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) a [Azure Portalban](https://portal.azure.com/#home).  

    b. Hajtsa végre a következő parancsokat a gyorsított hálózatkezelés engedélyezéséhez a további hálózati adapterekhez, amelyek a `inter` és az `hsr` alhálózatokhoz vannak csatolva.  

    ```azurecli
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-inter --accelerated-networking true
    
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-hsr --accelerated-networking true
    ```

7. A HANA DB virtuális gépek elindítása

### <a name="deploy-azure-load-balancer"></a>Azure Load Balancer üzembe helyezése

1. A standard Load Balancer használatát javasoljuk. A standard Load Balancer üzembe helyezéséhez kövesse az alábbi konfigurációs lépéseket:
   1. Először hozzon létre egy előtér-IP-címkészletet:

      1. Nyissa meg a terheléselosztó felületet, válassza a előtér **IP-készlet** lehetőséget, majd kattintson a **Hozzáadás** gombra.
      1. Adja meg az új előtér-IP-készlet nevét (például **Hana-frontend**).
      1. Állítsa a **hozzárendelést** **statikus** értékre, és adja meg az IP-címet (például **10.23.0.27**).
      1. Válassza az **OK** lehetőséget.
      1. Az új előtér-IP-készlet létrehozása után jegyezze fel a készlet IP-címét.

   1. Ezután hozzon létre egy háttér-készletet, és vegye fel az összes fürtbeli virtuális gépet a háttér-készletbe:

      1. Nyissa meg a Load balancert, válassza a **háttérbeli készletek** lehetőséget, majd válassza a **Hozzáadás** lehetőséget.
      1. Adja meg az új háttér-készlet nevét (például **Hana-backend**).
      1. Válassza **a virtuális gép hozzáadása** lehetőséget.
      1. Válassza a **Virtuális gép** lehetőséget.
      1. Válassza ki a SAP HANA-fürt virtuális gépei és az alhálózat IP-címeit `client` .
      1. Válassza a **Hozzáadás** lehetőséget.

   1. Következő lépésként hozzon létre egy állapot-mintavételt:

      1. Nyissa meg a terheléselosztó-t, válassza az **állapot**-tesztek elemet, majd kattintson a **Hozzáadás** gombra.
      1. Adja meg az új állapot-mintavétel nevét (például **Hana-HP**).
      1. Válassza a **TCP** lehetőséget a protokoll és a **625-** es port. Tartsa meg az **intervallum** értékét 5-re, a nem kifogástalan **állapot küszöbértékének** értéke pedig 2.
      1. Válassza az **OK** lehetőséget.

   1. Ezután hozza létre a terheléselosztási szabályokat:
   
      1. Nyissa meg a terheléselosztó-t, válassza a terheléselosztási **szabályok** lehetőséget, majd válassza a **Hozzáadás** lehetőséget.
      1. Adja meg az új terheléselosztó-szabály nevét (például **Hana-LB**).
      1. Válassza ki az előtér-IP-címet, a háttér-készletet és a korábban létrehozott állapot-mintavételt (például **Hana-frontend**, **Hana-backend** és **Hana-HP**).
      1. Válassza a **hektár portok** lehetőséget.
      1. Ügyeljen arra, hogy a **lebegő IP-címet engedélyezze**.
      1. Válassza az **OK** lehetőséget.

   > [!IMPORTANT]
   > A lebegő IP-címek nem támogatottak a terheléselosztási helyzetekben a hálózati adapter másodlagos IP-konfigurációjában. További részletek: az [Azure Load Balancer korlátozásai](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Ha a virtuális gép további IP-címére van szüksége, helyezzen üzembe egy második hálózati adaptert.    
   
   > [!Note]
   > Ha a nyilvános IP-címek nélküli virtuális gépek a belső (nincs nyilvános IP-cím) standard Azure Load Balancer háttér-készletbe kerülnek, nem lesz kimenő internetkapcsolat, kivéve, ha további konfigurálást végeznek a nyilvános végpontok útválasztásának engedélyezéséhez. A kimenő kapcsolatok elérésével kapcsolatos részletekért lásd: [nyilvános végpontú kapcsolat Virtual Machines az Azure standard Load Balancer használata az SAP magas rendelkezésre állási helyzetekben](./high-availability-guide-standard-load-balancer-outbound-connections.md).  


   > [!IMPORTANT]
   > Ne engedélyezze a TCP-időbélyegeket a Azure Load Balancer mögött elhelyezett Azure-beli virtuális gépeken. A TCP-időbélyegek engedélyezése az állapot-mintavételek meghibásodását eredményezi. Állítsa a paramétert a **0** értékre **net.IPv4.tcp_timestamps** . Részletekért lásd: [Load Balancer Health](../../../load-balancer/load-balancer-custom-probe-overview.md)-tesztek.
   > Lásd még: SAP Note [2382421](https://launchpad.support.sap.com/#/notes/2382421).  

### <a name="deploy-the-azure-netapp-files-infrastructure"></a>A Azure NetApp Files infrastruktúra üzembe helyezése 

Telepítse a fájlrendszer ANF-köteteit `/hana/shared` . `/hana/shared`Minden HANA rendszerbeli replikációs helyhez külön kötetre lesz szüksége. További információ: [a Azure NetApp Files-infrastruktúra beállítása](./sap-hana-scale-out-standby-netapp-files-suse.md#set-up-the-azure-netapp-files-infrastructure).

Ebben a példában a rendszer a következő Azure NetApp Files köteteket használta: 

* kötet **HN1**-Shared-s1 (NFS://10.23.1.7/**HN1**-Shared-S1)
* mennyiségi **HN1**– közös-s2 (NFS://10.23.1.7/**HN1**-Shared-S2)


## <a name="operating-system-configuration-and-preparation"></a>Operációs rendszer konfigurációja és előkészítése

A következő részekben található utasítások a következő rövidítések egyikével vannak ellátva:
* **[A]**: az összes csomópontra érvényes
* **[Ah]**: minden HANA db-csomópontra érvényes
* **[M]**: a többségi készítő csomópontra vonatkozik
* **[AH1]**: minden HANA db-csomópontra érvényes az 1. helyen
* **[AH2]**: minden HANA db-csomópontra érvényes a 2. helyen
* **[1]**: csak az 1. HANA db-csomópontra vonatkozik, 1. hely
* **[2]**: csak az 1. HANA db-csomópontra vonatkozik, 2. hely

Konfigurálja és készítse elő az operációs rendszert a következő lépések végrehajtásával:

1. **[A]** a gazdagép fájljainak karbantartása a virtuális gépeken. Bejegyzések belefoglalása az összes alhálózathoz. Ehhez a példához a következő bejegyzések lettek hozzáadva `/etc/hosts` .  

    ```bash
     # Client subnet
     10.23.0.19      hana-s1-db1
     10.23.0.20      hana-s1-db2
     10.23.0.21      hana-s1-db3
     10.23.0.22      hana-s2-db1
     10.23.0.23      hana-s2-db2
     10.23.0.24      hana-s2-db3
     10.23.0.25      hana-s-mm    
     # Internode subnet
     10.23.1.132     hana-s1-db1-inter
     10.23.1.133     hana-s1-db2-inter
     10.23.1.134     hana-s1-db3-inter
     10.23.1.135     hana-s2-db1-inter
     10.23.1.136     hana-s2-db2-inter
     10.23.1.137     hana-s2-db3-inter
     # HSR subnet
     10.23.1.196     hana-s1-db1-hsr
     10.23.1.197     hana-s1-db2-hsr
     10.23.1.198     hana-s1-db3-hsr
     10.23.1.199     hana-s2-db1-hsr
     10.23.1.200     hana-s2-db2-hsr
     10.23.1.201     hana-s2-db3-hsr
    ```

2. **[A]** a SUSE speciális erőforrás-ügynököket biztosít a SAP HANAokhoz és a SAP HANA ScaleUp tartozó alapértelmezett ügynökökhöz. Távolítsa el a ScaleUp tartozó csomagokat, ha telepítve van, és telepítse a forgatókönyvek SAP HANAScaleOut. A lépést az összes fürtözött virtuális gépen el kell elvégezni, beleértve a többségi gyártót is.   

    ```bash
     # Uninstall ScaleUp packages and patterns
     zypper remove patterns-sap-hana
     zypper remove SAPHanaSR 
     zypper remove SAPHanaSR-doc
     zypper remove yast2-sap-ha
     # Install the ScaleOut packages and patterns
     zypper in SAPHanaSR-ScaleOut  SAPHanaSR-ScaleOut-doc 
     zypper in -t pattern ha_sles
    ```

3. **[Ah]** A virtuális gépek előkészítése – a [2205917] SAP-megjegyzésekben javasolt beállítások alkalmazása SAP-alkalmazásokhoz SUSE Linux Enterprise Server.  

## <a name="prepare-the-file-systems"></a>A fájlrendszerek előkészítése
### <a name="mount-the-shared-file-systems"></a>A megosztott fájlrendszerek csatlakoztatása

Ebben a példában a közös HANA-fájlrendszerek üzembe helyezése Azure NetApp Files történik, és a Nfsv4 névleképezője-en keresztül vannak csatlakoztatva.  

1. **[Ah]** Hozzon létre csatlakoztatási pontokat a HANA-adatbázis kötetei számára.  

    ```bash
    mkdir -p /hana/shared
    ```

2. **[Ah]** Ellenőrizze az NFS-tartomány beállítását. Győződjön meg arról, hogy a tartomány alapértelmezett Azure NetApp Files-tartományként van konfigurálva, **`defaultv4iddomain.com`** és a leképezés értéke **senki**.  
   Erre a lépésre csak akkor van szükség, ha az Azure NetAppFiles NFSv 4.1-et használja.  

    > [!IMPORTANT]
    > Ügyeljen arra, hogy az NFS-tartományt a virtuális gépen állítsa be, `/etc/idmapd.conf` hogy az megfeleljen az alapértelmezett tartományi konfigurációnak Azure NetApp Files: **`defaultv4iddomain.com`** . Ha az NFS-ügyfél (azaz a virtuális gép) és az NFS-kiszolgáló (például az Azure NetApp-konfiguráció) közötti eltérés nem egyezik, akkor a virtuális gépekre csatlakoztatott Azure NetApp-köteteken található fájlok engedélyei a következőképpen jelennek meg `nobody` .  

    ```bash
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

3. **[Ah]** Ellenőrizze `nfs4_disable_idmapping` . Értékeként az **Y** értéknek kell lennie. A-t tartalmazó könyvtár-struktúra létrehozásához `nfs4_disable_idmapping` hajtsa végre a csatlakoztatási parancsot. Nem lehet manuálisan létrehozni a könyvtárat a/sys/modules alatt, mivel a hozzáférés a kernel/illesztőprogramok számára van fenntartva.  
   Erre a lépésre csak akkor van szükség, ha az Azure NetAppFiles NFSv 4.1-et használja.  

    ```bash
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.23.1.7:/HN1-share-s1 /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

4. **[AH1]** Csatlakoztassa a megosztott Azure NetApp Files köteteket a HELY1 HANA DB virtuális gépekhez.  

    ```bash
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.7:/HN1-shared-s1 /hana/shared nfs rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    ```

5. **[AH2]** Csatlakoztassa a megosztott Azure NetApp Files köteteket a SITE2 HANA DB virtuális gépekhez.  

    ```bash
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.7:/HN1-shared-s2 /hana/shared nfs rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    ```


10. **[Ah]** Ellenőrizze, hogy a megfelelő `/hana/shared/` fájlrendszerek csatlakoztatva vannak-e az NFS protokoll-verzió **NFSV4 NÉVLEKÉPEZŐJE** rendelkező HANA db-alapú virtuális gépekhez.  

    ```bash
    sudo nfsstat -m
    # Verify that flag vers is set to 4.1 
    # Example from SITE 1, hana-s1-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s1
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.11,local_lock=none,addr=10.23.1.7
    # Example from SITE 2, hana-s2-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s2
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.14,local_lock=none,addr=10.23.1.7
    ```

### <a name="prepare-the-data-and-log-local-file-systems"></a>Az adatfeldolgozás és a helyi fájlrendszerek előkészítése
A bemutatott konfigurációban a fájlrendszerek `/hana/data` és a `/hana/log` felügyelt lemezre vannak telepítve, és helyileg vannak csatlakoztatva az egyes HANA db-alapú virtuális gépekhez. Végre kell hajtania a lépéseket a helyi és a naplózási kötetek létrehozásához az egyes HANA DB-beli virtuális gépeken. 

Állítsa be a lemez elrendezését a  **logikai kötet-kezelővel (LVM)**. Az alábbi példa azt feltételezi, hogy minden HANA virtuális géphez három adatlemez van csatlakoztatva, amelyek két kötet létrehozásához használatosak.

1. **[Ah]** Az összes rendelkezésre álló lemez felsorolása:
    ```bash
    ls /dev/disk/azure/scsi1/lun*
    ```

   Példa a kimenetre:

    ```bash
    /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2 
    ```

2. **[Ah]** Fizikai kötetek létrehozása a használni kívánt összes lemezhez:
    ```bash
    sudo pvcreate /dev/disk/azure/scsi1/lun0
    sudo pvcreate /dev/disk/azure/scsi1/lun1
    sudo pvcreate /dev/disk/azure/scsi1/lun2
    ```

3. **[Ah]** Hozzon létre egy kötet-csoportot az adatfájlokhoz. Használjon egy kötetet a naplófájlok számára, és egyet a SAP HANA megosztott könyvtárához:
    ```bash
    sudo vgcreate vg_hana_data_HN1 /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
    sudo vgcreate vg_hana_log_HN1 /dev/disk/azure/scsi1/lun2
    ```

4. **[Ah]** Hozza létre a logikai köteteket. 
   A kapcsoló használata nélkül jön létre lineáris kötet `lvcreate` `-i` . Javasoljuk, hogy hozzon létre egy csíkozott kötetet a jobb I/O-teljesítmény érdekében, és igazítsa a sávok méretét a SAP HANA virtuálisgép- [tároló konfigurációjában](./hana-vm-operations-storage.md)dokumentált értékekhez. Az `-i` argumentumnak a mögöttes fizikai kötetek számának kell lennie, az `-I` argumentum pedig a sáv mérete. Ebben a dokumentumban két fizikai kötet van használatban az adatkötethez, így a `-i` switch argumentum értéke **2**. Az adatmennyiség szalagos mérete **256 KiB**. A rendszer egy fizikai kötetet használ a naplózási kötethez, így `-i` `-I` a naplózási kötet parancsaihoz nem használhatók explicit módon a kapcsolók.  

   > [!IMPORTANT]
   > Használja a `-i` kapcsolót, és állítsa be a mögöttes fizikai kötet számára, ha több fizikai kötetet használ minden egyes adathoz vagy naplózási kötethez. `-I`Csíkozott kötet létrehozásakor használja a kapcsolót a sáv méretének megadásához.  
   > Lásd: SAP HANA virtuálisgép- [tárolási konfigurációk](./hana-vm-operations-storage.md) az ajánlott tárolási konfigurációkhoz, beleértve a sávok méretét és a lemezek számát.  

    ```bash
    sudo lvcreate -i 2 -I 256 -l 100%FREE -n hana_data vg_hana_data_HN1
    sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_HN1
    sudo mkfs.xfs /dev/vg_hana_data_HN1/hana_data
    sudo mkfs.xfs /dev/vg_hana_log_HN1/hana_log
    ```

5. **[Ah]** Hozza létre a csatlakoztatási könyvtárakat, és másolja a logikai kötetek UUID azonosítóját:
    ```bash
    sudo mkdir -p /hana/data/HN1
    sudo mkdir -p /hana/log/HN1
    # Write down the ID of /dev/vg_hana_data_HN1/hana_data and /dev/vg_hana_log_HN1/hana_log
    sudo blkid
    ```

6. **[Ah]** Hozzon létre `fstab` bejegyzéseket a logikai kötetekhez és a csatlakoztatáshoz:
    ```bash
    sudo vi /etc/fstab
    ```

   Szúrja be a következő sort a `/etc/fstab` fájlba:

    ```bash
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_data_HN1-hana_data /hana/data/HN1 xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_log_HN1-hana_log /hana/log/HN1 xfs  defaults,nofail  0  2
    ```

   Az új kötetek csatlakoztatása:

    ```bash
    sudo mount -a
    ```

## <a name="create-a-pacemaker-cluster"></a>Pacemaker-fürt létrehozása

Ha alapszintű pacemaker-fürtöt szeretne létrehozni ehhez a HANA-kiszolgálóhoz, kövesse az Azure-beli [SUSE Linux Enterprise Server pacemaker beállítása](high-availability-guide-suse-pacemaker.md) című témakör lépéseit.
Az összes virtuális gép belefoglalása, beleértve a fürt többségi gyártóját is.  

> [!IMPORTANT]
> Ne állítsa `quorum expected-votes` 2 értékre, mert ez nem egy két csomópontos fürt.  
> Győződjön meg arról, hogy a cluster tulajdonság `concurrent-fencing`  engedélyezve van, hogy a csomópont-kerítés deszerializálva legyen.   

## <a name="installation"></a>Telepítés  

Ebben a példában az Azure-beli virtuális gépeken található HSR-alapú kibővített konfigurációban SAP HANA üzembe helyezéséhez a HANA 2,0 SP4-et használtuk.  

### <a name="prepare-for-hana-installation"></a>A HANA telepítésének előkészítése

1. **[Ah]** A HANA telepítésének megkezdése előtt állítsa be a legfelső szintű jelszót. A legfelső szintű jelszót a telepítés befejezése után tilthatja le. Végrehajtás `root` parancsként `passwd` .  

2. **[1, 2]** az engedélyek módosítása bekapcsolva `/hana/shared` 
    ```bash
    chmod 775 /hana/shared
    ```

3. **[1]** ellenőrizze, hogy SSH-n keresztül tud-e bejelentkezni a Hana db virtuális gépekre a következő helyen: **Hana-S1-DB2** és **HANA-S1-db3**, anélkül, hogy jelszót kellene megadnia. Ha ez nem igaz, az Exchange SSH-kulcsokat az [SSH-hozzáférés engedélyezése nyilvános kulcson keresztül](https://documentation.suse.com/sbp/all/html/SLES4SAP-hana-scaleOut-PerfOpt-12/index.html#_enable_ssh_access_via_public_key_optional)című témakörben leírtak szerint.  
    ```bash
    ssh root@hana-s1-db2
    ssh root@hana-s1-db3
    ```

4. **[2]** ellenőrizze, hogy SSH-n keresztül tud-e bejelentkezni a Hana db virtuális gépekre a következő helyen: **Hana-S2-DB2** és **HANA-S2-db3**, anélkül, hogy jelszót kellene megadnia.  
   Ha ez nem igaz, az Exchange SSH-kulcsokat.    
    ```bash
    ssh root@hana-s2-db2
    ssh root@hana-s2-db3
    ```

5. **[Ah]** Telepítsen további csomagokat, amelyek szükségesek a HANA 2,0 SP4-hez. További információ: SAP Note [2593824](https://launchpad.support.sap.com/#/notes/2593824) a SLES-verzióhoz. 

    ```bash
    # In this example, using SLES12 SP5
    sudo zypper install libgcc_s1 libstdc++6 libatomic1
    ```
### <a name="hana-installation-on-the-first-node-on-each-site"></a>HANA-telepítés az egyes helyek első csomópontján

1. **[1]** telepítse a SAP HANA az [SAP HANA 2,0 telepítési és frissítési útmutató](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html)utasításait követve. Az alábbi utasításokban bemutatjuk a SAP HANA telepítését az 1. hely első csomópontján.   

   a. Indítsa el a **hdblcm** programot `root` a HANA telepítési szoftver könyvtára alapján. Használja a `internal_network` paramétert, és adja át a címtartomány az alhálózat számára, amely a belső HANA-csomópontok közötti kommunikációhoz használatos.  

    ```bash
    ./hdblcm --internal_network=10.23.1.128/26
    ```

   b. A parancssorba írja be a következő értékeket:

     * **Válasszon műveletet**: **1** . lépés (telepítéshez)
     * **További összetevők a telepítéshez**: írja be a **2, 3** értéket
     * Telepítési útvonal: nyomja le az ENTER billentyűt (az alapértelmezett érték a/Hana/Shared)
     * **Helyi állomásnév** esetén: nyomja le az ENTER billentyűt az alapértelmezett érték elfogadásához.
     * A szeretné **hozzáadni a gazdagépeket a rendszerhez?**: ENTER **n**
     * **SAP HANA rendszer-azonosító**: írja be a **HN1**
     * **Példány száma** [00]: **03**
     * **Helyi gazdagép munkavégző csoportjának** [alapértelmezett]: az ENTER billentyű lenyomásával fogadja el az alapértelmezett értéket.
     * **Válassza a rendszerhasználat/ENTER index [4] lehetőséget**: írja be a **4** értéket (egyéni)
     * Az **adatkötetek helyének** eléréséhez [/Hana/Data/HN1]: nyomja le az ENTER billentyűt az alapértelmezett érték elfogadásához.
     * A **log kötetek helye** [/Hana/log/HN1]: az ENTER billentyű lenyomásával fogadja el az alapértelmezett értéket.
     * A **maximális memória kiosztásának korlátozása?** [n]: ENTER **n**
     * A **tanúsítvány állomásneve a következő gazdagép számára: Hana-S1-db1** [Hana-S1-db1]: az ENTER billentyű lenyomásával fogadja el az alapértelmezett értéket.
     * Az **SAP Host Agent User (sapadm) jelszava**: írja be a jelszót.
     * Az **SAP Host Agent User (sapadm) jelszó megerősítése**: írja be a jelszót.
     * A rendszergazda **(hn1adm) jelszava**: írja be a jelszót.
     * A **rendszergazdai kezdőkönyvtár** [/usr/SAP/HN1/Home] esetében: nyomja le az ENTER billentyűt az alapértelmezett érték elfogadásához
     * A **rendszergazda bejelentkezési rendszerhéj** [/bin/sh] esetében nyomja le az ENTER billentyűt az alapértelmezett érték elfogadásához.
     * A rendszergazda **felhasználói azonosító** [1001]: nyomja meg az ENTER billentyűt az alapértelmezett érték elfogadásához
     * A **felhasználói csoport (sapsys) [79] azonosítójának megadásához** nyomja le az ENTER billentyűt az alapértelmezett érték elfogadásához.
     * **Rendszeradatbázis-felhasználó (rendszer) jelszava**: adja meg a rendszer jelszavát.
     * **Rendszeradatbázis-felhasználó (rendszer) jelszavának megerősítése**: írja be a rendszer jelszavát.
     * **Újraindítási rendszer a gép újraindítása után?** [n]: ENTER **n** 
     * A **folytatja a folytatást (i/n)**: érvényesítse az összegzést, és ha minden jól látható, írja be az **y** értéket.

2. **[2]** ismételje meg az előző lépést a SAP HANA telepítéséhez a 2. hely első csomópontján.   

3. **[1, 2]** ellenőrzés global.ini  

   Jelenítse meg global.ini, és győződjön meg arról, hogy a belső SAP HANA csomópontok közötti kommunikáció konfigurációja van érvényben. Ellenőrizze a **kommunikáció** szakaszát. Meg kell adni a címtartományt az `inter` alhálózathoz, és a következőre kell `listeninterface` állítani: `.internal` . Ellenőrizze a **internal_hostname_resolution** szakaszt. Az alhálózathoz tartozó HANA virtuális gépek IP-címeinek kell lenniük `inter` .  

   ```bash
     sudo cat /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
     # Example from SITE1 
     [communication]
     internal_network = 10.23.1.128/26
     listeninterface = .internal
     [internal_hostname_resolution]
     10.23.1.132 = hana-s1-db1
     10.23.1.133 = hana-s1-db2
     10.23.1.134 = hana-s1-db3
   ```

4. **[1, 2]** felkészülés `global.ini` a nem megosztott környezetekben történő telepítésre az SAP Note [2080991](https://launchpad.support.sap.com/#/notes/0002080991)-ben leírtak szerint.  

   ```bash
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    [persistence]
    basepath_shared = no
   ```

4. **[1, 2]** indítsa újra SAP HANA a módosítások aktiválásához.  

   ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem
   ```

6. **[1, 2]** ellenőrizze, hogy az ügyfél felülete az alhálózat IP-címeit fogja-e használni a `client` kommunikációhoz.  

    ```bash
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "password" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result - example from SITE 2
    "hana-s2-db1","net_publicname","10.23.0.22"
   ```

   További információ a konfiguráció ellenőrzéséről: SAP Note [2183363 – SAP HANA belső hálózat konfigurálása](https://launchpad.support.sap.com/#/notes/2183363).  

7. **[Ah]** A HANA telepítési hibájának elkerülése érdekében módosítsa az adatkezelési és a naplózási könyvtárak engedélyeit.  

   ```bash
    sudo chmod o+w -R /hana/data /hana/log
   ```

8. **[1]** telepítse a másodlagos HANA-csomópontokat. Az ebben a lépésben szereplő példa az 1. helyen található.  
   a. Indítsa el a rezidens **hdblcm** programot `root` .    
    ```bash
     cd /hana/shared/HN1/hdblcm
     ./hdblcm 
    ```

   b. A parancssorba írja be a következő értékeket:

     * **Válasszon műveletet**: **2** . lépés (gazdagépek hozzáadása)
     * **Adja meg a vesszővel tagolt állomásnevek hozzáadásához használandó nevet**: Hana-S1-DB2, Hana-S1-db3
     * **További összetevők a telepítéshez**: írja be a **2, 3** értéket
     * Az **adja meg a gyökér felhasználóneve [root]**: az ENTER billentyű lenyomásával fogadja el az alapértelmezett értéket.
     * A **"Hana-S1-DB2" [1]: 1. gazdagép szerepköreinek kiválasztása**
     * A **gazdagép feladatátvételi csoportjának megadása a Hana-S1-DB2 számára [alapértelmezett]**: nyomja le az ENTER billentyűt az alapértelmezett érték elfogadásához
     * A **tárolási partíció számának megadása a következő gazdagép számára: Hana-S1-DB2 [<<assign automatically>>]**: az ENTER billentyű lenyomásával fogadja el az alapértelmezett értéket.
     * A (z) **"Hana-S1-DB2" gazdagéphez tartozó munkavégző csoport beírásához [alapértelmezett]**: nyomja le az ENTER billentyűt az alapértelmezett
     * A **"Hana-S1-db3" [1]: 1. gazdagéphez tartozó szerepkörök kiválasztása**
     * A **gazdagép feladatátvételi csoportjának megadása a "Hana-S1-db3" gazdagéphez [alapértelmezett]**: nyomja le az ENTER billentyűt az alapértelmezett érték elfogadásához
     * A **tárolási partíció számának megadása a következő gazdagép számára: Hana-S1-db3 ' [<<assign automatically>>]**: az ENTER billentyű lenyomásával fogadja el az alapértelmezett értéket.
     * A **"Hana-S1-db3" gazdagéphez tartozó Working Group (alapértelmezett) érték** esetén: nyomja le az ENTER billentyűt az alapértelmezett érték elfogadásához.
     * A rendszergazda **(hn1adm) jelszava**: írja be a jelszót.
     * Az **adja meg az SAP Host Agent User (sapadm) jelszót**: írja be a jelszót.
     * Az **SAP Host Agent User (sapadm) jelszó megerősítése**: írja be a jelszót.
     * A **tanúsítvány állomásneve a következő gazdagép számára: Hana-S1-DB2** [Hana-S1-DB2]: az ENTER billentyű lenyomásával fogadja el az alapértelmezett értéket.
     * A **tanúsítvány állomásneve a következő gazdagép számára: Hana-S1-db3** [Hana-S1-db3]: az ENTER billentyű lenyomásával fogadja el az alapértelmezett értéket.
     * A **folytatja a folytatást (i/n)**: érvényesítse az összegzést, és ha minden jól látható, írja be az **y** értéket.

9. **[2]** ismételje meg az előző lépést a másodlagos SAP HANA-csomópontok telepítéséhez a 2. helyen.   

## <a name="configure-sap-hana-20-system-replication"></a>SAP HANA 2,0 rendszerreplikáció konfigurálása

1. **[1]** a rendszerreplikáció konfigurálása az 1. helyen:

   Az adatbázisok biztonsági mentése **hn1** adm-ként:

    ```bash
    hdbsql -d SYSTEMDB -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupSYS')"
    hdbsql -d HN1 -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupHN1')"
    ```

   Másolja a System PKI-fájlokat a másodlagos helyre:

    ```bash
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/data/SSFS_HN1.DAT hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/data/
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/key/SSFS_HN1.KEY  hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/key/
    ```

   Hozza létre az elsődleges helyet:

    ```bash
    hdbnsutil -sr_enable --name=HANA_S1
    ```

2. **[2]** rendszerreplikáció konfigurálása a 2. helyen:
    
   Regisztrálja a második helyet a rendszerreplikáció elindításához. Futtassa a következő parancsot <hanasid adm-ként \> :

    ```bash
    sapcontrol -nr 03 -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=hana-s1-db1 --remoteInstance=03 --replicationMode=sync --name=HANA_S2
    sapcontrol -nr 03 -function StartSystem
    ```

3. **[1]** a replikálás állapotának megtekintése

   Vizsgálja meg a replikálás állapotát, és várjon, amíg az összes adatbázis szinkronban van.

    ```bash
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    # | Database | Host          | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary     | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
    # |          |               |       |              |           |         |           | Host          | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
    # | -------- | ------------- | ----- | ------------ | --------- | ------- | --------- | ------------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
    # | HN1      | hana-s1-db3   | 30303 | indexserver  |         5 |       1 | HANA_S1   | hana-s2-db3   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | SYSTEMDB | hana-s1-db1   | 30301 | nameserver   |         1 |       1 | HANA_S1   | hana-s2-db1   |     30301 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30307 | xsengine     |         2 |       1 | HANA_S1   | hana-s2-db1   |     30307 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30303 | indexserver  |         3 |       1 | HANA_S1   | hana-s2-db1   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db2   | 30303 | indexserver  |         4 |       1 | HANA_S1   | hana-s2-db2   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    #
    # status system replication site "2": ACTIVE
    # overall system replication status: ACTIVE
    #
    # Local System Replication State
    #   
    # mode: PRIMARY
    # site id: 1
    # site name: HANA_S1
    ```

4. **[1, 2]** módosítsa a Hana-konfigurációt úgy, hogy a Hana rendszer replikálásával kapcsolatos kommunikációt, ha a Hana rendszerreplikálási virtuális hálózati adaptereket irányította.   
   - A HANA leállítása mindkét helyen
    ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem HDB
    ```

   - global.ini szerkesztése a HANA rendszerreplikációhoz tartozó gazdagép-hozzárendelés hozzáadásához: használja az alhálózat IP-címeit `hsr` .  
    ```bash
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [system_replication_hostname_resolution]
    10.23.1.196 = hana-s1-db1
    10.23.1.197 = hana-s1-db2
    10.23.1.198 = hana-s1-db3
    10.23.1.199 = hana-s2-db1
    10.23.1.200 = hana-s2-db2
    10.23.1.201 = hana-s2-db3
    ```

   - A HANA elindítása mindkét helyen
   ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem HDB
   ```

   További információ: [a Rendszerreplikáció gazdagép](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/1.0.12/en-US/c0cba1cb2ba34ec89f45b48b2157ec7b.html)-névfeloldása.  

## <a name="create-file-system-resources"></a>Fájlrendszer erőforrásainak létrehozása

Hozzon létre egy dummy fájlrendszerbeli fürterőforrás-erőforrást, amely figyeli és jelenti a hibákat, ha probléma merül fel az NFS-hez csatlakoztatott fájlrendszer elérésekor `/hana/shared` . Ez lehetővé teszi a fürt számára a feladatátvétel elindítását abban az esetben, ha probléma van a elérésekor `/hana/shared` . További részletek: a [sikertelen NFS-megosztások használata a SUSE ha-fürtben a HANA rendszer-replikációhoz](https://www.suse.com/support/kb/doc/?id=000019904) 

1. **[1]** a pacemaker karbantartási módba helyezése a HANA-fürt erőforrásainak létrehozásához.  
    ```bash
    crm configure property maintenance-mode=true
    ```

2. **[1, 2]** hozza létre a KÖNYVTÁRAT a ANF/Hana/sahred kötetén, amelyet a rendszer a speciális fájlrendszer-figyelési erőforrásban fog használni. A címtárakat mindkét helyen létre kell hozni.  
    ```bash
    mkdir -p /hana/shared/HN1/check
    ```

2. **[Ah]** Hozza létre a könyvtárat, amelyet a rendszer a speciális fájlrendszer figyelési erőforrásának csatlakoztatására fog használni. A könyvtárat létre kell hozni a HANA-fürtcsomópontok összes csomópontján.  
    ```bash
    mkdir -p /hana/check
    ```

3. **[1]** hozza létre a fájlrendszerbeli fürt erőforrásait.     

    ```bash
    crm configure primitive fs_HN1_HDB03_fscheck Filesystem \
      params device="/hana/shared/HN1/check" \
      directory="/hana/check" fstype=nfs4 \
      options="bind,defaults,rw,hard,proto=tcp,intr,noatime,vers=4.1,lock" \
      op monitor interval=120 timeout=120 on-fail=fence \
      op_params OCF_CHECK_LEVEL=20 \
      op start interval=0 timeout=120 op stop interval=0 timeout=120

    crm configure clone cln_fs_HN1_HDB03_fscheck fs_HN1_HDB03_fscheck \
      meta clone-node-max=1 interleave=true

    crm configure location loc_cln_fs_HN1_HDB03_fscheck_not_on_mm \
      cln_fs_HN1_HDB03_fscheck -inf: hana-s-mm    
    ```
 
   `OCF_CHECK_LEVEL=20` a rendszer hozzáadja a (z) attribútumot a figyelő művelethez, így a figyelési műveletek olvasási/írási tesztet hajtanak végre a fájlrendszeren. Ezen attribútum nélkül a figyelő művelet csak azt ellenőrzi, hogy a fájlrendszer csatlakoztatva van-e. Ez problémát jelenthet, mert ha a kapcsolat megszakad, a fájlrendszer csatlakoztatva maradhat, annak ellenére, hogy nem érhető el.  

   `on-fail=fence` a rendszer az attribútumot is hozzáadja a figyelő művelethez. Ha ezt a lehetőséget választja, ha a figyelő művelet egy csomóponton meghiúsul, a csomópont azonnal be lesz kerítve.   

## <a name="create-sap-hana-cluster-resources"></a>SAP HANA-fürt erőforrásainak létrehozása

1. **[1, 2]** a HANA "rendszerreplikálási Hook" telepítése. A hookot egy HANA DB-csomópontra kell telepíteni az egyes rendszer-replikációs helyeken.         

   1. A Hook előkészítése `root` 
    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR-ScaleOut/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. A HANA leállítása mindkét rendszer replikációs helyein. Végrehajtás <SID adm-ként \> :
    ```bash
    sapcontrol -nr 03 -function StopSystem
    ```

   3. Módosíthatja `global.ini`
    ```bash
    # add to global.ini
    [ha_dr_provider_SAPHanaSR]
    provider = SAPHanaSR
    path = /hana/shared/myHooks
    execution_order = 1
    
    [trace]
    ha_dr_saphanasr = info
    ```

2. **[Ah]** A fürtön a <SID adm-hez tartozó fürtcsomópontok esetében a sudoers konfigurálása szükséges \> . Ebben a példában egy új fájl létrehozásával érhető el. Hajtsa végre a parancsokat a következőképpen: `root` .    
    ```bash
    cat << EOF > /etc/sudoers.d/20-saphana
    # SAPHanaSR-ScaleOut needs for srHook
    Cmnd_Alias SOK = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SOK -t crm_config -s SAPHanaSR
    Cmnd_Alias SFAIL = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SFAIL -t crm_config -s SAPHanaSR
    hn1adm ALL=(ALL) NOPASSWD: SOK, SFAIL
    EOF
    ```

3. **[1, 2]** a Start SAP HANA mindkét replikációs helyen. Végrehajtás <SID adm-ként \> .  

    ```bash
    sapcontrol -nr 03 -function StartSystem 
    ```

4. **[1]** ellenőrizze a Hook telepítését. Végrehajtás <SID adm-ként \> az aktív HANA rendszer-replikációs helyen.   

    ```bash
    cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*

     # 2021-03-31 01:02:42.695244 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:02:58.966856 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:03:04.453100 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:03:04.619768 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:03:04.743444 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:04:15.062181 ha_dr_SAPHanaSR SOK

    ```

5. **[1]** hozza létre a HANA-fürt erőforrásait. Hajtsa végre a következő parancsokat `root` .    
   1. Győződjön meg arról, hogy a fürt már karbantartási módban van.  
    
   2. Ezután hozza létre a HANA-topológia erőforrását.  
      ```bash
      sudo crm configure primitive rsc_SAPHanaTopology_HN1_HDB03 ocf:suse:SAPHanaTopology \
        op monitor interval="10" timeout="600" \
        op start interval="0" timeout="600" \
        op stop interval="0" timeout="300" \
        params SID="HN1" InstanceNumber="03"

      sudo crm configure clone cln_SAPHanaTopology_HN1_HDB03 rsc_SAPHanaTopology_HN1_HDB03 \
       meta clone-node-max="1" target-role="Started" interleave="true"
      ```

   3. Ezután hozza létre a HANA-példány erőforrását.  
      > [!NOTE] 
      > Ez a cikk a " *Master* " és a " *Slave*" kifejezésekre mutató hivatkozásokat tartalmaz, amelyeket a Microsoft már nem használ. Ha eltávolítja ezeket a feltételeket a szoftverből, a rendszer eltávolítja őket ebből a cikkből.
 
      ```bash
      sudo crm configure primitive rsc_SAPHana_HN1_HDB03 ocf:suse:SAPHanaController \
        op start interval="0" timeout="3600" \
        op stop interval="0" timeout="3600" \
        op promote interval="0" timeout="3600" \
        op monitor interval="60" role="Master" timeout="700" \
        op monitor interval="61" role="Slave" timeout="700" \
        params SID="HN1" InstanceNumber="03" PREFER_SITE_TAKEOVER="true" \
        DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

      sudo crm configure ms msl_SAPHana_HN1_HDB03 rsc_SAPHana_HN1_HDB03 \
        meta clone-node-max="1" master-max="1" interleave="true"
      ```
      > [!IMPORTANT]
      > Ajánlott eljárásként Azt javasoljuk, hogy csak a **nem** értékre állítsa a AUTOMATED_REGISTERt, miközben alapos feladatátvételi teszteket végez, hogy megakadályozza a sikertelen elsődleges példányok másodlagosként való automatikus regisztrálását. Miután a feladatátvételi tesztek sikeresen befejeződtek, állítsa AUTOMATED_REGISTER **Igen** értékre, hogy az áttelepítési rendszer replikációja automatikusan folytatódjon. 

   4. Hozzon létre virtuális IP-címet és kapcsolódó erőforrásokat.  
      ```bash
      sudo crm configure primitive rsc_ip_HN1_HDB03 ocf:heartbeat:IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="10.23.0.27"

      sudo crm configure primitive rsc_nc_HN1_HDB03 azure-lb port=62503 \
        meta resource-stickiness=0

      sudo crm configure group g_ip_HN1_HDB03 rsc_ip_HN1_HDB03 rsc_nc_HN1_HDB03
      ```

   5. A fürt korlátozásainak létrehozása  
      ```bash
      # Colocate the IP with HANA master
      sudo crm configure colocation col_saphana_ip_HN1_HDB03 4000: g_ip_HN1_HDB03:Started \
        msl_SAPHana_HN1_HDB03:Master  

      # Start HANA Topology before HANA  instance
      sudo crm configure order ord_SAPHana_HN1_HDB03 Optional: cln_SAPHanaTopology_HN1_HDB03 \
        msl_SAPHana_HN1_HDB03

      # HANA resources don't run on the majority maker node
      sudo crm configure location loc_SAPHanaCon_not_on_majority_maker msl_SAPHana_HN1_HDB03 -inf: hana-s-mm
      sudo crm configure location loc_SAPHanaTop_not_on_majority_maker cln_SAPHanaTopology_HN1_HDB03 -inf: hana-s-mm
      ```

6. **[1]** további fürt tulajdonságainak konfigurálása   
    ```bash
    sudo crm configure rsc_defaults resource-stickiness=1000
    sudo crm configure rsc_defaults migration-threshold=50
    ```
7. **[1]** a Hook és a fürt közötti kommunikáció ellenőrzése
    ```bash
    crm_attribute -G -n hana_hn1_glob_srHook
    # Expected result
    # crm_attribute -G -n hana_hn1_glob_srHook
    # scope=crm_config  name=hana_hn1_glob_srHook value=SOK
    ```

8. **[1]** helyezze el a fürtöt karbantartási módból. Győződjön meg arról, hogy a fürt állapota ok, és hogy az összes erőforrás el van indítva.  
    ```bash
    # Cleanup any failed resources - the following command is example 
    crm resource cleanup rsc_SAPHana_HN1_HDB03

    # Place the cluster out of maintenance mode
    sudo crm configure property maintenance-mode=false
    ```
  
   > [!NOTE]
   > A fenti konfiguráció időtúllépései csak példák, és előfordulhat, hogy az adott HANA-beállításhoz kell igazítani. Előfordulhat például, hogy a kezdési időkorlátot meg kell emelni, ha a SAP HANA-adatbázis elindításához tovább tart.
  

## <a name="test-sap-hana-failover"></a>Feladatátvételi SAP HANA tesztelése 

> [!NOTE]
> Ez a cikk a " *Master* " és a " *Slave*" kifejezésekre mutató hivatkozásokat tartalmaz, amelyeket a Microsoft már nem használ. Ha eltávolítja ezeket a feltételeket a szoftverből, a rendszer eltávolítja őket ebből a cikkből.

1. Mielőtt elkezdené a tesztet, ellenőrizze a fürtöt, és SAP HANA a rendszer replikálási állapotát.    

   a. Ellenőrizze, hogy nincsenek-e sikertelen műveletek a fürtben  
     ```bash
     #Verify that there are no failed cluster actions
     crm status
     # Example 
     #7 nodes configured
     #24 resource instances configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Full list of resources:
     #
     # stonith-sbd    (stonith:external/sbd): Started hana-s-mm
     # Clone Set: cln_fs_HN1_HDB03_fscheck [fs_HN1_HDB03_fscheck]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #     Stopped: [ hana-s-mm ]
     # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #     Stopped: [ hana-s-mm ]
     # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     #     Masters: [ hana-s1-db1 ]
     #     Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #     Stopped: [ hana-s-mm ]
     # Resource Group: g_ip_HN1_HDB03
     #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
     #     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hana-s1-db1
     ```

   b. Annak ellenőrzése, hogy SAP HANA rendszer-replikáció szinkronban van-e

     ```bash
     # Verify HANA HSR is in sync
     sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
     #| Database | Host         | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary    | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
     #|          |              |       |              |           |         |           | Host         | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
     #| -------- | ------------ | ----- | ------------ | --------- | ------- | --------- | ------------ | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
     #| SYSTEMDB | hana-s1-db1  | 30301 | nameserver   |         1 |       1 | HANA_S1   | hana-s2-db1  |     30301 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db1  | 30307 | xsengine     |         2 |       1 | HANA_S1   | hana-s2-db1  |     30307 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db1  | 30303 | indexserver  |         3 |       1 | HANA_S1   | hana-s2-db1  |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db3  | 30303 | indexserver  |         4 |       1 | HANA_S1   | hana-s2-db3  |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db2  | 30303 | indexserver  |         5 |       1 | HANA_S1   | hana-s2-db2  |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #
     #status system replication site "1": ACTIVE
     #overall system replication status: ACTIVE
     #
     #Local System Replication State
     #~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
     #
     #mode: PRIMARY
     #site id: 1
     #site name: HANA_S1
     ```

2. Javasoljuk, hogy alaposan érvényesítse a SAP HANA fürtkonfiguráció a SLES-on futó Azure-beli [virtuális gépeken](./sap-hana-high-availability.md#test-the-cluster-setup) , illetve a [SLES-replikáció kibővíthető teljesítményének optimalizált forgatókönyvében](https://documentation.suse.com/sbp/all/html/SLES4SAP-hana-scaleOut-PerfOpt-12/index.html#_testing_the_cluster)SAP HANA ismertetett tesztek végrehajtásával.

3. Ellenőrizze, hogy a fürt konfigurációja meghiúsul-e, amikor egy csomópont elveszti az NFS-megosztáshoz () való hozzáférést `/hana/shared` .  

   A SAP HANA erőforrás-ügynökök a `/hana/shared` feladatátvétel során a művelet végrehajtásához tárolt bináris fájloktól függenek. A fájlrendszer az `/hana/shared` NFS-en keresztül van csatlakoztatva a bemutatott konfigurációban. Az elvégezhető teszttel egy ideiglenes tűzfalszabály hozható létre, amely letiltja a ANF-kötet elérését az `/hana/shared` egyik elsődleges hely virtuális gépei között. Ez a megközelítés ellenőrzi, hogy a fürt átadja-e a feladatátvételt, ha `/hana/shared` az aktív rendszerreplikálási helyhez való hozzáférés elvész.  

   **Várt eredmény**: Ha letiltja a ANF-kötethez való hozzáférést az `/hana/shared` egyik elsődleges hely virtuális gépei között, a fájlrendszer olvasási/írási műveletét végrehajtó figyelési művelet sikertelen lesz, mivel nem fér hozzá a fájlrendszerhez, és elindítja a HANA erőforrás-feladatátvételt. Ugyanez az eredmény várható, ha a HANA-csomópont elveszti az NFS-megosztás elérését.  
     
   A fürt erőforrásainak állapotát a vagy a futtatásával is megtekintheti `crm_mon` `crm status` . Erőforrás állapota a teszt elindítása előtt:
     ```bash
     # Output of crm_mon
     #7 nodes configured
     #24 resource instances configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Active resources:
     #
     #stonith-sbd     (stonith:external/sbd): Started hana-s-mm
     # Clone Set: cln_fs_HN1_HDB03_fscheck [fs_HN1_HDB03_fscheck]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     #     Masters: [ hana-s1-db1 ]
     #     Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Resource Group: g_ip_HN1_HDB03
     #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hana-s2-db1
     #     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hana-s2-db1     
      ```

   A hibájának szimulálása érdekében `/hana/shared` először erősítse meg az `/hana/shared` elsődleges helyen található ANF-kötet IP-címét. Ezt a futtatásával teheti meg `df -kh|grep /hana/shared` . 
   Ezután állítson be egy ideiglenes tűzfalszabály a ANF-kötet IP-címéhez való hozzáférés blokkolásához a `/hana/shared` következő parancs futtatásával az elsődleges HANA rendszer-replikációs hely virtuális gépei egyikén.
   Ebben a példában a parancs a Hana-S1-db1-on lett végrehajtva.

     ```bash
     iptables -A INPUT -s 10.23.1.7 -j DROP; iptables -A OUTPUT -d 10.23.1.7 -j DROP
     ```

   A fürt erőforrásai át lesznek telepítve a másik HANA rendszerbeli replikációs helyre.    
              
   Ha a AUTOMATED_REGISTER = "false" értéket állítja be, akkor konfigurálnia kell SAP HANA rendszer-replikálást a másodlagos helyen. Ebben az esetben végrehajthatja ezeket a parancsokat a SAP HANA másodlagosként való újrakonfigurálásához.   

     ```bash
     # Execute on the secondary 
     su - hn1adm
     # Make sure HANA is not running on the secondary site. If it is started, stop HANA
     sapcontrol -nr 03 -function StopWait 600 10
     # Register the HANA secondary site
     hdbnsutil -sr_register --name=HANA_S1 --remoteHost=hana-s2-db1 --remoteInstance=03 --replicationMode=sync
     # Switch back to root and cleanup failed resources
     crm resource cleanup SAPHana_HN1_HDB03
     ```

   Az erőforrások állapota a teszt után: 

     ```bash
     # Output of crm_mon
     #7 nodes configured
     #24 resource instances configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Active resources:
     #
     #stonith-sbd     (stonith:external/sbd): Started hana-s-mm
     # Clone Set: cln_fs_HN1_HDB03_fscheck [fs_HN1_HDB03_fscheck]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     #     Masters: [ hana-s2-db1 ]
     #     Slaves: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db2 hana-s2-db3 ]
     # Resource Group: g_ip_HN1_HDB03
     #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hana-s2-db1
     #     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hana-s2-db1
     ```


## <a name="next-steps"></a>Következő lépések

* [Azure Virtual Machines az SAP tervezéséhez és megvalósításához][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP-ban][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hoz][dbms-guide]
* [NFS 4.1-es verziójú kötetek az SAP HANA-hoz készült Azure NetApp Filesban](./hana-vm-operations-netapp.md)
* A magas rendelkezésre állás és a SAP HANA Azure-beli virtuális gépeken történő vész-helyreállítási tervének megismeréséhez tekintse meg a [SAP HANA magas rendelkezésre állását az azure Virtual Machinesban (VM)][sap-hana-ha].
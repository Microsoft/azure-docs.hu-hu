---
title: SAP HANA a HSR és a pacemakert a RHEL-ben | Microsoft Docs
description: SAP HANA a HSR és a pacemaker on RHEL
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
ms.openlocfilehash: 6ce169eee4f931d02071a326c65ba94cfe723ae1
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259033"
---
# <a name="high-availability-of-sap-hana-scale-out-system-on-red-hat-enterprise-linux"></a>SAP HANA kibővíthető rendszer magas rendelkezésre állása Red Hat Enterprise Linux 

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


Ez a cikk azt ismerteti, hogyan helyezhető üzembe egy magasan elérhető SAP HANA rendszer a HANA System Replication (HSR) és a pacemaker használatával az Azure Red Hat Enterprise Linux Virtual Machines (VM) szolgáltatásban. A bemutatott architektúrában található megosztott fájlrendszereket [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) biztosítjuk, és az NFS-en keresztül vannak csatlakoztatva.  

A példában a konfigurációk, a telepítési parancsok és így tovább, a HANA-példány **03** , a HANA rendszer azonosítója pedig **HN1**. A példák a HANA 2,0 SP4 és az SAP 7,6 Red Hat Enterprise Linux alapján érhetők el. 

Mielőtt elkezdené, tekintse meg a következő SAP-megjegyzéseket és dokumentumokat:

* A [1928533] -es SAP-Megjegyzés tartalma:  
  * Az SAP-szoftverek üzembe helyezéséhez támogatott Azure-beli virtuálisgép-méretek listája
  * Fontos kapacitási információk Azure-beli virtuális gépek méreteihez
  * Támogatott SAP-szoftverek és operációs rendszerek (OS) és adatbázis-kombinációk
  * A Windows és a Linux szükséges SAP kernel-verziója a Microsoft Azure
* [2015553]-es SAP-Megjegyzés: az Azure-ban az SAP által támogatott SAP-szoftverek üzembe helyezésének előfeltételeit sorolja fel
* Az SAP-Megjegyzés [2002167] ajánlott operációsrendszer-beállításokkal Red Hat Enterprise Linux
* A [2009879] -es SAP-Megjegyzés SAP HANA irányelvek a Red Hat Enterprise Linux
* [2178632]-es SAP-Megjegyzés: részletes információkat tartalmaz az Azure-beli SAP-ban jelentett összes figyelési metrikáról
* [2191498]-es SAP-Megjegyzés: a szükséges SAP Host Agent-verziót tartalmazza az Azure-beli Linux rendszerhez
* [2243692]-es SAP-Megjegyzés: az Azure-beli Linuxon futó SAP-licenceléssel kapcsolatos információkat tartalmaz
* [1999351]-es SAP-Megjegyzés: az SAP-hez készült Azure Enhanced monitoring bővítmény további hibaelhárítási információit tartalmazza.
* [1900823]-es SAP-Megjegyzés: a tárolási követelményekre vonatkozó információkat tartalmaz SAP HANA
* [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): tartalmazza az összes szükséges SAP-megjegyzést Linux rendszerhez
* [Azure Virtual Machines tervezése és implementálása Linux rendszeren az SAP-ban][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP-hez Linux rendszeren][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hez Linux rendszeren][dbms-guide]
* [SAP HANA hálózati követelmények](https://www.sap.com/documents/2016/08/1cd2c2fb-807c-0010-82c7-eda71af511fa.html)
* Általános RHEL dokumentáció
  * [Magas rendelkezésre állású Add-On áttekintése](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Magas rendelkezésre állású Add-On felügyelet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Magas rendelkezésre állású Add-On referenciája](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Red Hat Enterprise Linux hálózati útmutató](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide)
  * [SAP HANA Scale-Out rendszerreplikáció konfigurálása egy pacemaker-fürtön az NFS-megosztásokon HANA-fájlrendszerrel Hogyan](https://access.redhat.com/solutions/5423971)
* Az Azure-specifikus RHEL dokumentációja:
  * [A Red Hat Enterprise Linux SAP HANA telepítése a Microsoft Azure-ben való használatra](https://access.redhat.com/public-cloud/microsoft-azure)
  * [Red Hat Enterprise Linux megoldás SAP HANA Scale-Out és rendszer-replikáláshoz](https://access.redhat.com/solutions/4386601)
* [NetApp SAP-alkalmazások Microsoft Azure a Azure NetApp Files használatával][anf-sap-applications-azure]
* [Azure NetApp Files dokumentáció][anf-azure-doc] 
* [NFS 4.1-es verziójú kötetek az SAP HANA-hoz készült Azure NetApp Filesban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-netapp)

## <a name="overview"></a>Áttekintés

Az egyik módszer a Hana-kibővíthető telepítések HANA magas rendelkezésre állásának elérésére, a HANA rendszerreplikáció konfigurálása és a megoldás és a pacemaker-fürt elleni védelem az automatikus feladatátvétel engedélyezéséhez. Ha egy aktív csomópont meghibásodik, a fürt átadja a HANA-erőforrásokat a másik helyre.  
A bemutatott konfiguráció három HANA-csomópontot jelenít meg az egyes helyeken, valamint a többségi készítő csomópontot, hogy megakadályozza a megosztott agyi forgatókönyvet. Az utasítások úgy módosíthatók, hogy több virtuális gépet is felvegyen HANA DB-csomópontként.  

A `/hana/shared` bemutatott architektúrában a HANA megosztott fájlrendszert [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md)biztosítjuk. A NFSv 4.1-es porton keresztül csatlakozik a Hana-rendszer replikációs helyének minden HANA-csomópontján. A fájlrendszerek `/hana/data` és a `/hana/log` helyi fájlrendszerek, és nincsenek megosztva a HANA db-csomópontok között. A SAP HANA nem megosztott módban lesznek telepítve. 

> [!TIP]
> Az ajánlott SAP HANA tárolási konfigurációkhoz lásd: [SAP HANA Azure virtuális gépek tárolási konfigurációi](./hana-vm-operations-storage.md).   

[![SAP HANA a HSR és a pacemaker-fürttel](./media/sap-hana-high-availability-rhel/sap-hana-high-availability-scale-out-hsr-rhel.png)](./media/sap-hana-high-availability-rhel/sap-hana-high-availability-scale-out-hsr-rhel-detail.png#lightbox)

Az előző ábrán három alhálózat található egy Azure-beli virtuális hálózaton belül, a SAP HANA hálózati javaslatok követésével: 
* ügyfél-kommunikációhoz – `client` 10.23.0.0/24  
* a belső HANA-csomópontok közötti kommunikációhoz – `inter` 10.23.1.128/26  
* HANA rendszerbeli replikáláshoz – `hsr` 10.23.1.192/26  

A `/hana/data` és a `/hana/log` helyi lemezeken való üzembe helyezése esetén nem szükséges külön alhálózatot és különálló virtuális hálózati kártyákat telepíteni a tárolóhoz való kommunikációhoz.  

Az Azure NetApp-kötetek külön alhálózatban vannak telepítve, [Azure NetApp Files] delegálva ( https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet: `anf` 10.23.1.0/26).   

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
      1. Állítsa a **hozzárendelést** **statikus** értékre, és adja meg az IP-címet (például **10.23.0.18**).
      1. Válassza az **OK** lehetőséget.
      1. Az új előtér-IP-készlet létrehozása után jegyezze fel a készlet IP-címét.

   1. Ezután hozzon létre egy háttér-készletet, és vegye fel az összes fürtbeli virtuális gépet a háttér-készletbe:

      1. Nyissa meg a Load balancert, válassza a **háttérbeli készletek** lehetőséget, majd válassza a **Hozzáadás** lehetőséget.
      1. Adja meg az új háttér-készlet nevét (például **Hana-backend**).
      1. Válassza **a virtuális gép hozzáadása** lehetőséget.
      1. Válassza a **Virtuális gép** lehetőséget.
      1. Válassza ki a SAP HANA-fürt virtuális gépei és az alhálózat IP-címeit `client` .
      1. Válassza a **Hozzáadás** elemet.

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
      1. Növelje az **üresjárati időkorlátot** 30 percre.
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

Telepítse a fájlrendszer ANF-köteteit `/hana/shared` . `/hana/shared`Minden HANA rendszerbeli replikációs helyhez külön kötetre lesz szüksége. További információ: [a Azure NetApp Files-infrastruktúra beállítása](./sap-hana-scale-out-standby-netapp-files-rhel.md#set-up-the-azure-netapp-files-infrastructure).

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
     10.23.0.11 hana-s1-db1
     10.23.0.12 hana-s1-db1
     10.23.0.13 hana-s1-db2
     10.23.0.14 hana-s2-db1
     10.23.0.15 hana-s2-db2
     10.23.0.16 hana-s2-db3
     10.23.0.17 hana-s-mm
     # Internode subnet
     10.23.1.138 hana-s1-db1-inter
     10.23.1.139 hana-s1-db2-inter
     10.23.1.140 hana-s1-db3-inter
     10.23.1.141 hana-s2-db1-inter
     10.23.1.142 hana-s2-db2-inter
     10.23.1.143 hana-s2-db3-inter
     # HSR subnet
     10.23.1.202 hana-s1-db1-hsr
     10.23.1.203 hana-s1-db2-hsr
     10.23.1.204 hana-s1-db3-hsr
     10.23.1.205 hana-s2-db1-hsr
     10.23.1.206 hana-s2-db2-hsr
     10.23.1.207 hana-s2-db3-hsr
    ```


2. **[A]** telepítse az NFS-ügyfélszoftvert.  

    ```yum install nfs-utils ```


3. **[Ah]** Red hat a HANA-konfigurációhoz.  

    Konfigurálja a RHEL a (z) <https://access.redhat.com/solutions/2447641> és a következő SAP-megjegyzésekben leírtak szerint:  
   - [2292690 – SAP HANA DB: ajánlott operációsrendszer-beállítások a RHEL 7 rendszerhez](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782 – SAP HANA DB: ajánlott operációsrendszer-beállítások a RHEL 8 rendszerhez](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582 – Linux: a GCC 6. x-sel lefordított SAP-alkalmazások futtatása](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824 – Linux: a GCC 7. x verzióval lefordított SAP-alkalmazások futtatása](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607 – Linux: a GCC 9. x-szel lefordított SAP-alkalmazások futtatása](https://launchpad.support.sap.com/#/notes/2886607)

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
    mount 10.9.0.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

   A paraméter módosításáról további információt a `nfs4_disable_idmapping` következő témakörben talál: https://access.redhat.com/solutions/1749883 .

4. **[AH1]** Csatlakoztassa a megosztott Azure NetApp Files köteteket a HELY1 HANA DB virtuális gépekhez.  

    ```bash
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.23.1.7:/HN1-shared-s1 /hana/shared
    ```

5. **[AH2]** Csatlakoztassa a megosztott Azure NetApp Files köteteket a SITE2 HANA DB virtuális gépekhez.  

    ```bash
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.23.1.7:/HN1-shared-s2 /hana/shared
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

## <a name="installation"></a>Telepítés  

Ebben a példában az Azure-beli virtuális gépeken található HSR-alapú kibővített konfigurációban SAP HANA üzembe helyezéséhez a HANA 2,0 SP4-et használtuk.  

### <a name="prepare-for-hana-installation"></a>A HANA telepítésének előkészítése

1. **[Ah]** A HANA telepítésének megkezdése előtt állítsa be a legfelső szintű jelszót. A legfelső szintű jelszót a telepítés befejezése után tilthatja le. Végrehajtás `root` parancsként `passwd` .  

2. **[1, 2]** az engedélyek módosítása bekapcsolva `/hana/shared` 
    ```bash
    chmod 775 /hana/shared
    ```

3. **[1]** ellenőrizze, hogy SSH-n keresztül tud-e bejelentkezni a Hana db virtuális gépekre a következő helyen: **Hana-S1-DB2** és **HANA-S1-db3**, anélkül, hogy jelszót kellene megadnia.  
   Ha ez nem igaz, az Exchange ssh-kulcsok a [kulcs alapú hitelesítés használatával](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/s2-ssh-configuration-keypairs)dokumentálva vannak.  
    ```bash
    ssh root@hana-s1-db2
    ssh root@hana-s1-db3
    ```

4. **[2]** ellenőrizze, hogy SSH-n keresztül tud-e bejelentkezni a Hana db virtuális gépekre a következő helyen: **Hana-S2-DB2** és **HANA-S2-db3**, anélkül, hogy jelszót kellene megadnia.  
   Ha ez nem igaz, az Exchange ssh-kulcsok a [kulcs alapú hitelesítés használatával](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/s2-ssh-configuration-keypairs)dokumentálva vannak.  
    ```bash
    ssh root@hana-s2-db2
    ssh root@hana-s2-db3
    ```

5. **[Ah]** Telepítsen további csomagokat, amelyek szükségesek a HANA 2,0 SP4-hez. További információ: SAP Note [2593824](https://launchpad.support.sap.com/#/notes/2593824) a RHEL 7 rendszerhez. 

    ```bash
    # If using RHEL 7
    yum install libgcc_s1 libstdc++6 compat-sap-c++-7 libatomic1
    # If using RHEL 8
    yum install libatomic libtool-ltdl.x86_64
    ```


6. **[A]** ideiglenesen tiltsa le a tűzfalat, hogy az ne zavarja a HANA telepítését. A HANA telepítésének befejezése után újra engedélyezheti. 
    ```bash
    # Execute as root
    systemctl stop firewalld
    systemctl disable firewalld
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
     10.23.1.138 = hana-s1-db1
     10.23.1.139 = hana-s1-db2
     10.23.1.140 = hana-s1-db3
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
    "hana-s2-db1","net_publicname","10.23.0.14"
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
    10.23.1.202 = hana-s1-db1
    10.23.1.203 = hana-s1-db2
    10.23.1.204 = hana-s1-db3
    10.23.1.205 = hana-s2-db1
    10.23.1.206 = hana-s2-db2
    10.23.1.207 = hana-s2-db3
    ```

   - A HANA elindítása mindkét helyen
   ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem HDB
   ```

   További információ: [a Rendszerreplikáció gazdagép](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/1.0.12/en-US/c0cba1cb2ba34ec89f45b48b2157ec7b.html)-névfeloldása.  

5. **[Ah]** Engedélyezze újra a tűzfalat.  
   - A tűzfal újbóli engedélyezése
       ```bash
       # Execute as root
       systemctl start firewalld
       systemctl enable firewalld
       ```

   - Nyissa meg a szükséges tűzfal portjait. A HANA-példány számának portjait módosítania kell.  

       > [!IMPORTANT]
       > Tűzfalszabályok létrehozása a HANA-csomópontok közötti kommunikáció és az ügyfelek forgalmának engedélyezéséhez. A szükséges portok az [összes SAP-termék TCP/IP-portjain](https://help.sap.com/viewer/ports)vannak felsorolva. A következő parancsok csak példaként szolgálnak. Ebben az esetben a használatban lévő rendszer 03-as számú.

       ```bash
        # Execute as root
        sudo firewall-cmd --zone=public --add-port=30301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30301/tcp
        sudo firewall-cmd --zone=public --add-port=30303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30303/tcp
        sudo firewall-cmd --zone=public --add-port=30306/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30306/tcp
        sudo firewall-cmd --zone=public --add-port=30307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30307/tcp
        sudo firewall-cmd --zone=public --add-port=30313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30313/tcp
        sudo firewall-cmd --zone=public --add-port=30315/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30315/tcp
        sudo firewall-cmd --zone=public --add-port=30317/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30317/tcp
        sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30340/tcp
        sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30341/tcp
        sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30342/tcp
        sudo firewall-cmd --zone=public --add-port=1128/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1128/tcp
        sudo firewall-cmd --zone=public --add-port=1129/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1129/tcp
        sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40302/tcp
        sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40301/tcp
        sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40307/tcp
        sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40303/tcp
        sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40340/tcp
        sudo firewall-cmd --zone=public --add-port=50313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50313/tcp
        sudo firewall-cmd --zone=public --add-port=50314/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50314/tcp
        sudo firewall-cmd --zone=public --add-port=30310/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30310/tcp
        sudo firewall-cmd --zone=public --add-port=30302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30302/tcp
       ```

## <a name="create-a-pacemaker-cluster"></a>Pacemaker-fürt létrehozása

Ha alapszintű pacemaker-fürtöt szeretne létrehozni ehhez a HANA-kiszolgálóhoz, kövesse az Azure-beli [Red Hat Enterprise Linux pacemaker beállítása](high-availability-guide-rhel-pacemaker.md) című témakör lépéseit.
Az összes virtuális gép belefoglalása, beleértve a fürt többségi gyártóját is.  

> [!IMPORTANT]
> Ne állítsa `quorum expected-votes` 2 értékre, mert ez nem egy két csomópontos fürt.  
> Győződjön meg arról, hogy a cluster tulajdonság `concurrent-fencing`  engedélyezve van, hogy a csomópont-kerítés deszerializálva legyen.   

## <a name="create-file-system-resources"></a>Fájlrendszer erőforrásainak létrehozása

1. **[1, 2]** SAP HANA leállítása mindkét replikációs helyen. Végrehajtás <SID adm-ként \> .  

    ```bash
    sapcontrol -nr 03 -function StopSystem
    ```

2. **[Ah]** Nem `/hana/shared` csatlakoztatott fájlrendszer, amelyet a rendszer ideiglenesen csatlakoztatt az összes HANA db-alapú virtuális gépen történő telepítéshez. Le kell állítania minden olyan folyamatot és munkamenetet, amely a fájlrendszert használja, mielőtt a csatlakoztatást el tudja végezni. 
 
    ```bash
    umount /hana/shared 
    ```

3. **[1]** a fájlrendszerbeli fürt erőforrásainak létrehozása `/hana/shared` letiltott állapotban. Az erőforrások a lehetőséggel jönnek létre `--disabled` , mert a csatlakoztatások engedélyezése előtt meg kell határoznia a hely megkötéseit.  

    ```bash
    # /hana/shared file system for site 1
    pcs resource create fs_hana_shared_s1 --disabled ocf:heartbeat:Filesystem device=10.23.1.7:/HN1-shared-s1  directory=/hana/shared \
    fstype=nfs options='defaults,rw,hard,timeo=600,rsize=262144,wsize=262144,proto=tcp,intr,noatime,sec=sys,vers=4.1,lock,_netdev' op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 \
    op start interval=0 timeout=120 op stop interval=0 timeout=120

    # /hana/shared file system for site 2   
    pcs resource create fs_hana_shared_s2 --disabled ocf:heartbeat:Filesystem device=10.23.1.7:/HN1-shared-s1 directory=/hana/shared \
    fstype=nfs options='defaults,rw,hard,timeo=600,rsize=262144,wsize=262144,proto=tcp,intr,noatime,sec=sys,vers=4.1,lock,_netdev' op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 \
    op start interval=0 timeout=120 op stop interval=0 timeout=120

    # clone the /hana/shared file system resources for both site1 and site2
    pcs resource clone fs_hana_shared_s1 meta clone-node-max=1 interleave=true
    pcs resource clone fs_hana_shared_s2 meta clone-node-max=1 interleave=true
    ```
 
   `OCF_CHECK_LEVEL=20` a rendszer hozzáadja a (z) attribútumot a figyelő művelethez, így a figyelési műveletek olvasási/írási tesztet hajtanak végre a fájlrendszeren. Ezen attribútum nélkül a figyelő művelet csak azt ellenőrzi, hogy a fájlrendszer csatlakoztatva van-e. Ez problémát jelenthet, mert ha a kapcsolat megszakad, a fájlrendszer csatlakoztatva maradhat, annak ellenére, hogy nem érhető el.  

   `on-fail=fence` a rendszer az attribútumot is hozzáadja a figyelő művelethez. Ha ezt a lehetőséget választja, ha a figyelő művelet egy csomóponton meghiúsul, a csomópont azonnal be lesz kerítve. A beállítás nélkül az alapértelmezett viselkedés az összes olyan erőforrás leállítása, amely a sikertelen erőforrástól függ, majd indítsa újra a sikertelen erőforrást, majd indítsa el az összes olyan erőforrást, amely a sikertelen erőforrástól függ. Ez a viselkedés nem csak hosszú időt vehet igénybe, ha egy SAPHana-erőforrás a sikertelen erőforrástól függ, de az is lehet, hogy teljesen meghiúsul. A SAPHana-erőforrás nem állítható le sikeresen, ha a HANA bináris fájljait tároló NFS-megosztás nem érhető el.  

4. **[1]** konfigurálja és ellenőrizze a csomópont attribútumait. A (z) 1. replikációs helyen található összes SAP HANA adatbázis-csomópont hozzárendelt attribútum `S1` , a 2. replikációs helyen található összes SAP HANA db-csomópont pedig hozzárendelt attribútum `S2` .  

    ```bash
    # HANA replication site 1
    pcs node attribute hana-s1-db1 NFS_SID_SITE=S1
    pcs node attribute hana-s1-db2 NFS_SID_SITE=S1
    pcs node attribute hana-s1-db3 NFS_SID_SITE=S1
    # HANA replication site 2
    pcs node attribute hana-s2-db1 NFS_SID_SITE=S2
    pcs node attribute hana-s2-db2 NFS_SID_SITE=S2
    pcs node attribute hana-s2-db3 NFS_SID_SITE=S2
    #To verify the attribute assignment to nodes execute
    pcs node attribute
    ```

5. **[1]** konfigurálja a korlátozásokat, amelyek meghatározzák, hogy hol lesznek csatlakoztatva az NFS-fájlrendszerek, és hogyan engedélyezze a fájlrendszer erőforrásait.  
    ```bash
    # Configure the constraints
    pcs constraint location fs_hana_shared_s1-clone rule resource-discovery=never score=-INFINITY NFS_SID_SITE ne S1
    pcs constraint location fs_hana_shared_s2-clone rule resource-discovery=never score=-INFINITY NFS_SID_SITE ne S2
    # Enable the file system resources
    pcs resource enable fs_hana_shared_s1
    pcs resource enable fs_hana_shared_s2
    ```

   Ha engedélyezi a fájlrendszer erőforrásait, a fürt csatlakoztatni fogja a `/hana/shared` fájlrendszereket.
 
6. **[Ah]** Győződjön meg arról, hogy a ANF-kötetek az `/hana/shared` összes HANA db virtuális gépen vannak csatlakoztatva mindkét helyen.

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

7. **[1]** konfigurálja az attribútumok erőforrásait. Konfigurálja a korlátozásokat, amelyek beállítja az attribútumokat `true` , ha az NFS-csatolók `hana/shared` csatlakoztatva vannak.  

    ```bash
    # Configure the attribure resources
    pcs resource create hana_nfs_s1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs_s1_active
    pcs resource create hana_nfs_s2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs_s2_active
    # Clone the attribure resources
    pcs resource clone hana_nfs_s1_active meta clone-node-max=1 interleave=true
    pcs resource clone hana_nfs_s2_active meta clone-node-max=1 interleave=true
    # Configure the constraints, which will set the attribute values
    pcs constraint order fs_hana_shared_s1-clone then hana_nfs_s1_active-clone
    pcs constraint order fs_hana_shared_s2-clone then hana_nfs_s2_active-clone
    ```

   > [!TIP]
   > Ha a konfiguráció más fájlrendszereket is tartalmaz, a/-t is beleértve, amelyek az NFS-hez `hana/shared` vannak csatlakoztatva, akkor belefoglalják a `sequential=false` lehetőséget, hogy a fájlrendszerek között ne legyenek rendezési függőségek. Az összes NFS-hez csatlakoztatott fájlrendszernek a megfelelő attribútum-erőforrás előtt el kell indulnia, de nem kell egymáshoz viszonyítva megadnia azokat. További információ: [Hogyan SAP HANA Scale-Out HSR beállítása pacemaker-fürtben, ha a HANA-fájlrendszer NFS-megosztás](https://access.redhat.com/solutions/5423971).  

8. **[1]** a pacemaker karbantartási módba helyezése a HANA-fürt erőforrásainak létrehozásához.  
    ```bash
    pcs property set maintenance-mode=true
    ```

## <a name="create-sap-hana-cluster-resources"></a>SAP HANA-fürt erőforrásainak létrehozása

1. **[A]** telepítse a HANA kibővíthető erőforrás-ügynököt az összes fürtcsomóponton, beleértve a többségi gyártót is.    

    ```bash
    yum install -y resource-agents-sap-hana-scaleout 
    ```


   > [!NOTE]
   > A [RHEL-fürtökkel kapcsolatos támogatási szabályzatok – SAP HANA kezelése a fürtben](https://access.redhat.com/articles/3397471) az `resource-agents-sap-hana-scaleout` operációs rendszer kiadásához szükséges csomag támogatott verziójának kezelésére.  

2. **[1, 2]** a HANA "rendszerreplikálási Hook" telepítése. A hookot egy HANA DB-csomópontra kell telepíteni az egyes rendszer-replikációs helyeken. A SAP HANAnak továbbra is le kell állnia.        

   1. A Hook előkészítése `root` 
    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR-ScaleOut/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. Módosíthatja `global.ini`
    ```bash
    # add to global.ini
    [ha_dr_provider_SAPHanaSR]
    provider = SAPHanaSR
    path = /hana/shared/myHooks
    execution_order = 1
    
    [trace]
    ha_dr_saphanasr = info
    ```

3. **[Ah]** A fürtön a <SID adm-hez tartozó fürtcsomópontok esetében a sudoers konfigurálása szükséges \> . Ebben a példában egy új fájl létrehozásával érhető el. Hajtsa végre a parancsokat a következőképpen: `root` .    
    ```bash
    cat << EOF > /etc/sudoers.d/20-saphana
    # SAPHanaSR-ScaleOut needs for srHook
     Cmnd_Alias SOK = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SOK -t crm_config -s SAPHanaSR
     Cmnd_Alias SFAIL = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SFAIL -t crm_config -s SAPHanaSR
     hn1adm ALL=(ALL) NOPASSWD: SOK, SFAIL
     EOF
    ```

4. **[1, 2]** a Start SAP HANA mindkét replikációs helyen. Végrehajtás <SID adm-ként \> .  

    ```bash
    sapcontrol -nr 03 -function StartSystem 
    ```

5. **[1]** ellenőrizze a Hook telepítését. Végrehajtás <SID adm-ként \> az aktív HANA rendszer-replikációs helyen.   

    ```bash
    cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*

     # Example entries
     # 2020-07-21 22:04:32.364379 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:46.905661 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:52.092016 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:52.782774 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:53.117492 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:06:35.599324 ha_dr_SAPHanaSR SOK
    ```

6. **[1]** hozza létre a HANA-fürt erőforrásait. Hajtsa végre a következő parancsokat `root` .    
   1. Győződjön meg arról, hogy a fürt már karbantartási módban van.  
    
   2. Ezután hozza létre a HANA-topológia erőforrását.  
      Ha RHEL **7. x** fürtöt épít ki, használja a következő parancsokat:  
      ```bash
      pcs resource create SAPHanaTopology_HN1_HDB03 SAPHanaTopologyScaleOut \
       SID=HN1 InstanceNumber=03 \
       op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600

      pcs resource clone SAPHanaTopology_HN1_HDB03 meta clone-node-max=1 interleave=true
      ```

      Ha RHEL **8. x** fürtöt épít ki, használja a következő parancsokat:  
      ```bash
      pcs resource create SAPHanaTopology_HN1_HDB03 SAPHanaTopology \
       SID=HN1 InstanceNumber=03 meta clone-node-max=1 interleave=true \
       op methods interval=0s timeout=5 \
       op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600

      pcs resource clone SAPHanaTopology_HN1_HDB03 meta clone-node-max=1 interleave=true
      ```

   3. Ezután hozza létre a HANA-példány erőforrását.  
      > [!NOTE]
      > Ez a cikk a *Slave* kifejezésre mutató hivatkozásokat tartalmaz, amelyek egy kifejezés, amelyet a Microsoft már nem használ. Ha a rendszer eltávolítja a kifejezést a szoftverből, azt a cikkből távolítjuk el.  
 
      Ha RHEL **7. x** fürtöt épít ki, használja a következő parancsokat:    
      ```bash
      pcs resource create SAPHana_HN1_HDB03 SAPHanaController \
       SID=HN1 InstanceNumber=03 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
       op start interval=0 timeout=3600 op stop interval=0 timeout=3600 op promote interval=0 timeout=3600 \
       op monitor interval=60 role="Master" timeout=700 op monitor interval=61 role="Slave" timeout=700
     
      pcs resource master msl_SAPHana_HN1_HDB03 SAPHana_HN1_HDB03 \
       meta master-max="1" clone-node-max=1 interleave=true
      ```

      Ha RHEL **8. x** fürtöt épít ki, használja a következő parancsokat:  
      ```bash
      pcs resource create SAPHana_HN1_HDB03 SAPHanaController \
       SID=HN1 InstanceNumber=03 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
       op demote interval=0s timeout=320 op methods interval=0s timeout=5 \
       op start interval=0 timeout=3600 op stop interval=0 timeout=3600 op promote interval=0 timeout=3600 \
       op monitor interval=60 role="Master" timeout=700 op monitor interval=61 role="Slave" timeout=700
     
      pcs resource promotable SAPHana_HN1_HDB03 \
       meta master-max="1" clone-node-max=1 interleave=true
      ```
      > [!IMPORTANT]
      > Ajánlott eljárásként Azt javasoljuk, hogy csak a **nem** értékre állítsa a AUTOMATED_REGISTERt, miközben alapos feladatátvételi teszteket végez, hogy megakadályozza a sikertelen elsődleges példányok másodlagosként való automatikus regisztrálását. Miután a feladatátvételi tesztek sikeresen befejeződtek, állítsa AUTOMATED_REGISTER **Igen** értékre, hogy az áttelepítési rendszer replikációja automatikusan folytatódjon. 

   4. Hozzon létre virtuális IP-címet és kapcsolódó erőforrásokat.  
      ```bash
      pcs resource create vip_HN1_03 ocf:heartbeat:IPaddr2 ip=10.23.0.18 op monitor interval="10s" timeout="20s"
      sudo pcs resource create nc_HN1_03 azure-lb port=62503
      sudo pcs resource group add g_ip_HN1_03 nc_HN1_03 vip_HN1_03
      ```

   5. A fürt korlátozásainak létrehozása  
      Ha RHEL **7. x** fürtöt épít ki, használja a következő parancsokat:  
      ```bash
      #Start HANA topology, before the HANA instance
      pcs constraint order SAPHanaTopology_HN1_HDB03-clone then msl_SAPHana_HN1_HDB03

      pcs constraint colocation add g_ip_HN1_03 with master msl_SAPHana_HN1_HDB03 4000
      #HANA resources are only allowed to run on a node, if the node's NFS file systems are mounted. The constraint also avoids the majority maker node
      pcs constraint location SAPHanaTopology_HN1_HDB03-clone rule resource-discovery=never score=-INFINITY hana_nfs_s1_active ne true and hana_nfs_s2_active ne true
      ```
 
      Ha RHEL **8. x** fürtöt épít ki, használja a következő parancsokat:  
      ```bash
      #Start HANA topology, before the HANA instance
      pcs constraint order SAPHanaTopology_HN1_HDB03-clone then SAPHana_HN1_HDB03-clone

      pcs constraint colocation add g_ip_HN1_03 with master SAPHana_HN1_HDB03-clone 4000
      #HANA resources are only allowed to run on a node, if the node's NFS file systems are mounted. The constraint also avoids the majority maker node
      pcs constraint location SAPHanaTopology_HN1_HDB03-clone rule resource-discovery=never score=-INFINITY hana_nfs_s1_active ne true and hana_nfs_s2_active ne true
      ```

7. **[1]** helyezze el a fürtöt karbantartási módból. Győződjön meg arról, hogy a fürt állapota ok, és hogy az összes erőforrás el van indítva.  
    ```bash
    sudo pcs property set maintenance-mode=false
    #If there are failed cluster resources, you may need to run the next command
    pcs resource cleanup
    ```
  
   > [!NOTE]
   > A fenti konfiguráció időtúllépései csak példák, és előfordulhat, hogy az adott HANA-beállításhoz kell igazítani. Előfordulhat például, hogy a kezdési időkorlátot meg kell emelni, ha a SAP HANA-adatbázis elindításához tovább tart.
  
## <a name="test-sap-hana-failover"></a>Feladatátvételi SAP HANA tesztelése 

1. Mielőtt elkezdené a tesztet, ellenőrizze a fürtöt, és SAP HANA a rendszer replikálási állapotát.  

   a. Ellenőrizze, hogy nincsenek-e sikertelen műveletek a fürtben  
     ```bash
     #Verify that there are no failed cluster actions
     pcs status
     # Example
     #Stack: corosync
     #Current DC: hana-s-mm (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
     #Last updated: Thu Sep 24 06:00:20 2020
     #Last change: Thu Sep 24 05:59:17 2020 by root via crm_attribute on hana-s1-db1
     #
     #7 nodes configured
     #45 resources configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Active resources:
     #
     #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
     #Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
     #    Started: [ hana--s1-db1 hana-s1-db2 hana-s1-db3 ]
     #Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
     #    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
     #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
     #Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
     #    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
     #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
     #    Masters: [ hana-s1-db1 ]
     #    Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Resource Group: g_ip_HN1_03
     #    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s1-db1
     #    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
     ```

   b. Annak ellenőrzése, hogy SAP HANA rendszer-replikáció szinkronban van-e

      ```bash
      # Verify HANA HSR is in sync
      sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
      #| Database | Host        | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary     | Secondary| Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
      #|          |             |       |              |           |         |           | Host          | Port     | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
      #| -------- | ----------- | ----- | ------------ | --------- | ------- | --------- | ------------- | -------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
      #| HN1      | hana-s1-db3 | 30303 | indexserver  |         5 |       2 | HANA_S1   | hana-s2-db3 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db2 | 30303 | indexserver  |         4 |       2 | HANA_S1   | hana-s2-db2 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |  
      #| SYSTEMDB | hana-s1-db1 | 30301 | nameserver   |         1 |       2 | HANA_S1   | hana-s2-db1 |     30301  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db1 | 30307 | xsengine     |         2 |       2 | HANA_S1   | hana-s2-db1 |     30307  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db1 | 30303 | indexserver  |         3 |       2 | HANA_S1   | hana-s2-db1 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |

      #status system replication site "1": ACTIVE
      #overall system replication status: ACTIVE

      #Local System Replication State
      #~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

      #mode: PRIMARY
      #site id: 1
      #site name: HANA_S1
      ```

2. Ellenőrizze, hogy a fürt konfigurációja meghiúsul-e, amikor egy csomópont elveszti az NFS-megosztáshoz () való hozzáférést `/hana/shared` .  

   A SAP HANA erőforrás-ügynökök a `/hana/shared` feladatátvétel során a művelet végrehajtásához tárolt bináris fájloktól függenek. A fájlrendszer az `/hana/shared` NFS-en keresztül van csatlakoztatva a bemutatott konfigurációban. A végrehajtható teszt a fájlrendszer újracsatlakoztatása `/hana/shared` *írásvédettként*. Ez a megközelítés ellenőrzi, hogy a fürt átadja-e a feladatátvételt, ha `/hana/shared` az aktív rendszerreplikálási helyhez való hozzáférés elvész.  

   **Várt eredmény**: Ha újracsatlakoztatja `/hana/shared` a *csak olvasási* művelettel, a fájlrendszerre vonatkozó olvasási/írási műveletet végző figyelési művelet sikertelen lesz, mivel nem tud írni a fájlrendszerbe, és a HANA-erőforrás feladatátvételét fogja elindítani. Ugyanez az eredmény várható, ha a HANA-csomópont elveszti az NFS-megosztás elérését.  
     
   A fürt erőforrásainak állapotát a vagy a futtatásával is megtekintheti `crm_mon` `pcs status` . Erőforrás állapota a teszt elindítása előtt:
      ```bash
      # Output of crm_mon
      #7 nodes configured
      #45 resources configured

      #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      #
      #Active resources:

      #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
      # Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
      #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
      #     Masters: [ hana-s1-db1 ]
      #     Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Resource Group: g_ip_HN1_03
      #     nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s1-db1
      #     vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
      ```

   Ha szimulálni szeretné az `/hana/shared` egyik elsődleges replikációs hely virtuális gépe hibáját, hajtsa végre a következő parancsot:
      ```bash
      # Execute as root 
      mount -o ro /hana/shared
      # Or if the above command returns an error
      sudo mount -o ro 10.23.1.7/HN1-shared-s1 /hana/shared
      ```
  
   A HANA virtuális gép, amely a fürt konfigurációjától függően megszakadt, `/hana/shared` újraindítást vagy leállást érhet el. A fürt erőforrásai áttelepülnek a másik HANA rendszerbeli replikációs helyre.  
         
   Ha a fürt nem indult el a virtuális gépen, az újraindult, indítsa el a fürtöt a következő végrehajtásával: 

      ```bash
      # Start the cluster 
      pcs cluster start
      ```
   
   A fürt indításakor a fájlrendszer `/hana/shared` automatikusan csatlakoztatva lesz.     
   Ha a AUTOMATED_REGISTER = "false" értéket állítja be, akkor konfigurálnia kell SAP HANA rendszer-replikálást a másodlagos helyen. Ebben az esetben végrehajthatja ezeket a parancsokat a SAP HANA másodlagosként való újrakonfigurálásához.   

      ```bash
      # Execute on the secondary 
      su - hn1adm
      # Make sure HANA is not running on the secondary site. If it is started, stop HANA
      sapcontrol -nr 03 -function StopWait 600 10
      # Register the HANA secondary site
      hdbnsutil -sr_register --name=HANA_S1 --remoteHost=hana-s2-db1 --remoteInstance=03 --replicationMode=sync
      # Switch back to root and cleanup failed resources
      pcs resource cleanup SAPHana_HN1_HDB03
      ```

   Az erőforrások állapota a teszt után: 

      ```bash
      # Output of crm_mon
      #7 nodes configured
      #45 resources configured
    
      #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      
      #Active resources:
    
      #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
      # Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
      #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
      #     Masters: [ hana-s2-db1 ]
      #     Slaves: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db2 hana-s2-db3 ]
      # Resource Group: g_ip_HN1_03
      #     nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s2-db1
      #     vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s2-db1
      ```


Javasoljuk, hogy alaposan tesztelje a SAP HANA-fürtöt úgy, hogy a teszteket is végrehajtja, ha az Azure-beli [virtuális gépeken a RHEL](./sap-hana-high-availability-rhel.md#test-the-cluster-setup)-on SAP HANA.


## <a name="next-steps"></a>Következő lépések

* [Azure Virtual Machines az SAP tervezéséhez és megvalósításához][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP-ban][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hoz][dbms-guide]
* [NFS 4.1-es verziójú kötetek az SAP HANA-hoz készült Azure NetApp Filesban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-netapp)
* A magas rendelkezésre állás és a SAP HANA Azure-beli virtuális gépeken történő vész-helyreállítási tervének megismeréséhez tekintse meg a [SAP HANA magas rendelkezésre állását az azure Virtual Machinesban (VM)][sap-hana-ha].
---
title: SAP Solutions-szolgáltatók Azure Monitor | Microsoft Docs
description: Ez a cikk a SAP Solutions-szolgáltatók Azure monitor szolgáltatásával kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: 1282d1916d669f1026707e15cc8d5437d885087f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101668991"
---
# <a name="azure-monitor-for-sap-solutions-providers-preview"></a>Azure monitor SAP Solutions-szolgáltatók számára (előzetes verzió)

## <a name="overview"></a>Áttekintés  

Az SAP-megoldások Azure Monitor kontextusában a *szolgáltató típusa* egy adott *szolgáltatóra* vonatkozik. Például *SAP HANA*, amely az SAP-környezet egy adott összetevőjéhez van konfigurálva, például SAP HANA adatbázishoz. A szolgáltató a megfelelő összetevőhöz tartozó kapcsolódási adatokat tartalmazza, és segít az adott összetevőből származó telemetria-adatok gyűjtésében. Az SAP-megoldások erőforrásának (más néven SAP-figyelő erőforrás) egy Azure Monitor több szolgáltatóval is konfigurálható ugyanazzal a szolgáltatói típussal vagy több szolgáltatói típussal.
   
Az ügyfelek különböző szolgáltatói típusokat állíthatnak be, amelyek lehetővé teszik az adatok gyűjtését az SAP-környezet megfelelő összetevőjéről. Az ügyfelek például konfigurálhatják SAP HANA szolgáltatói típust, egy másik szolgáltatót a magas rendelkezésre állású fürt szolgáltatói típusához és így tovább.  

Az ügyfelek úgy is dönthetnek, hogy egy adott szolgáltató típusának több szolgáltatóját is konfigurálják, hogy ugyanazt az SAP-figyelő erőforrást és a társított felügyelt csoportot tudják használni. Tudjon meg többet a felügyelt erőforráscsoporthoz. Nyilvános előzetes verzió esetén a következő szolgáltatói típusok támogatottak:   
- SAP HANA
- Magas rendelkezésre állású fürt
- Microsoft SQL Server

![SAP Solutions-szolgáltatók Azure Monitor](./media/azure-monitor-sap/azure-monitor-providers.png)

Javasoljuk, hogy az SAP-figyelő erőforrás üzembe helyezésének időpontjában legalább egy szolgáltatót konfigurálja az elérhető szolgáltatók típusai közül. A szolgáltató konfigurálásával az ügyfelek a megfelelő összetevőből kezdeményezik az adatgyűjtést, amelyhez a szolgáltató konfigurálva van.   

Ha az ügyfelek nem állítanak be szolgáltatót az SAP-figyelő erőforrásának telepítésekor, bár az SAP-figyelő erőforrása sikeresen települ, a rendszer nem gyűjt telemetria-adatokat. Az ügyfeleknek lehetőségük van a szolgáltatók hozzáadására az SAP figyelő erőforráson keresztül történő üzembe helyezés után Azure Portalon belül. Az ügyfelek bármikor hozzáadhatnak vagy törölhetnek szolgáltatókat az SAP-figyelő erőforrásból.

> [!Tip]
> Ha azt szeretné, hogy a Microsoft egy adott szolgáltatót valósít meg, kérjük, küldjön visszajelzést a dokumentum végén található hivatkozáson, vagy lépjen kapcsolatba a fiók csapatával.  

## <a name="provider-type-sap-hana"></a>Szolgáltató típusa SAP HANA

Az ügyfelek egy vagy több szolgáltatót *SAP HANA* használhatnak az adatgyűjtés SAP HANA adatbázisból való engedélyezéséhez. Az SAP HANA-szolgáltató az SQL-porton keresztül csatlakozik a SAP HANA adatbázishoz, lekéri a telemetria adatait az adatbázisból, és leküldi az ügyfél-előfizetés Log Analytics munkaterületére. A SAP HANA szolgáltató 1 percenként gyűjt adatokat az SAP HANA-adatbázisból.  

A nyilvános előzetes verzióban az ügyfelek a következő adatokat számíthatják SAP HANA szolgáltatóval: a mögöttes infrastruktúra kihasználtsága, a SAP HANA gazdagép állapota, SAP HANA a rendszer replikálása és SAP HANA biztonsági másolat telemetria adatai. SAP HANA szolgáltató konfigurálásához a gazdagép IP-címét, a HANA SQL-portszámot, valamint a SYSTEMDB felhasználónevét és jelszavát kötelező megadni. Javasoljuk, hogy a SAP HANA szolgáltatót a SYSTEMDB-ra konfigurálja, azonban több szolgáltató is konfigurálható más adatbázis-bérlők között.

![Azure Monitor SAP Solutions-szolgáltatók számára – SAP HANA](./media/azure-monitor-sap/azure-monitor-providers-hana.png)

## <a name="provider-type-high-availability-cluster"></a>Szolgáltató típusa magas rendelkezésre állású fürt
Az ügyfelek egy vagy több szolgáltatót is beállíthat *magas rendelkezésre állású fürtre* , hogy az adatgyűjtést az SAP-környezeten belül engedélyezzék a pacemaker-fürtről. A magas rendelkezésre állású fürt szolgáltatója a Pacemakerhez csatlakozik, [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) végpont használatával lekéri a telemetria adatait az adatbázisból, és leküldi az ügyfél-előfizetés log Analytics munkaterületére. A magas rendelkezésre állású fürt szolgáltatója 60 másodpercenként gyűjt adatokat a pacemakertől.  

A nyilvános előzetes verzióban az ügyfelek a következő, magas rendelkezésre állású fürttel rendelkező adatokra számíthatnak:   
 - A fürt állapota csomópont és erőforrás állapotának összesítése 
 - [mások](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md) 

![Azure Monitor SAP Solutions-szolgáltatók számára – magas rendelkezésre állású fürt](./media/azure-monitor-sap/azure-monitor-providers-pacemaker-cluster.png)

Magas rendelkezésre állású fürt konfigurálásához két elsődleges lépésre van szó:

1. Telepítse a [ha_cluster_exportert](https://github.com/ClusterLabs/ha_cluster_exporter) a pacemaker fürt *minden* csomópontján.

   A ha_cluster_exporter telepítéséhez két lehetősége van:
   
   - Magas rendelkezésre állású fürt üzembe helyezéséhez használjon Azure Automation szkripteket. A parancsfájlok a [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) minden fürtcsomóponton telepítik.  
   - Manuálisan végezze el a [telepítést](https://github.com/ClusterLabs/ha_cluster_exporter#manual-clone--build). 

2. Magas rendelkezésre állású fürtszolgáltatás konfigurálása a pacemaker fürt *minden* csomópontja számára.

   A magas rendelkezésre állású fürt szolgáltatójának konfigurálásához a következő információk szükségesek:
   
   - **Név**. A szolgáltató neve. Ennek a Azure Monitornek egyedinek kell lennie az SAP Solutions-példány esetében.
   - **Prometheus-végpont**. http \: // \<servername or ip address\> : 9664/mérőszámok.
   - **SID**. SAP-rendszerek esetén használja az SAP SID-t. Más rendszerekhez (például NFS-fürtökhöz) használjon egy három karakterből álló nevet a fürt számára. A biztonsági azonosítónak a figyelt többi fürttől eltérőnek kell lennie.   
   - **Fürt neve**. A fürt létrehozásakor használt fürt neve. A fürt neve megtalálható a cluster tulajdonságban `cluster-name` .
   - **Állomásnév**. A virtuális gép linuxos állomásneve.  


## <a name="provider-type-os-linux"></a>Szolgáltató típusa (Linux)
Az ügyfelek az operációs rendszer (Linux) egy vagy több szolgáltatóját konfigurálhatják az BareMetal vagy a virtuálisgép-csomópontból történő adatgyűjtés engedélyezéséhez. Az operációs rendszer (Linux) szolgáltatója BareMetal vagy virtuálisgép-csomópontokhoz csatlakozik [Node_Exporter](https://github.com/prometheus/node_exporter)végpont használatával lekéri   a telemetria adatait a csomópontokról, és leküldi az ügyfél-előfizetés log Analytics munkaterületére. Az operációs rendszer (Linux) szolgáltatója 60 másodpercenként gyűjt adatokat a csomópontok többsége számára. 

A nyilvános előzetes verzióban az ügyfelek a következő adatokra számíthatnak a (z) operációs rendszer (Linux) szolgáltatójának használatával: 
   - CPU-használat, CPU-használat folyamat szerint 
   - Lemez kihasználtsága, I/O-olvasási & írás 
   - Memória eloszlása, memóriahasználat, memória használatának cseréje 
   - Hálózati használat, hálózati bejövő & kimenő forgalom részletei. 

Egy operációs rendszer (Linux) szolgáltatójának konfigurálásához két elsődleges lépésre van szó:
1. Telepítse a [Node_Exportert](https://github.com/prometheus/node_exporter)   az egyes BareMetal vagy virtuálisgép-csomópontokon.
   A [Node_exporter](https://github.com/prometheus/node_exporter)telepítéséhez két lehetősége van: 
      - A Ansible-mel való automatizáláshoz az operációs rendszer (Linux) szolgáltatójának telepítéséhez minden BareMetal vagy virtuálisgép-csomóponton [Node_Exportert](https://github.com/prometheus/node_exporter) kell használni.  
      - Manuálisan végezze el a [telepítést](https://prometheus.io/docs/guides/node-exporter/).

2. Konfiguráljon egy operációsrendszer-(Linux-) szolgáltatót a környezet minden egyes BareMetal vagy virtuálisgép-csomópont-példányához. 
   Az operációs rendszer (Linux) szolgáltatójának konfigurálásához a következő információk szükségesek: 
      - név. A szolgáltató neve. Ennek a Azure Monitornek egyedinek kell lennie az SAP Solutions-példány esetében. 
      - Csomópont-exportőr végpontja. Általában http:// <servername or ip address> : 9100/mérőszámok 

> [!NOTE]
> a 9100 egy Node_Exporter végpont számára elérhető port.

> [!Warning]
> Győződjön meg arról, hogy a csomópont-exportőr fut a csomópont újraindítása után. 


## <a name="provider-type-microsoft-sql-server"></a>Szolgáltató típusa Microsoft SQL Server

Az ügyfelek egy vagy több szolgáltatói típust konfigurálhatják *Microsoft SQL Server* az adatgyűjtés [SQL Server on Virtual Machinesból](https://azure.microsoft.com/services/virtual-machines/sql-server/)való engedélyezéséhez. SQL Server-szolgáltató az SQL-porton keresztül csatlakozik az Microsoft SQL Serverhoz, lekéri a telemetria adatait az adatbázisból, és leküldi az ügyfél-előfizetés Log Analytics munkaterületére. A SQL Server az SQL-hitelesítéshez és egy SQL Server bejelentkezési azonosítóhoz kell konfigurálni, és az SAP-adatbázisnak kell létrehoznia a szolgáltató alapértelmezett adatbázisát. SQL Server-szolgáltató az SQL Server minden órájában 60 másodpercenként gyűjt adatokat.  

A nyilvános előzetes verzióban az ügyfelek a következő adatokat számíthatják SQL Server szolgáltatóval: a mögöttes infrastruktúra kihasználtsága, a legnépszerűbb SQL-utasítások, a legnagyobb táblázat, a SQL Server a hibák naplójában rögzített problémák, a blokkoló folyamatok és mások.  

Microsoft SQL Server szolgáltató konfigurálásához az SAP rendszer-azonosító, a gazdagép IP-címe, SQL Server portszám és a SQL Server bejelentkezési név és jelszó szükséges.

![Azure Monitor SAP Solutions-szolgáltatók számára – SQL](./media/azure-monitor-sap/azure-monitor-providers-sql.png)

## <a name="next-steps"></a>Következő lépések

- Hozza létre az első Azure Monitor az SAP Solutions-erőforráshoz.
- Kérdése van az SAP-megoldások Azure Monitorával kapcsolatban? Olvassa el a [Gyakori kérdések](./azure-monitor-faq.md) szakaszt.

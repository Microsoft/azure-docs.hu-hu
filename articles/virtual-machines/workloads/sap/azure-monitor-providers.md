---
title: Azure Monitor for SAP Solutions szolgáltatók| Microsoft Docs
description: Ez a cikk az SAP-megoldásszolgáltatókhoz elérhető Azure Monitorral kapcsolatos gyakori kérdésekre ad választ.
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: 54ce9ca0ddffe074f5a343d192b4599b3449a855
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538740"
---
# <a name="azure-monitor-for-sap-solutions-providers-preview"></a>Azure Monitor SAP-megoldásszolgáltatókhoz (előzetes verzió)

## <a name="overview"></a>Áttekintés  

A szolgáltatótípus a Azure Monitor for SAP Solutions *adott* szolgáltatóra *vonatkozik.* Például a *SAP HANA,* amely egy adott összetevőre van konfigurálva az SAP-környezetben, például egy SAP HANA adatbázishoz. A szolgáltató tartalmazza a megfelelő összetevő kapcsolati adatait, és segít az adott összetevőből származó telemetriai adatok gyűjtésében. Egy Azure Monitor for SAP Solutions erőforrás (más néven SAP Monitor-erőforrás) több azonos szolgáltatótípusú szolgáltatóval vagy több szolgáltatótípussal is konfigurálható.
   
Az ügyfelek dönthetnek úgy, hogy különböző szolgáltatótípusokat konfigurálnak az SAP-környezet megfelelő összetevőiből való adatgyűjtés engedélyezéséhez. Az ügyfelek például konfigurálhatnak egy szolgáltatót SAP HANA szolgáltatótípushoz, egy másikat a magas rendelkezésre állású fürtszolgáltató típushoz stb.  

Az ügyfelek úgy is dönthetnek, hogy több szolgáltatót is konfigurálnak egy adott szolgáltatótípushoz, hogy ugyanazt az SAP Monitor-erőforrást és a társított felügyelt csoportot újra felhasználják. További információ a felügyelt erőforráscsoportról. A nyilvános előzetes verzió a következő szolgáltatótípusokat támogatja:   
- SAP NetWeaver
- SAP HANA
- Microsoft SQL Server
- Magas rendelkezésre állású fürt
- Operációs rendszer

![Azure Monitor SAP-megoldásszolgáltatókhoz](./media/azure-monitor-sap/azure-monitor-providers.png)

Az SAP Monitor-erőforrás üzembe helyezésekor ajánlott legalább egy szolgáltatót konfigurálni az elérhető szolgáltatótípusokból. Egy szolgáltató konfigurálásával az ügyfelek az adatgyűjtést annak a megfelelő összetevőnek a segítségével kezdeményezik, amelyhez a szolgáltató konfigurálva van.   

Ha az ügyfelek nem konfigurálnak szolgáltatókat az SAP Monitor-erőforrás üzembe helyezésekor, bár az SAP Monitor-erőforrás üzembe helyezése sikeres lesz, a rendszer nem gyűjt telemetriai adatokat. Az ügyfelek az SAP Monitor-erőforráson keresztüli üzembe helyezés után hozzáadhatnak szolgáltatókat a Azure Portal. Az ügyfelek bármikor hozzáadhatnak vagy törölhet szolgáltatókat az SAP Monitor-erőforrásból.

## <a name="provider-type-sap-netweaver"></a>Szolgáltató típusa: SAP NetWeaver

Az ügyfelek egy vagy több SZOLGÁLTATÓ típusú SAP NetWeaver szolgáltatót konfigurálnak az SAP NetWeaver rétegből való adatgyűjtés engedélyezéséhez. Az AMS NetWeaver szolgáltató a meglévő [SAPControl webszolgáltatási](https://www.sap.com/documents/2016/09/0a40e60d-8b7c-0010-82c7-eda71af511fa.html) felületet használja a megfelelő telemetriai adatok lekérésére.

A jelenlegi kiadásban az alábbiakban az AMS által meghívott szabványos, alapértelmezés szerint használt SOAP-alapú webes metódusokat olvashatja.

![image1](https://user-images.githubusercontent.com/75772258/114600036-820d8280-9cb1-11eb-9f25-d886ab1d5414.png)

A nyilvános előzetes verzióban az ügyfelek a következő adatokat láthatják az SAP NetWeaver szolgáltatónál: 
- A rendszer és a példány rendelkezésre állása
- A munkafolyamat kihasználtsága
- Üzenetsor kihasználtsága
- Sorra hozási zárolási statisztikák.

![image](https://user-images.githubusercontent.com/75772258/114581825-a9f2eb00-9c9d-11eb-8e6f-79cee7c5093f.png)

## <a name="provider-type-sap-hana"></a>Szolgáltató típusa: SAP HANA

Az ügyfelek egy vagy több szolgáltatótípus-szolgáltatót *konfigurálnak, SAP HANA* lehetővé teszik az adatok gyűjtését SAP HANA adatbázisból. A SAP HANA-szolgáltató SQL-porton keresztül csatlakozik az SAP HANA-adatbázishoz, lekért telemetriai adatokat az adatbázisból, majd lekullja őket az ügyfél-előfizetés Log Analytics-munkaterületére. A SAP HANA szolgáltató 1 percenként gyűjt adatokat a SAP HANA adatbázisból.  

A nyilvános előzetes verzióban az ügyfelek a következő adatokat láthatják a SAP HANA-szolgáltatóval: Mögöttes infrastruktúra-kihasználtság, SAP HANA Gazdagép állapota, SAP HANA rendszerreplikáció és SAP HANA Backup telemetriai adatok. A szolgáltató SAP HANA, gazdagép IP-címe, HANA SQL-portszám és SYSTEMDB-felhasználónév és -jelszó megadása szükséges. Javasoljuk az ügyfeleknek, hogy konfigurálják SAP HANA szolgáltatót a SYSTEMDB-hez, de több szolgáltató konfigurálható más adatbázis-bérlőkhöz is.

![Azure Monitor SAP-megoldások szolgáltatói számára – SAP HANA](./media/azure-monitor-sap/azure-monitor-providers-hana.png)

## <a name="provider-type-microsoft-sql-server"></a>Szolgáltató típusa: Microsoft SQL Server

Az ügyfelek konfigurálhat egy vagy több szolgáltatótípus-szolgáltatót, *Microsoft SQL Server* az adatgyűjtést a [SQL Server on Virtual Machines.](https://azure.microsoft.com/services/virtual-machines/sql-server/) SQL Server szolgáltató az SQL Microsoft SQL Server porton keresztül csatlakozik az Microsoft SQL Server-hez, lekért telemetriai adatokat az adatbázisból, és lekullja őket az ügyfél-előfizetés Log Analytics-munkaterületére. A SQL Server konfigurálni kell az SQL-hitelesítéshez, és létre kell SQL Server egy bejelentkezési azonosítót, a szolgáltató alapértelmezett adatbázisaként pedig az SAP-adatbázist kell létrehozni. SQL Server szolgáltató 60 másodpercenként és óránként gyűjt adatokat az SQL Serverről.  

A nyilvános előzetes verzióban az ügyfelek a következő adatokat láthatják az SQL Server-szolgáltatóval: mögöttes infrastruktúra-kihasználtság, legfelső szintű SQL-utasítások, legnagyobb tábla, az SQL Server-hibanaplókban rögzített problémák, blokkolási folyamatok és egyebek.  

A szolgáltató Microsoft SQL Server meg kell adni az SAP-rendszerazonosítót, a gazdagép IP-címét, SQL Server portszámát és SQL Server bejelentkezési nevét és jelszavát.

![Azure Monitor SAP-megoldások szolgáltatói számára – SQL](./media/azure-monitor-sap/azure-monitor-providers-sql.png)

## <a name="provider-type-high-availability-cluster"></a>Szolgáltató típusa: Magas rendelkezésre állású fürt
Az ügyfelek konfigurálhat egy vagy  több szolgáltatótípusú magas rendelkezésre állású fürtöt, hogy az SAP-környezetben engedélyezze az adatgyűjtést a Pacemaker-fürtből. A magas rendelkezésre állású fürtszolgáltató egy ha_cluster_exporter-végpont használatával csatlakozik [a](https://github.com/ClusterLabs/ha_cluster_exporter) Pacemakerhez, lekért telemetriai adatokat az adatbázisból, és lekullja őket az ügyfél-előfizetés Log Analytics-munkaterületére. A magas rendelkezésre állású fürtszolgáltató 60 másodpercenként gyűjt adatokat a Pacemakertől.  

A nyilvános előzetes verzióban az ügyfelek a következő adatokat láthatják a magas rendelkezésre állású fürtszolgáltatóval:   
 - A fürt állapota a csomópont és az erőforrás állapotának összesítéseként ábrázolva 
 - [Mások](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md) 

![Azure Monitor SAP-megoldásszolgáltatók számára – Magas rendelkezésre állású fürt](./media/azure-monitor-sap/azure-monitor-providers-pacemaker-cluster.png)

Magas rendelkezésre állású fürtszolgáltató konfigurálásán két elsődleges lépés vesz részt:

1. Telepítse [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) a *Pacemaker-fürt* minden csomópontjára.

   A két lehetőség a következő két ha_cluster_exporter:
   
   - Magas Azure Automation fürt üzembe helyezéséhez használjon Azure Automation szkripteket. A szkriptek [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) fürtcsomópontra telepítik a fürtöt.  
   - Manuális [telepítést kell.](https://github.com/ClusterLabs/ha_cluster_exporter#manual-clone--build) 

2. Magas rendelkezésre állású fürtszolgáltató  konfigurálása a Pacemaker-fürt minden csomópontjához.

   A magas rendelkezésre állású fürtszolgáltató konfigurálásához a következő információkra van szükség:
   
   - **Név**. A szolgáltató neve. Ennek egyedinek kell lennie ehhez a Azure Monitor SAP-megoldáspéldányhoz.
   - **Prometheus-végpont.** http \: // \<servername or ip address\> :9664/metrics.
   - **SID (Biztonsági azonosító)**. SAP-rendszerekhez használja az SAP SID-t. Más rendszerek (például NFS-fürtök) esetében használjon egy három karakterbőlből következő nevet a fürtnek. A biztonsági azonosítónak különbözik a többi figyelt fürttől.   
   - **Fürt neve.** A fürt létrehozásakor használt fürtnév. A fürt neve a fürttulajdonságban `cluster-name` található.
   - **Gazdagépnév.** A virtuális gép Linux-gazdaneve.  

## <a name="provider-type-os-linux"></a>Szolgáltató típusa: Operációs rendszer (Linux)
Az ügyfelek konfigurálhat egy vagy több szolgáltatótípusú operációs rendszert (Linux) az BareMetal vagy a VM-csomópontról való adatgyűjtés engedélyezéséhez. Az operációs rendszer (Linux) szolgáltatója az Node_Exporter-végpont használatával csatlakozik az BareMetal- vagy [virtuálisgép-csomópontokhoz,](https://github.com/prometheus/node_exporter)lekért telemetriai adatokat a csomópontról, és lekultálja az ügyfél-előfizetés   Log Analytics-munkaterületére. Az operációs rendszer (Linux) szolgáltatója 60 másodpercenként gyűjt adatokat a csomópontok legtöbb metrikára. 

A nyilvános előzetes verzióban az ügyfelek a következő adatokat láthatják az operációs rendszer (Linux) szolgáltatójával: 
   - CPU-használat, cpu-használat folyamat szerint 
   - Lemezkihasználtság, írási I/O-& írás 
   - Memóriaelosztás, memóriahasználat, felcserélési memóriahasználat 
   - Hálózathasználat, bejövő hálózati forgalom & kimenő forgalom részletei. 

Az operációs rendszer (Linux) szolgáltatójának konfigurálásán két fő lépés vesz részt:
1. Telepítse [Node_Exporter](https://github.com/prometheus/node_exporter)   operációs rendszer nélküli vagy virtuálisgép-csomópontokra.
   A következő két lehetőség közül választhat a [Node_exporter:](https://github.com/prometheus/node_exporter) 
      - Az Ansible-alapú [](https://github.com/prometheus/node_exporter) Automation-telepítéshez minden BareMetal Node_Exporter virtuálisgép-csomóponton külön kell telepíteni az operációsrendszer-szolgáltatót (Linux).  
      - Manuális [telepítést kell.](https://prometheus.io/docs/guides/node-exporter/)

2. Konfigurálja az operációs rendszer (Linux) szolgáltatóját a környezetben található összes BareMetal- vagy VM-csomópontpéldányhoz. 
   Az operációs rendszer (Linux) szolgáltatójának konfigurálásához a következő információkra van szükség: 
      - név. A szolgáltató neve. Ennek egyedinek kell lennie ehhez a Azure Monitor SAP-megoldáspéldányhoz. 
      - Csomópont-végpont. Általában http:// <servername or ip address> :9100/metrics 

> [!NOTE]
> A 9100 egy végpont számára elérhetővé Node_Exporter port.

> [!Warning]
> A csomópont újraindítása után győződjön meg arról, hogy a Csomópont-csomópont-kiszolgáló továbbra is fut. 

## <a name="next-steps"></a>Következő lépések

- Tekintse [meg az sap-megoldások](./azure-monitor-sap-quickstart.md) Azure Monitor első lépéseit.
- Kérdése van a Azure Monitor for SAP Solutions? Tekintse meg a [GYAKORI KÉRDÉSEK](./azure-monitor-faq.md) szakaszt

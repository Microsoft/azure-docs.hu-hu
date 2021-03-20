---
title: Magas rendelkezésre állású összetevők az Azure HDInsight
description: A HDInsight-fürtök által használt különféle magas rendelkezésre állású összetevők áttekintése.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 336fe91174a8fc6d73d6e45c5fd1e2bf244eda52
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945303"
---
# <a name="high-availability-services-supported-by-azure-hdinsight"></a>Az Azure HDInsight által támogatott magas rendelkezésre állású szolgáltatások

Az analitikai összetevők optimális rendelkezésre állásának biztosítása érdekében a HDInsight egy egyedi architektúrával lett kifejlesztve, amely biztosítja a kritikus szolgáltatások magas rendelkezésre állását (HA). Az architektúra egyes összetevőit a Microsoft fejlesztette ki automatikus feladatátvétel biztosításához. A többi összetevő a szabványos Apache-összetevők, amelyek az adott szolgáltatások támogatására lettek telepítve. Ez a cikk ismerteti az IF Service-modell architektúráját a HDInsight-ben, hogy a HDInsight hogyan támogatja a feladatátvételt a HA szolgáltatások esetében, valamint az ajánlott eljárásokat a más szolgáltatásokból való helyreállításhoz.

> [!NOTE]
> Ez a cikk a *Slave* kifejezésre mutató hivatkozásokat tartalmaz, amelyek egy kifejezés, amelyet a Microsoft már nem használ. Ha a rendszer eltávolítja a kifejezést a szoftverből, azt a cikkből távolítjuk el.

## <a name="high-availability-infrastructure"></a>Magas rendelkezésre állású infrastruktúra

A HDInsight testreszabott infrastruktúrát biztosít annak biztosításához, hogy a négy elsődleges szolgáltatás magas rendelkezésre állású legyen az automatikus feladatátvételi képességekkel:

- Apache Ambari-kiszolgáló
- Application Timeline-kiszolgáló Apache-SZÁLhoz
- A Hadoop MapReduce tartozó korábbi feladatok kiszolgálója
- Apache Livy

Ez az infrastruktúra számos szolgáltatás-és szoftver-összetevőből áll, amelyek közül néhányat a Microsoft tervez. A következő összetevők egyediek a HDInsight platformon:

- Slave feladatátvételi vezérlő
- Fő feladatátvételi vezérlő
- Slave magas rendelkezésre állási szolgáltatás
- Fő magas rendelkezésre állási szolgáltatás

![magas rendelkezésre állású infrastruktúra](./media/hdinsight-high-availability-components/high-availability-architecture.png)

Vannak olyan magas rendelkezésre állású szolgáltatások is, amelyeket a nyílt forráskódú Apache megbízhatósági összetevői támogatnak. Ezek az összetevők a HDInsight-fürtökön is elérhetők:

- Hadoop fájlrendszer (HDFS) NameNode
- FONAL erőforráskezelő
- HBase Master

A következő szakaszokban részletesebben tájékozódhat arról, hogy ezek a szolgáltatások hogyan működnek együtt.

## <a name="hdinsight-high-availability-services"></a>HDInsight magas rendelkezésre állású szolgáltatások

A Microsoft támogatja a négy apache-szolgáltatást a következő táblázatban a HDInsight-fürtökben. Az Apache-összetevők által támogatott magas rendelkezésre állású szolgáltatásokból való különbségtételhez *HDINSIGHT ha-szolgáltatásoknak* nevezzük.

| Szolgáltatás | Fürtcsomópontok | Fürtök típusai | Cél |
|---|---|---|---|
| Apache Ambari-kiszolgáló| Aktív átjárócsomóponthoz | Mind | Figyeli és kezeli a fürtöt.|
| Application Timeline-kiszolgáló Apache-SZÁLhoz | Aktív átjárócsomóponthoz | A Kafka kivételével | A fürtön futó fonal-feladatokkal kapcsolatos hibakeresési adatokat kezeli.|
| A Hadoop MapReduce tartozó korábbi feladatok kiszolgálója | Aktív átjárócsomóponthoz | A Kafka kivételével | A MapReduce-feladatok hibakeresési adatait kezeli.|
| Apache Livy | Aktív átjárócsomóponthoz | Spark | Egyszerű interakciót tesz lehetővé egy Spark-fürttel REST-felületen keresztül |

>[!Note]
> A HDInsight Enterprise Security Package (ESP) fürtök jelenleg csak magas rendelkezésre állást biztosítanak a Ambari-kiszolgáló számára. Az alkalmazás idővonal-kiszolgálója, a Livy-kiszolgáló és a headnode0 csak a fut, és a Ambari failsover nem végez feladatátvételt a headnode1. Az alkalmazás idővonal-adatbázisa a headnode0-on is megtalálható, nem pedig a Ambari SQL Serveren.

### <a name="architecture"></a>Architektúra

Mindegyik HDInsight-fürt két átjárócsomópontokkal rendelkezik aktív és készenléti módban. A HDInsight HA szolgáltatások csak a átjárócsomópontokkal futnak. Ezeknek a szolgáltatásoknak mindig az aktív átjárócsomóponthoz kell futniuk, és karbantartási módba kell állítaniuk a készenléti átjárócsomóponthoz.

HA a Apache ZooKeeper HDInsight megfelelő állapotait szeretné fenntartani, és gyors feladatátvételt szeretne biztosítani, az aktív átjárócsomóponthoz-választást az elosztott alkalmazások koordinációs szolgáltatása biztosítja. A HDInsight Emellett néhány háttérben futó Java-folyamatot is kiépít, amelyek a HDInsight HEKTÁRos szolgáltatások feladatátvételi eljárását hangolják össze. Ezek a szolgáltatások a következők: a fő feladatátvevő vezérlő, a Slave feladatátvételi vezérlő, a *Master-ha-Service* és a *Slave-ha-Service*.

### <a name="apache-zookeeper"></a>Apache ZooKeeper

A Apache ZooKeeper az elosztott alkalmazások nagy teljesítményű koordinációs szolgáltatása. Éles környezetben a ZooKeeper általában replikált módban fut, ahol a ZooKeeper-kiszolgálók replikált csoportja kvórumot alkot. Mindegyik HDInsight-fürt három ZooKeeper-csomóponttal rendelkezik, amelyek lehetővé teszik három ZooKeeper-kiszolgáló Kvórumának megalkotása. A HDInsight két, egymással párhuzamosan futó ZooKeeper-kvórumot tartalmaz. Az egyik kvórum úgy dönt, hogy aktív átjárócsomóponthoz egy olyan fürtben, amelyen a HDInsight HA szolgáltatásoknak futniuk kell. Egy másik kvórum az Apache által nyújtott szolgáltatások koordinálására szolgál, a későbbi részekben részletezett módon.

### <a name="slave-failover-controller"></a>Slave feladatátvételi vezérlő

A Slave feladatátvételi vezérlő a HDInsight-fürt minden csomópontján fut. Ez a vezérlő felelős a Ambari-ügynök és a *Slave-ha-szolgáltatás* minden csomóponton történő elindításához. Rendszeresen lekérdezi az első ZooKeeper kvórumot az aktív átjárócsomóponthoz kapcsolatban. Az aktív és a készenléti átjárócsomópontokkal változásakor a Slave feladatátvételi vezérlő a következőket hajtja végre:

1. Frissíti a gazdagép konfigurációs fájlját.
1. Újraindítja a Ambari-ügynököt.

A *Slave-ha-Service* feladata a HDInsight ha szolgáltatások (kivéve a Ambari-kiszolgálót) leállítása a készenléti átjárócsomóponthoz.

### <a name="master-failover-controller"></a>Fő feladatátvételi vezérlő

A fő feladatátvevő vezérlő mindkét átjárócsomópontokkal fut. Mindkét fő feladatátvételi vezérlő kommunikál az első ZooKeeper kvórumtal, hogy az aktív átjárócsomóponthoz átjárócsomóponthoz jelölje meg.

Ha például a átjárócsomóponthoz 0 fő feladatátvételi vezérlő nyerte meg a választást, a következő módosítások lépnek érvénybe:

1. A átjárócsomóponthoz 0 aktívvá válik.
1. A fő feladatátvételi vezérlő elindítja a Ambari-kiszolgálót és a *Master-ha-Service* értéket a átjárócsomóponthoz 0 számítógépen.
1. A másik fő feladatátvételi vezérlő leállítja a Ambari-kiszolgálót és az 1. átjárócsomóponthoz *Master-ha-szolgáltatást* .

A Master-ha-Service csak az aktív átjárócsomóponthoz fut, leállítja a HDInsight hektár szolgáltatásokat (kivéve a Ambari-kiszolgálót) a készenléti átjárócsomóponthoz, és az aktív átjárócsomóponthoz indítja azokat.

### <a name="the-failover-process"></a>A feladatátvételi folyamat

![feladatátvételi folyamat](./media/hdinsight-high-availability-components/failover-steps.png)

Az állapotfigyelő az egyes átjárócsomóponthoz, valamint a fő feladatátvételi vezérlővel együtt a Zookeeper kvórumnak küldött szívverési értesítések küldésére is képes. Ebben a forgatókönyvben a átjárócsomóponthoz egy HA szolgáltatásnak számít. Az állapotfigyelő ellenőrzi, hogy az egyes magas rendelkezésre állású szolgáltatások állapota Kifogástalan-e, és hogy készen áll-e a részvételre a vezető választása során. Ha igen, akkor ez a átjárócsomóponthoz fog versenyezni a választásban. Ha nem, akkor kilép a választásból, amíg újra nem válik.

Ha a készenléti átjárócsomóponthoz valaha is megkezdi a vezetést, és aktívvá válik (például az előző aktív csomópont meghibásodása esetén), akkor a fő feladatátvételi vezérlő a HDInsight. A fő feladatátvételi vezérlő a többi átjárócsomóponthoz is leállítja ezeket a szolgáltatásokat.

HA a HDInsight, például a szolgáltatás leállt vagy sérült, akkor a fő feladatátvételi vezérlőnek automatikusan újra kell indítania vagy leállítania a szolgáltatásokat a átjárócsomóponthoz állapota alapján. A felhasználóknak nem kell manuálisan elindítaniuk a HDInsight HA szolgáltatásait mindkét fő csomóponton. Ehelyett engedélyezze az automatikus vagy manuális feladatátvételt a szolgáltatás helyreállításának elősegítése érdekében.

### <a name="inadvertent-manual-intervention"></a>Véletlen manuális beavatkozás

HDInsight HA a szolgáltatások csak az aktív átjárócsomóponthoz futnak, és szükség esetén automatikusan újraindulnak. Mivel az egyes HA-szolgáltatások nem rendelkeznek saját állapotfigyelő szolgáltatással, a feladatátvételt nem lehet az egyes szolgáltatások szintjén elindítani. A feladatátvételt a csomópont szintjén, nem pedig a szolgáltatási szinten biztosítjuk.

### <a name="some-known-issues"></a>Néhány ismert probléma

- Ha a készenléti átjárócsomóponthoz manuálisan indítja el a HA szolgáltatást, az nem áll le, amíg a következő feladatátvétel történik. Ha a HA szolgáltatások mindkét átjárócsomópontokkal futnak, a következő problémák merülhetnek fel: a Ambari felhasználói felülete nem érhető el, a Ambari hibák, FONALak, Spark és Oozie feladatok elakadnak.

- Ha az aktív átjárócsomóponthoz egy HA szolgáltatás leáll, az nem indul el, amíg a következő feladatátvétel bekövetkezik, vagy a fő feladatátvételi vezérlő/Master-ha-Service újraindul. Ha egy vagy több HA-szolgáltatás leáll az aktív átjárócsomóponthoz, különösen akkor, ha a Ambari-kiszolgáló leáll, a Ambari felhasználói felülete nem érhető el, más lehetséges problémák például a fonal, a Spark és a Oozie feladatok meghibásodása.

## <a name="apache-high-availability-services"></a>Apache magas rendelkezésre állású szolgáltatások

Az Apache magas rendelkezésre állást biztosít a HDFS NameNode, a fonal erőforráskezelő és a HBase Master, amelyek HDInsight-fürtökben is elérhetők. A HDInsight HA szolgáltatásokkal ellentétben az ESP-fürtökben támogatottak. Az Apache HA-szolgáltatások az aktív/készenléti állapotok megválasztásához és az automatikus feladatátvétel végrehajtásához a második ZooKeeper-kvórum (lásd a fenti szakaszban leírt) kommunikációt végzik. A következő részek részletesen ismertetik a szolgáltatások működését.

### <a name="hadoop-distributed-file-system-hdfs-namenode"></a>Hadoop elosztott fájlrendszer (HDFS) NameNode

A HDInsight-fürtök Apache Hadoop 2,0 vagy újabb rendszereken alapuló NameNode magas rendelkezésre állást biztosítanak. A átjárócsomópontokkal két NameNodes fut, amelyek automatikus feladatátvételre vannak konfigurálva. A NameNodes az *ZKFailoverController* segítségével kommunikál a Zookeeper, hogy az aktív/készenléti állapotot válassza. A *ZKFailoverController* mindkét átjárócsomópontokkal fut, és ugyanúgy működik, mint a fenti fő feladatátvételi vezérlő.

A második Zookeeper kvórum az első kvórumtól független, így előfordulhat, hogy az aktív NameNode nem fut az aktív átjárócsomóponthoz. Ha az aktív NameNode halott vagy sérült, a készenléti NameNode megnyeri a választást, és aktívvá válik.

### <a name="yarn-resourcemanager"></a>FONAL erőforráskezelő

A HDInsight-fürtök Apache Hadoop 2,4-es vagy újabb verziója alapján támogatják a erőforráskezelő magas rendelkezésre állását. Két ResourceManagers, RM1 és RM2 fut a átjárócsomóponthoz 0 és a átjárócsomóponthoz 1 rendszeren. A NameNode hasonlóan a fonal erőforráskezelő is automatikus feladatátvételre van konfigurálva. A rendszer automatikusan választ egy másik erőforráskezelő, amely akkor aktív, ha az aktuális aktív erőforráskezelő leáll vagy nem válaszol.

A fonal erőforráskezelő a beágyazott *ActiveStandbyElector* meghibásodási detektorként és vezető választóként használja. A HDFS NameNode eltérően a fonal erőforráskezelő nem igényel külön ZKFC démont. Az aktív erőforráskezelő az állapotait az Apache Zookeeper írja.

A fonal erőforráskezelő magas rendelkezésre állása független a NameNode és más HDInsight-szolgáltatástól. Előfordulhat, hogy az aktív erőforráskezelő nem fut az aktív átjárócsomóponthoz vagy a átjárócsomóponthoz, amelyen az aktív NameNode fut. A erőforráskezelő magas rendelkezésre állásáról további információt a [erőforráskezelő magas rendelkezésre állása](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/ResourceManagerHA.html)című témakörben talál.

### <a name="hbase-master"></a>HBase Master

A HDInsight HBase-fürtök támogatják HBase Master magas rendelkezésre állást. A átjárócsomópontokkal-on futó egyéb HA-szolgáltatásokkal ellentétben a HBase-főkiszolgálók a három Zookeeper csomóponton futnak, ahol az egyik az aktív főkiszolgáló, a másik kettő pedig készenléti. A NameNode-hez hasonlóan HBase Master az Apache Zookeeper-vel való koordinátákat a Leader-választásokhoz, és automatikus feladatátvételt hajt végre, ha a jelenlegi aktív főkiszolgáló problémába Egyszerre csak egy aktív HBase Master van.

## <a name="next-steps"></a>Következő lépések

- [Apache Hadoop-fürtök rendelkezésre állása és megbízhatósága a HDInsight-ben](./hdinsight-business-continuity.md)
- [Azure HDInsight virtuális hálózati architektúra](hdinsight-virtual-network-architecture.md)
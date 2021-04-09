---
title: 'Előnyök: helyszíni Apache Hadoop migrálása az Azure HDInsight'
description: Megismerheti a helyszíni Hadoop-fürtök Azure-HDInsight való áttelepítésének motivációját és előnyeit.
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 11/15/2019
ms.openlocfilehash: 975d72df32027888e217d5da9171dba0ba61f257
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943250"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---motivation-and-benefits"></a>Helyszíni Apache Hadoop-fürtök migrálása az Azure HDInsight – motiváció és előnyök

Ez a cikk a helyszíni Apache Hadoop öko-rendszer Azure HDInsight való áttelepítésének ajánlott eljárásainak első sorozata. A cikksorozat azon személyeknek szól, akik az Azure HDInsight Apache Hadoop-megoldások tervezésével, üzembe helyezésével és áttelepítésével felelősek. Ezek a cikkek a felhő-építészek, a Hadoop-rendszergazdák és a DevOps-mérnökök számára is hasznosak lehetnek. A szoftverfejlesztők, adatmérnökök és adatszakértők a különböző típusú fürtök Felhőbeli működésének magyarázatát is kihasználhatják.

## <a name="why-to-migrate-to-azure-hdinsight"></a>Miért érdemes migrálni az Azure HDInsight

Az Azure HDInsight a Hadoop-összetevők Felhőbeli eloszlása. Az Azure HDInsight segítségével könnyen, gyorsan és költséghatékonyan dolgozható fel nagy mennyiségű adat. A HDInsight tartalmazza a legnépszerűbb nyílt forráskódú keretrendszereket, például a következőket:

- Apache Hadoop
- Apache Spark
- Apache Hive LLAP
- Apache Kafka
- Apache Storm
- Apache HBase
- R

## <a name="azure-hdinsight-advantages-over-on-premises-hadoop"></a>Az Azure HDInsight előnyei a helyszíni Hadoop

- Az **alacsony költségek** csökkentéséhez a [fürtöket igény](../hdinsight-hadoop-create-linux-clusters-adf.md) szerint kell létrehozni, és csak azért kell fizetnie, amit ténylegesen használ. A leválasztott számítás és tárolás rugalmasságot biztosít azáltal, hogy az adatmennyiséget a fürt méretétől függetlenül tartja.

- **Automatikus fürt létrehozása** – a fürt automatikus létrehozásához minimális beállítás és konfigurálás szükséges. Az Automation használható igény szerinti fürtökhöz.

- **Felügyelt hardverek és konfigurációk** – nem kell aggódnia a fizikai hardver vagy infrastruktúra HDInsight-fürttel való használatával kapcsolatban. Csak adja meg a fürt konfigurációját, és az Azure beállítja.

- A **könnyen méretezhető** HDInsight lehetővé teszi a számítási feladatok fel-és [leskálázását](../hdinsight-administer-use-portal-linux.md) . Az Azure az adatfeldolgozási feladatok megszakítása nélkül gondoskodik az adatok újraelosztásáról és a számítási feladatok kiegyensúlyozásáról.

- **Globális rendelkezésre állás** – a HDInsight több [régióban](https://azure.microsoft.com/regions/services/) is elérhető, mint bármely más Big Data elemzési ajánlat. Az Azure HDInsight elérhető az Azure Governmentben, Kínában, és Németországban is, így megfelelhet a vállalati igényeknek a főbb szuverén területeken.

- A **biztonságos és megfelelő** HDInsight lehetővé teszi a vállalati adategységek védelmét az [Azure Virtual Network](../hdinsight-plan-virtual-network-deployment.md), [titkosítással](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md)és az [Azure Active Directoryokkal](../domain-joined/hdinsight-security-overview.md)való integrációval. A HDInsight továbbá megfelel a legnépszerűbb iparági és kormányzati [megfelelőségi szabványoknak](https://azure.microsoft.com/overview/trusted-cloud).

- **Egyszerűsített verziókövetés** – az Azure HDInsight kezeli a Hadoop Eco-System összetevőinek verzióját, és naprakészen tartja azokat. A szoftverfrissítések általában egy összetett folyamat a helyszíni környezetekben.

- Az **összetevők közötti kevesebb függőséggel rendelkező, meghatározott számítási feladatokhoz optimalizált kisebb fürtök** – a tipikus helyszíni Hadoop-telepítő egyetlen fürtöt használ, amely számos célt szolgál. Az Azure HDInsight a munkaterhelés-specifikus fürtök hozhatók létre. A fürtök adott számítási feladatokhoz való létrehozása eltávolítja a bonyolultságot, amely egyre összetettebbé válik egyetlen fürt fenntartásával.

- **Termelékenység** – különböző eszközöket használhat a Hadoop és a sparkhoz az előnyben részesített fejlesztési környezetben.

- **Bővíthetőség egyéni eszközökkel vagy harmadik féltől származó alkalmazásokkal** – a HDInsight-fürtök bővíthetők a telepített összetevőkkel, és a többi Big Data megoldással is integrálhatók az Azure piactéren történő [egykattintásos](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) üzembe helyezések használatával.

- **Egyszerű felügyelet, felügyelet és figyelés** – az Azure HDInsight integrálható [Azure monitor naplókkal](../hdinsight-hadoop-oms-log-analytics-tutorial.md) , így egyetlen felületet biztosít, amellyel az összes fürtöt nyomon követheti.

- **Integráció más Azure-szolgáltatásokkal** – a HDInsight könnyen integrálható más népszerű Azure-szolgáltatásokkal, például a következőkkel:

    - Azure Data Factory (ADF)
    - Azure Blob Storage
    - 2. generációs Azure Data Lake Storage
    - Azure Cosmos DB
    - Azure SQL Database
    - Azure Analysis Services

- **Önjavító folyamatok és összetevők** – a HDInsight folyamatosan ellenőrzi az infrastruktúrát és a nyílt forráskódú összetevőket a saját figyelési infrastruktúrájának használatával. Emellett automatikusan helyreállítja a kritikus hibákat, például a nyílt forráskódú összetevők és csomópontok nem rendelkezésre állását. A riasztások a Ambari-ben aktiválódnak, ha bármely OSS-összetevő meghiúsult.

További információ: [Mi az Azure HDInsight és a Apache Hadoop Technology stack](../hadoop/apache-hadoop-introduction.md).

## <a name="migration-planning-process"></a>Áttelepítési tervezési folyamat

A helyszíni Hadoop-fürtök Azure HDInsight-ba történő áttelepítésének megtervezéséhez a következő lépések szükségesek:

1. Ismerje meg az aktuális helyszíni üzembe helyezést és topológiákat.
2. Ismerje meg az aktuális projekt hatókörét, az ütemterveket és a csapat szakértelmét.
3. Az Azure követelményeinek megismerése.
4. Hozzon létre részletes tervet az ajánlott eljárások alapján.

## <a name="gathering-details-to-prepare-for-a-migration"></a>Az áttelepítésre való felkészülés részleteinek összegyűjtése

Ez a szakasz olyan sablon-kérdőíveket biztosít, amelyek segítenek a következő fontos információk gyűjtésében:

- Helyszíni üzembe helyezés
- Projekt részletei
- Azure-követelmények

### <a name="on-premises-deployment-questionnaire"></a>Helyszíni üzembe helyezési kérdőív

| **Kérdés** | **Példa** | **Válasz** |
|---|---|---|
|**Témakör**: **környezet**|||
|Fürt terjesztési verziója|HDP 2.6.5, CDH 5,7|
|Big adatkörnyezet-összetevők|HDFS, fonal, kaptár, LLAP, Impala, kudu, HBase, Spark, MapReduce, Kafka, Zookeeper, Solr, Sqoop, Oozie, Ranger, atlasz, Falcon, Zeppelin, R|
|Fürtök típusai|Hadoop, Spark, Fluent Kafka, Storm, Solr|
|Fürtök száma|4|
|Fő csomópontok száma|2|
|Munkavégző csomópontok száma|100|
|Peremhálózati csomópontok száma| 5|
|Teljes lemezterület|100 TB|
|Fő csomópont konfigurálása|m/y, CPU, lemez stb.|
|Adatcsomópontok konfigurálása|m/y, CPU, lemez stb.|
|Peremhálózati csomópontok konfigurálása|m/y, CPU, lemez stb.|
|HDFS titkosítás?|Yes|
|Magas rendelkezésre állás|HDFS HA, Metaadattár HA|
|Vész-helyreállítás/biztonsági mentés|Biztonsági mentési fürt?|  
|Fürttől függő rendszerek|SQL Server, Teradata, Power BI, MongoDB|
|Harmadik féltől származó integrációk|Tabló, GridGain, Qubole, Informatica, splunk|
|**Témakör**: **Biztonság**|||
|Szegélyhálózati biztonság|Tűzfalak|
|Fürt hitelesítésének & engedélyezése|Active Directory, Ambari, Cloudera Manager, nincs hitelesítés|
|HDFS Access Control|  Manuális, SSH-felhasználók|
|Struktúra-hitelesítés & engedélyezése|Sentry, LDAP, AD és Kerberos, Ranger|
|Naplózás|Ambari, Cloudera-navigátor, Ranger|
|Figyelés|Grafit, Collected, statd, InfluxDB|
|Riasztások kezelése|Kapacitor, Prometheus, Datadoggal|
|Adatmegőrzés időtartama| 3 év, 5 év|
|Fürt rendszergazdái|Egyetlen rendszergazda, több rendszergazda|

### <a name="project-details-questionnaire"></a>Project details – kérdőív

|**Kérdés**|**Példa**|**Válasz**|
|---|---|---|
|**Témakör**: számítási **feladatok és gyakoriság**|||
|MapReduce-feladatok|10 feladat – naponta kétszer||
|Struktúra-feladatok|100 feladat--óránként||
|Spark batch-feladatok|50 feladat – 15 percenként||
|Spark streaming-feladatok|5 feladat – 3 percenként||
|Strukturált adatfolyam-feladatok|5 feladat – percenként||
|ML Model betanítási feladatok|2 feladat – hetente egyszer||
|Programozási nyelvek|Python, Scala, Java||
|Parancsprogramok használata|Shell, Python||
|**Témakör**: **az adatkezelés**|||
|Adatforrások|Lapos fájlok, JSON, Kafka, RDBMS||
|Adatelőkészítés|Oozie-munkafolyamatok, légáram||
|Memóriabeli keresések|Apache Ignite, Redis||
|Adatelérési helyek|HDFS, RDBMS, Kafka, MPP ||
|**Témakör**: **meta-adatkezelés**|||
|Struktúra-adatbázis típusa|MySQL, postgres||
|Struktúra metaadattárak száma|2||
|Struktúra-táblák száma|100||
|A Ranger-szabályzatok száma|20||
|Oozie-munkafolyamatok száma|100||
|**Témakör**: **skála**|||
|Adatmennyiség, beleértve a replikálást|100 TB||
|Napi betöltési mennyiség|50 GB||
|Adatmennyiség növekedési aránya|évi 10%||
|Fürtcsomópontok növekedési aránya|évente 5%
|**Témakör**: **fürt kihasználtsága**|||
|Átlagos CPU-használat (%)|60%||
|Felhasznált memória átlagos százaléka (%)|75%||
|Felhasznált lemezterület|75%||
|Átlagos hálózati használat%-ban|25%
|**Témakör**: **munkatársak**|||
|Rendszergazdák száma|2||
|Fejlesztők száma|10||
|Végfelhasználók száma|100||
|Szakértelem|Hadoop, Spark||
|Az áttelepítési erőfeszítésekhez rendelkezésre álló erőforrások száma|2||
|**Témakör**: **korlátozások**|||
|Aktuális korlátozások|A késés magas||
|Aktuális feladvány|Egyidejűségi probléma||

### <a name="azure-requirements-questionnaire"></a>Azure-követelmények – kérdőív

|**Kérdés**|**Példa**|**Válasz**|
|---|---|---|
|**Témakör**: **infrastruktúra** |||
| Előnyben részesített régió|USA keleti régiója||
|VNet előnyben részesített?|Yes||
|HA/DR szükséges?|Yes||
|Integráció más felhőalapú szolgáltatásokkal?|ADF, CosmosDB||
|**Témakör**:   **adatáthelyezés**  |||
|Kezdeti betöltési beállítás|DistCp, adatmező, ADF, WANDisco||
|Adatátviteli különbözet|DistCp, AzCopy||
|Folyamatos növekményes adatátvitel|DistCp, Sqoop||
|**Témakör**:   **& riasztás figyelése** |||
|Az Azure monitoring & a riasztások és a harmadik féltől származó figyelés integrálása|Az Azure monitoring & riasztások használata||
|**Témakör**:   **biztonsági beállítások** |||
|Magán-és védett adatfolyamatok?|Yes||
|Tartományhoz csatlakoztatott fürt (ESP)?|     Yes||
|Helyszíni AD-szinkronizáló a felhőbe?|     Yes||
|A szinkronizálni kívánt AD-felhasználók száma?|          100||
|Szeretné szinkronizálni a jelszavakat a felhőbe?|    Yes||
|Csak Felhőbeli felhasználók?|                 Yes||
|MFA szükséges?|                       No|| 
|Az adatengedélyezési követelmények?|  Yes||
|Szerepköralapú hozzáférés-vezérlés?|        Yes||
|Naplózás szükséges?|                  Yes||
|Inaktív adatok titkosítása?|          Yes||
|Adattitkosítás az átvitel során?|       Yes||
|**Témakör**:   **újraarchitektúra beállításai** |||
|Egyetlen fürt és adott fürtök típusai|Adott fürtök típusai||
|A tárolók és a távoli tárolók is találhatók?|Távoli tárterület||
|Kisebb a fürt mérete, mert az adattárolás távolról történik?|Kisebb fürt mérete||
|Egy nagyméretű fürt helyett több kisebb fürtöt használjon?|Több kisebb fürt használata||
|Távoli metaadattár használ?|Yes||
|Megoszthatja a metaadattárak a különböző fürtök között?|Yes||
|Felépítési feladatok elkészítése?|Kaptár-feladatok cseréje a Spark-feladatokkal||
|Az ADF használata az adatelőkészítéshez?|Nem||

## <a name="next-steps"></a>Következő lépések

Olvassa el a következő cikket a sorozatban:

- [Ajánlott eljárások a helyszíni környezethez Azure HDInsight Hadoop áttelepítéshez](apache-hadoop-on-premises-migration-best-practices-architecture.md)

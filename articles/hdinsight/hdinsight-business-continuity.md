---
title: Az Azure HDInsight üzletmenet folytonossága
description: Ez a cikk áttekintést nyújt az ajánlott eljárásokról, az egyrégiós elérhetőségről és az Azure HDInsight üzletmenet-folytonossági tervezésének optimalizálási lehetőségeiről.
keywords: Hadoop magas rendelkezésre állása
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 74f8bdd26e000b89bfae84102077c241f85abf7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933307"
---
# <a name="azure-hdinsight-business-continuity"></a>Az Azure HDInsight üzletmenet folytonossága

Az Azure HDInsight-fürtök számos olyan Azure-szolgáltatástól függenek, mint a Storage, az adatbázisok, a Active Directory, a Active Directory Domain Services, a Hálózatkezelés és a Key Vault. Egy jól megtervezett, nagy rendelkezésre állású és hibatűrő elemzési alkalmazást úgy kell kialakítani, hogy az adott szolgáltatások közül egy vagy több esetében is ellenálljanak a regionális vagy helyi fennakadásoknak. Ez a cikk áttekintést nyújt az ajánlott eljárásokról, az egyrégiós rendelkezésre állásról, valamint az üzletmenet folytonosságának megtervezéséhez szükséges optimalizálási lehetőségekről.

## <a name="general-best-practices"></a>Általános ajánlott eljárások

Ez a szakasz az üzletmenet folytonosságának megtervezése során felmerülő ajánlott eljárásokat tárgyalja.

* Határozza meg, hogy milyen minimális üzleti funkciókra van szükség, ha van ilyen katasztrófa, és miért. Például kiértékelheti, hogy szüksége van-e feladatátvételi lehetőségekre az Adatátalakítási réteg (sárga színnel) *és* a (kék színnel megjelenő) réteg között, vagy ha csak feladatátvételre van szüksége az adatszolgáltatási réteghez.

   :::image type="content" source="media/hdinsight-business-continuity/data-layers.png" alt-text="Adatátalakítási és-adatkezelési rétegek":::

* A fürtöket a munkaterhelés, a fejlesztési életciklus és a részlegek alapján szegmentálhatja. Ha több fürttel rendelkezik, a több különböző üzleti folyamatot érintő egyetlen nagy hiba esélyeit csökkenti.

* A másodlagos régiók legyenek csak olvashatók. Az olvasási és írási képességgel rendelkező feladatátvételi régiók összetett architektúrához vezethetnek.

* Az átmeneti fürtök könnyebben kezelhetők, ha vészhelyzet van. Tervezze meg a munkaterheléseket úgy, hogy a fürtök akár kerékpárok is lehetnek, és a fürtökben ne maradjon állapot.

* A munkaterhelések gyakran üresen maradnak, ha vészhelyzet van, és újra kell indítani az új régióban. Tervezze meg a számítási feladatok idempotens a természetben.

* Használjon automatizálást a fürtök üzembe helyezése során, és győződjön meg arról, hogy a fürt konfigurációs beállításai a lehető legnagyobb mértékben vannak leképezve, így biztosítva a gyors és a teljesen automatizált üzembe helyezést, ha van

* Az Azure monitoring Tools on HDInsight segítségével azonosíthatja a fürt rendellenes viselkedését, és beállíthatja a megfelelő riasztási értesítéseket. Üzembe helyezheti az előre konfigurált HDInsight-fürthöz tartozó felügyeleti megoldásokat, amelyek az adott típusú fürt fontos teljesítménymutatóit gyűjtik össze. További információ: Azure- [figyelés HDInsight](./hdinsight-hadoop-oms-log-analytics-tutorial.md).  

* Fizessen elő az Azure Health-riasztásokra, hogy értesítést kapjon a szolgáltatással kapcsolatos problémákról, a tervezett karbantartásról, az előfizetés, szolgáltatás vagy régió állapotáról és biztonsági tanácsadóinak. A probléma okát és a határozott ETA-t tartalmazó állapot-értesítések segítenek a feladatátvétel és a failbacks jobb végrehajtásában. További információ: [Azure Service Health dokumentáció](../service-health/index.yml).

## <a name="single-region-availability"></a>Önálló régió elérhetősége

Az alapszintű HDInsight rendszer a következő összetevőket tartalmaz. Minden összetevő saját, egyetlen régió hibatűrési mechanizmussal rendelkezik.

* Számítás (virtuális gépek): Azure HDInsight-fürt
* Metaadattár (ok): Azure SQL Database
* Tárolás: Azure Data Lake Gen2 vagy blob Storage
* Hitelesítés: Azure Active Directory, Azure Active Directory Domain Services, Enterprise Security Package
* Tartománynév feloldása: Azure DNS

Más opcionális szolgáltatások is használhatók, például Azure Key Vault és Azure Data Factory.

:::image type="content" source="media/hdinsight-business-continuity/hdinsight-components.png" alt-text="HDInsight-összetevők":::

### <a name="azure-hdinsight-cluster-compute"></a>Azure HDInsight-fürt (számítás)

A HDInsight 99,9%-os rendelkezésre állási SLA-t biztosít. Ha magas rendelkezésre állást szeretne biztosítani egy központi telepítésben, a HDInsight alapértelmezés szerint számos, magas rendelkezésre állású szolgáltatással rendelkezik. A HDInsight-ben a hibatűrési mechanizmusokat a Microsoft és az Apache OSS ökoszisztéma magas rendelkezésre állású szolgáltatásai biztosítják.

A következő szolgáltatások nagyon elérhetővé válnak:

#### <a name="infrastructure"></a>Infrastruktúra

* Aktív és készenléti Átjárócsomópontokkal
* Több átjáró csomópontja
* Három Zookeeper kvórum csomópont
* Hibák és frissítési tartományok által terjesztett munkavégző csomópontok

#### <a name="service"></a>Szolgáltatás

* Apache Ambari-kiszolgáló
* A FONALhoz tartozó Application Timeline-severok
* A Hadoop MapReduce tartozó korábbi feladatok kiszolgálója
* Apache Livy
* HDFS
* A fonal Resource Manager
* HBase Master

További információt az [Azure HDInsight által támogatott magas rendelkezésre állású szolgáltatásokról](./hdinsight-high-availability-components.md) szóló dokumentációban talál.

Nem mindig katasztrofális eseményt gyakorol az üzleti funkciókra. A szolgáltatási incidensek az egyes régiókban a következő szolgáltatások közül egy vagy több esetében is a várt üzleti funkciók elvesztését eredményezhetik.

### <a name="hdinsight-metastore"></a>HDInsight metaadattár

A HDInsight [Azure SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/) metaadattár-ként használ, amely 99,99%-os SLA-t biztosít. Három adatreplikát tart fenn egy adatközpontban szinkron replikálással. Ha van replika elvesztése, a rendszer zökkenőmentesen kézbesít egy alternatív replikát. Az [aktív geo-replikáció](../azure-sql/database/active-geo-replication-overview.md) legfeljebb négy adatközpontból álló dobozban támogatott. Ha feladatátvétel van, vagy manuális vagy adatközpont van, a hierarchia első replikája automatikusan írási és olvasási képességgel fog válni. További információ: [Azure SQL Database üzletmenet folytonossága](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md).

### <a name="hdinsight-storage"></a>HDInsight-tár

A HDInsight az alapul szolgáló tárolási rétegként Azure Data Lake Storage Gen2 javasolja. Az [Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_5/), beleértve a Azure Data Lake Storage Gen2-t, 99,9%-os SLA-t biztosít. A HDInsight a LRS szolgáltatást használja, amelyben három adatreplikát tart fenn egy adatközpontban, és a replikáció szinkronban van. Ha van replika elvesztése, a rendszer zökkenőmentesen kézbesíti a replikát.

### <a name="azure-active-directory"></a>Azure Active Directory

A [Azure Active Directory](https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/) 99,9%-os SLA-t biztosít. A Active Directory a belső redundancia és az automatikus helyreállítás több szintjével rendelkező globális szolgáltatás. További információkért lásd: Hogyan [fejleszthető folyamatosan a Microsoft a Azure Active Directory megbízhatóságát](https://azure.microsoft.com/blog/advancing-azure-active-directory-availability/).

### <a name="azure-active-directory-domain-services-ad-ds"></a>Azure Active Directory Domain Services (AD DS)

A [Azure Active Directory Domain Services](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/) 99,9%-os SLA-t biztosít. Az Azure AD DS egy globálisan elosztott adatközpontokban üzemeltetett, magasan elérhető szolgáltatás. A replikakészlet az Azure AD DS előzetes funkciója, amely lehetővé teszi a földrajzi katasztrófák helyreállítását, ha az Azure-régió offline állapotba kerül. További információ: a [replika-készletek fogalmai és funkciói Azure Active Directory Domain Services](../active-directory-domain-services/concepts-replica-sets.md) további információért.  

### <a name="azure-dns"></a>Azure DNS

A [Azure DNS](https://azure.microsoft.com/support/legal/sla/dns/v1_1/) 100%-os SLA-t biztosít. A HDInsight a tartománynév-feloldás különböző helyein Azure DNSt használ.

## <a name="multi-region-cost-and-complexity-optimizations"></a>Többrégiós díjak és összetettségi optimalizálás

Az üzletmenet folytonosságának javítása a régiók közötti magas rendelkezésre állású vész-helyreállítási folyamatok esetében nagyobb komplexitást és magasabb költségeket igénylő építészeti kialakításokra van szükség. A következő táblázatokban részletesen ismertetünk néhány olyan technikai területet, amely növelheti a teljes tulajdonlási költségeket.

### <a name="cost-optimizations"></a>Költséghatékonysági optimalizálás

|Terület|A költségcsökkentés oka|Optimalizálási stratégiák|
|----|------------------------|-----------------------|
|Adattárolás|Elsődleges adattábla és táblák másolása másodlagos régióban|Csak a kurátori adatforrások replikálása|
|Kimenő adatforgalom|A kimenő régiók közötti adatátvitel díja. A sávszélesség díjszabásával kapcsolatos irányelvek áttekintése|Csak a kisegítő mennyiségeket replikálja a régió kimenő forgalmának csökkentése érdekében|
|Fürt számítási felszámítása|További HDInsight-fürt/s a másodlagos régióban|Az elsődleges meghibásodás után a másodlagos számítások az automatikus parancsfájlok használatával telepíthetők. Használja az automatikus skálázást a másodlagos fürt méretének minimálisra tartásához. Használjon olcsóbb VM SKU-t. Hozzon létre formátumú másodlagos zónák azokon a régiókban, amelyekben a VM SKU-ból diszkontálható.|
|Hitelesítés |A másodlagos régióban a többfelhasználós forgatókönyvek további Azure AD DS-telepítéseket is felmerülhetnek|Ne használjon többfelhasználós telepítést a másodlagos régióban.|

### <a name="complexity-optimizations"></a>Összetettségi optimalizálás

|Terület|Összetettségi eszkaláció oka|Optimalizálási stratégiák|
|----|------------------------|-----------------------|
|Írási minták olvasása |Az elsődleges és a másodlagos olvasási és írási műveletek engedélyezésének megkövetelése |A másodlagos csak olvashatóként való kialakításának megtervezése|
|Nulla RPO & RTO |Nulla adatvesztés megkövetelése (RPO = 0) és nulla állásidő (RTO = 0) |Tervezze meg a RPO és a RTO, hogy csökkentse a feladatátvételt igénylő összetevők számát.|
|Üzleti funkciók |Az elsődleges másodlagos üzleti funkciók teljes körű működésének megkövetelése |Értékelje ki, hogy futtatható-e a másodlagos üzleti funkciók minimális kritikus részhalmazával.|
|Kapcsolatok |Az összes felsőbb rétegbeli és alsóbb rétegbeli rendszer megkövetelése az elsődlegestől a másodlagoshoz való kapcsolódáshoz|Korlátozza a másodlagos kapcsolatot egy nem megfelelő minimális kritikus részhalmazra.|

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a cikkben tárgyalt elemekről, tekintse meg a következőt:

* [Az Azure HDInsight üzletmenet-folytonossági architektúrái](./hdinsight-business-continuity-architecture.md)
* [Azure HDInsight, magasan elérhető megoldás architektúra-esettanulmány](./hdinsight-high-availability-case-study.md)
* [Mi a Apache Hive és a HiveQL az Azure HDInsight?](./hadoop/hdinsight-use-hive.md)
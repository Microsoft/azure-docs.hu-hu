---
title: Apache Phoenix a HDInsight-ben – Azure HDInsight
description: A Apache Phoenix áttekintése
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: cb5230ae42703d19726fb8ea0d6c88aa70e589a8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864142"
---
# <a name="apache-phoenix-in-azure-hdinsight"></a>Apache Phoenix az Azure HDInsight

A [Apache Phoenix](https://phoenix.apache.org/) egy [Apache HBase](hbase/apache-hbase-overview.md)-ra épülő, nyílt forráskódú, nagymértékben párhuzamos, összehasonlítható adatbázis-réteg. A Phoenix használatával SQL-szerű lekérdezéseket használhat a HBase-on keresztül. A Phoenix JDBC-illesztőprogramokat használ, hogy lehetővé tegye a felhasználók számára az SQL-táblák,-indexek,-nézetek és-folyamatok létrehozását, törlését, megváltoztatását, valamint a upsert-sorok egyenkénti és tömeges használatát. A Phoenix noSQL natív fordítást használ ahelyett, hogy a MapReduce használatával lefordítsa a lekérdezéseket, és lehetővé teszi a kis késleltetésű alkalmazások létrehozását a HBase-on. A Phoenix felhasználja az ügyfél által megadott kód futtatásának támogatását a kiszolgáló címterület részeként, és a kód végrehajtásával az adott helyen található. Ez a megközelítés lekicsinyíti az ügyfél/kiszolgáló adatátvitelt.

Apache Phoenix megnyit big data lekérdezéseket a nem fejlesztőknek, akik a programozás helyett SQL-szerű szintaxist használhatnak. A Phoenix kifejezetten HBase-re van optimalizálva, eltérően más eszközökkel, mint például a [Apache Hive](hadoop/hdinsight-use-hive.md) és az Apache Spark SQL. A fejlesztők számára a nagy teljesítményű lekérdezéseket sokkal kevesebb kóddal kell írni.

SQL-lekérdezés elküldésekor a Phoenix lefordítja a lekérdezést, hogy HBase a natív hívásokat, és párhuzamosan futtatja az ellenőrzést (vagy a tervet) az optimalizáláshoz. Ez az absztrakciós réteg felszabadítja a fejlesztőt a MapReduce feladatok írásához, hogy az üzleti logikára és az alkalmazás munkafolyamatára koncentráljon a Phoenix big data Storage-ban.

## <a name="query-performance-optimization-and-other-features"></a>A teljesítmény optimalizálása és egyéb funkciók lekérdezése

Apache Phoenix számos teljesítménynövelő fejlesztést és szolgáltatást biztosít a lekérdezések HBase.

### <a name="secondary-indexes"></a>Másodlagos indexek

A HBase egyetlen indextel rendelkezik, amely az elsődleges lexicographically van rendezve. Ezek a rekordok csak a sor kulcsán keresztül érhetők el. Ha a rekordokat a sor kulcsán kívül bármely más oszlophoz fér hozzá, az összes adatokat be kell szkennelni a szükséges szűrő alkalmazása közben. Másodlagos indexben az Indexelt oszlopok vagy kifejezések egy másik sorban lévő kulcsot alkotnak, amely lehetővé teszi a kereséseket és a tartományon belüli vizsgálatokat az adott indexen.

Hozzon létre egy másodlagos indexet a `CREATE INDEX` paranccsal:

```sql
CREATE INDEX ix_purchasetype on SALTEDWEBLOGS (purchasetype, transactiondate) INCLUDE (bookname, quantity);
```

Ez a megközelítés jelentős teljesítménybeli növekedést eredményezhet az egyetlen indexelt lekérdezések végrehajtásakor. Ez a típusú másodlagos index egy leképező **index**, amely a lekérdezésben szereplő összes oszlopot tartalmazza. Ezért a tábla keresése nem kötelező, és az index megfelel a teljes lekérdezésnek.

### <a name="views"></a>Megjelenítések

A Phoenix-nézetek segítségével leküzdheti a HBase korlátozásokat, ahol a teljesítmény a 100-nál több fizikai tábla létrehozásakor csökken. A Phoenix-nézetek lehetővé teszik több *virtuális tábla* számára, hogy megosszák egy mögöttes fizikai HBase-táblázatot.

A Phoenix nézet létrehozása hasonló a szabványos SQL View szintaxis használatához. Az egyik különbség az, hogy a nézet oszlopait az alaptáblától örökölt oszlopok mellett is meghatározhatja. Emellett új `KeyValue` oszlopokat is hozzáadhat.

Például itt látható egy nevű fizikai tábla `product_metrics` a következő definícióval:

```sql
CREATE  TABLE product_metrics (
    metric_type CHAR(1),
    created_by VARCHAR,
    created_date DATE,
    metric_id INTEGER
    CONSTRAINT pk PRIMARY KEY (metric_type, created_by, created_date, metric_id));
```

Hozzon létre egy nézetet a táblázat felett, további oszlopokkal:

```sql
CREATE VIEW mobile_product_metrics (carrier VARCHAR, dropped_calls BIGINT) AS
SELECT * FROM product_metrics
WHERE metric_type = 'm';
```

Ha később további oszlopokat szeretne hozzáadni, használja az `ALTER VIEW` utasítást.

### <a name="skip-scan"></a>Vizsgálat kihagyása

A kihagyott vizsgálat egy összetett index egy vagy több oszlopát használja a különböző értékek megkereséséhez. A tartomány-ellenőrzéstől eltérően a kihagyás a vizsgálaton belüli beolvasást valósít meg, ami [jobb teljesítményt](https://phoenix.apache.org/performance.html#Skip-Scan)eredményez. A vizsgálat során az első egyező értéket a rendszer kihagyja az indextel együtt, amíg a következő érték nem található.

A kihagyási vizsgálat a `SEEK_NEXT_USING_HINT` HBase szűrő enumerálását használja. `SEEK_NEXT_USING_HINT`A használatával a vizsgálat kihagyása nyomon követi, hogy a rendszer mely kulcsokat vagy tartományokat használja az egyes oszlopokban. A kiugrási vizsgálat ezután a szűrő kiértékelése során átadott kulcsot fogad el, és meghatározza, hogy az egyik kombináció-e. Ha nem, a kiugrási vizsgálat kiértékeli a következő legmagasabb kulcsot a ugráshoz.

### <a name="transactions"></a>Tranzakciók

Míg a HBase adatsorokat biztosít, a Phoenix integrálva van a [tephra](https://tephra.io/) -szel, és lehetővé teszi a többsoros és a táblák közötti tranzakciók támogatását a teljes [sav](https://en.wikipedia.org/wiki/ACID) -szemantika használatával.

Csakúgy, mint a hagyományos SQL-tranzakciók esetében, a Phoenix Transaction Manageren keresztül biztosított tranzakciók lehetővé teszik, hogy a rendszer sikeresen upserted egy atomi adategységet, Visszagörgeti a tranzakciót, ha a upsert művelet meghiúsul a tranzakciót engedélyező táblákon.

A Phoenix-tranzakciók engedélyezéséhez tekintse meg a [Apache Phoenix tranzakció dokumentációját](https://phoenix.apache.org/transactions.html).

Ha olyan új táblát szeretne létrehozni, amelyeken engedélyezve vannak a tranzakciók, állítsa a `TRANSACTIONAL` tulajdonságot `true` egy `CREATE` utasításban:

```sql
CREATE TABLE my_table (k BIGINT PRIMARY KEY, v VARCHAR) TRANSACTIONAL=true;
```

Egy meglévő tábla tranzakciós állapotának módosításához használja ugyanazt a tulajdonságot egy `ALTER` utasításban:

```sql
ALTER TABLE my_other_table SET TRANSACTIONAL=true;
```

> [!NOTE]  
> Tranzakciós tábla nem állítható vissza nem tranzakciós értékre.

### <a name="salted-tables"></a>Sózott táblák

A *régió-kiszolgáló hotspotting* akkor fordulhat elő, ha szekvenciális kulccsal rendelkező rekordokat ír a HBase. Bár előfordulhat, hogy a fürtben több régió-kiszolgáló is található, az írások mindegyike csak egyet vesz igénybe. Ez a koncentráció hozza létre a hotspotting-problémát, ha az írási munkaterhelés helyett az összes rendelkezésre álló régió-kiszolgáló között oszlanak el az adatok, csak eggyel kezeli a terhelést. Mivel minden régió rendelkezik egy előre meghatározott maximális mérettel, amikor egy régió eléri ezt a méretkorlátot, két kis régióra oszlik. Ebben az esetben az egyik új régió minden új rekordot átvesz az új pontra.

A probléma megoldásához és a jobb teljesítmény eléréséhez, a táblák előre felosztásához, hogy az összes régió-kiszolgáló egyformán legyen használatban. A Phoenix *sós táblákat* biztosít, és transzparens módon hozzáadja a sós bájtot egy adott tábla soraihoz. A tábla előre felosztva van a só-bájtok határain, hogy a rendszer a tábla kezdeti fázisában egyenlő terhelési eloszlást biztosítson a régió-kiszolgálók között. Ez a megközelítés az összes rendelkezésre álló régió-kiszolgáló között elosztja az írási munkaterhelést, javítja az írási és olvasási teljesítményt. Egy tábla kiválasztásához a tábla `SALT_BUCKETS` létrehozásakor határozza meg a Table tulajdonságot:

```sql
CREATE TABLE Saltedweblogs (
    transactionid varchar(500) Primary Key,
    transactiondate Date NULL,
    customerid varchar(50) NULL,
    bookid varchar(50) NULL,
    purchasetype varchar(50) NULL,
    orderid varchar(50) NULL,
    bookname varchar(50) NULL,
    categoryname varchar(50) NULL,
    invoicenumber varchar(50) NULL,
    invoicestatus varchar(50) NULL,
    city varchar(50) NULL,
    state varchar(50) NULL,
    paymentamount DOUBLE NULL,
    quantity INTEGER NULL,
    shippingamount DOUBLE NULL) SALT_BUCKETS=4;
```

## <a name="enable-and-tune-phoenix-with-apache-ambari"></a>A Phoenix engedélyezése és finomhangolása az Apache Ambari

An méretű HDInsight HBase-fürt tartalmazza a [Ambari felhasználói felületét](hdinsight-hadoop-manage-ambari.md) , amely a konfiguráció módosítását teszi elérhetővé.

1. A Phoenix engedélyezéséhez vagy letiltásához, valamint a Phoenix lekérdezési időtúllépési beállításainak szabályozásához jelentkezzen be a Ambari webes felhasználói felületére ( `https://YOUR_CLUSTER_NAME.azurehdinsight.net` ) a Hadoop felhasználói hitelesítő adataival.

2. A bal oldali menüben válassza a **HBase** lehetőséget, majd válassza a **konfigurációk** lapot.

    :::image type="content" source="./media/hdinsight-phoenix-in-hdinsight/ambari-hbase-config1.png" alt-text="Apache Ambari HBase-konfigurációk":::

3. Keresse meg a **Phoenix SQL** konfigurációs szakaszt a Phoenix engedélyezéséhez vagy letiltásához, és állítsa be a lekérdezés időtúllépését.

    :::image type="content" source="./media/hdinsight-phoenix-in-hdinsight/apache-ambari-phoenix.png" alt-text="A Ambari Phoenix SQL konfigurációs szakasza":::

## <a name="see-also"></a>Lásd még

* [Apache Phoenix használata Linux-alapú HBase-fürtökkel a HDInsight-ben](hbase/apache-hbase-query-with-phoenix.md)

* [Az Apache Zeppelin használatával Apache Phoenix lekérdezéseket futtathat az Apache HBase az Azure HDInsight](./hbase/apache-hbase-phoenix-zeppelin.md)

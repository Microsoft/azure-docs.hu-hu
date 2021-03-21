---
title: Apache Hive replikáció használata az Azure HDInsight-fürtökben
description: Ismerje meg, hogyan replikálhatja a HDInsight-fürtökben a kaptár replikációját a Hive-metaadattár és a Azure Data Lake Storage Gen 2 adatközpont replikálásához.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 2e57b7d8121ac0bd6fc9cb693750c41a0ef6a5bc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101733057"
---
# <a name="how-to-use-apache-hive-replication-in-azure-hdinsight-clusters"></a>Apache Hive replikáció használata az Azure HDInsight-fürtökben

Az adatbázisok és a raktárak kontextusában a replikáció az entitások egyik tárházból egy másikba való másolásának folyamata. A Duplikálás a teljes adatbázisra vagy egy kisebb szintre, például egy táblára vagy partícióra is alkalmazható. A cél az, hogy egy replikát kell megváltoztatnia, amikor megváltozik az alapentitás. Apache Hive replikációja a vész-helyreállításra koncentrál, és az elsődleges – másolási replikálást is biztosítja. A HDInsight-fürtökben a struktúra replikálásával a Hive-metaadattár és a hozzá tartozó mögöttes adattó a Azure Data Lake Storage Gen2-on való egyirányú replikálására is használható.  

A struktúra replikációja az évek során újabb verziókkal bővült, ami jobb funkciókat biztosít, és gyorsabb és kevesebb erőforrás-igényű. Ebben a cikkben a kaptár-replikációt (Replv2) tárgyaljuk, amelyet a HDInsight 3,6 és a HDInsight 4,0 típusú fürtök egyaránt támogatnak.

## <a name="advantages-of-replv2"></a>A replv2 előnyei

A [kaptár ReplicationV2](https://cwiki.apache.org/confluence/display/Hive/HiveReplicationv2Development) (más néven Replv2) az alábbi előnyökkel jár a struktúra [IMPORTÁLÁSi-exportálását](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ImportExport)használó kaptár-replikáció első verziójában:

- Eseményvezérelt növekményes replikáció
- Időponthoz való replikálás  
- Kisebb sávszélességre vonatkozó követelmények  
- A köztes másolatok számának csökkentése  
- A replikációs állapot karbantartva
- Korlátozott replikáció  
- Sugaras modell támogatása  
- SAVAS táblák támogatása (HDInsight 4,0)

## <a name="replication-phases"></a>Replikálási fázisok

A kaptár eseményvezérelt replikációja az elsődleges és a másodlagos fürtök között van konfigurálva. Ez a replikálás két különböző fázisból áll: a rendszerindítás és a növekményes futtatások.

### <a name="bootstrapping"></a>Bootstrapping

Az indítás célja, hogy egyszer fusson az adatbázisok alapállapotának replikálásához az elsődlegesről a másodlagosra. Ha szükséges, konfigurálhatja a rendszerindítást úgy, hogy a megcélozt adatbázisban lévő táblák részhalmazát is tartalmazza, ahol engedélyezni kell a replikációt.

### <a name="incremental-runs"></a>Növekményes futtatások

A rendszerindítás után a növekményes futtatások automatikusan létrejönnek az elsődleges fürtön, és a növekményes futtatások során generált események a másodlagos fürtön is visszakerülnek. Amikor a másodlagos fürt felveszi az elsődleges fürtöt, a másodlagos az elsődleges eseményekkel konzisztens lesz.

## <a name="replication-commands"></a>Replikációs parancsok

A kaptár REPL-parancsokat –, és – az `DUMP` `LOAD` események áramlását hangolja össze `STATUS` . A `DUMP` parancs létrehoz egy helyi naplót az összes DDL/DML eseményről az elsődleges fürtön. A `LOAD` parancs egy olyan megközelítés, amellyel a rendszer a kinyert replikálási memóriaképbe naplózza a metaadatokat és az adatokat, és a cél fürtön hajtja végre. A `STATUS` parancs a célként megadott fürtről futtatja a legutóbbi replikációs betöltés sikeres replikálásának legújabb eseményazonosító megadását.

### <a name="set-replication-source"></a>Replikációs forrás beállítása

A replikálás megkezdése előtt győződjön meg arról, hogy a replikálni kívánt adatbázis replikációs forrásként van beállítva. A `DESC DATABASE EXTENDED <db_name>` paranccsal meghatározhatja, hogy a paraméter `repl.source.for` be van-e állítva a szabályzat nevével.

Ha a házirend be van ütemezve `repl.source.for` , és a paraméter nincs beállítva, először be kell állítania ezt a paramétert a következővel: `ALTER DATABASE <db_name> SET DBPROPERTIES ('repl.source.for'='<policy_name>')` .

```sql
ALTER DATABASE tpcds_orc SET DBPROPERTIES ('repl.source.for'='replpolicy1') 
```

### <a name="dump-metadata-to-the-data-lake"></a>Metaadatok kiírása a adat-tóhoz

A `REPL DUMP [database name]. => location / event_id` parancs a rendszerindítási fázisban a releváns metaadatok Azure Data Lake Storage Gen2 való kiírására szolgál. A `event_id` meghatározza azt a minimális eseményt, amelybe a releváns metaadatok bekerültek Azure Data Lake Storage Gen2ba. 
 
```sql
repl dump tpcds_orc; 
```
Példa a kimenetre:

| dump_dir|last_repl_id
|-|-|
|/tmp/hive/repl/38896729-67d5-41b2-90dc-46eeed4c5dd0|2925|

### <a name="load-data-to-the-target-cluster"></a>Adatgyűjtés a célként megadott fürthöz

A `REPL LOAD [database name] FROM [ location ] { WITH ( ‘key1’=‘value1’{, ‘key2’=‘value2’} ) }` parancs használatával a rendszer a replikációhoz és a replikálás növekményes fázisaihoz is betöltheti az adathalmazt. A `[database name]` nem egyezhet meg a forrással vagy más névvel a célként megadott fürtön. A a `[location]` korábbi parancs kimenetének helyét jelöli `REPL DUMP` . Ez azt jelenti, hogy a célként megadott fürtnek képesnek kell lennie kommunikálni a forrás-fürttel. A `WITH` záradék elsődlegesen a célként megadott fürt újraindításának megakadályozása érdekében lett hozzáadva, ami lehetővé teszi a replikálást.

```sql
repl load tpcds_orc from '/tmp/hive/repl/38896729-67d5-41b2-90dc-46eeed4c5dd0'; 
```

### <a name="output-the-last-replicated-event-id"></a>A legutóbbi replikált esemény AZONOSÍTÓjának kimenete

A `REPL STATUS [database name]` parancs a célként megadott fürtökön fut, és a legutóbb replikált kimenetet adja vissza `event_id` . A parancs azt is lehetővé teszi, hogy a felhasználók tudják, milyen állapotban vannak replikálva a célként megadott fürt. A parancs kimenetével a következő parancsot hozhatja létre `REPL DUMP` növekményes replikáláshoz.

```sql
repl status tpcds_orc;
```

Példa a kimenetre:

|last_repl_id|
|-|
|2925|

### <a name="dump-relevant-data-and-metadata-to-the-data-lake"></a>A kapcsolódó adatok és metaadatok kiírása a adat-tóhoz

A `REPL DUMP [database name] FROM [event-id] { TO [event-id] } { LIMIT [number of events] }` parancs a releváns metaadatok és adatok Azure Data Lake Storageba való kiírására szolgál. Ez a parancs a növekményes fázisban használatos, és a forrás-tárházon fut. A `FROM [event-id]` növekményes fázishoz szükséges, és a értéke a `event-id` cél raktáron futtatott parancs futtatásával származtatható `REPL STATUS [database name]` .

```sql
repl dump tpcds_orc from 2925;
```

Példa a kimenetre:

|dump_dir|last_repl_id|
|-|-|
| /tmp/hive/repl/38896729-67d5-41b2-90dc-466466agadd0 | 2960|

## <a name="hive-replication-process"></a>Struktúra replikálási folyamata

A következő lépések a kaptár replikálási folyamata során megjelenő szekvenciális események.

1. Győződjön meg arról, hogy a replikálni kívánt táblák egy adott házirend replikációs forrásaként vannak beállítva.

1. A `REPL_DUMP` parancs a társított korlátozásokkal (például az adatbázis nevével, az eseményazonosító tartományával és Azure Data Lake Storage Gen2 tárolási URL-címmel) van kiadva az elsődleges fürthöz.

1. A rendszer szerializálja az összes követett eseményt a metaadattár a legújabbra. A rendszer a (z) által megadott URL-címen tárolja a memóriaképet az elsődleges fürt Azure Data Lake Storage Gen2 Storage-fiókjában `REPL_DUMP` .  

1. Az elsődleges fürt megőrzi a replikálási metaadatokat az elsődleges fürt Azure Data Lake Storage Gen2 tárolóján. Az elérési út a Ambari struktúrájának konfigurációs felhasználói felületén állítható be. A folyamat biztosítja a metaadatok tárolási helyét és a legújabb követett DML/DDL-esemény AZONOSÍTÓját.

1. A `REPL_LOAD` parancs a másodlagos fürtből lett kiállítva. A parancs a 3. lépésben megadott elérési útra mutat.

1. A másodlagos fürt beolvassa a metaadat-fájlt a 3. lépésben létrehozott követett eseményekkel. Győződjön meg arról, hogy a másodlagos fürtnek van hálózati kapcsolata azon elsődleges fürt Azure Data Lake Storage Gen2 tárolójával, amelyen a nyomon követett események `REPL_DUMP` tárolódnak.  

1. A másodlagos fürt elindítja az elosztott másolás ( `DistCP` ) számítását.

1. A másodlagos fürt adatait az elsődleges fürt tárterületéről másolja.  

1. A másodlagos fürt metaadattár frissül.  

1. Az utolsó követett eseményazonosító az elsődleges metaadattár tárolódik.

A növekményes replikáció ugyanezt a folyamatot követi, és a legutolsó replikált eseményazonosító bemenetként kell megadni. Ez a legutóbbi replikálási esemény óta növekményes másolást eredményez. A növekményes replikációk általában automatizáltak, előre meghatározott gyakorisággal a szükséges helyreállítási pontok célkitűzéseinek (RPO) eléréséhez.

:::image type="content" source="media/apache-hive-replication/hive-replication-diagram.png" alt-text="Struktúra replikációs diagramja":::

## <a name="replication-patterns"></a>Replikációs minták  

A replikációt általában egyirányú módon konfigurálták az elsődleges és a másodlagos között, ahol az elsődleges a kérések olvasására és írására szolgál. A másodlagos fürt csak olvasási kérelmeket biztosít. Az írások engedélyezve vannak a másodlagos esetben, ha vészhelyzet van, de a visszirányú replikálást vissza kell állítani az elsődlegesre.

:::image type="content" source="media/apache-hive-replication/replication-pattern.png" alt-text="Struktúra replikálási mintája":::

Számos mintázat használható a kaptárak replikálásához, beleértve az elsődleges – másodlagos, a hub-és a küllős, valamint a továbbítási funkciókat.

A HDInsight aktív elsődleges – a készenléti másodlagos egy általános üzletmenet-folytonossági és vész-helyreállítási (BCDR) minta, és a HiveReplicationV2 ezt a mintát a Hadoop-társítással rendelkező, regionálisan elkülönített HDInsight VNet-fürtökkel használhatja. A replikációs automatizálási parancsfájlok futtatásához a fürtökhöz társított közös virtuális gép is használható. A lehetséges HDInsight BCDR-mintákkal kapcsolatos további információkért tekintse meg a [HDInsight üzletmenet-folytonossági dokumentációját](../hdinsight-business-continuity.md).  

### <a name="hive-replication-with-enterprise-security-package"></a>Struktúra replikációja Enterprise Security Package  

Azokban az esetekben, amikor a HDInsight Hadoop-Enterprise Security Package fürtökön tervezték meg a kaptár-replikációt, a metaadattár és a Azure Active Directory Domain Services (AD DS) replikációs mechanizmusait kell figyelembe vennie.  

Az Azure AD DS-replika funkció használatával több régióban is létrehozhat több Azure AD DS-replikát az Azure AD-bérlőn belül. Minden egyes másodpéldányt meg kell adni a megfelelő régióiban található HDInsight-virtuális hálózatok. Ebben a konfigurációban az Azure AD DS módosításait, például a konfigurációt, a felhasználói identitást és a hitelesítő adatokat, a csoportokat, a csoportházirend-objektumokat, a számítógép-objektumokat és más módosításokat a felügyelt tartomány összes másodpéldányára alkalmazza az Azure AD DS-replikáció használatával.
  
A Ranger-szabályzatok rendszeres biztonsági mentését és az elsődlegesről a másodlagosra való replikálását a Ranger Import-Export funkció segítségével végezheti el. Dönthet úgy, hogy a másodlagos fürtön megvalósítani kívánt engedélyek szintjétől függően replikálja a Ranger-szabályzatok egy részhalmazát.  

## <a name="sample-code"></a>Mintakód  

A következő kódrészlet egy példát mutat be a rendszerindítási és növekményes replikáció megvalósítására egy nevű minta táblán `tpcds_orc` .  

1. Állítsa be a táblázatot egy replikációs házirend forrásaként.

   ```sql
   ALTER DATABASE tpcds_orc SET DBPROPERTIES ('repl.source.   for'='replpolicy1');
   ```

1. Rendszertöltő memóriakép az elsődleges fürtön.

   ```sql
   repl dump tpcds_orc with ('hive.repl.rootdir'='/tmpag/hiveag/replag'); 
   ```
   
   Példa a kimenetre:
   
   |dump_dir|last_repl_id|
   |-|-|
   |/tmpag/hiveag/replag/675d1bea-2361-4cad-bcbf-8680d305a27a|2925|
 
1. Rendszertöltő terhelés a másodlagos fürtben. 

   ```sql
   repl load tpcds_orc from '/tmpag/hiveag/replag 675d1bea-2361-4cad-bcbf-8680d305a27a'; 
   ```

1. Vizsgálja meg az `REPL` állapotot a másodlagos fürtön.

   ```sql
   repl status tpcds_orc; 
   ```
 
   |last_repl_id|
   |-|
   |2925|

1. Növekményes memóriakép az elsődleges fürtön.

   ```sql
   repl dump tpcds_orc from 2925 with ('hive.repl.rootdir'='/tmpag/hiveag/ replag');
   ```

   Példa a kimenetre:
   
   |dump_dir | last_repl_id|
   |-|-|
   |/tmpag/hiveag/replag/31177ff7-a40f-4f67-a613-3b64ebe3bb31|2960|

1. Növekményes terhelés a másodlagos fürtben.  

   ```sql
   repl load tpcds_orc from '/tmpag/hiveag/replag/31177ff7-a40f-4f67-a613-3b64ebe3bb31';
   ```

1. `REPL`Állapot keresése a másodlagos fürtben.

   ```sql
   repl status tpcds_orc;
   ```

   |last_repl_id|
   |-|
   |2960|

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a cikkben tárgyalt elemekről, tekintse meg a következőt:

- [Az Azure HDInsight üzletmenet folytonossága](../hdinsight-business-continuity.md)
- [Az Azure HDInsight üzletmenet-folytonossági architektúrái](../hdinsight-business-continuity-architecture.md)
- [Azure HDInsight, magasan elérhető megoldás architektúra-esettanulmány](../hdinsight-high-availability-case-study.md)
- [Mi a Apache Hive és a HiveQL az Azure HDInsight?](../hadoop/hdinsight-use-hive.md)
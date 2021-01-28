---
title: Phoenix-teljesítmény az Azure HDInsight
description: Ajánlott eljárások az Azure HDInsight-fürtök Apache Phoenix teljesítményének optimalizálásához
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/27/2019
ms.openlocfilehash: ffba3b986b35c375d0404d9d2bae5af79f93c54f
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944799"
---
# <a name="apache-phoenix-performance-best-practices"></a>Az Apache Phoenix teljesítményével kapcsolatos ajánlott eljárások

[Apache Phoenix](https://phoenix.apache.org/) teljesítményének legfontosabb aspektusa az alapul szolgáló [Apache-HBase](https://hbase.apache.org/)optimalizálása. A Phoenix létrehoz egy, a HBase tetején lévő, az SQL-lekérdezéseket HBase-műveletekre, például vizsgálatokra konvertáló viszonyítási adatmodellt. A tábla sémájának kialakítása, a mezők kiválasztása és sorrendje az elsődleges kulcsban, valamint az indexek használata mind a Phoenix teljesítményét befolyásolja.

## <a name="table-schema-design"></a>Táblázatos séma kialakítása

Amikor létrehoz egy táblát a Phoenixben, a tábla egy HBase-táblában tárolódik. A HBase tábla olyan oszlopokat (családokat) tartalmazó csoportokat tartalmaz, amelyek együtt érhetők el. A Phoenix tábla egyik sora a HBase tábla egy sora, ahol minden egyes sor egy vagy több oszlophoz társított, verziószámmal rendelkező cellából áll. Logikailag egyetlen HBase-sor a kulcs-érték párok gyűjteménye, amelyek mindegyike azonos rowkey értékkel rendelkezik. Vagyis minden kulcs-érték pár rowkey attribútummal rendelkezik, és a rowkey attribútum értéke megegyezik egy adott sorra.

A Phoenix-tábla sémájának kialakítása magában foglalja az elsődleges kulcs kialakítását, az oszlop családjának kialakítását, az egyes oszlopok kialakítását, valamint az adatparticionálás módját.

### <a name="primary-key-design"></a>Elsődleges kulcs kialakítása

A Phoenix egyik táblájában definiált elsődleges kulcs határozza meg, hogy a rendszer hogyan tárolja az adatokat a mögöttes HBase tábla rowkey belül. A HBase-ben az adott sorok elérésének egyetlen módja a rowkey. Emellett a HBase-táblában tárolt adatok a rowkey szerint vannak rendezve. A Phoenix létrehozza a rowkey értéket úgy, hogy összefűzi a sor egyes oszlopainak értékeit az elsődleges kulcsban definiált sorrendben.

A névjegyalbumhoz tartozó tábla például az utónév, a vezetéknév, a telefonszám és a címe, amelyek mindegyike ugyanabban az oszlopban található. A növekvő sorszám alapján meghatározhatja az elsődleges kulcsot:

|rowkey|       address|   telefon| firstName| lastName|
|------|--------------------|--------------|-------------|--------------|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole|
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|RAJI|

Ha azonban gyakran az utónév alapján kérdezi le, hogy az elsődleges kulcs nem jól teljesít, mert minden lekérdezés teljes táblázatos vizsgálatot igényel az összes lastName értékének beolvasásához. Ehelyett megadhat egy elsődleges kulcsot a lastName, a firstName és a társadalombiztosítási szám oszlopokban. Ez az utolsó oszlop egy azonos nevű, például egy apa és egy fia, ugyanazon a címen lévő két lakos egyértelműsítse.

|rowkey|       address|   telefon| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|RAJI| 222 |

Ezzel az új elsődleges kulccsal a Phoenix által generált sorok kulcsai a következőek lesznek:

|rowkey|       address|   telefon| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  Zsolt – Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|RAJI| 222 |

A fenti első sorban a rowkey az alábbi módon jelenik meg:

|rowkey|       kulcs|   érték|
|------|--------------------|---|
|  Dole-John-111|address |1111 San Gabriel Dr.|  
|  Dole-John-111|telefon |1-425-000-0002|  
|  Dole-John-111|firstName |John|  
|  Dole-John-111|lastName |Dole|  
|  Dole-John-111|socialSecurityNum |111|

Ez a rowkey most az adatmásolatot tárolja. Vegye figyelembe az elsődleges kulcsban foglalt oszlopok méretét és számát, mivel ez az érték szerepel az alapul szolgáló HBase tábla minden cellájában.

Továbbá, ha az elsődleges kulcs monoton módon növekvő értékkel rendelkezik, akkor a táblát a *Salt buckets* használatával kell létrehoznia, hogy elkerülje az írási pontok létrehozását – lásd: [partíciós adatok](#partition-data).

### <a name="column-family-design"></a>Oszlop családjának kialakítása

Ha egyes oszlopokat gyakrabban használ, mint másokat, hozzon létre több oszlopos családot, hogy elkülönítse a gyakran használt oszlopokat a ritkán használt oszlopokból.

Továbbá, ha bizonyos oszlopok általában együtt férnek hozzá, az oszlopokat ugyanabba az oszlopba helyezi.

### <a name="column-design"></a>Oszlop kialakítása

* A nagyméretű oszlopok I/O-költségei miatt a VARCHAR-oszlopok körülbelül 1 MB-ot is megtartanak. A lekérdezések feldolgozásakor a HBase teljes egészében megkezdi a cellák megadását, mielőtt elküldené őket az ügyfélnek, és az ügyfél teljes egészében fogadja őket, mielőtt kiadná őket az alkalmazás kódjába.
* Az oszlopok értékeinek tárolása kompakt formátummal, például protopuf, Avro, msgpack vagy BSON. A JSON nem ajánlott, mert nagyobb.
* A késés és az I/O-költségek csökkentése érdekében érdemes lehet az adatok tömörítését a tárterület előtt.

### <a name="partition-data"></a>Partícióadatok

A Phoenix lehetővé teszi, hogy szabályozza a régiók számát, ahol az adatait forgalmazzák, ami jelentősen növelheti az írási/olvasási teljesítményt. Phoenix-tábla létrehozásakor megadhatja az adatait, vagy megoszthatja azokat előre.

Egy tábla a létrehozás során történő megadásához határozza meg a sós gyűjtők számát:

```sql
CREATE TABLE CONTACTS (...) SALT_BUCKETS = 16
```

Ez a sós feldarabolja a táblázatot az elsődleges kulcsok értékei mellett, automatikusan kiválasztja az értékeket. 

A tábla felosztásának szabályozásához megadhatja a tábla előzetes felosztását úgy, hogy megadja a tartomány értékeit, amelyeken a felosztás történik. Például egy három régióra bontott tábla létrehozásához:

```sql
CREATE TABLE CONTACTS (...) SPLIT ON ('CS','EU','NA')
```

## <a name="index-design"></a>Index kialakítása

A Phoenix index egy HBase tábla, amely az indexelt táblából származó adatok egy vagy több példányát tárolja. Az index javítja a különböző típusú lekérdezések teljesítményét.

Ha több index van meghatározva, majd lekérdez egy táblát, a Phoenix automatikusan kiválasztja a lekérdezés legjobb indexét. Az elsődleges index automatikusan létrejön a kiválasztott elsődleges kulcsok alapján.

A várt lekérdezések esetében másodlagos indexeket is létrehozhat az oszlopok megadásával.

Az indexek tervezésekor:

* Csak a szükséges indexeket hozza létre.
* Az indexek számának korlátozása a gyakran frissített táblákon. A táblák frissítései a főtáblára és az index táblákra is lefordítják az írásokat.

## <a name="create-secondary-indexes"></a>Másodlagos indexek létrehozása

A másodlagos indexek javíthatják az olvasási teljesítményt, ha megfordítják, hogy mi lenne a teljes táblázatos vizsgálat egy pontra történő kereséskor a tárterület és az írási sebesség alapján. A másodlagos indexek a tábla létrehozása után hozzáadhatók vagy eltávolíthatók, és nem szükséges módosítani a meglévő lekérdezéseket – a lekérdezések csak gyorsabban futnak. Az igényektől függően érdemes lehet a kezelt indexeket, a funkcionális indexeket vagy mindkettőt létrehozni.

### <a name="use-covered-indexes"></a>Kezelt indexek használata

A kezelt indexek olyan indexek, amelyek az indexelt értékeken kívül a sorból származó adatokkal is rendelkeznek. Miután megtalálta a kívánt tárgymutató-bejegyzést, nincs szükség az elsődleges táblához való hozzáférésre.

A példában szereplő Contact táblában például létrehozhat egy másodlagos indexet a socialSecurityNum oszlopon. Ez a másodlagos index felgyorsítja a socialSecurityNum értékek alapján szűrt lekérdezéseket, de a többi mező értékének beolvasásához egy másikat kell beolvasni a főtáblán.

|rowkey|       address|   telefon| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  Zsolt – Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|RAJI| 222 |

Ha azonban általában a firstName és a lastName alapján szeretné megkeresni a socialSecurityNum, létrehozhat egy kezelt indexet, amely tartalmazza a firstName és a lastName adatokat az index táblában lévő tényleges adatokként:

```sql
CREATE INDEX ssn_idx ON CONTACTS (socialSecurityNum) INCLUDE(firstName, lastName);
```

Ez a kezelt index lehetővé teszi, hogy a következő lekérdezés csak a másodlagos indexet tartalmazó táblából olvassa be az összes adatforrást:

```sql
SELECT socialSecurityNum, firstName, lastName FROM CONTACTS WHERE socialSecurityNum > 100;
```

### <a name="use-functional-indexes"></a>Funkcionális indexek használata

A funkcionális indexek lehetővé teszik, hogy indexet hozzon létre egy tetszőleges kifejezésen, amelyet a lekérdezésekben használni kíván. Ha egy funkcionális index van érvényben, és a lekérdezés ezt a kifejezést használja, az index az adattábla helyett az eredmények lekérésére használható.

Létrehozhat például egy indexet, amely lehetővé teszi a kis-és nagybetűket megkülönböztető keresések használatát egy személy összevont vezetékneve és vezetékneve alapján:

```sql
CREATE INDEX FULLNAME_UPPER_IDX ON "Contacts" (UPPER("firstName"||' '||"lastName"));
```

## <a name="query-design"></a>Lekérdezési terv

A lekérdezési terv főbb szempontjai a következők:

* Ismerje meg a lekérdezési tervet, és ellenőrizze a várt viselkedését.
* Hatékony csatlakozás.

### <a name="understand-the-query-plan"></a>A lekérdezési terv ismertetése

A [az sqlline használata](http://sqlline.sourceforge.net/)-ben használja a magyarázatot, majd az SQL-lekérdezést a Phoenix által végrehajtandó műveletek tervének megtekintéséhez. Győződjön meg arról, hogy a terv:

* Szükség esetén az elsődleges kulcsot használja.
* Az adattábla helyett megfelelő másodlagos indexeket használ.
* A tartomány vizsgálata vagy a vizsgálat kihagyása, ha lehetséges, a táblázat vizsgálata helyett.

#### <a name="plan-examples"></a>Példák tervezése

Tegyük fel például, hogy rendelkezik egy REPÜLŐJÁRATok nevű táblázattal, amely a repülési késleltetési adatokat tárolja.

Az összes olyan járat kiválasztásához, amely egy airlineid rendelkezik `19805` , ahol a airlineid olyan mező, amely nem szerepel az elsődleges kulcsban vagy bármely indexben:

```sql
select * from "FLIGHTS" where airlineid = '19805';
```

Futtassa a magyarázat parancsot az alábbiak szerint:

```sql
explain select * from "FLIGHTS" where airlineid = '19805';
```

A lekérdezési terv így néz ki:

```sql
CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN FULL SCAN OVER FLIGHTS
   SERVER FILTER BY AIRLINEID = '19805'
```

Ebben a csomagban jegyezze fel a teljes VIZSGÁLATra vonatkozó mondatot a JÁRATokon. Ez a kifejezés azt jelzi, hogy a végrehajtás a tábla összes sorának átvizsgálása helyett a hatékonyabb tartomány-ellenőrzés vagy a vizsgálat kihagyása lehetőség használatával történik.

Most tegyük fel, hogy a 2014 január 2-án szeretné lekérdezni a járatokat, `AA` ahol a flightnum nagyobb volt, mint 1. Tegyük fel, hogy az év, hónap, dayofmonth, Carrier és flightnum oszlopok szerepelnek a példában szereplő táblázatban, és az összetett elsődleges kulcsnak mind részét képezik. A lekérdezés a következőképpen néz ki:

```sql
select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;
```

Vizsgáljuk meg a lekérdezés tervét a következővel:

```sql
explain select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;
```

A létrejövő terv a következő:

```sql
CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER FLIGHTS [2014,1,2,'AA',2] - [2014,1,2,'AA',*]
```

A szögletes zárójelben lévő értékek az elsődleges kulcsok értékeinek tartománya. Ebben az esetben a tartomány értékeit a 2014, az 1. és a 2. hónap, a dayofmonth pedig a 2. és a (z) flightnum értékekkel kell megállapítani `*` . A lekérdezési terv megerősíti, hogy az elsődleges kulcs a várt módon van használatban.

Ezután hozzon létre egy indexet az nevű FLIGHTs táblán, `carrier2_idx` amely csak a hordozófrekvencia mezőben szerepel. Ez az index a flightdate, a tailnum, a Origin és a flightnum is tartalmazza olyan kezelt oszlopként, amely az indexben is tárolva van.

```sql
CREATE INDEX carrier2_idx ON FLIGHTS (carrier) INCLUDE(FLIGHTDATE,TAILNUM,ORIGIN,FLIGHTNUM);
```

Tegyük fel, hogy a szállítót a flightdate és a tailnum együtt szeretné beszerezni, ahogy az a következő lekérdezésben is szerepel:

```sql
explain select carrier,flightdate,tailnum from "FLIGHTS" where carrier = 'AA';
```

Ezt az indexet kell használnia:

```sql
CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER CARRIER2_IDX ['AA']
```

A kiértékelési terv eredményei között megjelenő elemek teljes listájáért tekintse meg a [Apache Phoenix hangolási útmutató](https://phoenix.apache.org/tuning_guide.html)a csomagok ismertetése című szakaszát.

### <a name="join-efficiently"></a>Hatékony csatlakozás

Általában érdemes elkerülni az illesztéseket, ha az egyik oldal kicsi, különösen a gyakori lekérdezéseknél.

Ha szükséges, nagy illesztéseket végezhet a mutatóval `/*+ USE_SORT_MERGE_JOIN */` , de a nagyméretű illesztés egy költséges művelet, amely nagy mennyiségű sort használ. Ha a jobb oldali táblák teljes mérete túllépi a rendelkezésre álló memóriát, használja a mutatót `/*+ NO_STAR_JOIN */` .

## <a name="scenarios"></a>Forgatókönyvek

Az alábbi irányelvek néhány gyakori mintát ismertetnek.

### <a name="read-heavy-workloads"></a>Nehezen olvasható számítási feladatok

Az olvasási idejű használatra vonatkozó esetekben győződjön meg róla, hogy indexeket használ. Emellett az olvasási idő terhelésének megtakarításához vegye fontolóra a kezelt indexek létrehozását.

### <a name="write-heavy-workloads"></a>Írási – nagy számítási feladatok

Az olyan írható és nagy számítási feladatokhoz, amelyekben az elsődleges kulcs monoton módon növekszik, hozzon létre Salt vödöreket az írási pontok elkerüléséhez, a további vizsgálatok miatt a teljes olvasási teljesítmény rovására. Emellett, ha a UPSERT nagy mennyiségű rekordot ír, kapcsolja ki az autocommit, és állítsa be a rekordok kötegelt feldolgozását.

### <a name="bulk-deletes"></a>Tömeges törlés

Nagyméretű adatkészletek törlésekor a DELETE lekérdezés kiadása előtt kapcsolja be az autocommit, hogy az ügyfél ne jegyezze fel a Törölt sorok sorait. Az autocommit utasítás megakadályozza, hogy az ügyfél pufferelést hajtson végre a törlés által érintett sorokon, hogy a Phoenix közvetlenül a régió-kiszolgálókon törölje azokat anélkül, hogy az ügyfélnek kellene visszaadnia azokat.

### <a name="immutable-and-append-only"></a>Nem módosítható és csak Hozzáfűzés

Ha a forgatókönyv az adatok integritásának írási sebességét részesíti előnyben, érdemes lehet letiltani a Write-Ahead naplót a táblák létrehozásakor:

```sql
CREATE TABLE CONTACTS (...) DISABLE_WAL=true;
```

Ezen és egyéb beállításokkal kapcsolatos további információkért lásd: [Apache Phoenix nyelvtan](https://phoenix.apache.org/language/index.html#options).

## <a name="next-steps"></a>Következő lépések

* [Apache Phoenix hangolási útmutató](https://phoenix.apache.org/tuning_guide.html)
* [Másodlagos indexek](https://phoenix.apache.org/secondary_indexing.html)

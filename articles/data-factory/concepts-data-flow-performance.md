---
title: Az adatfolyam teljesítményének és hangolási útmutatójának leképezése
description: Ismerje meg azokat a főbb tényezőket, amelyek hatással vannak a Azure Data Factory adatforgalmának teljesítményére.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.custom: seo-lt-2019
ms.date: 03/15/2021
ms.openlocfilehash: dd5b857c274e757f70920f244786df61c2770085
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103561685"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Adatfolyamatok teljesítményének és hangolási útmutatójának leképezése

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Azure Data Factory adatforgalmának leképezése kód nélküli felületet biztosít az adatátalakítások nagy léptékű kialakításához és futtatásához. Ha nem ismeri a leképezési adatfolyamatokat, tekintse meg a [leképezési adatfolyam áttekintése](concepts-data-flow-overview.md)című témakört. Ez a cikk az adatfolyamatok hangolásának és optimalizálásának különböző módjait mutatja be, hogy azok megfeleljenek a teljesítményre vonatkozó referenciaértékeknek.

Tekintse meg az alábbi videót, amelyből megtudhatja, hogyan alakíthat át adatfolyamatokat az adatáramlásokkal.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rNxM]

## <a name="testing-data-flow-logic"></a>Adatfolyam-logika tesztelése

Az ADF UX-ből származó adatfolyamatok tervezésekor és tesztelésekor a hibakeresési mód lehetővé teszi az interaktív tesztelést egy élő Spark-fürtön. Ez lehetővé teszi az adatelemzést és az adatfolyamatok végrehajtását anélkül, hogy a fürt bemelegítésére kellene várnia. További információ: [hibakeresési mód](concepts-data-flow-debug-mode.md).

## <a name="monitoring-data-flow-performance"></a>Az adatfolyam teljesítményének figyelése

Miután a hibakeresési mód használatával ellenőrizte az átalakítási logikát, az adatfolyamatot végpontok között futtathatja tevékenységként egy folyamaton belül. Az adatfolyamatok egy folyamat során működnek, az [adatfolyamok végrehajtása tevékenység](control-flow-execute-data-flow-activity.md)használatával. Az adatfolyam-tevékenység egyedi figyelési felülettel rendelkezik, mint a többi Azure Data Factory tevékenység, amely az átalakítási logika részletes végrehajtási tervét és teljesítményét jeleníti meg. Az adatfolyamok részletes figyelési információinak megtekintéséhez kattintson a tevékenység futtatása művelet kimenetében található szemüveg ikonra. További információ: a [leképezési adatok forgalmának figyelése](concepts-data-flow-monitoring.md).

![Adatfolyam-figyelő](media/data-flow/monitoring-details.png "Adatfolyam-figyelő 2")

Az adatfolyamok teljesítményének figyelése során négy lehetséges szűk keresztmetszetet talál a következő esetekben:

* Fürt indítási ideje
* Olvasás forrásból
* Átalakítási idő
* Írás egy fogadóba 

![Adatfolyam-monitorozás](media/data-flow/monitoring-performance.png "Adatfolyam-figyelő 3")

A fürt indítási ideje az Apache Spark-fürt felgyorsításához szükséges idő. Ez az érték a figyelési képernyő jobb felső sarkában található. Az adatfolyamatok egy igény szerinti modellen futnak, ahol az egyes feladatok elkülönített fürtöt használnak. Ez az indítási idő általában 3-5 percet vesz igénybe. Szekvenciális feladatokhoz ez a művelet csökkentheti az élettartam értékét. További információ: [a Azure Integration Runtime optimalizálása](#ir).

Az adatforgalom olyan Spark-optimalizáló használ, amely a lehető leggyorsabban végrehajtja az üzleti logikát a "fázisokban". A figyelési kimenet az egyes adatfolyamatok esetében az egyes transzformációs szakaszok időtartamát, valamint az adatfogadóba való íráshoz szükséges időt listázza. Az idő, amely a legnagyobb, valószínűleg az adatáramlás szűk keresztmetszetét jelenti. Ha a legnagyobb Adatátalakítási fázisban a forrás szerepel, érdemes megtekinteni az olvasási idő további optimalizálását. Ha egy átalakítás hosszú időt vesz igénybe, akkor előfordulhat, hogy újra kell particionálnia vagy meg kell emelnie az integrációs modul méretét. Ha a fogadó feldolgozási ideje nagy, lehet, hogy fel kell méreteznie az adatbázist, vagy ellenőriznie kell, hogy nincs-e egyetlen fájlra kiterjesztve.

Miután azonosította az adatáramlás szűk keresztmetszetét, az alábbi optimalizálási stratégiák használatával növelheti a teljesítményt.

## <a name="optimize-tab"></a>Optimalizálás lap

Az **optimalizálás** lap a Spark-fürt particionálási sémájának konfigurálásához szükséges beállításokat tartalmazza. Ez a lap az adatfolyam minden átalakításában megtalálható, és megadja, hogy az átalakítás befejeződése **után** szeretné-e újraparticionálni az adatparticionálást. A particionálás beállítása lehetővé teszi az adatok elosztását a számítási csomópontok és az adatkörnyezet-optimalizálások között, amelyek mind pozitív, mind negatív hatással lehetnek a teljes adatfolyam-teljesítményre.

![A képernyőképen az optimalizálás lap látható, amely tartalmazza a partíciós beállítást, a partíció típusát és a partíciók számát.](media/data-flow/optimize.png)

Alapértelmezés szerint a *jelenlegi particionálás használata* beállítás van kiválasztva, amely arra utasítja Azure Data Factory az átalakítás aktuális kimeneti particionálását. Ahogy az adatok újraparticionálása időt vesz igénybe, a legtöbb esetben ajánlott a *jelenlegi particionálás használata* . Olyan forgatókönyvek, amelyekben érdemes lehet az adatok újraparticionálása olyan összesítések és illesztések után, amelyek jelentősen elferdítik az adatokat, vagy ha az SQL-ADATBÁZISon alapuló forrás-particionálást használnak.

Ha módosítani szeretné a particionálást bármely transzformáción, válassza az **optimalizálás** fület, és válassza a **particionálás beállítása** választógombot. A particionálási lehetőségek sorozatát mutatjuk be. A particionálás legjobb módja az adatmennyiségek, a jelölt kulcsok, a null értékek és a kardinális alapján változhat. 

> [!IMPORTANT]
> Egyetlen partíció egyesíti az összes elosztott adatait egyetlen partícióban. Ez egy nagyon lassú művelet, amely szintén jelentősen befolyásolja az összes alsóbb rétegbeli átalakítást és írást. A Azure Data Factory nagy mértékben ajánljuk ezt a lehetőséget, hacsak nincs kifejezetten üzleti oka.

A következő particionálási lehetőségek érhetők el minden átalakításban:

### <a name="round-robin"></a>Ciklikus multiplexelés 

A ciklikus multiplexelés a partíciók között egyenlően osztja el az adatelosztást. Ciklikus multiplexelés használata, ha nem rendelkezik megfelelő kulcsfontosságú jelöltekkel a szilárd, intelligens particionálási stratégia megvalósításához. Megadhatja a fizikai partíciók számát.

### <a name="hash"></a>Kivonat

Azure Data Factory az oszlopok kivonatát állítja elő, hogy egységes partíciókat hozzon létre, például a hasonló értékekkel rendelkező sorok ugyanabba a partícióba esnek. Ha a kivonatoló kapcsolót használja, tesztelje a lehetséges partíciók döntését. Megadhatja a fizikai partíciók számát.

### <a name="dynamic-range"></a>Dinamikus tartomány

A dinamikus tartomány Spark dinamikus tartományokat használ az Ön által megadott oszlopok vagy kifejezések alapján. Megadhatja a fizikai partíciók számát. 

### <a name="fixed-range"></a>Rögzített tartomány

Hozzon létre egy olyan kifejezést, amely rögzített tartományt biztosít a particionált adatoszlopokban lévő értékek számára. Ha el szeretné kerülni a partíciók eldöntését, érdemes megismernie az adatait, mielőtt ezt a beállítást használja. A kifejezéshez megadott értékek a Partition függvény részeként használatosak. Megadhatja a fizikai partíciók számát.

### <a name="key"></a>Kulcs

Ha jól ismeri az Ön adatait, a kulcsfontosságú particionálás jó stratégia lehet. A kulcs particionálásával az oszlop minden egyedi értékéhez partíciók jönnek létre. A partíciók száma nem állítható be, mert a szám az adatok egyedi értékein alapul.

> [!TIP]
> A particionálási séma manuális beállítása átrendezi az adatmennyiséget, és ellensúlyozza a Spark-optimalizáló előnyeit. A legjobb megoldás, ha nem kell manuálisan beállítania a particionálást.

## <a name="logging-level"></a>Naplózási szint

Ha nincs szüksége az adatáramlási tevékenységek minden folyamatának végrehajtására az összes részletes telemetria-napló teljes naplózásához, igény szerint beállíthatja a naplózási szintet az "alapszintű" vagy a "None" értékre. Ha az adatfolyamatokat "részletes" módban hajtja végre (alapértelmezés), az automatikus lapadagolóba az adatátalakítás során minden egyes partíciós szinten teljes körű naplózási tevékenységet kér. Ez költséges művelet lehet, ezért csak akkor engedélyezze a részletes műveleteket, ha a hibaelhárítás javítja a teljes adatfolyamatot és a folyamat teljesítményét. Az "alapszintű" mód csak az átalakítási időtartamokat fogja naplózni, míg a "None" csak az időtartamok összegzését tartalmazza.

![Naplózási szint](media/data-flow/logging.png "Naplózási szint beállítása")

## <a name="optimizing-the-azure-integration-runtime"></a><a name="ir"></a> A Azure Integration Runtime optimalizálása

Az adatfolyamatok olyan Spark-fürtökön futnak, amelyek futás közben forognak. A használt fürt konfigurációja a tevékenység Integration Runtime (IR) szolgáltatásában van definiálva. Az integrációs modul definiálásakor három teljesítménnyel kapcsolatos szempontot kell figyelembe venni: a fürt típusa, a fürt mérete és az élettartam.

Integration Runtime létrehozásával kapcsolatos további információkért lásd: [Integration Runtime Azure Data Factory](concepts-integration-runtime.md).

### <a name="cluster-type"></a>Fürt típusa

Három elérhető lehetőség áll rendelkezésre a Spark-fürt felskálázásának típusára: általános célú, memóriára optimalizált és a számítás optimalizált.

Az **általános célú** fürtök az alapértelmezettek, és a legtöbb adatáramlási számítási feladathoz ideálisak lesznek. Ezek általában a teljesítmény és a költséghatékonyság legjobb egyensúlya.

Ha az adatfolyam számos illesztéssel és keresési lehetőséggel rendelkezik, érdemes lehet **memóriát használó optimalizált** fürtöt használni. A memóriára optimalizált fürtök több memóriát tárolhatnak a memóriában, és az esetlegesen fellépő memóriabeli hibákat is csökkenthetik. A memóriára optimalizált memória a legmagasabb árat eredményezi, de általában még sikeres folyamatokat is eredményezhet. Ha az adatfolyamatok végrehajtása során a memóriával kapcsolatos hibák merülnek fel, váltson át a memóriára optimalizált Azure IR konfigurációra. 

Az **optimalizált számítások** nem ideálisak az ETL-munkafolyamatokhoz, és a Azure Data Factory csapata nem javasolja a legtöbb éles számítási feladathoz. Az egyszerűbb, memórián kívüli, intenzív adatátalakítások, például adatok szűrése vagy származtatott oszlopok hozzáadása esetén a számítási optimalizált fürtök olcsóbb áron használhatók.

### <a name="cluster-size"></a>Fürt mérete

Az adatfolyamatok egy Spark-fürt különböző csomópontjain keresztül terjesztik az adatfeldolgozást, hogy párhuzamosan hajtsák végre a műveleteket. Egy több magot tartalmazó Spark-fürt növeli a számítási környezet csomópontjainak számát. Több csomópont fokozza az adatfolyam feldolgozási erejét. A fürt méretének növelése gyakran egyszerű módja a feldolgozási idő csökkentésének.

Az alapértelmezett fürt mérete négy illesztőprogram-csomópont és négy munkavégző csomópont.  Az adatfeldolgozás során nagyobb fürtök használata javasolt. Alább láthatók a lehetséges Méretezési lehetőségek:

| Munkavégző magok | Illesztőprogram-magok | Magok száma összesen | Jegyzetek |
| ------------ | ------------ | ----------- | ----- |
| 4 | 4 | 8 | Nem érhető el a számításhoz optimalizált |
| 8 | 8 | 16 | |
| 16 | 16 | 32 | |
| 32 | 16 | 48 | |
| 64 | 16 | 80 | |
| 128 | 16 | 144 | |
| 256 | 16 | 272 | |

Az adatfolyamatok díjszabása virtuális mag-óra, ami azt jelenti, hogy a fürt mérete és a végrehajtási idő tényezője is. A vertikális felskálázás során a fürt percenkénti díja növekedni fog, de a teljes idő csökken.

> [!TIP]
> A fürt méretétől függ, hogy mekkora a mérete egy adott adatfolyam teljesítményének. Az adatmérettől függően a fürt méretének növelésével leáll a teljesítmény javítása. Ha például több csomóponttal rendelkezik, mint az adatpartíciók, további csomópontok hozzáadása nem segít. Az ajánlott eljárás a kis-és a vertikális felskálázás, hogy megfeleljen a teljesítmény igényeinek. 

### <a name="time-to-live"></a>Élettartam

Alapértelmezés szerint minden adatfolyam-tevékenység egy új fürtöt indít el az IR-konfiguráció alapján. A fürt indítási ideje néhány percet vesz igénybe, és az adatfeldolgozás nem kezdődhet egészen addig, amíg be nem fejeződik. Ha a folyamatok több **szekvenciális** adatfolyamatot tartalmaznak, akkor engedélyezheti az élettartam (TTL) értékét. Az élő érték megadásával a fürt a végrehajtás befejeződése után bizonyos ideig életben marad. Ha egy új feladatot az élettartam ideje alatt az IR használatával kezdi meg, a rendszer újból felhasználja a meglévő fürtöt, és a kezdési idő jelentősen csökkenni fog. A második művelet befejezése után a fürt ismét életben marad a TTL-idő alatt.

Egyszerre csak egy feladatot lehet futtatni egyetlen fürtön. Ha van elérhető fürt, de két adatfolyam indul el, csak egy fogja használni az élő fürtöt. A második feladatot a saját elszigetelt fürtje fogja felkészíteni.

Ha a legtöbb adatfolyamat párhuzamosan fut, nem ajánlott engedélyezni az ÉLETTARTAMot. 

> [!NOTE]
> Az integrációs modul automatikus feloldása esetén az élettartam nem érhető el

## <a name="optimizing-sources"></a>Források optimalizálása

A Azure SQL Database kivételével minden forrás esetében ajánlott az **aktuális particionálást** a kijelölt értékként használni. Az adatforgalom az összes többi forrásból való olvasáskor az adatok mérete alapján automatikusan particionálja az adatok mennyiségét. A rendszer minden 128 MB-nyi adattal létrehoz egy új partíciót. Az adatméret növekedésével a partíciók száma növekszik.

Az egyéni particionálás a Spark beolvasása *után* következik be az adatokat, és negatív hatással lesz az adatfolyam teljesítményére. Mivel az információk egyenletesen particionálva vannak az olvasáskor, ez nem ajánlott. 

> [!NOTE]
> Az olvasási sebességet a forrásrendszer átviteli sebessége korlátozhatja.

### <a name="azure-sql-database-sources"></a>Azure SQL Database források

Azure SQL Database rendelkezik egy "forrás" particionálás nevű egyedi particionálási lehetőséggel. A forrás-particionálás engedélyezése javíthatja az Azure SQL-adatbázis olvasási idejét a párhuzamos kapcsolatok engedélyezésével a forrásrendszer használatával. Adja meg a partíciók számát és az adatai particionálásának módját. Használjon egy nagy fokú kihasználható partíciós oszlopot. Megadhat egy olyan lekérdezést is, amely megfelel a forrástábla particionálási sémájának.

> [!TIP]
> A forrás particionálás esetén a SQL Server I/O-értéke a szűk keresztmetszet. Ha túl sok partíciót ad hozzá, a forrás-adatbázis felmerülhet. A kapcsoló használata esetén általában négy vagy öt partíció ideális.

![Forrás particionálás](media/data-flow/sourcepart3.png "Forrás particionálás")

#### <a name="isolation-level"></a>Elkülönítési szint

Egy Azure SQL-forrás rendszer olvasásának elkülönítési szintje hatással van a teljesítményre. A "nem véglegesített olvasás" lehetőség kiválasztásával biztosíthatja a leggyorsabb teljesítményt, és megakadályozhatja az adatbázisok zárolását. Az SQL elkülönítési szintjeivel kapcsolatos további tudnivalókért tekintse meg az [elkülönítési szintek ismertetése](/sql/connect/jdbc/understanding-isolation-levels)című témakört.

#### <a name="read-using-query"></a>Olvasás a lekérdezés használatával

Azure SQL Database a tábla vagy SQL-lekérdezés használatával olvashatja el. Ha SQL-lekérdezést hajt végre, a lekérdezésnek el kell végeznie az átalakítás megkezdése előtt. Az SQL-lekérdezések hasznosak lehetnek olyan műveletek leküldésére, amelyek gyorsabban futnak, és csökkenthetik a SQL Server beolvasott adatok mennyiségét, például SELECT, WHERE és JOIN utasítások. A műveletek lenyomásakor elvész az átalakítások nyomon követése és teljesítménye, mielőtt az adatok bekerülnek az adatfolyamba.

### <a name="azure-synapse-analytics-sources"></a>Azure szinapszis analitikai források

Az Azure szinapszis Analytics használatakor a forrás beállításai között az **átmeneti engedélyezés** nevű beállítás található. Ez lehetővé teszi az ADF-nek a szinapszis használatával történő olvasását ```Staging``` , ami jelentősen javítja az olvasási teljesítményt. Az engedélyezéshez ```Staging``` meg kell adnia egy Azure Blob Storage vagy Azure Data Lake Storage Gen2-előkészítési helyet az adatfolyam tevékenység beállításainál.

![Előkészítés engedélyezése](media/data-flow/enable-staging.png "Előkészítés engedélyezése")

### <a name="file-based-sources"></a>Fájl alapú források

Míg az adatfolyamatok többféle fájltípust támogatnak, a Azure Data Factory a Spark-natív parketta formátum használatát javasolja az optimális olvasási és írási időpontokhoz.

Ha ugyanazokat az adatfolyamatokat futtatja egy adott fájlon, azt javasoljuk, hogy egy mappából, helyettesítő elérési utak használatával vagy a fájlok listájából olvasson be egy fájlt. Egyetlen adatfolyam-tevékenység futtatásával feldolgozhatja az összes fájlt a Batch szolgáltatásban. A beállítások megadásával kapcsolatos további információkért tekintse meg az összekötők dokumentációját, például az [Azure Blob Storage](connector-azure-blob-storage.md#source-transformation).

Ha lehetséges, ne használja a For-Each tevékenységet, hogy adatfolyamatokat futtasson a fájlok egy adott készletén. Ennek hatására a for-each minden egyes iterációja saját Spark-fürtöt hoz létre, ami gyakran nem szükséges, és költséges lehet. 

## <a name="optimizing-sinks"></a>Fogadók optimalizálása

Amikor az adatfolyamatok elsüllyednek, az egyéni particionálások azonnal megtörténnek az írás előtt. A forráshoz hasonlóan a legtöbb esetben azt javasoljuk, hogy a **jelenlegi particionálást használja** a kiválasztott partíciós beállításként. A particionált adatai jelentősen gyorsabban fognak megjelenni, mint a nem particionált adatértékek, még a cél sincs particionálva. Az alábbiakban láthatók a különböző fogadó típusok egyedi szempontjai. 

### <a name="azure-sql-database-sinks"></a>Azure SQL Database mosogatók

A Azure SQL Database esetében az alapértelmezett particionálásnak a legtöbb esetben működnie kell. Előfordulhat, hogy a fogadó túl sok partícióval rendelkezik az SQL-adatbázis kezeléséhez. Ha ezt a szolgáltatást futtatja, csökkentse a SQL Database fogadó által kiosztott partíciók számát.

#### <a name="impact-of-error-row-handling-to-performance"></a>A hibák sorainak a teljesítményre gyakorolt hatása

Ha engedélyezi a sorcsoport-kezelést ("Folytatás a hibánál") a fogadó átalakításban, az ADF további lépést fog tartani, mielőtt beírja a kompatibilis sorokat a céltábla táblájába. Ez a további lépés egy kisebb teljesítménnyel kapcsolatos szankciót tartalmaz, amely az ehhez a lépéshez tartozó 5%-os tartományba kerül, egy további kis teljesítményű találatot is hozzáadva, ha a beállítást úgy állítja be, hogy a nem kompatibilis sorok is a naplófájlhoz legyenek beállítva.

#### <a name="disabling-indexes-using-a-sql-script"></a>Indexek letiltása SQL-parancsfájl használatával

Az indexek letiltásával az SQL-adatbázisok terhelése jelentősen javíthatja a tábla írásának teljesítményét. Futtassa az alábbi parancsot az SQL-gyűjtőbe való írás előtt.

`ALTER INDEX ALL ON dbo.[Table Name] DISABLE`

Az írás befejezése után építse újra az indexeket a következő paranccsal:

`ALTER INDEX ALL ON dbo.[Table Name] REBUILD`

Ezek az Azure SQL DB-ben vagy a szinapszis-tárolón belül, az adatfolyamatok leképezése során egyaránt natív módon hajthatók végre.

![Indexek letiltása](media/data-flow/disable-indexes-sql.png "Indexek letiltása")

> [!WARNING]
> Az indexek letiltásakor az adatfolyam gyakorlatilag nem veszi át az adatbázis irányítását, és a lekérdezések nem valószínű, hogy sikeresek lesznek. Ennek eredményeképpen számos ETL-feladat aktiválva lesz az éjszaka közepén, hogy elkerülje ezt az ütközést. További információ az [indexek letiltásának korlátozásait](/sql/relational-databases/indexes/disable-indexes-and-constraints) ismerteti.

#### <a name="scaling-up-your-database"></a>Az adatbázis vertikális felskálázása

Ütemezze a forrás átméretezését, és az Azure SQL DB-t és a DW-t a folyamat futtatása előtt, növelje az átviteli sebességet, és csökkentse az Azure-szabályozást a DTU korlátainak elérése után. A folyamat befejezése után méretezze át az adatbázisokat a normál futtatási sebességre.

### <a name="azure-synapse-analytics-sinks"></a>Azure szinapszis Analytics-mosogatók

Az Azure szinapszis Analyticsbe való íráskor győződjön meg arról, hogy az **előkészítés engedélyezése** True (igaz) értékre van állítva. Ez lehetővé teszi az ADF számára az [SQL Copy parancs](/sql/t-sql/statements/copy-into-transact-sql) használatával történő írást, amely hatékonyan tölti fel az adatmennyiséget. Az átmeneti tárolás használatakor Azure Data Lake Storage Gen2 vagy Azure Blob Storage-fiókra kell hivatkoznia.

Az előkészítésen kívül ugyanezek az ajánlott eljárások az Azure szinapszis Analyticsre is érvényesek Azure SQL Databaseként.

### <a name="file-based-sinks"></a>Fájl alapú mosogatók 

Míg az adatfolyamatok többféle fájltípust támogatnak, a Azure Data Factory a Spark-natív parketta formátum használatát javasolja az optimális olvasási és írási időpontokhoz.

Ha az adatforgalom egyenletes eloszlású, az **aktuális particionálás** a fájlok írására szolgáló leggyorsabb particionálási lehetőség lesz.

#### <a name="file-name-options"></a>Fájlnév beállításai

A fájlok írásakor olyan elnevezési beállítások közül választhat, amelyek hatással vannak a teljesítményre.

![Fogadó beállításai](media/data-flow/file-sink-settings.png "fogadó beállításai")

Az alapértelmezett beállítás kiválasztásával a leggyorsabb **értéket** fogja írni. Az egyes partíciók a Spark alapértelmezett nevével rendelkező fájlnak felelnek meg. Ez akkor hasznos, ha csak az adatok mappájából olvas.

Az elnevezési **minta** beállításával az egyes partíciós fájlok egy több felhasználóbarát névre lesznek átnevezve. Ez a művelet az írás után történik, és valamivel lassabb, mint az alapértelmezett érték. A partíciók segítségével manuálisan nevezheti el az egyes partíciókat.

Ha egy oszlop megfelel az adatkimenetnek, akkor az **oszlopokban szereplő adatként** is kiválaszthatja az adatokat. Ez átrendezi az adatszerkezetet, és hatással lehet a teljesítményre, ha az oszlopok nem egyenletesen oszlanak el.

Az **egyetlen fájlba történő kimenet** egyetlen partícióba egyesíti az összes adatokat. Ez hosszú írási időt eredményez, különösen a nagyméretű adatkészletek esetében. A Azure Data Factory csapat kifejezetten azt ajánlja, hogy **ne** válassza ezt a lehetőséget, kivéve, ha erre nincs kifejezett üzleti ok.

### <a name="cosmosdb-sinks"></a>CosmosDB mosogatók

A CosmosDB való íráskor az átviteli sebesség és a köteg méretének módosítása az adatáramlás végrehajtása során javíthatja a teljesítményt. Ezek a módosítások csak az adatfolyam-tevékenységek futtatásakor lépnek érvénybe, és a következtetést követően visszatérnek az eredeti gyűjtemény beállításaihoz. 

**Köteg mérete:** Általában az alapértelmezett batch-mérettől kezdve elegendő. Az érték további finomhangolásához számítsa ki az adatainak durva mennyiségét, és győződjön meg arról, hogy az objektum mérete * a Batch mérete kisebb, mint 2 MB. Ha igen, növelheti a köteg méretét, hogy jobb átviteli sebességet kapjon.

**Átviteli sebesség:** Itt állíthatja be a nagyobb átviteli sebesség beállítást, hogy a dokumentumok gyorsabban CosmosDB. Ne feledje, hogy a magasabb RU-költségek magas átviteli sebességen alapulnak.

**Írási átviteli sebesség költségvetése:** Használjon olyan értéket, amely kisebb, mint a percenkénti teljes RUs. Ha nagy számú Spark-partícióval rendelkező adatfolyamot tartalmaz, a költségvetés átviteli sebességének beállítása nagyobb egyensúlyt tesz lehetővé a partíciók között.

## <a name="optimizing-transformations"></a>Átalakítások optimalizálása

### <a name="optimizing-joins-exists-and-lookups"></a>Illesztések, létező és keresési műveletek optimalizálása

#### <a name="broadcasting"></a>Szórásos

Az illesztések, a keresések és a meglévő átalakítások esetében, ha az egyik vagy mindkét adatfolyam elég kicsi ahhoz, hogy illeszkedjenek a munkavégző csomópont memóriához, a **szórás** engedélyezésével optimalizálhatja a teljesítményt. A szórás akkor történik meg, amikor kisméretű adatkereteket küld a fürt összes csomópontjára. Ez lehetővé teszi, hogy a Spark-motor a nagyméretű adatfolyamban lévő adatok újrakeverése nélkül hajtson végre egy illesztést. Alapértelmezés szerint a Spark-motor automatikusan eldönti, hogy a csatlakoztatás egyik oldalát sugározza-e a rendszer. Ha ismeri a bejövő adatait, és biztos lehet benne, hogy az egyik stream jelentősen kisebb lesz, mint a másik, válassza a **rögzített** szórás lehetőséget. A rögzített műsorszórási kényszeríti a Sparkot a kiválasztott adatfolyam szórására. 

Ha a sugárzott adat mérete túl nagy a Spark-csomóponthoz, előfordulhat, hogy kevés a memória. A memóriabeli hibák elkerülése érdekében használja a **memóriára optimalizált** fürtöket. Ha az adatfolyam-végrehajtás során szórási időtúllépéseket tapasztal, kikapcsolhatja a szórásos optimalizálást. Ez azonban lassabban fogja végrehajtani az adatfolyamatokat.

Ha olyan adatforrásokkal dolgozik, amelyek hosszabb időt vehetnek igénybe a lekérdezéshez, például nagyméretű adatbázis-lekérdezésekhez, akkor azt javasoljuk, hogy kapcsolja ki a szórást az illesztésekhez. A hosszú lekérdezési idővel rendelkező forrás a Spark időtúllépését okozhatja, ha a fürt a számítási csomópontok számára kísérli meg a szórást. Egy másik lehetőség a szórás kikapcsolására, ha olyan adatfolyamot használ az adatáramlásban, amely az értékeket egy későbbi keresési átalakításban való használatra összesíti. Ez a minta megzavarhatja a Spark-optimalizáló, és időtúllépéseket okozhat.

![Összekapcsolási átalakítás optimalizálása](media/data-flow/joinoptimize.png "Csatlakozás optimalizálása")

#### <a name="cross-joins"></a>Több illesztés

Ha literális értékeket használ a csatlakoztatási feladatokban, vagy több egyezést tartalmaz egy illesztés mindkét oldalán, a Spark az illesztést kereszt-csatlakozásként futtatja. A Cross JOIN egy teljes Descartes-szorzat, amely ezután kiszűri az illesztett értékeket. Ez lényegesen lassabb, mint a többi illesztési típus. A teljesítményre gyakorolt hatás elkerülése érdekében győződjön meg arról, hogy az illesztési feltételek mindkét oldalán az oszlopra hivatkozik.

#### <a name="sorting-before-joins"></a>Rendezés az illesztések előtt

Az egyesítési illesztéstől eltérően az olyan eszközökhöz, mint a SSIS, az illesztési átalakítás nem kötelező egyesítő illesztési művelet. Az illesztési kulcsok nem igénylik a rendezést az átalakítás előtt. Az Azure Data Factory csapat nem javasolja a rendezési átalakítások használatát a leképezési adatfolyamatokban.

### <a name="window-transformation-performance"></a>Ablak-átalakítási teljesítmény

Az átalakítási beállításokban a záradék részeként kiválasztott oszlopokban az [Adatátalakítási](data-flow-window.md) adatbázis az adatmennyiséget adja meg érték szerint ```over()``` . Számos népszerű összesített és analitikai funkció érhető el a Windows átalakításban. Ha azonban a használati eset a teljes adatkészlethez tartozó ablak létrehozása a rangsor ```rank()``` vagy a sor számának céljára ```rowNumber()``` , ajánlott inkább a [rangsor átalakítását](data-flow-rank.md) és a [helyettesítő kulcs átalakítását](data-flow-surrogate-key.md)használni. Ezek az átalakítások a függvények használatával hatékonyabban fogják elvégezni a teljes adatkészlet-műveleteket.

### <a name="repartitioning-skewed-data"></a>Ferde adatpartíciók újraparticionálása

Bizonyos átalakítások, például összekapcsolások és összesítések átrendezik az adatpartíciókat, és alkalmanként elferdítik az adatokat. A ferde adatforgalom azt jelenti, hogy az adatforgalom nem egyenletesen oszlik meg a partíciók között. A nagy mértékben elferdített adatműveletek lassabb, alsóbb rétegbeli átalakításokhoz és írási műveletekhez vezethetnek. Az adatáramlási folyamat bármely pontján megtekintheti az adatait, ha a figyelés képernyő átalakítás elemére kattint.

![Döntés és csúcsosságát](media/data-flow/skewness-kurtosis.png "Döntés és csúcsosságát")

A figyelési kijelző megmutatja, hogyan oszlanak el az adatok az egyes partíciók között két metrika, döntés és csúcsosságát mellett. A **torzítás** azt méri, hogy az adatmennyiség mennyire aszimmetrikus, pozitív, nulla, negatív vagy nem definiált értékkel. A negatív ferdeség azt jelenti, hogy a bal oldali farok hosszabb a jobb oldalon. A **csúcsosságát** azt méri, hogy az adatmennyiség nagy vagy világos-e. A magas csúcsosságát értékek nem kívánatosak. A ferdeség ideális tartománya-3 és 3 közötti, a csúcsosságát-tartomány pedig kevesebb, mint 10. Ezeket a számokat egyszerűen értelmezheti a particionálási diagramon, és megtekintheti, hogy az 1 sáv nagyobb-e, mint a többi.

Ha az adatai nem egyenletesen particionálva lettek egy átalakítás után, az [optimalizálás lapot](#optimize-tab) használhatja az újraparticionáláshoz. Az adatmennyiség újrakeverése időt vesz igénybe, és előfordulhat, hogy nem javítja az adatfolyamok teljesítményét.

> [!TIP]
> Ha újraparticionálja az adatokat, de olyan alsóbb rétegbeli átalakításokkal rendelkezik, amelyek átrendezik az adatokat, használja az összekapcsolási kulcsként használt oszlop kivonatoló particionálását.

## <a name="using-data-flows-in-pipelines"></a>Adatfolyamatok használata a folyamatokban 

Összetett folyamatok több adatfolyamattal való létrehozásakor a logikai folyamat nagy hatással lehet az időzítésre és a költséghatékonyságra. Ez a szakasz a különböző architektúrás stratégiák hatását tárgyalja.

### <a name="executing-data-flows-in-parallel"></a>Az adatfolyamatok párhuzamos végrehajtása

Ha párhuzamosan több adatfolyamot hajt végre, az ADF külön Spark-fürtöket hoz létre az egyes tevékenységekhez. Ez lehetővé teszi az egyes feladatok elkülönítését és párhuzamos futtatását, de egyszerre több fürtöt is futtatnak.

Ha az adatfolyamatok párhuzamosan futnak, az ajánlott, hogy ne engedélyezze a Azure IR időt az élő tulajdonságnak, mert több nem használt meleg készletet fog eredményezni.

> [!TIP]
> Ahelyett, hogy az egyes tevékenységek esetében többször is ugyanazt az adatfolyamot futtatja, az adatait egy adattóban kell megalkotni, és helyettesítő elérési utakat kell használnia az adatfolyamatok feldolgozásához.

### <a name="execute-data-flows-sequentially"></a>Az adatfolyamatok végrehajtása szekvenciálisan

Ha az adatfolyam-tevékenységeket egymás után hajtja végre, akkor azt javasoljuk, hogy a Azure IR konfigurációjában állítsa be a TTL értéket. Az ADF újra felhasználja a számítási erőforrásokat, így a fürt gyorsabb indítási ideje lesz. Minden tevékenység továbbra is el lesz különítve, és minden egyes végrehajtáshoz új Spark-környezetet kap.

A feladatok egymás utáni futtatása valószínűleg a leghosszabb időt vesz igénybe a végpontok végrehajtásához, de a logikai műveletek tiszta elkülönítését is lehetővé teszi.

### <a name="overloading-a-single-data-flow"></a>Egy adatfolyam túlterhelése

Ha egyetlen adatfolyamba helyezi az összes logikát, akkor az ADF egyetlen Spark-példányon hajtja végre a teljes feladatot. Habár ez úgy tűnhet, mint a költségek csökkentése, összekeveri a különböző logikai folyamatokat, és nehéz lehet figyelni és hibakeresést végezni. Ha egy összetevő meghibásodik, a feladat összes többi része is sikertelen lesz. Az Azure Data Factory csapat az adatfolyamatok független üzleti logikával való szervezését javasolja. Ha az adatfolyam túl nagy lesz, a különálló összetevőkre való felosztás egyszerűbbé teszi a figyelést és a hibakeresést. Habár az adatforgalomban az átalakítások száma nem korlátozott, túl sok lesz a feladatsor.

### <a name="execute-sinks-in-parallel"></a>A mosogatók párhuzamos végrehajtása

Az adatfolyam-elsüllyedés alapértelmezett viselkedése az egyes fogadók egymás utáni, soros módon történő végrehajtása, valamint az adatfolyamatok meghibásodása, amikor hiba történik a fogadóban. Emellett az összes mosogató alapértelmezés szerint ugyanahhoz a csoporthoz tartozik, hacsak nem lép be az adatfolyam tulajdonságaiba, és nem állítja be a mosdók különböző prioritásait.

Az adatfolyamatok lehetővé teszik, hogy csoportosítsa a mosogatókat csoportokba a felhasználói felületi tervező adatáramlás tulajdonságai lapján. Egyszerre állíthatja be a mosogatók végrehajtásának sorrendjét, valamint a mosogatók csoportosítását az azonos csoport számával együtt. A csoportok kezelésének megkönnyítéséhez megkérheti az ADF-et, hogy ugyanabban a csoportban futtassa a mosogatókat, hogy párhuzamosan fusson.

A folyamat "fogadó tulajdonságai" szakasza alatt az adatfolyamatok végrehajtása művelettel bekapcsolhatja a párhuzamos fogadó betöltését. Ha engedélyezi a "Futtatás párhuzamosan" lehetőséget, akkor az adatfolyamatokat a csatlakoztatott mosdóba kell írni, nem pedig szekvenciális módon. A párhuzamos lehetőség kihasználásához a nyelők csoportba kell tartoznia, és ugyanahhoz az adatfolyamhoz kell csatlakoznia egy új ág vagy feltételes felosztás használatával.

## <a name="next-steps"></a>Következő lépések

Tekintse meg a teljesítménnyel kapcsolatos egyéb adatfolyam-cikkeket:

- [Adatfolyam-tevékenység](control-flow-execute-data-flow-activity.md)
- [Adatfolyamok teljesítményének figyelése](concepts-data-flow-monitoring.md)

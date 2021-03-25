---
title: 'Oracle – Azure Database for PostgreSQL: áttelepítési útmutató'
titleSuffix: Azure Database for PostgreSQL
description: Ez az útmutató bemutatja, hogyan migrálhatja az Oracle-sémát Azure Database for PostgreSQLba.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: b41f894a7e4742b75ea06684a960221d4a5b7641
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024761"
---
# <a name="migrate-oracle-to-azure-database-for-postgresql"></a>Oracle migrálása Azure Database for PostgreSQLre

Ez az útmutató bemutatja, hogyan migrálhatja az Oracle-sémát Azure Database for PostgreSQLba. 

Részletes és átfogó áttelepítési útmutatást az [áttelepítési útmutató erőforrásai](https://github.com/microsoft/OrcasNinjaTeam/blob/master/Oracle%20to%20PostgreSQL%20Migration%20Guide/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Guide.pdf)című témakörben talál. 

## <a name="prerequisites"></a>Előfeltételek

Ha az Oracle-sémát Azure Database for PostgreSQLra szeretné áttelepíteni, a következőket kell tennie: 

- Ellenőrizze, hogy a forrás-környezet támogatott-e. 
- Töltse le a [ora2pg](https://ora2pg.darold.net/)legújabb verzióját. 
- A [DBD modul](https://www.cpan.org/modules/by-module/DBD/)legújabb verziója. 


## <a name="overview"></a>Áttekintés

A PostgreSQL a világ egyik legfejlettebb nyílt forráskódú adatbázisa. Ez a cikk azt ismerteti, hogyan lehet az ingyenes ora2pg segédprogrammal áttelepíteni egy Oracle-adatbázist a PostgreSQL-be. A ora2pg, egy ingyenes eszköz használatával egy Oracle-vagy MySQL-adatbázist egy PostgreSQL-kompatibilis sémára telepíthet át. A segédprogram összekapcsolja az Oracle-adatbázist, automatikusan megkeresi és kibontja a szerkezetét vagy adatait. Ezt követően a ora2pg olyan SQL-parancsfájlokat hoz létre, amelyeket betölthet a PostgreSQL-adatbázisba. a ora2pg felhasználható egy Oracle-adatbázis fordított mérnöki feladataihoz, a nagyvállalati adatbázisok áttelepítéséhez, vagy egyszerűen csak néhány Oracle-adat replikálásához egy PostgreSQL-adatbázisba. Az Oracle Database-hez való kapcsolódáshoz szükséges paraméterek megadása nem kötelező, és nem igényel semmilyen Oracle-adatbázist.

> [!NOTE]
> A ora2pg legújabb verziójának használatával kapcsolatos további információkért tekintse meg a [ora2pg dokumentációját](https://ora2pg.darold.net/documentation.html).

### <a name="typical-ora2pg-migration-architecture"></a>Tipikus ora2pg-áttelepítési architektúra

![Képernyőkép a ora2pg áttelepítési architektúráról.](media/howto-migrate-from-oracle/ora2pg-migration-architecture.png)

A virtuális gép és a Azure Database for PostgreSQL üzembe helyezése után két konfigurációra van szükség a közöttük való kapcsolat engedélyezéséhez: az **Azure-szolgáltatások engedélyezése** és az **SSL-kapcsolat kikényszerítés** a következőképpen látható módon:

- **Kapcsolatbiztonsági** panel – > **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** – >

- **Kapcsolatbiztonsági** panel – > **SSL-beállítások**  ->  **kényszeríti az SSL-kapcsolat használatát** – > letiltva

### <a name="recommendations"></a>Javaslatok

- A felmérési vagy exportálási műveletek teljesítményének növeléséhez az Oracle-kiszolgálón a következőképpen gyűjtheti a statisztikát:

   ```
   BEGIN
   
     DBMS_STATS.GATHER_SCHEMA_STATS
     DBMS_STATS.GATHER_DATABASE_STATS
     DBMS_STATS.GATHER_DICTIONARY_STATS
     END;
   ```

- Az adatexportálás a Másolás parancs használatával, Beszúrás helyett.

- Ne exportálja a táblákat a FKs, megkötésekkel és indexekkel, így a művelet lassabban importálja az adattárat a PostgreSQL-be.

- Hozzon létre egy, a **No adatzáradék** használatával létrehozott nézeteket, és frissítse később.

- Ha lehetséges, hozzon létre egyedi indexeket az anyagmintás nézetekben, ezzel a szintaxissal a frissítés gyorsabban elvégezhető `REFRESH MATERIALIZED VIEW CONCURRENTLY` .



## <a name="pre-migration"></a>A migrálás előtt 

Miután meggyőződött arról, hogy a forrás-környezet támogatott, és gondoskodik arról, hogy az előfeltételeket megválaszolja, készen áll az áttelepítés előtti fázis elindítására. A folyamat ezen része magában foglalja az áttelepíteni kívánt adatbázisok leltárának elvégzését, a lehetséges áttelepítési problémák vagy blokkolók kiértékelését, valamint az esetlegesen feltárt elemek feloldását. Ahhoz, hogy a különböző áttelepítések, például az Oracle Azure Database for PostgreSQL, ebben a fázisban a forrás-adatbázis (ok) sémájának ((k) átalakítását is be kell állítani a célként megadott környezettel való kompatibilitás érdekében.

### <a name="discover"></a>Felderítés

A felderítési fázis célja, hogy azonosítsa a meglévő adatforrásokat, és részletezse azokat a funkciókat, amelyek segítségével jobban megismerheti és megtervezheti az áttelepítést. Ez a folyamat a hálózat vizsgálatával azonosítja az összes szervezet Oracle-példányát, valamint a használatban lévő verziót és szolgáltatásokat.

A Microsoft Oracle előértékelési parancsfájljai az Oracle-adatbázison futnak. Az előértékelési parancsfájlok az Oracle-metaadatokat elérő lekérdezések összessége, amelyek a következőket biztosítják:

- Adatbázis-leltár, beleértve az objektumok mennyiségét a séma, a típus és az állapot alapján.

- Az egyes sémákban lévő nyers adatok durva becslése (statisztika alapján).

- A táblák méretezése az egyes sémákban.

- A kód sorainak száma csomagok, függvények, eljárások stb. alapján

Töltse le a kapcsolódó parancsfájlokat a [ora2pg webhelyről](http://ora2pg.darold.net/).

### <a name="assess"></a>Kiértékelés

Az Oracle-adatbázis (ok) leltárának elvégzése után az adatbázis méretének és a kihívásoknak a végrehajtásához a következő lépés az értékelés futtatása.

Az Oracle-ről a PostgreSQL-re való áttelepítési folyamat költségeit nem könnyű megbecsülni. Az áttelepítési díj jó értékeléséhez a ora2pg megvizsgálja az összes adatbázis-objektumot, az összes függvényt és a tárolt eljárásokat annak észlelésére, hogy van-e még néhány olyan objektum és olyan SQL-kód, amelyet a ora2pg nem tud automatikusan átalakítani.

a ora2pg olyan tartalmi elemzési móddal rendelkezik, amely megvizsgálja az Oracle-adatbázist, és szöveges jelentést készít arról, hogy mit tartalmaz az Oracle-adatbázis, és mit nem lehet exportálni.

Az **elemzési és jelentési** mód aktiválásához használja az exportált típust az `SHOW_REPORT` alábbi parancsban látható módon:

```
ora2pg -t SHOW_REPORT
```

Az adatbázis elemzése után a ora2pg az SQL és a com/SQL kód Oracle-szintaxisról PostgreSQL-re való átalakításának lehetősége a kód nehézségeit és a teljes adatbázis-áttelepítés elvégzéséhez szükséges időt is megbecsülheti.

Az áttelepítési díj kiszámításához az emberi napokon a ora2pg lehetővé teszi ESTIMATE_COST nevű konfigurációs irányelv használatát, amely a parancssorban is engedélyezhető:

```
ora2pg -t SHOW_REPORT --estimate_cost
```

Az alapértelmezett áttelepítési egység körülbelül öt percet képvisel a PostgreSQL-szakértők számára. Ha ez az első áttelepítés, a konfigurációs direktíva COST_UNIT_VALUE vagy a--cost_unit_value parancssori kapcsolóval magasabb szintű lehet.

A jelentés utolsó sora az összes becsült áttelepítési kódot mutatja az egyes objektumokra becsült áttelepítési egységek száma után.

Ez az áttelepítési egység körülbelül öt percet képvisel a PostgreSQL-szakértők esetében. Ha ez az első áttelepítés, megnövelheti az alapértelmezett értéket a konfigurációs irányelv COST_UNIT_VALUE vagy a--cost_unit_value parancssori kapcsoló használatával. Az a értékelés néhány variációja alatt megtalálhatja a táblázatok értékelését; b) az oszlopok értékelése c) séma-értékelés az alapértelmezett cost_unit (5 perc) d) séma-értékelést használva, 10 percnél a Cost egységként.

```
ora2pg -t SHOW_TABLE -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\tables.txt ora2pg -t SHOW_COLUMN -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\columns.txt
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf --dump_as_html --estimate_cost > c:\ts303\hr
_migration\reports\report.html
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf –-cost_unit_value 10 --dump_as_html --estimate_cost > c:\ts303\hr_migration\reports\report2.html
```

A séma értékelésének kimenetét az alábbi ábra szemlélteti:

**Áttelepítési szint: B-5**

Áttelepítési szintek:

A-Migrálás, amely automatikusan futtatható

B – Migrálás a kód újraírásával és az emberi napok ára 5 nap

C – Migrálás a kód újraírásával és az 5 napnál nagyobb, emberi napokra vonatkozó Cost

Technikai szintek:

1 = triviális: nincsenek tárolt függvények és triggerek

2 = egyszerű: nincs tárolt függvény, de eseményindítókkal, nincs manuális újraírása

3 = egyszerű: tárolt függvények és/vagy triggerek manuális újraírása nélkül

4 = manuális: nincs tárolt függvény, de a kód újraírásával eseményindítókkal vagy nézetekkel

5 = nehéz: tárolt függvények és/vagy triggerek kód újraírásával

Az értékelés egy betűből (A vagy B) áll, amely meghatározza, hogy az áttelepítéshez szükség van-e manuális újraírásra, illetve 1 és 5 közötti számra, hogy jelezze a technikai nehézségi szintet. További human_days_limit lehetőséggel megadhatja az emberi napok maximális számát, ha az áttelepítési szint a C értékre van állítva, ami azt jelzi, hogy nagy mennyiségű munkára és teljes projektmenedzsmentre van szüksége az áttelepítés támogatásával. Az alapértelmezett érték 10 emberi nap. Az alapértelmezett érték a HUMAN_DAYS_LIMIT konfigurációs irányelv használatával véglegesen módosítható.

Ez a szolgáltatás úgy lett kialakítva, hogy segítse annak eldöntését, hogy melyik adatbázist lehet elsőként áttelepíteni, és mi az a csapat, amelyet mozgósítani kell.

### <a name="convert"></a>Konvertálás

Minimális állásidő esetén az áttelepíteni kívánt forrás továbbra is megváltozik, a célként megadott adatok és séma alapján, az egyszeri áttelepítés után. Az **adatszinkronizálási** fázisban biztosítania kell, hogy a forrás összes módosítása rögzítve legyen, és a célt a közel valós időben alkalmazza a rendszer. Miután meggyőződött arról, hogy a forrás összes változása alkalmazva lett a célra, a forrásról a átváltás.

Az áttelepítés ebben a lépésében az Oracle-kód + DDLS átalakítása vagy fordítása a PostgreSQL-be történik. A ora2pg eszköz automatikusan exportálja az Oracle-objektumokat a PostgreSQL-formátumban. A generált objektumok esetében egyesek nem lesznek lefordítva a PostgreSQL-adatbázisban kézi módosítások nélkül.  
A manuális beavatkozást igénylő elemek megismerésének folyamata a ora2pg által a PostgreSQL-adatbázishoz generált fájlok fordítása, a napló ellenőrzése és a szükséges módosítások elvégzése, amíg a séma szerkezete nem kompatibilis a PostgreSQL-szintaxissal.


#### <a name="create-migration-template"></a>Áttelepítési sablon létrehozása 

Először is ajánlott létrehozni az áttelepítési sablont, amely a ora2pg-mel van ellátva. A két lehetőség – project_base és--init_project, ha a használat azt jelzi, hogy a ora2pg létre kell hoznia egy munkafájával rendelkező projektfájlt, egy konfigurációs fájlt és egy parancsfájlt, amely az összes objektumot exportálja az Oracle-adatbázisból. További információkért tekintse meg a [ora2pg dokumentációját](https://ora2pg.darold.net/documentation.html).

   Használja az alábbi parancsot: 

   ```
   ora2pg --project_base /app/migration/ --init_project test_project
   
   ora2pg --project_base /app/migration/ --init_project test_project
   ```

Példa a kimenetre: 
   
   ```
   Creating project test_project. /app/migration/test_project/ schema/ dblinks/ directories/ functions/ grants/ mviews/ packages/ partitions/ procedures/ sequences/ synonyms/    tables/ tablespaces/ triggers/ types/ views/ sources/ functions/ mviews/ packages/ partitions/ procedures/ triggers/ types/ views/ data/ config/ reports/
   
   Generating generic configuration file
   
   Creating script export_schema.sh to automate all exports.
   
   Creating script import_all.sh to automate all imports.
   ```

A forrás/könyvtár tartalmazza az Oracle-kódot, a séma/könyvtár tartalmazza a PostgreSQL-re portolt kódot. A jelentések/könyvtár tartalmazza az áttelepítési Cost értékelésével kapcsolatos HTML-jelentéseket.


A projekt struktúrájának létrehozása után létrejön egy általános konfigurációs fájl. Adja meg az Oracle Database-kapcsolatokat, valamint a megfelelő konfigurációs paramétereket a konfigurációban.  Tekintse meg a ora2pg dokumentációját, hogy megtudja, mi konfigurálható a konfigurációs fájlban, és hogyan.


#### <a name="export-oracle-objects"></a>Oracle-objektumok exportálása

Ezután exportálja az Oracle-objektumokat PostgreSQL-objektumokként a export_schema. sh fájl futtatásával.

   ```
   cd /app/migration/mig_project
   ./export_schema.sh
   
   Run the following command manually:
   
   SET namespace="/app/migration/mig_project"
   
   ora2pg -t DBLINK -p -o dblink.sql -b %namespace%/schema/dblinks -c
   %namespace%/config/ora2pg.conf
   ora2pg -t DIRECTORY -p -o directory.sql -b %namespace%/schema/directories -c
   %namespace%/config/ora2pg.conf
   ora2pg -p -t FUNCTION -o functions2.sql -b %namespace%/schema/functions -c
   %namespace%/config/ora2pg.conf ora2pg -t GRANT -o grants.sql -b %namespace%/schema/grants -c %namespace%/config/ora2pg.conf ora2pg -t MVIEW -o mview.sql -b %namespace%/schema/   mviews -c %namespace%/config/ora2pg.conf
   ora2pg -p -t PACKAGE -o packages.sql
   %namespace%/config/ora2pg.conf -b %namespace%/schema/packages -c
   ora2pg -p -t PARTITION -o partitions.sql %namespace%/config/ora2pg.conf -b %namespace%/schema/partitions -c
   ora2pg -p -t PROCEDURE -o procs.sql
   %namespace%/config/ora2pg.conf -b %namespace%/schema/procedures -c
   ora2pg -t SEQUENCE -o sequences.sql
   %namespace%/config/ora2pg.conf -b %namespace%/schema/sequences -c
   ora2pg -p -t SYNONYM -o synonym.sql -b %namespace%/schema/synonyms -c
   %namespace%/config/ora2pg.conf
   ora2pg -t TABLE -o table.sql -b %namespace%/schema/tables -c %namespace%/config/ora2pg.conf ora2pg -t TABLESPACE -o tablespaces.sql -b %namespace%/schema/tablespaces -c
   %namespace%/config/ora2pg.conf
   ora2pg -p -t TRIGGER -o triggers.sql -b %namespace%/schema/triggers -c
   %namespace%/config/ora2pg.conf ora2pg -p -t TYPE -o types.sql -b %namespace%/schema/types -c %namespace%/config/ora2pg.conf ora2pg -p -t VIEW -o views.sql -b %namespace%/   schema/views -c %namespace%/config/ora2pg.conf
   ```

   Az adatok kinyeréséhez használja a következő parancsot:

   ```
   ora2pg -t COPY -o data.sql -b %namespace/data -c %namespace/config/ora2pg.conf
   ```

#### <a name="compile-files"></a>Fájlok fordítása

Végül fordítsa le az összes fájlt Azure Database for PostgreSQL-kiszolgálóról. Most már lehetőség van a manuálisan létrehozott DDL-fájlok betöltésére vagy a második, import_all. sh parancsfájl használatára a fájlok interaktív importálásához.

   ```
   psql -f %namespace%\schema\sequences\sequence.sql -h server1-
   
   server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l
   
   %namespace%\ schema\sequences\create_sequences.log
   
   psql -f %namespace%\schema\tables\table.sql -h server1-server.postgres.database.azure.com p 5432 -U username@server1-server -d database -l    %namespace%\schema\tables\create_table.log
   ```

   Adatimportálási parancs:

   ```
   psql -f %namespace%\data\table1.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table1.log
   
   psql -f %namespace%\data\table2.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table2.log
   ```

A fájlok összeállítása során tekintse át a naplókat, és javítsa ki a szükséges szintaxisokat, amelyeket a ora2pg nem tudott kialakítani a dobozból.

Tekintse át az [Oracle Azure Database for PostgreSQL áttelepítési megkerülő megoldásait](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf) a problémák megoldásának támogatásához.

## <a name="migrate"></a>Migrate 

Miután elvégezte a szükséges előfeltételeket, és végrehajtotta az **áttelepítés előtti** fázishoz kapcsolódó feladatokat, készen áll a séma és az adatok áttelepítésére.

### <a name="migrate-schema-and-data"></a>Séma és az adatáttelepítés

A javítások érvénybe léptetése után az adatbázis stabil felépítése készen áll az üzembe helyezésre.

Ekkor az összes szükséges, hogy végrehajtsa a *psql* importálási parancsait, mutasson a módosított kódot tartalmazó fájlokra, hogy lefordítsa az adatbázis-objektumokat a PostgreSQL-adatbázisra, és importálja azokat.

Ebben a lépésben az adatimportálás bizonyos szintjének párhuzamossága is megvalósítható.

### <a name="data-sync-and-cutover"></a>Adatszinkronizálás és átváltás

Az online (minimális állásidő) áttelepítések esetén az áttelepíteni kívánt forrás továbbra is megváltozik, a célhelyről az adatok és a séma alapján, az egyszeri áttelepítés után. Az **adatszinkronizálási** fázisban biztosítania kell, hogy a forrás összes módosítása rögzítve legyen, és a célt a közel valós időben alkalmazza a rendszer. Miután meggyőződött arról, hogy a forrás összes változása alkalmazva lett a célra, a forrásról a átváltás.

Március 2019, ha online áttelepítést szeretne végezni, érdemes lehet a Attunity replikálást használni a Microsoft áttelepítéséhez vagy Striim.

A ora2pg-t használó *különbözeti/növekményes* áttelepítés esetében a technika az egyes táblákra vonatkozó, a szűrést (kivágást) dátummal vagy idővel, valamint a (z) és újabb, a többi adat áttelepítését követően.

A forrásadatok táblázatban először telepítse át az összes korábbi adatforrást. Példa erre:

```
select * from table1 where filter_data < 01/01/2019
```

A kezdeti áttelepítés óta végrehajtott módosításokat a következőhöz hasonló parancs futtatásával kérdezheti le:

```
select * from table1 where filter_data >= 01/01/2019
```

Ebben az esetben javasoljuk, hogy az érvényesítést az adatparitás mindkét oldalon, a forráson és a célon való ellenőrzésével fokozza.

## <a name="post-migration"></a>A migrálás után 

Miután sikeresen elvégezte az **áttelepítési** szakaszt, át kell haladnia az áttelepítés utáni feladatok sorozatán, hogy minden a lehető legzökkenőmentesen és hatékonyan működjön.

### <a name="remediate-applications"></a>Alkalmazások szervizelése

Miután áttelepítette az adatátvitelt a cél környezetbe, az összes olyan alkalmazásnak, amely korábban használta a forrást, el kell kezdenie a cél felhasználását. Ennek elvégzése bizonyos esetekben szükségessé teszi az alkalmazások módosítását.

### <a name="perform-tests"></a>Tesztek végrehajtása

Az adatok célhelyre való áttelepítése után végezzen teszteket az adatbázisokon annak ellenőrzéséhez, hogy az alkalmazások jól teljesítenek-e a célhelyen az áttelepítés után.

Annak biztosításához, hogy a forrás és a cél megfelelően migrálva legyen, futtassa a manuális adatellenőrzési parancsfájlokat az Oracle-forrás és a PostgreSQL-cél adatbázisain.

Ideális esetben, ha a forrás-és a célként megadott adatbázisok hálózati elérési úttal rendelkeznek, ora2pg kell használni az adatellenőrzéshez. A TESZTELÉSi művelettel ellenőrizheti, hogy az Oracle-adatbázis összes objektuma a PostgreSQL alatt lett létrehozva. Futtassa az alábbi parancsot az ábrán látható módon:

```
ora2pg -t TEST -c config/ora2pg.conf > migration_diff.txt
```

### <a name="optimize"></a>Optimalizálás

Az áttelepítés utáni fázis elengedhetetlen az adatok pontosságával kapcsolatos problémák összeegyeztetéséhez és a teljesség ellenőrzéséhez, valamint a számítási feladatok teljesítményével kapcsolatos problémák kezeléséhez.

## <a name="migration-assets"></a>Áttelepítési eszközök 

Ha további segítségre van az áttelepítési forgatókönyv végrehajtásával kapcsolatban, tekintse meg a következő forrásokat, amelyek a valós idejű migrációs projektek támogatásában lettek kifejlesztve.

| **Cím hivatkozása** | **Leírás**    |
| -------------- | ------------------ |
| [Oracle – Azure PostgreSQL Migration Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf) | Ennek a dokumentumnak a célja az, hogy az építészek, a tanácsadók, a Adattervezők és a kapcsolódó szerepkörök segítségével gyorsan áttelepíthetik a számítási feladatokat az Oracle-ből Azure Database for PostgreSQL a ora2pg eszközzel. |
| [Oracle – Azure PostgreSQL áttelepítési megkerülő megoldások](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf) | Ennek a dokumentumnak a célja az, hogy az építészek, a tanácsadók, a Adattervezők és a kapcsolódó szerepkörök segítséget nyújtsanak az Oracle-ből a Azure Database for PostgreSQLba történő áttelepítése során felmerülő feladatok gyors javításához/megoldásához. |
| [A ora2pg Windows vagy Linux rendszeren való telepítésének lépései](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)                       | Ez a dokumentum olyan gyors telepítési útmutatóként használható, amely lehetővé teszi a séma-& adatok Oracle-ből Azure Database for PostgreSQL való áttelepítését Windows vagy Linux rendszerű ora2pg eszközzel. Az eszköz részletes adatai a következő címen találhatók: http://ora2pg.darold.net/documentation.html . |

Ezek az erőforrások az Azure adatcsoport-mérnöki csapat által szponzorált adatsql ninja program részeként lettek kifejlesztve. Az adatelemzési program alapszintű alapokmánya az, hogy feloldja az összetett modernizációt, és az adatplatform-migrációs lehetőségeket a Microsoft Azure-beli adatplatformján is felgyorsítja. Ha úgy gondolja, hogy a szervezete szeretne részt venni az adatsql ninja programban, forduljon a fiókhoz, és kérje meg, hogy küldje el a jelölést.


### <a name="contact-support"></a>Kapcsolatfelvétel a támogatási szolgáltatással

Ha segítségre van szüksége a ora2pg-eszközökön túli áttelepítésekhez, lépjen kapcsolatba az [ @Ask Azure db for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) aliassal, és tájékozódjon az egyéb áttelepítési lehetőségekről.

## <a name="next-steps"></a>Következő lépések

- A Microsoft és a harmadik féltől származó szolgáltatások/eszközök a különböző adatbázis-és adatáttelepítési forgatókönyvek (és a speciális feladatok) számára történő támogatásához tekintse meg a cikk a [szolgáltatás és az eszközök az adatok áttelepítéséhez](https://docs.microsoft.com/azure/dms/dms-tools-matrix)című témakört.

További információ: 
- [Az Azure Database for PostgreSQL dokumentációja](https://docs.microsoft.com/azure/postgresql/)
- [a ora2pg dokumentációja](https://ora2pg.darold.net/documentation.html)
- [PostgreSQL-webhely](https://www.postgresql.org/)
- [Önálló tranzakciós támogatás a PostgreSQL-ben](http://blog.dalibo.com/2016/08/19/Autonoumous_transactions_support_in_PostgreSQL.html) 

Videó tartalma: 
- Az [áttelepítési út és a szükséges eszközök/szolgáltatások áttekintése az értékelés és a Migrálás elvégzéséhez](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/).
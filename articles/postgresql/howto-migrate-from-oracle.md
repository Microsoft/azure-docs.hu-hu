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
ms.openlocfilehash: 931528ec415cabde8e862db17b9f8f26502f6788
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968934"
---
# <a name="migrate-oracle-to-azure-database-for-postgresql"></a>Oracle migrálása Azure Database for PostgreSQLre

Ez az útmutató bemutatja, hogyan migrálhatja az Oracle-sémát Azure Database for PostgreSQLba. 

Részletes és átfogó áttelepítési útmutatást az [áttelepítési útmutató erőforrásai](https://github.com/microsoft/OrcasNinjaTeam/blob/master/Oracle%20to%20PostgreSQL%20Migration%20Guide/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Guide.pdf)című témakörben talál. 

## <a name="prerequisites"></a>Előfeltételek

Ha az Oracle-sémát Azure Database for PostgreSQLra szeretné áttelepíteni, a következőket kell tennie: 

- Ellenőrizze, hogy a forrás-környezet támogatott-e. 
- Töltse le a [ora2pg](https://ora2pg.darold.net/)legújabb verzióját. 
- A [DBD modul](https://www.cpan.org/modules/by-module/DBD/)legújabb verzióját kell megmutatnia. 


## <a name="overview"></a>Áttekintés

A PostgreSQL a világ egyik legfejlettebb nyílt forráskódú adatbázisa. Ez a cikk azt ismerteti, hogyan lehet az ingyenes ora2pg eszközzel áttelepíteni egy Oracle-adatbázist a PostgreSQL-be. A ora2pg használatával egy Oracle-adatbázist vagy egy MySQL-adatbázist egy PostgreSQL-kompatibilis sémára telepíthet át. 

A ora2pg eszköz összekapcsolja az Oracle-adatbázist, automatikusan megkeresi és kibontja a szerkezetét vagy adatait. Ezután a ora2pg olyan SQL-parancsfájlokat hoz létre, amelyeket betölthet a PostgreSQL-adatbázisba. A ora2pg olyan feladatokhoz használhatja, mint például a fordított mérnöki Oracle-adatbázisok, a hatalmas vállalati adatbázisok áttelepítése, vagy egyszerűen csak néhány Oracle-adat replikálása egy PostgreSQL-adatbázisba. Az eszköz könnyen használható, és az Oracle Database-hez való kapcsolódáshoz szükséges paraméterek megadásának lehetősége mellett nem igényel Oracle Database-ismereteket.

> [!NOTE]
> A ora2pg legújabb verziójának használatával kapcsolatos további információkért tekintse meg a [ora2pg dokumentációját](https://ora2pg.darold.net/documentation.html).

### <a name="typical-ora2pg-migration-architecture"></a>Tipikus ora2pg-áttelepítési architektúra

![Képernyőkép a ora2pg áttelepítési architektúráról.](media/howto-migrate-from-oracle/ora2pg-migration-architecture.png)

A virtuális gép és a Azure Database for PostgreSQL üzembe helyezése után két konfiguráció szükséges a közöttük való kapcsolat engedélyezéséhez: az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** és az **SSL-kapcsolat kikényszerítés**: 

- **A kapcsolatbiztonsági** panel > **engedélyezi az Azure-szolgáltatásokhoz való hozzáférést**  >  

- **Kapcsolatbiztonsági** panel > **SSL-beállítások**  >  **kényszerített SSL-kapcsolat**  >  **Letiltva**

### <a name="recommendations"></a>Javaslatok

- A felmérési vagy exportálási műveletek teljesítményének növeléséhez az Oracle-kiszolgálón gyűjtsön statisztikai adatokat:

   ```
   BEGIN
   
     DBMS_STATS.GATHER_SCHEMA_STATS
     DBMS_STATS.GATHER_DATABASE_STATS
     DBMS_STATS.GATHER_DICTIONARY_STATS
     END;
   ```

- Az adatexportálást a `COPY` parancs helyett a paranccsal exportálhatja `INSERT` .

- Kerülje a táblák külső kulcsokkal (FKs), korlátozásokkal és indexekkel való exportálását. Ezek az elemek lelassítják az adatimportálás folyamatát a PostgreSQL-ben.

- Hozzon létre a *nem adatzáradék* használatával létrehozott nézeteket. Ezután frissítse a nézeteket később.

- Ha lehetséges, használjon egyedi indexeket az anyagmintás nézetekben. Ezek az indexek felgyorsítják a frissítést a szintaxis használatakor `REFRESH MATERIALIZED VIEW CONCURRENTLY` .


## <a name="premigration"></a>Előzetes áttelepítési 

Miután meggyőződött arról, hogy a forrás-környezet támogatott, és hogy minden előfeltétele teljesült, készen áll az előtelepítési fázis elindítására. A kezdéshez: 

1. Leltárba kell vennie az áttelepíteni kívánt adatbázisokat. 
1. Ezeket az adatbázisokat a lehetséges áttelepítési problémák vagy blokkolók alapján kell felmérni.
1. Javítsa ki a feltárt elemeket. 
 
Ahhoz, hogy a különböző áttelepítések, például az Oracle Azure Database for PostgreSQL, ebben a fázisban a forrás adatbázis-sémák is kompatibilisek a célként megadott környezettel.

### <a name="discover"></a>Felderítés

A felderítési fázis célja, hogy azonosítsa a meglévő adatforrásokat és a használatban lévő funkciók részleteit. Ebben a fázisban könnyebben megismerheti és megtervezheti az áttelepítést. A folyamat a hálózat vizsgálatával azonosítja az összes szervezet Oracle-példányát, valamint a használatban lévő verziót és szolgáltatásokat.

Az Oracle-hez készült Microsoft előértékelési parancsfájlok az Oracle Database-ben futnak. Az előértékelési parancsfájlok az Oracle-metaadatokat kérdezik le. A szkriptek a következőket biztosítják:

- Egy adatbázis-leltár, beleértve az objektumok mennyiségét a séma, a típus és az állapot alapján.
- Az egyes sémákban a statisztikán alapuló nyers adatok durva becslése.
- A táblák mérete az egyes sémákban.
- A csomagok, függvények, eljárások és egyéb kódok sorainak száma.

Töltse le a kapcsolódó parancsfájlokat a [ora2pg webhelyről](https://ora2pg.darold.net/).

### <a name="assess"></a>Kiértékelés

Az Oracle-adatbázisok leltározása után az adatbázis méretével és a lehetséges kihívásokkal kapcsolatos elképzeléssel fog rendelkezni. A következő lépés az értékelés futtatása.

Az Oracle-ről a PostgreSQL-re való Migrálás költségeit nem könnyű megbecsülni. Az áttelepítési költségeket a ora2pg ellenőrzi, hogy az összes adatbázis-objektum, függvény és tárolt eljárás szerepel-e az objektumokhoz és a PL/SQL-kódokhoz, amelyeket nem tud automatikusan konvertálni.

A ora2pg eszköz olyan Content Analysis móddal rendelkezik, amely megvizsgálja az Oracle-adatbázist egy szöveges jelentés létrehozásához. A jelentés leírja, hogy az Oracle-adatbázis mit tartalmaz, és mit nem lehet exportálni.

Az *elemzési és jelentési* mód aktiválásához használja az exportált típust az `SHOW_REPORT` alábbi parancsban látható módon:

```
ora2pg -t SHOW_REPORT
```

A ora2pg eszköz képes átalakítani az SQL-t és a com/SQL-kódot az Oracle-szintaxisból a PostgreSQL-re. Így az adatbázis elemzése után a ora2pg megbecsülheti a kód nehézségeit és a teljes adatbázis átadásához szükséges időt.

A ora2pg emberi napokon történő áttelepítésének megbecsléséhez használhatja a nevű konfigurációs direktívát `ESTIMATE_COST` . Ezen irányelv a parancssorban is engedélyezhető:

```
ora2pg -t SHOW_REPORT --estimate_cost
```

Az alapértelmezett áttelepítési egység körülbelül öt percet képvisel a PostgreSQL-szakértők számára. Ha az áttelepítés az első, az alapértelmezett áttelepítési egységet a konfigurációs irányelv `COST_UNIT_VALUE` vagy a parancssori kapcsoló használatával növelheti `--cost_unit_value` .

A jelentés utolsó sora a teljes becsült áttelepítési kódot mutatja az emberi napokon. A becslés az egyes objektumokra becsült áttelepítési egységek számát követi.

Ez az áttelepítési egység körülbelül öt percet képvisel a PostgreSQL-szakértők esetében. Ha az áttelepítés az első, az alapértelmezett beállítások a konfigurációs irányelv `COST_UNIT_VALUE` vagy a parancssori kapcsoló használatával is megnövelhető `--cost_unit_value` . 

A következő példában az értékelés néhány változatát láthatja: 
* Táblák értékelése
* Oszlopok értékelése
* Séma-értékelés, amely 5 perces alapértelmezett költséghely-egységet használ
* Séma-értékelés, amely 10 perces egységköltséget használ

```
ora2pg -t SHOW_TABLE -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\tables.txt ora2pg -t SHOW_COLUMN -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\columns.txt
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf --dump_as_html --estimate_cost > c:\ts303\hr
_migration\reports\report.html
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf –-cost_unit_value 10 --dump_as_html --estimate_cost > c:\ts303\hr_migration\reports\report2.html
```

Itt látható a séma-Assessment áttelepítési szintjének (B-5) kimenete:

* Áttelepítési szintek:

  * A-Migrálás, amely automatikusan futtatható
    
  * B – Migrálás a kód újraírásával és az emberi napok ára 5 nap
    
  * C – Migrálás a kód újraírásával és az emberi napokra 5 nap alatt
    
* Technikai szintek:

   * 1 = triviális: nincsenek tárolt függvények és triggerek

   * 2 = egyszerű: nincsenek tárolt függvények, de triggerek; nincs kézi újraírás

   * 3 = egyszerű: tárolt függvények és/vagy triggerek; nincs kézi újraírás

   * 4 = manuális: nincsenek tárolt függvények, de a kód újraírásával eseményindítók vagy nézetek

   * 5 = nehéz: tárolt függvények és/vagy triggerek kód újraírásával

Az értékelés a következőkből áll: 
* Egy betű (A vagy B) annak megadásához, hogy az áttelepítés kézi újraírást igényel-e.

* Egy 1 és 5 közötti szám, amely a technikai nehézségeket jelzi. 

Egy másik lehetőség, `-human_days_limit` amely meghatározza az emberi napok korlátját. Itt úgy állíthatja be az áttelepítési szintet C értékre, hogy az áttelepítés nagy mennyiségű munkát, teljes projektmenedzsmentet és áttelepítési támogatást igényel. Az alapértelmezett érték 10 emberi nap. A konfigurációs irányelv segítségével `HUMAN_DAYS_LIMIT` véglegesen módosíthatja az alapértelmezett értéket.

Ez a séma-értékelés úgy lett kialakítva, hogy segítse a felhasználókat abban, hogy melyik adatbázist kell áttelepíteniük, és melyik csapatokat kell mozgósítani.

### <a name="convert"></a>Konvertálás

Minimális állásidő esetén az áttelepítés forrása megváltozik. Az egyszeri áttelepítést követően az adatok és a séma alapján sodródik a céltól. Az *adatszinkronizálási* fázisban győződjön meg arról, hogy a forrás összes módosítása rögzítve van, és a cél felé közel valós időben lesz alkalmazva. Miután meggyőződött arról, hogy *a rendszer az* összes módosítást alkalmazza a célra, a forrásról a célként megadott környezetre is kikerül.

Az áttelepítés ebben a lépésében az Oracle-kód és a DDL-parancsfájlok a PostgreSQL-re lesznek konvertálva vagy lefordítva. A ora2pg eszköz automatikusan exportálja az Oracle-objektumokat a PostgreSQL-formátumban. Néhány generált objektum nem fordítható le a PostgreSQL-adatbázisban kézi változtatások nélkül.  

Annak megismeréséhez, hogy mely elemek szükségesek a manuális beavatkozáshoz, először fordítsa le a ora2pg által generált fájlokat a PostgreSQL-adatbázisra. Ellenőrizze a naplót, majd végezze el a szükséges módosításokat, amíg a séma szerkezete nem kompatibilis a PostgreSQL-szintaxissal.


#### <a name="create-a-migration-template"></a>Áttelepítési sablon létrehozása 

Javasoljuk, hogy használja a ora2pg által biztosított áttelepítési sablont. A beállítások használatakor a `--project_base` `--init_project` ora2pg létrehoz egy munkafáját, egy konfigurációs fájlt és egy parancsfájlt, amely az összes objektumot exportálja az Oracle-adatbázisból. További információkért tekintse meg a [ora2pg dokumentációját](https://ora2pg.darold.net/documentation.html).

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

A `sources/` könyvtár tartalmazza az Oracle-kódot. A `schema/` könyvtár tartalmazza a PostgreSQL-re portolt kódot. A `reports/` könyvtár pedig tartalmazza a HTML-jelentéseket és az áttelepítési költségeket.


A projekt struktúrájának létrehozása után létrejön egy általános konfigurációs fájl. Adja meg az Oracle Database-kapcsolatokat és a megfelelő konfigurációs paramétereket a konfigurációs fájlban. A konfigurációs fájllal kapcsolatos további információkért tekintse meg a [ora2pg dokumentációját](https://ora2pg.darold.net/documentation.html).


#### <a name="export-oracle-objects"></a>Oracle-objektumok exportálása

Ezután exportálja az Oracle-objektumokat PostgreSQL-objektumokként a *export_schema. sh* fájl futtatásával.

```
cd /app/migration/mig_project
./export_schema.sh
```

Futtassa manuálisan a következő parancsot.

```
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

Az adatok kinyeréséhez használja a következő parancsot.

```
ora2pg -t COPY -o data.sql -b %namespace/data -c %namespace/config/ora2pg.conf
```

#### <a name="compile-files"></a>Fájlok fordítása

Végül fordítsa le az összes fájlt a Azure Database for PostgreSQL-kiszolgálóval. Megadhatja a manuálisan létrehozott DDL-fájlok betöltését, vagy a második parancsfájlt ( *import_all. sh* ) a fájlok interaktív importálásához.

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-

server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l

%namespace%\ schema\sequences\create_sequences.log

psql -f %namespace%\schema\tables\table.sql -h server1-server.postgres.database.azure.com p 5432 -U username@server1-server -d database -l    %namespace%\schema\tables\create_table.log
```

Itt látható az Adatimportálási parancs:

```
psql -f %namespace%\data\table1.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table1.log

psql -f %namespace%\data\table2.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table2.log
```

A fájlok lefordítása közben keresse meg a naplókat, és javítsa ki azokat a szintaxisokat, amelyeket a ora2pg nem tudott saját maga alakítani.

További információkért tekintse [meg az Oracle Azure Database for PostgreSQL áttelepítési megkerülő megoldásait](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf)ismertető témakört.

## <a name="migrate"></a>Migrate 

Miután elvégezte a szükséges előfeltételeket, és végrehajtotta az áttelepítés előtti lépéseket, elindíthatja a séma és az adatáttelepítés folyamatát.

### <a name="migrate-schema-and-data"></a>Séma és az adatáttelepítés

Ha elvégezte a szükséges javításokat, az adatbázis stabil buildje készen áll a telepítésre. Futtassa az `psql` importálási parancsokat, és mutasson a módosított kódot tartalmazó fájlokra. Ez a feladat lefordítja az adatbázis-objektumokat a PostgreSQL-adatbázisra, és importálja az adatmennyiséget.

Ebben a lépésben az adatimportálás szintjének párhuzamos megvalósítását is megvalósíthatja.

### <a name="sync-data-and-cut-over"></a>Az adatszinkronizálás és a Kivágás

Online (minimális állásidő) áttelepítések esetén az áttelepítési forrás továbbra is megváltozik. Az egyszeri áttelepítést követően az adatok és a séma alapján sodródik a céltól. 

Az *adatszinkronizálási* fázisban győződjön meg arról, hogy a forrás összes módosítása rögzítve van, és a cél felé közel valós időben lesz alkalmazva. Miután meggyőződött arról, hogy az összes módosítás alkalmazva lett, a forrásról a célként megadott környezetre lehet átvágni.

Online Migrálás esetén forduljon AskAzureDBforPostgreSQL@service.microsoft.com a támogatási szolgálathoz.

Az ora2pg-t használó *Delta/növekményes* Migrálás esetében az egyes táblák esetében olyan lekérdezést használjon, amely dátum, idő vagy más paraméter alapján szűri (*csökkenti a vágásokat*). Ezután fejezze be az áttelepítést egy második lekérdezéssel, amely áttelepíti a fennmaradó adatfájlokat.

A forrásadatok táblázatban először telepítse át az összes korábbi adatforrást. Bemutatunk egy példát:

```
select * from table1 where filter_data < 01/01/2019
```

A kezdeti áttelepítés óta a következő parancs futtatásával kérdezheti le a változásokat:

```
select * from table1 where filter_data >= 01/01/2019
```

Ebben az esetben javasoljuk, hogy javítsa az érvényesítést az adatparitás mindkét oldalon, a forráson és a célon való ellenőrzésével.

## <a name="postmigration"></a>Postmigration 

Az *áttelepítés* után végezze el a postmigration feladatok elvégzését, és győződjön meg arról, hogy minden a lehető leggördülékenyebb és hatékony működést biztosít.

### <a name="remediate-applications"></a>Alkalmazások szervizelése

Miután áttelepítette az adatátvitelt a cél környezetbe, az összes olyan alkalmazásnak, amely korábban használta a forrást, el kell kezdenie a cél felhasználását. A telepítő időnként módosításokat igényel az alkalmazásokban.

### <a name="test"></a>Tesztelés

Miután áttelepítette az adatátvitelt a célhelyre, futtasson teszteket az adatbázisokon annak ellenőrzéséhez, hogy az alkalmazások megfelelően működnek-e a céllal. Győződjön meg arról, hogy a forrás és a cél megfelelően át van-e telepítve a manuális adatellenőrzési parancsfájlok futtatásával az Oracle-forrás és a PostgreSQL-cél adatbázisain.

Ideális esetben, ha a forrás-és a célként megadott adatbázisok hálózati elérési úttal rendelkeznek, ora2pg kell használni az adatellenőrzéshez. A `TEST` művelettel ellenőrizheti, hogy az Oracle-adatbázis összes objektuma létre lett-e hozva a PostgreSQL-ben. 

Futtassa ezt a parancsot:

```
ora2pg -t TEST -c config/ora2pg.conf > migration_diff.txt
```

### <a name="optimize"></a>Optimalizálás

Az postmigration fázis elengedhetetlen az adatok pontosságával kapcsolatos problémák összeegyeztetéséhez és a teljesség ellenőrzéséhez. Ebben a fázisban a munkaterheléssel kapcsolatos teljesítményproblémák is megtalálhatók.

## <a name="migration-assets"></a>Áttelepítési eszközök 

Az áttelepítési forgatókönyvvel kapcsolatos további információkért tekintse meg a következő forrásokat. Támogatják a valós migrációs projektekkel kapcsolatos elkötelezettséget.

| Erőforrás | Leírás    |
| -------------- | ------------------ |
| [Oracle – Azure PostgreSQL Migration Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf) | Ez a dokumentum segít az építészek, a tanácsadók, az adatbázis-rendszergazdák és a kapcsolódó szerepkörök számára, hogy gyorsan áttelepítse az Oracle-ből a munkaterheléseket az ora2pg használatával Azure Database for PostgreSQL |
| [Oracle – Azure PostgreSQL áttelepítési megkerülő megoldások](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf) | Ez a dokumentum segít az építészek, a tanácsadók, az adatbázis-rendszergazdák és a kapcsolódó szerepkörök gyors javításában és megoldásában a számítási feladatok Oracle-ből Azure Database for PostgreSQLba való áttelepítése során. |
| [A ora2pg Windows vagy Linux rendszeren való telepítésének lépései](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)                       | Ez a dokumentum egy gyors telepítési útmutatót biztosít a séma és az adatok Oracle-ről Azure Database for PostgreSQLre való áttelepítéséhez a ora2pg használatával Windows vagy Linux rendszeren. További információkért tekintse meg a [ora2pg dokumentációját](http://ora2pg.darold.net/documentation.html). |

Az adatsql mérnöki csapat fejlesztette ezeket az erőforrásokat. A csapat alapszintű alapokmánya az adatplatform-áttelepítési projektek Microsoft Azure adatplatformra való felgyorsításának feloldása és felgyorsítása.

## <a name="more-support"></a>További támogatás

A ora2pg-eszközök hatókörén túli áttelepítési segítségért forduljon az [ @Ask Azure db for PostgreSQL-hez](mailto:AskAzureDBforPostgreSQL@service.microsoft.com).

## <a name="next-steps"></a>Következő lépések

Az adatbázishoz és az adatok áttelepítéséhez, valamint a speciális feladatokhoz használható szolgáltatások és eszközök mátrixának megtekintéséhez lásd: [szolgáltatások és eszközök az adatok áttelepítéséhez](https://docs.microsoft.com/azure/dms/dms-tools-matrix).

Dokumentáció: 
- [Az Azure Database for PostgreSQL dokumentációja](https://docs.microsoft.com/azure/postgresql/)
- [a ora2pg dokumentációja](https://ora2pg.darold.net/documentation.html)
- [PostgreSQL-webhely](https://www.postgresql.org/)
- [Önálló tranzakciós támogatás a PostgreSQL-ben](http://blog.dalibo.com/2016/08/19/Autonoumous_transactions_support_in_PostgreSQL.html) 

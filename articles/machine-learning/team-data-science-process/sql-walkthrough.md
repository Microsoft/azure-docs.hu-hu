---
title: Modell létrehozása és üzembe helyezése egy SQL Server VM csoportos adatelemzési folyamatban
description: Hozzon létre és helyezzen üzembe egy gépi tanulási modellt a SQL Server használatával egy nyilvánosan elérhető adatkészlettel rendelkező Azure-beli virtuális gépen.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 580181aaaea975ee07bcec8108297079c5373b92
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96007409"
---
# <a name="the-team-data-science-process-in-action-using-sql-server"></a>A csoportos adatelemzési folyamat működés közben: a SQL Server használata
Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre és helyezhet üzembe gépi tanulási modellt SQL Server és nyilvánosan elérhető adatkészlettel – a [New York-i taxis](https://www.andresmh.com/nyctaxitrips/) adatkészlettel. Az eljárás egy szabványos adatelemzési munkafolyamatot követ: az adatgyűjtést és-elemzést, a mérnöki funkciókat a tanulás megkönnyítésére, majd a modellek elkészítésére és üzembe helyezésére.

## <a name="nyc-taxi-trips-dataset-description"></a><a name="dataset"></a>NYC taxi TRIPS adatkészlet leírása
A New York-i taxi Trip-adat körülbelül 20 GB tömörített CSV-fájlból áll (~ 48 GB tömörítetlen), amely több mint 173 000 000 egyedi utazást és az egyes utazásokhoz fizetett viteldíjat foglal magában. Az egyes utazási rekordok tartalmazzák a felvételi és a lemorzsolódási helyét és időpontját, a névtelen csapkod (illesztőprogram) licencének számát és a Digitális medál (a taxi egyedi azonosítóját). Az adat a 2013-as év összes utazására vonatkozik, és a következő két adatkészletben szerepel minden hónapban:

1. A (z) "trip_data" CSV-fájl tartalmazza az utazás részleteit, például az utasok számát, a felvételi és a lemorzsolódási, az utazási időtartamot és a menetidő hosszát. Íme néhány példa a rekordokra:
   
    `medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`

    `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

2. A (z) "trip_fare" CSV tartalmazza az egyes utazások díjait, például a fizetési típust, a viteldíjat, a pótdíjat, az adókat, a tippeket és az autópályadíjat, valamint a teljes fizetett összeget. Íme néhány példa a rekordokra:
   
    `medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`

    `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

Az utazáshoz és az utazási viteldíjhoz való csatlakozás egyedi kulcsa \_ a következő \_ mezőkből áll: emlékérem, Hack- \_ licenc és a pickup \_ DateTime.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Példák az előrejelzési feladatokra
Három előrejelzési problémát fogunk kialakítani a *Tipp \_ összege* alapján, nevezetesen:

* Bináris besorolás: megjósolhatja, hogy egy adott utazási tipp kifizetése megtörtént-e, azaz az $0-nál nagyobb *tip- \_ mennyiség* pozitív példa, míg az $0-es *tip- \_ mennyiség* negatív példa.
* Többosztályos besorolás: az utazáshoz fizetett tipp tartományának előrejelzése. A *Tipp \_ összegét* öt raktárhelyre vagy osztályra osztjuk:

   `Class 0 : tip_amount = $0`

   `Class 1 : tip_amount > $0 and tip_amount <= $5`

   `Class 2 : tip_amount > $5 and tip_amount <= $10`

   `Class 3 : tip_amount > $10 and tip_amount <= $20`

   `Class 4 : tip_amount > $20`

* Regressziós feladat: az utazáshoz fizetett tipp mennyiségének előrejelzése.  

## <a name="setting-up-the-azure-data-science-environment-for-advanced-analytics"></a><a name="setup"></a>Az Azure adatelemzési környezet beállítása a speciális elemzésekhez
Ahogy a [terv a környezeti](plan-your-environment.md) útmutatóban is látható, több lehetőség is használható a New York-i taxis adatkészlettel az Azure-ban:

* Az Azure-blobokban tárolt adatmennyiségek, majd a Model in Azure Machine Learning
* Töltse be az SQL Server adatbázisba, majd a modellbe Azure Machine Learning

Ebben az oktatóanyagban az adat párhuzamos tömeges importálását mutatjuk be egy SQL Serverba, az adatfeltárásba, a szolgáltatások fejlesztésére, valamint a SQL Server Management Studio és a IPython notebook használatával végzett mintavételezésre. [](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) A IPython- [jegyzetfüzetek](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) a githubon vannak megosztva. Az Azure-blobokban tárolt adatmennyiségű IPython-jegyzetfüzet ugyanazon a helyen is elérhető.

Az Azure-beli adatelemzési környezet beállítása:

1. [Tárfiók létrehozása](../../storage/common/storage-account-create.md)
2. [Azure Machine Learning-munkaterület létrehozása](../classic/create-workspace.md)
3. [Data Science Virtual Machine kiépítése](../data-science-virtual-machine/overview.md), amely egy SQL Server és egy IPython notebook-kiszolgálót biztosít.
   
   > [!NOTE]
   > A rendszer a telepítési folyamat során letölti a minta parancsfájlokat és a IPython jegyzetfüzeteket az adatelemzési virtuális gépre. Ha a virtuális gép telepítés utáni parancsfájlja befejeződik, a minták a virtuális gép dokumentumok könyvtárában lesznek:  
   > 
   > * Példák a parancsfájlokra: `C:\Users\<user_name>\Documents\Data Science Scripts`  
   > * Minta IPython notebookok: `C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
   >   hol `<user_name>` található a virtuális gép Windows-bejelentkezési neve. A minta **mappák példaként** jelennek meg a minta-és **minta-IPython jegyzetfüzetekben**.
   > 
   > 

Az adathalmaz mérete, az adatforrás helye és a kiválasztott Azure-cél környezet alapján ez a forgatókönyv hasonló az [ \# 5. forgatókönyvhöz: nagyméretű adatkészlet helyi fájlokban, célként SQL Server az Azure-beli virtuális gépen](plan-sample-scenarios.md#largelocaltodb).

## <a name="get-the-data-from-public-source"></a><a name="getdata"></a>Adatok beolvasása nyilvános forrásból
Az új virtuális gépre történő másolásához az adatok [áthelyezéséhez az Azure Blob Storage az adatok áthelyezése az Azure-ba és az Azure](move-azure-blob.md) -ba című témakörben leírt módszerek bármelyikét felhasználhatja, hogy a [New York](https://www.andresmh.com/nyctaxitrips/) -i (NYC)

Az Adatmásolás az AzCopy használatával:

1. Bejelentkezés a virtuális gépre (VM)
2. Hozzon létre egy új könyvtárat a virtuális gép adatlemezén (Megjegyzés: ne használja a virtuális géphez tartozó ideiglenes lemezt adatlemezként).
3. A parancssori ablakban futtassa a következő Azcopy-parancssort, és cserélje le a <path_to_data_folder>t a (2) elemben létrehozott adatmappába:

    ```console
    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
    ```

    Ha a AzCopy befejeződik, az Adatmappában összesen 24 tömörített CSV-fájlt (12 az utazási \_ és a 12 for Trip \_ viteldíjat) kell megadni.
4. Bontsa ki a letöltött fájlokat. Jegyezze fel a mappát, ahol a tömörítetlen fájlok találhatók. Ezt a mappát a rendszer az \_ \_ \_ adatfájlok <elérési útjaként fogja hivatkozni \> .

## <a name="bulk-import-data-into-sql-server-database"></a><a name="dbload"></a>SQL Server adatbázisba való tömeges importálás
A nagy mennyiségű adat egy SQL Databaseba való betöltésének és továbbításának, valamint az azt követő lekérdezéseknek a teljesítményét *particionált táblák és nézetek* használatával lehet javítani. Ebben a szakaszban a [párhuzamos tömeges adatimportálás az SQL Partition Tables használatával](parallel-load-sql-partitioned-tables.md) című rész útmutatását követve hozzon létre egy új adatbázist, és párhuzamosan töltse be az információkat particionált táblákba.

1. Ha bejelentkezett a virtuális gépre, indítsa el **SQL Server Management Studio**.
2. Kapcsolódjon a Windows-hitelesítés használatával.
   
    ![SSMS-kapcsolat][12]
3. Ha még nem módosította a SQL Server hitelesítési módot, és létrehozott egy új SQL-bejelentkezési felhasználót, nyissa meg a **change \_ auth. SQL** nevű parancsfájlt a **minta parancsfájlok** mappában. Módosítsa az alapértelmezett felhasználónevet és jelszót. A parancsfájl futtatásához kattintson a **végrehajtás** gombra az eszköztáron.
   
    ![Parancsfájl végrehajtása][13]
4. Ellenőrizze és/vagy módosítsa a SQL Server alapértelmezett adatbázis-és napló-mappákat, hogy az újonnan létrehozott adatbázisok adatlemezen legyenek tárolva. Az adattárolási terhelésre optimalizált SQL Server VM rendszerkép előre konfigurálva van az adatok és a naplók lemezekkel. Ha a virtuális gép nem tartalmaz adatlemezt, és új virtuális merevlemezeket adott a virtuálisgép-telepítési folyamat során, módosítsa az alapértelmezett mappákat a következőképpen:
   
   * Kattintson a jobb gombbal a SQL Server nevére a bal oldali panelen, majd kattintson a **Tulajdonságok** elemre.
     
       ![SQL Server tulajdonságai][14]
   * Válassza ki az **adatbázis-beállítások** elemet a bal oldali **lap kiválasztása** listából.
   * Ellenőrizze és/vagy módosítsa az **adatbázis alapértelmezett helyét** az Ön által választott **adatlemez** -helyekre. Ez a hely, ahol az új adatbázisok találhatók, ha az alapértelmezett beállításokkal lettek létrehozva.
     
       ![SQL Database alapértelmezett beállítások][15]  
5. Új adatbázis létrehozásához és a particionált táblák tárolására szolgáló fájlcsoportok megnyitásához nyissa meg a minta parancsfájl **create \_ db \_ default. SQL** parancsot. A szkript létrehoz egy **TaxiNYC** és 12 fájlcsoportok nevű új adatbázist az alapértelmezett adatelérési helyen. Minden fájlcsoportja egy hónapos utazási \_ és utazási díjszabási adatmennyiséget fog tárolni \_ . Szükség esetén módosítsa az adatbázis nevét. A szkript futtatásához kattintson a **végrehajtás** gombra.
6. Következő lépésként hozzon létre két partíciós táblát, egyet az utazáshoz, \_ és egy másikat az utazás \_ viteldíjához. Nyissa meg a parancsfájl **létrehozása \_ particionált \_ tábla. SQL** parancsot, amely a következőket teszi:
   
   * Hozzon létre egy Partition függvényt az adat hónapok szerinti felosztásához.
   * Hozzon létre egy particionálási sémát, amely az egyes hónapok összes adatfájlját egy másik fájlcsoportja rendeli.
   * Hozzon létre két particionált táblát a particionálási sémához: a **nyctaxi \_ Trip** az utazási adatvesztést \_ és a **nyctaxi \_ viteldíjat** fogja tárolni \_ .
     
     A parancsfájl futtatásához és a particionált táblák létrehozásához kattintson a **végrehajtás** gombra.
7. A **Sample Scripts** mappában két PowerShell-szkript van megadva, amelyek az adatSQL Server táblákba való párhuzamos tömeges importálást mutatják be.
   
   * a **BCP \_ Parallel \_generic.ps1** egy általános parancsfájl, amely párhuzamosan importálja az adatmennyiséget egy táblába. Módosítsa a parancsfájlt úgy, hogy a bemeneti és a cél változót a parancsfájl Megjegyzés soraiban megadott módon állítsa be.
   * a **BCP \_ Parallel \_nyctaxi.ps1** az általános parancsfájl előre konfigurált verziója, és mindkét tábla betöltésére használható a New York-i taxis szolgáltatáshoz.  
8. Kattintson a jobb gombbal a **BCP \_ Parallel \_nyctaxi.ps1** parancsfájl nevére, és kattintson a **Szerkesztés** elemre a PowerShellben való megnyitásához. Tekintse át az előre definiált változókat, és módosítsa a kiválasztott adatbázis nevét, a bemeneti adatok mappáját, a cél napló mappáját, valamint a minta formátumú fájlok **nyctaxi_trip.xml** és a **nyctaxi \_fare.xml** elérési útját (a **minta parancsfájlok** mappában található).
   
    ![Az adatmennyiség tömeges importálása][16]
   
    Azt is megteheti, hogy a hitelesítési módot választja, az alapértelmezett érték a Windows-hitelesítés. A futtatáshoz kattintson a zöld nyílra az eszköztáron. A szkript 24 tömeges importálási műveletet indít el párhuzamosan, 12 minden particionált táblához. Az Adatimportálási folyamat figyeléséhez nyissa meg az SQL Server alapértelmezett adatmappát a fentiekben leírtak szerint.
9. A PowerShell-parancsfájl a kezdési és befejezési időpontokat jelenti. Ha az összes tömeges importálás befejeződött, a befejezési idő jelentés. A cél napló mappájában ellenőrizze, hogy a tömeges importálások sikeresek voltak-e, azaz nem történtek-e hibák a cél napló mappában.
10. Az adatbázis most már készen áll a feltárásra, a szolgáltatások fejlesztésére és az egyéb, igény szerinti műveletekre. Mivel a táblák a **felvételi \_ dátum** /idő mező szerint vannak particionálva, a **Where** záradékban a **felvételi \_ datetime** feltételeket tartalmazó lekérdezések a partíciós séma előnyeit is kihasználhatják.
11. A **SQL Server Management Studioban** tekintse meg a példaként megadott parancsfájl- **\_ lekérdezéseket. SQL**. A lekérdezési sorok bármelyikének futtatásához jelölje ki a lekérdezések sorát, majd kattintson az eszköztáron a **végrehajtás** lehetőségre.
12. A New York-i taxi-körutazások adatkészlete két külön táblában van betöltve. Az illesztési műveletek javítása érdekében erősen ajánlott a táblák indexelése. A **\_ particionált \_ index létrehozásához** a parancsfájl létrehoz egy particionált indexeket a kompozit JOIN Key **medalion, a hack \_ License és a pickup \_ datetime** értékkel.

## <a name="data-exploration-and-feature-engineering-in-sql-server"></a><a name="dbexplore"></a>Adatfeltárási és-szolgáltatás-mérnöki SQL Server
Ebben a szakaszban az adatok feltárását és a szolgáltatás létrehozását fogjuk elvégezni, ha az SQL-lekérdezéseket közvetlenül a **SQL Server Management Studio** futtatja a korábban létrehozott SQL Server-adatbázis használatával. A Sample **Scripts** mappában szerepel egy **minta- \_ lekérdezések. SQL** nevű minta parancsfájl. Módosítsa a parancsfájlt úgy, hogy módosítsa az adatbázis nevét, ha az eltér az alapértelmezetttől: **TaxiNYC**.

Ebben a gyakorlatban a következőket tesszük:

* Kapcsolódjon **SQL Server Management Studio** Windows-hitelesítéssel vagy SQL-hitelesítéssel, valamint az SQL-bejelentkezési név és jelszó használatával.
* Ismerkedjen meg néhány mező adateloszlásával a különböző időtartományokban.
* Vizsgálja meg a hosszúsági és a szélességi mezők adatminőségét.
* Bináris és többosztályos besorolási Címkék készítése a **tip- \_ összeg** alapján.
* Szolgáltatások és számítások előállítása és az utazási távolságok összehasonlítása.
* Csatlakoztassa a két táblázatot, és bontson ki egy véletlenszerű mintát, amelyet a rendszer a modellek létrehozásához használ majd.

Ha készen áll a Azure Machine Learning folytatására, az alábbiakat teheti:  

1. Mentse a végső SQL-lekérdezést az adatok kinyeréséhez és mintavételéhez, és másolja be a lekérdezést közvetlenül a Azure Machine Learning [importálási][import-data] adatmodulba, vagy
2. Maradjon meg az új adatbázistábla modell-létrehozásához használni kívánt mintavételes és megtervezett adattípusok, és használja az új táblát a Azure Machine Learning [adatimportálás][import-data] moduljában.

Ebben a szakaszban a végső lekérdezést fogjuk menteni az adatok kinyerésére és mintavételezésére. A második módszer a IPython notebook szakaszának [Adatfeltárási és-szolgáltatás-mérnöki](#ipnb) szakaszában látható.

A sorok és oszlopok számának gyors ellenőrzése a korábban a párhuzamos tömeges importálás használatával kitöltve

- A táblázatban nyctaxi_trip sorok száma tábla vizsgálata nélkül: `SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')`
- A tábla oszlopainak nyctaxi_trip száma: `SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'`

#### <a name="exploration-trip-distribution-by-medallion"></a>Kutatás: utazások eloszlása a medál alapján
Ez a példa a medalion (taxiállomás) azonosítóját mutatja be egy adott időszakon belül több mint 100 utazással. A lekérdezés kihasználja a particionált tábla elérését, mivel azt a **felvételi \_ datetime** partíciós sémája feltétele. A teljes adatkészlet lekérdezése a particionált tábla és/vagy indexek vizsgálatát is igénybe veszi.

```sql
SELECT medallion, COUNT(*)
FROM nyctaxi_fare
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
GROUP BY medallion
HAVING COUNT(*) > 100
```

#### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Feltárás: az utazás terjesztése a medál és a hack_license között

```sql
SELECT medallion, hack_license, COUNT(*)
FROM nyctaxi_fare
WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
GROUP BY medallion, hack_license
HAVING COUNT(*) > 100
```

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Adatok minőségének felmérése: helytelen hosszúságú és/vagy szélességű rekordok ellenőrzése
Ez a példa azt vizsgálja, hogy a hosszúsági és/vagy szélességi mezők bármelyike érvénytelen értéket tartalmaz-e (a radián fok-90 és 90 között kell lennie), vagy (0, 0) koordinátákat kell tartalmaznia.

```sql
SELECT COUNT(*) FROM nyctaxi_trip
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
OR    (pickup_longitude = '0' AND pickup_latitude = '0')
OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))
```

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Feltárás: kiosztott és nem kiosztott utak eloszlása
Ez a példa megkeresi a kiosztott vagy a megadott időszakban nem leképezett utak számát (vagy a teljes adathalmazban, ha a teljes évre kiterjed). Ez a terjesztés azt a bináris címkék eloszlását tükrözi, amelyet később a bináris besorolás modellezéséhez kell használni.

```sql
SELECT tipped, COUNT(*) AS tip_freq FROM (
  SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
  FROM nyctaxi_fare
  WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
GROUP BY tipped
```

#### <a name="exploration-tip-classrange-distribution"></a>Feltárás: tipp osztály/tartomány eloszlása
Ez a példa a tip-tartományok adott időszakban (vagy a teljes adathalmazban, ha a teljes évre kiterjedő) eloszlását számítja ki. A címkézési osztályok ezen eloszlását később a többosztályos besorolás modellezéséhez fogjuk használni.

```sql
SELECT tip_class, COUNT(*) AS tip_freq FROM (
    SELECT CASE
        WHEN (tip_amount = 0) THEN 0
        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
        ELSE 4
    END AS tip_class
FROM nyctaxi_fare
WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
GROUP BY tip_class
```

#### <a name="exploration-compute-and-compare-trip-distance"></a>Feltárás: számítás és az utazási távolság összehasonlítása
Ez a példa átalakítja a felvételi és a lemorzsolódási hosszúságot és a szélességet az SQL-földrajzi pontokra, kiszámítja az utazási távolságot az SQL földrajzi pontok különbségével, és az összehasonlítás eredményeinek véletlenszerű mintáját adja vissza. A példában az eredmények érvényes koordinátákra vannak korlátozva, csak a korábban lefedett adatminőségi felmérési lekérdezés használatával.

```sql
SELECT
pickup_location=geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326)
,dropoff_location=geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326)
,trip_distance
,computedist=round(geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326).STDistance(geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326))/1000, 2)
FROM nyctaxi_trip
tablesample(0.01 percent)
WHERE CAST(pickup_latitude AS float) BETWEEN -90 AND 90
AND   CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
AND   pickup_longitude != '0' AND dropoff_longitude != '0'
```

#### <a name="feature-engineering-in-sql-queries"></a>Funkciók mérnöki funkciója SQL-lekérdezésekben
A címkék és a földrajz átalakítására szolgáló elemzési lekérdezések a számlálási rész eltávolításával is felhasználhatók címkék vagy szolgáltatások létrehozására. További mérnöki SQL-példákat az [adatelemzési és funkció-mérnöki IPython notebook](#ipnb) szakaszban talál. Hatékonyabban futtathatja a szolgáltatás-létrehozási lekérdezéseket a teljes adatkészletre vagy annak nagy részhalmazára az SQL-lekérdezések használatával, amelyek közvetlenül a SQL Server adatbázis-példányon futnak. A lekérdezések **SQL Server Management Studio**, IPython jegyzetfüzetben vagy bármely olyan fejlesztési eszközön vagy környezetben hajthatók végre, amely helyileg vagy távolról is hozzáfér az adatbázishoz.

#### <a name="preparing-data-for-model-building"></a>Az adatmodell-létrehozási adatelőkészítés
A következő lekérdezés összekapcsolja a **nyctaxi \_ Trip** és a **nyctaxi \_ viteldíj** -táblázatokat, létrehoz egy bináris besorolási címkét, egy többosztályos besorolási címke **Tipp \_ osztályt** **, és** Kinyer egy 1%-os véletlenszerű mintát a teljes csatlakoztatott adatkészletből. Ez a lekérdezés átmásolható közvetlenül a [Azure Machine learning Studio](https://studio.azureml.net) [Importálás][import-data] adatmodulba az Azure-beli SQL Server-adatbázis példányának közvetlen adatfeldolgozásához. A lekérdezés helytelen (0, 0) koordinátákat tartalmazó rekordokat hagy figyelmen kívül.

```sql
SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
    CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
    CASE WHEN (tip_amount = 0) THEN 0
        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
        ELSE 4
    END AS tip_class
FROM nyctaxi_trip t, nyctaxi_fare f
TABLESAMPLE (1 percent)
WHERE t.medallion = f.medallion
AND   t.hack_license = f.hack_license
AND   t.pickup_datetime = f.pickup_datetime
AND   pickup_longitude != '0' AND dropoff_longitude != '0'
```

## <a name="data-exploration-and-feature-engineering-in-ipython-notebook"></a><a name="ipnb"></a>Adatfelderítési és-funkció-mérnöki IPython notebookon
Ebben a szakaszban az adatelemzési és-szolgáltatás-létrehozási műveleteket hajtjuk végre a Python és az SQL lekérdezések használatával a korábban létrehozott SQL Server adatbázison. Egy **Machine-learning-adat-Science-Process-SQL-Story** nevű minta IPython jegyzetfüzet. a Ipynb a **minta IPython jegyzetfüzetek** mappában található. Ez a jegyzetfüzet a [githubon](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks)is elérhető.

Big data használatakor kövesse az alábbi javasolt sorozatot:

* Olvasson be egy kis mintát az adatból egy memóriában tárolt adatkeretbe.
* A mintavételen áttekinthető vizualizációk és felderítések elvégzése.
* Kísérletezzen a funkciók mérnöki adataival a mintavételezéssel.
* Ha nagyobb adatfeltárásra, adatkezelésre és-fejlesztésre van lehetőség, a Python használatával közvetlenül az Azure-beli virtuális gépen lévő SQL Server-adatbázissal lehet SQL-lekérdezéseket kiadni.
* Döntse el, hogy milyen méretű mintát kíván használni Azure Machine Learning modell létrehozásához.

Ha készen áll a Azure Machine Learning folytatására, a következők lehetnek:  

1. Mentse a végső SQL-lekérdezést az adatok kinyeréséhez és mintavételéhez, és másolja be a lekérdezést közvetlenül a Azure Machine Learning [Importálás][import-data] adatmodulba. Ez a módszer a Azure Machine Learning szakaszban bemutatott [építési modellekben](#mlmodel) látható.    
2. Maradjon meg az új adatbázis-táblában a modell-létrehozáshoz használni kívánt mintavételes és megtervezett adatértékek, majd használja az új táblát az [adatimportálás][import-data] modulban.

A következőkben néhány adat-feltárási, adatvizualizációs és funkció-mérnöki példa található. További példákért tekintse meg az SQL IPython jegyzetfüzetet a **minta IPython jegyzetfüzetek** mappában.

#### <a name="initialize-database-credentials"></a>Adatbázis hitelesítő adatainak inicializálása
Inicializálja az adatbázis-kapcsolatok beállításait a következő változók szerint:

```sql
SERVER_NAME=<server name>
DATABASE_NAME=<database name>
USERID=<user name>
PASSWORD=<password>
DB_DRIVER = <database server>
```

#### <a name="create-database-connection"></a>Adatbázis-kapcsolatok létrehozása

```sql
CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
conn = pyodbc.connect(CONNECTION_STRING)
```

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Sorok és oszlopok számának jelentése a táblában nyctaxi_trip

```sql
nrows = pd.read_sql('''
    SELECT SUM(rows) FROM sys.partitions
    WHERE object_id = OBJECT_ID('nyctaxi_trip')
''', conn)

print 'Total number of rows = %d' % nrows.iloc[0,0]

ncols = pd.read_sql('''
    SELECT COUNT(*) FROM information_schema.columns
    WHERE table_name = ('nyctaxi_trip')
''', conn)

print 'Total number of columns = %d' % ncols.iloc[0,0]
```

* Sorok száma összesen = 173179759  
* Oszlopok száma összesen = 14

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>Kis mennyiségű adatok beolvasása a SQL Server adatbázisából

```sql
t0 = time.time()

query = '''
    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
        f.tolls_amount, f.total_amount, f.tip_amount
    FROM nyctaxi_trip t, nyctaxi_fare f
    TABLESAMPLE (0.05 PERCENT)
    WHERE t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
'''

df1 = pd.read_sql(query, conn)

t1 = time.time()
print 'Time to read the sample table is %f seconds' % (t1-t0)

print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])
```

A minta tábla olvasásához szükséges idő 6,492000 másodperc  
A beolvasott sorok és oszlopok száma = (84952, 21)

#### <a name="descriptive-statistics"></a>Leíró statisztikák
Most már készen áll a mintavételen alapuló adatelemzések megismerésére. Első lépésként tekintse meg az **utazási \_ távolság** (vagy bármely más) mező (ke) t leíró statisztikát:

```sql
df1['trip_distance'].describe()
```

#### <a name="visualization-box-plot-example"></a>Vizualizáció: Box Plot – példa
A következő lépésben megtekintjük a quantiles megjelenítésére szolgáló útvonal távolságát

```sql
df1.boxplot(column='trip_distance',return_type='dict')
```

![#1 ábrázolása][1]

#### <a name="visualization-distribution-plot-example"></a>Vizualizáció: példa a terjesztési ábrára

```sql
fig = plt.figure()
ax1 = fig.add_subplot(1,2,1)
ax2 = fig.add_subplot(1,2,2)
df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
df1['trip_distance'].hist(ax=ax2, bins=100, color='k')
```

![#2 ábrázolása][2]

#### <a name="visualization-bar-and-line-plots"></a>Vizualizáció: sáv és vonal ábrázolása
Ebben a példában az utazási távolságot öt rekeszbe helyezzük, és megjelenítjük a dobozolási eredményeket.

```sql
trip_dist_bins = [0, 1, 2, 4, 10, 1000]
df1['trip_distance']
trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
trip_dist_bin_id
```

A fenti raktárhely-eloszlást egy sáv vagy egy grafikonon ábrázoljuk, az alábbiak szerint

```sql
pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')
```

![#3 ábrázolása][3]

```sql
pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')
```
![#4 ábrázolása][4]

#### <a name="visualization-scatterplot-example"></a>Vizualizáció: scatterplot példa
Az **\_ időpontot \_ \_ másodpercben** , a **\_ távolságot** pedig a menetidő között láthatjuk, hogy van-e korreláció

```sql
plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])
```

![#6 ábrázolása][6]

Hasonlóképpen ellenőrizhető a **ráta \_ kódja** és az **utazási \_ távolság** közötti kapcsolat.

```sql
plt.scatter(df1['passenger_count'], df1['trip_distance'])
```

![#8 ábrázolása][8]

### <a name="sub-sampling-the-data-in-sql"></a>Az SQL-ben tárolt adatSub-Sampling
Az [Azure Machine learning Studio](https://studio.azureml.net)-beli modellre való adatelőkészítés során dönthet úgy, hogy az **SQL-lekérdezést közvetlenül az Adatimportálási modulban használja** , vagy megtartja a tervezett és a mintavételes adatok egy új táblázatban, amelyet az [adatimportálási][import-data] modul egyszerű Select * paranccsal használhat az **\_ új \_ Táblanév \_><**.

Ebben a szakaszban egy új táblázatot hozunk létre, amely a mintául szolgáló és a megtervezett adathalmazokat fogja tárolni. A modell építésének közvetlen SQL-lekérdezési példáját a SQL Server szakaszban található [Adatfeltárási és-szolgáltatás-fejlesztés](#dbexplore) tartalmazza.

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Hozzon létre egy minta táblát, és töltse fel az illesztett táblázatok 1%-át. Ha létezik, először dobja el a táblázatot.
Ebben a szakaszban összekapcsoljuk a táblákat a **nyctaxi \_ Trip** -és **nyctaxi- \_ díjszabással**, kinyerjük az 1%-os véletlenszerű mintát, és egy új táblanév **nyctaxi egy \_ \_ százalékban** megőrzik a mintákat:

```sql
cursor = conn.cursor()

drop_table_if_exists = '''
    IF OBJECT_ID('nyctaxi_one_percent', 'U') IS NOT NULL DROP TABLE nyctaxi_one_percent
'''

nyctaxi_one_percent_insert = '''
    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount
    INTO nyctaxi_one_percent
    FROM nyctaxi_trip t, nyctaxi_fare f
    TABLESAMPLE (1 PERCENT)
    WHERE t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude <> '0' AND dropoff_longitude <> '0'
'''

cursor.execute(drop_table_if_exists)
cursor.execute(nyctaxi_one_percent_insert)
cursor.commit()
```

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>Adatelemzés az SQL-lekérdezések használatával a IPython notebookon
Ebben a szakaszban a fent létrehozott új táblázatban megőrzött 1%-os mintavételes adateloszlásokat vizsgáljuk. Hasonló feltárások hajthatók végre az eredeti táblák használatával, opcionálisan a **TABLESAMPLE** használatával korlátozhatja a feltárási mintát, vagy az eredményeket egy adott időszakra korlátozhatja a **pickup \_ datetime** partíciók használatával, ahogyan az a SQL Server szakaszban található [adatfelderítési és-funkciói mérnöki](#dbexplore) tevékenységekben látható.

#### <a name="exploration-daily-distribution-of-trips"></a>Feltárás: az utak napi eloszlása

```sql
query = '''
    SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
    FROM nyctaxi_one_percent
    GROUP BY CONVERT(date, dropoff_datetime)
'''

pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distribution-per-medallion"></a>Feltárás: Trip Distribution per emlékérem

```sql
query = '''
    SELECT medallion,count(*) AS c
    FROM nyctaxi_one_percent
    GROUP BY medallion
'''

pd.read_sql(query,conn)
```

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>A IPython notebookon SQL-lekérdezéseket használó funkciók generálása
Ebben a szakaszban új címkéket és szolgáltatásokat hozunk létre közvetlenül az SQL-lekérdezések használatával, amelyek az előző szakaszban létrehozott 1%-os minta táblában működnek.

#### <a name="label-generation-generate-class-labels"></a>Címke létrehozása: osztályok feliratainak létrehozása
A következő példában két, a modellezéshez használandó címkét állítunk be:

1. Bináris **osztályok feliratai** (előre jelezve, ha a tipp meg lesz adva)
2. Többosztályos címkék **Tipp \_ osztálya** (a tip-raktárhely vagy-tartomány előrejelzése)

```sql   
    nyctaxi_one_percent_add_col = '''
        ALTER TABLE nyctaxi_one_percent ADD tipped bit, tip_class int
    '''
   
    cursor.execute(nyctaxi_one_percent_add_col)
    cursor.commit()
   
    nyctaxi_one_percent_update_col = '''
        UPDATE nyctaxi_one_percent
        SET
           tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
           tip_class = CASE WHEN (tip_amount = 0) THEN 0
                            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                            ELSE 4
                        END
    '''
   
    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()
```

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>Szolgáltatások mérnöki jellemzői: kategorikus oszlopok száma
Ez a példa egy kategorikus mezőt alakít át egy numerikus mezőbe úgy, hogy lecseréli az egyes kategóriák számát az adatban előforduló előfordulások számával.

```sql
nyctaxi_one_percent_insert_col = '''
    ALTER TABLE nyctaxi_one_percent ADD cmt_count int, vts_count int
'''

cursor.execute(nyctaxi_one_percent_insert_col)
cursor.commit()

nyctaxi_one_percent_update_col = '''
    WITH B AS
    (
        SELECT medallion, hack_license,
            SUM(CASE WHEN vendor_id = 'cmt' THEN 1 ELSE 0 END) AS cmt_count,
            SUM(CASE WHEN vendor_id = 'vts' THEN 1 ELSE 0 END) AS vts_count
        FROM nyctaxi_one_percent
        GROUP BY medallion, hack_license
    )

    UPDATE nyctaxi_one_percent
    SET nyctaxi_one_percent.cmt_count = B.cmt_count,
        nyctaxi_one_percent.vts_count = B.vts_count
    FROM nyctaxi_one_percent A INNER JOIN B
    ON A.medallion = B.medallion AND A.hack_license = B.hack_license
'''

cursor.execute(nyctaxi_one_percent_update_col)
cursor.commit()
```

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>Feature Engineering: a numerikus oszlopokhoz tartozó bin-funkciók
Ez a példa egy folytonos numerikus mezőt alakít át előre definiált kategóriákba, azaz a numerikus mezőt egy kategorikus mezővé alakítja.

```sql
nyctaxi_one_percent_insert_col = '''
    ALTER TABLE nyctaxi_one_percent ADD trip_time_bin int
'''

cursor.execute(nyctaxi_one_percent_insert_col)
cursor.commit()

nyctaxi_one_percent_update_col = '''
    WITH B(medallion,hack_license,pickup_datetime,trip_time_in_secs, BinNumber ) AS
    (
        SELECT medallion,hack_license,pickup_datetime,trip_time_in_secs,
        NTILE(5) OVER (ORDER BY trip_time_in_secs) AS BinNumber from nyctaxi_one_percent
    )

    UPDATE nyctaxi_one_percent
    SET trip_time_bin = B.BinNumber
    FROM nyctaxi_one_percent A INNER JOIN B
    ON A.medallion = B.medallion
    AND A.hack_license = B.hack_license
    AND A.pickup_datetime = B.pickup_datetime
'''

cursor.execute(nyctaxi_one_percent_update_col)
cursor.commit()
```

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>Szolgáltatások mérnöki jellemzői: a hely funkcióinak kinyerése decimális szélesség/hosszúság alapján
Ez a példa egy szélességi és/vagy hosszúsági mező decimális megjelenítését bontja fel több, különböző részletességgel rendelkező régióba, például ország/régió, város, város, blokk stb. Az új geo-mezők nincsenek a tényleges helyükre leképezve. További információ a geocode-telephelyek hozzárendeléséről: [Bing Maps Rest Services](/bingmaps/rest-services/locations/find-a-location-by-point).

```sql
nyctaxi_one_percent_insert_col = '''
    ALTER TABLE nyctaxi_one_percent
    ADD l1 varchar(6), l2 varchar(3), l3 varchar(3), l4 varchar(3),
        l5 varchar(3), l6 varchar(3), l7 varchar(3)
'''

cursor.execute(nyctaxi_one_percent_insert_col)
cursor.commit()

nyctaxi_one_percent_update_col = '''
    UPDATE nyctaxi_one_percent
    SET l1=round(pickup_longitude,0)
        , l2 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 1 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),1,1) ELSE '0' END     
        , l3 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 2 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),2,1) ELSE '0' END     
        , l4 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 3 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),3,1) ELSE '0' END     
        , l5 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 4 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),4,1) ELSE '0' END     
        , l6 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 5 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),5,1) ELSE '0' END     
        , l7 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 6 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),6,1) ELSE '0' END
'''

cursor.execute(nyctaxi_one_percent_update_col)
cursor.commit()
```

#### <a name="verify-the-final-form-of-the-featurized-table"></a>A featurized táblázat végső formátumának ellenőrzése

```sql
query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
pd.read_sql(query,conn)
```

Most már készen áll az építés és a modell üzembe helyezésének modellezésére [Azure Machine Learningban](https://studio.azureml.net). Az adatgyűjtés készen áll a korábban azonosított előrejelzési problémákra, azaz:

1. Bináris besorolás: megjósolhatja, hogy fizetett-e borravalót egy útra.
2. Többosztályos besorolás: a kifizetett tipp tartományának előrejelzéséhez a korábban definiált osztályok alapján.
3. Regressziós feladat: az utazáshoz fizetett tipp mennyiségének előrejelzése.  

## <a name="building-models-in-azure-machine-learning"></a><a name="mlmodel"></a>Modellek kiépítése a Azure Machine Learningban
A modellezési gyakorlat elindításához jelentkezzen be Azure Machine Learning munkaterületére. Ha még nem hozott létre Machine learning-munkaterületet, tekintse meg [a Azure Machine learning munkaterület létrehozása](../classic/create-workspace.md)című témakört.

1. A Azure Machine Learning megkezdéséhez tekintse meg a [Mi az a Azure Machine learning Studio?](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)
2. Jelentkezzen be [Azure Machine learning Studioba](https://studio.azureml.net).
3. A Studio kezdőlapja rengeteg információt, videót, oktatóanyagokat, hivatkozásokat tartalmaz a modulok hivatkozásához és más erőforrásokhoz. További információ a Azure Machine Learningről: [Azure Machine learning dokumentációs központ](https://azure.microsoft.com/documentation/services/machine-learning/).

Egy tipikus betanítási kísérlet a következő lépésekből áll:

1. Hozzon létre egy **+ új** kísérletet.
2. Az Azure Machine Learningba való beolvasása.
3. Szükség szerint előre feldolgozhatja, átalakíthatja és kezelheti az adatfeldolgozást.
4. Szükség szerint állítson elő szolgáltatásokat.
5. Az adatokat kioszthatja képzési/ellenőrzési/tesztelési adatkészletekbe (vagy külön adatkészletekkel).
6. Válasszon ki egy vagy több gépi tanulási algoritmust attól függően, hogy milyen tanulási problémát kell megoldania. Például a bináris besorolás, a többosztályos besorolás, a regresszió.
7. Egy vagy több modell betanítása a betanítási adatkészlet használatával.
8. Az érvényesítési adatkészlet kiértékelése a betanított modell (ek) használatával.
9. Értékelje ki a modell (eke) t, hogy kiszámítsa a tanulási probléma releváns mérőszámait.
10. Hangolja be a modell (eke) t, és válassza ki a telepítendő legjobb modellt.

Ebben a gyakorlatban már megvizsgáltuk és megtervezjük az SQL Server-ban lévő adatot, és úgy döntöttünk, hogy a minta mérete bekerül a Azure Machine Learningba. Egy vagy több előrejelzési modell kiépítéséhez a következőket döntöttük:

1. Az adatok beolvasása **és a kimenet** szakaszban elérhető adatok [importálása][import-data] modul használatával Azure Machine learning adatokat. További információ: az [adatok importálása][import-data] modul referenciája lap.
   
    ![Az adatimportálás Azure Machine Learning][17]
2. Válassza a **Azure SQL Database** lehetőséget a **Tulajdonságok** panelen lévő **adatforrásként** .
3. Adja meg az adatbázis DNS-nevét az **adatbázis-kiszolgáló neve** mezőben. Formátumban `tcp:<your_virtual_machine_DNS_name>,1433`
4. Adja meg az **adatbázis nevét** a megfelelő mezőben.
5. Adja meg az **SQL-felhasználónevet** a **kiszolgáló felhasználói fiókjának nevében**, és a **jelszót** a **kiszolgáló felhasználói fiókjának jelszavában**.
7. Az **adatbázis-lekérdezés** szövegmezőben illessze be azt a lekérdezést, amely kibontja a szükséges adatbázis-mezőket (beleértve a kiszámított mezőket, például a címkéket), és a legördülő menüben az adatok kinyerését a kívánt minta méretre.

Az alábbi ábrán egy példa látható a bináris besorolási kísérletre, amely közvetlenül az SQL Server adatbázisból olvas be adatot. A többosztályos besoroláshoz és a regressziós problémákhoz hasonló kísérletek is létrehozhatók.

![Azure Machine Learning Train][10]

> [!IMPORTANT]
> Az előző szakaszban ismertetett modellezési adatok kinyerésére és mintavételezésére vonatkozó példákban a **három modellezési gyakorlat összes címkéje szerepel a lekérdezésben**. A modellezési gyakorlatok fontos (kötelező) lépése, hogy **kizárják** a másik két probléma felesleges címkéit, valamint az egyéb **megcélzott szivárgásokat**. Ha például bináris besorolást használ, használja a megjelenő **címkét** , és zárja ki a mezők **Tipp \_ osztályát**, a **Tipp \_ összegét** és a **teljes \_ mennyiséget**. Az utóbbi a célzott szivárgásokat célozza meg, mivel a borravalót befizették.
> 
> A szükségtelen oszlopok és/vagy célzott szivárgások kizárásához használja az [Oszlopok kiválasztása az adatkészlet][select-columns] modulban vagy a [metaadatok szerkesztése][edit-metadata]lehetőséget. További információ: [Oszlopok kiválasztása az adatkészletben][select-columns] és a [metaadatokat][edit-metadata] tartalmazó hivatkozások szerkesztése.
> 
> 

## <a name="deploying-models-in-azure-machine-learning"></a><a name="mldeploy"></a>Modellek üzembe helyezése Azure Machine Learning
Ha a modell elkészült, egyszerűen üzembe helyezheti webszolgáltatásként közvetlenül a kísérletből. Az Azure Machine Learning webszolgáltatások telepítésével kapcsolatos további információkért lásd: [Azure Machine learning webszolgáltatás üzembe](../classic/deploy-a-machine-learning-web-service.md)helyezése.

Új webszolgáltatás üzembe helyezéséhez a következőket kell tennie:

1. Pontozási kísérlet létrehozása.
2. Telepítse a webszolgáltatást.

A **befejezett** betanítási kísérletből származó pontozási kísérlet létrehozásához kattintson a **pontozási kísérlet létrehozása** lehetőségre az alsó műveleti sávon.

![Azure-pontozási][18]

Azure Machine Learning megpróbál létrehozni egy pontozási kísérletet a betanítási kísérlet összetevői alapján. Különösen a következőket kell tennie:

1. Mentse a betanított modellt, és távolítsa el a modell betanítási modulját.
2. Azonosítson egy logikai **bemeneti portot** , amely a várt bemeneti adatsémát jelképezi.
3. Azonosítson egy logikai **kimeneti portot** a várt webszolgáltatás kimeneti sémájának megjelenítéséhez.

A pontozási kísérlet létrehozásakor tekintse át és módosítsa a szükséges módon. Egy tipikus beállítás, ha a bemeneti adatkészletet és/vagy lekérdezést lecseréli egy olyanra, amely kizárja a címke mezőket, mivel ezek a címkék nem lesznek elérhetők a sémában a szolgáltatás hívásakor. Ajánlott továbbá a bemeneti adatkészlet és/vagy a lekérdezés méretének csökkentése néhány rekordra, amely elegendő a bemeneti séma jelzéséhez. A kimeneti port esetében gyakori, hogy kizárják az összes bemeneti mezőt, és csak a kimenetben szereplő **mutatókat** és a kimutatott **valószínűségeket** tartalmazzák a [DataSet oszlopok kiválasztása az adatkészlet][select-columns] modulban.

A minta pontozási kísérlet az alábbi ábrán látható. Ha készen áll a telepítésre, kattintson a **közzétételi webszolgáltatás** gombra az alsó műveleti sávon.

![Közzététel Azure Machine Learning][11]

Ahhoz, hogy bedugni, ebben az útmutatóban létrehozott egy Azure-beli adatelemzési környezetet, amely egy nagyméretű nyilvános adatkészlettel dolgozott, amely az adatok beszerzésének modellezésére és a Azure Machine Learning webszolgáltatás üzembe helyezésére szolgál.

### <a name="license-information"></a>Licencelési információk
Ez a minta-útmutató és a hozzá tartozó parancsfájlok és IPython-jegyzetfüzet (ek) a Microsoft által a MIT licenc alatt vannak megosztva. További részletekért olvassa el a GitHubon található mintakód könyvtárában lévő LICENSE.txt fájlt.

### <a name="references"></a>Hivatkozások
•    [Andrés MONROY NYC taxi TRIPS letöltési oldal](https://www.andresmh.com/nyctaxitrips/)  
•    [A New York-i taxis utazási adatvédelme Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)   
• A    [New York-i taxi és a limuzin Bizottság kutatási és statisztikai adatai](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[1]: ./media/sql-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sql-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sql-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sql-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sql-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sql-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sql-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sql-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sql-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sql-walkthrough/azuremltrain.png
[11]: ./media/sql-walkthrough/azuremlpublish.png
[12]: ./media/sql-walkthrough/ssmsconnect.png
[13]: ./media/sql-walkthrough/executescript.png
[14]: ./media/sql-walkthrough/sqlserverproperties.png
[15]: ./media/sql-walkthrough/sqldefaultdirs.png
[16]: ./media/sql-walkthrough/bulkimport.png
[17]: ./media/sql-walkthrough/amlreader.png
[18]: ./media/sql-walkthrough/amlscoring.png


<!-- Module References -->
[edit-metadata]: /azure/machine-learning/studio-module-reference/edit-metadata
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[import-data]: /azure/machine-learning/studio-module-reference/import-data
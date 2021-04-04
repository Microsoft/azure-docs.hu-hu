---
title: Modell létrehozása és üzembe helyezése az Azure szinapszis Analytics használatával – csoportos adatelemzési folyamat
description: Egy gépi tanulási modell létrehozása és üzembe helyezése az Azure szinapszis Analytics használatával nyilvánosan elérhető adatkészlettel.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, devx-track-python, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b638cb2b33f24220e7ceb852402862c707cc7bc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "93316000"
---
# <a name="the-team-data-science-process-in-action-using-azure-synapse-analytics"></a>A csoportos adatelemzési folyamat működés közben: az Azure szinapszis Analytics használata
Ebben az oktatóanyagban bemutatjuk, hogyan hozhat létre és helyezhet üzembe gépi tanulási modellt az Azure szinapszis Analytics használatával egy nyilvánosan elérhető adatkészlethez – a [New York-i taxis](https://www.andresmh.com/nyctaxitrips/) adatkészlethez. A bináris besorolási modell alapján megjósolható, hogy egy adott utazási tipp díjköteles-e.  A modellek többosztályos besorolást tartalmaznak (legyen szó vagy sem a tippről) és a regresszióról (a tip-összeg kifizetésének eloszlása).

Az eljárás a [csoportos adatelemzési folyamat (TDSP)](./index.yml) munkafolyamatát követi. Bemutatjuk, hogyan állíthatja be az adatelemzési környezetet, hogyan tölthető be az Azure szinapszis Analyticsbe az adat, és hogyan használható az Azure szinapszis Analytics vagy egy IPython notebook az adat-és mérnöki funkciók modellezéséhez. Ezután bemutatjuk, hogyan hozhat létre és helyezhet üzembe egy modellt Azure Machine Learning használatával.

## <a name="the-nyc-taxi-trips-dataset"></a><a name="dataset"></a>A New York-i taxis adatkészlete
A New York-i taxi Trip-adat körülbelül 20 GB tömörített CSV-fájlból áll (~ 48 GB tömörítetlen), amely több mint 173 000 000 egyedi utazást és az egyes utazásokhoz fizetett viteldíjat rögzíti. Az egyes utazási rekordok tartalmazzák a felvételi és a lemorzsolódási helyszíneit és időpontját, a névtelen csapkod (illesztőprogram) licencének számát és a digitális medált (a taxi egyedi AZONOSÍTÓját). Az adat a 2013-as év összes utazására vonatkozik, és a következő két adatkészletben szerepel minden hónapban:

1. Az **trip_data.csv** -fájl utazási adatokat tartalmaz, például az utasok számát, a felvételi és a lemorzsolódási, az utazási időtartamot és a menetidő hosszát. Íme néhány példa a rekordokra:

`medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`

`89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

2. A **trip_fare.csv** fájl tartalmazza az egyes utazások díjait, például a fizetési típust, a díjszabást, a pótdíjat, az adókat, a tippeket és az autópályadíjat, valamint a teljes fizetett összeget. Íme néhány példa a rekordokra:

`medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`

`89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

Az utazáshoz és az utazási viteldíjhoz való csatlakozáshoz használt **egyedi kulcs** \_ \_ a következő három mezőből áll:

* medál
* csapkod \_ licenc és
* felvételi \_ dátum és idő.

## <a name="address-three-types-of-prediction-tasks"></a><a name="mltasks"></a>Az előrejelzési feladatok három típusának kezelése
Három előrejelzési problémát fogalmazunk meg a *Tipp \_ összege* alapján, amely háromféle modellezési feladatot ábrázol:

1. **Bináris besorolás**: Ha meg szeretné jósolni, hogy egy adott utazási tipp kifizetése megtörtént-e, azaz az $0-nál nagyobb *Tipp- \_ összeg* pozitív példa, míg az $0-es *tip- \_ mennyiség* negatív példa.
2. **Többosztályos besorolás**: az utazáshoz fizetett tipp tartományának előrejelzése. A *Tipp \_ összegét* öt raktárhelyre vagy osztályra osztjuk:

`Class 0 : tip_amount = $0`

`Class 1 : tip_amount > $0 and tip_amount <= $5`

`Class 2 : tip_amount > $5 and tip_amount <= $10`

`Class 3 : tip_amount > $10 and tip_amount <= $20`

`Class 4 : tip_amount > $20`

3. **Regressziós feladat**: az utazáshoz fizetett tipp mennyiségének előrejelzése.

## <a name="set-up-the-azure-data-science-environment-for-advanced-analytics"></a><a name="setup"></a>Az Azure adatelemzési környezet beállítása a speciális elemzésekhez
Az Azure-beli adatelemzési környezet beállításához kövesse az alábbi lépéseket.

**Saját Azure Blob Storage-fiók létrehozása**

* Ha saját Azure Blob Storage-tárolót épít ki, válasszon egy földrajzi helyet az Azure Blob Storage-hoz a vagy a lehető legközelebb az **USA déli középső** régiójában, ahol a NYC-taxi adatai tárolódnak. Az adatok másolása a AzCopy használatával történik a nyilvános blob Storage-tárolóból a saját Storage-fiókjában lévő tárolóba. Minél közelebb az Azure Blob Storage-hoz az USA déli középső régiója, annál gyorsabb ez a feladat (4. lépés).
* Saját Azure Storage-fiók létrehozásához kövesse az [Azure Storage-fiókokról](../../storage/common/storage-account-create.md)című témakör lépéseit. Ügyeljen arra, hogy jegyezze fel az alábbi Storage-fiók hitelesítő adataihoz tartozó értékeket, mivel ezek az útmutató későbbi szakaszában is szükségesek.

  * **Storage-fiók neve**
  * **Storage-fiók kulcsa**
  * A **tároló neve** (amelyet az Azure Blob Storage-ban tárolni kíván)

**Az Azure szinapszis Analytics-példány kiépítése.**
Az Azure szinapszis Analytics-példány kiépítéséhez kövesse a [Azure Portal Azure szinapszis Analytics létrehozása és lekérdezése](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md) című dokumentumát. Győződjön meg arról, hogy a következő Azure szinapszis Analytics-beli hitelesítő adatokkal rendelkezik, amelyeket a későbbi lépésekben fog használni.

* **Kiszolgáló neve**: \<server Name> . database.Windows.net
* **SQLDW (adatbázis) neve**
* **Felhasználónév**
* **Jelszó**

**Telepítse a Visual studiót és a SQL Server Data Tools.** Útmutatásért lásd: [Bevezetés a Visual Studio 2019 for Azure szinapszis Analytics használatába](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-install-visual-studio.md).

**Kapcsolódjon az Azure szinapszis Analyticshez a Visual Studióval.** Útmutatásért lásd: 1 & 2. lépés az [SQL analyticshez való kapcsolódáshoz az Azure szinapszis Analyticsben](../../synapse-analytics/sql/connect-overview.md).

> [!NOTE]
> Futtassa a következő SQL-lekérdezést az Azure szinapszis Analyticsben létrehozott adatbázison (a kapcsolódási témakör 3. lépésében megadott lekérdezés helyett) a **főkulcs létrehozásához**.
>
>

```sql
BEGIN TRY
       --Try to create the master key
    CREATE MASTER KEY
END TRY
BEGIN CATCH
       --If the master key exists, do nothing
END CATCH;
```

**Hozzon létre egy Azure Machine Learning munkaterületet az Azure-előfizetésében.** Útmutatásért lásd: [Azure Machine learning munkaterület létrehozása](../classic/create-workspace.md).

## <a name="load-the-data-into-azure-synapse-analytics"></a><a name="getdata"></a>Az Azure szinapszis Analytics szolgáltatásba való betöltés
Nyisson meg egy Windows PowerShell-parancssori konzolt. A következő PowerShell-parancsok futtatásával töltse le a GitHubon megosztott SQL-parancsfájlokat egy helyi könyvtárba, amelyet a *-DestDir* paraméterrel adott meg. A *DestDir* paraméter értékét bármely helyi könyvtárra módosíthatja. Ha a *-DestDir* nem létezik, akkor a PowerShell-szkript hozza létre.

> [!NOTE]
> Előfordulhat, hogy **rendszergazdaként kell futtatnia** a következő PowerShell-parancsfájl futtatásakor, ha a *DestDir* -címtárhoz rendszergazdai jogosultság szükséges a létrehozásához vagy a írásához.
>
>

```azurepowershell
$source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
$ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
$wc = New-Object System.Net.WebClient
$wc.DownloadFile($source, $ps1_dest)
.\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'
```

A sikeres végrehajtás után a jelenlegi munkakönyvtára a *-DestDir-* ra változik. A következőhöz hasonló képernyőt láthat:

![Aktuális munkakönyvtár változásai][19]

A *-DestDir* futtassa a következő PowerShell-parancsfájlt rendszergazdai módban:

```azurepowershell
./SQLDW_Data_Import.ps1
```

Amikor a PowerShell-parancsfájl első alkalommal fut, a rendszer arra kéri, hogy adja meg az adatokat az Azure szinapszis Analytics és az Azure Blob Storage-fiókból. Ha a PowerShell-parancsfájl első alkalommal fut, az Ön által megadott hitelesítő adatokat a rendszer a jelen munkakönyvtárban található SQLDW. conf konfigurációs fájlba írja. A PowerShell-parancsfájl jövőbeli futtatása lehetőséget tartalmaz a konfigurációs fájl összes szükséges paraméterének olvasására. Ha módosítania kell néhány paramétert, megadhatja a képernyőn megjelenő paramétereket a konfigurációs fájl törlésével és a paraméterek értékeinek a megadásával, vagy módosíthatja a paramétereket úgy, hogy a SQLDW. conf fájlt szerkeszti a *-DestDir* könyvtárban.

> [!NOTE]
> Annak elkerülése érdekében, hogy a séma neve ne legyen ütközik az Azure Azure szinapszis Analyticsben már meglévő paraméterekkel, ha közvetlenül az SQLDW. conf fájlból olvas paramétereket, a SQLDW. conf fájlból egy 3 jegyű véletlenszerű számot ad hozzá a séma neveként az egyes futtatások alapértelmezett sémájáként. Előfordulhat, hogy a PowerShell-parancsfájl megkéri a séma nevének megadására: a név felhasználói belátása szerint megadható.
>
>

Ez a **PowerShell-parancsfájl** a következő feladatokat hajtja végre:

* **Letölti és telepíti a AzCopy**, ha a AzCopy még nincs telepítve

  ```azurepowershell
  $AzCopy_path = SearchAzCopy
  if ($AzCopy_path -eq $null){
         Write-Host "AzCopy.exe is not found in C:\Program Files*. Now, start installing AzCopy..." -ForegroundColor "Yellow"
      InstallAzCopy
      $AzCopy_path = SearchAzCopy
  }
      $env_path = $env:Path
      for ($i=0; $i -lt $AzCopy_path.count; $i++){
          if ($AzCopy_path.count -eq 1){
              $AzCopy_path_i = $AzCopy_path
          } else {
              $AzCopy_path_i = $AzCopy_path[$i]
          }
          if ($env_path -notlike '*' +$AzCopy_path_i+'*'){
              Write-Host $AzCopy_path_i 'not in system path, add it...'
              [Environment]::SetEnvironmentVariable("Path", "$AzCopy_path_i;$env_path", "Machine")
              $env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
              $env_path = $env:Path
          }
  ```

* **Adatok másolása a saját blob Storage-fiókjába** a nyilvános blobból a AzCopy

  ```azurepowershell
  Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
  $start_time = Get-Date
  AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
  $end_time = Get-Date
  $time_span = $end_time - $start_time
  $total_seconds = [math]::Round($time_span.TotalSeconds,2)
  Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
  Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"
  ```

* A következő parancsokkal tölti be az adatait a saját blob Storage-fiókjából **(a LoadDataToSQLDW. SQL futtatásával) az Azure szinapszis Analytics** szolgáltatásba.

  * Séma létrehozása

    ```sql
    EXEC (''CREATE SCHEMA {schemaname};'');
    ```

  * Adatbázis-hatókörű hitelesítő adat létrehozása

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
    WITH IDENTITY = ''asbkey'' ,
    Secret = ''{StorageAccountKey}''
    ```

  * Külső adatforrás létrehozása Azure Storage-blobhoz

    ```sql
    CREATE EXTERNAL DATA SOURCE {nyctaxi_trip_storage}
    WITH
    (
        TYPE = HADOOP,
        LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
        CREDENTIAL = {KeyAlias}
    )
    ;

    CREATE EXTERNAL DATA SOURCE {nyctaxi_fare_storage}
    WITH
    (
        TYPE = HADOOP,
        LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
        CREDENTIAL = {KeyAlias}
    )
    ;
    ```

  * Hozzon létre egy külső fájlformátumot egy CSV-fájlhoz. Az adatok tömörítése nem történik meg, és a mezők a cső karakterével vannak elválasztva.

    ```sql
    CREATE EXTERNAL FILE FORMAT {csv_file_format}
    WITH
    (
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS
        (
            FIELD_TERMINATOR ='','',
            USE_TYPE_DEFAULT = TRUE
        )
    )
    ;
    ```

  * Hozzon létre külső viteldíjat és utazási táblákat az Azure Blob Storage-beli New York-i taxi adatkészlethez.

    ```sql
    CREATE EXTERNAL TABLE {external_nyctaxi_fare}
    (
        medallion varchar(50) not null,
        hack_license varchar(50) not null,
        vendor_id char(3),
        pickup_datetime datetime not null,
        payment_type char(3),
        fare_amount float,
        surcharge float,
        mta_tax float,
        tip_amount float,
        tolls_amount float,
        total_amount float
    )
    with (
        LOCATION    = ''/nyctaxifare/'',
        DATA_SOURCE = {nyctaxi_fare_storage},
        FILE_FORMAT = {csv_file_format},
        REJECT_TYPE = VALUE,
        REJECT_VALUE = 12
    )

      CREATE EXTERNAL TABLE {external_nyctaxi_trip}
      (
             medallion varchar(50) not null,
             hack_license varchar(50)  not null,
             vendor_id char(3),
             rate_code char(3),
             store_and_fwd_flag char(3),
             pickup_datetime datetime  not null,
             dropoff_datetime datetime,
             passenger_count int,
             trip_time_in_secs bigint,
             trip_distance float,
             pickup_longitude varchar(30),
             pickup_latitude varchar(30),
             dropoff_longitude varchar(30),
             dropoff_latitude varchar(30)
      )
      with (
          LOCATION    = ''/nyctaxitrip/'',
          DATA_SOURCE = {nyctaxi_trip_storage},
          FILE_FORMAT = {csv_file_format},
          REJECT_TYPE = VALUE,
          REJECT_VALUE = 12
      )
    ```

    - Adatok betöltése az Azure Blob Storage külső tábláiból az Azure szinapszis Analyticsbe

      ```sql
      CREATE TABLE {schemaname}.{nyctaxi_fare}
      WITH
      (
          CLUSTERED COLUMNSTORE INDEX,
          DISTRIBUTION = HASH(medallion)
      )
      AS
      SELECT *
      FROM   {external_nyctaxi_fare}
      ;

      CREATE TABLE {schemaname}.{nyctaxi_trip}
      WITH
      (
          CLUSTERED COLUMNSTORE INDEX,
          DISTRIBUTION = HASH(medallion)
      )
      AS
      SELECT *
      FROM   {external_nyctaxi_trip}
      ;
      ```

    - Hozzon létre egy mintaadatok-táblázatot (NYCTaxi_Sample), majd szúrja be az adatait az útvonal és a viteldíjak tábláiban található SQL-lekérdezések kiválasztásával. (A forgatókönyv néhány lépésének ezt a táblázatot kell használnia.)

      ```sql
      CREATE TABLE {schemaname}.{nyctaxi_sample}
      WITH
      (
          CLUSTERED COLUMNSTORE INDEX,
          DISTRIBUTION = HASH(medallion)
      )
      AS
      (
          SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount,
          tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
          tip_class = CASE
                  WHEN (tip_amount = 0) THEN 0
                  WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                  WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                  WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                  ELSE 4
              END
          FROM {schemaname}.{nyctaxi_trip} t, {schemaname}.{nyctaxi_fare} f
          WHERE datepart("mi",t.pickup_datetime) = 1
          AND t.medallion = f.medallion
          AND   t.hack_license = f.hack_license
          AND   t.pickup_datetime = f.pickup_datetime
          AND   pickup_longitude <> ''0''
          AND   dropoff_longitude <> ''0''
      )
      ;
      ```

A tárolási fiókok földrajzi elhelyezkedése hatással van a betöltési időpontokra.

> [!NOTE]
> A saját blob Storage-fiókja földrajzi helyétől függően az adatok nyilvános blobból a privát Storage-fiókba való másolása akár 15 percet is igénybe vehet, és az adatoknak a Storage-fiókból az Azure Azure szinapszis Analyticsbe való betöltésének folyamata akár 20 percet is igénybe vehet.
>
>

El kell döntenie, hogy mi történik, ha duplikált forrás-és célfájl van.

> [!NOTE]
> Ha a nyilvános blob Storage-ból a saját blob Storage-fiókjába másolandó. CSV fájlok már szerepelnek a saját blob Storage-fiókjában, a AzCopy megkérdezi, hogy szeretné-e felülírni őket. Ha nem kívánja felülírni őket, írja be az **n** bemenetet, amikor a rendszer kéri. Ha felül szeretné írni az **összeset** , írja be **a** következőt:, ha a rendszer kéri. **A.** CSV fájlok egyenként is beírhatók.
>
>

![Kimenet a AzCopy][21]

Saját adatait is használhatja. Ha az adatai a valós életben lévő helyszíni gépen vannak, akkor továbbra is használhatja a AzCopy-t a helyszíni adatok saját Azure Blob Storage-ba való feltöltéséhez. Csak a **forrás** helyét kell módosítania, a `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"` PowerShell-parancsfájl AzCopy parancsában a helyi könyvtárba, amely az adatait tartalmazza.

> [!TIP]
> Ha az adatok már szerepelnek a saját Azure Blob Storage-ban a valós életben, akkor kihagyhatja a AzCopy lépést a PowerShell-szkriptben, és közvetlenül feltöltheti az adatait az Azure Azure szinapszis Analytics szolgáltatásba. Ehhez további módosításokat kell végrehajtania a parancsfájlban az adatformátumának megfelelően.
>
>

Ez a PowerShell-szkript az Azure szinapszis elemzési információit is csatlakoztatja az adatelemzési példában található fájlokhoz SQLDW_Explorations. SQL, SQLDW_Explorations. ipynb és a SQLDW_Explorations_Scripts. a. file.

Sikeres végrehajtás után a következőhöz hasonló képernyő jelenik meg:

![Sikeres parancsfájl-végrehajtás kimenete][20]

## <a name="data-exploration-and-feature-engineering-in-azure-synapse-analytics"></a><a name="dbexplore"></a>Az Azure szinapszis Analytics adatfelderítési és-funkciós mérnöki szolgáltatása
Ebben a szakaszban a **Visual Studio Adateszközeivel** közvetlenül az Azure szinapszis Analytics szolgáltatásban futtatott SQL-lekérdezéseket végzünk az adatok feltárásához és a szolgáltatások létrehozásához. Az ebben a szakaszban használt összes SQL-lekérdezés megtalálható a *SQLDW_Explorations. SQL* nevű minta parancsfájlban. Ez a fájl már le van töltve a helyi könyvtárba a PowerShell-szkripttel. Azt is lekérheti a [githubról](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql). A GitHubon található fájl azonban nem rendelkezik az Azure szinapszis Analytics-információkkal, amelyekhez csatlakoztatva van.

Kapcsolódjon az Azure szinapszis Analytics szolgáltatáshoz a Visual Studióval az Azure szinapszis Analytics bejelentkezési nevével és jelszavával, és nyissa meg az **SQL Object Explorer** az adatbázis és a táblák importálásának megerősítéséhez. Kérje le a *SQLDW_Explorations. SQL* fájlt.

> [!NOTE]
> Egy párhuzamos adatraktár (PDW) lekérdezési szerkesztőjének megnyitásához használja az **új lekérdezési** parancsot, amíg a PDW be van jelölve az **SQL Object Explorer**. A PDW nem támogatja a szabványos SQL-lekérdezési szerkesztőt.
>
>

A jelen szakaszban leírt adatelemzési és szolgáltatás-létrehozási feladatok típusai:

* Ismerkedjen meg néhány mező adateloszlásával a különböző időtartományokban.
* Vizsgálja meg a hosszúsági és a szélességi mezők adatminőségét.
* Bináris és többosztályos besorolási Címkék készítése a **tip- \_ összeg** alapján.
* Szolgáltatások és számítások előállítása és az utazási távolságok összehasonlítása.
* Csatlakoztassa a két táblázatot, és bontson ki egy véletlenszerű mintát, amelyet a rendszer a modellek létrehozásához használ majd.

### <a name="data-import-verification"></a>Adatimportálás ellenőrzése
Ezek a lekérdezések gyors ellenőrzést biztosítanak a táblák sorainak és oszlopainak a korábban a "Base" párhuzamos tömeges importálással való feltöltésekor.

--A tábla sorainak száma <nyctaxi_trip> tábla vizsgálata nélkül

   ```sql
   SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
   ```

– A táblázat <nyctaxi_trip oszlopainak száma>

   ```sql
   SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'
   ```

**Kimenet:** 173 179 759 és 14 oszlopot kell kapnia.

### <a name="exploration-trip-distribution-by-medallion"></a>Kutatás: utazások eloszlása a medál alapján
Ez a példában szereplő lekérdezés azokat a medálokat (taxi számokat) azonosítja, amelyek egy adott időszakon belül több mint 100 utazást teljesítenek. A lekérdezés kihasználja a particionált tábla elérését, mivel azt a **felvételi \_ datetime** partíciós sémája feltétele. A teljes adatkészlet lekérdezése a particionált tábla és/vagy indexek vizsgálatát is igénybe veszi.

```sql
SELECT medallion, COUNT(*)
FROM <schemaname>.<nyctaxi_fare>
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
GROUP BY medallion
HAVING COUNT(*) > 100
```

**Kimenet:** A lekérdezésnek egy táblázatot kell visszaadnia, amely meghatározza a 13 369 medálokat (taxikat) és a 2013-ben befejezett utak számát. Az utolsó oszlopban szerepel a befejezett utak számának száma.

### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Feltárás: az utazás terjesztése a medál és a hack_license között
Ez a példa azokat a medálokat (taxi számokat) és hack_license számokat (illesztőprogramokat) azonosítja, amelyek egy adott időszakon belül több mint 100-utazást teljesítenek.

```sql
SELECT medallion, hack_license, COUNT(*)
FROM <schemaname>.<nyctaxi_fare>
WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
GROUP BY medallion, hack_license
HAVING COUNT(*) > 100
```

**Kimenet:** A lekérdezésnek egy 13 369-sorral rendelkező táblázatot kell visszaadnia, amely meghatározza a 13 369-es autó-és illesztőprogram 2013-azonosítókat, amelyek több, a 100-as utakon történtek Az utolsó oszlopban szerepel a befejezett utak számának száma.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Adatok minőségének felmérése: helytelen hosszúságú és/vagy szélességű rekordok ellenőrzése
Ez a példa azt vizsgálja, hogy a hosszúsági és/vagy szélességi mezők bármelyike érvénytelen értéket tartalmaz-e (a radián fok-90 és 90 között kell lennie), vagy (0, 0) koordinátákat kell tartalmaznia.

```sql
SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
OR    (pickup_longitude = '0' AND pickup_latitude = '0')
OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))
```

**Kimenet:** A lekérdezés 837 467 olyan utakat ad vissza, amelyek érvénytelen hosszúságú és/vagy szélességi mezővel rendelkeznek.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Feltárás: kiosztott és nem kiosztott utak eloszlása
Ez a példa megkeresi a kiosztott körutazások számát, illetve azt a számot, amelyet a megadott időszakban nem adott meg (vagy a teljes adathalmazban, ha az itt beállított teljes évet tartalmazza). Ez a terjesztés azt a bináris címkék eloszlását tükrözi, amelyet később a bináris besorolás modellezéséhez kell használni.

```sql
SELECT tipped, COUNT(*) AS tip_freq FROM (
  SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
  FROM <schemaname>.<nyctaxi_fare>
  WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
GROUP BY tipped
```

**Kimenet:** A lekérdezésnek a következő tip-frekvenciákat kell visszaadnia az 2013-as évre: 90 447 622-as és 82 264 709 nem.

### <a name="exploration-tip-classrange-distribution"></a>Feltárás: tipp osztály/tartomány eloszlása
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
FROM <schemaname>.<nyctaxi_fare>
WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
GROUP BY tip_class
```

**Kimeneti**

| tip_class | tip_freq |
| --- | --- |
| 1 |82230915 |
| 2 |6198803 |
| 3 |1932223 |
| 0 |82264625 |
| 4 |85765 |

### <a name="exploration-compute-and-compare-trip-distance"></a>Feltárás: számítás és az utazási távolság összehasonlítása
Ez a példa átalakítja a felvételi és a lemorzsolódási hosszúságot és a szélességet az SQL-földrajzi pontokra, kiszámítja az utazási távolságot az SQL földrajzi pontok különbségével, és az összehasonlítás eredményeinek véletlenszerű mintáját adja vissza. A példában az eredmények érvényes koordinátákra vannak korlátozva, csak a korábban lefedett adatminőségi felmérési lekérdezés használatával.

```sql
/****** Object:  UserDefinedFunction [dbo].[fnCalculateDistance] ******/
SET ANSI_NULLS ON
GO

SET QUOTED_IDENTIFIER ON
GO

IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
  DROP FUNCTION fnCalculateDistance
GO

-- User-defined function to calculate the direct distance  in mile between two geographical coordinates.
CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

RETURNS float
AS
BEGIN
      DECLARE @distance decimal(28, 10)
      -- Convert to radians
      SET @Lat1 = @Lat1 / 57.2958
      SET @Long1 = @Long1 / 57.2958
      SET @Lat2 = @Lat2 / 57.2958
      SET @Long2 = @Long2 / 57.2958
      -- Calculate distance
      SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
      --Convert to miles
      IF @distance <> 0
      BEGIN
        SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
      END
      RETURN @distance
END
GO

SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
FROM <schemaname>.<nyctaxi_trip>
WHERE datepart("mi",pickup_datetime)=1
AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
AND pickup_longitude != '0' AND dropoff_longitude != '0'
```

### <a name="feature-engineering-using-sql-functions"></a>Az SQL functions funkcióinak fejlesztései
Az SQL functions esetenként hatékony lehetőséget biztosít a funkciók mérnöki működéséhez. Ebben az útmutatóban egy SQL-függvényt definiálunk a pickup és a lemorzsolódási helyei közötti közvetlen távolság kiszámításához. A **Visual Studio Adateszközeiben** a következő SQL-szkripteket futtathatja.

Itt látható a távolság függvényt definiáló SQL-parancsfájl.

```sql
SET ANSI_NULLS ON
GO

SET QUOTED_IDENTIFIER ON
GO

IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
  DROP FUNCTION fnCalculateDistance
GO

-- User-defined function calculate the direct distance between two geographical coordinates.
CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

RETURNS float
AS
BEGIN
      DECLARE @distance decimal(28, 10)
      -- Convert to radians
      SET @Lat1 = @Lat1 / 57.2958
      SET @Long1 = @Long1 / 57.2958
      SET @Lat2 = @Lat2 / 57.2958
      SET @Long2 = @Long2 / 57.2958
      -- Calculate distance
      SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
      --Convert to miles
      IF @distance <> 0
      BEGIN
        SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
      END
      RETURN @distance
END
GO
```

Íme egy példa arra, hogy ezt a függvényt hívja meg az SQL-lekérdezés funkcióinak létrehozásához:

– A függvényt a funkciók létrehozásához meghívó minta lekérdezés

   ```sql
SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
FROM <schemaname>.<nyctaxi_trip>
WHERE datepart("mi",pickup_datetime)=1
AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
AND pickup_longitude != '0' AND dropoff_longitude != '0'
   ```

**Kimenet:** Ez a lekérdezés létrehoz egy táblát (2 803 538 sorral) a felvételi és a lemorzsolódási szélességi és hosszúsági körökkel, valamint a megfelelő közvetlen távolságot mérföldben. Az első három sorra vonatkozó eredmények:

| (Sor száma) | pickup_latitude | pickup_longitude | dropoff_latitude | dropoff_longitude | DirectDistance |
| --- | --- | --- | --- | --- | --- |
| 1 |40,731804 |– 74,001083 |40,736622 |– 73,988953 |.7169601222 |
| 2 |40,715794 |-74, 010635 |40,725338 |– 74,00399 |.7448343721 |
| 3 |40,761456 |– 73,999886 |40,766544 |– 73,988228 |0.7037227967 |

### <a name="prepare-data-for-model-building"></a>Az adatmodell-létrehozási művelet előkészítése
A következő lekérdezés összekapcsolja a **nyctaxi \_ Trip** és a **nyctaxi \_ viteldíj** -táblázatokat, létrehoz egy bináris besorolási címkét, egy többosztályos besorolási címke **Tipp \_ osztályt** **, és** Kinyer egy mintát a teljes csatlakoztatott adatkészletből. A mintavétel az utazások egy részhalmazának beolvasásával történik a felvételi idő alapján.  Ez a lekérdezés átmásolható közvetlenül a [Azure Machine learning Studio (klasszikus)](https://studio.azureml.net) [Importálás][adatimportálási] modulba az Azure-beli SQL Database példányból származó közvetlen adatfeldolgozáshoz. A lekérdezés helytelen (0, 0) koordinátákat tartalmazó rekordokat hagy figyelmen kívül.

```sql
SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
    CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
    CASE WHEN (tip_amount = 0) THEN 0
        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
        ELSE 4
    END AS tip_class
FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
WHERE datepart("mi",t.pickup_datetime) = 1
AND   t.medallion = f.medallion
AND   t.hack_license = f.hack_license
AND   t.pickup_datetime = f.pickup_datetime
AND   pickup_longitude != '0' AND dropoff_longitude != '0'
```

Ha készen áll a Azure Machine Learning folytatására, az alábbiakat teheti:

1. Mentse a végső SQL-lekérdezést az adatok kinyeréséhez és mintavételéhez, majd másolja be a lekérdezést közvetlenül egy importálási adatokba, majd[importálja] az adatok importálása modulba a Azure Machine learning, vagy
2. Maradjon meg az új Azure szinapszis Analytics-táblázatban a modell-létrehozáshoz használni kívánt mintavételes és megtervezett adattípusok, és használja az új táblázatot az[adatimportálási] Adatmodul [importálása]Azure Machine Learningban. A korábbi lépésben a PowerShell-szkript elvégezte ezt a feladatot. Az adatok importálása modulból közvetlenül a táblázatból is olvashat.

## <a name="data-exploration-and-feature-engineering-in-ipython-notebook"></a><a name="ipnb"></a>Adatfelderítési és-funkció-mérnöki IPython notebookon
Ebben a szakaszban az adatok feltárását és a szolgáltatás létrehozását fogjuk elvégezni a Python-és SQL-lekérdezésekkel a korábban létrehozott Azure szinapszis Analytics használatával. Egy **SQLDW_Explorations. ipynb** nevű és egy Python-parancsfájlt tartalmazó minta IPython-jegyzetfüzetet **SQLDW_Explorations_Scripts.** a helyi könyvtárba letöltöttük. A [githubon](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW)is elérhetők. Ez a két fájl azonos a Python-parancsfájlokban. Ha nem rendelkezik IPython notebook-kiszolgálóval, akkor a Python-parancsfájlt a rendszer arra az esetre kapja meg. Ez a két minta Python-fájl a **python 2,7** alatt lett tervezve.

A IPython jegyzetfüzetben és a helyi gépre letöltött Python-parancsfájlban a szükséges Azure szinapszis-elemzési információkat a korábban a PowerShell-parancsfájl csatlakoztatta. A végrehajtható fájlok módosítás nélkül futtathatók.

Ha már beállított egy Azure Machine Learning munkaterületet, közvetlenül feltöltheti a minta IPython notebookot a AzureML IPython notebook szolgáltatásba, és megkezdheti a futtatását. Az alábbi lépésekkel töltheti fel a AzureML IPython notebook Service-be:

1. Jelentkezzen be a Azure Machine Learning munkaterületre, kattintson a felül található **Studio** elemre, majd a weblap bal oldalán kattintson a **jegyzetfüzetek** elemre.

    ![Kattintson a Studio, majd a JEGYZETFÜZETek elemre.][22]
2. Kattintson az **új** elemre a weblap bal alsó sarkában, és válassza a **Python 2** lehetőséget. Ezután adjon meg egy nevet a jegyzetfüzetnek, és kattintson a pipa jelre az új üres IPython-jegyzetfüzet létrehozásához.

    ![Kattintson az új, majd a Python 2 elemre.][23]
3. Kattintson a **Jupyter** szimbólumra az új IPython jegyzetfüzet bal felső sarkában.

    ![Kattintson a Jupyter szimbólumra][24]
4. Húzza a minta IPython jegyzetfüzetet a AzureML IPython notebook szolgáltatás **faoldalára** , és kattintson a **feltöltés** elemre. Ezt követően a rendszer feltölti a minta IPython notebookot a AzureML IPython notebook szolgáltatásba.

    ![Kattintson a feltöltés gombra][25]

A minta IPython-jegyzetfüzet vagy a Python-parancsfájl futtatásához a következő Python-csomagok szükségesek. Ha a AzureML IPython notebook szolgáltatást használja, akkor ezek a csomagok előre telepítve vannak.

- pandas
- numpy
- matplotlib
- pyodbc
- PyTables

A nagy mennyiségű Azure Machine Learning speciális analitikai megoldásainak létrehozásakor itt látható a javasolt folyamat:

* Olvasson be egy kis mintát az adatból egy memóriában tárolt adatkeretbe.
* A mintavételen áttekinthető vizualizációk és felderítések elvégzése.
* Kísérletezzen a funkciók mérnöki adataival a mintavételezéssel.
* Ha nagyobb adatfeltárásra, adatkezelésre és-fejlesztésre van lehetőség, a Python használatával közvetlenül az Azure szinapszis Analytics szolgáltatásban adhat ki SQL-lekérdezéseket.
* Döntse el, hogy a minta mérete megfelel-e a Azure Machine Learning modell létrehozásához.

A következőkben néhány adat-feltárási, adatvizualizációs és funkció-mérnöki példa található. További adatelemzések a minta IPython jegyzetfüzetben és a Python-parancsfájlban találhatók.

### <a name="initialize-database-credentials"></a>Adatbázis hitelesítő adatainak inicializálása
Inicializálja az adatbázis-kapcsolatok beállításait a következő változók szerint:

```sql
SERVER_NAME=<server name>
DATABASE_NAME=<database name>
USERID=<user name>
PASSWORD=<password>
DB_DRIVER = <database driver>
```

### <a name="create-database-connection"></a>Adatbázis-kapcsolatok létrehozása
Itt látható a kapcsolódási karakterlánc, amely létrehozza a kapcsolódást az adatbázishoz.

```sql
CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
conn = pyodbc.connect(CONNECTION_STRING)
```

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>A táblázatban <sorok és oszlopok számának jelentése nyctaxi_trip>

```sql
nrows = pd.read_sql('''
    SELECT SUM(rows) FROM sys.partitions
    WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
''', conn)

print 'Total number of rows = %d' % nrows.iloc[0,0]

ncols = pd.read_sql('''
    SELECT COUNT(*) FROM information_schema.columns
    WHERE table_name = ('<nyctaxi_trip>') AND table_schema = ('<schemaname>')
''', conn)

print 'Total number of columns = %d' % ncols.iloc[0,0]
```

* Sorok száma összesen = 173179759
* Oszlopok száma összesen = 14

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_fare"></a>A táblázatban <sorok és oszlopok számának jelentése nyctaxi_fare>

```sql
nrows = pd.read_sql('''
    SELECT SUM(rows) FROM sys.partitions
    WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_fare>')
''', conn)

print 'Total number of rows = %d' % nrows.iloc[0,0]

ncols = pd.read_sql('''
    SELECT COUNT(*) FROM information_schema.columns
    WHERE table_name = ('<nyctaxi_fare>') AND table_schema = ('<schemaname>')
''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]
```

* Sorok száma összesen = 173179759
* Oszlopok száma összesen = 11

### <a name="read-in-a-small-data-sample-from-the-azure-synapse-analytics-database"></a>Kis adatminta beolvasása az Azure szinapszis Analytics-adatbázisából

```sql
t0 = time.time()

query = '''
    SELECT TOP 10000 t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
        f.tolls_amount, f.total_amount, f.tip_amount
    FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
    WHERE datepart("mi",t.pickup_datetime) = 1
    AND   t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
'''

df1 = pd.read_sql(query, conn)

t1 = time.time()
print 'Time to read the sample table is %f seconds' % (t1-t0)

print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])
```

A minta tábla olvasásának ideje 14,096495 másodperc.
A beolvasott sorok és oszlopok száma = (1000, 21).

### <a name="descriptive-statistics"></a>Leíró statisztikák
Most már készen áll a mintavételen alapuló adatelemzések megismerésére. Első lépésként tekintse meg az **utazási \_ távolságot** (vagy bármely más, a megadható mezőt) leíró statisztikát.

```sql
df1['trip_distance'].describe()
```

### <a name="visualization-box-plot-example"></a>Vizualizáció: Box Plot – példa
Ezután az quantiles megjelenítéséhez keresse fel az utazás távolságát.

```sql
df1.boxplot(column='trip_distance',return_type='dict')
```

![Mező kimenete][1]

### <a name="visualization-distribution-plot-example"></a>Vizualizáció: példa a terjesztési ábrára
Az eloszlást ábrázoló ábrák és a mintavételezési távolságok hisztogramja.

```sql
fig = plt.figure()
ax1 = fig.add_subplot(1,2,1)
ax2 = fig.add_subplot(1,2,2)
df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
df1['trip_distance'].hist(ax=ax2, bins=100, color='k')
```

![Terjesztési mintaterület kimenete][2]

### <a name="visualization-bar-and-line-plots"></a>Vizualizáció: sáv és vonal ábrázolása
Ebben a példában az utazási távolságot öt rekeszbe helyezzük, és megjelenítjük a dobozolási eredményeket.

```sql
trip_dist_bins = [0, 1, 2, 4, 10, 1000]
df1['trip_distance']
trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
trip_dist_bin_id
```

A fenti raktárhely-eloszlást egy sáv vagy egy sorba rajzoló ábrán ábrázoljuk:

```sql
pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')
```

![Sáv kimenetének ábrázolása][3]

és

```sql
pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')
```

![Nyomtatási sor kimenete][4]

### <a name="visualization-scatterplot-examples"></a>Vizualizáció: scatterplot-példák
Az **\_ időpontot \_ \_ másodpercben** , a **\_ távolságot** pedig a menetidő között láthatjuk, hogy van-e korreláció

```sql
plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])
```

![Scatterplot az idő és a távolság közötti kapcsolat kimenete][6]

Hasonlóképpen ellenőrizhető a **ráta \_ kódja** és az **utazási \_ távolság** közötti kapcsolat.

```sql
plt.scatter(df1['passenger_count'], df1['trip_distance'])
```

![Scatterplot a kód és a távolság közötti kapcsolat kimenete][8]

### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>A mintavételen alapuló adatelemzés a IPython notebookon futó SQL-lekérdezések használatával
Ebben a szakaszban a fent létrehozott új táblázatban megőrzött mintavételes adateloszlásokat vizsgáljuk. Hasonló feltárások is elvégezhetők az eredeti táblák használatával.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Feltárás: a sorok és oszlopok számának jelentése a mintául szolgáló táblában

```sql
nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
print 'Number of rows in sample = %d' % nrows.iloc[0,0]

ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
print 'Number of columns in sample = %d' % ncols.iloc[0,0]
```

#### <a name="exploration-tippednot-tripped-distribution"></a>Feltárás: formabontó/nem megoldott terjesztés

```sql
query = '''
SELECT tipped, count(*) AS tip_freq
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY tipped
    '''

    pd.read_sql(query, conn)
```

#### <a name="exploration-tip-class-distribution"></a>Feltárás: tipp osztály eloszlása

```sql
query = '''
    SELECT tip_class, count(*) AS tip_freq
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY tip_class
'''

tip_class_dist = pd.read_sql(query, conn)
```

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Feltárás: a tipp eloszlásának ábrázolása osztály szerint

```sql
tip_class_dist['tip_freq'].plot(kind='bar')
```

![#26 ábrázolása][26]

#### <a name="exploration-daily-distribution-of-trips"></a>Feltárás: az utak napi eloszlása

```sql
query = '''
    SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY CONVERT(date, dropoff_datetime)
'''

pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distribution-per-medallion"></a>Feltárás: Trip Distribution per emlékérem

```sql
query = '''
    SELECT medallion,count(*) AS c
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY medallion
'''

pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Feltárás: az utazások terjesztése a medál és a hack licenc alapján

```sql
query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
pd.read_sql(query,conn)
```

#### <a name="exploration-trip-time-distribution"></a>Feltárás: utazási idő eloszlása

```sql
query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distance-distribution"></a>Feltárás: utazási távolság eloszlása

```sql
query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
pd.read_sql(query,conn)
```

#### <a name="exploration-payment-type-distribution"></a>Feltárás: fizetési típus eloszlása

```sql
query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
pd.read_sql(query,conn)
```

#### <a name="verify-the-final-form-of-the-featurized-table"></a>A featurized táblázat végső formátumának ellenőrzése

```sql
query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
pd.read_sql(query,conn)
```

## <a name="build-models-in-azure-machine-learning"></a><a name="mlmodel"></a>Modellek készítése Azure Machine Learning
Most már készen áll az építés és a modell üzembe helyezésének modellezésére [Azure Machine Learningban](https://studio.azureml.net). Az adatgyűjtés készen áll arra, hogy a korábban azonosított előrejelzési problémák bármelyikében felhasználható legyen, nevezetesen:

1. **Bináris besorolás**: megjósolhatja, hogy fizetett-e borravalót egy útra.
2. **Többosztályos besorolás**: a kifizetett tipp tartományának előrejelzéséhez a korábban definiált osztályok alapján.
3. **Regressziós feladat**: az utazáshoz fizetett tipp mennyiségének előrejelzése.

A modellezési gyakorlat megkezdéséhez jelentkezzen be a **Azure Machine learning (klasszikus)** munkaterületre. Ha még nem hozott létre Machine learning-munkaterületet, tekintse meg a [Azure Machine learning Studio (klasszikus) munkaterület létrehozása](../classic/create-workspace.md)című témakört.

1. A Azure Machine Learning megkezdéséhez tekintse meg a [Mi az a Azure Machine learning Studio (klasszikus)?](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)
2. Jelentkezzen be [Azure Machine learning Studioba (klasszikus)](https://studio.azureml.net).
3. A Machine Learning Studio (klasszikus) kezdőlapja rengeteg információt, videót, oktatóanyagokat, hivatkozásokat tartalmaz a modulok hivatkozásához és egyéb erőforrásokhoz. További információ a Azure Machine Learningről: [Azure Machine learning dokumentációs központ](https://azure.microsoft.com/documentation/services/machine-learning/).

Egy tipikus betanítási kísérlet a következő lépésekből áll:

1. Hozzon létre egy **+ új** kísérletet.
2. Az Azure Machine Learning Studio (klasszikus) beolvasása.
3. Szükség szerint előre feldolgozhatja, átalakíthatja és kezelheti az adatfeldolgozást.
4. Szükség szerint állítson elő szolgáltatásokat.
5. Az adatokat kioszthatja képzési/ellenőrzési/tesztelési adatkészletekbe (vagy külön adatkészletekkel).
6. Válasszon ki egy vagy több gépi tanulási algoritmust attól függően, hogy milyen tanulási problémát kell megoldania. Például a bináris besorolás, a többosztályos besorolás, a regresszió.
7. Egy vagy több modell betanítása a betanítási adatkészlet használatával.
8. Az érvényesítési adatkészlet kiértékelése a betanított modell (ek) használatával.
9. Értékelje ki a modell (eke) t, hogy kiszámítsa a tanulási probléma releváns mérőszámait.
10. Hangolja be a modell (eke) t, és válassza ki a telepítendő legjobb modellt.

Ebben a gyakorlatban már megvizsgáltuk és megtervezjük az Azure szinapszis Analytics szolgáltatásban tárolt adatmennyiséget, és a minta méretét a Azure Machine Learning Studio (klasszikus) betöltéséhez is eldöntöttük. Az alábbi eljárással hozhat létre egy vagy több előrejelzési modellt:

1. Az adatok beolvasása a Azure Machine Learning Studio (klasszikus) elemre az adatok importálása **és kimenete** szakaszban elérhető [importálási adatok][importálása] modul használatával. További információkért lásd az [adatok][importálása –] adatmodul-hivatkozás lapot.

    ![Azure ML importálási adatkészletek][17]
2. Válassza a **Azure SQL Database** lehetőséget a **Tulajdonságok** panelen lévő **adatforrásként** .
3. Adja meg az adatbázis DNS-nevét az **adatbázis-kiszolgáló neve** mezőben. Formátumban `tcp:<your_virtual_machine_DNS_name>,1433`
4. Adja meg az **adatbázis nevét** a megfelelő mezőben.
5. Adja meg az *SQL-felhasználónevet* a **kiszolgáló felhasználói fiókjának nevében**, és a *jelszót* a **kiszolgáló felhasználói fiókjának jelszavában**.
7. Az **adatbázis-lekérdezés** szövegmezőben illessze be azt a lekérdezést, amely kibontja a szükséges adatbázis-mezőket (beleértve a kiszámított mezőket, például a címkéket), és a legördülő menüben az adatok kinyerését a kívánt minta méretre.

Példa egy bináris besorolási kísérletre, amely közvetlenül az Azure szinapszis Analytics-adatbázisból olvassa be az adatot, az alábbi ábrán látható (ne felejtse el lecserélni a táblanév nyctaxi_trip és nyctaxi_fare a séma neve és a forgatókönyvben használt táblanév alapján). A többosztályos besoroláshoz és a regressziós problémákhoz hasonló kísérletek is létrehozhatók.

![Azure ML-vonat][10]

> [!IMPORTANT]
> Az előző szakaszban ismertetett modellezési adatok kinyerésére és mintavételezésére vonatkozó példákban a **három modellezési gyakorlat összes címkéje szerepel a lekérdezésben**. A modellezési gyakorlatok fontos (kötelező) lépése, hogy **kizárják** a másik két probléma felesleges címkéit, valamint az egyéb **megcélzott szivárgásokat**. Ha például bináris besorolást használ, használja a megjelenő **címkét** , és zárja ki a mezők **Tipp \_ osztályát**, a **Tipp \_ összegét** és a **teljes \_ mennyiséget**. Az utóbbi a célzott szivárgásokat célozza meg, mivel a borravalót befizették.
>
> A szükségtelen oszlopok vagy a célzott szivárgások kizárásához használja az [Oszlopok kiválasztása az adatkészlet][select-columns] modulban vagy a [metaadatok szerkesztése][edit-metadata]lehetőséget. További információ: [Oszlopok kiválasztása az adatkészletben][select-columns] és a [metaadatokat][edit-metadata] tartalmazó hivatkozások szerkesztése.
>
>

## <a name="deploy-models-in-azure-machine-learning"></a><a name="mldeploy"></a>Modellek üzembe helyezése Azure Machine Learning
Ha a modell elkészült, egyszerűen üzembe helyezheti webszolgáltatásként közvetlenül a kísérletből. Az Azure ML-webszolgáltatások üzembe helyezésével kapcsolatos további információkért lásd: [Azure Machine learning webszolgáltatás üzembe](../classic/deploy-a-machine-learning-web-service.md)helyezése.

Új webszolgáltatás üzembe helyezéséhez a következőket kell tennie:

1. Pontozási kísérlet létrehozása.
2. Telepítse a webszolgáltatást.

A **befejezett** betanítási kísérletből származó pontozási kísérlet létrehozásához kattintson a **pontozási kísérlet létrehozása** lehetőségre az alsó műveleti sávon.

![Azure-pontozási][18]

Azure Machine Learning megpróbál létrehozni egy pontozási kísérletet a betanítási kísérlet összetevői alapján. Különösen a következőket kell tennie:

1. Mentse a betanított modellt, és távolítsa el a modell betanítási modulját.
2. Azonosítson egy logikai **bemeneti portot** , amely a várt bemeneti adatsémát jelképezi.
3. Azonosítson egy logikai **kimeneti portot** a várt webszolgáltatás kimeneti sémájának megjelenítéséhez.

A pontozási kísérlet létrehozásakor tekintse át az eredményeket, és szükség szerint végezze el a módosítást. Egy tipikus beállítás, hogy lecseréli a bemeneti adatkészletet vagy lekérdezést egy olyanra, amely kizárja a címke mezőket, mert ezek a címke mezők nem lesznek leképezve a sémához a szolgáltatás meghívásakor. Ajánlott továbbá a bemeneti adatkészlet és/vagy a lekérdezés méretének csökkentése néhány rekordra, amely elegendő a bemeneti séma jelzéséhez. A kimeneti port esetében gyakori, hogy kizárják az összes bemeneti mezőt, és csak a kimenetben szereplő **mutatókat** és a kimutatott **valószínűségeket** tartalmazzák a [DataSet oszlopok kiválasztása az adatkészlet][select-columns] modulban.

Az alábbi ábrán egy minta pontozási kísérlet szerepel. Ha készen áll a telepítésre, kattintson a **közzétételi webszolgáltatás** gombra az alsó műveleti sávon.

![Azure ML-közzététel][11]

## <a name="summary"></a>Összefoglalás
Ahhoz, hogy beolvassa az ebben az útmutatóban ismertetett oktatóanyagot, létrehozott egy Azure-beli adatelemzési környezetet, amely egy nagyméretű nyilvános adatkészlettel dolgozott, a csoportos adatelemzési folyamaton keresztül, egészen az adatgyűjtésig a modell betanítása, majd egy Azure Machine Learning webszolgáltatás üzembe helyezése során.

### <a name="license-information"></a>Licencelési információk
Ez a minta-útmutató és a hozzá tartozó parancsfájlok és IPython-jegyzetfüzet (ek) a Microsoft által a MIT licenc alatt vannak megosztva. További részletekért olvassa el a GitHubon található mintakód könyvtárában lévő LICENSE.txt fájlt.

## <a name="references"></a>Hivatkozások
- [Andrés Monroy NYC taxi TRIPS letöltési oldal](https://www.andresmh.com/nyctaxitrips/)
- [A New York-i taxis utazási adatvédelme Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)
- [A New York-i taxi és a limuzin Bizottság kutatási és statisztikai adatai](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[1]: ./media/sqldw-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sqldw-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sqldw-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sqldw-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sqldw-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sqldw-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sqldw-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sqldw-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sqldw-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sqldw-walkthrough/azuremltrain.png
[11]: ./media/sqldw-walkthrough/azuremlpublish.png
[12]: ./media/sqldw-walkthrough/ssmsconnect.png
[13]: ./media/sqldw-walkthrough/executescript.png
[14]: ./media/sqldw-walkthrough/sqlserverproperties.png
[15]: ./media/sqldw-walkthrough/sqldefaultdirs.png
[16]: ./media/sqldw-walkthrough/bulkimport.png
[17]: ./media/sqldw-walkthrough/amlreader.png
[18]: ./media/sqldw-walkthrough/amlscoring.png
[19]: ./media/sqldw-walkthrough/ps_download_scripts.png
[20]: ./media/sqldw-walkthrough/ps_load_data.png
[21]: ./media/sqldw-walkthrough/azcopy-overwrite.png
[22]: ./media/sqldw-walkthrough/ipnb-service-aml-1.png
[23]: ./media/sqldw-walkthrough/ipnb-service-aml-2.png
[24]: ./media/sqldw-walkthrough/ipnb-service-aml-3.png
[25]: ./media/sqldw-walkthrough/ipnb-service-aml-4.png
[26]: ./media/sqldw-walkthrough/tip_class_hist_1.png


<!-- Module References -->
[edit-metadata]: /azure/machine-learning/studio-module-reference/edit-metadata
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[adatimportálás]: /azure/machine-learning/studio-module-reference/import-data
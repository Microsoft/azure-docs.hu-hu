---
title: SQL Server virtuális gép adatelemzési folyamatának megismerése
description: Az Azure-beli SQL Server virtuális gépeken megismerheti az adatfeldolgozást és a szolgáltatások létrehozását a Python vagy az SQL használatával.
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
ms.openlocfilehash: 8be878cf40967356d68e9be0765e898c81b5ba0a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "93314626"
---
# <a name="process-data-in-sql-server-virtual-machine-on-azure"></a><a name="heading"></a>Adatfeldolgozás az Azure-beli SQL Server virtuális gépen
Ez a dokumentum ismerteti, hogyan lehet az Azure-ban SQL Server VM tárolt adatkezelési funkciókat és szolgáltatásokat előállítani. Ezt a célt az SQL használatával vagy a Pythonhoz hasonló programozási nyelv használatával lehet elvégezni az huzavona.

> [!NOTE]
> A dokumentumban szereplő minta SQL-utasítások azt feltételezik, hogy az adatokat SQL Server. Ha nem, tekintse meg a felhőalapú adatelemzési folyamat térképét, amelyből megtudhatja, hogyan helyezheti át az adatait SQL Serverba.
> 
> 

## <a name="using-sql"></a><a name="SQL"></a>SQL használata
Ebben a szakaszban a következő, az SQL-t használó huzavona-feladatokat ismertetjük:

1. [Adatelemzés](#sql-dataexploration)
2. [Szolgáltatás létrehozása](#sql-featuregen)

### <a name="data-exploration"></a><a name="sql-dataexploration"></a>Adatelemzés
Íme néhány példa olyan SQL-parancsfájlra, amely a SQL Serverban található adattárak feltárására használható.

> [!NOTE]
> Gyakorlati példaként használhatja a New York-i [taxi-adatkészletet](https://www.andresmh.com/nyctaxitrips/) , és a [IPython Notebook és a SQL Server segítségével](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) megtekintheti a IPNB című témakört a New York-i huzavona.
> 
> 

1. A napi megfigyelések számának lekérése
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Szintek beolvasása egy kategorikus oszlopban
   
    `select  distinct <column_name> from <databasename>`
3. Szintek számának lekérése két kategorikus oszlop kombinációjában 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Numerikus oszlopok eloszlásának beolvasása
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

### <a name="feature-generation"></a><a name="sql-featuregen"></a>Szolgáltatás létrehozása
Ebben a szakaszban az SQL-t használó szolgáltatások létrehozásának módjait ismertetjük:  

1. [Darabszám-alapú szolgáltatás létrehozása](#sql-countfeature)
2. [Dobozolási szolgáltatás létrehozása](#sql-binningfeature)
3. [A funkciók kimutatása egyetlen oszlopból](#sql-featurerollout)

> [!NOTE]
> Miután létrehozta a további szolgáltatásokat, hozzáadhatja őket oszlopként a meglévő táblához, vagy létrehozhat egy új táblát a további funkciókkal és elsődleges kulccsal, amelyek az eredeti táblával csatlakoztathatók. 
> 
> 

### <a name="count-based-feature-generation"></a><a name="sql-countfeature"></a>Darabszám-alapú szolgáltatás létrehozása
Az alábbi példák a Count-funkciók létrehozásának két módját szemléltetik. Az első metódus feltételes összeget használ, a második metódus pedig a "WHERE" záradékot használja. Ezek az eredmények ezután az eredeti táblával (az elsődleges kulcs oszlopainak használatával) is összekapcsolhatók, hogy az eredeti adatmennyiség mellett is meglegyenek a Count funkciók.

```sql
select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 
```

### <a name="binning-feature-generation"></a><a name="sql-binningfeature"></a>Dobozolási szolgáltatás létrehozása
Az alábbi példa azt szemlélteti, hogyan hozhatók dobozolni-funkciók a dobozolási (öt raktárhely használatával) egy numerikus oszlop, amely szolgáltatásként használható.

```sql
SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>
```

### <a name="rolling-out-the-features-from-a-single-column"></a><a name="sql-featurerollout"></a>A funkciók kimutatása egyetlen oszlopból
Ebben a szakaszban bemutatjuk, hogyan hozhatja ki egy tábla egyetlen oszlopát további funkciók létrehozásához. A példa azt feltételezi, hogy a táblázatban a szélesség vagy hosszúság oszlop szerepel, amelyből a szolgáltatásokat kívánja előállítani.

Íme egy rövid ismertető a szélességi és hosszúsági adatokról (a StackOverflow származik, [hogyan mérhető a szélességi és a hosszúsági pontosság?](https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude)). Ez az útmutató akkor hasznos, ha a helyet egy vagy több szolgáltatásként is meg szeretné érteni:

* A jel azt jelzi, hogy Észak-vagy Dél-, Kelet-vagy nyugati régiója a világon van-e.
* A nullától eltérő száz számjegy azt jelzi, hogy a földrajzi hosszúság nem!
* A tízes számjegy körülbelül 1 000 kilométert biztosít. Hasznos információkkal szolgál arról, hogy mi a kontinens vagy az óceán.
* Az egység számjegye (egy tizedes tört fok) akár 111 kilométer (60 tengeri mérföld, körülbelül 69 Miles) pozíciót biztosít. Megtudhatja, hogy nagyjából milyen államot, országot vagy régiót használ.
* Az első tizedes tört értéke 11,1 km lehet: egy nagy város pozícióját is megkülönböztetni egy szomszédos nagy városból.
* A második tizedes tört értéke 1,1 km lehet: a következőtől elkülöníthető egy falu.
* A harmadik tizedes tört akár 110 m is lehet: a nagy mezőgazdasági mező vagy intézményi kampusz azonosítására alkalmas.
* A negyedik tizedes tört érték legfeljebb 11 m lehet: a parcella azonosítható. A megoldás a nem megfelelő, interferencia nélküli GPS-egységek jellemző pontosságával összehasonlítható.
* Az ötödik tizedes tört érték 1,1 m-re van beállítva: megkülönbözteti a fákat egymástól. Az ehhez a szinthez tartozó pontosságot a kereskedelmi GPS-egységekkel csak különbözeti korrekcióval lehet megvalósítani.
* A hatodik tizedes tört érték akár 0,11 m is lehet: a szerkezetek részletes kialakításához, a Tájképek tervezéséhez, az utak létrehozásához használhatja ezt a lehetőséget. A gleccserek és a folyók forgalmának nyomon követéséhez elég jó legyen. Ezt úgy érheti el, ha a GPS-vel kapcsolatos aprólékos mértékeket, például differentially korrigált GPS-t használ.

A tartózkodási hely adatai a következőképpen featurized, elkülönítve a régió, a hely és a város információit. Meghívhat egy REST-végpontot is, például a Bing Maps API-t, [ahol megkeresheti](/bingmaps/rest-services/locations/find-a-location-by-point) a régió/körzet információit.

```sql
select 
    <location_columnname>
    ,round(<location_columnname>,0) as l1        
    ,l2=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 1 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),1,1) else '0' end     
    ,l3=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 2 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),2,1) else '0' end     
    ,l4=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 3 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),3,1) else '0' end     
    ,l5=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 4 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),4,1) else '0' end     
    ,l6=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 5 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),5,1) else '0' end     
    ,l7=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 6 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),6,1) else '0' end     
from <tablename>
```

Ezek a hely-alapú funkciók további, a korábban leírtaknak megfelelően további Count-funkciók létrehozásához használhatók. 

> [!TIP]
> Programozott módon beszúrhatja a rekordokat a választott nyelv használatával. Előfordulhat, hogy az írás hatékonyságának javításához be kell szúrnia az adattömbökben lévő adattömböket (például a pyodbc használatával, lásd: [HelloWorld-minta a Pythonhoz való SQLServer eléréséhez](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)). Egy másik alternatíva az, hogy a [BCP segédprogram](/sql/tools/bcp-utility)használatával szúrja be az adatbázist az adatbázisba.
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Csatlakozás a Azure Machine Learninghoz
Az újonnan létrehozott funkció hozzáadhatók oszlopként egy meglévő táblához, vagy egy új táblában tárolhatók, és a Machine learning eredeti táblájához csatlakoznak. A szolgáltatások létrehozhatók vagy elérhetők, ha már létrejöttek, az [adatimportálási][import-data] modul használatával Azure Machine learning az alábbi ábrán látható módon:

![azureml-olvasók][1] 

## <a name="using-a-programming-language-like-python"></a><a name="python"></a>Programozási nyelv (például Python) használata
A Python használata az adatelemzéshez és a szolgáltatások létrehozásához, amikor az adatai SQL Server hasonlóak az Azure blobban tárolt adatfeldolgozáshoz az [adatelemzési környezet Azure Blob-adatainak](data-blob.md)feldolgozása során dokumentált módon. Az adatok betöltése az adatbázisból egy Panda-adatkeretbe további feldolgozás céljából. Dokumentáljuk az adatbázishoz való csatlakozás folyamatát és az adatkeretbe való betöltését ebben a szakaszban.

A következő kapcsolati karakterlánc-formátum használható egy SQL Server-adatbázishoz való kapcsolódáshoz a Pythonból a pyodbc használatával (a kiszolgálónév, a dbname, a Felhasználónév és a jelszó lecserélése a megadott értékekkel):

```python
#Set up the SQL Azure connection
import pyodbc    
conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')
```

A Pythonban található [Panda Library](https://pandas.pydata.org/) számos adatstruktúrát és adatelemzési eszközt biztosít a Python programozási funkciók adatkezeléséhez. Az alábbi kód beolvassa az SQL Server-adatbázisból a Panda adatkeretbe visszaadott eredményeket:

```python
# Query database and load the returned results in pandas data frame
data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)
```

Most már dolgozhat a pandák adatkerettel, ahogyan az az Azure Blob-adatfeldolgozást az [adatelemzési környezetben](data-blob.md)is tárgyalja.

## <a name="azure-data-science-in-action-example"></a>Azure-adatelemzés működés közben – példa
Az Azure adatelemzési folyamat egy nyilvános adatkészletet használó teljes körű bemutató példáját az [Azure adatelemzési folyamata](sql-walkthrough.md)című témakörben tekintheti meg.

[1]: ./media/sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: /azure/machine-learning/studio-module-reference/import-data
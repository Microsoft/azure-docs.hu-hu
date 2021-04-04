---
title: Szolgáltatások létrehozása SQL Server az SQL és a Python – csoportos adatelemzési folyamat használatával
description: Szolgáltatások létrehozása az Azure-beli SQL Server VM tárolt adataihoz az SQL és a Python használatával – a csoportos adatelemzési folyamat részeként.
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
ms.openlocfilehash: 3c20bf1c5856276c4c7ee0e37ed4ef2120d1d93d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "93322035"
---
# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>Funkciók létrehozása az adatokhoz az SQL Serveren SQL és Python használatával
Ebből a dokumentumból megtudhatja, hogyan hozhatja ki az Azure-beli SQL Server VM tárolt adatok szolgáltatásait, amelyek segítségével az algoritmusok hatékonyabban megismerhetik az adatokból. Ezt a feladatot SQL vagy programozási nyelv (például Python) használatával hajthatja végre. Mindkét módszert itt mutatjuk be.

Ez a feladat a [csoportos adatelemzési folyamat (TDSP)](./index.yml)egyik lépése.

> [!NOTE]
> Gyakorlati példákért tekintse meg a [New York-i taxi-adatkészletet](https://www.andresmh.com/nyctaxitrips/) , és tekintse meg a New York-i HUZAVONA nevű IPNB a [IPython notebook használatával SQL Server és](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) a végpontok közötti átjáráshoz.
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy rendelkezik a következővel:

* Létrehozott egy Azure Storage-fiókot. Ha útmutatásra van szüksége, tekintse meg [Az Azure Storage-fiók létrehozása](../../storage/common/storage-account-create.md) című témakört.
* Az adatait SQL Server tárolja. Ha nem rendelkezik a szolgáltatással, olvassa el az [adatáthelyezés egy Azure Machine Learning Azure SQL Databasere](move-sql-azure.md) című témakört, amely útmutatást nyújt az adatáthelyezéshez.

## <a name="feature-generation-with-sql"></a><a name="sql-featuregen"></a>Funkciók létrehozása SQL-sel
Ebben a szakaszban az SQL-t használó szolgáltatások létrehozásának módjait ismertetjük:  

* [Darabszám-alapú szolgáltatás létrehozása](#sql-countfeature)
* [Dobozolási szolgáltatás létrehozása](#sql-binningfeature)
* [A funkciók kimutatása egyetlen oszlopból](#sql-featurerollout)

> [!NOTE]
> Miután létrehozta a további szolgáltatásokat, hozzáadhatja őket oszlopként a meglévő táblához, vagy létrehozhat egy új táblát a további funkciókkal és elsődleges kulccsal, amelyek az eredeti táblával csatlakoztathatók.
> 
> 

### <a name="count-based-feature-generation"></a><a name="sql-countfeature"></a>Darabszám-alapú szolgáltatás létrehozása
Ez a dokumentum két módszert mutat be a Count-funkciók létrehozásához. Az első metódus feltételes összeget használ, a második metódus pedig a "WHERE" záradékot használja. Ezek az új funkciók ezután csatlakoztathatók az eredeti táblához (az elsődleges kulcs oszlopainak használatával), hogy az eredeti adatértékekkel együtt is megszámolják a szolgáltatásokat.

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

Íme egy rövid ismertető a szélességi és a hosszúsági hely adatainak áttekintéséről (a StackOverflow-ből származnak `https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude` ). Íme néhány hasznos tudnivaló arról, hogy a szolgáltatások a mezőből való létrehozása előtt hogyan kell megismerni a helyadatok adatait:

* A jel azt jelzi, hogy a világ északi vagy déli régiója, illetve Kelet-vagy nyugati régiója.
* A nullától eltérő száz számjegy azt jelzi, hogy a földrajzi szélesség nem lesz használatban.
* A tízes számjegy körülbelül 1 000 kilométert biztosít. Hasznos információkkal szolgál arról, hogy mi a kontinens vagy az óceán.
* Az egység számjegye (egy tizedes tört fok) akár 111 kilométer (60 tengeri mérföld, körülbelül 69 Miles) pozíciót biztosít. Ez nagyjából azt jelzi, hogy milyen nagy az állam vagy ország/régió.
* Az első tizedes tört értéke 11,1 km lehet: egy nagy város pozícióját is megkülönböztetni egy szomszédos nagy városból.
* A második tizedes tört értéke 1,1 km lehet: a következőtől elkülöníthető egy falu.
* A harmadik tizedes tört akár 110 m is lehet: a nagy mezőgazdasági mező vagy intézményi kampusz azonosítására alkalmas.
* A negyedik tizedes tört érték legfeljebb 11 m lehet: a parcella azonosítható. A megoldás a nem megfelelő, interferencia nélküli GPS-egységek jellemző pontosságával összehasonlítható.
* Az ötödik tizedes tört érték 1,1 m-re van beállítva: megkülönbözteti a fákat egymástól. Az ehhez a szinthez tartozó pontosságot a kereskedelmi GPS-egységekkel csak különbözeti korrekcióval lehet megvalósítani.
* A hatodik tizedes tört érték 0,11 m-re van korlátozva: ezt a szintet részletesen, a Tájképek kialakításához, az utak létrehozásához használhatja. A gleccserek és a folyók forgalmának nyomon követéséhez elég jó legyen. Ezt a célt úgy érheti el, ha a GPS-szel, például a differentially korrigált GPS-sel kapcsolatos fáradságos mértékeket használ.

A tartózkodási hely adatai a régió, a hely és a város információinak elválasztásával is featurized. Ha egyszer is meghívhat egy REST-végpontot, például a Bing Maps API-t (lásd: `https://msdn.microsoft.com/library/ff701710.aspx` a régió/kerület információinak beszerzése).

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
> Programozott módon beszúrhatja a rekordokat a választott nyelv használatával. Előfordulhat, hogy az írási hatékonyság javítása érdekében be kell szúrnia az adattömbökbe az adathalmazokat. [Íme egy példa arra, hogyan teheti ezt meg a pyodbc használatával](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
> Egy másik alternatíva az, hogy az adatbázisban a [BCP segédprogram](/sql/tools/bcp-utility) használatával szúr be adatbevitelt
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Csatlakozás a Azure Machine Learninghoz
Az újonnan létrehozott funkció hozzáadhatók oszlopként egy meglévő táblához, vagy egy új táblában tárolhatók, és a Machine learning eredeti táblájához csatlakoznak. A szolgáltatások létrehozhatók vagy elérhetők, ha már létrejöttek, az [adatimportálási](/azure/machine-learning/studio-module-reference/import-data) modul használata az Azure ml-ben az alábbi ábrán látható módon:

![Azure ML-olvasók](./media/sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="using-a-programming-language-like-python"></a><a name="python"></a>Programozási nyelv (például Python) használata
A Python használatával olyan szolgáltatásokat hozhatnak elő, amelyekben az adatai SQL Server hasonlóak az Azure blobban lévő adatfeldolgozáshoz a Python használatával. Összehasonlításért lásd: az [Azure Blob-adatfeldolgozás adatelemzési környezetben](data-blob.md). Az adatok betöltése az adatbázisból egy Panda-adatkeretbe a további feldolgozás érdekében. Ebben a szakaszban az adatbázishoz való csatlakozás és az adatkeretbe való betöltés folyamata is dokumentálva van.

A következő kapcsolati karakterlánc-formátum használható egy SQL Server-adatbázishoz való kapcsolódáshoz a Pythonból a pyodbc használatával (a kiszolgálónév, a dbname, a Felhasználónév és a jelszó lecserélése a megadott értékekkel):

```python
#Set up the SQL Azure connection
import pyodbc
conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')
```

A Pythonban található [Panda Library](https://pandas.pydata.org/) számos adatstruktúrát és adatelemzési eszközt biztosít a Python programozási funkciók adatkezeléséhez. A következő kód beolvassa a SQL Server-adatbázisból a Panda adatkeretbe visszaadott eredményeket:

```python
# Query database and load the returned results in pandas data frame
data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)
```

Most már dolgozhat a Panda adatkerettel, ahogy az az [Azure Blob Storage-hoz készült szolgáltatások létrehozása a Panda használatával](./explore-data-blob.md)című témakörben leírtak szerint.
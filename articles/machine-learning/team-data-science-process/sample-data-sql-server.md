---
title: Mintaadatok SQL Server az Azure-ban – csoportos adatelemzési folyamat
description: Az Azure-ban SQL Server tárolt adatokat az SQL vagy a Python programozási nyelv használatával, majd a Azure Machine Learningba helyezheti át.
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
ms.openlocfilehash: 7ac1fc5688dad3406041f36ff858e6fd27c7272f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "93321872"
---
# <a name="sample-data-in-sql-server-on-azure"></a><a name="heading"></a>Adatmintavétel Azure-on futó SQL Serverben

Ez a cikk bemutatja, hogyan lehet az Azure-ban tárolt SQL Server adatokat az SQL vagy a Python programozási nyelv használatával megtekinteni. Emellett azt is bemutatja, hogyan helyezhetők át a mintavételen áthelyezett információk a Azure Machine Learningba úgy, hogy fájlba mentik, feltöltik azt egy Azure-blobba, majd beolvasják őket Azure Machine Learning Studioba.

A Python-mintavételezés a [pyodbc](https://code.google.com/p/pyodbc/) ODBC-függvénytárat használja az Azure-beli SQL Serverhoz való kapcsolódáshoz és a [Panda](https://pandas.pydata.org/) Library-hez a mintavételezés végrehajtásához.

> [!NOTE]
> Az ebben a dokumentumban szereplő SQL-kód azt feltételezi, hogy az adatSQL Server az Azure-ban. Ha nem, olvassa el az [adatáthelyezés SQL Server az Azure](move-sql-server-virtual-machine.md) -ban című cikket, amelyből megtudhatja, hogyan helyezheti át az adatait az Azure-beli SQL Serverba.
> 
> 

**Miért érdemes felvenni az adatait?**
Ha az elemezni kívánt adatkészlet nagy méretű, általában egy jó ötlet, hogy lerövidítse az adatokat, hogy csökkentse azt kisebb, de reprezentatív és felügyelhető méretre. A mintavétel megkönnyíti az adatmegismerést, a feltárást és a funkciók mérnöki felépítését. Szerepe a [csoportos adatelemzési folyamatban (TDSP)](./index.yml) az adatfeldolgozási függvények és a gépi tanulási modellek gyors prototípusának engedélyezése.

Ez a mintavételi feladat a [csoportos adatelemzési folyamat (TDSP)](./index.yml)egyik lépése.

## <a name="using-sql"></a><a name="SQL"></a>SQL használata
Ez a szakasz számos olyan módszert ismertet, amely az SQL használatával egyszerű véletlenszerű mintavételt végez az adatbázisban lévő adatokkal. Válasszon egy módszert az adatméret és a terjesztés alapján.

A következő két elem azt mutatja be, hogyan használható a `newid` SQL Server a mintavételezés végrehajtásához. A választott módszer attól függ, hogy a mintát milyen véletlenszerű módon szeretné használni (pk_id a következő mintakód egy automatikusan létrehozott elsődleges kulcsnak számít).

1. Kevésbé szigorú véletlenszerű minta

    ```sql
    select  * from <table_name> where <primary_key> in 
    (select top 10 percent <primary_key> from <table_name> order by newid())
    ```

2. Több véletlenszerű minta 

    ```sql
    SELECT * FROM <table_name>
    WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)
    ```

A Tablesample az adatmintavételezéshez is használható. Ez a lehetőség jobb megközelítés lehet, ha az adatméret nagy (feltéve, hogy a különböző lapokon lévő adatmennyiség nem összefügg egymással), és a lekérdezésnek ésszerű időn belül el kell végeznie.

```sql
SELECT *
FROM <table_name> 
TABLESAMPLE (10 PERCENT)
```

> [!NOTE]
> A mintául szolgáló adatok megismeréséhez és létrehozásához egy új táblában tárolja a szolgáltatást.
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Csatlakozás a Azure Machine Learninghoz
Közvetlenül használhatja a fenti lekérdezéseket a Azure Machine Learning [adatimportálási][import-data] modulból lefelé – mintát vehet a menet közben, és egy Azure Machine learning kísérletbe helyezheti. Itt látható egy képernyőkép az olvasó modul használatáról a mintául szolgáló információk olvasásához:

![olvasó SQL][1]

## <a name="using-the-python-programming-language"></a><a name="python"></a>A Python programozási nyelv használata
Ez a szakasz bemutatja, hogyan hozhat létre ODBC-kapcsolatot egy Pythonban található SQL Server-adatbázishoz a [pyodbc-függvénytár](https://code.google.com/p/pyodbc/) használatával. Az adatbázis-kapcsolatok karakterlánca a következő: (a kiszolgálónév, a dbname, a Felhasználónév és a jelszó cseréje a konfigurációval):

```python
#Set up the SQL Azure connection
import pyodbc    
conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')
```

A Pythonban található [Panda](https://pandas.pydata.org/) Library számos adatstruktúrát és adatelemzési eszközt biztosít a Python programozási funkciók adatkezeléséhez. A következő kód a Azure SQL Database egy táblázatból származó adatok 0,1%-os mintáját egy Panda-adatokba olvassa:

```python
import pandas as pd

# Query database and load the returned results in pandas data frame
data_frame = pd.read_sql('''select column1, column2... from <table_name> tablesample (0.1 percent)''', conn)
```

Mostantól használhatja a mintául szolgáló, a pandák adatkeretben tárolt adatmennyiséget. 

### <a name="connecting-to-azure-machine-learning"></a><a name="python-aml"></a>Csatlakozás a Azure Machine Learninghoz
Az alábbi mintakód használatával mentheti a lefelé mintavételezés alatt álló adatok egy fájlba, és feltöltheti azt egy Azure-blobba. A blobban található adatai közvetlenül beolvashatók egy Azure Machine Learning kísérletbe az [adatimportálási][import-data] modul használatával. Ennek lépései a következők: 

1. A Panda adatkeretének írása helyi fájlba

    ```python
    dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
    ```

2. Helyi fájl feltöltése az Azure-blobba

    ```python
    from azure.storage import BlobService
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    LOCALFILENAME= <local_file_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
    localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory

    try:

    #perform upload
    output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)

    except:            
        print ("Something went wrong with uploading blob:"+BLOBNAME)
    ```

3. Adatok beolvasása az Azure blobból Azure Machine Learning [importálási][import-data] modul használatával, ahogy az a következő képernyőfelvételen látható:

![olvasó blob][2]

## <a name="the-team-data-science-process-in-action-example"></a>A csoportos adatelemzési folyamat működés közben példa
Ha egy nyilvános adatkészletet használó csoportos adatelemzési folyamat példáját szeretné eljárni, tekintse [meg a csoportos adatelemzési folyamat](sql-walkthrough.md)című témakört, amely a SQL Server használatát mutatja be.

[1]: ./media/sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/sample-sql-server-virtual-machine/reader_blob.png

[import-data]: /azure/machine-learning/studio-module-reference/import-data
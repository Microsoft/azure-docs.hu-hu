---
title: SQL Server virtuális gép adatelemzési folyamatának megismerése
description: Az Azure-SQL Server VM tárolt, SQL-et vagy programozási nyelvet (például a Pythont) tároló adatokat vizsgálja meg.
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
ms.openlocfilehash: 33b55afb7796b197f7130ec9288abb01cc115651
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86085650"
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Az SQL Server virtuális gépen tárolt adatok megismerése az Azure rendszerben

Ez a cikk az Azure-ban SQL Server VM tárolt adatelemzések megismerését ismerteti. Az SQL vagy a Python használatával vizsgálja meg az adatelemzést.

Ez a feladat a [csoportos adatelemzési folyamat](overview.md)egyik lépése.

> [!NOTE]
> A dokumentumban szereplő minta SQL-utasítások azt feltételezik, hogy az adatokat SQL Server. Ha nem, tekintse meg a felhőalapú adatelemzési folyamat térképét, amelyből megtudhatja, hogyan helyezheti át az adatait SQL Serverba.
> 
> 

## <a name="explore-sql-data-with-sql-scripts"></a><a name="sql-dataexploration"></a>SQL-adatelemzés az SQL-szkriptekkel
Íme néhány példa olyan SQL-parancsfájlra, amely a SQL Serverban található adattárak feltárására használható.

1. A napi megfigyelések számának lekérése
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Szintek beolvasása egy kategorikus oszlopban
   
    `select  distinct <column_name> from <databasename>`
3. Szintek számának lekérése két kategorikus oszlop kombinációjában 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Numerikus oszlopok eloszlásának beolvasása
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [!NOTE]
> Gyakorlati példaként használhatja a New York-i [taxi-adatkészletet](https://www.andresmh.com/nyctaxitrips/) , és a [IPython Notebook és a SQL Server segítségével](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) megtekintheti a IPNB című témakört a New York-i huzavona.
> 
> 

## <a name="explore-sql-data-with-python"></a><a name="python"></a>SQL-adatelemzés a Pythonban
A Python segítségével megismerheti az adatelemzéseket, és olyan szolgáltatásokat hozhatnak elő, amelyekben az adatai SQL Server hasonlóak az Azure-blobban lévő adatfeldolgozáshoz a Python használatával, az [Azure Blob adatainak az adatelemzési környezetben](data-blob.md)való feldolgozása során dokumentált módon. Az adatbázisból egy Panda DataFrame tölti be az adatok adatait, majd később is feldolgozható. Dokumentáljuk az adatbázishoz való csatlakozás folyamatát, és betöltjük az adatbevitelt a szakasz DataFrame.

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

Most már dolgozhat a pandák DataFrame, ahogyan az az Azure Blob-adatfeldolgozási folyamat során az [adatelemzési környezetben](data-blob.md)is szerepel.

## <a name="the-team-data-science-process-in-action-example"></a>A csoportos adatelemzési folyamat működés közben példa
A Cortana Analytics-folyamat egy nyilvános adatkészlettel való teljes körű bemutató példáját a [csoportos adatelemzési folyamat működés közben: a SQL Server használata](sql-walkthrough.md)című témakörben tekintheti meg.


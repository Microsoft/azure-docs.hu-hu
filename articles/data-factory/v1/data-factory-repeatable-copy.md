---
title: Ismételhető másolás Azure Data Factory
description: Megtudhatja, hogyan kerülheti el a duplikált elemeket annak ellenére, hogy az adatmásoló szelet többször is fut.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c427cd90412121e896738ca43f4c66dd24b096dc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387087"
---
# <a name="repeatable-copy-in-azure-data-factory"></a>Ismételhető másolás Azure Data Factory

## <a name="repeatable-read-from-relational-sources"></a>Megismételhető olvasás a rokon forrásokból
Az adatok a kapcsolódó adattárakból való másolása során érdemes megismételni a nem kívánt eredmények elkerülését. Azure Data Factory a szeleteket manuálisan is újra futtathatja. Az újrapróbálkozási szabályzatot is konfigurálhatja egy adatkészlethez, hogy a rendszer hiba esetén újrafuttassa a szeleteket. Ha egy szeletet mindkét módon újrafuttat, meg kell győződnie arról, hogy a szeletek hányszor futnak.  
 
> [!NOTE]
> A következő minták az Azure SQL-re vonatkoznak, de minden olyan adattárra alkalmazhatók, amely támogatja a téglalap alakú adatkészleteket. Előfordulhat, hogy módosítania kell a forrás **típusát** és a **lekérdezési** tulajdonságot (például sqlReaderQuery helyett lekérdezés) az adattárhoz.   

A kapcsolódó áruházakból való olvasáskor általában csak az adott szeletnek megfelelő adatok olvashatók be. Ez a módszer a Azure Data Factoryben elérhető WindowStart és WindowEnd rendszerváltozók használatával történik. A [Azure Data Factory-functions és a System változók](data-factory-functions-variables.md) című cikkben olvashat a Azure Data Factory változóit és funkcióit. Példa: 

```json
"source": {
    "type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
Ez a lekérdezés a szeletek időtartamának tartományában (WindowStart-> WindowEnd) lévő adatokat olvassa be a tábla Sajáttábla. A szelet újbóli futtatása azt is biztosítja, hogy ugyanazok az információk legyenek beolvasva. 

Más esetekben érdemes lehet beolvasni a teljes táblázatot, és a következőképpen határozhatja meg a sqlReaderQuery:

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```

## <a name="repeatable-write-to-sqlsink"></a>Ismételhető írás a SqlSink
Az adatok **Azure SQL/SQL Serverba** más adattárakból való másolása során meg kell őrizni az ismételhetőséget a nem kívánt eredmények elkerülése érdekében. 

Az adatok Azure SQL/SQL Server adatbázisba való másolása során a másolási tevékenység alapértelmezés szerint hozzáfűzi az adatokhoz a fogadó táblához. Tegyük fel, hogy adatokat másol egy CSV (vesszővel tagolt) fájlból, amely két rekordot tartalmaz a következő táblázatba egy Azure SQL/SQL Server adatbázisban. Egy szelet futtatásakor a két rekord az SQL-táblába lesz másolva. 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Tegyük fel, hogy hibákat észlelt a forrásfájlban, és frissítette a legördülő cső mennyiségét 2 és 4 között. Ha az adott időszakhoz tartozó adatszeletet manuálisan Újrafuttatja, két új rekordot fog hozzáfűzni az Azure SQL/SQL Server-adatbázishoz. Ez a példa azt feltételezi, hogy a tábla egyik oszlopa sem rendelkezik az elsődleges kulcs korlátozásával.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

A viselkedés elkerülése érdekében a következő két mechanizmus egyikének használatával kell megadnia a UPSERT szemantika használatát:

### <a name="mechanism-1-using-sqlwritercleanupscript"></a>1. mechanizmus: a sqlWriterCleanupScript használata
A **sqlWriterCleanupScript** tulajdonság használatával törölheti a fogadó tábla adatait, mielőtt beszúrja az adatait egy szelet futtatásakor. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Egy szelet futtatásakor a rendszer először a karbantartási parancsfájlt futtatja az SQL-táblából származó szeletnek megfelelő adatok törléséhez. A másolási tevékenység ezután beszúrja az adatbevitelt az SQL-táblába. Ha a szelet újra van futtatva, a mennyiség a kívánt módon frissül.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Tegyük fel, hogy az Flat mosó-rekord el lesz távolítva az eredeti CSV-ből. Ezután a szelet ismételt futtatása a következő eredményt fogja eredményezni: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```

A másolási tevékenység lefuttatta a törlési parancsfájlt, hogy törölje az adott szelethez tartozó megfelelő adatmennyiséget. Ezután olvassa be a CSV-bemenetet (amely csak egy rekordot foglalt bele), és beszúrta azt a táblába. 

### <a name="mechanism-2-using-sliceidentifiercolumnname"></a>2. mechanizmus: a sliceIdentifierColumnName használata
> [!IMPORTANT]
> A sliceIdentifierColumnName jelenleg nem támogatott az Azure szinapszis Analytics szolgáltatásban. 

A ismételhetőség elérésének második mechanizmusa egy dedikált oszlop (sliceIdentifierColumnName) a cél táblában. Ezt az oszlopot a Azure Data Factory fogja használni a forrás és a célhely szinkronizálásának biztosítása érdekében. Ez a megközelítés akkor működik, ha a cél SQL-tábla sémájának módosítása vagy meghatározása rugalmas. 

Ezt az oszlopot a Azure Data Factory a megismételhetőségi célokra használja, és a folyamat Azure Data Factory nem végez séma-módosításokat a táblán. Módszer a módszer használatára:

1. Adjon meg egy **bináris (32)** típusú oszlopot a cél SQL-táblában. Ezen az oszlopon nem lehetnek megkötések. Ennek az oszlopnak a neve legyen AdfSliceIdentifier ebben a példában.


    Forrástábla:

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL
    )
    ```

    Céltábla: 

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL,
       [AdfSliceIdentifier] [binary](32) NULL
    )
    ```

1. Használja a másolási tevékenységben a következőképpen:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "AdfSliceIdentifier"
    }
    ```

Azure Data Factory ezt az oszlopot a szükséges módon tölti fel a forrás és a cél szinkronizálásának biztosítása érdekében. Ezen oszlop értékei nem használhatók ezen környezeten kívül. 

Az 1. mechanizmushoz hasonlóan a másolási tevékenység automatikusan törli az adott szelet adatait a cél SQL-táblából. Ezután beszúrja az adatait a forrásból a cél táblába. 

## <a name="next-steps"></a>Következő lépések
Tekintse át a következő összekötő-cikkeket a teljes JSON-példákhoz: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)

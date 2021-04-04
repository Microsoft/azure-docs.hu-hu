---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 9447cec55c53861ca57d5416a91ffefd35fdd20b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91571974"
---
## <a name="repeatability-during-copy"></a>Ismételhetőség a másolás során
Az adatok Azure SQL/SQL Serverba más adattárakból való másolása során meg kell őrizni az ismételhetőséget a nem kívánt eredmények elkerülése érdekében. 

Az adatok Azure SQL/SQL Server-adatbázisba másolásakor a másolási tevékenység alapértelmezés szerint HOZZÁFŰZi az adatkészletet a fogadó táblához. Ha például egy CSV-fájlból (vesszővel tagolt értékeket tartalmazó adatokból) másol adatokat, amelyek két rekordot tartalmaznak az Azure SQL/SQL Server Database-be, akkor a táblázat a következőképpen néz ki:

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Tegyük fel, hogy hibákat észlelt a forrásfájlban, és frissítette a legördülő cső mennyiségét 2 és 4 között a forrásfájlban. Ha újra futtatja az adatszeletet az adott időszakra vonatkozóan, két új rekordot fog hozzáfűzni az Azure SQL/SQL Server-adatbázishoz. Az alábbi feltételezi, hogy a tábla egyik oszlopa sem rendelkezik az elsődleges kulcs korlátozásával.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Ennek elkerüléséhez meg kell adnia a UPSERT szemantikai felépítését az alább ismertetett 2 mechanizmus egyikének kihasználása után.

> [!NOTE]
> A szeletek a megadott újrapróbálkozási házirend szerint automatikusan újra futtathatók Azure Data Factoryban.
> 
> 

### <a name="mechanism-1"></a>1. mechanizmus
A **sqlWriterCleanupScript** tulajdonság kihasználható úgy, hogy először a törlés műveletet hajtsa végre egy szelet futtatásakor. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

A rendszer először a törlési parancsfájlt fogja végrehajtani egy adott szelet másolásakor, amely az adott szeletnek megfelelő SQL-táblából törli az adatait. A tevékenység később beszúrja az adatfájlokat az SQL-táblába. 

Ha a szelet most újra fut, akkor a rendszer a kívánt mennyiséget fogja megtalálni.

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
Nincs új teendője. A másolási tevékenység lefuttatta a törlési parancsfájlt, hogy törölje az adott szelethez tartozó megfelelő adatmennyiséget. Ezután olvassa be a CSV-bemenetet (amely ezután csak 1 rekordot foglalt bele), és beszúrta azt a táblába. 

### <a name="mechanism-2"></a>2. mechanizmus
> [!IMPORTANT]
> a sliceIdentifierColumnName jelenleg nem támogatott az Azure szinapszis Analytics szolgáltatásban. 

Egy másik mechanizmus az ismételhetőség eléréséhez egy dedikált oszlopot (**sliceIdentifierColumnName**) kell kijelölnie a cél táblában. Ezt az oszlopot a Azure Data Factory fogja használni a forrás és a célhely szinkronizálásának biztosítása érdekében. Ez a megközelítés akkor működik, ha a cél SQL-tábla sémájának módosítása vagy meghatározása rugalmas. 

Ezt az oszlopot a Azure Data Factory a megismételhetőségi célokra használja, a folyamat Azure Data Factory nem végez sémát a táblán. Módszer a módszer használatára:

1. Adjon meg egy bináris (32) típusú oszlopot a cél SQL-táblában. Ezen az oszlopon nem lehetnek megkötések. Ebben a példában a "ColumnForADFuseOnly" nevet adja az oszlopnak.
2. Használja a másolási tevékenységben a következőképpen:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "ColumnForADFuseOnly"
    }
    ```

Azure Data Factory ezt az oszlopot kell feltöltenie, a forrás és a cél szinkronizálásának biztosítása érdekében. Ennek az oszlopnak az értékeit a felhasználó nem használhatja ezen környezeten kívül. 

Az 1. mechanizmushoz hasonlóan a másolási tevékenység automatikusan törli az adott szelet adatait a cél SQL-táblából, majd a másolási tevékenységet általában a forrás és a cél közötti adatok beszúrására futtatja. 


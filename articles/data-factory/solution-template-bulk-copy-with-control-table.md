---
title: Tömeges másolás egy adatbázisból a Control Table használatával
description: Megtudhatja, hogyan másolhat egy külső vezérlőt egy olyan megoldási sablonnal, amellyel a Azure Data Factory használatával tárolhatja a forrásadatok listáját egy adatbázisból.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/14/2018
ms.openlocfilehash: 46e81242c1fba463f547015a244650ae6e574580
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629082"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>Tömeges másolás egy vezérlőelem-táblázattal rendelkező adatbázisból

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ha az Oracle Server, a Netezza, a Teradata SQL Server vagy a Azure SQL Data Warehouse adattárházból szeretne Adatmásolást készíteni, több táblából is nagy mennyiségű adattal kell betöltenie. Az adatok általában az egyes táblákban particionálva vannak, így egyetlen táblából párhuzamosan több szálat tartalmazó sorok is betölthetők. Ez a cikk az ezekben a forgatókönyvekben használandó sablont ismerteti.

 >! Vegye figyelembe, hogy ha kis számú táblából szeretne adatmásolni viszonylag kis adatmennyiséggel SQL Data Warehouse, akkor hatékonyabbá válik a [Azure Data Factory adatok másolása eszköz](copy-data-tool.md)használata. A cikkben ismertetett sablon ennél a forgatókönyvnél többre van szüksége.

## <a name="about-this-solution-template"></a>Tudnivalók a megoldási sablonról

Ez a sablon lekéri a forrás adatbázis-partíciók listáját, amelyeket egy külső vezérlő táblából másolhat. Ezután megismétli a forrás-adatbázis egyes partícióit, és átmásolja azokat a célhelyre.

A sablon három tevékenységet tartalmaz:
- A **lookup** lekéri az adatbázis-partíciók listáját egy külső vezérlő táblából.
- A **foreach** beolvassa a keresési tevékenységből a partíciók listáját, és megismétli az egyes partíciókat a másolási tevékenységnek.
- **Másolja** az egyes partíciókat a forrás-adatbázis tárolójából a célhelyre.

A sablon a következő paramétereket definiálja:
- *Control_Table_Name* a külső vezérlő tábla, amely a forrás-adatbázishoz tartozó partíciók listáját tárolja.
- *Control_Table_Schema_PartitionID* a külső vezérlő táblában található oszlopnév neve, amely az egyes partíció-azonosítókat tárolja. Győződjön meg arról, hogy a partíció azonosítója egyedi a forrásadatbázis minden partícióján.
- *Control_Table_Schema_SourceTableName* a külső vezérlő tábla, amely az egyes táblák nevét a forrás-adatbázisból tárolja.
- *Control_Table_Schema_FilterQuery* a külső vezérlő tábla azon oszlopának neve, amely a szűrő lekérdezést tárolja, hogy a forrásadatbázis egyes partícióinak adatait beolvassa. Ha például az adatok évről évre particionálva lettek, az egyes sorokban tárolt lekérdezés hasonló lehet a "Select" adatforráshoz, ahol a LastModifytime >= "2015-01-01 00:00:00" és a LastModifytime <= "" 2015-12-31 23:59:59.999 "".
- *Data_Destination_Folder_Path* az az elérési út, ahová a rendszer átmásolja az adatait a célhelyre (akkor alkalmazható, ha a kiválasztott cél a "File System" vagy a "Azure Data Lake Storage Gen1"). 
- *Data_Destination_Container* a gyökérmappa elérési útja, ahová a rendszer átmásolja az adatait a célhelyre. 
- *Data_Destination_Directory* az a könyvtár elérési útja, amelynek a gyökerében az adatait a rendszer átmásolja a célhelyre. 

Az utolsó három paraméter, amely meghatározza a célhely elérési útját, csak akkor látható, ha a kiválasztott cél a fájl alapú tárterület. Ha a cél tárolóként az "Azure szinapszis Analytics (korábban SQL DW)" lehetőséget választja, akkor ezek a paraméterek nem szükségesek. A táblák neveinek és a SQL Data Warehouse sémájának meg kell egyeznie a forrás-adatbázisban találhatókkal.

## <a name="how-to-use-this-solution-template"></a>A megoldás sablonjának használata

1. Hozzon létre egy vezérlőelem-táblázatot SQL Server vagy Azure SQL Database a forrás adatbázis-partíciók listájának tömeges másoláshoz való tárolásához. A következő példában öt partíció található a forrás adatbázisban. A *datasource_table*három partíció van, a kettő pedig a *project_table*. A *LastModifytime* oszlop az adatok a forrásadatbázis *datasource_table* történő particionálására szolgál. Az első partíció olvasásához használt lekérdezés "select * from datasource_table, ahol LastModifytime >=" "2015-01-01 00:00:00" "és LastModifytime <=" "2015-12-31 23:59:59.999" ". Hasonló lekérdezést használhat más partíciók adatainak olvasásához.

     ```sql
            Create table ControlTableForTemplate
            (
            PartitionID int,
            SourceTableName  varchar(255),
            FilterQuery varchar(255)
            );

            INSERT INTO ControlTableForTemplate
            (PartitionID, SourceTableName, FilterQuery)
            VALUES
            (1, 'datasource_table','select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''),
            (2, 'datasource_table','select * from datasource_table where LastModifytime >= ''2016-01-01 00:00:00'' and LastModifytime <= ''2016-12-31 23:59:59.999'''),
            (3, 'datasource_table','select * from datasource_table where LastModifytime >= ''2017-01-01 00:00:00'' and LastModifytime <= ''2017-12-31 23:59:59.999'''),
            (4, 'project_table','select * from project_table where ID >= 0 and ID < 1000'),
            (5, 'project_table','select * from project_table where ID >= 1000 and ID < 2000');
    ```

2. Nyissa meg a **tömeges másolást az adatbázis-** sablonból. Hozzon létre egy **új** kapcsolódást az 1. lépésben létrehozott külső vezérlő táblához.

    ![Új kapcsolódás létrehozása a vezérlő táblához](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Hozzon létre egy **új** kapcsolódást a forrás-adatbázishoz, amelyből az adatok másolása folyamatban van.

    ![Új kapcsolódás létrehozása a forrás-adatbázishoz](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Hozzon létre egy **új** kapcsolódást ahhoz a célhely-adattárhoz, amelyhez az adatok másolása folyamatban van.

    ![Új kapcsolódás létrehozása a célhelyhez](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Válassza **a sablon használata**lehetőséget.

6. Ekkor megjelenik a folyamat, ahogy az az alábbi példában is látható:

    ![A folyamat áttekintése](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Válassza a **hibakeresés**lehetőséget, adja meg a **paramétereket**, majd kattintson a **Befejezés gombra**.

    ![Kattintson * * hibakeresés * *](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. A következő példához hasonló eredmények jelennek meg:

    ![Az eredmény áttekintése](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. Választható Ha úgy döntött, hogy az "Azure szinapszis Analytics (korábban SQL DW)" értéket választotta az adat célhelyként, meg kell adnia egy, az Azure Blob Storage-hoz való kapcsolódást az átmeneti tároláshoz, SQL Data Warehouse-alapú alapkövetelménynek megfelelően. A sablon automatikusan létrehozza a tároló elérési útját a blob Storage-hoz. Ellenőrizze, hogy létrejött-e a tároló a folyamat futtatása után.
    
    ![Alapszintű beállítás](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>További lépések

- [Az Azure Data Factory bemutatása](introduction.md)

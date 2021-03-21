---
title: Különbözeti másolás egy adatbázisból egy vezérlőelem-táblázat használatával
description: Megtudhatja, hogyan használhat megoldási sablont az új vagy frissített sorok növekményes másolásához csak Azure Data Factory-alapú adatbázisból.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/09/2020
ms.openlocfilehash: b3b5679b254a07b275cc7fd1295ba4ca5b405fbc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100362012"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>Különbözeti másolat egy adatbázisból egy vezérlő táblával

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk egy olyan sablont ismertet, amely egy adatbázis-táblázat új vagy frissített sorainak az Azure-ba való növekményes betöltését mutatja be egy olyan külső vezérlőelem-táblázat használatával, amely a nagy vízjel értéket tárolja.

Ehhez a sablonhoz az szükséges, hogy a forrásadatbázis sémája időbélyeg-oszlopot vagy növekményes kulcsot tartalmazzon az új vagy frissített sorok azonosításához.

>[!NOTE]
> Ha a forrásadatbázis timestamp oszlopa új vagy frissített sorok azonosítására szolgál, de nem szeretne létrehozni egy külső vezérlőt a különbözeti másoláshoz, használja a [Azure Data Factory adatok másolása eszközt](copy-data-tool.md) a folyamat lekéréséhez. Az eszköz egy trigger-ütemezett időpontot használ változóként az új sorok olvasásához a forrás-adatbázisból.

## <a name="about-this-solution-template"></a>Tudnivalók a megoldási sablonról

Ez a sablon először a régi küszöbértéket kérdezi le, és összehasonlítja az aktuális vízjel értékével. Ezt követően a rendszer csak a forrásadatbázis változásait másolja át a két vízjel értékének összehasonlítása alapján. Végül az új, magas küszöbértékű értékeket egy külső vezérlőelem-táblára tárolja a következő alkalommal betöltött különbözeti betöltéshez.

A sablon négy tevékenységet tartalmaz:
- A **Keresés** lekéri a régi, magas küszöbértékű értéket, amely egy külső vezérlő táblában van tárolva.
- Egy másik **keresési** tevékenység lekéri a jelenlegi, magas küszöbértékű értéket a forrás-adatbázisból.
- A másolás csak a forrásadatbázis változásait **másolja** a célhelyre. A forrásadatbázis módosításainak azonosítására szolgáló lekérdezés hasonló a "SELECT * FROM Data_Source_Table, ahol TIMESTAMP_Column >" utolsó nagy vízjel "és TIMESTAMP_Column <=" aktuális, magas vízjel "".
- A **SqlServerStoredProcedure** a jelenlegi magas küszöbértékű értéket egy külső vezérlőelem-táblázatba írja a következő alkalommal, a különbözeti másoláshoz.

A sablon a következő paramétereket definiálja:
- *Data_Source_Table_Name* a forrás-adatbázis azon táblája, amelyről be szeretné tölteni az adatait.
- *Data_Source_WaterMarkColumn* a forrás tábla azon oszlopának neve, amely az új vagy frissített sorok azonosítására szolgál. Az oszlop típusa általában *datetime*, *int* vagy hasonló.
- A *Data_Destination_Container* az a hely, ahol az Adatmásolás a célhelyre történik.
- A *Data_Destination_Directory* a könyvtár elérési útja annak a helynek a gyökerében, ahová az adat a célhelyre másolódik.
- *Data_Destination_Table_Name* az a hely, ahol az Adatmásolás a célhelyre történik (ez akkor alkalmazható, ha az "Azure szinapszis Analytics" lehetőséget választja az adat célhelyként).
- *Data_Destination_Folder_Path* az a hely, ahová a rendszer átmásolja az adatait a célhelyre (ha a "fájlrendszer" vagy a "Azure Data Lake Storage Gen1" lehetőség van kiválasztva adat célhelyként).
- A *Control_Table_Table_Name* a külső vezérlő tábla, amely a nagy vízjel értékét tárolja.
- *Control_Table_Column_Name* a külső vezérlő tábla azon oszlopa, amely a nagy vízjel értékét tárolja.

## <a name="how-to-use-this-solution-template"></a>A megoldás sablonjának használata

1. Ismerkedjen meg a betöltendő forrás táblával, és adja meg az új vagy frissített sorok azonosításához használható nagy vízjel oszlopot. Az oszlop típusa *datetime*, *int* vagy hasonló lehet. Az oszlop értéke nő, ahogy az új sorok hozzáadása megtörténik. A következő példában szereplő forrástábla (data_source_table) alapján a *LastModifytime* oszlopot a nagy vízjel oszlopként használhatja.

    ```sql
            PersonID    Name    LastModifytime
            1   aaaa    2017-09-01 00:56:00.000
            2   bbbb    2017-09-02 05:23:00.000
            3   cccc    2017-09-03 02:36:00.000
            4   dddd    2017-09-04 03:21:00.000
            5   eeee    2017-09-05 08:06:00.000
            6   fffffff 2017-09-06 02:23:00.000
            7   gggg    2017-09-07 09:01:00.000
            8   hhhh    2017-09-08 09:01:00.000
            9   iiiiiiiii   2017-09-09 09:01:00.000
    ```
    
2. Hozzon létre egy vezérlőelem-táblázatot SQL Server vagy Azure SQL Database a különbözeti betöltéshez szükséges magas vízjel értékének tárolására. A következő példában a vezérlő tábla neve *watermarktable*. Ebben a táblázatban a *WatermarkValue* az az oszlop, amely a nagy vízjel értékét tárolja, a típusa pedig *datetime*.

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. Hozzon létre egy tárolt eljárást ugyanabban a SQL Server-vagy Azure SQL Database-példányban, amelyet a vezérlő tábla létrehozásához használt. A tárolt eljárással az új, magas vízjel érték a külső vezérlő táblára írható a következő alkalommal betöltött különbözeti betöltéshez.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. Lépjen a **különbözeti másolás adatbázis-sablonból elemre** . Hozzon létre egy **új** kapcsolódást ahhoz a forrás-adatbázishoz, amelyről az adatok másolását el szeretné készíteni.

    ![Új kapcsolódás létrehozása a forrástábla számára](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. Hozzon létre egy **új** kapcsolódást ahhoz a célhely-adattárhoz, amelyhez az Adatmásolást el szeretné másolni.

    ![Új kapcsolódás létrehozása a céltábla](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. Hozzon létre egy **új** kapcsolódást a külső vezérlő táblával és a 2. és 3. lépésben létrehozott tárolt eljárással.

    ![Új kapcsolódás létrehozása a Control Table adattárhoz](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. Válassza **a sablon használata** lehetőséget.
    
8. A rendelkezésre álló folyamat az alábbi példában látható módon jelenik meg:
  
    ![A folyamat áttekintése](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. Válassza a **tárolt eljárás** lehetőséget. A **tárolt eljárás neve** mezőben válassza a **[dbo] lehetőséget. [ update_watermark]**. Válassza az **importálási paraméter** lehetőséget, majd válassza a **dinamikus tartalom hozzáadása** lehetőséget.  

    ![Tárolt eljárási tevékenység beállítása](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png)  

10. Írja be a **\@ (z) {Activity ("LookupCurrentWaterMark"). output. FirstRow. NewWatermarkValue}** nevű tartalmat, majd válassza a **Befejezés** lehetőséget.  

    ![A tárolt eljárás paramétereinek megírása](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)       
     
11. Válassza a **hibakeresés** lehetőséget, adja meg a **paramétereket**, majd kattintson a **Befejezés gombra**.

    ![Válassza a * * hibakeresés * * lehetőséget](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. Az alábbi példához hasonló eredmények jelennek meg:

    ![Az eredmény áttekintése](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. A forrástábla új sorokat is létrehozhat. Az új sorok létrehozásához az alábbi minta SQL nyelve szükséges:

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```

14. A folyamat ismételt futtatásához válassza a **hibakeresés** lehetőséget, adja meg a **paramétereket**, majd kattintson a **Befejezés gombra**.

    Látni fogja, hogy csak az új sorok lettek átmásolva a célhelyre.

15. Választható Ha az Azure szinapszis Analytics lehetőséget választja az adatok célhelyként, akkor az Azure-alapú blob Storage-hoz történő átmeneti tároláshoz is meg kell adnia egy kapcsolódási lehetőséget. A sablon létrehozza a tároló elérési útját. A folyamat futtatása után győződjön meg arról, hogy a tároló létre lett-e hozva a blob Storage-ban.
    
    ![A bázisterület konfigurálása](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>Következő lépések

- [Tömeges másolás adatbázisból egy Azure Data Factory tartalmazó vezérlőelem-táblázat használatával](solution-template-bulk-copy-with-control-table.md)
- [Több tárolóból származó fájlok másolása Azure Data Factory](solution-template-copy-files-multiple-containers.md)

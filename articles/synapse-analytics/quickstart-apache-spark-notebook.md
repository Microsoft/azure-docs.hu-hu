---
title: 'Gyors útmutató: kiszolgáló nélküli Apache Spark-készlet létrehozása webes eszközökkel'
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre kiszolgáló nélküli Apache Spark készletet az Azure szinapszis Analytics szolgáltatásban, és hogyan futtathat Spark SQL-lekérdezést a web Tools használatával.
services: synapse-analytics
author: euangMS
ms.author: euang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: quickstart
ms.date: 10/16/2020
ms.openlocfilehash: 060c78621b82f4698d4596383cd155d85d483d8e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101669494"
---
# <a name="quickstart-create-a-serverless-apache-spark-pool-in-azure-synapse-analytics-using-web-tools"></a>Gyors útmutató: kiszolgáló nélküli Apache Spark készlet létrehozása az Azure szinapszis Analyticsben webes eszközök használatával

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre kiszolgáló nélküli Apache Spark készletet az Azure Szinapszisban webes eszközökkel. Ezután megismerheti a Apache Spark készlethez való kapcsolódást és a Spark SQL-lekérdezések futtatását a fájlokon és táblákon. Az Apache Spark a memóriában végzett feldolgozás segítségével teszi lehetővé a gyors adatelemzést és fürtszámítást. További információ a Sparkról az Azure Szinapszisban [: Áttekintés: Apache Spark az Azure szinapszisban](./spark/apache-spark-overview.md).

> [!IMPORTANT]
> A Spark-példányok számlázása percenként történik, függetlenül attól, hogy használja-e őket. A használata után állítsa le a Spark-példányt, vagy állítson be egy rövid időkorlátot. További információkért lásd a cikk **Az erőforrások eltávolítása** című szakaszát.

Ha nem rendelkezik Azure-előfizetéssel, [a Kezdés előtt hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [Szinapszis Analytics-munkaterület](quickstart-create-workspace.md)
- [Kiszolgáló nélküli Apache Spark készlet](quickstart-create-apache-spark-pool-studio.md)

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="create-a-notebook"></a>Jegyzetfüzet létrehozása

A notebook egy interaktív környezet, amely különböző programozási nyelveket támogat. A notebook lehetővé teszi az adatokkal való interakciót, a kódok Markdown, szöveggel való egyesítését és egyszerű vizualizációk elvégzését.

1. A használni kívánt Azure-beli szinapszis-munkaterület Azure Portal nézetében válassza a **szinapszis Studio elindítása** lehetőséget.
2. A szinapszis Studio elindítása után válassza a **fejlesztés** lehetőséget. Ezután válassza a " **+** " ikont új erőforrás hozzáadásához.
3. Innen válassza a **Jegyzetfüzet** lehetőséget. Létrejön egy új jegyzetfüzet, amely automatikusan generált névvel van megnyitva.
 
     ![Új jegyzetfüzet](./media/quickstart-apache-spark-notebook/spark-get-started-new-notebook.png "Új jegyzetfüzet")

4. A **Tulajdonságok** ablakban adja meg a jegyzetfüzet nevét.
5. Az eszköztáron kattintson a **Közzététel** elemre.
6. Ha a munkaterületen csak egy Apache Spark készlet található, akkor alapértelmezés szerint ki van választva. A legördülő listából válassza ki a megfelelő Apache Spark készletet, ha nincs kiválasztva.
7. Kattintson a **kód hozzáadása** gombra. Az alapértelmezett nyelv: `Pyspark` . A Pyspark és a Spark SQL kombinációját fogja használni, ezért az alapértelmezett választás a megfelelő. Más támogatott nyelvek a Scala és a .NET for Spark.
8. Ezután hozzon létre egy egyszerű Spark DataFrame objektumot a kezeléshez. Ebben az esetben a kód alapján hozza létre. Három sor és három oszlop található:

   ```python
   new_rows = [('CA',22, 45000),("WA",35,65000) ,("WA",50,85000)]
   demo_df = spark.createDataFrame(new_rows, ['state', 'age', 'salary'])
   demo_df.show()
   ```

9. Most futtassa a cellát az alábbi módszerek egyikével:

   - Nyomja le a **SHIFT + ENTER** billentyűkombinációt.
   - A cella bal oldalán válassza a kék lejátszás ikont.
   - Jelölje be az **összes futtatása** gombot az eszköztáron.

       ![Adatkeret-objektum létrehozása](./media/quickstart-apache-spark-notebook/spark-get-started-create-data-frame-object.png)

10. Ha a Apache Spark Pool-példány még nem fut, az automatikusan elindul. A Apache Spark Pool példányának állapotát a futtatott cella alatt, valamint a jegyzetfüzet alján található állapotjelző panelen tekintheti meg. A készlet méretétől függően a kezdés 2-5 percet vesz igénybe. Miután a kód futása befejeződött, a cella alatt látható információk azt mutatják, hogy mennyi ideig tartott a Futtatás és a végrehajtás. A kimeneti cellában megjelenik a kimenet.

    ![Cella végrehajtásának kimenete](./media/quickstart-apache-spark-notebook/run-cell-with-output.png)

11. Az adatok már léteznek a DataFrame, így számos különböző módon használhatja az adatmennyiséget. A rövid útmutató hátralévő részében különböző formátumokra lesz szüksége.
12. Írja be az alábbi kódot egy másik cellába, és futtassa, ezzel létrehoz egy Spark-táblázatot, egy CSV-fájlt és egy, az összes másolattal rendelkező Parquet.

    ```python
     demo_df.createOrReplaceTempView('demo_df')
     demo_df.write.csv('demo_df', mode='overwrite')
     demo_df.write.parquet('abfss://<<TheNameOfAStorageAccountFileSystem>>@<<TheNameOfAStorageAccount>>.dfs.core.windows.net/demodata/demo_df', mode='overwrite')
    ```

    Ha a Storage Explorert használja, lehetséges, hogy a fentiekben használt két különböző módszer hatását is láthatja. Ha nincs megadva fájlrendszer, a rendszer az alapértelmezett értéket használja, ebben az esetben `default>user>trusted-service-user>demo_df` . A rendszer menti az adatfájlokat a megadott fájlrendszer helyére.

    Figyelje meg, hogy a "CSV" és a "parketta" formátumokban az írási műveletek számos particionált fájllal jönnek létre.

    ![A kimenet Storage Explorer-nézete](./media/quickstart-apache-spark-notebook/spark-get-started-default-storage.png "A kimenet Storage Explorer-nézete")

    ![Képernyőkép, amely kiemeli az alapértelmezett > demodata > demo_df elérési utat.](./media/quickstart-apache-spark-notebook/spark-get-started-default-storage2.png "A kimenet Storage Explorer-nézete")

## <a name="run-spark-sql-statements"></a>Spark SQL-utasítások futtatása

A Structured Query Language (SQL) a leggyakoribb és legszélesebb körben használt nyelv az adatlekérdezéshez és definiáláshoz. A Spark SQL az Apache Spark bővítményeként működik a strukturált adatok ismerős SQL-szintaxissal való feldolgozásához.

1. Illessze be a következő kódot egy üres cellába, majd futtassa a kódot. A parancs felsorolja a készletben lévő táblákat.

   ```sql
   %%sql
   SHOW TABLES
   ```

   Ha az Azure szinapszis Apache Spark készlettel rendelkező jegyzetfüzetet használ, olyan beállításkészletet kap, `sqlContext` amelyet a Spark SQL használatával lekérdezések futtatására használhat. `%%sql` azt jelzi, hogy a jegyzetfüzet az előre beállított használatával `sqlContext` futtatja a lekérdezést. A lekérdezés lekérdezi az első 10 sort egy olyan rendszertáblából, amely alapértelmezés szerint az összes Azure szinapszis Apache Spark készlethez tartozik.

2. Futtasson egy másik lekérdezést a `demo_df` adatainak megtekintéséhez.

    ```sql
    %%sql
    SELECT * FROM demo_df
    ```

    A kód két kimeneti cellát hoz létre, amelyek az egyiket, amely az adatokat tartalmazza, a másikat, amely megjeleníti a feladat nézetét.

    Alapértelmezés szerint az eredmények nézet egy rácsot jelenít meg. Van azonban egy nézet kapcsoló a rács alatt, amely lehetővé teszi, hogy a nézet a rács és a gráf nézetek között legyen.

    ![Lekérdezés kimenete az Azure szinapszis Sparkban](./media/quickstart-apache-spark-notebook/spark-get-started-query.png "Lekérdezés kimenete az Azure szinapszis Sparkban")

3. A **nézet** kapcsolójában válassza a **diagram** lehetőséget.
4. Válassza ki a **megtekintési beállítások** ikont a jobb szélső oldalról.
5. A **diagram típusa** mezőben válassza a "sávdiagram" lehetőséget.
6. Az X tengely oszlop mezőjében válassza az "állapot" lehetőséget.
7. Az Y tengely oszlop mezőjében válassza a "fizetés" lehetőséget.
8. Az **Összesítés** mezőben válassza az "AVG" lehetőséget.
9. Kattintson az **Alkalmaz** gombra.

   ![Diagram kimenete az Azure szinapszis Sparkban](./media/quickstart-apache-spark-notebook/spark-get-started-query-chart-output.png "Diagram kimenete az Azure szinapszis Sparkban")

10. Ugyanezt az élményt futtathatja az SQL-ben, de nem kell nyelvet váltania. Ezt úgy teheti meg, hogy lecseréli a fenti SQL-cellát a PySpark cellába, és a kimeneti élmény ugyanaz, mert a **Display** parancs használatban van:

    ```python
    display(spark.sql('SELECT * FROM demo_df'))
    ```

11. A korábban végrehajtott cellák mindegyike lehetőséget kapott az **Előzmények kiszolgáló** és a **figyelés** megugrására. A hivatkozásokra kattintva a felhasználói élmény különböző részeire juthat.

> [!NOTE]
> A [Apache Spark hivatalos dokumentációja](https://spark.apache.org/docs/latest/) a Spark-konzolt használja, amely nem érhető el a szinapszis Sparkban. Ehelyett használjon [jegyzetfüzetet](quickstart-apache-spark-notebook.md) vagy [IntelliJ](./spark/intellij-tool-synapse.md) -élményt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az Azure szinapszis Azure Data Lake Storage tárolja az adatait. Ha nincs használatban, nyugodtan engedélyezheti a Spark-példányok leállítását. A kiszolgáló nélküli Apache Spark-készletért kell fizetnie, ha az fut, még akkor is, ha nincs használatban. 

Mivel a készlet díjai több időt vesznek igénybe, mint a tárterületre vonatkozó díjak, a gazdasági érzék lehetővé teszi, hogy a Spark-példányok leálljanak, amikor nincsenek használatban.

A Spark-példány leállításának biztosításához fejezze be a csatlakoztatott munkameneteket (jegyzetfüzeteket). A készlet leáll, amikor eléri a Apache Spark készletben megadott **üresjárati időt** . A **befejezési munkamenetet** a jegyzetfüzet alján található állapotsorból is kiválaszthatja.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre kiszolgáló nélküli Apache Spark készletet, és hogyan futtathat egy alapszintű Spark SQL-lekérdezést.

- [Azure Synapse Analytics](overview-what-is.md)
- [.NET Apache Spark dokumentációhoz](/dotnet/spark)




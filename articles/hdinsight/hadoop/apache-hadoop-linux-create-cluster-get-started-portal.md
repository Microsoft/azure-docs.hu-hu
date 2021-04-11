---
title: 'Gyors útmutató: Apache Hadoop, Apache Hive & Azure HDInsight-portál'
description: Ebben a rövid útmutatóban egy HDInsight Hadoop-fürt létrehozásához használja a Azure Portal
keywords: hadoop első lépések, hadoop linux, hadoop rövid útmutató, hive első lépések, hive rövid útmutató
ms.service: hdinsight
ms.topic: quickstart
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/24/2020
ms.openlocfilehash: 92520be7e9d2bb83cb61856fe3df38487445ad9a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863887"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-azure-portal"></a>Rövid útmutató: Apache Hadoop-fürt létrehozása az Azure HDInsight Azure Portal használatával

Ebből a cikkből megtudhatja, hogyan hozhat létre Apache Hadoop-fürtöket a HDInsight-ben Azure Portal használatával, majd hogyan futtathat Apache Hive feladatokat a HDInsight. A legtöbb Hadoop-feladat kötegelt feladat. Létrehoz fog hozni egy fürtöt, futtat néhány feladatot, majd törölni fogja a fürtöt. Ebben a cikkben mind a három feladatot elvégzi. Az elérhető konfigurációk részletes ismertetését lásd: [fürtök beállítása a HDInsight-ben](../hdinsight-hadoop-provision-linux-clusters.md). A portál fürtön való létrehozásával kapcsolatos további információkért lásd: [fürtök létrehozása a portálon](../hdinsight-hadoop-create-linux-clusters-portal.md).

Ebben a rövid útmutatóban egy HDInsight Hadoop-fürtöt hoz létre az Azure Portal használatával. Az [Azure Resource Manager-sablonok](apache-hadoop-linux-tutorial-get-started.md) használatával is létrehozhat fürtöket.

A HDInsight jelenleg [hét különböző típusú fürtöt](../hdinsight-overview.md#cluster-types-in-hdinsight)tartalmaz. Minden egyes fürttípus más és más összetevőket támogat. A Hive-ot minden fürttípus támogatja. A HDInsight által támogatott összetevők listáját a következő témakörben tekintheti [meg: Újdonságok a Apache Hadoop-fürt HDInsight által biztosított verzióiban?](../hdinsight-component-versioning.md)  

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="create-an-apache-hadoop-cluster"></a>Apache Hadoop-fürt létrehozása

Ebben a szakaszban egy Hadoop-fürtöt hozhat létre a HDInsightban az Azure Portal használatával.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com).

1. A felső menüben válassza az **+ erőforrás létrehozása** lehetőséget.

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-create-resource.png" alt-text="Erőforrás-HDInsight fürt létrehozása" border="true":::

1. Válassza az **Analytics**  >  **Azure HDInsight** lehetőséget a **HDInsight-fürt létrehozása** lap megjelenítéséhez.

1. Az **alapok** lapon adja meg a következő információkat:

   |Tulajdonság  |Leírás  |
   |---------|---------|
   |Előfizetés    |  A legördülő listában válassza ki a fürthöz használt Azure-előfizetést. |
   |Erőforráscsoport     | A legördülő listából válassza ki a meglévő erőforráscsoportot, vagy válassza az **új létrehozása** lehetőséget.|
   |Fürt neve   | Adjon meg egy globálisan egyedi nevet. A név legfeljebb 59 karaktert tartalmazhat, beleértve a betűket, számokat és kötőjeleket. A név első és utolsó karaktere nem lehet kötőjel. |
   |Region    | A legördülő listából válassza ki azt a régiót, ahol a fürtöt létrehozták.  A legjobb teljesítmény érdekében válassza az Önhöz legközelebb eső helyet. |
   |Fürt típusa| Válassza a **fürt típusának kiválasztása** lehetőséget. Ezután válassza a **Hadoop** lehetőséget.|
   |Verzió|A legördülő listából válassza ki a kívánt **verziót**. Ha nem tudja, mit kell választania, használja az alapértelmezett verziót.|
   |A fürt bejelentkezési felhasználóneve és jelszava    | Az alapértelmezett bejelentkezési név a **rendszergazda**. A jelszónak legalább 10 karakterből kell állnia, és tartalmaznia kell legalább egy számot, egy nagybetűs és egy kisbetűs betűt, egy nem alfanumerikus karaktert (kivéve a következő karaktereket: "" " \) . Győződjön meg róla, hogy **ne adjon meg** gyakori jelszót, mint például a következő: Pass@word1.|
   |Secure Shell- (SSH-) felhasználónév | Az alapértelmezett felhasználónév az **sshuser**.  SSH-felhasználónévként más nevet is megadhat. |
   |Fürt bejelentkezési jelszavának használata SSH-hoz| Jelölje be ezt a jelölőnégyzetet, ha ugyanazt a jelszót szeretné használni az SSH-felhasználó számára, mint a fürt bejelentkezési felhasználójának.|

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-basics.png" alt-text="A HDInsight Linux első lépései a fürt alapértékeit biztosítják" border="true":::

   Válassza ki a **következőt: storage >>** a tárolási beállításokra való továbblépés érdekében.

1. A **Storage (tárolás** ) lapon adja meg a következő értékeket:

   |Tulajdonság  |Leírás  |
   |---------|---------|
   |Elsődleges tároló típusa|Használja az alapértelmezett értéket az **Azure Storage**-ban.|
   |Kiválasztási módszer|Használja az alapértelmezett értéket a **listából**.|
   |Az elsődleges tárfiók|A legördülő listából válasszon ki egy meglévő Storage-fiókot, vagy válassza az **új létrehozása** lehetőséget. Új fiók létrehozásakor a névnek 3 – 24 karakter hosszúnak kell lennie, és csak számokat és kisbetűket tartalmazhat.|
   |Tároló|Használja az automatikusan feltöltött értéket.|

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-storage.png" alt-text="HDInsight Linux – első lépések – a fürt tárolási értékeinek megadása" border="true":::

   Minden fürt rendelkezik egy [Azure Storage-fiókkal](../hdinsight-hadoop-use-blob-storage.md), egy [Azure Data Lake Gen1](../hdinsight-hadoop-use-data-lake-storage-gen1.md)vagy egy [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md) függőséggel. Ezt az alapértelmezett Storage-fióknak nevezzük. A HDInsight-fürtnek és az alapértelmezett Storage-fióknak ugyanabban az Azure-régióban kell elhelyezkednie. A fürtök törlésével nem törlődik a Storage-fiók.

   Válassza a **felülvizsgálat + létrehozás** lapot.

1. A **felülvizsgálat + létrehozás** lapon ellenőrizze a korábbi lépésekben kiválasztott értékeket.

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-review-create-hadoop.png" alt-text="HDInsight Linux – első lépések – fürt összefoglalása" border="true":::

1. Válassza a **Létrehozás** lehetőséget. Egy fürt létrehozása nagyjából 20 percet vesz igénybe.

   A fürt létrehozása után megjelenik a fürt áttekintési oldala az Azure Portalon.

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-settings-overview.png" alt-text="HDInsight Linux első lépései – fürtbeállítások" border="true":::

## <a name="run-apache-hive-queries"></a>Apache Hive lekérdezések futtatása

Az [Apache Hive](hdinsight-use-hive.md) a HDInsight legnépszerűbb összetevője. Számos módon futtathat Hive-feladatokat a HDInsightban. Ebben a rövid útmutatóban a Ambari struktúra nézetet használja a portálon. A Hive-feladatok egyéb küldési módjaiért lásd: [Use Hive in HDInsight](hdinsight-use-hive.md) (A Hive használata a HDInsightban).

> [!NOTE]
> Apache Hive nézet nem érhető el a HDInsight 4,0-ben.

1. Az Ambari megnyitásához az előző képernyőkép szerint válassza a **Fürt irányítópultja** elemet.  Tallózással is megkeresheti, hogy  `https://ClusterName.azurehdinsight.net` hol `ClusterName` található a fürt, amelyet az előző szakaszban hozott létre.

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-open-cluster-dashboard.png" alt-text="HDInsight Linux – első lépések fürt irányítópultja" border="true":::

2. Adja meg a fürt létrehozásakor megadott Hadoop-felhasználónevet és -jelszót. Az alapértelmezett felhasználónév az **admin**.

3. Nyissa meg a **Hive View** nézetet az alábbi képernyőfelvételen látható módon:

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-select-hive-view.png" alt-text="A kaptár nézet kiválasztása a Ambari" border="true":::

4. A **QUERY** (Lekérdezés) lapon másolja be a következő HiveQL-kifejezést a munkalapra:

   ```sql
   SHOW TABLES;
   ```

    :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-apache-hive-view1.png" alt-text="HDInsight-struktúra nézet lekérdezés-szerkesztője" border="true":::

5. Válassza a **Végrehajtás** lehetőséget. A **QUERY** (Lekérdezés) lap alatt megjelenik a **RESULTS** (Eredmények) lap, amelyen a feladat információi láthatók.

   A lekérdezés befejeződése után a **lekérdezés** lap a művelet eredményét jeleníti meg. Látni fog egy **hivesampletable** nevű táblát. Ezzel a Hive mintatáblával az összes HDInsight-fürt rendelkezik.

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-hive-views.png" alt-text="HDInsight Apache Hive eredményeinek megtekintése" border="true":::

6. Ismételje meg a 4. és az 5. lépést az alábbi lekérdezés futtatásához:

   ```sql
   SELECT * FROM hivesampletable;
   ```

7. A lekérdezés eredményeit mentheti is. Válassza a jobb oldalon lévő menügombot, és adja meg, hogy letölti az eredményeket CSV-fájlként, vagy a fürthöz társított tárfiókban szeretné tárolni őket.

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-hive-view-save-results.png" alt-text="Apache Hive lekérdezés eredményének mentése" border="true":::

A kaptár-feladatok elvégzése után [exportálhatja az eredményeket Azure SQL Database vagy SQL Server adatbázisba](apache-hadoop-use-sqoop-mac-linux.md), de [az Excel használatával is megjelenítheti az eredményeket](apache-hadoop-connect-excel-power-query.md). További információ a kaptár HDInsight-ben való használatáról: a [Apache Hive és a HiveQL használata Apache Hadoop a HDInsight-ben egy minta Apache log4j-fájl elemzéséhez](hdinsight-use-hive.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A gyors üzembe helyezés befejezése után érdemes lehet törölni a fürtöt. A HDInsight az adatait az Azure Storage tárolja, így biztonságosan törölheti a fürtöt, ha az nincs használatban. A HDInsight-fürtökért is fizetnie kell, még akkor is, ha nincs használatban. Mivel a fürt díjai több időt vesznek igénybe, mint a tárterületre vonatkozó díjak, a gazdasági érzékek törlik a fürtöket, ha nincsenek használatban.

> [!NOTE]  
> Ha *azonnal* továbblép a következő cikkre, amelyből megtudhatja, hogyan futtathat ETL-műveleteket a HDInsight Hadoop használatával, érdemes megtartania a fürtöt. Ennek az az oka, hogy az oktatóanyagban újra létre kell hoznia egy Hadoop-fürtöt. Ha azonban a következő cikk azonnal nem érhető el, azonnal törölnie kell a fürtöt.

### <a name="to-delete-the-cluster-andor-the-default-storage-account"></a>A fürt és/vagy az alapértelmezett tárfiók törlése

1. Térjen vissza ahhoz a böngészőlaphoz, amelyen meg van nyitva az Azure Portal. A portálon a fürt áttekintési lapja lesz látható. Ha csak a fürtöt szeretné törölni, de meg szeretné tartani az alapértelmezett tárfiókot, válassza a **Törlés** lehetőséget.

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-delete-cluster.png" alt-text="Azure HDInsight-fürt törlése" border="true":::

2. Ha a fürtöt és az alapértelmezett tárfiókot is törölni kívánja, válassza ki az erőforráscsoport nevét (amely az előző képernyőképen ki van emelve) az erőforráscsoport lapjának megnyitásához.

3. Válassza az **Erőforráscsoport törlése** lehetőséget a fürtöt és az alapértelmezett tárfiókot tartalmazó erőforráscsoport törléséhez. Vegye figyelembe, hogy az erőforráscsoport törlése a tárfiókot is törli. Ha szeretné megtartani a tárfiókot, csak a fürtöt törölje.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Linux-alapú HDInsight-fürtöt egy Resource Manager-sablonnal, és hogyan hajthat végre alapszintű kaptár-lekérdezéseket. A következő cikkben megtudhatja, hogyan végezheti el az adatok kinyerési, átalakítási és betöltési (ETL) műveleteit a Hadoop használatával a HDInsighton.

> [!div class="nextstepaction"]
> [Adatok kinyerése, átalakítása és betöltése az interaktív lekérdezés használatával a HDInsight-on](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)

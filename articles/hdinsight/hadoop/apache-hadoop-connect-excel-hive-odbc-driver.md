---
title: Excel & Apache Hadoop nyílt adatbázis-kapcsolati (ODBC) illesztőprogrammal – Azure HDInsight
description: Ismerje meg, hogyan állíthatja be és használhatja az Excelhez készült Microsoft kaptár ODBC-illesztőt a Microsoft Excel HDInsight-fürtök adatainak lekérdezéséhez.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 2c528859ea5abc6267c10a2ede9c2ca99f84e22f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946805"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Az Excel és az Apache Hadoop csatlakoztatása az Azure HDInsightban a Microsoft Hive ODBC-illesztővel

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

A Microsoft Big adatmegoldása integrálja a Microsoft Business Intelligence-(BI-) összetevőket a HDInsight-ben üzembe helyezett Apache Hadoop-fürtökkel. Ilyen lehet például az Excel és a Hadoop-fürt struktúrájának adattárháza közötti kapcsolat. Kapcsolódjon a Microsoft kaptár Open Database Connectivity (ODBC) illesztőprogram használatával.

A HDInsight-fürtökhöz kapcsolódó adatok összekapcsolhatók az Excel programból az Excel programhoz készült Microsoft Power Query bővítmény használatával. További információ: az [Excel és a HDInsight Összekötése Power Query használatával](./apache-hadoop-connect-excel-power-query.md).

## <a name="prerequisites"></a>Előfeltételek

A cikk elkezdése előtt a következő elemeket kell megadnia:

* An méretű HDInsight Hadoop-fürt. Az első lépésekhez tekintse meg [Az Azure HDInsight megismerését](apache-hadoop-linux-tutorial-get-started.md)ismertető témakört.
* Egy Office 2010 Professional Plus vagy újabb, vagy Excel 2010 vagy újabb verziójú munkaállomás.

## <a name="install-microsoft-hive-odbc-driver"></a>A Microsoft kaptár ODBC-illesztő telepítése

Töltse le és telepítse a [Microsoft kaptár ODBC-illesztőt](https://www.microsoft.com/download/details.aspx?id=40886). Válassza ki az alkalmazás azon verzióját, amely az ODBC-illesztőt fogja használni.  Ebben a cikkben a-illesztőprogramot az Office Excelhez használja a rendszer.

## <a name="create-apache-hive-odbc-data-source"></a>ODBC-adatforrás létrehozása Apache Hive

A következő lépések bemutatják, hogyan hozhat létre egy struktúra ODBC-adatforrást.

1. A Windowsban navigáljon a **Start > Windows felügyeleti eszközök > ODBC-adatforrások (32 bites)/(64 bites)** elemre.  Ez a művelet megnyitja az **ODBC-adatforrás felügyeleti** ablakát.

    ![OBDC-adatforrás rendszergazdája](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-datasourceadmin1.png "ADATFORRÁS konfigurálása az ODBC-adatforrás rendszergazdájával")

1. A **felhasználói DSN** lapon válassza a **Hozzáadás** lehetőséget az **új adatforrás létrehozása** ablak megnyitásához.

1. Válassza a **Microsoft kaptár ODBC-illesztő** lehetőséget, majd kattintson a **Befejezés** gombra a **Microsoft kaptár ODBC-illesztő DSN-telepítő** ablak megnyitásához.

1. Írja be vagy válassza ki az alábbi értékeket:

   | Tulajdonság | Leírás |
   | --- | --- |
   |  Adatforrás neve |Adjon nevet az adatforrásának |
   |  Állomás (ok) |Írja be a következő szöveget: `HDInsightClusterName.azurehdinsight.net`. Például: `myHDICluster.azurehdinsight.net`. Megjegyzés: `HDInsightClusterName-int.azurehdinsight.net` támogatott, ha az ügyfél virtuális gépe ugyanahhoz a virtuális hálózathoz van felhasználva. |
   |  Port |Használja a **443** számú portot. (Ez a port megváltozott a 563-ról 443-ra.) |
   |  Adatbázis |Használja az **alapértelmezett értéket**. |
   |  Mechanizmus |A **Windows Azure HDInsight szolgáltatás** kiválasztása |
   |  Felhasználónév |Adja meg a HDInsight-fürt HTTP-felhasználói felhasználónevét. Az alapértelmezett felhasználónév az **admin**. |
   |  Jelszó |Adja meg a HDInsight-fürt felhasználói jelszavát. Jelölje be a **Jelszó mentése (titkosított)** jelölőnégyzetet.|

1. Választható lehetőség: válassza a **Speciális beállítások lehetőséget...**  

   | Paraméter | Leírás |
   | --- | --- |
   |  Natív lekérdezés használata |Ha be van jelölve, az ODBC-illesztő nem próbálkozik a TSQL konvertálásával a HiveQL-be. Ezt csak akkor kell használni, ha 100%-ban biztos benne, hogy tiszta HiveQL-utasításokat küld. SQL Server vagy Azure SQL Databasehoz való csatlakozáskor nincs bejelölve. |
   |  Beolvasott sorok száma blokkban |Nagy számú rekord beolvasása esetén a paraméter finomhangolása az optimális teljesítmény biztosítása érdekében szükséges lehet. |
   |  Alapértelmezett karakterlánc-oszlop hossza, Bináris oszlop hossza, decimális oszlop mérete |Az adattípus hossza és pontossága befolyásolhatja az adatvisszaadás módját. A pontosság és a csonkítás elvesztése miatt helytelen adatokat ad vissza. |

    ![Speciális DSN-konfigurációs beállítások](./media/apache-hadoop-connect-excel-hive-odbc-driver/hiveodbc-datasource-advancedoptions1.png "Speciális DSN-konfigurációs beállítások")

1. Válassza a **teszt** lehetőséget az adatforrás teszteléséhez. Ha az adatforrás megfelelően van konfigurálva, a teszt eredménye a **sikert mutatja.**

1. A teszt ablak bezárásához kattintson **az OK gombra** .  

1. Kattintson az **OK** gombra a **Microsoft kaptár ODBC illesztőprogram-DSN telepítési** ablak bezárásához.  

1. Kattintson az **OK** gombra az **ODBC-adatforrás felügyeleti** ablakának bezárásához.  

## <a name="import-data-into-excel-from-hdinsight"></a>Adatok importálása Excel formátumba a HDInsight-ból

A következő lépések leírják, hogyan importálhat egy struktúra-táblázat adatait egy Excel-munkafüzetbe az előző szakaszban létrehozott ODBC-adatforrás használatával.

1. Nyisson meg egy új vagy egy meglévő munkafüzetet Excelben.

2. Az **adatok** lapon navigáljon az **adatok beolvasása**  >  **más forrásokból** az ODBC-ből,  >   hogy elindítsa a **from ODBC** ablakot.

    ![Az Excel-adatkapcsolatok varázsló megnyitása](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-excel-dataconnection1.png "Az Excel-adatkapcsolatok varázsló megnyitása")

3. A legördülő listában válassza ki az előző szakaszban létrehozott adatforrás nevét, majd kattintson az **OK gombra**.

4. Az első használathoz egy **ODBC-illesztőprogram** párbeszédablak nyílik meg. A bal oldali menüben válassza a **Windows** lehetőséget. Ezután válassza a **Kapcsolódás** lehetőséget a **navigátor** ablak megnyitásához.

5. A **Navigátorban** navigáljon a **kaptár**  >  **alapértelmezett**  >  **hivesampletable**, majd válassza a **Betöltés** lehetőséget. Néhány percet vesz igénybe, mielőtt az Excelbe importálja az adatimportálást.

    ![HDInsight Excel-struktúra ODBC-navigátor](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight-hive-odbc-navigator.png "HDInsight Excel-struktúra ODBC-navigátor")

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan használhatja a Microsoft kaptár ODBC-illesztőt a HDInsight szolgáltatás adatainak az Excelbe való lekéréséhez. Hasonlóképpen lekérheti az HDInsight szolgáltatás adatait a SQL Databaseba. Az adatok az HDInsight szolgáltatásba is feltölthetők. További információ:

* Az [Azure HDInsight-ban a Microsoft Power BI Apache Hivei az adatmegjelenítést](apache-hadoop-connect-hive-power-bi.md).
* [Interaktív lekérdezési struktúra-adatmegjelenítés az Azure HDInsight Power BIával](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* Az [Excel Apache Hadoop Power Query használatával csatlakoztatható](apache-hadoop-connect-excel-power-query.md).
* [Kapcsolódjon az Azure HDInsight-hez, és Apache Hive-lekérdezéseket futtathat a Visual studióhoz készült Data Lake Tools használatával](apache-hadoop-visual-studio-tools-get-started.md).
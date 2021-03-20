---
title: Apache Base-lekérdezések futtatása az Azure HDInsight Apache Phoenix
description: Ismerje meg, hogy az Apache Zeppelin használatával hogyan futtathat Apache Base-lekérdezéseket a Phoenixben.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 10/14/2019
ms.openlocfilehash: 50a72d0400b23162e05b17b37bdad48783261072
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944766"
---
# <a name="use-apache-zeppelin-to-run-apache-phoenix-queries-over-apache-hbase-in-azure-hdinsight"></a>Az Apache Zeppelin használatával Apache Phoenix lekérdezéseket futtathat az Apache HBase az Azure HDInsight

A Apache Phoenix egy nyílt forráskódú, nagymértékben párhuzamos, HBase-ra épülő, többszintű, összehasonlítható adatbázis-réteg. A Phoenix lehetővé teszi, hogy az SQL-lekérdezéseket, például a HBase-t használja. A Phoenix JDBC-illesztőprogramokat használ, amelyek lehetővé teszik az SQL-táblák,-indexek,-nézetek és-folyamatok létrehozását, törlését, módosítását.  A Phoenix használatával a sorokat egyenként és tömegesen is frissítheti. A Phoenix NOSQL natív fordítást használ ahelyett, hogy a MapReduce használatával lefordítsa a lekérdezéseket, és lehetővé teszi a kis késleltetésű alkalmazások létrehozását a HBase-on.

Az Apache Zeppelin egy nyílt forráskódú webes jegyzetfüzet, amely lehetővé teszi, hogy adatvezérelt, együttműködési dokumentumokat hozzon létre interaktív adatelemzéssel és-nyelvekkel, például az SQL és a Scala használatával. Segít az adatfejlesztőknek & adatszakértőknek az adatkezeléshez szükséges programkódok fejlesztésében, rendszerezésében, végrehajtásában és megosztásában. Lehetővé teszi az eredmények megjelenítését anélkül, hogy a parancssorra kellene hivatkozni, vagy a fürt részleteit kellene megadnia.

A HDInsight-felhasználók az Apache Zeppelin használatával kérdezik le a Phoenix-táblákat. Az Apache Zeppelin integrálva van a HDInsight-fürttel, és nincs szükség további lépésekre. Egyszerűen hozzon létre egy Zeppelin notebookot a JDBC-tolmácsmal, és kezdje meg a Phoenix SQL-lekérdezések írását

## <a name="prerequisites"></a>Előfeltételek

Egy Apache HBase-fürt a HDInsight-on. Lásd: [az Apache HBase első lépései](./apache-hbase-tutorial-get-started-linux.md).

## <a name="create-an-apache-zeppelin-note"></a>Apache Zeppelin-Megjegyzés létrehozása

1. Cserélje le a `CLUSTERNAME` nevet a fürt nevére a következő URL-címben `https://CLUSTERNAME.azurehdinsight.net/zeppelin` . Ezután írja be az URL-címet egy böngészőben. Adja meg a fürt bejelentkezési felhasználónevét és jelszavát.

1. A Zeppelin lapon válassza az **új Megjegyzés létrehozása** lehetőséget.

    ![HDInsight interaktív lekérdezés Zeppelin](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-create-note.png)

1. Az **új Megjegyzés létrehozása** párbeszédpanelen írja be vagy válassza ki a következő értékeket:

    - Megjegyzés neve: adja meg a jegyzet nevét.
    - Alapértelmezett értelmező: válassza a **JDBC** elemet a legördülő listából.

    Ezután válassza a **Megjegyzés létrehozása** lehetőséget.

1. Győződjön meg arról, hogy a jegyzetfüzet fejléce csatlakoztatott állapotot mutat. Ezt a jobb felső sarokban található zöld pont jelöli.

    ![Zeppelin notebook állapota](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-connected.png "Zeppelin notebook állapota")

1. Hozzon létre egy HBase táblát. Adja meg a következő parancsot, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt:

    ```sql
    %jdbc(phoenix)
    CREATE TABLE Company (
        company_id INTEGER PRIMARY KEY,
        name VARCHAR(225)
    );
    ```

    Az első sorban található **% JDBC (Phoenix)** utasítás azt jelzi, hogy a jegyzetfüzet a Phoenix JDBC tolmácsot használja.

1. Létrehozott táblák megtekintése

    ```sql
    %jdbc(phoenix)
    SELECT DISTINCT table_name
    FROM SYSTEM.CATALOG
    WHERE table_schem is null or table_schem <> 'SYSTEM';
    ```

1. Értékek beszúrása a táblába.

    ```sql
    %jdbc(phoenix)
    UPSERT INTO dbo.Company VALUES(1, 'Microsoft');
    UPSERT INTO dbo.Company (name, company_id) VALUES('Apache', 2);
    ```

1. A tábla lekérdezése.

    ```sql
    %jdbc(phoenix)
    SELECT * FROM dbo.Company;
    ```

1. Rekord törlése.

    ```sql
    %jdbc(phoenix)
    DELETE FROM dbo.Company WHERE COMPANY_ID=1;
    ```

1. A tábla eldobása.

    ```sql
    %jdbc(phoenix)
    DROP TABLE dbo.Company;
    ```

## <a name="next-steps"></a>Következő lépések

- [A Apache Phoenix mostantól támogatja a Zeppelin használatát az Azure HDInsight](/archive/blogs/ashish/apache-phoenix-now-supports-zeppelin-in-azure-hdinsight)
- [Apache Phoenix nyelvtan](https://phoenix.apache.org/language/index.html)
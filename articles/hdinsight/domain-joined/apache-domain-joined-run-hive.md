---
title: Az Apache Ranger Apache Hive házirendjei – Azure HDInsight
description: Ismerje meg, hogyan konfigurálhatja az Apache Ranger-szabályzatokat a Kaptárhoz egy Azure HDInsight-szolgáltatásban Enterprise Security Package használatával.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: 15c406576b373577a2a3a50108acad7ccbf36699
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863258"
---
# <a name="configure-apache-hive-policies-in-hdinsight-with-enterprise-security-package"></a>Apache Hive-szabályzatok konfigurálása a HDInsightban az Enterprise Security Package csomaggal

Megtudhatja, hogyan konfigurálhatja a Apache Hive Apache Ranger-szabályzatait. Ebben a cikkben két Ranger-házirendet hoz létre a hivesampletable nevű táblához való hozzáférés korlátozása érdekében. A hivesampletable HDInsight-fürtöket tartalmaz. A szabályzatok konfigurálása után az Excel és az ODBC-illesztő használatával csatlakozhat a HDInsight-ben található kaptár-táblákhoz.

## <a name="prerequisites"></a>Előfeltételek

* Enterprise Security Package-t tartalmazó HDInsight-fürt. Lásd: [HDInsight-fürtök beállítása az ESP-vel](./apache-domain-joined-configure-using-azure-adds.md).
* A nagyvállalati, Office 2016, Office 2013 Professional Plus, Excel 2013 önálló vagy Office 2010 Professional Plus Microsoft 365 alkalmazásokkal rendelkező munkaállomás.

## <a name="connect-to-apache-ranger-admin-ui"></a>Csatlakozás az Apache Ranger felügyeleti felhasználói felületéhez
**Csatlakozás az Apache Ranger felügyeleti felhasználói felületéhez**

1. Egy böngészőben nyissa meg a Ranger felügyeleti felhasználói felületét, `https://CLUSTERNAME.azurehdinsight.net/Ranger/` ahol a CLUSTERNAME a fürt neve.

   > [!NOTE]  
   > A Ranger eltérő hitelesítő adatokat használ, mint a Apache Hadoop-fürt. Ha meg szeretné akadályozni, hogy a böngészők gyorsítótárazott Hadoop hitelesítő adatokat használjanak, használja az új InPrivate-böngészőablakot a Ranger felügyeleti felhasználói felületéhez.

2. Jelentkezzen be a fürt rendszergazdai tartományi felhasználónevével és jelszavával:

    :::image type="content" source="./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png" alt-text="HDInsight ESP Ranger kezdőlapja" border="true":::

    A Ranger jelenleg csak a Yarn és a Hive rendszerrel működik.

## <a name="create-domain-users"></a>Tartományi felhasználók létrehozása

A hiveruser1 és a hiveuser2 létrehozásával kapcsolatos információkért tekintse meg [a HDInsight-fürt ESP-vel történő létrehozását](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)ismertető témakört. Ebben a cikkben a két felhasználói fiókot használja.

## <a name="create-ranger-policies"></a>Ranger-házirendek létrehozása

Ebben a szakaszban két Ranger-szabályzatot hoz létre a hivesampletable eléréséhez. Adjon kiválasztási engedélyt a különböző oszlopcsoportokra vonatkozóan. Mindkét felhasználó a HDInsight- [fürt és az ESP](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)együttes használatával lett létrehozva. A következő szakaszban tesztelheti a két házirendet az Excelben.

**Ranger-házirendek létrehozása**

1. Nyissa meg a Ranger felügyeleti felhasználói felületét. Lásd a Csatlakozás az Apache Ranger felügyeleti felhasználói felületéhez című részt.
2. Válassza a **CLUSTERNAME_Hive** lehetőséget a **struktúra** alatt. Két előre konfigurált házirendnek kell megjelennie.
3. Válassza az **új szabályzat hozzáadása** lehetőséget, majd adja meg a következő értékeket:

    |Tulajdonság |Érték |
    |---|---|
    |Házirend neve|Read-hivesampletable-all|
    |Struktúra-adatbázis|alapértelmezett|
    |tábla|hivesampletable|
    |Struktúra oszlop|*|
    |Felhasználó kiválasztása|hiveuser1|
    |Engedélyek|Válassza|

    :::image type="content" source="./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png" alt-text="HDINSIGHT ESP Ranger kaptár-szabályzatok konfigurálása" border="true":::.

    > [!NOTE]  
    > Ha egy tartományi felhasználó nem töltődik be a Felhasználó kiválasztása részben, várjon néhány másodpercig, amíg a Ranger szinkronizálódik az AAD-val.

4. A házirend mentéséhez válassza a **Hozzáadás** lehetőséget.

5. Ismételje meg az utolsó két lépést egy másik házirend létrehozásához, amely a következő tulajdonságokkal rendelkezik:

    |Tulajdonság |Érték |
    |---|---|
    |Házirend neve|Read-hivesampletable-devicemake|
    |Struktúra-adatbázis|alapértelmezett|
    |tábla|hivesampletable|
    |Struktúra oszlop|ClientID, devicemake|
    |Felhasználó kiválasztása|hiveuser2|
    |Engedélyek|Válassza|

## <a name="create-hive-odbc-data-source"></a>Hive ODBC-adatforrás létrehozása

Az utasítások a [Hive ODBC-adatforrás létrehozása](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md) című részben találhatók.  

 | Tulajdonság  |Leírás |
 | --- | --- |
 | Adatforrás neve | Adjon nevet az adatforrásának |
 | Gazdagép | Adja meg a CLUSTERNAME.azurehdinsight.net. Például: sajatHDICluster.azurehdinsight.net |
 | Port | Használja a **443** számú portot. (Ez a port megváltozott a 563-ról 443-ra.) |
 | Adatbázis | Használja az **alapértelmezett értéket**. |
 | Hive Server típusa | Válassza ki a **Hive Server 2** típust |
 | Mechanizmus | Válassza ki az **Azure HDInsight szolgáltatást** |
 | HTTP elérési útja | Hagyja üresen. |
 | Felhasználónév | Írja be a következő szöveget: hiveuser1@contoso158.onmicrosoft.com. Ha az eltérő, frissítse a tartománynevet. |
 | Jelszó | Adja meg a hiveuser1 jelszavát. |

Az adatforrás mentése előtt kattintson a **Tesztelés** gombra.

## <a name="import-data-into-excel-from-hdinsight"></a>Adatok importálása Excel formátumba a HDInsight-ból

Az utolsó szakaszban két házirendet konfigurált.  A hiveuser1 nevű felhasználó az összes oszlopra, míg hiveuser2 csak két oszlopra vonatkozó kiválasztási engedéllyel rendelkezik. Ebben a szakaszban Ön megszemélyesíti a két felhasználót, hogy adatokat importáljon Excel formátumba.

1. Nyisson meg egy új vagy egy meglévő munkafüzetet Excelben.

1. Az **adatok** lapon navigáljon az **adatok beolvasása**  >  **más forrásokból** az ODBC-ből,  >   hogy elindítsa a **from ODBC** ablakot.

    :::image type="content" source="./media/apache-domain-joined-run-hive/simbahiveodbc-excel-dataconnection1.png" alt-text="Az adatkapcsolatok varázsló megnyitása" border="true":::

1. A legördülő listában válassza ki az előző szakaszban létrehozott adatforrás nevét, majd kattintson az **OK gombra**.

1. Az első használathoz egy **ODBC-illesztőprogram** párbeszédablak nyílik meg. A bal oldali menüben válassza a **Windows** lehetőséget. Ezután válassza a **Kapcsolódás** lehetőséget a **navigátor** ablak megnyitásához.

1. Várja meg, amíg megnyílik az **Adatbázis és tábla kiválasztása** párbeszédpanel. Ez eltarthat néhány másodpercig.

1. Válassza a **hivesampletable** lehetőséget, majd kattintson a **tovább** gombra.

1. Válassza a **Befejezés** gombot.

1. Az **Adatok importálása** párbeszédpanelen módosíthatja vagy megadhatja a lekérdezést. Ehhez válassza a **Tulajdonságok** lehetőséget. Ez eltarthat néhány másodpercig.

1. Válassza a **definíció** lapot. A parancs szövege:

    ```sql
    SELECT * FROM "HIVE"."default"."hivesampletable"`
    ```

   A definiált Ranger-házirendek alapján a hiveuser1 az összes oszlopra vonatkozó kiválasztási engedéllyel rendelkezik.  Így ez a lekérdezés a hiveuser1 hitelesítő adataival működik, de ez a lekérdezés nem működik a hiveuser2 hitelesítő adataival.

1. A kapcsolódási tulajdonságok párbeszédpanel bezárásához kattintson **az OK gombra** .

1. Az **adatimportálás** párbeszédpanel bezárásához kattintson **az OK gombra** .  

1. Írja be újra a hiveuser1 jelszavát, majd kattintson az **OK** gombra. Az adatok importálása az Excelbe néhány másodpercet vesz igénybe. Ha elkészült, 11 oszlopot kell látnia.

A második szabályzat (read-hivesampletable-devicemake) teszteléséhez, amelyet az utolsó szakaszban hozott létre

1. Adjon hozzá egy új munkalapot az Excelben.
2. Az adatok importálásához kövesse az utolsó eljárást.  Az egyetlen változás, hogy a hiveuser2 hitelesítő adatokat használja a hiveuser1 helyett. Ez a művelet sikertelen, mert a hiveuser2 csak két oszlop megjelenítésére jogosult. A következő hibaüzenetnek kell megjelennie:

    ```output
    [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
    ```

3. Az adatok importálásához kövesse ugyanazt az eljárást. Ez alkalommal hiveuser2 hitelesítő adatait használja, és módosítsa a kiválasztási utasítást erről:

    ```sql
    SELECT * FROM "HIVE"."default"."hivesampletable"
    ```

    erre:

    ```sql
    SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"
    ```

    Ha elkészült, két, az importált adatoszlop jelenik meg.

## <a name="next-steps"></a>Következő lépések

* A HDInsight-fürtök Enterprise Security Package-vel való konfigurálásával kapcsolatban lásd: [HDInsight-fürtök beállítása az ESP-vel](./apache-domain-joined-configure-using-azure-adds.md).
* Az ESP-vel rendelkező HDInsight-fürtök kezelésével kapcsolatban lásd: [HDInsight-fürtök kezelése az ESP-vel](apache-domain-joined-manage.md).
* A HDInsight-fürtök SSH-val való futtatásához az ESP használatával: az [SSH használata a HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
* Ha a kaptárt a méhkas JDBC használatával Apache Hive szeretné csatlakoztatni, tekintse meg a következőt: [Csatlakozás az Azure HDInsight-hez a kaptár JDBC-illesztő](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md)
* Az Excel és a Hadoop összekapcsolása a kaptár ODBC használatával: [az Excel csatlakoztatása Apache Hadoop a Microsoft kaptár ODBC-meghajtóval](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* Az Excel és a Hadoop összekapcsolása Power Query használatával: az [Excel csatlakoztatása Apache Hadoophoz a Power Query használatával](../hadoop/apache-hadoop-connect-excel-power-query.md)
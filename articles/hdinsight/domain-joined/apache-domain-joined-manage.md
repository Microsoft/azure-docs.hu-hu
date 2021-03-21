---
title: Enterprise Security Package-fürtök kezelése – Azure HDInsight
description: Ismerje meg, hogyan kezelheti az Azure HDInsight-fürtöket Enterprise Security Packageokkal.
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/04/2019
ms.openlocfilehash: b0f8ba4adfa4b08c23d3f69fa4e2b01f4580bb19
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101742211"
---
# <a name="manage-hdinsight-clusters-with-enterprise-security-package"></a>HDInsight-fürtök kezelése Enterprise Security Package

Megismerheti a HDInsight Enterprise Security Package (ESP) felhasználóit és szerepköreit, valamint az ESP-fürtök kezelését.

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>VSCode használata egy tartományhoz csatlakozó fürthöz való kapcsolódáshoz

Az Apache Ambari Managed username használatával összekapcsolhat egy normál fürtöt, és a tartomány felhasználóneve (például:) használatával is összekapcsolhatja a biztonsági Apache Hadoop fürtöt `user1@contoso.com` .

1. Nyissa meg a [Visual Studio Code](https://code.visualstudio.com/)-ot. Győződjön meg arról, hogy a [Spark & struktúra-eszközök](../hdinsight-for-vscode.md) bővítmény telepítve van.

1. Kövesse a [fürt csatolása](../hdinsight-for-vscode.md#link-a-cluster) a Visual Studio Code-hoz című témakör lépéseit.

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>IntelliJ használata egy tartományhoz csatlakozó fürthöz való kapcsolódáshoz

A Ambari Managed username használatával összekapcsolhat egy normál fürtöt, és a tartomány felhasználóneve (például:) használatával is összekapcsolhatja a biztonsági Hadoop-fürtöt `user1@contoso.com` .

1. Nyissa meg az IntelliJ IDEA-t. Győződjön meg arról, hogy minden [előfeltétel](../spark/apache-spark-intellij-tool-plugin.md#prerequisites) teljesül.

1. Kövesse a [fürt csatolása](../spark/apache-spark-intellij-tool-plugin.md#link-a-cluster) a IntelliJ című témakör lépéseit.

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>Eclipse használata egy tartományhoz csatlakozó fürthöz való kapcsolódáshoz

A Ambari Managed username használatával összekapcsolhat egy normál fürtöt, és a tartomány felhasználóneve (például:) használatával is összekapcsolhatja a biztonsági Hadoop-fürtöt `user1@contoso.com` .

1. Nyissa meg az Eclipse környezetet. Győződjön meg arról, hogy minden [előfeltétel](../spark/apache-spark-eclipse-tool-plugin.md#prerequisites) teljesül.

1. Kövesse a [fürt csatolása](../spark/apache-spark-eclipse-tool-plugin.md#link-a-cluster) az Eclipse-hez című témakör lépéseit.

## <a name="access-the-clusters-with-enterprise-security-package"></a>A fürtök elérése Enterprise Security Package

Enterprise Security Package (korábbi nevén HDInsight Premium) többfelhasználós hozzáférést biztosít a fürthöz, ahol a hitelesítés az Apache Ranger és a tárolási ACL-ek (ADLS ACL-ek) által végzett Active Directory és engedélyezéssel történik. Az engedélyezés több felhasználó számára biztosít biztonságos határokat, és lehetővé teszi, hogy csak a jogosultsággal rendelkező felhasználók férhessenek hozzá az adataihoz az engedélyezési házirendek alapján.

A biztonság és a felhasználók elkülönítése a HDInsight-fürtök esetében fontos a Enterprise Security Package. A követelmények kielégítése érdekében a fürthöz való SSH-hozzáférés Enterprise Security Package támogatott a fürt létrehozási idején kiválasztott helyi felhasználó, valamint a HRE-DS-ben (például Kerberos) elérhető felhasználók számára. Az alábbi táblázat az egyes fürtök ajánlott hozzáférési módszereit mutatja be:

|Számítási feladat|Eset|Hozzáférési módszer|
|--------|--------|-------------|
|Apache Hadoop|Struktúra – interaktív feladatok/lekérdezések    |<ul><li>[Beeline](#beeline)</li><li>[Hive-nézet](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio-eszközök](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Interaktív feladatok/lekérdezések, interaktív PySpark|<ul><li>[Beeline](#beeline)</li><li>[Zeppelin és Livy](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Hive-nézet](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio-eszközök](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Batch-forgatókönyvek – Spark beküldés, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Interaktív lekérdezés (LLAP)|Interaktív|<ul><li>[Beeline](#beeline)</li><li>[Hive-nézet](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio-eszközök](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Bármelyik|Egyéni alkalmazás telepítése|<ul><li>[Parancsfájlok műveletei](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]  
   > A Jupyter nincs telepítve/nem támogatott a Enterprise Security Packageban.

A standard API-k használata a biztonsági szempontból nyújt segítséget. A következő előnyöket is élvezheti:

- **Felügyelet** – kezelheti a kódot, és automatizálhatja a feladatokat standard API-k használatával – LIVY, HS2 stb.
- **Auditálás** – SSH-val nem lehet naplózni, hogy mely felhasználók SSH-t kívánnak a fürtön. Ez nem lehetséges, ha a feladatokat standard végpontokon keresztül készíti el, mivel azok a felhasználó kontextusában lesznek végrehajtva.

### <a name="use-beeline"></a><a name="beeline"></a>A Beeline használata

Telepítse a Beeline szolgáltatást a gépére, és kapcsolódjon a nyilvános interneten keresztül, használja a következő paramétereket:

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Ha helyileg telepítette a telepítést, és egy Azure-Virtual Network kapcsolódik, használja a következő paramétereket:

```
Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

A átjárócsomóponthoz teljes tartománynevének megkereséséhez használja a HDInsight kezelése a Ambari REST API dokumentum segítségével című témakörben található információkat.

## <a name="users-of-hdinsight-clusters-with-esp"></a>ESP-vel rendelkező HDInsight-fürtök felhasználói

A nem ESP-alapú HDInsight-fürtök két, a fürt létrehozása során létrehozott felhasználói fiókkal rendelkeznek:

- **Ambari-rendszergazda**: Ez a fiók *Hadoop felhasználóként* vagy *http-felhasználóként* is ismert. Ez a fiók használható a Ambari-ba való bejelentkezéshez `https://CLUSTERNAME.azurehdinsight.net` . Emellett a Ambari-nézeteken végzett lekérdezések futtatására, a feladatok külső eszközökön (például a PowerShell, a Templeton, a Visual Studio) keresztül történő végrehajtására, valamint a kaptár ODBC-illesztővel és BI-eszközökkel való hitelesítésére is használható (például Excel, Power BI vagy tabló).

Az ESP-vel rendelkező HDInsight-fürtök három új felhasználóval rendelkeznek a Ambari-rendszergazda mellett.

- **Ranger-rendszergazda**: Ez a fiók a helyi Apache Ranger-rendszergazdai fiók. Nem Active Directory tartományi felhasználó. Ezzel a fiókkal házirendeket állíthat be, és más felhasználók rendszergazdáit vagy delegált rendszergazdáit is elvégezheti (így a felhasználók kezelhetik a szabályzatokat). Alapértelmezés szerint a Felhasználónév a *rendszergazda* , és a jelszó megegyezik a Ambari rendszergazdai jelszavával. A jelszót a Ranger beállítások lapján lehet frissíteni.

- **Fürt rendszergazdai tartományi felhasználója**: Ez a fiók egy Active Directory tartományi felhasználó, amely Hadoop-fürtként van kijelölve, beleértve a Ambari és a Rangert is. A fürt létrehozása során meg kell adnia a felhasználó hitelesítő adatait. Ez a felhasználó a következő jogosultságokkal rendelkezik:
    - Csatlakoztassa a gépeket a tartományhoz, és helyezze őket a fürt létrehozása során megadott szervezeti egységbe.
    - Hozzon létre egyszerű szolgáltatásokat a fürt létrehozása során megadott szervezeti egységen belül.
    - Fordított DNS-bejegyzések létrehozása.

    Vegye figyelembe, hogy a többi AD-felhasználó is rendelkezik ezekkel a jogosultságokkal.

    A fürtön belül vannak olyan végpontok (például Templeton), amelyeket nem a Ranger felügyel, ezért nem biztonságos. Ezek a végpontok minden felhasználó számára le vannak zárva, kivéve a fürt rendszergazdai tartományi felhasználóját.

- **Normál**: a fürt létrehozása során több Active Directory-csoportot is megadhat. A csoportok felhasználói szinkronizálva vannak a Ranger és a Ambari között. Ezek a felhasználók tartományi felhasználók, és csak a Ranger által felügyelt végpontokhoz férnek hozzá (például Hiveserver2). Az összes RBAC szabályzat és naplózás a felhasználókra érvényes lesz.

## <a name="roles-of-hdinsight-clusters-with-esp"></a>Az ESP-vel rendelkező HDInsight-fürtök szerepkörei

A HDInsight Enterprise Security Package a következő szerepkörökkel rendelkezik:

- Fürt rendszergazdája
- Fürt operátora
- Szolgáltatás-rendszergazda
- Szolgáltatás operátora
- Fürt felhasználója

**A szerepkörök engedélyeinek megtekintéséhez**

1. Nyissa meg a Ambari-kezelő felhasználói felületét.  Lásd: [a Ambari-kezelés felhasználói felületének megnyitása](#open-the-ambari-management-ui).
2. A bal oldali menüben válassza a **szerepkörök** lehetőséget.
3. Válassza ki a kék kérdőjelet az engedélyek megtekintéséhez:

    ![ESP HDInsight-szerepkörök engedélyei](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>A Ambari-kezelés felhasználói felületének megnyitása

1. Navigáljon `https://CLUSTERNAME.azurehdinsight.net/` oda, ahol a CLUSTERNAME a fürt neve.
1. Jelentkezzen be a Ambari a fürt rendszergazdai tartományának felhasználónevével és jelszavával.
1. A jobb felső sarokban kattintson a **rendszergazda** legördülő menüre, majd válassza a **Ambari kezelése** lehetőséget.

    ![ESP HDInsight az Apache Ambari kezelése](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    A felhasználói felület a következőképpen néz ki:

    ![ESP HDInsight Apache Ambari-kezelési felhasználói felület](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>A Active Directoryról szinkronizált tartományi felhasználók listázása

1. Nyissa meg a Ambari-kezelő felhasználói felületét.  Lásd: [a Ambari-kezelés felhasználói felületének megnyitása](#open-the-ambari-management-ui).
2. A bal oldali menüben válassza a **felhasználók** lehetőséget. A Active Directoryról a HDInsight-fürtre szinkronizált összes felhasználót látnia kell.

    ![ESP-HDInsight Ambari-kezelési felhasználói felületének listájának felhasználói](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>A Active Directory szinkronizált tartományi csoportok listázása

1. Nyissa meg a Ambari-kezelő felhasználói felületét.  Lásd: [a Ambari-kezelés felhasználói felületének megnyitása](#open-the-ambari-management-ui).
2. A bal oldali menüben válassza a **csoportok** lehetőséget. A Active Directoryról a HDInsight-fürtre szinkronizált összes csoportot látnia kell.

    ![ESP HDInsight Ambari felügyeleti felhasználói felületi csoportok listája](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>A kaptár-nézetek engedélyeinek konfigurálása

1. Nyissa meg a Ambari-kezelő felhasználói felületét.  Lásd: [a Ambari-kezelés felhasználói felületének megnyitása](#open-the-ambari-management-ui).
2. A bal oldali menüben válassza a **nézetek** elemet.
3. Válassza ki a **kaptárt** a részletek megjelenítéséhez.

    ![ESP HDInsight Ambari-kezelő felhasználói felületi struktúrájának nézetei](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)

4. A kaptár nézeteinek konfigurálásához válassza a **struktúra nézet** hivatkozását.
5. Görgessen le az **engedélyek** szakaszhoz.

    ![ESP HDInsight Ambari-kezelő felhasználói felületi struktúrájának nézeteire vonatkozó engedélyek konfigurálása](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)

6. Válassza a **felhasználó hozzáadása** vagy a **Csoport hozzáadása** lehetőséget, majd adja meg azokat a felhasználókat vagy csoportokat, akik használhatják a kaptár nézeteket.

## <a name="configure-users-for-the-roles"></a>Felhasználók konfigurálása a szerepkörökhöz

 A szerepkörök és a hozzájuk tartozó engedélyek listájának megtekintéséhez lásd: HDInsight-fürtök, ESP-vel.

1. Nyissa meg a Ambari-kezelő felhasználói felületét.  Lásd: [a Ambari-kezelés felhasználói felületének megnyitása](#open-the-ambari-management-ui).
2. A bal oldali menüben válassza a **szerepkörök** lehetőséget.
3. A felhasználók és csoportok különböző szerepkörökhöz való hozzárendeléséhez válassza a **felhasználó hozzáadása** vagy a **Csoport hozzáadása** lehetőséget.

## <a name="next-steps"></a>Következő lépések

- A HDInsight-fürtök Enterprise Security Package-vel való konfigurálásával kapcsolatban lásd: [HDInsight-fürtök beállítása az ESP-vel](./apache-domain-joined-configure-using-azure-adds.md).
- A kaptár-házirendek konfigurálásához és a kaptár-lekérdezések futtatásához lásd: [Apache Hive házirendek konfigurálása HDInsight-fürtökhöz ESP-vel](apache-domain-joined-run-hive.md).
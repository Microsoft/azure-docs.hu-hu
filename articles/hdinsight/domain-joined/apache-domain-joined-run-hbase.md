---
title: Apache HBase & Enterprise Security Package – Azure HDInsight
description: Oktatóanyag – megtudhatja, hogyan konfigurálhatja az Apache Ranger-szabályzatokat az Azure HDInsight-ben való HBase az Enterprise Security Package használatával.
ms.service: hdinsight
ms.topic: tutorial
ms.date: 09/04/2019
ms.openlocfilehash: a18e0b252facb4f00d9ba5c9b6bfe9fe6aefe1ef
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866998"
---
# <a name="tutorial-configure-apache-hbase-policies-in-hdinsight-with-enterprise-security-package"></a>Oktatóanyag: Apache HBase-házirendek konfigurálása a HDInsight-ben Enterprise Security Package

Ismerje meg, hogyan konfigurálhatja az Apache Ranger-házirendeket Enterprise Security Package (ESP) Apache HBase-fürtökhöz. Az ESP-fürtök egy tartományhoz csatlakoznak, lehetővé téve a felhasználók számára a tartományi hitelesítő adatokkal való hitelesítést. Ebben az oktatóanyagban két Ranger-szabályzatot hoz létre, hogy korlátozza a hozzáférést egy HBase-táblában lévő különböző oszlopokhoz.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Tartományi felhasználók létrehozása
> * Ranger-házirendek létrehozása
> * Táblák létrehozása HBase-fürtben
> * Ranger-házirendek tesztelése

## <a name="before-you-begin"></a>Előkészületek

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

* Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

* Hozzon létre egy [HDInsight HBase-fürtöt Enterprise Security Package](apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-apache-ranger-admin-ui"></a>Csatlakozás az Apache Ranger felügyeleti felhasználói felületéhez

1. Egy böngészőből lépjen be a Ranger rendszergazdai felhasználói felületére a következő címen: `https://<ClusterName>.azurehdinsight.net/Ranger/`. Ne felejtse el megváltoztatni a `<ClusterName>` HBase-fürt nevét.

    > [!NOTE]  
    > A Ranger hitelesítő adatai nem ugyanazok, mint a Hadoop-fürthöz használt hitelesítő adatok. Ha meg szeretné akadályozni, hogy a böngészők gyorsítótárazott Hadoop hitelesítő adatokat használjanak, egy új InPrivate-böngészőablakból csatlakozzon a Ranger rendszergazdai felhasználói felületéhez.

2. Jelentkezzen be az Azure Active Directory (AD) rendszergazdai hitelesítő adataival. Az Azure AD rendszergazdai hitelesítő adatok nem azonosak a HDInsight-fürt hitelesítő adataival vagy a Linux rendszerű HDInsight-csomópont SSH hitelesítő adataival.

## <a name="create-domain-users"></a>Tartományi felhasználók létrehozása

Látogasson el a [HDInsight-fürt létrehozásához Enterprise Security Package](./apache-domain-joined-configure-using-azure-adds.md)segítségével, és ismerkedjen meg a **sales_user1** és a **marketing_user1** tartományi felhasználók létrehozásával. Az éles forgatókönyvekben a tartományi felhasználókat az Active Directory-bérlő adja meg.

## <a name="create-hbase-tables-and-import-sample-data"></a>HBase táblák létrehozása és mintaadatok importálása

Az SSH-val HBase-fürtökhöz csatlakozhat, majd az [Apache HBase-rendszerhéj](https://hbase.apache.org/0.94/book/shell.html) használatával HBase-táblákat hozhat létre, és adatbeszúrási és Adatlekérdezési adatként is létrehozhat. További információ: az [SSH használata a HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="to-use-the-hbase-shell"></a>A HBase rendszerhéj használata

1. Az SSH-ból futtassa az alábbi HBase-parancsot:
   
    ```bash
    hbase shell
    ```

2. Hozzon létre egy HBase táblázatot `Customers` kétoszlopos családokkal: `Name` és `Contact` .

    ```hbaseshell   
    create 'Customers', 'Name', 'Contact'
    list
    ```
3. Adatok beszúrása:
    
    ```hbaseshell   
    put 'Customers','1001','Name:First','Alice'
    put 'Customers','1001','Name:Last','Johnson'
    put 'Customers','1001','Contact:Phone','333-333-3333'
    put 'Customers','1001','Contact:Address','313 133rd Place'
    put 'Customers','1001','Contact:City','Redmond'
    put 'Customers','1001','Contact:State','WA'
    put 'Customers','1001','Contact:ZipCode','98052'
    put 'Customers','1002','Name:First','Robert'
    put 'Customers','1002','Name:Last','Stevens'
    put 'Customers','1002','Contact:Phone','777-777-7777'
    put 'Customers','1002','Contact:Address','717 177th Ave'
    put 'Customers','1002','Contact:City','Bellevue'
    put 'Customers','1002','Contact:State','WA'
    put 'Customers','1002','Contact:ZipCode','98008'
    ```
4. A tábla tartalmának megtekintése:
    
    ```hbaseshell
    scan 'Customers'
    ```

    :::image type="content" source="./media/apache-domain-joined-run-hbase/hbase-shell-scan-table.png" alt-text="HDInsight Hadoop HBase-rendszerhéj kimenete" border="true":::

## <a name="create-ranger-policies"></a>Ranger-házirendek létrehozása

Hozzon létre egy Ranger-szabályzatot **sales_user1** és **marketing_user1hoz**.

1. Nyissa meg a **Ranger rendszergazdai felhasználói felületét**. Kattintson **\<ClusterName> _Hbase** a **hbase** alatt.

   :::image type="content" source="./media/apache-domain-joined-run-hbase/apache-ranger-admin-login.png" alt-text="HDInsight Apache Ranger felügyeleti felhasználói felülete" border="true":::

2. A **szabályzatok listája** képernyőn megjelenik a fürthöz létrehozott összes Ranger-szabályzat. Előfordulhat, hogy a felsorolásban megjelenik egy előre beállított házirend. Kattintson az **új szabályzat hozzáadása** lehetőségre.

    :::image type="content" source="./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policies-list.png" alt-text="Apache Ranger HBase szabályzatok listája" border="true":::

3. A **házirend létrehozása** képernyőn adja meg a következő értékeket:

   |**Beállítás**  |**Ajánlott érték**  |
   |---------|---------|
   |Házirend neve  |  sales_customers_name_contact   |
   |HBase táblázat   |  Ügyfelek |
   |HBase oszlop – család   |  Név, kapcsolattartó |
   |HBase oszlop   |  * |
   |Csoport kiválasztása  | |
   |Felhasználó kiválasztása  | sales_user1 |
   |Engedélyek  | Olvasás |

   A témakör nevében a következő helyettesítő karakterek használhatók:

   * `*` nulla vagy több karakterből álló előfordulást jelez.
   * `?` egyetlen karaktert jelöl.

   :::image type="content" source="./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-sales.png" alt-text="Apache Ranger-szabályzat – értékesítések létrehozása" border="true":::

   >[!NOTE]
   >Várjon néhány pillanatot, míg a Ranger szinkronizálódik az Azure AD-vel, ha a rendszer nem tölt be automatikusan egy tartományi felhasználót a **Select User** (Felhasználó kiválasztása) beállításhoz.

4. A házirend mentéséhez kattintson a **Hozzáadás** gombra.

5. Kattintson az **Add New Policy** (Új házirend hozzáadása) lehetőségre, majd adja meg a következő értékeket:

   |**Beállítás**  |**Ajánlott érték**  |
   |---------|---------|
   |Házirend neve  |  marketing_customers_contact   |
   |HBase táblázat   |  Ügyfelek |
   |HBase oszlop – család   |  Kapcsolattartó |
   |HBase oszlop   |  * |
   |Csoport kiválasztása  | |
   |Felhasználó kiválasztása  | marketing_user1 |
   |Engedélyek  | Olvasás |

   :::image type="content" source="./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-marketing.png" alt-text="Apache Ranger-szabályzat létrehozása marketing" border="true":::  

6. A házirend mentéséhez kattintson a **Hozzáadás** gombra.

## <a name="test-the-ranger-policies"></a>Ranger-házirendek tesztelése

A konfigurált Ranger-szabályzatok alapján a **sales_user1** megtekintheti az oszlopok összes adatait az `Name` és az `Contact` oszlop családokban is. A **marketing_user1** csak az oszlop családjában lévő adatmegjelenítést tudja megtekinteni `Contact` .

### <a name="access-data-as-sales_user1"></a>Az adathozzáférés sales_user1

1. Nyisson meg egy új SSH-kapcsolatot a fürthöz. A fürtbe való bejelentkezéshez használja a következő parancsot:

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. A kinit parancsot parancs használatával váltson a kívánt felhasználó környezetére.

   ```bash
   kinit sales_user1
   ```

2. Nyissa meg a HBase-rendszerhéjt, és vizsgálja meg a táblázatot `Customers` .

   ```hbaseshell
   hbase shell
   scan `Customers`
   ```

3. Figyelje meg, hogy az értékesítési felhasználó megtekintheti a tábla összes oszlopát `Customers` , beleértve az oszlop két oszlopát, valamint az `Name` `Contact` oszlop-család öt oszlopát.

    ```hbaseshell
    ROW                                COLUMN+CELL
     1001                              column=Contact:Address, timestamp=1548894873820, value=313 133rd Place
     1001                              column=Contact:City, timestamp=1548895061523, value=Redmond
     1001                              column=Contact:Phone, timestamp=1548894871759, value=333-333-3333
     1001                              column=Contact:State, timestamp=1548895061613, value=WA
     1001                              column=Contact:ZipCode, timestamp=1548895063111, value=98052
     1001                              column=Name:First, timestamp=1548894871561, value=Alice
     1001                              column=Name:Last, timestamp=1548894871707, value=Johnson
     1002                              column=Contact:Address, timestamp=1548894899174, value=717 177th Ave
     1002                              column=Contact:City, timestamp=1548895103129, value=Bellevue
     1002                              column=Contact:Phone, timestamp=1548894897524, value=777-777-7777
     1002                              column=Contact:State, timestamp=1548895103231, value=WA
     1002                              column=Contact:ZipCode, timestamp=1548895104804, value=98008
     1002                              column=Name:First, timestamp=1548894897419, value=Robert
     1002                              column=Name:Last, timestamp=1548894897487, value=Stevens
    2 row(s) in 0.1000 seconds
    ```

### <a name="access-data-as-marketing_user1"></a>Az adathozzáférés marketing_user1

1. Nyisson meg egy új SSH-kapcsolatot a fürthöz. A következő parancs használatával jelentkezzen be **marketing_user1ként**:

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. A kinit parancsot parancs használata a kívánt felhasználó kontextusára való váltáshoz

   ```bash
   kinit marketing_user1
   ```

1. Nyissa meg a HBase-rendszerhéjt, és vizsgálja meg a táblázatot `Customers` :

    ```hbaseshell
    hbase shell
    scan `Customers`
    ```

1. Figyelje meg, hogy a marketing felhasználó csak az oszlop öt oszlopát tekintheti meg `Contact` .

    ```hbaseshell
    ROW                                COLUMN+CELL
     1001                              column=Contact:Address, timestamp=1548894873820, value=313 133rd Place
     1001                              column=Contact:City, timestamp=1548895061523, value=Redmond
     1001                              column=Contact:Phone, timestamp=1548894871759, value=333-333-3333
     1001                              column=Contact:State, timestamp=1548895061613, value=WA
     1001                              column=Contact:ZipCode, timestamp=1548895063111, value=98052
     1002                              column=Contact:Address, timestamp=1548894899174, value=717 177th Ave
     1002                              column=Contact:City, timestamp=1548895103129, value=Bellevue
     1002                              column=Contact:Phone, timestamp=1548894897524, value=777-777-7777
     1002                              column=Contact:State, timestamp=1548895103231, value=WA
     1002                              column=Contact:ZipCode, timestamp=1548895104804, value=98008
    2 row(s) in 0.0730 seconds
    ```

1. A hozzáférési események naplózása a Ranger felhasználói felületről tekinthető meg.

   :::image type="content" source="./media/apache-domain-joined-run-hbase/apache-ranger-admin-audit.png" alt-text="HDInsight Ranger felhasználói felületi szabályzatának naplózása" border="true":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje a létrehozott HBase-fürtöt a következő lépésekkel:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A felső **keresőmezőbe** írja be a **HDInsight** kifejezést. 
1. Válassza ki a **HDInsight-fürtök** elemet a **szolgáltatások** területen.
1. A megjelenő HDInsight-fürtök listájában kattintson a **...** elemre az oktatóanyaghoz létrehozott fürt mellett. 
1. Kattintson a **Törlés** gombra. Kattintson a **Yes** (Igen) gombra.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az Apache HBase első lépései](../hbase/apache-hbase-tutorial-get-started-linux.md)
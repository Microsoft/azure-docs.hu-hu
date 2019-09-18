---
title: Oktatóanyag – az Apache HBase használata az Azure HDInsight
description: Kövesse ezt az Apache HBase-oktatóanyagot a Hadoop on HDInsight használatának megkezdéséhez. Táblákat hozhat létre a HBase rendszehéjból, és lekérdezheti azokat a Hive eszközzel.
keywords: hbasecommand,hbase példa
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: tutorial
ms.date: 06/25/2019
ms.author: hrasheed
ms.openlocfilehash: e43d2d64535085a9b22d2febc761fc7026498ba8
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077140"
---
# <a name="tutorial-use-apache-hbase-in-azure-hdinsight"></a>Oktatóanyag: Az Apache HBase használata az Azure HDInsightban

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy Apache HBase-fürtöt az Azure HDInsight, hogyan hozhat létre HBase-táblákat és lekérdezési táblákat Apache Hive használatával.  Az általános HBase kapcsolatos információkért lásd: a [HDInsight HBase áttekintése](./apache-hbase-overview.md).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Apache HBase-fürt létrehozása
> * HBase táblák létrehozása és az adatbeszúrás
> * Az Apache HBase lekérdezése Apache Hive használatával
> * HBase REST API-k használata Curl használatával
> * A fürt állapotának ellenőrzése

## <a name="prerequisites"></a>Előfeltételek

* Egy SSH-ügyfél. További információ: [Kapcsolódás HDInsight (Apache Hadoop) SSH használatával](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Bash. A cikkben szereplő példák a Windows 10-es bash rendszerhéját használják a curl-parancsokhoz. A telepítési lépésekért lásd: [Windows-alrendszer Linux-telepítési útmutató Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) rendszerhez.  Más [UNIX-rendszerhéj](https://www.gnu.org/software/bash/) is működik.  A curl-példák néhány kisebb módosítással is működhetnek Windows-parancssorban.  Azt is megteheti, hogy a [meghívott](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-restmethod)Windows PowerShell-parancsmagot használja. RestMethod.

## <a name="create-apache-hbase-cluster"></a>Apache HBase-fürt létrehozása

Az alábbi eljárás egy Azure Resource Manager sablonnal hoz létre egy HBase-fürtöt és a függő alapértelmezett Azure Storage-fiókot. Az eljárásban és egyéb fürtlétrehozási módszerekben használt paraméterek megértéséhez lásd: [Create Linux-based Hadoop clusters in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) (Linux-alapú Hadoop-fürtök létrehozása a HDInsightban).

1. Válassza ki az alábbi képet a sablon megnyitásához a Azure Portal. A sablon az [Azure Gyorsindítás sablonjaiban](https://azure.microsoft.com/resources/templates/)található.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Az **Egyéni üzembe helyezés** panelen adja meg a következő értékeket:

    |Tulajdonság |Leírás |
    |---|---|
    |Subscription|Válassza ki a fürt létrehozásához használt Azure-előfizetést.|
    |Resource group|Hozzon létre egy Azure Resource Management-csoportot, vagy használjon egy meglévőt.|
    |Location|Határozza meg az erőforráscsoport helyét. |
    |clusterName|Adja meg a HBase-fürt nevét.|
    |Fürt bejelentkezési neve és jelszava|Az alapértelmezett bejelentkezési név az **admin**.|
    |SSH-Felhasználónév és-jelszó|Az alapértelmezett felhasználónév az **sshuser**.|

    Más paraméterek opcionálisak.  

    Minden egyes fürt az Azure Storage-fióktól függ. A fürtök törlése után az adatok megmaradnak a tárfiókban. A fürt alapértelmezett tárfiókneve a fürt neve a „store” kifejezéssel kiegészítve. A név szoftveresen kötött a sablonváltozók szakaszban.

3. Jelölje be **az Elfogadom a fenti feltételeket és kikötéseket**, majd válassza a **vásárlás**lehetőséget. Egy fürt létrehozása nagyjából 20 percet vesz igénybe.

A HBase-fürtök törlése után egy másik HBase-fürtöt hozhat létre ugyanazon alapértelmezett blobtárolóval. Az új fürt felveszi az eredeti fürtben létrehozott HBase-táblákat. Az inkonzisztenciák elkerülése érdekében javasoljuk, hogy a fürt törlése előtt tiltsa le a HBase-táblákat.

## <a name="create-tables-and-insert-data"></a>Táblák létrehozása és adatok beszúrása

Az SSH-val HBase-fürtökhöz csatlakozhat, majd az [Apache HBase-rendszerhéj](https://hbase.apache.org/0.94/book/shell.html) használatával HBase-táblákat hozhat létre, és adatbeszúrási és Adatlekérdezési adatként is létrehozhat.

A legtöbbek számára az adatok táblázatos formátumban jelennek meg:

![HDInsight Apache HBase táblázatos adatokat](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png)

A HBase (a [Cloud BigTable](https://cloud.google.com/bigtable/)implementációja) ugyanazokat az adatmennyiségeket keresi:

![HDInsight Apache HBase BigTable-adatbázis](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png)

**A Hbase-rendszerhéj használata**

1. Használja `ssh` a parancsot a HBase-fürthöz való kapcsolódáshoz. Szerkessze az alábbi parancsot úgy `CLUSTERNAME` , hogy lecseréli a fürt nevét, majd beírja a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A `hbase shell` parancs használatával indítsa el a HBase interaktív rendszerhéját. Adja meg az alábbi parancsot az SSH-kapcsolatban:

    ```bash
    hbase shell
    ```

1. A `create` parancs használatával hozzon létre egy kétoszlopos családokat tartalmazó HBase-táblázatot. A tábla és az oszlop neve megkülönbözteti a kis-és nagybetűket. Írja be a következő parancsot:

    ```hbaseshell
    create 'Contacts', 'Personal', 'Office'
    ```

1. Használja `list` a parancsot a HBase összes táblájának listázásához. Írja be a következő parancsot:

    ```hbase
    list
    ```

1. A `put` parancs használatával szúrhat be értékeket egy adott oszlop megadott sorában egy adott táblába. Adja meg a következő parancsokat:

    ```hbaseshell
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

1. A `scan` tábla adatvizsgálatához és visszaküldéséhez használja a `Contacts` parancsot. Írja be a következő parancsot:

    ```hbase
    scan 'Contacts'
    ```

    ![HDInsight Apache Hadoop HBase-rendszerhéj](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png)

1. Egy `get` sor tartalmának lekéréséhez használja a parancsot. Írja be a következő parancsot:

    ```hbaseshell
    get 'Contacts', '1000'
    ```

    A `scan` parancs használatával hasonló eredmények jelennek meg, mivel csak egy sor van.

    A HBase táblázatos sémával kapcsolatos további információkért lásd: [Bevezetés az Apache HBase Schema design](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf)használatába. További HBase-parancsokért lásd az [Apache HBase-referenciát](https://hbase.apache.org/book.html#quickstart)ismertető témakört.

1. A `exit` HBase interaktív rendszerhéj leállításához használja a parancsot. Írja be a következő parancsot:

    ```hbaseshell
    exit
    ```

**Adatok kötegelt betöltése a névjegyek HBase-táblába**

A HBase több módszert tartalmaz az adatok táblába töltéséhez.  További információ: [Bulk loading](https://hbase.apache.org/book.html#arch.bulk.load) (Kötegelt betöltés).

Egy minta adatfájl található egy nyilvános blob-tárolóban `wasb://hbasecontacts\@hditutorialdata.blob.core.windows.net/contacts.txt`.  Az adatfájl tartalma a következő:

    8396    Calvin Raji      230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu         646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie         508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson     674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile     592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee        870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes       599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander  670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive

Igény szerint létrehozhat egy szövegfájlt, és feltöltheti a fájlt a saját tárfiókjába. Az utasításokért lásd: [adatok feltöltése Apache Hadoop feladatokhoz a HDInsight-ben](../hdinsight-upload-data.md).

Ez az eljárás az `Contacts` utolsó eljárás során létrehozott HBase táblát használja.

1. Az Open SSH-kapcsolatban futtassa a következő parancsot az adatfájl adatfájlt storefiles való átalakításához, és tárolja a által `Dimporttsv.bulk.output`megadott relatív elérési utat.

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. Futtassa az alábbi parancsot az adatok `/example/data/storeDataFileOutput` a HBase táblába való feltöltéséhez:

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. Megnyithatja a HBase-rendszerhéjt, és `scan` a parancs használatával listázhatja a tábla tartalmát.

## <a name="use-apache-hive-to-query-apache-hbase"></a>Az Apache HBase lekérdezése Apache Hive használatával

A HBase-táblákban lévő adatlekérdezéseket [Apache Hive](https://hive.apache.org/)használatával lehet lekérdezni. Ebben a szakaszban egy, a HBase-táblára leképezést biztosító Hive-táblát hoz létre, amellyel lekérdezheti a HBase-táblában lévő adatokat.

1. Az Open SSH-kapcsolatban a következő paranccsal indíthatja el a Beeline-t:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    A Beeline-nal kapcsolatos további információkért lásd [a Hive és a Hadoop együttes, a Beeline-nal történő használatát a HDInsightban](../hadoop/apache-hadoop-use-hive-beeline.md) ismertető cikket.

1. Futtassa a következő [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) -szkriptet egy struktúra-tábla létrehozásához, amely leképezi a HBase táblát. A jelen utasítás futtatása előtt győződjön meg arról, hogy létrehozta a cikkben korábban hivatkozott minta táblát a HBase-rendszerhéj használatával.

    ```hiveql
    CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
    STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
    TBLPROPERTIES ('hbase.table.name' = 'Contacts');
    ```

1. Futtassa a következő HiveQL-parancsfájlt a HBase-tábla adatainak lekérdezéséhez:

    ```hiveql
    SELECT count(rowkey) AS rk_count FROM hbasecontacts;
    ```

1. A Beeline kilépéséhez `!exit`használja a következőt:.

1. Az SSH-kapcsolatok kilépéséhez `exit`használja a következőt:.

## <a name="use-hbase-rest-apis-using-curl"></a>HBase REST API-k használata Curl használatával

A REST API védelméről [alapszintű hitelesítés](https://en.wikipedia.org/wiki/Basic_access_authentication) gondoskodik. Mindig biztonságos HTTP-n (HTTPS-en) keresztül kell kéréseket végeznie, hogy a hitelesítő adatait biztonságos módon küldje el a kiszolgálónak.

1. A könnyű használat érdekében kezdeményezzen környezeti változót. Szerkessze az alábbi parancsokat úgy `MYPASSWORD` , hogy lecseréli a fürt bejelentkezési jelszavát. Cserélje `MYCLUSTERNAME` le a nevet a HBase-fürt nevére. Ezután adja meg a parancsokat.

    ```bash
    export password='MYPASSWORD'
    export clustername=MYCLUSTERNAME
    ```

1. Használja az alábbi parancsot a meglévő HBase-táblák listázásához:

    ```bash
    curl -u admin:$password \
    -G https://$clustername.azurehdinsight.net/hbaserest/
    ```

1. Használja az alábbi parancsot egy új, kétoszlopos családokkal rendelkező HBase-tábla létrehozásához:

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/schema" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
    -v
    ```

    A séma a JSon formátumban van megadva.
1. Használja az alábbi parancsot néhány adat beviteléhez:

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"Row\":[{\"key\":\"MTAwMA==\",\"Cell\": [{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}]}]}" \
    -v
    ```

    A -d kapcsolóban megadott értékeket a base64 használatával kell kódolnia. A példában:

   * MTAwMA==: 1000
   * UGVyc29uYWw6TmFtZQ==: Személyes: név
   * Sm9obiBEb2xl: John Dole

     A [false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) lehetővé teszi több (kötegelt) érték beszúrását.

1. Használja az alábbi parancsot egy sor lekéréséhez:

    ```bash
    curl -u admin:$password \
    GET "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/1000" \
    -H "Accept: application/json" \
    -v
    ```

További információ a HBase REST-ről: [Apache HBase Reference Guide](https://hbase.apache.org/book.html#_rest) (Apache HBase referencia-útmutató).

> [!NOTE]  
> A HBase nem támogatja a Thriftet a HDInsightban.
>
> Amikor a Curl vagy más REST kommunikációt használ a WebHCattel, hitelesítenie kell a kéréseket a HDInsight fürt rendszergazdája felhasználónevének és jelszavának megadásával. A fürtnevet a kérések a kiszolgálóhoz küldéséhez használt egységes erőforrás-azonosító (URI) részeként is használnia kell.
> 
>   
>        curl -u <UserName>:<Password> \
>        -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status
>   
>    A következőhöz hasonló választ kell kapnia:
>   
>        {"status":"ok","version":"v1"}

## <a name="check-cluster-status"></a>A fürt állapotának ellenőrzése

A HBase a HDInsightban a fürtök megfigyelésére szolgáló webes felhasználói felülettel kapható. A webes felhasználói felülettel a régiók statisztikáit vagy információit kérheti le.

**A HBase mesterfelületének elérése**

1. Jelentkezzen be a Ambari webes felhasználói `https://CLUSTERNAME.azurehdinsight.net` felületére, ahol `CLUSTERNAME` a a HBase-fürt neve.

1. A bal oldali menüben válassza a **HBase** lehetőséget.

1. Válassza a **gyors hivatkozások** lehetőséget az oldal tetején, mutasson az aktív Zookeeper csomópontra, majd válassza ki **HBase Master felhasználói felületet**.  A felület egy új böngészőlapon nyílik meg:

   ![HDInsight Apache HBase HMaster felhasználói felülete](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

   A HBase-mesterfelület az alábbi részeket tartalmazza:

   - régiós kiszolgálók
   - biztonsági mentési főkiszolgálók
   - táblák
   - feladatok
   - szoftverattribútumok

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az inkonzisztenciák elkerülése érdekében javasoljuk, hogy a fürt törlése előtt tiltsa le a HBase-táblákat. Használhatja a HBase parancsot `disable 'Contacts'`. Ha nem folytatja az alkalmazás használatát, törölje a létrehozott HBase-fürtöt a következő lépésekkel:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. A felső **keresőmezőbe** írja be a **HDInsight**kifejezést.
1. Válassza ki a **HDInsight-fürtök** elemet a **szolgáltatások**területen.
1. A megjelenő HDInsight-fürtök listájában kattintson a **...** elemre az oktatóanyaghoz létrehozott fürt mellett.
1. Kattintson a **Törlés** gombra. Kattintson a **Yes** (Igen) gombra.

## <a name="next-steps"></a>További lépések

Ebből az oktatóanyagból megtudhatta, hogyan hozhat létre egy Apache HBase-fürtöt, és hogyan hozhat létre táblákat, és hogyan tekintheti meg a táblák adatait a HBase-rendszerhéjból. Azt is megtanulta, hogyan használhat Hive-lekérdezést a HBase-táblákban lévő adatokon, és hogyan használhatja a HBase C# REST API-kat egy HBase-tábla létrehozásához és adatok lekérdezéséhez a táblából. További tudnivalókért lásd:

> [!div class="nextstepaction"]
> [A HDInsight HBase áttekintése](./apache-hbase-overview.md)
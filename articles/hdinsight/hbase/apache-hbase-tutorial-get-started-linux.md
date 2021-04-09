---
title: Oktatóanyag – az Apache HBase használata az Azure HDInsight
description: Kövesse ezt az Apache HBase-oktatóanyagot a Hadoop on HDInsight használatának megkezdéséhez. Táblákat hozhat létre a HBase rendszehéjból, és lekérdezheti azokat a Hive eszközzel.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/22/2021
ms.openlocfilehash: 50216b83475e92cbabb5ed78c3836264ceff4600
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866607"
---
# <a name="tutorial-use-apache-hbase-in-azure-hdinsight"></a>Oktatóanyag: az Apache HBase használata az Azure HDInsight

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy Apache HBase-fürtöt az Azure HDInsight, hogyan hozhat létre HBase-táblákat és lekérdezési táblákat Apache Hive használatával.  A HBase-re vonatkozó általános információért lásd: [HDInsight HBase overview](./apache-hbase-overview.md) (A HDInsight HBase áttekintése).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Apache HBase-fürt létrehozása
> * HBase táblák létrehozása és az adatbeszúrás
> * Az Apache HBase lekérdezése Apache Hive használatával
> * HBase REST API-k használata Curl használatával
> * A fürt állapotának ellenőrzése

## <a name="prerequisites"></a>Előfeltételek

* Egy SSH-ügyfél. További információért lásd: [Csatlakozás a HDInsighthoz (Apache Hadoop) SSH-val](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Bash. A cikkben szereplő példák a Windows 10-es bash rendszerhéját használják a curl-parancsokhoz. A telepítési lépésekért lásd: [Windows-alrendszer Linux-telepítési útmutató Windows 10](/windows/wsl/install-win10) rendszerhez.  Más [UNIX-rendszerhéj](https://www.gnu.org/software/bash/) is működik.  A curl-példák néhány kisebb módosítással is működhetnek Windows-parancssorban.  Vagy használhatja a [Meghívási-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod)Windows PowerShell-parancsmagot is.

## <a name="create-apache-hbase-cluster"></a>Apache HBase-fürt létrehozása

Az alábbi eljárás egy Azure Resource Manager sablont használ egy HBase-fürt létrehozásához. A sablon emellett létrehozza a függő alapértelmezett Azure Storage-fiókot is. Az eljárásban és egyéb fürtlétrehozási módszerekben használt paraméterek megértéséhez lásd: [Create Linux-based Hadoop clusters in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) (Linux-alapú Hadoop-fürtök létrehozása a HDInsightban).

1. Válassza ki az alábbi képet a sablon megnyitásához a Azure Portal. A sablon az [Azure Gyorsindítás sablonjaiban](https://azure.microsoft.com/resources/templates/)található.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Az **Egyéni telepítés** párbeszédpanelen adja meg a következő értékeket:

    |Tulajdonság |Leírás |
    |---|---|
    |Előfizetés|Válassza ki a fürt létrehozásához használt Azure-előfizetést.|
    |Erőforráscsoport|Hozzon létre egy Azure Resource Management-csoportot, vagy használjon egy meglévőt.|
    |Hely|Határozza meg az erőforráscsoport helyét. |
    |ClusterName|Adja meg a HBase-fürt nevét.|
    |A fürt bejelentkezési neve és jelszava|Az alapértelmezett bejelentkezési név az **admin**.|
    |SSH-felhasználónév és jelszó|Az alapértelmezett felhasználónév az **sshuser**.|

    Más paraméterek opcionálisak.  

    Minden egyes fürt az Azure Storage-fióktól függ. A fürt törlése után az adattárolási fiókban maradnak. A fürt alapértelmezett tárfiókneve a fürt neve a „store” kifejezéssel kiegészítve. A sablon változói szakaszban található hardcoded.

3. Jelölje be **az Elfogadom a fenti feltételeket és kikötéseket**, majd válassza a **vásárlás** lehetőséget. Egy fürt létrehozása nagyjából 20 percet vesz igénybe.

A HBase-fürtök törlése után egy másik HBase-fürtöt hozhat létre ugyanazon alapértelmezett blobtárolóval. Az új fürt felveszi az eredeti fürtben létrehozott HBase-táblákat. Az inkonzisztenciák elkerülése érdekében javasoljuk, hogy a fürt törlése előtt tiltsa le a HBase-táblákat.

## <a name="create-tables-and-insert-data"></a>Táblák létrehozása és adatok beszúrása

Az SSH-val HBase-fürtökhöz csatlakozhat, majd az [Apache HBase-rendszerhéj](https://hbase.apache.org/0.94/book/shell.html) használatával HBase-táblákat hozhat létre, és adatbeszúrási és Adatlekérdezési adatként is létrehozhat.

A legtöbbek számára az adatok táblázatos formátumban jelennek meg:

:::image type="content" source="./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png" alt-text="HDInsight Apache HBase táblázatos adatokat" border="false":::

A HBase (a [Cloud BigTable](https://cloud.google.com/bigtable/)implementációja) ugyanazokat az adatmennyiségeket keresi:

:::image type="content" source="./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png" alt-text="HDInsight Apache HBase BigTable-adatbázis" border="false":::

**A Hbase-rendszerhéj használata**

1. Használja a `ssh` parancsot a HBase-fürthöz való kapcsolódáshoz. Szerkessze az alábbi parancsot úgy, hogy lecseréli a `CLUSTERNAME` fürt nevét, majd beírja a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. `hbase shell`A parancs használatával indítsa el a HBase interaktív rendszerhéját. Adja meg az alábbi parancsot az SSH-kapcsolatban:

    ```bash
    hbase shell
    ```

1. A `create` parancs használatával hozzon létre egy kétoszlopos családokat tartalmazó HBase-táblázatot. A tábla és az oszlop neve megkülönbözteti a kis-és nagybetűket. Írja be a következő parancsot:

    ```hbaseshell
    create 'Contacts', 'Personal', 'Office'
    ```

1. Használja a `list` parancsot a HBase összes táblájának listázásához. Írja be a következő parancsot:

    ```hbase
    list
    ```

1. A `put` parancs használatával szúrhat be értékeket egy adott oszlop megadott sorában egy adott táblába. Írja be a következő parancsokat:

    ```hbaseshell
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

1. `scan`A tábla adatvizsgálatához és visszaküldéséhez használja a parancsot `Contacts` . Írja be a következő parancsot:

    ```hbase
    scan 'Contacts'
    ```

    :::image type="content" source="./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png" alt-text="HDInsight Apache Hadoop HBase-rendszerhéj" border="false":::

1. `get`Egy sor tartalmának lekéréséhez használja a parancsot. Írja be a következő parancsot:

    ```hbaseshell
    get 'Contacts', '1000'
    ```

    A parancs használatával hasonló eredmények jelennek `scan` meg, mert csak egy sor van.

    A HBase táblázatos sémával kapcsolatos további információkért lásd: [Bevezetés az Apache HBase Schema design](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf)használatába. További Hbase-parancsokért lásd: [Apache HBase reference guide](https://hbase.apache.org/book.html#quickstart) (Apache HBase referencia-útmutató).

1. `exit`A HBase interaktív rendszerhéj leállításához használja a parancsot. Írja be a következő parancsot:

    ```hbaseshell
    exit
    ```

**Adatok kötegelt betöltése a névjegyek HBase-táblába**

A HBase több módszert tartalmaz az adatok táblába töltéséhez.  További információ: [Bulk loading](https://hbase.apache.org/book.html#arch.bulk.load) (Kötegelt betöltés).

Egy minta adatfájl található a következő nyilvános blobtárolóban található: `wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt`.  Az adatfájl tartalma a következő:

`8396    Calvin Raji      230-555-0191    230-555-0191    5415 San Gabriel Dr.`

`16600   Karen Wu         646-555-0113    230-555-0192    9265 La Paz`

`4324    Karl Xie         508-555-0163    230-555-0193    4912 La Vuelta`

`16891   Jonn Jackson     674-555-0110    230-555-0194    40 Ellis St.`

`3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive`

`3588    Osa Agbonile     592-555-0152    230-555-0196    1873 Lion Circle`

`10272   Julia Lee        870-555-0110    230-555-0197    3148 Rose Street`

`4868    Jose Hayes       599-555-0171    230-555-0198    793 Crawford Street`

`4761    Caleb Alexander  670-555-0141    230-555-0199    4775 Kentucky Dr.`

`16443   Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive`

Igény szerint létrehozhat egy szövegfájlt, és feltöltheti a fájlt a saját tárfiókjába. Az utasításokért lásd: [adatok feltöltése Apache Hadoop feladatokhoz a HDInsight-ben](../hdinsight-upload-data.md).

Ez az eljárás az `Contacts` utolsó eljárás során létrehozott HBase táblát használja.

1. Az Open SSH-kapcsolatban futtassa a következő parancsot az adatfájl adatfájlt storefiles való átalakításához, és tárolja a által megadott relatív elérési utat `Dimporttsv.bulk.output` .

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. Futtassa az alábbi parancsot az adatok `/example/data/storeDataFileOutput` a HBase táblába való feltöltéséhez:

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. Megnyithatja a HBase-rendszerhéjt, és a `scan` parancs használatával listázhatja a tábla tartalmát.

## <a name="use-apache-hive-to-query-apache-hbase"></a>Az Apache HBase lekérdezése Apache Hive használatával

A HBase-táblákban lévő adatlekérdezéseket [Apache Hive](https://hive.apache.org/)használatával lehet lekérdezni. Ebben a szakaszban egy, a HBase-táblára leképezést biztosító Hive-táblát hoz létre, amellyel lekérdezheti a HBase-táblában lévő adatokat.

1. Az Open SSH-kapcsolatban a következő paranccsal indíthatja el a Beeline-t:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    A Beeline-nal kapcsolatos további információkért lásd [a Hive és a Hadoop együttes, a Beeline-nal történő használatát a HDInsightban](../hadoop/apache-hadoop-use-hive-beeline.md) ismertető cikket.

1. Futtassa a következő [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) -szkriptet egy struktúra-tábla létrehozásához, amely leképezi a HBase táblát. A jelen utasítás futtatása előtt győződjön meg arról, hogy létrehozta a cikk korábbi részében hivatkozott minta táblát a HBase-rendszerhéj használatával.

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

1. A Beeline kilépéséhez használja a következőt: `!exit` .

1. Az SSH-kapcsolatok kilépéséhez használja a következőt: `exit` .

### <a name="separate-hive-and-hbase-clusters"></a>Különálló struktúra-és Hbase-fürtök

A HBase-adatok eléréséhez szükséges struktúra-lekérdezést nem kell végrehajtani a HBase-fürtről. A struktúra (beleértve a Spark, a Hadoop, a HBase vagy az interaktív lekérdezés) struktúrával rendelkező fürtök a HBase-információk lekérdezéséhez használhatók, a következő lépések elvégzésével:

1. Mindkét fürtöt ugyanahhoz a Virtual Networkhoz és alhálózathoz kell csatlakoztatni
2. Másolás `/usr/hdp/$(hdp-select --version)/hbase/conf/hbase-site.xml` a HBase-fürt átjárócsomópontokkal a kaptár-fürt átjárócsomópontokkal

### <a name="secure-clusters"></a>Biztonságos fürtök

A HBase az ESP-kompatibilis HBase használatával is lekérdezhető a kaptárból: 

1. Több fürtből álló minta követése esetén mindkét fürtnek ESP-kompatibilisnek kell lennie. 
2. Annak engedélyezéséhez, hogy a kaptár lekérdezze a HBase-fájlokat, győződjön meg arról, hogy a `hive` felhasználó engedélyt kap a HBase-adatbázis elérésére a HBase Apache Ranger beépülő modullal
3. Ha különálló, ESP-kompatibilis fürtöket használ, a `/etc/hosts` HBase-fürt átjárócsomópontokkal tartozó tartalmat hozzá kell fűzni `/etc/hosts` a kaptár-fürt átjárócsomópontokkal. 
> [!NOTE]
> A fürtök méretezése után `/etc/hosts` újra hozzá kell fűzni

## <a name="use-hbase-rest-apis-using-curl"></a>HBase REST API-k használata Curl használatával

A REST API védelméről [alapszintű hitelesítés](https://en.wikipedia.org/wiki/Basic_access_authentication) gondoskodik. Mindig biztonságos HTTP-n (HTTPS-en) keresztül kell kéréseket végeznie, hogy a hitelesítő adatait biztonságos módon küldje el a kiszolgálónak.

1. A HBase REST API-k a HDInsight-fürtön való engedélyezéséhez adja hozzá a következő Egyéni indítási parancsfájlt a **parancsfájl-művelet** szakaszhoz. Az indítási parancsfájlt a fürt létrehozásakor vagy a fürt létrehozása után is hozzáadhatja. A **csomópont típusa** beállításnál válassza a **régió kiszolgálók** lehetőséget, hogy a parancsfájl csak a HBase-régió kiszolgálóin legyen végrehajtva.


    ```bash
    #! /bin/bash

    THIS_MACHINE=`hostname`

    if [[ $THIS_MACHINE != wn* ]]
    then
        printf 'Script to be executed only on worker nodes'
        exit 0
    fi

    RESULT=`pgrep -f RESTServer`
    if [[ -z $RESULT ]]
    then
        echo "Applying mitigation; starting REST Server"
        sudo python /usr/lib/python2.7/dist-packages/hdinsight_hbrest/HbaseRestAgent.py
    else
        echo "Rest server already running"
        exit 0
    fi
    ```

1. Az egyszerű használat érdekében állítsa be a környezeti változót. Szerkessze az alábbi parancsokat úgy, hogy lecseréli `MYPASSWORD` a fürt bejelentkezési jelszavát. Cserélje le a `MYCLUSTERNAME` nevet a HBase-fürt nevére. Ezután adja meg a parancsokat.

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

    Base64 kódolással adja meg a-d kapcsolóban megadott értékeket. A példában:

   * MTAwMA==: 1000
   * UGVyc29uYWw6TmFtZQ = =: Personal: név
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
> `curl -u <UserName>:<Password> \`
>
> `-G https://<ClusterName>.azurehdinsight.net/templeton/v1/status`
>
> A következőhöz hasonló választ kell kapnia:
>
> `{"status":"ok","version":"v1"}`

## <a name="check-cluster-status"></a>A fürt állapotának ellenőrzése

A HBase a HDInsightban a fürtök megfigyelésére szolgáló webes felhasználói felülettel kapható. A webes felhasználói felülettel a régiók statisztikáit vagy információit kérheti le.

**A HBase mesterfelületének elérése**

1. Jelentkezzen be a Ambari webes felhasználói felületére, ahol a a `https://CLUSTERNAME.azurehdinsight.net` `CLUSTERNAME` HBase-fürt neve.

1. A bal oldali menüben válassza a **HBase** lehetőséget.

1. Válassza a **gyors hivatkozások** lehetőséget az oldal tetején, mutasson az aktív Zookeeper csomópontra, majd válassza ki **HBase Master felhasználói felületet**.  A felület egy új böngészőlapon nyílik meg:

   :::image type="content" source="./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png" alt-text="HDInsight Apache HBase HMaster felhasználói felülete" border="false":::

   A HBase-mesterfelület az alábbi részeket tartalmazza:

   - régiós kiszolgálók
   - biztonsági mentési főkiszolgálók
   - táblákban
   - feladatok
   - szoftverattribútumok

## <a name="cluster-recreation"></a>Fürt kikapcsolódása

A HBase-fürtök törlése után egy másik HBase-fürtöt hozhat létre ugyanazon alapértelmezett blobtárolóval. Az új fürt felveszi az eredeti fürtben létrehozott HBase-táblákat. A következetlenségek elkerülése érdekében azonban javasoljuk, hogy a fürt törlése előtt tiltsa le a HBase táblákat. 

Használhatja a HBase parancsot `disable 'Contacts'` . 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje a létrehozott HBase-fürtöt a következő lépésekkel:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. A felső **keresőmezőbe** írja be a **HDInsight** kifejezést.
1. Válassza ki a **HDInsight-fürtök** elemet a **szolgáltatások** területen.
1. A megjelenő HDInsight-fürtök listájában kattintson a **...** elemre az oktatóanyaghoz létrehozott fürt mellett.
1. Kattintson a **Törlés** gombra. Kattintson a **Yes** (Igen) gombra.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre Apache HBase-fürtöt. És hogyan hozhat létre táblákat, és hogyan tekintheti meg a táblák adatait a HBase-rendszerhéjból. Azt is megtanulta, hogyan használható struktúra-lekérdezés a HBase-táblákban található információkhoz. És hogyan használható a HBase C# REST API-k egy HBase tábla létrehozásához és adatok lekérdezéséhez a táblából. További információ:

> [!div class="nextstepaction"]
> [A HDInsight HBase áttekintése](./apache-hbase-overview.md)

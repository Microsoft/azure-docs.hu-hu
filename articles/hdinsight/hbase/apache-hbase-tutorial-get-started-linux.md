---
title: Ismerkedés a HDInsight - Azure-HBase-példájába
description: Kövesse ezt az Apache HBase-példát a HDInsight-alapú Hadoop használatának megkezdéséhez. Táblákat hozhat létre a HBase rendszehéjból, és lekérdezheti azokat a Hive eszközzel.
keywords: hbasecommand,hbase példa
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: hrasheed
ms.openlocfilehash: c1c582c60a7c91de40983daa07bdec1e8d748f8a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64718873"
---
# <a name="get-started-with-an-apache-hbase-example-in-hdinsight"></a>Bevezetés a HDInsight egy Apache HBase-példájába

Ismerje meg, hogyan hozhat létre egy [Apache HBase](https://hbase.apache.org/) HDInsight-fürt, hozzon létre HBase-táblákat és lekérdezéstáblákat [Apache Hive](https://hive.apache.org/).  A HBase-re vonatkozó általános információért lásd: [HDInsight HBase overview][hdinsight-hbase-overview] (A HDInsight HBase áttekintése).

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Előfeltételek
Az alábbi HBase-példa kipróbálásához a következőkkel kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Biztonságos rendszerhéj (SSH)](../hdinsight-hadoop-linux-use-ssh-unix.md). 
* [curl](https://curl.haxx.se/download.html).

## <a name="create-apache-hbase-cluster"></a>Az Apache HBase-fürt létrehozása
Az alábbi eljárás egy Azure Resource Manager-sablont használ egy HBase-fürt és a függő Azure Storage-fiók létrehozására. Az eljárásban és egyéb fürtlétrehozási módszerekben használt paraméterek megértéséhez lásd: [Create Linux-based Hadoop clusters in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) (Linux-alapú Hadoop-fürtök létrehozása a HDInsightban). A Data Lake Storage Gen2 használatával további információkért lásd: [a rövid útmutató: A HDInsight-fürtök beállítása](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

1. Az alábbi képre kattintva megnyithatja a sablont az Azure Portalon. A sablonban található [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/).
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Az **Egyéni üzembe helyezés** panelen adja meg a következő értékeket:
   
   * **Előfizetés**: Válassza ki az Azure-előfizetés, amely a fürt létrehozására szolgál.
   * **Erőforráscsoport**: Hozzon létre egy Azure-erőforrás felügyeleti csoportot, vagy használjon egy meglévőt.
   * **Hely**: Adja meg az erőforráscsoport helyét. 
   * **ClusterName**: Adjon meg egy nevet a HBase-fürt.
   * **Fürt bejelentkezési neve és jelszava**: Az alapértelmezett bejelentkezési név az **admin**.
   * **SSH-felhasználónevet és jelszót**: Az alapértelmezett felhasználónév az **sshuser**.  Ezt át lehet nevezni.
     
     Más paraméterek opcionálisak.  
     
     Minden egyes fürt az Azure Storage-fióktól függ. A fürtök törlése után az adatok megmaradnak a tárfiókban. A fürt alapértelmezett tárfiókneve a fürt neve a „store” kifejezéssel kiegészítve. A név szoftveresen kötött a sablonváltozók szakaszban.
3. Válassza az **I agree to the terms and conditions stated above** (Elfogadom a fenti feltételeket) lehetőséget majd kattintson a **Purchase** (Vásárlás) gombra. Egy fürt létrehozása nagyjából 20 percet vesz igénybe.

> [!NOTE]  
> A HBase-fürtök törlése után egy másik HBase-fürtöt hozhat létre ugyanazon alapértelmezett blobtárolóval. Az új fürt felveszi az eredeti fürtben létrehozott HBase-táblákat. Az inkonzisztenciák elkerülése érdekében javasoljuk, hogy a fürt törlése előtt tiltsa le a HBase-táblákat.
> 
> 

## <a name="create-tables-and-insert-data"></a>Táblák létrehozása és adatok beszúrása
Ön SSH-val HBase-fürtökhöz csatlakozhat, és ezután [Apache HBase rendszerhéj](https://hbase.apache.org/0.94/book/shell.html) hozhat létre HBase-táblákat, helyezze be az adatokat, és adatokat kérdezhet le. További információ: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

A legtöbbek számára az adatok táblázatos formátumban jelennek meg:

![HDInsight HBase táblázatos adatok][img-hbase-sample-data-tabular]

A HBase (megvalósítását [felhőalapú BigTable](https://cloud.google.com/bigtable/)), ugyanazokat az adatokat a következőhöz hasonló:

![HDInsight HBase BigTable-adatok][img-hbase-sample-data-bigtable]


**A Hbase-rendszerhéj használata**

1. Az SSH-ból futtassa az alábbi HBase-parancsot:
   
    ```bash
    hbase shell
    ```

2. Hozzon létre egy HBase-t kétoszlopos családokkal:

    ```hbaseshell   
    create 'Contacts', 'Personal', 'Office'
    list
    ```
3. Adatok beszúrása:
    
    ```hbaseshell   
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    scan 'Contacts'
    ```
   
    ![HDInsight Hadoop HBase-rendszerhéj][img-hbase-shell]
4. Egyetlen sor lekérése
   
    ```hbaseshell
    get 'Contacts', '1000'
    ```
   
    Ugyanazokat az eredményeket látja, mint a vizsgálat parancs használatakor, mert csak egy sor van.
   
    A HBase táblasémáról további információért lásd: [Bevezetés az Apache HBase Schema Design][hbase-schema]. További Hbase-parancsokért lásd: [Apache HBase reference guide][hbase-quick-start] (Apache HBase referencia-útmutató).
5. Kilépés a rendszerhéjból
   
    ```hbaseshell
    exit
    ```

**Adatok kötegelt betöltése a névjegyek HBase-táblába**

A HBase több módszert tartalmaz az adatok táblába töltéséhez.  További információ: [Bulk loading](https://hbase.apache.org/book.html#arch.bulk.load) (Kötegelt betöltés).

Egy minta adatfájlt egy nyilvános blob-tárolóban található *wasb://hbasecontacts\@hditutorialdata.blob.core.windows.net/contacts.txt*.  Az adatfájl tartalma a következő:

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

Igény szerint létrehozhat egy szövegfájlt, és feltöltheti a fájlt a saját tárfiókjába. Az utasításokért lásd: [Upload data for HDInsight az Apache Hadoop-feladatok][hdinsight-upload-data].

> [!NOTE]  
> Ez az eljárás az utolsó eljárás során létrehozott Contacts HBase táblát használja.

1. Az SSH-ból futtassa az alábbi parancsot, hogy az adatfájlt StoreFiles-fájllá alakítsa, és a Dimporttsv.bulk.output által meghatározott relatív elérési úton tárolja.  Ha a HBase rendszerhéjban van, a kilépés paranccsal lépjen ki.

    ```bash   
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. Futtassa a következő parancsot, hogy adatokat töltsön fel az /example/data/storeDataFileOutput mappából a HBase táblába:
   
    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. Megnyithatja a HBase rendszerhéjat, és a vizsgálat paranccsal listázhatja a tábla tartalmát.

## <a name="use-apache-hive-to-query-apache-hbase"></a>Az Apache Hive használata az Apache HBase lekérdezéséhez

Használatával lekérdezheti a HBase táblákban lévő adatok [Apache Hive](https://hive.apache.org/). Ebben a szakaszban egy, a HBase-táblára leképezést biztosító Hive-táblát hoz létre, amellyel lekérdezheti a HBase-táblában lévő adatokat.

1. Nyissa meg a **PuTTY** eszközt, és csatlakozzon a fürthöz.  Lásd az előző eljárás utasításait.
2. Az SSH-munkamenetből a következő paranccsal indíthatja el a Beeline-t:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    A Beeline-nal kapcsolatos további információkért lásd [a Hive és a Hadoop együttes, a Beeline-nal történő használatát a HDInsightban](../hadoop/apache-hadoop-use-hive-beeline.md) ismertető cikket.
       
3. Futtassa a következő [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) parancsfájl, a HBase táblára leképező Hive-tábla létrehozásához. Ellenőrizze, hogy létrehozta-e az oktatóanyag korábbi részében hivatkozott mintatáblát az utasítás futtatása előtt a HBase rendszerhéjjal.

    ```hiveql   
    CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
    STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
    TBLPROPERTIES ('hbase.table.name' = 'Contacts');
    ```

4. Futtassa a következő HiveQL-parancsfájlt a HBase-tábla adatainak lekérdezéséhez:

    ```hiveql   
    SELECT count(rowkey) FROM hbasecontacts;
    ```

## <a name="use-hbase-rest-apis-using-curl"></a>HBase REST API-k használata Curl használatával

A REST API védelméről [alapszintű hitelesítés](https://en.wikipedia.org/wiki/Basic_access_authentication) gondoskodik. Mindig biztonságos HTTP-n (HTTPS-en) keresztül kell kéréseket végeznie, hogy a hitelesítő adatait biztonságos módon küldje el a kiszolgálónak.

1. Használja az alábbi parancsot a meglévő HBase-táblák listázásához:

    ```bash
    curl -u <UserName>:<Password> \
    -G https://<ClusterName>.azurehdinsight.net/hbaserest/
    ```

1. Használja az alábbi parancsot egy új, kétoszlopos családokkal rendelkező HBase-tábla létrehozásához:

    ```bash   
    curl -u <UserName>:<Password> \
    -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
    -v
    ```

    A séma a JSon formátumban van megadva.
1. Használja az alábbi parancsot néhány adat beviteléhez:

    ```bash   
    curl -u <UserName>:<Password> \
    -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
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
    curl -u <UserName>:<Password> \
    -X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" \
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

1. Jelentkezzen be az Ambari webes felhasználói Felületet a https://&lt;Clustername >. azurehdinsight.NET formátumban van.
2. Kattintson a **HBase** elemre a bal oldali menüben.
3. Kattintson a **Gyorshivatkozások** elemre a lap tetején, mutasson az aktív Zookeeper-csomópont hivatkozására, majd kattintson a **HBase-mesterfelület** elemre.  A felület egy új böngészőlapon nyílik meg:

   ![HDInsight HBase HMaster felhasználói felülete](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

   A HBase-mesterfelület az alábbi részeket tartalmazza:

   - régiós kiszolgálók
   - biztonsági mentési főkiszolgálók
   - táblák
   - feladatok
   - szoftverattribútumok

## <a name="delete-the-cluster"></a>A fürt törlése
Az inkonzisztenciák elkerülése érdekében javasoljuk, hogy a fürt törlése előtt tiltsa le a HBase-táblákat.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Hibaelhárítás

Ha problémába ütközik a HDInsight-fürtök létrehozása során, tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, Apache HBase-fürt létrehozása és a táblák létrehozásához és az adatok megtekintése az adott táblák a HBase rendszerhéjból. Azt is megtanulta, hogyan használhat Hive-lekérdezést a HBase-táblákban lévő adatokon, és hogyan használhatja a HBase C# REST API-kat egy HBase-tábla létrehozásához és adatok lekérdezéséhez a táblából.

További tudnivalókért lásd:

* [HDInsight HBase overview][hdinsight-hbase-overview]: Az Apache HBase egy Apache, nyílt forráskódú nosql-alapú adatbázis az Apache hadoop, amely véletlenszerű hozzáférést és erős konzisztenciát biztosít a nagy mennyiségű strukturálatlan és félig strukturált adatot.

[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hbase-reference]: https://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: https://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]:apache-hbase-overview.md
[hdinsight-hbase-provision-vnet]:apache-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com/
[azure-create-storageaccount]: https://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hbase-shell]: ./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png

---
title: Apache Hadoop kaptár használata a HDInsight-Azure-beli curl használatával
description: Ismerje meg, hogyan küldhet távolról az Apache Pig-feladatokat az Azure HDInsight a curl használatával.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/06/2020
ms.openlocfilehash: 124661c57f779b9f8a639debcc093ed15e717694
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946463"
---
# <a name="run-apache-hive-queries-with-apache-hadoop-in-hdinsight-using-rest"></a>Apache Hive-lekérdezések futtatása a HDInsight Apache Hadoop a REST használatával

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Megtudhatja, hogyan futtathat Apache Hive-lekérdezéseket az Azure HDInsight-fürtön lévő Apache Hadoopokkal a Webhcaten REST API használatával.

## <a name="prerequisites"></a>Előfeltételek

* Egy Apache Hadoop-fürt a HDInsight-on. Lásd: Ismerkedés [a HDInsight Linux rendszeren](./apache-hadoop-linux-tutorial-get-started.md).

* REST-ügyfél. Ez a dokumentum a Windows PowerShellben [és a](https://curl.haxx.se/) [bash](/windows/wsl/install-win10)-on futó [-webkérést](/powershell/module/microsoft.powershell.utility/invoke-webrequest) használja.

* Ha bash-et használ, a parancssori JSON-processzort is jQ kell.  Lásd: [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/) .

## <a name="base-uri-for-rest-api"></a>Alapszintű URI a REST API-hoz

A HDInsight lévő REST API alapszintű Uniform Resource Identifier (URI) `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME` , ahol a a `CLUSTERNAME` fürt neve.  Az URI-k fürtjének nevei **megkülönböztetik a kis-és nagybetűket**.  Míg a fürt neve az URI () teljes tartományneve (FQDN) része, a kis-és nagybetűk megkülönböztetése `CLUSTERNAME.azurehdinsight.net` , az URI-n belüli más előfordulások megkülönböztetik a kis-és nagybetűket.

## <a name="authentication"></a>Hitelesítés

Ha cURL vagy bármilyen más REST-kommunikációt használ a Webhcaten-mel, a kérelmeket a HDInsight-fürt rendszergazdájához tartozó Felhasználónév és jelszó megadásával kell hitelesítenie. A REST API védelméről [alapszintű hitelesítés](https://en.wikipedia.org/wiki/Basic_access_authentication) gondoskodik. Annak érdekében, hogy a hitelesítő adatok biztonságosan legyenek továbbítva a kiszolgálónak, mindig a biztonságos HTTP (HTTPS) protokoll használatával végezze el a kérelmeket.

### <a name="setup-preserve-credentials"></a>Beállítás (hitelesítő adatok megőrzése)

Őrizze meg a hitelesítő adatait, hogy elkerülje az egyes példák újbóli beírását.  A fürt nevét külön lépésben megőrzi a rendszer.

**A. bash**  
Szerkessze az alábbi szkriptet úgy, hogy lecseréli a `PASSWORD` tényleges jelszavát.  Ezután adja meg a parancsot.

```bash
export password='PASSWORD'
```  

**B. PowerShell** Hajtsa végre az alábbi kódot, és adja meg a hitelesítő adatait az előugró ablakban:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>A megfelelő tokozású fürt nevének azonosítása

A fürt nevének tényleges burkolata különbözhet attól függően, hogy a fürt hogyan lett létrehozva.  Az itt látható lépések megjelenítik a tényleges burkolatot, majd egy változóban tárolják az összes későbbi példát.

Szerkessze az alábbi parancsfájlokat a `CLUSTERNAME` fürt nevével való lecseréléséhez. Ezután adja meg a parancsot. (Az FQDN fürt neve nem megkülönbözteti a kis-és nagybetűket.)

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
echo $clusterName
```  

```powershell
# Identify properly cased cluster name
$resp = Invoke-WebRequest -Uri "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" `
    -Credential $creds -UseBasicParsing
$clusterName = (ConvertFrom-Json $resp.Content).items.Clusters.cluster_name;

# Show cluster name
$clusterName
```

## <a name="run-a-hive-query"></a>Hive-lekérdezések futtatása

1. Annak ellenőrzéséhez, hogy tud-e csatlakozni a HDInsight-fürthöz, használja a következő parancsok egyikét:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Az alábbi szöveghez hasonló válasz érkezik:

    ```json
    {"status":"ok","version":"v1"}
    ```

    Ezen parancs paraméterei a következők:

    * `-u` – A kérelem hitelesítéséhez használt Felhasználónév és jelszó.
    * `-G` – Azt jelzi, hogy ez a kérelem lekéréses művelet.

1. Az URL-cím elejének `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1` megegyeznek az összes kérelemnél. Az elérési út `/status` azt jelzi, hogy a kérés visszaadja a kiszolgáló webhcaten (más néven Templeton) állapotát. A kaptár verzióját a következő parancs használatával is kérheti:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Ez a kérelem az alábbi szöveghez hasonló választ ad vissza:

    ```json
    {"module":"hive","version":"1.2.1000.2.6.5.3008-11"}
    ```

1. A **log4jLogs** nevű tábla létrehozásához használja a következőt:

    ```bash
    jobid=$(curl -s -u admin:$password -d user.name=admin -d execute="DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/rest" https://$clusterName.azurehdinsight.net/templeton/v1/hive | jq -r .id)
    echo $jobid
    ```

    ```powershell
    $reqParams = @{"user.name"="admin";"execute"="DROP TABLE log4jLogs;CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/;SELECT t4 AS sev,COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;";"statusdir"="/example/rest"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/hive" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    Ez a kérelem a POST metódust használja, amely az adatokat a kérelem részeként küldi el a REST API. A kérelem a következő adatértékeket küldi el:

     * `user.name` – A parancsot futtató felhasználó.
     * `execute` – A végrehajtandó HiveQL-utasítások.
     * `statusdir` – Az a könyvtár, amelyre a feladatokhoz tartozó állapot íródik.

   Ezek az utasítások a következő műveleteket hajtják végre:

   * `DROP TABLE` – Ha a tábla már létezik, törölve lett.
   * `CREATE EXTERNAL TABLE` -Új "külső" táblát hoz létre a kaptárban. A külső táblák csak a struktúra tábla definícióját tárolják. Az adatmező az eredeti helyen marad.

     > [!NOTE]  
     > Külső táblákat kell használni, ha várható, hogy a mögöttes adatokat külső forrás frissíti. Például egy automatizált adatfeltöltési folyamat vagy egy másik MapReduce művelet.
     >
     > Egy külső tábla eldobása **nem** törli az adatforrást, csak a tábla definícióját.

   * `ROW FORMAT` -Az adat formázása. Az egyes naplók mezői szóközzel vannak elválasztva.
   * `STORED AS TEXTFILE LOCATION` – Az adattárolás helye (a példa/adatkönyvtár), valamint a szövegként tárolt érték.
   * `SELECT` – Kiválasztja az összes olyan sor számát, ahol a **T4** oszlop tartalmazza a **[hiba]** értéket. Ez az utasítás **3** értéket ad vissza, mert három sor tartalmazza ezt az értéket.

     > [!NOTE]  
     > Figyelje meg, hogy a HiveQL utasítások közötti szóközöket a `+` karakter váltja fel a curl használatával. A szóközt (például az elválasztót) tartalmazó idézőjeles értékeket nem szabad lecserélni `+` .

      Ez a parancs egy olyan AZONOSÍTÓJÚ feladatot ad vissza, amely a feladatok állapotának vizsgálatára használható.

1. A feladatok állapotának megtekintéséhez használja a következő parancsot:

    ```bash
    curl -u admin:$password -d user.name=admin -G https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobid | jq .status.state
    ```

    ```powershell
    $reqParams=@{"user.name"="admin"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobID" `
       -Credential $creds `
       -Body $reqParams `
       -UseBasicParsing
    # ConvertFrom-JSON can't handle duplicate names with different case
    # So change one to prevent the error
    $fixDup=$resp.Content.Replace("jobID","job_ID")
    (ConvertFrom-Json $fixDup).status.state
    ```

    Ha a feladatot befejezte, az állapot **sikeres** lesz.

1. Ha a művelet állapota **sikeresen** módosult, az Azure Blob Storage-ból kérheti le a feladatok eredményeit. A `statusdir` lekérdezéssel átadott paraméter tartalmazza a kimeneti fájl helyét; ebben az esetben: `/example/rest` . Ez a címe tárolja a könyvtárban lévő kimenetet a `example/curl` fürtök alapértelmezett tárolójában.

    Ezeket a fájlokat az [Azure CLI](/cli/azure/install-azure-cli)használatával listázhatja és letöltheti. Az Azure CLI és az Azure Storage használatával kapcsolatos további információkért lásd az Azure [CLI használata](../../storage/blobs/storage-quickstart-blobs-cli.md) az Azure Storage-ban című dokumentumot.

## <a name="next-steps"></a>Következő lépések

További információ a HDInsight-beli Hadoop használható egyéb módszerekről:

* [Apache Hive használata a HDInsight Apache Hadoop használatával](hdinsight-use-hive.md)
* [A MapReduce használata a HDInsight Apache Hadoop használatával](hdinsight-use-mapreduce.md)

A dokumentumban használt REST API kapcsolatos további információkért tekintse meg a [webhcaten dokumentációját](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) .
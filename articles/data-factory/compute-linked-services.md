---
title: Azure Data Factory által támogatott számítási környezetek
description: Ismerje meg azokat a számítási környezeteket, amelyeket Azure Data Factory folyamatokban (például Azure HDInsight) használhat az adatok átalakításához és feldolgozásához.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/10/2019
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 59e31f0c280687dfd2a79b3a40d8474c82b794d4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681579"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Azure Data Factory által támogatott számítási környezetek
Ez a cikk az adatok feldolgozásához és átalakításához használható különböző számítási környezeteket ismerteti. Emellett a Data Factory által támogatott különböző konfigurációkról (igény szerinti és saját) is gondoskodik, ha a társított szolgáltatások konfigurálásával a számítási környezeteket egy Azure-beli adat-előállítóhoz kapcsolja.

A következő táblázat felsorolja a Data Factory által támogatott számítási környezeteket és a rajtuk futó tevékenységeket. 

| Számítási környezet                                          | tevékenységek                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Igény szerinti HDInsight-fürt](#azure-hdinsight-on-demand-linked-service) vagy [a saját HDInsight-fürt](#azure-hdinsight-linked-service) | [Kaptár](transform-data-using-hadoop-hive.md), [Pig](transform-data-using-hadoop-pig.md), [Spark](transform-data-using-spark.md), [MapReduce](transform-data-using-hadoop-map-reduce.md), [Hadoop streaming](transform-data-using-hadoop-streaming.md) |
| [Azure Batch](#azure-batch-linked-service)                   | [Egyéni](transform-data-using-dotnet-custom-activity.md)     |
| [Azure Machine Learning Studio](#azure-machine-learning-studio-linked-service) | [Machine Learning-tevékenységek: kötegelt végrehajtás és erőforrás frissítése](transform-data-using-machine-learning.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Azure Machine Learning folyamat végrehajtása](transform-data-machine-learning-service.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Azure Machine Learning folyamat végrehajtása](transform-data-machine-learning-service.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md) |
| [Azure SQL](#azure-sql-database-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), [SQL Server](#sql-server-linked-service) | [Tárolt eljárás](transform-data-using-stored-procedure.md) |
| [Azure Databricks](#azure-databricks-linked-service)         | [Jegyzetfüzet](transform-data-databricks-notebook.md), [jar](transform-data-databricks-jar.md), [Python](transform-data-databricks-python.md) |
| [Azure-függvény](#azure-function-linked-service)         | [Azure Function tevékenység](control-flow-azure-function-activity.md)
>  

## <a name="on-demand-hdinsight-compute-environment"></a>Igény szerinti HDInsight számítási környezet
Ebben a konfigurációban a számítástechnikai környezetet teljes mértékben a Azure Data Factory szolgáltatás felügyeli. A Data Factory szolgáltatás automatikusan hozza létre, mielőtt egy feladatot elküld az adatok feldolgozásához, és eltávolítja azokat a feladatok befejezésekor. Létrehozhat egy társított szolgáltatást az igény szerinti számítási környezethez, konfigurálhatja, és szabályozhatja a feladatok végrehajtásának, a fürtszolgáltatásnak és a rendszerindítási műveletek részletes beállításait.

> [!NOTE]
> Az igény szerinti konfiguráció jelenleg csak az Azure HDInsight-fürtökön támogatott. A Azure Databricks a feladatütemezés használatával is támogatja az igény szerinti feladatokat, további részletekért tekintse meg az [Azure Databricks társított szolgáltatást](#azure-databricks-linked-service) .

## <a name="azure-hdinsight-on-demand-linked-service"></a>Azure HDInsight igény szerinti társított szolgáltatás
A Azure Data Factory szolgáltatás automatikusan létrehozhat egy igény szerinti HDInsight-fürtöt az adatfeldolgozáshoz. A fürt ugyanabban a régióban jön létre, mint a fürthöz társított Storage-fiók (a JSON linkedServiceName tulajdonsága). A Storage-fióknak általános célú szabványos Azure Storage-fióknak kell lennie. 

Vegye figyelembe a következő **fontos** pontokat az igény szerinti HDInsight társított szolgáltatással kapcsolatban:

* Az igény szerinti HDInsight-fürt az Azure-előfizetése alatt jön létre. A fürt működése közben megtekintheti a Azure Portal fürtöt. 
* Az igény szerinti HDInsight-fürtön futó feladatok naplóit a rendszer a HDInsight-fürthöz társított Storage-fiókba másolja. A társított szolgáltatás definíciójában definiált clusterUserName, clusterPassword, clusterSshUserName, clusterSshPassword a fürt életciklusa során történő részletes hibaelhárításhoz használható a fürtbe való bejelentkezéshez. 
* Csak arra az időre számítunk fel díjat, amikor a HDInsight-fürt működik, és feladatait futtatja.
* **Parancsfájl-műveletet** használhat az Azure HDInsight igény szerinti társított szolgáltatásával.  

> [!IMPORTANT]
> Az Azure HDInsight-fürtök igény szerinti üzembe helyezése általában **20 percet** vesz igénybe.

### <a name="example"></a>Példa
A következő JSON egy Linux-alapú igény szerinti HDInsight társított szolgáltatást definiál. A Data Factory szolgáltatás automatikusan létrehoz egy **Linux-alapú HDInsight-** fürtöt a szükséges tevékenység feldolgozásához. 

```json
{
  "name": "HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "clusterType": "hadoop",
      "clusterSize": 1,
      "timeToLive": "00:15:00",
      "hostSubscriptionId": "<subscription ID>",
      "servicePrincipalId": "<service principal ID>",
      "servicePrincipalKey": {
        "value": "<service principal key>",
        "type": "SecureString"
      },
      "tenant": "<tenent id>",
      "clusterResourceGroup": "<resource group name>",
      "version": "3.6",
      "osType": "Linux",
      "linkedServiceName": {
        "referenceName": "AzureStorageLinkedService",
        "type": "LinkedServiceReference"
      }
    },
    "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
  }
}
```

> [!IMPORTANT]
> A HDInsight-fürt létrehoz egy **alapértelmezett tárolót** a JSON-fájlban megadott blob-tárolóban (**linkedServiceName**). A fürt törlésekor a HDInsight nem törli ezt a tárolót. Ez a működésmód szándékos. Igény szerinti HDInsight társított szolgáltatás esetén a rendszer mindig létrehoz egy HDInsight-fürt, amikor fel kell dolgozni egy szeletet, kivéve, ha van meglévő élő fürt (**timeToLive**), majd a feldolgozás végén a rendszer törli a fürtöt. 
>
> Ahogy egyre több tevékenység fut, számos tároló jelenik meg az Azure Blob Storage-ban. Ha nincs szüksége rájuk a feladatokkal kapcsolatos hibaelhárításhoz, törölheti őket a tárolási költségek csökkentése érdekében. A tárolók neve a következő mintát követi: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Az Azure Blob Storage-tárból olyan eszközökkel törölheti a tárolókat, mint például a [Microsoft Storage Explorer](https://storageexplorer.com/).
>
> 

### <a name="properties"></a>Tulajdonságok
| Tulajdonság                     | Leírás                              | Kötelező |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | A Type tulajdonságot **HDInsightOnDemand**értékre kell beállítani. | Igen      |
| clusterSize                  | A fürtben lévő feldolgozó/adatcsomópontok száma. A HDInsight-fürt két fő csomóponttal, valamint a tulajdonsághoz megadott munkavégző csomópontok számával együtt jön létre. A csomópontok olyan méretű Standard_D3 rendelkeznek, amelyek 4 maggal rendelkeznek, így a 4 feldolgozó csomópont-fürt 24 magot használ (4\*4 = 16 maggal a feldolgozó csomópontok számára, plusz 2\*4 = 8 mag a fő csomópontok számára). További részletekért lásd: [fürtök beállítása a HDInsight-ben a Hadoop, Spark, Kafka és más](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) rendszerekben. | Igen      |
| linkedServiceName            | Az Azure Storage társított szolgáltatása, amelyet az igény szerinti fürt használ az adattároláshoz és az adatfeldolgozáshoz. A HDInsight-fürt ugyanabban a régióban jön létre, mint ez az Azure Storage-fiók. Az Azure HDInsightban korlátozott azon magok száma, amelyek az egyes támogatott Azure-régiókban felhasználhatók. Győződjön meg arról, hogy az Azure-régióban van elég alapvető kvóta ahhoz, hogy megfeleljen a szükséges clusterSize. Részletekért lásd: [fürtök beállítása a HDInsight-ben a Hadoop, a Spark, a Kafka és más](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) szolgáltatásokkal<p>Jelenleg nem hozhat létre olyan igény szerinti HDInsight-fürtöt, amely egy Azure Data Lake Store használ tárolóként. Ha az eredményeket egy Azure Data Lake Store HDInsight-feldolgozásból szeretné tárolni, a másolási tevékenységgel másolja át az Azure Blob Storage adatait az Azure Data Lake Store. </p> | Igen      |
| clusterResourceGroup         | Ez az erőforráscsoport létrehozza a HDInsight-fürtöt. | Igen      |
| TimeToLive                   | Az igény szerinti HDInsight-fürt számára engedélyezett üresjárati idő. Meghatározza, hogy az igény szerinti HDInsight-fürt mennyi ideig maradjon életben a tevékenység futtatása után, ha nincsenek más aktív feladatok a fürtben. A minimálisan megengedett érték 5 perc (00:05:00).<br/><br/>Ha például egy tevékenység futása 6 percet vesz igénybe, és a TimeToLive 5 percre van állítva, a fürt a tevékenység futtatásának 6 perce után 5 perccel továbbra is életben marad. Ha egy másik tevékenység futtatása a 6 perces időszakot futtatja, a rendszer ugyanazt a fürtöt dolgozza fel.<br/><br/>Egy igény szerinti HDInsight-fürt létrehozása költséges művelet (eltarthat egy darabig), ezért szükség szerint ezt a beállítást használja az adat-előállító teljesítményének növeléséhez egy igény szerinti HDInsight-fürt újbóli felhasználásával.<br/><br/>Ha a TimeToLive értéket 0-ra állítja, akkor a rendszer a tevékenység futtatása után azonnal törli a fürtöt. Míg ha magas értéket ad meg, a fürt tétlen maradhat, ha valamilyen hibaelhárítási céllal jelentkezik be, de magas költségekhez vezethet. Ezért fontos, hogy az igényeinek megfelelően állítsa be a megfelelő értéket.<br/><br/>Ha a TimeToLive tulajdonság értéke megfelelően van beállítva, több folyamat is megoszthatja az igény szerinti HDInsight-fürt példányát. | Igen      |
| clusterType                  | A létrehozandó HDInsight-fürt típusa. Az engedélyezett értékek: "Hadoop" és "Spark". Ha nincs megadva, az alapértelmezett érték a Hadoop. Enterprise Security Package engedélyezett fürtöt nem lehet igény szerint létrehozni, hanem [meglévő fürtöt használnia, vagy saját számítási](#azure-hdinsight-linked-service)feladatokkal kell rendelkeznie. | Nem       |
| version                      | A HDInsight-fürt verziója. Ha nincs megadva, az a jelenlegi HDInsight-definíciót használja alapértelmezett verzióként. | Nem       |
| hostSubscriptionId           | A HDInsight-fürt létrehozásához használt Azure-előfizetés azonosítója. Ha nincs megadva, az Azure bejelentkezési környezetének előfizetés-AZONOSÍTÓját használja. | Nem       |
| clusterNamePrefix           | A HDI-fürt nevének előtagját a rendszer automatikusan hozzáfűzi a fürt nevének végéhez.| Nem       |
| sparkVersion                 | A Spark verziója, ha a fürt típusa "Spark" | Nem       |
| additionalLinkedServiceNames | További Storage-fiókokat ad meg a HDInsight társított szolgáltatáshoz, hogy a Data Factory szolgáltatás regisztrálja őket az Ön nevében. Ezeknek a tárolási fiókoknak ugyanabban a régióban kell lenniük, mint a HDInsight-fürtnek, amely ugyanabban a régióban jön létre, mint a linkedServiceName által megadott Storage-fiók. | Nem       |
| osType                       | Az operációs rendszer típusa. Az engedélyezett értékek a következők: Linux és Windows (csak HDInsight 3,3 esetén). Az alapértelmezett érték a Linux. | Nem       |
| hcatalogLinkedServiceName    | Az HCatalog-adatbázisra mutató Azure SQL társított szolgáltatás neve. Az igény szerinti HDInsight-fürtöt az Azure SQL Database metaadattár használatával hozza létre. | Nem       |
| Connectvia tulajdonsággal                   | A tevékenységeknek a HDInsight társított szolgáltatásba való küldéséhez használandó Integration Runtime. Az igény szerinti HDInsight társított szolgáltatás esetében csak a Azure Integration Runtime támogatja. Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. | Nem       |
| clusterUserName                   | A fürt eléréséhez használt Felhasználónév. | Nem       |
| clusterPassword                   | A fürt eléréséhez szükséges biztonságos karakterlánc típusú jelszó. | Nem       |
| clusterSshUserName         | Az SSH-Felhasználónév távolról csatlakozik a fürt csomópontjaihoz (Linux rendszeren). | Nem       |
| clusterSshPassword         | Biztonságos karakterlánc típusú jelszó az SSH-hoz távolról a fürt csomópontjának összekapcsolásához (Linux rendszeren). | Nem       |
| scriptActions | Adja meg a [HDInsight-fürt testreszabásához](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux) szükséges parancsfájlt az igény szerinti fürt létrehozása során. <br />A Azure Data Factory felhasználói felületének szerzői eszköze jelenleg csak 1 parancsfájl megadását támogatja, de ezt a korlátozást a JSON-ban is elérheti (több parancsfájl-műveletet is megadhat a JSON-ban). | Nem |


> [!IMPORTANT]
> A HDInsight több, üzembe helyezhető Hadoop-fürt verzióját is támogatja. Mindegyik verzió a Hortonworks adatplatform (HDP) terjesztésének egy adott verzióját hozza létre, amely az adott disztribúcióban található összetevőkből áll. A támogatott HDInsight-verziók listája továbbra is frissül, így biztosítva a legújabb Hadoop-ökoszisztéma-összetevőket és-javításokat. Győződjön meg arról, hogy mindig a [támogatott HDInsight-verzió és operációs rendszer típusa](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) legfrissebb információit használja a HDInsight támogatott verziójának a biztosításához. 
>
> [!IMPORTANT]
> A HDInsight társított szolgáltatások jelenleg nem támogatják a HBase, az interaktív lekérdezés (kaptár LLAP), a Storm. 
>
> 

#### <a name="additionallinkedservicenames-json-example"></a>additionalLinkedServiceNames JSON-példa

```json
"additionalLinkedServiceNames": [{
    "referenceName": "MyStorageLinkedService2",
    "type": "LinkedServiceReference"          
}]
```

### <a name="service-principal-authentication"></a>Egyszerű szolgáltatásnév hitelesítése

Az igény szerinti HDInsight társított szolgáltatáshoz a HDInsight-fürtök létrehozásához az Ön nevében szükség van egy egyszerű szolgáltatás hitelesítésére. Az egyszerű szolgáltatás hitelesítésének használatához regisztráljon egy alkalmazás entitást Azure Active Directory (Azure AD), és adja meg az előfizetés **közreműködői** szerepkörét, vagy azt az erőforráscsoportot, amelyben a HDInsight-fürt létrejött. A részletes lépéseket lásd: a [portál használata olyan Azure Active Directory alkalmazás és szolgáltatásnév létrehozásához, amely hozzáférhet az erőforrásokhoz](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Jegyezze fel a következő értékeket, amelyeket a társított szolgáltatás definiálásához használ:

- Alkalmazásazonosító
- Alkalmazás kulcsa 
- Bérlőazonosító

Az egyszerű szolgáltatás hitelesítését a következő tulajdonságok megadásával használhatja:

| Tulajdonság                | Leírás                              | Kötelező |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Határozza meg az alkalmazás ügyfél-AZONOSÍTÓját.     | Igen      |
| **servicePrincipalKey** | Az alkalmazás kulcsának meghatározása.           | Igen      |
| **Bérlő**              | Adja meg a bérlői adatokat (tartománynevet vagy bérlői azonosítót), amely alatt az alkalmazás található. Lekérheti a Azure Portal jobb felső sarkában lévő egér fölé. | Igen      |

### <a name="advanced-properties"></a>Speciális tulajdonságok

Az igény szerinti HDInsight-fürt részletes konfigurálásához a következő tulajdonságokat is megadhatja.

| Tulajdonság               | Leírás                              | Kötelező |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Meghatározza a létrehozandó HDInsight-fürt alapvető konfigurációs paramétereit (a Core-site. xml fájlban). | Nem       |
| hBaseConfiguration     | Megadja a HDInsight-fürt HBase-konfigurációs paramétereit (hbase-site. xml). | Nem       |
| hdfsConfiguration      | Megadja a HDInsight-fürt HDFS-konfigurációs paramétereit (hdfs-site. xml). | Nem       |
| hiveConfiguration      | Meghatározza a HDInsight-fürthöz tartozó struktúra konfigurációs paramétereit (Hive-site. xml). | Nem       |
| mapReduceConfiguration | Megadja a HDInsight-fürt MapReduce-konfigurációs paramétereit (mapred-site. xml). | Nem       |
| oozieConfiguration     | Megadja a HDInsight-fürt Oozie-konfigurációs paramétereit (oozie-site. xml). | Nem       |
| stormConfiguration     | Megadja a HDInsight-fürt Storm konfigurációs paramétereit (Storm-site. xml). | Nem       |
| yarnConfiguration      | Megadja a HDInsight-fürthöz tartozó fonal-konfigurációs paramétereket (yarn-site. xml). | Nem       |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Példa: igény szerinti HDInsight-fürt speciális tulajdonságokkal

```json
{
    "name": " HDInsightOnDemandLinkedService",
    "properties": {
      "type": "HDInsightOnDemand",
      "typeProperties": {
          "clusterSize": 16,
          "timeToLive": "01:30:00",
          "hostSubscriptionId": "<subscription ID>",
          "servicePrincipalId": "<service principal ID>",
          "servicePrincipalKey": {
            "value": "<service principal key>",
            "type": "SecureString"
          },
          "tenant": "<tenent id>",
          "clusterResourceGroup": "<resource group name>",
          "version": "3.6",
          "osType": "Linux",
          "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
            },
            "coreConfiguration": {
                "templeton.mapper.memory.mb": "5000"
            },
            "hiveConfiguration": {
                "templeton.mapper.memory.mb": "5000"
            },
            "mapReduceConfiguration": {
                "mapreduce.reduce.java.opts": "-Xmx4000m",
                "mapreduce.map.java.opts": "-Xmx4000m",
                "mapreduce.map.memory.mb": "5000",
                "mapreduce.reduce.memory.mb": "5000",
                "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
            },
            "yarnConfiguration": {
                "yarn.app.mapreduce.am.resource.mb": "5000",
                "mapreduce.map.memory.mb": "5000"
            },
            "additionalLinkedServiceNames": [{
                "referenceName": "MyStorageLinkedService2",
                "type": "LinkedServiceReference"          
            }]
        }
    },
      "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
}
```

### <a name="node-sizes"></a>Csomópontok méretei
A következő tulajdonságokkal adhatja meg a fej, az adatmennyiség és a Zookeeper-csomópont méretét: 

| Tulajdonság          | Leírás                              | Kötelező |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Megadja a fő csomópont méretét. Az alapértelmezett érték: Standard_D3. A részletekért tekintse **meg a csomópont-méretek megadása** szakaszt. | Nem       |
| dataNodeSize      | Megadja az adatcsomópont méretét. Az alapértelmezett érték: Standard_D3. | Nem       |
| zookeeperNodeSize | Megadja az állatkert-megtartó csomópont méretét. Az alapértelmezett érték: Standard_D3. | Nem       |

#### <a name="specifying-node-sizes"></a>Csomópontok méretének meghatározása
Az előző szakaszban említett tulajdonságok megadásához meg kell adnia [Virtual Machines cikk méretét](../virtual-machines/linux/sizes.md) . Az értékeknek meg kell felelniük a cikkben említett **parancsmagoknak & API** -kkal. Ahogy a cikkben látható, a nagyméretű (alapértelmezett) méret adatcsomópontja 7 GB memóriával rendelkezik, ami esetleg nem elég jó a forgatókönyvhöz. 

Ha a D4 méretű fő csomópontokat és munkavégző csomópontokat kívánja létrehozni, a **Standard_D4** értéket a headNodeSize és a dataNodeSize tulajdonságainak értékeként kell megadni. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Ha nem megfelelő értéket ad meg ezekhez a tulajdonságokhoz, a következő hibaüzenet jelenhet meg **:** nem sikerült létrehozni a fürtöt. Kivétel: Nem sikerült befejezni a fürt létrehozásának műveletét. A művelet 400-as kóddal meghiúsult. A fürt állapota: „hiba”. Üzenet: "PreClusterCreationValidationFailure". Ha ezt a hibaüzenetet kapja, győződjön meg arról, hogy a **parancsmag & API** -k nevét használja a táblázatból [Virtual Machines cikk mérete alapján](../virtual-machines/linux/sizes.md) .        

## <a name="bring-your-own-compute-environment"></a>Saját számítási környezet használata
Ebben a konfigurációban a felhasználók egy már meglévő számítástechnikai környezetet regisztrálhatnak Data Factory társított szolgáltatásként. A számítástechnikai környezetet a felhasználó felügyeli, és a Data Factory szolgáltatás ezt használja a tevékenységek végrehajtásához.

Az ilyen típusú konfiguráció a következő számítási környezetekben támogatott:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL-adatbázis, Azure SQL DW, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Azure HDInsight társított szolgáltatás
Létrehozhat egy Azure HDInsight társított szolgáltatást, hogy regisztrálja a saját HDInsight-fürtöt Data Factory használatával.

### <a name="example"></a>Példa

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
      "type": "HDInsight",
      "typeProperties": {
        "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
        "userName": "username",
        "password": {
            "value": "passwordvalue",
            "type": "SecureString"
          },
        "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```

### <a name="properties"></a>Tulajdonságok
| Tulajdonság          | Leírás                                                  | Kötelező |
| ----------------- | ------------------------------------------------------------ | -------- |
| type              | A Type tulajdonságot **HDInsight**értékre kell beállítani.            | Igen      |
| clusterUri        | A HDInsight-fürt URI-ja.                            | Igen      |
| felhasználónév          | Adja meg a meglévő HDInsight-fürthöz való kapcsolódáshoz használni kívánt felhasználó nevét. | Igen      |
| jelszó          | A felhasználói fiók jelszavának megadása.                       | Igen      |
| linkedServiceName | Az Azure Storage társított szolgáltatás neve, amely a HDInsight-fürt által használt Azure Blob Storage-tárolóra hivatkozik. <p>Jelenleg nem adhat meg Azure Data Lake Store társított szolgáltatást ehhez a tulajdonsághoz. Ha a HDInsight-fürt hozzáfér a Data Lake Storehoz, akkor a kaptár/Pig parancsfájlokból is hozzáférhet a Azure Data Lake Storeban lévő adatokhoz. </p> | Igen      |
| isEspEnabled      | Ha a HDInsight-fürt [Enterprise Security Package](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-architecture) engedélyezve van, az "*igaz*" értéket kell megadni. Az alapértelmezett érték a "*false*". | Nem       |
| Connectvia tulajdonsággal        | A tevékenységeknek a társított szolgáltatásba való küldéséhez használandó Integration Runtime. Azure Integration Runtime vagy saját üzemeltetésű Integration Runtime is használható. Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. <br />Enterprise Security Package (ESP) engedélyezése esetén a HDInsight-fürt saját üzemeltetésű integrációs modult használ, amely a fürtre mutat, vagy az ESP HDInsight-fürtöt tartalmazó Virtual Network belül kell telepíteni. | Nem       |

> [!IMPORTANT]
> A HDInsight több, üzembe helyezhető Hadoop-fürt verzióját is támogatja. Mindegyik verzió a Hortonworks adatplatform (HDP) terjesztésének egy adott verzióját hozza létre, amely az adott disztribúcióban található összetevőkből áll. A támogatott HDInsight-verziók listája továbbra is frissül, így biztosítva a legújabb Hadoop-ökoszisztéma-összetevőket és-javításokat. Győződjön meg arról, hogy mindig a [támogatott HDInsight-verzió és operációs rendszer típusa](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) legfrissebb információit használja a HDInsight támogatott verziójának a biztosításához. 
>
> [!IMPORTANT]
> A HDInsight társított szolgáltatások jelenleg nem támogatják a HBase, az interaktív lekérdezés (kaptár LLAP), a Storm. 
>
> 

## <a name="azure-batch-linked-service"></a>Társított szolgáltatás Azure Batch

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Létrehozhat egy Azure Batch társított szolgáltatást a virtuális gépek (VM-EK) batch-készletének egy adatgyárba való regisztrálásához. Az egyéni tevékenységeket Azure Batch használatával futtathatja.

Tekintse meg a következő cikkeket, ha új Azure Batch szolgáltatás:

* A Azure Batch szolgáltatás áttekintéséhez [Azure batch alapjai](../batch/batch-technical-overview.md) .
* A [New-AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) parancsmaggal hozzon létre egy Azure batch-fiókot (vagy) [Azure Portal](../batch/batch-account-create-portal.md) a Azure batch-fiók Azure Portal használatával történő létrehozásához. A parancsmag használatával kapcsolatos részletes utasításokért lásd: a [PowerShell használata Azure batch-fiók kezeléséhez](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) .
* [New-AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) parancsmag Azure batch készlet létrehozásához.

### <a name="example"></a>Példa

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
      "type": "AzureBatch",
      "typeProperties": {
        "accountName": "batchaccount",
        "accessKey": {
          "type": "SecureString",
          "value": "access key"
        },
        "batchUri": "https://batchaccount.region.batch.azure.com",
        "poolName": "poolname",
        "linkedServiceName": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```


### <a name="properties"></a>Tulajdonságok
| Tulajdonság          | Leírás                              | Kötelező |
| ----------------- | ---------------------------------------- | -------- |
| type              | A Type tulajdonságot **AzureBatch**értékre kell beállítani. | Igen      |
| accountName       | A Azure Batch fiók neve.         | Igen      |
| accessKey         | A Azure Batch fiók elérési kulcsa.  | Igen      |
| batchUri          | Az Azure Batch-fiók URL-címe https://*batchaccountname. region*. Batch.Azure.com formátumban. | Igen      |
| poolName          | A virtuális gépek készletének neve.    | Igen      |
| linkedServiceName | Az ehhez a Azure Batch társított szolgáltatáshoz társított Azure Storage társított szolgáltatás neve. Ez a társított szolgáltatás a tevékenység futtatásához szükséges átmeneti fájlokhoz használatos. | Igen      |
| Connectvia tulajdonsággal        | A tevékenységeknek a társított szolgáltatásba való küldéséhez használandó Integration Runtime. Azure Integration Runtime vagy saját üzemeltetésű Integration Runtime is használható. Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. | Nem       |

## <a name="azure-machine-learning-studio-linked-service"></a>Társított szolgáltatás Azure Machine Learning Studio
Hozzon létre egy Azure Machine Learning Studio társított szolgáltatást egy Machine Learning batch pontozási végpont egy adatgyárba való regisztrálásához.

### <a name="example"></a>Példa

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
      "type": "AzureML",
      "typeProperties": {
        "mlEndpoint": "https://[batch scoring endpoint]/jobs",
        "apiKey": {
            "type": "SecureString",
            "value": "access key"
        }
     },
     "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
}
```

### <a name="properties"></a>Tulajdonságok
| Tulajdonság               | Leírás                              | Kötelező                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| Típus                   | A Type tulajdonságot a következőre kell beállítani: **AzureML**. | Igen                                      |
| mlEndpoint             | A Batch-pontozási URL-cím.                   | Igen                                      |
| apiKey                 | A közzétett munkaterület-modell API-ját.     | Igen                                      |
| updateResourceEndpoint | A prediktív webszolgáltatás a betanított modellel való frissítéséhez használt Azure Machine Learning webszolgáltatás-végpont frissítési erőforrásának URL-címe | Nem                                       |
| servicePrincipalId     | Határozza meg az alkalmazás ügyfél-AZONOSÍTÓját.     | Kötelező, ha a updateResourceEndpoint meg van adva |
| servicePrincipalKey    | Az alkalmazás kulcsának meghatározása.           | Kötelező, ha a updateResourceEndpoint meg van adva |
| Bérlő                 | Adja meg a bérlői adatokat (tartománynevet vagy bérlői azonosítót), amely alatt az alkalmazás található. Lekérheti a Azure Portal jobb felső sarkában lévő egér fölé. | Kötelező, ha a updateResourceEndpoint meg van adva |
| Connectvia tulajdonsággal             | A tevékenységeknek a társított szolgáltatásba való küldéséhez használandó Integration Runtime. Azure Integration Runtime vagy saját üzemeltetésű Integration Runtime is használható. Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. | Nem                                       |

## <a name="azure-machine-learning-linked-service"></a>Társított szolgáltatás Azure Machine Learning
Egy Azure Machine Learning társított szolgáltatást hoz létre egy Azure Machine Learning munkaterület egy adatgyárhoz való összekapcsolásához.

> [!NOTE]
> A Azure Machine Learning társított szolgáltatás jelenleg csak az egyszerű szolgáltatásnév hitelesítését támogatja.

### <a name="example"></a>Példa

```json
{
    "name": "AzureMLServiceLinkedService",
    "properties": {
        "type": "AzureMLService",
        "typeProperties": {
            "subscriptionId": "subscriptionId",
            "resourceGroupName": "resourceGroupName",
            "mlWorkspaceName": "mlWorkspaceName",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime?",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="properties"></a>Tulajdonságok
| Tulajdonság               | Leírás                              | Kötelező                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| Típus                   | A Type tulajdonságot a következőre kell beállítani: **AzureMLService**. | Igen                                      |
| subscriptionId         | Azure-előfizetés azonosítója              | Igen                                      |
| resourceGroupName      | név | Igen                                      |
| mlWorkspaceName        | Azure Machine Learning munkaterület neve | Igen  |
| servicePrincipalId     | Határozza meg az alkalmazás ügyfél-AZONOSÍTÓját.     | Nem |
| servicePrincipalKey    | Az alkalmazás kulcsának meghatározása.           | Nem |
| Bérlő                 | Adja meg a bérlői adatokat (tartománynevet vagy bérlői azonosítót), amely alatt az alkalmazás található. Lekérheti a Azure Portal jobb felső sarkában lévő egér fölé. | Kötelező, ha a updateResourceEndpoint meg van adva | Nem |
| Connectvia tulajdonsággal             | A tevékenységeknek a társított szolgáltatásba való küldéséhez használandó Integration Runtime. Azure Integration Runtime vagy saját üzemeltetésű Integration Runtime is használható. Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. | Nem |    

## <a name="azure-data-lake-analytics-linked-service"></a>Társított szolgáltatás Azure Data Lake Analytics
Hozzon létre egy **Azure Data Lake Analytics** társított szolgáltatást egy Azure Data Lake Analytics számítási szolgáltatás Azure-beli adatgyárhoz való összekapcsolásához. A folyamat Data Lake Analytics U-SQL tevékenysége erre a társított szolgáltatásra hivatkozik. 

### <a name="example"></a>Példa

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics URI",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID",
            "subscriptionId": "<optional, subscription ID of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="properties"></a>Tulajdonságok

| Tulajdonság             | Leírás                              | Kötelező                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| type                 | A Type tulajdonságot a következőre kell beállítani: **AzureDataLakeAnalytics**. | Igen                                      |
| accountName          | Azure Data Lake Analytics fiók neve.  | Igen                                      |
| dataLakeAnalyticsUri | Azure Data Lake Analytics URI.           | Nem                                       |
| subscriptionId       | Azure-előfizetés azonosítója                    | Nem                                       |
| resourceGroupName    | Azure-erőforráscsoport neve                | Nem                                       |
| servicePrincipalId   | Határozza meg az alkalmazás ügyfél-AZONOSÍTÓját.     | Igen                                      |
| servicePrincipalKey  | Az alkalmazás kulcsának meghatározása.           | Igen                                      |
| Bérlő               | Adja meg a bérlői adatokat (tartománynevet vagy bérlői azonosítót), amely alatt az alkalmazás található. Lekérheti a Azure Portal jobb felső sarkában lévő egér fölé. | Igen                                      |
| Connectvia tulajdonsággal           | A tevékenységeknek a társított szolgáltatásba való küldéséhez használandó Integration Runtime. Azure Integration Runtime vagy saját üzemeltetésű Integration Runtime is használható. Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. | Nem                                       |



## <a name="azure-databricks-linked-service"></a>Társított szolgáltatás Azure Databricks
**Azure Databricks társított szolgáltatás** létrehozásával regisztrálhat Databricks-munkaterületet, amelyet a Databricks számítási feladatainak futtatásához fog használni (jegyzetfüzet, JAR, Python). 
> [!IMPORTANT]
> A Databricks társított szolgáltatások támogatják a [példány-készleteket](https://aka.ms/instance-pools). 

### <a name="example---using-new-job-cluster-in-databricks"></a>Példa – új feladatkártya használata a Databricks-ben

```json
{
    "name": "AzureDatabricks_LS",
    "properties": {
        "type": "AzureDatabricks",
        "typeProperties": {
            "domain": "https://eastus.azuredatabricks.net",
            "newClusterNodeType": "Standard_D3_v2",
            "newClusterNumOfWorker": "1:10",
            "newClusterVersion": "4.0.x-scala2.11",
            "accessToken": {
                "type": "SecureString",
                "value": "dapif33c9c721144c3a790b35000b57f7124f"
            }
        }
    }
}

```

### <a name="example---using-existing-interactive-cluster-in-databricks"></a>Példa – meglévő interaktív fürt használata a Databricks-ben

```json
{
    "name": " AzureDataBricksLinedService",
    "properties": {
      "type": " AzureDatabricks",
      "typeProperties": {
        "domain": "https://westeurope.azuredatabricks.net",
        "accessToken": {
            "type": "SecureString", 
            "value": "dapif33c9c72344c3a790b35000b57f7124f"
          },
        "existingClusterId": "{clusterId}"
        }
}

```

### <a name="properties"></a>Tulajdonságok

| Tulajdonság             | Leírás                              | Kötelező                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| név                 | A társított szolgáltatás neve               | Igen   |
| type                 | A Type tulajdonságot a következőre kell beállítani: **Azure Databricks**. | Igen                                      |
| Tartományi               | Az Azure-régiót ennek megfelelően határozza meg a Databricks munkaterület régiója alapján. Például: https://eastus.azuredatabricks.net | Igen                                 |
| accessToken          | A hozzáférési token szükséges ahhoz, hogy a Data Factory hitelesíthető legyen a Azure Databricks. A hozzáférési tokent a databricks munkaterületen kell létrehozni. A hozzáférési token megkeresésének részletes lépései [itt](https://docs.azuredatabricks.net/api/latest/authentication.html#generate-token) találhatók  | Igen                                       |
| existingClusterId    | Meglévő fürt fürtjének azonosítója az összes feladat futtatásához. Ennek egy már létrehozott interaktív fürtnek kell lennie. Előfordulhat, hogy manuálisan kell újraindítani a fürtöt, ha az nem válaszol. A Databricks azt sugallják, hogy az új fürtökön futó feladatok nagyobb megbízhatóságot jelentenek. Az interaktív fürt Databricks munkaterületen található – > fürtök – > interaktív fürt neve – > Configuration-> címkék. [További részletek](https://docs.databricks.com/user-guide/clusters/tags.html) | Nem 
| instancePoolId    | A databricks-munkaterület egy meglévő készletének példány-készletének azonosítója.  | Nem  |
| newClusterVersion    | A fürt Spark-verziója. Ekkor létrejön egy databricks a fürtben. | Nem  |
| newClusterNumOfWorker| Azon munkavégző csomópontok száma, amelyeknek a fürtnek rendelkeznie kell. Egy fürthöz egyetlen Spark-illesztőprogram és egy num_workers-végrehajtó tartozik, összesen num_workers + 1 Spark-csomópontra. Egy Int32 formátumú karakterlánc, például az "1", a numOfWorker értéke 1 vagy "1:10", ami az 1 – min és 10 as max. közötti autoskálázást jelenti.  | Nem                |
| newClusterNodeType   | Ez a mező egyetlen értékkel kódolja a fürt egyes Spark-csomópontjai számára elérhető erőforrásokat. A Spark-csomópontok például a memória vagy a nagy számítási igényű munkaterhelések számára is kiépíthető és optimalizálható. Ez a mező az új fürthöz szükséges                | Nem               |
| newClusterSparkConf  | választható, felhasználó által megadott Spark konfiguráció kulcs-érték párok halmaza. A felhasználók a Spark. Driver. extraJavaOptions és a Spark. végrehajtó. extraJavaOptions címen keresztül további JVM lehetőségeket is adhatnak az illesztőprogramnak és a végrehajtóknak. | Nem  |
| newClusterInitScripts| az új fürthöz választható, felhasználó által definiált inicializálási parancsfájlok készlete. Az init-parancsfájlok DBFS elérési útjának megadása. | Nem  |


## <a name="azure-sql-database-linked-service"></a>Azure SQL Database társított szolgáltatás
Hozzon létre egy Azure SQL társított szolgáltatást, és használja a [tárolt eljárási tevékenységgel](transform-data-using-stored-procedure.md) egy Data Factory folyamat által tárolt eljárás meghívásához. A társított szolgáltatással kapcsolatos további információkért lásd: [Azure SQL Connector-összekötő](connector-azure-sql-database.md#linked-service-properties) .

## <a name="azure-sql-data-warehouse-linked-service"></a>Társított szolgáltatás Azure SQL Data Warehouse
Létrehoz egy Azure SQL Data Warehouse társított szolgáltatást, és a [tárolt eljárási tevékenységgel](transform-data-using-stored-procedure.md) felhasználja a tárolt eljárás meghívását egy Data Factory folyamatból. A társított szolgáltatással kapcsolatos részletekért tekintse meg [Azure SQL Data Warehouse összekötőt](connector-azure-sql-data-warehouse.md#linked-service-properties) ismertető cikket.

## <a name="sql-server-linked-service"></a>Társított szolgáltatás SQL Server
Létrehoz egy SQL Server társított szolgáltatást, és a [tárolt eljárási tevékenységgel](transform-data-using-stored-procedure.md) felhasználja a tárolt eljárás meghívását egy Data Factory folyamatból. A társított szolgáltatással kapcsolatos részletekért tekintse meg [SQL Server összekötőt](connector-sql-server.md#linked-service-properties) ismertető cikket.

## <a name="azure-function-linked-service"></a>Azure Function társított szolgáltatás
Hozzon létre egy Azure Function társított szolgáltatást, és használja az [Azure Function tevékenységgel](control-flow-azure-function-activity.md) , hogy Azure functions futtasson egy Data Factory folyamaton. Az Azure-függvény visszatérési típusának érvényes `JObject`nak kell lennie. (Ne feledje, hogy [](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) a JArray *nem* `JObject`.) A `JObject`tól eltérő visszatérési típusok sikertelenek, és a felhasználói hibákra *adott tartalom nem érvényes JObject*.

| **Tulajdonság** | **Leírás** | **Szükséges** |
| --- | --- | --- |
| type   | A Type tulajdonságot a következőre kell beállítani: **AzureFunction** | igen |
| function alkalmazás URL-címe | Az Azure-függvényalkalmazás URL-címe. A formátum `https://<accountname>.azurewebsites.net`. Ez az URL-cím az **URL-** cím szakaszban található, amikor a függvényalkalmazás megtekinti a Azure Portal  | igen |
| függvény kulcsa | Az Azure-függvény elérési kulcsa. Kattintson a **Manage (kezelés** ) szakaszra a megfelelő függvényhez, és másolja a **függvény** vagy a **gazda kulcsot**. További információ: [Azure FUNCTIONS http-eseményindítók és-kötések](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | igen |
|   |   |   |

## <a name="next-steps"></a>További lépések
Az Azure Data Factory által támogatott átalakítási tevékenységek listáját lásd: az [adatátalakítás](transform-data.md).

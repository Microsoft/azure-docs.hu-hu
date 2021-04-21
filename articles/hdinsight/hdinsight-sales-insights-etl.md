---
title: 'Oktatóanyag: Egy végpontok között létrehozott ETL-folyamat létrehozása értékesítési elemzések származtatása Azure HDInsight'
description: Megtudhatja, hogyan hozhat létre ETL-folyamatokat a Azure HDInsight segítségével értékesítési adatokból nyerhet ki elemzéseket igény szerinti Spark-fürtök és Power BI.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 04/15/2020
ms.openlocfilehash: 140709a0ddb548c126ceca208fdeef2db77616bf
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761798"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights-in-azure-hdinsight"></a>Oktatóanyag: Hozzon létre egy végpontok között futó adat folyamatot, amely értékesítési elemzéseket hoz létre a Azure HDInsight

Ebben az oktatóanyagban egy teljes folyamatot fog felépíteni, amely kinyerési, átalakítási és betöltési (ETL) műveleteket hajt végre. A folyamat [a](./spark/apache-spark-overview.md) Apache Spark és Apache Hive futó fürtök Azure HDInsight az adatok lekérdezéséhez és manipulálására. Az adattároláshoz olyan technológiákat is Azure Data Lake Storage Gen2, mint a Power BI a vizualizációkhoz.

Ez az adat folyamat egyesíti a különböző tárolókból származó adatokat, eltávolítja a nemkívánatos adatokat, új adatokat fűz hozzá, és ezeket betölti a tárolóba az üzleti elemzések megjelenítéséhez. További információ az ETL-folyamatokról: Kinyerési, átalakítási és betöltési [(ETL) nagy méretekben.](./hadoop/apache-hadoop-etl-at-scale.md)

:::image type="content" source="./media/hdinsight-sales-insights-etl/architecture.png" alt-text="ETL-architektúra" border="false":::

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

* Azure CLI – legalább 2.2.0-s verzió. Lásd: [Az Azure CLI telepítése.](/cli/azure/install-azure-cli)

* JQ, egy parancssori JSON-feldolgozó.  Lásd: [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/) .

* Az Azure beépített [szerepkörének tagja – tulajdonos.](../role-based-access-control/built-in-roles.md)

* Ha a PowerShell használatával aktiválja a Data Factory folyamatot, szüksége lesz az [Az modulra.](/powershell/azure/)

* [Power BI Desktop](https://aka.ms/pbiSingleInstaller) az üzleti elemzéseket az oktatóanyag végén.

## <a name="create-resources"></a>Az erőforrások létrehozása

### <a name="clone-the-repository-with-scripts-and-data"></a>Az adattár klónozása szkriptekkel és adatokkal

1. Jelentkezzen be az Azure-előfizetésbe. Ha a kódblokkot Azure Cloud Shell használni,  válassza a kódblokk jobb felső sarkában található Kipróbálom lehetőséget. Egyéb módon írja be az alábbi parancsot:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Győződjön meg arról, hogy tagja az Azure-beli szerepkör [tulajdonosának.](../role-based-access-control/built-in-roles.md) Cserélje `user@contoso.com` le a helyére a fiókját, majd írja be a következő parancsot:

    ```azurecli
    az role assignment list \
    --assignee "user@contoso.com" \
    --role "Owner"
    ```

    Ha nem ad vissza rekordot, akkor Ön nem tag, és nem fogja tudni befejezni az oktatóanyagot.

1. Töltse le az oktatóanyaghoz szükséges adatokat és szkripteket a [HDInsight Sales Insights ETL-adattárból.](https://github.com/Azure-Samples/hdinsight-sales-insights-etl) Írja be a következő parancsot:

    ```bash
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. Győződjön meg `salesdata scripts templates` arról, hogy létrejött. Ellenőrizze a következő paranccsal:

   ```bash
   ls
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>A folyamathoz szükséges Azure-erőforrások üzembe helyezése

1. Adja hozzá az összes szkript végrehajtási engedélyét a következő megadásával:

    ```bash
    chmod +x scripts/*.sh
    ````

1. Állítsa be az erőforráscsoport változóját. Cserélje le a helyére egy meglévő vagy új `RESOURCE_GROUP_NAME` erőforráscsoport nevét, majd írja be a következő parancsot:

    ```bash
    resourceGroup="RESOURCE_GROUP_NAME"
    ```

1. Futtassa a szkriptet. Cserélje `LOCATION` le a helyére a kívánt értéket, majd írja be a következő parancsot:

    ```bash
    ./scripts/resources.sh $resourceGroup LOCATION
    ```

    Ha nem biztos benne, hogy melyik régiót kell megadnia, az [az account list-locations](/cli/azure/account#az_account_list_locations) paranccsal lekérheti az előfizetéséhez támogatott régiók listáját.

    A parancs a következő erőforrásokat telepíti:

    * Egy Azure Blob Storage-fiók. Ez a fiók fogja tartalmazni a vállalati értékesítési adatokat.
    * Egy Azure Data Lake Storage Gen2 fiók. Ez a fiók lesz a tárfiók mindkét HDInsight-fürt számára. További információ a HDInsightról és Data Lake Storage Gen2 a Azure HDInsight [integrációja Data Lake Storage Gen2.](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
    * Felhasználó által hozzárendelt felügyelt identitás. Ez a fiók hozzáférést biztosít a HDInsight-fürtök számára a Data Lake Storage Gen2 fiókhoz.
    * Egy Apache Spark fürt. Ez a fürt a nyers adatok tisztítására és átalakítására lesz használva.
    * Egy Apache Hive [Interactive Query](./interactive-query/apache-interactive-query-get-started.md) fürt. Ez a fürt lehetővé teszi az értékesítési adatok lekérdezését és vizualizációját a Power BI.
    * Egy hálózati biztonsági csoport (NSG) szabályai által támogatott Azure-beli virtuális hálózat. Ez a virtuális hálózat lehetővé teszi a fürtök kommunikációját és biztonságossá teszi a kommunikációjukat.

A fürt létrehozása körülbelül 20 percet is igénybe vehet.

A fürtök SSH-hozzáférésének alapértelmezett jelszava `Thisisapassword1` a . Ha módosítani szeretné a jelszót, akkor a fájlban módosítsa a `./templates/resourcesparameters_remainder.json` , , és paraméterek `sparksshPassword` `sparkClusterLoginPassword` `llapClusterLoginPassword` `llapsshPassword` jelszavát.

### <a name="verify-deployment-and-collect-resource-information"></a>Az üzembe helyezés ellenőrzése és erőforrás-információk gyűjtése

1. Ha ellenőrizni szeretné az üzemelő példány állapotát, a fürt erőforráscsoportját Azure Portal. A **Beállítások alatt** válassza az **Üzemelő példányok** lehetőséget, majd a telepítést. Itt láthatja a sikeresen üzembe helyezett erőforrásokat és a még folyamatban lévő erőforrásokat.

1. A fürtök nevének megtekintéséhez írja be a következő parancsot:

    ```bash
    sparkClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.sparkClusterName.value')
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')

    echo "Spark Cluster" $sparkClusterName
    echo "LLAP cluster" $llapClusterName
    ```

1. Az Azure Storage-fiók és a hozzáférési kulcs megtekintéséhez írja be a következő parancsot:

    ```azurecli
    blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')

    blobKey=$(az storage account keys list \
        --account-name $blobStorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $blobStorageName
    echo $blobKey
    ```

1. A fiók Data Lake Storage Gen2 hozzáférési kulcs megtekintéséhez írja be a következő parancsot:

    ```azurecli
    ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

    adlsKey=$(az storage account keys list \
        --account-name $ADLSGen2StorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $ADLSGen2StorageName
    echo $adlsKey
    ```

### <a name="create-a-data-factory"></a>Adat-előállító létrehozása

Azure Data Factory egy olyan eszköz, amely segít automatizálni az Azure Pipelinest. Nem ez az egyetlen módja ezeknek a feladatoknak, de nagyszerű módja a folyamatok automatizálásának. Az adatokkal kapcsolatos további Azure Data Factory lásd a Azure Data Factory [dokumentációját.](https://azure.microsoft.com/services/data-factory/)

Ez az adat-előállító egy folyamatból áll, amely két tevékenységből áll:

* Az első tevékenység átmásolja az adatokat az Azure Blob Storage-ból Data Lake Storage Gen 2-tárfiókba az adatbe való adatbetelés utánzása érdekében.
* A második tevékenység átalakítja az adatokat a Spark-fürtben. A szkript a nemkívánatos oszlopok eltávolításával alakítja át az adatokat. Emellett egy új oszlopot is hozzáfűz, amely kiszámítja az egyetlen tranzakció által generált bevételt.

A folyamat Azure Data Factory hajtsa végre az alábbi parancsot.  Még mindig a könyvtárban `hdinsight-sales-insights-etl` kell lennie.

```bash
blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')
ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

./scripts/adf.sh $resourceGroup $ADLSGen2StorageName $blobStorageName
```

Ez a szkript a következőket teszi:

1. Létrehoz egy szolgáltatásnév, amely `Storage Blob Data Contributor` engedélyekkel rendelkezik a Data Lake Storage Gen2 tárfiókban.
1. Egy hitelesítési jogkivonatot szerez be, amely engedélyezi a POST-kéréseket a Data Lake Storage Gen2 [fájlrendszerének REST API.](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create)
1. A és a fájlban Data Lake Storage Gen2 a tárfiók tényleges `sparktransform.py` `query.hql` nevét.
1. Be szerez tárkulcsokat a Data Lake Storage Gen2 Blob Storage-fiókokhoz.
1. Létrehoz egy másik erőforrás-üzembe helyezést egy Azure Data Factory létrehozásához a társított társított szolgáltatásokkal és tevékenységekkel. Paraméterként továbbítja a tárkulcsokat a sablonfájlnak, hogy a csatolt szolgáltatások megfelelően hozzáférnek a tárfiókhoz.

## <a name="run-the-data-pipeline"></a>Az adat folyamat futtatása

### <a name="trigger-the-data-factory-activities"></a>A Data Factory aktiválása

A létrehozott Data Factory első tevékenysége áthelyezi az adatokat a Blob Storage-ból a Data Lake Storage Gen2. A második tevékenység alkalmazza a Spark-átalakításokat az adatokon, és egy új helyre menti az átalakított .csv-fájlokat. A teljes folyamat eltarthat néhány percig.

A név Data Factory írja be a következő parancsot:

```azurecli
cat resourcesoutputs_adf.json | jq -r '.properties.outputs.factoryName.value'
```

A folyamat aktiválása a következő lehet:

* Aktiválja a Data Factory a PowerShellben. Cserélje `RESOURCEGROUP` le a , és `DataFactoryName` értékeket a megfelelő értékekre, majd futtassa a következő parancsokat:

    ```powershell
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

    $resourceGroup="RESOURCEGROUP"
    $dataFactory="DataFactoryName"

    $pipeline =Invoke-AzDataFactoryV2Pipeline `
        -ResourceGroupName $resourceGroup `
        -DataFactory $dataFactory `
        -PipelineName "IngestAndTransform"

    Get-AzDataFactoryV2PipelineRun `
        -ResourceGroupName $resourceGroup  `
        -DataFactoryName $dataFactory `
        -PipelineRunId $pipeline
    ```

    A folyamat `Get-AzDataFactoryV2PipelineRun` figyelése érdekében szükség szerint hajtsa végre újra a következőt: .

    Vagy

* Nyissa meg az adat-előállítót, és **válassza az Author & Monitor lehetőséget.** Aktiválja a `IngestAndTransform` folyamatot a portálról. A folyamatok portálon keresztüli aktiválásával kapcsolatos információkért lásd: Igény szerinti [Apache Hadoop-fürtök](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline)létrehozása a HDInsightban a Azure Data Factory.

A folyamat futásának ellenőrzéséhez az alábbi lépések egyikét használhatja:

* A portálon **keresztül** az adat-előállító Figyelése szakaszát kell követnie.
* A Azure Storage Explorer a 2. Data Lake Storage tárfiókját. A fájlrendszerben a mappában ellenőrizze annak tartalmát, hogy sikeres `files` `transformed` volt-e a folyamat.

Az adatok HDInsight használatával való átalakításának egyéb módjaiért tekintse meg ezt a cikket a [Jupyter Notebook.](/azure/hdinsight/spark/apache-spark-load-data-run-query)

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Hozzon létre egy táblát a Interactive Query fürtön az adatok megtekintéséhez a Power BI

1. Másolja a `query.hql` fájlt az LLAP-fürtre az SCP használatával. Írja be a következő parancsot:

    ```bash
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')
    scp scripts/query.hql sshuser@$llapClusterName-ssh.azurehdinsight.net:/home/sshuser/
    ```

    Emlékeztető: Az alapértelmezett jelszó a `Thisisapassword1` .

1. Használja az SSH-t az LLAP-fürt eléréséhez. Írja be a következő parancsot:

    ```bash
    ssh sshuser@$llapClusterName-ssh.azurehdinsight.net
    ```

1. Futtassa a szkriptet a következő paranccsal:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

    Ez a szkript létrehoz egy felügyelt táblát a Interactive Query fürtön, amely a Power BI.

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Irányítópult Power BI értékesítési adatokból

1. Nyissa meg a Power BI Desktopot.

1. A menüből lépjen az Adatok **lekérte...**  >    >  **Azure**  >  **HDInsight Interactive Query**.

1. Válassza a **Kapcsolódás** lehetőséget.

1. A **HDInsight Interactive Query** párbeszédpanelen:
    1. A **Kiszolgáló szövegmezőbe** írja be az LLAP-fürt nevét a következő formátumban: `https://LLAPCLUSTERNAME.azurehdinsight.net` .
    1. Az adatbázis **szövegmezőbe** írja be a következőt: `default` .
    1. Válassza az **OK** lehetőséget.

1. Az **AzureHive párbeszédpanelen:**
    1. A Felhasználónév **szövegmezőbe** írja be a következőt: `admin` .
    1. A Jelszó **szövegmezőbe** írja be a következőt: `Thisisapassword1` .
    1. Válassza a **Kapcsolódás** lehetőséget.

1. Az **adatok előnézetének** megtekintéséhez a Kezelőben válassza a `sales` , és/vagy `sales_raw` lehetőséget. Az adatok betöltése után kísérletezhet a létrehozni kívánt irányítópultmal. Az irányítópultok használatával kapcsolatos első lépésekért tekintse meg Power BI hivatkozásokat:

* [Irányítópultok bemutatása Power BI-tervezők számára](https://docs.microsoft.com/power-bi/service-dashboards)
* [Oktatóanyag: Első lépések a Power BI szolgáltatással](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje az összes erőforrást a következő paranccsal, hogy ne számítsa fel a díjakat.

1. Az erőforráscsoport eltávolításához írja be a következő parancsot:

    ```azurecli
    az group delete -n $resourceGroup
    ```

1. A szolgáltatásnév eltávolításához írja be a következő parancsokat:

    ```azurecli
    servicePrincipal=$(cat serviceprincipal.json | jq -r '.name')
    az ad sp delete --id $servicePrincipal
    ```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Nagy léptékű kinyerési, átalakítási és betöltési (ETL)](./hadoop/apache-hadoop-etl-at-scale.md)

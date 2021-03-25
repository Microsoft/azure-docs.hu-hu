---
title: 'Oktatóanyag: teljes körű ETL-folyamat létrehozása az értékesítési eredmények kinyeréséhez az Azure HDInsight'
description: Ismerje meg, hogyan hozhat létre ETL-folyamatokat az Azure HDInsight, és hogyan származtathatja az értékesítési adatokból származó információkat a Spark igény szerinti fürtök és a Power BI használatával.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 04/15/2020
ms.openlocfilehash: 32c331b10fcc73b5449588595370f8f69ef9893c
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104872047"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights-in-azure-hdinsight"></a>Oktatóanyag: végpontok közötti adatfolyamat létrehozása az értékesítési elemzések létrehozásához az Azure HDInsight

Ebben az oktatóanyagban egy végpontok közötti adatfolyamatot fog kiépíteni, amely kinyerési, átalakítási és betöltési (ETL) műveleteket hajt végre. A folyamat az Azure HDInsight-on futó [Apache Spark](./spark/apache-spark-overview.md) és Apache Hive fürtöket fogja használni az adatlekérdezéshez és-kezeléshez. Emellett olyan technológiákat is használhat, mint a Azure Data Lake Storage Gen2 az adattároláshoz, és Power BI a vizualizációhoz.

Ez az adatfolyamat a különböző áruházakból származó adatok összegyűjtését, a nemkívánatos adatok eltávolítását, az új adatok hozzáfűzését és a tárhelyre való visszatöltését az üzleti elemzések megjelenítéséhez. További információ a [kinyerési, átalakítási és betöltési (etl)](./hadoop/apache-hadoop-etl-at-scale.md)folyamatokról a skálán.

:::image type="content" source="./media/hdinsight-sales-insights-etl/architecture.png" alt-text="ETL-architektúra" border="false":::

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

* Azure CLI – legalább a 2.2.0-as verzió. Lásd: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

* jQ, parancssori JSON-processzor.  Lásd: [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/) .

* Az [Azure beépített szerepkör-tulajdonosának](../role-based-access-control/built-in-roles.md)tagja.

* Ha a PowerShell használatával indítja el a Data Factory folyamatot, szüksége lesz az az [modulra](/powershell/azure/).

* [Power bi Desktop](https://aka.ms/pbiSingleInstaller) az oktatóanyag végén az üzleti eredmények megjelenítéséhez.

## <a name="create-resources"></a>Az erőforrások létrehozása

### <a name="clone-the-repository-with-scripts-and-data"></a>A tárház klónozása parancsfájlokkal és adatkezeléssel

1. Jelentkezzen be az Azure-előfizetésbe. Ha azt tervezi, hogy Azure Cloud Shell használ, válassza a **kipróbálás** lehetőséget a kód blokk jobb felső sarkában. Máskülönben adja meg az alábbi parancsot:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Győződjön meg arról, hogy tagja az Azure szerepkör- [tulajdonosnak](../role-based-access-control/built-in-roles.md). Cserélje le a `user@contoso.com` fiókot a fiókra, majd írja be a parancsot:

    ```azurecli
    az role assignment list \
    --assignee "user@contoso.com" \
    --role "Owner"
    ```

    Ha nem ad vissza rekordot, nem tagja, és nem tudja befejezni az oktatóanyagot.

1. Az oktatóanyaghoz tartozó adatok és parancsfájlok letöltése a [HDInsight Sales bepillantást ETL adattárból](https://github.com/Azure-Samples/hdinsight-sales-insights-etl). Írja be a következő parancsot:

    ```bash
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. Győződjön meg róla, `salesdata scripts templates` hogy létrejött. Ellenőrizze a következő paranccsal:

   ```bash
   ls
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>A folyamathoz szükséges Azure-erőforrások üzembe helyezése

1. Adja hozzá az összes parancsfájl végrehajtási engedélyeit a következő beírásával:

    ```bash
    chmod +x scripts/*.sh
    ````

1. Erőforráscsoport változójának beállítása. Cserélje le a `RESOURCE_GROUP_NAME` értéket egy meglévő vagy egy új erőforráscsoport nevére, majd írja be a parancsot:

    ```bash
    resourceGroup="RESOURCE_GROUP_NAME"
    ```

1. Futtassa a szkriptet. Cserélje le a `LOCATION` értéket a kívánt értékre, majd írja be a parancsot:

    ```bash
    ./scripts/resources.sh $resourceGroup LOCATION
    ```

    Ha nem biztos abban, hogy melyik régiót kell megadnia, lekérheti az előfizetéséhez tartozó támogatott régiók listáját az az [Account List-Locations](/cli/azure/account#az-account-list-locations) paranccsal.

    A parancs a következő erőforrásokat fogja telepíteni:

    * Egy Azure Blob Storage-fiók. Ez a fiók fogja tárolni a vállalati értékesítési adatforgalmat.
    * Egy Azure Data Lake Storage Gen2-fiók. Ez a fiók a HDInsight-fürtökhöz tartozó Storage-fiókként fog szolgálni. További információ: HDInsight és Data Lake Storage Gen2 az [Azure HDInsight-integrációban Data Lake Storage Gen2okkal](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/).
    * Felhasználó által hozzárendelt felügyelt identitás. Ez a fiók biztosítja a HDInsight-fürtök számára a Data Lake Storage Gen2 fiók elérését.
    * Egy Apache Spark-fürt. Ezt a fürtöt a rendszer a nyers Adattisztításra és átalakításra használja.
    * Egy Apache Hive [interaktív lekérdezési](./interactive-query/apache-interactive-query-get-started.md) fürt. Ez a fürt lehetővé teszi az értékesítési adatlekérdezést és a Power BIsal való megjelenítését.
    * Hálózati biztonsági csoport (NSG) szabályai által támogatott Azure-beli virtuális hálózat. Ez a virtuális hálózat lehetővé teszi, hogy a fürtök kommunikálhassanak és biztonságossá tegye a kommunikációt.

A fürt létrehozása körülbelül 20 percet vesz igénybe.

A fürtökhöz való SSH-hozzáférés alapértelmezett jelszava `Thisisapassword1` . Ha módosítani szeretné a jelszót, nyissa meg a `./templates/resourcesparameters_remainder.json` fájlt, és módosítsa a `sparksshPassword` ,, `sparkClusterLoginPassword` `llapClusterLoginPassword` és paraméterek jelszavát `llapsshPassword` .

### <a name="verify-deployment-and-collect-resource-information"></a>Az üzembe helyezés ellenőrzése és az erőforrások adatainak összegyűjtése

1. Ha szeretné megtekinteni az üzemelő példány állapotát, nyissa meg az erőforráscsoportot a Azure Portal. A **Beállítások** területen válassza a **központi telepítések**, majd a telepítés lehetőséget. Itt láthatja a sikeresen telepített erőforrásokat és a még folyamatban lévő erőforrásokat.

1. A fürtök neveinek megtekintéséhez írja be a következő parancsot:

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

1. A Data Lake Storage Gen2 fiók és a hozzáférési kulcs megtekintéséhez írja be a következő parancsot:

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

A Azure Data Factory egy olyan eszköz, amely segít automatizálni az Azure-folyamatokat. Ezen feladatok elvégzése nem az egyetlen módszer, de ez nagyszerű módszer a folyamatok automatizálására. A Azure Data Factoryről a [Azure Data Factory dokumentációjában](https://azure.microsoft.com/services/data-factory/)talál további információt.

Ez az adatfeldolgozó egyetlen folyamattal rendelkezik, amely két tevékenységgel rendelkezik:

* Az első tevékenység az Azure Blob Storage-ból származó adatok másolását a Data Lake Storage Gen 2 Storage-fiókba másolja az adatfeldolgozási műveletek elvégzéséhez.
* A második tevékenység átalakítja a Spark-fürtben található adatfájlokat. A parancsfájl a nemkívánatos oszlopok eltávolításával átalakítja az adattípust. Egy új oszlopot is hozzáfűz, amely kiszámítja az egyetlen tranzakció által generált bevételt.

A Azure Data Factory folyamat beállításához hajtsa végre az alábbi parancsot.  Továbbra is a címtárban kell lennie `hdinsight-sales-insights-etl` .

```bash
blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')
ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

./scripts/adf.sh $resourceGroup $ADLSGen2StorageName $blobStorageName
```

A szkript a következő műveleteket végzi el:

1. Létrehoz egy egyszerű szolgáltatást, amely a `Storage Blob Data Contributor` Data Lake Storage Gen2 Storage-fiókra vonatkozó engedélyekkel rendelkezik.
1. Hitelesítési jogkivonat beszerzése a POST kérések engedélyezéséhez a [Data Lake Storage Gen2 fájlrendszer REST API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create).
1. A és a fájlban lévő Data Lake Storage Gen2 Storage-fiók tényleges nevét adja `sparktransform.py` meg `query.hql` .
1. A Data Lake Storage Gen2-és blob Storage-fiókokhoz tartozó tárolási kulcsok beszerzése.
1. Egy másik erőforrás-telepítést hoz létre egy Azure Data Factory-folyamat létrehozásához társított kapcsolódó szolgáltatásokkal és tevékenységekkel. A tárolási kulcsokat paraméterként adja át a sablonfájl számára, hogy a társított szolgáltatások megfelelően hozzáférhessenek a Storage-fiókokhoz.

## <a name="run-the-data-pipeline"></a>Az adatfolyamat futtatása

### <a name="trigger-the-data-factory-activities"></a>A Data Factory tevékenységek kiváltása

A létrehozott Data Factory folyamat első tevékenysége áthelyezi az adatait a blob Storage-ból a Data Lake Storage Gen2ba. A második tevékenység alkalmazza a Spark-átalakításokat az adathalmazon, és menti az átalakított. csv fájlokat egy új helyre. A teljes folyamat eltarthat néhány percig.

A Data Factory nevének lekéréséhez írja be a következő parancsot:

```azurecli
cat resourcesoutputs_adf.json | jq -r '.properties.outputs.factoryName.value'
```

A folyamat elindításához a következők közül választhat:

* Aktiválja a Data Factory folyamatot a PowerShellben. Cserélje le a `RESOURCEGROUP` és a `DataFactoryName` értéket a megfelelő értékekre, majd futtassa a következő parancsokat:

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

    A `Get-AzDataFactoryV2PipelineRun` folyamat nyomon követéséhez szükség szerint hajtsa végre újra a végrehajtást.

    Vagy

* Nyissa meg az adatelőállítót, és válassza a **szerző & figyelő** elemet. A `IngestAndTransform` folyamat elindítása a portálról. A folyamatoknak a portálon keresztüli aktiválásával kapcsolatos további információkért lásd: [igény szerinti Apache Hadoop-fürtök létrehozása a HDInsight-ben Azure Data Factory használatával](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline).

A folyamat futtatásának ellenőrzéséhez hajtsa végre a következő lépések egyikét:

* A portálon keresztül nyissa meg a **monitoring** szakaszt az adatgyárban.
* A Azure Storage Explorerban nyissa meg a Data Lake Storage 2. generációs Storage-fiókot. Nyissa meg a `files` fájlrendszert, majd nyissa meg a `transformed` mappát, és ellenőrizze annak tartalmát, és ellenőrizze, hogy a folyamat sikeres volt-e.

Az HDInsight használatával történő adatátalakítás egyéb módjairól [ebben a cikkben olvashat a Jupyter notebook használatáról](/azure/hdinsight/spark/apache-spark-load-data-run-query).

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Tábla létrehozása az interaktív lekérdezési fürtön Power BI

1. Másolja a `query.hql` fájlt a LLAP-fürtre SCP használatával. Adja meg a parancsot:

    ```bash
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')
    scp scripts/query.hql sshuser@$llapClusterName-ssh.azurehdinsight.net:/home/sshuser/
    ```

    Emlékeztető: az alapértelmezett jelszó `Thisisapassword1` .

1. Használja az SSH-t a LLAP-fürt eléréséhez. Adja meg a parancsot:

    ```bash
    ssh sshuser@$llapClusterName-ssh.azurehdinsight.net
    ```

1. A parancsfájl futtatásához használja a következő parancsot:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

    Ez a szkript egy felügyelt táblát hoz létre az interaktív lekérdezési fürtön, amely a Power BIból érhető el.

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Power BI irányítópult létrehozása értékesítési adatokból

1. Nyissa meg a Power BI Desktopot.

1. A menüből válassza az **adatok lekérése**  >  **továbbiak...**  >  lehetőséget. **Azure**  >  **HDInsight interaktív lekérdezés**.

1. Válassza a **Kapcsolódás** lehetőséget.

1. A **HDInsight interaktív lekérdezés** párbeszédpanelen:
    1. A **kiszolgáló** szövegmezőbe írja be a LLAP-fürt nevét a formátuma mezőbe `https://LLAPCLUSTERNAME.azurehdinsight.net` .
    1. Az **adatbázis** szövegmezőbe írja be a értéket `default` .
    1. Válassza az **OK** lehetőséget.

1. A **AzureHive** párbeszédpanelen:
    1. A **Felhasználónév** szövegmezőbe írja be a nevet `admin` .
    1. A **jelszó** szövegmezőbe írja be a értéket `Thisisapassword1` .
    1. Válassza a **Kapcsolódás** lehetőséget.

1. A **Navigátorból** válassza ki `sales` és/vagy `sales_raw` az adatok előnézetét. Az adatgyűjtés után kísérletezhet a létrehozni kívánt irányítópulttal. A Power BI-irányítópultok megismeréséhez tekintse meg az alábbi hivatkozásokat:

* [Irányítópultok bemutatása Power BI-tervezők számára](https://docs.microsoft.com/power-bi/service-dashboards)
* [Oktatóanyag: Első lépések a Power BI szolgáltatással](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, az alábbi parancs használatával törölje az összes erőforrást, hogy ne legyen rájuk felszámítva.

1. Az erőforráscsoport eltávolításához írja be a következő parancsot:

    ```azurecli
    az group delete -n $resourceGroup
    ```

1. Az egyszerű szolgáltatás eltávolításához írja be a következő parancsokat:

    ```azurecli
    servicePrincipal=$(cat serviceprincipal.json | jq -r '.name')
    az ad sp delete --id $servicePrincipal
    ```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Kinyerés, átalakítás és betöltés (ETL) skálán](./hadoop/apache-hadoop-etl-at-scale.md)

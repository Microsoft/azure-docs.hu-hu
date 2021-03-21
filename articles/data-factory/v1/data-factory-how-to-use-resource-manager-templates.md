---
title: Resource Manager-sablonok használata a Data Factoryban
description: Megtudhatja, hogyan hozhat létre és használhat Azure Resource Manager sablonokat Data Factory entitások létrehozásához.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: c17ef939b8d27035e5b28f4a651b9be2db8f2279
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100382939"
---
# <a name="use-templates-to-create-azure-data-factory-entities"></a>Sablonok használata Azure Data Factory entitások létrehozásához
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. 

## <a name="overview"></a>Áttekintés
Az adatintegrációs igényekhez Azure Data Factory használatakor előfordulhat, hogy a különböző környezetekben ugyanazt a mintát használja, vagy ismétlődően alkalmazza ugyanazt a feladatot ugyanazon a megoldáson belül. A sablonok megkönnyítik ezen forgatókönyvek megvalósítását és kezelését. A Azure Data Factory sablonjai ideálisak az újrahasználhatóságot és a ismétlődést érintő forgatókönyvekhez.

Vegye figyelembe azt a helyzetet, amelyben a szervezet 10 gyártó üzeme van a világ minden tájáról. Az egyes létesítmények naplófájljai külön SQL Server adatbázisban tárolódnak. A vállalat egyetlen adattárházat szeretne létrehozni a felhőben az ad hoc elemzésekhez. Azt is szeretné, hogy ugyanazokat a logikát, de a fejlesztési, tesztelési és éles környezetekben használt konfigurációkat is meg kell adni.

Ebben az esetben egy feladatot ugyanabban a környezetben kell megismételni, de különböző értékekkel kell rendelkeznie az egyes gyártói üzemekhez tartozó 10 adat-előállítók között. Érvényben van az **Ismétlődés** . A sablon lehetővé teszi ennek az általános folyamatnak az absztrakcióját (azaz a folyamatokat, amelyek ugyanazt a tevékenységet használják az egyes adatelőállítókban), de az egyes gyártási üzemekhez külön paramétereket használnak.

Továbbá, mivel a szervezet a 10 adatfeldolgozót többször szeretné telepíteni különböző környezetekben, a sablonok a fejlesztési, tesztelési és éles környezetekhez külön paraméter-fájlok használatával is használhatják ezt a **felhasználhatóságot** .

## <a name="templating-with-azure-resource-manager"></a>Sablon Azure Resource Manager
A [Azure Resource Manager-sablonok](../../azure-resource-manager/templates/overview.md) nagyszerű lehetőséget biztosítanak a sablonoknak a Azure Data Factory való megvalósítására. A Resource Manager-sablonok JSON-fájl segítségével határozzák meg az Azure-megoldás infrastruktúráját és konfigurációját. Mivel Azure Resource Manager sablonok az összes/legtöbb Azure-szolgáltatással működnek, széles körben használható az Azure-eszközök összes erőforrásának egyszerű kezelése. A Resource Manager-sablonokkal kapcsolatos további információkért lásd: [Azure Resource Manager-sablonok készítése](../../azure-resource-manager/templates/template-syntax.md) .

## <a name="tutorials"></a>Oktatóanyagok
A következő oktatóanyagok részletes útmutatást biztosítanak Data Factory entitások Resource Manager-sablonok használatával történő létrehozásához:

* [Oktatóanyag: adatmásolási folyamat létrehozása Azure Resource Manager sablon használatával](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [Oktatóanyag: folyamat létrehozása adatfeldolgozásra Azure Resource Manager sablon használatával](data-factory-build-your-first-pipeline.md)

## <a name="data-factory-templates-on-github"></a>Sablonok Data Factory a GitHubon
Tekintse meg a következő Azure-előugró sablonokat a GitHubon:

* [Hozzon létre egy adatelőállítót az adatok Azure-Blob Storageból való másolásához Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
* [Adatelőállító létrehozása struktúra-tevékenységgel az Azure HDInsight-fürtön](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
* [Adatok előállítójának létrehozása a Salesforce-ből az Azure-Blobokra való másoláshoz](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)
* [Adatfeldolgozó létrehozása a láncok tevékenységekhez: adatok másolása egy FTP-kiszolgálóról az Azure-Blobokra, egy struktúra-parancsfájl meghívása egy igény szerinti HDInsight-fürtön az adatok átalakításához, és a másolás eredménye Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-data-factory-ftp-hive-blob)

Nyugodtan megoszthatja Azure Data Factory-sablonjait az [Azure](https://azure.microsoft.com/documentation/templates/)rövid útmutatójában. Tekintse meg a [hozzájárulási útmutatót](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE) a tárházon keresztül megosztható sablonok fejlesztése során.

A következő szakaszokban részletesen ismertetjük Data Factory erőforrásoknak egy Resource Manager-sablonban való definiálásának részleteit.

## <a name="defining-data-factory-resources-in-templates"></a>Data Factory erőforrások definiálása a sablonokban
Az adatgyár definiálásának legfelső szintű sablonja a következő:

```JSON
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": { ...
},
"variables": { ...
},
"resources": [
{
    "name": "[parameters('dataFactoryName')]",
    "apiVersion": "[variables('apiVersion')]",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "westus",
    "resources": [
    { "type": "linkedservices",
        ...
    },
    {"type": "datasets",
        ...
    },
    {"type": "dataPipelines",
        ...
    }
}
```

### <a name="define-data-factory"></a>Data Factory definiálása
A data factoryt a Resource Manager-sablonban definiálhatja az alábbi minta szerint:

```JSON
"resources": [
{
    "name": "[variables('<mydataFactoryName>')]",
    "apiVersion": "2015-10-01",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "East US"
}
```
A dataFactoryName a következőként van definiálva: "változók".

```JSON
"dataFactoryName": "[concat('<myDataFactoryName>', uniqueString(resourceGroup().id))]",
```

### <a name="define-linked-services"></a>Társított szolgáltatások definiálása

```JSON
"type": "linkedservices",
"name": "[variables('<LinkedServiceName>')]",
"apiVersion": "2015-10-01",
"dependsOn": [ "[variables('<dataFactoryName>')]" ],
"properties": {
    ...
}
```

A telepíteni kívánt társított szolgáltatás JSON-tulajdonságainak részletes ismertetését lásd: [Storage társított szolgáltatás](data-factory-azure-blob-connector.md#azure-storage-linked-service) vagy [számítási társított szolgáltatások](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . A "dependsOn" paraméter a megfelelő adatelőállító nevét adja meg. Az Azure Storage-hoz készült társított szolgáltatás definiálásának példája a következő JSON-definícióban látható:

### <a name="define-datasets"></a>Adatkészletek definiálása

```JSON
"type": "datasets",
"name": "[variables('<myDatasetName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<myDatasetLinkedServiceName>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    ...
}
```
Tekintse át a [támogatott adattárakat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a telepíteni kívánt adatkészlet JSON-tulajdonságaival kapcsolatos részletekért. Vegye figyelembe, hogy a "dependsOn" paraméter a megfelelő adatgyár és a Storage társított szolgáltatás nevét adja meg. Az Azure Blob Storage adatkészlet-típusának definiálására példa látható a következő JSON-definícióban:

```JSON
"type": "datasets",
"name": "[variables('storageDataset')]",
"dependsOn": [
    "[variables('dataFactoryName')]",
    "[variables('storageLinkedServiceName')]"
],
"apiVersion": "2015-10-01",
"properties": {
"type": "AzureBlob",
"linkedServiceName": "[variables('storageLinkedServiceName')]",
"typeProperties": {
    "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
    "fileName": "[parameters('sourceBlobName')]",
    "format": {
        "type": "TextFormat"
    }
},
"availability": {
    "frequency": "Hour",
    "interval": 1
}
```

### <a name="define-pipelines"></a>Folyamatok definiálása

```JSON
"type": "dataPipelines",
"name": "[variables('<mypipelineName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<inputDatasetLinkedServiceName>')]",
    "[variables('<outputDatasetLinkedServiceName>')]",
    "[variables('<inputDataset>')]",
    "[variables('<outputDataset>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    activities: {
        ...
    }
}
```

A folyamatokat a telepíteni kívánt folyamat és tevékenységek definiálásához használt JSON-tulajdonságokkal kapcsolatos részletekért lásd: [folyamatok definiálása](data-factory-create-pipelines.md#pipeline-json) . Megjegyzés: a "dependsOn" paraméter megadja az adat-előállító nevét, valamint a kapcsolódó társított szolgáltatásokat és adatkészleteket. Az alábbi JSON-kódrészletben látható egy példa arra, hogy az Azure Blob Storageról Azure SQL Databasera másolt adatok másolása megtörténjen:

```JSON
"type": "datapipelines",
"name": "[variables('pipelineName')]",
"dependsOn": [
    "[variables('dataFactoryName')]",
    "[variables('azureStorageLinkedServiceName')]",
    "[variables('azureSqlLinkedServiceName')]",
    "[variables('blobInputDatasetName')]",
    "[variables('sqlOutputDatasetName')]"
],
"apiVersion": "2015-10-01",
"properties": {
    "activities": [
    {
        "name": "CopyFromAzureBlobToAzureSQL",
        "description": "Copy data frm Azure blob to Azure SQL",
        "type": "Copy",
        "inputs": [
            {
                "name": "[variables('blobInputDatasetName')]"
            }
        ],
        "outputs": [
            {
                "name": "[variables('sqlOutputDatasetName')]"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
            },
            "translator": {
                "type": "TabularTranslator",
                "columnMappings": "Column0:FirstName,Column1:LastName"
            }
        },
        "Policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 3,
            "timeout": "01:00:00"
        }
    }
    ],
    "start": "2016-10-03T00:00:00Z",
    "end": "2016-10-04T00:00:00Z"
}
```
## <a name="parameterizing-data-factory-template"></a>Parameterizing Data Factory sablon
A parameterizing ajánlott eljárásai: [ajánlott eljárások Azure Resource Manager sablonok létrehozásához](../../azure-resource-manager/templates/template-best-practices.md). Általánosságban elmondható, hogy a paraméterek használatának kisebbnek kell lennie, különösen, ha a változók használhatók. Csak a következő helyzetekben adja meg a paramétereket:

* A beállítások a környezettől függően változnak (például: fejlesztés, tesztelés és éles környezetben)
* Titkok (például jelszavak)

Ha a Azure Data Factory entitások sablonok használatával történő telepítésekor [Azure Key Vault](../../key-vault/general/overview.md) titkot kell lekérnie, adja meg a **Key vaultot** és a **titkos** kulcsot a következő példában látható módon:

```JSON
"parameters": {
    "storageAccountKey": {
        "reference": {
            "keyVault": {
                "id":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.KeyVault/vaults/<keyVaultName>",
             },
            "secretName": "<secretName>"
           },
       },
       ...
}
```

> [!NOTE]
> Bár a meglévő adatüzemekhez tartozó sablonok exportálása jelenleg még nem támogatott, az működik.
>
>
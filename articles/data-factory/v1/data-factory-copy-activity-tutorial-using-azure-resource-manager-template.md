---
title: 'Oktatóanyag: folyamat létrehozása Resource Manager-sablonnal '
description: Ebben az oktatóanyagban egy egyszerű Azure Data Factory-folyamatot fog létrehozni egy Azure Resource Manager-sablon segítségével. Ez a folyamat az Azure Blob Storage-ból Azure SQL Databaseba másolja az adatait.
author: linda33wj
ms.service: data-factory
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 18e8965a152deb6393d7e3b63ea22994484e100c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100377244"
---
# <a name="tutorial-use-azure-resource-manager-template-to-create-a-data-factory-pipeline-to-copy-data"></a>Oktatóanyag: Azure Resource Manager-sablonok használata Data Factory-folyamatok létrehozására adatmásolás céljából 
> [!div class="op_single_selector"]
> * [Áttekintés és előfeltételek](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Másolás varázsló](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-sablon](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg a [másolási tevékenység oktatóanyagát](../quickstart-create-data-factory-dot-net.md). 

Az oktatóanyagból megtudhatja, hogyan hozhat létre Azure-beli adat-előállítókat Azure Resource Manager-sablonokkal. Az oktatóanyagban található adatfeldolgozási folyamat adatokat másol egy forrásadattárból egy céladattárba. A bemeneti adatokat nem alakítja át kimeneti adatok létrehozásához. Az adatok Azure Data Factory használatával történő átalakításának útmutatásáért olvassa el [az adatok Hadoop-fürt segítségével történő átalakítására szolgáló folyamat létrehozását ismertető oktatóanyagot](data-factory-build-your-first-pipeline.md).

Az oktatóanyag segítségével egyetlen tevékenységgel (másolási tevékenységgel) rendelkező folyamatot hozhat létre. A másolási tevékenység adatokat másol a forrásadattárból egy támogatott fogadó adattárba. A forrásként és fogadóként támogatott adattárak listájáért lásd: [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats). A tevékenységet egy globálisan elérhető szolgáltatás működteti, amely biztonságos, megbízható és skálázható módon másolja az adatokat a különböző adattárak között. További információ a másolási tevékenységről: [adatáthelyezési tevékenységek](data-factory-data-movement-activities.md).

Egy folyamathoz több tevékenység is tartozhat. Ezenkívül össze is fűzhet két tevékenységet (egymás után futtathatja őket), ha az egyik tevékenység kimeneti adatkészletét a másik tevékenység bemeneti adatkészleteként állítja be. További információ az [egy folyamaton belüli több tevékenységről](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) szóló témakörben található. 

> [!NOTE] 
> Az oktatóanyagban található adatfeldolgozási folyamat adatokat másol egy forrásadattárból egy céladattárba. Az adatok Azure Data Factory használatával történő átalakításának útmutatásáért olvassa el [az adatok Hadoop-fürt segítségével történő átalakítására szolgáló folyamat létrehozását ismertető oktatóanyagot](data-factory-build-your-first-pipeline.md). 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Tekintse meg [Az oktatóanyag áttekintése és előfeltételei](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című cikket, és hajtsa végre az **előfeltételként** felsorolt lépéseket.
* Kövesse a [How to install and configure Azure PowerShell](/powershell/azure/) (Az Azure PowerShell telepítése és konfigurálása) című cikkben foglalt utasításokat az Azure PowerShell telepítéséhez a számítógépre. Ebben az oktatóanyagban Data Factory-entitásokat fogunk üzembe helyezni a PowerShell-lel. 
* (Nem kötelező) Az Azure Resource Manager-sablonokkal kapcsolatban az [Azure Resource Manager-sablonok készítése](../../azure-resource-manager/templates/template-syntax.md) című cikkben tájékozódhat bővebben.

## <a name="in-this-tutorial"></a>Az oktatóanyag tartalma
Ebben az oktatóanyagban az alábbi Data Factory-entitásokkal rendelkező adat-előállítót hozza létre:

| Entitás | Leírás |
| --- | --- |
| Azure Storage társított szolgáltatás |Társítja az Azure Storage-fiókot a data factoryhoz. Az Azure Storage a forrás adattár, a Azure SQL Database pedig az oktatóanyag másolási tevékenységének fogadó adattára. Meghatározza a másolási tevékenység bemeneti adatait tartalmazó tárfiókot. |
| Azure SQL Database társított szolgáltatás |Összekapcsolja Azure SQL Database adatbázisát az adatgyárban. Meghatározza azt az adatbázist, amely a másolási tevékenység kimeneti adatokat tárolja. |
| Azure Blob bemeneti adatkészlet |Az Azure Storage társított szolgáltatásra vonatkozik. A társított szolgáltatás egy Azure Storage-fiókra hivatkozik, az Azure-blob adatkészlet pedig meghatározza a bemeneti adatokat tartalmazó tárban lévő tárolót, mappát és fájlnevet. |
| Az Azure SQL kimeneti adatkészlete |Az Azure SQL társított szolgáltatásra vonatkozik. Az Azure SQL társított szolgáltatás egy logikai SQL-kiszolgálóra hivatkozik, az Azure SQL-adatkészlet pedig meghatározza a kimeneti adatokat tartalmazó tábla nevét. |
| Adatfolyamat |A folyamat egyetlen másolás típusú tevékenységgel rendelkezik, amely Azure Blob-adatkészletet bemenetként, az Azure SQL-adatkészletet pedig kimenetként használja. A másolási tevékenység egy Azure-blobból másol át egy Azure SQL Database lévő táblába. |

A data factory egy vagy több folyamattal rendelkezhet. A folyamaton belül egy vagy több tevékenység lehet. Kétféle típusú tevékenység létezik: az [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) és az [adatátalakítási tevékenységek](data-factory-data-transformation-activities.md). Az oktatóanyag során létrehoz egy egyetlen tevékenységgel (másolási tevékenységgel) rendelkező folyamatot.

![Az Azure Blob másolása az Azure SQL Database-be](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/CopyBlob2SqlDiagram.png) 

A következő szakasz a Data Factory-entitások meghatározására szolgáló teljes Resource Manager-sablont ismerteti, így gyorsan végighaladhat az oktatóanyagon, és tesztelheti a sablont. Az egyes Data Factory-entitások meghatározásának megértéséhez tekintse meg a [Data Factory-entitások a sablonban](#data-factory-entities-in-the-template) szakaszt.

## <a name="data-factory-json-template"></a>Data Factory JSON-sablon
A data factory meghatározásához szükséges legfelső szintű Resource Manager-sablon a következő: 

```json
{
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
                { ... },
                { ... },
                { ... },
                { ... }
            ]
        }
    ]
}
```
Hozzon létre egy JSON-fájlt **ADFCopyTutorialARM.json** néven a **C:\ADFGetStarted** mappában az alábbi tartalommal:

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
      "storageAccountName": { "type": "string", "metadata": { "description": "Name of the Azure storage account that contains the data to be copied." } },
      "storageAccountKey": { "type": "securestring", "metadata": { "description": "Key for the Azure storage account." } },
      "sourceBlobContainer": { "type": "string", "metadata": { "description": "Name of the blob container in the Azure Storage account." } },
      "sourceBlobName": { "type": "string", "metadata": { "description": "Name of the blob in the container that has the data to be copied to Azure SQL Database table" } },
      "sqlServerName": { "type": "string", "metadata": { "description": "Name of the logical SQL server that will hold the output/copied data." } },
      "databaseName": { "type": "string", "metadata": { "description": "Name of the Azure SQL Database in the logical SQL server." } },
      "sqlServerUserName": { "type": "string", "metadata": { "description": "Name of the user that has access to the logical SQL server." } },
      "sqlServerPassword": { "type": "securestring", "metadata": { "description": "Password for the user." } },
      "targetSQLTable": { "type": "string", "metadata": { "description": "Table in the Azure SQL Database that will hold the copied data." } 
      } 
    },
    "variables": {
      "dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]",
      "azureSqlLinkedServiceName": "AzureSqlLinkedService",
      "azureStorageLinkedServiceName": "AzureStorageLinkedService",
      "blobInputDatasetName": "BlobInputDataset",
      "sqlOutputDatasetName": "SQLOutputDataset",
      "pipelineName": "Blob2SQLPipeline"
    },
    "resources": [
      {
        "name": "[variables('dataFactoryName')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "West US",
        "resources": [
          {
            "type": "linkedservices",
            "name": "[variables('azureStorageLinkedServiceName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureStorage",
              "description": "Azure Storage linked service",
              "typeProperties": {
                "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
              }
            }
          },
          {
            "type": "linkedservices",
            "name": "[variables('azureSqlLinkedServiceName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureSqlDatabase",
              "description": "Azure SQL linked service",
              "typeProperties": {
                "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
              }
            }
          },
          {
            "type": "datasets",
            "name": "[variables('blobInputDatasetName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]",
              "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureBlob",
              "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
              "structure": [
                {
                  "name": "Column0",
                  "type": "String"
                },
                {
                  "name": "Column1",
                  "type": "String"
                }
              ],
              "typeProperties": {
                "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
                "fileName": "[parameters('sourceBlobName')]",
                "format": {
                  "type": "TextFormat",
                  "columnDelimiter": ","
                }
              },
              "availability": {
                "frequency": "Hour",
                "interval": 1
              },
              "external": true
            }
          },
          {
            "type": "datasets",
            "name": "[variables('sqlOutputDatasetName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]",
              "[variables('azureSqlLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureSqlTable",
              "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
              "structure": [
                {
                  "name": "FirstName",
                  "type": "String"
                },
                {
                  "name": "LastName",
                  "type": "String"
                }
              ],
              "typeProperties": {
                "tableName": "[parameters('targetSQLTable')]"
              },
              "availability": {
                "frequency": "Hour",
                "interval": 1
              }
            }
          },
          {
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
              "start": "2017-05-11T00:00:00Z",
              "end": "2017-05-12T00:00:00Z"
            }
          }
        ]
      }
    ]
  }
```

## <a name="parameters-json"></a>Paramétereket tartalmazó JSON-file
Hozzon létre egy **ADFCopyTutorialARM-Parameters.json** elnevezésű JSON-fájlt, amely paramétereket tartalmaz az Azure Resource Manager-sablon számára. 

> [!IMPORTANT]
> Adja meg az Azure Storage-fiók nevét és kulcsát a storageAccountName és a storageAccountKey paraméterek értékeiként.  
> 
> A sqlServerName, a databaseName, a sqlServerUserName és a sqlServerPassword paraméterekhez logikai SQL Servert, adatbázist, felhasználót és jelszót adhat meg.  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        "storageAccountName": { "value": "<Name of the Azure storage account>"    },
        "storageAccountKey": {
            "value": "<Key for the Azure storage account>"
        },
        "sourceBlobContainer": { "value": "adftutorial" },
        "sourceBlobName": { "value": "emp.txt" },
        "sqlServerName": { "value": "<Name of the logical SQL server>" },
        "databaseName": { "value": "<Name of the database>" },
        "sqlServerUserName": { "value": "<Name of the user who has access to the database>" },
        "sqlServerPassword": { "value": "<password for the user>" },
        "targetSQLTable": { "value": "emp" }
    }
}
```

> [!IMPORTANT]
> Készíthet különálló JSON-paraméterfájlokat a fejlesztési, tesztelési és az éles környezetek számára, amelyeket ugyanazzal a Data Factory JSON-sablonnal használhat. Ezekben a környezetekben automatizálhatja a Data Factory-entitások üzembe helyezését egy PowerShell-szkript használatával.  
> 
> 

## <a name="create-data-factory"></a>Data factory létrehozása
1. Indítsa el **Azure PowerShell** és futtassa a következő parancsot:
   * Futtassa a következő parancsot, és adja meg az Azure Portalra való bejelentkezéshez használt felhasználónevet és jelszót.
   
    ```PowerShell
    Connect-AzAccount       
    ```  
   * Futtassa a következő parancsot a fiókhoz tartozó előfizetések megtekintéséhez.
   
    ```PowerShell
    Get-AzSubscription
    ```   
   * Futtassa a következő parancsot a használni kívánt előfizetés kiválasztásához.
    
    ```PowerShell
    Get-AzSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzContext
    ```    
2. Futtassa a következő parancsot a Data Factory-entitásoknak az 1. lépésben létrehozott Resource Manager-sablonnal történő telepítéséhez.

    ```PowerShell   
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFCopyTutorialARM.json -TemplateParameterFile C:\ADFGetStarted\ADFCopyTutorialARM-Parameters.json
    ```

## <a name="monitor-pipeline"></a>Folyamat figyelése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókkal.

1. A bal oldali menüben kattintson az **Adat-előállítók** lehetőségre (vagy) kattintson a **Minden szolgáltatás****INTELLIGENCIA ÉS ELEMZÉS** kategóriájában található **Adat-előállítók** lehetőségre.
   
    ![Adat-előállítók menü](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factories-menu.png)

1. Az **Adat-előállítók** lapon keressen rá az adat-előállítóra (AzureBlobToAzureSQLDatabaseDF). 
   
    ![Adat-előállító keresése](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/search-for-data-factory.png)  

1. Kattintson az Azure Data Factoryre. Megjelenik az adat-előállító kezdőlapja.
   
    ![Az adat-előállító kezdőlapja](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factory-home-page.png)  

1. Az [Adatkészletek és folyamatok figyelése](data-factory-monitor-manage-pipelines.md) című cikk útmutatásait követve monitorozhatja az oktatóanyagban létrehozott folyamatot és adatkészleteket. A Visual Studio jelenleg nem támogatja a Data Factory-folyamatok monitorozását.

1. Ha a szelet **kész** állapotban van, ellenőrizze, hogy az adatmásolás a Azure SQL Database **EMP** táblájába történik-e.

A [Monitor datasets and pipeline](data-factory-monitor-manage-pipelines.md) (Adatkészletek és folyamatok figyelése) című cikkben útmutatást találhat arról, hogy hogyan használhatja az Azure Portal paneleit az oktatóanyagban létrehozott folyamatok és adatkészletek monitorozásához.

A [Monitor and manage Azure Data Factory pipelines using Monitoring App](data-factory-monitor-manage-app.md) (Azure Data Factory-folyamatok figyelése és felügyelete a Monitoring App használatával) című cikkben további információt találhat az adatfolyamatok Monitor & Manage alkalmazással való monitorozásáról.

## <a name="data-factory-entities-in-the-template"></a>Data Factory-entitások a sablonban

### <a name="define-data-factory"></a>Data Factory definiálása

A data factoryt a Resource Manager-sablonban definiálhatja az alábbi minta szerint:

```json
{
  "resources": [
    {
      "name": "[variables('dataFactoryName')]",
      "apiVersion": "2015-10-01",
      "type": "Microsoft.DataFactory/datafactories",
      "location": "West US"
    }
  ]
}
```

A dataFactoryName az alábbi módon van definiálva: 

```json
{
    "dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]"
}
```

Ez az erőforráscsoport-azonosítón alapuló egyedi sztring.

### <a name="defining-data-factory-entities"></a>Data Factory-entitások definiálása

Az alábbi Data Factory-entitások a JSON-sablonban vannak definiálva: 

1. [Azure Storage társított szolgáltatás](#azure-storage-linked-service)
2. [Azure SQL Database társított szolgáltatás](#azure-sql-database-linked-service)
3. [Azure Blob-adatkészlet](#azure-blob-dataset)
4. [Azure SQL-adatkészlet](#azure-sql-dataset)
5. [Másolási tevékenységgel rendelkező adatfolyamat](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Azure Storage társított szolgáltatás

Az AzureStorageLinkedService az Azure Storage-fiókot társítja az adat-előállítóval. Az [Előfeltételek](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)részeként létrehozott egy tárolót, és feltöltötte az adattárat ebbe a Storage-fiókba. Ebben a szakaszban megadhatja az Azure-tárfiók nevét és kulcsát. Az Azure Storage társított szolgáltatás definiálásához használt JSON-tulajdonságokkal kapcsolatos információkért tekintse meg az [Azure Storage társított szolgáltatás](data-factory-azure-blob-connector.md#azure-storage-linked-service) című szakaszt. 

```json
{
    "type": "linkedservices",
    "name": "[variables('azureStorageLinkedServiceName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureStorage",
        "description": "Azure Storage linked service",
        "typeProperties": {
            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    }
}
```

A connectionString a storageAccountName és storageAccountKey paramétereket használja. A paraméterek értékei a konfigurációs fájlok használatával adhatók át. A definíció a sablonban definiált változókat is használja: azureStorageLinkedService és dataFactoryName. 

#### <a name="azure-sql-database-linked-service"></a>Azure SQL Database társított szolgáltatás

A AzureSqlLinkedService összekapcsolja az adatbázist Azure SQL Database az adatgyárban. A blobtárolóból másolt adatokat a rendszer ebben az adatbázisban tárolja. Az [előfeltételek](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) részeként létrehozta az emp táblát az adatbázisban. Ebben a szakaszban a logikai SQL-kiszolgáló nevét, az adatbázis nevét, a felhasználónevet és a felhasználói jelszót kell megadnia. Az Azure SQL társított szolgáltatás definiálásához használt JSON-tulajdonságokkal kapcsolatos információkért tekintse meg az [Azure SQL társított szolgáltatás](data-factory-azure-sql-connector.md#linked-service-properties) című szakaszt.  

```json
{
    "type": "linkedservices",
    "name": "[variables('azureSqlLinkedServiceName')]",
    "dependsOn": [
      "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
      "type": "AzureSqlDatabase",
      "description": "Azure SQL linked service",
      "typeProperties": {
        "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
      }
    }
}
```

A connectionString az sqlServerName, databaseName, sqlServerUserName és sqlServerPassword paramétereket használja, amelyeknek az értékei a konfigurációs fájlok használatával adhatók át. A definíció a sablon következő változóit is használja: azureSqlLinkedServiceName, dataFactoryName.

#### <a name="azure-blob-dataset"></a>Azure Blob-adatkészlet
Az Azure Storage társított szolgáltatása határozza meg azt a kapcsolati sztringet, amelyet futtatáskor a Data Factory szolgáltatás az Azure Storage-fiók csatlakoztatásához használ. Az Azure Blob-adatkészletek definíciójában adhatja meg a bemeneti adatokat tartalmazó blobtároló, mappa és fájl nevét. Az Azure Blob-adatkészletek definiálásához használt JSON-tulajdonságokkal kapcsolatos információkért tekintse meg az [Azure Blob-adatkészlet tulajdonságai](data-factory-azure-blob-connector.md#dataset-properties) című szakaszt. 

```json
{
    "type": "datasets",
    "name": "[variables('blobInputDatasetName')]",
    "dependsOn": [
      "[variables('dataFactoryName')]",
      "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureBlob",
          "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
        "structure": [
        {
              "name": "Column0",
              "type": "String"
        },
        {
              "name": "Column1",
              "type": "String"
        }
          ],
          "typeProperties": {
            "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
            "fileName": "[parameters('sourceBlobName')]",
            "format": {
                  "type": "TextFormat",
                  "columnDelimiter": ","
            }
          },
          "availability": {
            "frequency": "Hour",
            "interval": 1
          },
          "external": true
    }
}
```

#### <a name="azure-sql-dataset"></a>Azure SQL-adatkészlet
Itt adhatja meg az Azure Blob Storage-ból másolt adatok tárolására szolgáló Azure SQL Databaseban található tábla nevét. Az Azure SQL-adatkészletek definiálásához használt JSON-tulajdonságokkal kapcsolatos információkért tekintse meg az [Azure SQL-adatkészlet tulajdonságai](data-factory-azure-sql-connector.md#dataset-properties) című szakaszt. 

```json
{
    "type": "datasets",
    "name": "[variables('sqlOutputDatasetName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
          "[variables('azureSqlLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
          "type": "AzureSqlTable",
          "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
          "structure": [
        {
              "name": "FirstName",
              "type": "String"
        },
        {
              "name": "LastName",
              "type": "String"
        }
          ],
          "typeProperties": {
            "tableName": "[parameters('targetSQLTable')]"
          },
          "availability": {
            "frequency": "Hour",
            "interval": 1
          }
    }
}
```

#### <a name="data-pipeline"></a>Adatfolyamat
Definiálhat egy folyamatot, amely adatokat másol az Azure Blob-adatkészletből az Azure SQL-adatkészletbe. A példában található folyamat definiálásához használt JSON-elemek leírásához tekintse meg [A folyamat JSON-fájlja](data-factory-create-pipelines.md#pipeline-json) című szakaszt. 

```json
{
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
          "start": "2017-05-11T00:00:00Z",
          "end": "2017-05-12T00:00:00Z"
    }
}
```

## <a name="reuse-the-template"></a>A sablon ismételt felhasználása
Az oktatóanyagban létrehozott egy sablont a Data Factory-entitások definiálásához, illetve egy másikat a paraméterek értékeinek átadásához. A folyamat egy Azure Storage-fiókból másolja át az adatait, hogy Azure SQL Database a paramétereken keresztül megadott értéket. Ha ugyanazt a sablont szeretné használni a Data Factory-entitások különböző környezetekben történő üzembe helyezéséhez, hozzon létre egy paraméterfájlt az egyes környezetekhez, és használja azt az adott környezetben történő üzembe helyezéskor.     

Példa:  

```PowerShell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Dev.json
```
```PowerShell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Test.json
```
```PowerShell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Production.json
```

Megfigyelheti, hogy az első parancs a fejlesztőkörnyezet, a második a tesztkörnyezet, a harmadik pedig az éles környezet paraméterfájlját használja.  

Emellett ismétlődő feladatok elvégzéséhez is újból felhasználhatja a sablont. Ilyen eset például, ha több olyan, egy vagy több folyamattal rendelkező adat-előállítót is létre kell hoznia, amelyek ugyanazt a logikát alkalmazzák, de az egyes adat-előállítók különböző Storage- és SQL Database-fiókokat használnak. Ebben a forgatókönyvben ugyanazt a sablont használja ugyanabban a környezetben (fejlesztői, teszt vagy éles) különböző paraméterfájlokkal az adat-előállítók létrehozásához.   

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban az Azure Blob Storage-t forrás adattárként használta, és a másolási művelet során Azure SQL Database célként megadott adattárként. Az alábbi táblázatban a másolási tevékenység által támogatott forrásadattárak és céladattárak listája látható: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

A táblázatban lévő adattárak hivatkozására kattintva megismerheti az adattárakba és az adattárakból történő adatmásolás módszereit.

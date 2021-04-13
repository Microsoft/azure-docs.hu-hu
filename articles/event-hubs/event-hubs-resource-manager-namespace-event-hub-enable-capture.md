---
title: Event hub létrehozása az engedélyezve rögzítéssel – Azure Event Hubs | Microsoft Docs
description: Azure Event Hubs-névtér létrehozása egy eseményközponttal és a Rögzítés funkció engedélyezése az Azure Resource Manager sablonjának használatával
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 1b7ce00c1db0866b1965ed6d1bba31aa28f8bc24
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304936"
---
# <a name="create-a-namespace-with-event-hub-and-enable-capture-using-a-template"></a>Névtér létrehozása egy eseményközponttal és a Rögzítés funkció engedélyezése sablon használatával

Ez a cikk ismerteti egy olyan Azure Resource Manager-sablon használatát, amely egy [Event Hubs](./event-hubs-about.md)-névteret hoz létre egy Event Hubs-példánnyal, valamint leírja az eseményközpont [Capture funkciójának](event-hubs-capture-overview.md) engedélyezését is. A cikk leírja továbbá, hogyan kell meghatározni, hogy mely erőforrások lesznek üzembe helyezve, és hogyan kell meghatározni az üzembe helyezés végrehajtásakor megadandó paramétereket. Ez a sablont használhatja a saját környezeteiben, vagy testre is szabhatja a saját követelményeinek megfelelően.

Ez a cikk azt is bemutatja, hogyan adhatja meg, hogy a rendszer az Azure Storage-blobokban vagy egy Azure Data Lake Store-ban rögzítse az eseményeket, az Ön által kiválasztott célhely alapján.

A sablonok létrehozásáról további információkat az [Authoring Azure Resource Manager templates][Authoring Azure Resource Manager templates] (Azure Resource Manager-sablonok készítése) című témakörben talál. A sablonban használandó JSON-szintaxis és-tulajdonságok megtekintéséhez lásd: [Microsoft. EventHub-erőforrástípusok](/azure/templates/microsoft.eventhub/allversions).

További információk az Azure-erőforrások elnevezési szabályainak mintáiról és gyakorlati megoldásairól: [Az Azure-erőforrások elnevezési szabályai][Azure Resources naming conventions].

Az összes sablon eléréséhez kattintson az alábbi GitHub-hivatkozásokra:

- [Eseményközpont és a Rögzítés tárolóban sablon engedélyezése][Event Hub and enable Capture to Storage template] 
- [Eseményközpont és a Rögzítés Azure Data Lake Store-ban sablon engedélyezése][Event Hub and enable Capture to Azure Data Lake Store template]

> [!NOTE]
> A legújabb sablonokért keresse fel az [Azure-gyorssablonok][Azure Quickstart Templates] gyűjteményt, és keressen az Event Hubs kifejezésre.
> 
> 

## <a name="what-will-you-deploy"></a>Mit fog üzembe helyezni?

Ezzel a sablonnal egy Event Hubs-névteret helyez üzembe eseményközponttal, továbbá engedélyezi az [az Event Hubs Rögzítés funkcióját](event-hubs-capture-overview.md). Az Event Hubs Capture funkciója lehetővé teszi a streamelt Event Hubs-adatok automatikus továbbítását az Azure Blob Storage-ba vagy az Azure Data Lake Store-ba egy megadott időtartamon belül vagy az Ön által kiválasztott méretegységekben. Kattintson az alábbi gombra az Event Hubs Azure Storage-ba való rögzítésének engedélyezéséhez:

[![Üzembe helyezés az Azure-ban](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-capture%2Fazuredeploy.json)

Kattintson az alábbi gombra az Event Hubs Azure Data Lake Store-ba való rögzítésének engedélyezéséhez:

[![Üzembe helyezés az Azure-ban](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-capture-for-adls%2Fazuredeploy.json)

## <a name="parameters"></a>Paraméterek

Az Azure Resource Managerrel meghatározhatja a sablon üzembe helyezésekor megadandó értékek paramétereit. A sablonban található egy `Parameters` nevű rész, amely magába foglalja az összes paraméterértéket. Azokhoz az értékekhez adjon meg paramétert, amelyek az üzembe helyezendő projekt vagy az üzembe helyezési környezet alapján változhatnak. Ne adjon meg olyan paramétereket olyan értékhez, amelyek nem változnak. A sablonban minden egyes paraméterérték az üzembe helyezendő erőforrások megadásához lesz felhasználva.

A sablon a következő paramétereket adja meg.

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

A létrehozandó Event Hubs-névtér neve.

```json
"eventHubNamespaceName":{  
     "type":"string",
     "metadata":{  
         "description":"Name of the EventHub namespace"
      }
}
```

### <a name="eventhubname"></a>eventHubName

Az Event Hubs-névtérben létrehozott eseményközpont neve.

```json
"eventHubName":{  
    "type":"string",
    "metadata":{  
        "description":"Name of the event hub"
    }
}
```

### <a name="messageretentionindays"></a>messageRetentionInDays

A napok száma, amíg az üzenetek meg lesznek őrizve az eseményközpontban. 

```json
"messageRetentionInDays":{
    "type":"int",
    "defaultValue": 1,
    "minValue":"1",
    "maxValue":"7",
    "metadata":{
       "description":"How long to retain the data in event hub"
     }
 }
```

### <a name="partitioncount"></a>partitionCount

Az eseményközpontban létrehozandó partíciók száma.

```json
"partitionCount":{
    "type":"int",
    "defaultValue":2,
    "minValue":2,
    "maxValue":32,
    "metadata":{
        "description":"Number of partitions chosen"
    }
 }
```

### <a name="captureenabled"></a>captureEnabled

A Rögzítés funkció engedélyezése az eseményközpontban.

```json
"captureEnabled":{
    "type":"string",
    "defaultValue":"true",
    "allowedValues": [
    "false",
    "true"],
    "metadata":{
        "description":"Enable or disable the Capture for your event hub"
    }
 }
```
### <a name="captureencodingformat"></a>captureEncodingFormat

Az eseményadat szerializálásához megadott kódolási formátum.

```json
"captureEncodingFormat":{
    "type":"string",
    "defaultValue":"Avro",
    "allowedValues":[
    "Avro"],
    "metadata":{
        "description":"The encoding format in which Capture serializes the EventData"
    }
}
```

### <a name="capturetime"></a>captureTime

Az az időintervallum, amelyben az Event Hubs Capture elkezdi az adatok rögzítését.

```json
"captureTime":{
    "type":"int",
    "defaultValue":300,
    "minValue":60,
    "maxValue":900,
    "metadata":{
         "description":"The time window in seconds for the capture"
    }
}
```

### <a name="capturesize"></a>captureSize
Az a méretegység, amelynek elérésekor a Capture funkció elkezdi az adatok rögzítését.

```json
"captureSize":{
    "type":"int",
    "defaultValue":314572800,
    "minValue":10485760,
    "maxValue":524288000,
    "metadata":{
        "description":"The size window in bytes for capture"
    }
}
```

### <a name="capturenameformat"></a>captureNameFormat

Az Event Hubs Capture által használt névformátum az Avro-fájlok írásakor. Vegye figyelembe, hogy a Capture névformátumának tartalmaznia kell a következő mezőket: `{Namespace}`, `{EventHub}`, `{PartitionId}`, `{Year}`, `{Month}`, `{Day}`, `{Hour}`, `{Minute}` és `{Second}`. Ezek a mezők bármilyen sorrend szerint rendezhetők, elválasztó karakterekkel vagy azok nélkül.
 
```json
"captureNameFormat": {
      "type": "string",
      "defaultValue": "{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}",
      "metadata": {
        "description": "A Capture Name Format must contain {Namespace}, {EventHub}, {PartitionId}, {Year}, {Month}, {Day}, {Hour}, {Minute} and {Second} fields. These can be arranged in any order with or without delimeters. E.g.  Prod_{EventHub}/{Namespace}\\{PartitionId}_{Year}_{Month}/{Day}/{Hour}/{Minute}/{Second}"
      }
    }
  
```

### <a name="apiversion"></a>apiVersion

A sablon API-verziója.

```json
 "apiVersion":{  
    "type":"string",
    "defaultValue":"2017-04-01",
    "metadata":{  
        "description":"ApiVersion used by the template"
    }
 }
```

Ha az Azure Storage-ot választja célhelyként, használja a következő paramétereket.

### <a name="destinationstorageaccountresourceid"></a>destinationStorageAccountResourceId

A Rögzítés használatához egy Azure Storage-fiókhoz tartozó erőforrás-azonosító szükséges, amellyel engedélyezheti a rögzítést a választott Storage-fiókban.

```json
 "destinationStorageAccountResourceId":{
    "type":"string",
    "metadata":{
        "description":"Your existing Storage account resource ID where you want the blobs be captured"
    }
 }
```

### <a name="blobcontainername"></a>blobContainerName

A blobtároló, amelyben rögzíti az eseményadatokat.

```json
 "blobContainerName":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage container in which you want the blobs captured"
    }
}
```

A következő paraméterek használata esetén válassza az 1. generációs Azure Data Lake Store a cél lehetőséget. Az engedélyeket arra a Data Lake Store-útvonalra kell beállítania, ahol az eseményeket rögzíteni kívánja. Az engedélyek megadásával kapcsolatban lásd: [adatgyűjtés az 1. generációs Azure Data Lake Storage](event-hubs-capture-enable-through-portal.md#capture-data-to-azure-data-lake-storage-gen-1).

### <a name="subscriptionid"></a>subscriptionId

Az Event Hubs-névtér és az Azure Data Lake Store előfizetés-azonosítója. Mindkét erőforráshoz azonos előfizetés-azonosítónak kell tartoznia.

```json
"subscriptionId": {
    "type": "string",
    "metadata": {
        "description": "Subscription ID of both Azure Data Lake Store and Event Hubs namespace"
     }
 }
```

### <a name="datalakeaccountname"></a>dataLakeAccountName

A rögzített események Azure Data Lake Store-neve.

```json
"dataLakeAccountName": {
    "type": "string",
    "metadata": {
        "description": "Azure Data Lake Store name"
    }
}
```

### <a name="datalakefolderpath"></a>dataLakeFolderPath

A rögzített események célmappájának elérési útja. Ez az a mappa a Data Lake Store-fiókban, amelybe a rendszer leküldi az eseményeket a rögzítési művelet során. A mappa engedélyeinek beállításáról lásd [az Event Hubsból származó adatok Azure Data Lake Store segítségével történő rögzítését ismertető](../data-lake-store/data-lake-store-archive-eventhub-capture.md) cikket.

```json
"dataLakeFolderPath": {
    "type": "string",
    "metadata": {
        "description": "Destination capture folder path"
    }
}
```

## <a name="resources-to-deploy-for-azure-storage-as-destination-to-captured-events"></a>Az Azure Storage-ban történő eseményrögzítéshez üzembe helyezendő erőforrások

Létrehoz egy **EventHub** típusú névteret egy eseményközponttal, valamint engedélyezi az Azure Blob Storage-ba való rögzítést is.

```json
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('eventHubNamespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources": [
    {
      "apiVersion": "2017-04-01",
      "name": "[parameters('eventHubNamespaceName')]",
      "type": "Microsoft.EventHub/Namespaces",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "isAutoInflateEnabled": "true",
        "maximumThroughputUnits": "7"
      },
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('eventHubName')]",
          "type": "EventHubs",
          "dependsOn": [
            "[concat('Microsoft.EventHub/namespaces/', parameters('eventHubNamespaceName'))]"
          ],
          "properties": {
            "messageRetentionInDays": "[parameters('messageRetentionInDays')]",
            "partitionCount": "[parameters('partitionCount')]",
            "captureDescription": {
              "enabled": "true",
              "skipEmptyArchives": false,
              "encoding": "[parameters('captureEncodingFormat')]",
              "intervalInSeconds": "[parameters('captureTime')]",
              "sizeLimitInBytes": "[parameters('captureSize')]",
              "destination": {
                "name": "EventHubArchive.AzureBlockBlob",
                "properties": {
                  "storageAccountResourceId": "[parameters('destinationStorageAccountResourceId')]",
                  "blobContainer": "[parameters('blobContainerName')]",
                  "archiveNameFormat": "[parameters('captureNameFormat')]"
                }
              }
            }
          }

        }
      ]
    }
  ]
```

## <a name="resources-to-deploy-for-azure-data-lake-store-as-destination"></a>Az Azure Data Lake Store célhelyként való használatához üzembe helyezendő erőforrások

Létrehoz egy **EventHub** típusú névteret egy eseményközponttal, valamint engedélyezi az Azure Data Lake Store-ba való rögzítést is.

```json
 "resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('eventHubName')]",
                        "captureDescription": {
                            "enabled": "true",
                            "skipEmptyArchives": false,
                            "encoding": "[parameters('archiveEncodingFormat')]",
                            "intervalInSeconds": "[parameters('captureTime')]",
                            "sizeLimitInBytes": "[parameters('captureSize')]",
                            "destination": {
                                "name": "EventHubArchive.AzureDataLake",
                                "properties": {
                                    "DataLakeSubscriptionId": "[parameters('subscriptionId')]",
                                    "DataLakeAccountName": "[parameters('dataLakeAccountName')]",
                                    "DataLakeFolderPath": "[parameters('dataLakeFolderPath')]",
                                    "ArchiveNameFormat": "[parameters('captureNameFormat')]"
                                }
                            }
                        }
                    }
                }
            ]
        }
    ]
```

> [!NOTE]
> Engedélyezheti vagy letilthatja az üres fájlok kibocsátását, ha a rögzítési időszak során nem történik esemény a **skipEmptyArchives** tulajdonság használatával. 

## <a name="commands-to-run-deployment"></a>Az üzembe helyezést futtató parancsok

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Helyezze üzembe a sablont az Azure Storage-ba történő Event Hubs-rögzítés engedélyezéséhez:
 
```powershell
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json
```

Helyezze üzembe a sablont az Azure Data Lake Store-ba történő Event Hubs-rögzítés engedélyezéséhez:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture-for-adls/azuredeploy.json
```

## <a name="azure-cli"></a>Azure CLI

Az Azure Blob Storage mint célhely:

```azurecli
az deployment group create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json][]
```

Az Azure Data Lake Store mint célhely:

```azurecli
az deployment group create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture-for-adls/azuredeploy.json][]
```

## <a name="next-steps"></a>Következő lépések

Az Event Hubs Rögzítés funkcióját az [Azure Portal](https://portal.azure.com) segítségével is konfigurálhatja. További információért tekintse meg az [Enable Event Hubs Capture using the Azure portal](event-hubs-capture-enable-through-portal.md) (Az Event Hubs Rögzítés funkciójának engedélyezése az Azure Portalon) című témakört.

Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs áttekintése](./event-hubs-about.md)
* [Eseményközpont létrehozása](event-hubs-create.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.yml)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Azure Resources naming conventions]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging
[Event hub and enable Capture to Storage template]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-capture
[Event hub and enable Capture to Azure Data Lake Store template]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-capture-for-adls

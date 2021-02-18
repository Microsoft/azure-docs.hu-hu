---
title: Azure Monitor exportált adatok lekérdezése az Azure Adatkezelő használatával (előzetes verzió)
description: Az Azure Adatkezelő segítségével lekérdezheti a Log Analytics-munkaterületről egy Azure Storage-fiókba exportált adatok lekérdezését.
ms.subservice: logs
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 5a26e5d045be78772ee5dc940fcfecd3cfde9dc7
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100618518"
---
# <a name="query-exported-data-from-azure-monitor-using-azure-data-explorer-preview"></a>Azure Monitor exportált adatok lekérdezése az Azure Adatkezelő használatával (előzetes verzió)
Az adatok a Azure Monitorból egy Azure Storage-fiókba való exportálása lehetővé teszi az alacsony költségeket és a naplók más régiókban való újrafoglalását. Az Azure Adatkezelő segítségével lekérdezheti az Log Analytics-munkaterületekről exportált adatok lekérdezését. A konfigurálást követően a munkaterületekről egy Azure Storage-fiókba küldendő támogatott táblák az Azure Adatkezelő adatforrásként lesznek elérhetők.

A folyamat a következő: 

1.  Adatok exportálása Log Analytics munkaterületről az Azure Storage-fiókba.
2.  Hozzon létre külső táblát az Azure Adatkezelő-fürtben, és rendelje az adattípusokhoz.
3.  Adatok lekérdezése az Azure Adatkezelőból.

:::image type="content" source="media/azure-data-explorer-query-storage/exported-data-query.png" alt-text="Az Azure Adatkezelő exportált Adatlekérdezési folyamat.":::



## <a name="send-data-to-azure-storage"></a>Adatküldés az Azure Storage szolgáltatásba
Azure Monitor naplókat az alábbi lehetőségek bármelyikével exportálhatja egy Azure Storage-fiókba.

- Az Log Analytics munkaterületről származó összes adatok Azure Storage-fiókba vagy Event hub-ba való exportálásához használja Azure Monitor naplók Log Analytics munkaterület adatexportálási szolgáltatását. Lásd: [log Analytics munkaterület-adatexportálás Azure monitorban (előzetes verzió)](../platform/logs-data-export.md)
- Ütemezett exportálás egy logikai alkalmazás használatával a log lekérdezésből. Ez hasonló az adatexportálási szolgáltatáshoz, de lehetővé teszi, hogy szűrt vagy összesített adatokat küldjön az Azure Storage-ba. Ez a metódus azonban a [naplózási lekérdezési korlátokra](../service-limits.md#log-analytics-workspaces)  vonatkozik, lásd: [log Analytics munkaterületről származó adatok archiválása az Azure Storage-ba a Logic App használatával](../platform/logs-export-logic-app.md).
- Egyszeri exportálás logikai alkalmazás használatával. [A Logic apps és a Power automatizálás Azure monitor naplók összekötője](../platform/logicapp-flow-connector.md)című témakörben talál.
- Egyszeri exportálás a helyi gépre PowerShell-parancsfájl használatával. Lásd: [meghívás-AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport).

> [!TIP]
> Használhat meglévő Azure Adatkezelő-fürtöt, vagy létrehozhat egy új dedikált fürtöt a szükséges konfigurációkkal.

## <a name="create-an-external-table-located-in-azure-blob-storage"></a>Az Azure Blob Storage-ban található külső tábla létrehozása
[Külső táblák](/azure/data-explorer/kusto/query/schema-entities/externaltables) használata az Azure adatkezelő Azure Storage-fiókhoz való összekapcsolásához. A külső tábla olyan Kusto séma-entitás, amely Kusto-adatbázison kívül tárolt adatokra hivatkozik. A táblákhoz hasonlóan a külső táblák is jól definiált sémával rendelkeznek. A táblákkal ellentétben a rendszer az Kusto-fürtön kívül tárolja és felügyeli az adatmennyiséget. Az előző szakaszból exportált adatok JSON-sorokban lesznek mentve.

A hivatkozás létrehozásához az exportált tábla sémájának kell megegyeznie. Használja az [GetSchema](/azure/data-explorer/kusto/query/getschemaoperator) operátort a log Analyticsból az adatok lekéréséhez, beleértve a tábla oszlopait és adattípusait is.

:::image type="content" source="media\azure-data-explorer-query-storage\exported-data-map-schema.jpg" alt-text="Log Analytics tábla sémája.":::

Mostantól a kimenet használatával létrehozhatja a Kusto-lekérdezést a külső tábla létrehozásához.
Az [Azure Storage vagy Azure Data Lake külső tábláinak létrehozása és módosítása](/azure/data-explorer/kusto/management/external-tables-azurestorage-azuredatalake)című témakör útmutatását követve hozzon létre egy külső táblát JSON formátumban, majd futtassa a lekérdezést az Azure adatkezelő-adatbázisból.

>[!NOTE]
>A külső tábla létrehozása két folyamatból épül fel. Az első a külső táblát hozza létre, a második pedig a leképezést hozza létre.

A következő PowerShell-szkript hozza létre a tábla és a leképezés [létrehozási](/azure/data-explorer/kusto/management/external-tables-azurestorage-azuredatalake#create-external-table-mapping) parancsait.

```powershell
PARAM(
    $resourcegroupname, #The name of the Azure resource group
    $TableName, # The Log Analytics table you wish to convert to external table
    $MapName, # The name of the map
    $subscriptionId, #The ID of the subscription
    $WorkspaceId, # The Log Analytics WorkspaceId
    $WorkspaceName, # The Log Analytics workspace name
    $BlobURL, # The Blob URL where the data is saved
    $ContainerAccessKey, # The blob container Access Key (Option to add a SAS url)
    $ExternalTableName = $null # The External Table name, null to use the same name
)

if($null -eq $ExternalTableName) {
    $ExternalTableName = $TableName
}

$query = $TableName + ' | getschema | project ColumnName, DataType'

$output = (Invoke-AzOperationalInsightsQuery -WorkspaceId $WorkspaceId -Query $query).Results

$FirstCommand = @()
$SecondCommand = @()

foreach ($record in $output) {
    if ($record.DataType -eq 'System.DateTime') {
        $dataType = 'datetime'
    } else {
        $dataType = 'string'
    }
    $FirstCommand += $record.ColumnName + ":" + "$dataType" + ","
    $SecondCommand += "{`"column`":" + "`"" + $record.ColumnName + "`"," + "`"datatype`":`"$dataType`",`"path`":`"$." + $record.ColumnName + "`"},"
}
$schema = ($FirstCommand -join '') -replace ',$'
$mapping = ($SecondCommand -join '') -replace ',$'

$CreateExternal = @'
.create external table {0} ({1})
kind=blob
partition by (TimeGeneratedPartition:datetime = bin(TimeGenerated, 1min))
pathformat = (datetime_pattern("'y='yyyy'/m='MM'/d='dd'/h='HH'/m='mm", TimeGeneratedPartition))
dataformat=multijson
(
   h@'{2}/WorkspaceResourceId=/subscriptions/{4}/resourcegroups/{6}/providers/microsoft.operationalinsights/workspaces/{5};{3}'
)
with
(
   docstring = "Docs",
   folder = "ExternalTables"
)
'@ -f $TableName, $schema, $BlobURL, $ContainerAccessKey, $subscriptionId, $WorkspaceName, $resourcegroupname,$WorkspaceId

$createMapping = @'
.create external table {0} json mapping "{1}" '[{2}]'
'@ -f $ExternalTableName, $MapName, $mapping

Write-Host -ForegroundColor Red 'Copy and run the following commands (one by one), on your Azure Data Explorer cluster query window to create the external table and mappings:'
write-host -ForegroundColor Green $CreateExternal
Write-Host -ForegroundColor Green $createMapping
```

Az alábbi képen a kimenet látható.

:::image type="content" source="media/azure-data-explorer-query-storage/external-table-create-command-output.png" alt-text="ExternalTable Create parancs kimenete.":::

[![Példa kimenetre](media/azure-data-explorer-query-storage/external-table-create-command-output.png)](media/azure-data-explorer-query-storage/external-table-create-command-output.png#lightbox)

>[!TIP]
>* Másolja, illessze be, majd futtassa a szkript kimenetét az Azure Adatkezelő-ügyfél eszközén a tábla és a leképezés létrehozásához.
>* Ha a tárolóban lévő összes adattal szeretné használni, módosítsa a parancsfájlt, és módosítsa az URL-címet a következőre: " https://your.blob.core.windows.net/containername ; SecKey'

## <a name="query-the-exported-data-from-azure-data-explorer"></a>Az exportált adatok lekérdezése az Azure Adatkezelő 

A leképezés konfigurálása után lekérdezheti az exportált adatait az Azure Adatkezelőról. A lekérdezés a [external_table](/azure/data-explorer/kusto/query/externaltablefunction) függvényt igényli, például a következő példában.

```kusto
external_table("HBTest","map") | take 10000
```

[![Az exportált adatLog Analytics lekérdezése](media/azure-data-explorer-query-storage/external-table-query.png)](media/azure-data-explorer-query-storage/external-table-query.png#lightbox)

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [írhat lekérdezéseket az Azure-ban adatkezelő](/azure/data-explorer/write-queries)
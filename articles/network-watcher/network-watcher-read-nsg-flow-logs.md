---
title: NSG-naplók olvasása | Microsoft Docs
description: Megtudhatja, hogyan használhatja a Azure PowerShellt a hálózati biztonsági csoport folyamatábráinak elemzéséhez, amelyek óránként jönnek létre, és néhány percenként frissülnek az Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.openlocfilehash: 27f94c43266fe324016a73e2e6d31e8488457416
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100593043"
---
# <a name="read-nsg-flow-logs"></a>NSG-forgalom naplóinak olvasása

Ismerje meg, hogyan olvashatja el a NSG flow-naplók bejegyzéseit a PowerShell használatával.

A NSG egy Storage-fiókban tárolódnak, amely [blokkolja a blobokat](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs). A blokkos Blobok kisebb blokkokból állnak. Minden napló egy különálló blokk-blob, amely óránként jön létre. Az új naplók óránként jönnek létre, a naplók a legújabb adatokkal néhány percenként frissülnek az új bejegyzésekkel. Ebből a cikkből megtudhatja, hogyan olvashatja el a flow-naplók egyes részeit.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario"></a>Eset

A következő esetben egy Storage-fiókban tárolt folyamat naplója van. Megtudhatja, hogyan olvashatja el a legújabb eseményeket a NSG flow naplóiban. Ebben a cikkben a PowerShellt használja, azonban a cikkben tárgyalt fogalmak nem korlátozódnak a programozási nyelvre, és alkalmazhatók az Azure Storage API-k által támogatott összes nyelvre.

## <a name="setup"></a>Beállítás

Mielőtt elkezdené, a fiókjában egy vagy több hálózati biztonsági csoporton engedélyezni kell a hálózati biztonsági csoport folyamatának naplózását. A hálózati biztonsági folyamatok naplófájljainak engedélyezésével kapcsolatos utasításokért tekintse meg a következő cikket: a [hálózati biztonsági csoportok flow-naplózásának bemutatása](network-watcher-nsg-flow-logging-overview.md).

## <a name="retrieve-the-block-list"></a>A blokkok listájának beolvasása

A következő PowerShell beállítja azokat a változókat, amelyek szükségesek a NSG folyamat naplófájljának lekérdezéséhez, és a blokkokat a [CloudBlockBlob](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob) blokk blobján belül listázza. Frissítse a parancsfájlt, hogy érvényes értékeket tartalmazzon a környezetéhez.

```powershell
function Get-NSGFlowLogCloudBlockBlob {
    [CmdletBinding()]
    param (
        [string] [Parameter(Mandatory=$true)] $subscriptionId,
        [string] [Parameter(Mandatory=$true)] $NSGResourceGroupName,
        [string] [Parameter(Mandatory=$true)] $NSGName,
        [string] [Parameter(Mandatory=$true)] $storageAccountName,
        [string] [Parameter(Mandatory=$true)] $storageAccountResourceGroup,
        [string] [Parameter(Mandatory=$true)] $macAddress,
        [datetime] [Parameter(Mandatory=$true)] $logTime
    )

    process {
        # Retrieve the primary storage account key to access the NSG logs
        $StorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $storageAccountResourceGroup -Name $storageAccountName).Value[0]

        # Setup a new storage context to be used to query the logs
        $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

        # Container name used by NSG flow logs
        $ContainerName = "insights-logs-networksecuritygroupflowevent"

        # Name of the blob that contains the NSG flow log
        $BlobName = "resourceId=/SUBSCRIPTIONS/${subscriptionId}/RESOURCEGROUPS/${NSGResourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/${NSGName}/y=$($logTime.Year)/m=$(($logTime).ToString("MM"))/d=$(($logTime).ToString("dd"))/h=$(($logTime).ToString("HH"))/m=00/macAddress=$($macAddress)/PT1H.json"

        # Gets the storage blog
        $Blob = Get-AzStorageBlob -Context $ctx -Container $ContainerName -Blob $BlobName

        # Gets the block blog of type 'Microsoft.Azure.Storage.Blob.CloudBlob' from the storage blob
        $CloudBlockBlob = [Microsoft.Azure.Storage.Blob.CloudBlockBlob] $Blob.ICloudBlob

        #Return the Cloud Block Blob
        $CloudBlockBlob
    }
}

function Get-NSGFlowLogBlockList  {
    [CmdletBinding()]
    param (
        [Microsoft.Azure.Storage.Blob.CloudBlockBlob] [Parameter(Mandatory=$true)] $CloudBlockBlob
    )
    process {
        # Stores the block list in a variable from the block blob.
        $blockList = $CloudBlockBlob.DownloadBlockListAsync()

        # Return the Block List
        $blockList
    }
}


$CloudBlockBlob = Get-NSGFlowLogCloudBlockBlob -subscriptionId "yourSubscriptionId" -NSGResourceGroupName "FLOWLOGSVALIDATIONWESTCENTRALUS" -NSGName "V2VALIDATIONVM-NSG" -storageAccountName "yourStorageAccountName" -storageAccountResourceGroup "ml-rg" -macAddress "000D3AF87856" -logTime "11/11/2018 03:00" 

$blockList = Get-NSGFlowLogBlockList -CloudBlockBlob $CloudBlockBlob
```

A `$blockList` változó a blobban lévő blokkok listáját adja vissza. Mindegyik blokk blob legalább két blokkot tartalmaz.  Az első blokk hossza `12` bájt, ez a blokk a JSON-napló nyitó zárójeleit tartalmazza. A másik blokk a záró zárójel, amelynek hossza `2` bájt.  Ahogy az alábbi példában is látható, a naplóban hét bejegyzés található, amelyek mindegyike külön bejegyzés. A napló összes új bejegyzése közvetlenül a végső blokk előtt lesz hozzáadva a végponthoz.

```
Name                                         Length Committed
----                                         ------ ---------
ZDk5MTk5N2FkNGE0MmY5MTk5ZWViYjA0YmZhODRhYzY=     12      True
NzQxNDA5MTRhNDUzMGI2M2Y1MDMyOWZlN2QwNDZiYzQ=   2685      True
ODdjM2UyMWY3NzFhZTU3MmVlMmU5MDNlOWEwNWE3YWY=   2586      True
ZDU2MjA3OGQ2ZDU3MjczMWQ4MTRmYWNhYjAzOGJkMTg=   2688      True
ZmM3ZWJjMGQ0ZDA1ODJlOWMyODhlOWE3MDI1MGJhMTc=   2775      True
ZGVkYTc4MzQzNjEyMzlmZWE5MmRiNjc1OWE5OTc0OTQ=   2676      True
ZmY2MjUzYTIwYWIyOGU1OTA2ZDY1OWYzNmY2NmU4ZTY=   2777      True
Mzk1YzQwM2U0ZWY1ZDRhOWFlMTNhYjQ3OGVhYmUzNjk=   2675      True
ZjAyZTliYWE3OTI1YWZmYjFmMWI0MjJhNzMxZTI4MDM=      2      True
```

## <a name="read-the-block-blob"></a>A blokk blobjának beolvasása

Ezután olvasnia kell a változót az adatlekérdezéshez `$blocklist` . Ebben a példában a Blocklist áttekintjük az egyes blokkokból származó bájtokat, és egy tömbben mesélik el őket. Az [DownloadRangeToByteArray](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadrangetobytearray) metódus használatával kérheti le az adatgyűjtést.

```powershell
function Get-NSGFlowLogReadBlock  {
    [CmdletBinding()]
    param (
        [System.Array] [Parameter(Mandatory=$true)] $blockList,
        [Microsoft.Azure.Storage.Blob.CloudBlockBlob] [Parameter(Mandatory=$true)] $CloudBlockBlob

    )
    # Set the size of the byte array to the largest block
    $maxvalue = ($blocklist | measure Length -Maximum).Maximum

    # Create an array to store values in
    $valuearray = @()

    # Define the starting index to track the current block being read
    $index = 0

    # Loop through each block in the block list
    for($i=0; $i -lt $blocklist.count; $i++)
    {
        # Create a byte array object to story the bytes from the block
        $downloadArray = New-Object -TypeName byte[] -ArgumentList $maxvalue

        # Download the data into the ByteArray, starting with the current index, for the number of bytes in the current block. Index is increased by 3 when reading to remove preceding comma.
        $CloudBlockBlob.DownloadRangeToByteArray($downloadArray,0,$index, $($blockList[$i].Length)) | Out-Null

        # Increment the index by adding the current block length to the previous index
        $index = $index + $blockList[$i].Length

        # Retrieve the string from the byte array

        $value = [System.Text.Encoding]::ASCII.GetString($downloadArray)

        # Add the log entry to the value array
        $valuearray += $value
    }
    #Return the Array
    $valuearray
}
$valuearray = Get-NSGFlowLogReadBlock -blockList $blockList -CloudBlockBlob $CloudBlockBlob
```

Most a `$valuearray` tömb minden blokk sztring értékét tartalmazza. A bejegyzés ellenőrzéséhez szerezze be a másodikat a tömb utolsó értékére a futtatásával `$valuearray[$valuearray.Length-2]` . Nem szeretné, hogy az utolsó érték, mert ez a záró zárójel.

Az érték eredményei az alábbi példában láthatók:

```json
        {
             "time": "2017-06-16T20:59:43.7340000Z",
             "systemId": "5f4d02d3-a7d0-4ed4-9ce8-c0ae9377951c",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/CONTOSORG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/CONTOSONSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_AllowInternetOutBound","flows":[{"mac":"000D3A18077E","flowTuples":["1497646722,10.0.0.4,168.62.32.14,44904,443,T,O,A","1497646722,10.0.0.4,52.240.48.24,45218,443,T,O,A","1497646725,10.
0.0.4,168.62.32.14,44910,443,T,O,A","1497646725,10.0.0.4,52.240.48.24,45224,443,T,O,A","1497646728,10.0.0.4,168.62.32.14,44916,443,T,O,A","1497646728,10.0.0.4,52.240.48.24,45230,443,T,O,A","1497646732,10.0.0.4,168.62.32.14,44922,443,T,O,A","14976
46732,10.0.0.4,52.240.48.24,45236,443,T,O,A","1497646735,10.0.0.4,168.62.32.14,44928,443,T,O,A","1497646735,10.0.0.4,52.240.48.24,45242,443,T,O,A","1497646738,10.0.0.4,168.62.32.14,44934,443,T,O,A","1497646738,10.0.0.4,52.240.48.24,45248,443,T,O,
A","1497646742,10.0.0.4,168.62.32.14,44942,443,T,O,A","1497646742,10.0.0.4,52.240.48.24,45256,443,T,O,A","1497646745,10.0.0.4,168.62.32.14,44948,443,T,O,A","1497646745,10.0.0.4,52.240.48.24,45262,443,T,O,A","1497646749,10.0.0.4,168.62.32.14,44954
,443,T,O,A","1497646749,10.0.0.4,52.240.48.24,45268,443,T,O,A","1497646753,10.0.0.4,168.62.32.14,44960,443,T,O,A","1497646753,10.0.0.4,52.240.48.24,45274,443,T,O,A","1497646756,10.0.0.4,168.62.32.14,44966,443,T,O,A","1497646756,10.0.0.4,52.240.48
.24,45280,443,T,O,A","1497646759,10.0.0.4,168.62.32.14,44972,443,T,O,A","1497646759,10.0.0.4,52.240.48.24,45286,443,T,O,A","1497646763,10.0.0.4,168.62.32.14,44978,443,T,O,A","1497646763,10.0.0.4,52.240.48.24,45292,443,T,O,A","1497646766,10.0.0.4,
168.62.32.14,44984,443,T,O,A","1497646766,10.0.0.4,52.240.48.24,45298,443,T,O,A","1497646769,10.0.0.4,168.62.32.14,44990,443,T,O,A","1497646769,10.0.0.4,52.240.48.24,45304,443,T,O,A","1497646773,10.0.0.4,168.62.32.14,44996,443,T,O,A","1497646773,
10.0.0.4,52.240.48.24,45310,443,T,O,A","1497646776,10.0.0.4,168.62.32.14,45002,443,T,O,A","1497646776,10.0.0.4,52.240.48.24,45316,443,T,O,A","1497646779,10.0.0.4,168.62.32.14,45008,443,T,O,A","1497646779,10.0.0.4,52.240.48.24,45322,443,T,O,A"]}]}
,{"rule":"DefaultRule_DenyAllInBound","flows":[]},{"rule":"UserRule_ssh-rule","flows":[]},{"rule":"UserRule_web-rule","flows":[{"mac":"000D3A18077E","flowTuples":["1497646738,13.82.225.93,10.0.0.4,1180,80,T,I,A","1497646750,13.82.225.93,10.0.0.4,
1184,80,T,I,A","1497646768,13.82.225.93,10.0.0.4,1181,80,T,I,A","1497646780,13.82.225.93,10.0.0.4,1336,80,T,I,A"]}]}]}
        }
```

Ez a forgatókönyv egy példa arra, hogyan olvashat be bejegyzéseket a NSG flow-naplókban anélkül, hogy a teljes naplót kellene elemezni. A naplóban lévő új bejegyzéseket a blokk-azonosító használatával vagy a blokk blobban tárolt blokkok hosszának nyomon követésével lehet beolvasni. Ez lehetővé teszi, hogy csak az új bejegyzéseket olvassa.

## <a name="next-steps"></a>Következő lépések


Látogasson el a [rugalmas verem használatára](network-watcher-visualize-nsg-flow-logs-open-source-tools.md), [használja a Grafana](network-watcher-nsg-grafana.md), és a [Graylog](network-watcher-analyze-nsg-flow-logs-graylog.md) használatával további információkat tudhat meg a NSG-folyamatok megtekintésének módjairól. Itt talál egy nyílt forráskódú Azure-függvényt, amely a Blobok közvetlen felhasználására és különböző log Analytics-felhasználókra való kibocsátására szolgál: az [Azure Network WATCHER NSG flow naplók összekötője](https://github.com/Microsoft/AzureNetworkWatcherNSGFlowLogsConnector).

Az [Azure Traffic Analytics](./traffic-analytics.md) használatával bepillantást nyerhet a forgalmi folyamatokra. A Traffic Analytics a [log Analytics](../azure-monitor/logs/log-analytics-tutorial.md) használatával teszi elérhetővé a forgalom folyamatát.

További információ a Storage-Blobok látogatásáról: [Azure functions blob Storage-kötések](../azure-functions/functions-bindings-storage-blob.md)
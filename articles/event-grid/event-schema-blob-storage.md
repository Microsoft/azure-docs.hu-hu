---
title: Azure-Blob Storage Event Grid forrásként
description: A blob Storage-eseményekhez megadott tulajdonságokat ismerteti Azure Event Grid
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 893e86ecf220ceb327eed9c6f95be4c7ed1afb1c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100363644"
---
# <a name="azure-blob-storage-as-an-event-grid-source"></a>Azure-Blob Storage Event Grid forrásként

Ez a cikk a blob Storage-események tulajdonságait és sémáját ismerteti. Az események sémáinak bemutatása: [Azure Event Grid Event Schema](event-schema.md). Emellett a gyors indulások és oktatóanyagok listáját is megjeleníti az Azure Blob Storage eseményforrásként való használatához.


>[!NOTE]
> Csak a **StorageV2 (általános célú v2)**, a **BlockBlobStorage** és a **BlobStorage** típusú tárolási fiókok támogatják az események integrálását. A **Storage (általános célú v1)** *nem* támogatja a Event Grid integrációját.

## <a name="available-event-types"></a>Elérhető események típusai

### <a name="list-of-events-for-blob-rest-apis"></a>BLOB REST API-k eseményeinek listája

Ezek az események akkor aktiválódnak, ha egy ügyfél a blob REST API-k meghívásával létrehozza, lecseréli vagy törli a blobot.

> [!NOTE]
> A `$logs` és a `$blobchangefeed` tárolók nincsenek integrálva a Event Gridba, ezért a tárolókban lévő tevékenységek nem hoznak majd fel eseményeket. Emellett az elosztott fájlrendszerbeli végpont használata nem *`(abfss://URI) `* hierarchikus névtérrel rendelkező fiókok esetében nem hoz majd eseményeket, de a blob-végpont *`(wasb:// URI)`* eseményeket fog eredményezni.

 |Esemény neve |Description|
 |----------|-----------|
 |**Microsoft. Storage. BlobCreated** |Egy blob létrehozásakor vagy cseréjekor aktiválódik. <br>Pontosabban ez az esemény akkor aktiválódik, ha az ügyfelek `PutBlob` a `PutBlockList` `CopyBlob` blob Rest APIban elérhető, vagy műveleteket használják.   |
 |**Microsoft. Storage. BlobDeleted** |A blob törlésekor aktiválódik. <br>Ez az esemény akkor aktiválódik, ha az ügyfelek a `DeleteBlob` Blob Rest APIban elérhető műveletet hívják meg. |

> [!NOTE]
> Az **Azure Blob Storage** esetén, ha biztosítani szeretné, hogy a **Microsoft. Storage. BlobCreated** esemény csak akkor legyen aktiválva, ha egy blokk blobja teljesen véglegesítve van, akkor szűrje a `CopyBlob` , `PutBlob` és a `PutBlockList` REST API hívások eseményeit. Ezek az API-hívások csak azt követően indítják el a **Microsoft. Storage. BlobCreated** eseményt, hogy az adatgyűjtés teljes mértékben véglegesítve lett egy blokk blobban. A szűrők létrehozásával kapcsolatos további információkért lásd: [Event Grid események szűrése](./how-to-filter-events.md).

### <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Azure Data Lake Storage 2. generációs REST API-k eseményeinek listája

Ezek az események akkor aktiválódnak, ha egy hierarchikus névteret engedélyez a Storage-fiókban, és az ügyfelek Azure Data Lake Storage Gen2 REST API-kat használnak. További információ a Azure Data Lake Storage Gen2ről: a [Azure Data Lake Storage Gen2 bemutatása](../storage/blobs/data-lake-storage-introduction.md).

|Esemény neve|Description|
|----------|-----------|
|**Microsoft. Storage. BlobCreated** | Egy blob létrehozásakor vagy cseréjekor aktiválódik. <br>Pontosabban ez az esemény akkor aktiválódik, ha az ügyfelek `CreateFile` a `FlushWithClose` Azure Data Lake Storage Gen2 REST API elérhető műveleteket használják. |
|**Microsoft. Storage. BlobDeleted** |A blob törlésekor aktiválódik. <br>Ez az esemény akkor is aktiválódik, ha az ügyfelek a `DeleteFile` Azure Data Lake Storage Gen2 REST API elérhető műveletet hívják meg. |
|**Microsoft. Storage. BlobRenamed**|Egy blob átnevezve. <br>Ez az esemény akkor aktiválódik, ha az ügyfelek a `RenameFile` Azure Data Lake Storage Gen2 REST API elérhető műveletet használják.|
|**Microsoft. Storage. DirectoryCreated**|Egy könyvtár létrehozásakor aktiválódik. <br>Ez az esemény akkor aktiválódik, ha az ügyfelek a `CreateDirectory` Azure Data Lake Storage Gen2 REST API elérhető műveletet használják.|
|**Microsoft. Storage. DirectoryRenamed**|Egy könyvtár átnevezve. <br>Ez az esemény akkor aktiválódik, ha az ügyfelek a `RenameDirectory` Azure Data Lake Storage Gen2 REST API elérhető műveletet használják.|
|**Microsoft. Storage. DirectoryDeleted**|Egy könyvtár törlésekor aktiválódik. <br>Ez az esemény akkor aktiválódik, ha az ügyfelek a `DeleteDirectory` Azure Data Lake Storage Gen2 REST API elérhető műveletet használják.|

> [!NOTE]
> **Azure Data Lake Storage Gen2** esetén, ha biztosítani szeretné, hogy a **Microsoft. Storage. BlobCreated** esemény csak akkor legyen aktiválva, ha egy blokk blobja teljesen véglegesítve van, akkor szűrje a `FlushWithClose` REST API hívás eseményét. Ez az API-hívás csak azt követően indítja el a **Microsoft. Storage. BlobCreated** eseményt, hogy az adathalmaz teljes mértékben véglegesítve lett egy blokk blobban. A szűrők létrehozásával kapcsolatos további információkért lásd: [Event Grid események szűrése](./how-to-filter-events.md).

## <a name="example-event"></a>Példa eseményre
Egy esemény indításakor a Event Grid szolgáltatás adatokat küld az eseményről a végpontra való feliratkozáshoz. Ez a szakasz egy példát mutat be, hogy az egyes blob Storage-események milyen módon néznek ki.

# <a name="event-grid-event-schema"></a>[Event Grid-eseményséma](#tab/event-grid-event-schema)

### <a name="microsoftstorageblobcreated-event"></a>Microsoft. Storage. BlobCreated esemény

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/test-container/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/new-file.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Microsoft. Storage. BlobCreated esemény (Data Lake Storage Gen2)

Ha a blob Storage-fiók hierarchikus névtérrel rendelkezik, az adatváltozások az előző példához hasonlóan jelennek meg, kivéve a következő módosításokat:

* A `dataVersion` kulcs értéke a következő értékre van beállítva: `2` .

* A `data.api` kulcs a karakterláncra vagy a értékre van beállítva `CreateFile` `FlushWithClose` .

* A `contentOffset` kulcs szerepel az adatkészletben.

> [!NOTE]
> Ha az alkalmazások a `PutBlockList` művelettel új blobot töltenek fel a fiókba, akkor az adatok nem tartalmazzák ezeket a módosításokat.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 0,
    "contentOffset": 0,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/new-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event"></a>Microsoft. Storage. BlobDeleted esemény

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/file-to-delete.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Microsoft. Storage. BlobDeleted esemény (Data Lake Storage Gen2)

Ha a blob Storage-fiók hierarchikus névtérrel rendelkezik, az adatváltozások az előző példához hasonlóan jelennek meg, kivéve a következő módosításokat:

* A `dataVersion` kulcs értéke a következő értékre van beállítva: `2` .

* A `data.api` kulcs a karakterláncra van beállítva `DeleteFile` .

* A `url` kulcs tartalmazza az elérési utat `dfs.core.windows.net` .

> [!NOTE]
> Ha az alkalmazások a `DeleteBlob` művelettel törölnek egy blobot a fiókból, akkor az adatok nem tartalmazzák ezeket a módosításokat.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
    "api": "DeleteFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/file-to-delete.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobrenamed-event"></a>Microsoft. Storage. BlobRenamed esemény

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-file.txt",
  "eventType": "Microsoft.Storage.BlobRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-file.txt",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorycreated-event"></a>Microsoft. Storage. DirectoryCreated esemény

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-new-directory",
  "eventType": "Microsoft.Storage.DirectoryCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-new-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectoryrenamed-event"></a>Microsoft. Storage. DirectoryRenamed esemény

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-directory",
  "eventType": "Microsoft.Storage.DirectoryRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-directory",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorydeleted-event"></a>Microsoft. Storage. DirectoryDeleted esemény

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/directory-to-delete",
  "eventType": "Microsoft.Storage.DirectoryDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "DeleteDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/directory-to-delete",
    "recursive": "true", 
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

# <a name="cloud-event-schema"></a>[Felhő-eseményséma](#tab/cloud-event-schema)

### <a name="microsoftstorageblobcreated-event"></a>Microsoft. Storage. BlobCreated esemény

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/test-container/blobs/new-file.txt",
  "type": "Microsoft.Storage.BlobCreated",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/new-file.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Microsoft. Storage. BlobCreated esemény (Data Lake Storage Gen2)

Ha a blob Storage-fiók hierarchikus névtérrel rendelkezik, az adatváltozások az előző példához hasonlóan jelennek meg, kivéve a következő módosításokat:

* A `data.api` kulcs a karakterláncra vagy a értékre van beállítva `CreateFile` `FlushWithClose` .
* A `contentOffset` kulcs szerepel az adatkészletben.

> [!NOTE]
> Ha az alkalmazások a `PutBlockList` művelettel új blobot töltenek fel a fiókba, akkor az adatok nem tartalmazzák ezeket a módosításokat.

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/new-file.txt",
  "type": "Microsoft.Storage.BlobCreated",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 0,
    "contentOffset": 0,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/new-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstorageblobdeleted-event"></a>Microsoft. Storage. BlobDeleted esemény

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file-to-delete.txt",
  "type": "Microsoft.Storage.BlobDeleted",
  "time": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/file-to-delete.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Microsoft. Storage. BlobDeleted esemény (Data Lake Storage Gen2)

Ha a blob Storage-fiók hierarchikus névtérrel rendelkezik, az adatváltozások az előző példához hasonlóan jelennek meg, kivéve a következő módosításokat:


* A `data.api` kulcs a karakterláncra van beállítva `DeleteFile` .
* A `url` kulcs tartalmazza az elérési utat `dfs.core.windows.net` .

> [!NOTE]
> Ha az alkalmazások a `DeleteBlob` művelettel törölnek egy blobot a fiókból, akkor az adatok nem tartalmazzák ezeket a módosításokat.

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/file-to-delete.txt",
  "type": "Microsoft.Storage.BlobDeleted",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
    "api": "DeleteFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/file-to-delete.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstorageblobrenamed-event"></a>Microsoft. Storage. BlobRenamed esemény

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-file.txt",
  "type": "Microsoft.Storage.BlobRenamed",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-file.txt",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstoragedirectorycreated-event"></a>Microsoft. Storage. DirectoryCreated esemény

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-new-directory",
  "type": "Microsoft.Storage.DirectoryCreated",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-new-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstoragedirectoryrenamed-event"></a>Microsoft. Storage. DirectoryRenamed esemény

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-directory",
  "type": "Microsoft.Storage.DirectoryRenamed",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-directory",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstoragedirectorydeleted-event"></a>Microsoft. Storage. DirectoryDeleted esemény

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/directory-to-delete",
  "type": "Microsoft.Storage.DirectoryDeleted",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "DeleteDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/directory-to-delete",
    "recursive": "true", 
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

---


## <a name="event-properties"></a>Esemény tulajdonságai

# <a name="event-grid-event-schema"></a>[Event Grid-eseményséma](#tab/event-grid-event-schema)

Egy esemény a következő legfelső szintű adattal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| `topic` | sztring | Az eseményforrás teljes erőforrás-elérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg. |
| `subject` | sztring | Az esemény tárgyra mutató, a közzétevő által megadott elérési út. |
| `eventType` | sztring | Az eseményforráshoz felvett eseménytípusok egyike. |
| `eventTime` | sztring | Az esemény a szolgáltató UTC-ideje alapján történő létrehozásakor. |
| `id` | sztring | Az esemény egyedi azonosítója. |
| `data` | object | BLOB Storage-események |
| `dataVersion` | sztring | Az adatobjektum sémaverziója. A sémaverziót a közzétevő határozza meg. |
| `metadataVersion` | sztring | Az esemény metaadatok sémaverziója. A legfelső szintű tulajdonságokra az Event Grid határozza meg a sémát. Az értéket az Event Grid adja meg. |

# <a name="cloud-event-schema"></a>[Felhő-eseményséma](#tab/cloud-event-schema)

Egy esemény a következő legfelső szintű adattal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| `source` | sztring | Az eseményforrás teljes erőforrás-elérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg. |
| `subject` | sztring | Az esemény tárgyra mutató, a közzétevő által megadott elérési út. |
| `type` | sztring | Az eseményforráshoz felvett eseménytípusok egyike. |
| `time` | sztring | Az esemény a szolgáltató UTC-ideje alapján történő létrehozásakor. |
| `id` | sztring | Az esemény egyedi azonosítója. |
| `data` | object | BLOB Storage-események |
| `specversion` | sztring | A CloudEvents séma specifikációjának verziója. |

---

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| `api` | sztring | Az eseményt kiváltó művelet. |
| `clientRequestId` | sztring | ügyfél által megadott kérelem azonosítója a tárolási API-művelethez. Ez az azonosító használható az Azure Storage diagnosztikai naplóinak az "ügyfél-kérelem-azonosító" mezővel való összekapcsolására a naplókban, és az "x-MS-Client-Request-id" fejléc használatával megadható az ügyfelek kérései. Lásd: [naplózási formátum](/rest/api/storageservices/storage-analytics-log-format). |
| `requestId` | sztring | A szolgáltatás által generált kérelem azonosítója a tárolási API-művelethez. Felhasználható az Azure Storage diagnosztikai naplóinak a naplók "Request-ID-header" mezővel való összekapcsolására, és a rendszer az "x-MS-Request-id" fejlécben az API-hívás kezdeményezését adja vissza. Lásd: [naplózási formátum](/rest/api/storageservices/storage-analytics-log-format). |
| `eTag` | sztring | Az az érték, amelyet a műveletek feltételes futtatására használhat. |
| `contentType` | sztring | A blobhoz megadott tartalomtípus. |
| `contentLength` | egész szám | A blob mérete bájtban megadva. |
| `blobType` | sztring | A blob típusa. Az érvényes értékek: "BlockBlob" vagy "PageBlob". |
| `contentOffset` | szám | Egy írási művelet bájtban kifejezett eltolása azon a ponton, ahol az eseményindító alkalmazás befejezte a fájlba való írást. <br>Csak olyan eseményeknél jelenik meg, amelyek hierarchikus névtérrel rendelkező blob Storage-fiókokban aktiválódnak.|
| `destinationUrl` |sztring | A művelet befejeződése után létező fájl URL-címe. Ha például egy fájl átnevezve lett, a `destinationUrl` tulajdonság az új fájlnév URL-címét tartalmazza. <br>Csak olyan eseményeknél jelenik meg, amelyek hierarchikus névtérrel rendelkező blob Storage-fiókokban aktiválódnak.|
| `sourceUrl` |sztring | A művelet végrehajtása előtt létező fájl URL-címe. Ha például egy fájl átnevezve van, a `sourceUrl` tartalmazza az eredeti fájlnév URL-címét az átnevezési művelet előtt. <br>Csak olyan eseményeknél jelenik meg, amelyek hierarchikus névtérrel rendelkező blob Storage-fiókokban aktiválódnak. |
| `url` | sztring | A blob elérési útja. <br>Ha az ügyfél blobot REST API használ, akkor az URL-cím a következő struktúrával rendelkezik: `<storage-account-name>.blob.core.windows.net\<container-name>\<file-name>` . <br>Ha az ügyfél Data Lake Storage REST API használ, akkor az URL-cím a következő struktúrával rendelkezik: `<storage-account-name>.dfs.core.windows.net/<file-system-name>/<file-name>` . |
| `recursive` | sztring | `True` a művelet futtatása az összes alárendelt könyvtáron; Ellenkező esetben `False` . <br>Csak olyan eseményeknél jelenik meg, amelyek hierarchikus névtérrel rendelkező blob Storage-fiókokban aktiválódnak. |
| `sequencer` | sztring | Egy átlátszatlan karakterlánc-érték, amely az események logikai sorát jelképezi az adott blob nevénél.  A felhasználók a szabványos sztringek összehasonlításával megértették, hogy az adott blob nevében két esemény relatív sorszáma látható. |
| `storageDiagnostics` | object | Az Azure Storage szolgáltatás időnként diagnosztikai adatelemzéseket is tartalmaz. Ha van ilyen, figyelmen kívül kell hagyni az esemény felhasználói számára. |

## <a name="tutorials-and-how-tos"></a>Oktatóanyagok és útmutatók
|Cím  |Leírás  |
|---------|---------|
| [Gyors útmutató: blob Storage-események átirányítása egyéni webes végpontra az Azure CLI-vel](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Bemutatja, hogyan küldhet blob Storage-eseményeket webhookba az Azure CLI használatával. |
| [Gyors útmutató: blob Storage-események átirányítása egyéni webes végpontra a PowerShell-lel](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Azt mutatja be, hogyan használható a Azure PowerShell a blob Storage-események webhookba való küldéséhez. |
| [Gyors útmutató: blob Storage-események létrehozása és átirányítása a Azure Portal](blob-event-quickstart-portal.md) | Bemutatja, hogyan küldhet blob Storage-eseményeket webhookba a portál használatával. |
| [Azure CLI: előfizetés egy blob Storage-fiók eseményeire](./scripts/event-grid-cli-blob.md) | A blob Storage-fiókra vonatkozó eseményre előfizetett minta parancsfájl. Az eseményt egy webhookba küldi. |
| [PowerShell: előfizetés egy blob Storage-fiók eseményeire](./scripts/event-grid-powershell-blob.md) | A blob Storage-fiókra vonatkozó eseményre előfizetett minta parancsfájl. Az eseményt egy webhookba küldi. |
| [Resource Manager-sablon: blob Storage és előfizetés létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Üzembe helyez egy Azure Blob Storage-fiókot, és feliratkozik a vele kapcsolatos eseményekre. Eseményeket küld egy webhooknak. |
| [Áttekintés: a blob Storage eseményeire való reagálás](../storage/blobs/storage-blob-event-overview.md) | A blob Storage Event Grid-val való integrálásának áttekintése. |

## <a name="next-steps"></a>Következő lépések

* A Azure Event Grid bemutatása: [Mi az Event Grid?](overview.md)
* Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid előfizetés sémája](subscription-creation-schema.md).
* A blob Storage-események használatának bemutatása: [blob Storage-események átirányítása – Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 

---
title: Azure Cosmos DB mellékletek
description: Ez a cikk a Azure Cosmos DB mellékletek áttekintését mutatja be.
author: aliuy
ms.author: andrl
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/07/2020
ms.reviewer: sngun
ms.openlocfilehash: 0a2ff0f24e3fe4711e0d2f1c306ae2eaa0fda5e0
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102217244"
---
# <a name="azure-cosmos-db-attachments"></a>Azure Cosmos DB mellékletek
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB a mellékletek olyan speciális elemek, amelyek egy külső blob-vagy médiafájl-fájllal kapcsolatos metaadatokra mutató hivatkozásokat tartalmaznak.

Azure Cosmos DB a mellékletek két típusát támogatja:

* A nem **felügyelt mellékletek** a külső szolgáltatásokban (például Azure Storage, OneDrive stb.) tárolt blobokra mutató URI-referenciák. Ez a megközelítés hasonló a standard Azure Cosmos DB-elemek URI-tulajdonságainak tárolásához.
* A **felügyelt mellékletek** a Blobok felügyelete alatt állnak, és belsőleg tárolódnak Azure Cosmos db, és a rendszer által létrehozott mediaLink keresztül teszik elérhetővé.


> [!NOTE]
> A melléklet egy örökölt szolgáltatás. A támogatásuk hatókörét képezi, hogy további funkciókat nyújtson, ha már használja ezt a funkciót.
> 
> A mellékletek használata helyett azt javasoljuk, hogy az Azure Blob Storage-t használja a blob-adat tárolására szolgáló, szándékosan létrehozott blob Storage-szolgáltatásként. Továbbra is tárolhatja a Blobokkal kapcsolatos metaadatokat, valamint a hivatkozási URI-hivatkozásokat a Azure Cosmos DB elem tulajdonságaiban. Az adatok Azure Cosmos DBban való tárolása lehetővé teszi a metaadatok lekérdezését és az Azure-Blob Storage tárolt blobokra mutató hivatkozásokat.
> 
> A Microsoft elkötelezte magát arra, hogy a mellékletek teljes elavulttá nyilvánítása előtt legalább 36 hónapos értesítést nyújtson, amely további időpontban lesz bejelentve.

## <a name="known-limitations"></a>Ismert korlátozások

A Azure Cosmos DB felügyelt mellékletei különböznek a standard elemek támogatásával – így korlátlan skálázhatóságot, globális elosztást és más Azure-szolgáltatásokkal való integrációt biztosít.

- A mellékletek nem támogatottak a Azure Cosmos DB SDK-k összes verziójában.
- A felügyelt mellékletek adatbázis-fiókok esetében legfeljebb 2 GB tárterületre korlátozódnak.
- A felügyelt mellékletek nem kompatibilisek a Azure Cosmos DB globális eloszlásával, és nem replikálódnak a régiók között.

## <a name="migrating-attachments-to-azure-blob-storage"></a>Mellékletek migrálása az Azure Blob Storageba

Azt javasoljuk, hogy az alábbi lépéseket követve áttelepítheti Azure Cosmos DB mellékleteit az Azure Blob Storageba:

1. Másolja a mellékletek adatait a forrás Azure Cosmos DB tárolóból a cél Azure Blob Storage-tárolóba.
2. Érvényesítse a feltöltött blob-adatforrásokat a cél Azure Blob Storage tárolóban.
3. Ha alkalmazható, adjon hozzá URI-referenciákat az Azure Blob Storageban található blobokhoz karakterlánc-tulajdonságokként az Azure Cosmos DB adatkészleten belül.
4. Az alkalmazás kódjának újrabontása az új Azure Blob Storage tárolóban lévő Blobok olvasásához és írásához.

A következő DotNet-kódrészlet bemutatja, hogyan másolhat mellékleteket Azure Cosmos DBról az Azure Blob Storage-ba egy áttelepítési folyamat részeként Azure Cosmos DB .NET SDK v2 és az Azure Blob Storage .NET SDK V12 használatával. Ne felejtse el lecserélni a `<placeholder values>` forrás Azure Cosmos db fiókra és a cél Azure Blob Storage-tárolóra.

```csharp

using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;

namespace attachments
{
    class Program
    {
        private static string cosmosAccount = "<Your_Azure_Cosmos_account_URI>";
        private static string cosmosKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>";
        private static string cosmosDatabaseName = "<Your_Azure_Cosmos_database>";
        private static string cosmosCollectionName = "<Your_Azure_Cosmos_collection>";
        private static string storageConnectionString = "<Your_Azure_Storage_connection_string>";
        private static string storageContainerName = "<Your_Azure_Storage_container_name>";
        private static DocumentClient cosmosClient = new DocumentClient(new Uri(cosmosAccount), cosmosKey);
        private static BlobServiceClient storageClient = new BlobServiceClient(storageConnectionString);
        private static BlobContainerClient storageContainerClient = storageClient.GetBlobContainerClient(storageContainerName);

        static void Main(string[] args)
        {
            CopyAttachmentsToBlobsAsync().Wait();
        }

        private async static Task CopyAttachmentsToBlobsAsync()
        {
            Console.WriteLine("Copying Azure Cosmos DB Attachments to Azure Blob Storage ...");

            int totalCount = 0;
            string docContinuation = null;

            // Iterate through each item (document in v2) in the Azure Cosmos DB container (collection in v2) to look for attachments.
            do
            {
                FeedResponse<dynamic> response = await cosmosClient.ReadDocumentFeedAsync(
                    UriFactory.CreateDocumentCollectionUri(cosmosDatabaseName, cosmosCollectionName),
                    new FeedOptions
                    {
                        MaxItemCount = -1,
                        RequestContinuation = docContinuation
                    });
                docContinuation = response.ResponseContinuation;

                foreach (Document document in response)
                {
                    string attachmentContinuation = null;
                    PartitionKey docPartitionKey = new PartitionKey(document.Id);

                    // Iterate through each attachment within the item (if any).
                    do
                    {
                        FeedResponse<Attachment> attachments = await cosmosClient.ReadAttachmentFeedAsync(
                            document.SelfLink,
                            new FeedOptions
                            {
                                PartitionKey = docPartitionKey,
                                RequestContinuation = attachmentContinuation
                            }
                        );
                        attachmentContinuation = attachments.ResponseContinuation;

                        foreach (var attachment in attachments)
                        {
                            // Download the attachment in to local memory.
                            MediaResponse content = await cosmosClient.ReadMediaAsync(attachment.MediaLink);

                            byte[] buffer = new byte[content.ContentLength];
                            await content.Media.ReadAsync(buffer, 0, buffer.Length);

                            // Upload the locally buffered attachment to blob storage
                            string blobId = String.Concat(document.Id, "-", attachment.Id);

                            Azure.Response<BlobContentInfo> uploadedBob = await storageContainerClient.GetBlobClient(blobId).UploadAsync(
                                new MemoryStream(buffer, writable: false),
                                true
                            );

                            Console.WriteLine("Copied attachment ... Item Id: {0} , Attachment Id: {1}, Blob Id: {2}", document.Id, attachment.Id, blobId);
                            totalCount++;

                            // Clean up attachment from Azure Cosmos DB.
                            // Warning: please verify you've succesfully migrated attachments to blog storage prior to cleaning up Azure Cosmos DB.
                            // await cosmosClient.DeleteAttachmentAsync(
                            //     attachment.SelfLink,
                            //     new RequestOptions { PartitionKey = docPartitionKey }
                            // );

                            // Console.WriteLine("Cleaned up attachment ... Document Id: {0} , Attachment Id: {1}", document.Id, attachment.Id);
                        }

                    } while (!string.IsNullOrEmpty(attachmentContinuation));
                }
            }
            while (!string.IsNullOrEmpty(docContinuation));

            Console.WriteLine("Finished copying {0} attachments to blob storage", totalCount);
        }
    }
}

```

## <a name="next-steps"></a>Következő lépések

- Ismerkedés az [Azure Blob Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md) szolgáltatással
- Hivatkozások a mellékletek használatára [Azure Cosmos db .net SDK v2](/dotnet/api/microsoft.azure.documents.attachment?preserve-view=true&view=azure-dotnet) használatával
- Hivatkozások a mellékletek használatára [Azure Cosmos db Java SDK v2](/java/api/com.microsoft.azure.documentdb.attachment) használatával
- Hivatkozások a mellékletek használatára [Azure Cosmos DB REST API](/rest/api/cosmos-db/attachments)
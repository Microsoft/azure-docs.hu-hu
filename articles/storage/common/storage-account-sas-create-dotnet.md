---
title: Fiók SAS létrehozása a .NET-tel
titleSuffix: Azure Storage
description: Megtudhatja, hogyan hozhat létre egy fiókhoz megosztott hozzáférési aláírást (SAS) a .NET ügyféloldali kódtár használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/12/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 2918b845430a6fc6dc59eca7041c114fc9d06515
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97092210"
---
# <a name="create-an-account-sas-with-net"></a>Fiók SAS létrehozása a .NET-tel

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Ez a cikk bemutatja, hogyan hozhat létre fiók SAS-t a .NET-hez készült [Azure Storage ügyféloldali kódtár](/dotnet/api/overview/azure/storage)használatával a Storage-fiók kulcsa alapján.

## <a name="create-an-account-sas"></a>Fiók SAS létrehozása

### <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

Egy fiókhoz tartozó SAS a fiók hozzáférési kulcsával van aláírva. A [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) osztály használatával hozza létre az SAS aláírásához használt hitelesítő adatokat. Ezután hozzon létre egy új [AccountSasBuilder](/dotnet/api/azure.storage.sas.accountsasbuilder) objektumot, és hívja meg a [ToSasQueryParameters](/dotnet/api/azure.storage.sas.accountsasbuilder.tosasqueryparameters) az SAS-jogkivonat karakterláncának beolvasásához.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetAccountSASToken":::

### <a name="net-v11"></a>[\.NETTÓ v11](#tab/dotnetv11)

Ha fiókot szeretne létrehozni egy tárolóhoz, hívja meg a [CloudStorageAccount. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.getsharedaccesssignature) metódust.

A következő kódrészlet egy olyan fiókot hoz létre, amely érvényes a blob és a Fájlszolgáltatások számára, és az ügyfél engedélyei olvasási, írási és listázási engedélyt biztosít a szolgáltatási szintű API-k eléréséhez. A fiók SAS korlátozza a protokollt a HTTPS-re, ezért HTTPS-kapcsolattal kell elvégeznie a kérést. Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire a szögletes zárójelekben:

```csharp
static string GetAccountSASToken()
{
    // To create the account SAS, you need to use Shared Key credentials. Modify for your account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=<storage-account>;AccountKey=<account-key>";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create a new access policy for the account.
    SharedAccessAccountPolicy policy = new SharedAccessAccountPolicy()
        {
            Permissions = SharedAccessAccountPermissions.Read | SharedAccessAccountPermissions.Write | SharedAccessAccountPermissions.List,
            Services = SharedAccessAccountServices.Blob | SharedAccessAccountServices.File,
            ResourceTypes = SharedAccessAccountResourceTypes.Service,
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Protocols = SharedAccessProtocol.HttpsOnly
        };

    // Return the SAS token.
    return storageAccount.GetSharedAccessSignature(policy);
}
```

---

## <a name="use-an-account-sas-from-a-client"></a>Fiók SAS használata ügyfélről

Ha a fiók SAS-t használja a Blob servicehoz tartozó szolgáltatási szintű API-k eléréséhez, a Storage-fiókhoz állítson össze egy Blob service-ügyfél objektumot a SAS és a blob Storage-végpont használatával.

### <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_UseAccountSAS":::

### <a name="net-v11"></a>[\.NETTÓ v11](#tab/dotnetv11)

Ebben a kódrészletben cserélje le a `<storage-account>` helyőrzőt a Storage-fiók nevére.

```csharp
static void UseAccountSAS(string sasToken)
{
    // Create new storage credentials using the SAS token.
    StorageCredentials accountSAS = new StorageCredentials(sasToken);
    // Use these credentials and the account name to create a Blob service client.
    CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "<storage-account>", endpointSuffix: null, useHttps: true);
    CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();

    // Now set the service properties for the Blob client created with the SAS.
    blobClientWithSAS.SetServiceProperties(new ServiceProperties()
    {
        HourMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        MinuteMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        Logging = new LoggingProperties()
        {
            LoggingOperations = LoggingOperations.All,
            RetentionDays = 14,
            Version = "1.0"
        }
    });

    // The permissions granted by the account SAS also permit you to retrieve service properties.
    ServiceProperties serviceProperties = blobClientWithSAS.GetServiceProperties();
    Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
    Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
    Console.WriteLine(serviceProperties.HourMetrics.Version);
}
```

---

## <a name="next-steps"></a>Következő lépések

- [Korlátozott hozzáférés biztosítása az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával](storage-sas-overview.md)
- [Fiók SAS létrehozása](/rest/api/storageservices/create-account-sas)

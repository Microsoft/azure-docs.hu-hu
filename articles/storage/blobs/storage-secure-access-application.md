---
title: Az alkalmazásadatok biztonságos elérése
titleSuffix: Azure Storage
description: Alkalmazásainak adatait SAS-jogkivonatok, titkosítás és HTTPS protokoll segítségével tudhatja biztonságban a felhőben.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: tamram
ms.reviewer: ozgun
ms.custom: mvc, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: f7c5dbaf30965fdd5f438f0351cfa2cd60e05b70
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92746566"
---
# <a name="secure-access-to-application-data"></a>Az alkalmazásadatok biztonságos elérése

Ez az oktatóanyag egy sorozat harmadik része. Megtudhatja, hogyan teheti biztonságossá a tárfiókhoz való hozzáférést. 

A sorozat harmadik részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A miniatűr képekhez a SAS-tokenek segítségével férhet hozzá
> * Kiszolgálóoldali titkosítás bekapcsolása
> * Csak HTTPS-protokollal történő átvitel engedélyezése

Az [Azure Blob Storage](../common/storage-introduction.md#blob-storage) stabil szolgáltatást biztosít az alkalmazások fájljainak tárolásához. Az oktatóanyag kibővíti [az előző témakört][previous-tutorial], hogy bemutassa, hogyan teheti biztonságossá a tárfiókhoz való hozzáférést egy webalkalmazásból. Az oktatóanyag elvégzése után a rendszer titkosítja a képeket, és a webalkalmazás biztonságos SAS-jogkivonatok segítségével fér hozzá a miniatűr képekhez.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez el kell végeznie az előző tárolási oktatóanyagot: [Feltöltött képek átméretezésének automatizálása az Event Grid használatával][previous-tutorial].

## <a name="set-container-public-access"></a>A tároló nyilvános hozzáférésének beállítása

Az oktatóanyag-sorozat ezen részében SAS-jogkivonatokat használunk a miniatűr képekhez való hozzáféréshez. Ebben a lépésben a *thumbnails* tároló nyilvános hozzáférését `off` értékre állítja be.

```bash
blobStorageAccount="<blob_storage_account>"

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
    --account-name $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \
    --account-name $blobStorageAccount \
    --account-key $blobStorageAccountKey \
    --name thumbnails \
    --public-access off
```

```powershell
$blobStorageAccount="<blob_storage_account>"

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup `
    --account-name $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission `
    --account-name $blobStorageAccount `
    --account-key $blobStorageAccountKey `
    --name thumbnails `
    --public-access off
```

## <a name="configure-sas-tokens-for-thumbnails"></a>SAS-jogkivonatok konfigurálása miniatűr képekhez

Az oktatóanyag-sorozat első részében a webalkalmazás egy nyilvános tárolóból származó képeket jelenített meg. A sorozat ezen részében a közös hozzáférésű aláírások (SAS) jogkivonatok segítségével kérheti le a miniatűr lemezképeket. A SAS-jogkivonatok segítségével korlátozott hozzáférést biztosíthat egy tárolóhoz vagy blobhoz IP-cím, protokoll, időintervallum vagy engedélyezett jogosultságok alapján. Az SAS-vel kapcsolatos további információkért lásd: [korlátozott hozzáférés engedélyezése az Azure Storage-erőforrásokhoz közös hozzáférési aláírások (SAS) használatával](../common/storage-sas-overview.md).

A példában a forráskód adattára a `sasTokens` ágat használja, amely egy frissített kódmintát tartalmaz. Törölje a meglévő GitHub-telepítést az [az webapp deployment source delete](/cli/azure/webapp/deployment/source) parancs segítségével. Azután konfigurálja a GitHub-telepítést a webalkalmazásba az [az webapp deployment source config](/cli/azure/webapp/deployment/source) parancs segítségével.

Az alábbi parancsban a `<web-app>` a webalkalmazás neve.

```bash
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> \
    --resource-group myResourceGroup --branch sasTokens --manual-integration \
    --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

```powershell
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> `
    --resource-group myResourceGroup --branch sasTokens --manual-integration `
    --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

Az adattár `sasTokens` ága frissíti a `StorageHelper.cs` fájlt. A `GetThumbNailUrls` feladatot az alábbi példakóddal cseréli ki. A frissített feladat lekérdezi a miniatűr URL-címeket egy [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) használatával a kezdő időpont, a lejárati idő és az SAS-token engedélyeinek megadásához. Telepítést követően a webalkalmazás mostantól SAS-jogkivonatot használó URL-címmel kéri le a miniatűr képeket. A frissített feladat az alábbi példában látható:

```csharp
public static async Task<List<string>> GetThumbNailUrls(AzureStorageConfig _storageConfig)
{
    List<string> thumbnailUrls = new List<string>();

    // Create a URI to the storage account
    Uri accountUri = new Uri("https://" + _storageConfig.AccountName + ".blob.core.windows.net/");

    // Create BlobServiceClient from the account URI
    BlobServiceClient blobServiceClient = new BlobServiceClient(accountUri);

    // Get reference to the container
    BlobContainerClient container = blobServiceClient.GetBlobContainerClient(_storageConfig.ThumbnailContainer);

    if (container.Exists())
    {
        // Set the expiration time and permissions for the container.
        // In this case, the start time is specified as a few 
        // minutes in the past, to mitigate clock skew.
        // The shared access signature will be valid immediately.
        BlobSasBuilder sas = new BlobSasBuilder
        {
            Resource = "c",
            BlobContainerName = _storageConfig.ThumbnailContainer,
            StartsOn = DateTimeOffset.UtcNow.AddMinutes(-5),
            ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
        };

        sas.SetPermissions(BlobContainerSasPermissions.All);

        // Create StorageSharedKeyCredentials object by reading
        // the values from the configuration (appsettings.json)
        StorageSharedKeyCredential storageCredential =
            new StorageSharedKeyCredential(_storageConfig.AccountName, _storageConfig.AccountKey);

        // Create a SAS URI to the storage account
        UriBuilder sasUri = new UriBuilder(accountUri);
        sasUri.Query = sas.ToSasQueryParameters(storageCredential).ToString();

        foreach (BlobItem blob in container.GetBlobs())
        {
            // Create the URI using the SAS query token.
            string sasBlobUri = container.Uri + "/" +
                                blob.Name + sasUri.Query;

            //Return the URI string for the container, including the SAS token.
            thumbnailUrls.Add(sasBlobUri);
        }
    }
    return await Task.FromResult(thumbnailUrls);
}
```

Az előző feladatban használt osztályok, tulajdonságok és metódusok a következők:

| Osztály | Tulajdonságok | Metódusok |
|-------|------------|---------|
|[StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) |  |  |
|[BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) |  |[GetBlobContainerClient](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainerclient) |
|[BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient) | [URI](/dotnet/api/azure.storage.blobs.blobcontainerclient.uri) |[Létezik](/dotnet/api/azure.storage.blobs.blobcontainerclient.exists) <br> [GetBlobs](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobs) |
|[BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) |  | [SetPermissions](/dotnet/api/azure.storage.sas.blobsasbuilder.setpermissions) <br> [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) |
|[Blobelemet](/dotnet/api/azure.storage.blobs.models.blobitem) | [Név](/dotnet/api/azure.storage.blobs.models.blobitem.name) |  |
|[UriBuilder](/dotnet/api/system.uribuilder) | [Lekérdezés](/dotnet/api/system.uribuilder.query) |  |
|[Lista](/dotnet/api/system.collections.generic.list-1) | | [Hozzáadás](/dotnet/api/system.collections.generic.list-1.add) |

## <a name="azure-storage-encryption"></a>Azure Storage-titkosítás

Az [Azure Storage-titkosítás](../common/storage-service-encryption.md) az adatok védelméhez és védelméhez nyújt segítséget azáltal, hogy titkosítja az adatait, és kezeli a titkosítást és a visszafejtést. Minden adat titkosítása 256 bites [AES-titkosítással](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) történik, amely a jelenleg elérhető egyik legerősebb blokktitkosító.

Megadhatja, hogy a Microsoft kezelje a titkosítási kulcsokat, vagy saját kulcsokat is használhat Azure Key Vault vagy Key Vault felügyelt hardveres biztonsági modellben (HSM) (előzetes verzió) tárolt, ügyfél által felügyelt kulcsokkal. További információ: [ügyfél által felügyelt kulcsok az Azure Storage encryption szolgáltatáshoz](../common/customer-managed-keys-overview.md).

Az Azure Storage-titkosítás automatikusan titkosítja az összes teljesítményszint (standard és prémium), az összes üzembe helyezési modell (Azure Resource Manager és klasszikus), valamint az összes Azure Storage-szolgáltatás (blob, üzenetsor, tábla és fájl) mennyiségét.

## <a name="enable-https-only"></a>Csak HTTPS engedélyezése

A tárfiókba érkező és az onnan kimenő adatkérések biztonsága érdekében korlátozhatja a kéréseket a HTTPS-protokoll használatára. Frissítse a tárfiók által kért protokollt az [az storage account update](/cli/azure/storage/account) parancs segítségével.

```azurecli-interactive
az storage account update --resource-group myresourcegroup --name <storage-account-name> --https-only true
```

Tesztelje a kapcsolatot a `HTTP` használatával a `curl` protokollal.

```azurecli-interactive
curl http://<storage-account-name>.blob.core.windows.net/<container>/<blob-name> -I
```

Most, hogy biztonságos átvitel szükséges, az alábbi üzenetet kapja:

```
HTTP/1.1 400 The account being accessed does not support http.
```

## <a name="next-steps"></a>Következő lépések

A sorozat harmadik részében megtanulta, hogy miként teheti biztonságossá a tárfiókhoz való hozzáférést, többek között:

> [!div class="checklist"]
> * A miniatűr képekhez a SAS-tokenek segítségével férhet hozzá
> * Kiszolgálóoldali titkosítás bekapcsolása
> * Csak HTTPS-protokollal történő átvitel engedélyezése

A sorozat negyedik részében a felhőalapú tárolási alkalmazások monitorozásának és hibaelhárításának módjaival ismerkedhet meg.

> [!div class="nextstepaction"]
> [Monitorozás és hibaelhárítás alkalmazás felhőalkalmazás-tárolójában](storage-monitor-troubleshoot-storage-application.md)

[previous-tutorial]: ../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json

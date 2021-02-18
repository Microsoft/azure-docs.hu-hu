---
title: adathordozó feltöltése: Azure Media Services Leírás: megtudhatja, hogyan tölthetők fel a média adatfolyam-továbbításra vagy kódolásra.
szolgáltatások: Media-Services documentationcenter: ' ' Author: IngridAtMicrosoft Manager: femila Editor: ' '

MS. Service: Media-Services MS. munkaterhelés: MS. topic: útmutató MS. Date: 08/31/2020 MS. Author: inhenkel
---

# <a name="upload-media-for-streaming-or-encoding"></a>Média feltöltése adatfolyamként vagy kódolásra

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Services a digitális fájlokat (adathordozókat) feltölti egy eszközhöz társított blob-tárolóba. Az [eszköz](/rest/api/media/operations/asset) entitás tartalmazhat videó-, hang-, kép-, miniatűr-gyűjtemények, szöveg-és zárt feliratú fájlokat (valamint a fájlokra vonatkozó metaadatokat is). A fájlok az objektum tárolóba való feltöltése után a tartalom biztonságosan tárolódik a felhőben a további feldolgozáshoz és folyamatos átvitelhez.

Mielőtt megkezdené a kezdést, össze kell gyűjtenie vagy gondolnia kell néhány értéket.

1. A feltölteni kívánt fájl helyi fájljának elérési útja
1. Az eszközhöz tartozó eszköz azonosítója (tároló)
1. A feltöltött fájlhoz használni kívánt név, beleértve a kiterjesztést is
1. A használt Storage-fiók neve
1. A Storage-fiók elérési kulcsa

## <a name="portal"></a>[Portál](#tab/portal/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-portal.md)]

## <a name="cli"></a>[Parancssori felület](#tab/cli/)

[!INCLUDE [Upload files with the CLI](./includes/task-upload-file-to-asset-cli.md)]

## <a name="python"></a>[Python](#tab/python)

Feltételezve, hogy a kód már létrejött a hitelesítés, és már létrehozott egy bemeneti objektumot, a következő kódrészlettel tölthet fel helyi fájlokat az adott eszközre (in_container).

```python
#The storage objects
from azure.storage.blob import BlobServiceClient, BlobClient

#Establish storage variables
storage_account_name = '<your storage account name'
storage_account_key = '<your storage account key'
storage_blob_url = 'https://<your storage account name>.blob.core.windows.net/'

in_container = 'asset-' + inputAsset.asset_id

#The file path of local file you want to upload
source_file = "ignite.mp4"

# Use the Storage SDK to upload the video
blob_service_client = BlobServiceClient(account_url=storage_blob_url, credential=storage_account_key)
blob_client = blob_service_client.get_blob_client(in_container,source_file)

# Upload the video to storage as a block blob
with open(source_file, "rb") as data:
    blob_client.upload_blob(data, blob_type="BlockBlob")
```

---
<!-- add these to the tabs when available -->
Más módszerekkel kapcsolatban lásd: az [Azure Storage dokumentációja](../../storage/blobs/index.yml) a Blobok használatához a [.net](../../storage/blobs/storage-quickstart-blobs-dotnet.md), a [Java](../../storage/blobs/storage-quickstart-blobs-java.md), a [Python](../../storage/blobs/storage-quickstart-blobs-python.md)és a [JavaScript (Node.js)](../../storage/blobs/storage-quickstart-blobs-nodejs.md)használatával.

## <a name="next-steps"></a>Következő lépések

> [Media Services áttekintése](media-services-overview.md)

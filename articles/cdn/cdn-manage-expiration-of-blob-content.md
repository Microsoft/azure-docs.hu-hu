---
title: Az Azure Blob Storage lejáratának kezelése
titleSuffix: Azure Content Delivery Network
description: A Azure CDN gyorsítótárazási szolgáltatásban a Blobok élettartamának szabályozására szolgáló lehetőségek ismertetése.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ad4801e9-d09a-49bf-b35c-efdc4e6034e8
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: how-to
ms.date: 02/1/2018
ms.author: mazha
ms.openlocfilehash: 206ff6f888229356743bebb816cf03e4f7a7504b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92778704"
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-cdn"></a>Az Azure Blob Storage lejáratának kezelése Azure CDN
> [!div class="op_single_selector"]
> * [Azure webes tartalom](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob Storage](cdn-manage-expiration-of-blob-content.md)
> 
> 

Az Azure Storage [blob Storage szolgáltatása](../storage/common/storage-introduction.md#blob-storage) az Azure Content DELIVERY Network (CDN) szolgáltatással integrált Azure-alapú eredetek egyike. A nyilvánosan elérhető Blobok tartalma a Azure CDN gyorsítótárazható, amíg az élettartama (TTL) el nem telik. Az ÉLETTARTAMot a forráskiszolgáló által a `Cache-Control` http-válasz fejléce határozza meg. Ez a cikk számos módszert ismertet, amelyekkel megadhatja az `Cache-Control` Azure Storage-beli Blobok fejlécét.

A gyorsítótár beállításait a Azure Portal a CDN gyorsítótárazási szabályainak beállításával is szabályozhatja. Ha gyorsítótárazási szabályt hoz létre, és a gyorsítótárazási viselkedését a gyorsítótár **felülbírálására** vagy **megkerülésére** állítja be, a cikkben tárgyalt forrásként megadott gyorsítótárazási beállításokat a rendszer figyelmen kívül hagyja. További információ az általános gyorsítótárazási fogalmakról: [Hogyan működik a gyorsítótárazás](cdn-how-caching-works.md).

> [!TIP]
> Megadhatja, hogy a blobon ne legyen TTL. Ebben az esetben a Azure CDN automatikusan alkalmazza a hét nap alapértelmezett ÉLETTARTAMát, hacsak nem állított be gyorsítótárazási szabályokat a Azure Portal. Ez az alapértelmezett TTL csak az általános webes kézbesítés optimalizálására vonatkozik. Nagyméretű fájlok optimalizálása esetén az alapértelmezett TTL egy nap, és a média-adatfolyam optimalizálásához az alapértelmezett TTL egy év.
> 
> További információ arról, hogy a Azure CDN hogyan használható a blobokhoz és egyéb fájlokhoz való hozzáférés felgyorsításához: [Az Azure Content Delivery Network áttekintése](cdn-overview.md).
> 
> Az Azure Blob Storage szolgáltatással kapcsolatos további információkért lásd: [a blob Storage bemutatása](../storage/blobs/storage-blobs-introduction.md).
 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Cache-Control fejlécek beállítása CDN gyorsítótárazási szabályok használatával
A blob fejlécének beállításához javasolt módszer a `Cache-Control` Azure Portal gyorsítótárazási szabályainak használata. További információ a CDN gyorsítótárazási szabályairól: [Azure CDN gyorsítótárazási viselkedés szabályozása gyorsítótárazási szabályokkal](cdn-caching-rules.md).

> [!NOTE] 
> A gyorsítótárazási szabályok csak a **Verizon Azure CDN standard** csomagból és a Akamai-profilokból **Azure CDN standard** csomagból érhetők el. A Verizon-profilokból **származó Azure CDN Premium** esetén a hasonló funkciók **kezeléséhez** a felügyeleti portálon a [Azure CDN szabályok motort](./cdn-verizon-premium-rules-engine.md) kell használnia.

**A CDN-gyorsítótárazási szabályok lapra** való váltáshoz:

1. A Azure Portal válassza ki a CDN-profilt, majd válassza ki a blob végpontját.

2. A bal oldali ablaktáblán, a Beállítások alatt válassza a **Gyorsítótárszabályok** lehetőséget.

   ![CDN-gyorsítótárazási szabályok gomb](./media/cdn-manage-expiration-of-blob-content/cdn-caching-rules-btn.png)

   Megjelenik a **Gyorsítótárszabályok** lap.

   ![CDN-gyorsítótárazási oldal](./media/cdn-manage-expiration-of-blob-content/cdn-caching-page.png)


**BLOB Storage szolgáltatás Cache-Control fejlécének beállítása globális gyorsítótárazási szabályok használatával:**

1. A **globális gyorsítótárazási szabályok** területen állítsa be a **lekérdezési karakterlánc gyorsítótárazási viselkedését** a **lekérdezési karakterláncok figyelmen kívül hagyása** és a **gyorsítótárazási viselkedés** beállítása **felülbírálásra**
      
2. A **gyorsítótár lejárati idejének** esetében adja meg a 3600 értéket a **másodperc** mezőben, vagy 1 az **óra** mezőben. 

   ![CDN globális gyorsítótárazási szabályok – példa](./media/cdn-manage-expiration-of-blob-content/cdn-global-caching-rules-example.png)

   Ez a globális gyorsítótárazási szabály egy óra gyorsítótári időtartamát állítja be, és a végpontra irányuló összes kérést érinti. Felülbírálja `Cache-Control` `Expires` a végpont által megadott forráskiszolgáló által elküldett bármely vagy HTTP-fejlécet.   

3. Kattintson a **Mentés** gombra.
 
**BLOB-fájlok Cache-Control fejlécének beállítása egyéni gyorsítótárazási szabályok használatával:**

1. Az **Egyéni gyorsítótárazási szabályok** alatt hozzon létre két egyeztetési feltételt:

     A. Az első egyeztetési feltétel beállításnál állítsa az **egyeztetés feltételt** az **elérési út** értékre, és adja meg `/blobcontainer1/*` az **egyezési értéket**. Állítsa be a **gyorsítótárazási viselkedést** a **felülbíráláshoz** , és adja meg a 4 értéket az **óra** mezőben.

    B. A második egyeztetési feltételnél állítsa az **egyeztetés feltételt** az **elérési út** értékre, és adja meg `/blobcontainer1/blob1.txt` az **egyezési értéket**. Állítsa be a **gyorsítótárazási viselkedést** a **felülbíráláshoz** , és adja meg a 2 értéket az **órák** mezőben.

    ![CDN – egyéni gyorsítótárazási szabályok – példa](./media/cdn-manage-expiration-of-blob-content/cdn-custom-caching-rules-example.png)

    Az első egyéni gyorsítótárazási szabály a `/blobcontainer1` végpont által megadott forráskiszolgáló mappájában lévő blob-fájlok esetében négy órányi gyorsítótári időtartamot állít be. A második szabály csak a blob első szabályát felülbírálja, `blob1.txt` és két órás gyorsítótári időtartamot állít be.

2. Kattintson a **Mentés** gombra.


## <a name="setting-cache-control-headers-by-using-azure-powershell"></a>Cache-Control fejlécek beállítása Azure PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure PowerShell](/powershell/azure/) az egyik leggyorsabb és leghatékonyabb módszer az Azure-szolgáltatások felügyeletéhez. A `Get-AzStorageBlob` parancsmag segítségével szerezzen be egy hivatkozást a blobra, majd állítsa be a `.ICloudBlob.Properties.CacheControl` tulajdonságot. 

Például:

```powershell
# Create a storage context
$context = New-AzStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

> [!TIP]
> A PowerShell használatával is [kezelheti a CDN-profilokat és-végpontokat](cdn-manage-powershell.md).
> 
>

## <a name="setting-cache-control-headers-by-using-net"></a>Cache-Control fejlécek beállítása a .NET használatával
A blob fejlécének .NET-kóddal való megadásához `Cache-Control` használja a [.net-hez készült Azure Storage ügyféloldali kódtárat](../storage/blobs/storage-quickstart-blobs-dotnet.md) a [CloudBlob. properties. CacheControl](/dotnet/api/microsoft.azure.storage.blob.blobproperties.cachecontrol) tulajdonság beállításához.

Például:

```csharp
class Program
{
    const string connectionString = "<storage connection string>";
    static void Main()
    {
        // Retrieve storage account information from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);

        // Create a blob client for interacting with the blob service.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Create a reference to the container
        CloudBlobContainer <container name> = blobClient.GetContainerReference("<container name>");

        // Create a reference to the blob
        CloudBlob <blob name> = container.GetBlobReference("<blob name>");

        // Set the CacheControl property to expire in 1 hour (3600 seconds)
        blob.Properties.CacheControl = "max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

> [!TIP]
> Több .NET-kód is elérhető az [Azure Blob Storage Samples for .net](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)-ben.
> 

## <a name="setting-cache-control-headers-by-using-other-methods"></a>Cache-Control fejlécek beállítása más metódusok használatával

### <a name="azure-storage-explorer"></a>Azure Storage Explorer
A [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)segítségével megtekintheti és szerkesztheti a blob Storage-erőforrásokat, beleértve a tulajdonságokat, például a *CacheControl* tulajdonságot. 

BLOB *CacheControl* tulajdonságának frissítése Azure Storage Explorer használatával:
   1. Válasszon ki egy blobot, majd válassza a **Tulajdonságok** elemet a helyi menüből. 
   2. Görgessen le a *CacheControl* tulajdonsághoz.
   3. Adjon meg egy értéket, majd kattintson a **Mentés** gombra.


![Azure Storage Explorer tulajdonságai](./media/cdn-manage-expiration-of-blob-content/cdn-storage-explorer-properties.png)

### <a name="azure-command-line-interface"></a>Azure parancssori felület
Az [azure Command-Line Interface](/cli/azure) (CLI) használatával az Azure Blob-erőforrásokat a parancssorból kezelheti. Ha a Cache-Control fejlécet a blob Azure CLI-vel való feltöltésekor szeretné beállítani, a kapcsoló használatával állítsa be a *cacheControl* tulajdonságot `-p` . Az alábbi példa bemutatja, hogyan állíthatja be az ÉLETTARTAMot egy órára (3600 másodperc):
  
```azurecli
azure storage blob upload -c <connectionstring> -p cacheControl="max-age=3600" .\<blob name> <container name> <blob name>
```

### <a name="azure-storage-services-rest-api"></a>Azure Storage-szolgáltatások REST API
Az [Azure Storage services REST API](/rest/api/storageservices/) használatával explicit módon állíthatja be az *x-MS-blob-Cache-Control* tulajdonságot a következő műveletekkel egy kérelemre:
  
   - [Put Blob](/rest/api/storageservices/Put-Blob)
   - [Tiltási lista](/rest/api/storageservices/Put-Block-List)
   - [BLOB tulajdonságainak beállítása](/rest/api/storageservices/Set-Blob-Properties)

## <a name="testing-the-cache-control-header"></a>A Cache-Control fejléc tesztelése
Könnyedén ellenőrizheti a Blobok ÉLETTARTAMának beállításait. A böngésző [fejlesztői eszközeivel](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)ellenőrizze, hogy a blob tartalmazza-e a `Cache-Control` Válasz fejlécét. Olyan eszközt is használhat, mint például a [wget](https://www.gnu.org/software/wget/), a [Poster](https://www.getpostman.com/)vagy a [Hegedűs](https://www.telerik.com/fiddler) , és megvizsgálhatja a válasz fejléceit.

## <a name="next-steps"></a>Következő lépések
* [Ismerje meg, hogyan kezelheti a Cloud Service-tartalmak elévülését Azure CDN](cdn-manage-expiration-of-cloud-service-content.md)
* [Tudnivalók a gyorsítótárazási fogalmakról](cdn-how-caching-works.md)
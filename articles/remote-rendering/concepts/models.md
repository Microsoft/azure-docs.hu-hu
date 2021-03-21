---
title: Modellek
description: Leírja, hogy mi a modell az Azure Remote rendering szolgáltatásban
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 0d1e66d09db3e3934871ed15493feb685d1cbe6a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99593874"
---
# <a name="models"></a>Modellek

Az Azure Remote rendering *modellje* egy teljes objektum-ábrázolásra utal, amely [entitásokból](entities.md) és [összetevőkből](components.md)áll. A modellek a legfontosabb módszer a távoli renderelési szolgáltatásba való egyéni adatgyűjtésre.

## <a name="model-structure"></a>Modellstruktúra

A modell pontosan egy [entitást](entities.md) tartalmaz, mint a legfelső szintű csomópont. Alább látható, hogy az alárendelt entitások tetszőleges hierarchiája lehet. Modell betöltésekor a rendszer erre a gyökérszintű entitásra mutató hivatkozást ad vissza.

Előfordulhat, hogy minden entitáshoz vannak csatolva [összetevők](components.md) . A leggyakoribb esetben az entitások rendelkeznek *MeshComponents*, amelyek a [háló erőforrásokra](meshes.md)hivatkoznak.

## <a name="creating-models"></a>Modellek létrehozása

A futtatókörnyezet modelljeinek létrehozása a bemeneti modellek fájlformátumokból (például FBX és GLTF) való [átalakításával](../how-tos/conversion/model-conversion.md) érhető el. Az átalakítási folyamat kibontja az összes erőforrást, például a textúrákat, az anyagokat és a hálókat, majd konvertálja azokat optimalizált futtatókörnyezeti formátumokra. Emellett a strukturális adatokat is kinyeri és átalakítja az ARR entitás/összetevő gráf-struktúrájába.

> [!IMPORTANT]
> A [modell konvertálása](../how-tos/conversion/model-conversion.md) az egyetlen módszer a [Rácsvonalak](meshes.md)létrehozására. Bár a rácsvonalak megoszthatók az entitások között futásidőben, nincs más mód arra, hogy a modell betöltésén kívül más módon is beolvasson egy rácsvonalat a futtatókörnyezetbe.

## <a name="loading-models"></a>Modellek betöltése

A modell konvertálása után az Azure Blob Storage-ból tölthető be a futtatókörnyezetbe.

Két különböző betöltési függvény van, amelyek eltérnek az eszköz blob Storage-ban való címzésének módjától:

* A modellt közvetlenül a blob Storage-paraméterekkel lehet megoldani, abban az esetben, ha a [blob Storage a fiókhoz van társítva](../how-tos/create-an-account.md#link-storage-accounts). Ebben az esetben a megfelelő betöltési függvény a `LoadModelAsync` paraméter `LoadModelOptions` .
* A modellt a SAS URI-ja tudja kezelni. A megfelelő betöltési függvény `LoadModelFromSasAsync` paraméterrel van ellátva `LoadModelFromSasOptions` . A [beépített modellek](../samples/sample-model.md)betöltésekor ezt a változatot is használhatja.

A következő kódrészletek bemutatják, hogyan tölthetők be modellek bármelyik függvénnyel. A modellek blob Storage-paraméterekkel való betöltéséhez használja az alábbihoz hasonló kódot:


```cs
async void LoadModel(RenderingSession session, Entity modelParent, string storageAccount, string containerName, string assetFilePath)
{
    // load a model that will be parented to modelParent
    var modelOptions = new LoadModelOptions(
        storageAccount, // storage account name + '.blob.core.windows.net', e.g., 'mystorageaccount.blob.core.windows.net'
        containerName,  // name of the container in your storage account, e.g., 'mytestcontainer'
        assetFilePath,  // the file path to the asset within the container, e.g., 'path/to/file/myAsset.arrAsset'
        modelParent
    );

    var loadOp = session.Connection.LoadModelAsync(modelOptions, (float progress) =>
    {
        Debug.WriteLine($"Loading: {progress * 100.0f}%");
    });

    await loadOp;
}
```

```cpp
void LoadModel(ApiHandle<RenderingSession> session, ApiHandle<Entity> modelParent, std::string storageAccount, std::string containerName, std::string assetFilePath)
{
    LoadModelOptions modelOptions;
    modelOptions.Parent = modelParent;
    modelOptions.Blob.StorageAccountName = std::move(storageAccount);
    modelOptions.Blob.BlobContainerName = std::move(containerName);
    modelOptions.Blob.AssetPath = std::move(assetFilePath);

    ApiHandle<LoadModelResult> result;
    session->Connection()->LoadModelAsync(modelOptions,
        // completion callback
        [](Status status, ApiHandle<LoadModelResult> result)
        {
            printf("Loading: finished.");
        },
        // progress callback
        [](float progress)
        {
            printf("Loading: %.1f%%", progress * 100.f);
        }
    );
}
```

Ha SAS-token használatával szeretne betölteni egy modellt, használja az alábbi kódrészlethez hasonló kódot:

```cs
async void LoadModel(RenderingSession session, Entity modelParent, string modelUri)
{
    // load a model that will be parented to modelParent
    var modelOptions = new LoadModelFromSasOptions(modelUri, modelParent);

    var loadOp = session.Connection.LoadModelFromSasAsync(modelOptions, (float progress) =>
    {
        Debug.WriteLine($"Loading: {progress * 100.0f}%");
    });

    await loadOp;
}
```

```cpp
void LoadModel(ApiHandle<RenderingSession> session, ApiHandle<Entity> modelParent, std::string modelUri)
{
    LoadModelFromSasOptions modelOptions;
    modelOptions.ModelUri = modelUri;
    modelOptions.Parent = modelParent;

    ApiHandle<LoadModelResult> result;
    session->Connection()->LoadModelFromSasAsync(modelOptions,
        // completion callback
        [](Status status, ApiHandle<LoadModelResult> result)
        {
            printf("Loading: finished.");
        },
        // progress callback
        [](float progress)
        {
            printf("Loading: %.1f%%", progress * 100.f);
        }
    );
}
```

Ezután áthaladhat az entitás-hierarchián, és módosíthatja az entitásokat és összetevőket. Ha ugyanaz a modell többször is be van töltve, több példányt hoz létre, amelyek mindegyike az entitás/összetevő struktúrájának saját példányával rendelkezik. Mivel a rácsvonalak, az anyagok és a textúrák [közös erőforrások](../concepts/lifetime.md), az adataik azonban nem lesznek újra betöltve. Ezért a modell többszöri létrehozása nem éri el a viszonylag kevés memória terhelését.

## <a name="api-documentation"></a>API-dokumentáció

* [C# RenderingConnection. LoadModelAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadmodelasync)
* [C# RenderingConnection. LoadModelFromSasAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadmodelfromsasasync)
* [C++ RenderingConnection:: LoadModelAsync ()](/cpp/api/remote-rendering/renderingconnection#loadmodelasync)
* [C++ RenderingConnection:: LoadModelFromSasAsync ()](/cpp/api/remote-rendering/renderingconnection#loadmodelfromsasasync)

## <a name="next-steps"></a>Következő lépések

* [Entitások](entities.md)
* [Hálók](meshes.md)
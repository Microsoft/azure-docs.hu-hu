---
title: Textúrák
description: Textúra erőforrás-munkafolyamata
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: e01ddf0690f11d41021e0a5ae5958c7c80646743
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594417"
---
# <a name="textures"></a>Textúrák

A textúrák egy megváltoztathatatlan [megosztott erőforrás](../concepts/lifetime.md). A textúrák a [blob Storage](../how-tos/conversion/blob-storage.md) -ból tölthetők be, és közvetlenül a modellekre alkalmazhatók, ahogy az [oktatóanyagban is látható: a környezet és az anyagok módosítása](../tutorials/unity/materials-lighting-effects/materials-lighting-effects.md). Leggyakrabban azonban a textúrák egy [átalakított modell](../how-tos/conversion/model-conversion.md)részei lesznek, ahol az [anyagok](materials.md)hivatkoznak rájuk.

## <a name="texture-types"></a>Textúra típusai

A különböző típusú anyagminták különböző használati esetekkel rendelkeznek:

* a **2D-textúrák** főleg [anyagokban](materials.md)használatosak.
* A **Cubemaps** használható az [égbolton](../overview/features/sky.md).

## <a name="supported-texture-formats"></a>Támogatott textúra-formátumok

Az ARR-nek adott összes textúráknak [DDS formátumúnak](https://en.wikipedia.org/wiki/DirectDraw_Surface)kell lennie. Lehetőleg a Mipmap és a textúra tömörítéssel.

## <a name="loading-textures"></a>Textúrák betöltése

Textúra betöltésekor meg kell adnia a várt típust. Ha a típus nem egyezik, a textúra betöltése sikertelen lesz.
Ha egy textúrát kétszer tölt be ugyanazzal az URI-val, akkor ugyanazt a textúrát fogja visszaadni, mivel ez egy [megosztott erőforrás](../concepts/lifetime.md).

A modellek betöltéséhez hasonlóan két változatban kell foglalkoznia a forrás blob Storage-ban található textúra-objektummal:

* A textúrát közvetlenül a blob Storage-paraméterekkel lehet megoldani, abban az esetben, ha a [blob Storage a fiókhoz van társítva](../how-tos/create-an-account.md#link-storage-accounts). Ebben az esetben a megfelelő betöltési függvény a `LoadTextureAsync` paraméter `LoadTextureOptions` .
* A textúrát az SAS URI-ja tudja kezelni. A megfelelő betöltési függvény `LoadTextureFromSasAsync` paraméterrel van ellátva `LoadTextureFromSasOptions` . A [beépített textúrák](../overview/features/sky.md#built-in-environment-maps)betöltésekor ezt a változatot is használhatja.

Az alábbi mintakód bemutatja, hogyan tölthető be a textúra:

```cs
async void LoadMyTexture(RenderingSession session, string storageContainer, string blobName, string assetPath)
{
    try
    {
        LoadTextureOptions options = new LoadTextureOptions(storageContainer, blobName, assetPath, TextureType.Texture2D);
        Texture texture = await session.Connection.LoadTextureAsync(options);
    
        // use texture...
    }
    catch (RRException ex)
    {
    }
}
```

```cpp
void LoadMyTexture(ApiHandle<RenderingSession> session, std::string storageContainer, std::string blobName, std::string assetPath)
{
    LoadTextureOptions params;
    params.TextureType = TextureType::Texture2D;
    params.Blob.StorageAccountName = std::move(storageContainer);
    params.Blob.BlobContainerName = std::move(blobName);
    params.Blob.AssetPath = std::move(assetPath);
    session->Connection()->LoadTextureAsync(params, [](Status status, ApiHandle<Texture> texture)
    {
        // use texture...
    });
}
```

Vegye figyelembe, hogy SAS-változatának használata esetén csak a loading Function/paraméter különbözik.

Attól függően, hogy mit kell használni a textúrában, a textúra típusa és tartalma korlátozásokat is tartalmazhat. Például a [pbr-anyagokhoz](../overview/features/pbr-materials.md) tartozó érdesség térképének szürkeárnyalatos kell lennie.

## <a name="api-documentation"></a>API-dokumentáció

* [C# textúra osztály](/dotnet/api/microsoft.azure.remoterendering.texture)
* [C# RenderingConnection. LoadTextureAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadtextureasync)
* [C# RenderingConnection. LoadTextureFromSasAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadtexturefromsasasync)
* [C++ textúra osztály](/cpp/api/remote-rendering/texture)
* [C++ RenderingConnection:: LoadTextureAsync ()](/cpp/api/remote-rendering/renderingconnection#loadtextureasync)
* [C++ RenderingConnection:: LoadTextureFromSasAsync ()](/cpp/api/remote-rendering/renderingconnection#loadtexturefromsasasync)

## <a name="next-steps"></a>Következő lépések

* [Anyagok](materials.md)
* [Ég](../overview/features/sky.md)
---
title: Objektumhatárok
description: Ismerteti, hogyan lehet lekérdezni a térbeli objektumok határait
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: df04b767035dffb62fde89d1e74b808d62fcc943
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594484"
---
# <a name="object-bounds"></a>Objektumhatárok

Az objektum-korlátok az [entitások](entities.md) és a gyermekeik által elfoglalt köteteket jelölik. Az Azure távoli renderelésben a rendszer mindig a *tengelyhez igazított határolókeret* (AABB) jelölésű objektum-határokat adja meg. Az objektumok határai a *helyi térben* vagy a *globális térben* is lehetnek. Mindkét esetben a tengely igazítva van, ami azt jelenti, hogy a mértékek és a kötetek eltérhetnek a helyi és a globális terület ábrázolástól.

## <a name="querying-object-bounds"></a>Objektum-korlátok lekérdezése

A [Rácsvonalak](meshes.md) helyi tengelyre igazított korlátja közvetlenül a Mesh erőforrásból kérdezhető le. Ezek a korlátok az entitás átalakításával alakíthatók át egy entitás helyi területére vagy területére.

Ily módon lehetséges a teljes objektum-hierarchia határainak kiszámítása, de ez a hierarchia bejárásához, az egyes rácsvonalak határainak lekérdezéséhez, valamint a manuális összekapcsolásához szükséges. Ez a művelet unalmas és nem hatékony.

A jobb módszer az `QueryLocalBoundsAsync` entitások hívása vagy használata `QueryWorldBoundsAsync` . Ekkor a rendszer kiszervezi a számítást a kiszolgálóra, és minimális késleltetéssel visszaadja.

```cs
public async void GetBounds(Entity entity)
{
    try
    {
        Task<Bounds> boundsQuery = entity.QueryWorldBoundsAsync();
        Bounds result = await boundsQuery;
    
        Double3 aabbMin = result.Min;
        Double3 aabbMax = result.Max;
        // ...
    }
    catch (RRException ex)
    {
    }
}
```

```cpp
void GetBounds(ApiHandle<Entity> entity)
{
    entity->QueryWorldBoundsAsync(
        // completion callback:
        [](Status status, Bounds bounds)
        {
           if (status == Status::OK)
            {
                Double3 aabbMin = bounds.Min;
                Double3 aabbMax = bounds.Max;
                // ...
            }
        }
    );
}
```

## <a name="api-documentation"></a>API-dokumentáció

* [C# entitás. QueryLocalBoundsAsync](/dotnet/api/microsoft.azure.remoterendering.entity.querylocalboundsasync)
* [C++ entitás:: QueryLocalBoundsAsync](/cpp/api/remote-rendering/entity#querylocalboundsasync)

## <a name="next-steps"></a>Következő lépések

* [Térbeli lekérdezések](../overview/features/spatial-queries.md)
---
title: Térbeli lekérdezések
description: Térbeli lekérdezések a jelenetekben
author: jakrams
ms.author: jakras
ms.date: 02/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: c664df586c260b3e16f64c071190055dbaeccd24
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594044"
---
# <a name="spatial-queries"></a>Térbeli lekérdezések

A térbeli lekérdezések olyan műveletek, amelyekkel megkérheti a távoli renderelési szolgáltatást, amely egy adott helyen található objektumok. A térbeli lekérdezéseket gyakran használják az interakciók megvalósításához, például kitalálni, hogy a felhasználó melyik objektumot mutat.

Az összes térbeli lekérdezés kiértékelése a kiszolgálón történik. Ezért az aszinkron műveletek, és az eredmények a hálózati késéstől függő késéssel érkeznek. Mivel minden térbeli lekérdezés hálózati forgalmat generál, ügyeljen arra, hogy ne legyen túl sok egyszerre.

## <a name="collision-meshes"></a>Ütközési rácsvonalak

A térbeli lekérdezéseket a [Havok fizikai](https://www.havok.com/products/havok-physics) motor működteti, és egy dedikált ütközési rácsvonalat kell megadnia. Alapértelmezés szerint a [modell konvertálása](../../how-tos/conversion/model-conversion.md) ütközési hálókat hoz létre. Ha nem igényel térbeli lekérdezéseket egy összetett modellen, érdemes lehet letiltani az ütközések hálójának létrehozását az [átalakítási beállításokban](../../how-tos/conversion/configure-model-conversion.md), mivel ez több módon is kihat:

* A [modell konvertálása](../../how-tos/conversion/model-conversion.md) jóval hosszabb időt vesz igénybe.
* A konvertált modell fájlmérete észrevehetően nagyobb, ami hatással van a letöltési sebességre.
* A futásidejű betöltési ideje már nem érhető el.
* A futásidejű CPU-memória kihasználtsága magasabb.
* A modell minden példánya esetében kis teljesítményű futtatókörnyezeti teljesítménnyel kell számolni.

## <a name="ray-casts"></a>Ray-öntvények

A *Ray Cast* egy térbeli lekérdezés, amelyben a futtatókörnyezet ellenőrzi, hogy egy fénysugár milyen objektumokat metsz el egy adott pozíciótól kezdve, és egy adott irányba mutat. Optimalizálásként a maximális távolságot is megadhatja, ha nem keres olyan objektumokat, amelyek túl távol vannak.

```cs
async void CastRay(RenderingSession session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast = new RayCast(new Double3(0, 0, 0), new Double3(0, 0, 1), 10);

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy.ClosestHit;

    RayCastQueryResult result = await session.Connection.RayCastQueryAsync(rayCast);
    RayCastHit[] hits = result.Hits;
    if (hits.Length > 0)
    {
        var hitObject = hits[0].HitObject;
        var hitPosition = hits[0].HitPosition;
        var hitNormal = hits[0].HitNormal;

        // do something with the hit information
    }
}
```

```cpp
void CastRay(ApiHandle<RenderingSession> session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast;
    rayCast.StartPos = {0, 0, 0};
    rayCast.EndPos = {0, 0, 1};
    rayCast.MaxHits = 10;

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy::ClosestHit;

    session->Connection()->RayCastQueryAsync(rayCast, [](Status status, ApiHandle<RayCastQueryResult> result)
    {
        if (status == Status::OK)
        {
            std::vector<RayCastHit> hits;
            result->GetHits(hits);

            if (hits.size() > 0)
            {
                auto hitObject = hits[0].HitObject;
                auto hitPosition = hits[0].HitPosition;
                auto hitNormal = hits[0].HitNormal;

                // do something with the hit information
            }
        }
    });
}
```


Három találati gyűjtési mód van:

* **`Closest` :** Ebben a módban csak a legközelebbi találatot fogja jelenteni.
* **`Any` :** Ezt a módot érdemes használni, ha azt szeretné tudni, hogy a Ray megtalál-e bármit, de nem érdekli, *hogy* pontosan mi volt a találat. Ez a lekérdezés jelentősen olcsóbbá teheti a kiértékelést, de csak néhány alkalmazást is tartalmaz.
* **`All` :** Ebben a módban a Ray összes találata látható, távolság szerint rendezve. Ne használja ezt a módot, ha nincs szüksége az első találatra. Korlátozza a jelentett találatok számát a `MaxHits` kapcsolóval.

Ha az objektumokat szelektív módon szeretné kizárni a Ray-öntvények számára, akkor a [HierarchicalStateOverrideComponent](override-hierarchical-state.md) összetevő használható.

<!--
The CollisionMask allows the query to consider or ignore some objects based on their collision layer. If an object has layer L, it will be hit only if the mask has bit L set.
It is useful in case you want to ignore objects, for instance when setting an object transparent, and trying to select another object behind it.
TODO : Add an API to make that possible.
-->

### <a name="hit-result"></a>Találat eredménye

A Ray Cast lekérdezés eredménye a találatok tömbje. A tömb üres, ha nincs találat.

A találatok a következő tulajdonságokkal rendelkeznek:

* **`HitEntity` :** Melyik [entitást](../../concepts/entities.md) észlelt a program.
* **`SubPartId` :** Melyik *alhálót* találta meg egy [MeshComponent](../../concepts/meshes.md). A használatával indexelheti `MeshComponent.UsedMaterials` a-t, és megkeresheti az [anyagot](../../concepts/materials.md) ezen a ponton.
* **`HitPosition` :** Az a hely, ahol a fénysugár összemetszi az objektumot.
* **`HitNormal` :** A háló területének normál mérete a rácsvonal pozíciójában.
* **`DistanceToHit` :** A sugár kezdő pozíciója és a találat közötti távolság.

## <a name="api-documentation"></a>API-dokumentáció

* [C# RenderingConnection. RayCastQueryAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.raycastqueryasync)
* [C++ RenderingConnection:: RayCastQueryAsync ()](/cpp/api/remote-rendering/renderingconnection#raycastqueryasync)

## <a name="next-steps"></a>Következő lépések

* [Objektumhatárok](../../concepts/object-bounds.md)
* [Hierarchikus állapotok felülbírálása](override-hierarchical-state.md)
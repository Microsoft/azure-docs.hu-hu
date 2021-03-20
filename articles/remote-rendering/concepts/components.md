---
title: Összetevők
description: Az Azure távoli renderelés hatókörében található összetevők definíciója
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 9679be03c69090a0c11d007cfc542bae70bd3cbc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99592194"
---
# <a name="components"></a>Összetevők

Az Azure távoli renderelés az [entitás-összetevő](https://en.wikipedia.org/wiki/Entity_component_system) rendszermintáját használja. Míg az [entitások](entities.md) képviselik a pozíciót és az objektumok hierarchikus összetételét, az összetevők a viselkedés végrehajtásáért felelősek.

Az összetevők leggyakrabban használt típusai [:::no-loc text="mesh components":::](meshes.md) , amelyek rácsvonalak hozzáadását teszik elérhetővé a renderelési folyamatban. Hasonlóképpen, a [világos összetevőket](../overview/features/lights.md) a rendszer a világító és a [kivágási sík összetevők](../overview/features/cut-planes.md) hozzáadására használja a nyitott rácsvonalak kivágására.

Ezek az összetevők a hivatkozási pontként, a hozzájuk kapcsolódó entitások átalakítását (pozícióját, elforgatását, méretezését) használják.

## <a name="working-with-components"></a>Összetevők használata

Az összetevők programozott módon egyszerűen hozzáadhatók, eltávolíthatók és kezelhetők:

```cs
// create a point light component
RenderingSession session = GetCurrentlyConnectedSession();
PointLightComponent lightComponent = session.Connection.CreateComponent(ObjectType.PointLightComponent, ownerEntity) as PointLightComponent;

lightComponent.Color = new Color4Ub(255, 150, 20, 255);
lightComponent.Intensity = 11;

// ...

// destroy the component
lightComponent.Destroy();
lightComponent = null;
```

```cpp
// create a point light component
ApiHandle<RenderingSession> session = GetCurrentlyConnectedSession();

ApiHandle<PointLightComponent> lightComponent = session->Connection()->CreateComponent(ObjectType::PointLightComponent, ownerEntity)->as<PointLightComponent>();

// ...

// destroy the component
lightComponent->Destroy();
lightComponent = nullptr;
```

Az összetevők létrehozáskor egy entitáshoz vannak csatolva. Ezt követően nem helyezhető át másik entitásba. Az összetevők explicit módon törlődnek a `Component.Destroy()` vagy automatikusan, ha az összetevő tulajdonosi entitása megsemmisül.

Egyszerre csak egy példány adható hozzá egy entitáshoz.

## <a name="unity-specific"></a>Unity-specifikus

Az Unity Integration további bővítmény-funkciókat biztosít az összetevőkkel való interakcióhoz. Lásd: [Unity game Objects és Components](../how-tos/unity/objects-components.md).

## <a name="api-documentation"></a>API-dokumentáció

* [C# ComponentBase](/dotnet/api/microsoft.azure.remoterendering.componentbase)
* [C# RenderingConnection. CreateComponent ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.createcomponent)
* [C# entitás. FindComponentOfType ()](/dotnet/api/microsoft.azure.remoterendering.entity.findcomponentoftype)
* [C++ ComponentBase](/cpp/api/remote-rendering/componentbase)
* [C++ RenderingConnection:: CreateComponent ()](/cpp/api/remote-rendering/renderingconnection#createcomponent)
* [C++ entitás:: FindComponentOfType ()](/cpp/api/remote-rendering/entity#findcomponentoftype)

## <a name="next-steps"></a>Következő lépések

* [Objektumhatárok](object-bounds.md)
* [Hálók](meshes.md)
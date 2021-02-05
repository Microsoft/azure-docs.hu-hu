---
title: Síkok kivágása
description: Ismerteti a kivágott síkokat és azok használatát
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: b3348e5a999b507aa0d286528970beb0e03f26cd
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594372"
---
# <a name="cut-planes"></a>Síkok kivágása

A *kivágott sík* olyan vizualizációs funkció, amely egy virtuális sík egyik oldalán lévő képpontokat jelenít meg, és kifedi a [Rácsvonalak](../../concepts/meshes.md)belsejét.
Az alábbi képen a hatás látható. A bal oldalon az eredeti rácsvonal látható, amely a jobb oldalon a Hálón belül látható:

![Sík kivágása](./media/cutplane-1.png)

## <a name="cutplanecomponent"></a>CutPlaneComponent

Egy *CutPlaneComponent* létrehozásával hozzáadhat egy kivágási síkot a jelenethez. A sík helyét és tájolását az összetevő tulajdonosi [entitása](../../concepts/entities.md)határozza meg.

```cs
void CreateCutPlane(RenderingSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Connection.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

```cpp
void CreateCutPlane(ApiHandle<RenderingSession> session, ApiHandle<Entity> ownerEntity)
{
    ApiHandle<CutPlaneComponent> cutPlane = session->Connection()->CreateComponent(ObjectType::CutPlaneComponent, ownerEntity)->as<CutPlaneComponent>();;
    cutPlane->SetNormal(Axis::X); // normal points along the positive x-axis of the owner object's orientation
    Color4Ub fadeColor;
    fadeColor.channels = { 255, 0, 0, 128 }; // fade to 50% red
    cutPlane->SetFadeColor(fadeColor);
    cutPlane->SetFadeLength(0.05f); // gradient width: 5cm
}
```

### <a name="cutplanecomponent-properties"></a>CutPlaneComponent tulajdonságai

A következő tulajdonságok vannak kitéve egy kivágott sík összetevőn:

* `Enabled`: Ideiglenesen kikapcsolhatja a kivágott síkokat az összetevő letiltásával. A letiltott síkok nem terhelik a renderelési terhelést, és nem számítanak bele a globálisan kivágott sík korlátba.

* `Normal`: Megadja, hogy a rendszer melyik irányt (+ X,-X, + Y,-Y, + Z,-Z) használja a normál síkon. Ez az irány a tulajdonos entitás tájolásához képest relatív. Helyezze át és forgassa el a tulajdonos entitást a pontos elhelyezéshez.

* `FadeColor` és `FadeLength` :

  Ha a *FadeColor* alfa értéke nem nulla, a kivágási sík közelébe mutató képpontok a FadeColor RGB-részéhez fognak elhalványulni. Az alfa-csatorna erőssége határozza meg, hogy teljes mértékben elhalványul-e az elhalványulás színe, vagy csak részben. A *FadeLength* határozza meg, hogy a Halványítás milyen távolságra legyen végrehajtva.

* `ObjectFilterMask`: Egy szűrő-bit maszk, amely meghatározza, hogy a kivágott sík milyen geometriát érint. Részletes információkat a következő bekezdésben talál.

### <a name="selective-cut-planes"></a>Szelektív kivágott síkok

Az egyes kivágási síkok konfigurálhatók úgy, hogy csak a megadott geometriára legyenek hatással. Az alábbi képen látható, hogyan jelennek meg a beállítás a gyakorlatban:

![Szelektív kivágott síkok](./media/selective-cut-planes.png)

A szűrés a **logikai bitek összevetését** , a kivágási sík oldalán lévő egy kis maszk és a geometriában beállított második bites maszk között működik. Ha a `AND` maszkok közötti logikai művelet eredménye nem nulla, a kivágott sík hatással lesz a geometriára.

* A kivágási sík összetevőn lévő bit mask a tulajdonságán keresztül van beállítva `ObjectFilterMask`
* A geometriai alhierarchián a [HierarchicalStateOverrideComponent](override-hierarchical-state.md#features) a

Angol nyelvű Példák:

| Sík szűrő maszkjának kivágása | Geometriai szűrő maszkja  | Logikai eredmény `AND` | A kivágott sík befolyásolja a geometriát?  |
|--------------------|-------------------|-------------------|:----------------------------:|
| (0000 0001) = = 1   | (0000 0001) = = 1  | (0000 0001) = = 1  | Yes |
| (1111 0000) = = 240 | (0001 0001) = = 17 | (0001 0000) = = 16 | Yes |
| (0000 0001) = = 1   | (0000 0010) = = 2  | (0000 0000) = = 0  | No |
| (0000 0011) = = 3   | (0000 1000) = = 8  | (0000 0000) = = 0  | No |

>[!TIP]
> A kivágott sík 0 értékre állítása azt `ObjectFilterMask` jelenti, hogy nem befolyásolja a geometriát, mert a logikai eredmény nem `AND` lehet null. A renderelési rendszer nem veszi figyelembe ezeket a gépeket az első helyen, ezért ez egy egyszerű módszer az egyes kivágott síkok letiltására. Ezek a kivágott síkok szintén nem számítanak bele a 8 aktív síkon.

## <a name="limitations"></a>Korlátozások

* Az Azure Remote rendering **legfeljebb nyolc aktív kivágott gépet** támogat. Több kivágási sík-összetevőt is létrehozhat, de ha több párhuzamosan próbálkozik, a rendszer figyelmen kívül hagyja az aktiválást. Ha azt szeretné, hogy az egyes összetevők milyen hatással legyenek a színtérre, tiltsa le a többi gépet.
* A kivágott síkok tisztán vizualizációs funkciók, nem befolyásolják a [térbeli lekérdezések](spatial-queries.md)eredményét. Ha azt szeretné, hogy a Ray kivágású nyitott rácsvonalba kerüljön, beállíthatja, hogy a Ray kiindulási pontja a kivágási síkon legyen. Így a fénysugár csak a látható részeket tudja megütni.

## <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások

Az aktív kivágott síkok kis költségekkel járnak a renderelés során. A kivágott síkok letiltása vagy törlése, ha nem szükségesek.

## <a name="api-documentation"></a>API-dokumentáció

* [C# CutPlaneComponent osztály](/dotnet/api/microsoft.azure.remoterendering.cutplanecomponent)
* [C++ CutPlaneComponent osztály](/cpp/api/remote-rendering/cutplanecomponent)

## <a name="next-steps"></a>Következő lépések

* [Egyoldalas megjelenítés](single-sided-rendering.md)
* [Térbeli lekérdezések](spatial-queries.md)
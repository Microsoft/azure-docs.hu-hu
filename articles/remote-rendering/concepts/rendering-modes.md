---
title: Renderelési módok
description: Ismerteti a kiszolgálóoldali megjelenítés különböző módjait
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 2cf1872bcdd7b1bda74046198f5fc32be1069913
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594501"
---
# <a name="rendering-modes"></a>Renderelési módok

A távoli renderelés két fő üzemmódot kínál: a **TileBasedComposition** mód és a **DepthBasedComposition** mód. Ezek a módok határozzák meg, hogyan oszlanak meg a számítási feladatok több GPU-ban a kiszolgálón. A módot a kapcsolódás időpontjában meg kell adni, és a Futtatás közben nem módosítható.

Mindkét mód előnyökkel jár, de a szolgáltatásra vonatkozó korlátozásokkal is rendelkezik, ezért a legmegfelelőbb mód kiválasztása a kis-és nagybetűket használja.

## <a name="modes"></a>Módok

A két mód már részletesebben szerepel.

### <a name="tilebasedcomposition-mode"></a>"TileBasedComposition" üzemmód

**TileBasedComposition** módban az összes érintett GPU a képernyőn megadott alnégyszögeket (csempéket) jeleníti meg. A fő GPU alkotja a csempék végső képét, mielőtt a rendszer videó keretként elküldi az ügyfélnek. Ennek megfelelően minden GPU-nak ugyanazokat az erőforrásokat kell megadnia a rendereléshez, így a betöltött objektumoknak egyetlen GPU memóriába kell illeszkednie.

A megjelenítési minőség ebben a módban valamivel jobb, mint a **DepthBasedComposition** módban, mivel a MSAA a geometriák teljes készletén MŰKÖDHET minden GPU számára. Az alábbi képernyőfelvételen látható, hogy az élsimítás megfelelően működik mindkét élek esetében:

![MSAA a TileBasedComposition](./media/service-render-mode-quality.png)

Emellett ebben a módban az egyes részek transzparens anyagokra válthatók, vagy a [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md) keresztül átállíthatók a **megtekintési** módra

### <a name="depthbasedcomposition-mode"></a>"DepthBasedComposition" üzemmód

**DepthBasedComposition** módban minden érintett GPU teljes képernyős felbontásban jelenik meg, de csak a rácsvonalak egy részhalmaza. A fő GPU-ban a végső képösszeállítás gondoskodik arról, hogy a részek megfelelően legyenek egyesítve a részletes információk alapján. A memóriahasználat természetesen a GPU-k között oszlik el, így lehetővé teszi olyan modellek megjelenítését, amelyek nem illenek egyetlen GPU memóriába.

Minden egyes GPU MSAA használ az élsimítás helyi tartalmára. Azonban előfordulhat, hogy a különböző GPU-k szélei között belső alias található. Ezt a hatást a utófeldolgozó a végső rendszerkép csökkenti, de a MSAA minősége még rosszabb, mint **TileBasedComposition** módban.

A MSAA összetevők a következő képen láthatók: ![ MSAA in DepthBasedComposition](./media/service-render-mode-balanced.png)

Az élsimítás megfelelően működik a szobor és a függöny között, mivel mindkét rész ugyanazon a GPU-on jelenik meg. Másfelől a függöny és a fal közötti szegély néhány aliast mutat be, mivel ez a két rész különálló GPU-ból áll.

Ennek a módnak a legnagyobb korlátozása, hogy a geometriai részek nem állíthatók be dinamikusan transzparens anyagokra, és a [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md)nem is működik a **megtekintési** mód. Más állapot-felülbírálási funkciók (Tagolás, színárnyalatok,...) működnek, bár. Az átváltáskor transzparensként megjelölt anyagok is megfelelően működnek ebben a módban.

### <a name="performance"></a>Teljesítmény

A mindkét üzemmód teljesítménybeli jellemzői a használati esettől függően változnak, és nehezen indokolható, vagy általános javaslatok is megadhatók. Ha nem korlátozza a fent említett korlátozásokat (memória vagy átlátszóság/megtekintés), javasoljuk, hogy mindkét módot kipróbálhatja, és figyelje a teljesítményt a különböző kamera-pozíciók használatával.

## <a name="setting-the-render-mode"></a>A renderelési mód beállítása

A távoli renderelési kiszolgálón használt renderelési mód a következőn keresztül van megadva: `RenderingSession.ConnectAsync` `RendererInitOptions` .

```cs
async void ExampleConnect(RenderingSession session)
{
    RendererInitOptions parameters = new RendererInitOptions();

    // Connect with one rendering mode
    parameters.RenderMode = ServiceRenderMode.TileBasedComposition;
    await session.ConnectAsync(parameters);

    session.Disconnect();

    // Wait until session.IsConnected == false

    // Reconnect with a different rendering mode
    parameters.RenderMode = ServiceRenderMode.DepthBasedComposition;
    await session.ConnectAsync(parameters);
}
```

## <a name="api-documentation"></a>API-dokumentáció

* [C# RenderingSession. ConnectAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingsession.connectasync)
* [C# RendererInitOptions struct](/dotnet/api/microsoft.azure.remoterendering.rendererinitoptions)
* [C++ RenderingSession:: ConnectToConnectAsyncRuntime ()](/cpp/api/remote-rendering/renderingsession#connectasync)
* [C++ RendererInitOptions struct](/cpp/api/remote-rendering/rendererinitoptions)

## <a name="next-steps"></a>Következő lépések

* [Munkamenetek](../concepts/sessions.md)
* [Hierarchikus állapot felülbírálása összetevő](../overview/features/override-hierarchical-state.md)
---
title: Vázlat renderelése
description: A kiválasztási vázlat megjelenítésének elvégzése
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: c04f2312926d3b6d668dff712eedb57d816c8bf3
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592007"
---
# <a name="outline-rendering"></a>Vázlat renderelése

A kijelölt objektumok vizuális megjelenítéséhez a [hierarchikus állapot felülbírálása összetevővel](../../overview/features/override-hierarchical-state.md)lehet kiemelni a tagolást. Ebből a fejezetből megtudhatja, hogyan módosulnak a globális paraméterek a vázlatos megjelenítéshez az ügyfél API-n keresztül.

A körvonal tulajdonságai globális beállítások. A tagolást használó összes objektum ugyanazt a beállítást fogja használni – az objektumon belüli körvonal színét nem lehet használni.

## <a name="parameters-for-outlinesettings"></a>Paraméterek a következőhöz: `OutlineSettings`

`OutlineSettings`Az osztály a globális vázlat tulajdonságaival kapcsolatos beállításokat tartalmazza. A következő tagokat teszi elérhetővé:

| Paraméter      | Típus    | Description                                             |
|----------------|---------|---------------------------------------------------------|
| `Color`          | Color4Ub | A körvonal rajzolásához használt szín Az alfa-rész figyelmen kívül lesz hagyva.         |
| `PulseRateHz`    | float   | A szerkezeti rezgések másodpercenkénti száma|
| `PulseIntensity` | float   | A tagolási impulzus hatásának intenzitása A teljes lüktetés esetén 0,0 és 1,0 közötti értéknek kell lennie. Az intenzitás implicit módon beállítja a vázlat minimális opacitását `MinOpacity = 1.0 - PulseIntensity` . |

![A különböző tagolási paraméterekkel háromszor megjelenített objektum ](./media/outlines.png) a (z `color` ) sárga (balra) és a magenta (Közép) és a `pulseIntensity` 0 – 0,8 (jobbra) közötti érték módosításának hatását eredményezi.

## <a name="example"></a>Példa

Az alábbi kód egy példát mutat be a szerkezeti paraméterek beállítására az API-n keresztül:

```cs
void SetOutlineParameters(RenderingSession session)
{
    OutlineSettings outlineSettings = session.Connection.OutlineSettings;
    outlineSettings.Color = new Color4Ub(255, 255, 0, 255);
    outlineSettings.PulseRateHz = 2.0f;
    outlineSettings.PulseIntensity = 0.5f;
}
```

```cpp
void SetOutlineParameters(ApiHandle<RenderingSession> session)
{
    ApiHandle<OutlineSettings> outlineSettings = session->Connection()->GetOutlineSettings();
    Color4Ub outlineColor;
    outlineColor.channels = { 255, 255, 0, 255 };
    outlineSettings->SetColor(outlineColor);
    outlineSettings->SetPulseRateHz(2.0f);
    outlineSettings->SetPulseIntensity(0.5f);
}
```

## <a name="performance"></a>Teljesítmény

A vázlatos renderelés jelentős hatással lehet a renderelési teljesítményre. Ez a hatás az adott keret kiválasztott és nem kijelölt objektumai közötti képernyő-térbeli kapcsolattól függ.

## <a name="api-documentation"></a>API-dokumentáció

* [C# RenderingConnection. OutlineSettings tulajdonság](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.outlinesettings)
* [C++ RenderingConnection:: OutlineSettings ()](/cpp/api/remote-rendering/renderingconnection#outlinesettings)

## <a name="next-steps"></a>Következő lépések

* [Hierarchikus állapot felülbírálása összetevő](../../overview/features/override-hierarchical-state.md)
---
title: Egyoldalas renderelés
description: Az egyoldalas megjelenítési beállítások és a használati esetek ismertetése
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: fea9deae3948b36732b5ea5203fceea6bec07fb9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594078"
---
# <a name="no-loc-textsingle-sided-rendering"></a>:::no-loc text="Single-sided"::: renderelési

A legtöbb megjelenítő [háttérbeli selejtezést](https://en.wikipedia.org/wiki/Back-face_culling) használ a teljesítmény növelése érdekében. Ha azonban a rácsvonalak kivágása a [kivágási síkokkal](cut-planes.md)történik, a felhasználók gyakran a háromszögek hátoldalán jelennek meg. Ha ezek a háromszögek el vannak selejtezett, az eredmény nem tűnik meggyőzőnek.

A probléma megbízható megelőzése a háromszögek *kétoldalas megjelenítésének módja.* Mivel a háttérbeli selejtezés nem használ teljesítménybeli következményeket, alapértelmezés szerint az Azure távoli renderelési funkció csak a kivágási síkon feldarabolt rácsvonalak kétoldalas megjelenítésére vált.

A *:::no-loc text="single-sided"::: renderelési* beállítás segítségével testre szabhatja ezt a viselkedést.

> [!CAUTION]
> A :::no-loc text="single-sided"::: renderelési beállítás egy kísérleti funkció. Előfordulhat, hogy a későbbiekben újra el lesz távolítva. Ne módosítsa az alapértelmezett beállítást, kivéve, ha az alkalmazásban kritikus problémát észlel.

## <a name="prerequisites"></a>Előfeltételek

A :::no-loc text="single-sided"::: renderelési beállítás csak olyan rácsvonalak esetében érvényes, amelyek a következő beállítással lettek [konvertálva](../../how-tos/conversion/configure-model-conversion.md) : `opaqueMaterialDefaultSidedness` `SingleSided` . Alapértelmezés szerint ez a beállítás a következőre van beállítva: `DoubleSided` .

## <a name="no-loc-textsingle-sided-rendering-setting"></a>:::no-loc text="Single-sided"::: renderelési beállítás

Három különböző mód van:

**Normál:** Ebben a módban a rácsvonalak mindig a konvertáláskor lesznek megjelenítve. Ez azt jelenti, hogy a készlettel konvertált rácsvonalak `opaqueMaterialDefaultSidedness` `SingleSided` mindig engedélyezve lesznek a háttérbeli selejtezéssel, még akkor is, ha egy kivágási síkon metszik őket.

**DynamicDoubleSiding:** Ebben a módban, amikor egy kivágási sík metszi a hálót, automatikusan a kétoldalas megjelenítésre vált. Ez az alapértelmezett mód.

**AlwaysDoubleSided:** Minden egyoldalas geometriát egyszerre kétoldalas megjelenítésre kényszerít. Ez a mód többnyire fel van téve, így könnyen összehasonlítható a teljesítményre gyakorolt hatás :::no-loc text="single-sided"::: és a :::no-loc text="double-sided"::: renderelés.

A :::no-loc text="single-sided"::: renderelési beállítások módosítása a következőképpen végezhető el:

```cs
void ChangeSingleSidedRendering(RenderingSession session)
{
    SingleSidedSettings settings = session.Connection.SingleSidedSettings;

    // Single-sided geometry is rendered as is
    settings.Mode = SingleSidedMode.Normal;

    // Single-sided geometry is always rendered double-sided
    settings.Mode = SingleSidedMode.AlwaysDoubleSided;
}
```

```cpp
void ChangeSingleSidedRendering(ApiHandle<RenderingSession> session)
{
    ApiHandle<SingleSidedSettings> settings = session->Connection()->GetSingleSidedSettings();

    // Single-sided geometry is rendered as is
    settings->SetMode(SingleSidedMode::Normal);

    // Single-sided geometry is always rendered double-sided
    settings->SetMode(SingleSidedMode::AlwaysDoubleSided);
}
```

## <a name="api-documentation"></a>API-dokumentáció

* [C# RenderingConnection. SingleSidedSettings tulajdonság](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.singlesidedsettings)
* [C++ RenderingConnection:: SingleSidedSettings ()](/cpp/api/remote-rendering/renderingconnection#singlesidedsettings)

## <a name="next-steps"></a>Következő lépések

* [Síkok kivágása](cut-planes.md)
* [A modell átalakításának konfigurálása](../../how-tos/conversion/configure-model-conversion.md)
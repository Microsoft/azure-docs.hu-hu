---
title: Shell-renderelés
description: A rendszerhéj megjelenítési effektusának használatának ismertetése
author: jumeder
ms.author: jumeder
ms.date: 10/23/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 7af95cba807cea340438a7de30f096758d0369ad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594163"
---
# <a name="shell-rendering"></a>Shell-renderelés

A [hierarchikus állapot felülbírálása összetevő](../../overview/features/override-hierarchical-state.md) rendszerhéj-állapota egy áttetszőségi hatás. A [megjelenített](../../overview/features/override-hierarchical-state.md) megjelenítéssel szemben csak az objektumok elülső rétege látható, amely az átlátszatlan megjelenítéshez hasonlít. Emellett az objektumok normál megjelenését is megváltoztathatja, ha rendszerhéjként jeleníti meg őket. A hatás olyan használati esetekhez használható, ahol a felhasználónak a nem fontos részektől kell megjelennie, miközben továbbra is a térbeli ismereteket tartja a teljes színtér számára.

A rendszerhéj által megjelenített objektumok megjelenését a globális állapot használatával konfigurálhatja `ShellRenderingSettings` . A rendszerhéj-renderelést használó összes objektum ugyanazt a beállítást fogja használni. Nincsenek objektum-paraméterek.

## <a name="shellrenderingsettings-parameters"></a>ShellRenderingSettings paraméterek

`ShellRenderingSettings`Az osztály a globális rendszerhéj-renderelési tulajdonságokkal kapcsolatos beállításokat tartalmazza:

| Paraméter      | Típus    | Description                                             |
|----------------|---------|---------------------------------------------------------|
| `Desaturation` | float   | Az általános végső objektum színére alkalmazandó detelítettségi mennyiség, a 0. tartomány (nincs kibontás) és 1 (teljes telítettség) között |
| `Opacity`      | float   | A rendszerhéj által megjelenített objektumok opacitása 0 (láthatatlan) és 1 (teljesen átlátszatlan) közötti tartományban |

Tekintse meg az alábbi táblázatot is, amely példákat mutat be a paraméterek hatására, ha a teljes színtérre alkalmaz:

|                | 0 | 0,25 | 0,5 | 0,75 | 1.0 | 
|----------------|:-:|:----:|:---:|:----:|:---:|
| **Desaturation** | ![Telítettség – 0,0](./media/shell-desaturation-00.png) | ![Telítettség – 0,25](./media/shell-desaturation-025.png) | ![Telítettség – 0,5](./media/shell-desaturation-05.png) | ![Telítettség – 0,75](./media/shell-desaturation-075.png) | ![Telítettség – 1,0](./media/shell-desaturation-10.png) |
| **Homály**      | ![Opacitás – 0,0](./media/shell-opacity-00.png) | ![Opacitás – 0,25](./media/shell-opacity-025.png) | ![Opacitás – 0,5](./media/shell-opacity-05.png) | ![Opacitás – 0,75](./media/shell-opacity-075.png) | ![Opacitás – 1,0](./media/shell-opacity-10.png) |

A rendszerhéj hatása a végső átlátszatlan színre lesz alkalmazva, amelyet a jelenet másképpen fog megjeleníteni. Ez magában foglalja az [árnyalat hierarchikus állapotának felülbírálását](../../overview/features/override-hierarchical-state.md).

## <a name="example"></a>Példa

Az alábbi kód az állapot példáját mutatja be `ShellRenderingSettings` az API használatával:

```cs
void SetShellSettings(RenderingSession session)
{
    ShellRenderingSettings shellRenderingSettings = session.Connection.ShellRenderingSettings;
    shellRenderingSettings.Desaturation = 0.5f;
    shellRenderingSettings.Opacity = 0.1f;
}
```

```cpp
void SetShellSettings(ApiHandle<RenderingSession> session)
{
    ApiHandle<ShellRenderingSettings> shellRenderingSettings = session->Connection()->GetShellRenderingSettings();
    shellRenderingSettings->SetDesaturation(0.5f);
    shellRenderingSettings->SetOpacity(0.1f);
}
```

## <a name="performance"></a>Teljesítmény

A rendszerhéj-renderelési funkció kis állandó terhelést eredményez a standard átlátszatlan rendereléshez képest. Sokkal gyorsabb, mint az objektumokon vagy a [megjelenítésen áttekinthető](../../overview/features/override-hierarchical-state.md) anyagok használata. A teljesítmény csökkenhet, ha a jelenet egyetlen része a rendszerhéj-renderelésre van állítva. Ez a romlás akkor fordulhat elő, ha a renderelést igénylő objektumokat felderítette. Ebben a tekintetben a teljesítmény ugyanúgy viselkedik, mint a [kivágott síkok](../../overview/features/cut-planes.md) funkció.

## <a name="next-steps"></a>Következő lépések

* [Hierarchikus állapot felülbírálása összetevő](../../overview/features/override-hierarchical-state.md)
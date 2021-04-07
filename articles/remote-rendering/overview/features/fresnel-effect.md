---
title: Fresnel-effektus
description: A Fresnel anyag hatásának funkció magyarázat lapja
author: jumeder
ms.author: jumeder
ms.date: 11/09/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: f63cd3b50642c3cf531387b4446992b6f15116f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594112"
---
# <a name="fresnel-effect"></a>Fresnel-effektus

A Fresnel hatás anyaga funkció nem fizikailag helyes, ad-hoc hatás. A szolgáltatás az objektumok fizikai megfigyelésén alapul, és ezek a szögek egyre jobban tükröznek. A Fresnel-reflexió önmagában már fizikailag is be van építve az Azure távoli renderelésben használt [pbr-anyagok modelljébe](../../overview/features/pbr-materials.md) . Ezzel szemben a Fresnel Effect anyag funkció csak az adalékanyag színeffektusa, amely nem függ a [fényektől](../../overview/features/lights.md) és a [Sky-környezettől](../../overview/features/sky.md).

A Fresnel hatás az érintett objektumokat színesen ragyogja az élek szélei körül. A következő részekben talál információt a hatás testreszabásáról és a renderelési eredményekről.

## <a name="enabling-the-fresnel-effect"></a>A Fresnel hatás engedélyezése

A Fresnel Effect funkció használatához engedélyezni kell a szóban forgó anyagokon. A [PbrMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering.pbrmaterialfeatures) FresnelEffect-bit beállításával engedélyezheti a [pbr-anyagokon](../../overview/features/pbr-materials.md). Ugyanez a minta a [ColorMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering) és a [színanyagra](../../overview/features/color-materials.md)is vonatkozik. A használati bemutatóhoz lásd a Code Samples szakaszt.

Az engedélyezés után a Fresnel effektus azonnal láthatóvá válik. Alapértelmezés szerint a ragyogás fehér lesz (1, 1, 1, 1), és 1. kitevővel rendelkezik. Ezeket a beállításokat az alábbi paraméterekkel lehet testreszabni.

## <a name="customizing-the-effect-appearance"></a>A hatás megjelenésének testreszabása

Jelenleg a Fresnel effektus testreszabható a következő tulajdonságok alapján:

| Anyagi tulajdonság | Típus | Magyarázat |
|-------------------|------|-------------|
| FresnelEffectColor | Color4 | Az a szín, amely a Fresnel ragyog. Az alfa-csatornát jelenleg figyelmen kívül hagyja a rendszer. |
| FresnelEffectExponent | float | A Fresnel fényének eloszlása. Az 0,01-tól (az összes objektumon elosztva) és 10-ig (csak a legnagyobb Gracie-beli szögek) terjedhet. |

A gyakorlatban a szín és a kitevő különböző beállításai a következőképpen fognak kinézni:

![Fresnel-effektusi példák](./media/fresnel-effect-examples.png)

Az Fresnel effektus kitevője fokozatosan növekszik 1 és 10 közötti értékkel minden egyes színsorhoz. Ezzel fokozatosan lekéri a Fresnel ragyog a megtekintett objektumok széleire. Az átláthatóság nem befolyásolja az Fresnel hatását, ahogy az alábbi példában is látható:

![Fresnel effektus-áttetszőségi példák](./media/fresnel-effect-transparent-examples.png)

Ahogy az látható, az átlón lévő objektumok teljes mértékben transzparensek, de a Fresnel Shine marad. A hatás a fizikai alapú Fresnel is utánozza, ami szintén megtalálható ezekben a képernyőképekben.

## <a name="code-samples"></a>Kódminták

A következő mintakód a [pbr-anyagok](../../overview/features/pbr-materials.md) és a [színes anyagok](../../overview/features/color-materials.md)Fresnel-effektusának engedélyezését és testreszabását mutatja be:

```cs
    void SetFresnelEffect(RenderingSession session, Material material)
    {
        if (material.MaterialSubType == MaterialType.Pbr)
        {
            var pbrMaterial = material as PbrMaterial;
            pbrMaterial.PbrFlags |= PbrMaterialFeatures.FresnelEffect;
            pbrMaterial.FresnelEffectColor = new Color4(1.0f, 0.5f, 0.1f, 1.0f);
            pbrMaterial.FresnelEffectExponent = 3.141592f;
        }
        else if (material.MaterialSubType == MaterialType.Color)
        {
            var colorMaterial = material as ColorMaterial;
            colorMaterial.ColorFlags |= ColorMaterialFeatures.FresnelEffect;
            colorMaterial.FresnelEffectColor = new Color4(0.25f, 1.0f, 0.25f, 1.0f);
            colorMaterial.FresnelEffectExponent = 7.654321f;
        }
    }
```

```cpp
void SetFresnelEffect(ApiHandle<RenderingSession> session, ApiHandle<Material> material)
{
    if (material->GetMaterialSubType() == MaterialType::Pbr)
    {
        auto pbrMaterial = material.as<PbrMaterial>();
        auto featureFlags = PbrMaterialFeatures((int32_t)pbrMaterial->GetPbrFlags() | (int32_t)PbrMaterialFeatures::FresnelEffect);
        pbrMaterial->SetPbrFlags(featureFlags);
        pbrMaterial->SetFresnelEffectColor(Color4{ 1.f, 0.5f, 0.1f, 1.f });
        pbrMaterial->SetFresnelEffectExponent(3.141592f);

    }
    else if (material->GetMaterialSubType() == MaterialType::Color)
    {
        auto colorMaterial = material.as<ColorMaterial>();
        auto featureFlags = ColorMaterialFeatures((int32_t)colorMaterial->GetColorFlags() | (int32_t)ColorMaterialFeatures::FresnelEffect);
        colorMaterial->SetColorFlags(featureFlags);
        colorMaterial->SetFresnelEffectColor(Color4{ 0.25f, 1.f, 0.25f, 1.f });
        colorMaterial->SetFresnelEffectExponent(7.654321f);
    }
}
```

## <a name="api-documentation"></a>API-dokumentáció

* [C# PbrMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering.pbrmaterialfeatures)
* [C++ PbrMaterialFeatures](/cpp/api/remote-rendering/pbrmaterialfeatures)
* [C# ColorMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering.colormaterialfeatures)
* [C++ ColorMaterialFeatures](/cpp/api/remote-rendering/colormaterialfeatures)

## <a name="next-steps"></a>Következő lépések

* [Anyagok](../../concepts/materials.md)
* [PBR-anyagok](../../overview/features/pbr-materials.md)
* [Színanyagok](../../overview/features/color-materials.md)
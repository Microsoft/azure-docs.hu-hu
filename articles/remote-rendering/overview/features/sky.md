---
title: Égbolt tükröződése
description: Ismerteti, hogyan lehet környezeti térképeket beállítani a Sky-tükrözésekhez
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 39e3b41d49ad06e5dbe5164809a6743da8dedae5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89613757"
---
# <a name="sky-reflections"></a>Égbolt tükröződése

Az Azure Remote rendering szolgáltatásban egy Sky-textúrát használunk az objektumok reális megvilágítására. A kibővített valóságot használó alkalmazások esetében ennek a textúrának hasonlónak kell lennie a valós környezetéhez, hogy az objektumok meggyőzőek legyenek. Ez a cikk az égbolt textúrájának módosítását ismerteti.

> [!NOTE]
> Az égbolt textúráját *környezeti térképnek*is nevezzük. Ezek a kifejezések szinonimaként használatosak.

## <a name="object-lighting"></a>Objektum megvilágítása

Az Azure távoli renderelése *fizikailag alapú renderelést* (pbr) alkalmaz a reális megvilágítás-számításokhoz. Bár a megfelelő Felhőbeli textúra használatával hozzáadhat [fényforrásokat](lights.md) a jelenethez, a legnagyobb hatással van.

Az alábbi képek a különböző felületek világításának eredményeit jelenítik meg, csak a Sky textúrával:

| Érdesség  | 0                                        | 0,25                                          | 0,5                                          | 0,75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| Nem fém  | ![Dielektromos, érdesség = 0](media/dielectric-0.png)   | ![Dielektromos, érdesség = 0,25](media/dielectric-0.25.png)  | ![Dielektromos, érdesség = 0,5](media/dielectric-0.5.png)  | ![Dielektromos, érdesség = 0,75](media/dielectric-0.75.png)  | ![Dielektromos, érdesség = 1](media/dielectric-1.png)  |
| Fém      | ![Fém, érdesség = 0](media/metallic-0.png)  | ![Fém, érdesség = 0,25](media/metallic-0.25.png)    | ![Fém, érdesség = 0,5](media/metallic-0.5.png)    | ![Fém, érdesség = 0,75](media/metallic-0.75.png)    | ![Fém, érdesség = 1](media/metallic-1.png)    |

A világítástechnikai modellel kapcsolatos további információkért tekintse meg az [anyagok](../../concepts/materials.md) című fejezetet.

> [!IMPORTANT]
> Az Azure Remote rendering csak a világítási modellekhez használja az égbolt textúráját. Nem háttérként jeleníti meg az eget, mivel a kibővített valóságot használó alkalmazások már rendelkeznek megfelelő háttérrel – a valós világban.

## <a name="changing-the-sky-texture"></a>Az égbolt textúrájának módosítása

A környezeti Térkép módosításához mindössze annyit kell tennie, hogy [betölti a textúrát](../../concepts/textures.md) , és megváltoztatja a munkamenetet `SkyReflectionSettings` :

```cs
LoadTextureAsync _skyTextureLoad = null;
void ChangeEnvironmentMap(AzureSession session)
{
    _skyTextureLoad = session.Actions.LoadTextureFromSASAsync(new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap));

    _skyTextureLoad.Completed += (LoadTextureAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                try
                {
                    session.Actions.SkyReflectionSettings.SkyReflectionTexture = res.Result;
                }
                catch (RRException exception)
                {
                    System.Console.WriteLine($"Setting sky reflection failed: {exception.Message}");
                }
            }
            else
            {
                System.Console.WriteLine("Texture loading failed!");
            }
        };
}
```

```cpp
void ChangeEnvironmentMap(ApiHandle<AzureSession> session)
{
    LoadTextureFromSASParams params;
    params.TextureType = TextureType::CubeMap;
    params.TextureUrl = "builtin://VeniceSunset";
    ApiHandle<LoadTextureAsync> skyTextureLoad = *session->Actions()->LoadTextureFromSASAsync(params);

    skyTextureLoad->Completed([&](ApiHandle<LoadTextureAsync> res)
        {
            if (res->GetIsRanToCompletion())
            {
                ApiHandle<SkyReflectionSettings> settings = session->Actions()->GetSkyReflectionSettings();
                settings->SetSkyReflectionTexture(res->GetResult());
            }
            else
            {
                printf("Texture loading failed!\n");
            }
        });
}

```

Vegye figyelembe, hogy a `LoadTextureFromSASAsync` rendszer a fenti változatot használja, mert a beépített textúra be van töltve. Ha [csatolt blob-tárolóból](../../how-tos/create-an-account.md#link-storage-accounts)tölt be betöltést, használja a `LoadTextureAsync` Variant elemet.

## <a name="sky-texture-types"></a>Égbolt típusú textúrák

A *[cubemaps](https://en.wikipedia.org/wiki/Cube_mapping)* és a *2D textúrákat* is használhatja környezeti térképként.

Az összes textúrának [támogatott textúra formátumúnak](../../concepts/textures.md#supported-texture-formats)kell lennie. Nem kell megadnia a Mipmap a Sky-textúrákhoz.

### <a name="cube-environment-maps"></a>Adatkocka-környezet térképe

Hivatkozásként itt látható egy csomagolatlan cubemap:

![Csomagolatlan cubemap](media/Cubemap-example.png)

`AzureSession.Actions.LoadTextureAsync` /  `LoadTextureFromSASAsync` A with paranccsal `TextureType.CubeMap` betöltheti a cubemap-textúrákat.

### <a name="sphere-environment-maps"></a>Gömb-környezeti térképek

A 2D-mintázatok környezeti térképként való használatakor a képnek [gömb-koordináta-térben](https://en.wikipedia.org/wiki/Spherical_coordinate_system)kell lennie.

![Egy Sky-rendszerkép a gömb koordinátáiban](media/spheremap-example.png)

`AzureSession.Actions.LoadTextureAsync`A with paranccsal `TextureType.Texture2D` betöltheti a gömb-környezeti térképeket.

## <a name="built-in-environment-maps"></a>Beépített környezeti térképek

Az Azure Remote rendering számos beépített környezeti leképezést biztosít, amelyek mindig elérhetők. Az összes beépített környezeti Térkép a cubemaps.

|Azonosító                         | Leírás                                              | Ábrán                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | Különféle szalagos fények, világos beltéri alapszintű megvilágítás    | ![Egy objektum megvilágításához használt SkyBox](media/autoshop.png)
|builtin://BoilerRoom               | Világos beltéri fény beállítása, több ablakos fények      | ![Objektum BoilerRoom SkyBox](media/boiler-room.png)
|builtin://ColorfulStudio           | Változóan színes fények Közepes fényű beltéri környezetben  | ![Objektum ColorfulStudio SkyBox](media/colorful-studio.png)
|builtin://Hangar                   | Közepesen fényes környezeti csarnok                     | ![Objektum SmallHangar SkyBox](media/hangar.png)
|builtin://IndustrialPipeAndValve   | Halvány beltéri beállítás világos sötét kontraszttal              | ![Objektum IndustrialPipeAndValve SkyBox](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | Nappali környezeti helyiség világos, világos ablakos fény     | ![Objektum Lebombo SkyBox](media/lebombo.png)
|builtin://SataraNight              | Sötét éjszakai égbolt és számos környező fénnyel   | ![Objektum SataraNight SkyBox](media/satara-night.png)
|builtin://SunnyVondelpark          | Napfény és árnyék kontrasztja                      | ![Objektum SunnyVondelpark SkyBox](media/sunny-vondelpark.png)
|builtin://Syferfontein             | Tiszta égbolt            | ![Objektum Syferfontein SkyBox](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | Mérsékelten változó nap és árnyalat                         | ![Objektum TearsOfSteelBridge SkyBox](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | Esti naplemente a félhomályban                    | ![Objektum VeniceSunset SkyBox](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | Világos, buja-zöld és fehér fény tónusok, halvány terep | ![Objektum WhippleCreekRegionalPark SkyBox](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | Nappali és világos környezeti alapfény                 | ![Objektum WinterRiver SkyBox](media/winter-river.png)
|builtin://DefaultSky               | Ugyanaz, mint a TearsOfSteelBridge                               | ![Objektum DefaultSky SkyBox](media/tears-of-steel-bridge.png)

## <a name="api-documentation"></a>API-dokumentáció

* [C# RemoteManager. SkyReflectionSettings tulajdonság](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.skyreflectionsettings)
* [C++ RemoteManager:: SkyReflectionSettings ()](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#skyreflectionsettings)

## <a name="next-steps"></a>Következő lépések

* [Fények](../../overview/features/lights.md)
* [Anyagok](../../concepts/materials.md)
* [Textúrák](../../concepts/textures.md)
* [A TexConv parancssori eszköz](../../resources/tools/tex-conv.md)

---
title: Kamera
description: A kamera beállításait és használati eseteit ismerteti
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.openlocfilehash: dbe86313054706af974ccb324a39e942e9b5ca44
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594129"
---
# <a name="camera"></a>Kamera

Annak érdekében, hogy a helyileg és távolról megjelenített tartalmak zökkenőmentesen összeállíthatók legyenek, a különböző kamera-tulajdonságokat szinkronban kell tartani a kiszolgáló és az ügyfél között. A kamera átalakítása és kivetítése. A helyileg megjelenített tartalomnak például ugyanazt a kamera-átalakítást és-vetítést kell használnia, mint a legújabb távoli keret.

![Helyi és távoli kamera](./media/camera.png)

A fenti képen a helyi kamera a kiszolgáló által eljuttatott távoli kerethez képest átkerült. Ennek eredményeképpen a helyi tartalmat a távoli kerettől megegyező perspektívában kell megjeleníteni, és végül az eredményül kapott képet a helyi kamera területére kell felvenni, amely részletesen a [késői fázisok újravetítésének](late-stage-reprojection.md)részleteit ismerteti.

A távoli és a helyi megjelenítés közötti késleltetés azt jelenti, hogy a beállítások bármilyen módosítása késéssel történik. Ezért az új értékek nem használhatók azonnal ugyanazt a keretet.

A közeli és a távoli sík távolságok a kamera beállításainál adhatók meg. A 2. HoloLens az átalakítási és a leképezési adathalmazt a hardver határozza meg. Az [asztali szimuláció](../../concepts/graphics-bindings.md)használatakor a rendszer a függvény bemenetén keresztül állítja be őket `Update` .

A módosított értékek helyileg is használhatók, amint az első távoli keret megérkezik a velük. A 2. HoloLens a rendereléshez használandó adatkészleteket a *HolographicFrame* a többi holografikus alkalmazáshoz hasonlóan nyújtja. Az [asztali szimuláció](../../concepts/graphics-bindings.md)a függvény kimenetén keresztül kéri le őket `Update` .

## <a name="camera-settings"></a>Kamera beállításai

A kamera beállításai a következő tulajdonságokkal módosíthatók:

**Közel és messzi síkja:**

Győződjön meg arról, hogy nem állíthatók be érvénytelen tartományok, a **NearPlane** és a **FarPlane** tulajdonság csak olvasható, és egy különálló függvény **SetNearAndFarPlane** létezik a tartomány módosításához. Ezeket az adatfájlokat a rendszer a keret végén küldi el a kiszolgálónak. Ezeknek az értékeknek a beállításakor a **NearPlane** kisebbnek kell lennie, mint a **FarPlane**. Ellenkező esetben hiba történik.

> [!IMPORTANT]
> Az egységben ez automatikusan, a fő kamera közelében és a távoli síkok módosításakor lesz kezelve.

**EnableDepth**:

Néha hasznos lehet letiltani a távoli rendszerkép pufferének a hibakeresési célokra való írását. A mélység letiltásával leállíthatja a kiszolgálót a mélységi adatok elküldéséről is, így csökkentve a sávszélességet.

> [!TIP]
> Az egységben egy **EnableDepthComponent** nevű hibakeresési összetevő van megadva, amely a funkció a szerkesztő felhasználói felületen való váltására használható.

**InverseDepth**:

> [!NOTE]
> Ez a beállítás csak akkor fontos, ha a értéke `EnableDepth` `true` . Ellenkező esetben ez a beállítás nincs hatással.

A mélységi pufferek általában a (z) [0; 1] lebegőpontos tartományba rögzítik az értékeket, a 0 értékkel pedig a sík mélységét, a távolságot pedig 1. Ezen tartomány megfordítása és az [1; 0] tartományba tartozó mélységi értékek is megadhatók, azaz a közel sík mélysége 1 lesz, a távolabbi sík mélysége pedig 0 lesz. Az utóbbi általában növeli a lebegőpontos pontosság eloszlását a nem lineáris z-tartományon belül.

> [!WARNING]
> Egy közös megközelítéssel megtalálhatók a közeli sík és a távoli sík értékei a kamera objektumain. Ez sikertelen lesz az Azure távoli renderelés hibája esetén, amikor ezt a-t próbálja meg `CameraSettings` .

Az Azure Remote rendering API-nak tudnia kell a helyi megjelenítő mélységi puffer-konvencióját, hogy helyesen lehessen a helyi mélységi pufferbe állítani a távoli mélységet. Ha a mélységi puffer tartománya [0; 1], akkor hagyja ezt a jelzőt `false` . Ha fordított mélységi puffert használ [1; 0] tartománnyal, állítsa a jelölőt a következőre: `InverseDepth` `true` .

> [!NOTE]
> Az Unity esetében a megfelelő beállítást már alkalmazza a rendszer, `RenderingConnection` így nincs szükség manuális beavatkozásra.

A kamera beállításainak módosítása a következőképpen végezhető el:

```cs
void ChangeCameraSetting(RenderingSession session)
{
    CameraSettings settings = session.Connection.CameraSettings;

    settings.SetNearAndFarPlane(0.1f, 20.0f);
    settings.EnableDepth = false;
    settings.InverseDepth = false;
}
```

```cpp
void ChangeCameraSetting(ApiHandle<RenderingSession> session)
{
    ApiHandle<CameraSettings> settings = session->Connection()->GetCameraSettings();

    settings->SetNearAndFarPlane(0.1f, 20.0f);
    settings->SetEnableDepth(false);
    settings->SetInverseDepth(false);
}
```

## <a name="api-documentation"></a>API-dokumentáció

* [C# CameraSettings](/dotnet/api/microsoft.azure.remoterendering.camerasettings)
* [C++ CameraSettings](/cpp/api/remote-rendering/camerasettings)
* [C# GraphicsBindingSimD3d11. Update függvény](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11.update)
* [C++ GraphicsBindingSimD3d11:: Update függvény](/cpp/api/remote-rendering/graphicsbindingsimd3d11#update)

## <a name="next-steps"></a>Következő lépések

* [Grafikus kötés](../../concepts/graphics-bindings.md)
* [Újravetítés késői fázisban](late-stage-reprojection.md)
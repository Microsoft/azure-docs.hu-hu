---
title: Előkészítési terület
description: Ismerteti a szakaszokra vonatkozó beállításokat és a használati eseteket
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 4a739907ce0a3b0b6dfcb8791b51d0ea5e7e76e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594010"
---
# <a name="stage-space"></a>Előkészítési terület

Ha az ARR-t olyan eszközön futtatja, amely Head-Tracking típusú, például a 2. HoloLens, a rendszer a fő problémát a felhasználói alkalmazásnak és a kiszolgálónak küldi el. Az a terület, amelyben a fő átalakító definiálva van, a *szakasz a fázis területének* nevezzük.

A helyi és a távoli tartalom igazítása azt feltételezi, hogy a szakasz és a globális terület azonos az ügyfél és a kiszolgáló között. Ha a felhasználó úgy dönt, hogy hozzáad egy további átalakítót a kamerához, azt is el kell juttatnia a kiszolgálónak, hogy megfelelően illeszkedjen a helyi és a távoli tartalomhoz.

A szakaszos terület áthelyezésének tipikus okai a [globális zárolási eszközök](https://microsoft.github.io/MixedReality-WorldLockingTools-Unity/README.html) vagy más valós szintű rögzítési technikák, valamint a virtuális karakterek a VR-ben való áthelyezése.

## <a name="stage-space-settings"></a>Szakasz beállításai

> [!CAUTION]
> KÍSÉRLETI: Ez a funkció kísérleti jellegű, és idővel változhat. Így az újabb ügyféloldali SDK-verziók frissítése további munkát igényelhet a kód frissítéséhez. A jelenlegi implementáció egy rövid pillanatra megszakítja a helyi és a távoli tartalom igazítását, amikor megváltoztatja a szakasz helyét.
Ennek megfelelően jelenleg kizárólag olyan globális zárolási célokra kell használni, mint az olyan horgonyok, amelyek az idő múlásával csak nagyon kis változásokat mutatnak.

Ahhoz, hogy tájékoztassa a kiszolgálót arról, hogy egy további átalakítót alkalmaznak a szakasz területére, a pozícióját a hely határozza meg, és el kell végezni a lemezterület rotációját. Ez a beállítás a *szakasz területének beállításán* keresztül érhető el.

> [!IMPORTANT]
> Az [asztali szimulációban](../../concepts/graphics-bindings.md) a felhasználói alkalmazás a kamera globális helyét is megadja. Ebben az esetben úgy kell kihagyni a szakaszhoz tartozó terület forrását, mert már meg van szorozva a kamera átalakításával.

```cs
void ChangeStageSpace(RenderingSession session)
{
    StageSpaceSettings settings = session.Connection.StageSpaceSettings;

    // Set position and rotation to the world-space transform of the stage space.
    settings.Position = new Double3(0, 0, 0);
    settings.Rotation = new Quaternion(0, 0, 0, 1);
}
```

```cpp
void ChangeStageSpace(ApiHandle<RenderingSession> session)
{
    ApiHandle<StageSpaceSettings> settings = session->Connection()->GetStageSpaceSettings();

    // Set position and rotation to the world-space transform of the stage space.
    settings->SetPosition({0, 0, 0});
    settings->SetRotation({0, 0, 0, 1});
}
```

## <a name="unity-stage-space-script"></a>Egység szakaszának parancsfájlja

Az Unity Integration egy **StageSpace** nevű szkriptet biztosít, amely a kamera szülő-GameObject is felvehető. Ez a szkript azonnal gondoskodik a szakasz helyének eredetéről.

## <a name="next-steps"></a>Következő lépések

* [Grafikus kötés](../../concepts/graphics-bindings.md)
* [Újravetítés késői fázisban](late-stage-reprojection.md)

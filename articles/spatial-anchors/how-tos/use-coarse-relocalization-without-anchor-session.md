---
title: Durva újrahonosítás használata rögzített munkamenet nélkül
description: Durva újrahonosítás használata rögzített munkamenet nélkül
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 0b3959ba3ec80f3113ea0358db550c86f1f276b1
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430309"
---
# <a name="use-coarse-relocalization-without-an-anchor-session"></a>Durva újrahonosítás használata rögzített munkamenet nélkül

A [durva újrahonosítási](../concepts/coarse-reloc.md) funkció részét képező érzékelő ujjlenyomat-szolgáltató objektum az Azure térbeli horgonyok munkameneteihez való csatolás nélkül használható.
A következő kódrészlet azt mutatja be, hogyan érhető el az érzékelő szolgáltatója, hogy megszerezze az eszköz pozíciójának becslését:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(
        x: geoPose.Longitude,
        y: geoPose.Latitude,
        radius: geoPose.HorizontalError);
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
// Game about to start, start tracking the sensors
[sensorProvider start];

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    ASAGeoLocation *geoPose = [sensorProvider getLocationEstimate];

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError);
}

// Game ended, no need to track the sensors anymore
[sensorProvider stop];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
// Game about to start, start tracking the sensors
sensorProvider?.start()

// Game loop
while m_isRunning
{
    // Get the GPS estimate
    var geoPose: ASAGeoLocation?
    geoPose = sensorProvider?.getLocationEstimate()

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError)
}

// Game ended, no need to track the sensors anymore
sensorProvider?.stop()
```

# <a name="java"></a>[Java](#tab/java)

```java
// Game about to start, start tracking the sensors
sensorProvider.start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.getLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.getLongitude(), geoPose.getLatitude(), geoPose.getHorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.stop();
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
// Game about to start, start tracking the sensors
sensorProvider->Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    std::shared_ptr<GeoLocation> geoPose = sensorProvider->GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose->Longitude(), geoPose->Latitude(), geoPose->HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider->Stop();
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.Longitude(), geoPose.Latitude(), geoPose.HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

---

### <a name="next-steps"></a>Következő lépések

Ez a cikk bemutatja, hogyan használható a durva áthonosítási API az eszköz pozíciójának becsléséhez. Ha többet szeretne megtudni a durva áthonosításról, folytassa a következő útmutatóval.

> [!div class="nextstepaction"]
> [Durva helyzet-újrameghatározás](../concepts/coarse-reloc.md)

---
title: Vezérlőelemek hozzáadása Android-térképhez | Microsoft Azure térképek
description: Nagyítás-vezérlés, szurok-vezérlőelem hozzáadása, a vezérlés elforgatása és a stílus kiválasztása a Microsoft Azure Maps Android SDK-ban.
author: rbrundritt
ms.author: richbrun
ms.date: 02/19/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 8224192ed0d13af2ff6ac60aac5aa928589ff01a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054890"
---
# <a name="add-controls-to-a-map-android-sdk"></a>Vezérlőelemek hozzáadása térképhez (Android SDK)

Ez a cikk bemutatja, hogyan adhat hozzá FELHASZNÁLÓIFELÜLET-vezérlőket a térképhez.

## <a name="add-zoom-control"></a>Nagyítási vezérlő hozzáadása

A nagyítás-vezérlés gombokkal bővítheti a térképet és a nagyítást. A következő mintakód létrehozza az osztály egy példányát `ZoomControl` , és hozzáadja azt egy térképhez.

```java
map.controls.add(new ZoomControl());
```

Az alábbi képernyőképen egy térképen betöltött nagyítási vezérlő látható.

![Leképezéshez hozzáadott nagyítási vezérlő](media/map-add-controls-android/android-zoom-control.jpg)

## <a name="add-pitch-control"></a>Pitch vezérlőelem hozzáadása

A Pitch vezérlő gombok hozzáadásával megdöntheti, hogy a szurok a horizonthoz viszonyítva legyen-e leképezve. A következő mintakód létrehozza az osztály egy példányát `PitchControl` , és hozzáadja azt egy térképhez.

```java
//Construct a pitch control and add it to the map.
map.controls.add(new PitchControl());
```

Az alábbi képernyőképen egy térképen betöltött szurok-vezérlőelem látható.

![Térképhez hozzáadott szurok-vezérlő](media/map-add-controls-android/android-pitch-control.jpg)

## <a name="add-compass-control"></a>Iránytű vezérlőelem hozzáadása

Az iránytű vezérlőelem egy gombot helyez el a Térkép elforgatásához. A következő mintakód létrehozza az osztály egy példányát `CompassControl` , és hozzáadja azt egy térképhez.

```java
//Construct a compass control and add it to the map.
map.controls.add(new CompassControl());
```

Az alábbi képernyőképen egy térképen betöltött iránytű-vezérlő látható.

![A térképhez hozzáadott iránytű vezérlő](media/map-add-controls-android/android-compass-control.jpg)

## <a name="add-traffic-control"></a>Traffic Control hozzáadása

Egy adatforgalom-vezérléssel egy gomb jelenik meg, amely a Térkép forgalmi adatok láthatóságát adja meg. A következő mintakód létrehozza az osztály egy példányát `TrafficControl` , és hozzáadja azt egy térképhez.

```java
//Construct a traffic control and add it to the map.
map.controls.add(new TrafficControl());
```

Az alábbi képernyőképen egy térképen betöltött forgalom vezérlőelem látható.

![Leképezéshez hozzáadott forgalom-vezérlés](media/map-add-controls-android/android-traffic-control.jpg)

## <a name="a-map-with-all-controls"></a>Térkép az összes vezérlővel

Több vezérlő helyezhető egy tömbbe, és a térképhez mind egyszerre, mind a Térkép ugyanazon területén helyezhető el, hogy egyszerűbbé váljon a fejlesztés. A következő lépés a szabványos navigációs vezérlőket hozzáadja a térképhez ezzel a módszerrel.

```java
map.controls.add(
    new Control[]{
        new ZoomControl(),
        new CompassControl(),
        new PitchControl(),
        new TrafficControl()
    }
);
```

Az alábbi képernyőképen a térképen betöltött összes vezérlő látható. Vegye figyelembe, hogy a térképhez hozzáadott sorrend a megjelenő sorrend.

![A leképezéshez hozzáadott összes vezérlő](media/map-add-controls-android/android-all-controls.jpg)

## <a name="next-steps"></a>Következő lépések

Az alábbi cikkekben további kódokat talál a Maps-hez való hozzáadáshoz:

> [!div class="nextstepaction"]
> [Szimbólumréteg hozzáadása](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Buborékréteg hozzáadása](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Vonalréteg hozzáadása](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Sokszögréteg hozzáadása](how-to-add-shapes-to-android-map.md)

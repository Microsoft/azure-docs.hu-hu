---
title: Azure Kinect DK koordináta-rendszerek
description: Az Azure-beli Azure-beli Kinect-érzékelőkhöz kapcsolódó Azure Kinect DK koordináta-rendszerek leírása
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, szenzor, SDK, részletes kamera, TOF, alapelvek, teljesítmény, érvénytelenítés
ms.openlocfilehash: 4bb1c3b79862b918870cff786042d9b4c66270d1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85277262"
---
# <a name="azure-kinect-dk-coordinate-systems"></a>Azure Kinect DK koordináta-rendszerek

Ebben a cikkben a 2D és 3D koordináta rendszerekhez használt konvenciókat ismertetjük.  Külön koordináta-rendszerek vannak társítva az egyes érzékelők eszközeihez, és az egymás közötti pontok átalakításához engedélyezett [kalibrációs funkciók](use-calibration-functions.md) . Az [átalakítási függvények](use-image-transformation.md) a teljes rendszerképeket átalakítja a koordináta rendszerek között.  

## <a name="2d-coordinate-systems"></a>2D koordináta-rendszerek

 A részletes és a színes fényképezőgépek is független 2D koordináta-rendszerrel vannak társítva. Az [x, y]-koordináták képpontban szerepelnek, ahol az *x* a 0 és a Width-1 közötti tartományba esik, és az *y* 0 és magasság közötti értékre van kiterjedően. A szélesség és a magasság a kiválasztott módból függ, amelyben a részletes és a színes kamerák működnek. A képpont koordináta `[0,0]` megfelel a kép bal felső képpontjának. A képpontok koordinátái lehetnek a alképpont-koordinátákat jelképező töredékek.

A 2D koordinátarendszer 0 – középre van, vagyis az alképpont koordináta a `[0.0, 0.0]` képpont középső és `[0.5,0.5]` jobb alsó sarkát jelöli, az alább látható módon.

   ![2D koordinátarendszer](./media/concepts/concepts-coordinate-systems/coordinate-systems-sdk-2d-system.png)

## <a name="3d-coordinate-systems"></a>3D koordináta-rendszerek

Minden kamera, a gyorsulásmérő és a giroszkóp egy független 3D koordináta-területhez van társítva.

A 3D koordináta-rendszerek pontjai metrikus [X, Y, Z]-koordinátákként jelennek meg.

### <a name="depth-and-color-camera"></a>Részletes és színes kamera

A forrás a `[0,0,0]` kamera fókuszpontjában található. A koordinátarendszer olyan irányú, hogy a pozitív X tengely jobb oldali, a pozitív Y tengely lefelé mutat, és a pozitív Z tengelyre mutat.

A mélységi kamera a színes kamera 6 fokos lefelé van letiltva, az alábbi ábrán látható módon. 

A mélységi kamera két megvilágítót használ. A keskeny látómező (NFOV) módban használt reflektor a részletes kamera esetéhez igazodik, így a megvilágítót nem lehet megdönteni. A széles látómező (WFOV) módokon használt reflektor a mélységi kamerához képest további 1,3 fokos szögben van.

![3D koordináta-konvenciók](./media/concepts/concepts-coordinate-systems/coordinate-systems-camera-features.png)

### <a name="gyroscope-and-accelerometer"></a>Giroszkóp és gyorsulásmérő

A giroszkóp forrása megegyezik a `[0,0,0]` mélységi kamera forrásával. A gyorsulásmérő forrása egybeesik a fizikai helyétől. A gyorsulásmérő és a giroszkóp koordináta-rendszerek is jobb oldaliak. A koordinátarendszer pozitív X tengelyes pontjai visszafelé, a pozitív Y tengely balra, a pozitív Z tengely pedig lefelé mutat, az alábbi ábrán látható módon.

![IMU koordináta-rendszere](./media/concepts/concepts-coordinate-systems/coordinate-systems-gyroscope.png)

## <a name="next-steps"></a>Következő lépések

[További tudnivalók az Azure Kinect Sensor SDK-ról](about-sensor-sdk.md)
---
title: Az Azure IoT Hub-megfelelőséggel kapcsolatos eszközök frissítésének ismertetése | Microsoft Docs
description: Ismerje meg, hogyan méri az Azure IoT Hub az eszköz frissítésének megfelelőségét.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ac6094efde8a32b1fcc04c55bbc537afeb4166f7
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663413"
---
# <a name="device-update-compliance"></a>Eszköz Update Compliance

A IoT Hub eszköz frissítésében a megfelelőségi mérték azt méri, hogy hány eszköz telepítette a legmagasabb verziószámú kompatibilis frissítést. Egy eszköz akkor megfelelő, ha telepítve van a legmagasabb rendelkezésre állású frissítés, amely kompatibilis az adott verzióval. 

Vegyük például az eszköz frissítésének egy példányát a következő frissítésekkel:

|Frissítés neve|Frissítés verziója|Kompatibilis eszköz modellje|
|-----------|--------------|-----------------------|
|1    |1.0    |Model1|
|Update2    |1.0    |Model2|
|Update3    |2.0    |Model1|

Tegyük fel, hogy a következő központi telepítések lettek létrehozva:

|Központi telepítés neve    |Frissítés neve    |Célcsoport|
|-----------|--------------|-------------------|
|Deployment1    |1    |Group1|
|Deployment2    |Update2    |Group2|
|Deployment3    |Update3    |3. csoport|

Most vegye figyelembe a következő eszközöket a csoporttagságok és a telepített verziók szerint:

|DeviceId   |Eszköz modellje   |Telepített frissítési verzió|Group |Megfelelőség|
|-----------|--------------|-----------------------|-----|---------|
|Device1    |Model1 |1.0    |Group1 |Új frissítések érhetők el</span>|
|Device2    |Model1 |2.0    |3. csoport |A legújabb frissítéskor|
|Device3    |Model2 |1.0    |Group2 |A legújabb frissítéskor|
|Device4    |Model1 |1.0    |3. csoport |Frissítés folyamatban|

A Device1 és a Device4 nem felel meg az előírásoknak, mert az 1,0-es verzió telepítve van, bár a frissítés újabb verziója, Update3, kompatibilis a modellhez az eszköz frissítési példányában. A Device2 és a Device3 egyaránt megfelelő, mert a legmagasabb verziószámú frissítések kompatibilisek a telepített modellekkel.

A megfelelőség nem veszi figyelembe, hogy a frissítés telepítve van-e az eszköz csoportjára, vagy sem; az eszköz frissítése során közzétett frissítéseket tekinti át. Tehát a fenti példában még abban az esetben is, ha a Device1 telepítette a frissítést, az nem megfelelőnek minősül. A Device1 továbbra is nem megfelelőnek tekinti, amíg a Update3 telepítése sikeresen megtörténik. A megfelelőségi állapot segíthet megállapítani, hogy szükség van-e új központi telepítésekre. 

Ahogy az a fentiekben is látható, a IoT Hub az eszköz frissítésében három megfelelőségi állapot található:

*   A **legújabb frissítéskor** – az eszköz telepítette az eszköz frissítése számára közzétett legmagasabb verziószámú kompatibilis frissítést.
*   **Frissítés folyamatban** – aktív központi telepítés folyamatban van a legmagasabb verziószámú kompatibilis frissítés továbbítása az eszközön.
*   **Új frissítések érhetők el** – egy eszköz még nem telepítette a legmagasabb verziószámú kompatibilis frissítést, és nem aktív központi telepítéssel rendelkezik a frissítéshez.

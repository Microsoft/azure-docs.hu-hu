---
title: Az Azure Percept hanggombja és a LED viselkedése
description: További információ a gombról és az Azure Percept hangjának LED-állapotáról
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: 1d956e33a84b5509c16400c8f5f8e813d116411a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102095748"
---
# <a name="azure-percept-audio-button-and-led-behavior"></a>Az Azure Percept hanggombja és a LED viselkedése

Az alábbi útmutatást követve megtekintheti az Azure Percept hangjának gombját és a LED állapotait.

## <a name="button-behavior"></a>Gomb viselkedése

A gombok segítségével szabályozhatja az eszköz viselkedését.

|Gomb állapota|  Működés|
|------------|----------|
|Némító|  Nyomja meg a gombot a MIC-Array némításához/felnémításához. A gomb eseménye lenyomáskor aktiválódik.|
|ADÓVEVÕ/PTS|   Nyomja le az ADÓVEVÕ gombot a Kulcsszóválasztó állapot megkerüléséhez, és aktiválja a parancs figyelési állapotát. Nyomja meg ismét az ügynök aktív párbeszédének leállításához és a kulcsszó-bepecsételés állapotának visszavonásához. A gomb eseménye lenyomáskor aktiválódik. A PTS csak akkor működik, ha az ügynök beszéde közben megnyomja a gombot, nem pedig az ügynök figyeli vagy gondolja.|

## <a name="led-behavior"></a>LED-viselkedés

A LED-mutatók segítségével megtudhatja, milyen állapotban van az eszköz.

|VEZETETT|   LED-állapot|  EAR SoM állapota|
|---|------------|----------------| 
|L02|   1x fehér, statikus bekapcsolás |Bekapcsolás |
|L02|   1x fehér, 0,5 Hz-es villogás|  Hitelesítés folyamatban |
|L01 & L02 & L03|   3x kék, statikus bekapcsolva|     Várakozás a kulcsszóra|
|L01 & L02 & L03|   LED-tömb villogása, 20fps | Figyelés vagy beszéd|
|L01 & L02 & L03|   LED array Racing, 20fps|    Gondolkodás|
|L01 & L02 & L03|   3x piros, statikus bekapcsolva | Némító|

## <a name="next-steps"></a>Következő lépések

Az Azure Percept hangeszközével kapcsolatos hibaelhárítási tippekért tekintse meg ezt az [útmutatót](./troubleshoot-audio-accessory-speech-module.md).
---
title: Az Azure Percept hanggombja és a LED viselkedése
description: További információ a gombról és az Azure Percept hangjának LED-állapotáról
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: fa919acb527084d19ab88b2e7895d4e6ab0b72d3
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609071"
---
# <a name="azure-percept-audio-button-and-led-behavior"></a>Az Azure Percept hanggombja és a LED viselkedése

Az alábbi útmutatást követve tájékozódhat az Azure Percept audioeszköz gomb és LED állapotáról.

## <a name="button-behavior"></a>Gomb viselkedése

Az eszköz működésének vezérléséhez használja a gombokat.

|Gomb állapota|Működés|
|------------|----------|
|Némító|Nyomja meg a gombot a MIC-tömb némításához/felnémításához. A gomb eseménye lenyomáskor aktiválódik.|
|ADÓVEVÕ/PTS|Nyomja le az ADÓVEVÕ gombot a Kulcsszóválasztó állapot megkerüléséhez, és aktiválja a parancs figyelési állapotát. Nyomja meg újra az ügynök aktív párbeszédének leállítását, és térjen vissza a Kulcsszóválasztó állapotba. A gomb eseménye lenyomáskor aktiválódik. A PTS csak akkor működik, ha az ügynök beszéd közben megnyomja a gombot, nem pedig az ügynök figyeli vagy gondolja.|

## <a name="led-behavior"></a>LED-viselkedés

A LED-mutatók segítségével megtudhatja, melyik állapotban van az eszköz.

|VEZETETT|LED-állapot|EAR SoM állapota|
|---|------------|----------------|
|L02|1x fehér, statikus bekapcsolás|Bekapcsolás |
|L02|1x fehér, 0,5 Hz-es villogás|Hitelesítés folyamatban |
|L01 & L02 & L03|3x kék, statikus bekapcsolva|Várakozás a kulcsszóra|
|L01 & L02 & L03|LED-tömb villogása, 20fps |Figyelés vagy beszéd|
|L01 & L02 & L03|LED array Racing, 20fps|Gondolkodás|
|L01 & L02 & L03|3x piros, statikus bekapcsolva |Némító|

## <a name="next-steps"></a>Következő lépések

Az Azure Percept hangeszközével kapcsolatos hibaelhárítási tippekért tekintse meg ezt az [útmutatót](./troubleshoot-audio-accessory-speech-module.md).
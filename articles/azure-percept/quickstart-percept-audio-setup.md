---
title: Az Azure Percept hang beállítása
description: Ismerje meg, hogyan csatlakoztatható az Azure Percept hangeszköz az Azure Percept DK-hoz
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 03/25/2021
ms.openlocfilehash: fa3dad8cdd38e6db621d8194cc9472430c7c5008
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605790"
---
# <a name="azure-percept-audio-setup"></a>Azure Percept – hangbeállítás

Az Azure Percept hang az Azure Percept DK-mel működik együtt. Nincs szükség egyedi beállításra.

## <a name="prerequisites"></a>Előfeltételek

- Azure Percept DK (fejlesztői készlet)
- Azure Percept hang
- [Azure-előfizetés](https://azure.microsoft.com/free/)
- Az [Azure PERCEPT DK telepítési felülete](./quickstart-percept-dk-set-up.md): csatlakoztatta a fejlesztői készlet egy Wi-Fi hálózathoz, létrehozott egy IoT hub, és csatlakoztatta a fejlesztői készlet a IoT hub
- A 3,5 mm-es hangkimenethez csatlakoztatható hangszóró vagy fejhallgató (nem kötelező)

## <a name="connecting-your-devices"></a>Az eszközök csatlakoztatása

1. Csatlakoztassa az Azure Percept hangeszközt az Azure Percept DK Carrier-táblához a mellékelt Micro USB-ről USB-re – a kábelhez. Csatlakoztassa a kábel Micro USB-végpontját a hangvezérelt (fejlesztői) táblához, és a típust a Percept DK Carrier-táblához.

1. (Nem kötelező) a hangcsatlakozón keresztül csatlakoztatható a hangszóró vagy a fejhallgató az Azure Percept hangeszközhöz. Ez lehetővé teszi a Hangválaszok meghallgatását.

1. Kapcsolja be a fejlesztői készlet. A hangvezérelt L02 LED-je villogó fehérre változik, jelezve, hogy az eszköz be van kapcsolva, és hogy a hang SoM hitelesítve van.

1. Várjon, amíg a hitelesítési folyamat befejeződik – ez akár 3 percet is igénybe vehet.

1. Készen áll a prototípusok megkezdésére, ha a következők valamelyikét látja:

    - A LED L02 értéke fehérre változik: Ez azt jelzi, hogy a hitelesítés befejeződött, és a fejlesztői készlet még nem lett konfigurálva kulcsszóval.
    - Mindhárom LED kék színű: Ez azt jelzi, hogy a hitelesítés befejeződött, és a fejlesztői készlet egy kulcsszóval van konfigurálva.

## <a name="next-steps"></a>Következő lépések

Hozzon létre egy [kód nélküli beszédfelismerési megoldást](./tutorial-no-code-speech.md) az [Azure Percept Studióban](https://go.microsoft.com/fwlink/?linkid=2135819).

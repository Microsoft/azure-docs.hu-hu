---
title: Ismerkedés az Azure Percept hanggal
description: Ismerje meg, hogyan csatlakoztatható az Azure Percept hangeszköz az Azure Percept DK-hoz
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/18/2021
ms.custom: template-quickstart
ms.openlocfilehash: 660f03ce248a27a00fdd443964fbdba2fe3adeb0
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179272"
---
# <a name="azure-percept-audio-setup"></a>Azure Percept – hangbeállítás

Az Azure Percept hang az Azure Percept DK-mel működik együtt. Nincs szükség egyedi beállításra.

## <a name="prerequisites"></a>Előfeltételek

- Azure Percept DK (fejlesztői készlet)
- Azure Percept hang
- [Azure-előfizetés](https://azure.microsoft.com/free/)
- Az [Azure PERCEPT DK telepítési felülete](./quickstart-percept-dk-set-up.md): csatlakoztatta a fejlesztői készlet egy Wi-Fi hálózathoz, létrehozott egy IoT hub, és csatlakoztatta a fejlesztői készlet a IoT hub
- A 3,5 mm-es hangkimenethez csatlakoztatható hangszóró vagy fejhallgató (opcionális)

## <a name="connecting-your-devices"></a>Az eszközök csatlakoztatása

1. Csatlakoztassa az Azure Percept hangeszközt az Azure Percept DK Carrier-táblához a mellékelt Micro USB-ről USB-re – a kábelhez. Csatlakoztassa a kábel Micro USB-végpontját a közvetítő (Developer) táblához, és a típust a Percept DK Carrier-táblához.
1. (Nem kötelező) a hangcsatlakozón keresztül csatlakoztatható a hangszóró vagy a fejhallgató az Azure Percept hanghoz. Ez lehetővé teszi, hogy meghallgassa a hangsegéd hangválaszait. Ha nem csatlakozik hangszóróhoz vagy fejhallgatóhoz, akkor továbbra is megtekintheti a válaszokat szövegként a bemutató ablakban. 

1. Kapcsolja be a fejlesztői készlet. A L02 LED-je villogó fehérre változik, jelezve, hogy az eszköz be van kapcsolva, és hogy a hang SoM hitelesítve van.

1. Várjon, amíg a hitelesítési folyamat befejeződik – ez akár 3 percet is igénybe vehet.

1. Készen áll a prototípusok megkezdésére, ha a következők valamelyikét látja:

    - A LED L02 folytonos fehérre változik. Ez azt jelzi, hogy a hitelesítés befejeződött, és a fejlesztői készlet még nem lett konfigurálva kulcsszóval.
    - Mindhárom LED kékre vált. Ez azt jelzi, hogy a hitelesítés befejeződött, és a fejlesztői készlet egy kulcsszóval van konfigurálva.

## <a name="next-steps"></a>Következő lépések

Hozzon létre egy [kód nélküli beszédfelismerési megoldást](./tutorial-no-code-speech.md) az [Azure Percept Studióban](https://go.microsoft.com/fwlink/?linkid=2135819).

---
title: Ismerkedés az Azure Percept hanggal
description: Ismerje meg, hogyan csatlakoztatható az Azure Percept hangeszköz az Azure Percept DK-hoz
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/18/2021
ms.custom: template-quickstart
ms.openlocfilehash: 575107859f56df742ab41a299269c250511022b3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101665498"
---
# <a name="azure-percept-audio-setup"></a>Azure Percept – hangbeállítás

Az Azure Percept hang az Azure Percept DK-mel működik együtt. Nincs szükség egyedi beállításra.

## <a name="prerequisites"></a>Előfeltételek

- Azure Percept DK (fejlesztői készlet)
- Azure Percept hang
- [Azure-előfizetés](https://azure.microsoft.com/free/)
- Az [Azure PERCEPT DK telepítési felülete](./quickstart-percept-dk-set-up.md): csatlakoztatta a fejlesztői készlet egy Wi-Fi hálózathoz, létrehozott egy IoT hub, és csatlakoztatta a fejlesztői készlet a IoT hub

## <a name="connecting-your-devices"></a>Az eszközök csatlakoztatása

1. Csatlakoztassa az Azure Percept hangeszközét az Azure Percept DK Carrier-táblához a mellékelt USB Micro Type-B típussal az USB-kábellel. Csatlakoztassa a kábel mikro-Type-B végét a hang SoM és a típus – a Percept DK Carrier Board végponthoz.

1. Kapcsolja be a fejlesztői készlet.

    - Az audio SoM LED-L01 a tömör zöldre vált, jelezve, hogy az eszköz be van kapcsolva.
    - A LED L02 a villogó zöldre változik, jelezve, hogy a hang SoM hitelesítve van.

1. Várjon, amíg a hitelesítési folyamat befejeződik – ez akár 3 percet is igénybe vehet.

1. Készen áll a prototípusok megkezdésére, ha a következők valamelyikét látja:

    - A LED L01 kikapcsol, és a L02 fehérre vált. Ez azt jelzi, hogy a hitelesítés befejeződött, és a fejlesztői készlet még nem lett konfigurálva kulcsszóval.
    - Mindhárom LED kékre vált. Ez azt jelzi, hogy a hitelesítés befejeződött, és a fejlesztői készlet egy kulcsszóval van konfigurálva.

    > [!NOTE]
    > Ha a fejlesztői készlet nem végzi el a hitelesítést, forduljon az ügyfélszolgálathoz.

## <a name="next-steps"></a>Következő lépések

Hozzon létre egy [kód nélküli beszédfelismerési megoldást](./tutorial-no-code-speech.md).
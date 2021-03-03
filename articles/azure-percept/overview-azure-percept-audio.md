---
title: Az Azure Percept hangeszközének áttekintése
description: További információ az Azure Percept hangról
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: a63f471498667f58fc80f89323ad93b0feb8bc95
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663688"
---
# <a name="introduction-to-azure-percept-audio"></a>Az Azure Percept audio bemutatása

Az Azure Percept audio egy kiegészítő eszköz, amely a Speech AI-képességeket hozzáadja az Azure Percept DK-hoz. Egy előre konfigurált beszédfelismerési AI-gyorsító és egy négy mikrofonos lineáris tömb, amely lehetővé teszi az egyéni parancsok, a kulcsszavak kimutatása és a széles körű beszédfelismerés alkalmazását a helyi figyelő eszközökön. Az Azure Percept audio szolgáltatása lehetővé teszi, hogy az eszközök gyártói az Azure Percept DK-jövőkép képességeit új, intelligens hangvezérelt eszközökre terjesszen. Az Azure Percept DK, az Azure Percept Studio és más Azure Edge felügyeleti szolgáltatásokkal integráltan integrálható. A [Microsoft online áruházban](https://go.microsoft.com/fwlink/p/?LinkId=2155270)megvásárolható.

![Azure Percept hangeszköz.](./media/overview-azure-percept-audio/percept-audio.png)


## <a name="azure-percept-audio-components"></a>Azure Percept – hangösszetevők

Az Azure Percept audio a következő főbb összetevőket tartalmazza:

- Éles üzemre kész Azure Percept hangeszköz (SoM) négy mikrofonos lineáris tömbvel
- Fejlesztői tábla (2x gombokkal, 3x LED-ekkel, USB Micro és 3,5 mm audio jack)
- Szükséges kábelek: Flex kábel, USB Micro Type-B – USB-A
- Üdvözlőlap
- Mechanikus csatlakoztatási tábla integrált 80/20 1010 sorozatú csatlakoztatással


<!---

## How it works

Azure Percept Audio passes the audio input to the Azure Percept DK carrier board in a hybrid edge-cloud manner. Specifically,

- The Azure Percept Audio device: processes the incoming speech input to the clearest format by executing beam forming and echo cancellation befor sending the input to the Azure Percept DK. 
- The Azure Percept DK uses edge processing to perform keyword spotting and then sends the relevant inputs to Azure speech services.
- Cloud: Processing of natural language commands and phrases, in addition to keyword verification and retraining.
- Offline: If the device is offline it will detect the keyword and capture telemetry that there is no internet connection at the time of the command. It will not be able to weed out false accepts since it cannot perform keyword verification.

-->

## <a name="getting-started"></a>Első lépések

- [Az Azure Percept DK összeállítása](./quickstart-percept-dk-unboxing.md)
- [Az Azure Percept DK telepítési felületének befejezése](./quickstart-percept-dk-set-up.md)
- [Az Azure Percept-hangeszköz csatlakoztatása a fejlesztői készlet](./quickstart-percept-audio-setup.md)

### <a name="build-a-no-code-prototype"></a>Kód nélküli prototípus készítése

Hozzon létre egy [kód nélküli beszédfelismerési megoldást](./tutorial-no-code-speech.md) az Azure Percept hangsegéd-sablonokkal a vendéglátás, az egészségügy, a leltár és a gépjármű-forgatókönyvek használatához.

## <a name="additional-technical-information"></a>További technikai információk

- [Azure Percept audio Adatlap](./azure-percept-audio-datasheet.md)

## <a name="next-steps"></a>Következő lépések

Rendeljen egy Azure Percept hangeszközt a [Microsoft online áruházban](https://go.microsoft.com/fwlink/p/?LinkId=2155270).
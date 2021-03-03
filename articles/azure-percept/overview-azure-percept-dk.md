---
title: Az Azure Percept DK áttekintése
description: További információ az Azure Percept DK-ról
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: 4fd0a7cb575a109d1393527b48de3fa4e3446167
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663472"
---
# <a name="azure-percept-dk-overview"></a>Az Azure Percept DK áttekintése

Az Azure Percept DK egy élvonalbeli AI fejlesztői csomag, amely a fogalmakra épülő mesterséges intelligencia-igazolás kialakítására készült. Az [Azure Percept Studióval](./overview-azure-percept-studio.md)kombinálva hatékony, mégis egyszerűen használható platformot kínál a Edge AI-megoldások széles SPEKTRUMÚ mesterséges intelligencia-alkalmazások létrehozásához. A [Microsoft online áruházban](https://go.microsoft.com/fwlink/p/?LinkId=2155270)megvásárolható.

:::image type="content" source="./media/overview-azure-percept-dk/dk-image.png" alt-text="Lemezkép.":::

## <a name="key-features"></a>A legfontosabb jellemzők

- **Lehetőség a mesterséges intelligencia futtatására az Edge-** ben. A beépített hardveres gyorsítás lehetővé teszi, hogy a felhőhöz való kapcsolódás nélkül is futtasson látási AI-modelleket.
- A **megbízhatósági kapcsolat biztonságának beépített hardveres gyökere**. További részletekért tekintse meg az [Azure Percept Security](./overview-percept-security.md) áttekintését.
- **Zökkenőmentes integráció az [Azure Percept Studióval](./overview-azure-percept-studio.md)** és más Azure-szolgáltatásokkal. Például az Azure IoT Hub, az Azure Cognitive Services és az [élő videó Analytics](https://docs.microsoft.com/azure/media-services/live-video-analytics-edge/overview)
- **Támogatás a legfontosabb AI-platformokhoz**. Ilyenek például a ONNX és a TensorFlow.
- **Integráció az 80/20 railing rendszerrel**. Így könnyebben hozhat létre prototípusokat éles környezetekben. További információ az [8/20-integrációról](./overview-8020-integration.md).

## <a name="hardware-components"></a>Hardver-összetevők

- Az Azure Percept DK Carrier Board
    - NXP iMX8m processzor
    - Platformmegbízhatósági modul (TPM) 2,0-es verzió
    - WiFi és Bluetooth kapcsolat
    - Tekintse meg a teljes [adatlapot](./azure-percept-dk-datasheet.md)
- Az Azure Percept látási rendszer a modulon (SoM)
    - Intel Movidius – számtalan X (MA2085) látási feldolgozó egység (VPU)
    - RGB-kamera érzékelő, amely lehetővé teszi a másodpercek hozzáadását
    - Tekintse meg a teljes [adatlapot](./azure-percept-vision-datasheet.md)

## <a name="get-started-with-the-azure-percept-dk"></a>Ismerkedés az Azure Percept DK-mel

- A gyors indítások befejezése
    - [Az Azure Percept DK testetlen és összegyűjtése](./quickstart-percept-dk-unboxing.md)
    - [Az Azure Percept DK beállítása és az első víziós AI-modell futtatása](./quickstart-percept-dk-set-up.md)
- Az oktatóanyagokkal kapcsolatos fogalmak bizonyításának megkezdése
    - [Kód nélküli jövőkép megoldás létrehozása az Azure Percept Studióban](./tutorial-nocode-vision.md)
    - [Hangsegéd létrehozása az Azure Percept Studióban](./tutorial-no-code-speech.md)

## <a name="next-steps"></a>Következő lépések

Rendeljen egy Azure Percept DK-t a [Microsoft online áruházban](https://go.microsoft.com/fwlink/p/?LinkId=2155270).
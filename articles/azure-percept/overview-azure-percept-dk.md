---
title: Az Azure Percept DK áttekintése
description: További információ az Azure Percept DK-ról
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: template-concept
ms.openlocfilehash: 4e3acc4d3c3b772cbc6edf47d7216566c58d3dd0
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105026887"
---
# <a name="azure-percept-dk-overview"></a>Az Azure Percept DK áttekintése

Az Azure Percept DK egy Edge AI fejlesztői csomag, amelynek célja, hogy az [Azure Percept Studio](./overview-azure-percept-studio.md)alkalmazással látási és audio AI-megoldásokat fejlesszen. Az Azure Percept DK megvásárolható a [Microsoft online áruházban](https://go.microsoft.com/fwlink/p/?LinkId=2155270).

> [!div class="nextstepaction"]
> [Vásárlás most](https://go.microsoft.com/fwlink/p/?LinkId=2155270)

</br>

> [!VIDEO https://www.youtube.com/embed/Qj8NGn-7s5A]

## <a name="key-features"></a>A legfontosabb jellemzők

- Futtassa az AI-t a peremhálózat szélén. A beépített hardveres gyorsítással a fejlesztői készlet a felhőhöz való kapcsolódás nélkül is futtathat AI-modelleket.

- A megbízhatósági kapcsolat biztonságának beépített hardveres gyökere. További információ az [Azure Percept biztonságáról](./overview-percept-security.md).

- Zökkenőmentes integráció az [Azure Percept Studióval](https://go.microsoft.com/fwlink/?linkid=2135819) és más Azure-szolgáltatásokkal, mint például az Azure IoT hub, az Azure Cognitive Services és az [élő video Analytics](https://docs.microsoft.com/azure/media-services/live-video-analytics-edge/overview).

- Kompatibilis az [Azure Percept audio](./overview-azure-percept-audio.md)szolgáltatással, amely egy opcionális tartozék a mesterséges intelligenciát használó megoldások létrehozásához.

- Harmadik féltől származó AI-eszközök, például a ONNX és a TensorFlow támogatása.

- Integráció az 80/20-es korláttal, amely lehetővé teszi a végtelen eszköz-csatlakoztatási konfigurációk használatát. További információ az [80/20-integrációról](./overview-8020-integration.md).

## <a name="hardware-components"></a>Hardver-összetevők

- Azure Percept DK Carrier Board:
    - NXP iMX8m processzor
    - Platformmegbízhatósági modul (TPM) 2,0-es verzió
    - Wi-Fi és Bluetooth-kapcsolat
    - További információ: [Azure PERCEPT DK adatlap](./azure-percept-dk-datasheet.md)

- Azure Percept látási rendszer – modul (SoM):
    - Intel Movidius – számtalan X (MA2085) látási feldolgozó egység (VPU)
    - RGB-kamera érzékelője
    - További információ: [Azure Percept-jövőkép Adatlapja](./azure-percept-vision-datasheet.md)

## <a name="getting-started-with-azure-percept-dk"></a>Ismerkedés az Azure Percept DK-mel

- A fejlesztői csomag beállítása:
    - [Az Azure Percept DK testetlen és összegyűjtése](./quickstart-percept-dk-unboxing.md)
    - [Az Azure Percept DK telepítési felületének befejezése](./quickstart-percept-dk-set-up.md)

- Megkezdheti a jövőkép és az Audio megoldások létrehozását:
    - [Kód nélküli jövőkép megoldás létrehozása az Azure Percept Studióban](./tutorial-nocode-vision.md)
    - [Kód nélküli beszédfelismerési megoldás létrehozása az Azure Percept Studióban](./tutorial-no-code-speech.md) (Azure Percept audio kellék szükséges)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure Percept DK vásárlása a Microsoft online áruházból](https://go.microsoft.com/fwlink/p/?LinkId=2155270)
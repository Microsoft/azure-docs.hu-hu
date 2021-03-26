---
title: Az Azure Percept DK áttekintése
description: További információ az Azure Percept DK-ról
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: template-concept
ms.openlocfilehash: b20448a88cee6c4ea4af0bcbfaa6ec465f2e28bb
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105560964"
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
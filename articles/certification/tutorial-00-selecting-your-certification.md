---
title: Azure Certified Device program – oktatóanyag – a minősítési program kiválasztása
description: Részletes útmutató az eszköz megfelelő minősítési programjainak kiválasztásához
author: Chuckb1300
ms.author: cbroad
ms.service: certification
ms.topic: tutorial
ms.date: 03/19/2021
ms.custom: template-tutorial
ms.openlocfilehash: b6ab8a4f971a065764731abddf72e26c628ba6cb
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969711"
---
# <a name="tutorial-select-your-certification-program"></a>Oktatóanyag: válassza ki a minősítési programot

Gratulálunk az Azure Certified-eszköz kiválasztásához! Izgatottan várjuk, hogy Ön csatlakozzon a hitelesített eszközök ökoszisztémához. Először meg kell határoznia, hogy mely minősítési programok felelnek meg legjobban az eszköz képességeinek.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Válassza ki az eszközének legmegfelelőbb minősítési program (oka) t

## <a name="selecting-a-certification-program-for-your-device"></a>Az eszközhöz tartozó minősítési program kiválasztása

Minden eszköznek meg kell felelnie az [**Azure Certified**](./program-requirements-azure-certified-device.md) minősítésű eszköz tanúsítványa által ismertetett alapkövetelményeknek. A másik három minősítési jelvény erre a programra épül, és egy másik ügyfél-értéket is biztosít. Kiválaszthat egyet vagy többet a három növekményes jelvény közül (IoT Plug and Play, Edge által felügyelt és Edge-védelemmel ellátott Core * előzetes verzió).

1. Tekintse át az alábbi táblázatban található minősítési programok követelményeit. Az egyes programokra vonatkozó részletes követelményeket a fejlécek kiszolgálónévnek.

    |Követelmény|[IoT Plug and Play](./program-requirements-edge-secured-core.md)|[Edge által felügyelt](./program-requirements-edge-managed.md)|[Edge Secured – mag](./program-requirements-edge-secured-core.md)|
    ---|---|---|---
    | Processzor | Bármelyik|MPU/CPU|MPU/CPU|
    | Operációs rendszer | Bármelyik|[1. szintű operációs rendszer](../iot-edge/support.md?view=iotedge-2018-06&preserve-view=true))|[1. szintű operációs rendszer](../iot-edge/support.md?view=iotedge-2018-06&preserve-view=true)|
    | IoT Edge-futtatókörnyezet | Nem támogatott |Kötelező|Kötelező|
    | Defender IoT-hez | Nem támogatott|Kötelező|Kötelező|
    | ADU/Windows Update | Nem támogatott|Kötelező|Kötelező|

1. Ha úgy érzi, hogy megérti, mi szükséges az eszközhöz, tekintse át a program technikai követelményeit. Ez lehet az Azure Certified eszköz minősítése, vagy az alapkonfiguráció-minősítés kombinációja a három növekményes jelvény egyikével. 

## <a name="next-steps"></a>Következő lépések

Most már készen áll arra, hogy megkezdje az eszköz tanúsítását! Folytassa a következő cikkel a projekt megkezdéséhez.
> [!div class="nextstepaction"]
>[Oktatóanyag: a projekt létrehozása](tutorial-01-creating-your-project.md)

---
title: Víziós AI-modell üzembe helyezése az Azure Percept DK-ben
description: Ismerje meg, hogyan helyezhet üzembe egy víziós AI-modellt az Azure Percept DK-ből az Azure Percept studióból
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 5ad263f5ae3b0b8e91de30b620002e33086608b4
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662287"
---
# <a name="capture-images-for-a-vision-project"></a>Képek rögzítése egy víziós projekthez

Ezt az útmutatót követve képeket rögzíthet az Azure Percept DK víziójában, amely az Azure Percept Studio egy meglévő víziós projektjét mutatja be. Ha még nem hozott létre egy víziós projektet, tekintse meg a [kód nélküli jövőképet ismertető oktatóanyagot](./tutorial-nocode-vision.md).

## <a name="prerequisites"></a>Előfeltételek

- Azure Percept DK (fejlesztői készlet)
- [Azure-előfizetés](https://azure.microsoft.com/free/)
- Az [Azure PERCEPT DK telepítési felülete](./quickstart-percept-dk-set-up.md): csatlakoztatta a fejlesztői készlet egy Wi-Fi hálózathoz, létrehozott egy IoT hub, és csatlakoztatta a fejlesztői készlet a IoT hub
- [Nem Code-víziós projekt](./tutorial-nocode-vision.md)

## <a name="capture-images"></a>Lemezképek rögzítése

1. Kapcsolja be a fejlesztői készlet.

1. Navigáljon az [Azure Percept studióhoz](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Az Áttekintés lap bal oldalán kattintson az **eszközök** elemre.

    :::image type="content" source="./media/how-to-capture-images/overview-devices-inline.png" alt-text="Az Azure Percept Studio áttekintése képernyő." lightbox="./media/how-to-capture-images/overview-devices.png":::

1. Válassza ki a fejlesztői készlet a listából.

    :::image type="content" source="./media/how-to-capture-images/select-device.png" alt-text="Percept-eszközök listája.":::

1. Az eszköz lapján kattintson a projektek rendszerképének **rögzítése** elemre.

    :::image type="content" source="./media/how-to-capture-images/capture-images.png" alt-text="Percept-eszközök lap a felsorolt elérhető műveletekkel.":::

1. A **rendszerkép rögzítése** ablakban tegye a következőket:

    1. A **projekt** legördülő menüben válassza ki azt a jövőkép-projektet, amelyhez képeket kíván gyűjteni.

    1. Kattintson az **eszköz stream megtekintése** elemre, és győződjön meg arról, hogy a rendszer helyesen helyezi el a jövőképet.

    1. A rendszerkép rögzítéséhez kattintson a **fénykép készítése** gombra.

    1. Másik lehetőségként jelölje be az **automatikus rendszerkép-rögzítés** melletti jelölőnégyzetet a rendszerkép-rögzítés időzítő beállításához:

        1. A **rögzítési arány** területen válassza ki a kívánt képalkotási arányt.
        1. Válassza ki a **cél** alatt gyűjteni kívánt rendszerképek teljes számát.

    :::image type="content" source="./media/how-to-capture-images/take-photo.png" alt-text="Percept-eszközök lap a felsorolt elérhető műveletekkel.":::

Minden rendszerkép elérhető lesz [Custom Visionban](https://www.customvision.ai/).

## <a name="next-steps"></a>Következő lépések

[Tesztelje és adja át a kód nélküli jövőkép modelljét](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/test-your-model).
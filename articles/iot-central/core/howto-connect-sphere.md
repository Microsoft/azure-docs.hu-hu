---
title: Azure Sphere-eszköz csatlakoztatása az Azure-ban IoT Central | Microsoft Docs
description: Megtudhatja, hogyan csatlakoztatható Azure Sphere (fejlesztői készlet) eszköz egy Azure IoT Central-alkalmazáshoz.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.author: sandeepu
author: sandeeppujar
ms.date: 04/30/2020
ms.custom: device-developer
ms.openlocfilehash: 770f6e56a669ab2d9b425a7a2879eeef5d37377b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92123423"
---
# <a name="connect-an-azure-sphere-device-to-your-azure-iot-central-application"></a>Azure Sphere-eszköz csatlakoztatása az Azure IoT Central-alkalmazáshoz

*Ez a cikk az eszközök fejlesztőire vonatkozik.*

Ez a cikk bemutatja, hogyan csatlakoztatható egy Azure Sphere (fejlesztői készlet) eszköz egy Azure IoT Central-alkalmazáshoz.

Az Azure Sphere biztonságos, magas szintű alkalmazásplatform, amely beépített kommunikációt és biztonsági funkciókat nyújt az internethez kapcsolódó eszközökhöz. Tartalmaz egy biztonságos, csatlakoztatott, crossover mikrovezérlő-egységet (MCU), egy egyéni, magas szintű Linux-alapú operációs rendszert (OS) és egy felhőalapú biztonsági szolgáltatást, amely folyamatos, megújítható biztonságot nyújt. További információ: [Mi az Azure Sphere?](/azure-sphere/product-overview/what-is-azure-sphere)

[Azure Sphere fejlesztői készletek](https://azure.microsoft.com/services/azure-sphere/get-started/) biztosítják mindazt, amire szüksége lehet a prototípus-készítéshez és a Azure Sphere alkalmazások fejlesztéséhez. Az Azure IoT Central with Azure Sphere lehetővé teszi egy teljes körű verem használatát egy IoT-megoldáshoz. A Azure Sphere biztosítja az eszközök támogatását, és a IoT Centralt nulla kód, felügyelt IoT alkalmazási platformként.

Ebben a útmutatóban a következőket kell tennie:

- Hozzon létre egy Azure Sphere eszközt a IoT Central a könyvtárból a Azure Sphere fejlesztői készlet-eszköz sablonjának használatával.
- Készítse elő Azure Sphere fejlesztői készlet eszközt az Azure IoT.
- Kapcsolódjon Azure Sphere fejlesztői készlet az Azure IoT Centralhoz.
- Tekintse meg a telemetria az eszközről IoT Centralban.

## <a name="prerequisites"></a>Előfeltételek

Az cikkben ismertetett lépések elvégzéséhez az alábbi erőforrásokra lesz szüksége:

- Azure IoT Central-alkalmazás.
- Visual Studio 2019, 16,4 vagy újabb verzió.
- Egy [Azure Sphere MT3620 Development Kit a Seeed studióból](/azure-sphere/hardware/mt3620-reference-board-design).

> [!NOTE]
> Ha nem rendelkezik fizikai eszközzel, az első lépés után ugorjon az utolsó szakaszra egy szimulált eszköz kipróbálásához.

## <a name="create-the-device-in-iot-central"></a>Az eszköz létrehozása IoT Central

Azure Sphere-eszköz létrehozása a IoT Centralban:

1. Az Azure IoT Central alkalmazásban válassza az **eszközök sablonok** fület, és válassza az **+ új** lehetőséget. A témakörben a **Kiemelt eszköz sablon használata** területen válassza a **Azure Sphere minta eszköz** elemet.

    :::image type="content" source="media/howto-connect-sphere/sphere-create-template.png" alt-text="Azure Sphere fejlesztői készlet tartozó eszköz sablonja":::

1. Az eszköz sablonjában szerkessze az **Áttekintés** nevű nézetet a **hőmérséklet** és a **gomb megnyomásának** megjelenítéséhez.

1. Az **eszköz és a Felhőbeli** adatnézet típusának kiválasztásával adjon hozzá egy másik nézetet, amely az olvasási/írási tulajdonság **állapotát** mutatja. Húzza a **status LED** tulajdonságot az űrlap jobb oldalán található üres, pontozott négyszögre. Kattintson a **Mentés** gombra.

## <a name="prepare-the-device"></a>Az eszköz előkészítése

Ahhoz, hogy a Azure Sphere fejlesztői készlet eszközt IoT Centralhoz lehessen kapcsolni, be kell [állítania az eszköz-és fejlesztési környezetet](https://github.com/Azure/azure-sphere-samples/tree/master/Samples/AzureIoT).

## <a name="connect-the-device"></a>Az eszköz csatlakoztatása

Ahhoz, hogy a minta IoT Centralhoz kapcsolódjon, [konfigurálnia kell egy Azure IoT Central alkalmazást, majd módosítania kell a minta alkalmazás-jegyzékét](https://aka.ms/iotcentral-sphere-git-readme).

## <a name="view-the-telemetry-from-the-device"></a>A telemetria megtekintése az eszközről

Amikor az eszköz csatlakoztatva van IoT Centralhoz, az irányítópulton láthatja a telemetria.

:::image type="content" source="media/howto-connect-sphere/sphere-view.png" alt-text="Azure Sphere fejlesztői készlet irányítópultja":::

## <a name="create-a-simulated-device"></a>Szimulált eszköz létrehozása

Ha nem rendelkezik fizikai Azure Sphere fejlesztői készlet eszközzel, egy szimulált eszközt hozhat létre az Azure IoT Central alkalmazás kipróbálásához.

Szimulált eszköz létrehozása:

- **Eszközök kiválasztása > Azure IoT szférában**
- Válassza a **+ Új** lehetőséget.
- Adjon meg egy egyedi **eszköz-azonosítót** és egy felhasználóbarát **eszköznév**.
- Engedélyezze a **szimulált** beállítást.
- Válassza a **Létrehozás** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ha Ön egy eszköz fejlesztője, néhány javasolt lépés a következő:

- További információ [az eszközök kapcsolatáról az Azure IoT Central](./concepts-get-connected.md)
- Ismerje meg, hogyan [figyelheti az eszközök kapcsolatát az Azure CLI használatával](./howto-monitor-devices-azure-cli.md)
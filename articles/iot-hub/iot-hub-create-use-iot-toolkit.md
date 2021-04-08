---
title: Azure-IoT Hub létrehozása a VS Code-hoz készült Azure IoT Tools használatával | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre egy Azure IoT hub-t egy erőforráscsoporthoz az Azure IoT Tools for Visual Studio Code használatával.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: 668087ae596688e86b7b84a16bc5c0fd0f9fcef6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "75912244"
---
# <a name="create-an-iot-hub-using-the-azure-iot-tools-for-visual-studio-code"></a>IoT hub létrehozása a Visual Studio Code-hoz készült Azure IoT Tools használatával

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Ez a cikk bemutatja, hogyan hozhat létre Azure IoT hub-t a [Visual Studio Code-hoz készült Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) használatával. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A cikk elvégzéséhez a következőkre lesz szüksége:

- Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- [Visual Studio Code](https://code.visualstudio.com/)

- A Visual Studio Code-hoz készült [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) .

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

1. A Visual Studio Code-ban nyissa meg a **Explorer** nézetet.

2. Az Explorer alján bontsa ki az **Azure IoT hub-eszközök** szakaszt. 

   ![Az Azure IoT Hub-eszközök kibontása](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. Kattintson a **...** elemre az **Azure IoT hub eszközök** szakasz fejlécében. Ha nem látja a három pontot, vigye a kurzort a fejléc fölé. 

4. Válassza a **létrehozás IoT hub** elemet.

5. A jobb alsó sarokban megjelenik egy előugró ablak, amely lehetővé teszi az Azure-ba való bejelentkezést az első alkalommal.

6. Válassza az Azure-előfizetés lehetőséget. 

7. Válassza az erőforráscsoport lehetőséget.

8. Válassza a hely lehetőséget.

9. Válassza ki az árképzési szintet.

10. Adja meg a IoT Hub globálisan egyedi nevét.

11. Várjon néhány percet, amíg a IoT Hub létre nem jön.

## <a name="next-steps"></a>Következő lépések

Most üzembe helyezett egy IoT hubot a Visual Studio Code-hoz készült Azure IoT Tools használatával. További információkért tekintse meg a következő cikkeket:

* [A Visual Studio Code-hoz készült Azure IoT Tools használatával üzeneteket küldhet és fogadhat az eszköz és egy IoT hub között](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

* [A Visual Studio Code-hoz készült Azure IoT Tools használata az Azure IoT Hub-eszközök felügyeletéhez](iot-hub-device-management-iot-toolkit.md)

* [Tekintse meg az Azure IoT hub a vs Code wikihöz lapot](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki).

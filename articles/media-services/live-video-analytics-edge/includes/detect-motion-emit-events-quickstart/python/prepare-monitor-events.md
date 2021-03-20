---
ms.openlocfilehash: 882ba60e16f770651a1d9fe3b02b61be2b4c34c8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99531623"
---
A Live Video Analytics IoT Edge modulján a beérkező élő videó streamben való mozgás észlelésére és az események IoT Hubba való küldésére használható. Az események megtekintéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az Explorer panelt a Visual Studio Code-ban, és keresse meg az Azure IoT Hub a bal alsó sarokban.
1. Bontsa ki az **eszközök** csomópontot.
1. Kattintson a jobb gombbal a **LVA-Sample-Device** elemre, és válassza a **figyelés beépített esemény végpontja** lehetőséget.

    ![Beépített esemény-végpont figyelésének megkezdése](../../../media/quickstarts/start-monitoring-iothub-events.png)

> [!NOTE]
> Előfordulhat, hogy a rendszer megkéri, hogy adjon meg egy beépített végponti információt a IoT Hub számára. Az információk lekéréséhez Azure Portalban navigáljon a IoT Hub, és keresse meg a **beépített végpontok** lehetőséget a bal oldali navigációs panelen. Kattintson ide, és az Event hub-kompatibilis **végpont** szakaszban keresse meg az **Event hub-kompatibilis végpontot** . Másolja ki és használja a szövegmezőben található szöveget. A végpont így fog kinézni:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

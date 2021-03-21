---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: fbb4e53e0047b9768a70c01aecfb7f31ae213b3f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96025497"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Fogyasztói csoport hozzáadása az IoT hub-hoz

A [fogyasztói csoportok](../articles/event-hubs/event-hubs-features.md#event-consumers) független nézeteket biztosítanak az esemény-adatfolyamhoz, amely lehetővé teszi, hogy az alkalmazások és az Azure-szolgáltatások egymástól függetlenül használják az adott Event hub-végpont adatait. Ebben a szakaszban egy fogyasztói csoportot ad hozzá az IoT hub beépített végpontához, amelyet az oktatóanyag későbbi részében használ az adatok végpontból való lekéréséhez.

Az alábbi lépéseket követve adhat hozzá egy fogyasztói csoportot az IoT hub-hoz:

1. Az [Azure Portalon](https://portal.azure.com/) nyissa meg az IoT Hubot.

2. A bal oldali panelen válassza a **beépített végpontok** lehetőséget, válassza a jobb oldali ablaktábla **események** elemét, és adjon meg egy nevet a **fogyasztói csoportok** területen. Kattintson a **Mentés** gombra.

   ![Fogyasztói csoport létrehozása az IoT hub-ban](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
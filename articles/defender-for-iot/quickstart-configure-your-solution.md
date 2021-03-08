---
title: 'Gyors útmutató: Azure-erőforrások hozzáadása a IoT-megoldáshoz'
description: Ebből a rövid útmutatóból megtudhatja, hogyan konfigurálhatja a végpontok közötti IoT megoldást az Azure Defender for IoT használatával.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: Shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/25/2021
ms.author: shhazam
ms.openlocfilehash: afe62e5cf255df28ea395405fc894ec5c15bb18c
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449713"
---
# <a name="quickstart-configure-your-azure-defender-for-iot-solution"></a>Gyors útmutató: Azure Defender konfigurálása IoT-megoldáshoz

Ez a cikk azt ismerteti, hogyan végezheti el a IoT biztonsági megoldás kezdeti konfigurálását a Defender for IoT használatával.

## <a name="prerequisites"></a>Előfeltételek

Nincs

## <a name="what-is-defender-for-iot"></a>Mi a IoT Defender?

A IoT Defender átfogó, teljes körű biztonságot nyújt az Azure-alapú IoT-megoldásokhoz.

A IoT Defender segítségével egyetlen irányítópulton figyelheti a teljes IoT-megoldást, és felhasználhatja az összes IoT-eszközt, IoT-platformot és háttér-erőforrást az Azure-ban.

Ha engedélyezte a IoT Hub, a Defender for IoT automatikusan azonosítja a többi Azure-szolgáltatást is, amely a IoT Hubhoz és a IoT-megoldáshoz kapcsolódik.

Az automatikus kapcsolat észlelése mellett kiválaszthatja és kiválaszthatja, hogy mely egyéb Azure-erőforráscsoportok legyenek felcímkézve a IoT-megoldás részeként.

A kiválasztott lehetőségek lehetővé teszik teljes előfizetések, erőforráscsoportok vagy önálló erőforrások hozzáadását.

Az összes erőforrás-kapcsolat definiálását követően a Defender a IoT a Defender használatával nyújt biztonsági javaslatokat és riasztásokat ezekhez az erőforrásokhoz.

## <a name="add-azure-resources-to-your-iot-solution"></a>Azure-erőforrások hozzáadása a IoT-megoldáshoz

Új erőforrás hozzáadása a IoT-megoldáshoz:

1. Nyissa meg a **IoT Hubt** Azure Portal.

1. A **biztonsági** válassza az **Áttekintés** elemet, majd a **Beállítások** elemet, majd válassza a **figyelt erőforrások** lehetőséget.

1. Válassza a **Szerkesztés** lehetőséget, és válassza ki a IoT-megoldáshoz tartozó megfigyelt erőforrásokat.

1. Válassza a **Hozzáadás** lehetőséget.

Gratulálunk! Új erőforráscsoportot adott hozzá a IoT-megoldáshoz.

A IoT Defender mostantól figyeli az újonnan hozzáadott erőforráscsoportokat, és a IoT-megoldás részeként felfedi a megfelelő biztonsági javaslatokat és riasztásokat.

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan hozhat létre biztonsági modulokat...

> [!div class="nextstepaction"]
> [Biztonsági modulok létrehozása](quickstart-create-security-twin.md)

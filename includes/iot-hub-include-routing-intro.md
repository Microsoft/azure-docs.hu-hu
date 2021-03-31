---
title: fájl belefoglalása
description: fájl belefoglalása
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 552a40be0c069d1002ebc7ea4dafe0d6f93a5755
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85128181"
---
Az [üzenet-útválasztás](../articles/iot-hub/iot-hub-devguide-messages-d2c.md) lehetővé teszi az telemetria-adatok küldését a IoT-eszközökről a beépített Event hub-kompatibilis végpontokra vagy egyéni végpontokra, például a blob Storage-ba, a Service Bus várólistákra, a Service Bus témakörökre és a Event Hubs. Az egyéni üzenet-útválasztás konfigurálásához [útválasztási lekérdezéseket](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md) kell létrehoznia az adott feltételnek megfelelő útvonal testreszabásához. A beállítás után az IoT Hub automatikusan a végpontokhoz irányítja át a bejövő adatokat. Ha egy üzenet nem egyezik a megadott útválasztási lekérdezések egyikével sem, a rendszer az alapértelmezett végpontra irányítja át.

Ebben a kétrészes oktatóanyagban megismerheti, hogyan állíthatja be és használhatja ezeket az egyéni útválasztási lekérdezéseket IoT Hub. Az üzeneteket egy IoT-eszközről irányítja át több végpontra, beleértve a blob Storage-t és egy Service Bus-várólistát is. A rendszer a Service Bus üzenetsor felé irányuló üzeneteket egy logikai alkalmazás használja, és e-mailben küldi el. Az egyéni üzenet-útválasztással nem rendelkező üzeneteket a rendszer az alapértelmezett végpontra küldi, majd Azure Stream Analytics és egy Power BI vizualizációban tekinti meg.

Az oktatóanyag 1. és 2. részének végrehajtásához hajtsa végre a következő feladatokat:

**I. rész: erőforrások létrehozása, üzenetek útválasztásának beállítása**
> [!div class="checklist"]
> * Hozza létre az erőforrásokat – egy IoT hub, egy Storage-fiók, egy Service Bus üzenetsor és egy szimulált eszköz. Ezt a Azure Portal, egy Azure Resource Manager sablon, az Azure CLI vagy a Azure PowerShell használatával teheti meg.
> * Konfigurálja a végpontokat és az üzenetek útvonalait IoT Hubban a Storage-fiók és a Service Bus üzenetsor számára.

**II. rész: üzenetek küldése a hubhoz, útválasztásos eredmények megtekintése**
> [!div class="checklist"]
> * Egy logikai alkalmazás létrehozása, amely egy üzenet Service Bus-üzenetsorba való érkezésekor indul el és küld e-maileket.
> * Egy olyan alkalmazás letöltése és futtatása, amely a Hubhoz üzeneteket küldő IoT-eszközt szimulál a különböző útválasztási lehetőségekhez.
> * Power BI-vizualizáció létrehozása az alapértelmezett végpontra küldött adatokról.
> * Eredmények megtekintése...
> * ... a Service Bus-üzenetsorban és az e-mailekben.
> * ... a tárfiókban.
> * ... a Power BI-vizualizációban.

## <a name="prerequisites"></a>Előfeltételek

* Az oktatóanyag 1. részében:
  - Rendelkeznie kell egy Azure-előfizetéssel. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Az oktatóanyag 2. részében:
  - El kell végeznie az oktatóanyag 1. részét, és az erőforrások továbbra is elérhetők maradnak.
  - A [Visual Studio](https://www.visualstudio.com/) telepítése.
  - Hozzáférés egy Power BI fiókhoz az alapértelmezett végpont stream Analytics elemzéséhez. ([A Power BI ingyenes kipróbálása](https://app.powerbi.com/signupredirect?pbi_source=web).)
  - Munkahelyi vagy iskolai fiókja van az értesítő e-mailek küldéséhez.
  - Győződjön meg arról, hogy a 8883-es port meg van nyitva a tűzfalon. Az oktatóanyagban szereplő minta MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](../articles/iot-hub/iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

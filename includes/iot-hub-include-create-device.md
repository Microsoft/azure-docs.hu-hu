---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 11/06/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 7b022f71e197c5695876f2049ee376c3616afc6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "70049030"
---
<!-- put the ## header in the file that includes this file -->

Ebben a szakaszban egy eszköz identitását hozza létre az IoT hub identitás-beállításjegyzékében. Egy eszköz nem tud csatlakozni a központhoz, hacsak nem rendelkezik bejegyzéssel az Identity registryben. További információ: [IoT Hub fejlesztői útmutató](../articles/iot-hub/iot-hub-devguide-identity-registry.md#identity-registry-operations).

1. Az IoT hub navigációs menüjében nyissa meg a **IoT eszközöket**, majd válassza az **új** lehetőséget az eszköz hozzáadásához az IoT hub-ban.

    ![Eszköz identitásának létrehozása a portálon](./media/iot-hub-include-create-device/create-identity-portal-vs2019.png)

1. Az **eszköz létrehozása** lapon adja meg az új eszköz nevét, például **myDeviceId**, majd válassza a **Mentés** lehetőséget. Ez a művelet létrehoz egy eszköz identitását az IoT hub számára.

   ![Új eszköz hozzáadása](./media/iot-hub-include-create-device/create-a-device-vs2019.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Az eszköz létrehozása után nyissa meg azt az **IoT-eszközök** panel eszközlistáján. Másolja az **elsődleges kapcsolódási karakterláncot** a későbbi használatra.

    ![Eszköz-csatlakoztatási karakterlánc](./media/iot-hub-include-create-device/device-details-vs2019.png)

> [!NOTE]
> Az IoT Hub-identitásjegyzék csak az IoT Hub biztonságos elérésének biztosításához tárolja az eszközidentitásokat. Az eszközazonosítókat és kulcsokat biztonsági hitelesítő adatokként tárolja, valamint tartalmaz egy engedélyezve/letiltva jelzőt, amellyel letilthatja egy adott eszköz hozzáférését. Ha az alkalmazásnak más eszközspecifikus metaadatokat kell tárolnia, egy alkalmazásspecifikus tárolót kell használnia. További információ: [IoT hub fejlesztői útmutató](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

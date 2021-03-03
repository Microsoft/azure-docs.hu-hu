---
title: Az Azure IoT Hub-erőforrások eszköz-frissítésének ismertetése | Microsoft Docs
description: Az Azure IoT Hub-erőforrások eszköz-frissítésének ismertetése
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ba43889b885252f68bb3b4b158b5626411aac3d5
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663641"
---
# <a name="device-update-resources"></a>Eszköz frissítési erőforrásai

Ha a IoT Hub eszköz frissítését szeretné használni, létre kell hoznia egy eszköz frissítési fiókját és a példány erőforrását. 

## <a name="device-update-account"></a>Eszköz frissítési fiókja

Az eszköz frissítési fiókja az Azure-előfizetésen belül létrehozott erőforrás. Az eszköz frissítési fiók szintjén kiválaszthatja azt a régiót, ahol az eszköz frissítési fiókja létre lesz hozva. Emellett engedélyeket is beállíthat azon felhasználók engedélyezéséhez, akik hozzáférhetnek az eszköz frissítéséhez.


## <a name="device-update-instance"></a>Eszköz frissítési példánya
A fiók létrehozása után létre kell hoznia egy eszköz-frissítési példányt. A példány egy olyan logikai tároló, amely egy adott IoT-hubhoz társított frissítéseket és központi telepítéseket tartalmaz. Az eszköz frissítése az IoT hub-t használja az eszköz könyvtáraként, valamint egy kommunikációs csatornát az eszközökkel. 

A nyilvános előzetes verzióban az előfizetés alapján két eszköz-frissítési fiók is létrehozható. Emellett a fiókhoz két eszköz frissítési példánya is létrehozható.

## <a name="configuring-device-update-linked-iot-hub"></a>Az eszköz frissítéséhez csatolt IoT Hub konfigurálása 

Ahhoz, hogy az eszköz frissítse IoT Hub változási értesítéseit, az eszköz frissítése a "beépített" esemény-hubhoz integrálódik. A példányban a "IoT Hub konfigurálása" gombra kattintva konfigurálhatja a IoT-eszközökkel való kommunikációhoz szükséges üzenet-útvonalakat és hozzáférési szabályzatot. 

A következő üzenet-útvonalak vannak konfigurálva az eszköz frissítéséhez:

|   Útvonal neve    | Útválasztási lekérdezés  | Leírás  |
| :--------- | :---- |:---- |
|  DeviceUpdate.DigitalTwinChanges | true |A digitális kettős változások eseményeinek figyelése  |
|  DeviceUpdate.DeviceLifeCycle | opType = ' deleteDeviceIdentity '  | A törölt eszközök figyelése |
|  DeviceUpdate.TelemetryModelInformation | iothub-Interface-id = "urn: azureiot: ModelDiscovery: ModelInformation: 1 | Új eszközök típusának figyelése |
|  DeviceUpdate.DeviceTwinEvents| (opType = ' updateTwin' vagy opType = ' replaceTwin') ÉS IS_DEFINED ($body. Tags. ADUGroup) | Az új eszköz frissítési csoportjai figyelése |

## <a name="next-steps"></a>Következő lépések

[Eszköz frissítési erőforrásainak létrehozása](./create-device-update-account.md)

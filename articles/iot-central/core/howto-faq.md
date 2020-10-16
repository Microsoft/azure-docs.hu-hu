---
title: Azure IoT Central gyakori kérdések | Microsoft Docs
description: Azure IoT Central gyakori kérdések (GYIK) és válaszok
author: dominicbetts
ms.author: dobett
ms.date: 09/23/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: cbe0dbe55f903c25556698a5d510bf9e67b408be
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92126720"
---
# <a name="frequently-asked-questions-for-iot-central"></a>Gyakori kérdések a IoT Central

**Hogyan ellenőrizze a hitelesítő adatokat, ha az eszköz nem csatlakozik a IoT Central alkalmazáshoz?**

Az [eszközöknek az Azure IoT Centralban nem látható adataival kapcsolatos hibák elhárítása](troubleshoot-connection.md) az eszközök csatlakozási problémáinak diagnosztizálásához szükséges lépéseket tartalmazza.

**Hogyan az ügyfélszolgálattal rendelkező jegyet?**

Ha segítségre van szüksége, egy Azure- [támogatási jegyet](https://portal.azure.com/#create/Microsoft.Support)is benyújthat.

További információkért, beleértve az egyéb támogatási lehetőségeket is, tekintse meg az [Azure IoT-támogatás és a súgó beállításait](../../iot-fundamentals/iot-support-help.md).

**Hogyan egy eszköz blokkolását?**

Ha egy eszköz le van tiltva, nem tud adatküldést küldeni a IoT Central alkalmazásnak. A letiltott eszközök állapota **Letiltva** az alkalmazás **eszközök** lapján. Az operátornak fel kell oldania az eszköz zárolását, mielőtt folytathatja az adatok küldését:

:::image type="content" source="media/howto-faq/blocked.png" alt-text="A letiltott eszközt bemutató képernyőfelvétel":::

Ha egy operátor feloldja egy eszköz zárolását, az állapot visszatér az előző értékre, **regisztrálva** vagy **kiépítve**.

**Hogyan jóváhagy egy eszközt?**

Ha az eszköz állapota **jóváhagyásra vár** az **eszközök** lapon, az azt jelenti, hogy az **automatikus jóváhagyás** lehetőség le van tiltva:

:::image type="content" source="media/howto-faq/auto-approve.png" alt-text="A letiltott eszközt bemutató képernyőfelvétel":::

Az operátornak explicit módon jóvá kell hagynia az eszközt az adatok küldésének megkezdése előtt. Az **Eszközök lapon nem** regisztrált eszközök, de az érvényes hitelesítő adatokkal való kapcsolat az eszköz állapota **jóváhagyásra vár**. Az operátorok a **jóváhagyás** gomb használatával hagyhatják jóvá ezeket az eszközöket az **eszközök** lapról:

:::image type="content" source="media/howto-faq/approve-device.png" alt-text="A letiltott eszközt bemutató képernyőfelvétel":::

**Hogyan az eszközt társítja egy eszköz sablonnal?**

Ha az eszköz állapota nincs **társítva**, az azt jelenti, hogy a IoT Centralhoz csatlakozó eszközhöz nincs társítva eszköz sablonja. Ez a helyzet általában a következő esetekben fordul elő:

- A Devices ( **eszközök** ) lapon található **Importálás** használatával az eszközök egy készlete lesz hozzáadva az eszköz sablonjának megadása nélkül.
- Egy eszköz regisztrálása manuálisan megtörténjen az **eszközök** lapon az eszköz sablonjának meghatározása nélkül. Az eszköz ezután érvényes hitelesítő adatokkal csatlakozik.  

Az operátor az **eszközök** lapon az **áttelepítés** gomb használatával rendelheti hozzá az eszközt az eszközhöz. További információ: eszközök [kezelése az Azure IoT Central alkalmazásban > eszközök áttelepítése sablonba](howto-manage-devices.md).

**Hol tudhatok meg többet a IoT Hubról?**

Az Azure IoT Central az Azure IoT Hubt használja Felhőbeli átjáróként, amely lehetővé teszi az eszközök kapcsolódását. IoT Hub a következőket teszi lehetővé:

- Adatfeldolgozás a felhőben.
- Eszközkezelés.
- Biztonságos eszközök kapcsolata.

További információ a IoT Hubről: [Azure IoT hub](../../iot-hub/index.yml).

**Hol tudhatok meg többet a Device kiépítési szolgáltatásról (DPS)?**

Az Azure IoT Central a DPS használatával teszi lehetővé, hogy az eszközök csatlakozzanak az alkalmazáshoz. Ha többet szeretne megtudni a DPS szerepkörről a IoT Central eszközök csatlakoztatásával kapcsolatban, tekintse meg az [Azure IoT Centralhoz](concepts-get-connected.md)való csatlakozást ismertető témakört. További információ a DPS-ról: [eszközök kiépítés az Azure IoT hub Device Provisioning Service](../../iot-dps/about-iot-dps.md)használatával.
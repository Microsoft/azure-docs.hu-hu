---
title: Az Azure IoT Hub az adatok titkosítását az ügyfelek által felügyelt kulcsok használatával | Microsoft Docs
description: Inaktív adatok titkosítása az ügyfél által felügyelt kulcsokkal IoT Hub
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: asrastog
ms.openlocfilehash: 352da24b36124ff0446a81c1ecbc584da545bb16
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92142212"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>Inaktív adatok titkosítása az ügyfél által felügyelt kulcsokkal IoT Hub

IoT Hub támogatja az inaktív adatok titkosítását az ügyfél által felügyelt kulcsokkal (CMK), más néven saját kulcs használata (BYOK). Az Azure IoT Hub inaktív és átvitel alatt álló adatok titkosítását teszi lehetővé az adatközpontokban, és a szolgáltatáshoz való hozzáférés során visszafejti azokat. Alapértelmezés szerint a IoT Hub a Microsoft által felügyelt kulcsokat használja az adatok titkosításához a nyugalmi állapotban. A CMK-mel egy másik titkosítási réteget is megadhat az alapértelmezett titkosításhoz, és a [Azure Key Vaulton](https://azure.microsoft.com/services/key-vault/)keresztül felügyelhető kulcs titkosítási kulccsal titkosíthatja az inaktív adatok mennyiségét. Ez rugalmasságot biztosít a hozzáférés-vezérlések létrehozásához, forgatásához, letiltásához és visszavonásához. Ha a BYOK konfigurálva van a IoT Hubhoz, kettős titkosítást is biztosítunk, amely egy második védelmi réteget biztosít, miközben lehetővé teszi a titkosítási kulcs vezérlését a Azure Key Vaulton keresztül.

Ehhez a funkcióhoz új IoT Hub létrehozása szükséges (alapszintű vagy standard csomag). Ha szeretné kipróbálni ezt a funkciót, lépjen kapcsolatba velünk a [Microsoft támogatási szolgálatán](https://azure.microsoft.com/support/create-ticket/)keresztül. Ossza meg vállalata nevét és előfizetési AZONOSÍTÓját, amikor kapcsolatba lép a Microsoft ügyfélszolgálatával.


## <a name="next-steps"></a>Következő lépések

* [További információ a IoT Hub](./about-iot-hub.md)

* [További információ a Azure Key Vault](../key-vault/general/overview.md)
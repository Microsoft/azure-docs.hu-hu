---
title: Ügyfél-adattárolás az Azure IoT Centralban | Microsoft Docs
description: Ez a cikk a vásárlói adattárolást ismerteti az Azure IoT Central-alkalmazásokban.
author: dominicbetts
ms.author: dobett
ms.date: 11/02/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 9cf803ddd5a3c9329cadefffc35ae6d6999323a1
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280688"
---
# <a name="azure-iot-central-customer-data-residency"></a>Azure IoT Central ügyfél-adattárolás

A IoT Central az ügyfél által megadott földrajzi helyen kívül nem tárolja az ügyféladatokat, kivéve az alábbi eseteket:

- Amikor új felhasználót ad hozzá egy meglévő IoT Central alkalmazáshoz, a felhasználó e-mail-azonosítója a földrajzon kívül is tárolható, amíg a meghívott felhasználó első alkalommal nem fér hozzá az alkalmazáshoz.

- IoT Central irányítópult-Térkép csempéi a [Azure Mapst](../../azure-maps/about-azure-maps.md)használják. Ha a Térkép csempét meglévő IoT Central alkalmazáshoz adja hozzá, a helyadatok feldolgozhatók vagy tárolhatók a Azure Maps szolgáltatás térinformatikai szabályainak megfelelően.

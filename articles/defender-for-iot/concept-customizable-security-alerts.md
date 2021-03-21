---
title: IoT Hub egyéni biztonsági riasztásai
description: Ismerje meg a testreszabható biztonsági riasztásokat, valamint ajánlott szervizelést a Defender használatával IoT Hub funkcióit és szolgáltatásait.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/16/2021
ms.author: shhazam
ms.openlocfilehash: 04198432f2b600a3c703d5e4f253656f116000db
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100636526"
---
# <a name="defender-for-iot-hub-custom-security-alerts"></a>Defender IoT Hub egyéni biztonsági riasztásokhoz

A IoT Defender folyamatosan elemzi a IoT-megoldást a fejlett Analitika és a fenyegetések felderítése révén, hogy figyelmeztessen a rosszindulatú tevékenységekre.

Javasoljuk, hogy hozzon létre egyéni riasztásokat a várt eszköz viselkedésének ismerete alapján, hogy a riasztások a leghatékonyabb mutatók legyenek a lehető leghatékonyabban az egyedi szervezeti üzembe helyezés és a környezet szempontjából.

A IoT-riasztások következő listáját a várt IoT Hub viselkedés alapján meghatározhatja. További információ az egyes riasztások testreszabásáról: [egyéni riasztások létrehozása](quickstart-create-custom-alerts.md).

## <a name="built-in-custom-alerts-in-the-iot-hub"></a>Beépített egyéni riasztások a IoT Hub

| Súlyosság | Riasztás neve | Adatforrás | Description | Javasolt szervizelés |
|--|--|--|--|--|
| Alacsony | Egyéni riasztás – az AMQP-protokollban az eszközökön lévő Felhőbeli üzenetek száma kívül esik az engedélyezett tartományon. | IoT Hub | Egy adott időszakon belül a felhőből az eszközre irányuló üzenetek (AMQP protokoll) száma a jelenleg konfigurált és engedélyezett tartományon kívül esik. |  |
| Alacsony | Egyéni riasztás – az AMQP-protokollban lévő elutasított Felhőbeli üzenetek száma kívül esik az engedélyezett tartományon. | IoT Hub | Az eszköz által visszautasított Felhőbeli üzenetek (AMQP protokoll) száma egy adott időszakon belül a jelenleg konfigurált és engedélyezett tartományon kívül esik. |  |
| Alacsony | Egyéni riasztás – az eszköz Felhőbeli üzeneteinek száma az AMQP protokollon kívül esik az engedélyezett tartományon. | IoT Hub | Az eszközről a felhőbe irányuló üzenetek (AMQP protokoll) egy adott időintervallumon belül a jelenleg konfigurált és engedélyezett tartományon kívül esnek. |  |
| Alacsony | Egyéni riasztás – a meghívott közvetlen metódusok száma kívül esik az engedélyezett tartományon. | IoT Hub | A megadott időszakon belül meghívott közvetlen metódusok mennyisége a jelenleg konfigurált és engedélyezett tartományon kívül esik. |  |
| Alacsony | Egyéni riasztás – a fájlfeltöltés száma kívül esik az engedélyezett tartományon. | IoT Hub | Egy adott időtartományon belüli fájlfeltöltés mennyisége a jelenleg konfigurált és engedélyezett tartományon kívül esik. |  |
| Alacsony | Egyéni riasztás – a HTTP-protokollon belül a felhőben lévő üzenetek száma a megengedett tartományon kívül esik | IoT Hub | Egy időablakban a felhőből az eszközre irányuló üzenetek (HTTP-protokoll) mennyisége nem a konfigurált engedélyezett tartományba esik. |
| Alacsony | Egyéni riasztás – a HTTP-protokollon belül visszautasított Felhőbeli üzenetek száma nem az engedélyezett tartományba esik. | IoT Hub | A megadott időintervallumon belül a felhőből az eszközre irányuló üzenetek (HTTP-protokoll) mennyisége a jelenleg konfigurált és engedélyezett tartományon kívül esik. |
| Alacsony | Egyéni riasztás – az eszköz és a felhő közötti üzenetek száma a HTTP-protokollon kívül esik az engedélyezett tartományon. | IoT Hub | Egy adott időintervallumon belül a Felhőbeli üzenetek (HTTP-protokoll) mennyisége a jelenleg konfigurált és engedélyezett tartományon kívül esik. |  |
| Alacsony | Egyéni riasztás – az MQTT-protokollban az eszközökön lévő Felhőbeli üzenetek száma kívül esik az engedélyezett tartományon. | IoT Hub | Egy adott időszakon belül a felhőből az eszközre irányuló üzenetek (MQTT protokoll) mennyisége a jelenleg konfigurált és engedélyezett tartományon kívül esik. |  |
| Alacsony | Egyéni riasztás – az MQTT-protokollban lévő elutasított Felhőbeli üzenetek száma kívül esik az engedélyezett tartományon. | IoT Hub | Az eszköz által az adott időszakon belül visszautasított felhő-eszköz üzenetek (MQTT protokoll) mennyisége a jelenleg konfigurált és engedélyezett tartományon kívül esik. |
| Alacsony | Egyéni riasztás – az eszköz Felhőbeli üzeneteinek száma az MQTT protokollon kívül esik az engedélyezett tartományon. | IoT Hub | Az eszközről a felhőbe irányuló üzenetek (MQTT protokoll) egy adott időintervallumon belül a jelenleg konfigurált és engedélyezett tartományon kívül esnek. |
| Alacsony | Egyéni riasztás – az engedélyezett tartományon kívül eső parancssori törlések száma | IoT Hub | Egy adott időszakon belül a parancssori törlések mennyisége a jelenleg konfigurált és engedélyezett tartományon kívül esik. |  |
| Alacsony | Egyéni riasztás – a modul két frissítésének száma kívül esik az engedélyezett tartományon. | IoT Hub | Egy adott időszakon belül a modulhoz tartozó dupla frissítések mennyisége a jelenleg konfigurált és engedélyezett tartományon kívül esik. |
| Alacsony | Egyéni riasztás – a jogosulatlan műveletek száma az engedélyezett tartományon kívül esik | IoT Hub | Egy adott időszakon belül nem engedélyezett műveletek mennyisége a jelenleg konfigurált és engedélyezett tartományon kívül esik. |

## <a name="next-steps"></a>Következő lépések

- Tudnivalók a [riasztások testreszabásáról](quickstart-create-custom-alerts.md)
- Defender a IoT szolgáltatáshoz [– Áttekintés](overview.md)
- [A biztonsági adatai elérésének](how-to-security-data-access.md) megismerése
- További információ az [eszközök kivizsgálása](how-to-investigate-device.md)

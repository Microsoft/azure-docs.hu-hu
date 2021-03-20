---
title: Azure IoT protokoll-átjáró | Microsoft Docs
description: Az Azure IoT protokoll-átjáró használata a IoT Hub képességek és protokollok támogatásának kibővítéséhez, hogy az eszközök a IoT Hub natív módon nem támogatott protokollok használatával csatlakozhassanak a központhoz.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/11/2017
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 315d76715973b79a971f3ca15bcc5186d20135a2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87325032"
---
# <a name="support-additional-protocols-for-iot-hub"></a>További protokollok támogatása IoT Hub

Az Azure IoT Hub natív módon támogatja a MQTT, a AMQP és a HTTPS protokollon keresztüli kommunikációt. Bizonyos esetekben előfordulhat, hogy az eszközök vagy a mezők átjárói nem tudják használni ezeket a szabványos protokollokat, és nem igénylik a protokollok módosítását. Ilyen esetekben egyéni átjárót használhat. Az egyéni átjárók lehetővé teszik a protokollok IoT Hub végpontokhoz való igazítását a IoT Hub felé irányuló és onnan érkező forgalom áthidalása révén. Az [Azure IoT protokoll-átjárót](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) egyéni átjáróként használva engedélyezheti a protokollok módosítását a IoT hub számára.

## <a name="azure-iot-protocol-gateway"></a>Azure IoT protokoll-átjáró

Az Azure IoT Protocol Gateway a protokollok átalakításának keretrendszere, amelyet nagy léptékű, kétirányú kommunikációra terveztek IoT Hub használatával. A Protocol Gateway egy csatlakoztatott összetevő, amely az eszközök kapcsolatait egy adott protokollon keresztül fogadja el. A forgalom a 1,0-as AMQP keresztül IoT Hub.

Az Azure-beli protokoll-átjárót az Azure Service Fabric, az Azure Cloud Services feldolgozói szerepkörök vagy a Windows Virtual Machines használatával rugalmasan méretezhető módon helyezheti üzembe. Emellett a protokoll-átjáró helyszíni környezetekben, például a Field gatewayben is üzembe helyezhető.

Az Azure IoT Protocol Gateway tartalmaz egy MQTT protokoll-adaptert, amely lehetővé teszi a MQTT-protokoll viselkedésének testreszabását, ha szükséges. Mivel IoT Hub a MQTT v 3.1.1 protokoll beépített támogatását biztosítja, csak akkor érdemes használni a MQTT protokoll-adaptert, ha a protokoll testreszabása vagy a további funkciókra vonatkozó speciális követelmények szükségesek.

A MQTT-adapter azt is mutatja be, hogy milyen programozási modellt kell létrehozni a protokoll-adapterek számára más protokollok létrehozásához. Emellett az Azure IoT Protocol Gateway programozási modellje lehetővé teszi egyéni összetevők csatlakoztatását speciális feldolgozáshoz, például az egyéni hitelesítéshez, az üzenetek átalakításához, a tömörítéshez/kibontáshoz, illetve az eszközök és a IoT Hub közötti adatforgalom titkosításához/visszafejtéséhez.

A rugalmasság érdekében az Azure IoT protokoll-átjárót és az MQTT-implementációt egy nyílt forráskódú szoftveres projekt biztosítja. A nyílt forráskódú projekttel különböző protokollok és protokollok támogatását is felhasználhatja, vagy testre szabhatja a forgatókönyv megvalósítását. 

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az Azure IoT protokoll-átjáróról, valamint arról, hogyan használhatja és telepítheti a IoT-megoldás részeként, tekintse meg a következőt:

* [Az Azure IoT Protocol Gateway adattár a GitHubon](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)

* [Az Azure IoT Protocol Gateway fejlesztői útmutatója](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Ha többet szeretne megtudni a IoT Hub központi telepítésének megtervezéséről, olvassa el a következőt:

* [Összehasonlítás Event Hubs](iot-hub-compare-event-hubs.md)

* [Skálázás, magas rendelkezésre állás és vész-helyreállítás](iot-hub-scaling.md)

* [IoT Hub fejlesztői útmutató](iot-hub-devguide.md)
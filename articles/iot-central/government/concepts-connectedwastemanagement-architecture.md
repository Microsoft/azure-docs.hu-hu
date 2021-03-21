---
title: Az Azure IoT Central beépített, csatlakoztatott hulladékgazdálkodási megoldás hivatkozási architektúrája | Microsoft Docs
description: Megtudhatja, hogyan használhatók az Azure IoT Central-vel létrehozott, csatlakoztatott hulladékgazdálkodási megoldás fogalmai.
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 2d6c8c693db42789a965ee896a6d913d6ee20ae0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99831671"
---
# <a name="connected-waste-monitoring-reference-architecture"></a>Az összekapcsolt hulladékmonitorozás referenciaarchitektúrája 

A csatlakoztatott hulladékkezelési megoldás az **Azure IoT Central alkalmazás-sablonnal** is létrehozható kick starter IoT alkalmazásként. Ez a cikk magas szintű hivatkozási architektúra-útmutatást nyújt a végpontok közötti megoldás létrehozásához. 

![Csatlakoztatott hulladékgazdálkodási architektúra](./media/concepts-connectedwastemanagement-architecture/concepts-connectedwastemanagement-architecture1.png)


Fogalmak:

1. Eszközök és kapcsolatok  
1. IoT Central 
1. Bővíthetőség és integrációk
1. Üzleti alkalmazások

Vessünk egy pillantást a kulcsfontosságú összetevőkre, amelyek általában egy részét játszanak a vízfogyasztás figyelésére szolgáló megoldásban.

## <a name="devices-and-connectivity"></a>Eszközök és kapcsolatok 
A nyílt környezetekben, például a hulladék-tárolókban használt eszközök az alacsony teljesítményű, nagy kiterjedésű hálózatokon (LPWAN) keresztül csatlakoztathatók egy külső hálózati szolgáltatón keresztül. Ilyen típusú eszközök esetén az [azure IoT Central Device Bridge](../core/howto-build-iotc-device-bridge.md) használatával az eszköz adatait elküldheti a IoT alkalmazásnak az Azure IoT Centralban. Előfordulhat, hogy az IP-címmel rendelkező eszköz-átjárók képesek, és közvetlenül a IoT Centralhoz tudnak csatlakozni.

## <a name="iot-central"></a>IoT Central 
Az Azure IoT Central egy IoT alkalmazási platform, amellyel gyorsan elindíthatja és futtathatja a IoT-megoldását. A megoldásokat a harmadik féltől származó szolgáltatásokkal is kiegészítheti, testreszabhatja és integrálhatja.
Miután az intelligens vízeszközeit IoT Centralhoz csatlakoztatotta, az eszköz parancsait és vezérlését, figyelését és riasztását, felhasználói felületét beépített RBAC, konfigurálható elemzéseket tartalmazó irányítópultokat és bővíthetőségi lehetőségeket kap. 

## <a name="extensibility-and-integrations"></a>Bővíthetőség és integrációk
Kiterjesztheti a IoT alkalmazást IoT Central és opcionálisan:
* a IoT-adatok átalakítása és integrálása speciális elemzésekhez, például a gépi tanulási modellek IoT Central alkalmazásból történő folyamatos exportálásával.
* a munkafolyamatok automatizálása más rendszerekben a műveletek automatizálásával vagy webhookok IoT Central alkalmazásból való aktiválásával
* a programozott módon IoT Central IoT Central API-kon keresztül férhet hozzá a IoT alkalmazáshoz.

## <a name="business-applications"></a>Üzleti alkalmazások 
A IoT-adatmennyiség használatával számos üzleti alkalmazást lehet kiszolgálni a hulladékkezelési segédprogramban. Ha szeretné megtudni, hogyan csatlakoztathatja a IoT Central csatlakoztatott hulladékgazdálkodási alkalmazást a Field Services szolgáltatással, kövesse a [Dynamics 365 Field Services integrálásával](./how-to-configure-connected-field-services.md) kapcsolatos oktatóanyagot. 

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [hozhat létre csatlakoztatott hulladékgazdálkodási](./tutorial-connected-waste-management.md) IoT Central alkalmazást
* További információ a [IoT Central Government-sablonokról](./overview-iot-central-government.md)
* További információ a IoT Centralről: [IoT Central áttekintése](../core/overview-iot-central.md)

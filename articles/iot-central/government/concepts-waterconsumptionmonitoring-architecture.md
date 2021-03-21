---
title: Az Azure IoT Central beépített vízfelhasználás-figyelési megoldás hivatkozási architektúrája | Microsoft Docs
description: Megtudhatja, hogyan használhatók az Azure IoT Central-vel készített, vízfogyasztást figyelő megoldásokra vonatkozó fogalmak.
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: da1eafee719919d7f3086a059d8d2c70ef8d8cb1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99831654"
---
# <a name="water-consumption-monitoring-reference-architecture"></a>A vízfogyasztás-monitorozás referenciaarchitektúrája 

A víz felhasználásának figyelésére szolgáló megoldások az **Azure IoT Central app sablonnal** is felhasználhatók a kick starter IoT alkalmazásként. Ez a cikk magas szintű hivatkozási architektúra-útmutatást nyújt a végpontok közötti megoldás létrehozásához. 

![Víz-felhasználás figyelési architektúrája](./media/concepts-waterconsumptionmonitoring-architecture/concepts-waterconsumptionmonitoring-architecture1.png)

Fogalmak:

1. Eszközök és kapcsolatok  
1. IoT Central 
1. Bővíthetőség és integrációk
1. Üzleti alkalmazások

Vessünk egy pillantást a kulcsfontosságú összetevőkre, amelyek általában egy részét játszanak a vízfogyasztás figyelésére szolgáló megoldásban.

## <a name="devices-and-connectivity"></a>Eszközök és kapcsolatok 
Ez a szakasz az intelligens vizes megoldásokhoz, például a vízminőség-figyeléshez vagy a vízfogyasztás figyeléséhez használt eszközökre vonatkozik, általában intelligens vízeszközökként. Az intelligens vizes eszközök lehetnek flow-mérők, vízminőség-figyelők, intelligens szelepek, szivárgás-felismerők stb.

Az intelligens vizes megoldásokban használt eszközök általában alacsony teljesítményű, nagy kiterjedésű hálózatokon (LPWAN) keresztül kapcsolódnak egy külső hálózati szolgáltatón keresztül. Ilyen típusú eszközök esetén az [azure IoT Central Device Bridge](../core/howto-build-iotc-device-bridge.md) használatával az eszköz adatait elküldheti a IoT alkalmazásnak az Azure IoT Centralban. Előfordulhat, hogy az IP-címmel rendelkező eszköz-átjárók képesek, és közvetlenül a IoT Centralhoz tudnak csatlakozni.

## <a name="iot-central"></a>IoT Central 
Az Azure IoT Central egy IoT alkalmazási platform, amellyel gyorsan elindíthatja és futtathatja a IoT-megoldását. A megoldásokat a harmadik féltől származó szolgáltatásokkal is kiegészítheti, testreszabhatja és integrálhatja.
Miután az intelligens vízeszközeit IoT Centralhoz csatlakoztatotta, az eszköz parancsait és vezérlését, figyelését és riasztását, felhasználói felületét beépített RBAC, konfigurálható elemzéseket tartalmazó irányítópultokat és bővíthetőségi lehetőségeket kap. 


## <a name="extensibility-and-integrations"></a>Bővíthetőség és integrációk
Kiterjesztheti a IoT alkalmazást IoT Central és opcionálisan:
* a IoT-adatok átalakítása és integrálása speciális elemzésekhez, például a gépi tanulási modellek IoT Central alkalmazás folyamatos exportálásával
* a munkafolyamatok automatizálása más rendszerekben a műveletek automatizálásával vagy webhookok IoT Central alkalmazásból való aktiválásával
* a IoT-alkalmazás programozott módon való elérése IoT Central API-kon keresztül IoT Central

## <a name="business-applications"></a>Üzleti alkalmazások 
A IoT-adatmennyiség különböző típusú üzleti alkalmazások kikapcsolására használható a víz-eszközön belül. Ha szeretné megismerni, hogyan csatlakoztatható a IoT Central a vízfelhasználást figyelő alkalmazáshoz a Field Services használatával, kövesse az oktatóanyagot a [Dynamics 365 Field Services integrálásával](./how-to-configure-connected-field-services.md) kapcsolatban. 


## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [hozhat létre vízfelhasználást](./tutorial-water-consumption-monitoring.md) IoT Central alkalmazást
* További információ a [IoT Central Government-sablonokról](./overview-iot-central-government.md)
* További információ a IoT Centralről: [IoT Central áttekintése](../core/overview-iot-central.md)

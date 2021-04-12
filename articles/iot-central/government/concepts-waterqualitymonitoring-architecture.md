---
title: Az Azure IoT Central beépített vízminőség-figyelési megoldás hivatkozási architektúrája | Microsoft Docs
description: Az Azure IoT Central beépített vízminőség-figyelési megoldással kapcsolatos fogalmak megismerése.
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 3dad4aea56586444bd54ac47c0462232913e173f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831603"
---
# <a name="water-quality-monitoring-reference-architecture"></a>A vízminőség-monitorozás referenciaarchitektúrája 
A vízminőség-figyelési megoldások az **Azure IoT Central app sablonnal** is felhasználhatók a kick starter IoT alkalmazásként. Ez a cikk magas szintű hivatkozási architektúra-útmutatást nyújt a végpontok közötti megoldás létrehozásához. 

![Vízminőség-figyelési architektúra](./media/concepts-waterqualitymonitoring-architecture/concepts-waterqualitymonitoring-architecture1.png)

Fogalmak:

1. Eszközök és kapcsolatok  
1. IoT Central 
1. Bővíthetőség és integrációk
1. Üzleti alkalmazások

Vessünk egy pillantást a kulcsfontosságú összetevőkre, amelyek általában a vízminőség-figyelési megoldás részét képezik.

## <a name="devices-and-connectivity"></a>Eszközök és kapcsolatok 
Ebben a szakaszban a víz minőségének monitorozása és a vízfelhasználás figyelése során használt eszközöket fogjuk használni, általában az intelligens víz eszközeként. Az intelligens vizes eszközök lehetnek flow-mérők, vízminőség-figyelők, intelligens szelepek, szivárgás-felismerők stb.

Az intelligens vizes megoldásokban használt eszközök általában alacsony teljesítményű, nagy kiterjedésű hálózatokon (LPWAN) keresztül kapcsolódnak egy külső hálózati szolgáltatón keresztül. Az ilyen típusú eszközök esetében kihasználhatja az [azure IoT Central Device Bridge](../core/howto-build-iotc-device-bridge.md) eszközt, hogy az eszköz adatait a IoT alkalmazásba küldje az Azure IoT Centralban. Előfordulhat, hogy az IP-címmel rendelkező eszköz-átjárók képesek, és közvetlenül a IoT Centralhoz tudnak csatlakozni.

## <a name="iot-central"></a>IoT Central 
Az Azure IoT Central egy IoT alkalmazási platform, amellyel gyorsan elindíthatja és futtathatja a IoT-megoldását. A megoldásokat a harmadik féltől származó szolgáltatásokkal is kiegészítheti, testreszabhatja és integrálhatja.
Miután az intelligens vízeszközeit IoT Centralhoz csatlakoztatotta, az eszköz parancsait és vezérlését, figyelését és riasztását, felhasználói felületét beépített RBAC, konfigurálható elemzéseket tartalmazó irányítópultokat és bővíthetőségi lehetőségeket kap. 

## <a name="extensibility-and-integrations"></a>Bővíthetőség és integrációk
Kiterjesztheti a IoT alkalmazást IoT Central és opcionálisan:
* a IoT-adatok átalakítása és integrálása speciális elemzésekhez, például a gépi tanulási modellek IoT Central alkalmazás folyamatos exportálásával
* a munkafolyamatok automatizálása más rendszerekben a műveletek automatizálásával vagy webhookok IoT Central alkalmazásból való aktiválásával
* a IoT-alkalmazás programozott módon való elérése IoT Central API-kon keresztül IoT Central

## <a name="business-applications"></a>Üzleti alkalmazások 
A IoT-adatforrások számos üzleti alkalmazást használhatnak a víz-eszközön belül. Ha szeretné megismerni, hogyan csatlakoztatható a IoT Central a vízminőség-figyelési alkalmazáshoz a Field Services használatával, kövesse a következő cikket: a [Dynamics 365 Field Services integrálása](./how-to-configure-connected-field-services.md). 


## <a name="next-steps"></a>Következő lépések
* Ismerje meg, hogyan [hozhat létre vízminőség-figyelési](./tutorial-water-quality-monitoring.md) IoT Central alkalmazást
* További információ a [IoT Central Government-sablonokról](./overview-iot-central-government.md)
* További információ a IoT Centralről: [IoT Central áttekintése](../core/overview-iot-central.md)

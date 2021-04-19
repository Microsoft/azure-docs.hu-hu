---
title: Az eszközszimuláció áttekintése – Azure | Microsoft Docs
description: Az eszközszimulációs megoldásgyorsító és képességeinek leírása.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 12/03/2018
ms.author: dobett
ms.openlocfilehash: 27a23ff924c2fa9e9e35fec010ca2a177868eacc
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713911"
---
# <a name="device-simulation-solution-accelerator-overview"></a>Az eszközszimulációs megoldásgyorsító áttekintése

Egy felhőalapú IoT-megoldásban az eszközök egy felhőbeli végponthoz csatlakoznak, és telemetriai adatokat, például hőmérsékletet, helyet és állapotot küldenek. A megoldás felhasználja ezt a telemetriát, lehetővé téve a műveleteket, vagy elemzések származtatását.

IoT-megoldás fejlesztésekor a kísérletezés és a tesztelés a folyamat alapvető része. A szimuláció fontos eszköz a folyamat során. Az Eszközszimulációval a következőt használhatja:

* Gyorsan beállíthatja a prototípus működését, majd a szimulált eszköz viselkedésének működés közbeni módosításával iterálhat. Ez a folyamat lehetővé teszi, hogy bizonyítsa az ötletet, mielőtt költséges hardverbe fektetne. A webes felhasználói felületen egyéni eszközöket hozhat létre másodpercek alatt egy prototípus létrehozásához.
* Valós eszközviselkedés szimulálásával ellenőrizze, hogy a megoldás az elvárt módon működik-e az eszköztől a megoldáson át. A JavaScript használatával összetett eszközviselkedéseket szkripteltethet valós szimulált telemetria létrehozásához.
* Skálázásával tesztelje a megoldást a normál, a csúcsidős és a csúcsterhelési körülményeken túli szimulálásával. A méretezési tesztek a megoldás futtatásához szükséges Azure-erőforrások megfelelő méretezésében is segítenek.

![Drónszimulációs minta](media/iot-accelerators-device-simulation-overview/dronesimulation.png)

Az Eszközszimulációval eszközmodelleket definiálhat a valós eszközök szimulálására. Ez a modell üzenetformátumokat, ikertulajdonságokat és metódusokat tartalmaz. A JavaScripttel összetett eszközviselkedéseket is szimulálhat.

Szimulációkat futtathat egy vagy több ezer, bármely IoT Hubhoz csatlakozó eszközről. A teszteléshez szükség esetén üzembe helyezhet egy IoT Hubot az Eszközszimulációval együtt egy önálló környezetben.

Az Eszközszimuláció ingyenes. Az Eszközszimuláció azonban a felhőben helyez üzembe egy Azure-előfizetést, és felhasználja az Azure-erőforrásokat. Ha az eszközszimuláció nem felel meg a követelményeknek, a forráskód a [GitHubon](https://github.com/Azure/azure-iot-pcs-device-simulation) is elérhető a másoláshoz és módosításhoz.

## <a name="sample-simulations"></a>Mintaszimulációk

Az Eszközszimuláció üzembe helyezésekor mintaszimulációkat és mintaeszközöket kap. Ezekkel a mintákkal megismerheti az Eszközszimuláció használatát. Első lépésekként futtatassa a [mintaszimulációt.](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/README.md) Saját szimulációt is létrehozhat a rendelkezésre bocsátott [mintaeszközök egyikével.](iot-accelerators-device-simulation-create-simulation.md)

![Szimuláció konfigurálása](media/iot-accelerators-device-simulation-overview/samplesimulation1.png)

## <a name="custom-simulated-devices"></a>Egyéni szimulált eszközök

Az Eszközszimulációval [egyéni eszközmodelleket](iot-accelerators-device-simulation-create-custom-device.md) hozhat létre a szimulációkban való használathoz. Meghatározhat például egy új hűtőeszközmodellt, amely hőmérséklet- és páratartalom-telemetriát küld. Az egyéni szimulált eszközök ideálisak az egyszerű eszközviselkedéshez véletlenszerű, növekvő vagy csökkenő telemetriaértékekkel.

![Eszközmodell létrehozása](media/iot-accelerators-device-simulation-overview/adddevicemodel.png)

## <a name="advanced-simulated-devices"></a>Speciális szimulált eszközök

Ha nagyobb felügyeletre van szüksége az eszközök által küldött telemetriai értékek felett, speciális eszközmodellt használhat. A speciális eszközmodellek lehetővé teszik a JavaScript-támogatást az elküldött telemetriaértékek manipulálására. Szimulálhatja például egy leparkolt autó belső hőmérsékletét egy meleg, meleg napon – a hőmérséklet emelkedése után a belső hőmérséklet exponenciálisan növekszik.

A fejlett eszközmodellekkel saját eszközmodelleket hozhat létre és tölthet fel, amelyek egy JSON-eszközdefiníciós fájlból és a megfelelő JavaScript-fájlokból állnak. [](iot-accelerators-device-simulation-advanced-device.md)

A speciális eszközmodellek a következő funkciókat ják:

* Adja meg az eszközről küldött üzenetformátumot és a telemetriatípusokat.
* Egyéni szkriptek használatával olyan telemetriai értékeket hozhat létre, amelyek fenntartják az eszköz állapotát az idő alatt.
* Egyéni szkriptek használatával adhatja meg, hogyan reagáljon a szimulált eszköz a metódusra.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben az eszközszimulációs megoldásgyorsítóról és annak képességeiről tanult. A megoldásgyorsító üzembe helyezéséhez látogasson el a GitHub-adattárba:

> [!div class="nextstepaction"]
> [IoT-eszközszimuláció üzembe helyezése és futtatása az Azure-ban](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/README.md)

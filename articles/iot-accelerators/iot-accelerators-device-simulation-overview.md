---
title: Az eszköz szimulációjának áttekintése – Azure | Microsoft Docs
description: Az eszköz szimulációs megoldás-gyorsító és képességeinek leírása.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 12/03/2018
ms.author: dobett
ms.openlocfilehash: f58eb05ed582cf18157a76f4d637d72a228f4e96
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "65464866"
---
# <a name="device-simulation-solution-accelerator-overview"></a>Az eszközszimulációs megoldásgyorsító áttekintése

Felhőalapú IoT-megoldás esetén az eszközök egy Felhőbeli végponthoz csatlakozva telemetria, például hőmérsékletet, helyet és állapotot küldenek. A megoldás ezt a telemetria használja, amely lehetővé teszi, hogy a műveletek elvégzéséhez vagy az adatokból származtatott eredményekhez jussanak.

IoT-megoldás fejlesztésekor a kísérletezés és a tesztelés a folyamat alapvető részei. A szimuláció egy fontos eszköz a folyamat során. Az eszköz szimulálásával a következőket teheti:

* Gyorsan lekérhet egy prototípust, majd megismételheti a szimulált eszköz viselkedésének menet közbeni módosítását. Ez a folyamat lehetővé teszi, hogy kiderítse az ötletet, mielőtt befektet a költséges hardverbe. A webes felületen keresztül létrehozhat egyéni eszközöket a prototípus-eszköz létrehozásához másodpercek alatt.
* Ellenőrizze, hogy a megoldás a várt módon működik-e az eszközről a megoldásra a valós eszközök viselkedésének szimulálása révén. A JavaScript használatával összetett eszköz-viselkedést is létrehozhat a reális szimulált telemetria létrehozásához.
* A megoldást a normál, a csúcs és a maximális terhelési feltételek szimulálása révén méretezheti a megoldásban. A méretezési tesztek segítenek a megoldás futtatásához szükséges Azure-erőforrások méretének megfelelő méretekben is.

![Példa a drone-szimulációra](media/iot-accelerators-device-simulation-overview/dronesimulation.png)

Az eszköz-szimulációval megadhatja az eszközök modelljeit, hogy szimulálja a valódi eszközöket. Ez a modell az üzenetek formátumait, a Twin tulajdonságokat és a metódusokat tartalmazza. A JavaScript használatával is szimulálhatja a komplex eszközök viselkedését.

A szimulációkat egy vagy több ezer eszközhöz is futtathatja, amely bármely IoT-hubhoz csatlakozik. Ha segítségre van szüksége a teszteléshez, telepítheti az IoT hubot, valamint az eszköz-szimulációt önálló környezetben.

Az eszköz szimulációja ingyenes. Az eszköz szimulációja azonban üzembe helyezi az Azure-előfizetését a felhőben, és az Azure-erőforrásokat használja fel. Ha az eszköz szimulációja nem felel meg a követelményeknek, a [forráskód a githubon is elérhető](https://github.com/Azure/device-simulation-dotnet) a másoláshoz és a módosításhoz.

## <a name="sample-simulations"></a>Példa szimulációk

Az eszköz-szimuláció üzembe helyezése során néhány példa szimulációt és minta-eszközt kap. Ezekkel a mintákkal megtudhatja, hogyan használhatja az eszköz-szimulációt. Első lépésként futtasson egy olyan [minta-szimulációt, amely 10 teherautót szimulál](quickstart-device-simulation-deploy.md). [Saját szimulációt is létrehozhat a számos megadott eszköz egyikének használatával](iot-accelerators-device-simulation-create-simulation.md).

![Szimuláció konfigurálása](media/iot-accelerators-device-simulation-overview/samplesimulation1.png)

## <a name="custom-simulated-devices"></a>Egyéni szimulált eszközök

Az eszköz szimulációjának használatával [Egyéni modelleket hozhat létre](iot-accelerators-device-simulation-create-custom-device.md) a szimulációkban való használatra. Megadhat például egy új hűtőszekrény-eszköz modellt, amely hőmérséklet-és páratartalom-telemetria küld. Az egyéni szimulált eszközök ideálisak az egyszerű eszközök működéséhez véletlenszerű, növekvő vagy csökkenő telemetria-értékekkel.

![Eszközmodell létrehozása](media/iot-accelerators-device-simulation-overview/adddevicemodel.png)

## <a name="advanced-simulated-devices"></a>Speciális szimulált eszközök

Ha nagyobb mértékű vezérlésre van szüksége az eszköz által küldött telemetria értékek felett, használhatja a speciális eszköz modelljét. A speciális eszközbeállítások lehetővé teszik a JavaScript támogatását az eljuttatott telemetria-értékek kezeléséhez. Megteheti például, hogy egy forró napsütéses nap belső hőmérsékletét szimulálja, ahogy a külső hőmérséklet növekszik, a belső hőmérséklet exponenciálisan növekszik.

A speciális eszközbeállítások lehetővé teszik [a saját eszköz-modelljeinek létrehozását és feltöltését](iot-accelerators-device-simulation-advanced-device.md) , amelyek egy JSON-eszköz definíciós fájljából és a megfelelő JavaScript-fájlokból állnak.

A speciális eszköz modelljei a következőket teszik lehetővé:

* Az eszközről a telemetria-típusokkal együtt küldött üzenet formátumának megadása.
* Egyéni parancsfájlok használatával olyan telemetria-értékeket hozhat elő, amelyek az eszköz állapotát az idő múlásával őrzik meg.
* Egyéni parancsfájlok használata annak megadásához, hogy a szimulált eszköz hogyan válaszoljon a metódusokra.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta az eszköz-szimulációs megoldás-gyorsító és annak képességeit. A megoldás-gyorsító használatának megkezdéséhez folytassa a gyors üzembe helyezéssel:

> [!div class="nextstepaction"]
> [IoT-eszköz szimulációjának üzembe helyezése és futtatása az Azure-ban](quickstart-device-simulation-deploy.md)

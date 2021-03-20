---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 08/20/2019
ms.openlocfilehash: c412f7f3e20e4d04083e457bfb245b850b65e126
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "70050398"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

Az ikereszközök JSON-dokumentumok, amelyek az eszközök állapotinformációit, például a metaadatokat, konfigurációkat és állapotokat tárolják. A IoT Hub minden olyan eszközön megtartja a különálló eszközt, amely csatlakozik hozzá.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Az eszközökhöz tartozó ikrek használata:

* Tárolja az eszköz metaadatait a megoldás hátterében.

* A jelenlegi állapotinformációkat, például az elérhető képességeket és feltételeket jelenti, például a használt csatlakozási módszert az eszköz alkalmazásból.

* Szinkronizálhatja a hosszan futó munkafolyamatok, például a belső vezérlőprogram és a konfigurációs frissítések állapotát egy eszköz-alkalmazás és egy háttérbeli alkalmazás között.

* Az eszköz metaadatait, konfigurációját vagy állapotát kérdezheti le.

Az eszközökhöz készült ikrek szinkronizálásra és az eszközök konfigurációjának és feltételeit kérdezik le. További információ az eszköz-ikrek használatáról: az [eszközök megismerése](../articles/iot-hub/iot-hub-devguide-device-twins.md).

Az eszközök az ikrek egy IoT-központban tárolódnak, és az alábbi elemeket tartalmazzák:

* **Címkék**. Az eszköz metaadatai csak a megoldás hátterében érhetők el.

* **Kívánt tulajdonságok**. A megoldás háttérbe állításával és az eszköz alkalmazás által Megfigyelhető JSON-objektumok.

* **Jelentett tulajdonságok**. Az eszköz alkalmazás által módosítható JSON-objektumok, amelyek a megoldás hátterében olvashatók.

A címkék és a tulajdonságok nem tartalmazhatnak tömböket, az objektumok pedig ágyazhatók egymásba.

Az alábbi ábrán az eszköz kettős szervezete látható:

![Eszköz különálló képe, amely funkcionalitást mutat](./media/iot-hub-selector-twin-get-started/twin.png)

Emellett a megoldás háttérbe állítása az eszközökhöz tartozó ikreket is lekérdezheti az összes fenti adattal függően.
További információ az eszközökről az ikrekről: az [eszközök megismerése](../articles/iot-hub/iot-hub-devguide-device-twins.md). A lekérdezéssel kapcsolatos további információkért lásd: [IoT hub lekérdezési nyelv](../articles/iot-hub/iot-hub-devguide-query-language.md).


Ez az oktatóanyag a következőket mutatja be:

* Hozzon létre egy háttérbeli alkalmazást, amely címkéket tesz a Twin eszközhöz, valamint egy szimulált eszköz alkalmazást, amely a kapcsolati csatornát jelentett tulajdonságként jelenti a Twin eszközön.

* A korábban létrehozott címkék és tulajdonságok szűrőinek használatával lekérdezheti a háttérbeli alkalmazásból származó eszközöket.

---
title: Az Azure IoT Hub beépített végpontjának ismertetése | Microsoft Docs
description: Fejlesztői útmutató – ismerteti, hogyan használható a beépített, Event hub-kompatibilis végpont az eszközről a felhőbe irányuló üzenetek olvasásához.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.custom: amqp
ms.openlocfilehash: 169d926e466559bc83ba64ce9e976e0d725f614d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81729993"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Az eszközről a felhőbe irányuló üzenetek beolvasása a beépített végpontról

Alapértelmezés szerint az üzenetek a beépített szolgáltatás felé irányuló végponthoz (**üzenetek/események**) lesznek irányítva, amely kompatibilis a [Event Hubsával](https://azure.microsoft.com/documentation/services/event-hubs/). Ez a végpont jelenleg csak a 5671-es porton elérhető [AMQP](https://www.amqp.org/) protokoll használatával érhető el. Az IoT hub a következő tulajdonságokat teszi elérhetővé, hogy lehetővé tegye a beépített Event hub-kompatibilis üzenetküldési végpont **üzeneteinek/eseményeinek**vezérlését.

| Tulajdonság            | Leírás |
| ------------------- | ----------- |
| **Partíciók száma** | Állítsa ezt a tulajdonságot a Létrehozás gombra az eszközről a felhőbe irányuló események betöltéséhez szükséges [partíciók](../event-hubs/event-hubs-features.md#partitions) számának meghatározásához. |
| **Megőrzési idő**  | Ez a tulajdonság határozza meg, hogy az üzenetek mennyi ideig maradnak meg a IoT Hub. Az alapértelmezett érték egy nap, de akár hét napig is megnövelhető. |

A IoT Hub legfeljebb 7 napig engedélyezi az adatok megőrzését a beépített Event Hubs. Megadhatja a megőrzési időt a IoT Hub létrehozásakor. Az adatmegőrzési idő IoT Hub az IoT hub szintjétől és az egység típusától függ. A méret szempontjából a beépített Event Hubs megtarthatja az üzenetek maximális mérete legfeljebb 24 órányi kvótát. Például az 1 S1 egység IoT Hub elegendő tárterületet biztosít ahhoz, hogy legalább 4k-os 400K-üzenetet őrizzen meg. Ha az eszközei kisebb üzeneteket küldenek, azok a tárterület méretétől függően (akár 7 napig) is megtarthatók. Garantáljuk, hogy a megadott megőrzési időre vonatkozó adatok minimálisan megmaradjanak. Az üzenetek lejárnak, és a megőrzési idő lejárta után nem lesznek elérhetők. 

A IoT Hub lehetővé teszi a fogyasztói csoportok kezelését is a beépített, eszközről a felhőbe irányuló fogadási végponton. Az egyes IoT Hubekhez legfeljebb 20 fogyasztói csoport tartozhat.

Ha [üzenet-útválasztást](iot-hub-devguide-messages-d2c.md) használ, és a [tartalék útvonal](iot-hub-devguide-messages-d2c.md#fallback-route) engedélyezve van, minden olyan üzenet, amely nem felel meg egyetlen lekérdezésnek sem, lépjen a beépített végpontra. Ha letiltja ezt a tartalék útvonalat, a rendszer elveti az összes lekérdezésnek nem megfelelő üzeneteket.

A megőrzési időt a [IoT hub erőforrás-szolgáltató REST API](/rest/api/iothub/iothubresource)-k vagy a [Azure Portal](https://portal.azure.com)használatával lehet módosítani.

IoT Hub elérhetővé teszi az **üzenetek/események** beépített végpontját a háttér-szolgáltatások számára, hogy beolvassa a hub által fogadott eszközről a felhőbe irányuló üzeneteket. Ez a végpont az Event hub-kompatibilis, amely lehetővé teszi az Event Hubs szolgáltatás által az üzenetek olvasásához támogatott mechanizmusok használatát.

## <a name="read-from-the-built-in-endpoint"></a>Olvasás a beépített végpontról

Néhány termék-integráció és Event Hubs SDK ismeri a IoT Hub, és lehetővé teszi az IoT hub szolgáltatás kapcsolati karakterláncának használatát a beépített végponthoz való csatlakozáshoz.

Ha Event Hubs SDK-kat vagy olyan termék-integrációt használ, amely nem tud IoT Hub, akkor az Event hub-kompatibilis végpont és az Event hub-kompatibilis név szükséges. Ezeket az értékeket a következő módon kérheti le a portálról:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon az IoT hubhoz.

2. Kattintson a **beépített végpontok**elemre.

3. Az **események** szakasz a következő értékeket tartalmazza: **Partitions**, **Event hub-kompatibilis név**, **Event hub-kompatibilis végpont**, **megőrzési idő**és **fogyasztói csoportok**.

    ![Eszközről a felhőbe irányuló beállítások](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

A portálon az Event hub-kompatibilis végpont mező egy teljes Event Hubs kapcsolati karakterláncot tartalmaz, amely a következőképpen néz ki: **Endpoint = SB://abcd1234namespace.servicebus.Windows.net/; SharedAccessKeyName = iothubowner; SharedAccessKey = keykeykeykeykeykey =; EntityPath = iothub-eHub-ABCD-1234-123456**. Ha az Ön által használt SDK más értékeket igényel, akkor a következő lesz:

| Name (Név) | Érték |
| ---- | ----- |
| Végpont | sb://abcd1234namespace.servicebus.windows.net/ |
| Gazdanév | abcd1234namespace.servicebus.windows.net |
| Névtér | abcd1234namespace |

Ezután bármilyen megosztott elérési házirendet használhat, amely rendelkezik a **ServiceConnect** engedéllyel a megadott Event hubhoz való kapcsolódáshoz.

A beépített, az Event hub-kompatibilis végponthoz való kapcsolódáshoz használható SDK-k, amelyek IoT Hub teszik a következőket:

| Nyelv | SDK | Példa | Megjegyzések |
| -------- | --- | ------ | ----- |
| .NET | https://github.com/Azure/azure-event-hubs-dotnet | [Első lépések](quickstart-send-telemetry-dotnet.md) | Event Hubs-kompatibilis információt használ |
 Java | https://github.com/Azure/azure-event-hubs-java | [Első lépések](quickstart-send-telemetry-java.md) | Event Hubs-kompatibilis információt használ |
| Node.js | https://github.com/Azure/azure-event-hubs-node | [Első lépések](quickstart-send-telemetry-node.md) | IoT Hub-kapcsolatok sztringjét használja |
| Python | https://github.com/Azure/azure-event-hubs-python | https://github.com/Azure/azure-event-hubs-python/blob/master/examples/iothub_recv.py | IoT Hub-kapcsolatok sztringjét használja |

A beépített, az Event hub-kompatibilis végponttal használható termék-integrációk, amelyeket IoT Hub tesznek elérhetővé:

* [Azure functions](https://docs.microsoft.com/azure/azure-functions/). Lásd: [adatok feldolgozása IoT Hubról Azure functions](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/).
* [Azure stream Analytics](https://docs.microsoft.com/azure/stream-analytics/). Lásd: [stream-adatok bevitele stream Analyticsba](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).
* [Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/). Lásd: [az IoT hub-eseményforrás hozzáadása a Time Series Insights-környezethez](../time-series-insights/time-series-insights-how-to-add-an-event-source-iothub.md).
* [Apache Storm kiöntő](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). A [kiöntő forrás](https://github.com/apache/storm/tree/master/external/storm-eventhubs) a githubon tekinthető meg.
* [Apache Spark integráció](../hdinsight/spark/apache-spark-eventhub-streaming.md).
* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).

## <a name="next-steps"></a>További lépések

* IoT Hub végpontokkal kapcsolatos további információkért lásd: [IoT hub-végpontok](iot-hub-devguide-endpoints.md).

* A [rövid](quickstart-send-telemetry-node.md) útmutatók bemutatják, hogyan küldhet eszközről a felhőbe irányuló üzeneteket a szimulált eszközökről, és hogyan olvashatja el az üzeneteket a beépített végpontról. 

További részletekért tekintse meg a [folyamat IoT hub eszközről a felhőbe](tutorial-routing.md) irányuló üzeneteket az útvonalak oktatóanyag használatával.

* Ha az eszközről a felhőbe irányuló üzeneteket egyéni végpontokra szeretné irányítani, tekintse meg [az üzenetek és az egyéni végpontok használata az eszközről a felhőbe irányuló üzenetekhez](iot-hub-devguide-messages-read-custom.md)című témakört.

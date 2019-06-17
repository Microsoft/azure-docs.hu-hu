---
title: IoT hub-eseményforrás hozzáadása Azure Time Series Insights |} A Microsoft Docs
description: Ez a cikk ismerteti, amely csatlakozik az IoT hub a Time Series Insights-környezetbe eseményforrás hozzáadása.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/01/2019
ms.custom: seodec18
ms.openlocfilehash: 955b0e36c63b181e2fe6d2f87e7b015196fceff9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66455597"
---
# <a name="add-an-iot-hub-event-source-to-your-time-series-insights-environment"></a>IoT hub-eseményforrás hozzáadása a Time Series Insights-környezethez

Ez a cikk ismerteti, amely adatokat olvas az Azure IoT Hub az Azure Time Series Insights-környezethez eseményforrás hozzáadása az Azure portal használatával.

> [!NOTE]
> A jelen cikkben lévő utasítások az Azure Time Series Insights általánosan elérhető és a Time Series Insights előzetes környezetekben egyaránt vonatkoznak.

## <a name="prerequisites"></a>Előfeltételek

* Hozzon létre egy [Azure Time Series Insights-környezet](time-series-insights-update-create-environment.md).
* Hozzon létre egy [az IoT hub által az Azure portal használatával](../iot-hub/iot-hub-create-through-portal.md).
* Az IoT hub küld a rendszer aktív üzenetek esemény kell rendelkeznie.
* Hozzon létre egy dedikált fogyasztói csoportot a Time Series Insights környezethez való felhasználásához az IoT hubban. Minden egyes Time Series Insights-eseményforrás rendelkeznie kell a saját dedikált fogyasztói csoportot, amely bármely más fogyasztók nincs megosztva. Ha több olvasók ugyanabban a fogyasztói csoportban lévő események felhasználásához, minden olvasók valószínűleg hibák. További információkért lásd: a [Azure IoT Hub fejlesztői útmutatójának](../iot-hub/iot-hub-devguide.md).

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Az IoT hub fogyasztói csoport hozzáadása

Alkalmazások fogyasztói csoportok segítségével szerez adatokat használja az Azure IoT Hubból. Megbízhatóan adatokat olvasni az IoT hubhoz, adja meg, amely csak a Time Series Insights-környezet által használt dedikált fogyasztói csoportot.

Az IoT hub egy új felhasználói csoport hozzáadása:

1. Az Azure Portalon keresse meg és nyissa meg az IoT hubnak.

1. Alatt **beállítások**válassza **beépített végpontokról**, majd válassza ki a **események** végpont.

   [![A beépített végpontok lapon válassza ki az események gomb](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_one.png)](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_one.png#lightbox)

1. A **fogyasztói csoportok**, adjon meg egy egyedi nevet a fogyasztói csoportot. Használja ezt a nevet a Time Series Insights-környezetben, ha egy új eseményforrás létrehozása.

1. Kattintson a **Mentés** gombra.

## <a name="add-a-new-event-source"></a>Adjon hozzá egy új esemény forrása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali menüben válassza ki a **Minden erőforrás** elemet. Válassza ki az Azure Time Series Insights-környezetet.

1. Alatt **környezeti topológia**válassza **eseményforrások**, majd válassza ki **Hozzáadás**.

   [![Válassza ki a eseményforrások, és válassza a Hozzáadás gombra.](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_two.png)](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_two.png#lightbox)

1. Az a **új eseményforrás** panelen a **eseményforrás nevének**, adja meg a Time Series Insights-környezet egyedi nevét. Adja meg például **esemény-adatfolyam**.

1. A **forrás**válassza **az IoT Hub**.

1. Adja meg a **importálási beállítás**:

   * Ha már rendelkezik egy IoT hubot az egyik előfizetésének átadását, válassza ki a **használja az IoT Hub a rendelkezésre álló előfizetések**. Ez a lehetőség akkor a legegyszerűbb megközelítés.
   
     [![Válassza ki a beállításokat az új esemény forrás panelen](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_three.png)](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_three.png#lightbox)

    * A következő táblázat ismerteti a tulajdonságok, amelyek szükségesek a **használja az IoT Hub a rendelkezésre álló előfizetések** lehetőséget:

       [![Új esemény adatforrás ablaktábla - tulajdonságok beállítása IoT Hub használata a rendelkezésre álló előfizetések közül](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_four.png)](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_four.png#lightbox)

       | Tulajdonság | Leírás |
       | --- | --- |
       | Előfizetés azonosítója | Válassza ki az előfizetést, amelyben az IoT hub létrehozása.
       | IoT hub nevét | Válassza ki az IoT hub nevét.
       | IoT hub házirend neve | Válassza ki a megosztott elérési házirendet. A megosztott elérési házirendet az IoT hub beállításai lapon találja. Minden megosztott elérési házirend neve, hogy Ön meghatározott engedélyekkel és hozzáférési kulcsok van. A megosztott hozzáférési szabályzatot az eseményforrás *kell* rendelkezik **szolgáltatás csatlakozása** engedélyeket.
       | IoT hub házirendjének kulcsa | A kulcs van töltve.
       | IoT hub fogyasztói csoport | A fogyasztói csoportot, amely eseményeket olvas az IoT hub. Kifejezetten ajánljuk, hogy használjon dedikált fogyasztói csoportot az eseményforrást.
       | Eseményszerializációs formátum | JSON-ban jelenleg csak akkor érhető el szerializálási formátum. Az eseményt üzenetek a következő formátumban kell lennie, vagy az adatok nem olvashatók. |
       | Időbélyegző-tulajdonság neve | Az érték meghatározásához kell megérteni az állapotüzenet-adatokat az IoT hubnak küldött üzenet formátumát. Ez az érték a **neve** az állapotüzenet-adatokat használja, mint az esemény időbélyegzője kívánt az adott esemény tulajdonság. Az érték a kis-és nagybetűket. Ha üresen hagyja, a **eseményt sorba idő** az a Eseménynapló timestamp forrás szolgál. |

    * Ha az IoT hub külső az előfizetésekhez, vagy ha azt szeretné, válassza ki a speciális beállításokat **adja meg az IoT Hub beállításainak manuális**.

      A következő táblázat ismerteti a szükséges tulajdonságokat a **adja meg az IoT Hub beállításainak manuális**:

       | Tulajdonság | Leírás |
       | --- | --- |
       | Előfizetés azonosítója | Az előfizetés, amelyben az IoT hub létrehozása történt.
       | Erőforráscsoport | Az IoT hub létrehozása erőforráscsoport nevét.
       | IoT hub nevét | az IoT hub nevére. Az IoT hub létrehozásakor adott meg az IoT hub nevét.
       | IoT hub házirend neve | A megosztott elérési házirendet. A megosztott elérési házirend létrehozhat az IoT hub beállításai lapon. Minden megosztott elérési házirend neve, hogy Ön meghatározott engedélyekkel és hozzáférési kulcsok van. A megosztott hozzáférési szabályzatot az eseményforrás *kell* rendelkezik **szolgáltatás csatlakozása** engedélyeket.
       | IoT hub házirendjének kulcsa | A közös hozzáférési kulcs, amellyel hitelesíti a hozzáférést az Azure Service Bus-névteret. Itt adhatja meg az elsődleges vagy másodlagos kulcsot.
       | IoT hub fogyasztói csoport | A fogyasztói csoportot, amely eseményeket olvas az IoT hub. Kifejezetten ajánljuk, hogy használjon dedikált fogyasztói csoportot az eseményforrást.
       | Eseményszerializációs formátum | JSON-ban jelenleg csak akkor érhető el szerializálási formátum. Az eseményt üzenetek a következő formátumban kell lennie, vagy az adatok nem olvashatók. |
       | Időbélyegző-tulajdonság neve | Az érték meghatározásához kell megérteni az állapotüzenet-adatokat az IoT hubnak küldött üzenet formátumát. Ez az érték a **neve** az állapotüzenet-adatokat használja, mint az esemény időbélyegzője kívánt az adott esemény tulajdonság. Az érték a kis-és nagybetűket. Ha üresen hagyja, a **eseményt sorba idő** az a Eseménynapló timestamp forrás szolgál. |

1. Adja hozzá a dedikált Time Series Insights fogyasztói csoport neve az IoT hub korábban hozzáadott.

1. Kattintson a **Létrehozás** gombra.

   [![A Létrehozás gombra](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_five.png)](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_five.png#lightbox)

1. Miután létrehozta az eseményforrás, a Time Series Insights automatikusan elindul, streamelési adatok a környezetben.

## <a name="next-steps"></a>További lépések

* [Adathozzáférési házirendek meghatározása](time-series-insights-data-access.md) védi az adatait.

* [Események küldése](time-series-insights-send-events.md) esemény forrását.

* A környezet eléréséhez a [Time Series Insights explorer](https://insights.timeseries.azure.com).

---
title: Az Azure IoT Hub bemutatása | Microsoft Docs
description: Tudnivalók az Azure IoT Hubról. Ez az IoT-szolgáltatás skálázható adatfeldolgozáshoz, eszközkezeléshez és biztonsághoz lett tervezve.
author: nberdy
ms.author: nberdy
ms.date: 08/08/2019
ms.topic: overview
ms.custom:
- mvc
- amqp
- mqtt
- 'role: Direction'
- 'role: System Architecture'
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 86a373844b370cc9f9ce31dc65b2039a81279803
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "102454770"
---
# <a name="what-is-azure-iot-hub"></a>Mi az Azure IoT Hub?

Az IoT Hub egy olyan felügyelt szolgáltatás, amely a felhőn fut, és amely az IoT-alkalmazás és az általa felügyelt eszközök közötti kétirányú kommunikáció üzenetközpontjaként működik. Az Azure IoT Hub segítségével megbízható és biztonságos kommunikációt használó IoT-megoldásokat készíthet több millió IoT-eszköz és egy felhőalapú háttérmegoldás között. Gyakorlatilag bármilyen eszközt csatlakoztathat az IoT Hubhoz.

Az IoT Hub az eszközről a felhőbe és a felhőről az eszközre irányuló kommunikációt is támogatja. Az IoT Hub több üzenetkezelési mintát támogat, például az eszközről a felhőbe irányuló telemetriát, a fájlfeltöltést az eszközökről, valamint a kérés-válasz módszereket, amelyekkel a felhőről vezérelhetők az eszközök. Az IoT Hub monitorozása az események (például az eszköz létrehozása, az eszköz meghibásodásai és az eszközkapcsolatok) követésével segít fenntartani a megoldás épségét.

Az IoT Hub képességei segítenek a skálázható, teljes körű IoT-megoldások felépítésében, amelyek például a gyártásban használt ipari berendezések kezelésére, értékes egészségügyi eszközök követésére és irodaépületek használatának monitorozására szolgálnak.

## <a name="scale-your-solution"></a>A megoldás méretezése

Az IoT Hub több millió egyszerre csatlakoztatott eszközre és másodpercenként több millió eseményre skálázható az IoT számítási feladatok támogatása érdekében. További információ a IoT Hub skálázásáról: [IoT hub skálázás](iot-hub-scaling.md). Ha többet szeretne megtudni a IoT Hub által kínált több rétegről, valamint arról, hogy miként lehet a legjobban megfelelni a méretezhetőségi igényeknek, tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="secure-your-communications"></a>Biztonságos kommunikáció

Az IoT Hub biztonságos kommunikációs csatornát nyújt az eszközöknek az adatküldéshez.

* Az eszközönkénti hitelesítés lehetővé teszi az összes eszköz biztonságos kezelését és az IoT Hubhoz való biztonságos kapcsolódását.

* Teljeskörűen irányíthatja az eszközök hozzáférését, és eszközönként szabályozhatja a kapcsolatokat.

* Az [IoT Hub Device Provisioning szolgáltatás](../iot-dps/index.yml) automatikusan a megfelelő IoT Hubhoz irányítja az eszközöket, amikor először indulnak el.

* Több hitelesítéstípus különböző eszközképességeket támogat:

  * SAS-jogkivonat alapú hitelesítés az IoT megoldás használatának gyors megkezdéséhez.

  * Egyéni X.509-tanúsítványhitelesítés a biztonságos, szabványokon alapuló hitelesítés érdekében.

  * X.509-hitelesítésszolgáltatói hitelesítés az egyszerű, szabványalapú regisztráció érdekében.

## <a name="route-device-data"></a>Eszközadatok útválasztása

A beépített üzenet-útválasztási funkció rugalmasságot biztosít az automatikus, szabályalapú üzenetelosztás beállításához:

* Az [üzenet-útválasztás](iot-hub-devguide-messages-d2c.md) használatával szabályozhatja, hogy a hub hová küldi az eszköz telemetria.

* Az üzenetek több végpontra való irányítása nem jár többletköltségekkel.

* Az egyéni üzenetátadói kód helyett a rendszer kód nélküli útválasztási szabályokat használ.

## <a name="integrate-with-other-services"></a>Integráció más szolgáltatásokkal

Az IoT Hub integrálható más Azure-szolgáltatásokkal teljes, végpontok közötti megoldások felépítése érdekében. Például használhatja a következőket:

* Az [Azure Event Grid](../event-grid/index.yml) lehetővé teszi, hogy gyorsan, megbízhatóan, biztonságosan és skálázható módon reagáljon a kritikus eseményekre.

* Az [Azure Logic Apps](../logic-apps/index.yml) az üzleti folyamatok automatizálására használható.

* Az [Azure Machine Learninggel](iot-hub-weather-forecast-machine-learning.md) gépi tanulást és mesterségesintelligencia-modelleket adhat a megoldáshoz.

* Az [Azure Stream Analytics](../stream-analytics/index.yml) használatával valós idejű elemző számításokat futtathat az adatstreameken az eszközökről.

## <a name="configure-and-control-your-devices"></a>Az eszközök konfigurálása és vezérlése

Az IoT Hubhoz csatlakoztatott eszközöket különböző beépített funkciókkal kezelheti.

* Tárolhatja, szinkronizálhatja és lekérdezheti a minden eszköz metaadatait és állapotinformációit.

* Az eszközök állapotát eszközönként vagy az eszközök közös jellemzői alapján állíthatja be.

* Automatikusan válaszolhat az eszközök által jelentett állapotváltozásokra az üzenet-útválasztás integrációjával.

## <a name="make-your-solution-highly-available"></a>Biztosíthatja a megoldás magas rendelkezésre állását

Az [IoT Hubhoz](https://azure.microsoft.com/support/legal/sla/iot-hub/) 99,9%-os szolgáltatói szerződés érhető el. Az Azure egészére vonatkozó rendelkezésre állási garancia magyarázata a teljes [Azure SLA](https://azure.microsoft.com/support/legal/sla/)-ban található.

## <a name="connect-your-devices"></a>Az eszközök csatlakoztatása

Az [Azure IoT eszközoldali SDK](./iot-hub-devguide-sdks.md)-kódtárakkal olyan alkalmazásokat készíthet, amelyek az eszközökön futnak, és az IoT Hubbal kommunikálnak. A támogatott platformok közé tartozik több Linux-disztribúció, Windows és valós idejű operációs rendszer. A támogatott nyelvek közé tartoznak a következők:

* C
* Beágyazott C
* C#
* Java
* Python
* Node.js.

Az IoT Hub és az eszközoldali SDK-k a következő protokollokat támogatják az eszközök csatlakoztatásához:

* HTTPS
* AMQP
* AMQP WebSocketen keresztül
* MQTT
* MQTT WebSocketen keresztül

A IoT Hub és az eszköz SDK-k támogatják az [Azure IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md) az eszközök csatlakoztatására vonatkozó konvenciókat. A IoT Plug and Play-eszközök egy eszköz modell használatával hirdetik meg a képességeiket a Plug and Play-kompatibilis alkalmazások IoT. Az eszköz modellje lehetővé teszi a megoldás-építők számára, hogy manuális konfiguráció nélkül integrálják az intelligens eszközöket a megoldásaikkal.

Ha a megoldása nem tudja használni az eszközkódtárakat, az eszközök natív módon csatlakozhatnak a központhoz az MQTT v3.1.1, HTTPS 1.1 vagy AMQP 1.0 protokollokkal.

Ha a megoldás nem tudja használni egyik támogatott protokollt sem, kibővítheti az IoT Hubot az egyéni protokollok támogatásához:

* Az [Azure IoT Edge](../iot-edge/index.yml) használatával hozzon létre egy helyi átjárót a peremhálózaton végzett protokollfordításhoz.

* Szabja testre az [Azure IoT-protokollátjárót](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md), hogy protokollfordítást hajtson végre a felhőben.

## <a name="quotas-and-limits"></a>Kvóták és korlátok

Minden Azure-előfizetésre alapértelmezett kvótakorlátozások vonatkoznak, amelyek meggátolják a szolgáltatással való visszaélést, és ezek a korlátozások hatással lehetnek az IoT-megoldás hatókörére. Az előfizetések jelenlegi korlátja a 50 IoT hub/előfizetés. A támogatási részlegtől kérheti a kvóta növelését. További információ: [IoT hub kvóták és szabályozás](iot-hub-devguide-quotas-throttling.md). A kvóta korlátaival kapcsolatos további részletekért tekintse meg a következő cikkek egyikét:

* [Azure-előfizetési szolgáltatás korlátai](../azure-resource-manager/management/azure-subscription-service-limits.md)

* [Az IoT Hub szabályozása és Ön](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/)

## <a name="iot-hub-on-azure-stack-hub-preview"></a>IoT Hub az Azure Stack Event Hubon (előzetes verzió)

A IoT Hub on Azure Stack hub (előzetes verzió) lehetővé teszi hibrid IoT-megoldások létrehozását. A IoT Hub felügyelt szolgáltatás, amely központi üzenetsorként működik a IoT-alkalmazás és az általa kezelt eszközök közötti kétirányú kommunikációhoz. A IoT Hub on Azure Stack hub segítségével IoT-megoldásokat hozhat létre a IoT-eszközök és a helyszíni megoldások közötti megbízható és biztonságos kommunikációval.

A nyilvános előzetes verzióban a IoT Hub on Azure Stack hub ingyenes. További információ: [IoT Hub Azure stack hub – áttekintés](/azure-stack/operator/iot-hub-rp-overview).

## <a name="next-steps"></a>Következő lépések

A végpontok közötti IoT-megoldás kipróbálásához tekintse meg az IoT Hub rövid útmutatóit:

* [Rövid útmutató: Telemetria küldése egy eszközről IoT Hubra](quickstart-send-telemetry-node.md)

Ha többet szeretne megtudni arról, hogyan hozhat létre és helyezhet üzembe IoT-megoldásokat az Azure IoT, látogasson el ide:

* [Alapismeretek: az Azure IoT-technológiák és-megoldások](../iot-fundamentals/iot-services-and-technologies.md).
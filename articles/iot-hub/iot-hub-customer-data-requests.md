---
title: Az Azure IoT Hub-eszközökre vonatkozó ügyféladatok-kérelmek
description: Az Azure IoT Hub által felügyelt eszközök többsége nem személyes, néhány pedig. Ez a cikk azt ismerteti, hogy a rendszergazdák hogyan tudják exportálni vagy törölni a személyes adatait egy eszközről.
author: robinsh
ms.author: robinsh
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: df1d4f3a12c6e15e2954bb0e02454ef58b22cb73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92535790"
---
# <a name="customer-data-request-features-for-azure-iot-hub-devices"></a>Az Azure IoT Hub-eszközök felhasználói adatkérési funkciói

Az Azure IoT Hub a nagyvállalati ügyfeleknek szánt, REST API-alapú felhőalapú szolgáltatás, amely biztonságos, kétirányú kommunikációt tesz lehetővé több millió eszköz és egy particionált Azure-szolgáltatás között.

[!INCLUDE [gdpr-related guidance](../../includes/gdpr-intro-sentence.md)]

Az egyes eszközökhöz a bérlői rendszergazda rendeli az eszköz azonosítóját (eszköz-AZONOSÍTÓját). Az eszközre vonatkozó adat a hozzárendelt eszköz azonosítója alapján történik. A Microsoft nem tart fenn információt, és nem fér hozzá olyan adatokhoz, amelyek lehetővé tennék az eszköz AZONOSÍTÓjának felhasználói korrelációt.

Az Azure IoT Hubban felügyelt eszközök közül sok nem személyes eszköz, például irodai termosztát vagy gyári robot. Előfordulhat azonban, hogy egyes eszközök személyes azonosításra alkalmasak, és saját belátásuk szerint fenntartják saját eszközeiket vagy leltározási követési módszereiket, amelyek az eszközöket az egyéni felhasználók számára használják. Az Azure IoT Hub kezeli és tárolja az eszközökhöz kapcsolódó összes olyan adatmennyiséget, amely személyes adatként volt.

A bérlői rendszergazdák vagy a Azure Portal vagy a szolgáltatás REST API-jai segítségével teljesítik az adatkéréseket az eszköz-AZONOSÍTÓhoz társított adatok exportálásával vagy törlésével.

Ha az Azure IoT Hub szolgáltatás útválasztási funkciójával továbbítja az üzeneteket más szolgáltatásoknak, akkor az egyes útválasztási végpontok esetén az adatkéréseket a bérlői rendszergazdának kell elvégeznie az adott eszközre vonatkozó teljes kérelem teljesítése érdekében. További részletekért tekintse meg az egyes végpontok hivatkozási dokumentációját. További információ a támogatott végpontokról: [referenciák IoT hub végpontok](iot-hub-devguide-endpoints.md).

Ha az Azure IoT Hub szolgáltatás Azure Event Grid integrációs szolgáltatását használja, akkor az egyes előfizetőknél az adatkéréseket a bérlői rendszergazdának kell elvégrehajtania. További információ: [IoT hub eseményekre való reagálás Event Grid használatával](iot-hub-event-grid.md).

Ha az Azure IoT Hub szolgáltatás Azure Monitor integrációs funkciójával erőforrás-naplókat hoz létre, akkor az adatkéréseket a bérlői rendszergazdának kell végrehajtania a tárolt naplókon. További információ: [IoT hub figyelése](monitor-iot-hub.md).

## <a name="deleting-customer-data"></a>Ügyféladatok törlése

A bérlői rendszergazdák az Azure IoT Hub bővítmény IoT-eszközök paneljén használhatják a Azure Portal egy eszköz törléséhez, amely törli az adott eszközhöz társított adatmennyiséget.

A REST API-kat használó eszközök törlési műveleteit is el lehet végezni. További információ: [szolgáltatás – eszköz törlése](/azure/iot-hub/iot-c-sdk-ref/iothub-registrymanager-h/iothubregistrymanager-deletedevice).

## <a name="exporting-customer-data"></a>Ügyféladatok exportálása

A bérlői rendszergazdák a Azure Portal Azure IoT Hub bővítményének IoT-eszközök paneljén a másolás és beillesztés funkciót használhatják az eszközhöz társított adatexportáláshoz.

A REST API-kat használó eszközök exportálási műveleteit is el lehet végezni. További információ: [szolgáltatás – eszköz beolvasása](/azure/iot-hub/iot-c-sdk-ref/iothub-registrymanager-h/iothubregistrymanager-getdevice).

> [!NOTE]
> A Microsoft nagyvállalati szolgáltatásainak használatakor a Microsoft bizonyos adatokat hoz létre a rendszer által létrehozott naplók néven. Néhány Azure-IoT Hub rendszer által generált napló nem érhető el vagy nem exportálható a bérlői rendszergazdák számára. Ezek a naplók a szolgáltatáson belüli, illetve az egyes eszközökhöz kapcsolódó diagnosztikai adatokból származó ténybeli műveleteket alkotnak.

## <a name="links-to-additional-documentation"></a>További dokumentációra mutató hivatkozások

Az Azure IoT Hub Service API-k teljes dokumentációja [IoT hub Service API](/rest/api/iothub/service/configuration)-kon található.
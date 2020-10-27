---
title: Azure IoT Hub skálázás | Microsoft Docs
description: Az IoT hub méretezése a várt üzenet-átviteli sebesség és a kívánt funkciók támogatásához. Összefoglalja az egyes szintek támogatott átviteli sebességét és a horizontális skálázási lehetőségeket.
author: wesmc7777
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: wesmc
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: Operations'
ms.openlocfilehash: bd016fcfe377dc610d5918ad8128fff4f6473fe5
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2020
ms.locfileid: "92538459"
---
# <a name="choose-the-right-iot-hub-tier-for-your-solution"></a>Egy adott megoldáshoz leginkább megfelelő IoT Hub-szint kiválasztása

Minden IoT-megoldás eltérő, így az Azure IoT Hub számos lehetőséget kínál a díjszabás és a skála alapján. A cikk célja, hogy segítséget nyújtson a IoT Hub igényeinek kiértékelésében. A IoT Hub-csomagokra vonatkozó díjszabással kapcsolatban lásd: [IoT hub díjszabása](https://azure.microsoft.com/pricing/details/iot-hub).

Ha szeretné eldönteni, hogy melyik IoT Hub szint a legmegfelelőbb a megoldásához, kérdezzen két kérdést:

**Milyen funkciókat kívánok használni?**

Az Azure IoT Hub két, alapszintű és standard szintű csomagot kínál, amelyek az általuk támogatott szolgáltatások számától eltérőek. Ha a IoT-megoldás az adatok az eszközökről való gyűjtésén és központilag történő elemzésén alapul, az alapszintű csomag valószínűleg az Ön számára megfelelő. Ha további speciális konfigurációkat szeretne használni a IoT-eszközök távoli vezérléséhez vagy az egyes számítási feladatok saját eszközökre való terjesztéséhez, akkor érdemes megfontolnia a standard szintet. Az egyes szintekbe tartozó funkciók részletes részletezése az alapszintű [és a standard csomagra](#basic-and-standard-tiers)is vonatkozik.

**Mekkora mennyiségű adatáthelyezést tervezek naponta?**

Az egyes IoT Hub szintek három méretben érhetők el, attól függően, hogy mekkora mennyiségű adatátvitelt képesek kezelni egy adott napon belül. Ezeket a méreteket a rendszer numerikusan 1, 2 és 3 értékként azonosítja. Például az 1. szintű IoT hub minden egysége naponta 400 000 üzenetet képes kezelni, míg a 3. szint egység képes a 300 000 000 kezelésére. Az adatirányelvekkel kapcsolatos további információkért folytassa az [üzenet átviteli sebességét](#message-throughput).

## <a name="basic-and-standard-tiers"></a>Alapszintű és standard csomag

A IoT Hub standard szintje lehetővé teszi az összes funkció használatát, és minden olyan IoT-megoldáshoz szükséges, amely a kétirányú kommunikációs képességeket kívánja kihasználni. Az alapszint a funkciók egy részhalmazát engedélyezi, és olyan IoT-megoldásokhoz való, amelyek csak egyirányú kommunikációt igényelnek az eszközöktől a felhő felé. Mindkét szint ugyanazokat a biztonsági és hitelesítési funkciókat kínálja.

Egy adott rétegen belül csak egy [kiadást](https://azure.microsoft.com/pricing/details/iot-hub/) lehet kiválasztani IoT hub. Létrehozhat például egy, az S1 több egységgel rendelkező IoT Hub, de különböző kiadásokból (például S1 és S2) álló egységeket is.

| Képesség | Alapszint | Ingyenes/standard szint |
| ---------- | ---------- | ------------- |
| [Eszközről a felhőbe irányuló telemetria](iot-hub-devguide-messaging.md) | Igen | Igen |
| [Eszközönkénti azonosító](iot-hub-devguide-identity-registry.md) | Igen | Igen |
| [Üzenetek útválasztása](iot-hub-devguide-messages-read-custom.md), [üzenetek gazdagítása](iot-hub-message-enrichments-overview.md)és [Event Grid integráció](iot-hub-event-grid.md) | Igen | Igen |
| [HTTP-, AMQP-és MQTT-protokollok](iot-hub-devguide-protocols.md) | Igen | Igen |
| [Device Provisioning Service](../iot-dps/about-iot-dps.md) | Igen | Igen |
| [Monitorozás és diagnosztika](monitor-iot-hub.md) | Igen | Igen |
| [A felhőből az eszközre irányuló üzenetküldés](iot-hub-devguide-c2d-guidance.md) |   | Igen |
| [Eszközök](iot-hub-devguide-device-twins.md), ikrek, [modulok](iot-hub-devguide-module-twins.md) [és eszközkezelés](iot-hub-device-management-overview.md) |   | Igen |
| [Eszköz streamek (előzetes verzió)](iot-hub-device-streams-overview.md) |   | Igen |
| [Azure IoT Edge](../iot-edge/about-iot-edge.md) |   | Igen |
| [IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md) |   | Igen |

A IoT Hub ingyenes szintet is biztosít teszteléshez és értékeléshez. Rendelkezik a standard csomag összes képességével, de korlátozott üzenetküldési támogatással. Az ingyenes szintről nem lehet alapszintű vagy standard szintűre frissíteni.

## <a name="partitions"></a>Partíciók

Az Azure IoT-hubok az [azure Event Hubs](../event-hubs/event-hubs-features.md)számos alapvető összetevőjét tartalmazzák, beleértve a [partíciókat](../event-hubs/event-hubs-features.md#partitions)is. A IoT hubok esetében általában a különböző IoT-eszközök által jelentett bejövő telemetria adatokkal vannak feltöltve. Az esemény-adatfolyam particionálásával csökkenthető az esemény-adatfolyamok egyidejű olvasásakor és írásakor felmerülő események.

A partíciós korlát akkor van kiválasztva, ha IoT Hub jön létre, és nem módosítható. Az alapszintű csomag IoT Hub és a standard szintű IoT Hub maximális partíciós korlátja 32. A legtöbb IoT-hubhoz csak 4 partíció szükséges. A partíciók meghatározásával kapcsolatos további információkért tekintse meg a Event Hubs GYIK, [hogy hány partícióra van szükségem?](../event-hubs/event-hubs-faq.md#how-many-partitions-do-i-need)

## <a name="tier-upgrade"></a>Rétegek frissítése

Az IoT hub létrehozása után az alapszintű csomagról a standard szintre válthat a meglévő műveletek megszakítása nélkül. További információ: az [IoT hub frissítése](iot-hub-upgrade.md).

A partíció konfigurációja változatlan marad az alapszintről a standard szintű csomagra való Migrálás során.

> [!NOTE]
> Az ingyenes szint nem támogatja az alapszintű vagy a standard verzióra való frissítést.

## <a name="iot-hub-rest-apis"></a>IoT Hub REST API-k

Az alapszintű és a standard szintű IoT Hub által támogatott képességek különbsége azt jelenti, hogy egyes API-hívások nem működnek az alapszintű csomópontokkal. A következő táblázat bemutatja, hogy mely API-k érhetők el:

| API | Alapszint | Ingyenes/standard szint |
| --- | ---------- | ------------- |
| [Eszköz törlése](/javascript/api/azure-iot-digitaltwins-service/registrymanager?view=azure-node-latest#deletedevice-string--models-registrymanagerdeletedeviceoptionalparams-) | Igen | Igen |
| [Eszköz beolvasása](/azure/iot-hub/iot-c-sdk-ref/iothub-registrymanager-h/iothubregistrymanager-getdevice) | Igen | Igen |
| [Modul törlése](/azure/iot-hub/iot-c-sdk-ref/iothub-registrymanager-h/iothubregistrymanager-deletemodule) | Igen | Igen |
| [Modul beolvasása](/java/api/com.microsoft.azure.sdk.iot.service.registrymanager.getmodule?view=azure-java-stable) | Igen | Igen |
| [Regisztrációs statisztika beolvasása](/javascript/api/azure-iot-digitaltwins-service/registrymanager?view=azure-node-latest#getdevicestatistics-msrest-requestoptionsbase-) | Igen | Igen |
| [Szolgáltatások statisztikáinak beolvasása](/javascript/api/azure-iot-digitaltwins-service/registrymanager?view=azure-node-latest#getservicestatistics-msrest-requestoptionsbase-) | Igen | Igen |
| [Eszköz létrehozása vagy frissítése](/javascript/api/azure-iot-digitaltwins-service/registrymanager?view=azure-node-latest#createorupdatedevice-string--device--servicecallback-device--) | Igen | Igen |
| [Modul létrehozása vagy frissítése](/javascript/api/azure-iot-digitaltwins-service/registrymanager?view=azure-node-latest#createorupdatemodule-string--string--module--models-registrymanagercreateorupdatemoduleoptionalparams-) | Igen | Igen |
| [Lekérdezés IoT Hub](/dotnet/api/microsoft.azure.devices.registrymanager?view=azure-dotnet) | Igen | Igen |
| [Fájlfeltöltés SAS URI-ja létrehozása](/rest/api/iothub/device/createfileuploadsasuri) | Igen | Igen |
| [Eszköz kötött értesítésének fogadása](/rest/api/iothub/device/receivedeviceboundnotification) | Igen | Igen |
| [Eszköz eseményének küldése](/rest/api/iothub/device/senddeviceevent) | Igen | Igen |
| Modul küldése esemény | Csak AMQP és MQTT | Csak AMQP és MQTT |
| [Fájlfeltöltés állapotának frissítése](/rest/api/iothub/device/updatefileuploadstatus) | Igen | Igen |
| [Tömeges eszköz művelete](/javascript/api/azure-iot-digitaltwins-service/registrymanager?view=azure-node-latest#bulkdevicecrud-exportimportdevice----msrest-requestoptionsbase-) | Igen, a IoT Edge képességek kivételével | Igen |
| [Importálási exportálási feladat megszakítása](/rest/api/iothub/service/jobs/cancelimportexportjob) | Igen | Igen |
| [Importálási exportálási feladatok létrehozása](/rest/api/iothub/service/jobs/createimportexportjob) | Igen | Igen |
| [Importálási exportálási feladatok beolvasása](/rest/api/iothub/service/jobs/getimportexportjob) | Igen | Igen |
| [Importálási exportálási feladatok beolvasása](/rest/api/iothub/service/jobs/getimportexportjobs) | Igen | Igen |
| [Parancssori várólista kiürítése](/javascript/api/azure-iot-digitaltwins-service/registrymanager?view=azure-node-latest#purgecommandqueue-string--msrest-requestoptionsbase-) |   | Igen |
| [Eszköz dupla beolvasása](/java/api/com.microsoft.azure.sdk.iot.device.deviceclient.getdevicetwin?view=azure-java-stable) |   | Igen |
| [Dupla modul beolvasása](/azure/iot-hub/iot-c-sdk-ref/iothub-devicetwin-h/iothubdevicetwin-getmoduletwin) |   | Igen |
| [Eszköz metódusának meghívása](./iot-hub-devguide-direct-methods.md) |   | Igen |
| [Eszköz dupla frissítése](./iot-hub-devguide-device-twins.md) |   | Igen |
| [Modul frissítése – Twin](/azure/iot-hub/iot-c-sdk-ref/iothub-devicetwin-h/iothubdevicetwin-updatemoduletwin) |   | Igen |
| [Eszköz kötött értesítésének elhagyása](/rest/api/iothub/device/abandondeviceboundnotification) |   | Igen |
| [Eszköz kötött értesítésének befejezése](/rest/api/iothub/device/completedeviceboundnotification) |   | Igen |
| [Feladat megszakítása](/rest/api/media/jobs/canceljob) |   | Igen |
| [Feladat létrehozása](/rest/api/media/jobs/create) |   | Igen |
| [Feladatok beolvasása](/java/api/com.microsoft.azure.sdk.iot.service.jobs.jobclient.getjob?view=azure-java-stable) |   | Igen |
| [Lekérdezési feladatok](/javascript/api/azure-iot-digitaltwins-service/jobclient?view=azure-node-latest#queryjobs-jobclientqueryjobsoptionalparams--servicecallback-queryresult--) |   | Igen |

## <a name="message-throughput"></a>Üzenet átviteli sebessége

IoT Hub megoldás méretezésének legjobb módja a forgalom kiértékelése egységnyi alapon. Különösen vegye figyelembe a következő típusú műveletek szükséges maximális átviteli sebességét:

* Az eszközről a felhőbe irányuló üzenetek
* Felhőből az eszközre irányuló üzenetek
* Identitásjegyzék műveletei

Az IoT hub forgalmát egységenként kell mérni. Az IoT hub létrehozásakor kiválaszthatja annak szintjét és kiadását, és beállíthatja a rendelkezésre álló egységek számát. Akár 200 egységet is megvásárolhat a B1, a B2, az S1 vagy az S2 kiadáshoz, vagy akár 10 egységet a B3 vagy az S3 kiadáshoz. Az IoT hub létrehozása után megváltoztathatja a kiadáson belül elérhető egységek számát, a szintet (B1 – B2) belüli kiadások közötti frissítést vagy visszalépést, vagy az alapszintről a standard szintre (B1 – S1) történő frissítést anélkül, hogy megszakítja a meglévő műveleteket. További információ: az [IoT hub frissítése](iot-hub-upgrade.md).  

Az egyes szintek forgalmának, az eszközről a felhőbe irányuló üzenetek esetében például a következő fenntartható átviteli sebességre vonatkozó irányelvek láthatók:

| Szintű kiadás | Tartós átviteli sebesség | Tartós küldési arány |
| --- | --- | --- |
| B1, S1 |Akár 1111 KB/perc egységenként<br/>(1,5 GB/nap/egység) |Átlagosan 278 üzenet/perc egységenként<br/>(400 000 üzenet/nap egységenként) |
| B2, S2 |Akár 16 MB/perc egységenként<br/>(22,8 GB/nap/egység) |Átlagosan 4 167 üzenet/perc egységenként<br/>(6 000 000 üzenet/nap egységenként) |
| B3, S3 |Akár 814 MB/perc egységenként<br/>(1144,4 GB/nap/egység) |Átlagosan 208 333 üzenet/perc egységenként<br/>(300 000 000 üzenet/nap egységenként) |

Az eszközről a felhőbe irányuló átviteli sebesség csak az egyik mérőszám, amelyet figyelembe kell vennie egy IoT-megoldás tervezésekor. További információk: [IoT hub kvóták és szabályozások](iot-hub-devguide-quotas-throttling.md).

### <a name="identity-registry-operation-throughput"></a>Azonosító beállításjegyzékbeli művelet átviteli sebessége

Az IoT Hub Identity registry-műveletek nem feltételezetten futásidejű műveletek, mivel ezek többnyire az eszközök kiépítés kapcsán állnak kapcsolatban.

Adott burst-teljesítményi számok esetében lásd: [IoT hub kvóták és szabályozások](iot-hub-devguide-quotas-throttling.md).

## <a name="auto-scale"></a>Automatikus méretezés

Ha közeledik az engedélyezett IoT hub-korláthoz, a következő [lépésekkel automatikusan méretezheti](https://azure.microsoft.com/resources/samples/iot-hub-dotnet-autoscale/) az IoT hub egységet ugyanazon a IoT hub szinten.

## <a name="next-steps"></a>Következő lépések

* További információ a IoT Hub képességekről és a teljesítmény részleteiről: [IoT hub díjszabási](https://azure.microsoft.com/pricing/details/iot-hub) vagy [IoT hub kvóták és szabályozások](iot-hub-devguide-quotas-throttling.md).

* Ha módosítani szeretné a IoT Hub szintet, kövesse az [IoT hub frissítésének](iot-hub-upgrade.md)lépéseit.
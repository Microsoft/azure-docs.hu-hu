---
title: Az ügynök és a hub ikermodulok tulajdonságai – Azure IoT Edge
description: Tekintse át az edgeAgent és az edgeHub modul ikermodulok specifikus tulajdonságait és azok értékeit
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/16/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 29ec958764f4a464d51f29f4b9c8223d5d7a1760
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576006"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>A IoT Edge és IoT Edge ikermodulok tulajdonságai

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

A IoT Edge ügynök és IoT Edge két modulból áll, amelyek a IoT Edge teszik ki. További információ az egyes futásidejű modulok felelősségéről: A Azure IoT Edge és [architektúrája.](iot-edge-runtime.md)

Ez a cikk a futásidejű ikermodulok kívánt tulajdonságait és jelentett tulajdonságait biztosítja. További információ a modulok üzembe helyezéséről IoT Edge eszközökre: A modulok üzembe helyezése és útvonalak létrehozása a [IoT Edge.](module-composition.md)

A modulikre a következőket tartalmazza:

* **Kívánt tulajdonságok.** A megoldás háttérmodulja beállíthatja a kívánt tulajdonságokat, és a modul olvashatja őket. A modul értesítéseket is kaphat a kívánt tulajdonságok változásairól. A modul konfigurációjának vagy feltételeinek szinkronizálása a kívánt tulajdonságokkal és jelentett tulajdonságokkal együtt használatos.

* **Jelentett tulajdonságok.** A modul beállíthatja a jelentett tulajdonságokat, és a megoldás háttérmodulja olvashatja és lekérdezheti őket. A jelentett tulajdonságokat és a kívánt tulajdonságokat a modul konfigurációjának vagy feltételeinek szinkronizálása során használja a rendszer.

## <a name="edgeagent-desired-properties"></a>EdgeAgent kívánt tulajdonságok

A rendszer a IoT Edge ügynök modulikonját hívják meg, és koordinálják az eszközön futó IoT Edge `$edgeAgent` és a IoT Hub. A kívánt tulajdonságok akkor vannak beállítva, amikor egy adott eszközre egy adott eszközre vagy nagy léptékű üzembe helyezésre alkalmaznak egy üzembe helyezési jegyzékfájlt.

| Tulajdonság | Leírás | Kötelező |
| -------- | ----------- | -------- |
| schemaVersion (sémaverzió) | "1.0" vagy "1.1". Az 1.1-es verzió az IoT Edge 1.0.10-es verziójával lett bevezetve, és ajánlott. | Yes |
| runtime.type | Dockernek kell lennie | Yes |
| runtime.settings.minDockerVersion | Állítsa be az üzembe helyezési jegyzékhez minimálisan szükséges Docker-verziót | Yes |
| runtime.settings.loggingOptions | Az ügynöktároló naplózási beállításait tartalmazó sztringesített JSON IoT Edge. [A Docker naplózási beállításai](https://docs.docker.com/engine/admin/logging/overview/) | No |
| runtime.settings.registryCredentials<br>. {registryId}.username | A tároló-beállításjegyzék felhasználóneve. A Azure Container Registry a felhasználónév általában a beállításjegyzék neve.<br><br> A beállításjegyzékbeli hitelesítő adatokra minden privát modul rendszerképéhez szükség van. | No |
| runtime.settings.registryCredentials<br>. {registryId}.password | A tároló-beállításjegyzék jelszava. | No |
| runtime.settings.registryCredentials<br>. {registryId}.address | A tároló-beállításjegyzék címe. A Azure Container Registry a cím általában *{beállításjegyzék neve}.azurecr.io.* | No |  
| systemModules.edgeAgent.type | Dockernek kell lennie | Yes |
| systemModules.edgeAgent.settings.image | Az IoT Edge-ügynök képének URI-ját. A IoT Edge ügynök jelenleg nem tudja frissíteni magát. | Yes |
| systemModules.edgeAgent.settings<br>.createOptions | Egy sztringesített JSON, amely az ügynöktároló létrehozásának IoT Edge lehetőségeket tartalmazza. [A Docker létrehozási beállításai](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| systemModules.edgeAgent.configuration.id | A modult üzembe helyező üzemelő példány azonosítója. | IoT Hub beállítja ezt a tulajdonságot, amikor a jegyzékfájlt üzembe helyezéssel alkalmazzák. Nem része egy üzembe helyezési jegyzéknek. |
| systemModules.edgeHub.type | Dockernek kell lennie | Yes |
| systemModules.edgeHub.status | "Fut" kell, hogy legyen | Yes |
| systemModules.edgeHub.restartPolicy | Mindig "always" kell lennie | Yes |
| systemModules.edgeHub.startupOrder | Egész szám, amelynek a spot értéke a modul indítási sorrendjében van. A 0 az első, a maximális egész szám (4294967295) pedig az utolsó. Ha nincs megjelölve érték, az alapértelmezett érték a maximális egész szám.  | No |
| systemModules.edgeHub.settings.image | Az IoT Edge uri-ját. | Yes |
| systemModules.edgeHub.settings<br>.createOptions | Egy sztringesített JSON, amely a központtároló létrehozásának IoT Edge lehetőségeket tartalmazza. [A Docker létrehozási beállításai](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| systemModules.edgeHub.configuration.id | A modult üzembe helyező üzemelő példány azonosítója. | IoT Hub beállítja ezt a tulajdonságot, amikor a jegyzékfájlt üzembe helyezéssel alkalmazzák. Nem része egy üzembe helyezési jegyzéknek. |
| Modulok. {moduleId}.version | A modul verzióját képviselő, felhasználó által definiált sztring. | Yes |
| Modulok. {moduleId}.type | Dockernek kell lennie | Yes |
| Modulok. {moduleId}.status | {"running" \| "leállítva"} | Yes |
| Modulok. {moduleId}.restartPolicy | {"never" \| "on-failure" \| "on-un-healthy" \| "always"} | Yes |
| Modulok. {moduleId}.startupOrder | Egész szám, amelynek a spot értéke a modul indítási sorrendjében van. A 0 az első, a maximális egész szám (4294967295) pedig az utolsó. Ha nincs megjelölve érték, az alapértelmezett érték a maximális egész szám.  | No |
| Modulok. {moduleId}.imagePullPolicy | {"on-create" \| "never"} | No |
| Modulok. {moduleId}.env | A modulnak átadható környezeti változók listája. A formátumot veszi át `"<name>": {"value": "<value>"}` | No |
| Modulok. {moduleId}.settings.image | A modul rendszerképének URI-ját. | Yes |
| Modulok. {moduleId}.settings.createOptions | A modultároló létrehozásának beállításait tartalmazó sztringesített JSON. [A Docker létrehozási beállításai](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| Modulok. {moduleId}.configuration.id | A modult üzembe helyező üzemelő példány azonosítója. | IoT Hub beállítja ezt a tulajdonságot, amikor a jegyzékfájlt üzembe helyezéssel alkalmazzák. Nem része egy üzembe helyezési jegyzéknek. |

## <a name="edgeagent-reported-properties"></a>EdgeAgent jelentett tulajdonságok

A IoT Edge jelentett tulajdonságok három fő információt tartalmaznak:

1. Az utoljára látható kívánt tulajdonságok alkalmazásának állapota;
2. Az eszközön jelenleg futó modulok állapota a IoT Edge szerint; És
3. Az eszközön jelenleg futó kívánt tulajdonságok másolata.

Az aktuálisan kívánt tulajdonságok másolata hasznos annak eldöntése érdekében, hogy az eszköz a legújabb üzembe helyezést alkalmazta-e, vagy még mindig egy korábbi üzembe helyezési jegyzék fut-e.

> [!NOTE]
> A IoT Edge ügynök jelentett tulajdonságai hasznosak, mivel lekérdezhetőek a [](../iot-hub/iot-hub-devguide-query-language.md) IoT Hub lekérdezési nyelvvel a nagy léptékű központi telepítések állapotának vizsgálatára. További információ a IoT Edge ügynök tulajdonságainak állapotra való használatával kapcsolatban: [Az](module-deployment-monitoring.md)IoT Edge telepítései és nagy méretekben való használata.

A következő táblázat nem tartalmazza a kívánt tulajdonságokból másolt információkat.

| Tulajdonság | Leírás |
| -------- | ----------- |
| lastDesiredVersion (lastDesiredVersion) | Ez az egész szám az ügynök által feldolgozott kívánt tulajdonságok utolsó verziójára IoT Edge vonatkozik. |
| lastDesiredStatus.code | Ez az állapotkód a IoT Edge utolsó kívánt tulajdonságát jelenti. Megengedett értékek: `200` Sikeres, `400` Érvénytelen konfiguráció, `412` Érvénytelen sémaverzió, `417` a kívánt tulajdonságok üresek, `500` Sikertelen |
| lastDesiredStatus.description | Az állapot szöveges leírása |
| configurationHealth (konfiguráció megfelelő). {deploymentId}.health | `healthy` ha a(z) {deploymentId} üzemelő példány által beállított összes modul futásidejű állapota `running` vagy `stopped` , `unhealthy` egyébként |
| runtime.platform.OS | Az eszközön futó operációs rendszer jelentése |
| runtime.platform.architecture | Az eszközön található CPU architektúrája jelentéskészítése |
| systemModules.edgeAgent.runtimeStatus | A következő ügynök jelentett IoT Edge: {"running" \| "unhealthy"} |
| systemModules.edgeAgent.statusDescription | Az IoT Edge jelentett állapotának szöveges leírása. |
| systemModules.edgeHub.runtimeStatus | A IoT Edge állapota: { "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| systemModules.edgeHub.statusDescription | A központ állapotának szöveges IoT Edge, ha nem megfelelő állapotú. |
| systemModules.edgeHub.exitCode | Az IoT Edge hub-tároló által jelentett kilépési kód, ha a tároló kilép |
| systemModules.edgeHub.startTimeUtc | A IoT Edge utolsó útjára való töltés ideje |
| systemModules.edgeHub.lastExitTimeUtc | A központ IoT Edge kilépésének ideje |
| systemModules.edgeHub.lastRestartTimeUtc | A IoT Edge újraindításának ideje |
| systemModules.edgeHub.restartCount | A modul újraindításának száma az újraindítási szabályzat részeként. |
| Modulok. {moduleId}.runtimeStatus | A modul állapota: { "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| Modulok. {moduleId}.statusDescription | A modul állapotának szöveges leírása, ha nem megfelelő állapotú. |
| Modulok. {moduleId}.exitCode | A modultároló által jelentett kilépési kód, ha a tároló kilép |
| Modulok. {moduleId}.startTimeUtc | A modul utolsó útjára való betöltés ideje |
| Modulok. {moduleId}.lastExitTimeUtc | A modul utolsó kilépésének ideje |
| Modulok. {moduleId}.lastRestartTimeUtc | A modul utolsó újraindításának ideje |
| Modulok. {moduleId}.restartCount | A modul újraindításának száma az újraindítási szabályzat részeként. |

## <a name="edgehub-desired-properties"></a>Az EdgeHub kívánt tulajdonságai

A rendszer a IoT Edge hub ikermodulját hívják meg, és koordinálják az eszközön futó IoT Edge hub és `$edgeHub` a IoT Hub. A kívánt tulajdonságok akkor vannak beállítva, amikor egy adott eszközre egy adott eszközre vagy nagy léptékű üzembe helyezésre alkalmaznak egy üzembe helyezési jegyzékfájlt.

| Tulajdonság | Leírás | Szükséges az üzembe helyezési jegyzékben |
| -------- | ----------- | -------- |
| schemaVersion (sémaverzió) | "1.0" vagy "1.1". Az 1.1-es verzió az IoT Edge 1.0.10-es verziójával lett bevezetve, és ajánlott. | Yes |
| Útvonalak. {routeName} | Egy központútvonalat IoT Edge sztring. További információ: Útvonalak [deklarálása.](module-composition.md#declare-routes) | Az `routes` elem jelen lehet, de üres is. |
| storeAndForwardConfiguration.timeToLiveSecs | Az az idő másodpercben, IoT Edge a központ megőrzi az üzeneteket, ha az útválasztási végpontok le vannak választva, akár IoT Hub, akár helyi modulról van szó. Az érték bármilyen pozitív egész szám lehet. | Yes |

## <a name="edgehub-reported-properties"></a>Az EdgeHub jelentett tulajdonságai

| Tulajdonság | Leírás |
| -------- | ----------- |
| lastDesiredVersion (lastDesiredVersion) | Ez az egész szám a kívánt tulajdonságoknak a központ által feldolgozott utolsó IoT Edge hivatkozik. |
| lastDesiredStatus.code | A központ által látott utolsó kívánt tulajdonságokra hivatkozó állapotkód IoT Edge központ. Megengedett értékek: `200` Sikeres, `400` Érvénytelen konfiguráció, `500` Sikertelen |
| lastDesiredStatus.description | Az állapot szöveges leírása. |
| Ügyfelek. {device or moduleId}.status | Az eszköz vagy modul kapcsolati állapota. Lehetséges értékek {"connected" \| "disconnected"}. Csak a modul identitása lehet leválasztott állapotban. A központhoz csatlakozó IoT Edge eszközök csak akkor jelennek meg, ha csatlakoztatva vannak. |
| Ügyfelek. {device or moduleId}.lastConnectTime | Az eszköz vagy modul utolsó csatlakozásának ideje. |
| Ügyfelek. {device or moduleId}.lastDisconnectTime | Az eszköz vagy modul legutóbbi leválasztása. |

## <a name="next-steps"></a>Következő lépések

Ha meg szeretne ismerkedni ezekkel a tulajdonságokkal üzembe helyezési jegyzékek összeállítása céljából, olvassa el az IoT Edge modulok használatát, konfigurálásának és újrafelhasználásának a [mikéntjéhez szükséges tudnivalókat.](module-composition.md)

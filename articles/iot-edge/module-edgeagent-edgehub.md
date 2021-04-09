---
title: Az ügynök és a hub-modul tulajdonságai – Azure IoT Edge
description: Tekintse át a edgeAgent és a edgeHub-modul ikrek adott tulajdonságait és értékeit
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/31/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 951111b217b7ace3f12676edf6febfa7266094df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103489948"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>A IoT Edge-ügynök és az IoT Edge hub-modulok ikrek tulajdonságai

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

A IoT Edge ügynök és a IoT Edge hub két, a IoT Edge futtatókörnyezetet alkotó modul. Az egyes futásidejű modulok feladataival kapcsolatos további információkért lásd: [a Azure IoT Edge futtatókörnyezet és az architektúrájának megismerése](iot-edge-runtime.md).

Ez a cikk a futtatókörnyezeti modulok kívánt tulajdonságait és az ikrek által jelentett tulajdonságokat tartalmazza. A modulok IoT Edge eszközökön való telepítésével kapcsolatos további információkért lásd: [modulok üzembe helyezése és útvonalak létrehozása IoT Edgeokban](module-composition.md).

A modulok Twin-tartalma:

* **Kívánt tulajdonságok**. A megoldás háttere beállíthatja a kívánt tulajdonságokat, a modul pedig elolvashatja őket. A modul a kívánt tulajdonságok változásairól is fogadhat értesítéseket. A kívánt tulajdonságok a jelentett tulajdonságokkal együtt használhatók a modul konfigurációjának vagy feltételeinek szinkronizálásához.

* **Jelentett tulajdonságok**. A modul beállíthatja a jelentett tulajdonságokat, és a megoldás háttere olvasható és kérdezhető le. A jelentett tulajdonságokat a rendszer a kívánt tulajdonságokkal együtt használja a modul konfigurációjának vagy feltételeinek szinkronizálásához.

## <a name="edgeagent-desired-properties"></a>EdgeAgent kívánt tulajdonságai

A rendszer meghívja a IoT Edge ügynökhöz tartozó modult, `$edgeAgent` és összehangolja az eszközön futó IoT Edge ügynök és IoT hub közötti kommunikációt. A kívánt tulajdonságok akkor jelennek meg, ha egy adott eszközön egy eszköz vagy egy méretezéses telepítés részeként alkalmazza a központi telepítési jegyzéket.

| Tulajdonság | Leírás | Kötelező |
| -------- | ----------- | -------- |
| sémaverzióval | Vagy "1,0" vagy "1,1". Az 1,1-es verzió a IoT Edge 1.0.10-verzióval lett bevezetve, és ajánlott. | Yes |
| futtatókörnyezet. Type | A "Docker"-nek kell lennie | Yes |
| Runtime. Settings. minDockerVersion | Állítsa be az üzembe helyezési jegyzékben szükséges minimális Docker-verziót | Yes |
| Runtime. Settings. loggingOptions | Egy sztringesített JSON, amely a IoT Edge Agent tároló naplózási beállításait tartalmazza. [Docker naplózási beállításai](https://docs.docker.com/engine/admin/logging/overview/) | No |
| Runtime. Settings. registryCredentials<br>. {registryId}. Felhasználónév | A tároló-beállításjegyzék felhasználóneve. Azure Container Registry esetén a Felhasználónév általában a beállításjegyzék neve.<br><br> A beállításjegyzékbeli hitelesítő adatok szükségesek a privát modulok képeihez. | No |
| Runtime. Settings. registryCredentials<br>. {registryId}. jelszó | A tároló beállításjegyzékének jelszava. | No |
| Runtime. Settings. registryCredentials<br>. {registryId}. címe | A tároló beállításjegyzékének címe. Azure Container Registry esetén a címnek általában *{Registry Name}. azurecr. IO nevűnek* kell lennie. | No |  
| systemModules. edgeAgent. Type | A "Docker"-nek kell lennie | Yes |
| systemModules. edgeAgent. Settings. rendszerkép | A IoT Edge ügynök rendszerképének URI-ja. Jelenleg a IoT Edge ügynök nem tudja frissíteni magát. | Yes |
| systemModules. edgeAgent. Settings<br>.createOptions | Egy sztringesített JSON, amely a IoT Edge ügynök tárolójának létrehozására vonatkozó beállításokat tartalmazza. [Docker-létrehozási beállítások](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| systemModules.edgeAgent.configuration.id | A modult üzembe helyező központi telepítés azonosítója. | IoT Hub beállítja ezt a tulajdonságot, ha a jegyzékfájlt központi telepítés használatával alkalmazza a rendszer. Nem része az üzembe helyezési jegyzéknek. |
| systemModules. edgeHub. Type | A "Docker"-nek kell lennie | Yes |
| systemModules. edgeHub. status | "Fut"-nek kell lennie | Yes |
| systemModules.edgeHub.restartPolicy | "Mindig" kell lennie | Yes |
| systemModules.edgeHub.startupOrder | Egész érték, amely esetében az indítási sorrendbe kerülő modul helye. a 0 érték az első és a maximális egész szám (4294967295) utolsó. Ha nincs megadva érték, az alapértelmezett érték a maximális egész szám.  | No |
| systemModules. edgeHub. Settings. rendszerkép | Az IoT Edge hub rendszerképének URI-ja. | Yes |
| systemModules. edgeHub. Settings<br>.createOptions | Az IoT Edge hub-tároló létrehozására vonatkozó beállításokat tartalmazó sztringesített JSON. [Docker-létrehozási beállítások](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| systemModules.edgeHub.configuration.id | A modult üzembe helyező központi telepítés azonosítója. | IoT Hub beállítja ezt a tulajdonságot, ha a jegyzékfájlt központi telepítés használatával alkalmazza a rendszer. Nem része az üzembe helyezési jegyzéknek. |
| modulok. {moduleId}. verzió | A modul verzióját jelölő, felhasználó által definiált karakterlánc. | Yes |
| modulok. {moduleId}. Type | A "Docker"-nek kell lennie | Yes |
| modulok. {moduleId}. állapot | {"fut" \| "leállítva"} | Yes |
| modulok. {moduleId}. restartPolicy | {"soha" \| "on-failure" " \| on-inhealth" " \| mindig"} | Yes |
| modulok. {moduleId}. startupOrder | Egész érték, amely esetében az indítási sorrendbe kerülő modul helye. a 0 érték az első és a maximális egész szám (4294967295) utolsó. Ha nincs megadva érték, az alapértelmezett érték a maximális egész szám.  | No |
| modulok. {moduleId}. imagePullPolicy | {"on-create" \| "soha"} | No |
| modulok. {moduleId}. env | A modulnak átadandó környezeti változók listája. A formátum `"<name>": {"value": "<value>"}` | No |
| modulok. {moduleId}. Settings. rendszerkép | A modul rendszerképének URI-ja. | Yes |
| modulok. {moduleId}. Settings. createOptions | Egy sztringesített JSON, amely a modul-tároló létrehozásához szükséges beállításokat tartalmazza. [Docker-létrehozási beállítások](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| modulok. {moduleId}. Configuration. id | A modult üzembe helyező központi telepítés azonosítója. | IoT Hub beállítja ezt a tulajdonságot, ha a jegyzékfájlt központi telepítés használatával alkalmazza a rendszer. Nem része az üzembe helyezési jegyzéknek. |

## <a name="edgeagent-reported-properties"></a>EdgeAgent jelentett tulajdonságai

A IoT Edge ügynök által jelentett tulajdonságok három fő információt tartalmaznak:

1. A legutóbb látott kívánt tulajdonságok alkalmazásának állapota;
2. Az eszközön jelenleg futó modulok állapota a IoT Edge ügynök által jelentett módon; és
3. Az eszközön jelenleg futó kívánt tulajdonságok másolata.

Az aktuálisan szükséges tulajdonságok másolata hasznos annak megadásához, hogy az eszköz alkalmazta-e a legújabb telepítést, vagy továbbra is egy korábbi telepítési jegyzék fut-e.

> [!NOTE]
> A IoT Edge ügynök jelentett tulajdonságai hasznosak lehetnek, mivel lekérdezhető a [IoT hub lekérdezési nyelvvel](../iot-hub/iot-hub-devguide-query-language.md) , hogy kivizsgálják a központi telepítések állapotát a skálán. További információ a IoT Edge ügynök tulajdonságainak állapotáról: [IoT Edge központi telepítések ismertetése egyetlen eszközhöz vagy nagy méretekben](module-deployment-monitoring.md).

A következő táblázat nem tartalmazza a kívánt tulajdonságokból másolt adatokat.

| Tulajdonság | Leírás |
| -------- | ----------- |
| lastDesiredVersion | Ez az egész szám a IoT Edge ügynök által feldolgozott kívánt tulajdonságok utolsó verziójára hivatkozik. |
| lastDesiredStatus. code | Ez az állapotkód a IoT Edge ügynök által látott utolsó kívánt tulajdonságokra utal. Megengedett értékek: `200` sikeres, `400` Érvénytelen konfiguráció, `412` érvénytelen séma-verzió, `417` a kívánt tulajdonságok üresek, `500` nem sikerült |
| lastDesiredStatus. Description | Az állapot szövegének leírása |
| deviceHealth | `healthy` Ha az összes modul futtatókörnyezeti állapota `running` vagy vagy `stopped` , `unhealthy` máskülönben |
| configurationHealth. {deploymentId}. Health | `healthy` Ha az üzemelő példány ({deploymentId}) által beállított összes modul futtatókörnyezeti állapota vagy `running` vagy `stopped` , `unhealthy` máskülönben |
| Runtime. platform. OS | Az eszközön futó operációs rendszer jelentése |
| Runtime. platform. Architecture | A CPU architektúrájának jelentése az eszközön |
| systemModules.edgeAgent.runtimeStatus | IoT Edge ügynök jelentett állapota: {"nem megfelelő" állapotú "" nem megfelelő " \| } |
| systemModules.edgeAgent.statusDescription | A IoT Edge ügynök jelentett állapotának szöveges leírása. |
| systemModules.edgeHub.runtimeStatus | IoT Edge hub állapota: {"Running" " \| Leállítva" "sikertelen" " \| leállítási" "nem megfelelő" \| \| } |
| systemModules.edgeHub.statusDescription | IoT Edge hub állapotának szöveges leírása, ha a sérült. |
| systemModules. edgeHub. exitCode | Az IoT Edge hub-tároló által jelentett kilépési kód, ha a tároló kilép |
| systemModules.edgeHub.startTimeUtc | IoT Edge hub utolsó indításának ideje |
| systemModules.edgeHub.lastExitTimeUtc | Az IoT Edge hub utolsó kilépésének időpontja |
| systemModules.edgeHub.lastRestartTimeUtc | IoT Edge hub legutóbbi újraindításakor eltöltött idő |
| systemModules.edgeHub.restartCount | A modul újraindításainak száma az újraindítási szabályzat részeként. |
| modulok. {moduleId}. runtimeStatus | A modul állapota: {"Running" " \| Leállítva" "sikertelen" " \| \| leállítási" " \| sérült"} |
| modulok. {moduleId}. statusDescription | A modul állapotának szöveges leírása, ha a sérült. |
| modulok. {moduleId}. exitCode | A modul tárolója által jelentett kilépési kód, ha a tároló kilép |
| modulok. {moduleId}. startTimeUtc | A modul utolsó indításának ideje |
| modulok. {moduleId}. lastExitTimeUtc | A modul utolsó kilépésének időpontja |
| modulok. {moduleId}. lastRestartTimeUtc | A modul utolsó újraindításakor eltöltött idő |
| modulok. {moduleId}. restartCount | A modul újraindításainak száma az újraindítási szabályzat részeként. |

## <a name="edgehub-desired-properties"></a>EdgeHub kívánt tulajdonságai

Meghívja az IoT Edge hub-modult, `$edgeHub` és összehangolja az eszközön futó IoT Edge hub és a IoT hub közötti kommunikációt. A kívánt tulajdonságok akkor jelennek meg, ha egy adott eszközön egy eszköz vagy egy méretezéses telepítés részeként alkalmazza a központi telepítési jegyzéket.

| Tulajdonság | Leírás | Szükséges az üzembe helyezési jegyzékben |
| -------- | ----------- | -------- |
| sémaverzióval | Vagy "1,0" vagy "1,1". Az 1,1-es verzió a IoT Edge 1.0.10-verzióval lett bevezetve, és ajánlott. | Yes |
| útvonalak. z | Egy IoT Edge hub-útvonalat jelölő sztring. További információ: [útvonalak deklarálása](module-composition.md#declare-routes). | Az `routes` elem lehet jelen, de üres. |
| storeAndForwardConfiguration.timeToLiveSecs | Az az időtartam (másodpercben), ameddig IoT Edge hub megtartja az üzeneteket, ha leválasztják az útválasztási végpontokat, függetlenül attól, hogy IoT Hub vagy egy helyi modulról. Az érték bármilyen pozitív egész szám lehet. | Yes |

## <a name="edgehub-reported-properties"></a>EdgeHub jelentett tulajdonságai

| Tulajdonság | Leírás |
| -------- | ----------- |
| lastDesiredVersion | Ez az egész szám az IoT Edge hub által feldolgozott kívánt tulajdonságok utolsó verziójára utal. |
| lastDesiredStatus. code | Az IoT Edge hub által látott utolsó kívánt tulajdonságokra hivatkozó állapotkód. Megengedett értékek: `200` sikeres, `400` Érvénytelen konfiguráció, `500` sikertelen |
| lastDesiredStatus. Description | Az állapot szövegének leírása. |
| ügyfelek. {eszköz-vagy moduleId}. állapot | Az eszköz vagy modul kapcsolati állapota. Lehetséges értékek: {"Connected" " \| leválasztva"}. Csak a modul-identitások lehetnek leválasztott állapotban. Az IoT Edge hubhoz csatlakozó alsóbb rétegbeli eszközök csak akkor jelennek meg, ha csatlakoztatva vannak. |
| ügyfelek. {eszköz-vagy moduleId}. lastConnectTime | Az eszköz vagy modul csatlakozásának legutóbbi időpontja. |
| ügyfelek. {eszköz-vagy moduleId}. lastDisconnectTime | Az eszköz vagy modul leválasztásának legutóbbi időpontja. |

## <a name="next-steps"></a>Következő lépések

Ha szeretné megtudni, hogyan használhatja ezeket a tulajdonságokat az üzembe helyezési jegyzékek kiépítéséhez, tekintse meg a [IoT Edge modulok használatának, konfigurálásának és](module-composition.md)újbóli használatának megismerését ismertető témakört.

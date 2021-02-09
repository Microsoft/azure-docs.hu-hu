---
title: Oktatóanyag – videó figyelése az Azure IoT Central video Analytics használatával – objektum-és mozgásérzékelő-észlelési alkalmazás sablonja
description: Ez az oktatóanyag bemutatja, hogyan kezelheti a kamerákat és figyelheti a videót az irányítópultok használatával a video Analytics-Object és a Motion Detection alkalmazás sablonjában.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: fbfef094cd062e437f2a28369162de96631ef41b
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832606"
---
# <a name="tutorial-monitor-and-manage-a-video-analytics---object-and-motion-detection-application"></a>Oktatóanyag: video Analytics-objektum-és mozgásészlelési alkalmazás figyelése és kezelése

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Vegyen fel objektumokat és mozgásészlelési kamerákat a IoT Central alkalmazásba.
> * Kezelheti a videó streameket, és megjátszhatja őket, ha a rendszer érdekes eseményeket észlel.

## <a name="prerequisites"></a>Előfeltételek

A Kezdés előtt a következőket kell elvégeznie:

* Az egyik előző [Live Video Analytics-alkalmazás létrehozása az Azure-ban IoT Central](./tutorial-video-analytics-create-app-yolo-v3.md) vagy [egy videó-elemzés létrehozása az Azure IoT Central (OpenVINO &trade; )](tutorial-video-analytics-create-app-openvino.md) oktatóanyagokban.
* Az egyik előző [létrehoz egy IoT Edge példányt az élő videó elemzéséhez (linuxos virtuális gép)](tutorial-video-analytics-iot-edge-vm.md) , vagy [létrehoz egy IoT Edge-példányt az élő video Analytics (Intel NUC)](tutorial-video-analytics-iot-edge-nuc.md) oktatóanyagokhoz.

A video Viewer alkalmazás futtatásához a [Docker](https://www.docker.com/products/docker-desktop) -t telepíteni kell a helyi gépre.

## <a name="add-an-object-detection-camera"></a>Objektum-észlelési kamera hozzáadása

A IoT Central alkalmazásban navigáljon a korábban létrehozott **LVA Gateway 001** -eszközhöz. Ezután válassza a **parancsok** lapot.

Használja az alábbi táblázatban szereplő értékeket a **kamera-kérelem hozzáadása** parancs paramétereinek értékeiként. A táblázatban látható értékek feltételezik, hogy a szimulált kamerát az Azure-beli virtuális gépen használja, az értékek megfelelő módosításával, ha valódi kamerát használ:

| Mező| Leírás| Mintaérték|
|---------|---------|---------|
| Kamera azonosítója      | Az eszköz azonosítója az üzembe helyezéshez | kamera – 003 |
| Kamera neve    | Felhasználóbarát név           | Objektum-észlelési kamera |
| RTSP URL-cím       | Az adatfolyam címe   | RTSP://10.0.0.4:554/media/camera-300s.mkv|
| RTSP-Felhasználónév  |                         | felhasználó!    |
| RTSP-jelszó  |                         | jelszó    |
| Észlelés típusa | Legördülő lista                | Objektumészlelés       |

A kamera eszköz hozzáadásához válassza a **Futtatás** elemet:

:::image type="content" source="media/tutorial-video-analytics-manage/add-camera.png" alt-text="Kamera hozzáadása":::

> [!NOTE]
> Az **LVA Edge Object detektor** eszköz sablonja már létezik az alkalmazásban.

## <a name="add-a-motion-detection-camera-optional"></a>Mozgásészlelési kamera hozzáadása (nem kötelező)

Ha két kamerája van a IoT Edge átjáró-eszközhöz csatlakoztatva, ismételje meg az előző lépéseket, és adjon hozzá egy mozgásérzékelő kamerát az alkalmazáshoz. Különböző értékeket használhat a **kamera azonosítójának**, a **kamera nevének** és az **RTSP URL-címének** paramétereinek.

## <a name="view-the-downstream-devices"></a>Az alsóbb rétegbeli eszközök megtekintése

Válassza ki a **LVA Gateway 001** eszköz **alsóbb rétegbeli eszközök** lapját az imént hozzáadott kamera-eszközök megtekintéséhez:

:::image type="content" source="media/tutorial-video-analytics-manage/inspect-downstream.png" alt-text="Vizsgálata":::

A kamera eszközei az alkalmazás **eszközök** lapján is megjelennek a listában.

## <a name="configure-and-manage-the-camera"></a>A kamera konfigurálása és kezelése

Navigáljon a **kamera-003** eszközhöz, és válassza a **kezelés** lapot.

Használja az alapértelmezett értékeket, vagy módosítsa, ha testre kell szabnia az eszköz tulajdonságait:

**Kamera beállításai**

| Tulajdonság | Leírás | Ajánlott érték |
|-|-|-|
| Videolejátszás gazdagép | A Azure Media Player Viewer gazdagépe | http://localhost:8094 |

**LVA-műveletek és-diagnosztika**

| Tulajdonság | Leírás | Ajánlott érték |
|-|-|-|
| Automatikus indítás | Az objektum észlelésének elindítása a LVA-átjáró újraindításakor | Jelölje be |
| Hibakeresés telemetria | Események nyomkövetése | Választható |
|Következtetési időkorlát (mp)| A következtetések leállításának időtartamára használt idő | 20 |

**AI-objektumok észlelése**

| Tulajdonság | Leírás | Ajánlott érték |
|-|-|-|
| Maximális következtetések rögzítési ideje (s) | Maximális rögzítési idő | 15 |
| Észlelési osztályok | Karakterláncok vesszővel elválasztva, az észlelési címkékkel. További információkért tekintse meg a [támogatott címkék listáját](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/yolov3-onnx/tags.txt) . | Tehergépkocsi, busz |
| Megbízhatósági küszöb | A minősítés százalékos értéke annak megállapításához, hogy az objektum észlelése érvényes-e | 70 |
| Következtetési keret mintavételi sebessége (FPS) | [Leírás itt] | 2 |

Kattintson a **Mentés** gombra.

Néhány másodperc elteltével megjelenik az egyes beállításokhoz tartozó **elfogadott** megerősítő üzenet:

:::image type="content" source="media/tutorial-video-analytics-manage/object-detection.png" alt-text="Objektum észlelése":::

## <a name="start-lva-processing"></a>LVA-feldolgozás indítása

Navigáljon a **kamera-003** eszközhöz, és válassza a **parancsok** lapot.

Futtassa a **Start LVA Processing** parancsot.

Ha a parancs befejeződik, tekintse meg a parancs előzményeit, és győződjön meg róla, hogy nincsenek hibák:

:::image type="content" source="media/tutorial-video-analytics-manage/start-processing.png" alt-text="LVA-feldolgozási parancs elindítása":::

## <a name="monitor-the-cameras"></a>A kamerák figyelése

Navigáljon a **kamera-003** eszközhöz, és válassza az **irányítópult** fület:

:::image type="content" source="media/tutorial-video-analytics-manage/camera-dashboard.png" alt-text="Kamera irányítópultja":::

Az **észlelések száma** csempe a kiválasztott észlelési osztályok objektumainak átlagos észlelési számát jeleníti meg egy másodperces észlelési intervallumban.

Az **észlelési osztályok** tortadiagram az egyes osztályok típusaihoz tartozó észlelések százalékos arányát jeleníti meg.

A **következtetést** kimutató esemény videó az észleléseket tartalmazó Azure Media Services eszközeire mutató hivatkozások listája. A hivatkozás a következő szakaszban ismertetett gazdagépet használja.

## <a name="start-the-streaming-endpoint"></a>A streamvégpont elindítása

A Media Services végpont elindításával engedélyezheti, hogy az ügyfél Media Player alkalmazás a rögzített videó streamjét:

* A Azure Portal navigáljon az **LVA-RG** erőforráscsoporthoz.
* Nyissa meg az **adatfolyam-végpont** erőforrását.
* Az **adatfolyam-végpont részletei** lapon válassza az **Indítás** lehetőséget. Megjelenik egy figyelmeztetés, miszerint a számlázás a végpont indításakor elindul.

## <a name="view-stored-video"></a>Tárolt videó megtekintése

A kamerák figyelésének napjai és a gyanús képekre való reagálás meghalad. Az automatikus esemény-címkézéssel és a tárolt videóra mutató közvetlen hivatkozásokkal a késleltetett észleléssel a biztonsági operátorok a listában szereplő érdekes eseményeket találhatják meg, majd a videó megtekintéséhez a hivatkozást követve.

Az [amp videolejátszó](https://github.com/Azure/live-video-analytics/tree/master/utilities/amp-viewer) segítségével megtekintheti a Azure Media Services fiókjában tárolt videót.

A IoT Central alkalmazás a videót a Azure Media Services tárolja, ahonnan adatfolyamként tud továbbítani. A Azure Media Servicesban tárolt videó lejátszásához videolejátszó szükséges.

Győződjön meg arról, hogy a **Docker** fut a helyi gépen.

Nyisson meg egy parancssort, és a következő parancs használatával futtassa a videolejátszó egy Docker-tárolóban a helyi gépen. Cserélje le a parancsban található helyőrzőket azokra az értékekre, amelyeket korábban a *scratchpad.txt* fájlban jegyzett készített. A (z),,,, `amsAadClientId` `amsAadSecret` és az Media Services- `amsAadTenantId` `amsSubscriptionId` `amsAccountName` fiókhoz létrehozott egyszerű szolgáltatásnév:

```bash
docker run -it --rm -e amsAadClientId="<FROM_AZURE_PORTAL>" -e amsAadSecret="<FROM_AZURE_PORTAL>" -e amsAadTenantId="<FROM_AZURE_PORTAL>" -e amsArmAadAudience="https://management.core.windows.net" -e amsArmEndpoint="https://management.azure.com" -e amsAadEndpoint="https://login.microsoftonline.com" -e amsSubscriptionId="<FROM_AZURE_PORTAL>" -e amsResourceGroup="<FROM_AZURE_PORTAL>" -e amsAccountName="<FROM_AZURE_PORTAL>" -p 8094:8094 mcr.microsoft.com/lva-utilities/amp-viewer:1.0-amd64
```

|Docker-paraméter | AMS API-hozzáférés (JSON)|
|-|-|
|amsAadClientId| AadClientId|
|amsAadSecret| AadSecret |
|amsAadTenantId| AadTenantId |
|amsSubscriptionId| SubscriptionId|
|amsResourceGroup| ResourceGroup |
|amsAccountName| AccountName|

Navigáljon a **kamera-003** eszközhöz, és válassza az **irányítópult** fület. Ezután kattintson az egyik rögzített objektum-észlelési hiperhivatkozásra a **következtetési esemény videó** csempén. A videó a helyi videolejátszó által megjelenített oldalon jelenik meg:

:::image type="content" source="media/tutorial-video-analytics-manage/video-snippet.png" alt-text="Videó kódrészlet":::

## <a name="change-the-simulated-devices-in-application-dashboards"></a>Szimulált eszközök módosítása alkalmazás-irányítópultokon

Az alkalmazás-irányítópultok eredetileg a IoT Central szimulált eszközökről generált telemetria és tulajdonságokkal vannak feltöltve. Az alábbi lépéseket követve konfigurálhatja a csempéket a valódi kamerákból vagy a Live555-szimulátorból származó telemetria:

1. Navigáljon a **(minta) valódi kamera-figyelő** alkalmazás irányítópultra.
1. Válassza a **Szerkesztés** elemet.
1. Válassza ki a **jegyzet** csempét, és törölje azt.
1. Módosítsa az irányítópult címét *valódi kamera-figyelőre*.
1. A **következtetések száma** csempén válassza a konfigurálás ikont.
1. A **diagram konfigurálása** szakaszban válasszon ki egy vagy több valódi kamerát az **LVA Edge Object detektor** eszköz csoportjában.
1. Válassza ki a `AI Inference Interface/Inference Count` telemetria mezőt.
1. Válassza a **Frissítés** lehetőséget.

1. Ismételje meg a lépéseket a következő csempék esetében:
    1. Az **észlelési** tortadiagram a `AI Inference Interface/Inference/entity/tag/value` telemetria típust használja.
    1. A **következtetés** az `AI Inference Interface/Inference/entity/tag/value` utolsó ismert értéket használja.
    1. A **(z)% megbízhatósági%** az `AI Inference Interface/Inference/entity/tag/confidence` utolsó ismert értéket használja
    1.  A pillanatkép `AI Inference Interface/Inference Image` képként jelenik meg.
    1. A **következtetési esemény videó** `AI Inference Interface/Inference Event Video` hivatkozásként jelenik meg.
1. Kattintson a **Mentés** gombra.

Az **igazi kamera-figyelő** irányítópultja mostantól megjeleníti a valódi kamera-eszköz értékeit:

:::image type="content" source="media/tutorial-video-analytics-manage/update-real-cameras.png" alt-text="Valódi fényképezőgépek alkalmazás-irányítópult":::

## <a name="pause-processing"></a>Feldolgozás szüneteltetése

Az alkalmazásban szüneteltetheti az élő videók elemzésének feldolgozását:

1. Az alkalmazásban navigáljon a **parancsok** lapra az objektum-észlelési kamerához. Ezután futtassa a **stop LVA Processing** parancsot.

1. Állítsa le a Media Services-fiók streaming-végpontját:
    * A Azure Portal navigáljon az **LVA-RG** erőforráscsoporthoz.
    * Kattintson a **streaming Endpoint** erőforrásra.
    * A **folyamatos átviteli végpont részletei** lapon válassza a **Leállítás** lehetőséget.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett az alkalmazással, a következőképpen távolíthatja el az összes létrehozott erőforrást:

1. A IoT Central alkalmazásban navigáljon az **alkalmazás** lapjára az **Adminisztráció** szakaszban. Ezután válassza a **Törlés** elemet.
1. A Azure Portal törölje a **LVA-RG** erőforráscsoportot.
1. A helyi gépen állítsa le az **amp-Viewer** Docker-tárolót.

## <a name="next-steps"></a>Következő lépések

Most megtanulta, hogyan adhat hozzá kamerákat a IoT Central alkalmazáshoz, és hogyan konfigurálhatja őket az objektumok és a mozgásérzékelők észleléséhez.

A IoT Edge modulok forráskódjának testreszabásával kapcsolatban a következő témakörben tájékozódhat:

> [!div class="nextstepaction"]
> [Az élő videó Analytics-átjáró moduljainak módosítása és összeállítása](./tutorial-video-analytics-build-module.md)

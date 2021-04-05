---
title: Építészeti fogalmak az Azure IoT Centralban | Microsoft Docs
description: Ez a cikk bemutatja az Azure architektúrával kapcsolatos főbb fogalmakat IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: c2d5310d1a664aa2e22d4241d8066e41d9c82bd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97796720"
---
# <a name="azure-iot-central-architecture"></a>Az Azure IoT Central architektúrája

Ez a cikk áttekintést nyújt a Microsoft Azure IoT Central architektúráról.

![Legfelső szintű architektúra](media/concepts-architecture/architecture.png)

## <a name="devices"></a>Eszközök

Az eszközök az Azure IoT Central alkalmazással cserélik át az adatait. Az eszköz A következőket teheti:

- Mérések küldése, például telemetria.
- Szinkronizálhatja a beállításokat az alkalmazással.

Az Azure IoT Centralban az eszköz és az alkalmazás közti adatcseréhez használt adatokat egy eszközsablonban adhatja meg. További információ az eszközök sablonjairól: [metaadatok kezelése](#metadata-management).

Ha többet szeretne megtudni arról, hogy az eszközök hogyan csatlakoznak az Azure IoT Central-alkalmazáshoz, tekintse meg az [eszköz kapcsolatát](concepts-get-connected.md).

## <a name="azure-iot-edge-devices"></a>Azure IoT Edge-eszközök

Továbbá az [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks)-k használatával létrehozott eszközökhöz [Azure IoT Edge eszközöket](../../iot-edge/about-iot-edge.md) is csatlakozhat egy IoT Central alkalmazáshoz. IoT Edge lehetővé teszi, hogy közvetlenül a IoT Central által felügyelt IoT-eszközökön futtassa a felhőalapú intelligenciát és az egyéni logikát. A IoT Edge Runtime a következőket teszi lehetővé:

- A számítási feladatok telepítése és frissítése az eszközön.
- IoT Edge biztonsági szabványok fenntartása az eszközön.
- Győződjön meg arról, hogy IoT Edge modulok mindig futnak.
- Jelentési modul állapota a felhőben távoli figyeléshez.
- Kezelheti az alárendelt levelek eszközei és a IoT Edge eszközök közötti kommunikációt egy IoT Edge eszköz moduljai és egy IoT Edge eszköz és a felhő között.

![Azure-IoT Central Azure IoT Edge](./media/concepts-architecture/iotedge.png)

A IoT Central a következő képességeket teszi lehetővé IoT Edge eszközökhöz:

- IoT Edge eszköz képességeinek leírására szolgáló eszközök, például:
  - Az üzembe helyezési jegyzékfájl feltöltési képessége, amely segítséget nyújt az eszközök flottájának jegyzékfájljának kezeléséhez.
  - A IoT Edge eszközön futó modulok.
  - Az egyes modulok telemetria.
  - Az egyes modulok által jelentett tulajdonságok
  - Az egyes modulok parancsai a következőre válaszolnak:.
  - IoT Edge átjáró-eszköz és az alsóbb rétegbeli eszköz közötti kapcsolatok.
  - A IoT Edge eszközön nem tárolt Felhőbeli tulajdonságok.
  - A IoT Central alkalmazás részét képező testreszabások, irányítópultok és űrlapok.

  További információ: [Azure IoT Edge-eszközök csatlakoztatása Azure IoT Central-alkalmazáshoz](./concepts-iot-edge.md) című cikk.

- Lehetőség IoT Edge eszközök méretezésére az Azure IoT Device kiépítési szolgáltatás használatával
- Szabályok és műveletek.
- Egyéni irányítópultok és elemzések.
- A telemetria folyamatos adatexportálása IoT Edge-eszközökről.

### <a name="iot-edge-device-types"></a>IoT Edge eszközök típusai

A IoT Central az alábbi módon osztályozza IoT Edge eszközök típusát:

- Leaf-eszközök. Egy IoT Edge eszközön lehetnek alsóbb rétegbeli eszközök, de ezek az eszközök nincsenek kiépítve IoT Central.
- Átjáró-eszközök alsóbb rétegbeli eszközökkel. Az átjáró-eszköz és az alsóbb rétegbeli eszközök kiépítve IoT Central

![IoT Central IoT Edge áttekintéssel](./media/concepts-architecture/gatewayedge.png)

### <a name="iot-edge-patterns"></a>IoT Edge mintázatok

A IoT Central a következő IoT Edge eszköz-mintákat támogatja:

#### <a name="iot-edge-as-leaf-device"></a>IoT Edge Leaf-eszközként

![IoT Edge Leaf-eszközként](./media/concepts-architecture/edgeasleafdevice.png)

A IoT Edge eszköz IoT Central és az alsóbb rétegbeli eszközökön van kiépítve, és a telemetria a IoT Edge eszköztől érkezőként jelennek meg. Az IoT Edge eszközhöz csatlakoztatott alsóbb rétegbeli eszközök nincsenek kiépítve a IoT Central.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity"></a>IoT Edge átjáró-eszköz, amely identitással csatlakozik az alsóbb rétegbeli eszközökhöz

![IoT Edge alsóbb rétegbeli eszköz identitásával](./media/concepts-architecture/edgewithdownstreamdeviceidentity.png)

A IoT Edge eszköz IoT Central az IoT Edge eszközhöz csatlakoztatott alsóbb rétegbeli eszközök mellett van kiépítve. Az alsóbb rétegbeli eszközök az átjárón keresztüli üzembe helyezésének támogatása jelenleg nem támogatott.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity-provided-by-the-iot-edge-gateway"></a>IoT Edge átjáró-eszköz csatlakoztatása az IoT Edge átjáró által megadott identitással rendelkező alsóbb rétegbeli eszközökhöz

![IoT Edge az alárendelt eszközzel identitás nélkül](./media/concepts-architecture/edgewithoutdownstreamdeviceidentity.png)

A IoT Edge eszköz IoT Central az IoT Edge eszközhöz csatlakoztatott alsóbb rétegbeli eszközök mellett van kiépítve. Jelenleg nem támogatott az átjáró támogatása az alsóbb rétegbeli eszközök személyazonosságának biztosításával és az alsóbb rétegbeli eszközök kiépítési támogatásával. Ha saját személyazonosság-fordítási modult használ, IoT Central támogatja ezt a mintát.

## <a name="cloud-gateway"></a>Felhőbeli átjáró

Az Azure IoT Central az Azure IoT Hubt használja Felhőbeli átjáróként, amely lehetővé teszi az eszközök kapcsolódását. IoT Hub a következőket teszi lehetővé:

- Adatfeldolgozás a felhőben.
- Eszközkezelés.
- Biztonságos eszközök kapcsolata.

További információ a IoT Hubről: [Azure IoT hub](../../iot-hub/index.yml).

Ha többet szeretne megtudni az Azure IoT Central eszközével való kapcsolatról, tekintse meg az [eszköz kapcsolatát](concepts-get-connected.md)ismertető témakört.

## <a name="data-stores"></a>Adattárolók

Az Azure IoT Central az alkalmazásadatok felhőben való tárolását. Az alkalmazásadatok tárolt alkalmazásai a következők:

- Eszközök sablonjai.
- Eszköz identitásai.
- Eszköz metaadatai.
- Felhasználói és szerepköri adat.

Az Azure IoT Central egy idősorozat-tárolót használ az eszközökről elküldett mérési adatokhoz. Az idősorozat-adatok az analitikai szolgáltatás által használt eszközökről.

## <a name="analytics"></a>Elemzés

Az elemzési szolgáltatás feladata az alkalmazás által megjelenített egyéni jelentéskészítési adat létrehozása. Az operátor [testreszabhatja az](howto-create-analytics.md) alkalmazásban megjelenített elemzéseket. Az elemzési szolgáltatás a [Azure Time Series Insightsra](https://azure.microsoft.com/services/time-series-insights/) épül, és feldolgozza az eszközökről továbbított mérési adatok mennyiségét.

## <a name="rules-and-actions"></a>Szabályok és műveletek

A [szabályok és műveletek](tutorial-create-telemetry-rules.md) szorosan együttműködnek az alkalmazáson belüli feladatok automatizálásához. A Builder olyan szabályokat határozhat meg, amelyek az eszköz telemetria alapulnak, például a megadott küszöbértéket meghaladó hőmérsékletet. Az Azure IoT Central egy stream-processzor használatával határozza meg, hogy mikor teljesülnek a szabályok feltételei. Ha a szabály feltétele teljesül, a rendszer elindítja a szerkesztő által definiált műveletet. Egy művelet elküldheti például egy e-mailt, amely értesíti a mérnöket arról, hogy az eszköz hőmérséklete túl magas.

## <a name="metadata-management"></a>Metaadatok kezelése

Egy Azure IoT Central-alkalmazásban az eszközök sablonjai határozzák meg az eszközök típusának viselkedését és képességeit. Egy hűtőszekrény-eszköz sablonja például meghatározza azt a telemetria, amelyet a hűtőszekrény küld az alkalmazásnak.

![Sablon architektúrája](media/concepts-architecture/template-architecture.png)

Egy IoT Central [eszköz sablonjában](concepts-device-templates.md) a következőket tartalmazza:

- Egy eszköz **modellje** , amely meghatározza egy eszköz képességeit, például az általa küldött telemetria, az eszköz állapotát meghatározó tulajdonságokat, valamint azokat a parancsokat, amelyekre az eszköz válaszol. Az eszköz képességei egy vagy több interfészbe vannak rendezve.
- A **felhő tulajdonságai** határozzák meg az adott eszközhöz tartozó tulajdonságokat IoT Central. Ezek a tulajdonságok csak IoT Central tárolódnak, és soha nem lesznek elküldve egy eszközre.
- A **nézetek** határozzák meg azokat az irányítópultokat és űrlapokat, amelyeket a készítő hoz létre, hogy az operátor figyelje és felügyelje az eszközöket.
- A **testreszabások** lehetővé teszik, hogy a szerkesztő felülbírálja az eszköz modelljében szereplő definíciók némelyikét, hogy azok jobban megfeleljenek a IoT Central alkalmazásnak.

Egy alkalmazáshoz egy vagy több szimulált és valós eszköz tartozhat az egyes eszközök alapján.

## <a name="data-export"></a>Adatexportálás

Egy Azure IoT Central alkalmazásban [folyamatosan exportálhatja az adatait](howto-export-data.md) a saját azure-Event Hubs és Azure Service Bus példányaiba. Az Azure Blob Storage-fiókjába rendszeres időközönként is exportálhatja adatait. IoT Central exportálhat méréseket, eszközöket és az eszközök sablonjait.

## <a name="batch-device-updates"></a>Batch-eszközök frissítései

Egy Azure IoT Central alkalmazásban [létrehozhat és futtathat feladatokat](howto-run-a-job.md) a csatlakoztatott eszközök kezeléséhez. Ezek a feladatok lehetővé teszik az eszközök tulajdonságainak vagy beállításainak tömeges frissítését, illetve parancsok futtatását. Létrehozhat például egy feladatot, amellyel növelheti a ventilátor sebességét több hűtött árusító gép esetében is.

## <a name="role-based-access-control-rbac"></a>Szerepköralapú hozzáférés-vezérlés (RBAC)

Minden IoT Central alkalmazás rendelkezik saját beépített RBAC rendszerrel. A [rendszergazdák meghatározhatnak](howto-manage-users-roles.md) egy Azure IoT Central alkalmazás hozzáférési szabályait az egyik előre definiált szerepkör használatával vagy egyéni szerepkör létrehozásával. A szerepkörök határozzák meg, hogy az alkalmazás mely területeihez férhet hozzá a felhasználók, és milyen műveleteket végezhetnek el.

## <a name="security"></a>Biztonság

Az Azure IoT Central biztonsági funkciói a következők:

- Az adatok továbbítása és nyugalmi állapotban vannak titkosítva.
- A hitelesítés Azure Active Directory vagy Microsoft-fiókkal történik. A kétfaktoros hitelesítés támogatott.
- Teljes bérlő elkülönítése.
- Az eszköz szintjének biztonsága.

## <a name="ui-shell"></a>FELHASZNÁLÓIFELÜLET-rendszerhéj

A felhasználói felületi rendszerhéj egy modern, rugalmas, HTML5-alapú böngészőalapú alkalmazás.
A rendszergazdák egyéni témák alkalmazásával szabhatják testre az alkalmazás felhasználói felületét, és módosíthatják a Súgó hivatkozásait, hogy az egyéni súgó erőforrásaira mutassanak. További információ a felhasználói felület testreszabásáról: [Az Azure IoT Central felhasználói felületének testreszabása](howto-customize-ui.md) .

Az operátorok személyre szabott alkalmazás-irányítópultokat hozhatnak létre. Több irányítópultot is beállíthat, amelyek különböző adathalmazokat jelenítenek meg, és válthatnak egymás között.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte az Azure-IoT Central architektúráját, a javasolt következő lépés az [eszköz kapcsolatának](concepts-get-connected.md) megismerése az Azure IoT Centralban.
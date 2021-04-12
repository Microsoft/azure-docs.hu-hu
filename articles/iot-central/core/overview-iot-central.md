---
title: Mi az az Azure IoT Central? | Microsoft Docs
description: Az Azure IoT Central egy IoT-alkalmazási platform, amely leegyszerűsíti a IoT-megoldások létrehozását, és segít csökkenteni a IoT-kezelési műveletek terheit és költségeit, valamint a fejlesztést. Ez a cikk áttekintést nyújt az Azure IoT Central jellemzőiről.
author: dominicbetts
ms.author: dobett
ms.date: 11/23/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc, contperf-fy21q2
ms.openlocfilehash: 6f7b24c711d99b1127ee77a920b305acb114d20e
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505127"
---
# <a name="what-is-azure-iot-central"></a>Mi az az Azure IoT Central?

Az IoT Central egy IoT-alkalmazásplatform, amellyel kevesebb munkával és költséggel lehet nagyvállalati szintű IoT-megoldásokat fejleszteni, kezelni és karbantartani. A IoT Central való kiépítésének köszönhetően lehetősége van arra, hogy az üzleti környezet IoT-adataival való átalakításának idejére, pénzére és energiára koncentráljon, nem csupán a komplex és folyamatosan fejlődő IoT-infrastruktúra fenntartását és frissítését.

A webes felhasználói felülettel figyelheti az eszközök feltételeit, szabályokat hozhat létre, és több millió eszközt és adatmennyiséget kezelhet életciklusa során. Továbbá lehetővé teszi, hogy az IoT-intelligenciát az üzletági alkalmazásokra kiterjesztve az eszköz elemzéseit is fellépjen.

Ez a cikk IoT Central:

- Egy projekthez társított tipikus felhasználói szerepkörök.
- Egy alkalmazás létrehozása.
- Az eszközök az alkalmazáshoz történő csatlakoztatása
- Az alkalmazás felügyelete.
- IoT Central Azure IoT Edge képességei.
- A Azure IoT Edge Runtime-alapú eszközök csatlakoztatása az alkalmazáshoz.

## <a name="user-roles"></a>Felhasználói szerepkörök

Az IoT Central dokumentációja négy olyan felhasználói szerepkörre hivatkozik, amelyek egy IoT Central alkalmazással működnek:

- A _megoldás-szerkesztő_ feladata egy [alkalmazás létrehozása](quick-deploy-iot-central.md), [szabályok és műveletek konfigurálása](quick-configure-rules.md), [integrációk definiálása más szolgáltatásokkal](howto-export-data.md), valamint az alkalmazások további testreszabása a kezelők és az eszközök fejlesztői számára.
- A _kezelő_ [felügyeli az](howto-manage-devices.md) alkalmazáshoz csatlakoztatott eszközöket.
- A _rendszergazdák_ felelősek az olyan felügyeleti feladatokért, mint például a [felhasználói szerepkörök és engedélyek](howto-administer.md) kezelése az alkalmazáson belül.
- Az _eszközök fejlesztői_ [létrehozzák az](concepts-telemetry-properties-commands.md) alkalmazáshoz csatlakoztatott eszközön vagy [IoT Edge modulon](concepts-iot-edge.md) futó kódot.

## <a name="create-your-iot-central-application"></a>A IoT Central-alkalmazás létrehozása

Megoldás-szerkesztőként a IoT Central használatával létrehozhat egy egyéni, felhőalapú IoT-megoldást a szervezet számára. Az egyéni IoT-megoldások általában a következőket foglalják magukban:

- Egy felhőalapú alkalmazás, amely telemetriai adatokat fogad az eszközökről és lehetővé teszi az eszközök felügyeletét.
- Több eszköz, amelyek egyéni kódokat futtatnak és a felhőalapú alkalmazáshoz csatlakoznak.

Gyorsan üzembe helyezhet egy új IoT Central alkalmazást, és testre szabhatja a böngészőben megadott igényei szerint. Az általános _alkalmazási sablonnal_ vagy a [kereskedelmi](../retail/overview-iot-central-retail.md), [energia](../energy/overview-iot-central-energy.md)-, [kormányzati](../government/overview-iot-central-government.md)és [egészségügyi](../healthcare/overview-iot-central-healthcare.md)alkalmazások egyik iparági alkalmazási sablonjaival is elindítható.

Megoldás-szerkesztőként a webalapú eszközök használatával hozzon létre egy _sablont_ az alkalmazáshoz csatlakozó eszközökhöz. Az eszköz sablonja az a terv, amely meghatározza egy adott típusú eszköz jellemzőit és viselkedését, például:

- Telemetria. Ilyenek például a hőmérséklet és a páratartalom. A telemetria adatfolyam-adatátviteli szolgáltatás.
- Az operátor által módosítható üzleti tulajdonságok. Ilyenek például az ügyfél címe és az utolsó kiszolgált dátum.
- Azok az eszköztulajdonságok, amelyeket az eszköz állít be, és az alkalmazásban csak olvashatók. Például a szelep állapota nyitott vagy leállított állapotú lehet.
- A tulajdonságok, amelyeket az operátor állít be, amelyek meghatározzák az eszköz viselkedését. Például az eszközhöz tartozó cél hőmérséklet.
- Az eszközön futó operátor által hívható parancsok. Például egy eszköz távoli újraindítására szolgáló parancs.

Ez az [eszköz](howto-set-up-template.md) a következőket tartalmazza:

- Az eszköz által megvalósított képességeket ismertető _eszköz modellje_ . Az eszköz képességei a következők:

  - A IoT Central telemetria.
  - A csak olvasható tulajdonságok a jelentés állapotát IoT Central.
  - Az eszköz állapotának beállításához a IoT Centraltól kapott írható tulajdonságok.
  - A IoT Central által hívott parancsok.

- Az eszközön nem tárolt Felhőbeli tulajdonságok.
- A IoT Central alkalmazás részét képező testreszabások, irányítópultok és űrlapok.

### <a name="create-device-templates"></a>Eszközök sablonjainak létrehozása

Megoldás-szerkesztőként több lehetőség közül választhat az eszközök sablonjainak létrehozásához:

- Tervezze meg IoT Central az eszköz sablonját, majd implementálja az eszköz modelljét az eszköz kódjában.
- Hozzon létre egy eszköz modellt a Visual Studio Code használatával, és tegye közzé a modellt egy adattárban. Implementálja az eszköz kódját a modellből, és kapcsolja össze az eszközt a IoT Central alkalmazással. IoT Central megkeresi az eszköz modelljét a tárházból, és létrehoz egy egyszerű eszközt a sablonhoz.
- Hozzon létre egy eszköz modellt a Visual Studio Code használatával. Implementálja az eszköz kódját a modellből. Manuálisan importálja az eszköz modelljét a IoT Central alkalmazásba, majd adja hozzá a IoT Central alkalmazás igényeinek megfelelő Felhőbeli tulajdonságokat, testreszabásokat és irányítópultokat.

Megoldás-szerkesztőként a IoT Central segítségével programkódot hozhatja meg az eszközök teszteléséhez az eszköz sablonjainak ellenőrzéséhez.

Ha Ön egy eszköz fejlesztője, tekintse meg a [IoT Central az Eszközkezelő áttekintését](./overview-iot-central-developer.md) az eszközöket használó eszközök megvalósításának bevezetéséről.

### <a name="customize-the-ui"></a>A felhasználói felület testreszabása

Megoldás-szerkesztőként testreszabhatja a IoT Central alkalmazás felhasználói felületét azon operátorok esetében is, akik felelősek az alkalmazás napi használatért. A megoldás-szerkesztő által végrehajtható testreszabások:

- Egy eszközsablonban található tulajdonságok és beállítások elrendezésének meghatározása.
- Egyéni irányítópultok konfigurálása, amelyek segítik az operátorokat a megállapítások felismerésében és a problémák gyorsabb megoldásában.
- Egyéni elemzések konfigurálása, amelyek a csatlakoztatott eszközökről származó idősorozat-adatokat vizsgálják.

## <a name="manage-your-devices"></a>Eszközök kezelése

Kezelőként a IoT Central alkalmazással [felügyelheti az eszközöket](howto-manage-devices.md) a IoT Central-megoldásban. A kezelők az alábbiakhoz hasonló feladatokat végzik el:

- Az alkalmazáshoz csatlakoztatott eszközök monitorozása.
- Az eszközök hibáinak keresése és elhárítása.
- Új eszközök üzembe helyezése.

Megoldás-szerkesztőként olyan [Egyéni szabályokat és műveleteket adhat meg](howto-configure-rules.md) , amelyek a csatlakoztatott eszközökről végeznek adatátvitelt. Az operátor eszközszinten engedélyezheti vagy letilthatja ezeket a szabályokat, az alkalmazáson belüli feladatok vezérlése és automatizálása céljából.

A nagy léptékű működésre tervezett IoT-megoldások esetében fontos az eszközkezelés strukturált megközelítése. Nem elég egyszerűen csatlakoztatni az eszközöket a felhőhöz, és az eszközöket csatlakoztatni és kifogástalan állapotba kell hozni. Az operátorok az alábbi IoT Central képességekkel kezelhetik eszközeiket az alkalmazások életciklusa során:

### <a name="dashboards"></a>Irányítópultok

A beépített [irányítópultok](./howto-set-up-template.md#generate-default-views) testreszabható felhasználói felületet biztosítanak az eszközök állapotának és telemetria figyeléséhez. Kezdje egy beépített irányítópulttal egy [alkalmazás-sablonban](howto-use-app-templates.md) , vagy hozzon létre saját irányítópultokat az operátorok igényei szerint. Az irányítópultokat megoszthatja az alkalmazás összes felhasználójának használatával, vagy megtarthatja azokat.

### <a name="rules-and-actions"></a>Szabályok és műveletek

Az eszköz állapotán és telemetria alapuló [Egyéni szabályok](tutorial-create-telemetry-rules.md) alapján azonosíthatja az eszközöket, amelyeknek figyelmet igényelnek. Konfigurálja a megfelelő személyek értesítéséhez szükséges műveleteket, és gondoskodjon arról, hogy a javítási intézkedések időben legyenek elvégezve.

### <a name="jobs"></a>Feladatok

A [feladatok](howto-run-a-job.md) lehetővé teszik az eszközökön történő egyszeri vagy tömeges frissítés beállítását tulajdonságok vagy hívási parancsok megadásával.

## <a name="integrate-with-other-services"></a>Integráció más szolgáltatásokkal

Az alkalmazási platformként a IoT Central lehetővé teszi a IoT-információk átalakítását az üzleti elemzésekben, amelyek a gyakorlatban hasznosítható eredményeket vezetnek. A [szabályok](./tutorial-create-telemetry-rules.md), az [adatexportálás](./howto-export-data.md)és a [nyilvános REST API](/learn/modules/manage-iot-central-apps-with-rest-api/) példák arra, hogyan integrálható az IoT Central az üzletági alkalmazásokkal:

![Hogyan alakíthatja át a IoT Central a IoT-adatait](media/overview-iot-central/transform.png)

Létrehozhat olyan üzleti elemzéseket, mint például a gépi hatékonysági trendek meghatározása vagy a jövőbeli energiafelhasználás megjósolása a gyári szinten, ha egyéni elemzési folyamatokat hoz létre az telemetria az eszközökről való feldolgozásához és az eredmények tárolásához. Konfigurálja az adatexportálást a IoT Central alkalmazásban a telemetria, az eszköz tulajdonságainak változásainak és az eszköz sablonjának más szolgáltatásokra való módosításainak exportálására, ahol elemezheti, tárolhatja és megjelenítheti az adatait az Ön által előnyben részesített eszközökkel.

### <a name="build-custom-iot-solutions-and-integrations-with-the-rest-apis"></a>Egyéni IoT-megoldások és-integrációk létrehozása a REST API-kkal

IoT-megoldások létrehozása, például:

- Az eszközök távoli beállítására és vezérlésére képes mobil Companion-alkalmazások.
- Olyan egyéni integrációk, amelyek lehetővé teszik a meglévő üzletági alkalmazások számára a IoT-eszközökkel és-adataikkal való interakciót.
- Eszközkezelő alkalmazások az eszközök modellezéséhez, előkészítéséhez, kezeléséhez és az adathozzáféréshez.

## <a name="administer-your-application"></a>Alkalmazás felügyelése

IoT Central alkalmazásait teljes mértékben a Microsoft üzemelteti, ami csökkenti az alkalmazások felügyeletének terhelését. A rendszergazdák [felhasználói szerepkörökkel és engedélyekkel](howto-administer.md)kezelhetik az alkalmazáshoz való hozzáférést.

## <a name="pricing"></a>Díjszabás

A 7 napos ingyenes próbaverzió használatával IoT Central alkalmazást hozhat létre, vagy használhat standard díjszabási csomagot is.

- Az *ingyenes* csomag használatával létrehozott alkalmazások hét napig ingyenesen használhatók, és legfeljebb öt eszközt támogatnak. Egy standard díjszabási csomag használatára a lejárat előtt bármikor átalakítható.
- A *standard* csomag használatával létrehozott alkalmazások számlázása eszközönként történik, a **standard 0**, **standard 1** vagy standard **2** díjszabás közül választhat, amely az első két eszközzel ingyenes. További információ a [IoT Central díjszabásáról](https://aka.ms/iotcentral-pricing).

## <a name="quotas"></a>Kvóták

Minden Azure-előfizetéshez tartozik alapértelmezett kvóta, amely hatással lehet a IoT-megoldás hatókörére. Jelenleg IoT Central korlátozza az előfizetésben üzembe helyezhető alkalmazások számát 10-re. Ha a korlát növelésére van szüksége, forduljon a [Microsoft ügyfélszolgálatához](https://azure.microsoft.com/support/options/).

## <a name="known-issues"></a>Ismert problémák

- A folyamatos adatexportálás nem támogatja a Avro formátumot (inkompatibilitás).
- A GeoJSON jelenleg nem támogatott.
- A Térkép csempe jelenleg nem támogatott.
- A tömb sémájának típusai nem támogatottak.
- Csak a C eszközoldali SDK és a Node.js eszköz és a szolgáltatás SDK-k támogatottak.
- IoT Central jelenleg a Egyesült Államok, Európa, Ázsia és a Csendes-óceáni térség, Ausztrália, Egyesült Királyság és Japán helyszíneken érhető el.

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintette a IoT Centralt, néhány javasolt lépés:

- Ha Ön egy eszköz fejlesztője, és szeretne belemerülni egy kódra, a javasolt következő lépés egy [ügyfélalkalmazás létrehozása és csatlakoztatása az Azure IoT Central-alkalmazáshoz](./tutorial-connect-device.md).
- Ismerkedés az [Azure IoT Central kezelőfelületével](overview-iot-central-tour.md).
- [Egy Azure IoT Central-alkalmazás létrehozásának](quick-deploy-iot-central.md) első lépései.

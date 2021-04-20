---
title: Mi az az Azure IoT Central? | Microsoft Docs
description: Azure IoT Central egy IoT-alkalmazásplatform, amely leegyszerűsíti az IoT-megoldások létrehozását, és segít csökkenteni az IoT-felügyeleti műveletek és a fejlesztés terhét és költségeit. Ez a cikk áttekintést nyújt az Azure IoT Central jellemzőiről.
author: dominicbetts
ms.author: dobett
ms.date: 04/19/2021
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc, contperf-fy21q2
ms.openlocfilehash: 88f59c1b3fc1014cef5035845f1f2e8616bea908
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739907"
---
# <a name="what-is-azure-iot-central"></a>Mi az az Azure IoT Central?

Az IoT Central egy IoT-alkalmazásplatform, amellyel kevesebb munkával és költséggel lehet nagyvállalati szintű IoT-megoldásokat fejleszteni, kezelni és karbantartani. A IoT Central választásával időt, pénzt és energiát összpontosíthat az üzlet IoT-adatokkal való átalakítására egy összetett és folyamatosan fejlődő IoT-infrastruktúra karbantartása és frissítése helyett.

A webes felhasználói felület lehetővé teszi az eszközök gyors csatlakoztatását, az eszközfeltételek figyelése, szabályok létrehozása és eszközök millióinak és adatainak kezelését az életciklusuk során. Emellett lehetővé teszi az eszközelemzések alapján történő tevékenységhez az IoT-intelligencia üzletági alkalmazásokra való kiterjesztését.

Ez a cikk a következő IoT Central:

- A projekthez társított tipikus felhasználói szerepkörök.
- Egy alkalmazás létrehozása.
- Az eszközök az alkalmazáshoz történő csatlakoztatása
- Az alkalmazás felügyelete.
- Azure IoT Edge képességeket a IoT Central.
- A Azure IoT Edge eszközök csatlakoztatása az alkalmazáshoz.

## <a name="user-roles"></a>Felhasználói szerepkörök

A IoT Central dokumentáció négy felhasználói szerepkörre vonatkozik, amelyek egy IoT Central alkalmazással:

- A _megoldáskészítők_ feladata az alkalmazások [létrehozása,](quick-configure-rules.md) [a](quick-deploy-iot-central.md)szabályok és műveletek [konfigurálása,](howto-export-data.md)a más szolgáltatásokkal való integrációk meghatározása, valamint az alkalmazás további testreszabása az operátorok és eszközfejlesztők számára.
- Az _operátor_ [felügyeli az alkalmazáshoz](howto-manage-devices.md) csatlakoztatott eszközöket.
- A _rendszergazda_ felelős az olyan rendszergazdai feladatokért, mint a felhasználói [szerepkörök és](howto-administer.md) engedélyek kezelése az alkalmazásban.
- Az _eszközfejlesztők_ [az](concepts-telemetry-properties-commands.md) alkalmazáshoz csatlakoztatott eszközön vagy IoT Edge futó kódot hoznak létre. [](concepts-iot-edge.md)

## <a name="create-your-iot-central-application"></a>Saját IoT Central létrehozása

Gyorsan üzembe helyezhet egy új IoT Central alkalmazást, majd testreszabhatja az adott követelményeknek megfelelően. Kezdjen egy általános _alkalmazássablonnal_ vagy az egyik iparág-központú alkalmazássablonnal:

- [Retail](../retail/overview-iot-central-retail.md)
- [Energia](../energy/overview-iot-central-energy.md)
- [Államigazgatás](../government/overview-iot-central-government.md)
- [Healthcare .](../healthcare/overview-iot-central-healthcare.md)

Az első [alkalmazás létrehozásához](quick-deploy-iot-central.md) tekintse meg az Új alkalmazás létrehozása rövid útmutatót.

## <a name="connect-devices"></a>Eszközök csatlakoztatása

Az alkalmazás létrehozása után az első lépés az eszközök létrehozása és csatlakoztatása. Minden eszköz, amely IoT Central _eszközsablont használ._ Az eszközsablon egy olyan terv, amely meghatározza egy eszköztípus jellemzőit és viselkedését, például a következőt:

- Az eszköz által küldött telemetria. Ilyenek például a hőmérséklet és a páratartalom. A telemetria streamelési adatok.
- Az operátor által módosítható üzleti tulajdonságok. Ilyen például az ügyfél címe és az utolsó szervizelt dátum.
- Azok az eszköztulajdonságok, amelyeket az eszköz állít be, és az alkalmazásban csak olvashatók. Ilyen lehet például egy nyitott vagy leállított éter állapota.
- Az operátor által beállítja tulajdonságok, amelyek meghatározzák az eszköz viselkedését. Például az eszköz célhőmérséklete.
- Egy operátor által hívhatja meg az eszközön futó parancsokat. Például egy parancs egy eszköz távoli újraindítására.

Minden [eszközsablon a következőket](howto-set-up-template.md) tartalmazza:

- Egy _eszközmodell,_ amely leírja, hogy az eszköznek milyen képességeket kell megvalósítania. Az eszköz képességei a következők:

  - A telemetria, amelybe a IoT Central.
  - Azok a csak olvasható tulajdonságok, amelyek az állapot jelentéséhez IoT Central.
  - Az eszköztől kapott írható IoT Central eszközállapot beállítására.
  - A parancsokat a IoT Central.

- Az eszközön nem tárolt felhőtulajdonságok.
- Az alkalmazás részét IoT Central testreszabások, irányítópultok és űrlapok.

Eszközsablonok létrehozására több lehetőség is rendelkezésre áll:

- Tervezni kell az eszközsablont a IoT Central majd implementálja az eszközmodellt az eszközkódban.
- Hozzon létre egy eszközmodellt Visual Studio és tegye közzé a modellt egy adattárban. Implementálja az eszköz kódját a modellből, és csatlakoztassa az eszközt IoT Central alkalmazásához. IoT Central megkeresi az eszközmodellt az adattárból, és létrehoz egy egyszerű eszközsablont.
- Eszközmodell létrehozása a Visual Studio használatával. Implementálja az eszköz kódját a modellből. Manuálisan importálja az eszközmodellt a IoT Central-alkalmazásba, majd adja hozzá az alkalmazás számára szükséges felhőtulajdonságokat, testreszabásokat és IoT Central irányítópultokat.

Az első [eszköz létrehozásához](quick-create-simulated-device.md) és csatlakoztatáshoz tekintse meg a Szimulált eszköz hozzáadása rövid útmutatót.

### <a name="customize-the-ui"></a>A felhasználói felület testreszabása

A napi használatért IoT Central operátorok felhasználói felületét is testreszabhatja. Többek között a következő testreszabásokat lehet testreszabadni:

- Egyéni irányítópultok konfigurálása, amelyek segítik az operátorokat a megállapítások felismerésében és a problémák gyorsabb megoldásában.
- Egyéni elemzések konfigurálása, amelyek a csatlakoztatott eszközökről származó idősorozat-adatokat vizsgálják.
- Egy eszközsablonban található tulajdonságok és beállítások elrendezésének meghatározása.

## <a name="manage-your-devices"></a>Eszközök kezelése

Operátorként a IoT Central használatával kezelheti a [](howto-manage-devices.md) saját megoldásában IoT Central eszközöket. Az operátorok a következő feladatokat végrehajtásához végrehajtásához:

- Az alkalmazáshoz csatlakoztatott eszközök monitorozása.
- Az eszközök hibáinak keresése és elhárítása.
- Új eszközök üzembe helyezése.

Egyéni szabályokat [és műveleteket definiálhat,](howto-configure-rules.md) amelyek a csatlakoztatott eszközökről streameléssel működnek. Az operátor eszközszinten engedélyezheti vagy letilthatja ezeket a szabályokat, az alkalmazáson belüli feladatok vezérlése és automatizálása céljából.

Mint minden nagy léptékű működésre tervezett IoT-megoldásnál, az eszközkezelés strukturált megközelítése is fontos. Nem elég csupán csatlakoztatni az eszközöket a felhőhöz, hanem csatlakoztatottnak és kifogástalan állapotúnak kell tartania az eszközöket. Az alábbi IoT Central az eszközök kezeléséhez az alkalmazás teljes életciklusa során:

### <a name="dashboards"></a>Irányítópultok

A beépített [irányítópultok testreszabható](./howto-set-up-template.md#generate-default-views) felhasználói felületet biztosítanak az eszközök állapotának és telemetriának figyelése érdekében. Kezdje egy előre létrehozott irányítópulttal egy alkalmazássablonban, [](howto-use-app-templates.md) vagy hozzon létre saját, az operátorok igényeihez igazított irányítópultokat. Az irányítópultokat megoszthatja az alkalmazás összes felhasználójának, vagy megtarthatja őket privátként.

### <a name="rules-and-actions"></a>Szabályok és műveletek

Az [eszközállapot és](tutorial-create-telemetry-rules.md) a telemetria alapján egyéni szabályokat hozhat létre a figyelmet igényelő eszközök azonosításához. Konfigurálja a megfelelő személyeket értesítő műveleteket, és biztosítsa a korrekciós intézkedések időben történő megtételét.

### <a name="jobs"></a>Feladatok

[A](howto-run-a-job.md) feladatok segítségével egy- vagy tömeges frissítéseket alkalmazhat az eszközökre tulajdonságok beállításával vagy parancsok hívásával.

## <a name="integrate-with-other-services"></a>Integráció más szolgáltatásokkal

Alkalmazásplatformként a IoT Central lehetővé teszi az IoT-adatok üzleti elemzésekké való átalakítását, amelyek a beavatkozást lehetővé tő eredményekhez vezetnek. [A szabályok,](./tutorial-create-telemetry-rules.md)az [](/learn/modules/manage-iot-central-apps-with-rest-api/) [adatexport](./howto-export-data.md)REST API és a nyilvános alkalmazások példák arra, hogyan integrálhatók IoT Central üzletági alkalmazásokkal:

![Hogyan IoT Central az IoT-adatokat?](media/overview-iot-central/transform.png)

Olyan üzleti elemzéseket hozhat létre, mint a gépi hatékonysági trendek meghatározása vagy a gyárak energiafelhasználásának előrejelzése egyéni elemzési folyamatok építésével az eszközök telemetriai adatainak feldolgozásához és az eredmények tárolására. Konfigurálhatja az adatexportációt az IoT Central-alkalmazásban, hogy exportálja a telemetriai adatokat, az eszköztulajdonság változásait és az eszközsablon módosításait más szolgáltatásokba, ahol az adatokat elemezheti, tárolhatja és vizualizálhatja a kívánt eszközökkel.

### <a name="build-custom-iot-solutions-and-integrations-with-the-rest-apis"></a>Egyéni IoT-megoldások és -integrációk létrehozása REST API-okkal

IoT-megoldások, például:

- Mobil társalkalmazások, amelyek távolról állíthatnak be és vezérelnek eszközöket.
- Egyéni integrációk, amelyek lehetővé teszik a meglévő üzletági alkalmazások számára az IoT-eszközökkel és -adatokkal való interakciót.
- Eszközkezelési alkalmazások eszközmodellezéshez, beléptethez, felügyelethez és adateléréshez.

## <a name="administer-your-application"></a>Alkalmazás felügyelése

IoT Central alkalmazások teljes körűen a Microsoft által vannak üzemeltetve, ami csökkenti az alkalmazások felügyeletével kapcsolatos adminisztrációs többletterhelést. A rendszergazdák felhasználói szerepkörökkel és engedélyekkel kezelik az [alkalmazáshoz való hozzáférést.](howto-administer.md)

## <a name="pricing"></a>Díjszabás

Létrehozhat egy IoT Central 7 napos ingyenes próbaverzióval, vagy standard tarifacsomagot is használhat.

- Az ingyenes csomag  használatával létrehozott alkalmazások hét napig ingyenesek, és legfeljebb öt eszközt támogatnak. A lejáratuk előtt bármikor átalakíthatja őket standard tarifacsomag használatára.
- A standard csomaggal létrehozott alkalmazások számlázása eszközönként történik. Választhat, hogy **Standard 0,** **Standard 1** vagy **Standard 2** tarifacsomagot használ, és az első két eszköz ingyenes.  További információ a [IoT Central díjszabásról.](https://aka.ms/iotcentral-pricing)

## <a name="quotas"></a>Kvóták

Minden Azure-előfizetés alapértelmezett kvótákkal rendelkezik, amelyek hatással lehetnek az IoT-megoldás hatókörre. Jelenleg IoT Central 10-re korlátozza az előfizetésben üzembe helyezhető alkalmazások számát. Ha növelnie kell ezt a korlátot, forduljon a [Microsoft ügyfélszolgálatához.](https://azure.microsoft.com/support/options/)

## <a name="known-issues"></a>Ismert problémák

- A folyamatos adatexportozás nem támogatja az Avro formátumot (inkompatibilitás).
- A GeoJSON jelenleg nem támogatott.
- A térképcsempe jelenleg nem támogatott.
- A tömbsémák típusai nem támogatottak.
- Csak a C eszközoldali SDK és Node.js eszköz- és szolgáltatásoldali SDK-k támogatottak.
- IoT Central jelenleg a következő helyeken Egyesült Államok, Európában, Ázsia és a Csendes-óceáni térség, Ausztráliában, az Egyesült Királyságban és Japánban.

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekinti a IoT Central, íme néhány javasolt következő lépés:

- Ha Ön eszközfejlesztő, és szeretne belemerülni néhány kódba, a következő javasolt lépés egy ügyfélalkalmazás létrehozása és csatlakoztatása a Azure IoT Central [alkalmazáshoz.](./tutorial-connect-device.md)
- Ismerkedés az [Azure IoT Central kezelőfelületével](overview-iot-central-tour.md).
- [Egy Azure IoT Central-alkalmazás létrehozásának](quick-deploy-iot-central.md) első lépései.

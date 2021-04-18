---
title: Mi az az Azure IoT Central? | Microsoft Docs
description: IoT Central egy biztonságos üzemeltetett IoT-alkalmazásplatform, amely a vállalat növekedésével skálázható, és integrálható a meglévő üzleti alkalmazásokkal. Ez a cikk áttekintést nyújt az Azure IoT Central jellemzőiről.
author: vishwam
ms.author: vishwams
ms.date: 04/16/2021
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc, contperf-fy21q2
ms.openlocfilehash: 1ff8243f9f4d070592ae6dc99e7c33472a6a90de
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600442"
---
# <a name="what-is-azure-iot-central"></a>Mi az az Azure IoT Central?

IoT Central egy biztonságos üzemeltetett IoT-alkalmazásplatform, amely a vállalat növekedésével skálázható, és integrálható a meglévő üzleti alkalmazásokkal. A IoT Central választásával időt, pénzt és energiát összpontosíthat az üzlet IoT-adatokkal való átalakítására egy összetett és folyamatosan fejlődő IoT-infrastruktúra karbantartása és frissítése helyett.

IoT Central segítségével gyorsan csatlakoztathat eszközöket, figyelheti az eszközfeltételeket, szabályokat hozhat létre, és eszközök millióit és adatait kezelheti az életciklusuk során. Emellett lehetővé teszi az eszközelemzések alapján történő tevékenységhez az IoT-intelligencia üzletági alkalmazásokra való kiterjesztését.

## <a name="create-your-iot-central-application"></a>Saját IoT Central létrehozása

Gyorsan létrehozhat egy új IoT Central, majd testreszabhatja az egyedi igényeinek megfelelően. Kezdhet egy általános alkalmazássablonnal, vagy az egyik iparág-központú alkalmazássablonnal a [Kiskereskedelmi,](../retail/overview-iot-central-retail.md)  Energiaipari, [Kormányzati](../government/overview-iot-central-government.md)vagy [Egészségügyi szolgáltatásokhoz.](../healthcare/overview-iot-central-healthcare.md) [](../energy/overview-iot-central-energy.md)

Az első [alkalmazás létrehozásához](quick-deploy-iot-central.md) tekintse meg az új alkalmazás létrehozása rövid útmutatót.

## <a name="connect-your-devices"></a>Az eszközök csatlakoztatása
Az alkalmazás létrehozása után az első lépés az eszközök csatlakoztatása. Az eszközök [saját alkalmazáshoz](./overview-iot-central-developer.md) való csatlakoztatásának bemutatásáért tekintse meg az eszközfejlesztés IoT Central áttekintését.

### <a name="device-templates"></a>Eszközsablonok

A IoT Central egy eszközsablonhoz _vannak társítva._ Az eszközsablonok a tervekhez hasonlóak: az eszközök jellemzőit és viselkedését határozzák meg, például:

- Telemetriai adatok, amelyek az érzékelőktől származó méréseket, például hőmérsékletet vagy páratartalmat ábrázolnak.
- Az eszköz tartós állapotát jelképező tulajdonságok. Ilyen például a hűtők szivattyú állapota vagy az eszköz célhőmérséklete. A tulajdonságokat deklarálhatja csak olvashatóként vagy írhatóként. Csak az eszközök frissítheti a csak olvasható tulajdonság értékét. Az operátor beállíthatja az eszközre küldhető írható tulajdonság értékét.
- Az eszközön aktiválható parancsok, műveletek, például egy parancs, amely távolról újraindít egy eszközt.
- Felhőtulajdonságok, amelyek a felhőalkalmazásban tárolható eszközmetaadatok IoT Central például az ügyfél címe vagy az utolsó szervizelt dátum.

További információt [az Eszközsablon létrehozása cikkben](howto-set-up-template.md) talál.

### <a name="customize-the-ui"></a>A felhasználói felület testreszabása

Az alkalmazás IoT Central testre szabható az alkalmazás napi használatáért felelős operátorok számára, például:

- Egyéni irányítópultok konfigurálása, amelyek segítik az operátorokat a megállapítások felismerésében és a problémák gyorsabb megoldásában.


## <a name="manage-your-devices"></a>Eszközök kezelése


A nagy léptékű működésre tervezett IoT-megoldásoknál fontos az eszközkezelés strukturált megközelítése. Nem elég csupán csatlakoztatni az eszközöket a felhőhöz, hanem az eszközök csatlakoztatott és kifogástalan állapotúnak kell tartania.

Az eszközöket [a saját alkalmazásával](howto-manage-devices.md) IoT Central, például a következő feladatok elvégzésére:

- Az eszközök figyelése.
- Az eszközök hibáinak keresése és elhárítása.
- Tömeges frissítések végrehajtása az eszközökön.

### <a name="dashboards"></a>Irányítópultok

A beépített [irányítópultok testre](./howto-set-up-template.md#generate-default-views) szabható felhasználói felületet biztosítanak az eszközök állapotának és telemetriának figyelése érdekében. Kezdje egy előre létrehozott irányítópulttal egy alkalmazássablonban, [](howto-use-app-templates.md) vagy hozzon létre saját, az operátorok igényeihez igazított irányítópultokat. Az irányítópultokat megoszthatja az alkalmazás összes felhasználójának, vagy megtarthatja őket privátként.

### <a name="rules-and-actions"></a>Szabályok és műveletek

Az [eszközállapot és](tutorial-create-telemetry-rules.md) a telemetria alapján egyéni szabályokat hozhat létre a figyelmet igényelő eszközök azonosításához. Konfigurálja a megfelelő személyeket értesítő műveleteket, és biztosítsa a korrekciós intézkedések időben történő megtételét.

### <a name="jobs"></a>Feladatok

[A](howto-run-a-job.md) feladatok segítségével egy- vagy tömeges frissítéseket alkalmazhat az eszközökre tulajdonságok beállításával vagy parancsok hívásával.

### <a name="analytics"></a>Elemzés
[Az](howto-create-analytics.md) elemzés számos képességet biztosít az előzménytrendek elemzéséhez és az eszközök különböző telemetrikáihoz való korrelál.

## <a name="integrate-with-other-services"></a>Integráció más szolgáltatásokkal

Alkalmazásplatformként a IoT Central lehetővé teszi az IoT-adatok üzleti elemzésekké való átalakítását, amelyek a beavatkozást lehetővé tő eredményekhez vezetnek. [A szabályok,](./tutorial-create-telemetry-rules.md)az [](/learn/modules/manage-iot-central-apps-with-rest-api/) [adatexport](./howto-export-data.md)REST API és a nyilvános alkalmazások példák arra, hogyan integrálhatók IoT Central üzletági alkalmazásokkal:

![Hogyan IoT Central az IoT-adatokat?](media/overview-iot-central/transform.png)

Olyan üzleti elemzéseket hozhat létre, mint a gépi hatékonysági trendek meghatározása vagy a gyár területén a jövőbeli energiafelhasználás előrejelzése egyéni elemzési folyamatok építésével az eszközök telemetriai adatainak feldolgozásához és az eredmények tárolására. Konfigurálja az adatexportációt a IoT Central alkalmazásban, hogy más szolgáltatásokba exportálja az adatokat, ahol elemezheti, tárolhatja és vizualizálhatja őket a kívánt eszközökkel.

### <a name="build-custom-iot-solutions-and-integrations-with-the-rest-apis"></a>Egyéni IoT-megoldások és -integrációk létrehozása REST API-okkal

IoT-megoldások, például:

- Mobil társalkalmazások, amelyek távolról állíthatják be és vezérelik az eszközöket.
- Egyéni integrációk, amelyek lehetővé teszik a meglévő üzletági alkalmazások számára az IoT-eszközökkel és -adatokkal való interakciót.
- Eszközkezelési alkalmazások eszközmodellezéshez, beléptetéshez, felügyelethez és adateléréshez.

## <a name="administer-your-application"></a>Alkalmazás felügyelése

IoT Central alkalmazások teljes mértékben a Microsoft által vannak üzemeltetve, ami csökkenti az alkalmazások felügyeletével kapcsolatos adminisztrációs többletterhelést. A rendszergazdák felhasználói szerepkörökkel és engedélyekkel kezelik az [alkalmazáshoz való hozzáférést.](howto-administer.md)

## <a name="pricing"></a>Díjszabás

Létrehozhat egy IoT Central egy 7 napos ingyenes próbaverzióval, vagy használhat standard tarifacsomagot.

- Az ingyenes csomag  használatával létrehozott alkalmazások hét napig ingyenesek, és legfeljebb öt eszközt támogatnak. A lejáratuk előtt bármikor átalakíthatja őket standard tarifacsomag használatára.
- A standard csomag *használatával* létrehozott alkalmazások számlázása eszközönként történik. Választhat a **Standard 0,** **a Standard 1** vagy a **Standard 2** tarifacsomag közül, ha az első két eszköz ingyenes. További információ a [IoT Central díjszabásról.](https://aka.ms/iotcentral-pricing)

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekinti a IoT Central, íme néhány javasolt következő lépés:

- [Egy Azure IoT Central-alkalmazás létrehozásának](quick-deploy-iot-central.md) első lépései.
- Ismerkedés az [Azure IoT Central kezelőfelületével](overview-iot-central-tour.md).
- Ha Ön eszközfejlesztő, és szeretne belemerülni néhány kódba, hozzon létre és kössön össze egy ügyfélalkalmazást [a Azure IoT Central alkalmazással.](./tutorial-connect-device.md)

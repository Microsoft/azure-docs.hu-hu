---
title: Új IoT-eszköz típusának meghatározása az Azure IoT Centralban | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan hozhat létre új Azure IoT-sablont az Azure IoT Central-alkalmazásban. Megadhatja a típus telemetria, állapotát, tulajdonságait és parancsait.
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom:
- contperf-fy21q1
- device-developer
ms.openlocfilehash: 22e948a0100f23dbddef8fc138576bb4b9372c77
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100363202"
---
# <a name="define-a-new-iot-device-type-in-your-azure-iot-central-application"></a>Új IoT-eszköztípus definiálása az Azure IoT Central-alkalmazásban

*Ez a cikk a megoldás-építők és az eszközök fejlesztőire vonatkozik.*

Az eszköz sablonja egy olyan terv, amely meghatározza egy [Azure IoT Central-alkalmazáshoz](concepts-app-templates.md)csatlakozó eszköz típusának jellemzőit és viselkedését.

A Builder például létrehozhat egy eszköz sablont egy csatlakoztatott ventilátorhoz, amely a következő jellemzőkkel rendelkezik:

- Hőmérséklet telemetria küld
- Location tulajdonság küldése
- Ventilátor motoros hibák eseményeinek küldése
- Ventilátor működési állapotának küldése
- Egy írható ventilátor sebességét biztosító tulajdonságot biztosít
- Az eszköz újraindítására szolgáló parancsot biztosít
- Általános áttekintést nyújt az eszközről egy nézet használatával

Az eszköz sablonja alapján a kezelők valódi ventilátoros eszközöket hozhatnak létre és csatlakozhatnak. Ezek a ventilátorok olyan mérésekkel, tulajdonságokkal és parancsokkal rendelkeznek, amelyeket az operátorok a figyelésre és felügyeletre használnak. A kezelők az eszközök [nézeteit](#add-views) és űrlapait használják a ventilátorral való interakcióhoz. Az eszközök fejlesztői a sablont használják annak megismeréséhez, hogy az eszköz hogyan kommunikál az alkalmazással. További információ: [telemetria, Property és Command hasznos](concepts-telemetry-properties-commands.md)adatok.

> [!NOTE]
> Csak az építők és a rendszergazdák hozhatnak létre, szerkeszthetnek és törölhetnek eszközöket. Bármely felhasználó létrehozhat eszközöket az **eszközök** lapon a meglévő eszközök sablonjaiból.

Egy IoT Central alkalmazásban az eszköz egy eszköz modelljét használja az eszköz képességeinek leírására. A Builder számos lehetőséget kínál az eszközök sablonjainak létrehozására:

- Tervezze meg IoT Central az eszköz sablonját, majd [implementálja az eszköz modelljét az eszköz kódjában](concepts-telemetry-properties-commands.md).
- Importáljon egy sablont az [Azure Certified for IoT-Device Catalog](https://aka.ms/iotdevcat)eszközre. Szabja testre az eszköz sablonját a IoT Central igényeinek megfelelően.
> [!NOTE]
> IoT Central a teljes modellt az azonos fájlban lévő összes hivatkozott csatolóval együtt kell megadnia, amikor egy modellt importál a modell-adattárból, a "kibontott" kulcsszó használatával szerezheti be a teljes verziót.
Például:. https://devicemodels.azure.com/dtmi/com/example/thermostat-1.expanded.json

- Az eszköz modelljét a [digitális Twins Definition Language (DTDL) – 2. verzió](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)használatával hozhatja létre. A Visual Studio Code olyan bővítményt tartalmaz, amely támogatja a DTDL-modellek készítését. További információ: [install and use The DTDL authoring Tools](../../iot-pnp/howto-use-dtdl-authoring-tools.md). Ezután tegye közzé a modellt a nyilvános modell adattárában. További információ: [eszköz modell tárháza](../../iot-pnp/concepts-model-repository.md). Implementálja az eszköz kódját a modellből, és kapcsolja össze a valódi eszközt a IoT Central alkalmazással. IoT Central megkeresi és importálja az eszköz modelljét a nyilvános adattárból, és létrehoz egy sablont. Ezután hozzáadhat bármilyen Felhőbeli tulajdonságot, testreszabást, és megtekintheti az IoT Central alkalmazásnak az eszköz sablonját.
- Egy eszköz modell létrehozása a DTDL használatával. Implementálja az eszköz kódját a modellből. Manuálisan importálja az eszköz modelljét a IoT Central alkalmazásba, majd adja hozzá a IoT Central alkalmazás igényeinek megfelelő Felhőbeli tulajdonságokat, testreszabásokat és nézeteket.

> [!TIP]
> IoT Central a teljes modellt az összes hivatkozott csatolóval megegyező fájlban kell megadnia. Ha modellt importál a modell-adattárból, a *kibontott* kulcsszó használatával szerezheti be a teljes verziót.
> Például: [https://devicemodels.azure.com/dtmi/com/example/thermostat-1.expanded.json](https://devicemodels.azure.com/dtmi/com/example/thermostat-1.expanded.json).

Az IoT Central alkalmazáshoz a [REST API](/learn/modules/manage-iot-central-apps-with-rest-api/) vagy a [parancssori](howto-manage-iot-central-from-cli.md)felület használatával is hozzáadhat eszközillesztőket.

Egyes [alkalmazás-sablonok](concepts-app-templates.md) már tartalmaznak olyan eszközöket, amelyek hasznosak lehetnek abban az esetben, amikor az alkalmazás sablonja támogatja. Lásd például: [in-store Analytics Architecture](../retail/store-analytics-architecture.md).

## <a name="create-a-device-template-from-the-device-catalog"></a>Eszköz sablonjának létrehozása az eszköz-katalógusból

A Builder segítségével gyorsan megkezdheti a megoldás kiépítését egy hitelesített eszköz használatával. Tekintse meg a listát az [Azure IoT-eszköz katalógusában](https://catalog.azureiotsolutions.com/alldevices). IoT Central integrálódik az eszköz-katalógussal, így bármely hitelesített eszközről importálhatja az eszköz modelljét. Eszköz sablonjának létrehozása ezen eszközök egyikéről a IoT Centralban:

1. Nyissa meg az IoT Central alkalmazás **eszköz sablonok** lapját.
1. Válassza az **+ új** lehetőséget, majd a katalógusból válassza ki a hitelesített eszközök egyikét. IoT Central létrehoz egy, az eszköz modellje alapján létrehozott sablont.
1. Bármilyen Felhőbeli tulajdonságot, testreszabást és nézetet hozzáadhat az eszköz sablonhoz.
1. Válassza a **Közzététel** lehetőséget, hogy a sablon elérhető legyen az operátorok számára az eszközök megtekintéséhez és csatlakoztatásához.

## <a name="create-a-device-template-from-scratch"></a>Sablon létrehozása a semmiből

Az eszköz sablonjai A következőket tartalmazzák:

- Egy _eszköz modellje_ , amely az eszköz által megvalósított telemetria, tulajdonságokat és parancsokat határozza meg. Ezek a képességek egy vagy több összetevőbe vannak rendezve.
- A _felhő tulajdonságai_ , amelyek a IoT Central alkalmazás által az eszközökön tárolt adatokat határozzák meg. Előfordulhat például, hogy egy Felhőbeli tulajdonság rögzíti az eszköz legutóbbi kiszolgálásának dátumát. Ezeket az adatokat soha nem osztja meg az eszközzel.
- A _testreszabások_ lehetővé teszik, hogy a szerkesztő felülbírálja az eszköz modellje definícióit. A szerkesztő például felülbírálhatja egy eszköz tulajdonságának a nevét. A tulajdonságok nevei IoT Central nézetekben és űrlapokban jelennek meg.
- A _nézetek és az űrlapok_ lehetővé teszik, hogy a szerkesztő olyan felhasználói felületet hozzon létre, amely lehetővé teszi, hogy a kezelők az alkalmazáshoz csatlakoztatott eszközöket figyeljenek

Eszköz sablonjának létrehozása a IoT Centralban:

1. Nyissa meg az IoT Central alkalmazás **eszköz sablonok** lapját.
1. Válassza az **+ új**  >  **IoT eszközt**. Ezután válassza a **Tovább: testreszabás** lehetőséget.
1. Adja meg a sablon nevét, például a **termosztátot**. Ezután válassza a **Tovább: Áttekintés** , majd a **Létrehozás** lehetőséget.
1. IoT Central létrehoz egy üres sablont, és lehetővé teszi, hogy egyéni modellt hozzon létre a semmiből, vagy importáljon egy DTDL modellt.

## <a name="manage-a-device-template"></a>Eszköz sablonjának kezelése

A sablon kezdőlapján átnevezheti vagy törölheti a sablonokat.

Miután hozzáadta az eszköz modelljét a sablonhoz, közzéteheti azt. Amíg nem tette közzé a sablont, nem tud csatlakozni az eszközhöz a sablon alapján, hogy az operátorok megjelenjenek az **eszközök** lapon.

## <a name="create-a-capability-model"></a>Képesség modell létrehozása

Az eszköz modell létrehozásához a következőket teheti:

- A IoT Central használatával hozzon létre egy egyéni modellt a semmiből.
- DTDL-modell importálása JSON-fájlból. Előfordulhat, hogy egy eszköz-szerkesztő a Visual Studio Code használatával létrehozta az alkalmazáshoz tartozó eszköz modelljét.
- Válasszon egy eszközt az eszköz-katalógusból. Ezzel a beállítással importálhatja azt az eszköz-modellt, amelyet a gyártó közzétett az eszközön. Az ehhez hasonló módon importált eszköz automatikusan közzé lesz téve.

## <a name="manage-a-capability-model"></a>Képesség modell kezelése

Az eszköz modell létrehozása után a következőket teheti:

- Összetevők hozzáadása a modellhez. A modellnek rendelkeznie kell legalább egy összetevővel.
- Szerkessze a modell metaadatait, például az azonosítót, a névteret és a nevet.
- Törölje a modellt.

## <a name="create-a-component"></a>Összetevő létrehozása

Az eszköz modelljében legalább egy alapértelmezett összetevőnek szerepelnie kell. Az összetevő a képességek újrafelhasználható gyűjteménye.

Összetevő létrehozása:

1. Nyissa meg az eszköz modelljét, és válassza az **+ összetevő hozzáadása elemet**.

1. Az **összetevő-felület hozzáadása** lapon a következőket teheti:

    - Hozzon létre egy teljesen új egyéni összetevőt.
    - Meglévő összetevő importálása DTDL-fájlból. Előfordulhat, hogy egy eszközön a Visual Studio Code használatával létrehozták az eszközhöz tartozó összetevő-felületet.

1. Miután létrehozta az összetevőt, az **identitás szerkesztése** elemre kattintva módosíthatja az összetevő megjelenítendő nevét.

1. Ha úgy dönt, hogy teljesen új egyéni összetevőt hoz létre, felveheti az eszköz képességeit. Az eszköz képességei a következők: telemetria, tulajdonságok és parancsok.

### <a name="telemetry"></a>Telemetria

A telemetria az eszközről küldött értékek streamje, jellemzően egy érzékelőből. Egy érzékelő például jelenthetheti a környezeti hőmérsékletet.

A következő táblázat a telemetria képesség konfigurációs beállításait mutatja be:

| Mező | Leírás |
| ----- | ----------- |
| Megjelenítendő név | A nézetekben és űrlapokon használt telemetria érték megjelenítendő neve. |
| Name | A mező neve a telemetria üzenetben. IoT Central a megjelenített név alapján létrehoz egy értéket a mezőhöz, de szükség esetén kiválaszthatja a saját értékét is. A mezőnek alfanumerikusnak kell lennie. |
| Képesség típusa | Telemetria. |
| Szemantikai típus | A telemetria szemantikai típusa, például hőmérséklet, állapot vagy esemény. A szemantikai típus megválasztása határozza meg, hogy a következő mezők közül melyek érhetők el. |
| Séma | A telemetria adattípus, például Double, string vagy Vector. Az elérhető beállításokat a szemantikai típus határozza meg. A séma nem érhető el az esemény és az állapot szemantikai típusaihoz. |
| Súlyosság | Csak az esemény szemantikai típusához érhető el. A megszakítások a következők: **hiba**, **információ** vagy **Figyelmeztetés**. |
| Állapot értékei | Csak az állapot szemantikai típusához érhető el. Definiálja a lehetséges állapotinformációkat, amelyek mindegyike megjelenített névvel, névvel, számbavételi típussal és értékkel rendelkezik. |
| Unit (Egység) | A telemetria értékének (például: **mph**, **%** , vagy **&deg; C**) egysége. |
| Megjelenítési egység | Nézeteken és űrlapokon használható megjelenítési egység. |
| Megjegyzés | A telemetria képességgel kapcsolatos megjegyzések. |
| Description | A telemetria képesség leírása. |

### <a name="properties"></a>Tulajdonságok

A tulajdonságok a pont – idő értékeket jelölik. Egy eszköz használhat például egy tulajdonságot a elérni kívánt cél hőmérséklet jelentésére. Az írható tulajdonságokat IoT Central lehet beállítani.

A következő táblázat a tulajdonságok funkciójának konfigurációs beállításait mutatja be:

| Mező | Leírás |
| ----- | ----------- |
| Megjelenítendő név | A nézeteken és űrlapokon használt tulajdonságérték megjelenítendő neve. |
| Name | A tulajdonság neve. IoT Central a megjelenített név alapján létrehoz egy értéket a mezőhöz, de szükség esetén kiválaszthatja a saját értékét is. A mezőnek alfanumerikusnak kell lennie. |
| Képesség típusa | Tulajdonság. |
| Szemantikai típus | A tulajdonság szemantikai típusa, például hőmérséklet, állapot vagy esemény. A szemantikai típus megválasztása határozza meg, hogy a következő mezők közül melyek érhetők el. |
| Séma | A tulajdonság adattípusa, például Double, string vagy Vector. Az elérhető beállításokat a szemantikai típus határozza meg. A séma nem érhető el az esemény és az állapot szemantikai típusaihoz. |
| Írható | Ha a tulajdonság nem írható, az eszköz jelentést készíthet IoT Central. Ha a tulajdonság írható, az eszköz jelentést készíthet IoT Central, és IoT Central a tulajdonságok frissítését az eszközre.
| Súlyosság | Csak az esemény szemantikai típusához érhető el. A megszakítások a következők: **hiba**, **információ** vagy **Figyelmeztetés**. |
| Állapot értékei | Csak az állapot szemantikai típusához érhető el. Definiálja a lehetséges állapotinformációkat, amelyek mindegyike megjelenített névvel, névvel, számbavételi típussal és értékkel rendelkezik. |
| Unit (Egység) | A tulajdonság értékének egysége, például: **mph**, **%** , vagy **&deg; C**. |
| Megjelenítési egység | Nézeteken és űrlapokon használható megjelenítési egység. |
| Megjegyzés | A tulajdonság képességével kapcsolatos megjegyzések. |
| Description | A tulajdonság funkciójának leírása. |

### <a name="commands"></a>Parancsok

A IoT Central eszköz parancsai hívhatók. A parancsok opcionálisan továbbítják a paramétereket az eszköznek, és választ kapnak az eszköztől. Meghívhat például egy parancsot 10 másodperc alatt egy eszköz újraindítására.

A következő táblázat a parancs funkciójának konfigurációs beállításait mutatja be:

| Mező | Leírás |
| ----- | ----------- |
| Megjelenítendő név | A nézeteken és űrlapokon használt parancs megjelenítendő neve. |
| Name | A parancs neve. IoT Central a megjelenített név alapján létrehoz egy értéket a mezőhöz, de szükség esetén kiválaszthatja a saját értékét is. A mezőnek alfanumerikusnak kell lennie. |
| Képesség típusa | Parancs. |
| Megjegyzés | A parancs képességével kapcsolatos megjegyzések. |
| Description | A parancs funkciójának leírása. |
| Kérés | Ha engedélyezve van, a kérelem paraméterének definíciója, beleértve a következőket: név, megjelenítendő név, séma, egység és megjelenítési egység. |
| Reagálás | Ha engedélyezve van, a parancs válaszának definíciója, beleértve a következőket: név, megjelenítendő név, séma, egység és megjelenítési egység. |

Ha többet szeretne megtudni arról, hogy az eszközök hogyan implementálják a parancsokat, tekintse meg a [telemetria, a Property és a Command hasznos adatokat > parancsokat és a hosszan futó parancsokat](concepts-telemetry-properties-commands.md#commands).

#### <a name="offline-commands"></a>Offline parancsok

Ha egy eszköz jelenleg offline állapotban van, a várólista-utasítások közül választhat, ha az eszközön **Offline** lehetőség van egy parancsra az eszköz sablonjában.

Ez a beállítás a IoT Hub felhőből az eszközre irányuló üzeneteket használja az értesítések küldéséhez az eszközökre. További információkért tekintse meg a IoT Hub a [felhőből az eszközre küldött üzenetek küldését](../../iot-hub/iot-hub-devguide-messages-c2d.md)ismertető cikket.

Felhőből az eszközre irányuló üzenetek:

- Az eszközre irányuló egyirányú értesítések a megoldásból.
- Legalább egyszeri üzenet kézbesítésének garantálása. IoT Hub a felhőből az eszközre irányuló üzeneteket az eszközönkénti várólistákban tartja fenn, így biztosítva a kapcsolat és az eszközök meghibásodása elleni rugalmasságot.
- A felhőből az eszközre irányuló üzenet feldolgozásának megkövetelése az eszközön egy üzenetkezelő megvalósításához.

> [!NOTE]
> Ez a beállítás csak a IoT Central webes felhasználói felületén érhető el. Ez a beállítás nem szerepel, ha az eszköz sablonjában exportál egy modellt vagy összetevőt.

## <a name="manage-a-component"></a>Összetevő kezelése

Ha még nem tette közzé az összetevőt, módosíthatja az összetevő által definiált képességeket. Miután közzétette az összetevőt, ha módosítani kívánja a módosításokat, létre kell hoznia az eszköz sablonjának új verzióját és [az összetevő verzióját](howto-version-device-template.md). A **Testreszabás** szakaszban olyan módosításokat végezhet, amelyek nem igényelnek verziószámozást, például megjelenítendő neveket vagy egységeket.

Azt is megteheti, hogy az összetevőt JSON-fájlként exportálja, ha újra szeretné használni egy másik képesség modellben.

## <a name="add-cloud-properties"></a>Felhőtulajdonságok hozzáadása

A felhő tulajdonságai a IoT Central lévő eszközök adatainak tárolására használhatók. A felhő tulajdonságai soha nem továbbítódnak az eszközre. Például a Cloud Properties használatával tárolhatja annak az ügyfélnek a nevét, aki az eszközt telepítette, vagy az eszköz utolsó szolgáltatásának dátuma.

A következő táblázat a Cloud Property konfigurációs beállításait mutatja be:

| Mező | Leírás |
| ----- | ----------- |
| Megjelenítendő név | A nézeteken és űrlapokon használt Cloud Property érték megjelenítendő neve. |
| Name | A felhő tulajdonság neve IoT Central a megjelenített név alapján létrehoz egy értéket a mezőhöz, de szükség esetén kiválaszthatja a saját értékét is. |
| Szemantikai típus | A tulajdonság szemantikai típusa, például hőmérséklet, állapot vagy esemény. A szemantikai típus megválasztása határozza meg, hogy a következő mezők közül melyek érhetők el. |
| Séma | A Felhőbeli tulajdonság adattípusa, például Double, string vagy Vector. Az elérhető beállításokat a szemantikai típus határozza meg. |

## <a name="add-customizations"></a>Testreszabások hozzáadása

Ha módosítania kell egy importált összetevőt, vagy IoT Central-specifikus funkciókat kell hozzáadnia egy képességhez, használja a testreszabásokat. Csak olyan mezőket szabhat testre, amelyek nem bontják le az összetevők kompatibilitását. Megteheti például a következőt:

- Testreszabhatja egy képesség megjelenített nevét és egységeit.
- Adja meg az alapértelmezett színt, amelyet akkor kell használni, ha az érték megjelenik a diagramon.
- Egy tulajdonság kezdeti, minimális és maximális értékének megadása.

Nem szabhatja testre a képesség nevét vagy a képesség típusát. Ha a **Testreszabás** szakaszban nem végez módosítást, akkor a funkció módosításához telepítenie kell az eszköz sablonját és összetevőjét.

### <a name="generate-default-views"></a>Alapértelmezett nézetek előállítása

Az alapértelmezett nézetek létrehozásával gyorsan megjelenítheti az eszköz fontos adatait. Az eszköz sablonjában legfeljebb három alapértelmezett nézet hozható létre:

- **Parancsok**: az eszköz parancsaival rendelkező nézet, amely lehetővé teszi, hogy az operátor az eszközre küldje őket.
- **Áttekintés**: az eszközök telemetria, diagramok és metrikák megjelenítésére szolgáló nézet.
- **Névjegy**: az eszköz adatait tartalmazó nézet, az eszköz tulajdonságainak megjelenítése.

Miután kiválasztotta az **alapértelmezett nézetek létrehozása** lehetőséget, láthatja, hogy az eszköz sablonjának **nézetek** szakaszában automatikusan hozzá lettek adva.

## <a name="add-views"></a>Nézetek hozzáadása

Nézetek hozzáadása egy sablonhoz, amely lehetővé teszi, hogy a kezelők diagramok és metrikák használatával jelenítsék meg az eszközöket. Az eszközök sablonjaihoz több nézet is tartozhat.

Nézet hozzáadása egy eszköz sablonhoz:

1. Nyissa meg az eszköz sablonját, és válassza a **nézetek** lehetőséget.
1. Válassza ki **az eszköz megjelenítését**.
1. Adja meg a nézet nevét a **nézet nevében**.
1. Csempe hozzáadása a nézethez a statikus, a tulajdonság, a Cloud Property, a telemetria és a Command csempék listájából. Húzza a nézetbe felvenni kívánt csempéket.
1. Ha több telemetria-értéket szeretne ábrázolni egyetlen diagramon, válassza ki a telemetria-értékeket, majd kattintson az **összevonás** elemre.
1. Konfigurálja az egyes hozzáadott csempéket az adatmegjelenítés testreszabásához. A beállítás eléréséhez válassza a fogaskerék ikont, vagy a diagram csempén a **konfiguráció módosítása** lehetőséget választva.
1. A csempék elrendezése és átméretezése a nézetben.
1. Mentse a módosításokat.

### <a name="configure-preview-device-to-view"></a>Előnézet eszköz konfigurálása a megtekintéshez

A nézet megtekintéséhez és teszteléséhez válassza az **előnézet eszköz konfigurálása** lehetőséget. Ez a funkció lehetővé teszi a nézet megtekintését, ahogy az operátor látja azt a közzététel után. Ezzel a szolgáltatással ellenőrizheti, hogy a nézetek a megfelelő adatokon jelenjenek-e meg. Az alábbi lehetőségek közül választhat:

- Nincs előnézeti eszköz.
- Az eszköz sablonja számára konfigurált valós tesztelési eszköz.
- Egy meglévő eszköz az alkalmazásban az eszköz AZONOSÍTÓjának használatával.

## <a name="add-forms"></a>Űrlapok hozzáadása

Űrlapokat adhat hozzá egy eszköz sablonhoz, hogy az operátorok a tulajdonságok megtekintésével és beállításával lehetővé tegyék az eszközök felügyeletét. Az operátorok csak a felhő tulajdonságait és az írható eszköz tulajdonságait módosíthatják. Az eszközök sablonjaihoz több űrlap is tartozhat.

Űrlap hozzáadása egy eszköz sablonhoz:

1. Nyissa meg az eszköz sablonját, és válassza a **nézetek** lehetőséget.
1. Válassza **az eszköz és a Felhőbeli adattárolás szerkesztése** lehetőséget.
1. Adja meg az űrlap nevét az **űrlap nevében**.
1. Válassza ki az űrlap elrendezéséhez használni kívánt oszlopok számát.
1. Adja hozzá a tulajdonságokat egy meglévő szakaszhoz az űrlapon, vagy válassza a tulajdonságok lehetőséget, és válassza a **Hozzáadás szakaszt**. Az űrlapon található tulajdonságok csoportosításához használjon szakaszt. Hozzáadhat egy címet egy szakaszhoz.
1. Konfigurálja az űrlap minden tulajdonságát, hogy testre szabja a viselkedését.
1. Rendezze a tulajdonságokat az űrlapon.
1. Mentse a módosításokat.

## <a name="publish-a-device-template"></a>Eszközsablon közzététele

Az eszköz modelljét implementáló eszköz csatlakoztatása előtt közzé kell tennie az eszköz sablonját.

Miután közzétett egy sablont, csak korlátozott módosításokat végezhet az eszköz modelljében. Egy összetevő módosításához [létre kell hoznia és közzé kell tennie egy új verziót](./howto-version-device-template.md).

Az eszköz közzétételéhez nyissa meg az eszköz sablonját, és válassza a **Közzététel** lehetőséget.

Miután közzétett egy sablont, az operátor megkeresheti az **eszközök** lapot, és hozzáadhat akár valódi, akár szimulált eszközöket, amelyek az eszköz sablonját használják. A módosítások végrehajtása során továbbra is módosíthatja és mentheti az eszköz sablonját. Ha ezeket a módosításokat a kezelőben a **Devices (eszközök** ) lapon szeretné megtekinteni, ki kell választania a **közzétételi** időt.

## <a name="next-steps"></a>Következő lépések

Ha Ön egy eszköz fejlesztője, javasolt következő lépésként olvassa el az [eszköz sablon verziószámozását](./howto-version-device-template.md)ismertető témakört.

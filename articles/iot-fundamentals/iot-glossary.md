---
title: Az Azure IoT szószedete – fogalmak | Microsoft Docs
description: Fejlesztői útmutató – a Szószedet ismerteti az Azure IoT-cikkekben használt általános kifejezéseket.
author: dominicbetts
ms.author: dobett
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: a45b8db1f17ead8e55bac2b2bf82aa44cb7f080e
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304698"
---
# <a name="glossary-of-iot-terms"></a>A IoT feltételeinek glosszáriuma

Ez a cikk a IoT-cikkekben használt általános kifejezéseket sorolja fel.

## <a name="a"></a>A

### <a name="advanced-message-queueing-protocol"></a>Speciális üzenetsor-kezelési protokoll

A [speciális üzenetsor-kezelési protokoll (AMQP)](https://www.amqp.org/) a [IoT hub](#iot-hub) által támogatott üzenetküldési protokollok egyike az eszközökkel való kommunikációhoz. További információ a IoT Hub által támogatott üzenetküldési protokollokról: [üzenetek küldése és fogadása IoT hub](../iot-hub/iot-hub-devguide-messaging.md)használatával.

### <a name="allocation-policy"></a>Kiosztási szabályzat

A [Device kiépítési szolgáltatásban](#device-provisioning-service)a foglalási házirend határozza meg, hogy a szolgáltatás hogyan rendeli hozzá az eszközöket a [csatolt IoT hubokhoz](#linked-iot-hub).

### <a name="attestation-mechanism"></a>Igazolási mechanizmus

Az [eszköz kiépítési szolgáltatásában](#device-provisioning-service)az igazolási mechanizmus az eszköz identitásának megerősítésére szolgál. Az igazolási mechanizmus egy [regisztráción](#enrollment)van konfigurálva.

Az igazolási mechanizmusok közé tartoznak az X. 509 tanúsítványok, a platformmegbízhatósági modulok és a szimmetrikus kulcsok.

### <a name="automatic-deployment"></a>Automatikus központi telepítés

IoT Edge az automatikus központi telepítés IoT Edge eszközök egy adott készletét konfigurálja IoT Edge modulok futtatásához. Az egyes központi telepítések folyamatosan biztosítják, hogy a célként megadott feltételnek megfelelő összes eszköz futtatja a modulok adott készletét, még akkor is, ha új eszközök jönnek létre vagy módosulnak a cél feltételnek megfelelően. Minden IoT Edge eszköz csak a legmagasabb prioritású telepítést kapja, amelynek a célja megfelel. További információ a [IoT Edge automatikus központi telepítéséről](../iot-edge/module-deployment-monitoring.md).

### <a name="automatic-device-configuration"></a>Eszköz automatikus konfigurálása

A megoldás háttérbe állítása [automatikus eszköz-konfigurációk](../iot-hub/iot-hub-automatic-device-management.md) használatával rendelhető hozzá a kívánt tulajdonságok az [eszközök](#device-twin) egy készletéhez és a jelentés állapotához a rendszermetrikák és az egyéni metrikák használatával.

### <a name="automatic-device-management"></a>Automatikus eszközkezelés

Az Azure-ban az automatikus eszközkezelés IoT Hub automatizálja a nagyméretű eszközök flottáinak teljes életciklusával kapcsolatos ismétlődő és összetett feladatokat. Az automatikus eszközkezelés lehetővé teszi, hogy a tulajdonságok alapján csoportosítsa az eszközöket, Definiáljon egy kívánt konfigurációt, és hagyja IoT Hub frissíteni az eszközöket, amikor azok hatókörbe kerülnek.  [Automatikus eszköz-konfigurációkból](../iot-hub/iot-hub-automatic-device-management.md) és [IoT Edge automatikus központi telepítésből](../iot-edge/how-to-deploy-at-scale.md)áll.

### <a name="azure-digital-twins"></a>Azure Digital Twins

Az Azure Digital Twins egy szolgáltatásként nyújtott platform, amely a valós dolgok, helyek, üzleti folyamatok és személyek digitális ábrázolásának létrehozására szolgál. Hozzon létre a teljes környezeteket képviselő tudásbázisokat, és használja őket a jobb termékek megszerzéséhez, a műveletek és a költségek optimalizálásához, valamint a felhasználói élmények kihasználásához. További információ: [Azure digitális Twins](../digital-twins/index.yml).

### <a name="azure-digital-twins-instance"></a>Azure digitális Twins-példány

Az Azure Digital Twins szolgáltatás egyetlen példánya az ügyfél előfizetésében. Míg az [Azure Digital Twins](#azure-digital-twins) az Azure-szolgáltatás egészére utal, az Azure Digital Twins- **példány** az Ön egyedi Azure digitális Twins-erőforrása.

### <a name="azure-iot-device-sdks"></a>Azure IoT-eszközök SDK-k

Több nyelven is elérhetők az _eszközök SDK_ -k, amelyek lehetővé teszik, hogy az IoT-hubhoz interaktív [eszközöket](#device-app) hozzon létre. A IoT Hub oktatóanyagok bemutatják, hogyan használhatja ezeket az eszköz SDK-kat. Ebben a GitHub- [tárházban](https://github.com/Azure/azure-iot-sdks)megtalálja a forráskódot és további információkat az eszköz SDK-ról.

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

Az [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer) használatával megtekintheti az eszköz által küldött telemetria, az eszköz tulajdonságait és a hívási parancsokat. A Explorerrel is használhatja az eszközöket, és tesztelheti az eszközöket, és felügyelheti a [IoT Plug and Play-eszközöket](#iot-plug-and-play-device).

### <a name="azure-iot-service-sdks"></a>Azure IoT Service SDK-k

Több nyelven is elérhetők olyan _szolgáltatási SDK_ -k, amelyek lehetővé teszik, hogy az IoT-hubhoz interaktív [háttérbeli alkalmazásokat](#back-end-app) hozzon létre. A IoT Hub oktatóanyagok bemutatják, hogyan használhatja ezeket a szolgáltatási SDK-kat. Ebben a GitHub- [tárházban](https://github.com/Azure/azure-iot-sdks)megtalálja a forráskódot és a szolgáltatás SDK-k további információit.

### <a name="azure-iot-tools"></a>Azure IoT-eszközök

Az [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) egy platformfüggetlen, nyílt forráskódú Visual Studio Code-bővítmény, amely segít az Azure-IoT hub és-eszközök felügyeletében a vs Code-ban. Az Azure IoT Tools révén a IoT-fejlesztők könnyedén hozhatnak létre IoT-projekteket a VS Code-ban.

## <a name="b"></a>B

### <a name="back-end-app"></a>Háttérbeli alkalmazás

[IoT hub](#iot-hub)kontextusában a háttérbeli alkalmazás egy olyan alkalmazás, amely egy IoT hub szolgáltatáshoz kapcsolódó végpontokhoz csatlakozik. A háttérbeli alkalmazások például lekérhetik az [eszközről a felhőbe irányuló](#device-to-cloud) üzeneteket, vagy kezelhetik az [identitás-beállításjegyzéket](#identity-registry). A háttérben futó alkalmazások jellemzően a felhőben futnak, de számos oktatóanyagban a háttérbeli alkalmazások a helyi fejlesztési gépen futó konzolos alkalmazások.

### <a name="built-in-endpoints"></a>Beépített végpontok

IoT Hubba beépített [végpont](#endpoint) típusa. Minden IoT hub tartalmaz egy beépített [végpontot](../iot-hub/iot-hub-devguide-endpoints.md) , amely az Event hub-kompatibilis. Az eszközről a felhőbe irányuló üzenetek a végpontról való olvasásához használhatja a Event Hubs-vel használható bármely mechanizmust.

## <a name="c"></a>C

### <a name="cloud-gateway"></a>Felhőbeli átjáró

A Cloud Gateway lehetővé teszi a csatlakozást olyan eszközökhöz, amelyek nem tudnak közvetlenül csatlakozni a [IoT hubhoz](#iot-hub). A felhőben üzemelő átjárók a felhőben futnak, szemben egy helyi [átjáróval](#field-gateway) , amely az eszközein fut. A Felhőbeli átjáró tipikus használati esete a protokollok fordításának megvalósítása az eszközökön.

### <a name="cloud-to-device"></a>Felhőből eszközre

Egy IoT hub-ból egy csatlakoztatott eszközre küldött üzenetekre hivatkozik. Ezek az üzenetek gyakran olyan parancsok, amelyek arra utasítja az eszközt, hogy végezze el a műveletet. További információ: [üzenetek küldése és fogadása IoT Hubsal](../iot-hub/iot-hub-devguide-messaging.md).

### <a name="commands"></a>Parancsok

A IoT-Plug and Play az [illesztőfelületben](#interface) definiált parancsok olyan metódusokat jelölnek, amelyek a [digitális iker](#digital-twin)-on hajthatók végre. Például egy eszköz újraindítására szolgáló parancs.

### <a name="component"></a>Összetevő

A IoT Plug and Play és az Azure Digital Twins szolgáltatásban az összetevők lehetővé teszik, hogy más felületek szerelvényként építsenek modell- [felületet](#interface) . Az [eszköz modelljei](#device-model) több felületet is kombinálhatók összetevőkként. Előfordulhat például, hogy egy modell egy kapcsoló összetevőt és egy termosztát-összetevőt is tartalmaz. Egy modellben több összetevő is használhatja ugyanazt az illesztőfelület-típust. Előfordulhat például, hogy egy modell két termosztát-összetevőt tartalmaz.

### <a name="configuration"></a>Konfiguráció

Az [automatikus eszköz-konfiguráció](../iot-hub/iot-hub-automatic-device-management.md)kontextusában a IoT hub belüli konfiguráció határozza meg az eszközök egy készletének kívánt konfigurációját, és mérőszámokat biztosít az állapot és a folyamat jelentéséhez.

### <a name="connection-string"></a>Kapcsolati sztring

Az alkalmazás kódjában kapcsolati karakterláncokat használ a végponthoz való kapcsolódáshoz szükséges információk beágyazásához. A kapcsolatok karakterlánca általában tartalmazza a végpontok és a biztonsági információk címeit, de a kapcsolatok karakterlánc-formátumai eltérőek a szolgáltatások között. A IoT Hub szolgáltatáshoz két típusú kapcsolattípus van társítva:

- Az *eszköz kapcsolati karakterláncai* lehetővé teszik az eszközök számára az IoT hub eszközre irányuló végpontokhoz való kapcsolódását.

- *IoT hub kapcsolati karakterláncok* lehetővé teszik a háttérbeli alkalmazások számára, hogy csatlakozzanak a IoT hub szolgáltatáshoz csatlakozó végpontokhoz.

### <a name="custom-endpoints"></a>Egyéni végpontok

Létrehozhat egyéni [végpontokat](../iot-hub/iot-hub-devguide-endpoints.md) egy IoT-hubhoz az [útválasztási szabályok](#routing-rules)által elküldött üzenetek kézbesítéséhez. Az egyéni végpontok közvetlenül kapcsolódnak egy Event hub-hoz, egy Service Bus-várólistához vagy egy Service Bus témakörhöz.

### <a name="custom-gateway"></a>Egyéni átjáró

Az átjárók lehetővé teszik a kapcsolódást olyan eszközökhöz, amelyek nem tudnak közvetlenül csatlakozni a [IoT hubhoz](#iot-hub). A Azure IoT Edge használatával olyan egyéni átjárókat hozhat létre, amelyek egyéni logikát implementálnak az üzenetek kezelésére, az egyéni protokoll átalakítására és az Edge egyéb feldolgozására.

## <a name="d"></a>T

### <a name="data-point-message"></a>Adatpontra vonatkozó üzenet

Az adatpontok üzenete egy olyan [eszközről a felhőbe](#device-to-cloud) irányuló üzenet, amely a [telemetria](#telemetry) , például a szél sebességét vagy a hőmérsékletet tartalmazza.

### <a name="default-component"></a>Alapértelmezett összetevő

A IoT Plug and Play minden [eszköz modellje](#device-model) rendelkezik alapértelmezett összetevővel. Egy egyszerű eszköz-modellnek csak egy alapértelmezett összetevője van – ilyen modell más néven összetevő-eszköz. Az összetettebb modell több összetevőt tartalmaz, amelyek az alapértelmezett összetevő alá vannak ágyazva.

### <a name="deployment-manifest"></a>Üzembehelyezési jegyzék

[IoT Edge](#iot-edge)az üzembe helyezési jegyzék egy olyan JSON-dokumentum, amely egy vagy több IoT Edge-eszköz két moduljában másolandó adatokat tartalmazza, hogy modulokat, útvonalakat és társított modul kívánt tulajdonságokat helyezzen üzembe.

### <a name="desired-configuration"></a>Kívánt konfiguráció

Egy [különálló eszköz](../iot-hub/iot-hub-devguide-device-twins.md)kontextusában a kívánt konfiguráció a tulajdonságok és metaadatok teljes készletét jelenti, amelyeket szinkronizálni kell az eszközzel.

### <a name="desired-properties"></a>Kívánt tulajdonságok

Az [eszközök Twin](../iot-hub/iot-hub-devguide-device-twins.md)-környezetében a kívánt tulajdonságok a [jelentett tulajdonságokkal](#reported-properties) rendelkező, az eszköz konfigurációjának vagy feltételének szinkronizálásához használt eszköz-iker alszakasza. A kívánt tulajdonságokat csak [háttérbeli alkalmazások](#back-end-app) állíthatják be, és az [eszköz alkalmazás](#device-app)figyeli őket.

### <a name="device"></a>Eszköz

A IoT kontextusában az eszköz általában egy kis méretű, különálló számítástechnikai eszköz, amely adatokat gyűjthet vagy más eszközöket is vezérelheti. Előfordulhat például, hogy egy eszköz egy környezeti figyelő eszköz, vagy egy olyan vezérlő, amely egy üvegházban található öntözési és szellőzési rendszerekhez szükséges. Az [eszköz katalógusa](https://catalog.azureiotsolutions.com/) felsorolja a [IoT hub](#iot-hub)használatával tanúsított hardvereszközök listáját.

### <a name="device-app"></a>Eszközalkalmazás

Az eszközön fut egy alkalmazás [, és kezeli](#device) a kommunikációt az [IoT hubhoz](#iot-hub). Általában az [Azure IoT Device SDK](#azure-iot-device-sdks) -k egyikét használja egy eszköz alkalmazásának megvalósításakor. A IoT számos oktatóanyagában [szimulált eszközt](#simulated-device) használ a kényelem érdekében.

### <a name="device-builder"></a>Eszköz-szerkesztő

Az Eszközkezelő egy [eszköz modelljét](#device-model) és [felületét](#interface) használja, amikor programkódot implementál egy [IoT Plug and Play eszközön](#iot-plug-and-play-device)való futtatáshoz. Az eszközök építői jellemzően az [Azure IoT Device SDK](#azure-iot-device-sdks) -k egyikét használják az eszköz ügyfélszoftverének megvalósításához.

### <a name="device-certification"></a>Eszköztanúsítás

A IoT Plug and Play eszköz minősítési program ellenőrzi, hogy az eszköz megfelel-e a IoT Plug and Play minősítési követelményeinek. Hozzáadhat egy hitelesített eszközt az [Azure IoT-eszközök nyilvános minősítésére](https://aka.ms/devicecatalog).

### <a name="device-condition"></a>Eszköz feltétele

Az eszköz állapotával kapcsolatos információk, például a jelenleg használatban lévő kapcsolati módszer, amelyet egy [eszköz alkalmazás](#device-app)jelentett. Az [eszközök alkalmazásai](#device-app) is jelenthetik a képességeiket. A feltételek és a képességek adatait az eszközök ikrek használatával kérdezheti le.

### <a name="device-data"></a>Eszközbeállítások

Az eszköz adatai a IoT Hub [Identity registryben](#identity-registry)tárolt eszközönkénti adatokra vonatkoznak. Az ilyen típusú adatimportálás és-exportálás lehetséges.

### <a name="device-identity"></a>Eszközidentitás

Az eszköz identitása (vagy az eszköz azonosítója) az IoT Hub [Identity registryben](#identity-registry)regisztrált összes eszközhöz hozzárendelt egyedi azonosító.

### <a name="device-management"></a>Eszközfelügyelet

Az eszközkezelés magában foglalja a IoT-megoldás eszközeinek kezeléséhez kapcsolódó teljes életciklust, beleértve a tervezést, a létesítést, a konfigurálást, a figyelést és a kivonást.

### <a name="device-management-patterns"></a>Eszközfelügyeleti minták

Az [IoT hub](#iot-hub) lehetővé teszi a közös eszközkezelés mintázatát, beleértve az újraindítást, a gyári beállítások visszaállítását és a belső vezérlőprogram frissítését az eszközökön.

### <a name="device-model"></a>Eszközmodell

Az eszköz modellje olyan [modell](#model) , amely a [digitális ikrek definíciós nyelvét](#digital-twins-definition-language-dtdl) használja egy IoT Plug and Play eszköz képességeinek leírására. Egy egyszerű eszköz-modell egyetlen felületet használ az eszköz képességeinek leírására. Egy összetettebb eszköz-modell több összetevőt tartalmaz, amelyek mindegyike különböző képességeket ír le. További információ: [IoT Plug and Play-összetevők a modellekben](../iot-pnp/concepts-components.md).

### <a name="device-modeling"></a>Eszköz modellezése

Az [Eszközkezelő](#device-builder) vagy [modul-szerkesztő](#module-builder)a [digitális ikrek definíciós nyelvét](#digital-twins-definition-language-dtdl) használja egy [IoT Plug and Play eszköz](#iot-plug-and-play-device)képességeinek modellezéséhez. A [megoldás-szerkesztő](#solution-builder) konfigurálhat egy IoT-megoldást a modellből.

### <a name="device-provisioning"></a>Eszköz kiépítés

Az eszköz kiépítés során a rendszer a kezdeti [eszköz adatait](#device-data) hozzáadja a megoldásban lévő üzletekhez. Ahhoz, hogy egy új eszköz csatlakozhasson a központhoz, hozzá kell adnia egy eszköz AZONOSÍTÓját és kulcsait a IoT Hub [Identity registryhez](#identity-registry). A kiépítési folyamat részeként előfordulhat, hogy más megoldás-tárolókban is inicializálnia kell az eszközre vonatkozó adatmennyiséget.

### <a name="device-provisioning-service"></a>Device Provisioning Service

A IoT Hub Device Provisioning Service (DPS) a [IoT hub](#iot-hub) segítő szolgáltatása, amellyel a nulla érintéses eszköz kiépíthető a megadott IoT hubhoz. A DPS segítségével akár több millió eszközt is kiépítheti biztonságos és skálázható módon.

### <a name="device-rest-api"></a>Eszköz REST API

Az eszközről [REST API eszközről](/rest/api/iothub/device) az eszközről a felhőbe irányuló üzeneteket küldhet egy IoT hubhoz, és a [felhőből az eszközre](#cloud-to-device) irányuló üzeneteket fogadhat egy IoT hub-ból. Általában a IoT Hub oktatóanyagokban látható, a magasabb szintű [eszközök SDK](#azure-iot-device-sdks) -k egyikét kell használnia.

### <a name="device-twin"></a>Ikereszközök

Az eszköz Twin egy JSON-dokumentum, amely az eszköz állapotával kapcsolatos információkat, például a metaadatokat, a konfigurációkat és a feltételeket tárolja. IoT Hub megőrzi az IoT hub-ban kiépített eszközökhöz tartozó különálló eszközöket. Az eszközökhöz tartozó ikrek lehetővé teszik az eszközök és a konfigurációk szinkronizálását az eszköz és a megoldás hátterében. Lekérdezheti az eszközökből származó ikreket, hogy megkeresse az adott eszközöket és a hosszan futó műveletek állapotát.

### <a name="device-to-cloud"></a>Eszközről a felhőbe

Egy csatlakoztatott eszközről [IoT Hubre](#iot-hub)küldött üzenetekre hivatkozik. Ezek az üzenetek [adatponthoz](#data-point-message) vagy [interaktív](#interactive-message) üzenetekhez tartozhatnak. További információ: [üzenetek küldése és fogadása IoT Hubsal](../iot-hub/iot-hub-devguide-messaging.md).

### <a name="digital-twin"></a>Digitális Twin

A digitális Twin a fizikai objektumot jelképező digitális adatgyűjtemény. A fizikai objektum változásai a digitális Twin-ben is megjelennek. Bizonyos helyzetekben a digitális IKeret használhatja a fizikai objektum kezelésére. Az [Azure Digital Twins szolgáltatás](../digital-twins/index.yml) a [digitális Twins Definition Language (DTDL) nyelvben](#digital-twins-definition-language-dtdl) kifejezett [modelleket](#model) használ a digitális Twins fizikai eszközök vagy magasabb szintű absztrakt üzleti fogalmak ábrázolására, amely lehetővé teszi a felhőalapú digitális kettős megoldások széles választékát. Egy [IoT Plug and Play](../iot-pnp/index.yml) -eszközön digitális Twin, a DTDL- [eszköz modellje](#device-model)van leírva.

### <a name="digital-twin-change-events"></a>Digitális ikermódosítási események

Ha egy [IoT Plug and Play eszköz](#iot-plug-and-play-device) csatlakozik egy IoT hubhoz, a hub útválasztási képességével digitális kettős változtatásokról is küldhet értesítéseket. Ha például egy [tulajdonság](#properties) értéke megváltozik egy eszközön, IoT hub küldhet értesítést egy végpontnak, például egy Event hub-nak.

### <a name="digital-twin-route"></a>Digitális kettős útvonal

Egy IoT-hubhoz beállított útvonal, amely [digitális kettős változási eseményeket](#digital-twin-change-events) továbbít egy végpontra, például egy Event hub-ra.

### <a name="digital-twins-definition-language-dtdl"></a>A digitális ikerpéldányok definíciós nyelve (DTDL)

Egy JSON-LD nyelv, amely leírja a [modelleket](#model) és a [csatolókat](#interface) a [IoT Plug and Play-eszközök](#iot-plug-and-play-device) és az [Azure Digital Twins](../digital-twins/index.yml) -entitások számára. A Digital [Twins Definition Language 2-es verziójának](https://github.com/Azure/opendigitaltwins-dtdl) használatával ismertesse a [digitális Twin](#digital-twin) funkcióit, és lehetővé teszi, hogy a IoT platform és a IoT megoldások használják az entitás szemantikai feltételeit. A digitális ikrek definíciós nyelve gyakran rövidítve DTDL.

### <a name="direct-method"></a>Közvetlen metódus

A [közvetlen módszer](../iot-hub/iot-hub-devguide-direct-methods.md) segítségével elindíthat egy metódust az eszközön, ha az IOT hub API-ját hívja meg.

### <a name="downstream-services"></a>Alsóbb rétegbeli szolgáltatások

Relatív kifejezés, amely az aktuális környezetből származó adatok fogadására szolgáló szolgáltatásokat ismerteti. Ha például az Azure Digital Twins kontextusában gondolkodik, akkor a [Time Series Insights](../time-series-insights/index.yml) egy alsóbb rétegbeli szolgáltatásnak számít, ha az adatait az Azure digitális ikrekből Time Series Insightsba kívánja állítani.

## <a name="e"></a>E

### <a name="endpoint"></a>Végpont

Egy adatútválasztási szolgáltatás névvel ellátott ábrázolása, amely más szolgáltatásokból származó adatok fogadására képes.

Az IoT hub több [végpontot](../iot-hub/iot-hub-devguide-endpoints.md) tesz elérhetővé, amelyek lehetővé teszik az alkalmazások számára az IoT hubhoz való kapcsolódást. Az eszközökön elérhető végpontok olyan műveleteket hajthatnak végre, mint például az [eszközről a felhőbe](#device-to-cloud) irányuló üzenetek küldése és [a felhőből az eszközre irányuló](#cloud-to-device) üzenetek fogadása. Vannak olyan szolgáltatásokkal kapcsolatos felügyeleti végpontok, amelyek lehetővé teszik a [háttérbeli alkalmazások](#back-end-app) számára, hogy olyan műveleteket hajtsanak végre, mint például az [eszközök identitásának](#device-identity) kezelése és az eszközök kettős kezelése. Az eszközről a felhőbe irányuló üzenetek olvasásához a szolgáltatáshoz kapcsolódó [beépített végpontok](#built-in-endpoints) tartoznak. Létrehozhat [Egyéni végpontokat](#custom-endpoints) a [útválasztási szabályok](#routing-rules)által elküldött eszközről a felhőbe irányuló üzenetek fogadásához.

### <a name="enrollment"></a>Beléptetés

A [Device kiépítési szolgáltatásban](#device-provisioning-service)a regisztráció az egyes eszközök vagy eszközök olyan csoportjai, amelyek az automatikus kiépítés útján regisztrálhatók egy [csatolt IoT hubhoz](#linked-iot-hub) .

### <a name="enrollment-group"></a>Beléptetési csoport

A [Device kiépítési szolgáltatásban](#device-provisioning-service)egy beléptetési csoport azonosítja az X. 509 vagy a szimmetrikus kulcs [igazolási mechanizmusát](#attestation-mechanism)használó eszközök egy csoportját.

### <a name="event-handlers"></a>Eseménykezelők

Ez bármely olyan folyamatra vonatkozhat, amelyet az esemény megérkezése és valamilyen feldolgozási művelet elindít. Az eseménykezelők létrehozásának egyik módja az esemény-feldolgozási kód hozzáadása egy Azure-függvényhez, és az adatok továbbítása a [végpontok](#endpoint) és az [esemény-útválasztás](#event-routing)használatával.

### <a name="event-hub-compatible-endpoint"></a>Event hub-kompatibilis végpont

Az IoT hub-ba küldött, az [eszközről a felhőbe](#device-to-cloud) irányuló üzenetek olvasásához csatlakozhat egy végponthoz a hub-on, és bármilyen Event hub-kompatibilis módszert használhat az üzenetek olvasásához. Az Event hub-kompatibilis módszerek közé tartozik a [Event Hubs SDK](../event-hubs/event-hubs-programming-guide.md) -k és a [Azure stream Analytics](../stream-analytics/stream-analytics-introduction.md)használata.

### <a name="event-routing"></a>Események útválasztása

Az események és az adatok az egyik eszközről vagy szolgáltatásból egy másik [végpontba](#endpoint) való küldésének folyamata. 

Az IOT hub-ban megadhat [útválasztási szabályokat](#routing-rules) az üzenetek küldési módjának leírásához. Az Azure Digital Twinsban az Event Routes olyan entitások, amelyek erre a célra lettek létrehozva. Az Azure Digital Twins esemény-útvonalak szűrőket tartalmazhatnak, amelyekkel korlátozható, hogy milyen típusú eseményeket küld a rendszer az egyes végpontoknak.

## <a name="f"></a>F

### <a name="field-gateway"></a>Mező átjáró

A helyszíni átjárók lehetővé teszik a csatlakozást olyan eszközökhöz, amelyek nem tudnak közvetlenül csatlakozni a [IoT hubhoz](#iot-hub) , és általában helyileg vannak telepítve az eszközökön. További információ: [Mi az az Azure IoT hub?](../iot-hub/about-iot-hub.md)

## <a name="g"></a>G

### <a name="gateway"></a>Átjáró

Az átjárók lehetővé teszik a kapcsolódást olyan eszközökhöz, amelyek nem tudnak közvetlenül csatlakozni a [IoT hubhoz](#iot-hub). Lásd még: [Field Gateway](#field-gateway), [Cloud Gateway](#cloud-gateway)és [Custom Gateway](#custom-gateway).

### <a name="gateway-device"></a>Átjáró eszköz

Az eszköz egy példa egy mező- [átjáróra](#field-gateway). Az átjáró-eszköz lehet szabványos IoT- [eszköz](#device) vagy [IoT Edge-eszköz](#iot-edge-device).

Az átjáró-eszköz lehetővé teszi a kapcsolat használatát olyan alsóbb rétegbeli eszközökhöz, amelyek nem tudnak közvetlenül kapcsolódni [IoT hubhoz](#iot-hub).

## <a name="h"></a>H

### <a name="hardware-security-module"></a>Hardveres biztonsági modul

A hardveres biztonsági modul (HSM) a biztonságos, hardveres tárolásra szolgál az eszközök titkaihoz. Ez az eszköz titkos tárterületének legbiztonságosabb formája. Az X. 509 tanúsítványok és a szimmetrikus kulcsok is tárolhatók egy HSM-ben. Az [eszközök kiépítési szolgáltatásában](#device-provisioning-service)az [igazolási mechanizmus](#attestation-mechanism) a HSM használatát is felhasználhatja.

## <a name="i"></a>I

### <a name="id-scope"></a>AZONOSÍTÓ hatóköre

Az azonosító hatóköre egyedi érték, amelyet a rendszer a létrehozáskor egy [eszköz-kiépítési szolgáltatás (DPS)](#device-provisioning-service) példányához rendel hozzá.

IoT Central alkalmazások a DPS-példányokat használják, és az azonosító hatókörét elérhetővé teszik a IoT Central felhasználói felületen.

### <a name="identity-registry"></a>Identitás-nyilvántartó

Az [Identity Registry](../iot-hub/iot-hub-devguide-identity-registry.md) egy IoT hub beépített összetevője, amely az IoT hubhoz való kapcsolódásra jogosult egyes eszközök adatait tárolja.

### <a name="individual-enrollment"></a>Egyéni regisztráció

A [Device kiépítési szolgáltatásban](#device-provisioning-service)az egyéni regisztráció egyetlen olyan eszközt azonosít, amely egy X. 509 levél-tanúsítványt vagy egy szimmetrikus kulcsot használ [igazolási mechanizmusként](#attestation-mechanism).

### <a name="interactive-message"></a>Interaktív üzenet

Az interaktív üzenet egy [felhőből az eszközre](#cloud-to-device) irányuló üzenet, amely azonnali műveletet indít el a megoldás hátterében. Előfordulhat például, hogy egy eszköz riasztást küld egy olyan hibáról, amelyet automatikusan be kell jelentkeznie egy CRM-rendszerbe.

### <a name="interface"></a>Interfész

A IoT Plug and Playban az interfész leírja a [IoT Plug and Play eszköz](#iot-plug-and-play-device) vagy a [digitális iker](#digital-twin)által megvalósított kapcsolódó képességeket. A különböző [eszközökön](#device-model)keresztül is felhasználhat felületeket. Ha egy illesztőfelületet egy eszköz modelljében használ, az meghatározza az eszköz [összetevőjét](#component) . Egy egyszerű eszköz csak egy alapértelmezett felületet tartalmaz.

Az Azure Digital Twins-ban az *interfész* a [DTDL](#digital-twins-definition-language-dtdl) -modell definíciójának legfelső szintű kódjára utalhat.

### <a name="iot-edge"></a>IoT Edge

Azure IoT Edge lehetővé teszi az Azure-szolgáltatások és a megoldás-specifikus kódok felhőalapú üzembe helyezését a helyszíni eszközökön. Az eszközök a felhőbe való elküldésük előtt más eszközökről származó adatokat összesítenek, így a számítástechnikai és elemzési feladatokat is elvégezhetik. [IoT Edge](#iot-edge-device) További információ: [Azure IoT Edge](../iot-edge/index.yml).

### <a name="iot-edge-agent"></a>IoT Edge ügynök

A modulok üzembe helyezéséhez és figyeléséhez felelős IoT Edge futtatókörnyezetének része.

### <a name="iot-edge-device"></a>IoT Edge-eszköz

Az IoT Edge-eszközök tárolós IoT Edge [modulokat](#modules) használnak az Azure-szolgáltatások, harmadik féltől származó szolgáltatások vagy a saját kódok futtatásához. A IoT Edge eszközön a [IoT Edge futtatókörnyezet](#iot-edge-runtime) kezeli a modulokat. A felhőből távolról is figyelheti és felügyelheti IoT Edge eszközeit.

### <a name="iot-edge-hub"></a>IoT Edge hub

A modul kommunikációs, felsőbb rétegbeli (IoT Hub) és alsóbb rétegbeli (IoT Hub) kommunikációjának IoT Edgei futtatókörnyezetének része.

### <a name="iot-edge-runtime"></a>IoT Edge-futtatókörnyezet

IoT Edge Runtime tartalmaz mindent, amit a Microsoft terjeszt IoT Edge eszközre való telepítésre. Ide tartozik az Edge Agent, az Edge hub és a IoT Edge biztonsági démon.

### <a name="iot-extension-for-azure-cli"></a>IoT-bővítmény az Azure CLI-hez

[Az Azure CLI-hez készült IoT-bővítmény](https://github.com/Azure/azure-iot-cli-extension) egy platformfüggetlen, parancssori eszköz. Az eszköz lehetővé teszi az eszközök kezelését az [Identity registryben](#identity-registry), üzenetek és fájlok küldését és fogadását az eszközökről, valamint az IoT hub-műveletek figyelését.

### <a name="iot-hub"></a>IoT Hub

A IoT Hub egy teljes körűen felügyelt Azure-szolgáltatás, amely megbízható és biztonságos kétirányú kommunikációt tesz lehetővé több millió eszköz és egy megoldás hátterében. További információ: [Mi az az Azure IoT hub?](../iot-hub/about-iot-hub.md) Az Azure-előfizetését használva IoT hubokat hozhat létre a IoT üzenetkezelési számítási feladatainak kezeléséhez.

### <a name="iot-hub-metrics"></a>IoT Hub metrikák

IoT Hub mérőszámok adatokat biztosítanak az Azure-előfizetésében lévő IoT-hubok állapotáról. IoT Hub mérőszámok lehetővé teszik a szolgáltatás általános állapotának és a hozzájuk kapcsolódó eszközök értékelését. IoT Hub mérőszámok segítségével megtekintheti, hogy mi történik az IoT-hubhoz, és hogyan vizsgálja meg a kiváltó problémákat anélkül, hogy kapcsolatba kellene lépnie az Azure ügyfélszolgálatával. További információért lásd: [IoT hub figyelése](../iot-hub/monitor-iot-hub.md).

### <a name="iot-hub-query-language"></a>IoT Hub lekérdezés nyelve

A [IoT hub lekérdezési nyelv](../iot-hub/iot-hub-devguide-query-language.md) egy SQL-szerű nyelv, amely lehetővé teszi a [feladatok](#job), a digitális ikrek és az eszközök ikrek általi lekérdezését.

### <a name="iot-hub-resource-rest-api"></a>Erőforrás-REST API IoT Hub

A [IoT hub erőforrás-REST API](/rest/api/iothub/iothubresource) segítségével kezelheti az Azure-előfizetéshez tartozó IoT-hubokat olyan műveletek végrehajtásához, mint például a hubok létrehozása, frissítése és törlése.

### <a name="iot-plug-and-play-bridge"></a>IoT Plug and Play-híd

A IoT Plug and Play Bridge egy nyílt forráskódú alkalmazás, amely lehetővé teszi a Windows-vagy Linux-átjárók számára csatlakoztatott meglévő érzékelők és perifériák csatlakoztatását a [IoT Plug and Play-eszközökhöz](#iot-plug-and-play-device).

### <a name="iot-plug-and-play-conventions"></a>Az IoT Plug and Playhez kapcsolódó konvenciók

A IoT Plug and Play- [eszközöknek](#iot-plug-and-play-device) a megoldással való adatcserére vonatkozó konvenciókat kell követniük.

### <a name="iot-plug-and-play-device"></a>IoT Plug and Play eszköz

A IoT Plug and Play-eszközök általában egy kis méretű, önálló számítástechnikai eszköz, amely adatokat gyűjt, vagy más eszközöket irányít, valamint az [eszköz modelljét](#device-model)implementáló szoftvert vagy belső vezérlőprogramot futtat.  Például egy IoT Plug and Play eszköz lehet környezeti figyelő eszköz vagy egy intelligens mezőgazdasági öntözési rendszer vezérlője. Előfordulhat, hogy egy IoT Plug and Play eszköz közvetlenül vagy IoT Edge modulként van megvalósítva. Felhőalapú IoT-megoldást is írhat a IoT Plug and Play eszközökről származó adatok parancsára, vezérlésére és fogadására.

### <a name="iot-solution-accelerators"></a>IoT-megoldásgyorsítók

Az Azure IoT megoldás-gyorsító csomag több Azure-szolgáltatást is kínál a megoldásokhoz. Ezek a megoldások lehetővé teszik a gyakori IoT-forgatókönyvek teljes körű megvalósításának gyors megkezdését. További információ: [Mi az Azure IoT megoldás-gyorsító?](../iot-accelerators/about-iot-accelerators.md)

## <a name="j"></a>J

### <a name="job"></a>Feladat

A megoldás háttérbe állítása [feladatok](../iot-hub/iot-hub-devguide-jobs.md) használatával ütemezhet és követheti a tevékenységeket az IoT hub-ban regisztrált eszközökön. A tevékenységek közé tartozik az eszköz dupla [kívánt tulajdonságainak](#desired-properties)frissítése, az eszköz kettős [címkék](#tags)frissítése és a [közvetlen metódusok](#direct-method)meghívása. A [IoT hub](#iot-hub) az azonosítót is használja az [identitás-beállításjegyzékből](#identity-registry) [való importálásra és exportálásra](../iot-hub/iot-hub-devguide-identity-registry.md#import-and-export-device-identities) .

## <a name="l"></a>L

### <a name="leaf-device"></a>Levél eszköz

[IoT Edge](#iot-edge)a levél eszköz olyan eszköz, amely nem rendelkezik alsóbb rétegbeli eszközzel.

### <a name="lifecycle-event"></a>Életciklus-esemény

Az Azure Digital Twins-ban ez az esemény akkor jön létre, amikor egy adatelem – például egy digitális Twin, egy kapcsolat vagy egy eseménykezelő – létrejött vagy törölve lett az Azure Digital Twins-példányból.

### <a name="linked-iot-hub"></a>Csatolt IoT hub

Az eszközök [kiépítési szolgáltatása (DPS)](#device-provisioning-service)képes eszközöket kiépíteni a hozzájuk társított IoT hubokba. Az IoT hub DPS-példánnyal való összekapcsolása lehetővé teszi, hogy a szolgáltatás regisztrálja az eszköz AZONOSÍTÓját, és állítsa be a kezdeti konfigurációt a Twin eszközön.

## <a name="m"></a>M

### <a name="model"></a>Modellezés

A modellek definiálják az entitás típusát a fizikai környezetben, beleértve annak tulajdonságait, telemetriáiról, összetevőit és esetenként más információkat. A modellek használatával olyan [digitális ikrek](#digital-twin) hozhatók létre, amelyek adott típusú fizikai objektumokat jelképeznek. A modellek a [digitális ikrek definíciós nyelvén](#digital-twins-definition-language-dtdl)íródnak.

Az [Azure Digital Twins szolgáltatásban](../digital-twins/index.yml)a modellek meghatározhatnak eszközöket vagy magasabb szintű absztrakt üzleti fogalmakat is. A [IoT Plug and Playban](../iot-pnp/index.yml)az eszközök [modellje](#device-model) kifejezetten az eszközök leírására szolgál.

### <a name="model-id"></a>Modellazonosító

Amikor egy IoT Plug and Play-eszköz csatlakozik egy IoT Hubhoz, elküldi az általa megvalósított [DTDL](#digital-twins-definition-language-dtdl) modell **azonosítóját** . Ez az azonosító lehetővé teszi, hogy a megoldás megtalálja az eszköz modelljét.

### <a name="model-repository"></a>Modelladattár

A modell tárháza az [eszközök modelljeit](#device-model) és [felületeit](#interface)tárolja.

### <a name="model-repository-rest-api"></a>Modell tárháza REST API

API a modell adattárának kezeléséhez és interakcióhoz. Használhatja például az API-t az [eszközök modelljeinek](#device-model)hozzáadásához és kereséséhez.

### <a name="module-builder"></a>Modul-szerkesztő

Egy modul-szerkesztő [eszköz modelljét](#device-model) és [felületét](#interface) használja a kód [IoT Plug and Play eszközön](#iot-plug-and-play-device)való futtatásához. A modul-építők a kódot modulként vagy IoT Edge modulként implementálják az eszközön IoT Edge futtatókörnyezetbe való üzembe helyezéshez.

### <a name="module-identity"></a>Modul identitása

A modul identitása az eszközhöz tartozó összes modulhoz hozzárendelt egyedi azonosító. A modul identitása is regisztrálva van az [Identity registryben](#identity-registry).

A modul azonosítja azokat a biztonsági hitelesítő adatokat, amelyeket a modul használ a [IoT hub](#iot-hub) való hitelesítéshez, vagy IoT Edge modul esetén a [IoT Edge hubhoz](#iot-edge-hub).

### <a name="module-image"></a>Modul képe

A [IoT Edge futtatókörnyezet](#iot-edge-runtime) által a modul példányainak létrehozásához használt Docker-rendszerkép.

### <a name="module-twin"></a>Modul Twin

Az eszközökhöz hasonlóan a Twin modul egy JSON-dokumentum, amely a modul állapotával kapcsolatos információkat, például metaadatokat, konfigurációkat és feltételeket tárol. IoT Hub megőrzi az IoT hub eszköz-identitása alatt kiépített modul-identitások külön modulját. Az ikrek modul lehetővé teszi, hogy szinkronizálja a modul feltételeit és konfigurációit a modul és a megoldás háttér-végpontja között. Az ikrek modulban lekérdezheti az adott modulokat, és lekérdezheti a hosszan futó műveletek állapotát.

### <a name="modules"></a>Modulok

Az eszköz oldalon a IoT Hub eszköz SDK-k lehetővé teszik, hogy olyan [modulokat](../iot-hub/iot-hub-devguide-module-twins.md) hozzon létre, amelyekben mindegyik egy független kapcsolódást nyit meg a IoT hubhoz. Ez a funkció lehetővé teszi, hogy külön névtereket használjon az eszköz különböző összetevőihez.

A modul identitása és a modul Twin ugyanazokat a funkciókat biztosítja, mint az [eszköz identitása](#device-identity) és az [eszközök Twin](#device-twin) , de finomabb részletességgel. Ez a finomabb részletesség lehetővé teszi, hogy a képes eszközök, például az operációs rendszer alapú eszközök vagy a belső vezérlőprogram eszközei több összetevőt kezelnek, az egyes összetevők konfigurációjának és feltételeinek elkülönítéséhez.

[IoT Edge](#iot-edge)a modul egy Docker-tároló, amelyet IoT Edge eszközökön telepíthet. Egy adott feladatot hajt végre, például egy eszközön lévő üzenet betöltését, egy üzenet átalakítását vagy egy üzenet küldését egy IoT hubhoz. Más modulokkal kommunikál, és adatokat küld a [IoT Edge futtatókörnyezetnek](#iot-edge-runtime).

### <a name="mqtt"></a>MQTT

A [MQTT](https://mqtt.org/) az egyik üzenetküldési protokoll, amelyet a [IoT hub](#iot-hub) támogat az eszközökkel való kommunikációhoz. További információ a IoT Hub által támogatott üzenetküldési protokollokról: [üzenetek küldése és fogadása IoT hub](../iot-hub/iot-hub-devguide-messaging.md)használatával.

## <a name="o"></a>O

### <a name="ontology"></a>Ontológia

Egy adott tartomány modelljeinek összessége, például az ingatlanok, az intelligens városok, a IoT-rendszerek, az energiahálózatok és egyebek. A ontológiákat-ket gyakran használják az Azure-beli [digitális Twins](#azure-digital-twins)-hoz hasonló tudásbázisok sémái, mert az iparági szabványokon és ajánlott eljárásokon alapuló kiindulási pontot biztosítanak.

További információ a ontológiákat: [Mi az az ontológia?](../digital-twins/concepts-ontologies.md)

### <a name="operations-monitoring"></a>Műveletek figyelése

A IoT Hub [Operations monitoring](../iot-hub/iot-hub-operations-monitoring.md) lehetővé teszi, hogy valós időben figyelje a IoT hub műveleteinek állapotát. [IoT hub](#iot-hub) az eseményeket több különböző kategóriába tartozó műveletben követi nyomon. A feldolgozáshoz egy vagy több kategóriából is küldhet eseményeket IoT Hub végpontra. A hibák figyelése és az adatmintázatok alapján összetettebb feldolgozás is beállítható.

## <a name="p"></a>P

### <a name="physical-device"></a>Fizikai eszköz

A fizikai eszközök olyan valódi eszközök, mint például a málna PI, amely egy IoT hubhoz csatlakozik. A IoT Hub számos oktatóanyaga [szimulált eszközök](#simulated-device) használatával teszi lehetővé, hogy mintákat futtasson a helyi gépen.

### <a name="primary-and-secondary-keys"></a>Elsődleges és másodlagos kulcsok

Ha egy IoT hub eszközre irányuló vagy szolgáltatáshoz kapcsolódó végpontján csatlakozik, a [kapcsolati sztring](#connection-string) tartalmaz egy kulcsot, amely hozzáférést biztosít Önnek. Ha hozzáad egy eszközt az [azonosító beállításjegyzékhez](#identity-registry) , vagy egy [megosztott hozzáférési szabályzatot](#shared-access-policy) ad hozzá a központhoz, a szolgáltatás létrehoz egy elsődleges és egy másodlagos kulcsot. A két kulcs lehetővé teszi, hogy átadja az egyik kulcsról a másikra, ha az IoT hub elérésének elvesztése nélkül frissíti a kulcsot.

### <a name="properties"></a>Tulajdonságok

A tulajdonságok olyan adatmezők, amelyek egy olyan [felületen](#interface) vannak meghatározva, amely egy [digitális Twin](#digital-twin)állandó állapotát jelöli. A tulajdonságokat csak olvasható vagy írható módon deklarálhatja. A csak olvasható tulajdonságokat, például a sorozatszámot a [IoT Plug and Play eszközön](#iot-plug-and-play-device) futó kód állítja be. Az írható tulajdonságok, például a riasztási küszöbértékek általában a felhőalapú IoT-megoldás alapján vannak beállítva.

### <a name="property-change-event"></a>Tulajdonság-változási esemény

Egy olyan esemény, amely egy [digitális Twin](#digital-twin)tulajdonság változását eredményezi.

### <a name="protocol-gateway"></a>Protokollátjáró

A protokoll-átjáró jellemzően a felhőben van üzembe helyezve, és protokoll-fordítási szolgáltatásokat biztosít a [IoT hubhoz](#iot-hub)csatlakozó eszközökhöz. További információ: [Mi az az Azure IoT hub?](../iot-hub/about-iot-hub.md)

## <a name="r"></a>R

### <a name="registration"></a>Regisztráció

A regisztráció a IoT Hub [Identity registryben](#identity-registry)található eszköz rekordja. A regisztrációhoz vagy az eszközhöz közvetlenül is regisztrálhat, vagy a [Device kiépítési szolgáltatás](#device-provisioning-service) segítségével automatizálhatja az eszközök regisztrálását.

### <a name="registration-id"></a>Regisztrációs azonosító

A regisztrációs azonosító segítségével egyedileg azonosíthatók az eszközök [regisztrációja](#registration) az [eszköz kiépítési szolgáltatásával](#device-provisioning-service). A regisztrációs azonosító lehet az [eszköz identitásával](#device-identity)megegyező érték.

### <a name="relationship"></a>Kapcsolat

Az [Azure Digital Twins](../digital-twins/index.yml) szolgáltatásban a kapcsolatok a [digitális ikrek](#digital-twin) összekapcsolására szolgálnak, amelyek a teljes fizikai környezetét digitálisan reprezentálják. Az ikrek által megadható kapcsolatok típusai az ikrek [modell](#model) -definícióinak részeként vannak meghatározva – a [DTDL](#digital-twins-definition-language-dtdl) modell bizonyos típusú Twin-típusokhoz tartalmaz információt arról, hogy milyen kapcsolatokra lehet más ikrek számára.

### <a name="reported-configuration"></a>Jelentett konfiguráció

Egy [különálló eszköz](../iot-hub/iot-hub-devguide-device-twins.md)kontextusában a jelentett konfiguráció a tulajdonságok és a metaadatok teljes készletét jelenti, amelyeket jelenteni kell a megoldás hátterének.

### <a name="reported-properties"></a>Jelentett tulajdonságok

Az [eszközök Twin](../iot-hub/iot-hub-devguide-device-twins.md)-környezetében a jelentett tulajdonságok a [kívánt tulajdonságokkal](#desired-properties) rendelkező eszközök iker szakasza az eszköz konfigurációjának vagy feltételének szinkronizálásához. A jelentett tulajdonságok csak az [eszköz alkalmazásban](#device-app) állíthatók be, és egy [háttérbeli alkalmazás](#back-end-app)által olvashatók és kérhetők le.

### <a name="retry-policy"></a>Újrapróbálkozási szabályzat

A felhőalapú szolgáltatásokhoz való csatlakozáskor az újrapróbálkozási szabályzat segítségével kezelheti az [átmeneti hibákat](/azure/architecture/best-practices/transient-faults) .

### <a name="routing-rules"></a>Útválasztási szabályok

A IoT hub [útválasztási szabályait](../iot-hub/iot-hub-devguide-messages-read-custom.md) úgy konfigurálja, hogy az eszközről a felhőbe irányuló üzeneteket egy [beépített végpontra](#built-in-endpoints) vagy [Egyéni végpontokra](#custom-endpoints) irányítsa a megoldás hátterének feldolgozásához.

## <a name="s"></a>S

### <a name="sasl-plain"></a>SASL PLAIN

SASL PLAIN egy protokoll, amelyet a AMQP protokoll a biztonsági jogkivonatok átvitelére használ.

### <a name="service-operations-endpoint"></a>Szolgáltatási műveletek végpontja

A szolgáltatás-rendszergazda által használt szolgáltatási beállítások kezelésére szolgáló [végpont](#endpoint) . Az [eszköz kiépítési szolgáltatásában](#device-provisioning-service) például a szolgáltatás végpontját használja a regisztrációk kezeléséhez.

### <a name="service-rest-api"></a>Szolgáltatás REST API-ja

Az eszközök kezeléséhez használhatja a megoldás háttér- [REST API a szolgáltatását](/rest/api/iothub/service/configuration) . Az API lehetővé teszi az [eszközök Twin](#device-twin) tulajdonságainak lekérését és frissítését, a [közvetlen metódusok](#direct-method)meghívását és a [feladatok](#job)ütemezését. Általában a magasabb szintű [szolgáltatási SDK](#azure-iot-service-sdks) -k egyikét kell használnia, ahogy az a IoT hub oktatóanyagokban is látható.

### <a name="shared-access-policy"></a>Megosztott elérési házirend

A megosztott elérési házirend határozza meg, hogy az adott szabályzathoz tartozó érvényes [elsődleges vagy másodlagos kulccsal](#primary-and-secondary-keys) rendelkezők számára milyen engedélyek vannak megadva. A központ megosztott hozzáférési házirendjeit és kulcsait a portálon kezelheti.

### <a name="shared-access-signature"></a>Közös hozzáférésű jogosultságkód

A közös hozzáférésű aláírások (SAS) az SHA-256 biztonságos kivonatokon vagy URI-kon alapuló hitelesítési mechanizmus. Az SAS-hitelesítésnek két összetevője van: egy _közös hozzáférési házirend_ és egy _közös hozzáférési aláírás_ (más néven token). Az eszközök SAS használatával végzik el a hitelesítést egy IoT hub használatával. A [háttérbeli alkalmazások](#back-end-app) az SAS használatával is hitelesítik magukat az IoT hub szolgáltatással szemben lévő végpontokkal. Általában az SAS-tokent a [kapcsolati karakterláncban](#connection-string) adja meg, amelyet az alkalmazás használ a IoT hub-kapcsolat létesítéséhez.

### <a name="simulated-device"></a>Szimulált eszköz

A IoT Hub számos oktatóanyaga szimulált eszközök használatával teszi lehetővé, hogy mintákat futtasson a helyi gépen. Ezzel szemben a [fizikai eszköz](#physical-device) egy valódi eszköz, például egy málna PI, amely egy IoT hubhoz csatlakozik.

### <a name="solution"></a>Megoldás

Egy _megoldás_ olyan Visual Studio-megoldásra is hivatkozhat, amely egy vagy több projektet tartalmaz. A _megoldások_ olyan IoT-megoldásra is hivatkozhatnak, amely olyan elemeket tartalmaz, mint például az eszközök, az eszközök [alkalmazásai](#device-app), az IoT hub, az egyéb Azure-szolgáltatások és a [háttérbeli alkalmazások](#back-end-app).

### <a name="solution-builder"></a>Megoldás-szerkesztő

A megoldás-szerkesztő létrehozza a megoldás hátterét. A megoldás-szerkesztő általában az Azure-erőforrásokkal, például a IoT Hub és a [Model repositorykkal](#model-repository)működik.

### <a name="system-properties"></a>Rendszertulajdonságok

Egy [különálló eszköz](../iot-hub/iot-hub-devguide-device-twins.md)kontextusában a Rendszertulajdonságok csak olvashatók, és információkat tartalmaznak az eszköz használatáról, például a legutóbbi tevékenység idejéről és a kapcsolatok állapotáról.

## <a name="t"></a>T

### <a name="tags"></a>Címkék

A [Twin eszközök](../iot-hub/iot-hub-devguide-device-twins.md)kontextusában a címkék a megoldás által tárolt, és egy JSON-dokumentum formájában lekért eszköz-metaadatok. A címkék nem láthatók az eszközön futó alkalmazások számára.

### <a name="target-condition"></a>Cél feltétel

Egy IoT Edge üzemelő példányban a célként megadott feltétel kiválasztja az üzemelő példányhoz tartozó eszközöket, például: **tag. environment = Prod**. A célként megadott feltétel folyamatosan ki van értékelve, hogy tartalmazza a követelményeknek megfelelő új eszközöket, vagy távolítsa el azokat az eszközöket, amelyek már nem rendelkeznek.

### <a name="telemetry"></a>Telemetria

Az eszközök telemetria adatokat gyűjtenek, például a szél sebességét vagy a hőmérsékletet, és adatpont-üzenetek használatával küldik el a telemetria egy IoT-hubhoz.

A IoT Plug and Play és az Azure digitális Twins-ban az [illesztőfelületekben](#interface) definiált telemetria mezők a méréseket jelölik. Ezek a mérések jellemzően olyan értékek, mint az eszközök által küldött szenzorok, például a [IoT Plug and Play-eszközök](#iot-plug-and-play-device)adatfolyamként történő továbbítása.

A [tulajdonságoktól](#properties)eltérően a telemetria nem [digitális Twin](#digital-twin)-ben van tárolva; Ez az idő kötött adatesemények streamje, amelyeket az alkalmazás során kell kezelni.

### <a name="telemetry-event"></a>Telemetria esemény

Egy esemény, amely a telemetria-adatmennyiség megérkezését jelzi.

### <a name="token-service"></a>Jogkivonat-szolgáltatás

A jogkivonat-szolgáltatás használatával hitelesítési mechanizmust valósíthat meg az eszközökhöz. Egy IoT Hub [megosztott hozzáférési szabályzatot](#shared-access-policy) használ, amely **DeviceConnect** engedélyekkel rendelkezik az *eszköz hatókörű* jogkivonatok létrehozásához. Ezek a tokenek lehetővé teszik, hogy az eszköz csatlakozhasson az IoT hubhoz. Az eszköz egyéni hitelesítési mechanizmust használ a jogkivonat-szolgáltatással történő hitelesítéshez. Ha az eszköz hitelesítése sikeresen megtörtént, a jogkivonat szolgáltatás egy SAS-tokent bocsát ki az eszköz számára az IoT hub eléréséhez.

### <a name="twin-graph-or-digital-twin-graph"></a>Twin gráf (vagy digitális dupla gráf)

Az [Azure Digital Twins](../digital-twins/index.yml) szolgáltatásban összekapcsolhatók a [digitális ikrek](#digital-twin) [kapcsolatokkal](#relationship) , amelyek a teljes fizikai környezet digitális megjelenítéséhez szükséges tudásbázisokat hozhatnak létre. Egyetlen [Azure Digital Twins-példány](#azure-digital-twins-instance) számos leválasztott gráfot vagy egyetlen összekapcsolt gráfot képes tárolni.

### <a name="twin-queries"></a>Dupla lekérdezés

Az [eszközök és modulok két lekérdezése](../iot-hub/iot-hub-devguide-query-language.md) az SQL-Like IoT hub lekérdezési nyelvet használja az eszközöktől az ikrekből vagy a modulokból származó adatok lekéréséhez. Ugyanazt a IoT Hub lekérdezési nyelvet használhatja az IoT hub-on futó [feladatok](#job) adatainak lekéréséhez.

### <a name="twin-synchronization"></a>Kettős szinkronizálás

A Twin synchronization a [kívánt tulajdonságokat](#desired-properties) használja az ikrek vagy a modul ikrek számára az eszközök vagy modulok konfigurálásához és a [jelentett tulajdonságok](#reported-properties) lekéréséhez a Twin-ben való tároláshoz.

## <a name="u"></a>U

### <a name="upstream-services"></a>Felsőbb rétegbeli szolgáltatások

Egy relatív kifejezés, amely leírja, hogy mely szolgáltatásokat kell az aktuális környezetbe betáplálni. Ha például az Azure Digital Twins kontextusában gondolkodik, IoT Hub egy felsőbb rétegbeli szolgáltatásnak számít, mivel az adatok IoT Hubról az Azure digitális Ikrekbe áramlanak.

## <a name="x"></a>X

### <a name="x509-client-certificate"></a>X. 509 ügyféltanúsítvány

Az eszközök X. 509 tanúsítvánnyal hitelesíthetők a [IoT hub](#iot-hub)használatával. Az X. 509 tanúsítvány használata a [sas-tokenek](#shared-access-signature)használatának alternatívája.
